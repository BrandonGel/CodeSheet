# A template for setting up conda and poetry

## Installing Miniconda
Follow the link: https://docs.anaconda.com/miniconda/

## Setting up Conda and Poetry (First Time)

```ENV_NAME``` is the name of the conda environment you want to be. ```poetry``` is a python package distributor that will be used for Conda.
```
conda create -n "ENV_NAME" python=3.10.0 ipython
conda activate "ENV_NAME"
conda install poetry
```

This will export the conda environment into a yaml file for installation
```
conda env export -n "ENV_NAME" -f environment.yml --no-builds
```

This will setup the poetry template for you. 
Linus Version
```
poetry init
mkdir tests
touch tests/__init__.py
mkdir ENV_NAME
cd  "$(\ls -1dt ./*/ | head -n 1)"
touch __init__.py
cd ..
```
Window Version. For using vi, type :wq
```
poetry init
mkdir tests
vi tests/__init__.py
mkdir ENV_NAME
vi ENV_NAME/__init__.py
```


## Setting up Conda and Poetry (Next Time)
First, download conda. After cloning this repo, input this command in the terminal to create your conda environment called ```ENV_NAME```from ```environment.yml```. This process would take at least a few to several minutes to finish. 
```
conda env create -f environment.yml
```
Now, activate your conda environment.
```
conda activate ENV_NAME
```
Next go to ```ENV_NAME``` and run this poetry command to install the listed packages. Poetry tutorial can be found on this website: 
```
cd ENV_NAME
python -m pip install .
```
## Remove Environment
```
conda remove --name <environment_name> --all
```


# Resources:
For a cheat sheet in using Conda, use this [link](https://docs.conda.io/projects/conda/en/4.6.0/_downloads/52a95608c49671267e40c689e0bc00ca/conda-cheatsheet.pdf).
For a tutorial in Poetry, use this [link](https://python-poetry.org/docs/basic-usage/.).

