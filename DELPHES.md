# Pablo's instructions

The Delphes modified version that can be found in `/gpfs/projects/cms/parbol/code` includes several improvements.

   * timing information;
   * long-lived particles treatment;
   * muon efficiencies vs. d0 and dz;
   * tight muons realistic parameterization vs. d0 and dz;
   * accepts CMS GEN as input;
   * splits the number of jobs;

The main macro is called `make_gridui.py`. It creates the output structure and the `run.sh` script, which contains all that is needed to submit the jobs. We aware that every user needs to adapt the file `submit_template_gridui.sh` to his/her directories.

    python make_gridui.py <directory> <eventsperjob> <output>

       directory = location of the input sample
    eventsPerJob = number of events per job
       outputdir = output location

Notice that `make_gridui.py` will look for the **PhaseIISummer17GenOnly/\<my-sample-name\>/GEN** path in the `directory` parameter, and it will prepare the output structure in `outputdir/<my-sample-name>`.


# Setup a CMSSW release

    ssh gridui.ifca.es

    cd /gpfs/users/piedra/project/work

    source /cvmfs/cms.cern.ch/cmsset_default.sh
    export SCRAM_ARCH=slc7_amd64_gcc630
    cmsrel CMSSW_9_4_0_pre3
    cd CMSSW_9_4_0_pre3/src/
    cmsenv


# Get Pablo's code and prepare it

    ssh gridui.ifca.es

    cp -r /gpfs/projects/cms/parbol/code /gpfs/projects/cms/piedra/.


Replace **parbol** by your USER and directories.

    cd /gpfs/projects/cms/piedra/code/madanalysis5/tools/delphes

    emacs -nw submit_template_gridui.sh
    emacs -nw cards/CMS_PhaseII/CMS_PhaseII_200PU_v03_orig.tcl


# Time to test

    ssh gridui.ifca.es

    cd /gpfs/users/piedra/project/work/CMSSW_9_4_0_pre3/src
    source /cvmfs/cms.cern.ch/cmsset_default.sh
    cmsenv

    cd /gpfs/projects/cms/piedra/code/madanalysis5/tools/delphes

    mkdir test

    python make_gridui.py /gpfs/gaes/cms/store/mc/PhaseIISummer17GenOnly/DisplacedSUSY_stopToBottom_M_500_10000mm_TuneCP5_14TeV_pythia8/GEN/93X_upgrade2023_realistic_v5-v1/80000 1000 /gpfs/projects/cms/piedra/code/madanalysis5/tools/delphes/test

    cd test/DisplacedSUSY_stopToBottom_M_500_10000mm_TuneCP5_14TeV_pythia8/


Test one file.

    chmod +x submit_DisplacedSUSY_stopToBottom_M_500_10000mm_TuneCP5_14TeV_pythia8_0_0.sh

    qsub submit_DisplacedSUSY_stopToBottom_M_500_10000mm_TuneCP5_14TeV_pythia8_0_0.sh


Once the single file test has been successful, you can then submit all the files.

    source run.sh


# Check your jobs

    qstat -u piedra


# Work in progress

We need to create a path in gridui where we can copy the private GEN files produced by Alicia.

    ssh gridui.ifca.es
    mkdir -p /gpfs/gaes/cms/store/mc/PhaseIISummer17GenOnly/DisplacedSUSY_SmuonToMuNeutralino_M-200_CTau-2_14TeV_PhaseII/GEN

    ssh lxplus.cern.ch
    cd /eos/cms/store/group/phys_higgs/cmshww/calderon/Phase2/CRAB_PrivateMC/DisplacedSUSY_SmuonToMuNeutralino_M-200_CTau-2_14TeV_PhaseII/180808_073714/0000

    scp EXO-PhaseIITDRSpring17GS-00001_3.root piedra@gridui.ifca.es:/gpfs/gaes/cms/store/mc/PhaseIISummer17GenOnly/DisplacedSUSY_SmuonToMuNeutralino_M-200_CTau-2_14TeV_PhaseII/GEN/.
    scp EXO-PhaseIITDRSpring17GS-00001_4.root piedra@gridui.ifca.es:/gpfs/gaes/cms/store/mc/PhaseIISummer17GenOnly/DisplacedSUSY_SmuonToMuNeutralino_M-200_CTau-2_14TeV_PhaseII/GEN/.

Now we can test Pablo's code with Alicia's private GEN production.

    ssh gridui.ifca.es
    ...
    python make_gridui.py /gpfs/gaes/cms/store/mc/PhaseIISummer17GenOnly/DisplacedSUSY_SmuonToMuNeutralino_M-200_CTau-2_14TeV_PhaseII/GEN 1000 /gpfs/projects/cms/piedra/code/madanalysis5/tools/delphes/test

It looks like gridui is busy.

    qstat -u piedra

    job-ID  prior   name       user         state submit/start at     queue                          slots ja-task-ID 
    -----------------------------------------------------------------------------------------------------------------
     135233 0.60165 DisplacedS piedra       qw    08/14/2018 15:35:16                                    1

