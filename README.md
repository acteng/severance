# Calculating active travel potential and severance around major
transport infrastructure


This repo builds on work documented in the
[srn](https://github.com/acteng/srn) repo, focussing on active travel
potential and severance around major transport infrastructure.

# Input datasets

## SRN and MRN

The Strategic Road Network (SRN) and Major Road Network (MRN) are
illustrated below (source: [browse tool of
ATIP](https://acteng.github.io/atip/browse.html?style=dataviz#6.2/52.917/-1.327)).

![](images/paste-1.png)

After downloading and combining the datasets, we can plot them as
follows with R:

``` r
mrn_srn = rbind(mrn, srn)
mrn_srn |>
  ggplot() +
  geom_sf(aes(color = infrastructure_type)) +
  theme_void()
```

![](README_files/figure-commonmark/combine-srn-mrn-1.png)

# Active travel potential

As a first approximation of active travel potential, we’ll calculate
cycling potential from the Propensity to Cycle Tool (PCT) data. We’ll
aggregate to 1 km resolution so the operations work fast for national
data, as a starter for 10, and smooth the data to remove noise.

     [1] "local_id"            "bicycle"             "govtarget_slc"      
     [4] "govnearmkt_slc"      "gendereq_slc"        "dutch_slc"          
     [7] "ebike_slc"           "segment_length_km"   "cycling_km_baseline"
    [10] "cycling_km_go_dutch" "geom"               

We’ll convert the pct linestring data to a 1 km raster grid with the
{terra} package.

![](README_files/figure-commonmark/pct-raster-1.png)

We can subset all grids with high cycling potential (e.g. 5000 km
cycling potential) and extract nearby roads to calculate severance as
follows:

![](README_files/figure-commonmark/severance-1.png)
