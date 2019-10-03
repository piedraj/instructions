# Connect to lxplus

    ssh -Y lxplus.cern.ch -o ServerAliveInterval=240


# Set the Bash shell

    bash -l


# Setup a CMSSW release

    cmsrel CMSSW_10_2_15_patch2
    cd CMSSW_10_2_15_patch2/src
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


# Obtain a VO CMS certificate

The complete set of instructions can be found [here](https://twiki.cern.ch/twiki/bin/view/CMSPublic/SWGuideLcgAccess#How_to_register_in_the_CMS_VO). In order to obtain a VOMS certificate you must first get a personal certificate (Steps 1 and 2 of the previous link), which consists of a private and a public keys (PEM files). You can follow the [personal certificates](https://twiki.cern.ch/twiki/bin/view/CMSPublic/PersonalCertificate) TWiki to install the certificate in your browser. Then you will need to export the certificate into a pair of PEM files, following these [how to](https://ca.cern.ch/ca/Help/?kbid=024010) instructions. Once you have your personal certificate you can proceed with Step 3. Go to the [VOMS registration](https://voms2.cern.ch:8443/voms/cms/register) page and submit a registration. Then you need to wait for an admin to confirm your registration (you will receive a notification email). Here you can find the VOMS [FAQ](https://twiki.cern.ch/twiki/bin/view/CMSPublic/SWGuideVomsFAQ).

To verify the expiration date of your certificate,

    openssl x509 -subject -dates -noout -in $HOME/.globus/usercert.pem

To check if you can generate proxies,

    grid-proxy-init -debug -verify

To check if you are a member of the CMS VO,

    voms-proxy-init -voms cms


# Produce a valid VOMS proxy

    voms-proxy-init -voms cms -rfc --valid 168:0
    cmsenv


# Produce histograms

    cd $CMSSW_BASE/src/PlotsConfigurations/Configurations/VH2j/Full2016_nanoAODv4

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
        --nThreads=8 \
        --doNotCleanup


# Draw distributions

Check and remove from `plot.py` the proccesses that are not in `samples.py`.

    mkPlot.py \
        --pycfg=configuration.py \
        --inputFile=rootFile/plots_VH2j_2017.root \
        --minLogC=0.01 \
        --minLogCratio=0.01 \
        --maxLogC=1000 \
        --maxLogCratio=1000 \
        --showIntegralLegend=1


# Create a CERN personal website

Follow these [instructions](https://cernbox-manual.web.cern.ch/cernbox-manual/en/web/personal_website_content.html). Once the website has been created you can manage it [here](https://webservices.web.cern.ch/webservices/Services/ManageSite/). In order to allow access to your website, add an `.htaccess` file to your www directory.

    wget https://raw.githubusercontent.com/piedraj/AnalysisCMS/master/test/.htaccess

Check that it contains `Options+Indexes`. To improve the usability of the website add an `index.php` file to every directory and subdirectory.

    wget https://raw.githubusercontent.com/piedraj/AnalysisCMS/master/index.php

If you have followed these steps and the website is unavailable (error 403) please open a ticket with the [CERN Service Portal](https://cern.service-now.com/service-portal/).

# Share the results

    cp -r plotVH2j_2017 /afs/cern.ch/user/p/piedra/www/.
    cd /afs/cern.ch/user/p/piedra/www/plotVH2j_2017
    cp ../index.php .

Go to the web.

    https://piedra.web.cern.ch/piedra/

