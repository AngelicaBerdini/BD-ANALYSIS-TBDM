# BD#ANALYSIS

## Problem Description

The project aims to implement data analystics solutions. Starting from a public dataset, the prototype must implement several analysis (e.g. sentiment analysis) considering semistructured and unstructured data. For the implementation, students can choose distributed computing frameworks such as Apache Spark (or a similar framework like Apache Flink). Students can play with multiple dataset files to perform joins and correlation and build enriched data.
The results must be published on ElasticSearch on which they must be provide basic views on the sentiment.
The starting dataset is available at the following link:
https://www.kaggle.com/datasets/yelp-dataset/yelp-dataset/data

Project prototype should cover the following topics:
Read data from existing JSON files in a Data Analytics Framework
Execut analytics in Spark (e.g. sentiment analysis) in order to enrich the json data, perform joins, etc..
Publish the data on ElasticSearch
Show some dashboard about the sentiment analysis (using charts, maps, etc.)

The high level architecture can be defined as follows:
![alt text](https://github.com/AngelicaBerdini/BD-ANALYSIS-TBDM/blob/main/utils/images/SoftwareArchitecture.png?raw=true)

## Requirements

To execute the project the requirments are:

- Cloning the repository
- Downloading the _yelp dataset_ from [Kaggle](https://www.kaggle.com/datasets/yelp-dataset/yelp-dataset)
- Having installed [Docker Desktop](https://www.docker.com)

## Installation

First clone the repository with the command:

```bash
  git clone https://github.com/AngelicaBerdini/BD-ANALYSIS-TBDM.git
```

Launch the [`docker-compose`](docker-compose.yml) from inside the repository directory:

```bash
  cd BD-ANALYSIS-TBDM
  docker-compose up -d
```

(The first time the command is executed it will require sometime since it has to install all the images)

## Run Locally

Once the **Installation** is done, we can go the local instance of Jupyter Notebook by visiting the URL:

- http://localhost:8888/

Once inside, execute cell by cell the file:

- [ClusterPySparkNotebook.ipynb](notebooks/ClusterPySparkNotebook.ipynb)

Also, it is possible to check the execution of job by the nodes in the Spark Cluster by visiting the URL:

- http://localhost:9090/

## Authors

- [@BianchiLuca28](https://github.com/BianchiLuca28)
- [@AngelicaBerdini](https://github.com/AngelicaBerdini)
- [@MartiniMichele](https://github.com/MartiniMichele)
