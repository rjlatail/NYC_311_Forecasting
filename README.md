# Forecasting Usage of NYC's 311 Service

Since 2003, New York City has maintained the 311 service, a hub that connects residents to city agencies and provide them with quick and easy access to local government.  It is available 24 hours a day, 7 days a week, 365 days a year, providing assistance in 175 languages and to the hearing-impaired.  In 2023, residents used the 311 service 3.2 million times.  It has become the first stop for residents for a whole host of services, including:

- To secure official documents, such as business, pet or marriage licenses
- To get a business, pet or marriage license
- To report a pothole or request street resurfacing
- To stay up-to-date on public school calendars, including snow days
- To be informed about city-wide parking rules
- To inform city agencies about quality of life concerns such as noise or litter
- To request maintenance for city-owned apartments and buildings
- To make payments towards property taxes, parking tickets, or other city assessments

![pothole](Images/potholes.jpg)
*[Image source](https://www.flickr.com/photos/nycstreets/25527414131): Used under CC BY-NC-ND 2.0*

While the cost to the city is not large ($68 million out of a $107 annual billion budget, or less than 0.1%) the value it provides to residents and government officials is considerably larger.  Prior to its advent, the city maintained over 40 separate hotlines and call centers for non-emergency resident services, often with overlapping responsibilities.  As a result of having so many entry points to the city, residents often were confused regarding the right way to contact the City.  Very often, residents chose to call the NYPD or 911, even for non-criminal or non-emergency purposes.  By creating such a recognizable and easy-to-remember portal, the City relieved its agencies of this burden and has been able to more effectively address residents' concerns.

Since inception, the service has continued to expand its usefulness and connectivity.  It maintains a significant social media presence, and in 2013 introduced the 311 App to answer questions or allow service request submissions while avoid lengthening call queues.  However, the service is still in very high demand.  The City Council recently passed a bill to require virtual queues with estimated wait times for callers:

![tweet](Images/NYC_Council_Tweet.png)

Given the consistent demand for and high expectations of the service, anticipating usage of the service would enable the City to meet its citizens needs on a timely basis.  <ins><b>Successful forecasting of next-day, next-week and next-month volumes</b></ins> would lead to residents who are more satisfied with the responsiveness of their local government.  

### Data Sources
**NYC Open Data**: Under the Open Data Law, all New York City agencies are required to make their datasets publicly available on a public portal.  Prior to the creation of the portal, city agencies were required to provide information upon request through New York State's Freedom of Information Law.  However, this method of retrieving information was cumbersome for the requestor and often ineffective, as requests would be submitted ad hoc to a bureaucracy that was not structured to handle them.  The law required city agencies to proactively gather and make available their datasets to the public at large.  The [311 Service Requests](https://data.cityofnewyork.us/Social-Services/311-Service-Requests-from-2010-to-Present/erm2-nwe9/about_data) data set has been available since 2011 and now has over 36 million records included with 41 features.

**Open Meteo**: Daily weather data for New York City was sourced from Open Meteo's public API.  Open Meteo utilizes open data from various national weather services and permits usage under the "CC BY 4.0 DEED (Attribution 4.0 International)" which allows reuse with attribution.  The license for this data can be found [at this link](https://open-meteo.com/en/license).

**Population**: Annual figures sourced from the US Census Bureau, interpolated on a linear basis for intermediate dates.

### Data Preparation and Transformation
The data from NYC Open Data was originally over 20GB.  In addition, while the dataset is generally clean, there is some standard cleaning and transformations that need to be done.  In order to limit the read/write time and make the operation of the main notebook more efficient, all of the data was read, cleaned and converted into a pickle file.  The main notebook reads this data considerably faster as a result.

**Scaling**.  The weather data is unscaled, and has different types of distribution.


INSERT WEATHER CHARTS HERE


Based on these distributions, each feature was scaled in an appropriate fashion:

* Rainfall, snowfall and wind speed: Box-Cox transformation, addresses right-skewed distributions
* Temperatures - Temperatures are normally distributed when holding seasonality constant. Seasonal decompose first, then standard scale, then seasonal recompose
* Daylight Duration - Minmax scaling

### Modeling
Time series frequently use one of a few types of baseline model.  In particular, AR(1) (or shift(1), which reports yesterday's value with a coefficient) are particularly common, since time series typically have a strong autoregressive component.  The goal is to surpass the performance of this baseline.

INSERT TIME SERIES IMAGES FROM PPT

**Root Mean Squared Error**.  The value of forecasting to the 311 service and other agencies is to better predict the necessary resources to respond to requests. The amount of resources necessary is most directly applicable to mean absolute error. However, the service should place a heavier emphasis on outliers. Residents' dissatisfaction with government performance likely follows an exponential pattern, not a linear one. 20 minutes wait time is more than two times worse than 10 minutes. Two weeks for an agency to respond is more than twice as bad as one week. **RMSE** captures both the scale of the problem and the importance of outliers. When using grid search to select parameters, **Akaike Information Criterion** will be used to select the winning combination.

**Baseline Model AR(1)**.  AR(1) is a simple model that is a fairly good predictor of many time series variables.  Because the model only looks back a single period, it can only forecast reliably for one period.  In order to test how the model performs on the test set, a rolling forecast must be produced which projects one period, then rolls forward into the next period.  The period that was previously the first period in the test set is now the last period in a new training set, and the oldest training period is dropped.  This is the first model's results:

| | AR(1) daily |
|-|-------------------|
| RMSE on train | 1215 |
| RMSE on test | 1129 |

**First Simple Model: ARIMA**.  ARIMA models integrate autoregressive components, moving averages and differencing.  To apply ARIMA, we will need to search for the autoregressive (p) term and the moving average (q) term.  But first, we need to ensure that our data is stationary.  Stationarity in data refers to the condition where the statistical properties of the series (mean, variance, autocorrelation) do not change over time.  Time series forecasting models benefit from stationary data.  The Augmented Dickey-Fuller test (or ADF) is a significance test to determine whether the data is stationary.

ADF Statistic: -2.521  
p-value: 0.110  

**Not stationary**. Because the p-value is not less than 0.05, and the statistic is not very large, it is not clear that the data is stationary.  In order to transform the data into a stationary dataset, one-period differencing will be applied.  After that transformation, the ADF test is run again:

ADF Statistic (1st diff): -21.567  
p-value (1st diff): 0.0

**Stationary**.  The data is now clearly stationary.  Next, the p and q terms must be established.  To do, the Autocorrelation Function and Partial Autocorrelation Function creates "lollipop" charts to help visually determine the likely terms:

INSERT LOLLIPOP CHARTS

The drop-off after 1 term in each chart suggests that p = 1 and q = 1.  The oscillation makes it difficult to determine for sure.  Notably, the 7-day pattern of spikes suggests 7-day seasonality.  The first simple model will be ARIMA(1,1,1).  

| | AR(1) daily | ARIMA(1,1,1) |
|-|-------------------|--------|
| RMSE on train | 1215 | 1068 |
| RMSE on test | 1129 | 989 |

- ARIMA(1,1,1)
- SARIMA(1,1,1)(1,1,1,7)
- SARIMA GRID SEARCH
- SARIMAX
-   Exogenous variables
- Prophet
- GARCH-X
- LSTM


### Results


