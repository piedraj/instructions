# Latino postprocessing

The common documentation can be found in the [NanoGardener](https://github.com/latinos/LatinoAnalysis/tree/master/NanoGardener) page. At IFCA we are in charge of postprocessing several MC samples. The file [fall17_nAOD_v1.py](https://github.com/latinos/LatinoAnalysis/blob/master/NanoGardener/python/framework/samples/fall17_nAOD_v1.py) contains both the samples and the assigned names.

# Get ready

    ssh -Y lxplus.cern.ch -o ServerAliveInterval=240
    bash -l
    cd work/CMSSW_projects/CMSSW_9_4_9/src/LatinoAnalysis
    cmsenv
    git pull
    scram b -j 8

# Submit jobs

To submit jobs the procedure is simple.

    voms-proxy-init -voms cms -rfc --valid 168:0
    mkPostProc.py -p Fall2017_nAOD_v1_Full2017v2 -s MCl1loose2017v2 -b -T WWW,WWZ,WZZ,ZZZ,WWG,Wg_MADGRAPHMLM

# Output

The output will be located here.

    /eos/cms/store/group/phys_higgs/cmshww/amassiro/HWWNano/Fall2017_nAOD_v1_Full2017v2/MCl1loose2017v2/

# HTCondor

To submit jobs to the [HTCondor](http://batchdocs.web.cern.ch/batchdocs/local/quick.html) batch system add the line `batchType = 'condor'` in your `LatinoAnalysis/Tools/python/userConfig.py` file. Then you can proceed as usual by adding the batch [queue flavour](https://twiki.cern.ch/twiki/bin/view/ABPComputing/LxbatchHTCondor).

    mkBatch.py --clean
    voms-proxy-init -voms cms -rfc --valid 168:0
    mkPostProc.py -p Fall2017_nAOD_v1_Full2017v2 -s MCCorr2017 -i MCl1loose2017v2 -b -T WWW,WWZ,WZZ,ZZZ,WWG,Wg_MADGRAPHMLM --queue=tomorrow

# Status

To check the status of the jobs.

    mkBatch.py --status
    condor_q

# Cleanup

In case the jobs submitted by `$USER` to the HTCondor batch system need to be removed.

    condor_rm $USER

In case the files produced by `$USER` need to be removed.

    cd /eos/cms/store/group/phys_higgs/cmshww/amassiro/HWWNano/Fall2017_nAOD_v1_Full2017v2/MCl1loose2017v2__MCCorr2017
    ls -lrt | grep $USER | awk '{print $9}' | xargs -n 1 rm
