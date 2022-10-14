# Homework2: Considering Bias in Data

## About the project
The goal of this project is to explore the concept of bias in data using Wikipedia articles. In this project, articles on political figures from different countries will be studied. A dataset of Wikipedia articles with a dataset of country populations will be combined, and use a machine learning service called ORES to estimate the quality of each article.


## Data Source
There are two main sources of input data:
- politicians_by_country.SEPT.2022.csv was generated from crawling [The Wikipedia Category:Politicians by nationality](https://en.wikipedia.org/wiki/Category:Politicians_by_nationality)
- population_by_country_2022.csv drawn from [the world population data sheet](https://www.prb.org/international/indicator/population/table) published by the Population Reference Bureau

In addition, two API are used :
- [Wikimedia REST API](https://www.mediawiki.org/wiki/Wikimedia_REST_API) for article page view information
- [ORES](https://www.mediawiki.org/wiki/ORES) for article quality prediction

ORES predict articles to the following levels:
 * **FA** - Featured article
 * **GA** - Good article
 * **B** - B-class article
 * **C** - C-class article
 * **Start** - Start-class article
 * **Stub** - Stub-class article

## License
- Wikimedia Foundation REST API [Terms of Use](https://foundation.wikimedia.org/wiki/Terms_of_Use/en) and [Privacy Policy](https://foundation.wikimedia.org/wiki/Privacy_policy)
- Wikimedia ORES API [MIT License](https://github.com/wikimedia/ores/blob/master/LICENSE)

## Intermediate Data
There are three intermediate CSV data file:
[revid_df.csv](/IntermediateData/revid_df.csv): Request Result from Wikimedia Foundation REST API, containing article title and Revision id
[ores_df.csv](/IntermediateData/ores_df.csv): Request Result from ORES API, containing article title and ORES Prediction Score
[region_population.csv](/IntermediateData/region_population.csv): Subset of population_by_country_2022.csv, containing "Lowest hierarchy" Region Name and Population

variables and description:
- article_title: article name
- revid: Revision id
- ores_prediction: ORES Prediction Score (FA, GA, B, C, Start, Stub)
- region: "Lowest hierarchy" Region Name
- population: Population (in millions)

## Data Output
There are two main outputs:
[wp_countries-no_match.txt](/DataOutput/wp_countries-no_match.txt): Country name list which there is no matched article (politicians) in this country
[wp_politicians_by_country.csv](/DataOutput/wp_politicians_by_country.csv): Combination of country information and article quality information

There is one additional output:
[article_no_revid.txt](/DataOutput/article_no_revid.txt): article name list which ORES Score/Revision id cannot be found

variables and description:
- article_title: article name
- revision_id: Revision id
- article_quality: ORES Prediction Score (FA, GA, B, C, Start, Stub)
- country: country name
- region: "Lowest hierarchy" region
- population: population of country

## known issues or special considerations
- There are some duplicated url for articles with same name but from different countries. See [Notebook](/data-512-homework_2.ipynb) Section 1.2 for detailed considerations.
- There are some articles from "Korean" which cannot be matched to country names. See [Notebook](/data-512-homework_2.ipynb) Section 2.3 for detailed considerations.
- There are some countries with fewer than 1 million population are displayed as 0.0. See [Notebook](/data-512-homework_2.ipynb) Section 2.3 for detailed considerations.
- In analysis, articles with "FA" (featured article) or "GA" (good article) ORES Score are considered "High Quality Article".

## Research Implications

### Self Reflection

#### Bias can easily occurs when processing Data
From this project, I realized that when processing data, bias can easily occur from almost every step, which leads to misleading analysis. There are plenty of decisions that should be made during the data processing process: Which data source to use? How to deal with duplicated information? How to find mismatched information? How to handle missing data? These decisions must be made after careful consideration and investigations. Researches and investigations are necessary to prevent biases in the data.

### Study Questions

#### What biases did you expect to find in the data (before you started working with it), and why?
Before starting working with the data, I expect bias would be found in the article quality rating. Since the API is providing article quality prediction based on an English Wikipedia page, the politicians from non-English speaking countries will gain a relatively lower score even if the Wikipedia page in their native language might be informative.
Besides, the politicians_by_country list might not be comprehensive. Therefore, I would expect there might be a sampling bias: that not all politicians from different countries have an equal chance to be included. 

#### What (potential) sources of bias did you discover in the course of your data processing and analysis?
During the process analysis and process of the data, I find that there are some biases in the input data. The data is not sufficient for conducting meaningful analysis: politicians' information is not included for a great number of countries. More importantly, during data processing, I find there are problems of data duplication, data missing, and data mismatch, which all probably leads to biased analysis.
Another source for bias is the ORES prediction system, based on the documentation, it evaluates the quality of the article more biased towards the structure than the actual content itself.

#### How might a researcher supplement or transform this dataset to potentially correct for the limitations/biases you observed?
To correct the limitations, I think collecting a more comprehensive list of politicians' page information is necessary. The mismatched data, out-of-date data, and missing data also should be fixed in order to avoid biases. The evaluation should be based on the politicians' speaking language Wiki page. More importantly, a more powerful article quality prediction tool is needed for a more comprehensive evaluation of the article structure and article contents. 
