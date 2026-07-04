# Clusiau-Johnson Family Tree Viewer

[![Public Tree](https://img.shields.io/badge/public-tree-blue?logo=github)](https://genealogy-johnson.github.io/genealogy-tree/viewer/gedcom-viewer.html)
![People](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/genealogy-johnson/genealogy-tree/main/badges/people.json)
![Generations](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/genealogy-johnson/genealogy-tree/main/badges/generations.json)

[![Python 3.14+](https://img.shields.io/badge/python-3.14%2B-blue?logo=python)](https://github.com/genealogy-johnson/genealogy-tree/blob/main/pyproject.toml)
[![CI](https://github.com/genealogy-johnson/genealogy-tree/actions/workflows/ci-python-zensical.yml/badge.svg?branch=main)](https://github.com/genealogy-johnson/genealogy-tree/actions/workflows/ci-python-zensical.yml)
[![Links](https://github.com/genealogy-johnson/genealogy-tree/actions/workflows/links.yml/badge.svg?branch=main)](https://github.com/genealogy-johnson/genealogy-tree/actions/workflows/links.yml)
[![Dependabot](https://img.shields.io/badge/Dependabot-enabled-brightgreen.svg)](https://github.com/genealogy-johnson/genealogy-tree/security)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

> Clusiau-Johnson family tree viewer.

## Where to start

- **Browse the public tree:**
  View the redacted public tree at
  <https://genealogy-johnson.github.io/genealogy-tree/viewer/gedcom-viewer.html>

- **Documentation:**
  View the docs at
  <https://genealogy-johnson.github.io/genealogy-tree/>

- **GEDCOM source data:**
  See the private GEDCOM files in `ged/`.
  The hand-edited source of truth is `ged/data.ged`.
  Do not publish raw GEDCOM files directly unless they have been reviewed
  for living/private individuals.

## Developer

```shell
uv self update
uv python pin 3.14
uv lock --upgrade
uv sync --extra docs
```

## Examples

![Figure 1](./docs/images/Figure_1.png)

![Figure 2](./docs/images/Figure_2.png)

## Project Documentation

Additional documentation:

[docs/index.md](docs/index.md)

## Citation

[CITATION.cff](./CITATION.cff)

## License

[MIT](./LICENSE)
