# Bias in Data

## <a name="toc"></a>Table of Contents

1. [Goal](#goal)
2. [Data Sources](#data-sources-used)
    1. [Data Schemas](#data-descriptionsschemas)
3. [Resources Used](#resources-used)
4. [Files Created](#files-created)
5. [Reproducibility](#reproducibility)
6. [License](#license)
7. [Writeup](#writeup)

## Goal

The goal of this project is to explore the concept of bias through data on Wikipedia articles - specifically, articles on political figures from a variety of countries.

## Data Sources Used

To create these tables, we will draw from two data sources:
  1. [The Wikipedia English Article Dataset (within the category "Category:Politicians by nationality")](https://figshare.com/articles/Untitled_Item/5513449)  
  2. [Population Data by Country](https://www.dropbox.com/s/5u7sy1xt7g0oi2c/WPDS_2018_data.csv?dl=0)  

### Data Descriptions/Schemas

#### The Wikipedia Dataset

| Column | Description |
|--------|-------------|
| page | The title of the page |
| country | The country of the political entity |
| rev_id | The revision ID |

#### The Population Dataset

| Column | Description |
|--------|-------------|
| Geography | The name of the geographical entity (not necessarily a country) |
| Population mid-2018 (millions) | Population of the geographical region in millions, as of 2018 |

#### Article Quality (Intermediate file)

| Column | Description |
|--------|-------------|
| rev_id | The revision ID |
| prediction | One of "B", "C", "Start", "Stub", "FA" or "GA" [(see below)](#ores) |

#### Output File

This is the final file used for analysis

| Column | Description |
|--------|-------------|
| country | Country name |
| hq_article_counts | The number of high quality articles for this country's politicians |
| all_article_counts | The number of total articles for this country's politicians |
| population | The population of this country, as of 2018 |
| hq_articles_ratio | The ratio of the number of high-quality articles to the total number of articles |
| all_articles_per_pop | The ratio of the total number of articles to the population |

## Resources Used

### Versions and Documentation
 * This analysis was prepared using Python 3.7 running in a Jupyter Notebook environment.  
 * Documentation for Python can be found [here](https://docs.python.org/3.5/)   
 * Documentation for Jupyter Notebook can be found [here](http://jupyter-notebook.readthedocs.io/en/latest/)   

### ORES
For predicting article quality, the [ORES API](https://www.mediawiki.org/wiki/ORES) was used. The prediction returned by ORES is in the form of probabilities, as well as an absolute prediction. An article may be predicted to have one of the following quality levels:
 * **FA** - Featured article
 * **GA** - Good article
 * **B** - B-class article
 * **C** - C-class article
 * **Start** - Start-class article
 * **Stub** - Stub-class article

We define "high-quality" as an article predicted to be either **FA** or **GA**.

A sample response from ORES looks like this:
```json
{
  "enwiki": {
    "models": {
      "wp10": {
        "version": "0.5.0"
      }
    },
    "scores": {
      "757539710": {
        "wp10": {
          "score": {
            "prediction": "Start",
            "probability": {
              "B": 0.0950995993086368,
              "C": 0.1709859524092081,
              "FA": 0.002534267983331672,
              "GA": 0.005731369423122624,
              "Start": 0.7091352495053856,
              "Stub": 0.01651356137031511
            }
          }
        }
      },
      "783381498": {
        "wp10": {
          "score": {
            "prediction": "Start",
            "probability": {
              "B": 0.020202281665235494,
              "C": 0.040498863202895134,
              "FA": 0.002648428776337411,
              "GA": 0.005101906528059532,
              "Start": 0.4793812253273645,
              "Stub": 0.452167294500108
            }
          }
        }
      }
    }
  }
}
```

### Python Packages
The following Python packages were used and their documentation can be found at the accompanying links:
 * [pandas](https://pandas.pydata.org/pandas-docs/stable/api.html): Python Data Analysis Library
 * [tqdm](https://github.com/tqdm/tqdm): Fast and Extensible Progress Bar
 * [requests](http://docs.python-requests.org/en/master/): HTTP for Humans

## Files Created

1. [`article_quality.csv`](#article-quality-intermediate-file) contains the predicted quality of each article.
2. [`article_quality_with_population.csv`](#output-file) contains the final data used for analysis

## Reproducibility

In order to reproduce the results in this notebook, follow the following steps:

1. Clone this repository:
```
git clone https://github.com/havanagrawal/data-512-a2.git
```
2. (Optional) Create and activate a virtual environment using `virtualenv`:
```
virtualenv hcds-a2
. hcds-a2/bin/activate.sh
```
3. Install external libraries using pip (or conda)
```
pip3 install pandas requests tqdm
```
4. Start the Jupyter notebook:
```
jupyter notebook
```
5. To observe the exact same results as this notebook, simply rerun it in Jupyter. To retrieve fresh predictions from ORES, delete (or rename) the [`article_quality.csv`](./data/article_quality.csv) file

## License

 * This assignment code is released under the [MIT License](./LICENSE).  
 * The Wikipedia English language articles data source is released under the CC-BY-SA 4.0 license.  
 * The population data is released under the ??? license.  

## Writeup

### What I Learned

#### Documentation and Reproducibility Are Hard
I often find myself complaining about how most open-source projects that I use (or try to) have scarce levels of documentation, and that they are hardly understandable, let alone reproducible. I now realize how hard it is to provide just enough information for someone else to have all the context that I have in order to replicate my work. When I put on my "new person to this repo" hat, I find my own work rife with holes and gaps. It is commendable that projects that are more complex by several orders of magnitude are adopted so heavily, and that the community drives the quality of the repository.

#### Accuracy is not the End Goal

For the short period of four years that I have been dabbling in machine learning, I have always considered a numeric metric to be the goal; 99% is _always_ better than 95%. This assignment has taught me that critically analyzing the algorithm at hand, investigating the sources of bias, and providing context to the results are equally, if not more, important. I relate back to the phrase "the numbers speak for themselves", and think to myself "Well I can change that."

### What I Suspected (and Validated)

1. An obvious but important thing to note is that the source of these data is from the *English* Wikipedia pages. One might already suspect a bias in article quality due to this; articles about local politicians might be far richer in pages in their native language, than in English. Alternatively, if certain languages are not supported by Wikipedia, then pages relating to those countries, regardless of category, can be expected to be poorer in quality.
2. With the metric that we are trying to measure, the population might be a stronger factor than the number of articles for that country. The number of articles vary from 1 to a few thousand (max-min ratio of 10<sup>3</sup>), whereas the population varies from 10<sup>4</sup> to 10<sup>9</sup> (max-min ratio of 10<sup>5</sup>). This, in my opinion, is a biased metric to measure; a country with twice the population does not necessarily have twice the number of politicians, let alone pages about them.

### What I Found

1. As suspected, the highest ranked countries for the total number of articles per population overlap strongly with the least populated countries (8 out of 10 in the former are in the latter).
2. From the ORES Wiki:
 > The wp10 model bases its predictions on __structural characteristics__ of the article. E.g. How many sections are there? Is there an infobox? How many references? And do the references use a {{cite}} template? The wp10 model __doesn't evaluate the quality of the writing__ or whether or not there's a tone problem (e.g. a point of view being pushed). However, many of the structural characteristics of articles __seem to correlate__ strongly with good writing and tone, so the models work very well in practice.

The way the ORES model evaluates the quality of the article itself appears to be biased towards the structure of the article than the content. In contrast, the original WP10 [article assessment](https://en.wikipedia.org/wiki/Wikipedia:Content_assessment#Grades) performed by humans has very strongly worded and thoughtful criteria to attain a certain quality level.
