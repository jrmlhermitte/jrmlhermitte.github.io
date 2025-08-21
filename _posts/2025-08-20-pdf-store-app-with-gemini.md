# Writing PDF Store App With Gemini

**TL;DR**: Wrote pdf store app with Gemini. Try it out here: <a
href="https://pdf-reader-app-latest.onrender.com/">https://pdf-reader-app-latest.onrender.com/</a>,
or scroll down to see screenshots.

## Motivation

I have been searching for an app that allows me to aggregate pdfs and annotate
them. I eventually [found
one](/2025/08/17/managing-papers-with-zotero-and-personal-file-server.html).
However, I thought it would be a nice opportunity to give Gemini a try. The goal
was less to develop a rigorous agent setup, but see how far an out of the box
agent cli could take me. I was impressed with how easy it was to have Gemini
kick start an MVP and let me focus on the interaction with the product.

All I did was install [Gemini CLI](https://github.com/google-gemini/gemini-cli)
and create an empty folder and start typing 6 letters:

```
$ gemini
```

Here are the highlights of my 1-2 hour interaction spread over 3 days:

### Day 1: From Scratch
- Write me a pdf uploader and viewer app. It created one that allows me to
provide pdf links, and lists the ones I have previously uploaded. Clicking on
them opens up a pdf viewer.
- Have it take an arXiv abstract link (ex: <a
href="https://arxiv.org/abs/2210.07897">https://arxiv.org/abs/2210.07897</a>)
and extract metadata and download the associated pdf. I also gave it these xpath selectors
[here](https://github.com/fivefilters/ftr-site-config/blob/master/arxiv.org.txt)
to extract metadata. I can now post arXiv links and have them show up.
- Add keyword search so I can easily (and simply) search for a paper.
- Render an image thumbnail of the pdf and add it to the list items
- Add highlighting to the pdf

After asking it back and forth and in between moving on to other things, I had a
working app that runs locally by running backend and frontend separately. It
felt like working with and checking in on a very knowledgeable colleague [^1].
Very promising.

[^1]: I want to add that such a statement does not imply agents will replace
    interns/junior engineers. **We absolutely need software engineering
    knowledge**. Just like we need to go to college to learn fundamental things
    we rarely use (e.g. Calculus for engineering), this will just be the same
    stepping stone interns/junior devs have always gone through except a bit
    more accelerated (i.e. when you master the foundations, you can build a
    larger project much quicker). As a matter of fact, I'm jealous because their
    careers will actually accelerate way faster!  :)

### Day 2: Add a feature to Existing App
- Add the ability to add scribble annotations to the pdf as a drawing overlaid on top of the pdf

Took a few tries and the feature isn't great (scribbles don't follow page), but
serves as a good MVP for future iteration.

### Day 3: Prepare for Deployment
- Add a dockerfile to allow me to deploy this app.

Gemini both wrote a Dockerfile and had the backend serve the frontend to make it easier to deploy.

### Day 4 (No Gemini): Manually Deploy to Render
I did some quick searches for places to manually deploy these days. Render
seemed like a good choice.

## The outcome

I deployed the app live at <a
href="https://pdf-reader-app-latest.onrender.com/">https://pdf-reader-app-latest.onrender.com/</a>.
Try it out. It's still barebones, but for very little time commitment, I think
it's off to a pretty good start.

**NOTE**: It will take about a minute to load and may not work if
there is high traffic; I'm using freebie quota.

You can find the code [here](https://github.com/jrmlhermitte/pdf_reader) [^2].

The app currently only has two views, list view and pdf view. 
If the app does not render, here is the list view (main page) of the app:

<figure>
<img src="/images/2025-08-20-pdf-reader-main-page.png">
<figcaption>List view of the pdf reader app.</figcaption>
</figure>

And finally, the pdf view:
<figure>
<img src="/images/2025-08-20-pdf-reader-pdf-view.png">
<figcaption>PDF view of the pdf reader app.</figcaption>
</figure>

[^2]: Warning: the code isn't the prettiest. I have avoided making any edits to
    try to preserve what a Gemini project would look like.

## Discussion

During this interaction, the majority of my time was towards thinking about how
to get the product working. In the past, I would have devoted more time towards
development (reading documentation, searching libraries, writing code).
I still did need to devote time to some debugging (read console log output, send
to Gemini, or in a small number of cases, search and fix it myself).

I have noticed a few other things I believe worth highlighting. Here are some
other takeaways:

1. **Gemini is a software overengineer**: Gemini will sometimes choose solution more complex
    than needed. For example, at first when I asked for Gemini to add highlighting,
    it wrote its own custom tool. It also kept making mistakes, getting the
    coordinate conversions wrong, and calling methods for converting them that don't
    exist. Surprised nothing existed, I did a simple search and found [this
    plugin](https://react-pdf-viewer.dev/plugins/highlight/).  I then insisted that
    it try to use it and it [eventually
    did](https://github.com/jrmlhermitte/pdf_reader/blob/56c5296860ccaf8aa68dd87402ba1b47153e49ee/frontend/src/PdfViewer.tsx#L5)
    (phew, because UI's are not my forte).
    <figure>
    <img src="/images/2025-08-20-engineer-engifar.jpeg" alt="engineer-engifar" width="200">
    <figcaption>
    Credit: <a href="https://github.com/google-gemini/gemini-cli">https://imgur.com/gallery/before-lock-yK9GRw8</a>
    </figcaption>
    </figure>
2. **Do not give Gemini git access**: Gemini sometimes made a bad change and
wanted to commit it over an uncommitted working change. I never allowed access
and simply committed when I felt the change was ready (and working).
3. **Be specific on the product goal, but loose on the implementation details**: This is
counter to what many people suggest and is generally good for apps where we are
past the MVP and want a specific implementation. However, for MVP's, you
generally don't know what you don't know. So let Gemini give you some creative
feedback by being vague where the implementation matters less for your intended
goal. For example, I wanted to add a drawing feature on top of the pdf viewer. I
didn't mention how the user would interact with it (should there be a toggle
button etc)? This let Gemini suggest the likely most common and reasonable
option which seemed good to me.
4. **Run the server and test it yourself** During prototyping, you don't really
know what you want. I advise against letting gemini try to run the server. Run
it yourself, see what errors Gemini runs into. Often, Gemini can get stuck
trying to run the server and examinig more output than it needs and this can
waste valuable tokens.
5. **Loops happen, ensure you have manual validation checkpoints**:
I had Gemini always ask me when it wanted to run the server. Most of the time I
would refuse and run it myself. I did this because I've seen Gemini get stuck
into loops. A common one has been to run the server, find a warning, introduce a
bad change to fix it, see the server fail, undo it, see the warning again, then
attempt to introduce the bad change again etc etc.
6. **Eventually do add unit tests**. This will allow gemini to partially validate its changes,
and avoid you from having to copy paste errors you see back at it. Gemini makes mistakes
like we all do. Unfortuantely, I did not do this here (1-2 hours is not much
time). I mainly write it because I did see Gemini run into simple errors that
resulted in the server no longer working that a unit test could have caught
earlier.
8. **For the simple things, it appears to follow some best practices**: I was impressed by the
[Dockerfile](https://github.com/jrmlhermitte/pdf_reader/blob/main/Dockerfile).
Gemini used staged builds, which means that the image was pretty lightweight and
didn't contain the npm toolset, which is unnecessary. The [compressed size](https://hub.docker.com/repository/docker/jrmlhermitte/pdf_reader_app/tags/latest/sha256-70cab5ec2e5895416652ab79958a311ca4dcb3104cc3f5ec5df48fc192acdb0f) of the image
is still 95MB, but this is a great start. 

## Conclusion
At each technological wave, we see ourselves transformed into a new work
dynamic. Although it seems these changes are happening on a faster scale to keep
up with, I believe there is one comfortable constant. To grow in our careers, we
must remain focused not on specific technological advances, but value
creation. What value do you create today? What would be the next step of your
career that would enable you to create more value than you are doing so now?
Gemini can probably help make it happen.


## Footnotes