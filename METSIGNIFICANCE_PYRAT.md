    ssh -Y -l piedra lxplus.cern.ch -o ServerAliveInterval=240
    cd work
    git clone ssh://git@gitlab.cern.ch:7999/pyrat-crew/pyRAT.git
    cd pyRAT
    python3 executables/main/first_time_setup.py



* pyRAT repository: https://gitlab.cern.ch/pyrat-crew/pyRAT


* Setup instructions: https://pyrat.docs.cern.ch/getting_started/




* Add the MET Significance repository within pyRAT: https://gitlab.cern.ch/pyrat-crew/met_significance
