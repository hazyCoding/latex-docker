# tex-dockerfile

This repo contains the Dockerfile I use to run LaTeX (or specifically, XeLaTeX).

I run LaTeX in a Docker container so:

*   I don't have to muck around with installing MacTeX or TeX Live.
    I've had particular issues in the past when the year increments (e.g. 2018 to 2019), and suddenly `tlmgr` stops working.

*   I have consistent package installations across machines.

*   I can easily run LaTeX on a Linux server, which means I can compile LaTeX documents on my iPad over SSH.

The idea is that the base container should be pretty small, so it builds quickly, and then I add packages as needed with `tlmgr`.

## Usage

Build a Docker image from the Dockerfile:

```
docker build --tag hazycoding/latex . && docker push hazycoding/latex
```

To build a document, mount the folder containing the document at `/data` and run the Docker image, passing the name of the LaTeX binary and the source document as arguments.
For example:

```
docker run \
  --volume $(pwd):/data \
  hazycoding/latex \
  xelatex example.tex
```

## Adding fonts and packages

If you want to make extra fonts available to XeLaTeX, copy the font files (e.g. `.otf`, `.ttf`) into the `fonts` directory and rebuild the Docker image.

If you want to install extra packages, create a second Dockerfile and use `tlmgr` to install the packages in that image.
For example:

```dockerfile
# lastpage.Dockerfile
FROM hazycoding/latex

RUN tlmgr update --self && tlmgr install lastpage
```

```
docker build --tag hazycoding/latex-with-lastpage --file lastpage.Dockerfile .
```

To build documents with this image, use the new image name in the `docker run` command:

```
docker run \
  --volume $(pwd):/data \
  hazycoding/latex-with-lastpage \
  xelatex example.tex
```

## Licence

MIT.
