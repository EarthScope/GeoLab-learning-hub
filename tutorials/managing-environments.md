# Creating and Managing Virtual Environments in GeoLab

Virtual environments are a best practice and an integral part of Python development because they provide isolated environments for project development. Isolation ensures that the dependencies of one project do not conflict with dependencies of other packages. Developers can manage project-specific dependencies effectively and avoid potential conflicts between package versions in a virtual environment. The result is an easy-to-manage, clean, and organized development environment.

## Python Environments and Package Management

Python environments are collections of software packages that ideally work together without dependency conflicts. Python packages are collections of modules that provide functions and tools when writing a program. Packages can functions, classes, and variables that are designed to perform tasks, ranging from data manipulation and scientific computing to web development and machine learning. Researches and developers can leverage pre-written, tested code to save time and reduce the potential for errors.

Package managers are tools for installing, updating, configuring, and removing Python packages. They handle dependencies and ensure that software projects have the necessary libraries and supporting packages to run correctly. Package managers fetch and install packages, simplifying the process of integrating external libraries into projects.

## Package Managers:Pip and Conda

[Pip](https://github.com/pypa/pip) and [conda](https://docs.conda.io/en/latest/) are popular package managers. Pip is a popular package manager and is commonly used because of its integration with PyPI. Conda is a package manager that is part of [Anaconda distribution](https://www.anaconda.com/docs/getting-started/anaconda/main). The difference between the two is that Conda can manage packages from languages other than Python. It is useful in data science and scientific computing because it can manage non-Python dependencies and complex binary packages. 

Python has a large and diverse set of packages. Packages can have dependencies based on other packages and Python versions. Scientific packages are often wrappers that extend software compiled in C or FORTRAN. This adds another level of complexity when creating an environment.

Pip is sufficient for pure Python projects; pip does not check for dependencies and may install packages that conflict. When conda builds an environment, it checks for dependencies and upgrades or downgrades packages as needed to create a working environment. This additional function facilitates building complex environments, making it a preferred choice for many data scientists and researchers.

GeoLab is based on the [PanGeo](https://github.com/pangeo-data/pangeo-docker-images) computing environment and uses conda as the package manager. You might see `mamba` which is a variant of conda that is faster but may not be as comprehensive as conda's package checking.

## Working with the GeoLab Virtual Environment

To list the virtual environments in GeoLab, type the following command in the terminal.

```bash
conda env list
```

The environment with an asterisk is the current environment. To list the installed packages, type:

```bash
conda list
```

The list has four columns: Name, Version, Build, and Channel. The Channel is the repository for a package. If you scroll through the list, [conda-forge](https://conda-forge.org/) is the predominant channel, and [ PyPI or the Python Package Index](https://pypi.org/) is occasionally listed. You can install packages from PyPI in a conda environment, and in general, it will work with the existing dependencies. 

To use or activate a specific environment, for example, `my_environment`, use the activate command.

```bash
conda activate my environment
```

To leave an environment, use the `deactivate` command 

``` bash
conda deactivate
```

### Creating Virtual Environments

You can create an environment with this command.

```bash
conda create --name <environment name>
```

The `activate` command enables the new environment.

```bash
conda activate <environment name>
```

### Installing Packages in a Notebook

GeoLab instances are ephemeral. That means if you install a package in either the terminal or in a notebook, it will not be there after a GeoLab instance has stoped. If you want a package available after signing out of GeoLab, install them in a notebook with the [`%` magic command](https://ipython.readthedocs.io/en/stable/interactive/magics.html). The `%` executes built-in IPython "magic" commands within the notebook environment, which means packages that you install are available in the notebook.

### Installing Packages with Conda in Notebook

You can add packages to the new environment. More information about creating and managing environments is available in the [documentation](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html). 

To install Python packages with conda, type:

```ipynb
%conda install <package_name>
```

This command installs packages from anaconda.org. Packages from Anaconda are vetted and guaranteed to work in a conda virtual environment, however, they are a subset of packages available. Conda-forge is a large repository of contributed packages that often contain the most recent versions or specialty packages not offered by the Anaconda repository. 

Add the channel parameter To install packages from conda-forge. Any of the syntax shown below installs a package from a channel.

```ipynb
%conda install --channel conda-forge <package-name>
%conda install -c conda-forge <package-name>
%conda install conda-forge::<package-name>
```

You are not limited to either conda-forge or the the Anaconda repository. Packages can be maintained in a specialty repositories. Specify the repository name after the channel parameter to add packages.

```ipynb
%conda install -c <repository> <package-name>
```

You can specify package versions, including Python. For example, a package may require Python 3.10.

```ipynb
%conda install conda-forge:python=3.10
```

Conda list all the dependent packages before installing and a successful install will return the following message:

```ipynb
Downloading and Extracting Packages:
                                                                                
Preparing transaction: done
Verifying transaction: done
Executing transaction: done
```
#### Installing Packages with Pip in a Notebook

You can install packages from PyPi with pip in a conda environment.

```ipynb
%pip install <package_name>
```

Pip does not guarantee that all dependencies will me resolved. However, some packages are only available in Pypi. Pip will list the supporting packages it will install and a successful install will return a message like this:

```ipynb
Installing collected packages: python-utils, progressbar2, obsplus
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 3/3 [obsplus]m2/3 [obsplus]
Successfully installed obsplus-0.3.5 progressbar2-4.5.0 python-utils-3.9.1
```

### Creating a New Environment in GeoLab

The previous section demonstrated how to install packages within the default GeoLab environment. Sometimes you will need a completely different environment. For example, GeoLab's default is Python 3.13, but you need packages that only work with Python 3.10. In this scenario, you will need to create a new environment that is accessible to Jupyter Lab's Python kernel. A kernel is the engine that points to a specific environment and runs the notebook.

#### The environment.yml file

The preferred method of creating an environment with multiple packages is to use an environment.yml file, which is a formatted listing of dependencies and packages. The environment.yml file consists of a name, channels, conda packages as dependencies, and the eqcorrscan package from Pypi installed using pip.

```yml
name: custom_environment
channels:
  - conda-forge
dependencies:
  - python>=3.9,<3.13
  - ipykernel
  - obsplus
  - obspy
  - eqcorrscan
  - pip
  - pip:
    - earthscope-sdk
```

To create the environment, run the following command in a terminal.

```bash
conda env create -f environment.yml
conda activate custom_environment
```

#### Using the Custom Environment

After the environment is created, you will need to add it as a kernel so that is available to notebooks.

```
python -m ipykernel install --user --name custom_environment --display-name "Python (custom_environment)"
```

To use this environment in a notebook, select `Kernel` > `Change Kernel...` from the top menu.

![](./images/select_kernel.png)

Select `Python (custom_environment)` to set the kernel to the environment. Note that you will have to change the kernel for every notebook.

![](./images/select_custom.png)

Verify that the custom_environment is the kernel in the upper right hand corner of the notebook.

![](./images/custom_env.png)


## Summary

Python projects use libraries of software, or packages. Building packages with different versions of Python can introduce dependencies on a Python version and other packages as well. Virtual environments manage dependencies by creating a repeatable working space. Conda, the Python package manager, takes this one step further and checks for dependencies and finds, or solves, for packages without dependency issues.

Conda can install many common packages, however, there are also geoscience packages distributed through the Python Package Index (PyPI). These packages can be installed in a conda environment.

These are the common commands to manage a python environment.

1. List the conda environments with `conda env list`.
2. Create conda environment with `conda create` environment_name.
3. Use the environment you created with `conda activate` environment_name.
4. Add conda packages with `conda install` package_name or from the CondaForge repository, `conda install --channel conda-forge` package_name.
5. Add packages from PyPI with `pip install` package_name.
6. To leave an environment use `conda deactivate`.