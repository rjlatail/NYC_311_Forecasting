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

Given the consistent demand for and high expectations of the service, anticipating usage of the service would enable the City to meet its citizens needs on a timely basis.  <b>Successful forecasting of next-day, next-week and next-month volumes would lead to residents who are more satisfied with the responsiveness of their local government.  

### Data Sources
**NYC Open Data**: Under the Open Data Law, all New York City agencies are required to make their datasets publicly available on a public portal.  Prior to the creation of the portal, city agencies were required to provide information upon request through New York State's Freedom of Information Law.  However, this method of retrieving information was cumbersome for the requestor and often ineffective, as requests would be submitted ad hoc to a bureaucracy that was not structured to handle them.  The law required city agencies to proactively gather and make available their datasets to the public at large.  The <ins>311 Service Requests</ins> data set has been available since 2011 and now has over 36 million records included with 41 features.

**Open Meteo**: Daily weather data for New York City was sourced from Open Meteo's public API.  Open Meteo utilizes open data from various national weather services and permits usage under the "CC BY 4.0 DEED (Attribution 4.0 International)" which allows reuse with attribution.  The license for this data can be found [at this link](https://open-meteo.com/en/license).

### Data Preparation and Transformation
<code style>TO BE COMPLETED</code>
- Data = 20+ GB, be sure to select only relevant features
- Limited dates
- Drop no locations
- Alter datetimes
- Comsolidated agencies and complaint types

### Modeling
Time series frequently use one of a few types of baseline model.  In particular, AR(1) (or shift(1), which reports yesterday's value with a coefficient) are particularly common, since time series typically have a strong autoregressive component.  The goal is to surpass the performance of...

- AR(1)
- ARIMA(1,1,1)
- SARIMA(1,1,1)(1,1,1,7)
- SARIMA GRID SEARCH
- SARIMAX
-   Exogenous variables
- Prophet
- LSTM


### Results


