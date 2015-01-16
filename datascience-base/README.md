Dockerized Notebook for (Pythonic) Data Science
======================

Docker container with Python data science tools (particularly, pandas, numpy, matplotlib, plotly, sklearn, scikit-image) the IPython notebook (single user).

*This wiseio.io image is based off of the official [https://github.com/ipython/docker-notebook/tree/master/notebook](Dockerfiles from IPython), but using [http://continuum.io](Continuum/Anaconda) rather than bleeding edge base containers.*

## Quickstart the Notebook Server

Set a few environment variables locally (add later to `.bash_profile` if you like):

```
export HACKDAY_CODE_DIR="$(PWD)"           # or wherever you want to code
export HACKDAY_DATA_DIR="$(PWD)/data"      # likewise.
export IPYTHON_PASSWORD=MakeAPassword # not this!
alias wiseds="docker run -d -p 80:8888 -v $(PWD):/workspace/ -v $(PWD)/data:/workspace/data -e "PASSWORD=$IPYTHON_PASSWORD" wiseio/datascience-base ; echo 'Now go to your browser: http://$(boot2docker ip). The password is $IPYTHON_PASSWORD' "
```

Assuming you have docker installed, run this to start up a notebook server over HTTPS.

```
docker run -d -p 80:8888 -v $HACKDAY_CODE_DIR:/workspace/ -v $HACKDAY_DATA_DIR:/workspace/data -e "PASSWORD=$IPYTHON_PASSWORD" wiseio/datascience-base
```

You'll now be able to access your notebook at https://localhost with password MakeAPassword (please change the environment variable above).

If you are on OSX, you'll need to know the name of your VM from boot2docker:

```
 boot2docker ip
```

You'll then connect via `http://<ip>`

## Using the Container ##

Once you're running the container, you can get a terminal window inside if needed:

```
docker exec -it <container_name> bash
```

Here you can add new functionality if you need it. E.g.,

```
apt-get package-name
pip install requirements.txt
...
```

This will dump you into 

## Hacking on the Dockerfile

Clone this repository, make changes then build the container:

```
docker build -t  datascience-base .
docker run -d -p 80:8888 -e "PASSWORD=$(IPYTHON_PASSWORD)" datascience-base
```



## Use your own certificate
This image looks for `/key.pem`. If it doesn't exist a self signed certificate will be made. If you would like to use your own certificate, concatenate your private and public key along with possible intermediate certificates in a pem file. The order should be (top to bottom): key, certificate, intermediate certificate.

Example:

```
cat hostname.key hostname.pub.cert intermidiate.cert > hostname.pem
```

Then you would mount this file to the docker container:

```
docker run -v /path/to/hostname.pem:/key.pem -d -p 443:8888 -e "PASSWORD=pass" wiseio/datascience-base
```

## Using HTTPS
This docker image by default runs IPython notebook in HTTP.  If you'd like to run this in HTTPS,
you can use the `USE_HTTP` environment variable.  Setting it to a non-zero value enables HTTP.

Example:

```
docker run -d -p 443:8888 -e "PASSWORD=$IPYTHON_PASSWORD" -e "USE_HTTP=1" wiseio/datascience-base

You'll then connect via `http://<ip>`
```