The mkShapesRDF documentation can be found [here](https://mkshapesrdf.readthedocs.io/en/latest/).

# Connect to lxplus

    ssh -Y piedra@lxplus9.cern.ch -o ServerAliveInterval=240
    bash -l

# Clone the project

    git clone https://github.com/BlancoFS/mkShapesRDF.git
    cd mkShapesRDF
    git checkout Run3

# Install the framework

First you need to modify `install.sh`.

    emacs -nw install.sh

Add the following line right before `python -m pip install -e ".[docs,dev,processor]"`.

    unset SSH_ASKPASS

Now you can do the installation.

    ./install.sh

As the next step you need to set `eosTmpWorkDir` as `/eos/home-p/piedra/work/LatinosPostProcessing` in `Sites_cfg.py`. Write your home path instead of `/home-p/piedra`.

    emacs -nw mkShapesRDF/processor/framework/Sites_cfg.py

The script `start.sh` has to be run everytime to activate the environment.

    source start.sh

# Produce a valid VOMS proxy

    voms-proxy-init -voms cms -rfc --valid 168:0

# Get the analysis configuration

    git clone https://github.com/calderona/DarkHiggs_RDF
    cd DarkHiggs_RDF/Full2018_v7/
    
