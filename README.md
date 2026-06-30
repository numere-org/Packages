# NumeRe::Packages
## The official and central package repository for NumeRe
This repository lists all officially published packages and plugins, which are available for NumeRe. It was migrated from SourceForge, because forking and creating pull requests makes it easier for the package authors to actually share their creations. NumeRe supports this repository starting with the full release of v1.1.8 (executing `version() > 118.2605` will return `true` if you run this or a higher version).

## License
Although the package repository is flagged as licensed under the terms of Apache-2.0, each package may be shared with a separate and even more restrictive license. Ensure that you read and understand the conditions of said license before installing any packages.

## Guarantee Disclaimer
Unless required by applicable law or agreed to in writing, NumeRe.org provides the Work (and each Contributor provides its Contributions) on an **"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND**, either express or implied, including, without limitation, any warranties or conditions of **TITLE, NON-INFRINGEMENT, MERCHANTABILITY**, or **FITNESS FOR A PARTICULAR PURPOSE**. You are solely responsible for determining the appropriateness of using or redistributing the Work and assume any risks associated with Your exercise of permissions under this License.

## Contributing
Please read the [Contribution Guideline](https://github.com/numere-org/NumeRe-Packages/blob/main/CONTRIBUTING.md).

## Questions?
Please get in touch or open an issue. We will extend this readme during the resolution of your inquiry.

# Advanced Topics
This section is about advanced topics, which are not relevant for every user.

## Custom or private repositories
Especially in an enterprise context, where the intellectual property topic may be challenging, not all code might be allowed to leave the company's IT infrastructure. Contributing packages with such code snippets won't be allowed. Therefore, it is necessary to have a separate package repository especially for and governed by the company.

NumeRe supports (with the full release of v1.1.8) the usage of multiple package repositories, which can co-exist side-by-side. To be usable, the package repositories need to provide a REST API similar to the one here on GitHub. We can ensure that private and public repositories work on GitHub and Gitlab.

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

If your repository is a public one, we recommend that you add the corresponding `*.repository` either as a file or make copyable from a `readme`, to make it easy for possible users to configure NumeRe for your repository.

#### GitHub as Host
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
To add a GitHub-hosted repo, just copy the contents of this JSON, change the three URLs (replace `numere-org/Packages` with the correspond URL sections for your repository) and adapt the authentication section, if your repo is private, with `"required": true,` and `"method": "Authorization: Bearer ghp_YOURGITHUBTOKEN"`.

#### Gitlab as Host
If you want to host the repository on Gitlab (or a self-hosted instance), then the following `*.repository` might be a good starting point (this one is a private one):
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

#### Other Hosts
Other hosts than GitHub or Gitlab may not work, although their API might be similar to GitHub's (Bitbucket seems to be such a candidate). Do not hesitate to ask for assistance in spinning your repository up and make it accessible. This service is free for individuals. Enterprise users may get in touch to clarify licensing, warranty and compensation.

#### Field documentation
The meaning of the fields in a `*.repository` configuration is as follows:
- `"version"`: defines the version of the `*.repository` file standard by itself. Is used for future compatibility.
- `"name"`: defines the package name used for uniquely identifying packages via `PKG_ID@name`
- `"url"`: this is the (browser) URL for the repository. Not used yet
- `"authentication"`: Defines settings for user authentication
    - `"required"`: Is an authentication required?
    - `"method"`: The HTTP header to be used for authenticating a user. Authenticated users are also less likely to experience a rate limit
- `"keys"`: defines translations keys from one REST API to the Github format
    - `"path"`: the JSON field for the path of the package in the repository
    - `"sha"`: the SHA of the file in the tree
    - `"root"`: defines the root element in the REST API response. GitHub uses `"tree"`, Gitlab doesn't have it at all
- `"tree"`: defines the REST API for obtaining the repository tree
- `"raw-file"`: defines the URL to be used to obtain the raw file from the repository. Can contain either the `{path}` or the `{sha}` placeholders, which will be replaced by the respective keys