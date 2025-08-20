# Managing Papers With Zotero For Free Using Caddy And Tailscale

**TL;DR**: I've chosen Zotero for papers as it has very nice annotation
capabilities. 99% of this post shows you how to link your own **secure** storage
server to store the pdfs and annotations.

<figure>
<img src="/images/2025-08-17-zotero-example.png" alt="zotero-example" width="300"/>
<figcaption>Example annotations using Zotero of <a href="https://dl.acm.org/doi/10.1145/279227.279229">The Part-time Parliament</a>. Note these annotations are as serious as Leslie Lamport showing up in an <a href="https://lamport.azurewebsites.net/pubs/pubs.html">Indiana Jones outfit</a>.</figcaption>
</figure>


## Motivation
I try read a lot of papers to try to stay up to date on recent work and also as
a potential source of new ideas. Reading papers is a nice low-overhead way to
have a challenging dialogue with the authors, which can often lead to gaining a
new perspective, and sometimes coming up with a new idea you or the authors
would not have imagined. Often, when reading papers, I'm on the go and rarely
have my laptop with me. I'll have either a tablet if I'm lucky, or likely simply
my phone in a crowded subway. I need a solution that lets me keep track of what
I'm reading and switch between devices easily.

<figure>
<img src="/images/2025-08-17-random-scribbles.png" alt="random-scribbles" width="300"/>
<figcaption>Sample annotation of <a href="https://lamport.azurewebsites.net/pubs/time-clocks.pdf">
Time, Clocks, and the Ordering of Events in a Distributed System
</a> through Apple Books.
</figcaption>
</figure>

I have been keeping it embarrassingly simple and using Apple Books as my viewer
and annotator, and icloud as my storage. However, I have grown to the point where I
have so many it's hard to keep track and search through previous papers. It also
doesn't help that the number of papers published per month grows exponentially,
with [arXiv for example](https://arxiv.org/stats/monthly_submissions) almost
doubling in 2020 (at about 14000) to 24000 in 2025:
![arvxiv graph](/images/2025-08-17-arxiv-graph.png).

I'm currently at 432 pdfs totalling at 2.4GB with this file size distribution (note the x-axis is logarithmic, in powers of two) [^1]:

![file sizes](/images/2025-08-17-file-sizes.png)

This led to the need for a better solution. After doing some reading, I have
found that [Zotero](https://www.zotero.org/) meets all my needs. However, you
still have to [pay for storage](https://www.zotero.org/storage). I'm not sure
I'm ready to do that yet as I'm not sure how much I want to commit to Zotero yet
so I wanted to find a better alternative. I found Zotero does allow for using an
alternate storage option so long as it supports the
[WebDAV](https://en.wikipedia.org/wiki/WebDAV) protocol. As a matter of fact,
they list free providers
[here](https://www.zotero.org/support/kb/webdav_services). However, the storage
options are still a bit small for me, especially since I'm already at 2.4GB of
mostly papers. Some are also not in the US, which I would hesitate.

Given it's pretty easy to switch storage servers with Zotero (so I can change my
mind later), I decided instead to run this out of my home server for now.
However, doing so poses great risk and most of the work in getting this done
right has nothing to do with the webserver itself but the security around it.
This blog post will show you how to accomplish this.

[^1]: Yes, there are large pdfs, for example, [Nymeria: A Massive Collection of Multimodal Egocentric Daily Motion in the Wild](https://arxiv.org/abs/2406.09905) is a 50MB pdf, or one of my favorites, this 29MB pdf [Generating Physically Stable and Buildable Brick Structures from Text](https://arxiv.org/abs/2505.05469). The top 4 are actually textbooks, with the largest at 152MB.

### Step 1: Choose Your Server

Before you begin, obviously, you need a server. It can be as simple as a raspberry pi connected to a 64GB usb drive, up to you.

### Step 2: Install Tailscale On All Your Connected Devices

Get [tailscale](https://tailscale.com/). It's free and quite powerful.  You'll
need it installed on both the device that hosts the webDAV server and the
devices that connect to it.

On your tailscale network, your devices will have names like `machine-name.tailnet-name.ts.net`.

**TIP**: I recommend naming your devices something simple, like `workstation` or
`raspberrypi`. Try avoiding personally identifiable information like `johndoe`
as this information will appear in a public ledger of https certificates (see
[here](https://tailscale.com/kb/1153/enabling-https#configure-https)).

### Step 3: Get an HTTPS certificate for your server

This only needs to be run on the server that will host the webdav service.
1. Enable https certificates for your tailnet
[here](https://tailscale.com/kb/1153/enabling-https).

2. On the machine that will serve the files, run:

```
tailscale cert
```

to get your machine name, then 
```
sudo tailscale cert machine-name.tailnet-name.ts.net
```

with the machine name and tailnet name you got from the previous step
information.

NOTE: This will only create a cert once, which at the time of this writing, are
valid for 90 days. When the cert expires, you can either call the `tailscale
cert` command again or optionally, you can install [this extension](https://github.com/tailscale/caddy-tailscale).


### Step 4: Lock Down Your Server
**DO NOT SKIP THIS STEP**

Running a server that accepts requests from anyone, even if a local network is
extremely risky. Fortunately, since we're using our tailscale VPN which is
already doing the security heavy lifting, we don't actually have to worry about
that. We can simply add firewall rules that only accepts incoming requests from
the VPN itself. See [this
document](https://tailscale.com/kb/1077/secure-server-ubuntu) for more details.

It will recommend using [UFW](https://help.ubuntu.com/community/UFW) (Uncomplicated Firewall) and the steps are:

**1. Enable the firewall**
  ```
  sudo ufw enable
  ```

**2. Deny everything incoming**

  ```
  # deny all incoming by default
  sudo ufw default deny incoming
  sudo ufw default allow outgoing
  ```

**3. List all rules (to find the tailscale one)**

  ```
  sudo ufw status verbose
  ```

**4. Allow connections from the tailscale vpn (which should be `tailscale0`)**

  ```
  sudo ufw allow in on tailscale0
  ```

**Restart everything**

  ```
  sudo ufw reload
  sudo service ssh restart
  ```

**NOTE**: Please do not skip this step unless you absolutely know what you're
doing. The web is dangerous.

### Step 4: Install caddy

You can find instructions [here](https://caddyserver.com/docs/install).
For example for ubuntu: `sudo apt install caddy`.

### Step 5: Allow caddy access to your tailscale certificate


In the configuration file `/etc/default/tailscaled` for the tailscale daemon, add this line:
```
TS_PERMIT_CERT_UID=caddy
```

This will give the `caddy` user (which caddy should run as) permission to access the certificates from tailscale. Interestingly, caddy out of the box knows to try to do this when it sees domains that end in `*.ts.net`, so nothing else needs to be done.

After this edit, make sure to restart the daemon. For example, if using `systemd` you would run:

```
sudo systemctl restart tailscaled
```

### Step 6: Add webdav plugin to caddy

```
sudo caddy add-package github.com/mholt/caddy-webdav
```

(From instructions [here](https://marko.euptera.com/posts/caddy-webdav.html)).

### Step 7: Edit your caddy file

Caddy's configuration file is located in `/etc/caddy/Caddyfile`.
Add these lines to it:

```
# configure webdav module
{
    order webdav before file_server
}

# add webdav.
# (Note the 443 is optional as it's the default, but I find it clearer)
machine-name.tailnet-name.ts.net:443 {
  # set up webdav for the host
  handle_path /webdav/* {
    root * /data/webdav
    webdav
    basicauth {
        user some-password-hash
    }
  }
}
```

replace `some-password-hash` with the hash of your desired password. You can get it by running:

```
caddy hash-password
```

and pasting that output.

**NOTE**: The password protection is not necessary for security. It is only here
to help prevent accidental requests into the webdav server which are pretty easy
to make.

(Again from instructions [here](https://marko.euptera.com/posts/caddy-webdav.html)).

NOTE: You can remove the nested  `handle_path` and simply have this if you prefer:
```
machine-name.tailnet-name.ts.net {
  root * /data/webdav
  webdav
  basicauth {
      user some-password-hash
  }
}
```

I'm using the
[handle_path](https://caddyserver.com/docs/caddyfile/directives/handle_path)
directive so that the address to my webdav server will be
https://machine-name.tailnet-name.ts.net/webdav and not
https://machine-name.tailnet-name.ts.net. I do this because my https server
points to multiple servers (webdav is one, but
[code-server](https://github.com/coder/code-server) is another).

### Step 8: Restart Caddy

```
sudo systemctl restart caddy
```

### Step 9: Test your webdav connection

Just listing should be fine:

```
$ curl -X PROPFIND -u "user:pass" https://my-machine.tailnet-name.ts.net/webdav/
```

(or remove the `webdav/` suffix if you didn't use the `handle_path` directive).

### Step 10: Add this to Zotero

For example:

![img](/images/2025-08-17-zotero-webdav-example.png)


This should work just fine across all your devices!

## Discussion

### Why Caddy?
The goto choice for a webdav server tends to be
[apache2](https://httpd.apache.org/docs/current/mod/mod_dav.html). I only chose
caddy since it has a built in integration with tailscale certificates.

## Questions/Comments?
If you've ever written your own instructions for anyone, you'll know very well
that it's extremely difficult to get this with all the right details right so
that it works for most people without sinking a lot of time into it.

If something doesn't work for you or seems confusing, please add any questions
or comments in the comments section below. These are rough guidelines of what
worked for me, but it's possible I missed some detail that seemed obvious to me
but may not be obvious to someone with less context.

If you have a better suggestion for how to organize research papers as well, please share!

Thank you!

## Future Plans

I would love to have a general queue of to-read items that include links to the
web and papers (while still keeping the advanced annotations and reference
tracking that Zotero provides for papers). I love
[pocket](https://getpocket.com/home) but unfortunately it shut down.
Fortunately, it looks like a [few
alternatives](https://www.reddit.com/r/selfhosted/comments/1ktgzpf/pocket_is_shutting_down_here_are_5_open_source/)
have popped up, one that seems to be the very promising is
[readeck](https://readeck.org/en/). It looks like someone requested [zotero
integration](https://codeberg.org/readeck/readeck/issues/268). Perhaps in the
future this might be a nice way to integrate a generalized reading queue.
