---
title: "Hospital Performance Analysis"
excerpt: "Business analysis of KPI trends and strategic recommendations for improving performance."
tools: [ "Python", "Pandas", "Matplotlib", "PowerPoint" ]
header:
  teaser: /assets/images/hospital_analysis_teaser_2.jpg
layout: archive
classes: wide
author_profile: true
sidebar:
order: 1
---

This is a business intelligence analytics case study using synthetic hospital data with the goal of delivering actionable, strategic insights to hospital administrators. I began by defining four key performance indicators (KPIs) in line with business priorities. These KPIs are:

1. Utilization Rate = beds occupied / total available beds
2. Refusal Rate = patients refused admission / patients requested admission
3. Patient Satisfaction = average of patient satisfaction ratings / 100
4. Staff Morale = average of staff morale ratings / 100

I used Python (NumPy, Pandas, Matplotlib, and Seaborn) to assess the hospital's current performance along these metrics, both hospital-wide and grouped by hospital service. Where areas of concern emerged, I performed deeper analysis to identify drivers of trends. The source code for the analysis is available as a Kaggle notebook <a href="https://www.kaggle.com/code/jennagorham/hospital-performance-analysis-python-version" target="_blank">here</a>.

I produced a PowerPoint slide deck to present my analytical results. The slides communicate the data at a high level, discussing broad trends rather than technical details, with a focus on strategic takeaways and insights. I used Matplotlib and Seaborn to create professional and presentation-ready data visualizations for the slides. The final slide contains detailed recommendations, each addressing a specific area for improvement with clear action items.

For example, I observed that utilization rates are an area of critical concern across all hospital services, with an overall utilization rate of 92.5%, indicating significant strain and consistent over-utilization. Looking deeper, I uncovered the following trends which provide further insight into high utilization rates:

* High patient request volumes are a strong driver of high utilization rates, indicating that demand is consistently exceeding the hospital's capacity and resources.
* Patient demand is particularly high during flu epidemics, leading to poorer outcomes during flu season.
* When utilization rates reach 100%, patients must be turned away and refused care, leading to elevated refusal rates. 

I produced a scatterplot data visualization to help understand the relationship between patient request volumes and utilization rates. This illustrates that across all hospital services, as demand increases, utilization increases and resources become increasingly strained until the hospital's capacity is exceeded and utilization rates reach 100%.

![Scatter plot of patient request counts and utilization rates by hospital service]({{ site.url }}{{ site.baseurl }}/assets/images/hospital_requests_viz.png){: .full}

These trends led me to conclude that a core root cause of poor performance is insufficient hospital resources to meet high patient demand. Accordingly, I presented multiple strategic recommendations centered around improving resource allocation.

See the final presentation embedded below.

<div style="position: relative; width: 100%; padding-top: 56.25%; height: 0; overflow: hidden;">
  <iframe src="https://1drv.ms/p/c/5fb1e378f3e2dec1/IQQYjTgbJrZRSJ9fX3korLsTAfEFkp18w_haA4oCpXguJM0?wdAr=1.7777777777777777" title="PowerPoint Viewer"
  style="position: absolute; top: 0; left: 0; width: 100%; height: 100%; border: 0;" allowfullscreen>This is an embedded <a target="_blank" href="https://office.com">Microsoft Office</a> presentation, powered by <a target="_blank" href="https://office.com/webapps">Office</a>.</iframe>
</div>

The notebook embedded below shows every step of the analysis, including definitions of KPIs and the raw code used to produce summary statistics and data visualizations.

<iframe src="https://www.kaggle.com/embed/jennagorham/hospital-performance-analysis-python-version?kernelSessionId=363306722" height="800" style="margin: 0 auto; width: 100%; max-width: 950px;" frameborder="0" scrolling="auto" title="Hospital Performance Analysis - Python Version"></iframe>
