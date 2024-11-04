# 1. Always do

    ssh -Y -l piedra lxplus.cern.ch -o ServerAliveInterval=240
    
    ./start_el7.sh

    cd work/METSignificance/CMSSW_10_2_18/src
    cmsenv
    scram b -j 8
 
# 2. Get a proxy certificate

    voms-proxy-init -voms cms --valid 192:00 --vomslife 192:0

# 3. Caching normalization in samples

This step should be performed only when a sample has been added.

    python $CMSSW_BASE/src/nanoMET/nanoAOD/python/UL18_nanoAODv9.py
    python $CMSSW_BASE/src/nanoMET/nanoAOD/python/UL18_DATA_nanoAODv9.py

# 4. Postprocessing samples

    cd $CMSSW_BASE/src/nanoMET/postprocessing

Test interactively a sample in `postProcessing_2018_mu_nanoAODv9.sh`.

    python postProcessing_singlemu_new.py --skim singlemu --era v9 --year 2018 --ul --samples DYJetsToLL_M10to50_LO #SPLIT11

Submit to condor.

    submitCondor.py --dpm --queue tomorrow --execFile condor.sh postProcessing_2018_mu_nanoAODv9.sh --logLevel DEBUG

Location of the condor log files.

    /afs/cern.ch/work/${USER::1}/$USER/condor_output/
