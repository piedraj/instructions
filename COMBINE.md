# Connect to lxplus

    ssh -Y lxplus.cern.ch -o ServerAliveInterval=240


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


# Make the datacards

    cd ~piedra/work/CMSSW_projects/CMSSW_9_4_9/src/PlotsConfigurations/Configurations/VH2j/Full2017
    cmsenv
    mkDatacards.py --pycfg=configuration.py --inputFile=rootFile_sf/plots_WW_sf.root

If you cannot make (yet) datacards, you can download these to get started with combine.

    cd ~piedra/work/CMSSW_projects/CMSSW_9_4_9/src/PlotsConfigurations/Configurations/VH2j/Full2017
    git clone https://github.com/piedraj/instructions
    mv instructions/datacards .
    rm -rf instructions


# To use combine

You need to do `cmsenv` in the combine release.

    pushd ~piedra/combine/CMSSW_8_1_0/src/
    cmsenv
    popd


# Combine datacards

If you have several regions to fit, you need to combine all datacards into a single one.

    combineCards.py datacards/ww_0jet_sf/events/datacard.txt \
                    datacards/ww_1jet_sf/events/datacard.txt \
                    datacards/ww_top0jet_sf/events/datacard.txt \
                    datacards/ww_top1jet_sf/events/datacard.txt \
                    > datacards/datacard_combined.txt

Add `[0.1,10]` in the normalization lines of the `datacard_combined.txt` file.

    emacs -nw datacards/datacard_combined.txt

    Topnorm0j     rateParam ch3 top 1.0000  [0.1,10]
    Topnorm1j     rateParam ch4 top 1.0000  [0.1,10]
    Topnorm0j     rateParam ch1 top 1.0000  [0.1,10]
    Topnorm1j     rateParam ch2 top 1.0000  [0.1,10]


# Get the datacard in ROOT format

    text2workspace.py datacards/datacard_combined.txt -m 125


# Run combine

Fix the signal parameter to 1 and run only on MC by using the `-t -1` option.

    combineTool.py \
        -M Impacts \
        -d datacards/datacard_combined.root \
        -m 125 \
        --doInitialFit \
        -t -1 \
        --expectSignal 1 \
        --robustFit 1

This is the output.

    Have POIs: ['r']
    >> combine -M MultiDimFit -n _initialFit_Test --algo singles --redefineSignalPOIs r -t -1 --expectSignal 1 --robustFit 1 -m 125 -d datacards/datacard_combined.root

     <<< Combine >>> 
    >>> including systematics
       Options for Robust Minimizer :: 
            Tolerance  0.1
            Strategy   0
            Type,Algo  Minuit2,Migrad
    >>> method used is MultiDimFit
    >>> random number generator seed is 123456
    Computing results starting from expected outcome (a-priori)
    
     --- MultiDimFit ---
    best fit parameter values and profile-likelihood uncertainties: 
       r :    +1.000   -0.158/+0.122 (68%)
    Done in 0.05 min (cpu), 0.05 min (real)

Now you can get the impact of each nuisance. It might take a while.

    combineTool.py -M Impacts -d datacards/datacard_combined.root -m 125 -t -1 --expectSignal=1 --robustFit 1 --doFits

Get the impacts in json format.

    combineTool.py -M Impacts -d datacards/datacard_combined.root -m 125 -o impacts_sf.json

Plot the impacts.

    plotImpacts.py -i impacts_sf.json -o impacts_sf

Fit the data.

    combineTool.py -M Impacts -d datacards/datacard_combined.root -m 125 --doInitialFit --robustFit 1

This is the output.

    Have POIs: ['r']
    >> combine -M MultiDimFit -n _initialFit_Test --algo singles --redefineSignalPOIs r --robustFit 1 -m 125 -d datacards/datacard_combined.root
     <<< Combine >>> 
    >>> including systematics
       Options for Robust Minimizer :: 
            Tolerance  0.1
            Strategy   0
            Type,Algo  Minuit2,Migrad
    >>> method used is MultiDimFit
    >>> random number generator seed is 123456
    Computing results starting from observation (a-posteriori)
    
     --- MultiDimFit ---
    best fit parameter values and profile-likelihood uncertainties: 
       r :    +0.892   -0.171/+0.142 (68%)
    Done in 0.05 min (cpu), 0.05 min (real)
