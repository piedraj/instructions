# Introduction

The goal of this exercise is to get acquainted with a particle physics measurement, analyzing proton-proton collisions produced by the Large Hadron Collider (LHC) and recorded by the Compact Muon Solenoid (CMS) experiment.

# Physics

The measurement to be performed is the top quark pair production cross section in proton-proton collisions at the CMS experiment. A recommended reading is this [CMS paper at 8 TeV](https://link.springer.com/article/10.1007/JHEP02(2014)024).

# Technical part

The code is executed in the **Ubuntu operating system** using the [ROOT](https://root.cern/) data analysis framework developed at CERN. To work in the Ubuntu operating system using ROOT regardless of your operating system (such as macOS or Windows) we will work with [Docker containers](https://www.docker.com/resources/what-container/). A container is a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another. A Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings.

# Framework setup

Install XQuartz. **[macOS]**

    https://www.xquartz.org/

Install Docker Desktop.

    https://www.docker.com/get-started/

Download the ROOT Ubuntu image from Docker Hub.

    docker pull calderona/root-ubuntu16

Check your Docker images.

    docker images

If you want to delete some image.

    docker rmi calderona/root-ubuntu16

Needed for display. **[macOS]**

    ip=$(ifconfig en0 | grep inet | awk '$1=="inet" {print $2}')
    export DISPLAY=:0
    /opt/X11/bin/xhost + $ip

Download the HEPAnalysis code. **[Unix]**

    wget https://calderon.web.cern.ch/calderon/codes/HEPAnalysis.tgz

Download the HEPAnalysis code. **[macOS]**

    curl -L https://calderon.web.cern.ch/calderon/codes/HEPAnalysis.tgz > HEPAnalysis.tgz

Extract the HEPAnalysis code.

    tar -xvzf HEPAnalysis.tgz

Run the Docker image.

`userhome` will point to $FULLPATH/HepAnalysis.

    docker run --rm -it -v $FULLPATH/HEPAnalysis/:/userhome -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY=$ip:0 calderona/root-ubuntu16 bash

Jonatan's laptop.

    docker run --rm -it -v /Users/Pandora/HEPAnalysis/:/userhome -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY=$ip:0 calderona/root-ubuntu16 bash

# CMS practical exercise

The physics behind this exercise, together with an introduction of some high energy concepts such as purity or trigger efficiencies, are presented in [CMS.pdf](https://github.com/piedraj/instructions/blob/master/CMS.pdf). The provided HEPAnalysis code is also briefly explained here.

# Your turn

0. Read [CMS.pdf](https://github.com/piedraj/instructions/blob/master/CMS.pdf).
1. Execute the provided code out-of-the-box and look at the produced histograms.
2. Open the provided code and modify it to produce an additional histogram.

# Credit

This exercise has been developed by **Alicia Calderón**, based on a [CMS HEP tutorial](https://ippog-static.web.cern.ch/ippog-static/resources/2012/cms-hep-tutorial.html) by the International Particle Physics Outreach Group (IPPOG). The data provided for the analysis is CMS [open data](https://opendata.cern.ch/).
