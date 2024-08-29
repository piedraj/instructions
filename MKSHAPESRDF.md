# 0. Documentation

* [mkShapesRDF](https://mkshapesrdf.readthedocs.io/en/latest/)
* [ROOT RDataFrame class reference](https://root.cern/doc/master/classROOT_1_1RDataFrame.html)
* [HTCondor batch system](https://twiki.cern.ch/twiki/bin/view/ABPComputing/LxbatchHTCondor)
* [Condor commands](https://twiki.cern.ch/twiki/bin/view/CENF/NeutrinoClusterCondorDoc)
* [PyROOT tutorials](https://root.cern.ch/doc/master/group__tutorial__pyroot.html)
* [SWAN](https://swan.cern.ch/)
* [Monte Carlo particle numbering scheme](https://pdg.lbl.gov/2020/reviews/rpp2020-rev-monte-carlo-numbering.pdf)

# 1. Everything begins here

Log in to the CERN LXPLUS cluster.

    ssh -Y -l <username> lxplus9.cern.ch -o ServerAliveInterval=240
    bash -l

# 2. Installation

Clone the project.

    git clone https://github.com/BlancoFS/mkShapesRDF.git
    cd mkShapesRDF
    git checkout Run3

Once you have cloned the project you need to modify `install.sh`.

    emacs -nw install.sh

Add the following line right before `python -m pip install -e ".[docs,dev,processor]"`.

    unset SSH_ASKPASS

Now you can proceed with the installation.

    ./install.sh

*Only necessary if Grid access is needed.* Set `eosTmpWorkDir` as `/eos/home-p/piedra/work/LatinosPostProcessing` in `Sites_cfg.py`. Write your home path instead of `/home-p/piedra`.

    emacs -nw mkShapesRDF/processor/framework/Sites_cfg.py

The script `start.sh` has to be run everytime to activate the environment.

    source start.sh

Get the analysis configuration.

    git clone https://github.com/calderona/DarkHiggs_RDF
    cd DarkHiggs_RDF/Full2018_v7/

# 3. Always do

Everytime you start a new session you need to follow these steps.

    ssh -Y -l <username> lxplus9.cern.ch -o ServerAliveInterval=240
    bash -l
    cd mkShapesRDF
    source start.sh
    cd DarkHiggs_RDF/Full2018_v7/

*Only necessary if Grid access is needed.* Produce a valid VOMS proxy.

    voms-proxy-init -voms cms -rfc --valid 168:0

# 4. Do the work

| Step             | Action            | Command                                                                 |
|:-----------------|:------------------|:------------------------------------------------------------------------|
| Compilation      | Compile           | `mkShapesRDF --compile 1`                                               |
| Local (test) run | Run on local      | `mkShapesRDF --operationMode 0 --folder . --doBatch 0 --limitEvents 10` |
| Batch (full) run | Run on batch      | `mkShapesRDF --operationMode 0 --folder . --doBatch 1`                  |
| ^                | Check filled jobs | `mkShapesRDF --operationMode 1 --folder .`                              |
| ^                | Resubmit jobs     | `mkShapesRDF --operationMode 1 --folder . --resubmit 1`                 |
| ^                | Merge root files  | `mkShapesRDF --operationMode 2 --folder .`                              |

# 5. Do the plots

    mkPlot --inputFile rootFiles__darkHiggs2018_v7/mkShapes__darkHiggs2018_v7.root --showIntegralLegend 1
