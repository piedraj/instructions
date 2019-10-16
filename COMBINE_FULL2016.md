# Connect to lxplus7

    ssh -Y lxplus.cern.ch -o ServerAliveInterval=240
    bash -l
    
    
# Setup combine

Follow the instructions documented in [COMBINE_SLC7](https://github.com/piedraj/instructions/blob/master/COMBINE_SLC7.md). The option `-t` is used to specify to combine to first generate a toy dataset which will be used in replacement of the real data. The `-t -1` choice will produce an Asimov dataset in which statistical fluctuations are suppressed. **The steps below correspond to the VH2j datacards reading 2016 data.**

    http://cms-analysis.github.io/HiggsAnalysis-CombinedLimit/


# Make datacards

    cd ~piedra/work/CMSSW_projects/CMSSW_9_4_9/src/PlotsConfigurations/Configurations/VH2j/Full2016_nanoAODv4
    cmsenv

    mkDatacards.py --pycfg=configuration.py --inputFile=rootFile/plots_VH2j_2016.root


# Combine datacards

    pushd ~piedra/combine/CMSSW_10_2_13/src/
    cmsenv
    popd

    pushd datacards
    combineCards.py VH_2j_emu/mll/datacard.txt VH_2j_DYtautau/events/datacard.txt VH_2j_topemu/events/datacard.txt > datacard_combined.txt
    text2workspace.py datacards/datacard_combined.txt -m 125
    popd


# Fixed signal strength

    combineTool.py -M Impacts --expectSignal=1 -d datacards/datacard_combined.root -m 125 -t -1 --doInitialFit --robustFit 1
    combineTool.py -M Impacts --expectSignal=1 -d datacards/datacard_combined.root -m 125 -t -1 --doFits --robustFit 1
    combineTool.py -M Impacts --expectSignal=1 -d datacards/datacard_combined.root -m 125 -t -1 -o impacts.json
    plotImpacts.py -i impacts.json -o impacts


# Significance

    combine -M Significance --expectSignal=1 -t -1 datacards/datacard_combined.txt


# Fit the data

    combineTool.py -M Impacts -d datacards/datacard_combined.root -m 125 --doInitialFit --robustFit 1
    combineTool.py -M Impacts -d datacards/datacard_combined.root -m 125 --doFits --robustFit 1
    combineTool.py -M Impacts -d datacards/datacard_combined.root -m 125 -o impacts.json
    plotImpacts.py -i impacts.json -o impacts


# Signal strength

    combine -M FitDiagnostics --rMin=-4 --rMax=7 --expectSignal=1 -t -1 datacards/datacard_combined.txt -m 125 -n mytest > result.txt

# Likelihood scan

    combine -M MultiDimFit datacards/datacard_combined.txt -m 125 --expectSignal=1 -t -1 --algo=grid --points 100 --setParameterRanges r=-4,7 -n "_MyScan"

    wget https://raw.githubusercontent.com/latinos/PlotsConfigurations/a22b3dd23e0f6f7ff293ee1fbb41906ee0fbfe43/Configurations/ggH/Moriond/scripts/drawNLL.C

    root -l higgsCombine_MyScan.MultiDimFit.mH125.root drawNLL.C

# MC Asimov

    combine -M FitDiagnostics --expectSignal=1 -t -1 -m 125 datacards/datacard_combined.txt

# Significance (profile likelihood)

    combine -M ProfileLikelihood --significance --expectSignal=1 -t -1 -m 125 datacards/datacard_combined.txt > result.txt
