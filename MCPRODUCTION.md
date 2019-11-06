# MCPRODUCTION

These instructions need to be detailed and expanded.

    ssh -Y lxplus.cern.ch -o ServerAliveInterval=240
    bash -l
    
    mkdir testmcproduction
    cd testmcproduction
    cp /cvmfs/cms.cern.ch/phys_generator/gridpacks/slc6_amd64_gcc481/13TeV/madgraph/V5_2.2.2/TTbarDM_DiLept/DMScalar_ttbar01j_mphi_50_mchi_1_gSM_1p0_gDM_1p0_tarball.tar.xz .

    cmsrel CMSSW_8_0_21
    pushd CMSSW_8_0_21/src/
    cmsenv
    popd

    tar xvf DMScalar_ttbar01j_mphi_50_mchi_1_gSM_1p0_gDM_1p0_tarball.tar.xz
    ./runcmsgrid.sh 5000 $RANDOM 1
