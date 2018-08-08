# brilcalc

Log in to lxplus.

    ssh -Y piedra@lxplus.cern.ch -o ServerAliveInterval=240

    bash -l

Go to the **Prerequisite** section of the [BRIL Work Suite](http://cms-service-lumi.web.cern.ch/cms-service-lumi/brilwsdoc.html) and export the PATH that corresponds to the _centrally installed virtual environment on lxplus_.

    export PATH=$HOME/.local/bin:/afs/cern.ch/cms/lumi/brilconda-1.1.7/bin:$PATH

Do this the first time. Do it also if you want to update the brilcalc version.

    pip uninstall brilws

    pip install --install-option="--prefix=$HOME/.local" brilws

Check your brilcalc version.

    brilcalc --version
    3.3.2

Get the 2017 luminosity. Based on the [PdmV](https://twiki.cern.ch/twiki/bin/view/CMS/PdmV2017Analysis) and on the [TwikiLUM](https://twiki.cern.ch/twiki/bin/viewauth/CMS/TWikiLUM) TWikis one should use the following.

    brilcalc lumi -b "STABLE BEAMS" \
                  --normtag /cvmfs/cms-bril.cern.ch/cms-lumi-pog/Normtags/normtag_PHYSICS.json \
                  -u /fb \
                  -i /afs/cern.ch/cms/CAF/CMSCOMM/COMM_DQM/certification/Collisions17/13TeV/Final/Cert_294927-306462_13TeV_PromptReco_Collisions17_JSON.txt

    #Summary: 
    +-------+------+--------+--------+-------------------+------------------+
    | nfill | nrun | nls    | ncms   | totdelivered(/fb) | totrecorded(/fb) |
    +-------+------+--------+--------+-------------------+------------------+
    | 175   | 474  | 206564 | 205445 | 44.172            | 41.530           |
    +-------+------+--------+--------+-------------------+------------------+

If you want to get the luminosity by trigger path.

    brilcalc lumi -b "STABLE BEAMS" \
                  --normtag /cvmfs/cms-bril.cern.ch/cms-lumi-pog/Normtags/normtag_PHYSICS.json \
                  -u /fb \
                  -i /afs/cern.ch/cms/CAF/CMSCOMM/COMM_DQM/certification/Collisions17/13TeV/Final/Cert_294927-306462_13TeV_PromptReco_Collisions17_JSON.txt \
                  --hltpath "HLT_Ele8_CaloIdL_TrackIdL_IsoVL_PFJet30_v*"

    #Summary: 
    +---------------------------------------------+-------+------+-------+-------------------+------------------+
    | hltpath                                     | nfill | nrun | ncms  | totdelivered(/fb) | totrecorded(/fb) |
    +---------------------------------------------+-------+------+-------+-------------------+------------------+
    | HLT_Ele8_CaloIdL_TrackIdL_IsoVL_PFJet30_v10 | 27    | 56   | 23305 | 0.000             | 0.000            |
    | HLT_Ele8_CaloIdL_TrackIdL_IsoVL_PFJet30_v11 | 18    | 47   | 19043 | 0.001             | 0.000            |
    | HLT_Ele8_CaloIdL_TrackIdL_IsoVL_PFJet30_v12 | 67    | 144  | 90816 | 0.002             | 0.002            |
    | HLT_Ele8_CaloIdL_TrackIdL_IsoVL_PFJet30_v13 | 6     | 27   | 14589 | 0.001             | 0.001            |
    | HLT_Ele8_CaloIdL_TrackIdL_IsoVL_PFJet30_v14 | 2     | 13   | 4469  | 0.000             | 0.000            |
    | HLT_Ele8_CaloIdL_TrackIdL_IsoVL_PFJet30_v8  | 9     | 21   | 5909  | 0.000             | 0.000            |
    | HLT_Ele8_CaloIdL_TrackIdL_IsoVL_PFJet30_v9  | 20    | 84   | 23011 | 0.001             | 0.001            |
    +---------------------------------------------+-------+------+-------+-------------------+------------------+
    #Sum delivered : 0.004
    #Sum recorded : 0.004

