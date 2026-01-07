# Cookiecutter Data Science - Customized for BIG IDEAs Lab

This is a modified version of the cookiecutter data science template with a few key changes for our lab.

Key changes include:
* Automatic inclusion/installation of nbstripout
* Utilities for using Box for remote data storage 
* Changed defaults for package management

## Getting Started

### 1. Installation
Install the ccds library. You can, for example, install this on your "base" conda environment, and use it to create new projects, each of which will have their own conda environment

```bash
# With pip from PyPI
pip install cookiecutter-data-science

# With conda from conda-forge (coming soon)
# conda install cookiecutter-data-science -c conda-forge
```

### 2. Starting a new project

To start a new project, run:

```bash
ccds https://github.com/hershkovichl/cookiecutter-data-science-bigideaslab -c master
```

Notes:
* If you don't include the URL to this repo, it will just install the default template from the [original cookiecutter data science](https://github.com/drivendataorg/cookiecutter-data-science).
* `-c master` refers to the branch of this repo to grab. E.g. if you're testing a new template, you can use the name of that branch.

### 3. Activate the project
Once in the new directory for your project, run the following command to create your environment that you specified in the creation process (e.g. conda):
```bash
make create_environment
```

Then, activate your environment:

```bash
# Example, if using conda:
conda activate [my_project_name]
```

Finally, run the following to update your environment:

```bash
make requirements
```


### 4. Understanding the template

To understand how to fully utilize the template, I recommend you read CCDS's philosophy on their [project homepage](https://cookiecutter-data-science.drivendata.org/). Specifically, I recommend checking out ["Why ccds?"](https://cookiecutter-data-science.drivendata.org/why/) and ["Opinions"](https://cookiecutter-data-science.drivendata.org/opinions/) to understand the motivation.

Importantly, check out ["Using the template"](https://cookiecutter-data-science.drivendata.org/using-the-template/) for detailed instructions on how to fully utilize the template. 



# Making Changes to this template
The following instructions are only useful if you're trying to modify the template.

## What files/folders to edit
Notes: This is a fork from the [original cookiecutter data science](https://github.com/drivendataorg/cookiecutter-data-science), so it has more files than strictly needed. The original repo is both a Python library (posted on PyPI) and the template for projects. In order to prevent this repo from rebasing on new releases of CCDS, all of the excessive files are left in place.

The important locations for making updates are:
* ccds.json
* Anything under the {{cookiecutter.repo_name}} folder
* hooks/post_gen_project.py

### ccds.json
This file runs through the options that will be given to the user upon creation of the new project.

### The {{cookiecutter.repo_name}} folder
This is the folder that is the actual template- it will be turned into the new project folder. As I understand it, the main function of cookiecutter is to look for these curly brackets in files and switch them out for the user's selection. For example, the second question that will be asked is "repo_name", and the typed answer will replace the name of this folder. 

You can also use these properties in "if statements" to specify things that should be included if the user chooses certain things in the project creation. For example the `.env` file under {{cookiecutter.module_name}} has the following lines:

```bash
{% if cookiecutter.dataset_storage.box %}
CLIENT_ID="[your client id]"
CLIENT_SECRET="[your client secret]"
CALLBACK_HOSTNAME = "localhost"
CALLBACK_PORT = 3000
REDIRECT_URI='http://localhost:3000/callback'
BOX_TOKEN_CACHE_FILE = ".oauth.tk"
{% endif %}
```
If any option other than "box" is chosen for dataset_storage, then these lines will not be included in the `.env` file.

### hooks/post_gen_project.py
This is where more complex features can be accomplished. After the main run through of cookiecutter copying over the template and renaming all files and pasting in all if statements, this python script will be executed. The utility of this file is that ***it will also be affected by the curly bracket autocomplete of cookiecutter***, so you can use the `{%if%}` statements and any property autofill. 

This file specifies packages that will be installed (based on the package manager) in the `packages_to_install` list, so if you need certain packages to install, you can append them to the list. There is also a separate `pip_only_packages` list, which specifies that the package must be installed from PyPI, even if you're using a different package manager like conda.

An example usage of this module is for file inclusion/deletion. If you want certain files to be included sometimes (e.g. the box OAuth utils), just put them in the {{cookiecuter.repo_name}} folder where you want them to end up, and then use this module to delete those files if the conditions are not met. For example, the box utils files are in the {{cookiecuter.repo_name}} folder, but if "box" is not chosen for dataset_storage, the whole folder gets deleted. Note that at the end of the file, if cookiecutter.include_code_scaffold is chosen "No", then most of the files/folders get deleted- if you want to keep something in, then you'll have to carve out an exception with a cookiecutter if statement, as was done with the following snippet:
```python
        # {% if cookiecutter.dataset_storage.box %}
        if generated_path.name == 'utils':
            continue
        # {% endif %}
```
This just has the for-loop skip the 'utils' folder if "box" was chosen for dataset_storage.

## Testing
As mentioned above, the -c flag in ccds can be used to specify which branch of the github repo to pull from. So while testing new features, push them to a separate branch, and use `ccds [github repo link] -c [branch_name]` to test new projects with your edits. Once happy with the changes, use a github pull request to merge your branch onto `master`.