---
title: "Week 4 Lab"
author: "Helena Lindsay"
date: "9/15/2020"
output:
  flexdashboard::flex_dashboard:
  orientation: columns
  vertical_layout: fill
editor_options: 
  chunk_output_type: console

---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
library(flexdashboard)
load("worldecongraph.RData")
library(plotly)
library(DT)
library(ggplot2)
library(plyr)
library(ggrepel)
save.image('graphic.RData')
```

Column {data-width=750} 
-----------------------------------------------------------------------
### Pseudocode

  - Create scatterplot of data; CPI vs. HDI
  - Add title and axis labels; italicize axis labels
  - Scale and intervals for axes
  - Make points circles
  - Remove vertical background lines
  - Rename regions to match graphic legend
  - Color points by region
  - Add red regression line
  - Label one point from each country
    - Create label set for countries


### Graphic Replication

```{r, echo=FALSE, warning=FALSE}
data = read.csv('EconomistData.csv')

data$Region <- revalue(data$Region, c("Asia Pacific" = "Asia & Oceania", 
"MENA" = "Middle East & North Africa", "SSA" = "Sub-Saharan Africa",
"East EU Cemt Asia" = "Central & Eastern Europe", "EU W. Europe" = "OECD"))
# Renamed each region to match reference graphic

countries = c('Congo', 'Afghanistan', 'Sudan', 'Myanmar', 'Iraq',
              'Venezuela', 'Russia', 'Argentina', 'Greece', 'Brazil',
              'Italy', 'India', 'China', 'South Africa', 'Rwanda',
              'Cape Verde', 'Bhutan', 'Botswana', 'Spain', 'France',
              'US', 'Germany', 'Britain', 'Japan', 'Barbados',
              'Norway', 'New Zealand', 'Singapore')
# Created list of countries to assign labels

colorlist <- c("#006D6F", "lightskyblue1", "steelblue2", 
         "indianred1", "seagreen", "brown")

# Used online colorpicker to find closest colors to original graphic

graphic = ggplot(data, aes(x=CPI, y=HDI)) + 
  geom_point(aes(color = Region), size = 3, shape = 21, fill = 'white') + 
  xlab("Corruption Perceptions Index, 2011 (10=least corrupt)") +
  ylab("Human Development Index (1=best)") +

  labs(title = 'Corruption and human development',
       caption = 'Sources: Transparency International; UN Human Development Report') +

  scale_x_continuous(breaks = seq(0, 10, by = 1), limits = c(1,10)) +
  scale_y_continuous(breaks = seq(0, 1, by = 0.1), limits = c(0.2, 1)) +
  theme(legend.position = 'top') +
  theme(panel.grid.major.x = element_blank(),
        panel.grid.minor.x = element_blank(),
        panel.grid.major.y = element_line(color = 'grey'),
        panel.background = element_blank(),
        axis.line.x = element_line(color = 'grey'),
        axis.ticks.y = element_blank()) +
  stat_smooth(method = 'lm' , se = FALSE, 
              formula = y ~ log(x),
              color = 'red') +
  geom_text(aes(label = ifelse(Country %in% countries, Country, NA)),
                  color = 'black') +
  scale_color_manual(values = colorlist,
                     breaks = c("OECD", "Americas", "Asia & Oceania", 
               "Middle East & North Africa", 
               "Central & Eastern Europe",
               "Sub-Saharan Africa"),
               labels = c("OECD", "Americas", "Asia & Oceania", 
               "Middle East & North Africa", 
               "Central & Eastern Europe",
               "Sub-Saharan Africa"))+
  annotate("text", x = 10, y = 1, label = "R^2= 56%", parse= TRUE)

ggplotly(graphic)
```

