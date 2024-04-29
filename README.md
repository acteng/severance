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

After downloading and combining the datasets, we can combine them and
plot them as follows with R:

``` r
# waldo::compare(names(mrn), names(srn))
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

``` r
if (!file.exists("pct_rnet.gpkg")) {
  remotes::install_cran("pct")
  pct_rnet = pct::get_pct(layer = "rnet", national = TRUE)
  pct_projected = pct_rnet |>
    sf::st_transform(27700)
  pct_projected$segment_length_km = sf::st_length(pct_projected) / 1000 |>
    as.numeric()
  pct_projected = pct_projected |>
    mutate(
      cycling_km_baseline = bicycle * segment_length_km,
      cycling_km_go_dutch = dutch_slc * segment_length_km
    )
  sf::write_sf(pct_projected, "pct.gpkg", delete_dsn = TRUE)
}
```

    Skipping install of 'pct' from a cran remote, the SHA1 (0.9.9) has not changed since last install.
      Use `force = TRUE` to force installation

    Loading required package: sp

``` r
pct = sf::st_read("pct.gpkg")
```

    Reading layer `pct' from data source 
      `/home/robin/github/acteng/severance/pct.gpkg' using driver `GPKG'
    Simple feature collection with 558173 features and 10 fields
    Geometry type: LINESTRING
    Dimension:     XY
    Bounding box:  xmin: 136412.2 ymin: 14072.62 xmax: 655160.5 ymax: 654381.5
    Projected CRS: OSGB36 / British National Grid

``` r
names(pct)
```

     [1] "local_id"            "bicycle"             "govtarget_slc"      
     [4] "govnearmkt_slc"      "gendereq_slc"        "dutch_slc"          
     [7] "ebike_slc"           "segment_length_km"   "cycling_km_baseline"
    [10] "cycling_km_go_dutch" "geom"               

We’ll convert the pct linestring data to a 1 km raster grid with the
{terra} package.

``` r
library(terra)
pct_raster = terra::rast(pct, res = 5000)
# ?rasterize
pct_raster = terra::rasterize(pct, pct_raster, field = "cycling_km_baseline", fun = sum)
plot(pct_raster, type = "interval", breaks = c(0, 1, 2, 5, 10, 20, 100)*1000)
```

![](README_files/figure-commonmark/pct-raster-1.png)

We can subset all grids with high cycling potential (e.g. 5000 km
cycling potential) and extract nearby roads to calculate severance as
follows:

``` r
# pct_raster_high = pct_raster > 5000
# pct_raster_high_sf = terra::as.points(pct_raster_high)
```
