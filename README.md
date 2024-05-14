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

Building on the simplistic approach, we’ll assign active travel
potential to each road segment based on the values of the ‘active travel
potential cells’ that they intersect with. This approach is illustrated
below (a logical extension of this approach would be to use PCT section
centroids as the basis for road active travel potential values):

![](README_files/figure-commonmark/unnamed-chunk-10-1.png)

![](README_files/figure-commonmark/unnamed-chunk-10-2.png)

Let’s pre-process the roads into named groups, that we’ll use to
identify segments with high potential:

      [1] "A63 Hessle Road 29 10"           "A63 Clive Sullivan Way 29 10"   
      [3] "A1033 Hedon Road 29 10"          "A63 Roger Millward Way 29 10"   
      [5] "M27 Unnamed 23 10"               "M275 Unnamed 23 10"             
      [7] "M602 Unnamed 10 10"              "A52 Clifton Boulevard 3 10"     
      [9] "M60 Unnamed 10 10"               "M32 Unnamed 7 10"               
     [11] "A3113 Airport Way 14 10"         "A453 Remembrance Way 3 10"      
     [13] "A27 Unnamed 23 10"               "A66 Unnamed 15 10"              
     [15] "A19 Unnamed 15 10"               "M4 Unnamed 14 10"               
     [17] "M6 Unnamed 16 10"                "A30 Staines Road 14 10"         
     [19] "A19 Fleet Bridge Road 15 10"     "M621 Unnamed 8 10"              
     [21] "M1 Unnamed 3 10"                 "M69 Unnamed 3 10"               
     [23] "A5103 Princess Parkway 10 10"    "A30 London Road 14 10"          
     [25] "A52 Derby Road 3 10"             "M25 Unnamed 14 10"              
     [27] "A1 Unnamed 41 10"                "M56 Unnamed 10 10"              
     [29] "A47 Soke Parkway 102 10"         "A46 Unnamed 3 10"               
     [31] "A419 Unnamed 101 10"             "M62 Unnamed 10 10"              
     [33] "A5 Unnamed 89 10"                "A47 Unnamed 102 10"             
     [35] "A69 Unnamed 41 10"               "M4 Unnamed 101 10"              
     [37] "A38 Queensway 3 10"              "A19 Unnamed 41 10"              
     [39] "A5111 Raynesway 3 10"            "A500 Queensway 5 10"            
     [41] "M11 Unnamed 13 10"               "A34 Southern By-Pass Road 66 10"
     [43] "A40 Unnamed 73 10"               "A50 Unnamed 5 10"               
     [45] "M5 Unnamed 16 10"                "M1 Unnamed 8 10"                
     [47] "A34 Unnamed 66 10"               "A38 Unnamed 72 10"              
     [49] "M5 Unnamed 73 10"                "A38 The Parkway 72 10"          
     [51] "M6 Unnamed 10 10"                "M55 Unnamed 10 10"              
     [53] "A417 Unnamed 73 10"              "A5036 Church Road 10 10"        
     [55] "M67 Unnamed 10 10"               "A45 Nene Valley Way 6 10"       
     [57] "M57 Unnamed 10 10"               "M5 Unnamed 7 10"                
     [59] "M18 Unnamed 78 10"               "A64 Unnamed 32 10"              
     [61] "A696 Ponteland Road 41 9"        "A696 Unnamed 41 9"              
     [63] "M56 Unnamed 10 9"                "A453 Remembrance Way 3 9"       
     [65] "M67 Unnamed 10 9"                "A52 Brian Clough Way 3 9"       
     [67] "A419 Unnamed 101 9"              "A38 Abbey Hill 3 9"             
     [69] "M621 Unnamed 8 9"                "M5 Unnamed 73 9"                
     [71] "A52 Radcliffe Road 3 9"          "A30 Staines By-Pass 14 9"       
     [73] "A30 Unnamed 14 9"                "A5036 Dunnings Bridge Road 10 9"
     [75] "A59 Dunnings Bridge Road 10 9"   "A19 Unnamed 41 9"               
     [77] "A38 Kingsway 3 9"                "M27 Unnamed 23 9"               
     [79] "A46 Unnamed 3 9"                 "A585 Amounderness Way 18 9"     
     [81] "M1 Unnamed 3 9"                  "M5 Unnamed 82 9"                
     [83] "M62 Unnamed 10 9"                "A40 Unnamed 73 9"               
     [85] "M3 Unnamed 23 9"                 "A47 Soke Parkway 102 9"         
     [87] "A64 Unnamed 32 9"                "A500 Queensway 5 9"             
     [89] "M11 Unnamed 13 9"                "A45 London Road 6 9"            
     [91] "M3 Unnamed 14 9"                 "A1033 Hedon Road 29 9"          
     [93] "M6 Unnamed 3 9"                  "A27 Unnamed 21 9"               
     [95] "A38 Unnamed 3 9"                 "A500 Unnamed 5 9"               
     [97] "A1(M) Doncaster By-Pass 78 9"    "A47 Unnamed 102 9"              
     [99] "A14 Unnamed 13 9"                "A47 Eye Road 102 9"             

       Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
       1011    2437    4243    6915    9184   54741 

       Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
      15513   16598   19378   20848   24280   44869 

       Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
       1011    2437    4243    6915    9184   54741 

![](README_files/figure-commonmark/unnamed-chunk-14-1.png)

# Desire lines approach

<!-- # Breaking-up SRN into small sections -->
