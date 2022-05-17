# Everything begins here

Log in to lxplus.

    ssh -Y piedra@lxplus.cern.ch -o ServerAliveInterval=240
    bash -l

# First time

Setup the CMSSW release. The code `nanoFakes.C` fails with (at least) `10_2_0` and `10_6_4`.

    cd work
    mkdir fakes

    export SCRAM_ARCH=slc7_amd64_gcc630
    cmsrel CMSSW_10_1_0
    cd CMSSW_10_1_0/src
    cmsenv

    git clone https://github.com/latinos/FakeRateMeasurement

# Get ready

    cd work/fakes/CMSSW_10_1_0/src
    cmsenv
    cd FakeRateMeasurement

# Submit jobs

The tight lepton names might differ between 2016, 2017, 2018. Look at them in `nanoFakes.h` before any job submission. Also before submitting, check that the data and MC samples names in `submitJobs.py` match the current production.

### 2017

    python submitJobs.py -d /eos/cms/store/group/phys_higgs/cmshww/amassiro/HWWNano/Run2017_UL2017_nAODv9_Full2017v9/DATAl1loose2017v9__fakeSel/ -y 2017
    python submitJobs.py -d /eos/cms/store/group/phys_higgs/cmshww/amassiro/HWWNano/Summer20UL17_106x_nAODv9_Full2017v9/MCl1loose2017v9__fakeSelKinMC/ -y 2017

### 2018

    python submitJobs.py -d /eos/cms/store/group/phys_higgs/cmshww/amassiro/HWWNano/Run2018_UL2018_nAODv9_Full2018v9/DATAl1loose2018v9__fakeSel -y 2018
    python submitJobs.py -d /eos/cms/store/group/phys_higgs/cmshww/amassiro/HWWNano/Summer20UL18_106x_nAODv9_Full2018v9/MCl1loose2018v9__fakeSelKinMC/ -y 2018

# Babysit jobs

    condor_q
    condor_q -hold -af HoldReason

# Wrap it up

    cd results

### 2017

    hadd -f -k hadd_wjets.root nanoLatino_WJetsToLNu*.root
    hadd -f -k hadd_zjets.root nanoLatino_DYJetsToLL*.root

2017 data can be "hadded" in one shot.

    hadd -f -k hadd_data.root nanoLatino_*_Run201*.root

    mkdir 2017
    mv *.root 2017/.

### 2018

    hadd -f -k hadd_wjets.root nanoLatino_WJetsToLNu*.root
    hadd -f -k hadd_zjets.root nanoLatino_DYJetsToLL*.root

For 2018 data there are too many files, and the `hadd` has to be done in two steps.

    hadd -f -k hadd_EGamma_Run2018.root nanoLatino_EGamma_Run2018*.root
    hadd -f -k hadd_DoubleMuon_Run2018.root nanoLatino_DoubleMuon_Run2018*.root

    hadd -f -k hadd_data.root hadd_EGamma_Run2018.root hadd_DoubleMuon_Run2018.root

    rm hadd_EGamma_Run2018.root
    rm hadd_DoubleMuon_Run2018.root

    mkdir 2018
    mv *.root 2018/.

# Extract the fake and prompt rates

    root -l -b -q getFakeRate.C\(2017\)
    root -l -b -q getFakeRate.C\(2018\)

# Share on the web

    mkdir /afs/cern.ch/user/p/piedra/www/fakes

    cp -r png/2017 /afs/cern.ch/user/p/piedra/www/fakes/.
    cp -r png/2018 /afs/cern.ch/user/p/piedra/www/fakes/.

    pushd /afs/cern.ch/user/p/piedra/www/fakes
    cp ../index.php .
    find . -type d -exec cp index.php {} \;
    popd

And the results should appear here,

    https://piedra.web.cern.ch/piedra/fakes/

# Some relevant physics

   * The jet pt thresholds for electrons are 35 GeV for 0-jet, 1-jet and 2-jets.
   * The jet pt thresholds for muons are 20 GeV for 0-jet, 25 GeV for 1-jet, and 35 GeV for 2-jets.