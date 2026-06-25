# NumeRe::Packages
## The official package repository for NumeRe
This repository lists all officially published packages and plugins, which are available for NumeRe. It was migrated from SourceForge, because forking and creating pull requests makes it easier for the package authors to actually share their creations. NumeRe supports this repository starting with the full release of v1.1.8.

## Contributing
Because we want to ensure that all contributions are safe and have some kind of quality standard, we introduced the requirement for forking and the creation of pull requests as a desired friction. However, the process is not so different from Winget's approach, although there's more automation behind it. It goes as follows:

1. Fork this repository if you haven't already, otherwise sync your fork so that the main branch is up-to-date
2. Create a branch within your fork, i.e. `add_pkg_XYZ`
3. Push your package to your fork. Ensure that the folder structure is correct, i.e. `packages/pkg_XYZ/i.j.k/pkg_XYZ.nscr` with `i.j.k` referencing the version number of your package. We provide a dedicated package `pkg_packaging_tools` to simplify this process.
4. Create a pull request within this repository with which you intend to merge your branch
5. Respond to feedback
6. Smile, once the pull request is accepted and the package is available in the official repository (it may take up to 10min until NumeRe finds the new package)

Why do we enforce that the package is part of this repository? That's simply because we want to be sure what the users are downloading and that the contents of the referenced file match the reviewed file. That's different from Winget, but we do expect that this repository will stay a lot smaller in terms of contributions as well as byte size.

## Questions?
Please get in touch or open an issue. We will extend this readme during the resolution of your inquiry.

## Custom or private repositories
NumeRe supports (with the full release of v1.1.8) the usage of multiple package repositories, which can exist side-by-side. To be usable, the package repositories need to provide a REST API similar to the one here on GitHub. We can ensure that private and public repositories work on GitHub and Gitlab.

### Setting up the repository
To set up the repository, you need to decide, whether it's public or private. In the latter case, you need some kind of authentication. NumeRe supports (right now) only the HTTP header based authentication using access tokens, which is used by GitHub and Gitlab as well.

A package repository needs to provide the standard directory structure (it does not need to use git, only the REST API is important): The root folder in the repository needs to be `packages`. This folder shall contain a subfolder per package, which themselves will contain the version folders with the package and the `meta.json`.
```
packages/
    pkg_XYZ/
        i.j.k/
            pkg_XYZ.nscr
            meta.json
        n.m.o/
            ...
    pkg_ABC/
        ...
```
To make that easier for you, you can install the packaging tools (`install pkg_packaging_tools@NumeRe::Packages`) and use their functionalities to fill your repository with the necessary structure.

### Setting up NumeRe for the secondary repository
To make NumeRe find and understand the secondary package repository, you need to configure it using a `<>/remotes/*.repository` file (just a renamed JSON file). The naming convention defines also the priority, i.e. the package repositories are searched in alphabetical order, so if you want to have your repository a higher priority, give it a smaller number in the filename prefix (the default repository uses `10` for this exact purpose).

If your repository is a public one, we recommend that you add the corresponding `*.repository` either as a file or copyable from a `readme`, so that possible user can just take the configuration.

This section shows the contents of `10_numere_packages.repository`:
```json
{
    "version": "1.0.0",
    "name": "NumeRe::Packages",
    "url": "https://github.com/numere-org/NumeRe-Packages",
    "authentication": {
         "required": false,
         "method": ""
    },
    "keys": {
        "path" : "path",
        "sha": "sha",
        "root" : "tree"
    },
    "tree": "https://api.github.com/repos/numere-org/NumeRe-Packages/git/trees/HEAD?recursive=true",
    "raw-file": "https://raw.githubusercontent.com/numere-org/NumeRe-Packages/refs/heads/main/{path}"
}
```
To add a GitHub-hosted repo, just copy the contents of this JSON, change the three URLs (replace `numere-org/Packages` with the correspond URL sections for your repository) and adapt the authentication section, if your repo is private with `"required": true,` and `"method": "Authorization: Bearer ghp_YOURGITHUBTOKEN"`.

If you want to host the repository on Gitlab (or a self-hoste instance), then the following `*.repository` might be a good starting point (this one is a private one):
```json
{
    "version": "1.0.0",
    "name": "Gitlab repository",
    "url": "https://gitlab.com/my/Repo",
    "authentication": {
         "required": true,
         "method": "PRIVATE-TOKEN: <AUTHTOKEN>"
    },
    "keys": {
        "path" : "path",
        "sha": "id",
        "root" : null
    }
    "tree": "https://gitlab.com/api/v4/projects/42/repository/tree?recursive=true&per_page=100000",
    "raw-file": "https://gitlab.com/api/v4/projects/42/repository/blobs/{sha}/raw"
}
```
Note that the `42` in the paths stands for the repository ID not the repository name. You can copy the ID from your repository page. The `per_page=100000` limit is to allow a tree of identical size as on Github. However, it is not guaranteed that this limit will be supported by Gitlab in the future. If you face troubles, give us a hint.

#### Field documentation
The meaning of the fields in a `*.repository` configuration is as follows:
- `"version"`: defines the version of the `*.repository` file standard by itself. Is used for future compatibility.
- `"name"`: defines the package name used for uniquely identifying packages via `PKG_ID@name`
- `"url"`: this is the (browser) URL for the repository. Not used yet
- `"authentication"`: Defines settings for user authentication
    - `"required"`: Is an authentication required?
    - `"method"`: The HTTP header to be used for authenticating a user
- `"keys"`: defines translations keys from one REST API to the Github format
    - `"path"`: the JSON field for the path of the package in the repository
    - `"sha"`: the SHA of the file in the tree
    - `"root"`: defines the root element in the REST API response. GitHub uses `"tree"`, Gitlab doesn't have it at all
- `"tree"`: defines the REST API for obtaining the repository tree
- `"raw-file"`: defines the URL to be used to obtain the raw file from the repository. Can contain either the `{path}` or the `{sha}` placeholders, which will be replaced by the respective keys