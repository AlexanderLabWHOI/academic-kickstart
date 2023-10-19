+++
title = "eukrhythmic: automating metatranscriptomics"
date = 2021-07-02T15:15:49-04:00
draft = false

# Tags: can be used for filtering projects.
# Example: `tags = ["machine-learning", "deep-learning"]`
tags = ['Transcriptomics','Metatranscriptomics','Community-Ecology']

# Project summary to display on homepage.
summary = ""

# Slides (optional).
#   Associate this page with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides = "example-slides"` references
#   `content/slides/example-slides.md`.
#   Otherwise, set `slides = ""`.
slides = ""

# Optional external URL for project (replaces project detail page).
external_link = ""

# Links (optional).
url_pdf = ""
url_code = ""
url_dataset = ""
url_slides = ""
url_video = ""
url_poster = ""

# Custom links (optional).
#   Uncomment line below to enable. For multiple links, use the form `[{...}, {...}, {...}]`.
# links = [{icon_pack = "fab", icon="twitter", name="Follow", url = "https://twitter.com"}]

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
[image]
  # Caption (optional)
  caption = ""

  # Focal point (optional)
  # Options: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight
  focal_point = "Center"
+++

Metatranscriptomics is a useful probe into the members of an ecological community, capturing community members and their potential functions in a single sequencing effort. Because transcribed RNA is sequenced, via thorough processing of metatrancriptomes, we can decipher both the members constituting a community as well as the genes that each member was expressing at the instant that the sample was collected. Metatranscriptomics is a relatively immature pursuit as compared to genomics or transcriptomics. However, even more palpable is the lack of thorough analysis and readily available tools for metatranscriptomes from the environment. More complicated still is the use of metatranscriptomes for eukaryotes from the environment, many of which have not been previously or thoroughly sequenced and have complex genetic features. Eukrhythmic is an approach to consolidating existing assembly effort for environmental eukaryotes. The pipeline includes assembly via multiple external assembly tools, and then a process of consolidating those assemblies based on sequential clustering steps. Also included with the pipeline is the option to perform downstream functional and taxonomic annotation of retrieved transcripts. 

The goal of this project is both to validate and provide support for the efforts of [marine] microbial ecologists that use metatranscriptomics to assess community structure and function, and to provide a tool that researchers can use to assemble their own environmental metatranscriptomes. Some questions associated with this project are:
1. Can we recover complex communities from shuffled metatranscriptomic sequencing reads? 
2. Does the choice of assembler affect the quality of the assembly output?

**Project Repository**: https://github.com/AlexanderLabWHOI/eukrhythmic
