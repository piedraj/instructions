# Connect to lxplus

    ssh -Y lxplus.cern.ch -o ServerAliveInterval=240


# Set the Bash shell

    bash -l


# Setup a CMSSW release

    export SCRAM_ARCH=slc6_amd64_gcc630
    cmsrel CMSSW_9_4_9
    cd CMSSW_9_4_9/src
    cmsenv


# Connect to GitHub with SSH

The instructions to generate an SSH key can be found [here](https://help.github.com/en/articles/connecting-to-github-with-ssh). Once you have the SSH key it has to be [associated with your GitHub account](https://github.com/settings/keys). If the key is not permanently added, the following should be done in every new login.

    eval "$(ssh-agent -s)"
    ssh-add ~/.ssh/id_rsa
    ssh-add -l


# Get the latino main code

    cd $CMSSW_BASE/src/
    git clone --branch 13TeV git@github.com:latinos/setup.git LatinosSetup
    source LatinosSetup/SetupShapeOnly.sh


# Create your user configuration

    cd $CMSSW_BASE/src/LatinoAnalysis/Tools/python
    cp userConfig_TEMPLATE.py userConfig.py

    emacs -nw userConfig.py

Replace the following line

    baseDir = '/afs/cern.ch/user/x/xjanssen/cms/HWW2015/'

with your own directory,

    baseDir = '/afs/cern.ch/user/[YourInitial]/[YourUsername]/cms/HWW2015/'

And add the following line

    batchType = 'condor'


# Get PlotsConfigurations

    cd $CMSSW_BASE/src/
    git clone git@github.com:latinos/PlotsConfigurations.git


# Compile

    cd $CMSSW_BASE/src/
    cmsenv
    scram b -j 10


# Produce a valid VOMS proxy

    voms-proxy-init -voms cms -rfc --valid 168:0
    cmsenv


# Produce histograms

We are following the [WW configuration](https://github.com/latinos/PlotsConfigurations/tree/master/Configurations/WW/Full2017).

    cd $CMSSW_BASE/src/PlotsConfigurations/Configurations/WW/Full2017

    mkShapesMulti.py \
        --pycfg=configuration.py \
        --inputDir=/eos/cms/store/group/phys_higgs/cmshww/amassiro/HWWNano/ \
        --doBatch=True \
        --batchQueue=workday \
        --treeName=Events \
        --batchSplit=Samples,Files


# Check job status

    condor_q

And wait until all jobs have finished.


# Group (hadd) histograms

    mkShapesMulti.py \
        --pycfg=configuration.py \
        --inputDir=/eos/cms/store/group/phys_higgs/cmshww/amassiro/HWWNano/ \
        --doHadd=True \
        --batchSplit=Samples,Files \
        --doNotCleanup


# Draw distributions

Check that `samples.py and `plot.py` have the same content. Remove the proccesses that are not in `samples.py` from `plot.py`.

    mkPlot.py \
        --pycfg=configuration.py \
        --inputFile=rootFile/plots_WW.root \
        --minLogC=0.01 \
        --minLogCratio=0.01 \
        --maxLogC=1000 \
        --maxLogCratio=1000 \
        --showIntegralLegend=1


# Copy or link to the www area

To be filled.
