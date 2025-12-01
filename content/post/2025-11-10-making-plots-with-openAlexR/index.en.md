---
title: "Creating plots with openAlexR"
author: ["Alex Jack"]
date: 2025-11-25
slug: making-plots-with-openalexr
categories: ["R"]
tags: ["R_Visualization"]
---
  
Writing a thesis or paper introduction? Don't just *say* your field is growing or that your topic is becoming important—**show it**. With a a few lines of R, you can create compelling visualizations that demonstrate trends in your field and precisely why what you're talking about is *important*.

Here's how to go from search results to publication trend plots in about 5 minutes.

Here are the packages you will need to actually run this.

``` r
library(dplyr) # for general data-wrangling purposes
library(openalexR)
library(ggplot2)
library(Rmoji) # not strictly necessary but highly recommended 
```

Above the `dplyr` package is great for data wrangling and cleaning datam, `openAlexR` allows us to easily *legally* scrape the web for scientific journals, and of course `ggplot2` helps us make things pretty. Most importantly I just (at the time of writing this) found an R package called Rmoji 🔥.

In this example I'm particularly interested in seeing what trends there are in predictive ecology. The metric I'm using to measure the change in predictive ecology is to look at annual trends in the number of publications that feature key terms in the abstract or the title.


``` r
results <- oa_fetch(
  entity = "works",
  title.search = "prediction OR predict OR forecasting",  # Search in title
  abstract.search = "prediction OR predict OR decision support tool OR forecasting",  # Search in abstract
  concepts.id = "https://openalex.org/C86803240",  # Ecology concept ID
  from_publication_date = "2015-01-01",
  to_publication_date = "2024-12-31",
  options = list(sample = 100, seed = 12345) # sample 100 random journals
)
```
In the above example we're querying **open Alex's** servers using the `openAlexR` function `oa_fetch`. The **works** parameter specifies what type of object we're querying **open Alex** for. We specified works meaning we're looking for papers. The next parameter is **title.search** which tells `oa_fetch` that we want to get papers that contain "prediction", "predict", or "forecasting" in the title. Like **title.search** **abstract.search** tells `oa_fetch` that we want to search for the the keywords specified after the '=' sign. concepts.id allows us to specify a specific domain (note that `C86803240` is for the entire domain of Ecology). To get a list of all the concepts check [this webpage](https://docs.openalex.org/api-entities/concepts) out. Of course we want to specify date ranges for that (the next two parameters). The last parameter tells `oa_fetch` to randomly sample 100 of those works that come up and I set the seed for reproducibility. 



``` r
# OpenAlex returns a lot of metadata - let's select key columns
df_clean <- results %>%
  select(
    id,
    title = display_name,
    publication_year,
    publication_date,
    type,
    cited_by_count,
    doi,
    language # This is a list column
  ) %>%
  filter(!is.na(publication_year))
```
Above we did a little data cleaning with `dplyr` just to make the columns a little prettier and also make sure that all of our works did actually contain a year (since we're interested in looking at annual trends).


``` r
df_yearly <- df_clean %>%
  group_by(publication_year) %>%
  summarise(
    num_publications = n(),
    mean_citations = mean(cited_by_count, na.rm = TRUE),
    .groups = 'drop'
  ) %>%
  arrange(publication_year)
```
Speaking of annual trends here we actually summarise the number of publications by year.

``` r
ggplot(df_yearly, aes(x = publication_year, y = num_publications)) +
  geom_point(color = "steelblue", size = 3) +
  geom_line(color = "steelblue", linewidth = 1) +
  labs(
    x = "Publication Year", 
    y = "Number of Publications",
    title = "Growth of Predictive Ecology Research (2000-2025)",
    subtitle = "Data from OpenAlex: prediction + forecasting + decision support",
    caption = paste("Total unique records:", nrow(df_clean))
  ) +
  theme_minimal(base_size = 12) + 
  theme(
    axis.text.x = element_text(angle = 45, hjust = 1),
    plot.title = element_text(face = "bold", size = 14),
    plot.subtitle = element_text(color = "gray40"),
    panel.grid.minor = element_blank()
  ) +
  scale_x_continuous(breaks = seq(2015, 2024, by = 1))
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-5-1.png" width="672" />
So great! We've done it. We've created a cool chart showing how important orecasting and decision support are becoming in Ecology with just a few lines of code. How about that to knock the socks off your advisor for your research proposal 🐱.
