# Packages
## The official package repository for NumeRe 
This repository lists all officially published packages and plugins, which are available for NumeRe. It was migrated from SourceForge, because forking and creating pull requests makes it easier for the package authors to actually share their creations.

## Contributing
Because we want to ensure that all contributions are safe and have some kind of quality standard, we introduced the requirement for forking and the creation of pull requests as a desired friction. However, the process is not so different from Winget's approach, although there's more automation behind it. It goes as follows:

1. Fork this repository if you haven't already, otherwise sync your fork so that the main branch is up-to-date
2. Create a branch within your fork, i.e. `add_pkg_XYZ`
3. Push your package to your fork. Ensure that the folder structure is correct, i.e. `packages/pkg_XYZ/x.y.z/pkg_XYZ.nscr` with `x.y.z` referencing the version number of your package. We provide a dedicated package `pkg_packaging_tools` to simplify this process.
4. Create a pull request within this repository with which you intend to merge your branch
5. Respond to feedback
6. Smile, once the pull request is accepted and the package is available in the official repository (it may take up to 10min until NumeRe finds the new package)

Why do we enforce that the package is part of this repository? That's simply because we want to be sure what the users are downloading and that the contents of the referenced file match the reviewed file. That's different from Winget, but we do expect that this repository will stay a lot smaller in terms of contributions as well as byte size.

## Questions?
Please get in touch or open an issue. We will extend this readme during the resolution of your inquiry.
