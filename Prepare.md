# Prepare

Date: June 3, 2024 → June 5, 2024
Status: Done

Sršen encourages you to use public data that explores smart device users’ daily habits. She points you to a specific data set:
● FitBit Fitness Tracker Data (CC0: Public Domain, dataset made available through Mobius): This Kaggle data set contains personal fitness tracker from thirty fitbit users. Thirty eligible Fitbit users consented to the submission of personal tracker data, including minute-level output for physical activity, heart rate, and sleep monitoring. It includes information about daily activity, steps, and heart rate that can be used to explore users’ habits.

Sršen tells you that this data set might have some limitations, and encourages you to consider adding another data to help address those limitations as you begin to work more with this data.

Now, prepare your data for analysis using the following Case Study Roadmap as a guide:
Case Study Roadmap - Prepare

Guiding questions
● Where is your data stored?
● How is the data organized? Is it in long or wide format?
● Are there issues with bias or credibility in this data? Does your data ROCCC?
● How are you addressing licensing, privacy, security, and accessibility?
● How did you verify the data’s integrity?
● How does it help you answer your question?
● Are there any problems with the data

## Notes

### datetime-timestamp format

One major issue getting the data from csv to big query was uploading the tables. The time column was one that the autodetect schema could not attribute as a timestamp or datetime due to incorrect formatting of the date or the time part of the data. 

The solution to this issue was to upload the time column as a string by writing a schema as follows

```markdown
Id:integer,
Time:string,
Value:integer
```

### Choosing BigQuery

The data was provided in a zip file, within the zip file are the excel files. Some of the files are too big to work with in excel. Using SQL would be a faster way of processing the data in addition to BigQuery being easy to export data from. Making deeper analysis with R or Python easier in the future.

### Naming tables

It is not efficient to rename tables in BigQuery. If possible name tables only once.

## Answering the guiding questions

Where is the data stored?

Google BigQuery

How is the data organized? Is it in long or wide format?

The data is organized in tables per the files provided. The tables are under a dataset. In this case long format data is preferred due to wide format data being very wide and not providing more clarity or ease of use about the data.

Are there issues with bias or credibility in this data? Does the data ROCCC?

According to the source of the data on kaggle. The data is generated from a survey via Amazon Mechanical Turk, a marketplace where tasks are outsourced to people that perform them virtually. For this dataset, 30 respondents provided their FitBit data.

Digging deeper to find the source of the data, the author on kaggle provides a link to the original upload of the dataset and the authors. With this in mind there is no reason to believe that the data has bias or lacks credibility, due to the crowdsourced nature. This makes the data reliable & original. The dataset is from 2016 which is 8 years old at the time of analysis, however the FitBit technology is still used at the time of writing, making it current. The dataset includes data recorded by minute as well as a variety of different health metrics, so it is comprehensive. Finally, the data is cited to the original upload.

How are you addressing licensing, privacy, security & accessibility?

The data has been made open source thus licensing is not an issue. Credit and citation will be given in the final report.

Regarding privacy the data has been anonymized, meaning that there are no identifying datapoints. For security & accessibility, choosing to store the data in BigQuery uses Google Cloud’s security infrastructure ensuring at the very least the data is only accessible by authorized accounts.

How did you identify the data’s integrity?

Amazon Mechanical Turk is a well known platform for crowdsourcing data, the people who provided the data were compensated by Mechanical Turk. Thus there is no reason to believe that the data provided has been artificially generated.

How does it help you answer your question?

The data has in-depth usage recorded, although it has a small sample size, meaning that the trends found are going to present more detailed insights that can reveal small habits about how people use their smart devices. 

Are there any problems with the data?

Yes, the data will not tell me anything about how popular the use of a certain feature or function is nor will it tell me the pain points people have with their smart wellness devices. I think finding data that is more general and have a wider sample and combining it with the existing data will yield more valuable insights.

Important: Research and find a source of pain points about FitBit smart watches. The data collected needs to be of the same class of product to align with the usage data currently available.

## Other data

Henriksen, André; Woldaregay, Ashenafi Zebene; Muzny, Miroslav; Hartvigsen, Gunnar; Hopstock, Laila Arnesdatter; Grimsgaard, Sameline, 2021, "Replication data for Using Fitness Trackers and Smartwatches to Measure Physical Activity in Research", [https://doi.org/10.18710/6ZWC9Z](https://doi.org/10.18710/6ZWC9Z), DataverseNO, V1

- Data comes from a research paper. It does not list user data, merely a collection of all the fitness trackers and smartwatches released up to 2017
- Its purpose is to provide context to the market that bellabeat is competing in
- Data collected from other online and offline datasets

https://www.kaggle.com/datasets/devsubhash/fitness-trackers-products-ecommerce

- Product reviews scrapped from flipkart, an indian ecommerce platform.
- Should provide context as to the features or qualities that the users prefer.
- Its purpose is to support any trends found
