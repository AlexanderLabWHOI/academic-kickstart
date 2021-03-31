+++
title = "Fixing my matplotlib font woes"
subtitle = "Falling back to DejaVu Sans"

# Add a summary to display on homepage (optional).
summary = "Linux machine font issues"

date = 2021-03-30T15:25:12-04:00
draft = false

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = ['halexand']

# Is this a featured post? (true/false)
featured = true

# Tags and categories
# For example, use `tags = []` for no tags, or the form `tags = ["A Tag", "Another Tag"]` for one or more tags.
tags = ["python","matplotlib", "fonts", "data visualization"]
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

Over the last few years I have made a transition in my workflow from doing most of my data exploration and plotting on my local machine (a 2016 MacBook laptop) to doing most of my plotting on a remote HPC (check out this [other blog post]({{< relref "post/2019-03-08_jpn_slurm" >}}) for more on that). This workflow is very convenient for many reasons (keeping all files in one location, more extendability and power for my notebooks that my personal laptop can't handle, minimizing file storage on my local machine, etc. etc.).

However, I *could not* get any of my font specifications to work in `matplotlib` (or any dependent programs) in my notebooks that I was running via ssh tunnel. Period.

I constantly got some variation of `findfont: Font family ['sans-serif'] not found. Falling back to DejaVu Sans.`.:

![Error message](resources/error-message.png "Error message from `matplotlib`")

This happened both when I directly specified the font in my notebook:

```
import matplotlib.pyplot as plt
plt.rcParams["font.family"] = "sans-serif"
```

And also when I modified my `matplotlibrc` file (which you can think of as the `.bashrc` file equivalent in `matplotlib`).

I figured I was simply doing something wrong and eventually gave up on trying to fix it and just succumbed to my fate of manually changing fonts post-plotting in Adobe Illustrator. I did this for... several years.

And then yesterday, I had an epiphany. My remote HPC probably doesn't have Microsoft  fonts installed.

![Facepalm](resources/picard-meme-facepalm.jpg "Facepalm")

And so, I present to you...

## How to specify fonts in `matplotlib` when you are working on a Linux system:

First off, I highly recommend using `conda` environments for your plotting analysis. Regardless of what you do, the following should work... but I do recommend trying to tidy up your work spaces with `conda`.

Conveniently, there is a conda package [`mscorefonts`](https://anaconda.org/conda-forge/mscorefonts) that contains all the "core fonts" that are used for the Web. These fonts include: Andale Mono, Arial, Arial Black, Comic Sans MS, Courier New, Georgia, Impact, Times New Roman, Trebuchet MS, Verdana, and Webdings. While not totally comprehensive, it provides enough font variability for my purposes.

After having activated whatever environment you are using (for the sake of demonstration mine is called `general_plotting`), you can then install the `mscorefonts` package.

```
conda activate general_plotting
conda install -c conda-forge mscorefonts
```

This will install and make those fonts available to you within your environment. We are almost done-- but a few more steps just to make it work.

I found that I had to delete my cached `matplotlib` folder. If you have `conda` installed I believe that it should be located in your home directory; however, if you can't find it there leave me a comment below.

```
rm ~/.cache/matplotlib -rf
```

Now, any adjustments you want to make should be fairly straightforward!

I have found it easiest to directly edit my `matplotlibrc` file as I generally like to use Arial as the default font for all my plots. This file can be a bit buried (especially if you are using `conda` environments). One easy way to find your file is to open a notebook within whatever environment you are using and type the following:

```
import matplotlib
print(matplotlib.matplotlib_fname())
```

This command will print the absolute path to your `matplotlibrc`.

Within this file, I edited the file such that I uncommented the following lines and made the following specifications:

```
font.family:  sans-serif
font.sans-serif: Arial
```

As a random aside while you have your `matplotlibrc` file open and handy, you should consider changing your `pdf.fonttype` to `TrueType` rather than the default `Type 3`. If you go to edit or tinker with a `.pdf` file sometime down the line, you might be upset to discover that all the fonts have been converted to vectors (or outlined shapes of the fonts that can't be easily altered). You can set your default behavior to be exporting editable `TrueType` fonts by finding `pdf.fonttype` in `matplotlibrc` and uncommenting it and changing it as follows:

```
pdf.fonttype:       42
```

And with that, I say to you happy plotting! You can now change your fonts as you please and not be stuck with DejaVu Sans (a font that I really, *really*, **REALLY** dislike).

Happy plotting!
