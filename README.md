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
