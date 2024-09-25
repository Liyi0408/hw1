---
title: "challenge1"
author: "Liyi Wang"
format: html
editor: visual
---

## Quarto

```{r}
library(readr)
library(tidyverse)
library(here)
```

## **Figure 1**

Make a scatter plot showing the milk production in pounds versus the average number of milk cows using `milkcow_facts`. Write a caption for this plot.

-   The x-axis will be the number of milk cows (in millions) and the y-axis will be milk production (in millions of pounds)

-   Color the plot using a continuous variable of years

-   Label the axes (do not use the variable name)

-   Add a title for the plot

-   Label the legend (do not use the variable name)

```{r}
milkcow_facts <- read.csv("/Users/wang11/Downloads/milkcow_facts.csv")
library(ggplot2)
ggplot(milkcow_facts, aes(x = avg_milk_cow_number / 1e6, y = milk_production_lbs / 1e6, color = year)) + 
  geom_point(size = 3) +
  scale_color_continuous(name = "Year") + #Color the plot using a continuous variable of years
  labs(
    x = "Number of Milk Cows (Millions)",  
    y = "Milk Production (Millions of Pounds)",  
    title = "Milk Production vs. Number of Milk Cows by Year"  
  ) +
  theme_minimal()  
```

Figure captions：

This plot shows the relationship between milk production and the number of milk cows over different years.In earlier years (1980-1990), 11 millions cows can create about 140000 millions of pounds' milk while later years like 2010 can create more than 190000 millions of pounds' milk. Milk production over number of milk cows increased over years, indicating increased efficiency in milk production per cow over time.

## **Figure 2**

Examine the milk total milk production in each region over time using `state_milk_production`. We will plot line plots for the milk production versus the year faceted by region. Write a caption for this plot.

-   First, find the total production of milk each year by region (hint: use `summarise()` to find this)

-   The x-axis will be time and the y-axis will be total milk produced (in millions of pounds) in the region that year

-   Make line plots and color each line a different color

-   Facet the plot by region

-   Label the axes (do not use the variable name)

-   Add a title for the plot

-   Remove the legend from the plot

```{r}
region_milk_production <- state_milk_production %>%
  group_by(region, year) %>%
  summarise(total_milk_production = sum(milk_produced) / 1e6)
```

```{r}
ggplot(region_milk_production, aes(x = year, y = total_milk_production, color = region)) +
  geom_line(size = 1.2) + 
  facet_wrap(~ region) + #seperate the chart by region
  labs(
    x = "Year", #Label the axes
    y = "Total Milk Production (Millions of Pounds)", 
    title = "Total Milk Production Over Time by Region" 
  ) + #Add a title for the plot
  theme_minimal() + 
  theme(legend.position = "none") #Remove the legend from the plot
```

Figure captions：

This line plot shows the total milk production by various regions in the U.S from 1970 to 2010.Pacific,Mountain, and Lake States regions increased notable over years while other regions shows slightly increase except Appalachian and Delta states. It indicates that different regions have different developments of milk production.

## **Figure 3**

Make a line plot showing the amount (in pounds) of each milk products sold over time using `fluid_milk_sales`. Write a caption for this plot.

-   Do not include the category ‘Total Production’ in the plot

-   The x-axis will be time and the y-axis will be pounds of product

-   Each milk product will be a separate line on the plot

-   A different color will be assigned for each milk product

-   Make the plot on the log scale for better visualization

-   Label the axes (do not use the variable name)

-   Add a title for the plot

-   Label the legend (do not use the variable name)

```{r}
milk_sales1 <- fluid_milk_sales %>%
  filter(milk_type!= 'Total Production')
ggplot(milk_sales1, aes(x = year, y = pounds, color = milk_type)) +
  geom_line(size = 1.2) +
  scale_y_log10() +  #Make the plot on the log scale for better visualization
  labs(
    x = "Year", #Label the axes 
    y = "Pounds of Product Sold", 
    title = "Milk Products Sold Over Time",  #Add a title for the plot
    color = "Milk Product"  
  ) +
  theme_minimal()  

```

Figure captions：

This chart shows the sales of various milk products over time, measured in pounds. Whole milk initially sale best in 1975 to 1985, but was exceeded by reduced fat at 2000 . Skim milk saw an increase in sales during the middle period but sharply declined toward the end. The flavored not whol and low fat milk increased continuiously over time.The sale of flavored milk (whole) and butter milk declined over time. Sales for eggnog were consistently low with minimal changes. The chart indicated that consumer prefer low fat milk more over time.

## **Figure 4**

Make a stacked bar plot of the different type of cheeses consumed over time using a long format version of `clean_cheese` that you create. Write a caption for this plot.

Data wrangling (hint: create a new dataframe called `clean_cheese_long`):

-   Remove all of the total cheese variables (we do not want these in the plot)

-   Change the `clean_cheese` data to long format using `pivot_longer()`

-   Make sure all of the cheese categories are capitalized

Plotting:

-   The x-axis will be time and the y-axis will be pounds of cheese

-   Color the stacked bar plot by the different types of cheese

-   Label the axes (do not use the variable name)

-   Add a title for the plot

-   Change the color of the plot using a palette from RColorBrewer (note that not all palettes will have enough colors for the plot)

```{r}
# Load necessary libraries
library(ggplot2)
library(dplyr)
library(tidyr)
library(RColorBrewer)

clean_cheese_long <- clean_cheese %>%
  select(-contains("total")) %>% 
  pivot_longer(
    cols = -Year,  
    names_to = "cheese_type",  
    values_to = "pounds_consumed"  
  ) %>%
  mutate(cheese_type = toupper(cheese_type)) 

#Change the color of the plot using a palette from RColorBrewer
cheese_palette <- brewer.pal(n = length(unique(clean_cheese_long$cheese_type)), "Set3")

ggplot(clean_cheese_long, aes(x = Year, y = pounds_consumed, fill = cheese_type)) +
  geom_bar(stat = "identity") +  
  scale_fill_manual(values = cheese_palette) +#Color the stacked bar plot by the different types of cheese
  labs(
    x = "Year",  
    y = "Pounds of Cheese", 
    title = "Cheese Consumption Over Time by Type",  
    fill = "Type of Cheese"  
  ) +
  theme_minimal()  



```

Figure captions：

This chart shows the increasing consumption of various types of cheese from 1970 to 2015.It rising from around 17 pounds to over 48 pounds per year. Cheddar and Mozzarella are the most consumed types, contributing significantly to the total growth. Other dairy cheeses and swiss consumed low over years.Each cheese has increasing trend during 1970 to 2015 except swiss which remain unchanged.The trend indicates a growing preference for a wider variety of cheese products.

## **Figure 5**

Time to be creative! Make an original figure using the data. Join two of the dataframes together and use variables from both sides of the join to make this figure. This figure should tell a story and be easy to understand (just as the figures above are!). Change the colors of this plot from the default ggplot2 colors. Write a caption for this plot.

```{r}
# Load necessary libraries
library(ggplot2)
library(dplyr)
library(tidyr)
library(RColorBrewer)


clean_cheese_long <- clean_cheese %>%
  select(-contains("total")) %>%
  pivot_longer(cols = -Year, names_to = "cheese_type", values_to = "pounds_consumed") %>%
  group_by(Year) %>%
  summarise(total_cheese_consumed = sum(pounds_consumed))

region_milk_production1 <- region_milk_production %>%
  mutate(Year = year)
# Join clean_cheese_long and milk_production by year
combined_data <- clean_cheese_long %>%
  left_join(region_milk_production1, by = "Year")

ggplot(combined_data, aes(x = Year)) +
  geom_line(aes(y = total_milk_production / 1e6, color = "Milk Production"), size = 1.2) +
  geom_line(aes(y = total_cheese_consumed / 1e6, color = "Cheese Consumption"), size = 1.2) +  
  scale_color_manual(values = c( "Milk Production" = "skyblue","Cheese Consumption" = "darkorange")) +  
  labs(
    x = "Year", 
    y = "Millions of Pounds",  
    title = "Comparison of Total Cheese Consumption and Milk Production Over Time",  
    color = "Metric" 
  ) +
  theme_minimal() 
```

Because the units are a little bit different, one is 1e6 and another is 1e9,so take the logarithm and do it once.

```{r}
ggplot(combined_data, aes(x = Year)) +
  geom_line(aes(y = log10(total_milk_production), color = "Milk Production"), size = 1.2) +  # Apply log transformation
  geom_line(aes(y = log10(total_cheese_consumed), color = "Cheese Consumption"), size = 1.2) +  
  scale_color_manual(values = c("Milk Production" = "skyblue", "Cheese Consumption" = "darkorange")) +  
  labs(
    x = "Year", 
    y = "Log10 of Pounds",  # Updated label to reflect log transformation
    title = "Log Comparison of Total Cheese Consumption and Milk Production Over Time",  
    color = "Metric" 
  ) +
  theme_minimal() +
  theme(
    plot.title = element_text(hjust = 0.5, size = 16, face = "bold"),
    axis.title = element_text(size = 14),
    axis.text = element_text(size = 12)
  )

```

Figure captions：

This chart compares the total cheese consumption and milk production over time, measured in millions of pounds and take the logarithm. Milk production and cheese consumption have the same trend over time,they are both growing,it may result from cheese are made by milk. However,While milk production has shown stability with minor increases over the years, cheese consumption has continued to rise. This suggests a growing demand for cheese products, possibly due to changing consumer preferences or an increase in dairy product diversity.
