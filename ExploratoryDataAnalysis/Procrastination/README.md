## MSDS Procrastination
"Students are biologically and socially predisposed to put off until tomorrow what we should do today, says Dr. Piers Steel, author of The Procrastination Equation (Knezevic 2012). Because of this effect of procrastination on education, Dr. Daniel Engels, the head of the MSDS program at Southern Methodist University, reached out to us to try and determine which kind of people are procrastinating and where are they coming from?  To start, we conducted a multi-nation survey to determine the procrastination level of 4264 individuals by giving them 3 procrastination questionnaires the Decisional Procrastination Scale, the AIP, and the General Procrastination Scale.  Along with these measures of procrastination, we asked all participants to rate both how much they view themselves as a procrastinator and how much they feel others think they procrastinate. We also collected general information about the participants, such as their age, size of family, education level, and other demographic information. After we collected all of this information, we realized that we received a very diverse group of participants in terms of where they are from. We decided that we wanted to see if there was a relationship between country of residence and procrastination level. One way of doing this is looking at the Human Development Index (HDI) for the countries included in the survey. To get this data, we scraped the HDI information from Wikipedia (https://en.wikipedia.org/wiki/List_of_countries_by_Human_Development_Index#Complete_list_of_countries). Along with the indexes, we also added the category of HDI (Low, Medium, High, or Very High) for each country into our original data set.  
  
Now, with our new, complete data set, we discussed with Dr. Engels what information he was looking to get out of analysis, so we generated the following questions to start with:  
* What are the top 15 nations in average procrastination scores? (In terms of DP, AIP, and GP)  
* How many nations, if any, show up in both of these categories?  
* Is there a relationship between Age and Income?  If so, what are some potential reasons for this?  
* Is there a relationship between Life Satisfaction and HDI?  

We found that Qatar, Panama, Sri Lanka, Austria, Ecuador, Slovenia, and Portugal all are included in the top procrastination scores when looking at both the Decisional Procrastination and General Procrastination Scales. This tells us that these are the countries that are most likely to procrastinate, based on these two scales. 

After analysis, we found that when looking at the association of age when looking at annual income, although the relationship is significant (p-value <.0001 from an ANOVA f-test), only 19.68% of the variance in income can be explained by age. When looking at the association of HDI with life satifcation, there is a relationship (p-value = .0113) but only .14% of the variance in life satifaction can be explained by life satisfaction. Looking at both of these, we know that there are relationships, but in order to determine either annual income or life satisfaction, we would need to look at the effect other variables have in conjuction with age and HDI respectively. 

From these results, Dr. Engels is hoping to change the marketing of the program towards individuals who are less likely to procrastinate or towards countries that are less likely to procrastinate. That being said, everyone has a tendency to procrastinate, so it may be wise to determine a way to focus on helping people not procrastinate rather than targeting non-procrastinators.  

Although this client for this analysis was the MSDS program at Southern Methodist University, both the data set and the analysis are global and can be applied to a variety of businesses.  

## Repository Breakdown  
In order to make this analysis as reproducible as possible,we tried to include as much of the code as we can, as well as sufficient information on why and how we got our results, so that anyone can look at our analysis using additional data.  

Within the root directory, you can find both the Rmarkdown file and the Markdown file which include the full analysis of our data.  
You can also find a folder with all of the graphs that are also found in the markdown file. This file can be used as a quick visual reference.  

References:  
* `CaseStudy2.pdf`: a specific outline of how we proceeded through the project  
* `Checklist.txt`: a general outline of how we split up the tasks  

Analysis:  
* `.RHistory`: contains the raw code used for analysis  
* `Analysis.R`: contains more specific code used for analysis  
* `Codebook_Procrastination.txt`: contains the variable information for our main analysis  
* `MakeFile1.R`: the code we used to scrape more information from the web  
* `MakeFile2.R`: the code we used to merge and clean our data in order to make it tidy and ready for analysis    
* `Data`: a folder with the data sets used  

Data:  
* `.Rhistory`: contains the raw code used for analysis  
* `HumanDevelopmentIndex.csv`:  contains information regarding the Human Development Index (HDI)  
* `Procrastination.csv`: the raw data  
* `Procrastination2.csv`: the tidied data (including HDI information)  
* `AdultProcrastination.csv`: contains information from the top 15 countries in terms of procrastination  
* `Occupation.csv`: contains the csv with all of the occupations that were input  
  
## References  
Knezevic, Milana. "Procrastination: a student's worst enemy?", theguardian.com. 2012. https://www.theguardian.com/education/mortarboard/2012/may/09/students-procrastinating-exams.  

### Contact Information  
##### If you have any questions regarding our code or analysis, please do not hesitate to reach out to either of us. 
Casey Martinez:
* Phone:  512-993-9089
* E-mail:  casianom@smu.edu

Lizzy Sterling:  
* Phone: 864-704-5739  
* E-mail: lsterling@smu.edu  

