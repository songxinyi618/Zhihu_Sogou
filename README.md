# Zhihu_Sogou

一句话介绍：模拟浏览器在搜狗-知乎搜索框中输入关键字，爬取与关键词相关的前10个问题

备注：有一个爬取知乎问题的需求，知乎反爬机制严密怕有问题，虽然可以通过模拟登录解决，但是据说知乎不定期就改变相应策略，所以不如试试selenium吧~
     另外，搜狗搜索真是个好东西，可以搜索知乎问题（http://zhihu.sogou.com/） 和微信公众号文章（http://weixin.sogou.com/）

语言：Python

软件：Spyder(Python 3.6)

调用的库：selenium —— selenium 是一套完整的web应用程序测试系统，包含了测试的录制（selenium IDE）,编写及运行（Selenium Remote Control）和测试的并行处理（Selenium Grid）。Selenium的核心Selenium Core基于JsUnit，完全由JavaScript编写，因此可以用于任何支持JavaScript的浏览器上。

以下就是代码啦~

# -*- coding: utf-8 -*-
"""
Created on Fri Mar 30 17:43:24 2018

@author: Administrator
"""

############访问页面###############
from selenium import webdriver
 
browser = webdriver.Firefox()
browser.get('http://zhihu.sogou.com/')

############输入关键词#############
input = browser.find_element_by_id('query')
input.send_keys('管理')#关键词在此

button = browser.find_element_by_class_name('swz')
button.click()#点击搜索框

############找到所有问题并保存###################
from bs4 import BeautifulSoup
import pandas as pd

sreach_window=browser.current_window_handle#切换到新页面

all_html = browser.find_elements_by_class_name('result-about-list')

result = []

for html in all_html:
    #html = all_html[1]
    html = html.get_attribute('innerHTML')
    soup = BeautifulSoup(html,'lxml')
    #print(soup)
    question = soup.find('a').get_text()
    summary = soup.find('a', {'class':'link-white-pre'}).get_text()
    #print(summary)    
    question_url = soup.find('a', {'class':'link-white-pre'}).get('href')
    sdata = {'question':question, 'summary':summary, 'question_url':question_url}
    #print(sdata)
    result.append(sdata)

result = pd.DataFrame(result)  
print(result)  
