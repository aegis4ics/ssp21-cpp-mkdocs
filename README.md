# ssp21-cpp-mkdocs
Documentation for SSP21-CPP library using [MkDocs](https://www.mkdocs.org/)

# building the docs

Install MkDocs using the instructions in the [website](https://www.mkdocs.org/).

Install the markdown extra data plugin:

```
pip install mkdocs-markdownextradata-plugin
```

You can then build a static version of the docs using:

```
mkdocs build
```

Live editing can be done with: (localhost:8000)

```
mkdocs serve
```