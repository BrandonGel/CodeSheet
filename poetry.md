# Poetry cheat sheet

## Setting up a project
In an existing folder (`ENV_NAME` is your package name):
```bash
poetry init
mkdir -p tests ENV_NAME
touch tests/__init__.py ENV_NAME/__init__.py
```
Or create the whole layout in a new folder:
```bash
poetry new ENV_NAME
```

## Adding packages
```bash
poetry add numpy scipy matplotlib
```

## Local packages
Add under `[tool.poetry.dependencies]` in `pyproject.toml`, where `PACKAGE_NAME` is the package and `DIRECTORY` is the folder containing it:
```toml
PACKAGE_NAME = { path = "DIRECTORY/PACKAGE_NAME/" }
```

## Optional packages (extras)
Mark the package as optional, then list it in an extra so it is only installed when asked for:
```toml
[tool.poetry.dependencies]
robomimic = { version = "*", optional = true }

[tool.poetry.extras]
dev = ["robomimic"]
```
```bash
poetry install --extras dev
```

## Filtering pytest warnings
```toml
[tool.pytest.ini_options]
filterwarnings = [
  "error",
  "ignore::UserWarning",
  "ignore:.*:DeprecationWarning",
]
```

## Installing from a custom source
For example, PyTorch built for CUDA 12.1:
```bash
poetry source add --priority=supplemental torch https://download.pytorch.org/whl/cu121
poetry add --source torch torch==2.3.1
```

## Installing without dependencies
Installs only the current package, skipping everything it depends on (plain pip, not Poetry):
```bash
pip install . --no-deps
```

## Resources
- [Poetry basic usage](https://python-poetry.org/docs/basic-usage/)
