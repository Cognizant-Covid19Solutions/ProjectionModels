# COVID-19 Projection Model

Licensed to the Apache Software Foundation (ASF) under one
or more contributor license agreements.  See the NOTICE file
distributed with this work for additional information
regarding copyright ownership.  The ASF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an
"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
KIND, either express or implied.  See the License for the
specific language governing permissions and limitations
under the License.

Additional information on the development of this COVID Case Projection Model can be found here: (Link to article be pasted)

*Applicable datasets are made available by their respective providers pursuant to the terms set forth on the applicable sites on which they are made available.*

## Development Approach
 
This COVID case projection model is an attempt to build, in the short-term, a  model to project potential upticks in  COVID-19 cases, taking into    accounts social distancing-related containment measures such as - education institution closures, non-essential business closures and a complete lockdown with stay-at-home orders.  By including these key public health and safety measures into the projection, we believe  allows  for more up-to-date evaluation of the impact of these measures, as well as a more accurate  assessment of whether measures such as social distancing  will continue to be effective and warrant extension.  
  
The model considered here is based on the classic Susceptible - Infectious - Recovered (SIR) epidemiological model combined with Bayesian inferencing.  This model allows analyses of the number of COVID-19 positive cases in the region and then shows how the infection rates change over time as various social distancing measures (mild distancing, strong distancing and total lockdown) come into effect. Based on a research conducted on the daily number of confirmed positive COVID-19 cases in Germany (Dehning, et al., 2020), the model assumes that the infection rate in the region begins to decline over time.  The rate of decline projected will depend on when the social distancing measures went into  effect, and the type of social distancing measures that were put into place for that region(infection rate would reduce drastically in the case of a total lockdown and would reduce slowly in the case of only mild distancing). Thus, along with the daily count of confirmed cases in the area, the model can  take into account the dates at which certain social distancing measures were initiated and whether those measures are still in place. Using Bayesian Markov-Chain-Monte-Carlo sampling, the model  leverages the data inputs to estimate a temporal change in the virus transmission rate.  This estimated transmission rate is  then fed into the SIR Model,  which provides a projection of & estimation of the cumulative COVID positive cases in a region for each of the social-distancing scenario, over a period of approximately 28 days.   
 
In order to create a model that is more accurately representative of the actual reported cases, we assume the infection rate and recovery rate follow a dynamic distribution and change over time. For this process, we apply Bayesian Markov Chain Monte Carlo Sampling in order to infer a temporal infection rate from the reported cases over time, and leverage the same for our projections. Furthermore, we assume that at each level of Social Distancing implemented, the infection rate changes to follow a more representative distribution with respect to the shape of the temporal infection rate.  
 
**Theories and Assumptions for Model Development Approach:**

The creation of this model occurred in three different phases:  

**Iteration 1:** Here, we assumed the default prior distribution to be a Half-Cauchy distribution with the infection rate and recovery rate following Log-Normal Distributions. This was a baseline model created for a short-term projection of 35 days. Extending the projection time window to a longer time period, however, did not yield good results as the default distribution applied was not sensitive enough to account for a peak in the infections in time. In other words, if we increased the projection time-window to 120 days instead of 35, the projections were seen to be continuously exponentially or linearly increasing, without a peak in the number of infections.  

**Iteration 2:** In order to increase the projection time window, we looked at making the default distribution a little more sensitive, and redeveloped the algorithm with the Default Priors following a Half-Normal distribution instead of a Half-Cauchy Distribution. This allowed the default distribution  

**Iteration 3:** The final iteration involved changing the Default Prior Distribution to unique shapes more unique and suitable to the Vulnerability of each State. As a result, the Social Vulnerability Index value corresponding to each State (which represents socio-economic factors for vulnerability such as transportation, accommodation, ethnicity, and other factors) was incorporated into the Default Distribution, in order to create more drastic increases in the infection rates for more vulnerable states. This created infection distributions more uniquely representative for each State. 

The model runs on pymc3, where each sampling chain performs 600 steps in order to approximate a posterior distribution. As a result, we obtain three distinct projections for each scenario with respect to each location. These three projections can then be visualized to show a basic what-if scenario on each social distancing measure from the time of projection, which can also then be compared to the actual cases being reported in real-time.  
 
**Estimated Hospital Projections:** 
 
For hospital projections, we have not used or built  any specific model to create  the  projection profiles.. Instead, we leveraged the projection numbers from the simulation model. This is essentially done by analyzing the rate of change in reported hospitalizations and establishing a relationship between the first order derivative of the hospitalization rates and that of the projected confirmed cases. This allows us to extend the expected rate of hospitalizations to the projection time-window as a component of the projected cases.  



 

## Code Details 
  
The python notebooks pertaining to the projection of confirmed cases and hospitalizations follow the naming convention as shown below:

New_code_run_SVI_<day (of month) of run>_<month of run>_<State Code for State in US>.ipynb

For example, the notebook New_code_run_SVI_13_5_CT.ipynb runs the model with actual data observed up to the 13th of May, and generates three datasets for the State of Connecticut: 
•	Confirmed cumulative case projections (for three different social distancing scenarios), along with their upper and lower bounds.
•	Confirmed daily cases projections (for three different social distancing scenarios), along with their upper and lower bounds. 
•	Projected hospitalizations (cumulative and active/effective) with their upper and lower bounds. 

Separate notebooks have been created for each State’s projections, owing to the long runtime for the model. The State to be run can be changed by simply initializing the value of ‘i’ in the notebook. For example: i=4 refers to California, i=31 refers to New Jersey, and i=34 refers to New York. Additionally, the model leverages the Social Vulnerability Index data to differentiate the default distribution of each State, depending on how vulnerable the State is reported to be. 

This projection model can very well be replicated at the County Level by reading the data at the county level instead of the State Level (lockdown dates, County population and Social Vulnerability Index values will need to be initialized separately). 

The notebook can be run on Python (>3.6) and leverages the following packages (can also be installed from the requirements.txt file):

•	Theano (>=1.0.4) 

•	Pymc3 (>=3.7)

•	Matplotlib (>=3.2)

•	Datetime (>=4.3)

•	Pandas (>=1.0.3)

•	Numpy (>=1.18.4)

It’s important to note that development and support for Theano has ended, and is only used here as a backend for pymc3. The newer version, pymc4 was considered initially, as it runs with Tensorflow rather than Theano. However, pymc4 is still at a pre-release phase, and some parts of it may not be as stable as required. 

The datasets used in the Python code can be downloaded at the county level from the following sources:
•	https://usafactsstatic.blob.core.windows.net/public/data/covid-19/covid_confirmed_usafacts.csv 

•	https://svi.cdc.gov/data-and-tools-download.html 
(No direct download link. Go to Data Download section and select Year – 2018, Geography – United States, Geography Type – Counties, File Type – CSV. Documentation for the same can be found here: https://svi.cdc.gov/Documents/Data/2018_SVI_Data/SVI2018Documentation.pdf)

•	https://gis.cdc.gov/grasp/COVIDNet/COVID19_3.html

•	https://www.aljazeera.com/news/2020/03/emergencies-closures-states-handling-coronavirus-200317213356419.html
(Lockdown dates need to be updated based on the changes on this site)

*Applicable datasets are made available by their respective providers pursuant to the terms set forth on the applicable sites on which they are made available.*


The projection approach here is a Bayesian MCMC Sampling algorithm that projects the infection rate and recovery rate over time to be fed into a conventional Epidemiological SIR (Susceptible-Infected-Recovered) Model for the Simulation. The different social distancing scenarios correspond to the behavior of the infection rate in these respective scenarios. Thus, the model projection essentially looks at a what-if scenario for the projected confirmed cases if certain lockdown measures are to be lifted at the time of projection. 

The simulation is regularly validated against the actual reported cases, and for 5 out of 6 States, the actual cases fall within a 95% confidence interval for the models projections. 


The hospitalization projections are calculated by creating a relationship between the first order derivatives of the actual positive cases and the hospitalization rate reported by the CDC for each state on a weekly basis (which can be found for each state on https://gis.cdc.gov/grasp/COVIDNet/COVID19_3.html).

Here, the column ‘cumulative_hos_per_100k’ refers to the weekly cumulative hospitalization rates per 100,000 population for a State. We refer to values published by the CDC up until the 9th of May (for hospitalization projections for the week of 16th May onwards). In order to infer a hospitalization rate for the next few weeks, we look at calculating a ratio of first order derivatives in terms of hospitalizations and confirmed cases for the reported period. In other words, we look at the weekly change in reported hospitalization rates till the 9th of May, as well as the weekly change in the reported confirmed cases till the 9th of May. We then calculate the ratio of change in weekly hospitalizations to the change in weekly confirmed cases. The last value of the reported ratio is then used to show the expected increase/decrease in number of hospitalizations as a factor of the projected confirmed cases, based on which, we can calculate projected hospitalization rates over the next few weeks. 

This relationship is then extended through the projection timeline of the confirmed cases. 


### Cautions when utilizing this Projection Model:

*•The accuracy and confidence limits of the projections developed by this COVID  Case Projection Model depend on the use of the most up to date, publicly available data associated with the area being evaluated.  As information about positive case rates for a particular region and social distancing measures in place may change over time, projections will change.*

*•This  COVID Case Projection Model has not been endorsed,  assessed or cleared for use by any public health or regulatory authorities for its proposed use.*
 
 
### Disclaimer:

*The models developed here are simulation models and their outputs should be taken as simulated projections, not actual model predictions. Validation of the models have been conducted by comparing the projections (along with their confidence intervals) with actual reported cases in the same time period. However, because this is only a simulation model,  Model may not accurately project cases due to to the  anomalous changes in infection rates caused by non-adherence to social distancing, reporting errors, and other factors.*
*Additionally, it is to be noted that the current version of the code takes upwards of 18 hours to complete all iterations.  This is primarily because the current version of the code leverages Theano (support for this open code has now stopped).  
Note that updates to this COVID Case Projection Model may be made without prior notice. Always review the READ ME files prior to running this Model.*


>**Reference**
>
>Dehning, J., Zierenberg, J., Spitzner, F. P., Wibral, M., Neto, J. P., Wilczek, M., & Priesemann, V. (2020, March 29). Inferring change points in the COVID-19 spreading reveals >the eﬀectiveness of interventions . Retrieved from arxiv.org: https://arxiv.org/pdf/2004.01105.pdf 
