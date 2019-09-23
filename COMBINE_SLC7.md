# Connect to lxplus7

    ssh -Y lxplus.cern.ch -o ServerAliveInterval=240
    bash -l


# Before setting up combine

You need to have a `PlotsConfigurations` area. To do so, follow the [PLOTSCONFIGURATIONS.md](https://github.com/piedraj/instructions/blob/master/PLOTSCONFIGURATIONS.md) instructions.


# Prepare the combine environment

The instructions that we have followed can be found [here](https://github.com/nucleosynthesis/HiggsAnalysis-CombinedLimit/wiki/gettingstarted). This is what we have done on September 23rd, 2019.

    mkdir combine
    cd combine

    cmsrel CMSSW_10_2_13
    cd CMSSW_10_2_13/src
    cmsenv
    git clone https://github.com/cms-analysis/HiggsAnalysis-CombinedLimit.git HiggsAnalysis/CombinedLimit

In addition `CombineHarvester` needs to be installed.

    cd $CMSSW_BASE/src
    git clone https://github.com/cms-analysis/CombineHarvester.git CombineHarvester

Now you can compile everything.

    cd $CMSSW_BASE/src
    scramv1 b clean
    scramv1 b -j 8
