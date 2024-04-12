## Quarto hybrid project

Add formatting for light/dark mode:

```
format:
  html: 
    theme: 
      light: flatly
      dark: darkly
```

Preview the quarto doc: 
```bash
quarto preview notebook.ipynb
quarto preview quarto-hybrid.qmd 
quarto preview /usr/home/lisa.anders/Demo/code_sandbox/Python/quarto-hybrid-poetry/quarto-hybrid.qmd --no-browser --no-watch-inputs
```

## Deploy

Overview: 

* Create and activate a virtual environment 
* Run the examples locally
* Acquire an [API key](https://docs.rstudio.com/connect/user/api-keys/) 
* Publish the examples with the [rsconnect cli](https://github.com/rstudio/rsconnect-python)
* Save the environment and deployment details for future git-backed publishing

```bash
rsconnect add \
    --api-key <MY-API-KEY> \
    --server <https://connect.example.org:3939> \
    --name <SERVER-NICKNAME>
```

Create the requirements file:
```bash
python -m pip freeze > requirements.txt
```

Create the manifest for future git-backed publishing:
```bash
rsconnect write-manifest api .
```

In order to deploy it needs to be a project: 
```bash
quarto create project 
```

Deploy it: 
```bash
rsconnect deploy quarto . <- doesn't work because of the r chunks needing knitr (could be deployed from rstudio though with push button)

quarto publish 
```

### Requirements.txt file

Write the requirements.txt file using pip: 

```bash
pip freeze > requirements.txt
```

## Environment management

Why not just tell people to use pyenv, poetry, or anaconda? <https://www.bitecode.dev/p/why-not-tell-people-to-simply-use> 

### venv (preferred)

```bash
$ cd flask-sentiment-analysis-api
$ python -m venv .venv
$ source .venv/bin/activate
$ source env/bin/activate
$ python -m pip install -U pip setuptools wheel
```

Use the requirements. txt file to install dependencies: 

```bash
$ python -m pip install -r requirements.txt
```

Write the requirements.txt file using pip: 

```bash
pip freeze > requirements.txt
```

Point pip to the repo you want to install from. For example to point at package manager: 

```bash
pip config set global.index-url https://packagemanager.posit.co/pypi/latest/simple
pip config set global.trusted-host packagemanager.posit.co
```

You can also point it to the desired repo from the requirements.txt file: 
```
--index-url https://packagemanager.rstudio.com/pypi/2022-09-13/simple
anyio==3.6.2
appnope==0.1.3
```

Point the requirements.txt file to install a [package from git](https://stackoverflow.com/questions/16584552/how-to-state-in-requirements-txt-a-direct-github-source): 
```
package-two @ git+https://github.com/owner/repo@41b95ec
package-two @ git+https://github.com/owner/repo@main
package-two @ git+https://github.com/owner/repo@0.1
package-two @ git+https://github.com/owner/repo@releases/tag/v3.7.1
```

### Poetry 

Sam's notes on poetry: <https://samedwardes.quarto.pub/using-poetry-for-python-dependency-management/> 
Add repository: <https://python-poetry.org/docs/repositories/>

Install poetry: 
```bash
curl -sSL https://install.python-poetry.org | python3 -
```

Note that you will [need ensurepip](https://stackoverflow.com/questions/29871372/i-have-python3-4-but-no-pip-or-ensurepip-is-something-wrong-with-my-python3-4): 
```bash
apt-get install python3-pip
```

```bash
poetry init \
    --no-interaction \
    --name my_app \
    --author "Lisa <lisamaeanders@gmail.com>" \
    --description "A poetry example"
```

In order to publish to Connect the requirements.txt file will [need to be exported](https://samedwardes.quarto.pub/using-poetry-for-python-dependency-management/#faq): 
```bash
poetry export --without-hashes --output requirements.txt
```

## Python 

If you want to explore an example more closely before deploying it:

* Clone this repository
* create a virtual environment in the folder you want to work in
* restore the needed packages into the virtual environment

```bash
$ cd flask-sentiment-analysis-api
$ python -m venv .venv
$ source .venv/bin/activate
$ source env/bin/activate
$ python -m pip install -U pip setuptools wheel
$ python -m pip install -r requirements.txt
```

For reticulated content, set the `RETICULATE_PYTHON` environment variable to point to your virtual environment, by placing an `.Renviron` file in the folder containing the following:

```
RETICULATE_PYTHON=.venv/bin/python
```





