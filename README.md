# environments
Store locked conda environments for use on different computers 

JupyterHubs typically have a common default environment. This is good because all users have a unified experience and administrators can curate the packages and make sure they all work together. 

*But what if you want to experiment with your own environment or different packages?*

In this case you can install a custom environment. When working on Cloud servers like JupyterHub it's a good idea to treat everything as ephemeral. You want to avoid storing anything permanently on the Cloud because it costs money. So for example, store archival data you're analyzing in `/tmp` (read more [here](https://docs.2i2c.org/data/#quick-recommendations)). 

*Another way to frame this is that if it's easy to recreate something, don't store it permanently*. Instead store the lightweight script to recreate it! The added benefit of this approach is that it starts to make workflows more reproducible and facilitates collaboration. 

So below we go over how to create, store, and recreate conda environments:

### First note the default Python environment
```
(notebook) jovyan@jupyter-scottyhq:~$ conda info -e
# conda environments:
#
base                     /srv/conda
notebook              *  /srv/conda/envs/notebook
```

```
(notebook) jovyan@jupyter-scottyhq:~$ echo $JUPYTER_IMAGE
quay.io/cryointhecloud/cryo-hub-image:6d24b8991c82
```

### Create a new environment
```
(notebook) jovyan@jupyter-scottyhq:~$ mamba create -n sliderule ipykernel
```

```
(notebook) jovyan@jupyter-scottyhq:~$ conda info -e
# conda environments:
#
base                     /srv/conda
notebook              *  /srv/conda/envs/notebook
sliderule                /srv/conda/envs/sliderule
```

### Use the new environment

```
(notebook) jovyan@jupyter-scottyhq:~$ mamba activate sliderule 
```

#### Make the environment available a as Jupyter Notebook Kernel
```
(sliderule) jovyan@jupyter-scottyhq:~$ python -m ipykernel install --user --name sliderule --display-name "sliderule"
Installed kernelspec sliderule in /home/jovyan/.local/share/jupyter/kernels/sliderule
```

*NOTE* to uninstall the kernelspec `jupyter kernelspec uninstall sliderule`


### Save a list of all packages

It's good practice to put this environment definition somewhere publicly accessible so that you can install it on other machines and other people can install the identical environment. For example create an `environments` repository on GitHub and put it there (https://github.com/scottyhq/environments).


```
(sliderule) jovyan@jupyter-scottyhq:~/environments/crycloud$ mamba list --explicit > sliderule-explicit.txt
```

### Re-create the environment

Now that the listing of packages is online, we can easily recreate it next time we login:

```
mamba create --name sliderule --file https://raw.githubusercontent.com/scottyhq/environments/main/crycloud/sliderule-explicit.txt
```

