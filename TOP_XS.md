# Introduction

The goal of this exercise is to get acquainted with a particle physics measurement, analyzing proton-proton collisions produced by the Large Hadron Collider (LHC) and recorded by the Compact Muon Solenoid (CMS) experiment.

# Physics

The measurement to be performed is the top quark pair production cross section in proton-proton collisions at the CMS experiment. A good reference is the following [CMS paper at 8 TeV](https://link.springer.com/article/10.1007/JHEP02(2014)024)

# Technical part

To perform the measurement










   

Por la parte tecnica, aqui tienes la imagen que debes descargarte para correr ROOT en Ubuntu:

   docker pull calderona/root-ubuntu16

Y aqui esta el codigo que usaras. De momento es suficiente con que lo descargues.

   wget https://calderon.web.cern.ch/calderon/codes/HEPAnalysis.tgz

Propongo que nuestra siguiente reuninnn sea por Teams el viernes 12 de febrero a las 10 am. Si quieres comenzar a usar docker antes de dicha reunion puedes hacer lo siguiente:

   docker run --rm -it -v $FULLPATH/HEPAnalysis/:/userhome -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY=$ip:0 calderona/root-ubuntu16 bash

Con dicho comando, el directorio userhome contendra todo el material que has descargado en $FULLPATH/HEPAnalysis. En la reunion del viernes te indicare que cosas concretas puedes empezar a realizar. Por supuesto, cualquier duda que te vaya surgiendo no dudes en preguntarme.

El quark top se desintegra practicamente siempre en un boson W y un quark b. Para tu analisis tienes

   proton + proton --> top + antitop --> Wb + Wb

El quark top se desintegra practicamente siempre en un boson W y un quark b. Para tu analisis tienes

   proton + proton --> top + antitop --> Wb + Wb

Dentro de las posibles desintegraciones del boson W trabajaras con la desintegracion en muon + neutrino de un W, y la desintegracion en dos quarks del otro W,

   Wb + Wb --> (muon + neutrino + b) + (q + q + b)

O dicho de otra forma, en tu estado final esperas dos quarks b, dos jets provenientes de los quarks q de un W, un muon y energia perdida, el neutrino. Los primeros pasos que te pido son:

   1. Leer las diapositivas.
   2. Ejecutar el codigo y mirar los histogramas que produces.
   3. Mirar el codigo y modificarlo para producir, adems, otro histograma, el que quieras.













# Docker



0. Install XQuartz

https://www.xquartz.org/

1. What is a Container?

https://www.docker.com/resources/what-container

A container is a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another. A Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings.

2. Download and install Docker Desktop for Mac

https://docs.docker.com/docker-for-mac/install/

3. Download the image from Docker Hub

docker pull calderona/root-ubuntu16

4. Check docker images

docker images

5. If you want to delete some image

docker rmi calderona/root-ubuntu16

6. Needed for display in macOS

ip=$(ifconfig en0 | grep inet | awk '$1=="inet" {print $2}')
export DISPLAY=:0
/opt/X11/bin/xhost + $ip

7. Download and extract the HEPAnalysis code

# In Unix
wget https://calderon.web.cern.ch/calderon/codes/HEPAnalysis.tgz

# In Mac
curl -L https://calderon.web.cern.ch/calderon/codes/HEPAnalysis.tgz > HEPAnalysis.tgz

tar -xvzf HEPAnalysis.tgz

8. Run the docker image

By doing the following, userhome will point to $FULLPATH/HepAnalysis.

docker run --rm -it -v $FULLPATH/HEPAnalysis/:/userhome -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY=$ip:0 calderona/root-ubuntu16 bash

docker run --rm -it -v /Users/Pandora/HEPAnalysis/:/userhome -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY=$ip:0 calderona/root-ubuntu16 bash




