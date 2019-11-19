# Get everything

    ssh -XY lxplus.cern.ch

    bash -l
    cd work/CMSSW_projects/

    export SCRAM_ARCH=slc6_amd64_gcc700
    cmsrel CMSSW_10_2_0
    cd CMSSW_10_2_0/src
    cmsenv

    git cms-addpkg Validation/RecoMuon
    git cms-addpkg HLTrigger/Configuration

    scram b -j 8

    mkdir RelVal


# Edition

Add these lines in the **HLTDebugFEVT** block of `HLTrigger/Configuration/python/HLTrigger_EventContent_cff.py`.

    'keep *_hltIterL3GlbMuon_*_*',
    'keep *_hltIterL3MuonsNoID_*_*',
    'keep *_hltIterL3Muons_*_*',
    'keep *_hltIterL3OIMuonTrackSelectionHighPurity_*_*',
    'keep *_hltIterL3MuonMerged_*_*',
    'keep *_hltIterL3MuonAndMuonFromL1Merged_*_*',


# GEN-SIM

Create the GEN-SIM configuration file using [cmsDriver.py](https://twiki.cern.ch/twiki/bin/view/CMSPublic/SWGuideCmsDriver).

    cd RelVal

    cmsDriver.py SingleMuPt100_pythia8_cfi \
              --beamspot Realistic25ns13TeVEarly2018Collision \
              --relval 9000,100 \
              --mc \
              --conditions auto:phase1_2018_realistic \
              --step GEN,SIM \
              --datatier GEN-SIM \
              --number 10 \
              --era Run2_2018 \
              --geometry DB:Extended \
              --eventcontent FEVTDEBUG \
              --no_exec \
              --nThreads 8 \
              --io SingleMuPt100_pythia8_2018_GenSimFull.io \
              --python SingleMuPt100_pythia8_2018_GenSimFull.py \
              --fileout file:step1.root

Run the GEN-SIM configuration file.

    cmsRun SingleMuPt100_pythia8_2018_GenSimFull.py


# GEN-SIM-DIGI-RAW

Create the GEN-SIM-DIGI-RAW configuration file.

    cd RelVal

    cmsDriver.py step2 \
              --mc \
              --conditions auto:phase1_2018_realistic \
              --step DIGI:pdigi_valid,L1,DIGI2RAW,HLT:@relval2018 \
              --datatier GEN-SIM-DIGI-RAW \
              --number 10 \
              --era Run2_2018 \
              --geometry DB:Extended \
              --eventcontent FEVTDEBUGHLT \
              --no_exec \
              --nThreads 8 \
              --io DigiFull_2018.io \
              --python DigiFull_2018.py \
              --filein file:step1.root \
              --fileout file:step2.root

Run the GEN-SIM-DIGI-RAW configuration file.

    cmsRun DigiFull_2018.py

Check the results.

    edmDumpEventContent step2.root > step2_edmDumpEventContent.out

These collections are in [Santiago's list](https://its.cern.ch/jira/browse/CMSMUONS-169), and **they are** in the event dump.

    cat step2_edmDumpEventContent.out | grep hltIterL3GlbMuon
    cat step2_edmDumpEventContent.out | grep hltIterL3MuonsNoID
    cat step2_edmDumpEventContent.out | grep hltIterL3Muons
    cat step2_edmDumpEventContent.out | grep hltIterL3OIMuonTrackSelectionHighPurity
    cat step2_edmDumpEventContent.out | grep hltIterL3MuonMerged
    cat step2_edmDumpEventContent.out | grep hltIterL3MuonAndMuonFromL1Merged


# DQMIO

Create the DQMIO configuration file.

    cd RelVal

    cmsDriver.py step3 \
              --runUnscheduled \
              --mc \
              --conditions auto:phase1_2018_realistic \
              --step RAW2DIGI,L1Reco,RECO,RECOSIM,EI,PAT,VALIDATION:@standardValidation+@miniAODValidation,DQM:@standardDQM+@ExtraHLT+@miniAODDQM \
              --datatier GEN-SIM-RECO,MINIAODSIM,DQMIO \
              --number 10 \
              --era Run2_2018 \
              --geometry DB:Extended \
              --eventcontent RECOSIM,MINIAODSIM,DQM \
              --no_exec \
              --nThreads 8 \
              --io RecoFull_2018.io \
              --python RecoFull_2018.py \
              --filein file:step2.root \
              --fileout file:step3.root

Run the DQMIO configuration file.

    cmsRun RecoFull_2018.py


# Harvesting

Create the harvesting configuration file.

    cmsDriver.py step4 \
              --filetype DQM \
              --scenario pp \
              --mc \
              --conditions auto:phase1_2018_realistic \
              --step HARVESTING:@standardValidation+@standardDQM+@ExtraHLT+@miniAODValidation+@miniAODDQM \
              --number 100 \
              --era Run2_2018 \
              --geometry DB:Extended \
              --no_exec \
              --io HARVESTFull_2018.io \
              --python HARVESTFull_2018.py \
              --filein file:step3_inDQM.root

Run the harvesting configuration file.

    cmsRun HARVESTFull_2018.py


# Validation

    cd CMSSW_10_2_0/src/Validation/RecoMuon/python

    cp ~calderon/public/for_Cedric/associators_cff.py .
    cp ~calderon/public/for_Cedric/muonValidationHLT_cff.py .

Edit these two files and compare with the originals.

    diff associators_cff.py ~calderon/public/for_Cedric/associators_cff.py
    diff muonValidationHLT_cff.py ~calderon/public/for_Cedric/muonValidationHLT_cff.py
    
    
# Open issue

**November 19th 2019.** We haven't yet managed to get validation distributions for the six requested collections. As one can see in the table below, we are getting meaningful distributions for the `reco::Track` collections but not for the [`reco::Muon`](https://github.com/cms-sw/cmssw/blob/master/DataFormats/MuonReco/interface/Muon.h) collections, in the [upgrade2018](https://cms-muonpog.web.cern.ch/cms-muonpog/Validation/CMSSW_10_6_0_pre4/106X_upgrade2018_realistic_v4_PU25ns/RelValTTbar_13/) validation. Notice that the [MuonAssociatorByHits](https://github.com/cms-sw/cmssw/blob/master/SimMuon/MCTruth/plugins/MuonAssociatorEDProducer.cc#L16) is asking for a `reco::Track` collection. To check the `FEVTDEBUGHLT` event content (the one used in DQM) we have taken a `RelVarTTbar` file.

    edmDumpEventContent /eos/cms/store/relval/CMSSW_10_6_4_patch1/RelValTTbar_13UP18/FEVTDEBUGHLT/PUpmx25ns_106X_upgrade2018_realistic_v10-v1/10000/6AAD1878-D25B-9F4F-88CE-37FC07EB7219.root

| Collection                              | Type          | Validation status |
| :-------------------------------------- |:-------------:|:-----------------:|
| hltIterL3GlbMuon                        | reco::Track   | [works](https://cms-muonpog.web.cern.ch/cms-muonpog/Validation/CMSSW_10_6_0_pre4/106X_upgrade2018_realistic_v4_PU25ns/RelValTTbar_13/hltIterL3GlbMuon/) |
| hltIterL3MuonMerged                     | reco::Track   | [works](https://cms-muonpog.web.cern.ch/cms-muonpog/Validation/CMSSW_10_6_0_pre4/106X_upgrade2018_realistic_v4_PU25ns/RelValTTbar_13/hltIterL3MuonMerged/) |
| hltIterL3OIMuonTrackSelectionHighPurity | reco::Track   | [works](https://cms-muonpog.web.cern.ch/cms-muonpog/Validation/CMSSW_10_6_0_pre4/106X_upgrade2018_realistic_v4_PU25ns/RelValTTbar_13/hltIterL3OIMuonTrackSelectionHighPurity/)             |
| hltIterL3Muons                          | reco::Muon    | [N/A](https://cms-muonpog.web.cern.ch/cms-muonpog/Validation/CMSSW_10_6_0_pre4/106X_upgrade2018_realistic_v4_PU25ns/RelValTTbar_13/hltIterL3Muons/) |
| hltIterL3MuonsNoID                      | reco::Muon    | [N/A](https://cms-muonpog.web.cern.ch/cms-muonpog/Validation/CMSSW_10_6_0_pre4/106X_upgrade2018_realistic_v4_PU25ns/RelValTTbar_13/hltIterL3MuonsNoID/) |
| hltIterL3MuonAndMuonFromL1Merged        | not requested | [N/A](https://cms-muonpog.web.cern.ch/cms-muonpog/Validation/CMSSW_10_6_0_pre4/106X_upgrade2018_realistic_v4_PU25ns/RelValTTbar_13/hltIterL3MuonAndMuonFromL1Merged/) |

 Any progress should be reported in the [CMSMUONS-169](https://its.cern.ch/jira/browse/CMSMUONS-169) JIRA ticket.
