# Connect to lxplus

    ssh -Y lxplus6.cern.ch -o ServerAliveInterval=240


# Set the Bash shell

    bash -l


# Before setting up combine

You need to have your `PlotsConfigurations` area ready. To do so, follow the [PLOTSCONFIGURATIONS.md](https://github.com/piedraj/instructions/blob/master/PLOTSCONFIGURATIONS.md) instructions.


# Now you can prepare the combine environment

The instructions that we have followed can be found [here](https://github.com/nucleosynthesis/HiggsAnalysis-CombinedLimit/wiki/gettingstarted#root6-slc6-release-cmssw_8_1_x---recommended-version). To be more precise, this is what we have done on June 12th, 2019.

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


cd /afs/cern.ch/work/p/piedra/public/CMSSW_projects/CMSSW_9_4_9/src
cmsenv
cd /afs/cern.ch/work/p/piedra/public/CMSSW_projects/CMSSW_9_4_9/src/PlotsConfigurations/Configurations/VH2j/Full2017
cp -r ~fernanpe/public/for_Jonatan/datacards_sf_ptwwRenorm .


1. mkDatacards con latinos: mkDatacards.py   --pycfg=configuration_sf.py  --inputFile=rootFile_sf/plots_WW_sf.root
2. cmsenv en combine

pushd ~/work/CMSSW_projects/CMSSW_9_4_9/src/PlotsConfigurations/Configurations/VH2j/Full2017/datacards_sf_ptwwRenorm; popd

3. combinar las datacards: combineCards.py datacards_sf/ww_0jet_sf/events/datacard.txt datacards_sf/ww_1jet_sf/events/datacard.txt datacards_sf/ww_top0jet_sf/events/datacard.txt datacards_sf/ww_top1jet_sf/events/datacard.txt > datacards_sf/datacard_combined.txt

#### Aadir [0.1,10]
Topnorm0j     rateParam ch3 top 1.0000 [0.1,10]
Topnorm1j     rateParam ch4 top 1.0000 [0.1,10]
Topnorm0j     rateParam ch1 top 1.0000 [0.1,10]
Topnorm1j     rateParam ch2 top 1.0000 [0.1,10]



4. pasar de .txt a .root: text2workspace.py datacards_sf/datacard_combined.txt -m 125


5. combineTool.py -M Impacts -d datacards_sf/datacard_combined.root -m 125 --doInitialFit -t -1 --expectSignal=1 --robustFit 1 --allPars    # -t -1 ----> solo MC

En el fichero nuisances.py defines lnN o shapes...

6. combineTool.py -M Impacts -d datacards_sf/datacard_combined.root -m 125 -t -1 --expectSignal=1 --robustFit 1 --doFits --allPars



7. combineTool.py -M Impacts -d datacards_sf/datacard_combined.root -m 125 -o impacts_sf.json --allPars
Var