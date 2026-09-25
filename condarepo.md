# Conda + Poetry project template

Conda manages the Python version; [Poetry](poetry.md) manages the packages.

## Set your environment name first
Type your environment (and Python package) name once. Every command below uses it, so you can copy and paste them as-is. Run this again in any new terminal.

Linux / macOS:
```bash
export ENV_NAME=my_env
```

Windows (Command Prompt):
```bat
set ENV_NAME=my_env
```

## Installing Miniconda
Follow the instructions at https://docs.anaconda.com/miniconda/

## First-time setup
Create and activate the environment, with Poetry installed inside it.

Linux / macOS:
```bash
conda create -n "$ENV_NAME" python=3.10 ipython
conda activate "$ENV_NAME"
conda install poetry
```

Windows (Command Prompt):
```bat
conda create -n %ENV_NAME% python=3.10 ipython
conda activate %ENV_NAME%
conda install poetry
```

Export the environment to a YAML file so it can be recreated later.

Linux / macOS:
```bash
conda env export -n "$ENV_NAME" -f environment.yml --no-builds
```

Windows (Command Prompt):
```bat
conda env export -n %ENV_NAME% -f environment.yml --no-builds
```

Set up the Poetry project.

Linux / macOS:
```bash
poetry init
mkdir -p tests "$ENV_NAME"
touch tests/__init__.py "$ENV_NAME/__init__.py"
```

Windows (Command Prompt):
```bat
poetry init
mkdir tests %ENV_NAME%
type nul > tests\__init__.py
type nul > %ENV_NAME%\__init__.py
```

## Setting up on a new machine
After installing Conda and cloning the repo, create the environment from `environment.yml` (this can take several minutes), then activate it.

Linux / macOS:
```bash
conda env create -f environment.yml
conda activate "$ENV_NAME"
```

Windows (Command Prompt):
```bat
conda env create -f environment.yml
conda activate %ENV_NAME%
```

Then, from the folder containing `pyproject.toml`, install the packages:
```bash
poetry install
```
or, with pip:
```bash
python -m pip install .
```

## Removing an environment
Linux / macOS:
```bash
conda remove --name "$ENV_NAME" --all
```

Windows (Command Prompt):
```bat
conda remove --name %ENV_NAME% --all
```

## Resources
- [Conda cheat sheet](https://docs.conda.io/projects/conda/en/latest/user-guide/cheatsheet.html)
- [Poetry basic usage](https://python-poetry.org/docs/basic-usage/)
