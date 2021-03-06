Data Visualization
================

This tutorial provides some R scripts that can be adapted to plot your data. I am no expert in R, few minutes of googling might give you better solutions than mine, but hopefully you will find these useful.

Installation
------------

We will need the following library: *ggplot2*. Make sure that it installed in your R session.

``` r
install.packages("ggplot2")
```

Ggplot2 can be frustrating to get started with when you are used to Excel for example. But it is very powerful and once you know it you know it for life. So be patient, it will soon become your best friend and you will never make a plot in Excel anymore!

``` r
library(ggplot2)
```

Example Dataset
---------------

R features some very interesting built-in example datasets. We are going to use the chicken weight dataset that contains weight of chicken fed with different diets.

``` r
chickenDataset <- chickwts
```

Box Plots
---------

The following plots the weight of chicken depending on their diet. Note that you need to have your data points organized in a single vector (here the chicken weigth `chickenDataset$weight`) and you need a vector with the different categories (here the diet `chickenDataset$weight`). Note that setting the *alpha* allows viewing overlapping points.

``` r
chickenScatterPlot <- ggplot()
chickenScatterPlot <- chickenScatterPlot + geom_point(aes(x=chickenDataset$feed, y=chickenDataset$weight), alpha = 0.5)
chickenScatterPlot <- chickenScatterPlot + labs(x = "Diet", y = "Weigth [g]")
plot(chickenScatterPlot)
```

![](data_visualization_files/figure-markdown_github/chicken_scatter-1.png)

It can be difficult to distinguish the overlapping points so you can spread them on the x axis. Note that setting the *width* mekes the display not too packed.

``` r
chickenJitterPlot <- ggplot()
chickenJitterPlot <- chickenJitterPlot + geom_jitter(aes(x=chickenDataset$feed, y=chickenDataset$weight), width = 0.2, alpha = 0.5)
chickenJitterPlot <- chickenJitterPlot + labs(x = "Diet", y = "Weigth [g]")
plot(chickenJitterPlot)
```

![](data_visualization_files/figure-markdown_github/chicken_jitter_plot-1.png)

This is only readable with a limited number of points. For large datasets we can overlay the corresponding [box plots](https://en.wikipedia.org/wiki/Box_plot) that will illustrate the median and interquartile range.

``` r
chickenBoxPlot <- ggplot()
chickenBoxPlot <- chickenBoxPlot + geom_boxplot(aes(x=chickenDataset$feed, y=chickenDataset$weight), width = 0.6, alpha = 0.5)
chickenBoxPlot <- chickenBoxPlot + geom_jitter(aes(x=chickenDataset$feed, y=chickenDataset$weight), width = 0.2, alpha = 0.5)
chickenBoxPlot <- chickenBoxPlot + labs(x = "Diet", y = "Weigth [g]")
plot(chickenBoxPlot)
```

![](data_visualization_files/figure-markdown_github/chicken_box_plot-1.png)

Having the density of the points is more informative (and beautiful) and can be plotted using [violin plots](https://en.wikipedia.org/wiki/Violin_plot).

``` r
chickenViolinPlot <- ggplot()
chickenViolinPlot <- chickenViolinPlot + geom_violin(aes(x=chickenDataset$feed, y=chickenDataset$weight), width = 0.6, alpha = 0.5)
chickenViolinPlot <- chickenViolinPlot + geom_jitter(aes(x=chickenDataset$feed, y=chickenDataset$weight), width = 0.2, alpha = 0.5)
chickenViolinPlot <- chickenViolinPlot + labs(x = "Diet", y = "Weigth [g]")
plot(chickenViolinPlot)
```

![](data_visualization_files/figure-markdown_github/chicken_violin_plot-1.png)

We can plot the variation of the median across conditions. Note that we put the median of every condition in a vector and we need to provide as well a vector with the categories and a vector showing that these are all from the same group, i.e. it will be a single line.

``` r
medianLabels <- levels(chickenDataset$feed)
medianValues <- c()
medianGroups <- c()
for (level in medianLabels) {
  medianValue <- median(chickenDataset$weight[chickenDataset$feed == level])
  medianValues <- c(medianValues, medianValue)
  medianGroups <- c(medianGroups, "Chicken")
}

chickenViolinPlot <- ggplot()
chickenViolinPlot <- chickenViolinPlot + geom_violin(aes(x=chickenDataset$feed, y=chickenDataset$weight), width = 0.6, alpha = 0.5)
chickenViolinPlot <- chickenViolinPlot + geom_jitter(aes(x=chickenDataset$feed, y=chickenDataset$weight), width = 0.2, alpha = 0.5)
chickenViolinPlot <- chickenViolinPlot + geom_line(aes(x=medianLabels, y=medianValues, group=medianGroups), linetype = "dashed" , alpha = 0.5)
chickenViolinPlot <- chickenViolinPlot + labs(x = "Diet", y = "Weigth [g]")
plot(chickenViolinPlot)
```

![](data_visualization_files/figure-markdown_github/chicken_violin_plot_median-1.png)

Now let's make a fake second population of chicken that is gaining more weight for all diets but sunflower. The original population will be called *Real* and the new *Fake*.

``` r
# 1. We need a population vector that will tell from which population the chicken comes from
populationReal <- character(length(chickenDataset$weight))
populationReal[] <- "Real"
populationFake <- character(length(chickenDataset$weight))
populationFake[] <- "Fake"
newPopulation <- c(populationReal, populationFake)

# 2. We create a vector giving the diet of every chicken
newDiet <- c(chickenDataset$feed, chickenDataset$feed)

# 3. We create the fake weight and append it to the original weight
fakeWeight <- ifelse(chickenDataset$feed == "sunflower",  runif(1, 0.3, 0.6) * chickenDataset$weight, runif(1, 1.7, 1.9) * chickenDataset$weight)
newWeight <- c(chickenDataset$weight, fakeWeight)

# 5. We create a data frame for the fake population
fakeDataset <- data.frame(feed=chickenDataset$feed, weight=fakeWeight)

# 4. we put everything in a new data frame that will this time contain three vectors: the population, the diet, and the weigth
chickenDataset2 <- data.frame(population=newPopulation, feed=newDiet, weight=newWeight)
```

If you do a scatter plot, you can color according to the population. Note that the values of the *Fake* population are chosen randomly so they will always be different.

``` r
colorTrue <- "blue"
colorFake <- "red"

chickenJitterPlot2 <- ggplot()
chickenJitterPlot2 <- chickenJitterPlot2 + geom_jitter(aes(x=chickenDataset2$feed, y=chickenDataset2$weight, col=chickenDataset2$population), width = 0.2, alpha = 0.5)
chickenJitterPlot2 <- chickenJitterPlot2 + labs(x = "Diet", y = "Weigth [g]", col = "Population")
chickenJitterPlot2 <- chickenJitterPlot2 + scale_colour_manual(values = c(colorFake, colorTrue))
plot(chickenJitterPlot2)
```

![](data_visualization_files/figure-markdown_github/chicken_jitter_plot_2-1.png)

We can add box plots or violin like above. Note that it is easier to add the boxplot series one by one.

``` r
chickenViolinPlot2 <- ggplot()
chickenViolinPlot2 <- chickenViolinPlot2 + geom_violin(aes(x=chickenDataset$feed, y=chickenDataset$weight), col= "darkblue", width = 0.6, alpha = 0.5)
chickenViolinPlot2 <- chickenViolinPlot2 + geom_violin(aes(x=fakeDataset$feed, y=fakeDataset$weight), col= "darkred", width = 0.6, alpha = 0.5)
chickenViolinPlot2 <- chickenViolinPlot2 + geom_jitter(aes(x=chickenDataset2$feed, y=chickenDataset2$weight, col=chickenDataset2$population), width = 0.2, alpha = 0.5)
chickenViolinPlot2 <- chickenViolinPlot2 + labs(x = "Diet", y = "Weigth [g]", col = "Population")
chickenViolinPlot2 <- chickenViolinPlot2 + scale_colour_manual(values = c(colorFake, colorTrue))
plot(chickenViolinPlot2)
```

![](data_visualization_files/figure-markdown_github/chicken_violin_plot2-1.png)

To add the median you will have to get it yourself because I am drunk.

``` r
medianLabels <- levels(chickenDataset$feed)
medianValues <- c()
medianGroups <- c()
for (level in medianLabels) {
  medianValue <- median(chickenDataset$weight[chickenDataset$feed == level])
  medianValues <- c(medianValues, medianValue)
  medianGroups <- c(medianGroups, "Real")
}
for (level in medianLabels) {
  medianValue <- median(fakeDataset$weight[fakeDataset$feed == level])
  medianValues <- c(medianValues, medianValue)
  medianGroups <- c(medianGroups, "Fake")
}
medianLabels <- c(medianLabels, medianLabels)

chickenViolinPlot2 <- ggplot()
chickenViolinPlot2 <- chickenViolinPlot2 + geom_violin(aes(x=chickenDataset$feed, y=chickenDataset$weight), col = "darkblue", width = 0.6, alpha = 0.5)
chickenViolinPlot2 <- chickenViolinPlot2 + geom_violin(aes(x=fakeDataset$feed, y=fakeDataset$weight), col = "darkred", width = 0.6, alpha = 0.5)
chickenViolinPlot2 <- chickenViolinPlot2 + geom_jitter(aes(x=chickenDataset2$feed, y=chickenDataset2$weight, col = chickenDataset2$population), width = 0.2, alpha = 0.5)
chickenViolinPlot2 <- chickenViolinPlot2 + geom_line(aes(x=medianLabels, y=medianValues, group=medianGroups, col = medianGroups), alpha = 0.5, linetype = "dashed")
chickenViolinPlot2 <- chickenViolinPlot2 + labs(x = "Diet", y = "Weigth [g]", col = "Population")
chickenViolinPlot2 <- chickenViolinPlot2 + scale_colour_manual(values = c(colorFake, colorTrue))
plot(chickenViolinPlot2)
```

![](data_visualization_files/figure-markdown_github/chicken_violin_plot_median_2-1.png)

With boxes

``` r
chickenViolinPlot2 <- ggplot()
chickenViolinPlot2 <- chickenViolinPlot2 + geom_boxplot(aes(x=chickenDataset$feed, y=chickenDataset$weight), col = "darkblue", width = 0.6, alpha = 0.5)
chickenViolinPlot2 <- chickenViolinPlot2 + geom_boxplot(aes(x=fakeDataset$feed, y=fakeDataset$weight), col = "darkred", width = 0.6, alpha = 0.5)
chickenViolinPlot2 <- chickenViolinPlot2 + geom_jitter(aes(x=chickenDataset2$feed, y=chickenDataset2$weight, col = chickenDataset2$population), width = 0.2, alpha = 0.5)
chickenViolinPlot2 <- chickenViolinPlot2 + geom_line(aes(x=medianLabels, y=medianValues, group=medianGroups, col = medianGroups), alpha = 0.5, linetype = "dashed")
chickenViolinPlot2 <- chickenViolinPlot2 + labs(x = "Diet", y = "Weigth [g]", col = "Population")
chickenViolinPlot2 <- chickenViolinPlot2 + scale_colour_manual(values = c(colorFake, colorTrue))
plot(chickenViolinPlot2)
```

![](data_visualization_files/figure-markdown_github/chicken_box_plot_median_2-1.png)
