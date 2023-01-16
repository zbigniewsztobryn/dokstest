---
title: "Test  4"
description: ""
lead: " My main concern today is to browse through budgets of movies. I would like to see how it evolved in time and how it's related to genre. This would consider <b>CPI</b> to get most realistic numbers. My main tool in this struggle would be <b>ggplot2</b> in producing charts and <b>lubridate</b> package in calculate nominal budgets considering inflation."
content: ""
date: 2022-10-16T09:19:42+01:00
lastmod: 2020-10-16T09:19:42+01:00
draft: false
toc: true
weight: 40
images: ["say-hello-to-doks.png"]
contributors: ["SQL","Python"]
---

<p align="center">
  <img src="adv_movies.jpg" width="100%" title="hover text">
</p>


\
&emsp; In order to see this repo directly <b><a href="https://github.com/zbigniewsztobryn/worksR/blob/main/Basic%20movie%20analytics%20using%20R.R" target="_blank"> go right on Github repo→</a></b>
\
&emsp; If you would like to keep up with me, go and <b><a href="https://github.com/zbigniewsztobryn/worksR/tree/main/data" target="_blank">get work datasets→</a></b>
### Let's got to work
&emsp;Set working directories in running folder
```bash
setwd(dirname(rstudioapi::getActiveDocumentContext()$path))
```

&emsp;Import packages
```bash
library(ggplot2)
library(dplyr)
```

&emsp;Load build-in dataset
```bash
economics%>% as_tibble() -> data
```

&emsp;This is function getting two diagrams on one plot. It would be used later.
```bash
multiplot <- function(..., plotlist=NULL, file, cols=1, layout=NULL) {
  library(grid)

  # Make a list from the ... arguments and plotlist
  plots <- c(list(...), plotlist)

  numPlots = length(plots)

  # If layout is NULL, then use 'cols' to determine layout
  if (is.null(layout)) {
    # Make the panel
    # ncol: Number of columns of plots
    # nrow: Number of rows needed, calculated from # of cols
    layout <- matrix(seq(1, cols * ceiling(numPlots/cols)),
                     ncol = cols, nrow = ceiling(numPlots/cols))
  }

  if (numPlots==1) {
    print(plots[[1]])

  } else {
    # Set up the page
    grid.newpage()
    pushViewport(viewport(layout = grid.layout(nrow(layout), ncol(layout))))
  }
}
```
### Private expences and unemployment
&emsp;Plot function of private consumption expences
```bash
diagr_pce <- (ggplot(data, aes(x=date, y=pce, size=pop))+
              geom_smooth(se=FALSE, size=1.5, color="#FF7F50", linetype = "dashed")+
              geom_line(size=0.2,color="#FF7F50"))+
              labs(title= 'Private consumption expences in time',
                   x = "Date",
                   y= "PCE [bln]",
                   caption = "diag. [1.1.1]")
```
&emsp;Plot function of unemployment count
```bash
diagr_unemploy <- (ggplot(data, aes(x=date, y=unemploy,size=pop))+
                    geom_smooth(se=FALSE, size=1.5,linetype = "dashed")+
                    geom_line(size=0.2,color='blue'))+
                    labs(title= 'Unemployed number in time',
                         x = "Date",
                         y= "Unempl. [x1000]",
                         caption = 'diag. [1.1.2]')
```

&emsp;Finally we are plotting two diagrams in one print
```bash
multiplot(diagr_pce, diagr_unemploy)
```

<p align="center">
  <img src="Rplot001.png" width="100%" title="hover text">
</p>


### Unemployment rate and insights into economy fluctuations
&emsp;Plot function of unemployment rate in time
```bash
diagr_unemploy_rate <- (ggplot(data, aes(x=date, y=unemploy*100/pop))+
                     geom_smooth(se=FALSE, size=1.5,linetype = "dashed",color='orange')+
                     geom_line(size=0.2,color='orange'))+
                     labs(title= 'Unemployment rate in time',
                          x = "Date",
                          y= "Unemloyment rate [%]",
                          caption= 'diag. [1.2]')
```

```bash
diagr_unemploy_rate
```
<p align="center">
  <img src="Rplot07.png" width="100%" title="hover text">
</p>


&emsp; In the chart, you can see that the economy is subject to business cycles.
These are fluctuations that are referred to as such factors as GDP dynamics
age of import/export, but also the unemployment rate.
At the same time, the long-term trend is generally increasing.
For example, Figure 1.2 shows such cycles
Unemployment in the US have been so far at its highest around 1982 due to the great crisis.
It was caused by the <b><a href="https://en.wikipedia.org/wiki/1979_oil_crisis" target="_blank">Iranian energy crisis of 1979→</a></b>. Rising oil prices have made
incl. in the USA, double-digit inflation and a significant slowdown in the economy.

### Relation between savings rate and PCE

&emsp; Bubble chart of savings rate to private consumption expenditure. The color indicates the median period of unemployment, expressed in months.
```bash
ggplot(data, aes(x=pce, y=psavert, color=uempmed)) +
        geom_point(size=1)+
        labs(title= 'Prv. savings rate to PCE',
         x= "PCE [bln$]",
         y= "Prv. savings rate[%]",
         colour= "Unempl.",
         caption= 'diag. [2.1]')
```

<p align="center">
  <img src="Rplot-bubb.png" width="100%" title="hover text">
</p>

&emsp; It can be clearly seen in the chart that as consumer spending increases, the percentage of private savings decreases. It seems that the consumptive lifestyle is depleting our savings. The chart is difficult to read because we did not take into account the inflation rate. More about it in the next part of the advanced analysis.

Test github direct
