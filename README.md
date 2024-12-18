# Brainwave_Matrix_Intern
For Brain-wave-Matrix AI/ML Internship

FAKE-NEWS-DETECTOR

Project Overview

Fake news has become a significant issue in today's digital age, where information spreads rapidly through various online platforms. This project leverages machine learning algorithms to automatically determine the authenticity of news articles, providing a valuable tool to combat misinformation. The project aims to develop a machine-learning model capable of identifying and classifying any news article as fake or not. The distribution of fake news can potentially have highly adverse effects on people and culture. This project involves building and training a model to classify news as fake news or not using a diverse dataset of news articles. We have used four techniques to determine the results of the model.

    Logistic Regression
    Decision Tree Classifier
    Gradient Boost Classifier
    Random Forest Classifier

The projects is for collecting, analyzing, and visualizing of fake news and the related dissemination on social media.

The minimalistic version of latest dataset provided in this repo (located in dataset folder) include following files:

    politifact_fake.csv - Samples related to fake news collected from PolitiFact
    politifact_real.csv - Samples related to real news collected from PolitiFact
    gossipcop_fake.csv - Samples related to fake news collected from GossipCop
    gossipcop_real.csv - Samples related to real news collected from GossipCop

Each of the above CSV files is comma separated file and have the following columns

    id - Unique identifider for each news
    url - Url of the article from web that published that news
    title - Title of the news article
    tweet_ids - Tweet ids of tweets sharing the news. This field is list of tweet ids separated by tab.

Installation
Requirements:

Data download scripts are writtern in python and requires python 3.6 + to run.

Script make use of keys from tweet_keys_file.json file located in code/resources folder. So the API keys needs to be updated in tweet_keys_file.json file. Provide the keys as array of JSON object with attributes app_key,app_secret,oauth_token,oauth_token_secret as mentioned in sample file.

Install all the libraries in requirements.txt using the following command

pip install -r requirements.txt

Usage

    Clone this repository to your local machine:

git clone https://github.com/eKidenge/Fake-News-Detector.git

###  Configuration:

Fake-News-Detector contains 2 datasets collected using ground truths from _Politifact_ and _Gossipcop_.  
    
The `config.json` can be used to configure and collect only certain parts of the dataset. Following attributes can be configured    
  
 - **num_process** - (default: 4) This attribute indicates the number of parallel processes used to collect data.    
 - **tweet_keys_file** - Provide the number of keys available configured in tweet_keys_file.txt file       
 - **data_collection_choice** - It is an array of choices of various parts of the dataset. Configure accordingly to download only certain parts of the dataset.       
   Available values are  
     {"news_source": "politifact", "label": "fake"},{"news_source": "politifact", "label":    "real"}, {"news_source": "gossipcop", "label": "fake"},{"news_source": "gossipcop", "label": "real"}  
  
 - **data_features_to_collect** - FakeNewsNet has multiple dimensions of data (News + Social). This configuration allows one to download desired dimension of the dataset. This is an array field and can take following values.  
	              
	 - **news_articles** : This option downloads the news articles for the dataset.  
     - **tweets** : This option downloads tweets objects posted sharing the news in Twitter. This makes use of Twitter API to download tweets.  
     - **retweets**: This option allows to download the retweets of the tweets provided in the dataset.  
     - **user_profile**: This option allows to download the user profile information of the users involved in tweets. To download user profiles, tweet objects need to be downloaded first in order to identify users involved in tweets.  
     - **user_timeline_tweets**: This option allows to download upto 200 recent tweets from the user timeline. To download user's recent tweets, tweet objects needs to be downloaded first in order to identify users involved in tweets.
     - **user_followers**: This option allows to download the user followers ids of the users involved in tweets. To download user followers ids, tweet objects need to be downloaded first in order to identify users involved in tweets.  
     - **user_following**: This option allows to download the user following ids of the users involved in tweets. To download user's following ids, tweet objects needs to be downloaded first in order to identify users involved in tweets.


## Running Code

Inorder to collect data set fast, code makes user of process parallelism and to synchronize twitter key limitations across mutiple python processes, a lightweight flask application is used as keys management server.
Execute the following commands inside `code` folder,

    nohup python -m resource_server.app &> keys_server.out&

The above command will start the flask server in port 5000 by default.

**Configurations should be done before proceeding to the next step !!**

Execute the following command to start data collection,

    nohup python main.py &> data_collection.out&

Logs are wittern in the same folder in a file named as `data_collection_<timestamp>.log` and can be used for debugging purposes.

The dataset will be downloaded in the directory provided in the `config.json` and progress can be monitored in `data_collection.out` file. 

### Dataset Structure
The downloaded dataset will have the following  folder structure,
```bash
├── gossipcop
│   ├── fake
│   │   ├── gossipcop-1
│   │	│	├── news content.json
│   │	│	├── tweets
│   │	│	│	├── 886941526458347521.json
│   │	│	│	├── 887096424105627648.json
│   │	│	│	└── ....		
│   │	│  	└── retweets
│   │	│		├── 887096424105627648.json
│   │	│		├── 887096424105627648.json
│   │	│		└── ....
│   │	└── ....			
│   └── real
│      ├── gossipcop-1
│      │	├── news content.json
│      │	├── tweets
│      │	└── retweets
│		└── ....		
├── politifact
│   ├── fake
│   │   ├── politifact-1
│   │   │	├── news content.json
│   │   │	├── tweets
│   │   │	└── retweets
│   │	└── ....		
│   │
│   └── real
│      ├── poliifact-2
│      │	├── news content.json
│      │	├── tweets
│      │	└── retweets
│      └── ....					
├── user_profiles
│		├── 374136824.json
│		├── 937649414600101889.json
│   		└── ....
├── user_timeline_tweets
│		├── 374136824.json
│		├── 937649414600101889.json
│	   	└── ....
└── user_followers
│		├── 374136824.json
│		├── 937649414600101889.json
│	   	└── ....
└──user_following
        	├── 374136824.json
		├── 937649414600101889.json
	   	└── ....

News Content

news content.json: This json includes all the meta information of the news articles collected using the provided news source URLs. This is a JSON object with attributes including:

    text is the text of the body of the news article.
    images is a list of the URLs of all the images in the news article web page.
    publish date indicate the date that news article is published.

Social Context

tweets folder: This folder contains all tweets related to the news sample. This contains the tweet objects of the all the tweet ids provided in the tweet_ids attribute of the dataset csv. All the files in this folder are named as <tweet_id>.json . Each <tweet_id>.json file is a JSON file with format mentioned in https://developer.twitter.com/en/docs/tweets/data-dictionary/overview/tweet-object.html.

retweets folder: This folder contains the retweets of the all tweets posted sharing a particular news article. This folder contains files named as <tweet_id>.json and it contains a array of the retweets for a particular tweets. Each object int the retweet array have format mentioned in https://developer.twitter.com/en/docs/tweets/post-and-engage/api-reference/get-statuses-retweets-id.

user_profiles folder: This folder contains all the user profiles of the users posting tweets related to all news articles. This same folder is used for both datasources ( Politifact and GossipCop). It contains files named as <user_id>.json and have JSON formated mentioned in https://developer.twitter.com/en/docs/tweets/data-dictionary/overview/user-object.html

user_timeline_tweets folder: This folder contains files representing the time line of tweets of users posting tweets related to fake and real news. All files in the folder are named as <user_id>.json and have JSON array of upto 200 recent tweets of the users. The files have format mentioned same as https://developer.twitter.com/en/docs/tweets/timelines/api-reference/get-statuses-user_timeline.html.

user_followers folder: This folder contains all the user followers ids of the users posting tweets related to all news articles. This same folder is used for both datasources ( Politifact and GossipCop). It contains files named as <user_id>.json and have JSON data with user_id and followers attributes.

user_following folder: This folder contains all the user following ids of the users posting tweets related to all news articles. This same folder is used for both datasources ( Politifact and GossipCop). It contains files named as <user_id>.json and have JSON data with user_id and following attributes.
References


