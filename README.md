# ssp21-cpp-mkdocs
Documentation for SSP21-CPP library using [MkDocs](https://www.mkdocs.org/)

# building the docs

After installing MkDocs, install the [mkdocs-markdownextradata-plugin](https://github.com/rosscdh/mkdocs-markdownextradata-plugin) and the [Material](https://squidfunk.github.io/mkdocs-material/) theme:

```
> pip install mkdocs-markdownextradata-plugin
> pip install mkdocs-material
```

You can then build a static version of the docs using:

```
mkdocs build
```

Live editing can be done with: (localhost:8000)

```
mkdocs serve
```