# Setting up Python in VSCode

## Rationale ✏️

This sets up a simple python repository with a few handy tools to make Python development (a little) smoother.

## Getting stared 🚀

### Prerequisites 🧱

To get the most out of this setup you need to install

* [VSCode editor](https://code.visualstudio.com/Download)
* [Python 3.8+](https://www.python.org/)
* [Poetry for python](https://python-poetry.org/)
* [Docker and docker-compose (Optional)](https://www.docker.com/)
* [Pyenv (Optional)](https://github.com/pyenv/pyenv)

>_Docker_ and _docker-compose_ is only necessary if one plans to utilize VSCode _devcontainers_

## Project structure 🧭

The structure is fairly simple. In this section we focus on the files directly related to setting up/configuring the development environment.

1. Editor settings are in ```.vscode/settings.json``` referencing ```setup.cfg``` as the ground truth.
2. Dependencies are defined ```pyproject.toml``` which replaces ```requirements.txt``` AND the ```setup.py```
3. Githooks are installed with ```.pre-commit-config.yaml``` to keep garbage out of the git tree.
4. The  content of ```.devcontainer/``` is auto detected by VSCode and can spin up containerized environment

```bash
├── README.md
├── .vscode
|   └── settings.json (1)  
├── .devcontainer (4)
├── python_package
├── tests
├── pyproject.toml (2)
├── poetry.lock (2)
├── .gitignore
├── .pre-commit-config.yaml (3)
└── setup.cfg (1)
```

## Dependencies and building 🕸️

Instead of installing packages with ```pip```, keeping track of them with ```requirements.txt``` and build with ```setup.py``` this project utilize ```poetry``` bundled with ```pyproject.toml```. Poetry is both a build and dependency tool which in many ways is comparable with ``` npm ```.

>If you haven't already - here is the [guide to install poetry](https://python-poetry.org/docs/#installation)

### Installing the project

When the project is defined by a  ```pyproject.toml``` it can easily be installed with the ```poetry``` cli. This will create a virtual environment, install all dependencies and dev-tools to this environment and add the source folder to the ```PYTHONPATH```.

```bash
poetry install
```

```pyproject.toml``` is compatible with ```pip``` and can be installed by running:

```bash
pip install .
```

### Locking down dependencies

The true power of ```poetry``` lies in resolving dependency conflicts and locking these down. This way an identical working version of the project can be installed across environments.

When adding or removing dependencies from the project a ```poetry.lock``` file is generated/altered. This file **should** be checked into the repository.  

>The ```pyproject.toml``` file can be edited directly or altered with the ```poetry``` CLI. [The CLI documentation can be found here](https://python-poetry.org/docs/cli/)

### Building

Since ```pyproject.toml``` replaces ```setup.py``` project can easily be built by running:

```bash
poetry build
```
Poetry will then build wheels and by default add them to a ```dist``` folder in the project root.

## Linting 🔎

### Autoformatting in VSCode

### Linting in VSCode

VSCode support the python language with really good intellisense support through the Python extension(s). Lately there have also been created extensions for the most popular linters. These can also be used by installing them in the Python environment (we will in fact do both).

For an enhanced coding experience:

* [Black](https://black.readthedocs.io/en/stable/) will autoformat the code (on save)
* [Isort](https://pycqa.github.io/isort/) will reorder the imports (on save)
* [Flake8](https://flake8.pycqa.org/en/latest/#) enforces pep8 coding standard
* [Pylint](https://pylint.pycqa.org/en/latest/) removes pesky bugs
* [Pydocstyle](http://www.pydocstyle.org/en/stable/) make sure we write docstrings
* [Autodocstring](https://marketplace.visualstudio.com/items?itemName=njpwerner.autodocstring) Helps us create docstring templates (and have type hint support)


The configuration of the linters are set in ```setup.cfg``` and ```pyproject.toml```. Ideally we would have _one_ config file but ```flake8``` and ```black``` are mutually exclusive and do not support one common config file (yet). The linters are also managed by ```.vscode/settings.json```.

>For VSCode these linters are actual extensions with bundled executables and _should_ be faster then envoking linters installed with the python interpeter. To use the linters installed with the interpeter set  
```importStrategy``` to ```fromEnvironment```

### Linting with pre-commit

Linting (and other code quality measures) are enforced on us by [pre-commit](https://pre-commit.com/#intro), a tool for creating [githooks](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks). In this setup these hooks will be run either before ```commit``` or ```push```. A list of available pre-commit hooks can be found [here :-)](https://pre-commit.com/hooks.html)

>Look at pre-commit as your personal CI-pipline helping you to remember agreed coding styles and preventing annoying mistakes and subsequent fixes ever reaching your source tree. This "pipeline" is declared in ```pre-commit-config.yaml```.

To add the ```git-hooks``` declared in ```.pre-commit-config.yaml``` run the following command:

```bash
pre-commit install
```

The hooks are now installed and most of them will be run every time you try to ```commit``` to you local branch. A few are ```on push``` only.

If you need _really_ to check in some code and ```pre-commit``` prevents you can run the following to override the hooks. Although tempting this is not recommended.

```bash
pre-commit commit -m "commit message" --no-verify
```

## Testing 👷

The Python extension in VSCode comes with support of several testing frameworks. Here the choice fell on ```pytest``` and ```pytest-cov``` (the coverage plugin for ```pytest```).

### Configuring pytest

Pytest is mainly configured through the ```setup.cfg``` file which specifies the location to the ```tests``` directory. In the ```.vscode/settings.json``` the pytest support of VSCode is setup with _autoDiscoverOnSave_ which enables VSCode to discover the included tests. Handy 👋

### Running tests

To run the tests in VSCode you can either:

#### Run tests from the GUI

...by going to the ```tests``` pane or opening an actual test file like ```package_test.py``` and go to a specific test. Next to the test an icon should appear that one can click to run the specific test! This requires that the tests are discovered.

>Tests should have been auto discovered by VSCode - but if they're not showing - try running
> ```Python: Configure Tests``` in the VSCode command palette 🎨.

#### Run tests from the command line

Tests can always be run in the terminal by typing:

```python
pytest tests
```

### Debugging tests

One of the nicest features with VSCode and testing is the ability to easily debug tests. In the world of test driven development (TDD) this is really useful!

To debug a specific test - right-click the "Run" icon nest to the test and select ```debug test``` from the drop down. Add a break point in the test and step through your code!

### Coverage

Test coverage is currently not well supported in VSCode. Although there are a few extensions available (a bit less then 1m downloads). In this setup we choose to do coverage the old fashion way!

This means generating a coverage report using [coverage](https://coverage.readthedocs.io/en/7.0.1/).

#### Generate a coverage report

To generate a coverage report run the following command in the terminal:

```python
pytest --cov-report term-missing --cov=python_package tests
```

To generate an interactive coverage report in html simply run:

```python
pytest --cov-report html --cov=python_package tests
```


## Devcontainer 🛸

Devcontainers are one of the most powerful features of VSCode. Instead of going round "helping" everyone with environment related issues we can package everything into a docker-container and spin it up!
Did an intentional or unintentional change happen to you environment? No worries - check in intentional changes and distribute faster than I can say "git commit" or rebuild the entire environment with a click of a button!


The devcontainer files sit in ```.devcontainer``` folder and consist of

```bash
├── Dockerfile(1)
├── devcontainer.json(2)
├── docker-compose.yaml(3)
└── postCreateCommand.sh(4)
```
1. The Dockerfile that defines the environment. Build your own or there is [a variaty to choose from here](https://hub.docker.com/_/microsoft-vscode-devcontainers)
2. ```devcontainer.json```- The file that configures the environment!
3. ```docker-compose.yaml```- I have chosen to extend ```.devcontainer.json```with a docker-compose file. This allows to easily extend the environment with supporting services such as a database or a S3 mock.
4. ```postCreateCommand.sh``` is a script that is run after the environment is built the first time! Good place to add user specific actions like installing pre-commit hooks or python dependencies.



[Follow this excellent guide to get going!](https://code.visualstudio.com/docs/devcontainers/containers)

There is not much more to add other than:

1. Open the repository root!
2. Windows users - you **must** use this in context of WSL2. Its both faster, simpler and less error-prone! ([and strongly recommended!](https://code.visualstudio.com/docs/devcontainers/containers#_open-a-wsl-2-folder-in-a-container-on-windows))
3. Press ```ctrl/command+shift+p``` and type ```"open folder in container"```
4. Sit back and relax while the environment is spun up 🚀🚀🚀!
