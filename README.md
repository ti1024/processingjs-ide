# Processing.js IDE

This is a project to create a lightweight web-based IDE for
[Processing.js](http://processingjs.org/).

The target audience are students in Japan who participate in introductory
programming workshops, having the first ever experience of text-based
programming. For this reason the main focus areas of this project are:

*   Syntax checker.

*   Online / inline help system.

*   Translation of the documentation to Japanese.

Currently this project includes the local version of HTML files for quick
development and a very small application to serve the static HTML file
of the IDE from Google App Engine.

## Getting started (locally)

Install the dependencies using npm

    npm install

Open the file `ide.html` in browser.

## Building the project with Bazel

Currently the project is in half-way state of transition
to Bazel-based build system. So parts of the code (including
the unprocessed `ide.html`) still rely on the modules
installed with `npm install`, and parts of the code
(especially rewritten `ide-bin.html` that is intended
to be served as part of App Engine application) are
using the files produced with the Bazel build.
So you need to run both:

    npm install
    bazel build ...

Note, that you need a fairly recent verion of Bazel (tested with 0.16.1).

## Deploy to Google App Engine

First you need to download and install Google Cloud SDK and install Go App
Engine component (app-engine-go). Then you need to create a project using Cloud
Console
([instruction](https://cloud.google.com/appengine/docs/standard/go/quickstart))
and enable App Engine on it, and configure the default project for gcloud.

    gcloud init

Second you need to build the static assets using combination of NPM and Bazel:

    npm install
    bazel build ...

Finally you may deploy your project:

    cd appengine && gcloud app deploy

## Online help system details.

The in-browser IDE is a single static HTML file that has some simple
single-page app functionality, e.g. it builds a dictionary of help
sections and shows them on demand in the help pane.

The source data for the online help system is checked in in the
markdown format in the docs/ subdirectory. There are a few conventions
on top of the standard Markdown syntax.

### Markdown source structure

The source file is split into pages using H1 markers (single #) as
separators.  The H1 element itself is currently included into the text of the
page, but this may change. Every page gets an identifier, either by taking
the H1 title prepended with `"ref-"`, or specified explicitly in the form of
`{#ref-topic}` markdown ID annotations on H1 elements. The convention is to
have all IDs to start with `"ref-"` prefix, in order to avoid potential
conflicts with IDs of other HTML elements inside `ide.html`.

### Links

The intra-helppage links may be specified in two ways:

* `[topic]` --- as a name of the topic (without the `"ref-"` prefix). As a
  small convenience for referring to function names, the pair of parentheses
  is automatically stripped from the link target, so `[topic()]` would refer
  to the same help page with the id `ref-topic`, but will include empty
  parentheses pair in the rendered link text.

* `[text][topic]` --- as a decorated link, where `text` will be rendered
   as a link title, and the link will point to the page with the ID
   `"ref-topic"`. This is useful for translating pages into non-English
   languages.

### Code snippets

There are 4 ways to render code snippets:

* The default format, produced by 4-space indented code snippets
  or by triple-tick fenced code blocks without language specifier.
  These code blocks will have a default markdown code block rendering
  without any extras.

* Triple-tick fenced code blocks annotated with `example` language
  specifier will get a button added automatically. If the user pushes
  the button, the fenced code is copied to the IDE source area.
  This is intended for complete runnable examples.

* Triple-tick fenced code blocks annotated with `prerender` language
  specifier will get a similar button, and additionally will also 
  get a rendered sketch floating in the right-hand side of the block.

* Triple-tick fenced code blocks annotated with `hidden` language
  specifier will get a load button, and will be put into a collapsible
  zipper that is closed by default.

## License

Apache-2.0; see [LICENSE](LICENSE) for details.

## Disclaimer

This project is not an official Google project. It is not supported by Google
and Google specifically disclaims all warranties as to its quality,
merchantability, or fitness for a particular purpose.
