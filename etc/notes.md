## Setup

Review the notes on the Docker `r-base` image [here](https://hub.docker.com/_/r-base)

```
sudo apt-get install docker docker.io
# check for old images and containers
sudo docker ps -a
# if necessary sudo docker rm <process>
sudo docker images
# if necessary sudo docker rmi <imageid>
sudo docker pull r-base
cd ~/Documents/repositories/lhs/etc
sudo docker build --tag=lhs_r_base .
```

## Usage of the base image

```
# interactive mode with the r-base image (Auto starts in R)
sudo docker run -ti --rm --name r_base_interactive r-base
# interactive mode with the lhs_r_base image (Auto starts in R with all packages installed)
sudo docker run -ti --rm --name lhs_r_interactive lhs_r_base

# run a local script
#  -ti is TTY interactive
#  --rm is remove container when done
#  -v is bind mount a volume
#  -w is the working directory
#  -u is the user

sudo docker run -ti --rm -v ~/repositories:/home/docker -w /home/docker -u docker --name build_lhs lhs_r_base R CMD build lhs
sudo docker run -ti --rm -v ~/repositories:/home/docker -w /home/docker -u docker --name check_lhs lhs_r_base R CMD check lhs_*.tar.gz
```

## Usage of the built image for Reverse Dependency Checks

```
sudo docker run -ti --rm -v ~/repositories:/home/docker -w /home/docker/lhs/etc -u docker --name lhs_revdep_check lhs_r_base Rscript lhs_rev_dep_check.R
```

#### For failed installations, check the package directly in the docker

```
sudo docker run -ti --rm -v ~/repositories:/home/docker -w /home/docker/lhs/etc -u docker --name lhs_revdep_failed lhs_r_base "/bin/bash"

# build the package from the repo
R CMD build <my package>
R CMD INSTALL <my package>_<ver>.tar.gz

cd <package>/revdep/checks/<dependency>
wget -r -l2 --accept-regex='<package>[_][0-9.-_]*[.]tar[.]gz' -R "*.txt" https://cran.r-project.org/src/contrib
mv cran.r-project.org/src/contrib/*.tar.gz .
rm -rf cran.r-project.org
R CMD check <dependency>_<rev>.tar.gz
```
