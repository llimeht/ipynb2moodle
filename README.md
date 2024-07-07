# ipynb2moodle - render Jupyter notebooks for the Moodle Assignment marking interface

`ipynb2moodle` is a tool to help markers work with Jupyter notebooks in the
[Moodle Assignment activity](https://docs.moodle.org/404/en/Assignment_activity).
It is designed to let students submit their Jupyter notebooks and markers
view the student work using the standard marking interface of the Assignment
activity.


## Installation

This is (at present) just a simple script to run on the desktop.

 1. Download the Python script an put it somewhere sensible
    (e.g. `~/bin/` on a Linux machine)
 1. Install necessary tools
    - `jupyter-nbconvert`: e.g. `pip install nbconvert` or
       `apt install jupyter-nbconvert`
    - `wkhtmltoimage` from `wkhtmltopdf` e.g.
       [from their downloads](https://wkhtmltopdf.org/downloads.html) or
       `apt install wkhtmltopdf`
    - `imagemagick` for the `convert` command, e.g.
       [from their downloads](https://imagemagick.org/script/download.php) or
       `apt install imagemagick`
 1. Configure imagemagick to deal with larger files, on Debian, that means
    editing `/etc/ImageMagick-6/policy.xml` to include the following items:

```
        <policy domain="resource" name="width" value="16KP"/>
        <policy domain="resource" name="height" value="40KP"/>
```


## Usage

Steps to use `ipynb2moodle`

 1. From the Assignment tool, go to View Submissions
 1. Ensure "Download all submissions in folders" is ticked
 1. Download all submissions, saving the zip file
 1. Run `ipynb2moodle download.zip`
 1. Wait a while... it's not fast. Check the output to see that conversion
    completed successfully.
 1. Upload the newly created `uploadable.zip` file
 1. Optionally, clean up the temporary PDF and PNG files that were created


## Design

The theory of operation for the tool is as follows:

 1. Jupyter notebooks are downloaded from Moodle as a zip file.
 1. Each notebook is rendered to a PNG and a PDF.
    - Conversion goes via an HTML view of the notebook rather than
      directly to PDF with `nbconvert`.
    - The notebook is rendered to a single (very tall) image.
    - The PDF is the artefact that will be marked by the marker in Moodle.
    - The PNG is uploaded as a "preview" image which speeds up the rendering
      of the page in Moodle.
 1. Each converted notebook along with its preview image is uploaded back to
    the same Moodle assignment tool using the bulk upload of submissions
    option.
 1. The Assignment marking tool will show the PDF (via its PNG preview) in
    the marking view. It will also show a little message saying that it
    doesn't know how to show the Jupyter notebook to the marker, but that
    can be ignored since the conversion has been performed.

Rendering via HTML then to PNG and PDF is an unusual strategy - after lots
of experimentation this was found to be the best approach:

 - There is no pagination of the notebook so it looks better on screen.
 - The Moodle marking interface doesn't support continuous scrolling of
    pages, so pagination is annoying there too.
 - The Moodle marking interface is going to rasterise the output anyway,
    so there is no loss in quality by rasterising it during conversion
 - `nbconvert`'s HTML output looks like the original notebook (and the PDF
    output does not), which is better for students and markers.
    In particular, the HTML output
    - has better syntax highlighting
    - doesn't depend on LaTeX
    - avoids lots of potential issues with LaTeX choking on the Markdown
      sections of the notebooks.


## Contributions

Contributions to this project are most welcome!
Contributions to the code or documentation can be made as pull requests
against this repository.
Bug reports, feature requests and suggestions for improvement can be made
as issues.
The items in the Roadmap might offer some thoughts about contributions you
could make!


## Roadmap / to-do list

 - add a test suite with CI
 - refactoring the tool so that it can operate both on a zip file from
   Moodle and on a single Jupyter notebook
 - move from imagemagick to a different PDF generator (and check compatibility with imagemagick 7)
 - ensure that mathjax rendering works correctly
 - PowerAutomate/SharePoint integration for non-desktop workflow`


## Licence

`ipynb2moodle` is available under the 3-clause BSD licence.
