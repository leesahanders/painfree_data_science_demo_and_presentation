# It's dangerous to go alone - take this! Lisa's best practices for pain-free data science

> **Warning**
> This is part of a repository that is prone to changes, and overhauls will happen without notice, but feel free to reach out with any questions/corrections/or help needs at lisamaeanders@gmail.com

**Access the presentation slides at: <> or at <>.**


## Resources

- Happy git with R: <https://github.com/jennybc/happy-git-with-r> 
- Oh sh*t git: <https://ohshitgit.com/> 
- git illustrations: <https://github.com/allisonhorst/stats-illustrations> 
- renv example from the VA Dept. Environmental Quality: <https://github.com/JosiahParry/renv-example>
- Version control and RStudio with NHS-R Community: <https://www.youtube.com/watch?v=qNMOPWT8jSo> 
- You should be using renv: <https://www.youtube.com/watch?v=GwVx_pf2uz4> 
- First steps in learning the use of git and github: <https://www.youtube.com/watch?v=jN6tvgt3GK8&pp=ygUUZ2l0aHViIHBvc2l0IHdlYmluYXI%3D> 
- Cheat sheet for git (contributed): <https://rstudio.github.io/cheatsheets/contributed-cheatsheets.html>
- Integrating git with Connect: <https://docs.posit.co/connect/admin/integrations/version-control/> 
- Version control with git and github: <https://support.posit.co/hc/en-us/articles/200532077-Version-Control-with-Git-and-SVN> 
- Webinar on version control and RStudio: <https://nhsrcommunity.com/events/may-2023-webinar-version-control-and-rstudio-ryan-johnson-data-science-advisor-posit/> 
- Collaboration and time travel version control with git, github, and rstudio: <https://posit.co/resources/videos/collaboration-and-time-travel-version-control-with-git-github-and-rstudio/> 
- Missing semester of your CS education on version control: <https://missing.csail.mit.edu/2020/version-control/> 
- Ryan's recording (internal) for RStudio and Version control workshop: <https://positpbc.zoom.us/rec/share/RUgM2UCR8WvEWyNH8ahacc6mOjuBa8fDTHR0CbgNHeIv2g1tPWss3vmFCh6cE24P.-FnnzKHXJexBtYMU?pwd=kMQ8vVBrqg8T8dcbEMsAhTOvggKNcNG1>
- Learn git branching: <https://learngitbranching.js.org/> 
- The tidyverse code styling guide: <https://style.tidyverse.org/> 
- The hitchhikers guide to Python code styling: <https://docs.python-guide.org/writing/style/> 
- Google code style guides: <https://google.github.io/styleguide/> 

## Troubleshooting 

```r
library(usethis)
library(gitcreds)
library(gh)
library(credentials)

usethis::gh_token_help()

usethis::git_sitrep()

gh::gh_whoami()

```

## Git file locking 

- Gitlab: <https://software.rcc.uchicago.edu/git/help/user/project/file_lock.md> 
- Github: <https://github.blog/2017-03-02-git-lfs-2-0-0-released/> 


## Version Control

*Topics:*

-   Cloning down a repository
-   Keeping repositories up to date
-   Collaboration using version control (branches, blame, etc)
-   Advanced

```
library(usethis)
library(gitcreds)
library(gh)
library(credentials)
```

On a cloud instance check that is is writing to somewhere persistent, IE into /home/ and not into /opt/. Find where your library is:

```
.libPaths()
```

Check the git configuration:

``` bash
git config --list 
```

Check if an existing repository was cloned with ssh or https:

``` bash
git remote show origin
```

You can check that SSH keys exist and the name with:

```         
ls ~/.ssh

ssh-keygen -p
```

Check for any pre-existing credentials:

```
gh::gh_whoami()
```

Remove any pre-existing credentials:

```
gitcreds::gitcreds_delete()
```

Check that R is reading the correct configuration information. It's possible to do this with gitcreds:

```
gitcreds::gitcreds_list_helpers()
```

### Change where git credentials are stored

This can be useful on a cloud compute instance. For Sagemaker for example git tokens will save to the local transient instance. When the compute session is closed your tokens will be closed and lost. Instead change the location for where the credentials are stored so that they are saving to your home directory, which is mounted persistent storage.

``` bash
git config --global credential.helper 'store --file ~/.my-credentials'
```

### PAT

```
usethis::create_github_token()
```

I highly recommend selecting "repo", "user", and "workflow".

Copy the generated PAT to your clipboard. Provide this PAT next time a Git operation asks for your password OR store the PAT explicitly.

```
gitcreds::gitcreds_set()
```

For example, you can store a PAT for a GitHub Enterprise deployment like so:

```
gitcreds::gitcreds_set("https://github.acme.com")
```

Add your email if needed:

```
git config --global user.email "lisa.anders@posit.co"
```

Check that it stored with:

```
gitcreds_get()
```

#### Store credentials: gitcreds R environment

> Recommendation: Do not store your PAT(s) in plain text in, e.g., .Renviron. In the past, this has been a common and recommended practice for pragmatic reasons. However, gitcreds/gh have now evolved to the point where it's possible for all of us to follow better security practices.

```
usethis::edit_r_environ()
```

Add a line like this, but substitute your PAT:

```         
GITHUB_PAT=ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

Restart your R session so the credentials are applied. In my experience this method works in Sagemaker if the project is closed and then a new session with a new ec2 instance has been started.

#### Clone project

Use the HTTPS address for opening a new project, either through the UI or programmatically:

```
usethis::create_from_github(
  "https://github.com/YOU/YOUR_REPO.git",
  destdir = "~/path/to/where/you/want/the/local/repo/"
)
```

### SSH key

From RStudio:

-   Go to Tools -\> Global Options -\> Git / SVN
-   Create SSH Key
-   Approve the key and add a password (if appropriate)
-   View Public Key
-   Copy that public key that was provided into the SSH and GPG keys section in your git under your profile settings.

You'll copy / update code using the `SSH` method from git.

From terminal:

-   Generate a new key with: `ssh-keygen -t ed25519 -C "your_email@example.com"`
-   Add your ssh key to the background client: `eval "$(ssh-agent -s)"` `ssh-add ~/.ssh/id_ed25519`
-   Or find an existing key with: `ls -al ~/.ssh`
-   Copy the key to your clipboard: `clip < ~/.ssh/id_ed25519.pub`

Tip: With Windows Subsystem for Linux (WSL), you can use clip.exe. Otherwise if clip isn't working, you can locate the hidden .ssh folder, open the file in your favorite text editor, and copy it to your clipboard.

Follow [here](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account) to add it to your github account.

How to remove / add a password: <https://stackoverflow.com/questions/112396/how-do-i-remove-the-passphrase-for-the-ssh-key-without-having-to-create-a-new-ke>

You may also need to configure some global options:

```         
git config --global user.name "leesahanders"
git config --global user.email "lisamaeanders@gmail.com"
git config --global --list
```

You can check that SSH keys exist and the name with:

```         
ls ~/.ssh

ssh-keygen -p
```

```         
eval "$(ssh-agent -s)"
ssh-add /root/.ssh/id_ed25519
```

Use the SSH address to clone new projects. This also works with using git clone from the terminal.

### Switching to a git backed project that was shared with you

If switching to a project that was shared with you need to run: `git config --global --add safe.directory /usr/home/xu.fei/shiny-test1`

And then close and reopen the session.

This command tells your session to consider the shared directory as safe and allow it to be opened. Since personal credentials are cached, it will not be detected as a git project nor will you be able to push changes back to the source. Instead coordinate with the owner for any version control.

### Troubleshooting

```
usethis::gh_token_help()

usethis::git_sitrep()

gh::gh_whoami()
```

> git push \# fatal: The current branch foo has no upstream branch. \# To push the current branch and set the remote as upstream, use \# \# git push --set-upstream origin foo

You can do as the error message says and explicitly set the upstream branch with --set-upstream. However I would recommend instead changing the default behavior of push to automatically set the upstream branch to the branch with the same name on the remote.

([reference](https://happygitwithr.com/git-remotes#upstream-tracking-branches))

``` bash
git config --global push.default current
```

> "Unable to Fork"

This can happen if you're using ssh to fetch the remote and you don't have ssh installed. However note that support for password authentication was removed on August 13, 2021 from Workbench. This means that one of the methods above needs to be done with http instead of using ssh as part of the built in UI for git cloning.

## Renv

*Topics:*

-   Initializing a project
-   Adding / updating packages
-   Reverting
-   Setting up a project using renv on another machine

*Resources:*

-   Renv: <https://rstudio.github.io/renv/articles/renv.html>
-   There is an excellent video by David Aja discussing why he started using renv at the 2022 RStudio Conference here: <https://www.rstudio.com/conference/2022/talks/you-should-use-renv/>

*What is renv*

Ever had your code mysteriously stop working or start producing different results after upgrading packages, and had to spend hours debugging to find which package was the culprit? Ever tried to collaborate on code just to get stuck on trying to decipher various package dependencies?

renv helps you track and control package changes - making it easy to revert back if you need to. It works with your current methods of installing packages (install.packages()), and was designed to work with most data science workflows.

*Terms*

-   R Project - a special kind of directory of files and supporting functionality.
-   Package - a collection of functions beyond base R that developers can install and use.
-   Library - a directory containing installed packages.

*Workflow*

New project -\> updates -\> reverting -\> advanced

*New project*

Initialize your project with:

```         
library(renv)
renv::init()
```

Look at the renv.lock file and see the information that has been captured about the packages supporting your project.

*Making updates*

Add / change the packages being called in the project:

```
install.packages("arrow")
library(arrow)
library(rsconnect)
```

Renv will detect that packages have been added when you check the current status with `renv::status()`

Update the lock file with `renv::snapshot()`

The renv lock file is updated by you when you run the command to snapshot. This means you can update packages, or install new packages, without changing your lock file.

Install an old version of a package:

```
renv::install("devtools") # Shorter than install.packages("devtools")
library(devtools)
devtools::install_version('connectapi', '0.1.1.1')
library(connectapi)
```

Update everything to the latest for each package (according to the repository you are pointed at) with:

`renv::update()`

Remove packages that are no longer needed with:

`renv::clean()`

*How to revert*

We can easily revert back the current session. Install another old package, this time using renv for the version:

```
# install digest 0.6.19
renv::install("digest@0.6.19")

# save library state to lockfile
renv::snapshot()

# remove digest from library
renv::remove("digest")
```

We can revert back (to the current commit and re-add digest\@0.6.19) with:

```
## Revert to the most recent snapshot 
renv::revert()
```

If you want to revert back to an earlier snapshot saved into git you can do that with:

```
renv::revert(commit = commit)
```

See the commit history with: `renv::history()`

Update your session for the correct versions to be pulled in.

Practice updating the packages your project relies on, each time saving to git. Note: Currently, only Git repositories are supported by renv::history() and renv::revert().

*Setting up projects that already have renv*

This is easy, just run this command after cloning the project down and your environment will match the environment in the lock file: `renv::restore()`

It might also be good to make sure that you've updated to the latest versions of all packages if that aligns with your package management strategy prior to making any code changes: \`renv::update()\`\`

*Advanced renv*

There are a couple other commands that can be used depending on your workflow that are useful to know about.

If you need to upgrade renv use this command: `renv::upgrade()`

Say that your organization has a managed package host, for example Package Manager, and is using the [Shared Baseline](strategy). Meaning that all developers in the organization are pointed to a snapshot of available packages frozen to a particular date when the managing team had intentionally tested and made them available. On some cadence, let's say quarterly, the managing team goes through, performs testing again, and provides a new updated snapshot that is available for developers to switch to. There are a lot of advantages in switching with new features, resolved bugs, etc.

In order for developers to know that a new release has happened (that the local package dates are out of date in reference to the repo being pointed at) they can run:

```         
view(old.packages())
```

The same process would be followed with then updating the packages, testing as needed, and then creating a new snapshot so the renv lock file is updated to the latest. This provides the added security that in case it does not pass testing the renv lock file will still point at the supported package versions and not the ones that caused the breakage.

Retrieve the latest-available packages from a set of package repositories:

```         
# supply the full repository path
renv::checkout(repos = "https://packagemanager.rstudio.com/cran/2023-01-02")

# only check out some subset of packages (and their recursive dependencies)
renv::checkout(packages = "dplyr", date = "2023-01-02")
```

Update the in-memory cache of packages from the active R package repository:

```         
# check available packages 
available.packages()

# refresh the local available packages database (the old locally cached db will be removed)
renv::refresh()
```

One of the more recent releases of the renv package has included [having separate profiles for the project](https://rstudio.github.io/renv/articles/profiles.html). Meaning that a developer could have a production profile, a validation profile, and testing profile that can be easily switched between while working through their workflow towards deployment into production.

*Troubleshooting*

The renv.lock file can be manually changed to update the packages that are included with:

```         
renv::modify()
```

If you are having particular issue with a package and it keeps being pulled in from the cache then doing a complete purge and reinstall can be useful:

``` r
renv::purge("stringr")
renv::purge("stringi")
install.packages("stringr")
```

`renv::purge` removes packages completely from the package cache (which may be shared across projects) rather than just removing the package from the project which is what `renv::remove` does. This can be useful if a package which had previously been installed in the cache has become corrupted or unusable, and needs to be re-installed.

It may also be useful to verify both the OS you are currently useing as well as checking that the repository you are pointing towards is using the correct OS if it is pulling in the binaries.

For debian/ubuntu distributions:

``` bash
lsb_release -a
```

For other distributions (more broadly cross-linux compatible command):

``` bash
cat /etc/os-release
```

Check the repository being pointed to and update it to use the URL from your package manager instance:

``` r
options('repos')
options(repos = c(REPO_NAME = "https://packagemanager.posit.co/cran/__linux__/jammy/latest"))
```

Check your current repo with: `options('repos')`

For example, you might see: `https://packagemanager.rstudio.com/all/latest` or `https://cran.rstudio.com/`.

Change your repo with: `options(repos = c(REPO_NAME = "https://colorado.rstudio.com/rspm/cran/__linux__/focal/2022-06-29"))` or `options(repos = c(REPO_NAME = "https://packagemanager.rstudio.com/all/latest"))`

If using a shared cache location issues may arise around permissions for installing / changing packages installed. From renv:

> By default, packages copied into the cache will remain "owned" by the user that requested installation of that package. If you'd like renv to instead re-assign ownership of the cached package to a separate user account, you can set the RENV_CACHE_USER environment variable. When set, renv will attempt to run chown -R <package> <user> to update cache ownership after the package has been copied into the cache.

For example by saving it at the project level into the .Renviron file:

```         
library(usethis)
usethis::edit_r_environ(scope = "project")
```

Look up your user name from terminal with: `whoami`

Check file permissions from terminal with: `ls -l` or see the whole tree with `namei -l /usr/home/lisa.anders/Development/renv_version_control_demo/renv/library/R-4.2/x86_64-pc-linux-gnu/arrow`

Error messages:

> renv took longer than expected (11 seconds) to activate the sandbox.

Use `usethis::edit_r_environ(scope = c("user"))` to open and edit the `.Renviron` file to add RENV_CONFIG_SANDBOX_ENABLED=FALSE. Restart session.

## Manifest

*Topics:*

-   Create a manifest so Connect can replicate your environment

*Resources:*

-   rsconnect: <https://github.com/rstudio/rsconnect>
-   git issue discussing the quarto path error: <https://github.com/rstudio/rsconnect/issues/658>

*Quarto Manifests trick:*

The trick I found was to use this command for writing the manifest (that way it is pointing explicitly at your quarto version, fixed with (this git issue)\[https://github.com/rstudio/rsconnect/issues/658\]:

``` r
rsconnect::writeManifest(quarto = Sys.which("quarto"), appPrimaryDoc = "quarto-test.qmd")
```

After setting the work directory to the appropriate folder:

``` r
setwd("/usr/home/lisa.anders/Development/renv_version_control_demo")
```

The manifest should declare the details on the environment, including R version, quarto version, package versions, etc:

```         
{
  "version": 1,
  "locale": "en_US",
  "platform": "4.2.3",
  "metadata": {
    "appmode": "quarto-static",
    "primary_rmd": "quarto-test-manifest.qmd",
    "primary_html": null,
    "content_category": null,
    "has_parameters": false
  },
  "quarto": {
    "version": "1.3.340",
    "engines": ["knitr"]
  },
  "packages": {
    "R6": {
      "Source": "CRAN",
      "Repository": "https://cloud.r-project.org",
      "description": {
        "Package": "R6",
        "Title": "Encapsulated Classes with Reference Semantics",
        "Version": "2.5.1",
        "Authors@R": "person(\"Winston\", \"Chang\", role = c(\"aut\", \"cre\"), email = \"winston@stdout.org\")",
        "Description": "Creates classes with reference semantics, similar to R's built-in\n    reference classes. Compared to reference classes, R6 classes are simpler\n    and lighter-weight, and they are not built on S4 classes so they do not\n    require the methods package. These classes allow public and private\n    members, and they support inheritance, even when the classes are defined in\n    different packages.",
        "Depends": "R (>= 3.0)",
        "Suggests": "testthat, pryr",
        "License": "MIT + file LICENSE",
        "URL": "https://r6.r-lib.org, https://github.com/r-lib/R6/",
        "BugReports": "https://github.com/r-lib/R6/issues",
        "RoxygenNote": "7.1.1",
        "NeedsCompilation": "no",
        "Packaged": "2021-08-06 20:18:46 UTC; winston",
        "Author": "Winston Chang [aut, cre]",
        "Maintainer": "Winston Chang <winston@stdout.org>",
        "Repository": "RSPM",
        "Date/Publication": "2021-08-19 14:00:05 UTC",
        "Encoding": "UTF-8",
        "Built": "R 4.2.0; ; 2022-04-22 20:24:04 UTC; unix"
      }
    },
```

The renv generated .Rprofile file should not be included in deployments to RStudio Connect.

## Environment Variables

When working with pulling data from secure databases or other sources a developer might find themselves in a situation of needing to provide very sensitive information, such as a password or a token, in order to access the data that is needed or to successfully deploy a project. Handling those secrets in way that doesn't expose them in the code directly is critical and where using [environmental variable's for securing sensitive variables](https://db.rstudio.com/best-practices/deployment/) is strongly recommended.

Additionally there may be parameters that are often needed that can be accessed as a variable more easily rather than having to type in every time.

For both of these cases knowing how environment variables can be leveraged can be very rewarding and it is surprising how little effort it can to take to set up.

### Working with the .Renviron file

When R starts it loads a bunch of variables, settings, and configs for the user. This is really powerful and some of the magic for how it can work so apparently seamlessly.

However for power users we can leverage these behind the scenes config files so that we can include such things as variables in our project without including it in our code. The .Renviron file is the one most commonly interacted with for adding sensitive variables to a project in order to protect them from being exposed in the code.

With increased use of these behind the scenes config files and the growing direction of arranging code into projects there was the development of giving, on startup, having multiple options for each config file that can be loaded depending on what the user specifies. Broadly speaking there are two levels of config files: - User - Project

On startup, since R is trying to make things as seamless as possible for the user, it will use some logic to figure out which config to use. It will assume that if a project level config exists it should load that one (and not any others). If that project level config doesn't exist, then it will default to the user level config. For more details on the different config files and the nuances see [Managing R with .Rprofile, .Renviron, Rprofile.site, Renviron.site, rsession.conf, and repos.conf](https://support.rstudio.com/hc/en-us/articles/360047157094-Managing-R-with-Rprofile-Renviron-Rprofile-site-Renviron-site-rsession-conf-and-repos-conf).

Just to re-iterate the key takeaway: When in doubt note that the **project level file is given preference over user level config files**. Only if the project level config file doesn't exist will the user level file be sourced/pulled in.

There is a really excellent [overview of R's startup process here](https://rviews.rstudio.com/2017/04/19/r-for-enterprise-understanding-r-s-startup/) but in short it can be thought of this way:

#### Example with a user level .Renviron config file

usethis has a function for creating and editing the .Renviron file

```         
library(usethis)
usethis::edit_r_environ()
```

Add the variables to that file in the format `variable_name = "variable_value"` and save it. Restart the session so the new environment variables will be loaded with ctrl shift f10 or through the RStudio IDE

Saved variables can be accessed with:

```         
variable_name <- Sys.getenv("variable_name")
```

When working in a more complex environment structure where separate project, site, and user environments are being support [this support article has useful information](https://support.rstudio.com/hc/en-us/articles/360047157094-Managing-R-with-Rprofile-Renviron-Rprofile-site-Renviron-site-rsession-conf-and-repos-conf) with a [deeper dive into R's startup here](https://rviews.rstudio.com/2017/04/19/r-for-enterprise-understanding-r-s-startup/).

#### Example with a project level .Renviron config file

Storing secrets securely can be done using the [edit_r_environ function from the usethis package](https://usethis.r-lib.org/reference/edit.html). For more overview see [this overview](https://support.rstudio.com/hc/en-us/articles/360047157094-Managing-R-with-Rprofile-Renviron-Rprofile-site-Renviron-site-rsession-conf-and-repos-conf).

Example:

```         
library(usethis)
usethis::edit_r_environ(scope = "project")
```

Accessing those stored parameters later can be done using `Sys.getenv("DB_NAME")`.

Be sure to add the project level .Renviron file to your .gitignore so you aren't exposing secrets when code is being saved to your git repository. Similarly this can be done with the `edit_git_ignore(scope = c("user", "project"))` function. For more best practices see [securing credentials](https://db.rstudio.com/best-practices/managing-credentials).

-   While typically explicitly listing the file name is the desired addition, wildcards can be added to exclude a type of file. For example: `*.html`.

After updating these files the project should be closed and re-opened for any additions to be pulled in. One way to do this is through session -\> restart R (ctrl-shift-f10).

#### Example with project level github secrets for environment variables

Another approach, particularly useful when automating testing and deployments using github actions, is to include the environment variables as secrets. Once this has been added through the git UI for the project they can then be referenced in the relevant deployment .yaml file with something like `CONNECT_ENV_SET_ZD_USER: ${{ secrets.ZD_USER }}`. In the R scripts they will be referenced as usual with something like `Sys.getenv("DB_NAME")`.

#### References for adding environment variables through the Connect UI

Starting with version 1.6, RStudio Connect allows [Environment Variables](https://docs.rstudio.com/connect/admin/security-and-auditing/#application-environment-variables). The variables are encrypted on-disk, and in-memory.

This can be done at the project level with [securing deployment](https://db.rstudio.com/best-practices/deployment/) through the [Connect UI](https://support.rstudio.com/hc/en-us/articles/228272368-Managing-your-content-in-RStudio-Connect).


## System dependencies 

### Pak 

<https://pak.r-lib.org/reference/pkg_deps.html?q=dependenc#null> 

Michael has an example here: <https://github.com/michaelmayer2/pak-singularity-example/> 

### Posit Package Manager (free)

<https://docs.posit.co/rspm/user/packages/viewing-package/> 

### Posit Package Manager (paid for)

<https://docs.posit.co/rspm/admin/appendix/system-dependency-detection/> 



