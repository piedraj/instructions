# Documentation

* [mkShapesRDF](https://mkshapesrdf.readthedocs.io/en/latest/)
* [ROOT RDataFrame Class Reference](https://root.cern/doc/master/classROOT_1_1RDataFrame.html)
* [HTCondor batch system](https://twiki.cern.ch/twiki/bin/view/ABPComputing/LxbatchHTCondor)
* [Condor commands](https://twiki.cern.ch/twiki/bin/view/CENF/NeutrinoClusterCondorDoc)
* [PyROOT tutorials](https://root.cern.ch/doc/master/group__tutorial__pyroot.html)
* [SWAN](https://swan.cern.ch/)
* [Monte Carlo particle numbering scheme](https://pdg.lbl.gov/2020/reviews/rpp2020-rev-monte-carlo-numbering.pdf)

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

*Only necessary for batch job submission.* As the next step you need to set `eosTmpWorkDir` as `/eos/home-p/piedra/work/LatinosPostProcessing` in `Sites_cfg.py`. Write your home path instead of `/home-p/piedra`.

    emacs -nw mkShapesRDF/processor/framework/Sites_cfg.py

The script `start.sh` has to be run everytime to activate the environment.

    source start.sh

# Produce a valid VOMS proxy

*Only necessary for batch job submission.* 

    voms-proxy-init -voms cms -rfc --valid 168:0

# Get the analysis configuration

    git clone https://github.com/calderona/DarkHiggs_RDF
    cd DarkHiggs_RDF/Full2018_v7/

# Do the work

Compile.

    mkShapesRDF --compile 1

Run on local.

    mkShapesRDF --operationMode 0 --folder . --doBatch 0 --limitEvents 10

Run on batch.
    
    mkShapesRDF --operationMode 0 --folder . --doBatch 1

Check if there are filled jobs.

    mkShapesRDF --operationMode 1 --folder .

Resubmit jobs.

    mkShapesRDF --operationMode 1 --folder . --resubmit 1

Merge all root files. 

    mkShapesRDF --operationMode 2 --folder .

# Do the plots

    mkPlot --inputFile rootFiles__darkHiggs2018_v7/mkShapes__darkHiggs2018_v7.root --showIntegralLegend 1
