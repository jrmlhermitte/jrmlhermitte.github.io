## MCP With Gemini

MCP is a powerful protocol that enables your LLMs to run tools. As seen among
numerous literature, LLMs scale once they're able to not only read from
their environment, but [change it](https://arxiv.org/pdf/2505.10361).

There are two modes MCP can run in: [stdio](https://modelcontextprotocol.io/docs/concepts/transports#standard-input%2Foutput-stdio), or [HTTP](https://modelcontextprotocol.io/docs/concepts/transports#streamable-http). The latter is just a standard HTTP server. The former is interesting as it uses stdin/stdout.


This post will demonstrate how to leverage the full power of MCP through Gemini series LLMs
by adding your custom MCP server to [Gemini CLI](https://blog.google/technology/developers/introducing-gemini-cli-open-source-ai-agent/).
Lots of these posts exist, but I didn't see one that actually demonstrates how Stdio works with real calls into it. This post attempts to fill that gap. 

![sequence diagram](/images/2025_07_04_gemini_graph.png)


Most of this has also been put in the readme [here](https://github.com/jrmlhermitte/gemini-mcp-example).

### What this will not be about

This is geared towards home projects where one agent connected to a bunch of tools is helpful.
This post will not go into setting up multiple agents. See Google's [Agent2Agent](https://developers.googleblog.com/en/a2a-a-new-era-of-agent-interoperability/) protocol for more information. See the [ADK Libary](https://google.github.io/adk-docs/) for some more details, or see this nice [Hugging Face Tutorial](https://huggingface.co/blog/tsadoq/agent2agent-and-mcp-tutorial) on that.


## Quick Setup
First of all, for this to work, you'll need to ensure you have the proper tools.
I'll recommend some that I believe has the least amount of friction (`uv`), but
feel free to mix and match which whatever python toolset you prefer:

### 1. Install UV

[Install UV](https://docs.astral.sh/uv/getting-started/installation/#installing-uv)

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

(linux and mac)

### 2. Initialize
Clone project and initialize virtualenv

```bash
git clone https://github.com/jrmlhermitte/gemini-mcp-example.git 
cd gemini-mcp-example
uv sync
source .venv/bin/activate
```

## Write MCP Server And Test

Here, we'll start the MCP server and send requests to it
via stdin directly into the terminal, watching the responses via stdout.

Roughly, we'll follow this (where we'll be "Gemini CLI"):

![sequence diagram](/images/2025_07_04_gemini_sequence.png)

### 1. Example File
The file we'll run is in [gemini-mcp-example/main.py](https://github.com/jrmlhermitte/gemini-mcp-example/blob/main/gemini-mcp-example/main.py) and already defined.
Take a look at it. The main components are 

```python
# ...
mcp = FastMCP("greeter")
# ...
@mcp.tool()
def greet(name: str) -> str:
    return f'Hello {name}!'
# ...
if __name__ == "__main__":
    # NOTE: stdio is the default.
    mcp.run(transport='stdio')
```

What's important here is that we initialize the MCP server with `mcp = FastMCP("greeter")`, define a tool and decorate it with `@mcp.tool()`,
and finally run this with `mcp.run()` with the `stdio` protocol.

This will start a server that will receive commands from stdin and output them
through stdout. The alternative is http but given we're running this locally,
this is simpler.

### 2. Run file

To better understand how this works, let's give this server a test run:
(**Don't forget** to activate your virtual env `source .venv/bin/activate`)

```bash
python gemini-mcp-example/main.py
```

### 3. Init communication

We're going to initialize the [2024-11-05](https://modelcontextprotocol.io/specification/2025-06-18/basic/lifecycle) protocol version using stdin/stdout (the `stdio` protocol which we setup our fast MCP server to use).

This works by basically send json strings back and forth. Let's test it out. Paste this exactly into your shell:

```json
{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2024-11-05","capabilities":{"roots":{"listChanged":true},"tools":{"listChanged":true},"sampling":{},"elicitation":{}},"clientInfo":{"name":"ExampleClient","title":"ExampleClientDisplayName","version":"1.0.0"}}}
```


You should see something like this:

```json
{"jsonrpc":"2.0","id":1,"result":{"protocolVersion":"2024-11-05","capabilities":{"experimental":{},"prompts":{"listChanged":false},"resources":{"subscribe":false,"listChanged":false},"tools":{"listChanged":false}},"serverInfo":{"name":"greeter","version":"1.10.1"}}}
```

**NOTE**: The json commands here and below must be pasted **as is**. You cannot have newlines in between. If the formatting is incorrect, the server will just ignore your requests.

When you do, paste this to start the connection:

```json
{"jsonrpc":"2.0","method":"notifications/initialized"}
```

You **must** follow with this command. Otherwise, the server will not finish the connection initialization (it's a requirement of the protocol).

Now type this to list available tools:

```json
{"jsonrpc":"2.0","method":"tools/list","id":1}
```

you should see something like this (you may see additional logging):

```json
{"jsonrpc":"2.0","id":1,"result":{"tools":[{"name":"greet","description":"","inputSchema":{"properties":{"name":{"title":"Name","type":"string"}},"required":["name"],"title":"greetArguments","type":"object"},"outputSchema":{"properties":{"result":{"title":"Result","type":"string"}},"required":["result"],"title":"greetOutput","type":"object"}}]}}
```

Congratulations! You have successfully started a Stdio connection with an MCP
server! Now test calling your tool:
```json
{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"greet","arguments":{"name":"Teal'c"}}}
```

you should then see:
```json
{"jsonrpc":"2.0","id":1,"result":{"content":[{"type":"text","text":"Hello Teal'c!"}],"structuredContent":{"result":"Hello Teal'c!"},"isError":false}}
```

This is how you're going to setup an MCP server with Gemini.

Gemini CLI will run your server as a child process
and send commands to stdin and receive responses from stdout using the stdio protocol.

#### Additional Challenge: Two Terminals (Linux Only)
Want to try this in separate terminals?

Just run the command with:
```
cat | python gemini-mcp-exampe/main.py
```

locate the PID with `ps uxaw | grep gemini-mcp-example`, and
send requests to `/proc/$PROC_PID/fd/0` and read responses from
`/proc/$PROC_PID/fd/1`. There are other ways but this will be the simplest to setup.

## Gemini CLI
Integrating with Gemini CLI.

You'll need to install some more tools: node and Gemini CLI itself.

### 1. Install Node
[Install node](https://nodejs.org/en/download)

### 2. Install Gemini CLI
[Install Gemini CLI](https://github.com/google-gemini/gemini-cli?tab=readme-ov-file):

```
npm install -g @google/gemini-cli
```

### 3. Gemini Extension

Now add the Gemini extension (see instructions [here](https://github.com/google-gemini/gemini-cli/blob/main/docs/extension.md)):

```
git clone https://github.com/jrmlhermitte/gemini-mcp-example.git
mkdir -p $PWD/gemini/extensions
ln -s $PWD/gemini-mcp-example ~/.gemini/extensions
```

This will clone the github repo containing the extension and soft link it into the extensions directory.

### 4. Start Everything

Start gemini and list mcp servers

```
gemini
```

Then type:
```
/mcp
```

You should see this:
![mcp](/images/gemini_mcp_command.png)


**NOTE**: You **must** start gemini from the code folder. The reason is that the
extension runs `python ./gemini-mcp-example/main.py`. If you want to make this runnable from everywhere, you'll need to make sure your base python environment contains the `fastmcp` library and that the `gemini-extension.json` refers to an absolute path.
**NOTE**: If this is your first time setting up Gemini CLI, you will also see some easy to follow setup steps.

### 5. Test Run
Give it your name. It will likely try to call your tools.

Input something like:
```
My name is Teal'c
```

Gemini should figure that it might want to call the greeting tool, given you've introduced yourself. You should get a request to call the tool:
![confirmation](/images/greet_request.png)

And it should hopefully have called the tool.
![tool_called](/images/greet_tool_called.png)


## Troubleshooting

Running into problems? Try running the mcp server yourself to see if it's able to start up:

```
source .venv/bin/activate
python gemini-extension/main.py
```

(Also don't forget to run `source .venv/bin/activate` before starting `gemini`; We're running this in a local virtual environment here.)

## Where to go from here?

This demonstrates how easy it is to setup an MCP server and integrate it with Gemini. You should be able to have a basic enough understanding to integrate it with your own tools now!

## Citation

```
@misc{jlhermitte2025geminimcp,
  title={MCP With Gemini},
  author={Julien R. Lhermitte},
  year={2025},
  howpublished={\url{https://jrmlhermitte.github.io/2025/07/03/gemini-mcp.html}}
}
```
