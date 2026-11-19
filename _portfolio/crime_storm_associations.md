---
title: "Crimes and Storms Association Deep Dive"
excerpt: "Analytical deep dive into the associations between crimes and storms using rigorous statistics."
tools: [ "Python", "Pandas", "SciPy", "Seaborn" ]
header:
  teaser: /assets/images/violin_plot_teaser.png
layout: archive
classes: wide
author_profile: true
sidebar:
order: 3
---

This is an analytical deep dive into relationships between crimes and storms. The scope of the analysis is Miami, FL in 2024, utilizing open data from <a href="https://gis-mdc.opendata.arcgis.com/" target="_blank">Miami-Dade County</a> and the National Oceanic and Atmospheric Administration (NOAA) <a href="https://www.ncei.noaa.gov/access/storm-events-database/" target="_blank">Storm Events Database</a>.

The first step of this project was processing, cleaning, and transforming the data to prepare it for analysis. A key component of this was joining the crimes dataset with the storm dataset in order to determine whether a given crime was associated with a storm. I defined this association as follows: a crime is associated with a storm if both the crime and the storm occurred within Miami-Dade County and the timestamp recorded for the police response to the crime incident falls between the start and stop timestamps recorded for the storm event.

Performing this transformation required complex data manipulation logic in Python using the Pandas module. Part of this transformation is shown in the excerpt below.

{% highlight python %}
def merge_crime_and_storm_data(crime_df, storm_df):
    # Perform a cross merge on the two data sets
    crimes_during_storms = pd.merge(crime_df, storm_df, how='cross')

    # Filter to include rows where
    # storm start timestamp <= crime incident timestamp <= storm end timestamp
    crimes_during_storms = crimes_during_storms.query(
        "BEGIN_DATE_TIME <= CFSDate <= END_DATE_TIME").copy()

    # This data set contains crimes that occurred during storms
    # Record this in a new column
    crimes_during_storms.loc[:, 'DuringStorm'] = 1

    # Select relevant crime columns and remove duplicates
    # Duplicates could arise if 1 crime is associated with >1 storm
    crimes_during_storms = crimes_during_storms[['CFSNumber', 'CFSDate',
                                                 'IncidentType', 'IBRCode',
                                                 'CrimeCategory', 
                                                 'DuringStorm']
                                                ].drop_duplicates()

    # Left join full crime data set with this newly transformed data
    crime_storm_df = pd.merge(crime_df, crimes_during_storms, how='left',
                              on=['CFSNumber', 'CFSDate', 'IncidentType', 
                              'IBRCode', 'CrimeCategory'])

    # Crimes not associated with storms currently have a during_storm value 
    # of NaN - fill these values with 0 indicating no associated storm
    crime_storm_df.loc[:, 'DuringStorm'] = \
        crime_storm_df['DuringStorm'].fillna(0)

    return crime_storm_df
{% endhighlight %}

My analysis sought to answer two research questions:

1. Are crime rates higher during stormy weather?
2. Are property crimes more common during stormy weather?

For each question, I chose an appropriate analytical approach based on a relevant statistical method and hypothesis test. After performing the statistical test, I produced a professional, scientific-quality data visualization to communicate the results clearly. The sections below include further details about these chosen analytical methodologies.

<h2>Question 1: Are crime rates higher during stormy weather?</h2>

Analytical method: Poisson means hypothesis test.

This hypothesis test relies on a Poisson distribution, a model of the number of times a random event occurs within a time interval. The mean of a Poisson distribution is the average rate at which the event occurs. This test compares two Poisson means; that is, it compares the rates of occurrences of events across two different groups. I applied the Poisson means test to compare the crime rate during calm weather against the crime rate during storm events.

The aggregate data used for the Poisson means test is included below:


| Group	| Crime Count |	Total Hours |	Avg Crimes per Hour |
| --- | --- | --- | --- |
|	During Calm Weather	| 87,901 | 8584.67 | 10.24 |
|	During Storm Event | 1,784	| 199.33 | 8.95 |

Details of the hypothesis test are included below:

| Test | Hypotheses | Test Statistic | P Value | Result |
| :--- | :--- | :--- | :--- | :--- |
| Poisson means test for crime rates during storm events versus during calm weather | $$H_0: \lambda_{storm} = \lambda_{calm}$$ <br> $$H_a: \lambda_{storm} > \lambda_{calm}$$ | -6.006 | >0.999 | Fail to reject the null hypothesis |

I produced a violin plot to illustrate these results.

![Violin plot showing hourly storm rates during calm weather versus during storm events]({{ site.url }}{{ site.baseurl }}/assets/images/crime_rates_violin_chart.png){: .full}

Failing to reject the null hypothesis indicates there is insufficient evidence to conclude that crime rates are higher during storm events. To the contrary, there are, on average, 8.95 crimes per hour during storm events but 10.24 crimes per hour during calm weather. Perhaps these mean rates are influenced by outliers, as the violin plot indicates that some hours of calm weather saw nearly 80 crimes per hour. Regardless, stormy weather does not appear to be a significant predictor of an increase in crime, though storm events may be meaningfully associated with crime statistics in other ways.

<h2>Question 2: Are property crimes more common during stormy weather?</h2>

Analytical method: one-sided two-proportion z-test.

This hypothesis test uses z-distributions to compare two proportions. In this context, I was interested in the proportion of crimes that were categorized as property crimes. A property crime is a crime in which property is stolen or damaged without violence or threat of violence against people. I applied the z-test to compare the proportion of property crimes during calm weather against the proportion of property crimes during storm events.

The aggregate data used for the two-sample z-test is included below:


| Group	| Property Crimes |	Other Crimes	| Total Crimes	| Pct Property Crimes |
|	During Storm Event	| 461 |	1,323 |	1,784	| 25.84 |
|	During Calm Weather | 19,615	| 68,286	| 87,901	| 22.31 |

Details of the hypothesis test are included below:

| Test | Hypotheses | Test Statistic | P Value | Result |
| :--- | :--- | :--- | :--- | :--- |
| Two-sample z-test for proportions of property crimes during storm events versus during calm weather | $$H_0: p_{storm} = p_{calm}$$ <br> $$H_a: p_{storm} > p_{calm}$$ | 3.537 | <0.001 | Reject the null hypothesis |

I produced a bar chart including error bars for measured uncertainty in proportion calculations to illustrate these results.

![Bar chart showing proportions of property crimes during calm weather versus during storm events]({{ site.url }}{{ site.baseurl }}/assets/images/property_crime_proportions_bar_chart.png){: .full}

Rejecting the null hypothesis indicates there is strong evidence that storm events are associated with a higher proportion of property crimes. During storm events, 25.84% of crimes committed were property crimes, while during calm weather, 22.31% of crimes committed were property crimes. This result indicates a meaningful association between weather and the types of crimes committed.

Finally, the complete Jupyter notebook used to perform this analysis is included below. The notebook includes the full analytical scope, including data import, cleaning, processing, transformation, hypothesis testing, and visualization.

<iframe src="{{ '/assets/html/crime_storm_analysis.html' | relative_url }}" width="100%" height="800px" style="border:none;"></iframe>
