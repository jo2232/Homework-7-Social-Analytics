
Observation #1: It is not as obvious in the scatter plot but the bar chart reveals that news on average across all outlets is negative.

Observation #2: Too many points end up on the neutral line and with a more complex sentiment analyzer some more detail could be gained from these points.

Observation #3: My expectations were for both plots to be even more negatively skewed as that seems to be what sells.


```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import json
import tweepy
import time
import seaborn as sns
import os
import datetime as dt

from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer
analyzer = SentimentIntensityAnalyzer()
```


```python
file_name = "../api_keys.json"
data = json.load(open(file_name))

consumer_key = data['twitter_consumer_key']
consumer_secret = data['twitter_consumer_secret']
access_token = data['twitter_access_token']
access_token_secret = data['twitter_access_token_secret']
```


```python
auth = tweepy.OAuthHandler(consumer_key, consumer_secret)
auth.set_access_token(access_token, access_token_secret)
api = tweepy.API(auth, parser=tweepy.parsers.JSONParser())
```


```python
target_terms = ["@cnnbrk", "@CBSNews", "@BBCWorld", "@FoxNews", "@nytimes"]
```


```python
#sentiment_df = ""
```


```python
compound_list = []
positive_list = []
neutral_list = []
negative_list = []
sentiments = []
```


```python
for news_handle in target_terms:
    public_tweets = api.user_timeline(news_handle, count=100, result_type="recent")
    counter = 1

    for tweet in public_tweets:
        
        compound = analyzer.polarity_scores(tweet["text"])["compound"]
        pos = analyzer.polarity_scores(tweet["text"])["pos"]
        neu = analyzer.polarity_scores(tweet["text"])["neu"]
        neg = analyzer.polarity_scores(tweet["text"])["neg"]
        tweets_ago = counter
    
        sentiments.append({"News Account": tweet["user"]["name"], "Date": tweet["created_at"], "Text": tweet['text'], "Compound": compound, "Positive": pos, "Negative": neu, "Neutral": neg, "Tweets Ago": tweets_ago})
        counter = counter + 1
```


```python
sentiments_df = pd.DataFrame.from_dict(sentiments)
sentiments_df.to_csv("News_Sentiments.csv", sep=',', encoding='utf-8')
sentiments_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Compound</th>
      <th>Date</th>
      <th>Negative</th>
      <th>Neutral</th>
      <th>News Account</th>
      <th>Positive</th>
      <th>Text</th>
      <th>Tweets Ago</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-0.4588</td>
      <td>Sat Feb 03 13:44:24 +0000 2018</td>
      <td>0.750</td>
      <td>0.175</td>
      <td>CNN Breaking News</td>
      <td>0.075</td>
      <td>Exclusive: CNN has caught North Korea violatin...</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-0.7506</td>
      <td>Sat Feb 03 13:42:31 +0000 2018</td>
      <td>0.684</td>
      <td>0.316</td>
      <td>CNN Breaking News</td>
      <td>0.000</td>
      <td>Police in Italy arrested a man suspected of op...</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-0.3818</td>
      <td>Sat Feb 03 08:27:54 +0000 2018</td>
      <td>0.885</td>
      <td>0.115</td>
      <td>CNN Breaking News</td>
      <td>0.000</td>
      <td>Lady Gaga has canceled the final 10 dates of h...</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-0.4939</td>
      <td>Sat Feb 03 07:32:04 +0000 2018</td>
      <td>0.849</td>
      <td>0.151</td>
      <td>CNN Breaking News</td>
      <td>0.000</td>
      <td>North Korea earned nearly $200 million from ex...</td>
      <td>4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.2960</td>
      <td>Sat Feb 03 07:22:32 +0000 2018</td>
      <td>0.913</td>
      <td>0.000</td>
      <td>CNN Breaking News</td>
      <td>0.087</td>
      <td>The man who says he sent out the false ballist...</td>
      <td>5</td>
    </tr>
    <tr>
      <th>5</th>
      <td>0.2960</td>
      <td>Sat Feb 03 03:09:09 +0000 2018</td>
      <td>0.763</td>
      <td>0.093</td>
      <td>CNN Breaking News</td>
      <td>0.144</td>
      <td>A White House spokesman says there will be "no...</td>
      <td>6</td>
    </tr>
    <tr>
      <th>6</th>
      <td>0.4767</td>
      <td>Sat Feb 03 01:15:05 +0000 2018</td>
      <td>0.846</td>
      <td>0.000</td>
      <td>CNN Breaking News</td>
      <td>0.154</td>
      <td>Rep. Nunes says the House Intelligence Committ...</td>
      <td>7</td>
    </tr>
    <tr>
      <th>7</th>
      <td>-0.7269</td>
      <td>Sat Feb 03 00:22:30 +0000 2018</td>
      <td>0.766</td>
      <td>0.234</td>
      <td>CNN Breaking News</td>
      <td>0.000</td>
      <td>A man who sold ammunition to the killer in las...</td>
      <td>8</td>
    </tr>
    <tr>
      <th>8</th>
      <td>-0.0031</td>
      <td>Fri Feb 02 23:07:05 +0000 2018</td>
      <td>0.657</td>
      <td>0.172</td>
      <td>CNN Breaking News</td>
      <td>0.171</td>
      <td>Trump threatens to cut off aid to countries th...</td>
      <td>9</td>
    </tr>
    <tr>
      <th>9</th>
      <td>0.0000</td>
      <td>Fri Feb 02 21:54:22 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>CNN Breaking News</td>
      <td>0.000</td>
      <td>RT @CNNPolitics: The US says Russia is "develo...</td>
      <td>10</td>
    </tr>
    <tr>
      <th>10</th>
      <td>0.1280</td>
      <td>Fri Feb 02 21:33:55 +0000 2018</td>
      <td>0.747</td>
      <td>0.116</td>
      <td>CNN Breaking News</td>
      <td>0.137</td>
      <td>RT @CNNMoney: U.S. stocks pummeled. The Dow fa...</td>
      <td>11</td>
    </tr>
    <tr>
      <th>11</th>
      <td>-0.9618</td>
      <td>Fri Feb 02 20:15:42 +0000 2018</td>
      <td>0.460</td>
      <td>0.540</td>
      <td>CNN Breaking News</td>
      <td>0.000</td>
      <td>Prostate cancer has surpassed breast cancer as...</td>
      <td>12</td>
    </tr>
    <tr>
      <th>12</th>
      <td>-0.3400</td>
      <td>Fri Feb 02 19:52:42 +0000 2018</td>
      <td>0.854</td>
      <td>0.146</td>
      <td>CNN Breaking News</td>
      <td>0.000</td>
      <td>President Trump won't say whether he's plannin...</td>
      <td>13</td>
    </tr>
    <tr>
      <th>13</th>
      <td>-0.4023</td>
      <td>Fri Feb 02 19:26:10 +0000 2018</td>
      <td>0.838</td>
      <td>0.162</td>
      <td>CNN Breaking News</td>
      <td>0.000</td>
      <td>The Dow has dropped more than 500 points Frida...</td>
      <td>14</td>
    </tr>
    <tr>
      <th>14</th>
      <td>0.0000</td>
      <td>Fri Feb 02 19:16:47 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>CNN Breaking News</td>
      <td>0.000</td>
      <td>Former FBI Director Comey tweets in response t...</td>
      <td>15</td>
    </tr>
    <tr>
      <th>15</th>
      <td>-0.4588</td>
      <td>Fri Feb 02 19:06:43 +0000 2018</td>
      <td>0.647</td>
      <td>0.230</td>
      <td>CNN Breaking News</td>
      <td>0.122</td>
      <td>Judge denies request by CNN and others to forc...</td>
      <td>16</td>
    </tr>
    <tr>
      <th>16</th>
      <td>-0.4215</td>
      <td>Fri Feb 02 18:59:42 +0000 2018</td>
      <td>0.865</td>
      <td>0.135</td>
      <td>CNN Breaking News</td>
      <td>0.000</td>
      <td>Polar bears are starving and could face extinc...</td>
      <td>17</td>
    </tr>
    <tr>
      <th>17</th>
      <td>0.0000</td>
      <td>Fri Feb 02 18:25:57 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>CNN Breaking News</td>
      <td>0.000</td>
      <td>RT @CNNPolitics: Democrats respond to the rele...</td>
      <td>18</td>
    </tr>
    <tr>
      <th>18</th>
      <td>0.4767</td>
      <td>Fri Feb 02 18:25:53 +0000 2018</td>
      <td>0.853</td>
      <td>0.000</td>
      <td>CNN Breaking News</td>
      <td>0.147</td>
      <td>RT @CNNPolitics: House Intelligence Committee ...</td>
      <td>19</td>
    </tr>
    <tr>
      <th>19</th>
      <td>-0.6901</td>
      <td>Fri Feb 02 17:50:27 +0000 2018</td>
      <td>0.659</td>
      <td>0.341</td>
      <td>CNN Breaking News</td>
      <td>0.000</td>
      <td>RT @CNNPolitics: Read the highly controversial...</td>
      <td>20</td>
    </tr>
    <tr>
      <th>20</th>
      <td>0.1779</td>
      <td>Fri Feb 02 17:32:52 +0000 2018</td>
      <td>0.645</td>
      <td>0.155</td>
      <td>CNN Breaking News</td>
      <td>0.200</td>
      <td>RT @CNNPolitics: The disputed GOP-Nunes intell...</td>
      <td>21</td>
    </tr>
    <tr>
      <th>21</th>
      <td>-0.2023</td>
      <td>Fri Feb 02 16:54:00 +0000 2018</td>
      <td>0.917</td>
      <td>0.083</td>
      <td>CNN Breaking News</td>
      <td>0.000</td>
      <td>RT @CNNPolitics: BREAKING: President Trump has...</td>
      <td>22</td>
    </tr>
    <tr>
      <th>22</th>
      <td>-0.5106</td>
      <td>Fri Feb 02 15:01:06 +0000 2018</td>
      <td>0.870</td>
      <td>0.130</td>
      <td>CNN Breaking News</td>
      <td>0.000</td>
      <td>The father of three daughters who were abused ...</td>
      <td>23</td>
    </tr>
    <tr>
      <th>23</th>
      <td>0.7717</td>
      <td>Fri Feb 02 14:35:32 +0000 2018</td>
      <td>0.688</td>
      <td>0.000</td>
      <td>CNN Breaking News</td>
      <td>0.312</td>
      <td>RT @CNNMoney: Dow drops 200 points, sinking be...</td>
      <td>24</td>
    </tr>
    <tr>
      <th>24</th>
      <td>0.3818</td>
      <td>Fri Feb 02 13:45:29 +0000 2018</td>
      <td>0.843</td>
      <td>0.000</td>
      <td>CNN Breaking News</td>
      <td>0.157</td>
      <td>RT @CNNMoney: Wage growth picked up as U.S. ad...</td>
      <td>25</td>
    </tr>
    <tr>
      <th>25</th>
      <td>-0.5994</td>
      <td>Fri Feb 02 13:40:10 +0000 2018</td>
      <td>0.837</td>
      <td>0.163</td>
      <td>CNN Breaking News</td>
      <td>0.000</td>
      <td>At least 90 migrants believed to have drowned ...</td>
      <td>26</td>
    </tr>
    <tr>
      <th>26</th>
      <td>0.0000</td>
      <td>Fri Feb 02 13:05:34 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>CNN Breaking News</td>
      <td>0.000</td>
      <td>Punxsutawney Phil sees his shadow during a Gro...</td>
      <td>27</td>
    </tr>
    <tr>
      <th>27</th>
      <td>0.5859</td>
      <td>Fri Feb 02 12:28:46 +0000 2018</td>
      <td>0.664</td>
      <td>0.094</td>
      <td>CNN Breaking News</td>
      <td>0.242</td>
      <td>RT @CNNPolitics: President Trump accuses FBI a...</td>
      <td>28</td>
    </tr>
    <tr>
      <th>28</th>
      <td>-0.4019</td>
      <td>Fri Feb 02 10:59:27 +0000 2018</td>
      <td>0.881</td>
      <td>0.119</td>
      <td>CNN Breaking News</td>
      <td>0.000</td>
      <td>Four people have been shot and 18 injured afte...</td>
      <td>29</td>
    </tr>
    <tr>
      <th>29</th>
      <td>-0.1531</td>
      <td>Fri Feb 02 06:46:07 +0000 2018</td>
      <td>0.733</td>
      <td>0.147</td>
      <td>CNN Breaking News</td>
      <td>0.121</td>
      <td>All of the more than 900 miners trapped in a S...</td>
      <td>30</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>470</th>
      <td>-0.2732</td>
      <td>Fri Feb 02 21:20:07 +0000 2018</td>
      <td>0.870</td>
      <td>0.130</td>
      <td>The New York Times</td>
      <td>0.000</td>
      <td>Salt Bae opened a steakhouse to New York City....</td>
      <td>71</td>
    </tr>
    <tr>
      <th>471</th>
      <td>0.0000</td>
      <td>Fri Feb 02 21:16:36 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>The New York Times</td>
      <td>0.000</td>
      <td>RT @amyfiscus: NEW: The FBI was a lot more for...</td>
      <td>72</td>
    </tr>
    <tr>
      <th>472</th>
      <td>-0.6808</td>
      <td>Fri Feb 02 21:13:03 +0000 2018</td>
      <td>0.752</td>
      <td>0.248</td>
      <td>The New York Times</td>
      <td>0.000</td>
      <td>Breaking News: Stocks ended a bad week with a ...</td>
      <td>73</td>
    </tr>
    <tr>
      <th>473</th>
      <td>0.0000</td>
      <td>Fri Feb 02 21:05:40 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>The New York Times</td>
      <td>0.000</td>
      <td>What if your employer made you wear a wristban...</td>
      <td>74</td>
    </tr>
    <tr>
      <th>474</th>
      <td>0.0000</td>
      <td>Fri Feb 02 20:55:00 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>The New York Times</td>
      <td>0.000</td>
      <td>2. "Zora Neale Hurston Fieldwork Footage" (192...</td>
      <td>75</td>
    </tr>
    <tr>
      <th>475</th>
      <td>0.7269</td>
      <td>Fri Feb 02 20:47:14 +0000 2018</td>
      <td>0.681</td>
      <td>0.000</td>
      <td>The New York Times</td>
      <td>0.319</td>
      <td>Fettuccine with sausage and fried sage makes a...</td>
      <td>76</td>
    </tr>
    <tr>
      <th>476</th>
      <td>-0.2960</td>
      <td>Fri Feb 02 20:30:19 +0000 2018</td>
      <td>0.885</td>
      <td>0.115</td>
      <td>The New York Times</td>
      <td>0.000</td>
      <td>Rebekah Martinez was reported missing in Novem...</td>
      <td>77</td>
    </tr>
    <tr>
      <th>477</th>
      <td>-0.9001</td>
      <td>Fri Feb 02 20:15:11 +0000 2018</td>
      <td>0.571</td>
      <td>0.429</td>
      <td>The New York Times</td>
      <td>0.000</td>
      <td>The worst flu season in nearly a decade has fi...</td>
      <td>78</td>
    </tr>
    <tr>
      <th>478</th>
      <td>-0.7964</td>
      <td>Fri Feb 02 20:05:03 +0000 2018</td>
      <td>0.747</td>
      <td>0.253</td>
      <td>The New York Times</td>
      <td>0.000</td>
      <td>RT @jdelreal: My latest: The Brock Turner sexu...</td>
      <td>79</td>
    </tr>
    <tr>
      <th>479</th>
      <td>0.5413</td>
      <td>Fri Feb 02 19:55:01 +0000 2018</td>
      <td>0.757</td>
      <td>0.000</td>
      <td>The New York Times</td>
      <td>0.243</td>
      <td>These girls want to compete as Jamaica's first...</td>
      <td>80</td>
    </tr>
    <tr>
      <th>480</th>
      <td>0.2023</td>
      <td>Fri Feb 02 19:44:52 +0000 2018</td>
      <td>0.917</td>
      <td>0.000</td>
      <td>The New York Times</td>
      <td>0.083</td>
      <td>RT @amyfiscus: What's NOT in the memo might be...</td>
      <td>81</td>
    </tr>
    <tr>
      <th>481</th>
      <td>0.0000</td>
      <td>Fri Feb 02 19:42:42 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>The New York Times</td>
      <td>0.000</td>
      <td>The full text of the memo released by House Re...</td>
      <td>82</td>
    </tr>
    <tr>
      <th>482</th>
      <td>0.5994</td>
      <td>Fri Feb 02 19:40:07 +0000 2018</td>
      <td>0.849</td>
      <td>0.000</td>
      <td>The New York Times</td>
      <td>0.151</td>
      <td>RT @nytimesmusic: When Justin Timberlake was a...</td>
      <td>83</td>
    </tr>
    <tr>
      <th>483</th>
      <td>0.7906</td>
      <td>Fri Feb 02 19:30:13 +0000 2018</td>
      <td>0.696</td>
      <td>0.000</td>
      <td>The New York Times</td>
      <td>0.304</td>
      <td>The Charleston Gazette-Mail won a Pulitzer Pri...</td>
      <td>84</td>
    </tr>
    <tr>
      <th>484</th>
      <td>0.0000</td>
      <td>Fri Feb 02 19:23:03 +0000 2018</td>
      <td>0.800</td>
      <td>0.100</td>
      <td>The New York Times</td>
      <td>0.100</td>
      <td>The memo released by House Republicans falls s...</td>
      <td>85</td>
    </tr>
    <tr>
      <th>485</th>
      <td>0.3612</td>
      <td>Fri Feb 02 19:15:12 +0000 2018</td>
      <td>0.848</td>
      <td>0.000</td>
      <td>The New York Times</td>
      <td>0.152</td>
      <td>Alec Baldwin skewers President Trump on SNL. A...</td>
      <td>86</td>
    </tr>
    <tr>
      <th>486</th>
      <td>0.0000</td>
      <td>Fri Feb 02 19:00:18 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>The New York Times</td>
      <td>0.000</td>
      <td>An Arizona man said he sold 720 rounds of ammu...</td>
      <td>87</td>
    </tr>
    <tr>
      <th>487</th>
      <td>0.2732</td>
      <td>Fri Feb 02 18:50:09 +0000 2018</td>
      <td>0.905</td>
      <td>0.000</td>
      <td>The New York Times</td>
      <td>0.095</td>
      <td>The Met isn’t trying to create a slasher opera...</td>
      <td>88</td>
    </tr>
    <tr>
      <th>488</th>
      <td>-0.4588</td>
      <td>Fri Feb 02 18:40:01 +0000 2018</td>
      <td>0.818</td>
      <td>0.182</td>
      <td>The New York Times</td>
      <td>0.000</td>
      <td>7 Humane Society board members have resigned i...</td>
      <td>89</td>
    </tr>
    <tr>
      <th>489</th>
      <td>0.0000</td>
      <td>Fri Feb 02 18:30:09 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>The New York Times</td>
      <td>0.000</td>
      <td>Carter Page is at the center of the Republican...</td>
      <td>90</td>
    </tr>
    <tr>
      <th>490</th>
      <td>0.7783</td>
      <td>Fri Feb 02 18:20:05 +0000 2018</td>
      <td>0.599</td>
      <td>0.081</td>
      <td>The New York Times</td>
      <td>0.320</td>
      <td>Opinion: "He went from being a devoted and lov...</td>
      <td>91</td>
    </tr>
    <tr>
      <th>491</th>
      <td>0.0000</td>
      <td>Fri Feb 02 18:06:52 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>The New York Times</td>
      <td>0.000</td>
      <td>Read the full text of the previously secret me...</td>
      <td>92</td>
    </tr>
    <tr>
      <th>492</th>
      <td>-0.3182</td>
      <td>Fri Feb 02 17:53:08 +0000 2018</td>
      <td>0.769</td>
      <td>0.142</td>
      <td>The New York Times</td>
      <td>0.089</td>
      <td>What changes are you seeing in how sexual hara...</td>
      <td>93</td>
    </tr>
    <tr>
      <th>493</th>
      <td>-0.2960</td>
      <td>Fri Feb 02 17:42:08 +0000 2018</td>
      <td>0.891</td>
      <td>0.109</td>
      <td>The New York Times</td>
      <td>0.000</td>
      <td>Our film critics, looking at the complex histo...</td>
      <td>94</td>
    </tr>
    <tr>
      <th>494</th>
      <td>0.0000</td>
      <td>Fri Feb 02 17:36:43 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>The New York Times</td>
      <td>0.000</td>
      <td>Here's how to experiment with augmented realit...</td>
      <td>95</td>
    </tr>
    <tr>
      <th>495</th>
      <td>0.1027</td>
      <td>Fri Feb 02 17:20:07 +0000 2018</td>
      <td>0.743</td>
      <td>0.119</td>
      <td>The New York Times</td>
      <td>0.139</td>
      <td>The memo alleges that senior government offici...</td>
      <td>96</td>
    </tr>
    <tr>
      <th>496</th>
      <td>-0.5574</td>
      <td>Fri Feb 02 17:17:46 +0000 2018</td>
      <td>0.816</td>
      <td>0.184</td>
      <td>The New York Times</td>
      <td>0.000</td>
      <td>Breaking News: House Republicans released a se...</td>
      <td>97</td>
    </tr>
    <tr>
      <th>497</th>
      <td>0.3182</td>
      <td>Fri Feb 02 17:08:23 +0000 2018</td>
      <td>0.905</td>
      <td>0.000</td>
      <td>The New York Times</td>
      <td>0.095</td>
      <td>The New York Times is about to expand our jour...</td>
      <td>98</td>
    </tr>
    <tr>
      <th>498</th>
      <td>-0.7579</td>
      <td>Fri Feb 02 17:00:47 +0000 2018</td>
      <td>0.745</td>
      <td>0.255</td>
      <td>The New York Times</td>
      <td>0.000</td>
      <td>RT @NYTHealth: Flu hospitalizations have soare...</td>
      <td>99</td>
    </tr>
    <tr>
      <th>499</th>
      <td>0.2500</td>
      <td>Fri Feb 02 16:53:05 +0000 2018</td>
      <td>0.895</td>
      <td>0.000</td>
      <td>The New York Times</td>
      <td>0.105</td>
      <td>We answer your questions about applying for Th...</td>
      <td>100</td>
    </tr>
  </tbody>
</table>
<p>500 rows × 8 columns</p>
</div>




```python
plt.style.use('ggplot')

date = dt.date.today()
cnn_df = sentiments_df[sentiments_df["News Account"]=="CNN Breaking News"]
cbs_df = sentiments_df[sentiments_df["News Account"]=="CBS News"]
bbc_df = sentiments_df[sentiments_df["News Account"]=="BBC News (World)"]
fox_df = sentiments_df[sentiments_df["News Account"]=="Fox News"]
nyt_df = sentiments_df[sentiments_df["News Account"]=="The New York Times"]
plt.scatter(cnn_df["Tweets Ago"], cnn_df["Compound"], label="CNN", edgecolor="black")
plt.scatter(cbs_df["Tweets Ago"], cbs_df["Compound"], label="CBS", edgecolor="black")
plt.scatter(bbc_df["Tweets Ago"], bbc_df["Compound"], label="BBC", edgecolor="black")
plt.scatter(fox_df["Tweets Ago"], fox_df["Compound"], label="Fox", edgecolor="black")
plt.scatter(nyt_df["Tweets Ago"], nyt_df["Compound"], label="NYT", edgecolor="black")
plt.legend(title="Outlets", loc="best")
plt.xlabel("Tweets Ago")
plt.ylabel("Tweet Polarity")
plt.title("Sentiment Analysis of Media Tweets (" + str(date) + ")")
plt.savefig("Sentiment_Scatter.png")
```


![png](output_9_0.png)



```python
news = sentiments_df.groupby("News Account")["Compound"].mean()
plt.figure(figsize=(12,8))
news = news.plot(kind="bar", rot=0)
#news_bar = news.plot(kind="bar")
plt.ylabel("Twitter Polarity")
plt.xlabel("")
plt.title("Overall Media Sentiment Based\n on Twitter (" + str(date) + ")")
plt.savefig("Sentiment_Bar.png")


```


![png](output_10_0.png)



```python
sentiments_df.groupby("News Account")["Compound"].mean()
```




    News Account
    BBC News (World)     -0.113237
    CBS News             -0.152701
    CNN Breaking News    -0.016371
    Fox News             -0.046613
    The New York Times    0.009700
    Name: Compound, dtype: float64


