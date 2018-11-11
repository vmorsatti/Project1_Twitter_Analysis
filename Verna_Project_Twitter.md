
## Project Tweepler
### Project Team: Zach, Verna, James & Mandy

#### Project Summary
##### Analysis of the trending twitter users globally to analyze trending topics, user behavior and sentiment.
#### Tools / Imports Used
-  Tweepler API to extract. Tweepler based on “ World” is based on the number of tweets per 20K tweets coming from the World in timespan of current to last 13 minutes.
-  Jupyter Notebook / GitHub 
-  Matplotlib/ Seaborn for graphing and animation.
-  Beautiful Soup for scraping data Tweepler summary
-  Re for scraping ISO 639 -1 language codes
-  Vader Sentiment Analyzor
-  WordCloud and Stopword to create wordcloud image of trending words
-  Botometer

#### Data Set and Quality Issues
500 tweets for top 10 trending users
Location data not “usable” as it is a text field entered by users.
27.2% of data in tweets have multi language or language not supported by the Vader tool.

#### Definitions
Trending Users –twitter users mentioned by other users
Language Group – “Other” - any language appearing less than 10 times in the data and is based on the tweet language.

Tweeters - Users tweeting about the trending users.


```python
import tweepy
import pandas as pd
import numpy as np
import seaborn as sns
import requests

# Regex text tools
import re

# Text Sentiment Analyzer
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer
analyzer = SentimentIntensityAnalyzer()
```


```python
# Twitter API Keys
consumer_key = "rWYwPO5GujlB2DJnYxuiFXpv0"
consumer_secret = "tkqWqUDNeGNCxHAgSPzObW88YKBxfxGazD3T0XWqH9aklACNiZ"
app_key = "891138909161242624-brnipgrui4Zl2ygfbVIrwc5OwEwRGIN"
app_secret = "yajiQuWe5ux8hxU2xjgb7ZBAM6LHEzL8NnUpmgrNziAom"

#Tweepy API Authentication
auth = tweepy.OAuthHandler(consumer_key, consumer_secret)
auth.set_access_token(app_key, app_secret)

#api = tweepy.API(auth)
api = tweepy.API(auth, wait_on_rate_limit=True, wait_on_rate_limit_notify=True)
```


```python
# Get html page to scrape
file = 'http://tweeplers.com/?cc=WORLD'
response = requests.get(file)
```


```python
# Display html text to search for '@TwitterUserName<'
print(response.text)
```

    
    <!DOCTYPE html>
    
    <html lang="en">
    <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <!-- The above 3 meta tags *must* come first in the head; any other head content must come *after* these tags -->
        <title>Twitter Trending People, World</title>
        <meta name="description" content="Know who all are trending right now on the twitter from World. Who is being most talked about and making it to the news on twitter.">
    
        <!-- twitter card -->
        <meta name="twitter:card" content="summary_large_image">
    <meta name="twitter:site" content="@tweeplers">
    <meta name="twitter:creator" content="@tweeplers">
    <meta name="twitter:title" content="Twitter Trending People (Mentions) in the World">
    <meta name="twitter:description" content="Know who all are trending right now on twitter from World, what's trending on twitter and who is being most talked about and making it to the news on twitter.">
    <meta name="twitter:image" content="http://www.tweeplers.com/tweeplers_profile.jpg">
    <link rel="image_src" href="/tweeplers_profile.jpg" />
    
    <!-- favicon -->
    <link rel="apple-touch-icon" sizes="120x120" href="/apple-touch-icon.png">
    <link rel="icon" type="image/png" href="/favicon-32x32.png" sizes="32x32">
    <link rel="icon" type="image/png" href="/favicon-16x16.png" sizes="16x16">
    <link rel="manifest" href="/manifest.json">
    <link rel="mask-icon" href="/safari-pinned-tab.svg" color="#5bbad5">
    <meta name="theme-color" content="#ffffff">
    <!-- favicon end -->
    
        <!-- Bootstrap -->
        <link href="bootstrap-3.3.7-dist/css/bootstrap.min.css" rel="stylesheet">
        <link href="bootstrap-3.3.7-dist/css/tweeplers.css" rel="stylesheet">
        <script src="https://apis.google.com/js/platform.js" async defer></script>
    
        <!-- HTML5 shim and Respond.js for IE8 support of HTML5 elements and media queries -->
        <!-- WARNING: Respond.js doesn't work if you view the page via file:// -->
        <!--[if lt IE 9]>
          <script src="https://oss.maxcdn.com/html5shiv/3.7.3/html5shiv.min.js"></script>
          <script src="https://oss.maxcdn.com/respond/1.4.2/respond.min.js"></script>
        <![endif]-->
    </head>
    <body>
    <script>
            (function (i, s, o, g, r, a, m) {
                i['GoogleAnalyticsObject'] = r; i[r] = i[r] || function () {
                    (i[r].q = i[r].q || []).push(arguments)
                }, i[r].l = 1 * new Date(); a = s.createElement(o),
      m = s.getElementsByTagName(o)[0]; a.async = 1; a.src = g; m.parentNode.insertBefore(a, m)
            })(window, document, 'script', 'https://www.google-analytics.com/analytics.js', 'ga');
    
            ga('create', 'UA-350192-19', 'auto');
            ga('send', 'pageview');
    
    </script>
    <nav class="navbar navbar-inverse navbar-static-top" >
          <div class="container-fluid">
            <div class="navbar-header">
              <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#navbar" aria-expanded="false" aria-controls="navbar">
                <span class="sr-only">Toggle navigation</span>
                <span class="icon-bar"></span>
                <span class="icon-bar"></span>
                <span class="icon-bar"></span>
              </button>
              <div class="hidden-xs" style="height:10px"></div>
              <a class="navbar-brand" style="padding-top:0px;padding-bottom:0px;" href="/">
              <img class="img-responsive" style="height:50px;width:264px;" src="trophy.png"/></a>
            </div>
            <div id="navbar" class="collapse navbar-collapse">
              <ul class="nav navbar-nav">
                <li><a href="/mapview/?cc=WORLD" title="Twitter Trending People Map of World">Twitter Trending People Map</a></li>
                <li><a href="/hashtags/?cc=WORLD" title="Twitter Trending Hashtags in World">Twitter Trending Hashtags</a></li>
                <li><a href="/cities/?cc=WORLD" title="Top Tweeting Cities in World">Top Tweeting Cities</a></li>
                <li><a href="/map/hashtags/">Realtime Hashtags Map</a></li>
                <li><a href="/countries/">Top Tweeting Countries</a></li>
                <li><a href="/map/">Realtime Tweets Map</a></li>
                <li><div class="dropdown" style="padding-top:8px">
                      <button class="btn btn-default dropdown-toggle" type="button" id="dropdownMenu1" data-toggle="dropdown" aria-haspopup="true" aria-expanded="true">
                        Country
                        <span class="caret"></span>
                      </button>
                      <ul class="dropdown-menu" aria-labelledby="dropdownMenu1">
                        <li><a href="?cc=WORLD"><img src="/flags/WORLD.png"/>&nbsp;Worldwide</a></li>
                        <li role="separator" class="divider"></li>
                        <li><a href="?cc=US"><img src="/flags/US.png"/>&nbsp;United States</a></li>
                        <li><a href="?cc=BR"><img src="/flags/BR.png"/>&nbsp;Brazil</a></li>
                        <li><a href="?cc=JP"><img src="/flags/JP.png"/>&nbsp;Japan</a></li>
                        <li><a href="?cc=GB"><img src="/flags/GB.png"/>&nbsp;United Kingdom</a></li>
                        <li><a href="?cc=CA"><img src="/flags/CA.png"/>&nbsp;Canada</a></li>
                        <li><a href="?cc=AU"><img src="/flags/AU.png"/>&nbsp;Australia</a></li>
                        <li><a href="?cc=ES"><img src="/flags/ES.png"/>&nbsp;Spain</a></li>
                        <li><a href="?cc=MX"><img src="/flags/MX.png"/>&nbsp;Mexico</a></li>
                        <li><a href="?cc=MY"><img src="/flags/MY.png"/>&nbsp;Malaysia</a></li>
                        <li><a href="?cc=PH"><img src="/flags/PH.png"/>&nbsp;Philippines</a></li>
                        <li><a href="?cc=TH"><img src="/flags/TH.png"/>&nbsp;Thailand</a></li>
                        <li><a href="?cc=SA"><img src="/flags/SA.png"/>&nbsp;Saudi Arabia</a></li>
                        <li><a href="?cc=AR"><img src="/flags/AR.png"/>&nbsp;Argentina</a></li>
                        <li><a href="?cc=TR"><img src="/flags/TR.png"/>&nbsp;Turkey</a></li>
                        <li><a href="?cc=FR"><img src="/flags/FR.png"/>&nbsp;France</a></li>
                        <li><a href="?cc=IN"><img src="/flags/IN.png"/>&nbsp;India</a></li>
                        <li><a href="?cc=IT"><img src="/flags/IT.png"/>&nbsp;Italy</a></li>
                        <li><a href="?cc=ID"><img src="/flags/ID.png"/>&nbsp;Indonesia</a></li>
                        <li><a href="?cc=NL"><img src="/flags/NL.png"/>&nbsp;Netherlands</a></li>
                        <li role="separator" class="divider"></li>
                        <li><a href="/countries/">More</a></li>
                       </ul></li>
                       </ul>
            </div><!--/.nav-collapse -->
          </div>
        </nav>
    
        <div class="container">
        <div class="row">
          <div class="col-md-8">
          <div class="panel panel-primary" >
              <div class="panel-heading" >
              <h3 class="panel-title" style="padding-bottom:10px;"><img src="flags/WORLD.png" style="vertical-align:middle" />&nbsp;
              <b>Twitter Trending People (Mentions) in the World right now.</b></h3>
              <a class="twitter-share-button"  href="http://www.tweeplers.com/?cc=WORLD">Tweet</a>&nbsp;&nbsp;&nbsp;&nbsp;
              <iframe src="http://www.facebook.com/plugins/like.php?href=http://www.tweeplers.com/?cc=WORLD&layout=button_count"
                    scrolling="no" frameborder="0" allowTransparency="true"
                    style="border:none; width:85px; height:20px; color:White;"></iframe>
                    <g:plusone href="http://www.tweeplers.com/?cc=WORLD" size="medium"></g:plusone>
                    
                    <h3 class="panel-title" style="padding-top:10px;">See Also: <a title="United States Trending Twitter People" style="text-decoration:underline" href="?cc=US"><b>United States Trending Twitter People</b></a></h3>
                    <h3 class="panel-title" style="padding-top:10px;">See Also: <a title="Map of Trending Twitter People" style="text-decoration:underline" href="/mapview/?cc=WORLD"><b>Map of Trending Twitter People</b></a></h3>
                    
    </div>
    <div class="panel-body">
         <div class="row" style="font-size:0.8em;color:gray;font-weight:bold;">
                <div class="col-xs-1 col-md-1">Rank</div>
                <div class="col-xs-1 col-md-2"></div>
                <div class="col-xs-4 col-md-5">Twitter User</div>
                <div class="col-xs-3 col-md-2">Followers</div>
                <div class="col-xs-1 col-md-2">Tweets /20K</div>
                </div>
          
                        <div class="row" id="row_1" style="padding-top:15px;padding-bottom:15px;background-color:#f1f1f1;">
                        
                <div class="col-xs-1" ><span class="badge">1</span></div>
                <div class="col-xs-2" id="item_i_1"><a href="http://www.twitter.com/realDonaldTrump"><img class="img-rounded" src="http://pbs.twimg.com/profile_images/874276197357596672/kUuht00m_normal.jpg" /></a></div>
                <div class="col-xs-5">
                <div class="col-md-5 wordwrap" id="item_n_1"><a href="http://www.twitter.com/search?q=%40realDonaldTrump"><b>Donald J. Trump</b></a></div>
                <div class="col-md-7" id="item_u_1" name="realDonaldTrump"><a href="http://www.twitter.com/search?q=%40realDonaldTrump">@realDonaldTrump</a></div>
                </div>
                <div class="col-xs-1 col-md-1" id="item_f_1" style="font-size:0.8em;padding-left:0px;">55.5M</div>
                <div class="col-xs-1 col-md-1" ></div>
                <div class="col-xs-2 col-md-2" id="item_c_1" style="font-size:1.5em;color:Blue;font-weight:bold;">176</div>
                </div>
                
                        <div class="row"  id="row_2" style="padding-top:15px;padding-bottom:15px;">
                        
                <div class="col-xs-1" ><span class="badge">2</span></div>
                <div class="col-xs-2" id="item_i_2"><a href="http://www.twitter.com/YouTube"><img class="img-rounded" src="http://pbs.twimg.com/profile_images/1013436760859299847/aQltRN9T_normal.jpg" /></a></div>
                <div class="col-xs-5">
                <div class="col-md-5 wordwrap" id="item_n_2"><a href="http://www.twitter.com/search?q=%40YouTube"><b>YouTube</b></a></div>
                <div class="col-md-7" id="item_u_2" name="YouTube"><a href="http://www.twitter.com/search?q=%40YouTube">@YouTube</a></div>
                </div>
                <div class="col-xs-1 col-md-1" id="item_f_2" style="font-size:0.8em;padding-left:0px;">70.9M</div>
                <div class="col-xs-1 col-md-1" ></div>
                <div class="col-xs-2 col-md-2" id="item_c_2" style="font-size:1.5em;color:Blue;font-weight:bold;">44</div>
                </div>
                
                        <div class="row" id="row_3" style="padding-top:15px;padding-bottom:15px;background-color:#f1f1f1;">
                        
                <div class="col-xs-1" ><span class="badge">3</span></div>
                <div class="col-xs-2" id="item_i_3"><a href="http://www.twitter.com/AdamSKutner"><img class="img-rounded" src="http://pbs.twimg.com/profile_images/2904344504/b7bfe495428101f82b06cc779363645c_normal.jpeg" /></a></div>
                <div class="col-xs-5">
                <div class="col-md-5 wordwrap" id="item_n_3"><a href="http://www.twitter.com/search?q=%40AdamSKutner"><b>Adam Kutner</b></a></div>
                <div class="col-md-7" id="item_u_3" name="AdamSKutner"><a href="http://www.twitter.com/search?q=%40AdamSKutner">@AdamSKutner</a></div>
                </div>
                <div class="col-xs-1 col-md-1" id="item_f_3" style="font-size:0.8em;padding-left:0px;">2.9K</div>
                <div class="col-xs-1 col-md-1" ></div>
                <div class="col-xs-2 col-md-2" id="item_c_3" style="font-size:1.5em;color:Blue;font-weight:bold;">32</div>
                </div>
                
                        <div class="row"  id="row_4" style="padding-top:15px;padding-bottom:15px;">
                        
                <div class="col-xs-1" ><span class="badge">4</span></div>
                <div class="col-xs-2" id="item_i_4"><a href="http://www.twitter.com/NissanUSA"><img class="img-rounded" src="http://pbs.twimg.com/profile_images/674321950815092738/-HU7IH6f_normal.png" /></a></div>
                <div class="col-xs-5">
                <div class="col-md-5 wordwrap" id="item_n_4"><a href="http://www.twitter.com/search?q=%40NissanUSA"><b>Nissan</b></a></div>
                <div class="col-md-7" id="item_u_4" name="NissanUSA"><a href="http://www.twitter.com/search?q=%40NissanUSA">@NissanUSA</a></div>
                </div>
                <div class="col-xs-1 col-md-1" id="item_f_4" style="font-size:0.8em;padding-left:0px;">0.7M</div>
                <div class="col-xs-1 col-md-1" ></div>
                <div class="col-xs-2 col-md-2" id="item_c_4" style="font-size:1.5em;color:Blue;font-weight:bold;">28</div>
                </div>
                
                        <div class="row" id="row_5" style="padding-top:15px;padding-bottom:15px;background-color:#f1f1f1;">
                        
                <div class="col-xs-1" ><span class="badge">5</span></div>
                <div class="col-xs-2" id="item_i_5"><a href="http://www.twitter.com/BTS_twt"><img class="img-rounded" src="http://pbs.twimg.com/profile_images/1030106929467600896/Pzt9KhMn_normal.jpg" /></a></div>
                <div class="col-xs-5">
                <div class="col-md-5 wordwrap" id="item_n_5"><a href="http://www.twitter.com/search?q=%40BTS_twt"><b>방탄소년단</b></a></div>
                <div class="col-md-7" id="item_u_5" name="BTS_twt"><a href="http://www.twitter.com/search?q=%40BTS_twt">@BTS_twt</a></div>
                </div>
                <div class="col-xs-1 col-md-1" id="item_f_5" style="font-size:0.8em;padding-left:0px;">16.9M</div>
                <div class="col-xs-1 col-md-1" ></div>
                <div class="col-xs-2 col-md-2" id="item_c_5" style="font-size:1.5em;color:Blue;font-weight:bold;">23</div>
                </div>
                
                <div class="row" style="text-align:center;padding-top:5px;padding-bottom:5px;background-color:#FFFF99;">
                <div class="col-md-12">
                <a href="http://wikitrends.net" title="Most Viewed Articles - Trending Topics on Wikipedia"><img src="/flags/thumb.png" /><b>Most Viewed Articles - Trending Topics on Wikipedia</b></a>
                </div>
                </div>
                
                        <div class="row"  id="row_6" style="padding-top:15px;padding-bottom:15px;">
                        
                <div class="col-xs-1" ><span class="badge">6</span></div>
                <div class="col-xs-2" id="item_i_6"><a href="http://www.twitter.com/POTUS"><img class="img-rounded" src="http://pbs.twimg.com/profile_images/859982100904148992/hv5soju7_normal.jpg" /></a></div>
                <div class="col-xs-5">
                <div class="col-md-5 wordwrap" id="item_n_6"><a href="http://www.twitter.com/search?q=%40POTUS"><b>President Trump</b></a></div>
                <div class="col-md-7" id="item_u_6" name="POTUS"><a href="http://www.twitter.com/search?q=%40POTUS">@POTUS</a></div>
                </div>
                <div class="col-xs-1 col-md-1" id="item_f_6" style="font-size:0.8em;padding-left:0px;">24.4M</div>
                <div class="col-xs-1 col-md-1" ></div>
                <div class="col-xs-2 col-md-2" id="item_c_6" style="font-size:1.5em;color:Blue;font-weight:bold;">22</div>
                </div>
                
                        <div class="row" id="row_7" style="padding-top:15px;padding-bottom:15px;background-color:#f1f1f1;">
                        
                <div class="col-xs-1" ><span class="badge">7</span></div>
                <div class="col-xs-2" id="item_i_7"><a href="http://www.twitter.com/FoxNews"><img class="img-rounded" src="http://pbs.twimg.com/profile_images/918480715158716419/4X8oCbge_normal.jpg" /></a></div>
                <div class="col-xs-5">
                <div class="col-md-5 wordwrap" id="item_n_7"><a href="http://www.twitter.com/search?q=%40FoxNews"><b>Fox News</b></a></div>
                <div class="col-md-7" id="item_u_7" name="FoxNews"><a href="http://www.twitter.com/search?q=%40FoxNews">@FoxNews</a></div>
                </div>
                <div class="col-xs-1 col-md-1" id="item_f_7" style="font-size:0.8em;padding-left:0px;">18.1M</div>
                <div class="col-xs-1 col-md-1" ></div>
                <div class="col-xs-2 col-md-2" id="item_c_7" style="font-size:1.5em;color:Blue;font-weight:bold;">20</div>
                </div>
                
                        <div class="row"  id="row_8" style="padding-top:15px;padding-bottom:15px;">
                        
                <div class="col-xs-1" ><span class="badge">8</span></div>
                <div class="col-xs-2" id="item_i_8"><a href="http://www.twitter.com/CSEmelec"><img class="img-rounded" src="http://pbs.twimg.com/profile_images/990574942642167808/Z5H66vsP_normal.jpg" /></a></div>
                <div class="col-xs-5">
                <div class="col-md-5 wordwrap" id="item_n_8"><a href="http://www.twitter.com/search?q=%40CSEmelec"><b>Club Sport Emelec</b></a></div>
                <div class="col-md-7" id="item_u_8" name="CSEmelec"><a href="http://www.twitter.com/search?q=%40CSEmelec">@CSEmelec</a></div>
                </div>
                <div class="col-xs-1 col-md-1" id="item_f_8" style="font-size:0.8em;padding-left:0px;">0.5M</div>
                <div class="col-xs-1 col-md-1" ></div>
                <div class="col-xs-2 col-md-2" id="item_c_8" style="font-size:1.5em;color:Blue;font-weight:bold;">16</div>
                </div>
                
                        <div class="row" id="row_9" style="padding-top:15px;padding-bottom:15px;background-color:#f1f1f1;">
                        
                <div class="col-xs-1" ><span class="badge">9</span></div>
                <div class="col-xs-2" id="item_i_9"><a href="http://www.twitter.com/JREast_official"><img class="img-rounded" src="http://pbs.twimg.com/profile_images/583122926326583297/fdjHKu43_normal.png" /></a></div>
                <div class="col-xs-5">
                <div class="col-md-5 wordwrap" id="item_n_9"><a href="http://www.twitter.com/search?q=%40JREast_official"><b>JR東日本（公式）</b></a></div>
                <div class="col-md-7" id="item_u_9" name="JREast_official"><a href="http://www.twitter.com/search?q=%40JREast_official">@JREast_official</a></div>
                </div>
                <div class="col-xs-1 col-md-1" id="item_f_9" style="font-size:0.8em;padding-left:0px;">45.9K</div>
                <div class="col-xs-1 col-md-1" ></div>
                <div class="col-xs-2 col-md-2" id="item_c_9" style="font-size:1.5em;color:Blue;font-weight:bold;">16</div>
                </div>
                
                        <div class="row"  id="row_10" style="padding-top:15px;padding-bottom:15px;">
                        
                <div class="col-xs-1" ><span class="badge">10</span></div>
                <div class="col-xs-2" id="item_i_10"><a href="http://www.twitter.com/TeletonOficial"><img class="img-rounded" src="http://pbs.twimg.com/profile_images/1028028377381445632/TJ2Bx5Rn_normal.jpg" /></a></div>
                <div class="col-xs-5">
                <div class="col-md-5 wordwrap" id="item_n_10"><a href="http://www.twitter.com/search?q=%40TeletonOficial"><b>Teleton AACD</b></a></div>
                <div class="col-md-7" id="item_u_10" name="TeletonOficial"><a href="http://www.twitter.com/search?q=%40TeletonOficial">@TeletonOficial</a></div>
                </div>
                <div class="col-xs-1 col-md-1" id="item_f_10" style="font-size:0.8em;padding-left:0px;">0.2M</div>
                <div class="col-xs-1 col-md-1" ></div>
                <div class="col-xs-2 col-md-2" id="item_c_10" style="font-size:1.5em;color:Blue;font-weight:bold;">16</div>
                </div>
                
                <div class="row" style="text-align:center;padding-top:5px;padding-bottom:5px;background-color:#FFFF99;">
                <div class="col-md-12">
                <a href="http://wikitrends.net/?ca=5" title="Most Viewed Peolple Articles - Trending Peolple on Wikipedia"><img src="/flags/thumb.png" /><b>Most Viewed Peolple Articles - Trending Peolple on Wikipedia</b></a>
                </div>
                </div>
                
                        <div class="row" id="row_11" style="padding-top:15px;padding-bottom:15px;background-color:#f1f1f1;">
                        
                <div class="col-xs-1" ><span class="badge">11</span></div>
                <div class="col-xs-2" id="item_i_11"><a href="http://www.twitter.com/krassenstein"><img class="img-rounded" src="http://pbs.twimg.com/profile_images/979820353475117057/P1XFvbc2_normal.jpg" /></a></div>
                <div class="col-xs-5">
                <div class="col-md-5 wordwrap" id="item_n_11"><a href="http://www.twitter.com/search?q=%40krassenstein"><b>Brian Krassenstein</b></a></div>
                <div class="col-md-7" id="item_u_11" name="krassenstein"><a href="http://www.twitter.com/search?q=%40krassenstein">@krassenstein</a></div>
                </div>
                <div class="col-xs-1 col-md-1" id="item_f_11" style="font-size:0.8em;padding-left:0px;">0.5M</div>
                <div class="col-xs-1 col-md-1" ></div>
                <div class="col-xs-2 col-md-2" id="item_c_11" style="font-size:1.5em;color:Blue;font-weight:bold;">15</div>
                </div>
                
                        <div class="row"  id="row_12" style="padding-top:15px;padding-bottom:15px;">
                        
                <div class="col-xs-1" ><span class="badge">12</span></div>
                <div class="col-xs-2" id="item_i_12"><a href="http://www.twitter.com/GOP"><img class="img-rounded" src="http://pbs.twimg.com/profile_images/975343259001106432/7uzLo2Tx_normal.jpg" /></a></div>
                <div class="col-xs-5">
                <div class="col-md-5 wordwrap" id="item_n_12"><a href="http://www.twitter.com/search?q=%40GOP"><b>GOP</b></a></div>
                <div class="col-md-7" id="item_u_12" name="GOP"><a href="http://www.twitter.com/search?q=%40GOP">@GOP</a></div>
                </div>
                <div class="col-xs-1 col-md-1" id="item_f_12" style="font-size:0.8em;padding-left:0px;">1.7M</div>
                <div class="col-xs-1 col-md-1" ></div>
                <div class="col-xs-2 col-md-2" id="item_c_12" style="font-size:1.5em;color:Blue;font-weight:bold;">15</div>
                </div>
                
                        <div class="row" id="row_13" style="padding-top:15px;padding-bottom:15px;background-color:#f1f1f1;">
                        
                <div class="col-xs-1" ><span class="badge">13</span></div>
                <div class="col-xs-2" id="item_i_13"><a href="http://www.twitter.com/LindseyGrahamSC"><img class="img-rounded" src="http://pbs.twimg.com/profile_images/1045473448783466496/XnZoIj-Q_normal.jpg" /></a></div>
                <div class="col-xs-5">
                <div class="col-md-5 wordwrap" id="item_n_13"><a href="http://www.twitter.com/search?q=%40LindseyGrahamSC"><b>Lindsey Graham</b></a></div>
                <div class="col-md-7" id="item_u_13" name="LindseyGrahamSC"><a href="http://www.twitter.com/search?q=%40LindseyGrahamSC">@LindseyGrahamSC</a></div>
                </div>
                <div class="col-xs-1 col-md-1" id="item_f_13" style="font-size:0.8em;padding-left:0px;">0.5M</div>
                <div class="col-xs-1 col-md-1" ></div>
                <div class="col-xs-2 col-md-2" id="item_c_13" style="font-size:1.5em;color:Blue;font-weight:bold;">15</div>
                </div>
                
                        <div class="row"  id="row_14" style="padding-top:15px;padding-bottom:15px;">
                        
                <div class="col-xs-1" ><span class="badge">14</span></div>
                <div class="col-xs-2" id="item_i_14"><a href="http://www.twitter.com/davidhogg111"><img class="img-rounded" src="http://pbs.twimg.com/profile_images/1056901243287805952/5HXJ5Jo6_normal.jpg" /></a></div>
                <div class="col-xs-5">
                <div class="col-md-5 wordwrap" id="item_n_14"><a href="http://www.twitter.com/search?q=%40davidhogg111"><b>David Hogg</b></a></div>
                <div class="col-md-7" id="item_u_14" name="davidhogg111"><a href="http://www.twitter.com/search?q=%40davidhogg111">@davidhogg111</a></div>
                </div>
                <div class="col-xs-1 col-md-1" id="item_f_14" style="font-size:0.8em;padding-left:0px;">0.8M</div>
                <div class="col-xs-1 col-md-1" ></div>
                <div class="col-xs-2 col-md-2" id="item_c_14" style="font-size:1.5em;color:Blue;font-weight:bold;">15</div>
                </div>
                
                        <div class="row" id="row_15" style="padding-top:15px;padding-bottom:15px;background-color:#f1f1f1;">
                        
                <div class="col-xs-1" ><span class="badge">15</span></div>
                <div class="col-xs-2" id="item_i_15"><a href="http://www.twitter.com/10APG"><img class="img-rounded" src="http://pbs.twimg.com/profile_images/1048386314939195393/HJvJkhSF_normal.jpg" /></a></div>
                <div class="col-xs-5">
                <div class="col-md-5 wordwrap" id="item_n_15"><a href="http://www.twitter.com/search?q=%4010APG"><b>Gignac Andre-pierre</b></a></div>
                <div class="col-md-7" id="item_u_15" name="10APG"><a href="http://www.twitter.com/search?q=%4010APG">@10APG</a></div>
                </div>
                <div class="col-xs-1 col-md-1" id="item_f_15" style="font-size:0.8em;padding-left:0px;">1.4M</div>
                <div class="col-xs-1 col-md-1" ></div>
                <div class="col-xs-2 col-md-2" id="item_c_15" style="font-size:1.5em;color:Blue;font-weight:bold;">15</div>
                </div>
                
                <div class="row" style="text-align:center;padding-top:5px;padding-bottom:5px;background-color:#FFFF99;">
                <div class="col-md-12">
                <a href="http://wikitrends.net/?ca=11424" title="Most Viewed Film Articles - Trending Film on Wikipedia"><img src="/flags/thumb.png" /><b>Most Viewed Film Articles - Trending Film on Wikipedia</b></a>
                </div>
                </div>
                
                        <div class="row"  id="row_16" style="padding-top:15px;padding-bottom:15px;">
                        
                <div class="col-xs-1" ><span class="badge">16</span></div>
                <div class="col-xs-2" id="item_i_16"><a href="http://www.twitter.com/marcorubio"><img class="img-rounded" src="http://pbs.twimg.com/profile_images/953778314057015296/PwOvxEQ-_normal.jpg" /></a></div>
                <div class="col-xs-5">
                <div class="col-md-5 wordwrap" id="item_n_16"><a href="http://www.twitter.com/search?q=%40marcorubio"><b>Marco Rubio</b></a></div>
                <div class="col-md-7" id="item_u_16" name="marcorubio"><a href="http://www.twitter.com/search?q=%40marcorubio">@marcorubio</a></div>
                </div>
                <div class="col-xs-1 col-md-1" id="item_f_16" style="font-size:0.8em;padding-left:0px;">3.6M</div>
                <div class="col-xs-1 col-md-1" ></div>
                <div class="col-xs-2 col-md-2" id="item_c_16" style="font-size:1.5em;color:Blue;font-weight:bold;">14</div>
                </div>
                
                        <div class="row" id="row_17" style="padding-top:15px;padding-bottom:15px;background-color:#f1f1f1;">
                        
                <div class="col-xs-1" ><span class="badge">17</span></div>
                <div class="col-xs-2" id="item_i_17"><a href="http://www.twitter.com/PascoSheriff"><img class="img-rounded" src="http://pbs.twimg.com/profile_images/1058008660369117191/ptR51bxJ_normal.jpg" /></a></div>
                <div class="col-xs-5">
                <div class="col-md-5 wordwrap" id="item_n_17"><a href="http://www.twitter.com/search?q=%40PascoSheriff"><b>Pasco Sheriff</b></a></div>
                <div class="col-md-7" id="item_u_17" name="PascoSheriff"><a href="http://www.twitter.com/search?q=%40PascoSheriff">@PascoSheriff</a></div>
                </div>
                <div class="col-xs-1 col-md-1" id="item_f_17" style="font-size:0.8em;padding-left:0px;">91.4K</div>
                <div class="col-xs-1 col-md-1" ></div>
                <div class="col-xs-2 col-md-2" id="item_c_17" style="font-size:1.5em;color:Blue;font-weight:bold;">14</div>
                </div>
                
                        <div class="row"  id="row_18" style="padding-top:15px;padding-bottom:15px;">
                        
                <div class="col-xs-1" ><span class="badge">18</span></div>
                <div class="col-xs-2" id="item_i_18"><a href="http://www.twitter.com/LauraLoomer"><img class="img-rounded" src="http://pbs.twimg.com/profile_images/1021406028347461632/Y4HinDOQ_normal.jpg" /></a></div>
                <div class="col-xs-5">
                <div class="col-md-5 wordwrap" id="item_n_18"><a href="http://www.twitter.com/search?q=%40LauraLoomer"><b>Laura Loomer</b></a></div>
                <div class="col-md-7" id="item_u_18" name="LauraLoomer"><a href="http://www.twitter.com/search?q=%40LauraLoomer">@LauraLoomer</a></div>
                </div>
                <div class="col-xs-1 col-md-1" id="item_f_18" style="font-size:0.8em;padding-left:0px;">0.2M</div>
                <div class="col-xs-1 col-md-1" ></div>
                <div class="col-xs-2 col-md-2" id="item_c_18" style="font-size:1.5em;color:Blue;font-weight:bold;">13</div>
                </div>
                
                        <div class="row" id="row_19" style="padding-top:15px;padding-bottom:15px;background-color:#f1f1f1;">
                        
                <div class="col-xs-1" ><span class="badge">19</span></div>
                <div class="col-xs-2" id="item_i_19"><a href="http://www.twitter.com/dbongino"><img class="img-rounded" src="http://pbs.twimg.com/profile_images/982777167351373824/2qrZ01gs_normal.jpg" /></a></div>
                <div class="col-xs-5">
                <div class="col-md-5 wordwrap" id="item_n_19"><a href="http://www.twitter.com/search?q=%40dbongino"><b>Dan Bongino</b></a></div>
                <div class="col-md-7" id="item_u_19" name="dbongino"><a href="http://www.twitter.com/search?q=%40dbongino">@dbongino</a></div>
                </div>
                <div class="col-xs-1 col-md-1" id="item_f_19" style="font-size:0.8em;padding-left:0px;">0.7M</div>
                <div class="col-xs-1 col-md-1" ></div>
                <div class="col-xs-2 col-md-2" id="item_c_19" style="font-size:1.5em;color:Blue;font-weight:bold;">12</div>
                </div>
                
                        <div class="row"  id="row_20" style="padding-top:15px;padding-bottom:15px;">
                        
                <div class="col-xs-1" ><span class="badge">20</span></div>
                <div class="col-xs-2" id="item_i_20"><a href="http://www.twitter.com/OfficialLivePD"><img class="img-rounded" src="http://pbs.twimg.com/profile_images/913518372859637760/cVtM0tst_normal.jpg" /></a></div>
                <div class="col-xs-5">
                <div class="col-md-5 wordwrap" id="item_n_20"><a href="http://www.twitter.com/search?q=%40OfficialLivePD"><b>Live PD on A&E</b></a></div>
                <div class="col-md-7" id="item_u_20" name="OfficialLivePD"><a href="http://www.twitter.com/search?q=%40OfficialLivePD">@OfficialLivePD</a></div>
                </div>
                <div class="col-xs-1 col-md-1" id="item_f_20" style="font-size:0.8em;padding-left:0px;">0.3M</div>
                <div class="col-xs-1 col-md-1" ></div>
                <div class="col-xs-2 col-md-2" id="item_c_20" style="font-size:1.5em;color:Blue;font-weight:bold;">12</div>
                </div>
                
          </div>
          </div>
          </div>
          
                <div class="col-md-4">
                    <div class="panel panel-primary">
              <div class="panel-heading">
              
              <h3 class="panel-title"><b>About "tweeplers"</b></h3>
                <a class="twitter-follow-button"
                href="https://twitter.com/tweeplers">
                Follow @tweeplers</a>
              </div>
              <div class="panel-body">
              If you ever wondered who is trending on the twitter in the World and who is being most talked about on the twitter right now, then tweeplers.com is the right place for you. The list of trending twitter users from the World will help you to discover latest news as well as you will get idea about whome to follow on twitter.
              The list shows which twitter people are being most mentioned currently. You can follow the link of the trending twitter user to go to the user profile or the search results on twitter. The rank of trending twitter people from the World is based on the number of tweets per 20K tweets coming from the World in timespan of right now to last 13 minutes.
              <br />
              See Also:<br/>
              <ul>
             <li><a href="/mapview/?cc=WORLD" title="Twitter Trending People Map of World">Twitter Trending People Map</a></li>
                <li><a href="/hashtags/?cc=WORLD" title="Twitter Trending Hashtags in World">Twitter Trending Hashtags</a></li>
                <li><a href="/cities/?cc=WORLD" title="Top Tweeting Cities in World">Top Tweeting Cities</a></li>
                <li><a href="/map/hashtags/">Realtime Hashtags Map</a></li>
                <li><a href="/countries/">Top Tweeting Countries</a></li>
                <li><a href="/map/">Top Tweeting Countries</a></li>
                <li><a href="http://www.wikitrends.net">Most Viewed Articles-Trending Topics on Wikipedia</a></li>
                </ul>
              </div>
              </div>
              </div>
          </div>
          <footer style="text-align:center;">
          © 2018 tweeplers.com | <A href="http://www.tweeplers.com/termsofuse/"> Terms of Use </A>|
        <a href="http://www.tweeplers.com/contact/">Contact</a>
    </footer>
    
        </div><!-- /.container -->
        
        <!-- jQuery (necessary for Bootstrap's JavaScript plugins) -->
        <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.12.4/jquery.min.js"></script>
        <script type="text/javascript" language="javascript">
            var cc='WORLD'
        </script>
        <script src="js/df.js"></script>
        <script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>
        <!-- Include all compiled plugins (below), or include individual files as needed -->
        <script src="bootstrap-3.3.7-dist/js/bootstrap.min.js"></script>
    
    </body>
    </html>
    



```python
# Extract Top Twitter Names
top_twitter_names = re.findall("@(.*?)<", response.text)
top_twitter_names
```




    ['realDonaldTrump',
     'YouTube',
     'AdamSKutner',
     'NissanUSA',
     'BTS_twt',
     'POTUS',
     'FoxNews',
     'CSEmelec',
     'JREast_official',
     'TeletonOficial',
     'krassenstein',
     'GOP',
     'LindseyGrahamSC',
     'davidhogg111',
     '10APG',
     'marcorubio',
     'PascoSheriff',
     'LauraLoomer',
     'dbongino',
     'OfficialLivePD',
     'tweeplers']




```python
# Select top 10 names
top_names = top_twitter_names[:10]
top_names
```




    ['realDonaldTrump',
     'YouTube',
     'AdamSKutner',
     'NissanUSA',
     'BTS_twt',
     'POTUS',
     'FoxNews',
     'CSEmelec',
     'JREast_official',
     'TeletonOficial']




```python
# Get tweets using rate limit Sleep function
tweets = []
for user in top_names:
    for status in tweepy.Cursor(api.search, q=user).items(500):
        # Serialize JSON response data
        _status = status._json
        _status["top_user"] = user
        tweets.append(_status)
    print(user)
```

    realDonaldTrump
    YouTube
    AdamSKutner
    NissanUSA
    BTS_twt


    Rate limit reached. Sleeping for: 853


    POTUS
    FoxNews
    CSEmelec
    JREast_official
    TeletonOficial



```python
# Verify length of tweet string is 10 top users X 500 tweets
print(len(tweets))
```

    5000



```python
# View first tweet
print(tweets[0])
```

    {'created_at': 'Sun Nov 11 02:24:06 +0000 2018', 'id': 1061444427804278785, 'id_str': '1061444427804278785', 'text': 'RT @jpuopolo: Wow! @JustinTrudeau was full on trolling @realDonaldTrump on this one!  \n\n#VeteransDay \n#RememberanceDay https://t.co/KsS9mKQ…', 'truncated': False, 'entities': {'hashtags': [{'text': 'VeteransDay', 'indices': [88, 100]}, {'text': 'RememberanceDay', 'indices': [102, 118]}], 'symbols': [], 'user_mentions': [{'screen_name': 'jpuopolo', 'name': 'Joseph Puopolo', 'id': 14443412, 'id_str': '14443412', 'indices': [3, 12]}, {'screen_name': 'JustinTrudeau', 'name': 'Justin Trudeau', 'id': 14260960, 'id_str': '14260960', 'indices': [19, 33]}, {'screen_name': 'realDonaldTrump', 'name': 'Donald J. Trump', 'id': 25073877, 'id_str': '25073877', 'indices': [55, 71]}], 'urls': []}, 'metadata': {'iso_language_code': 'en', 'result_type': 'recent'}, 'source': '<a href="http://twitter.com/download/iphone" rel="nofollow">Twitter for iPhone</a>', 'in_reply_to_status_id': None, 'in_reply_to_status_id_str': None, 'in_reply_to_user_id': None, 'in_reply_to_user_id_str': None, 'in_reply_to_screen_name': None, 'user': {'id': 744185307977768960, 'id_str': '744185307977768960', 'name': 'DeeAnnDay', 'screen_name': 'DebAma818', 'location': 'Texas, USA', 'description': '🖍PSteacher 20+yrs., NO GUNS in school!!! 🔊Voter, 🙋🏽\u200d♀️Yellow-dog Democrat, 🦅#Resist #FBR 🌊', 'url': None, 'entities': {'description': {'urls': []}}, 'protected': False, 'followers_count': 2071, 'friends_count': 2051, 'listed_count': 6, 'created_at': 'Sat Jun 18 15:09:38 +0000 2016', 'favourites_count': 60926, 'utc_offset': None, 'time_zone': None, 'geo_enabled': True, 'verified': False, 'statuses_count': 80365, 'lang': 'en', 'contributors_enabled': False, 'is_translator': False, 'is_translation_enabled': False, 'profile_background_color': 'F5F8FA', 'profile_background_image_url': None, 'profile_background_image_url_https': None, 'profile_background_tile': False, 'profile_image_url': 'http://pbs.twimg.com/profile_images/1060729821632827392/ZO-rPk31_normal.jpg', 'profile_image_url_https': 'https://pbs.twimg.com/profile_images/1060729821632827392/ZO-rPk31_normal.jpg', 'profile_banner_url': 'https://pbs.twimg.com/profile_banners/744185307977768960/1541734322', 'profile_link_color': '1DA1F2', 'profile_sidebar_border_color': 'C0DEED', 'profile_sidebar_fill_color': 'DDEEF6', 'profile_text_color': '333333', 'profile_use_background_image': True, 'has_extended_profile': False, 'default_profile': True, 'default_profile_image': False, 'following': False, 'follow_request_sent': False, 'notifications': False, 'translator_type': 'none'}, 'geo': None, 'coordinates': None, 'place': None, 'contributors': None, 'retweeted_status': {'created_at': 'Sun Nov 11 01:34:19 +0000 2018', 'id': 1061431896381501440, 'id_str': '1061431896381501440', 'text': 'Wow! @JustinTrudeau was full on trolling @realDonaldTrump on this one!  \n\n#VeteransDay \n#RememberanceDay https://t.co/KsS9mKQYX8', 'truncated': False, 'entities': {'hashtags': [{'text': 'VeteransDay', 'indices': [74, 86]}, {'text': 'RememberanceDay', 'indices': [88, 104]}], 'symbols': [], 'user_mentions': [{'screen_name': 'JustinTrudeau', 'name': 'Justin Trudeau', 'id': 14260960, 'id_str': '14260960', 'indices': [5, 19]}, {'screen_name': 'realDonaldTrump', 'name': 'Donald J. Trump', 'id': 25073877, 'id_str': '25073877', 'indices': [41, 57]}], 'urls': [], 'media': [{'id': 1061431750507749376, 'id_str': '1061431750507749376', 'indices': [105, 128], 'media_url': 'http://pbs.twimg.com/media/Drr2japV4AAqDu9.jpg', 'media_url_https': 'https://pbs.twimg.com/media/Drr2japV4AAqDu9.jpg', 'url': 'https://t.co/KsS9mKQYX8', 'display_url': 'pic.twitter.com/KsS9mKQYX8', 'expanded_url': 'https://twitter.com/jpuopolo/status/1061431896381501440/photo/1', 'type': 'photo', 'sizes': {'large': {'w': 720, 'h': 840, 'resize': 'fit'}, 'thumb': {'w': 150, 'h': 150, 'resize': 'crop'}, 'small': {'w': 583, 'h': 680, 'resize': 'fit'}, 'medium': {'w': 720, 'h': 840, 'resize': 'fit'}}}]}, 'extended_entities': {'media': [{'id': 1061431750507749376, 'id_str': '1061431750507749376', 'indices': [105, 128], 'media_url': 'http://pbs.twimg.com/media/Drr2japV4AAqDu9.jpg', 'media_url_https': 'https://pbs.twimg.com/media/Drr2japV4AAqDu9.jpg', 'url': 'https://t.co/KsS9mKQYX8', 'display_url': 'pic.twitter.com/KsS9mKQYX8', 'expanded_url': 'https://twitter.com/jpuopolo/status/1061431896381501440/photo/1', 'type': 'photo', 'sizes': {'large': {'w': 720, 'h': 840, 'resize': 'fit'}, 'thumb': {'w': 150, 'h': 150, 'resize': 'crop'}, 'small': {'w': 583, 'h': 680, 'resize': 'fit'}, 'medium': {'w': 720, 'h': 840, 'resize': 'fit'}}}]}, 'metadata': {'iso_language_code': 'en', 'result_type': 'recent'}, 'source': '<a href="http://twitter.com" rel="nofollow">Twitter Web Client</a>', 'in_reply_to_status_id': None, 'in_reply_to_status_id_str': None, 'in_reply_to_user_id': None, 'in_reply_to_user_id_str': None, 'in_reply_to_screen_name': None, 'user': {'id': 14443412, 'id_str': '14443412', 'name': 'Joseph Puopolo', 'screen_name': 'jpuopolo', 'location': 'Waterloo/San Francisco', 'description': 'Founder @Printchomp, among others. Husband, father, farmer, digital renaissance man. Man constantly in search of a new challenge! #NoHate', 'url': 'https://t.co/kWzvmWgsfx', 'entities': {'url': {'urls': [{'url': 'https://t.co/kWzvmWgsfx', 'expanded_url': 'http://www.jpuopolo.com', 'display_url': 'jpuopolo.com', 'indices': [0, 23]}]}, 'description': {'urls': []}}, 'protected': False, 'followers_count': 62057, 'friends_count': 49313, 'listed_count': 685, 'created_at': 'Sat Apr 19 14:11:01 +0000 2008', 'favourites_count': 1387, 'utc_offset': None, 'time_zone': None, 'geo_enabled': True, 'verified': False, 'statuses_count': 73769, 'lang': 'en', 'contributors_enabled': False, 'is_translator': False, 'is_translation_enabled': False, 'profile_background_color': '1A1B1F', 'profile_background_image_url': 'http://abs.twimg.com/images/themes/theme9/bg.gif', 'profile_background_image_url_https': 'https://abs.twimg.com/images/themes/theme9/bg.gif', 'profile_background_tile': False, 'profile_image_url': 'http://pbs.twimg.com/profile_images/998573613455360000/AZmzIXV7_normal.jpg', 'profile_image_url_https': 'https://pbs.twimg.com/profile_images/998573613455360000/AZmzIXV7_normal.jpg', 'profile_banner_url': 'https://pbs.twimg.com/profile_banners/14443412/1526913479', 'profile_link_color': '2FC2EF', 'profile_sidebar_border_color': '181A1E', 'profile_sidebar_fill_color': '252429', 'profile_text_color': '666666', 'profile_use_background_image': True, 'has_extended_profile': True, 'default_profile': False, 'default_profile_image': False, 'following': False, 'follow_request_sent': False, 'notifications': False, 'translator_type': 'none'}, 'geo': None, 'coordinates': None, 'place': None, 'contributors': None, 'is_quote_status': False, 'retweet_count': 50, 'favorite_count': 93, 'favorited': False, 'retweeted': False, 'possibly_sensitive': False, 'lang': 'en'}, 'is_quote_status': False, 'retweet_count': 50, 'favorite_count': 0, 'favorited': False, 'retweeted': False, 'lang': 'en', 'top_user': 'realDonaldTrump'}



```python
# Function to get Sentiment of Tweet texts
def get_sentiment(tweet_text):
    results = analyzer.polarity_scores(tweet_text)
    return results
```


```python
# Create Dataframe with full tweet data
df = pd.DataFrame(tweets)
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 5000 entries, 0 to 4999
    Data columns (total 32 columns):
    contributors                 0 non-null object
    coordinates                  275 non-null object
    created_at                   5000 non-null object
    entities                     5000 non-null object
    extended_entities            403 non-null object
    favorite_count               5000 non-null int64
    favorited                    5000 non-null bool
    geo                          275 non-null object
    id                           5000 non-null int64
    id_str                       5000 non-null object
    in_reply_to_screen_name      994 non-null object
    in_reply_to_status_id        731 non-null float64
    in_reply_to_status_id_str    731 non-null object
    in_reply_to_user_id          994 non-null float64
    in_reply_to_user_id_str      994 non-null object
    is_quote_status              5000 non-null bool
    lang                         5000 non-null object
    metadata                     5000 non-null object
    place                        403 non-null object
    possibly_sensitive           1596 non-null object
    quoted_status                105 non-null object
    quoted_status_id             448 non-null float64
    quoted_status_id_str         448 non-null object
    retweet_count                5000 non-null int64
    retweeted                    5000 non-null bool
    retweeted_status             2480 non-null object
    source                       5000 non-null object
    text                         5000 non-null object
    top_user                     5000 non-null object
    truncated                    5000 non-null bool
    user                         5000 non-null object
    withheld_in_countries        1 non-null object
    dtypes: bool(4), float64(3), int64(3), object(22)
    memory usage: 1.1+ MB



```python
# Initial dataframe of tweets
df
```




```python
# Add distinct columns for added sentiment information % easy access of desired data
df['sentiment'] = df['text'].map(get_sentiment)
df['pos'] = df['sentiment'].map(lambda x: x.get('pos'))
df['neg'] = df['sentiment'].map(lambda x: x.get('neg'))
df['neu'] = df['sentiment'].map(lambda x: x.get('neu'))
df['comp'] = df['sentiment'].map(lambda x: x.get('compound'))
df['user_screen_name'] = df['user'].map(lambda x: x.get('screen_name'))
df['hashtags'] = df['entities'].map(lambda x: [j.get('text') for j in x.get('hashtags')]).apply(', '.join)
df['friends_count'] = df['user'].map(lambda x: x.get('friends_count')) 
df['tweet_count'] = df.index.values + 1
df['profile_url'] = df['user'].map(lambda x: x.get('profile_image_url'))
df['language'] = df['user'].map(lambda x: x.get('lang'))
df['location'] = df['user'].map(lambda x: x.get('location'))
df['profile_url'] = df['user'].map(lambda x: x.get('profile_image_url'))
df['statuses_count'] = df['user'].map(lambda x: x.get('statuses_count'))
df['user_created_at'] = df['user'].map(lambda x: x.get('created_at'))
df['verified'] = df['user'].map(lambda x: x.get('verified'))
df['user_name'] = df['user'].map(lambda x: x.get('name'))
df['description'] = df['user'].map(lambda x: x.get('description'))
df['followers_count'] = df['user'].map(lambda x: x.get('followers_count'))
df['listed'] = df['user'].map(lambda x: x.get('listed'))
df['favorites_count'] = df['user'].map(lambda x: x.get('favourites_count'))
df['notifications'] = df['user'].map(lambda x: x.get('notifications'))
df['following'] = df['user'].map(lambda x: x.get('following'))
```


```python
# Apply enhanced dataframe function with Seaborn
import seaborn as sns
cm = sns.light_palette("orange", as_cmap=True)
df.style.background_gradient(cmap=cm)
df
```




```python
# Verify dataframe info
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 5000 entries, 0 to 4999
    Data columns (total 54 columns):
    contributors                 0 non-null object
    coordinates                  275 non-null object
    created_at                   5000 non-null object
    entities                     5000 non-null object
    extended_entities            403 non-null object
    favorite_count               5000 non-null int64
    favorited                    5000 non-null bool
    geo                          275 non-null object
    id                           5000 non-null int64
    id_str                       5000 non-null object
    in_reply_to_screen_name      994 non-null object
    in_reply_to_status_id        731 non-null float64
    in_reply_to_status_id_str    731 non-null object
    in_reply_to_user_id          994 non-null float64
    in_reply_to_user_id_str      994 non-null object
    is_quote_status              5000 non-null bool
    lang                         5000 non-null object
    metadata                     5000 non-null object
    place                        403 non-null object
    possibly_sensitive           1596 non-null object
    quoted_status                105 non-null object
    quoted_status_id             448 non-null float64
    quoted_status_id_str         448 non-null object
    retweet_count                5000 non-null int64
    retweeted                    5000 non-null bool
    retweeted_status             2480 non-null object
    source                       5000 non-null object
    text                         5000 non-null object
    top_user                     5000 non-null object
    truncated                    5000 non-null bool
    user                         5000 non-null object
    withheld_in_countries        1 non-null object
    sentiment                    5000 non-null object
    pos                          5000 non-null float64
    neg                          5000 non-null float64
    neu                          5000 non-null float64
    comp                         5000 non-null float64
    user_screen_name             5000 non-null object
    hashtags                     5000 non-null object
    friends_count                5000 non-null int64
    tweet_count                  5000 non-null int64
    profile_url                  5000 non-null object
    language                     5000 non-null object
    location                     5000 non-null object
    statuses_count               5000 non-null int64
    user_created_at              5000 non-null object
    verified                     5000 non-null bool
    user_name                    5000 non-null object
    description                  5000 non-null object
    followers_count              5000 non-null int64
    listed                       0 non-null object
    favorites_count              5000 non-null int64
    notifications                5000 non-null bool
    following                    5000 non-null bool
    dtypes: bool(7), float64(7), int64(8), object(32)
    memory usage: 1.8+ MB



```python
df.to_csv("Verna_Twitter_Data.csv")
```

### Data file is Used in Presentation Jupyter Notebook:
###       Project-Twitter-Analysis-Presentation.ipynb
