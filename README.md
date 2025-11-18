# Flight Delay Predictions and Optimization for JetBlue at JFK Airport

This project analyzes the 2023 flight operations for JetBlue Airways (B6) at JFK Airport with the primary goal of predicting departure delays and identifying root causes. The central hypothesis is that departure delays are not random and are driven by cascading delays from late arriving inbound aircraft.

The project successfully builds a machine learning model that confirms this hypothesis. This model is then used as a predictive engine to run simulations that quantify the potential savings from operational improvements and provide a data-drive approach for scheduling optimization.

In addition, a Flight Delay Forecasting Model using Time Series Analysis was performed to shift the focus from individual flight segments to daily flight operational trends. The goal here was to forecast the average daily departure delay at JFK which can better enable resource planning and crew scheduling.

## Data

* `departures.csv`: All JetBlue departures from JFK in 2023
* `arrivals.csv`: All JetBlue arrivals from JFK in 2023

Data sourced from the Bureau of Transportation Statistics by the US Department of Transportation website.

## Analysis and Modeling

1. Key Feature Engineering: Linking Flights by "Tail Number" (Aircraft registration)
  * Data Cleaning: All columns were loaded and processed by stripping white space and dropping empty rows
  * Arrivals and Departures combined under a single tail number
  * Merged datasets to combine arrivals and departures
  * Created more features like `inbound_flight_arr_delay` which is the total delay of the aircraft's previous flight and various inbound flight delay reasons, `actual_turnaround_time_min` to calculate time spent on the ground, and others.

2. Exploratory Data Analysis
   * Plot showing Turnaround vs Delay

3. Modeling (Classification)

Initially, Random Forest Classifiction was performed with the binary dependent variable of delayed or not delayed. This initial model shows how modeling flight delays are more complicated and involved than trying to predict a simple delay as a 5 minute delay does not incur the same effect as a 50 minute delay.
Thus, Regression was chosen to model the continuous dependent variable as minutes delayed.

4. Modeling (Regression)

* Temporal data split to avoid data leakage (Jan - Oct: Training) (Nov - Dec: Testing)
* XGBoost chosen as best model with 4 variations:
  * Model A: Schedule only
  * Model B: Schedule + Inbound delay
  * Model C: Schedule + Inbound delay + Delay reasons
  * Model D: Hyperparameter Tuned Model C
* Final Model D R-squared: 0.3314 and MAE: 22.4 minutes

This model can explain about 33.14% of all departure delay variance with predictions that are generally about 22 minutes off.

## Schedule Optimization and Simulation

Scenario 1: Capping ALL Inbound Flight Delays
* Question: What if no inbound flight arrives more than 15 minutes late?
* Method: Cap inbound delays at 15 minutes for predictions
* Result: Maximum and greatest possible outcome for avoiding delays and saving time/money

Scenario 2: Targeting Riskiest Routes
* Question: Because capping all flights is unrealistic, what if I target the 20 routes with the highest inbound delays on average?
* Method: Identify which routes have the highest delays and only capping the inbound delays for those 20 airport routes
* Result: Although this scenario did not "save" as much time/money, it is a more realistic and targeting approach for segmenting certain flights and focusing operations on those routes that are chronically delayed.

## Flight Delay Forecasting

Two Time Series Models were selected for the analysis (ARIMA and SARMIAX). The ARIMA model acted as a baseline approach that only used past departure delays to predict future departure delays while the SARIMAX model used two exogenous variables (inbound flight delay and inbound weather delay) for the forecasting.

The same temporal training and testing data set were used for the forecasting analysis.

ARIMA Results:
MAE (min) - 10.66
RMSE (min) - 14.26

SARIMAX Results:
MAE (min) - 7.45
RMSE (min) - 9.28

The SARIMAX model was the best performer for forecasting future flight delays.

## Tools and Libraries

* Python
* Pandas
* NumPy
* Scikit-learn (Pipeline, Column Transformer, Standard Scaler, One Hot Encoder, Randomized Search CV, Train/Test/Split)
* XGBoost
* Matplotlib and Seaborn
* ARIMA
* SARIMAX
* Statsmodels
