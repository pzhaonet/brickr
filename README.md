LEGO Mosaics in R
================

# brickr <img src='man/figures/logo.png' align="right" height="138" />

<!-- <!-- badges: start -->

[![Lifecycle:
experimental](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://www.tidyverse.org/lifecycle/#experimental)
<!-- <!-- badges: end -->

## Overview

**brickr** is a package for creating LEGO-esque 2D and 3D models using
the R [tidyverse](https://www.tidyverse.org/) and [Tyler
Morgan-Wall](https://twitter.com/tylermorganwall)’s
[rayshader](https://github.com/tylermorganwall/rayshader) package.

The package has two key uses:

  - Converting image files in to 2D and 3D LEGO mosaics
  - Building 3D LEGO models from simple data frames

## Installation

``` r
# To install the latest version from Github:
# install.packages("devtools")
devtools::install_github("ryantimpe/brickr")

#For 3D features, rayshader is also required.
install.packages("rayshader")
```

## 3D Models

Currently, 3D models can be built from one of two data input formats:
`bricks_from_table()` or `bricks_from_coords()`.

**These functions are very experimental and will change. Better
documentation will be released soon.**

  - `bricks_from_table()` converts a matrix-shaped table of integers
    into LEGO bricks. For simple models, this table can be made manually
    using `data.frame()` or `tibble::tribble()`. For more advanced
    models, it’s recommended you use MS Excel or a .csv file. The
    left-most column in the table is associated with the Level or z-axis
    of the model. The function by default converts this to numeric for
    you. Each other column is an x-coordinate and each row is a
    y-coordinate. More flexible inputs will be available in a future
    release.

  - `bricks_from_excel()` is a wrapper function to more easily build
    models designed using a Microsoft Excel template. Please see this
    repo: [brickr toybox](https://github.com/ryantimpe/brickr_toybox).

  - `bricks_from_coords()` takes a data frame with `x`, `y`, & `z`
    integer values, and `Color` columns, where each combination of x, y,
    & z is a point in 3-dimensional space. Color must be an official
    LEGO color name from `display_colors()`. This format is much more
    flexible than `bricks_from_table()` and allows the programatic
    development of 3D models.

Pass the output from any `bricks_from_*()` function to
`display_bricks()` to see the 3D model.

``` r
library(brickr)

#This is a brick
brick <- data.frame(
  Level="A",
  X1 = rep(1,4),
  X2 = rep(1,4)
)

brick
```

    ##   Level X1 X2
    ## 1     A  1  1
    ## 2     A  1  1
    ## 3     A  1  1
    ## 4     A  1  1

``` r
brick %>% 
  bricks_from_table() %>% 
  display_bricks()

rayshader::render_snapshot()
```

![](README_files/figure-gfm/bricks_1-1.png)<!-- -->

### Brick Colors

There are 2 ways to assign the color of the bricks. The
`display_colors()` function helps with ID numbers and names

  - Use the `brickrID` value instead of ‘1’ in the model input table.
    Values of ‘0’ are blank spaces.

  - Create a table of color assignments and pass this to
    `bricks_from_table()`

<!-- end list -->

``` r
brick_colors <- data.frame(
  .value = 1,
  Color = "Bright blue"
)

brick %>% 
  bricks_from_table(brick_colors) %>% 
  display_bricks()

rayshader::render_snapshot()
```

![](README_files/figure-gfm/bricks_2-1.png)<!-- -->

### Stacking bricks

The Level column in the input table determines the height of the bricks.
`bricks_from_table()` will convert alphanumeric levels into a z
coordinate.

``` r
# A is the bottom Level, B is the top
brick <- data.frame(
  Level= c(rep("A",4), rep("B",4)),
  X1 = rep(1,4),
  X2 = rep(1,4)
)

brick %>% 
  bricks_from_table(brick_colors) %>% 
  display_bricks()

rayshader::render_snapshot()
```

![](README_files/figure-gfm/bricks_3-1.png)<!-- -->

The same process works with many levels

``` r
#You can stack many bricks ----
brick <- data.frame(
  Level="A",
  X1 = rep(1,4),
  X2 = rep(1,4)
)

#... And they can all be different colors ----
1:10 %>% 
  purrr::map_df(~dplyr::mutate(brick, Level = LETTERS[.x], X1 = .x, X2 = .x)) %>% 
  bricks_from_table() %>% 
  display_bricks()

rayshader::render_snapshot()
```

![](README_files/figure-gfm/bricks_4-1.png)<!-- -->

# Full Models

For larger models, use `tibble::tribble()` to more easily visualize the
model. For very large models, use MS Excel.

``` r
my_first_model <- tibble::tribble(
  ~Level, ~X1, ~X2, ~X3, ~x4, ~x5, ~X6, ~x7, ~x8,
  "A", 1, 1, 1, 0, 1, 1, 1, 1,
  "A", 1, 0, 0, 0, 0, 0, 0, 1,
  "A", 1, 0, 0, 0, 0, 0, 0, 1,
  "A", 1, 1, 1, 1, 1, 1, 1, 1,
  "B", 1, 0, 1, 0, 1, 1, 0, 1,
  "B", 1, 0, 0, 0, 0, 0, 0, 1,
  "B", 1, 0, 0, 0, 0, 0, 0, 1,
  "B", 1, 0, 1, 0, 0, 1, 0, 1,
  "C", 1, 1, 1, 1, 1, 1, 1, 1,
  "C", 1, 0, 0, 0, 0, 0, 0, 1,
  "C", 1, 0, 0, 0, 0, 0, 0, 1,
  "C", 1, 1, 1, 1, 1, 1, 1, 1,
  "D", 2, 2, 2, 2, 2, 2, 2, 2,
  "D", 1, 0, 0, 0, 0, 0, 0, 1,
  "D", 1, 0, 0, 0, 0, 0, 0, 1,
  "D", 2, 2, 2, 2, 2, 2, 2, 2,
  "E", 0, 0, 0, 0, 0, 0, 0, 0,
  "E", 2, 2, 2, 2, 2, 2, 2, 2,
  "E", 2, 2, 2, 2, 2, 2, 2, 2,
  "E", 0, 0, 0, 0, 0, 0, 0, 0
)

brick_colors <- tibble::tribble(
  ~`.value`, ~Color,
  1, "Bright blue",
  2, "Dark orange"
)
  
my_first_model %>% 
  bricks_from_table(brick_colors) %>% 
  display_bricks(theta = 210)

rayshader::render_snapshot()
```

![](README_files/figure-gfm/bricks_5-1.png)<!-- -->

### Programmatically build models

Use `bricks_from_coords()` to programmatically build 3D LEGO models
instead of manually drawing them in a spreadsheet or table. Here you
must provide whole number coordinates for x, y, and z, along with an
official LEGO color name for each point.

``` r
radius <- 4
sphere_coords <- expand.grid(
  x = 1:round((radius*2.5)),
  y = 1:round((radius*2.5)),
  z = 1:round((radius/(6/5)*2.5)) #A brick is 6/5 taller than it is wide/deep
) %>%
  mutate(
    #Distance of each coordinate from center
    dist = (((x-mean(x))^2 + (y-mean(y))^2 + (z-mean(z))^2)^(1/2)),
    Color = case_when(
      #Yellow stripes on the surface with a 2to4 thickness
      between(dist, (radius-1), radius) & (x+y+z) %% 6 %in% 0:1 ~ "Bright yellow",
      #Otherwise, sphere is blue
      dist <= radius ~ "Bright blue"
  ))

sphere_coords %>% 
  bricks_from_coords() %>% 
  display_bricks(phi = 30, theta = 30)

rayshader::render_snapshot()
```

![](README_files/figure-gfm/bricks_6-1.png)<!-- -->

### Examples

More examples using `bricks_from_table()` and `bricks_from_coords()` can
be found at the links below.

  - [**Get
    started**](https://gist.github.com/ryantimpe/a784beaa4f798f57010369329d46ce71)
    with the framework for building a brick from scratch.
  - [**Build an
    owl**](https://gist.github.com/ryantimpe/ceab2ed6b8a4737077280fc9b0d1c886)
    with `bricks_from_table()` by manually placing each brick.
  - Generate a punny [**random forest
    model**](https://gist.github.com/ryantimpe/a7363a5e99dceabada150a43925beec7)
    using `bricks_from_coords()` and {purrr}.
  - [**brickr toybox**](https://github.com/ryantimpe/brickr_toybox) repo
    for tools and resources to get started.

## Mosaics

The mosaic functions renders an imported JPG or PNG file using LEGO
colors and bricks. The resulting mosaic can be viewed in 2D and 3D. A
full explanation can be found on [this blog
post](http://www.ryantimpe.com/post/lego-mosaic1/), this [follow-up
post](http://www.ryantimpe.com/post/lego-mosaic2/),and this [third
post](http://www.ryantimpe.com/post/lego-mosaic3/).

``` r
mosaic1 <- jpeg::readJPEG("Images/goldengirls.jpg") %>% 
  image_to_bricks(img_size = 48) #Length of each side of mosaic in "bricks"

#Plot 2D mosaic
mosaic1 %>% display_set()
```

![](README_files/figure-gfm/m1_set-1.png)<!-- -->

If you had previously created mosaics before the package release, the
script below will still work.

``` r
mosaic1 <- jpeg::readJPEG("Images/goldengirls.jpg") %>% 
  scale_image(img_size = 48) %>% #Length of each side of mosaic in "bricks"
  legoize() %>%  
  collect_bricks()
```

### 2D Mosaics

`image_to_bricks()` can take a few important arguments:

  - `img_size` Providing a single value, such as `48`, crops the image
    to a square. Inputting a 2-element array, `c(56, 48)`, will output a
    rectangular image of `c(width, height)`.

  - `color_table` Data frame of possible brick colors in the mosaic.
    Defaults to the included data set `lego_colors`.

  - `brightness` adjusts the light of the image. Values greater than 1
    will lighten the image, while value less than 1 will darken it.

`display_set()` creates a ggplot of the image.

### 3D Mosaics

Two additional functions can convert the `image_to_bricks()` output into
a 3D mosaic using the
[rayshader](https://github.com/tylermorganwall/rayshader) package by
[Tyler Morgan-Wall](https://twitter.com/tylermorganwall).

  - `collect_3d()` translates the 2D LEGO mosaic into two matrices - one
    for the color image and one for the elevation of each point on the
    image. By default, the produced image has the height of 6 LEGO
    plates (2 LEGO bricks) with lighter colors having a higher
    elevation. Use `mosaic_height` to change the height of the mosaic
    and set `highest_el = 'dark'` to set the darker colors as the
    tallest bricks.

  - `display_3d()` simply calls `rayshader::plot_3d()`, but pulls both
    the hillshade and elevation matrices from the output of
    `collect_3d()` and fixes some of the arguments. See
    `?rayshader::plot_3d()` for more information.

<!-- end list -->

``` r
library(rayshader)

mosaic1 %>% 
  collect_3d() %>% 
  display_3d(fov=0,theta=-20,phi=30,windowsize=c(1000,800),zoom=0.7)

render_snapshot()
```

![](README_files/figure-gfm/m1_3d-1.png)<!-- -->

## LEGO Mosaics IRL

Additional functions assist in the translation from the LEGO mosaic
image into a real LEGO set.

### Instructions

Use `generate_instructions()` to break the LEGO mosaic image into
easier-to-read steps for building the set. This defaults to 6 steps, but
passing any integer value will generate that many steps.

``` r
mosaic1 %>% generate_instructions(9)
```

![](README_files/figure-gfm/m1_instructions-1.png)<!-- -->

### Piece list and count

Use `display_pieces()` to generate a graphic and count of all required
plates or bricks (for stacked mosaics). These are sorted by color and
size for easy purchase on LEGO.com’s
[Pick-a-Brick](https://shop.lego.com/en-US/Pick-a-Brick) section using
the advanced search option. Alternatively, use `table_pieces()` to
produce a data frame table of all required bricks.

``` r
mosaic1 %>% display_pieces()
```

![](README_files/figure-gfm/m1_pieces-1.png)<!-- -->
