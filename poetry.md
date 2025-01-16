# A  list of package for poetry to install from.

For setting up poetry:
```
poetry init
mkdir tests
touch test/__init__.py
mkdir ENV_NAME
cd  "$(\ls -1dt ./*/ | head -n 1)"
touch __init__.py
cd ..
```
or 
```
poetry new ENV_NAME
```


For installing packages:
```
poetry add numpy scipy matplotlib 
```

For installing local packages under [tool.poetry.dependencies] where ```PACKAGE_NAME``` is the name of the package and ```DIRECTORY``` is the directory:
```
PACKAGE_NAME = { path = "DIRECTORY/PACKAGE_NAME/" }
```

For installing packages once:
```
[tool.poetry.extras]
dev = ["robomimic"]
```

For filter unnecessary texts:
```
[tool.pytest.ini_options]
filterwarnings = [
  "error",
  "ignore::UserWarning",
  "ignore:.*:DeprecationWarning",
]
```

For installing libraries with a source or website:
```
poetry source add --priority=supplemental torch https://download.pytorch.org/whl/cu121
poetry add --source torch torch=2.3.1
```


For installing libraries with no supplementaty packages
```
pip install . --no-dependencies
```

For a tutorial in Poetry, use this [link](https://python-poetry.org/docs/basic-usage/.).
