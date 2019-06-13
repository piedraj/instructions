# Connect to lxplus

    ssh -Y lxplus6.cern.ch -o ServerAliveInterval=240


# Set the Bash shell

    bash -l


# Before setting up combine

You need to have your `PlotsConfigurations` area ready. To do so, follow the [PLOTSCONFIGURATIONS.md](https://github.com/piedraj/instructions/blob/master/PLOTSCONFIGURATIONS.md) instructions.


# Now you can prepare the combine environment

The instructions that we have followed can be found [here](https://github.com/nucleosynthesis/HiggsAnalysis-CombinedLimit/wiki/gettingstarted#root6-slc6-release-cmssw_8_1_x---recommended-version). This is what we have done on June 12th, 2019.

    export SCRAM_ARCH=slc6_amd64_gcc530
    cmsrel CMSSW_8_1_0
    cd CMSSW_8_1_0/src 
    cmsenv
    git clone https://github.com/cms-analysis/HiggsAnalysis-CombinedLimit.git HiggsAnalysis/CombinedLimit
    cd HiggsAnalysis/CombinedLimit

    cd $CMSSW_BASE/src/HiggsAnalysis/CombinedLimit
    git fetch origin
    git checkout v7.0.12
    scramv1 b clean; scramv1 b -j 8

In addition, `CombineHarvester` needs to be installed and compiled.

    cd $CMSSW_BASE/src
    git clone https://github.com/cms-analysis/CombineHarvester.git CombineHarvester
    scramv1 b -j 8


# Make the datacards

    cd /afs/cern.ch/work/p/piedra/public/CMSSW_projects/CMSSW_9_4_9/src/PlotsConfigurations/Configurations/VH2j/Full2017
    cmsenv
    mkDatacards.py --pycfg=configuration.py --inputFile=rootFile_sf/plots_WW_sf.root

If you cannot make (yet) datacards, you can download these to get started with combine.

    cd /afs/cern.ch/work/p/piedra/public/CMSSW_projects/CMSSW_9_4_9/src/PlotsConfigurations/Configurations/VH2j/Full2017
    git clone https://github.com/piedraj/instructions
    mv instructions/datacards .
    rm -rf instructions


# Anytime you use combine

You need to do `cmsenv` in the combine release.

    pushd ~piedra/combine/CMSSW_8_1_0/src/
    cmsenv
    popd


# Combine datacards

If you have several regions to fit, you need to combine each datacard into a single one.

    combineCards.py datacards/ww_0jet_sf/events/datacard.txt \
                    datacards/ww_1jet_sf/events/datacard.txt \
                    datacards/ww_top0jet_sf/events/datacard.txt \
                    datacards/ww_top1jet_sf/events/datacard.txt \
                    datacards/datacard_combined.txt

Add `[0.1,10]` in the corresponding lines of `datacard_combined.txt`

    Topnorm0j     rateParam ch3 top 1.0000 [0.1,10]
    Topnorm1j     rateParam ch4 top 1.0000 [0.1,10]
    Topnorm0j     rateParam ch1 top 1.0000 [0.1,10]
    Topnorm1j     rateParam ch2 top 1.0000 [0.1,10]


# Get the datacard in ROOT format

    text2workspace.py datacards/datacard_combined.txt -m 125


# Run combine

Fix the signal parameter to 1 and run only on MC by using the `-t -1` option.

    combineTool.py -M Impacts -d datacards/datacard_combined.root -m 125 --doInitialFit -t -1 --expectSignal=1 --robustFit 1 --allPars

Get the impact of each parameter and/or nuisance.

    combineTool.py -M Impacts -d datacards/datacard_combined.root -m 125 -t -1 --expectSignal=1 --robustFit 1 --doFits --allPars

Get the impacts in json format.

    combineTool.py -M Impacts -d datacards_sf/datacard_combined.root -m 125 -o impacts_sf.json --allPars

Plot the impacts.

    plotImpacts.py -i impacts_sf.json -o impacts_sf

Fit the data

    combineTool.py -M Impacts -d datacards/datacard_combined.root -m 125 --doInitialFit --robustFit 1 --allPars
