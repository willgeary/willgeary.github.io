---
published: true
layout: post
date: '2017-02-08 04:15:14 -0400'
categories: data
---
<iframe width="100%" height="680" frameborder="0" src="https://willgeary.github.io/visualizingNetworks/" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>

### Interactive Network Visualization Using R

Made with only ~10 lines of code!

```r
nodes$shape <- "dot"  
nodes$shadow <- FALSE # Nodes will drop shadow
nodes$title <- strsplit(paste0(nodes$media,'\n Audience Size: ', nodes$audience.size),"\n") # Text on click
nodes$label <- nodes$media # Node label
nodes$size <- nodes$audience.size # Node size
nodes$borderWidth <- 2 # Node border width

nodes$color.background <- c("slategrey", "lightgreen", "skyblue")[nodes$media.type]
nodes$color.border <- "black"
nodes$color.highlight.background <- "orange"
nodes$color.highlight.border <- "darkred"

links$width <- 1+links$weight/20 # line width
links$color <- "gray"    # line color  
links$arrows <- "middle" # arrows: 'from', 'to', or 'middle'
links$smooth <- TRUE    # should the edges be curved?
links$shadow <- FALSE    # edge shadow

v <- visNetwork(nodes, links) %>%
  visOptions(highlightNearest = TRUE, 
             selectedBy = "type.label")
```

Check out this excellent tutorial here: http://kateto.net/network-visualization
