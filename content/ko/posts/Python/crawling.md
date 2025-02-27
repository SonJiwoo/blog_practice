---
collapsible: false
date: "2022-01-08T10:09:56+09:00"
description: 유튜브 크롤링 Youtube Crawling
draft: false
title: 유튜브 크롤링 
weight: 1
---

# Youtube Crawling

## 1. 전체 함수
(유의사항) `2. 개별 함수`의 함수들을 순차적으로 정의한 후에 전체 함수(`youtube_crawling`)를 정의해야 실행이 된다.
```python
def youtube_crawling(channel_name, csv_name, export=True, verbose=True):
    ###########################################
    # Check
    ###########################################
    # Today date to string
    today_str = make_date_str()

    # Check if today's crawling already exists
    if os.path.isfile('result\\\\' + csv_name + '_' + today_str + '.csv'):
        print(csv_name + ' 이미 오늘자 파일이 존재합니다.')
        return

    ###########################################
    # Crawling
    ###########################################
    # Selenium Option Setting
    options = set_selenium_option()

    # Extract (1) Video Links, (2) Subscribers by crawling
    today_video_link_list, subscriber = youtube_video_list_subscriber(channel_name, options)

    # Load previously crawled data
    previous_df = previous_csv(csv_name)

    # Update crawling data
    df = update_video_info(previous_df, today_video_link_list, subscriber)

    ###########################################
    # Export & Print
    ###########################################
    # Export
    if export:
        df.to_csv('result/' + csv_name + '_' + today_str + '.csv', index=False)
    
    # Print Log
    latest_date = get_latest_date(csv_name)
    if verbose:
        new_video_links = set(today_video_link_list) - set(previous_df['video_url'])
        deleted_video_links = set(previous_df['video_url']) - set(today_video_link_list)
        print('오늘({}) 크롤링 영상 개수: {}개'.format(today_str, len(today_video_link_list)))
        print('최근({}) 크롤링 영상 개수: {}개 (전체 {}행)'.format(latest_date, len(np.unique(previous_df['title'])), previous_df.shape[0]))
        print('새로 업로드 된 영상 개수: {}개'.format(len(new_video_links)))
        print('삭제된 영상 개수: {}개'.format(len(deleted_video_links)))
        print(date.today(), '영상정보 저장 완료')

    ###########################################
    # Delete Previous File
    ###########################################    
    if export:
        delete_previous_csv(csv_name, latest_date)
```

## 2. 개별 함수

### Step 1. Library Import
{{<expand "Code">}}
```python
import os
import time
import pandas as pd
import numpy as np
import re
import send2trash
from datetime import date, datetime

from bs4 import BeautifulSoup
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.common.by import By

from pytube import YouTube

import warnings
warnings.filterwarnings(action='ignore')
```
{{</expand>}}

### Step 2. Selenium Option
{{<expand "Code">}}
```python
def set_selenium_option():
    global options
    # Setting
    os.chdir('C:\\\\Users\\\\bunga\\\\Desktop\\\\python\\\\youtube')
    options = webdriver.ChromeOptions()
    user_agent = 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36'

    # Option Control
    options.add_argument('user-agent=' + user_agent)
    options.add_argument('headless')  # 창을 띄우지 않습니다
    options.add_argument('window-size=1920x1080')
    options.add_argument('disable-gpu')
    options.add_argument('disable-infobars')
    options.add_argument('--disable-extensions')
    options.add_argument('--mute-audio')
    options.add_argument('--blink-settings=imagesEnabled=false') # 브라우저에서 이미지 로딩을 하지 않습니다.
    options.add_argument('incognito') # 시크릿모드의 브라우저가 실행됩니다.
    options.add_argument('--start-maximized')

    return options
```
{{</expand>}}

### Step 3. Youtube 영상 리스트 및 구독자수 크롤링
{{<expand "Code">}}
```python
def scrollToEnd():
    prev_height = driver.execute_script("return document.documentElement.scrollHeight")
    # 웹페이지 맨 아래까지 무한 스크롤
    while True:
        # 스크롤을 화면 가장 아래로 내린다
        element = driver.find_element(By.TAG_NAME, 'body')
        element.send_keys(Keys.END)

        # 페이지 로딩 대기
        time.sleep(2)

        # 현재 문서 높이를 가져와서 저장
        curr_height = driver.execute_script("return document.documentElement.scrollHeight")

        if(curr_height == prev_height):
            break
        else:
            prev_height = driver.execute_script("return document.documentElement.scrollHeight")
```
{{</expand>}}

{{<expand "Code">}}
```python
def youtube_video_list_subscriber(channel_name, selenium_option):
    # Channel URL
    channel_url = 'https://www.youtube.com/' + channel_name + '/videos'
    
    # 페이지 탐색
    global driver
    driver = webdriver.Chrome('chromedriver.exe', options=selenium_option)
    driver.get(channel_url)
    driver.switch_to.window(driver.window_handles[-1])
    scrollToEnd()
    
    # 페이지 정보 추출
    html = driver.page_source
    soup = BeautifulSoup(html, 'lxml')
    
    # Driver 닫기
    driver.close()
    
    # 영상 링크 추출
    sample_list = soup.find_all('a', class_='yt-simple-endpoint focus-on-expand style-scope ytd-rich-grid-media')
    video_link_list = []
    for sample in sample_list:
        video_link = sample.get_attribute_list('href')[0]
        video_link_list.append('https://youtube.com'+video_link)

    # 구독자 수 추출
    subscriber = soup.find_all(id='subscriber-count')[0].text
    scale = subscriber[-2:-1]
    if scale == '만':
        subscriber = re.sub(r'[^0-9.]', '', subscriber)
        subscriber = np.float_(subscriber)*10000
    elif scale == '천':
        subscriber = re.sub(r'[^0-9.]', '', subscriber)
        subscriber = np.float_(subscriber)*1000
    else:
        subscriber = subscriber[:-1]
    subscriber = int(np.round(subscriber))
    
    return video_link_list, subscriber
```
{{</expand>}}

### Step 4. 날짜 변수 만들기
{{<expand "Code">}}
```python
def make_date_str():
    # 날짜변수 제작
    today = date.today().year*10000 + date.today().month*100 + date.today().day
    today_str = str(today)
    yesterday = today-1
    yesterday_str = str(yesterday)
    return today_str, yesterday_str
```
{{</expand>}}

{{<expand "Code">}}    
```python
def get_latest_date(csv_name):
    try:
        file_list = [filename for filename in os.listdir(path='result\\\\') if filename.startswith(csv_name)]
        date_list = [int(filename[-12:-4]) for filename in file_list]
        latest_date = np.max(date_list)
        return str(latest_date)
    except:
        return 'X'
```
{{</expand>}}

### Step 5. 이전일자 csv 불러오기
{{<expand "Code">}}
```python
def previous_csv(csv_name):
    # 최근 csv 불러오기 (없으면 빈 데이터프레임 만들기)
    try: 
        latest_date = get_latest_date(csv_name)
        latest_filename = csv_name + '_'+ latest_date + '.csv'
        df = pd.read_csv('result/' + latest_filename)
    except:
        print('이전 파일이 없습니다.')
        df_columns=['crawl_datetime', 'channel', 'subscriber', 'title', 'length', 'views', 'publish_date',
                    'video_url','thumbnail_url', 'keywords', 'description']
        df = pd.DataFrame(columns=df_columns)

    return df
```
{{</expand>}}

### Step 6. Pytube 활용하기
제목, 영상길이, 게시자, 게시날짜, 조회수, 키워드, 설명, 썸네일 등의 정보를 간단하게 얻을 수 있다. pytube 중에서 Channel이라는 class가 있는데, 2023년 1월 9일 기준으로는 주요 함수(ex. 업로드 영상들의 URL 반환하는 함수)들에서 empty list만 리턴하는 문제가 있다. 해당 문제가 해결이 된다면, `Step3. Youtube 영상 리스트 크롤링` 과정이 단순화될 수 있다. 

{{<expand "Code">}}
```python
def update_video_info(df, video_link_list, subscriber, verbose=True):
    print('--------------------------------------------------------------------------')
    today_datetime = datetime.today()
    for idx, url in enumerate(video_link_list):
        # 유튜브 정보 불러오기
        youtube = YouTube(url)

        # 전체 영상 수 
        if idx==0:
            print('{} / 영상: {}개'.format(youtube.author, len(video_link_list)))

        # 개별 영상 정보
        new = [today_datetime, youtube.author, subscriber, youtube.title, youtube.length, youtube.views, youtube.publish_date.date(),
               youtube.watch_url, youtube.thumbnail_url, youtube.keywords, youtube.description]
        df_new = pd.DataFrame([new], index=[idx], columns=df.columns)
        df = pd.concat([df, df_new])

        # 진행상황 체크
        if verbose and (idx+1)%10==0:
            print('{}번째 영상정보 정리 완료!'.format(idx+1))
    print('\n')
    return df
```
{{</expand>}}

{{<expand "All arguments">}}
```python
# example URL
url = 'https://www.youtube.com/watch?v=Ktw22y8VFHs'
youtube = YouTube(url)

# All arguments
youtube.age_restricted
youtube.allow_oauth_cache
youtube.author
youtube.bypass_age_gate
youtube.caption_tracks
youtube.captions
youtube.channel_id
youtube.channel_url
youtube.check_availability
youtube.description
youtube.embed_html
youtube.embed_url
youtube.fmt_streams
youtube.from_id
youtube.initial_data
youtube.js
youtube.js_url
youtube.keywords
youtube.length
youtube.metadata
youtube.publish_date
youtube.rating
youtube.register_on_complete_callback
youtube.register_on_progress_callback
youtube.stream_monostate
youtube.streaming_data
youtube.streams
youtube.thumbnail_url
youtube.title
youtube.use_oauth
youtube.vid_info
youtube.video_id
youtube.views
youtube.watch_html
youtube.watch_url
```
{{</expand>}}

### Step 7. 이전 csv 지우기
{{<expand "Code">}}
```python
def delete_previous_csv(csv_name, latest_date):
    if latest_date != 'X': # 이전 파일이 없는 경우
        latest_filename = csv_name + '_'+ latest_date + '.csv'
        send2trash.send2trash('result/' + latest_filename)
```
{{</expand>}}

## 3. Example
```python
youtube_crawling(channel_name = '@junsooham', csv_name='hamjunsoo')
```

```result
이전 파일이 없습니다.
--------------------------------------------------------------------------
JUNSOO 함준수 / 영상: 343개
10번째 영상정보 정리 완료!
20번째 영상정보 정리 완료!
30번째 영상정보 정리 완료!
40번째 영상정보 정리 완료!
50번째 영상정보 정리 완료!
60번째 영상정보 정리 완료!
70번째 영상정보 정리 완료!
80번째 영상정보 정리 완료!
90번째 영상정보 정리 완료!
100번째 영상정보 정리 완료!
110번째 영상정보 정리 완료!
120번째 영상정보 정리 완료!
130번째 영상정보 정리 완료!
140번째 영상정보 정리 완료!
150번째 영상정보 정리 완료!
160번째 영상정보 정리 완료!
170번째 영상정보 정리 완료!
180번째 영상정보 정리 완료!
190번째 영상정보 정리 완료!
200번째 영상정보 정리 완료!
210번째 영상정보 정리 완료!
220번째 영상정보 정리 완료!
230번째 영상정보 정리 완료!
240번째 영상정보 정리 완료!
250번째 영상정보 정리 완료!
260번째 영상정보 정리 완료!
270번째 영상정보 정리 완료!
280번째 영상정보 정리 완료!
290번째 영상정보 정리 완료!
300번째 영상정보 정리 완료!
310번째 영상정보 정리 완료!
320번째 영상정보 정리 완료!
330번째 영상정보 정리 완료!
340번째 영상정보 정리 완료!


오늘(20230109) 크롤링 영상 개수: 343개
어제(20230108) 크롤링 영상 개수: 0개 (전체 0행)
새로 업로드 된 영상 개수: 343개
삭제된 영상 개수: 0개
2023-01-09 영상정보 저장 완료
```
