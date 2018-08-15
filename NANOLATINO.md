# nanoLatino

These notes should be implemented in the evolving code that reads nanoLatino trees.


# Evolving code at IFCA

So far it compares the Lepton, Electron and Muon collections. It works for both data and MC.

    cd AnalysisCMS/test
    root -l -b -q runTestNano.C


# How to normalize the MC

This has changed with respect to the 2016 latino trees. While in the past we were using the sign, now we take the full `Generator_weight` value.

    baseW * Generator_weight * puWeight * lumi


# Can I use tight electrons?

In the current nanoLatino production there is only one tight electron that can be used in the Lepton collection. It is `Lepton_isTightElectron_TightFall17`. To redefine offline other tight leptons one should require thresholds as in the example below, following the [Multivariate Electron Identification for Run2
](https://twiki.cern.ch/twiki/bin/viewauth/CMS/MultivariateElectronIdentificationRun2) instructions.

    Electron_mvaFall17V2noIso_WPL[Lepton_electronIdx[idx]] < -0.86 (barrel high pt)
    Electron_mvaFall17V2noIso_WPL[Lepton_electronIdx[idx]] < -0.72 (endcap high pt)


# What triggers should I use?

**DoubleEG**

    DATA and MC: HLT_Ele23_Ele12_CaloIdL_TrackIdL_IsoVL

**DoubleMuon**

    DATA: HLT_Mu17_TrkIsoVVL_Mu8_TrkIsoVVL_DZ       (run <= 299367)
          HLT_Mu17_TrkIsoVVL_Mu8_TrkIsoVVL_DZ_Mass8 (run >  299367)
      MC: HLT_Mu17_TrkIsoVVL_Mu8_TrkIsoVVL_DZ

**MuonEG**

We need to apply a lumi weighted DZ efficiency SF, which is 0.86 for emu and 0.986 for mue.

    DATA: HLT_Mu12_TrkIsoVVL_Ele23_CaloIdL_TrackIdL_IsoVL_DZ OR HLT_Mu23_TrkIsoVVL_Ele12_CaloIdL_TrackIdL_IsoVL_DZ (run <= 299367)
          HLT_Mu12_TrkIsoVVL_Ele23_CaloIdL_TrackIdL_IsoVL_DZ OR HLT_Mu23_TrkIsoVVL_Ele12_CaloIdL_TrackIdL_IsoVL    (run >  299367)
    MC:   HLT_Mu12_TrkIsoVVL_Ele23_CaloIdL_TrackIdL_IsoVL OR HLT_Mu23_TrkIsoVVL_Ele12_CaloIdL_TrackIdL_IsoVL

**SingleMuon**

    DATA and MC: HLT_IsoMu27

**SingleElectron**

    DATA and MC: HLT_Ele35_WPTight_Gsf
