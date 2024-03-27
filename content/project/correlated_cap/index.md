---
title: "A Neighborhood Analysis of Drug/Narcotic Arrests in Seattle, Washington"
author: "Jay Anderson, Sofia Barragan, Ben Christensen"

summary: "A capstone project studying the neighborhood-level development of the second opioid epidemic in Seattle."

tags: 
- Data Science
- Public Health
categories: []

image:
  caption: ""
  focal_point: ""
  preview_only: true

links:
- name: ""
  url: https://sofiabarragan.com/media/bayes/bayes_final.html
  icon_pack: fas
  icon: external-link-alt

date: 2021-12-15T14:47:00-05:00
---

<script src="//yihui.org/js/math-code.js" defer></script>
<!-- Just one possible MathJax CDN below. You may use others. -->
<script defer
  src="//mathjax.rstudio.com/latest/MathJax.js?config=TeX-MML-AM_CHTML">
</script>

<style>
    table {
        width: 100%;
    }
</style>

# Motivation

In collaboration with [Jay Anderson](https://juthidewan-portfolio.netlify.app) and [Ben Christensen](https://sdingx.github.io/portfolio/), we designed this project for our Correlated Data Capstone course taught by [Dr. Brianna Heggeseth](https://ajohns24.github.io/portfolio/). 

To Brianna, we sincerely want to thank you for being an amazing, patient, and compassionate professor and working with us throughout all our various ups-and-downs in this course and in this project. 




# Introduction







1. Widespread abuse of prescription opioids (1999-2010)
2. Widespread abuse of illicit heroin (2010-2013)
3. Widespread abuse of synthetic opioids (i.e. fentanyl) (2013-Present)







This project is, then, an attempt to understand and identify neighborhood disparities of opioid use throughout Seattle, King County, Washington and potential risk factors during the second wave of the epidemic. Specifically, we aim to model narcotic arrest counts throughout Seattle between 2010-2013 using spatial regression with geotagged SPD arrest records accessed via the Seattle Open Data Portal and census tract-level demographic information from the 2010, 2011, 2012, and 2013 American Community Surveys accessed via the Tidycensus package in R.


- **Longitude and Latitude:** The relative location of the crime.
- **Year:** The Year that the crime occurred. 
- **Narcotic Crime Arrest Count:** a variable we created that is a count of all of the Narcotic related arrests per year per census tract.
	+ To scale this coefficients to be practically significant, we multiplied this variable by 5.
- **Black:**  The percent of residents identifying as Black in a defined census  tract.
	+ To scale this coefficients to be practically significant, we multiplied this variable by 5.
	+ To scale this coefficients to be practically significant, we multiplied this variable by 5.
- **Immigrant:** The percent of residents who immigrated to the United States living in a defined census  tract.
	+ To scale this coefficients to be practically significant, we multiplied this variable by 5.
- **Age:** The average age of residents living in a defined census tract.
- **Income:** The median yearly income of residents living in a defined census tract.
	+ To scale this coefficient to be practically significant, we divided this variable by 100.
    


# Methods




<center>
<img src="/media/correlated/plot_1.png" width="100%" height="100%" />
</center>

From the observed distributions, the clear zero-inflation and non-constant decay as count increases indicate that a simple poisson regression may be inappropriate. Although multiple approaches that account for zero-inflation exist, we performed standard negative binomial regression with a log-link function to model narcotic arrest counts for each year; we implemented this model to account for non-constant variance, establish crude baselines for comparison, and to motivate the application of spatial regression models since this technique cannot account for spatial clustering. Our four year-specific models of the arrest count in census tract, `$i$`, are of the form 
where `$k$` is an estimated dispersion parameter and
`$$
$$`

<center>
<img src="/media/correlated/plot_2.png" width="100%" height="100%" />
</center>

The simultaneous autoregressive (SAR) model is a spatial regression technique that extends the standard regression frameworks to account for underlying spatial relationships between observations by weighting observations according to an underlying NNS (Whittle 1954; Hooten, Ver Hoef, and Hanks 2014; Wall 2004; Heggeseth 2022). The conditional autoregressive (CAR) model is another spatial regression technique that also adjusts for underlying spatial correlation between observations, but in a manner that is distinct from SAR models (Besag 1974; Wall 2004; Heggeseth 2022). Specifically, CAR models assume a Markov property or ‘memorylessness’ to the relationship between neighbors and thus impose weaker spatial correlation structures than SAR models (Wall 2004). The relative benefits of CAR and SAR models are highly contextual and specific to the data and problem at hand. Despite this difference, they do share the same general formula.

In this analysis, we implemented year-specific SAR and CAR models to predict average narcotic arrest counts within census tracts. We define our proposed models below:

- `$\lambda\boldsymbol{WY}$` is a matrix product of some constant `$\lambda$`, our penalized proximity matrix `$W$`, and a matrix of narcotic arrests for all neighborhoods `$Y$`.
- `$\boldsymbol{X\beta}$` is the product of the `$134 \times 8$` covariate matrix— where entries of this matrix are the observed values of the `$8$` predictors (columns) for the `$134$` different census tracts (rows) in our data— and a `$1 \times 8$` coefficient vector.
- `$\boldsymbol{\epsilon}$` is the matrix of our tract-specific error terms with `$\boldsymbol{\epsilon} \sim N(0,\sigma ^2I)$`

- Bayesian Information Criterion (BIC) metrics 
- Moran's I Test Statistics of Residuals

# Results





<center>
<img src="/media/correlated/plot_3.png" width="100%" height="100%" />
</center>

<center>
| Year 	| Observed Incidence Rate 	|
|:----:	|:-----------------------:	|
| 2010 	|         18.32843        	|
| 2011 	|         12.86119        	|
| 2012 	|         12.46315        	|
| 2013 	|         10.42816        	|
</center>

Yearly citywide IR estimates suggest that the incidence rate of narcotic arrests was highest in 2010 and lowest in 2013. However, the census tract counts narcotic arrests captured in the above figure demonstrate considerable spatial clustering of narcotic arrests between the I-5 and Duwamish Waterway in Seattle. Importantly, arrests were highly concentrated in Seattle's Industrial and Downtown districts with the highest observed incidence rates occurring in one census tract (GEOID: 53033008100) encompassing Seattle's Downtown district and Pioneer Square. Unlike the citywide estimates, these observed incidence rate estimates suggest that between 2010 and 2011, for every group of 1000 people, there would would be `$\approx$` 358 narcotic-related arrests— a dramatic difference from the citywide estimate. Additionally, incidence rates in these metropolitan areas sharply increased in 2012 and 2013, with IR estimates of 592 in 2012 and 496 in 2013.

<center>
<img src="/media/correlated/plot_2.png" width="100%" height="100%" />
</center>

Like narcotic arrests, many of our proposed predictors demonstrate clear spatial clustering behaviors. Namely, the percent of white residents was negatively spatially associated with arrest incidence rates across all year, while immigrant percent was positively associated. However, due to demographic transitions across Seattle many of these associations may differ by year. Covariate maps demonstrate some slight collinear trends between average age and income where the wealthiest census tracts also tended to be some of the oldest. While income was negatively associated with both Black, Immigrant, and Latinx percentages.


## Model Comparison



<center>
**Simple Negative Binomial (with and without outliers)**
</center>

<center>
<img src="/media/correlated/plot_5.png" width="100%" height="100%" />
</center>




<center>
<img src="/media/correlated/plot_6.png" width="100%" height="100%" />
</center>



<center>
<img src="/media/correlated/plot_7.png" width="100%" height="100%" />
</center>



<center>
| Year 	| Negative Binomial 	|    SAR    	|    CAR   	|
|:----:	|:-----------------:	|:---------:	|:--------:	|
| 2010 	|      11.43584     	| 15.553052 	| 14.99905 	|
| 2011 	|      13.73855     	| 18.710910 	| 19.53239 	|
| 2012 	|      45.55720     	|  9.956018 	| 14.71446 	|
| 2013 	|     116.89098     	|  9.677963 	| 12.54004 	|
</center>






### Moran's I



The Moran's I Test is a statistical test used to identify the occurrence of spatial clustering via standard hypothesis testing where the null, `$H_0$`, assumes that residuals are independent over space. In this analysis, we use the Moran's I hypothesis test on our residual errors to identify if there is leftover spatial effects on our model. In this section, we present tables of our Moran's I test output for each of our three models over time.


<center>
| Year 	|  p-value 	| Moran I statistic 	| Expectation 	|  Variance 	|
|:-----:	|:--------:	|:-----------------:	|:-----------:	|:---------:	|
|  2010 	| 0.00e+00 	|     0.3135200     	|  -0.0075758 	| 0.0026623 	|
|  2011 	| 7.00e-07 	|     0.2481396     	|  -0.0075758 	| 0.0026553 	|
|  2012 	| 8.40e-06 	|     0.2208913     	|  -0.0075758 	| 0.0026315 	|
|  2013 	| 1.99e-05 	|     0.2093290     	|  -0.0075758 	| 0.0025846 	|
</center>


From the above table, we found statistically significant evidence of positive spatial correlation (Moran I statistic > 0) in our residual errors for every year in our study (p-value < 0.05), suggesting that nearby errors are correlated. As such, we reject `$H_0$` and conclude that our simple negative binomial model could not sufficiently model narcotic arrests because arrests are spatially correlated. Interestingly, the magnitude of spatial clustering (Moran I statistics) varied over time with 2010 seeing the highest clustering and 2012 seeing the lowest.

<center>
| Year 	|  p-value  	| Moran I statistic 	| Expectation 	|  Variance 	|
|:-----:	|:---------:	|:-----------------:	|:-----------:	|:---------:	|
|  2010 	| 0.5213880 	|     0.0254418     	|  -0.0075758 	| 0.0026515 	|
|  2011 	| 0.4052197 	|     0.0352947     	|  -0.0075758 	| 0.0026529 	|
|  2012 	| 0.4737983 	|     0.0294463     	|  -0.0075758 	| 0.0026713 	|
|  2013 	| 0.3782288 	|     0.0380083     	|  -0.0075758 	| 0.0026762 	|
</center>


Using a SAR model structure, we found insufficient evidence to reject the `$H_0$` and ultimately the absence of spatial correlation between observations across all years (p-value > 0.05). This is good! This means that our SAR model sufficiently accounted for the spatial autocorrelation between narcotic arrests and can then produce reliable hypothesis tests. 

<center>
| Year 	|  p-value  	| Moran I statistic 	| Expectation 	|  Variance 	|
|:-----:	|:---------:	|:-----------------:	|:-----------:	|:---------:	|
|  2010 	| 0.0143041 	|     -0.1339276    	|  -0.0075758 	| 0.0026607 	|
|  2011 	| 0.1599391 	|     -0.0800319    	|  -0.0075758 	| 0.0026584 	|
|  2012 	| 0.0167360 	|     -0.1312093    	|  -0.0075758 	| 0.0026704 	|
|  2013 	| 0.1781474 	|     -0.0772408    	|  -0.0075758 	| 0.0026769 	|
</center>

From the above table, we found statistically significant evidence of positive spatial correlation (Moran I statistic	> 0) in our residual errors for 2010 (p-value = 0.014) and 2012 (p-value = 0.016). As such, we reject `$H_0$` and conclude that our CAR model could not sufficiently model narcotic arrests because arrests are spatially correlated.





<center>
| Year 	| Negative Binomial 	|    SAR   	|    CAR   	|
|:----:	|:-----------------:	|:--------:	|:--------:	|
| 2010 	| 1381.217          	| 459.2198 	| 474.4112 	|
| 2011 	| 1292.076          	| 489.8557 	| 499.3556 	|
| 2012 	| 1239.588          	| 502.6637 	| 509.9817 	|
| 2013 	| 1177.707          	| 488.0485 	| 497.4415 	|
</center>

Across all three models, the SAR model consistently had the best BIC metrics for all years in our study. Although the negative binomial and SAR BICs were dramatically different, the differences between SAR and CAR models were marginal. For both spatial models, there was meaningful temporal variation in BIC estimates, with both spatial models performing best in 2010 and worst in 2012. While the negative binomial model never outperformed the SAR or CAR models, negative binomial BIC metrics were worst in 2010 and best in 2013. As indicated by our Moran's I test, there is some considerable variation in the spatial effects over time which may be reflected in the temporal shifts in these BIC metrics. 




Using our SAR model with `$\alpha=0.05$` and when controlling for other covariates, we found sufficient evidence to conclude that major metropolitan areas (i.e. total population above the 80th percentile) had statistically significant elevated risks of narcotic arrests relative to all other census tracts, regardless of the year. However, most other predictors had time-varyingly significant effects on the risk of narcotic arrests; for the sake of brevity, we exclusively list the significant predictors for each year-specific SAR model in the following tables.

|    term   	|  estimate 	| std.error 	|  z.value 	|  p.value  	|
|:---------:	|:---------:	|:---------:	|:--------:	|:---------:	|
| majorTRUE 	|  94.85268 	| 0.2363774 	| 2.822070 	| 0.0047715 	|
|   black   	| 216.64634 	| 0.4527111 	| 2.546028 	| 0.0108956 	|
| immigrant 	| 134.38144 	| 0.3977942 	| 2.141257 	| 0.0322533 	|
</center>


<center>
|    term   	|  estimate 	| std.error 	|  z.value 	|  p.value  	|
|:---------:	|:---------:	|:---------:	|:--------:	|:---------:	|
| majorTRUE 	|  114.2626 	| 0.2619126 	| 2.909489 	| 0.0036202 	|
|   white   	| 4800.3169 	| 1.7940684 	| 2.169307 	| 0.0300594 	|
|   black   	|  263.6595 	| 0.4627853 	| 2.789734 	| 0.0052751 	|
| immigrant 	|  210.1960 	| 0.4537137 	| 2.495041 	| 0.0125943 	|
</center>

<center>
|    term   	| estimate 	| std.error 	|  z.value 	|  p.value  	|
|:---------:	|:--------:	|:---------:	|:--------:	|:---------:	|
| majorTRUE 	| 153.9560 	| 0.2796861 	| 3.332274 	| 0.0008614 	|
|   latinx  	| 244.0459 	| 0.4765524 	| 2.592799 	| 0.0095198 	|
</center>

<center>
|    term    	|   estimate  	| std.error 	|  z.value  	|  p.value  	|
|:----------:	|:-----------:	|:---------:	|:---------:	|:---------:	|
| excessTRUE 	|  920.40565  	| 1.0810157 	|  2.148706 	| 0.0316577 	|
|  majorTRUE 	|  114.45400  	| 0.2453656 	|  3.109340 	| 0.0018751 	|
|    white   	| 18694.91763 	| 1.7932750 	|  2.919893 	| 0.0035015 	|
| med_income 	| -99.18991   	| 1.3696090 	| -3.516174 	| 0.0004378 	|
| immigrant  	| 374.84528   	| 0.4590921 	| 3.393260  	| 0.0006907 	|
</center>








# References



2018. To Acknowledge That the War on Drugs Has Been a Failed Policy in Achieving the Goal of Reducing Drug Use, and for the House of Representatives to Apologize to the Individuals and Communities That Were Victimized by This Policy. https://www.govinfo.gov/content/pkg/BILLS-115hres933ih/html/BILLS-115hres933ih.htm.

Alcohol Institute, Addictions Drugs \&. 2022. “Opioid Trends Across Washington State.” Washington State Opioid Trends. University of Washington. https://adai.washington.edu/WAdata/opiate_home.htm.

Altekruse, Sean F, Candace M Cosgrove, William C Altekruse, Richard A Jenkins, and Carlos Blanco. 2020. “Socioeconomic Risk Factors for Fatal Opioid Overdoses in the United States: Findings from the Mortality Disparities in American Communities Study (MDAC).” PLoS One 15 (1): e0227966.
Besag, Julian. 1974. “Spatial Interaction and the Statistical Analysis of Lattice Systems.” Journal of the Royal Statistical Society: Series B (Methodological) 36 (2): 192–225.
Disease Control, Centers for. 2021. “Drug Overdose.” Centers for Disease Control and Prevention. Centers for Disease Control; Prevention. https://www.cdc.gov/drugoverdose/epidemic/index.html.

Division, Seattle Field. 2017. Opioid Overdose Deaths Remain High in Seattle and King County.
Heggeseth, Brianna. 2022. “7.4 Areal Data.” In Correlated Data Notes.

Hooten, Mevin B, Jay M Ver Hoef, and Ephraim M Hanks. 2014. “Simultaneous Autoregressive (SAR) Model.” Wiley StatsRef: Statistics Reference Online, 1–10.
KFF. 2021. “Opioid Overdose Deaths by Race/Ethnicity.” KFF. Kaiser Family Foundation. https://www.kff.org/other/state-indicator/opioid-overdose-deaths-by-raceethnicity/?dataView=1¤tTimeframe=20\&amp;selectedRows=.

Swift, M. Maria AND Elfassy, Samuel L. AND Glymour. 2019. “Racial Discrimination in Medical Care Settings and Opioid Pain Reliever Misuse in a u.s. Cohort: 1992 to 2015.” PLOS ONE 14 (12): 1–12. https://doi.org/10.1371/journal.pone.0226490.

Walker, Kyle. 2022. Tigris: Load Census TIGER/Line Shapefiles. https://CRAN.R-project.org/package=tigris.

Walker, Kyle, and Matt Herman. 2021. Tidycensus: Load US Census Boundary and Attribute Data as ’Tidyverse’ and ’Sf’-Ready Data Frames. https://CRAN.R-project.org/package=tidycensus.

Wall, Melanie M. 2004. “A Close Look at the Spatial Structure Implied by the CAR and SAR Models.” Journal of Statistical Planning and Inference 121 (2): 311–24.
Whittle, Peter. 1954. “On Stationary Processes in the Plane.” Biometrika, 434–49.


# R Packages


Alboukadel Kassambara (2020). ggpubr: ‘ggplot2’ Based Publication Ready Plots. R package version 0.4.0. https://CRAN.R-project.org/package=ggpubr

Bivand, Roger S. and Wong, David W. S. (2018) Comparing implementations of global and local indicators of spatial association TEST, 27(3), 716-748. URL https://doi.org/10.1007/s11749-018-0599-x

Garrett Grolemund, Hadley Wickham (2011). Dates and Times Made Easy with lubridate. Journal of Statistical Software, 40(3), 1-25. URL https://www.jstatsoft.org/v40/i03/.

Hao Zhu (2021). kableExtra: Construct Complex Table with ‘kable’ and Pipe Syntax. R package version 1.3.4. https://CRAN.R-project.org/package=kableExtra

Kirill Müller (2020). here: A Simpler Way to Find Your Files. R package version 1.0.1. https://CRAN.R-project.org/package=here

Kyle Walker (2021). tigris: Load Census TIGER/Line Shapefiles. R package version 1.5. https://CRAN.R-project.org/package=tigris

Kyle Walker and Matt Herman (2021). tidycensus: Load US Census Boundary and Attribute Data as ‘tidyverse’ and ‘sf’-Ready Data Frames. R package version 1.1. https://CRAN.R-project.org/package=tidycensus

Pebesma, E., 2018. Simple Features for R: Standardized Support for Spatial Vector Data. The R Journal 10 (1), 439-446, https://doi.org/10.32614/RJ-2018-009

Roger S. Bivand, Edzer Pebesma, Virgilio Gomez-Rubio, 2013. Applied spatial data analysis with R, Second edition. Springer, NY. https://asdar-book.org/

Sam Firke (2021). janitor: Simple Tools for Examining and Cleaning Dirty Data. R package version 2.1.0. https://CRAN.R-project.org/package=janitor

Stefan Milton Bache and Hadley Wickham (2020). magrittr: A Forward-Pipe Operator for R. R package version 2.0.1. https://CRAN.R-project.org/package=magrittr

Venables, W. N. & Ripley, B. D. (2002) Modern Applied Statistics with S. Fourth Edition. Springer, New York. ISBN 0-387-95457-0

Wickham et al., (2019). Welcome to the tidyverse. Journal of Open Source Software, 4(43), 1686, https://doi.org/10.21105/joss.01686

Winston Chang (2012). extrafontdb: Package for holding the database for the extrafont package. R package version 1.0. https://CRAN.R-project.org/package=extrafontdb

Winston Chang, (2014). extrafont: Tools for using fonts. R package version 0.17. https://CRAN.R-project.org/package=extrafont