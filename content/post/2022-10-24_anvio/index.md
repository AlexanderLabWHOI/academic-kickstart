+++
title = "Using anvi'o in interactive mode"
subtitle = "Mostly just a bunch of installation steps"

# Add a summary to display on homepage (optional).
summary = "How to use anvi'o in interactive mode specifically on WHOI's server"

date = 2022-10-24T15:25:12-04:00
draft = false

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = ['akrinos']

# Is this a featured post? (true/false)
featured = true

# Tags and categories
# For example, use `tags = []` for no tags, or the form `tags = ["A Tag", "Another Tag"]` for one or more tags.
tags = ["python","data visualization","anvio","bioinformatics","genomics"]
categories = ["computation", ]

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
[image]
  # Caption (optional)
  caption = ""

  # Focal point (optional)
  # Options: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight
  focal_point = "Center"
+++

Anvi'o is a powerful tool for analyzing and vizualizing 'omics data, much of which relies on one's ability to visualize those data. On Poseidon, it can sometimes be difficult to work with these kinds of plotting tools, because we're working on the command line. To use anvi'o interactively on Poseidon, we need to go through a few extra steps.

I'll walk you through the whole installation of anvi'o here from start to finish! Most of these instructions are also available on anvio.org. The other half of these instructions are mostly in the posts [here](https://alexanderlabwhoi.github.io/post/2019-03-08_jpn_slurm/) (for Mac) and [here](https://alexanderlabwhoi.github.io/post/2019-07-24-slurm-win/) (for Windows) that we've previously uploaded to the blog!

### Step One: create a fresh `conda` environment for anvi'o and install dependencies

I like using `mamba`. That will make the rest of your installs much faster. So I've modified anvi'o's installation instructions a little bit to fit that principle. After logging into Poseidon's login node (and making sure that you have the most recent and/or your preferred anaconda module loaded), we'll run:

```
conda create -y --name anvio python=3.6 mamba
```

We'll wait for that to finish, then execute `conda activate anvio` (or whatever you chose to name your environment! Next, we have a whole list of packages we'll want mamba to go ahead and (efficiently) install for us in order for anvi'o to work. You should be able to just copy, paste, and execute this entire code block right in your terminal window.

```
mamba install -y -c bioconda "sqlite>=3.31.1"
mamba install -y -c bioconda prodigal
mamba install -y -c bioconda mcl
mamba install -y -c bioconda muscle=3.8.1551
mamba install -y -c bioconda hmmer
mamba install -y -c bioconda diamond
mamba install -y -c bioconda blast
mamba install -y -c bioconda megahit
mamba install -y -c bioconda spades
mamba install -y -c bioconda bowtie2 tbb=2019.8
mamba install -y -c bioconda bwa
mamba install -y -c bioconda samtools=1.9
mamba install -y -c bioconda centrifuge
mamba install -y -c bioconda trimal
mamba install -y -c bioconda iqtree
mamba install -y -c bioconda trnascan-se
mamba install -y -c bioconda r-base
mamba install -y -c bioconda r-stringi
mamba install -y -c bioconda r-tidyverse
mamba install -y -c bioconda r-magrittr
mamba install -y -c bioconda r-optparse
mamba install -y -c bioconda bioconductor-qvalue
mamba install -y -c bioconda fasttree
mamba install -y -c bioconda vmatch
mamba install -y -c bioconda fastani ## anvio says you don't need this one
```

Note: anvi'o's website says that you don't need the `fastani` package installed for the software to work. The install went fine for me. The whole install process might take quite a while, depending on the speed of the connection. If you elect to use `conda` over `mamba`, it will take a *lot* longer.

### Step Two: actually install anvi'o

You should execute this in a folder where you don't mind software files being downloaded to: 

```
curl -L https://github.com/merenlab/anvio/releases/download/v7.1/anvio-7.1.tar.gz \
        --output anvio-7.1.tar.gz
pip install anvio-7.1.tar.gz
```

Now, you should be good to go to run anvi'o!

### Step Three: get a worker node spun up to do your bidding

We'll run an interactive job on Poseidon using `srun`. You can modify this command as needed if you want this job to go for longer than two hours...or if people are hogging all the `compute` nodes and you need to hedge your bets with a pre-empt-able `scavenger` run, for example.

```
srun -p compute --time=02:00:00 --mem=50gb --pty bash
```
Eventually, this should log you into a clean worker node, so your prompt should change from <username>@poseidon-l<1 or 2>.whoi.edu to <username>@pn<# of node you were assigned>. Now, you're ready to not annoy the IT department by executing expensive programs on the common login node! Make sure now to `conda activate anvio`.

### Step Four: run anvi'o interactive, but make sure to specify a port

In order to run this, I am going to use the example the [Eren lab](https://merenlab.org/2016/02/27/the-anvio-interactive-interface/) provides, since I don't use anvi'o much. Note that their link is broken on their tutorial website. I found one that works [here](https://github.com/meren/anvio/blob/master/anvio/tests/sandbox/example-newick-tree.txt). I just saved the Newick text at that link to a file called `meren_tree.txt`, then (inside my interactive session!), I executed: 

```
anvi-interactive -t meren_tree.txt -p profile.db --title 'Simple tree test' --server-only -P 5785
```

### Step Five: log into your node and forward that port to your browser

All of these steps are straight out of our tutorial on running a Jupyter notebook remotely that I linked above. If you use Windows, be sure to check out that post - I'm only using the Mac command here.

```
ssh -t -t <username>@poseidon-l1.whoi.edu -L 5785:localhost:5785 ssh <the name of the "pn" node from above that showed up when you ran srun> -L 5785:localhost:5785
```

To summarize, above you need to swap out:
- your own username
- the port number if you changed it
- the name of the worker node you ssh'd into

For example, if my username is "akrinos", I used port 5786, and I ssh'd into "pn075", I would type:

```
ssh -t -t akrinos@poseidon-l1.whoi.edu -L 5786:localhost:5786 ssh pn075 -L 5786:localhost:5786
```

Be sure to check out our more detailed post on ssh tunneling if you want shortcuts for doing this!

### Step Six: access your interactive session

Simply type `localhost:5786` into your web browser (replacing this second number with your present node), and you should be able to see your anvi'o interactive session! Feel free to use any `anvi-interactive` command and accessory files that you want to after creating/uploading those files on Poseidon.

Happy interacting!s
