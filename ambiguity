#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
Created on Mon Feb 17 01:16:32 2020

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
import preprocessor as p
import preprocessor as p
from nltk.corpus import stopwords
from nltk.tokenize import word_tokenize
import pandas as pd
import numpy as np 
import re as reg
from nltk.stem import PorterStemmer
                                     
                                             

COLS = ['id','Original text','Clean text','create_at']
# files to store tweets
TheFile='/Users/salwaalthobaity/Downloads/Semester#2/WebScience/Classes_Files/disgust_tweets.csv' 
File_happy_tweet='/Users/salwaalthobaity/Downloads/Semester#2/WebScience/Classes_Files/temporal_happy_tweets.csv'
File_sadness_tweet='/Users/salwaalthobaity/Downloads/Semester#2/WebScience/Classes_Files/temporal_sad_tweets.csv'
File_anger_tweet='/Users/salwaalthobaity/Downloads/Semester#2/WebScience/Classes_Files/temporal_anger_tweets.csv'
File_disgust_tweet='/Users/salwaalthobaity/Downloads/Semester#2/WebScience/Classes_Files/cleaned_disgust_tweets.csv'
File_fear_tweet='/Users/salwaalthobaity/Downloads/Semester#2/WebScience/Classes_Files/temporal_fear_tweets.csv'
File_surprise_tweet='/Users/salwaalthobaity/Downloads/Semester#2/WebScience/Classes_Files/temporal_surprise_tweets.csv'
filepath = "/Users/salwaalthobaity/Downloads/Semester#2/WebScience/NRC-Emotion-Lexicon/NRC-Emotion-Lexicon-v0.92/NRC-Emotion-Lexicon-Wordlevel-v0.92.txt"
emolex_df = pd.read_csv(filepath,  names=["word", "emotion", "association"], skiprows=45, sep='\t')
emolex_words = emolex_df.pivot(index='word', columns='emotion', values='association').reset_index()
emolex_words=emolex_words.replace(np.nan, 0)
#print(emolex_words.head())
filepath2 = "/Users/salwaalthobaity/Downloads/Semester#2/WebScience/NRC-Hashtag-Emotion-Lexicon/NRC-Hashtag-Emotion-Lexicon-v0.2/NRC-Hashtag-Emotion-Lexicon-v0.2.txt"
emolex_df_hash = pd.read_csv(filepath2,  names=["word", "emotion", "association"], skiprows=45, sep='\t')
emolex_words_hash = emolex_df_hash.pivot(index='emotion', columns='word', values='association').reset_index()
emolex_words_hash=emolex_words_hash.replace(np.nan, 0)
#http://jonathansoma.com/lede/algorithms-2017/classes/more-text-analysis/nrc-emotional-lexicon/



#lists of emoticons for each class 

emoticons_joy=[u"\U0001F600",u"\U0001F603",u"\U0001F604",u"\U0001F601",u"\U0001F606",u"\U0001F605",u"\U0001F923",u"\U0001F602",u"\U0001F970",u"\U0001F60D",u"\U0001F493"]
emoticons_sadness=[u"\U0001F97A",u"\U0001F61E",u"\U0001F62D",u"\U0001F625"]
emoticons_fear=[u"\U0001F628",u"\U0001F630",u"\U0001F631",u"\U0001F629"]
emoticons_disgust=[u"\U0001F611",u"\U0001F974",u"\U0001F92E"]
emoticons_surprise=[u"\U0001F92D",u"\U0001F914",u"\U0001F632",u"\U0001F633"]
emoticons_anger=[u"\U0001F624",u"\U0001F621",u"\U0001F620",u"\U0001F92C",u"\U0001F608"]



def Categorize_tweet(original,clean,hashtags,ids,createat):
    
    count_sad=0
    count_happy=0
    count_fear=0
    count_disgust=0
    count_surprise=0
    count_anger=0
    # counts are used to keep track of each class score 
    dic={count_sad:"count_sad",count_happy:"count_happy",count_fear:"count_fear",count_disgust:"count_disgust",count_surprise:"count_surprise",count_anger:"count_anger"}    
    
    word_tokens=word_tokenize(str(clean))
    
    for w in word_tokens:
       
        if w in emolex_words.values:
            # Check if the word in NRC word level lexicon
            #print(emolex_words[emolex_words.word ==w]['joy'])
            i=emolex_words[emolex_words.word ==w]
            count_sad=count_sad+i.iloc[0]['sadness'] # add the association score to the count 
            count_happy=count_happy+i.iloc[0]['joy']
            count_fear=count_fear+i.iloc[0]['fear']
            count_disgust=count_disgust+i.iloc[0]['disgust']
            count_surprise=count_surprise+i.iloc[0]['surprise']
            count_anger=count_anger+i.iloc[0]['anger']
        
        # check if the token in the emoticons lists 
        if w in emoticons_joy :
            
            count_happy+=1          # ass 1 to the count if ths emoticon belong to this class
        if w in emoticons_sadness:
            count_sad+=1
        if w in emoticons_anger:
            count_anger+=1
        if w in emoticons_surprise:
            count_surprise+=1
        if w in emoticons_disgust:
            count_disgust+=1
        if w in emoticons_fear:
            count_fear+=1
        
       
    #check if the hashtag in NRC emotion hashtag lexicon   
    if hashtags !='':
        hashtagslist=str(hashtags).lower().split (",")# read the hashtags from the file , and since they stored as somma seperated strings we need to split them
        hashtagslistnew=[]
        for i in hashtagslist:
            hashtagslistnew.append("#"+i)
        for h in hashtagslistnew: 
            if h in emolex_words_hash.values:
                #print (h)
                #print(emolex_words_hash[emolex_words_hash.emotion ==h])
                i=emolex_words_hash[emolex_words_hash.emotion ==h]
                count_sad=count_sad+i.iloc[0]['sadness'] # add the association score to the count
                count_happy=count_happy+i.iloc[0]['joy']
                #print(i.iloc[0]['joy'])
                count_fear=count_fear+i.iloc[0]['fear']
                count_disgust=count_disgust+i.iloc[0]['disgust']
                count_surprise=count_surprise+i.iloc[0]['surprise']
                count_anger=count_anger+i.iloc[0]['anger']
            
    clean = re.sub(r'[^\x00-\x7F]+',' ', str(clean))    
    #print(clean,count_sad,count_happy,count_fear,count_disgust,count_surprise,count_anger)
    dic={count_sad:"count_sad",count_happy:"count_happy",count_fear:"count_fear",count_disgust:"count_disgust",count_surprise:"count_surprise",count_anger:"count_anger"} 
    #print(dic.get(max(dic)))
    name=dic.get(max(dic))
    list2=[]
    list2.append(ids)
    list2.append(original)
    list2.append(clean)
    list2.append(createat)
    
    single_tweet_df = pd.DataFrame([list2],columns=COLS)
    if max(dic) !=0 :
        #print("add this tweet") # check the largest counter to add the tweet to the corresponding class
        if name == 'count_happy':
            csvFile = open(File_happy_tweet, 'a' ,encoding='utf-8')
            single_tweet_df.to_csv(csvFile, mode='a',header=COLS, encoding="utf-8")
            csvFile.close()
        elif name == 'count_sad':
            csvFile = open(File_sadness_tweet, 'a' ,encoding='utf-8')
            single_tweet_df.to_csv(csvFile, mode='a',header=COLS,  encoding="utf-8")
            csvFile.close()
        elif name == 'count_fear':
            csvFile = open(File_fear_tweet, 'a' ,encoding='utf-8')
            single_tweet_df.to_csv(csvFile, mode='a',header=COLS,  encoding="utf-8")
            csvFile.close()
        elif name == 'count_anger':
            csvFile = open(File_anger_tweet, 'a' ,encoding='utf-8')
            single_tweet_df.to_csv(csvFile, mode='a',header=COLS,  encoding="utf-8")
            csvFile.close()
        elif name == 'count_disgust':
            csvFile = open(File_disgust_tweet, 'a' ,encoding='utf-8')
            single_tweet_df.to_csv(csvFile, mode='a',header=COLS, encoding="utf-8")
            csvFile.close()
        elif name == 'count_surprise':
            csvFile = open(File_surprise_tweet, 'a' ,encoding='utf-8')
            single_tweet_df.to_csv(csvFile, mode='a',header=COLS,  encoding="utf-8")
            csvFile.close()
        
    
  
 
    
df = pd.read_csv(TheFile, header=0)


for index, row in df.iterrows(): # iterate over each tweet and its attributes
    Categorize_tweet(row["original_text"],row["clean_text"],row["hashtags"],row['id'], row['created_at'])

  
    
