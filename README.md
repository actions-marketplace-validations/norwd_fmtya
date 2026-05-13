# fmtya

GitHub Action to run [yamlfmt]

<img src="https://codeberg.org/norwd/human/raw/branch/main/docs/automatic-logo.svg" height="50" />

## Usage

### Basic Setup

The following is a reasonable, bare-minimum, setup.
It runs `fmtya` with all the settings set to their default values.
This will run `yamlfmt` on the repository and if any yaml files are reformatted, it will commit them back to the branch that was pushed.

Note that if the default token is used, GitHub Action workflow files cannot be formatted.
This is due to a limitation in GitHub's permissions system. See the [advanced setup] example to see how to format workflow files.

```yaml
---

name: "Format yaml files"

on:
  push:

jobs:
  yamlfmt:
    runs-on: ubuntu-latest
    steps:
      - uses: norwd/fmtya@v1
        with:
          exclude-files: '.github/workflows/*.{yaml,yml}'
```

### Advanced Setup

A more advanced setup can customise everything from the commit's message down to the specific version of the `yamlfmt` backend!
The configuration options `fmtya` uses are loosely based on the [`.yamlfmt`] file format.

```yaml
---

name: "Format yaml files"

on:
  push:

jobs:
  yamlfmt:
    runs-on: ubuntu-latest
    steps:
      - uses: norwd/fmtya@v1
        with:

          # Due to how GitHub's permissions system is set up, the default token
          # does not have the necessary access to update workflow files. If you
          # want to want `fmtya` to format the files in the `.github/workflows`
          # directory, you will need to set up a PAT with at least write access
          # to both the `repo` and `workflows` permissions.
          token: ${{ secrets.<YOUR_PAT> }}

          # By default, `fmtya` uses the latest available version of `yamlfmt`.
          # This may result changes to the behaviour of `fmtya`, for example if
          # a major version of `yamlfmt` is released. If you prefer to manually
          # select a specific version of `yamlfmt`, any released version can be 
          # pecifically requested.
          yamlfmt-version: vX.Y.Z

          # If there are files in your repo that follow a different convention,
          # are auto-generated, or you just don't want to format for any reason
          # at all, you can exclude specific files, or only selectively include
          # certain files. The patterns are parsed by `yamlfmt`, and internally
          # use https://github.com/bmatcuk/doublestar.
          exclude-files: test/data/*.{yaml,yml}
          include-files: |
            **/*.{yaml,yml}
            .yamlfmt

          # Specific formatting options to configure the size of indents, or if
          # yaml anchors or aliases should or shouldn't be allowed, can be used
          # to control how yamlfmt formats the files in your repository.
          indent-size: 2
          line-ending-type: crlf
          include-document-start: false
          keep-line-breaks: false
          disallow-anchors: true

          # If the default commit message is too generic, or you want to create
          # a custom commit message, for example, from the output of a previous
          # workflow step, you can overwrite the commit message to any string.
          commit-message: Reformat .yml files with yamlfmt

          # When the formatting changes are made, they are committed by the bot
          # account for GitHub Actions, and whoever pushed the unformatted file
          # is listed as a co-author. To attribute the commit to a real person,
          # or if you want to sign the commit, you can specify the username and
          # email the same way you would to `git config user.name <USERNAME>`.
          commit-user-name: <YOUR_GITHUB_USERNAME>
          commit-user-email: <YOUR_GITHUB_EMAIL

          # Not to be confused with signing a commit with the GPG key provided,
          # a true boolean value for `signoff-on-commit` will add the `-s` flag
          # to the commit command. This appends the `Signed-off-by` trailler to
          # the commit message, certifying the commit.
          #
          # See also: http://developercertificate.org
          signoff-on-commit: yes

          # By default, the commits are not signed, which may make them show as
          # unverified if any of the co-authors have vigilant mode turned on in
          # their GitHub account settings. You can enable signed commits with a
          # GPG private key stored in a secret, just be sure that the email set
          # in the GPG key matches the email you set in `commit-user-email`!
          signing-private-key: ${{ secrets.<YOUR_GPG_SIGNING_KEY> }}
          signing-passphrase: ${{ secrets.<YOUR_GPG_PASSWORD> }}
```

[yamlfmt]: https://github.com/google/yamlfmt
[advanced setup]: #advanced-setup
[`.yamlfmt`]: https://github.com/google/yamlfmt#configuration

# I'm Using GitHub Under Protest

My projects are currently hosted on GitHub.
This is not ideal; GitHub is a proprietary, trade-secret system that is not Free and Open Source Software (FOSS).
I am deeply concerned about using a proprietary system like GitHub to develop my FOSS projects.
I have an [open discussion][Leaving GitHub Discussion] where I am planning how to move away from GitHub in the long term.
I urge you to read about the [Give up GitHub] campaign from the [Software Freedom Conservancy] to understand some of the reasons why GitHub is not a good place to host FOSS projects.

If you are a contributor who personally has already quit using GitHub, please [check this resource][Leaving GitHub Discussion] for how to send in contributions without using GitHub directly.

Any use of my projects' code by GitHub Copilot, past or present, is done without my permission.
I do not consent to GitHub's use of any of my projects' code in Copilot.

![Logo of the GiveUpGitHub campaign](https://sfconservancy.org/static/img/GiveUpGitHub.png)

[Leaving GitHub Discussion]: https://github.com/norwd/norwd/discussions/7
[Give up GitHub]: https://GiveUpGitHub.org
[Software Freedom Conservancy]: https://sfconservancy.org
