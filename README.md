# COVID TRACKER

### By: Eating the frog
Nasa Space Apps Challenge. A code to calculate the infection risk of COVID 19.

## Summary 
COVID Tracker is an application that predicts individualized and geolocated COVID-19 risk infection in real time. This mobile application is being developed mainly to collect and process data related to Sars Cov2 from the Peruvian regions. Therefore, the information used comes from specialized databases on the subject. The novel model for calculating the percentage risk of infection is based on a multilayer approach with epidemiological, environmental and other variables (such as reinfection rate and vaccination status), supported by the most recent literature. The OpenCovid Peru web portal is being used to obtain data on the status of the pandemic in each region of the country and the Waki portal to obtain data on environmental variables. After obtaining the information of the current location of the user through GPS or from a manual configuration, the application calculates the risk taking into account the variables already mentioned. Based on this, personalized messages for prevention are displayed in relation to a certain range of risk (very low, low, moderate, high and very high). This application will prevent massive contagion and generate a culture of more responsible prevention, since it provides biosafety measures according to the current situation of the user and their environment. In the long term, COVID Tracker can be used in other parts of the world, as long as there is enough data updated daily to perform the calculations.

## How COVID Tracker Works
Open Covid Peru, Waqi and important personal information (previous infection and vaccination) help us to determinate a multi-variable model for calculating the Covid 19 infection risk factor. To get the data from the mentioned databases we are using APIs which provide us real time updates. Inside the app, the user gives permission to use GPS feature or sets an specific location. Then, a form containing the personal information is filled. With this information the app shows the user the risk factor which is divided in five risks levels: very low 0 \% - 20 \%), low ( 20 \% - 40 \%), moderate (40 \% - 60 \%), high (60 \% - 80 \%) and very high (80 \%, 100 \%). Finally, according to the ranges some messages that guide social awareness and health security are displayed."

## Our Novel Model
Our model is composed by 3 layers: COVID epidemiological context, environmental context and vaccination and reinfection factor.

![logframe](https://github.com/morphisjustfun/NASA_CHALLENGE_UTILS/blob/master/images/readME/MODEL.jpeg)

### Epidemiological layer
Open Covid Peru offers six real-time epidemiological factors since february 2021:

- regions.avgTest = Quantity of applied COVID tests regions.positivity = Quantity of positive results for each one hundred tests. regions.population - Quantity of inhabitants.
- regions.camasCovid = Percentage of Covid occupied hospital beds until Sunday of this same week.
- regions.uci = Percentage of ICU occupied hospital beds until Sunday of this same week.
- Weekly confirmed cases = Quantity of people with COVID positive results in the week = regions.positiveCases_100 * regions.population / 100000
- Weekly deaths = Quantity of people with comfirmed COVID deaths in the week = regions.fall_100 * regions.population / 100000

In order to ensure a relative scale and match the rates proposed by HHS, we divided two variables by the population, and renamed Open Covid Peru variables with more descriptive names:

- confirmedDeaths100k = Quantity of confirmed COVID deaths for each one hundred thousand people.
- confirmedCases100k = Quantity of positive COVID tests for each one hundred thousand tests.
- appliedTests100k = Quantity of applied COVID tests for each one hundred thousand people.
- positivityRate = Rate of positive COVID tests by total applied COVID tests of this same week.
- bedsOccupiedPercentage = Percentage of Covid occupied hospital beds until Sunday of this same week.
- bedsUCIOccupiedPercentage = Percentage of ICU occupied hospital beds until Sunday of this same week.


To analize the relation between the variables we built heat maps and pairplots in a regional context. Even though at the beginning it was planned to take a national approach, the correlations did not follow a regular pattern across the different regions. Some graphis can be inspected in images folder of this repository.

In the peruvian context the measure of confirmed cases were not rigorous, then to base our model in this variable was not appropiate at all. For that reason we better took a robust variable: confirmedDeaths100k. First of all, the number of reported deaths in a day is unique and there are not duplicated data. Something that would happen with covid tests which can be applied many times in a day for only one person. Second, the number of reported deaths in a day reveals the impact of covid status 14 days ago. As it is known that the time for the covid19 to incubate is 14 days. Third, the 'confirmedDeaths100k' variable has a  significant correlation (R greater than 0.6) with the other variables (confirmedCases100k, appliedTests100k, positivityRate, bedsOccupiedPercentage, bedsUCIOccupiedPercentage) and we noticed that it was the best variable to explain the others in the model. In that sense, it is possible to take into consideration the confirmed deaths for each 100k people variable (*confirmedDeaths100k*) as a basis to make predictions of the risk in the epidemiological layer of the model. 

![logframe](https://github.com/morphisjustfun/NASA_CHALLENGE_UTILS/blob/master/images/readME/amazonasPLOTS.png)


To establish risk scales we contextualized the HHS's (U.S. Department of Health and Human Services) model 


![logframe](https://github.com/morphisjustfun/NASA_CHALLENGE_UTILS/blob/master/images/readME/HHS_Criteria.JPG)


Then we obtained the weights of the epidemiological variables through the correlations that they had between confirmedDeaths100k variable with data since February 7th, 2021 (the first day of report in Open Covid Peru) until the day of the user consult. Finally, with the weights of the variable and its risk scales we can obtain a general average of the epidemiological layer.

### Environmental layer
In the environmental layer we considered real-time data from Waqi website about PM2.5 (µg/m³) and PM10 (µg/m³). Due to the limited data, we considered an average value that would represent air quality in the whole country. Acording to our research there is a correlation between air quality and the probability to get covid19. In the model, if the air quality is poor, then there is high risk to get the virus. Contrary, if it is good there is low risk.

This layer, compared to the previous one, has a model based on past data (since January 25th, 2021 until the date of data adquisition) obtained from Open AQ repository. Again, the next step was to correlate the environmental variables with the *confirmedDeaths100k* variable to assign the respective weights. In this way, we finally obtained a constant layer of the model which will permit to evaluate the risk of the daily environmental variables reported by Waqi.

### Vaccine and Reinfectation Rate layer
In this layer we asked the user about his/her vaccination status and also if he/she had covid before. 

First, we obtained the probability of Covid 19 infection through the effectiveness vaccine report given by WHO. The resulting number depended on the number of administered doses and the brand of the vaccine. 

In Peru, there are only 3 brands of vaccines that are being administered: Pzifer, Astrazeneca and Sinopharm. In case the user does not remember which vaccined got, we calculate an average of the effectiveness. 

Second, we found out in a research that if a person got Covid 19 before the probability of reinfection decreased in 84 \%. Therefore, we concluded that a person who got the Covid before would have only 16 \% of chance to get infected again. The result obtained in the third phase of calculus (including vaccine parameter) was multiply by 0.16 to get the final risk value for the user.  

## Limitations

- No meteorological data has been found for each peruvian region. An average between available zones (recognised by Senamhi.gob.pe) is given instead. In all Peru, there are just five measuring points, all of those in Lima. 

- Waqi only informs the actual environmental data and it does not show us past values. In addition to that, there few studied variables. 

## Main Insigths

 - The correlation between the variables are significant because they have an R number greater than 0.6.
 - The app obtains data in real time to create the dynamics epidemiological model of risk.
 - The app calculates the risk for the user at the moment with updated daily data
 - The app follows the rules of UX and UI design.
 - The app shows recommendations to prevent the Covid 19 depending on the user's risk scale.
 - The app pretends to be translated to quechua, aymara (the languages with the most speakers in the country) and English (a global language for tourists).
 - The use of React Native allows us to be compatible with major mobile platforms such as Android and iOS.
 - In the future the app can be replicated in other countries.

## Main Resources

### Methodology: Design Thinking
Mock up:
- Figma
- Miro
- Google Slides
  
### Frontend

- React Native
- Typescript

[Frontend](https://github.com/morphisjustfun/Nasa-challenge-Frontend)


### Backend

- Flask

[Backend](https://github.com/morphisjustfun/Nasa-challenge-Backend)


## Technique Requirements
### Libraries used
1. Pandas & NumPy 
2. Matplotlib & Seaborn
3. http.client
4. json
5. datetime
6. statistics
7. sklearn.linear_model
8. sklearn.metrics
9. sklearn.preprocessing
10. sklearn.model_selection
11. sklearn

### Install Libraries

In order to install the libraries
```console
foo@bar:~$ pip3 install -r requirements.txt
```

Now you are ready to start

## Project Demo

Our Website and MVP (Apk for download)
NASAFancy jupyter notebook

## Dataset

OpenCovid-Perú (Database of covid-19 status in peru)
waqi.info (World Air Pollution: Real-time Air Quality Index)
OpenAQ (https://openaq.org/#/)

## Resources

E. E. Félix-Arellano, A. Schilmann, M. Hurtado-Díaz, J. L. Texcalac-Sangrador, and H. Riojas-Rodríguez, “Revisión rápida: contaminación del aire y morbimortalidad por Covid-19,” salud publica mex, vol. 62, no. 5, pp. 582–589, Sep. 2020.
M. V. Beusekon,  Apr 12 and 2021, “Previous COVID-19 may cut risk of reinfection 84%,” CIDRAP. https://www.cidrap.umn.edu/news-perspective/2021/04/previous-covid-19-may-cut-risk-reinfection-84 (accessed Oct. 02, 2021).
M. V. Beusekon, Jul 22 and 2021, “Study: 2 COVID vaccine doses much more effective than 1 against Delta,” CIDRAP. https://www.cidrap.umn.edu/news-perspective/2021/07/study-2-covid-vaccine-doses-much-more-effective-1-against-delta (accessed Oct. 02, 2021).
M. Rochabrun and R. Liu, “Peru study finds Sinopharm COVID vaccine 50.4% effective against infections,” Reuters, Aug. 13, 2021. Accessed: Oct. 02, 2021. [Online]. Available: https://www.reuters.com/world/americas/peru-study-finds-sinopharm-covid-vaccine-504-effective-against-infections-2021-08-13/
“Coronavirus: vacunas contra la COVID-19 en el Perú.” https://www.gob.pe/11571-coronavirus-vacunas-contra-la-covid-19-en-el-peru (accessed Oct. 02, 2021).
“Lo que debe saber sobre la vacuna BNT162b2 de Pfizer-BioNTech contra la COVID-19.” https://www.who.int/es/news-room/feature-stories/detail/who-can-take-the-pfizer-biontech-covid-19--vaccine (accessed Oct. 02, 2021).
“La vacuna de Oxford/AstraZeneca contra la COVID-19: lo que debe saber.” https://www.who.int/es/news-room/feature-stories/detail/the-oxford-astrazeneca-covid-19-vaccine-what-you-need-to-know (accessed Oct. 02, 2021).
“Todo lo que se debe saber sobre la vacuna de Sinopharm contra la COVID-19.” https://www.who.int/es/news-room/feature-stories/detail/the-sinopharm-covid-19-vaccine-what-you-need-to-know (accessed Oct. 02, 2021).

## Authors
Mario Rios
Noemi Ramirez
Cesar Otárola
Pedro Toledo
Jairo Narro
