# MET Significance with pyRAT

## 0. Installation

Log in to the CERN LXPLUS cluster. Do it from a Windows PowerShell if your operating system is Windows.

    ssh -Y -l piedra lxplus.cern.ch -o ServerAliveInterval=240
    echo $SHELL
    /usr/bin/tcsh
    cd work

Clone the pyRAT (Python ROOT Analysis Toolkit) [repository](https://gitlab.cern.ch/pyrat-crew/pyRAT) from GitLab.
    
    git clone ssh://git@gitlab.cern.ch:7999/pyrat-crew/pyRAT.git

[Get started](https://pyrat.docs.cern.ch/getting_started/).
    
    cd pyRAT
    
    python3 executables/main/first_time_setup.py
    INFO:: Starting first time setup for pyRAT
    Platform: el9. isCI: None
      --> settings/user_configs/piedra.json does not exist. Creating default config.
    Default config created for user 'piedra':
    {
            "username": "piedra",
            "email": "piedra@cern.ch",
            "shell": "csh",
            "pyrat": "/afs/cern.ch/work/p/piedra/pyRAT",
            "input": "./",
            "clean": 999,
            "tmp_proxy_folder": "/tmp/",
            "voms": "cms",
            "site": "lxplus",
            "output": "/eos/user/p/piedra"
    }
    INFO:: pyRAT setup completed. Use `source setup_environment.sh` to initialize the environment.

    source setup_environment.sh
    /cvmfs/cms.cern.ch/rucio/setup.csh: No such file or directory.

Add the [MET Significance repository](https://gitlab.cern.ch/pyrat-crew/met_significan) within pyRAT.
