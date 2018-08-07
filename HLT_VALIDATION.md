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


# Edition

Add these lines in HLTrigger/Configuration/python/HLTrigger_EventContent_cff.py

        'keep *_hltIterL3GlbMuon_*_*',
        'keep *_hltIterL3MuonsNoID_*_*',
        'keep *_hltIterL3Muons_*_*',
        'keep *_hltIterL3OIMuonTrackSelectionHighPurity_*_*',
        'keep *_hltIterL3MuonMerged_*_*',
        'keep *_hltIterL3MuonAndMuonFromL1Merged_*_*',
        'keep *_hltIter2IterL3MuonMerged_*_*',
        'keep *_hltIter2IterL3FromL1MuonMerged_*_*',

    #cp ~calderon/public/for_Cedric/associators_cff.py             Validation/RecoMuon/python/.
    #cp ~calderon/public/for_Cedric/muonValidationHLT_cff.py       Validation/RecoMuon/python/.
    #cp ~calderon/public/for_Cedric/HLTrigger_EventContent_cff.py  HLTrigger/Configuration/python/.


# Test

    scram b -j 8

    mkdir RelVal
    cd RelVal

    cmsDriver.py SingleMuPt100_pythia8_cfi \
 	      --conditions auto:phase1_2018_realistic -n 10 \
	      --era Run2_2018 \
	      --eventcontent FEVTDEBUG \
	      --relval 9000,100 -s GEN,SIM \
	      --datatier GEN-SIM \
	      --beamspot Realistic25ns13TeVEarly2018Collision \
	      --geometry DB:Extended \
	      --io SingleMuPt100_pythia8_2018_GenSimFull.io \
	      --python SingleMuPt100_pythia8_2018_GenSimFull.py \
	      --no_exec \
	      --fileout file:step1.root \
	      --nThreads 8

    cmsRun SingleMuPt100_pythia8_2018_GenSimFull.py

    cp ~calderon/public/for_Cedric/step3.py .

    cmsRun step3.py


# Check the results

    edmDumpEventContent step2.root > step2_edmDumpEventContent.out

These collections are in Santi's list [1]. They ARE NOT in the event dump.

    cat step2_edmDumpEventContent.out | grep hltIterL3GlbMuon
    cat step2_edmDumpEventContent.out | grep hltIterL3MuonsNoID
    cat step2_edmDumpEventContent.out | grep hltIterL3Muons
    cat step2_edmDumpEventContent.out | grep hltIterL3OIMuonTrackSelectionHighPurity
    cat step2_edmDumpEventContent.out | grep hltIterL3MuonMerged
    cat step2_edmDumpEventContent.out | grep hltIterL3MuonAndMuonFromL1Merged

These collections are only in Cedric's list [1]. They ARE in the event dump.

    cat step2_edmDumpEventContent.out | grep hltIter2IterL3MuonMerged
    cat step2_edmDumpEventContent.out | grep hltIter2IterL3FromL1MuonMerged

[1] https://its.cern.ch/jira/browse/CMSMUONS-169

