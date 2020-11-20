# ARMA_In-OutSample_Rolling_Backtest  
I am going to back-test strategies of forecasting FX returns on a rolling basis.  
I'll back-test two strategies, one based on Model Selection Method A(In-Sample), and the other based on Method B(Out-Sample).  
## Step 1: Transfrom currency prices to monthly returns.  
## Step 2: Starting from 12/31/1999, at each month-end date, I'll forecast next-month returns for the seven currencies separately using two different model selection methods.  
**Model Selection Method A(In-Sample)**:  
&emsp;At the time point t, estimate AR(1) and MA(1) based on the past 24 monthly return observations(current observation at t included).  
&emsp;Calculate the AICs for the two estimated models for those 24 obsevations, and choose the one with lower AIC(or MSE instead).  
&emsp;Forecast the next period return at time (t+1) using the selected model.  
**Model Selection Method A(Out-Sample)**:  
&emsp;At the time point t, for each model, out-of-samplely forecast the returns for the past 12 months.  
&emsp;For each time point from t-11 to t, say for t-k where k=0,…,11, I'll estimate each model using the past 24 observations, i.e., observations at (t-k-24,…,t-k-1), then based on the estimated model to forecast returns at t-k. Now, for AR(1) model, I have forecasts for t-11, t-10,…,t, the MSE can be calculated as the mean of (forecast - true return)^2. For MA(1) model, I have another vector of 12 forecasts, with corresponded 12 true returns, and I can calculate MSE. The model with lower MSE would be selected. And then we need to calibrate that model using the past 24 observations.  
&emsp;Forecast the next period return at time (t+1).  
## Step 3:  
&emsp;Now at time t, I have made my forecasts for the seven currencies of their monthly returns at t+1.  
&emsp;Suppose the forecasted returns are (r1,r2,…,r7).  
&emsp;I'll transform the forecasts to return sign only: (sign(r1),sign(r2),…,sign(r7)).  
&emsp;I'll invest in currency h with a long position if sign(rh)=1, otherwise I'll invest in it with a short position.  

&emsp;Now, I will figure out the weight vector based on Risk Parity.  
&emsp;Prepare the covariance matrix corresponding to (sign(r1),sign(r2),…,sign(r7)), i.e., for currency h, if sign(rh)=1, I use the currency's original returns; otherwise, use the negative of original returns.  
&emsp;Then I'll calculate the covariance matrix based on past 60 monthly returns (time t included).  
&emsp;Based on the covariance matrix, run Risk Parity to get a weight vector (w1, …, w7) whose summation equals 1.  
&emsp;However, I'll target an annualized volatility of 5%, i.e., I'll invest with the updated weight vector (w1*,…,w7*)=levarage_factor * (w1,…,w7) where the leverage factor = 5%/(the expected volatility of the portfolio given (w1,…,w7)).(Note: w1*,…, w7* are all of positive values)  
## Step 4:  
&emsp;The portfolio (w1*,…,w7*)'s return at time t+1 will be calculated as (sign(r1) x w1* x r1_new+…+ sign(r7) x w1* x r7_new), where sign(rh) is my directional forecast for currency h at time t+1, and rh_new is its actual return at time t+1.  
&emsp;Record the portfolio's returns at time t+1, and go to Step 2 to make forecast for time t+2 and build the portfolio, and stop until 3/29/2019.
