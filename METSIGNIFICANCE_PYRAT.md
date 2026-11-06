# MET Significance within pyRAT

The _old_ MET Significance instructions can be found [here](https://github.com/piedraj/instructions/blob/master/METSIGNIFICANCE.md).

## 0. Installation

Log in to the CERN LXPLUS cluster. Do it from a Windows PowerShell if your operating system is Windows.

    ssh -Y -l piedra lxplus.cern.ch -o ServerAliveInterval=240

Change the shell to Bash.

    /bin/bash

Clone the pyRAT (Python ROOT Analysis Toolkit) [repository](https://gitlab.cern.ch/pyrat-crew/pyRAT) from GitLab.

    cd work
    
    git clone ssh://git@gitlab.cern.ch:7999/pyrat-crew/pyRAT.git

    cd pyRAT

[Get started](https://pyrat.docs.cern.ch/getting_started/). Create a user-specific configuration file `settings/user_configs/piedra.json`.

    {
        "username": "piedra",
        "email": "piedra@cern.ch",
        "shell": "sh",
        "pyrat": "/afs/cern.ch/work/p/piedra/pyRAT",
        "input": "./",
        "clean": 999,
        "tmp_proxy_folder": "/tmp/",
        "voms": "cms",
        "site": "lxplus",
        "output": "/eos/user/p/piedra"
    }
    
Run the setup script to install all required dependencies.

    python3 executables/main/first_time_setup.py

After installation, source the environment script to activate the setup.

    source setup_environment.sh

## 1. Add the MET Significance workflow

Follow [these instructions](https://gitlab.cern.ch/pyrat-crew/met_significance). Start by cloning the `met_significance` code from GitLab.

    git clone ssh://git@gitlab.cern.ch:7999/pyrat-crew/met_significance

Activate the pyRAT repository environment.

    source setup_environment.sh

The steer file follows the usual pyRAT pattern and may be configured to print, run, submit, recover, or plot jobs.

    python met_significance/macros/steer.py
