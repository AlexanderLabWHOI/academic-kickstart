+++
title = "Running Jupyter Notebooks Remotely with Slurm"
subtitle = ""

# Add a summary to display on homepage (optional).
summary = "Using SSH to tunnel into jupyter notebooks deployed remotely on slurm"

date = 2019-03-11T18:30:13-04:00
draft = false

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = ["halexand"]

# Is this a featured post? (true/false)
featured = true

# Tags and categories
# For example, use `tags = []` for no tags, or the form `tags = ["A Tag", "Another Tag"]` for one or more tags.
tags = ["conda", "jupyter", "HPC", "computing", "data analysis"]
categories = ["computation", "bioinformatics", "python"]

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["deep-learning"]` references
#   `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
# projects = ["internal-project"]

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
[image]
  # Caption (optional)
  caption = ""

  # Focal point (optional)
  # Options: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight
  focal_point = ""
+++
I run the bulk of my bioinformatic analyses remotely on a server or HPC, as they require more computational power or space than I have on my local computer. Rather than transfer the intermediate byproducts of these analyses (which may often be very large) to my local machine, I  prefer to examine and analyze the data remotely using Jupyter. As jupyter notebook are browser-based, if you run the command `jupyter notebook` on a remote machine you will not be able to automatically interact with the jupyter dashboard as you do not have access to a browser on the remote machine. Rather, you need to create a connection between your local browser and the remote Jupyter session. Here I am showing a special case, where you might want to run jupyter notebooks on a larger compute node via an interactive session with slurm.

## Starting your interactive job with slurm

First things first: start up a `tmux` session (or `screen` if you prefer). If I am looking to have some program running for longer than I am wanting to keep a terminal window open -- `tmux` or `screen` are great options as they keep your session from timing out on remote machines. I have a preference for [tmux](https://superuser.com/questions/236158/tmux-vs-screen), but it is really up to you.

```
tmux new -s jupyter
```

Now that we are in our new tmux session, it is time to request an real-time run on the remote HPC. Using the slurm command [srun](https://slurm.schedmd.com/srun.html), I am asking for 2 hours to run on two CPUs on a queue called main. You can customize this to your needs and resources by requesting more nodes, memory, etc.:


```
srun -p main --time=02:00:00 --ntasks-per-node 2 --pty bash
```

This will log you onto some node which will be noted in your command prompt. For example, my command line prompt changed from `halexander@hpc` to `halexander@node1`.

I have many commands that I like in my `.bash_profile` that are not otherwise carried over to this new machine that we just logged into, so go ahead and source my bash profile (`source ~/.bash_profile`).

## Creating a conda environment and starting a jupyter notebook

I like to run each of my various projects in its own conda environment. There are many reasons for this: reproducibility, control over program versions, dealing with conflicting package requirements, and, especially for on a shared compute resource (like an HPC), bypassing having root permission for installing programs. Another nice bonus for this particular case is that conda environments will automatically have `jupyter` installed. So, create a conda environment (if you haven't already):

```
conda create conda-env
```
and activate it:

```
source activate conda-env
```

You should now see that your terminal prompt has changed to something like the following, indicating that you are logged onto the interactive node and working within the conda-env environment:

```
(conda-env) halexander@node1
```

One issue I encountered (that may be specific to my local HPC) that I want to note. If I try to run the command `jupyter notebook` right away I get the following error:

```
Traceback (most recent call last):
  File "/address/home/halexander/.conda/envs/conda-env/lib/python3.6/site-packages/traitlets/traitlets.py", line 528, in get
    value = obj._trait_values[self.name]
KeyError: 'runtime_dir'
....
PermissionError: [Errno 13] Permission denied: '/run/user/12746'
```

To get around this issue I found some help on [StackOverflow](https://stackoverflow.com/questions/35878178/jupyter-notebook-permission-error). (Google is your friend for pesky errors like this.) This simple export command fixed my problem:

```
export XDG_RUNTIME_DIR=""
```

Now, it is time to start up a jupyter notebook! On the remote machine type:

```
jupyter notebook --no-browser --port=8888
```

(Note, the default is for jupyter notebook to automatically open a browser -- but we can't do that on a remote server, so we bypass that function with the `--no-browser` flag.)

I regularly want to run this command and hate typing, so I went ahead and created a function to streamline this process:
```
function jpt(){
    jupyter notebook --no-browser --port=$1
}
```

This allows you to just type `jpt` and a port number and the command will be taken care of. If you want to use this function, simply copy the above and place it in your `.bash_profile`.

Now, `jpt 8888` will start a jupyter notebook on the port 8888.

If all is well, after running the above command, you should see something like the following:

```
[I 14:22:55.931 NotebookApp] Writing notebook server cookie secret to /hpc/home/halexander/.local/share/jupyter/runtime/notebook_cookie_secret
[I 14:23:01.371 NotebookApp] [jupyter_nbextensions_configurator] enabled 0.4.1
[I 14:23:01.371 NotebookApp] Serving notebooks from local directory: /vortexfs1/scratch/halexander/tara/sourmash-analysis
[I 14:23:01.371 NotebookApp] The Jupyter Notebook is running at:
[I 14:23:01.371 NotebookApp] http://localhost:8888/
[I 14:23:01.371 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
```

##Creating an SSH tunnel

You are now ready to create a [tunnel](https://en.wikipedia.org/wiki/Tunneling_protocol) from your local computer to the jupyter notebook running on the HPC.

Open a new terminal on your local machine. In the above example, I started a notebook on `node1` at port `8888` and my username is `halexander` and the address of my HPC is `hpc.address.edu`. So, to create the tunnel I would type the following:

```
ssh -t -t halexander@hpc.address.edu -L 8888:localhost:8888 ssh node1 -L 8888:localhost:8888
```

This is a lot of typing, so it is simpler to create another bash function that you can put in your local `.bash_profile`.

```
function jptnode(){
    # Forwards port $1 from node $2 into port $1 on the local machine and listens to it
        ssh -t -t halexander@hpc.address.edu -L $1:localhost:$1 ssh $2 -L $1:localhost:$1
}
```

You will of course want to customize the above function to contain your username in place of `halexander` and the address of your hpc in place of `hpc.address.edu`.

The `$` indicates the input variables in this function, with `$1` being the port you specified for the jupyter notebook and `$2` being the name of the node you are running the notebook on. So, to use this function you would type:

```
jptnode 8888 node1
```

Once you run this, you should notice that your terminal window has been logged on to your remote HPC and then logged onto the requested node.

You can now open your browser of choice and go to `localhost:8888` and you should see the jupyter dashboard. You should be able to start working in jupyter notebooks, downloading data, or do any other things you want to do through the jupyter dashboard. Make sure to shutdown the jupyter notebook when you are done.

## A few important notes

1) Before you connect to a remote server with jupyter notebook make sure that you have configured jupyter with password information. You can do this by editing the `jupyter-notebook_config.json` which is usually located in `~/.jupyter` or by typing:

```
jupyter notebook password
```
which will prompt you for a password that will be used for future notebooks.

2) Make sure you shutdown  your jupyter notebook when you are done. To do this, you can log back onto the tmux session you started earlier (`tmux a -t jupyter`) where the jupyter notebook is running and use `ctrl-C` should shutdown the jupyter notebook. If you run into issues with a port still being used, chances are that a notebook is still running somewhere.
