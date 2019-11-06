# MCPRODUCTION

These instructions need to be detailed and expanded.

    ssh -Y lxplus.cern.ch -o ServerAliveInterval=240
    bash -l
    
    mkdir testmcproduction
    cd testmcproduction
    cp /afs/cern.ch/user/c/cprieels/public/ForJonatan/DMScalar_ttbar01j_mphi_50_mchi_1_gSM_1p0_gDM_1p0_tarball.tar.xz .

    cmsrel CMSSW_8_0_21
    pushd CMSSW_8_0_21/src/
    cmsenv
    popd

    tar xvf DMScalar_ttbar01j_mphi_50_mchi_1_gSM_1p0_gDM_1p0_tarball.tar.xz
    ./runcmsgrid.sh 5000 $RANDOM 1
