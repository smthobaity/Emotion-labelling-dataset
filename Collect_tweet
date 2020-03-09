#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
Created on Thu Feb 20 14:31:12 2020

@author: salwaalthobaity
"""




from __future__ import print_function
import tweepy
import emoji
import json

import os
import pandas as pd
import tweepy
import re
import string
from textblob import TextBlob
import re as reg    
import preprocessor as p
from nltk.corpus import stopwords
from nltk.tokenize import word_tokenize
from nltk.stem import WordNetLemmatizer 
                                          
                                             
                                            
# list of hashtags for each class                                     
WORDS_Happy_Class = '#happy OR #HAPPY OR #joy OR #JOY OR #love OR #LOVE  OR  #felicitous  OR  #glad OR #Delighted OR #Pleased'
WORDS_Surprise_Class='#surprisal OR #surprise OR #Astonished OR #surprises OR #surprised'
WORDS_Angry_Class='#Angry OR #ANGRY OR  #angry OR #furious OR #Annoyed OR #Frustrated'
WORDS_Sad_Class='#Depressed OR #Miserable OR #Sad OR #Gloomy OR #distressing OR #lamentable OR #pitiful OR #sorry OR #deplorable OR #sadness'
WORDS_Disgust_Class='#nauseated OR #Disgust OR #disgusted OR #distateful OR #disgusting'
WORDS_Fear_Class='#Afraid OR #Alarmed OR #scared OR #fear OR #scare OR #nightmare OR #frighten OR #frightened OR #terrify OR #terrified'
KeyWords=WORDS_Surprise_Class
#KeyWords='#Afraid OR #Alarmed OR #Angry OR #Frustrated OR #Depressed OR #Tense OR #Annoyed OR #Miserable OR #Sad OR #Gloomy OR #Excited OR #Amused OR #Happy OR #Delighted OR #Glad OR #Pleased OR #Tired OR #Sleepy OR #Calm \
#OR #Satisfied OR #Bored OR #Droopy OR #Relaxed OR #Content OR #Astonished OR #serene OR #angry OR #surprised OR #scared OR #disgust OR #joy OR #love OR #fear'
#/Users/salwaalthobaity/Downloads/Semester#2/WebScience/Classes_Files
   
CONSUMER_KEY = "dKFDpNcaQngEcGZOpWLg7IBzO"
CONSUMER_SECRET = "i8n2pwFezJRTzcr2WXmsRYBWrkNgURQroPgGWMhaKVI7v5iXWX"
ACCESS_TOKEN = "1030579915618496518-CjVL3irVefTf2yGqcGgHTsROXpmIyC"
ACCESS_TOKEN_SECRET = "r7ku2mf94WyZSHiUGTndzLCP9OpYeRQcJ2Gwsl9yQVLJz"

auth = tweepy.OAuthHandler(CONSUMER_KEY, CONSUMER_SECRET)
auth.set_access_token(ACCESS_TOKEN, ACCESS_TOKEN_SECRET)

api = tweepy.API(auth, wait_on_rate_limit=True)

#columns of the csv file
COLS = ['id', 'created_at', 'source', 'original_text','clean_text', 'lang','hashtags']
TheFile='/Users/salwaalthobaity/Downloads/Semester#2/WebScience/Classes_Files/surprise_tweets.csv' 


lemmatizer = WordNetLemmatizer() 



def extra_processing(tweet):
    stop_words = set(stopwords.words('english'))
   
    
    # remove the colon :
    tweet = re.sub(r':', '', tweet)
    tweet = re.sub(r'‚Ä¶', '', tweet)
   
   
     # Dealing with negation form
    tweet = tweet.replace(r"(can't|cannot)", 'can not')
    tweet = tweet.replace(r"n't", ' not')
    tweet = tweet.lower()
    pattern = reg.compile(r"(.)\1{2,}", reg.DOTALL)
    tweet = pattern.sub( r"\1",tweet)
     # tokenize the text into words
    tokens = word_tokenize(tweet)
 
 
    #to remove stop words and only store the filtered words
    clean_tweet = []
    
    for token in tokens:
        #to remove stop words , punctuation 
        if token not in stop_words and token not in string.punctuation:
            token=lemmatizer.lemmatize(token) # keep the root of the word
            clean_tweet.append(token)
    return ' '.join(clean_tweet)
    
 
def write_tweets(keyword, file):
    
    listids=[]# keep track of tweets' ids
    listtext=[] # keep track of tweets' text
    
    WriteHeadedr = 0
   
   
    df = pd.DataFrame(columns=COLS)
    # define dataframe to store the tweets
    # Now using api search we will search for the defined keywords and iterate over 60 pages, for each page we will iterate over 40 tweets
    for page in tweepy.Cursor(api.search, q=keyword,
                               count=40,include_rts=False).pages(60):
        print("Inside page")
        for status in page:
            #iterate over status/tweets
            status_fields = []
            # define a list to store the most important attribute of each tweet
            status = status._json
            # loading json file
 
            ## only english tweet willl be used
            if status['lang'] != 'en':
                continue
 
           # this function will remove any link in the text of the tweet, RT, @ symbols
            clean_text = p.clean(status['text'])
 
            #extra processing 
            clean_text=extra_processing(clean_text)
 
           # new entry append
            status_fields = [status['id'], status['created_at'],
                          status['source'], status['text'],clean_text,status['lang']]
            
            
            # hashtagas and mentiones are saved using comma separted
            hashtags = ", ".join([hashtag_item['text'] for hashtag_item in status['entities']['hashtags']])
            status_fields.append(hashtags)
 
           
 
            single_tweet_df = pd.DataFrame([ status_fields],columns=COLS)
           # df = df.append(single_tweet_df, ignore_index=True)
            if status['id'] not in listids and status['text'] not in listtext:# try to store the unique tweets only
                csvFile = open(file, 'a' ,encoding='utf-8')
                if WriteHeadedr == 0:
                    single_tweet_df.to_csv(csvFile, mode='a', index=False, encoding="utf-8")
                    WriteHeadedr=WriteHeadedr+1
                else:
                    single_tweet_df.to_csv(csvFile, mode='a',header=False, index=False, encoding="utf-8")
                    csvFile.close()
            listids.append(status['id'])
            listtext.append(status['text'])
            


write_tweets(KeyWords,  TheFile)# call write function to start collecting data


