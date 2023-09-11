# Installing Python libraries

![Python](../assets/python.png)

## Install dependencies

```bash
sudo apt-get install --no-install-recommends python3-pip
```

```bash
# Upgrade Pip
sudo pip3 install --upgrade pip
```

## Install Python libraries

```bash
# Basic libraries
sudo pip3 install \
    progressbar2 \
    requests \
    Cython \
    pylint \
    autopep8 \
    pytest \
    pydocstyle
```

## Make use of virtual environments

If you aren't familiar with virtual environments, check out [this article on RealPython](https://realpython.com/python-virtual-environments-a-primer/).

Install `virtualenv` and `virtualenvwrapper`:

```bash
sudo pip3 install \
    virtualenv \
    virtualenvwrapper
```

To finish, we need to update the `~/.profile` file (similar to `.bashrc` or `.bash_profile`).

Copy and paste the following commands:

```bash
read -r -d '' PROFILE <<"EOF"
# virtualenv and virtualenvwrapper
export WORKON_HOME=${HOME}/.virtualenvs
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
source /usr/local/bin/virtualenvwrapper.sh
EOF

# Create the ~/.profile file
echo "${PROFILE}" >> ${HOME}/.profile
```

```bash
# Update the terminal
source ${HOME}/.profile
```
