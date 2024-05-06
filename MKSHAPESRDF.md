The mkShapesRDF documentation can be found [here](https://mkshapesrdf.readthedocs.io/en/latest/).

# Connect to lxplus

    ssh -Y piedra@lxplus9.cern.ch -o ServerAliveInterval=240
    bash -l

# Clone the project

    mkdir work
    cd work
    git clone https://github.com/BlancoFS/mkShapesRDF.git
    cd mkShapesRDF
    git checkout Run3

# Install the framework

First you need to modify `install.sh`.

    emacs -nw install.sh

Add the following line before `python -m pip install -e ".[docs,dev,processor]"`.

    unset SSH_ASKPASS

Do the installation.

    ./install.sh

Set your `eosTmpWorkDir`.

    pushd mkShapesRDF/processor/framework
    emacs -nw Sites_cfg.py

Set `eosTmpWorkDir` as `/eos/home-p/piedra/work/LatinosPostProcessing`. Instead of `/home-p/piedra` write your home path.

    popd
    source start.sh
    voms-proxy-init -voms cms -rfc --valid 168:0

# Get the analysis configuration

    git clone https://github.com/calderona/DarkHiggs_RDF
    cd DarkHiggs_RDF/Full2018_v7/
    
