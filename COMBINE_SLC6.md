# Connect to lxplus6

    ssh -Y lxplus6.cern.ch -o ServerAliveInterval=240


# Set the Bash shell

    bash -l


# Before setting up combine

You need to have a `PlotsConfigurations` area. To do so, follow the [PLOTSCONFIGURATIONS.md](https://github.com/piedraj/instructions/blob/master/PLOTSCONFIGURATIONS.md) instructions.


# Prepare the combine environment

The instructions that we have followed can be found [here](https://github.com/nucleosynthesis/HiggsAnalysis-CombinedLimit/wiki/gettingstarted#root6-slc6-release-cmssw_8_1_x---recommended-version). This is what we have done on June 13th, 2019.

    mkdir combine
    cd combine

    export SCRAM_ARCH=slc7_amd64_gcc530  # slc6 in the reference instructions
    cmsrel CMSSW_8_1_0
    cd CMSSW_8_1_0/src 
    cmsenv
    git clone https://github.com/cms-analysis/HiggsAnalysis-CombinedLimit.git HiggsAnalysis/CombinedLimit

    cd $CMSSW_BASE/src/HiggsAnalysis/CombinedLimit
    git fetch origin
    git checkout v7.0.12

In addition `CombineHarvester` needs to be installed.

    cd $CMSSW_BASE/src
    git clone https://github.com/cms-analysis/CombineHarvester.git CombineHarvester

Now you can compile everything.

    cd $CMSSW_BASE/src
    scramv1 b clean
    scramv1 b -j 8
