# COVID-19 Risk Assesment for Knox County
COVID-19_School_Risk.py allows you to view the latest Knox County COVID-19 data, as well as calculate the expected number of people in a 
group or school of a given size that would have COVID-19 and be asymptomatic/presymptomatic. Alternatively, you can use the daily_risk.csv to search up the expected number of people in a group of a given size that would have COVID-19 and be asymptomatic/presymptomatic without using the program. daily_school_risk_huge.csv can also be used to apply the West High School Model to a school of a different size, but this file is big and it would have to be viewed in Excel (it also would be a pain to search through to the right combination of teachers and students).
This project scrapes its data from the Knox County Health Department at: https://covid.knoxcountytn.gov/case-count.html at the time the program is run.

### The Data Logger is not Intended for Use 
* Right now it has personalized paths (on my PC it is located in the directory above this repository), but I added it in the interest of transparency. 
* For automatic updates, I use a seperate batch file, which runs the data logger and pushes to this Github repository automatically at 4pm (using task scheduler).

###  Assumption Basis:
* As of July 10 (2020), in the CDC's 5 Pandemic Scenarios (https://www.cdc.gov/coronavirus/2019-ncov/hcp/planning-scenarios.html), they describe 10% of cases being asymptomatic or presymptomatic carriers as their lower-bound estimate, 40% as their best estimate (this has since been updated to 30% as of 8/30/21; I have not updated the program to refelct this fact), and 70% as their upper-bound estimate. 
* Georgia Tech's COVID-19 Event Risk Assessment page (https://covid19risk.biosci.gatech.edu/) made around March 13th assumes that there are about 10x as many infections as reported cases. During the week of March 13th, 69,383 total tests were preformed in the US; during the week of August 8th, 256,042 total tests were preformed in the US (https://www.cdc.gov/coronavirus/2019-ncov/covid-data/covidview/08142020/public-health-lab.html). Assuming that the ascertainment bias (proportion of infections that are not reported as cases) decreases proportionally to the growth in testing, we would expect a reduction in ascertainment bias by a factor of 3.69 (256,042/69,383). As such, we will use GA Tech's assumption of an ascertainment bias of 10 and scale it down to 2.7 (10/3.69).

### Model Assumptions:
1) This program makes six different predictions, assuming that 10% (the given minium), 21.2% (Q1), 32.4% (Median), 43.7% (Q3), 70.0% (the given maximum), and 40% (the best given estimate) of COVID infections are asymptomatic or presymptomatic carriers (see above).
2) The model assumes an ascertainment bias (proportion of infections that are not reported as cases) of 2.7 (see above).
3) The model assumes that symptomatic carriers are self-isolating until being tested, and are thus of no concern (hopefully this is true, but it probably isn't entirely).
4) The model assumes that the group in question has the same prevalence of COVID as all other groups in Knox County (this is not the reality).
5) The model assumes that asymptomatic or presymptomatic carriers don't know they have the virus (this isn't necessarily true and there might be some included in the active case counts).

### The Model:
* Total Infections = Active Cases \* 2.7 (The Ascertainment Bias: See Assumption Basis)
* Total Asymptomatic/Presymptomatic Infections = Total Infections \* Assumed Asymptomatic/Presymptomatic Percentage (e.g. 0.40) 
* Predicted Asymptomatic/Presymptomatic Infections in a Group of Size N =  Total Asymptomatic/Presymptomatic Infections / Knox County's Population \* N

### The West High School Model Rational
* The West High School model was designed to capture the fact that as of 8/17/2020 people under the age of 18 have had ~60 cases per 10,000 minors, while adults 18-64 have had ~150 cases per 10,000 18-64 year-old adults. As such, this model should be more accurate for West High School with its large number of minors. This model will also--at least initially--predict much fewer asymp. / presymp. infections than the normal one. This might change as schools reopen and the minor cases go up. This model is also used for school predictions of sizes other than West in both the program and the daily_school_risk_huge.csv file.

### Extra West Model Assumptions
6) The prediction assumes that the population of West High School is 1090 people (1443 students + 82 teaching staff - 435 students that chose the online option). The student and teaching staff numbers are for the 2018-2019 school year from https://nces.ed.gov/ccd/schoolsearch/school_detail.asp?Search=1&DistrictID=4702220&SchoolPageNum=6&ID=470222000822.
7) The West HS model does NOT assume that the group in question has the same prevalence of COVID as all other groups in Knox County.
8) The West HS model assumes that the only factor that affects a group's prevalence of COVID is age (this is obviously not true, but I think that--of the variables we have--this is most closely tied to behavior and therefore likelihood of having COVID-19).
9) The West HS model assumes that all teachers are between the ages of 18-64 (probably some outliers here).
10) The West HS model assumes that all students are between the ages of 0-17 (basically true).
11) Other than assumption #4, the West HS model assumes all assumptions previous assumptions (#1-3 and #5).

### West HS Model:
* Total Infections = Active Cases \* 2.7 (The Ascertainment Bias: See Assumption Basis)
* Total Asymptomatic/Presymptomatic Infections = Total Infections \* Assumed Asymptomatic/Presymptomatic Percentage (e.g. 0.40) 
* Student Infection Likelihood = Total 0-17 Year-old Cases per 10,000 Minors / Total Knox County Cases per 10,000 People
* Predicted Student Asymptomatic/Presymptomatic Infections = Total Asymptomatic/Presymptomatic Infections / Knox County's Population \* \# of In-Person Students (1008) * Student Infection Likelihood
* Teacher Infection Likelihood = (Total 18-44 Year-old Cases per 10,000 in Group + Total 45-64 Year-old Cases per 10,000 in Group) / (2\*Total Knox County Cases per 10,000 People)
* Predicted Teacher Asymptomatic/Presymptomatic Infections = Total Asymptomatic/Presymptomatic Infections / Knox County's Population \* \# of Teachers (82) * Teacher Infection Likelihood
* Predicted West Asymptomatic/Presymptomatic Infections = Predicted Teacher Asymptomatic/Presymptomatic Infections + Predicted Student Asymptomatic/Presymptomatic Infections
* \* Note: The West HS Model is also used if you respond that your group is a school, or if you search through the daily_school_risk_huge.csv file for a prediction.

### How to Use Without Installing
* daily_risk.csv, daily_risk_lite.csv, and daily_risk_mobile.csv are the simplest ways to get a result, as they does not require downloading the program and installing Python. Simpily download or click on one of daily_risk.csv files\* and search for the group size with command/control F and typing in the group size the "Best Asymp/Presymp Estimate" column shows the best estimate for the expected number of people in a group of the given size on the left that would have COVID-19 and be asymptomatic/presymptomatic. These tables are updated everyday automatically at 4pm. The daily_school_risk_huge.csv also can be used to apply the West HS Model to different school sizes, but it is very large and will have to be opened in Excel or LibreOffice Calc.
* \*Note: daily_risk_lite.csv has fewer group sizes (up to 2999) for slow computers and people that aren't planning for thousands of people, and daily_risk_mobile.csv has even fewer group sizes (up to 1099) for people on phones.

### Historical Predictions:
* I have set up a script that runs every day at 4pm and updates the historical_predictions.csv file with the minimum, maximum, and best prediction of expected county and West High School asymptomatic COVID cases. This is to help place any day's results in context.

### Historical Data:
* All folders that start with "hist" have CSVs of every peice of data avaiable from Knox County's over time (named by their date). This data is updated automatically at 4pm.

### To Do:
- [ ] Change 40% asymp. best estimate to 30% + recalculate all previous values
- [ ] Save historical Knox County School numbers from: https://datastudio.google.com/u/0/reporting/3d460c9b-1a41-4566-88b0-9264ae8c2965/page/aEpcB?feature=opengraph
- [ ] Add new model based around these Knox County School-only numbers.
- [ ] I just found out that the actual number of students and staff at West High School is listed on the bottom of the main page on West's website. As such, I will update the program, as well as recalculate the historical predictions to reflect these new numbers. These new numbers are larger than the previous numbers, and, as such, will increase the expected risk.
- [ ] Add expected number of asymp./presymp. infections for a class of a given size at West (and for the program, for any school size).
- [ ] Add expected number of asymp./presymp. infections for staff and students seperately.
- [ ] Make function to update historical predicions based on whatever the current model is.

### To Install on Windows: 
1) Download and install the latest version of Python (https://www.python.org/downloads/), checking the "Add to PATH" option on install. This WILL NOT WORK if you have not checked the "Add to PATH" option, which is not checked by default.
2) Download COVID-19_School_Risk.py
3) Press the Windows key and type "cmd" in the Windows search box. Press enter to open Command Prompt.
4) Type '`pip install requests`' and press enter. 
5) Press the "y" key when prompted.
6) You can now check up on the data and enter any group size you want by simpily double clicking on the COVID-19_School_Risk.py file (if this asks for an application to open it with select Python3.8).

### To Install on Mac OS:
1) Download and install the latest version of Python (https://www.python.org/downloads/), checking the "Add to PATH" option on install. This WILL NOT WORK if you have not checked the "Add to PATH" option, which is not checked by default.
2) Download COVID-19_School_Risk.py
3) Press Command + Space and search for "terminal". Press enter to open it.
4) Type '`pip install requests`' and press enter. 
5) Press the "y" key when prompted.
6) You can now check up on the data and enter any group size you want by simpily double clicking on the COVID-19_School_Risk.py file (if this asks for an application to open it with select Python3.8).

* Note: If you plan to use Python for anything else, I would recommend installing anaconda to manage your Python packages using conda environments.

### License:
Copyright 2020 Dylan Johnson

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
