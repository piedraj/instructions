# MET Significance

Original instructions extracted from the [MET Significance studies using nanoAOD](https://twiki.cern.ch/twiki/bin/viewauth/CMS/METSignificance) TWiki.

[MET Significance tuning and performance in dimuon event](https://gitlab.cern.ch/cms-jetmet/coordination/coordination/-/issues/218) GitLab open issue.

## 0. Installation

Log in to the CERN LXPLUS cluster.

    ssh -Y -l <user> lxplus.cern.ch -o ServerAliveInterval=240

The code has to be run with an el7 image. Follow the [El7 image with condor support for lxplus](https://gitlab.cern.ch/cms-cat/cmssw-lxplus/) instructions to create a `start_el7.sh` script, and then make it executable.

    chmod u+x start_el7.sh

Now you can run it.

    ./start_el7.sh

Initialize a CMSSW release.

    unset SCRAM_ARCH
    cmsrel CMSSW_10_2_18
    cd CMSSW_10_2_18/src/
    cmsenv

Clone the needed git repositories.

    cd $CMSSW_BASE/src
    git cms-init
    git clone -b dimuon_analysis https://github.com/cesarecazzaniga/nanoMET
    git clone https://github.com/HephyAnalysisSW/RootTools.git
    git clone https://github.com/HephyAnalysisSW/Samples.git

Checkout the custom tools for the MET Significance producer.

    git clone https://github.com/cms-nanoAOD/nanoAOD-tools.git PhysicsTools/NanoAODTools
    cd PhysicsTools/NanoAODTools
    git remote add metsig https://github.com/cesarecazzaniga/nanoAOD-tools.git
    git fetch metsig
    git checkout metsig/MetSig_devel

Checkout JetMETCorrections.

    cd $CMSSW_BASE/src
    git cms-addpkg JetMETCorrections

Get the JER files.

    sh $CMSSW_BASE/src/nanoMET/core/data/JER/getJER.sh

Some user specific paths are hardcoded in the nanoMET repository. Edit the `$CMSSW_BASE/src/nanoMET/tools/python/user.py` file and add the paths suited for your setup.

| Path name                         | Path description                        |
|:----------------------------------|:----------------------------------------|
| `plot_directory`	                | Path to store produced plots            |
| `postprocessing_output_directory` | Path to the nanoMET flat ntuples output |
| `data_directory`	                | Path to the postprocessed samples       |
| `dbDir`	                        | Path to the used samples database       |

## 1. Always do

It is necessary that you provide a full AFS path to your proxy file. Information regarding proxy settings can be found [here](https://batchdocs.web.cern.ch/tutorial/exercise2e_proxy.html).

    ssh -Y -l piedra lxplus.cern.ch -o ServerAliveInterval=240
    
    ./start_el7.sh

    cd CMSSW_10_2_18/src
    cmsenv
    scram b -j 8

    export X509_USER_PROXY=/afs/cern.ch/work/p/piedra/private/x509up
    voms-proxy-init -voms cms --valid 192:00 --vomslife 192:0

## 2. Caching normalization in samples

This step should be performed the first time, and anytime one or more samples have been added.

    python $CMSSW_BASE/src/nanoMET/nanoAOD/python/UL16_nanoAODAPVv9_preVFP.py
    python $CMSSW_BASE/src/nanoMET/nanoAOD/python/UL16_DATA_nanoAODAPVv9_preVFP.py

    python $CMSSW_BASE/src/nanoMET/nanoAOD/python/UL16_nanoAODv9_postVFP.py
    python $CMSSW_BASE/src/nanoMET/nanoAOD/python/UL16_DATA_nanoAODv9_postVFP.py

    python $CMSSW_BASE/src/nanoMET/nanoAOD/python/UL17_nanoAODv9.py
    python $CMSSW_BASE/src/nanoMET/nanoAOD/python/UL17_DATA_nanoAODv9.py

    python $CMSSW_BASE/src/nanoMET/nanoAOD/python/UL18_nanoAODv9.py
    python $CMSSW_BASE/src/nanoMET/nanoAOD/python/UL18_DATA_nanoAODv9.py

## 3. Postprocessing samples

    cd $CMSSW_BASE/src/nanoMET/postprocessing

Test interactively with the `WZ` sample in `postProcessing_2018_mumu_nanoAODv9.sh`. This is slow, but once you see a file appear in the `postprocessing_output_directory` defined in `$CMSSW_BASE/src/nanoMET/tools/python/user.py` the test can be considered successful.

    python postProcessing_doublemu_new.py --skim dimuon --era v9 --year 2018 --ul --samples WZ #SPLIT16

Submit to condor. The `--resubmitFailedJobs` argument can be included to resubmit jobs with nonzero exit code.
    
    submitCondor.py --dpm --queue tomorrow --execFile condor.sh postProcessing_2016_mumu_nanoAODAPVv9_preVFP.sh
    submitCondor.py --dpm --queue tomorrow --execFile condor.sh postProcessing_2016_mumu_nanoAODv9_postVFP.sh
    submitCondor.py --dpm --queue tomorrow --execFile condor.sh postProcessing_2017_mumu_nanoAODv9.sh
    submitCondor.py --dpm --queue tomorrow --execFile condor.sh postProcessing_2018_mumu_nanoAODv9.sh

Jobs can be submitted to different condor queues.

| Queue name     | Queue length |
|:---------------|:-------------|
| `espresso`     | 20 minutes   |
| `microcentury` | 1 hour       |
| `longlunch`    | 2 hours      |
| `workday`      | 8 hours      |
| `tomorrow`     | 1 day        |
| `testmatch`    | 3 days       |
| `nextweek`     | 1 week       |

Check job status.

    condor_q

If you need to cancel all the submitted jobs.

    condor_rm [YourUserName]

Location of the condor log files.

    /afs/cern.ch/work/${USER::1}/$USER/condor_output/

Location of the postprocessed files.

    /eos/cms/store/group/phys_jetmet/piedra/MET_studies/MET_significance/OUTPUT_DIR/2016_UL_preVFP_v9/dimuon
    /eos/cms/store/group/phys_jetmet/piedra/MET_studies/MET_significance/OUTPUT_DIR/2016_UL_postVFP_v9/dimuon
    /eos/cms/store/group/phys_jetmet/piedra/MET_studies/MET_significance/OUTPUT_DIR/2017_UL_v9/dimuon
    /eos/cms/store/group/phys_jetmet/piedra/MET_studies/MET_significance/OUTPUT_DIR/2018_UL_v9/dimuon

## 4. Tuning the MET Significance

    cd $CMSSW_BASE/src/nanoMET/run

Submit to condor. The content of the predefined cut strings can be found in the [`nanoMET/tools/python/cutInterpreter.py`](https://github.com/cesarecazzaniga/nanoMET/blob/dimuon_analysis/tools/python/cutInterpreter.py) file.

    submitCondor.py --queue tomorrow --execFile condor.sh tune_UL2016_DoubleMuon_preVFP.sh
    submitCondor.py --queue tomorrow --execFile condor.sh tune_UL2016_DoubleMuon_postVFP.sh 
    submitCondor.py --queue tomorrow --execFile condor.sh tune_UL2017_DoubleMuon.sh 
    submitCondor.py --queue tomorrow --execFile condor.sh tune_UL2018_DoubleMuon.sh 

The tuning parameters for data and MC should appear in the following folder.

    $CMSSW_BASE/src/nanoMET/run/results

## 5. Recomputing the MET Significance

    cd $CMSSW_BASE/src/nanoMET/postprocessing

Copy the data and MC tuning parameters obtained in the previous step in the `tunes_dimuon.py` file. Once the parameters have been copied you can submit the *after tuning* jobs to condor.

    submitCondor.py --dpm --queue tomorrow --execFile condor.sh postProcessing_2016_mumu_nanoAODAPVv9_preVFP_after_tuning.sh
    submitCondor.py --dpm --queue tomorrow --execFile condor.sh postProcessing_2016_mumu_nanoAODv9_postVFP_after_tuning.sh
    submitCondor.py --dpm --queue tomorrow --execFile condor.sh postProcessing_2017_mumu_nanoAODv9_after_tuning.sh
    submitCondor.py --dpm --queue tomorrow --execFile condor.sh postProcessing_2018_mumu_nanoAODv9_after_tuning.sh

Location of the *after tuning* postprocessed files.

    /eos/cms/store/group/phys_jetmet/piedra/MET_studies/MET_significance/OUTPUT_DIR/2016_UL_preVFP_v9/dimuon_afterTuning
    /eos/cms/store/group/phys_jetmet/piedra/MET_studies/MET_significance/OUTPUT_DIR/2016_UL_postVFP_v9/dimuon_afterTuning
    /eos/cms/store/group/phys_jetmet/piedra/MET_studies/MET_significance/OUTPUT_DIR/2017_UL_v9/dimuon_afterTuning
    /eos/cms/store/group/phys_jetmet/piedra/MET_studies/MET_significance/OUTPUT_DIR/2018_UL_v9/dimuon_afterTuning

## 6. Draw analysis and systematics plots

    cd $CMSSW_BASE/src/nanoMET/plots
    cp $CMSSW_BASE/src/nanoMET/postprocessing/tunes_dimuon.py .

Replace `dimuon` by `dimuon_afterTuning` in the corresponding `$CMSSW_BASE/src/nanoMET/samples/python/*postProcessed_mumu.py` files.

Submit to condor. The content of the predefined cut strings can be found in the [`nanoMET/tools/python/cutInterpreter.py`](https://github.com/cesarecazzaniga/nanoMET/blob/dimuon_analysis/tools/python/cutInterpreter.py) file.

    submitCondor.py --execFile condor.sh --queue tomorrow analysisPlots_dimuon.sh
    submitCondor.py --execFile condor.sh --queue tomorrow systematicsPlots_dimuon.sh

Location of the plots as defined in `$CMSSW_BASE/src/nanoMET/tools/python/user.py`.

    /afs/cern.ch/user/p/piedra/MET_studies/MET_significance/PLOT_DIR/analysisPlots
    /afs/cern.ch/user/p/piedra/MET_studies/MET_significance/PLOT_DIR/systematicsPlots

## 7. Share on the web

Create a `met-studies` directory in your EOS `www` location, and copy there the plots.

    mkdir /eos/home-p/piedra/www/met-studies
    cd /eos/home-p/piedra/www/met-studies
    
    cp -r /afs/cern.ch/user/p/piedra/MET_studies/MET_significance/PLOT_DIR/analysisPlots .
    cp -r /afs/cern.ch/user/p/piedra/MET_studies/MET_significance/PLOT_DIR/systematicsPlots .
    
Once the plots have been copied index them, so they can be seen in your webEOS site.

    cd /eos/home-p/piedra/www/met-studies
    cp ../index.php .
    find . -type d -exec cp index.php {} \;

Enjoy the plots.
    
    https://piedra.web.cern.ch/met-studies/
