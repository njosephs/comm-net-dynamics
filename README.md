# comm-net-dynamics
This repository contains the publically available data from the text "Communication network dynamics in a large organizational hierarchy"

https://arxiv.org/abs/2208.01208

## Citation

Josephs, Nathaniel, Side Peng, and Forrest W. Crawford. "Communication network dynamics in a large organizational hierarchy." (2024+). _The Annals of Applied Statistics_.

## Data

**email_graph.txt**: a sparse matrix file representing the weighted adjacency matrix of the communication network for the entire Microsoft Corporation in May 2019

**RD_summary.csv**: a data frame summarizing the path analysis of the organizational hierarchy

| Head | RD | Count | Comm_freq |
|:----- |:----------- |:----------- |:----------- |
| 1   | 1 | 2616 | 17.5 |
| 1   | 2 | 18940 | 4.3 |
| 1   | 3 | 19352 | 1.6 |
| 1   | 4 | 52924 | 0.7 |
| 1   | 5 | 92684 | 0.3 |

## Code

### Email communication network

``` r
library(Matrix)

A <- readMM("email_graph.txt")
```

### Organizational hierarchy

``` r
library(ggplot2)
library(scales)

SRD <- read.csv("SRD_summary.csv")

number_of_teams <- length(unique(SRD$Head))
team_colors <- viridis::viridis(n = number_of_teams, option = "H")

ggplot(SRD
       , aes(x = SRD, y = Comm_freq
             , group = Head, color = as.factor(Head))) +
  geom_line(size = 1, alpha = .5, position = position_dodge(.5)) +
  geom_point(aes(size = Count)
             , alpha = .5, position = position_dodge(.5)) +
  scale_y_log10(breaks = trans_breaks("log10", function(x) 10^x)
                , labels = trans_format("log10", math_format(10^.x))) +
  labs(x = "Signed reporting distance", y = "", color = "Head") +
  guides(size = "none", color = "none") +
  scale_color_manual("Head", values = team_colors) +
  theme_minimal(base_size = 25)

ggplot(SRD
       , aes(x = SRD, y = Comm_freq
             , group = SRD)) +
  geom_boxplot(aes(weight = NULL)) +
  geom_smooth(method = "loess", se = TRUE, aes(group = 1)) +
  scale_y_log10(breaks = trans_breaks("log10", function(x) 10^x)
                , labels = trans_format("log10", math_format(10^.x))) +
  labs(x = "Signed reporting distance", y = "", color = "Head") +
  guides(size = "none", color = "none") +
  scale_color_viridis_d(option = "C") +
  theme_minimal(base_size = 25)
```
