# Calculating active travel potential and severance around major
transport infrastructure


This repo builds on work documented in the
[srn](https://github.com/acteng/srn) repo, focussing on active travel
potential and severance around major transport infrastructure.

# Input datasets

## Major road network

The Strategic Road Network (SRN) and Major Road Network (MRN) are
illustrated below (source: [browse tool of
ATIP](https://acteng.github.io/atip/browse.html?style=dataviz#6.2/52.917/-1.327)).

![](images/paste-1.png)

After downloading and combining the datasets, we can plot them as
follows with R:

``` r
mrn_srn = rbind(mrn, srn)
# mrn_srn |>
#   ggplot() +
#   geom_sf(aes(color = infrastructure_type)) +
#   theme_void() 
names(mrn_srn)
```

    [1] "name"                "road_function"       "form_of_way"        
    [4] "road_classification" "infrastructure_type" "geom"               

``` r
mrn |>
  tm_shape() +
  tm_lines(col = "grey") +
  tm_shape(srn) +
  tm_lines() +
  tm_title("SRN (black) and MRN (grey)", just = c("center", "top"))
```

![](README_files/figure-commonmark/combine-srn-mrn-1.png)

The main focus of the analysis presented in this report is the SRN, the
road network controlled by National Highways (NH).

## Active travel potential

Data on active travel potential was taken from the Propensity to Cycle
Tool (PCT), a Department for Transport funded project that builds on
official data to model cycling potential nationwide. In future we would
like to add data representing walking potential, possibly based on
travel to school data.

# Active travel potential

As a first approximation of active travel potential, we calculated
cycling potential from the Propensity to Cycle Tool (PCT) data. To
ensure consistency across areas, we defined ‘cycling potential’ in this
case as the distance that could be cycled *within* each grid cell,
reducing the influence of variable road lengths on the results (10 100 m
segments with cycling potential of 20 has the same impact as a single 5
km segment with cycling potential of 20 trips per day). We’ll aggregate
to 5 km resolution so the operations work fast for national data, as a
starter for 10, and smooth the data to remove noise.

We’ll convert the pct linestring data to a 5 km raster grid with the
{terra} package.

![](README_files/figure-commonmark/pct-raster-1.png)

We can subset all grids with high cycling potential (e.g. 5000 km
cycling potential) and extract nearby roads to calculate severance as
follows:

![](README_files/figure-commonmark/severance-1.png)
