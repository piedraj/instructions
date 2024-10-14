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

    ssh -Y -l <username> lxplus.cern.ch -o ServerAliveInterval=240

Switch to a [Bash](https://en.wikipedia.org/wiki/Bash_(Unix_shell)) shell.
    
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

<!---
*Only necessary if Grid access is needed.* Set `eosTmpWorkDir` as `/eos/home-p/piedra/work/LatinosPostProcessing` in `Sites_cfg.py`. Write your home path instead of `/home-p/piedra`.

    emacs -nw mkShapesRDF/processor/framework/Sites_cfg.py
-->

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

<!---
*Only necessary if Grid access is needed.* Produce a valid VOMS proxy.

    voms-proxy-init -voms cms -rfc --valid 168:0
-->

# 4. Produce the analysis histograms

| Action                        | Command                                                                 |
|:------------------------------|:------------------------------------------------------------------------|
| Compile                       | `mkShapesRDF --compile 1`                                               |
| Run on local                  | `mkShapesRDF --operationMode 0 --folder . --doBatch 0 --limitEvents 10` |
| Run on batch                  | `mkShapesRDF --operationMode 0 --folder . --doBatch 1`                  |
| Check filled jobs             | `mkShapesRDF --operationMode 1 --folder .`                              |
| Resubmit jobs                 | `mkShapesRDF --operationMode 1 --folder . --resubmit 1`                 |
| Merge root files (batch only) | `mkShapesRDF --operationMode 2 --folder .`                              |
| Available arguments           | `mkShapesRDF --help`                                                    |

Always proceed as follows:

1. **Modify the code.** This is where the analaysis development starts.
2. **Compile.** If you miss this step the implemented changes won't be considered when running the code.
3. **Run on local.** This is a very important step, as it allows you to check if any error has been introduced with the latest modifications.
4. **Run on batch.** When running on batch check that there are **631 jobs** submitted.

# 5. Check job status

    condor_q

If you need to cancel all the submitted jobs.

    condor_rm <username>

# 6. Plot the analysis histograms

    mkPlot --inputFile rootFiles__darkHiggs2018_v7/mkShapes__darkHiggs2018_v7.root \
           --showIntegralLegend 1 \
           --onlyPlot cratio \
           --fileFormats png

If needed, the available arguments can be printed.

    mkPlot --help

When running on batch check that you have produced these [default distributions](https://piedra.web.cern.ch/plots-v7/).
