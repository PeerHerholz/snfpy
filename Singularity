# Your version: 0.6.0 Latest version: 0.6.0
# Generated by Neurodocker version 0.6.0
# Timestamp: 2020-04-07 21:56:45 UTC
# 
# Thank you for using Neurodocker. If you discover any issues
# or ways to improve this software, please submit an issue or
# pull request on our GitHub repository:
# 
#     https://github.com/kaczmarj/neurodocker

Bootstrap: docker
From: neurodebian:stretch-non-free

%post
export ND_ENTRYPOINT="/neurodocker/startup.sh"
apt-get update -qq
apt-get install -y -q --no-install-recommends \
    apt-utils \
    bzip2 \
    ca-certificates \
    curl \
    locales \
    unzip
apt-get clean
rm -rf /var/lib/apt/lists/*
sed -i -e 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen
dpkg-reconfigure --frontend=noninteractive locales
update-locale LANG="en_US.UTF-8"
chmod 777 /opt && chmod a+s /opt
mkdir -p /neurodocker
if [ ! -f "$ND_ENTRYPOINT" ]; then
  echo '#!/usr/bin/env bash' >> "$ND_ENTRYPOINT"
  echo 'set -e' >> "$ND_ENTRYPOINT"
  echo 'export USER="${USER:=`whoami`}"' >> "$ND_ENTRYPOINT"
  echo 'if [ -n "$1" ]; then "$@"; else /usr/bin/env bash; fi' >> "$ND_ENTRYPOINT";
fi
chmod -R 777 /neurodocker && chmod a+s /neurodocker

apt-get update -qq
apt-get install -y -q --no-install-recommends \
    git
apt-get clean
rm -rf /var/lib/apt/lists/*

test "$(getent passwd snfpy)" || useradd --no-user-group --create-home --shell /bin/bash snfpy
su - snfpy

export PATH="/opt/miniconda-latest/bin:$PATH"
echo "Downloading Miniconda installer ..."
conda_installer="/tmp/miniconda.sh"
curl -fsSL --retry 5 -o "$conda_installer" https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash "$conda_installer" -b -p /opt/miniconda-latest
rm -f "$conda_installer"
conda update -yq -nbase conda
conda config --system --prepend channels conda-forge
conda config --system --set auto_update_conda false
conda config --system --set show_channel_urls true
sync && conda clean --all && sync
conda create -y -q --name snfpy
conda install -y -q --name snfpy \
    "python=3.7" \
    "notebook" \
    "ipython"
sync && conda clean --all && sync
bash -c "source activate snfpy
  pip install --no-cache-dir  \
      "git+https://github.com/rmarkello/snfpy.git@master" \
      "ipywidgets" \
      "ipyevents" \
      "jupytext" \
      "seaborn" \
      "pandas""
rm -rf ~/.cache/pip/*
sync
sed -i '$isource activate snfpy' $ND_ENTRYPOINT


mkdir -p ~/.jupyter && echo c.NotebookApp.ip = \"0.0.0.0\" > ~/.jupyter/jupyter_notebook_config.py

echo '{
\n  "pkg_manager": "apt",
\n  "instructions": [
\n    [
\n      "base",
\n      "neurodebian:stretch-non-free"
\n    ],
\n    [
\n      "_header",
\n      {
\n        "version": "generic",
\n        "method": "custom"
\n      }
\n    ],
\n    [
\n      "install",
\n      [
\n        "git"
\n      ]
\n    ],
\n    [
\n      "user",
\n      "snfpy"
\n    ],
\n    [
\n      "miniconda",
\n      {
\n        "conda_install": [
\n          "python=3.7",
\n          "notebook",
\n          "ipython"
\n        ],
\n        "pip_install": [
\n          "git+https://github.com/rmarkello/snfpy.git@master",
\n          "ipywidgets",
\n          "ipyevents",
\n          "jupytext",
\n          "seaborn",
\n          "pandas"
\n        ],
\n        "create_env": "snfpy",
\n        "activate": true
\n      }
\n    ],
\n    [
\n      "run",
\n      "mkdir -p ~/.jupyter && echo c.NotebookApp.ip = \\\"0.0.0.0\\\" > ~/.jupyter/jupyter_notebook_config.py"
\n    ],
\n    [
\n      "entrypoint",
\n      "/neurodocker/startup.sh"
\n    ]
\n  ]
\n}' > /neurodocker/neurodocker_specs.json

%environment
export LANG="en_US.UTF-8"
export LC_ALL="en_US.UTF-8"
export ND_ENTRYPOINT="/neurodocker/startup.sh"
export CONDA_DIR="/opt/miniconda-latest"
export PATH="/opt/miniconda-latest/bin:$PATH"

%runscript
/neurodocker/startup.sh
