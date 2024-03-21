# BD#ANALYSIS

## Problem Description

The project aims to implement data analysis solutions. Starting from a public dataset, the prototype must implement several analyses (e.g. sentiment analysis) considering semistructured and unstructured data. For the implementation, students can choose distributed computing frameworks such as Apache Spark (or a similar framework like Apache Flink). Students can play with multiple dataset files to perform joins and correlation and build enriched data.
The results must be published on ElasticSearch on which they must provide basic views on the sentiment.
The starting dataset is available at the following link:
https://www.kaggle.com/datasets/yelp-dataset/yelp-dataset/data

The project prototype should cover the following topics:
Read data from existing JSON files in a Data Analytics Framework
Execute analytics in Spark (e.g. sentiment analysis) in order to enrich the JSON data, perform joins, etc...
Publish the data on ElasticSearch
Show some dashboards about the sentiment analysis (using charts, maps, etc.)

The high-level architecture can be defined as follows:
![alt text](https://github.com/AngelicaBerdini/BD-ANALYSIS-TBDM/blob/main/utils/images/SoftwareArchitecture.png?raw=true)

## Architecture
The following is the software architecture that is designed to set the cluster of nodes and interact with it through Jupyter Notebook and a stack made of Elasticsearch + Kibana:
![alt text](https://github.com/AngelicaBerdini/BD-ANALYSIS-TBDM/blob/main/utils/images/ProjectArchitecture.png?raw=true)

__ATTENTION__:
Unfortunately, to run this complex architecture is needed a quite expensive computational power. Therefore keep in mind that some related problems are related to reaching the maximum available resources on your local machine.

## Requirements

To execute the project the requirements are:

- Cloning the repository
- Downloading the _yelp dataset_ from [Kaggle](https://www.kaggle.com/datasets/yelp-dataset/yelp-dataset)
- Having installed [Docker Desktop](https://www.docker.com)

## Installation

First, clone the repository with the command:

```bash
  git clone https://github.com/AngelicaBerdini/BD-ANALYSIS-TBDM.git
```
Extract the downloaded datasets from Kaggle under the directory: 
```bash
  ./data
```
Launch the [`docker-compose`](docker-compose.yml) from inside the repository directory:

```bash
  cd BD-ANALYSIS-TBDM
  docker-compose up -d
```

(The first time the command is executed it will require some time since it has to install all the images)

## Run Locally

Once the [Installation](https://github.com/AngelicaBerdini/BD-ANALYSIS-TBDM/tree/main?tab=readme-ov-file#installation) is done, we can go to the local instance of Jupyter Notebook by visiting the URL:

- http://localhost:8888/

Once inside, execute cell by cell the file:
- [ClusterPySparkNotebook.ipynb](notebooks/ClusterPySparkNotebook.ipynb)

Also, it is possible to check the execution of the job by the nodes in the Spark Cluster by visiting the URL:
- http://localhost:8080/

Another important endpoint is the one needed to interact with the Elasticsearch instance:
- http://localhost:9200/

And respectively, the one needed to access the Kibana interface:
- http://localhost:5601/

Lastly, to stop the containers, execute the command:
```bash
  docker-compose down
```



## Project Structure
Regarding the structure of the project and the respective repository, the whole work is structured behind the 2 main notebooks [ClusterPySparkNotebook.ipynb](notebooks/ClusterPySparkNotebook.ipynb) and [PySparkNotebook.ipynb](notebooks/PySparkNotebook.ipynb).

The first one is the notebook that will be used in the context of the cluster execution.
In fact, once the cluster has been set, this notebook will be able to make use of the __distributed execution__ with the following code:
```bash
  appName = "Analysis Application with PySpark"

    #initialize the spark session
    spark = SparkSession.\
            builder.\
            appName(appName).\
            master("spark://spark-master:7077").\
            config("spark.executor.memory", "2048m").\
            config("spark.jars.packages", "org.elasticsearch:elasticsearch-spark-30_2.12:8.7.1").\
            config("spark.es.nodes","elasticsearch").\
            config("spark.es.port","9200").\
            config("spark.es.nodes.wan.only", "true").\
            getOrCreate()

    sc = spark.sparkContext
    sqlContext = SQLContext(sc)
```
In this sample code, the Jupyter Notebook instance will first connect to the master of the Spark standalone cluster, and then will download the Maven library and set the configurations to then make requests to Elasticsearch, in order to store data.

The second notebook instead can be used in the case of a Colab execution, in order to make use of the Cloud provider computational power to make tests and much more. Though, keep in mind that in this case the execution of PySpark would happen in a single node, while in the first case it would be a distributed environment.

Both notebooks are structured following the typical data science pipeline:
- Loading of the datasets;
- Exploratory Data Analysis (EDA): which is the process of analyzing and visualizing data to understand its underlying patterns and relationships. EDA helps in gaining insights into the dataset, identifying outliers, detecting correlations between variables, and selecting relevant features for modeling;
- Data pre-processing: which involves cleaning, transforming, and preparing raw data for analysis. It includes tasks like handling missing values, encoding categorical variables, scaling numerical features, and splitting the data into training and testing sets.
- Models development.

Regarding this last part, we approached this topic with many different approaches, in order to gain insight on the the dataset.

### Sentiment Analysis with trained model
The first thing we tried to do was to train a model (the best performing one was the RandomForest) using the text of the reviews and the number of stars.
More in particular, the transformations we applied regarding the stars of the reviews was considering:
- stars >= 4: the comment is positive;
- stars == 3: the comment is neutral;
- stars <= 2: the comment is negative.
Such model didn't perform that well, therefore we moved to the second model.

### Sentiment Analysis using BERT
In this second case, we considered instead a much more precise model from HuggingFace.

By applying such model on the text and confronting the "transformed stars" and the result of this model, we found out that the reviews, and much more the users, weren't that reliable.
This would also explain the bad performances of the Random Forest trying to build a sentiment analysis model.

Therefore, in the notebooks is also mentioned a work related to the evaluation of the reliability of the users based on their posted reviews (whether they were the same as the prediction of the model or not).

### ALS Recommandation System
In the context of the reviews, we also started an initial job on developing a recommandation system using the Alternating Least Squares (ALS) algorithm.

This simple algorithm just considers the stars of the reviews of certain businesses and then recommend to new users a new reusturant on which they might go.

This algorithm was trained only considering the city _'Cherry Hill'_, since it would be useless to consider all possible reusturants. Also, it would be too computational demanding to apply it as such.

### Models
Regarding the directory [models](models), in here can be found all the models that were stated above and then saved to save time in the next executions.
For this reason, in the notebooks, every time it is written:
```bash
  model.write().overwrite().save("models/...")
```
The model is saved to be then loaded with the `load` instruction.

Note that here the models are divided by _"cluster"_ and _"colab"_ execution, since the 2 models are indeed completely different and won't work on each other contexts.

### Docker-Compose
Lastly, talking about the `docker-compose` this is structured in 6 services as stated before:
- Jupyter Notebook instance
- Spark Node Master
- Spark Worker 1
- Spark Worker 2
- Elasticsearch instance
- Kibana instance

Also, each of them has their respective volumes in order to read notebooks, models, and datasets.
There are also 2 Dockerfiles to better structure the docker-compose and to avoid overcrowding it.

## Authors

- Luca Bianchi - [@BianchiLuca28](https://github.com/BianchiLuca28)
- Angelica Berdini - [@AngelicaBerdini](https://github.com/AngelicaBerdini)
- Michele Martini - [@MartiniMichele](https://github.com/MartiniMichele)
