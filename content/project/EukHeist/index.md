+++
title = "EukHeist: Recovering Eukaryotic MAGs from Metagenomes"
date = 2021-07-02T15:15:49-04:00
draft = false

# Tags: can be used for filtering projects.
# Example: `tags = ["machine-learning", "deep-learning"]`
tags = ['Metagenome', 'Eukaryote', 'MAGs', 'snakemake', "Tara Oceans"]

# Project summary to display on homepage.
summary = ""

# Slides (optional).
#   Associate this page with Markdown slides.ß
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
Eukaryotic microbes, or protists, are key drivers of marine ecosystems, with roles ranging from primary producers (phytoplankton) to consumers (heterotrophs or mixotrophs). As with their bacteria and archaea, many protists are difficult or impossible to culture. This has limited our ability to directly interrogate their biology in the laboratory and has led us to underestimate their diversity across marine ecosystems. Molecular and genomic approaches, particularly those applied to whole, mixed communities (e.g. metagenomics, metatranscriptomics), have shed light on the ecological roles, evolutionary histories, and physiological capabilities of these organisms.

EukHeist presents a scalable and reproducible bioinformatic pipeline to facilitate the retrieval of eukaryotic metagenome assembled genomes (MAGs) from mixed metagenomes, called EUKHeist (https://github.com/AlexanderLabWHOI/EUKHeist). EUKHeist streamlines and automates the discovery, recovery, quality assessment, and analysis of eukaryotic metagenome assembled genomes (MAGs) from mixed community metagenomes. EukHeist incorporates taxonoßßmic annotation through EUKulele (https://github.com/AlexanderLabWHOI/EUKulele) as well as scalable structural and functional annotation of coding regions in eukaryotic MAGs with EukMetaSanity (https://github.com/cjneely10/EukMetaSanity).

**Project repository:** https://github.com/AlexanderLabWHOI/EUKHeist

*Funding for this project provided by the Simons Foundation and WHOI*
