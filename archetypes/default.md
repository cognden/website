---
title: {{ replace .File.ContentBaseName "-" " " | title }}
date: {{ .Date }}
tags: ["draft"]
showToc: true
TocOpen: false
draft: false
# description: "Desc Text."
summary: "Summarize text manually."
cover:
    image: "<image path/url>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
---
