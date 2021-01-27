---
layout: post
title:  "Python Crawler(EP1)"
description: An introduction of python web spider and a simple example .
date:   2020-08-11 21:03:36 +0530
---

This passage will give a comprehensive description of python crawler, including a web spider to crawl data and a data viewer to make data visible on a simple website(which will be demonstrated in the next episode).

## Overview

To obtain the Top250 movie information on Douban:

[http://movie.douban.com/top250](http://movie.douban.com/top250)

The relating information contains name, rating, judging number, general information, and link.

![DoubanSite](https://img-blog.csdnimg.cn/202008171715216.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hoaGhoYW9oYW8=,size_16,color_FFFFFF,t_70#pic_center)

<center>Douban Top250</center>

One page contains 25 pieces and there are 10 pages of web pages.

The entire codes are the following, and the passage will explain the details of these code in order:

```python
# -*- coding = utf-8 -*-
# @Time : 2020-08-12 11:19
# @Author : Danny
# @File : spider.py
# @Software: PyCharm

from bs4 import BeautifulSoup  # Web page parsing
import re  # Regular expression to match the requiring characters
import urllib.request,urllib.error  # Formulate the URL and get the web data
import xlwt  # Excel operation
import sqlite3  # SQlite database operation

# The regular expression of each movie

findlink = re.compile(r'<a href="(.*?)">')  # declare a regular expression object, to represent the string pattern
# The link of each movie

findImgSrc = re.compile(r'<img.*src="(.*?)"', re.S)  # re.S: to include the line break
# The image of each movie

findTitle = re.compile(r'<span class="title">(.*)</span>')
# The title of each movie

findRating = re.compile(r'<span class="rating_num" property="v:average">(.*?)</span>')
# The rating of each movie

findJudge = re.compile(r'<span>(\d*)人评价</span>')
# The judging number of each movie

findInq = re.compile(r'<span class="inq">(.*?)</span>')
# The basic condition of each movie

findBd = re.compile(r'<p class="">(.*?)</p>', re.S)
# The relating information of each movie


def getData(baseUrl):
    datalist = []
    for i in range(0, 10):  # call the def of obtaining 10 times
        url = baseUrl + str(i * 25)
        html = askURL(url)  # Save the source code of a web page

        # 2. Analyse Data
        soup = BeautifulSoup(html, "html.parser")
        for item in soup.find_all('div', class_="item"):  # find the required string to form a list
            # print(item)  # Test the information of item
            data = []  # Save the information of a single movie
            item = str(item)

            link = re.findall(findlink, item)[0]  # re library to find the specified string via regular expression
            data.append(link)  # Add link
            # print(link)

            imgSrc = re.findall(findImgSrc, item)[0]
            data.append(imgSrc)  # Add Image
            # print(data)

            titles = re.findall(findTitle, item)  # The movie may have both Chinese name and foreign name
            if(len(titles) == 2):
                ctitle = titles[0]  # add Chinese name
                data.append(ctitle)
                otitle = titles[1].split()  # leave out other characters
                Ftitle = ""
                for i in range(1, len(otitle)):
                    Ftitle += otitle[i] + " "

                data.append(Ftitle)  # add foreign name
            else:
                data.append(titles[0])
                data.append(' ')  # leave the foreign name to blank

            rating = re.findall(findRating, item)[0]
            data.append(rating)  # add rating

            judgeNum = re.findall(findJudge, item)
            data.append(judgeNum)  # add the judging number

            inq = re.findall(findInq, item)
            if len(inq) != 0:
                inq = inq[0].replace("。", "")  # leave out the Chinese full stop
                data.append(inq)  # add the general description
            else:
                data.append(" ")  # leave blank

            bd = re.findall(findBd, item)[0]
            # print(bd)
            re.sub('<br/>'," ",bd)  # leave out <br/>
            re.sub('/', " ", bd)  # replace /
            # print(bd)
            data.append(bd.strip())  # leave the blanks at the front and tail

            datalist.append(data)
    print(datalist)
    return datalist


# Require a specified web page
def askURL(url):
    head = {  # Simulate a browser header information
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:79.0) Gecko/20100101 Firefox/79.0"
    }

    request = urllib.request.Request(url, headers=head)
    html = ""

    try:
        response = urllib.request.urlopen(request)
        html = response.read().decode("utf-8")  # obtain the content of the web page
        # print(html)
    except urllib.error.URLError as e:
        if hasattr(e, "code"):
            print(e.code)
        if hasattr(e, "reason"):
            print(e.reason)

    return html


def saveData(datalist, savepath):
    print("save....")
    book = xlwt.Workbook(encoding='utf-8', style_compression=0)
    sheet = book.add_sheet('doubanTop250', cell_overwrite_ok=True)
    col = ("Movie Link", "Image Link", "Chinese Name", "Foreign Name", "Rating", "Rating Number", "General Description", "Info")
    for i in range (0,8):
        sheet.write(0, i, col[i])
    for i in range(0,250):
        print("The %d th pieces"%(i+1))
        data = datalist[i]
        for j in range(0,8):
            sheet.write(i+1, j, data[j])

    book.save(savepath)



if __name__ == '__main__':
    print("Start")
    # askURL("https://movie.douban.com/top250?start=")
    baseurl = "https://movie.douban.com/top250?start="
    # 1. Crawling web pages
    datalist = getData(baseurl)
    savepath = ".\\doubanTop250.xls"
    # 3. Save Data
    saveData(datalist, savepath)
    # print(askURL(baseurl))
    print("Finish")

```



------

## Basic Process

+ Preparation

  Analyse the source code of the target webpage, apply regular expression to the targeted code.

+ Obtain data

  Sending a request to the target web page  through `HTTP` library, the request can contain extra `header`  information, if the server response normally, it will return a `Response` , which is the content of the obtaining page.

+ Analyse Content

  The content we obtained may be `HTML`, `json` formats, we can decode the content through **page resolution library** or **regular expression**.

+ Save data

  The format of the saving file can be various, including `excel`, `database` and so on.

### Preparation

#### URL Analysis

![doubanweb1](https://img-blog.csdnimg.cn/2020081717170360.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hoaGhoYW9oYW8=,size_16,color_FFFFFF,t_70#pic_center)

![Doubanweb2](https://img-blog.csdnimg.cn/20200817171721638.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hoaGhoYW9oYW8=,size_16,color_FFFFFF,t_70#pic_center)

<center>
    We can see that the web page cantains 250 pieces of movie information and it they are divided into 10 pages. 
    The different point of the URL is that the Number at the tail = (Number of page - 1) * 25 
</center>


#### Web Page Analysis

+ We can apply **Developer Tool** to analyse the web page.

![doubanwebDevT](https://img-blog.csdnimg.cn/20200817171801246.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hoaGhoYW9oYW8=,size_16,color_FFFFFF,t_70#pic_center)
<center>
    The pointer can display the corresponding source code of the elements in the page
</center>

#### Code Structure

```python
from bs4 import BeautifulSoup  # Web page parsing
import re  # Regular expression to match the requiring characters
import urllib.request,urllib.error  # Formulate the URL and get the web data
import xlwt  # Excel operation
import sqlite3  # SQlite database operation

def main():
    baseurl = "https://movie.douban.com/top250?start="
    # 1. Crawling the web page
    datalist = getData(baseurl)
    savepath = r".\\doubanTop250.xls"
    # 3. Save data
    saveData(savepath)
    
    
# Crawling the web page
def getData(baseurl):
    datalist = []
    # 2. Parsing data one by one
    return datalist


# Save data
def saveData(savepath):
	print(Saving.....)
    
    
if __name__ == "__main__":
    main()
```

### Obtaining Data

+ We can obtain data through `urllib2` library

```python
def askURL(url):
    head = {  # Simulate a browser header information
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:79.0) Gecko/20100101 Firefox/79.0"
    }

    request = urllib.request.Request(url, headers=head)  # Sending a request
    html = ""

    try:
        response = urllib.request.urlopen(request)  # obtaining the response
        html = response.read().decode("utf-8")  # obtaining the content of the web page 
        # print(html)
    except urllib.error.URLError as e:
        if hasattr(e, "code"):
            print(e.code)
        if hasattr(e, "reason"):
            print(e.reason)

    return html
```

+ We can obtain data through `urllib2` library
  + As for each single page, call `askURL` to obtain the content
  + Define a function `askURL` to obtain the content of each web page, and pass in a `url` parameter to represent a link
  + `urllib2.Request()`generates the request, `urllib2.urlopen()` send the request to obtain response, and `read()` reads the content of the page
  + To avoid the errors such as runtime and, we can sorround the codes with `try...except`

#### urllib 

**Basic use**:

+ We can obtain information of a web page by using `urllib.request.urloprn("web address")`

  and the information is returned to a `response` object

+ To print out the information, we can use `response.read().decode('utf-8')` which will return a html file with the decoding method utf-8.

```python
# get a webpage source code

response = urllib.request.urlopen("http://www.baidu.com")
print(response.read().decode('utf-8'))
```

+ To deal with time out problem, we can surround the code with `try....except....`

  ```
  try:
       response = urllib.request.urlopen("http://httpbin.org/get", timeout=0.01)
       print(response.read().decode("utf-8"))
   except urllib.error.URLError as e:
       print("Time out!")
  ```


### Analyse Data

#### BeautifulSoup4

BeautifulSoup4 can convert a complicated HTML file into a complicated tree structure,
Every node is a python object, and the objects can be divided in to 4 kinds

- Tag
- NavigableString
- BeautifulSoup
- Comment

To sort out the information we need in a html file such as the source code of www.baidu.com:

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<link rel="stylesheet" type="text/css" href="css/base.css"/>
<link rel="stylesheet" type="text/css" href="css/index.css"/>
  <title>百度一下，你就知道</title>
</head>
<body>
    <div class="nav">
    	<span id="nav-b">
             <a href=""class="nav-a"><!--新闻--></a>
     		 <a href=""class="nav-a">新闻</a>
      	   	<a href=""class="nav-a">hao123</a>
     		 <a href=""class="nav-a">地图</a>
     		 <a href=""class="nav-a">视频</a>
     		 <a href=""class="nav-a">贴吧</a>
     		 <a href=""class="nav-a">学术</a>
     		 <a style="font-weight:normal" href=""class="nav-a">登录</a>
        </span>
      <li id="zzr">
          <span id="nav-b">
      </span>
      <ul>
        <li><a href="#" class="shezhi">搜索设置</a></li>
        <li><a href="#" class="shezhi">高级搜索</a></li>
        <li><a href="#" class="shezhi">关闭预测</a></li>
        <li><a href="#" class="shezhi">搜索历史</a></li>
      </ul></li>
      <a class="more" href="">更多产品</a>
      </div>
     <div id="logo">
      <img src="img/bd_logo1.png" width="270" height="120">
      </div>
      <form>
      		  <input type="text">
    	<input type="submit" name="send" class="search_butt" value="百度一下" />
    	     <!--<button type="button">百度一下</button>-->
    </form>
  <div class="footer">
  	<div class="img"><img src="img/zbios_x2_9d645d9.png "width="60" height="60">
  	<br /><p><b>百度</b></p>
  	</div>
  	<div class="about">
  		<a href=""class="about-a">把百度设为主页</a>
  		<a href=""class="about-a">关于百度</a>
  		<a href=""class="about-a">About Baidu</a>
  		<a href=""class="about-a">百度推广</a>
  	</div>
  	<div class="foot">
©2019&nbsp;Baidu&nbsp;
  		<a href="">使用百度前必读</a>
  		<a href="">意见反馈 </a>&nbsp;
  		京ICP证030173号  <img src="img/icons_1.png"/>&nbsp;&nbsp;
  		<a href="">京公网安备11000002000001号</a><img src="img/icons_2.png"/>
  	</div>
  </div>
    </div>
</body>
</html>
```

1. **Tag**:  The Tag and its content(String), only return the first found one

    In order to obtain a particular content in the html file, we can call a function `BeautifulSoup(htmlObj, "html.parser")` . After calling the `type(bs.title.string)`, we can know that the type of the returning obj is **Tag** .

```
    from bs4 import BeautifulSoup

    file = open("./baidu.html", "rb")
    html = file.read()
    bs = BeautifulSoup(html, "html.parser")

    print(bs.title)
    print(type(bd.title))
```

​		If we look into the original `html`file of "`./baidu.html`", we can find that the corresponding output is   the following:

```python
C:\Users\pc\AppData\Local\Programs\Python\Python38\python.exe D:/zwh52/coursework/python/douban/test/testBs4.py
<title>百度一下，你就知道</title>

Process finished with exit code 0
```

2. **NavigableString:** The String in the **Tag**:

   ```python
   print(bs.title.string)
   ```

   The output is the following:

   ```python
   C:\Users\pc\AppData\Local\Programs\Python\Python38\python.exe D:/zwh52/coursework/python/douban/test/testBs4.py
   百度一下，你就知道
   <class 'bs4.element.Tag'>
   
   Process finished with exit code 0
   ```

3. **BeautifulSoup:** Represents the whole document

   ```python
   print(type(bs))
   ```

   The output is following:

   ```
   C:\Users\pc\AppData\Local\Programs\Python\Python38\python.exe D:/zwh52/coursework/python/douban/test/testBs4.py
   <class 'bs4.BeautifulSoup'>
   
   Process finished with exit code 0
   ```

4. **Comments:** To leave out the comments symbol such as `<!-- -->`

   ```
   print(bs.a.string)
   print(type(bs.a.string))
   ```

   If we look back to the html file, the returning value leaves out the comment symbol

   ```html
   <a href=""class="nav-a"><!--新闻--></a>
   <a href=""class="nav-a">新闻</a>
   ```

   The output is the following:

   ```python
   C:\Users\pc\AppData\Local\Programs\Python\Python38\python.exe D:/zwh52/coursework/python/douban/test/testBs4.py
   新闻
   <class 'bs4.element.Comment'>
   
   Process finished with exit code 0
   ```

5.  Search in a file

   1. find_all(): 

      ``` python
      # Search through string, and return a list of obj which is the string
      t_list = bs.find_all("a")
      print(t_list)
      ```

      The output is:

      ```
      C:\Users\pc\AppData\Local\Programs\Python\Python38\python.exe D:/zwh52/coursework/python/douban/test/testBs4.py
      [<a class="nav-a" href=""><!--新闻--></a>, <a class="nav-a" href="">新闻</a>, <a class="nav-a" href="">hao123</a>, <a class="nav-a" href="">地图</a>, <a class="nav-a" href="">视频</a>, <a class="nav-a" href="">贴吧</a>, <a class="nav-a" href="">学术</a>, <a class="nav-a" href="" style="font-weight:normal">登录</a>, <a class="shezhi" href="#">搜索设置</a>, <a class="shezhi" href="#">高级搜索</a>, <a class="shezhi" href="#">关闭预测</a>, <a class="shezhi" href="#">搜索历史</a>, <a class="more" href="">更多产品</a>, <a class="about-a" href="">把百度设为主页</a>, <a class="about-a" href="">关于百度</a>, <a class="about-a" href="">About Baidu</a>, <a class="about-a" href="">百度推广</a>, <a href="">使用百度前必读</a>, <a href="">意见反馈 </a>, <a href="">京公网安备11000002000001号</a>]
      
      Process finished with exit code 0
      ```

      Search through **Regular Expression**, and it will return a list of obj which **match the Regular Expression **:

      ```python
      t_list = bs.find_all(re.compile("a"))
      
      print(t_list)
      ```

      The out put is:

      ```
      C:\Users\pc\AppData\Local\Programs\Python\Python38\python.exe D:/zwh52/coursework/python/douban/test/testBs4.py
      [<head>
      <meta charset="utf-8"/>
      <link href="css/base.css" rel="stylesheet" type="text/css"/>
      <link href="css/index.css" rel="stylesheet" type="text/css"/>
      <title>百度一下，你就知道</title>
      </head>, <meta charset="utf-8"/>, <span id="nav-b">
      <a class="nav-a" href=""><!--新闻--></a>
      <a class="nav-a" href="">新闻</a>
      <a class="nav-a" href="">hao123</a>
      <a class="nav-a" href="">地图</a>
      <a class="nav-a" href="">视频</a>
      <a class="nav-a" href="">贴吧</a>
      <a class="nav-a" href="">学术</a>
      <a class="nav-a" href="" style="font-weight:normal">登录</a>
      </span>, <a class="nav-a" href=""><!--新闻--></a>, <a class="nav-a" href="">新闻</a>, <a class="nav-a" href="">hao123</a>, <a class="nav-a" href="">地图</a>, <a class="nav-a" href="">视频</a>, <a class="nav-a" href="">贴吧</a>, <a class="nav-a" href="">学术</a>, <a class="nav-a" href="" style="font-weight:normal">登录</a>, <span id="nav-b">
      </span>, <a class="shezhi" href="#">搜索设置</a>, <a class="shezhi" href="#">高级搜索</a>, <a class="shezhi" href="#">关闭预测</a>, <a class="shezhi" href="#">搜索历史</a>, <a class="more" href="">更多产品</a>, <a class="about-a" href="">把百度设为主页</a>, <a class="about-a" href="">关于百度</a>, <a class="about-a" href="">About Baidu</a>, <a class="about-a" href="">百度推广</a>, <a href="">使用百度前必读</a>, <a href="">意见反馈 </a>, <a href="">京公网安备11000002000001号</a>]
      
      Process finished with exit code 0
      ```

      We can see that all the objs in the list contains "a".

   2. Search through**Kwarg** Parameter:

      ```python
      # Search through the id of the Tag which contains class
      t_list = bs.find_all(class_=True)
      
      for item in t_list:
          print(item)
      ```

      The output is：

      ```
      C:\Users\pc\AppData\Local\Programs\Python\Python38\python.exe D:/zwh52/coursework/python/douban/test/testBs4.py
      <div class="nav">
      <span id="nav-b">
      <a class="nav-a" href=""><!--新闻--></a>
      <a class="nav-a" href="">新闻</a>
      <a class="nav-a" href="">hao123</a>
      <a class="nav-a" href="">地图</a>
      <a class="nav-a" href="">视频</a>
      <a class="nav-a" href="">贴吧</a>
      <a class="nav-a" href="">学术</a>
      <a class="nav-a" href="" style="font-weight:normal">登录</a>
      </span>
      <li id="zzr">
      <span id="nav-b">
      </span>
      <ul>
      <li><a class="shezhi" href="#">搜索设置</a></li>
      <li><a class="shezhi" href="#">高级搜索</a></li>
      <li><a class="shezhi" href="#">关闭预测</a></li>
      <li><a class="shezhi" href="#">搜索历史</a></li>
      </ul></li>
      <a class="more" href="">更多产品</a>
      </div>
      <a class="nav-a" href=""><!--新闻--></a>
      <a class="nav-a" href="">新闻</a>
      <a class="nav-a" href="">hao123</a>
      <a class="nav-a" href="">地图</a>
      <a class="nav-a" href="">视频</a>
      <a class="nav-a" href="">贴吧</a>
      <a class="nav-a" href="">学术</a>
      <a class="nav-a" href="" style="font-weight:normal">登录</a>
      <a class="shezhi" href="#">搜索设置</a>
      <a class="shezhi" href="#">高级搜索</a>
      <a class="shezhi" href="#">关闭预测</a>
      <a class="shezhi" href="#">搜索历史</a>
      <a class="more" href="">更多产品</a>
      <input class="search_butt" name="send" type="submit" value="百度一下">
      <!--<button type="button">百度一下</button>-->
      </input>
      <div class="footer">
      <div class="img"><img height="60" src="img/zbios_x2_9d645d9.png " width="60"/>
      <br/><p><b>百度</b></p>
      </div>
      <div class="about">
      <a class="about-a" href="">把百度设为主页</a>
      <a class="about-a" href="">关于百度</a>
      <a class="about-a" href="">About Baidu</a>
      <a class="about-a" href="">百度推广</a>
      </div>
      <div class="foot">
      ©2019 Baidu 
        		<a href="">使用百度前必读</a>
      <a href="">意见反馈 </a> 
        		京ICP证030173号  <img src="img/icons_1.png">  
        		<a href="">京公网安备11000002000001号</a><img src="img/icons_2.png">
      </img></img></div>
      </div>
      <div class="img"><img height="60" src="img/zbios_x2_9d645d9.png " width="60"/>
      <br/><p><b>百度</b></p>
      </div>
      <div class="about">
      <a class="about-a" href="">把百度设为主页</a>
      <a class="about-a" href="">关于百度</a>
      <a class="about-a" href="">About Baidu</a>
      <a class="about-a" href="">百度推广</a>
      </div>
      <a class="about-a" href="">把百度设为主页</a>
      <a class="about-a" href="">关于百度</a>
      <a class="about-a" href="">About Baidu</a>
      <a class="about-a" href="">百度推广</a>
      <div class="foot">
      ©2019 Baidu 
        		<a href="">使用百度前必读</a>
      <a href="">意见反馈 </a> 
        		京ICP证030173号  <img src="img/icons_1.png">  
        		<a href="">京公网安备11000002000001号</a><img src="img/icons_2.png">
      </img></img></div>
      
      Process finished with exit code 0
      
      ```

   3. Search through **Text** armuments:

      ```python
      t_list = bs.find_all(text= re.compile("\d")) # apply regular expression to find the specified content containing digits in the file
      
      for item in t_list:
          print(item)
      ```

      The output is:

      ```
      C:\Users\pc\AppData\Local\Programs\Python\Python38\python.exe D:/zwh52/coursework/python/douban/test/testBs4.py
      hao123
      
      ©2019 Baidu 
        		
       
        		京ICP证030173号  
      京公网安备11000002000001号
      
      Process finished with exit code 0
      ```

   4. Css Selecor:

      ```python
      t_list1 = bs.select('title')  # selector as title
      
      t_list2 = bs.select(".shezhi")  # select as class name
      
      t_list3 = bs.select("#logo")  # select as id
      
      t_list4 = bs.select("body > div > span")  # select as child tag
      print("selector as title: ")
      for item in t_list1:
          print(item)
      
      print("\n" + "select as class name: ")
      for item in t_list2:
          print(item)
      
      print("\n"+"select as id")
      for item in t_list3:
          print(item)
      
      print("\n"+"select as child tag")
      for item in t_list4:
          print(item)
      ```

      The output is:

      ```
      C:\Users\pc\AppData\Local\Programs\Python\Python38\python.exe D:/zwh52/coursework/python/douban/test/testBs4.py
      selector as title: 
      <title>百度一下，你就知道</title>
      
      select as class name: 
      <a class="shezhi" href="#">搜索设置</a>
      <a class="shezhi" href="#">高级搜索</a>
      <a class="shezhi" href="#">关闭预测</a>
      <a class="shezhi" href="#">搜索历史</a>
      
      select as id
      <div id="logo">
      <img height="120" src="img/bd_logo1.png" width="270"/>
      </div>
      
      select as child tag
      <span id="nav-b">
      <a class="nav-a" href=""><!--新闻--></a>
      <a class="nav-a" href="">新闻</a>
      <a class="nav-a" href="">hao123</a>
      <a class="nav-a" href="">地图</a>
      <a class="nav-a" href="">视频</a>
      <a class="nav-a" href="">贴吧</a>
      <a class="nav-a" href="">学术</a>
      <a class="nav-a" href="" style="font-weight:normal">登录</a>
      </span>
      
      Process finished with exit code 0
      ```


#### re 

1. Regular expression

   | Operator |                       Description                       |                          Instances                           |
   | :------: | :-----------------------------------------------------: | :----------------------------------------------------------: |
   |    .     |             represent any single character              |                                                              |
   |   [ ]    |   character set, to give range of a single character    | [abc] represents a, b,c and [a-z] represents a single character from a to z |
   |   [^ ]   |                   none-charaacter set                   |    [^abc] means a single character which is not a, b or c    |
   |    *     |      any times of expansion of the last character       |         abc* means ab, abc, abcc or abccc and so on          |
   |    +     |  one or more times of expansion of the last character   |            abc+ means abc, abcc, abccc and so on             |
   |    ?     |   one or zero time of expansion of the last character   |                      abc? means ab, abc                      |
   |    \|    |              the left one or the right one              |                  abc\|def means abc or def                   |
   |   {m}    |          expand m times of the last character           |                       ab{2} means abbc                       |
   |  {m,n}   | expand m to n (including n) times of the last character |                   ab{1,2}c means abc, abbc                   |
   |    ^     |              match the head of the String               |          ^abc means abc is at the head of a String           |
   |    $     |              match the tail of the String               |          abc$ means abc is at the tail of a String           |
   |   ( )    |           group sign, can only use \| inside            |    (abc) represents abc, (abc\|def) represents abc or def    |
   |    \d    |                         digits                          |                                                              |
   |    \w    |                words, equals[A-Za-z0-9_]                |                                                              |

2. Re Library Main Functions

   |  Functions   |                         Description                          |
   | :----------: | :----------------------------------------------------------: |
   | re.search()  | find and match the first position of the string matched by the regular expression, and return a match object |
   |  re.match()  | match the regular expression at the beginning position of a string, and return a match object |
   | re.findall() | search the whole string and return the matched child string through a list object |
   |  re.split()  | split a string through the results of the regular expression matching, and return a list object |
   |   re.sub()   | replace all the child string matched by the regular expression, and return the string after replacing |

   The regular expression can contain some optional modifier to control the pattern of the matching schema.

   | Modifier |                         Descroption                          |
   | :------: | :----------------------------------------------------------: |
   |   re.l   |              to make the match case insensitive              |
   |   re.L   |          to match the pattern through locale-aware           |
   |   re.M   |  to match with multiple rows, which will influence ^ and $   |
   |   re.S   |    to make the . match the string includes the link break    |
   |   re.U   | decode the string with Unicode standard, this symbol influences \w,\W, \b, \B |
   |          |                                                              |

3.  For instance, in order to find a pattern in the target string:

   ```python
   import re
   
   m = re.search("asd", "wAasd")  # the front string is the pattern and the last string is the examined object
   
   print(re.findall("[A-Z]+", "ASDaDFGAs"))  # the front is regular expression which represents the capital letters
                                             # and the last string is the verifies string
   
   print(m)
   
   print(re.sub("a", "A", "abxdefgaasa"))  # find a and replace them with A
   ```

   The output is:

   ```
   C:\Users\pc\AppData\Local\Programs\Python\Python38\python.exe D:/zwh52/coursework/python/douban/test/testRe.py
   ['ASD', 'DFGA']
   <re.Match object; span=(2, 5), match='asd'>
   AbxdefgAAsA
   
   Process finished with exit code 0
   ```


#### code

```python
# The regular expression of each movie

findlink = re.compile(r'<a href="(.*?)">')  # declare a regular expression object, to represent the string pattern
# The link of each movie

findImgSrc = re.compile(r'<img.*src="(.*?)"', re.S)  # re.S: to include the line break
# The image of each movie

findTitle = re.compile(r'<span class="title">(.*)</span>')
# The title of each movie

findRating = re.compile(r'<span class="rating_num" property="v:average">(.*?)</span>')
# The rating of each movie

findJudge = re.compile(r'<span>(\d*)人评价</span>')
# The judging number of each movie

findInq = re.compile(r'<span class="inq">(.*?)</span>')
# The basic condition of each movie

findBd = re.compile(r'<p class="">(.*?)</p>', re.S)
# The relating information of each movie

def getData(baseUrl):
    datalist = []
    for i in range(0, 10):  # call the def of obtaining 10 times
        url = baseUrl + str(i * 25)
        html = askURL(url)  # Save the source code of a web page

        # 2. Analyse Data
        soup = BeautifulSoup(html, "html.parser")
        for item in soup.find_all('div', class_="item"):  # find the required string to form a list
            # print(item)  # Test the information of item
            data = []  # Save the information of a single movie
            item = str(item)

            link = re.findall(findlink, item)[0]  # re library to find the specified string via regular expression
            data.append(link)  # Add link
            # print(link)

            imgSrc = re.findall(findImgSrc, item)[0]
            data.append(imgSrc)  # Add Image
            # print(data)

            titles = re.findall(findTitle, item)  # The movie may have both Chinese name and foreign name
            if(len(titles) == 2):  # if the movie have both Chinese name and Foreign name
                ctitle = titles[0]  # add Chinese name
                data.append(ctitle)
                otitle = titles[1].split()  # leave out other characters
                Ftitle = ""
                for i in range(1, len(otitle)):
                    Ftitle += otitle[i] + " "

                data.append(Ftitle)  # add foreign name
            else:  # if the movie only has Chinese name
                data.append(titles[0])
                data.append(' ')  # leave the foreign name to blank

            rating = re.findall(findRating, item)[0]
            data.append(rating)  # add rating

            judgeNum = re.findall(findJudge, item)
            data.append(judgeNum)  # add the judging number

            inq = re.findall(findInq, item)
            if len(inq) != 0:
                inq = inq[0].replace("。", "")  # leave out the Chinese full stop
                data.append(inq)  # add the general description
            else:
                data.append(" ")  # leave blank

            bd = re.findall(findBd, item)[0]
            # print(bd)
            re.sub('<br/>'," ",bd)  # leave out <br/>
            re.sub('/', " ", bd)  # replace /
            # print(bd)
            data.append(bd.strip())  # leave the blanks at the front and tail

            datalist.append(data)
    print(datalist)
    return datalist
```

+ To obtain the data we need, we can look insight into the web source code:

  ![web source code](https://img-blog.csdnimg.cn/20200817171935748.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hoaGhoYW9oYW8=,size_16,color_FFFFFF,t_70#pic_center)

  We can find that the information of each movies is rendered in the `<div class="item">` section. 

  Thus，we can obtain the data by `soup.find_all('div', class_="item")`.

  The following is the code of the first movie, and the  information of all the other movies are the same.

  ```
  <div class="item">
  <div class="pic">
  <em class="">1</em>
  <a href="https://movie.douban.com/subject/1292052/">
  <img alt="肖申克的救赎" class="" src="https://img3.doubanio.com/view/photo/s_ratio_poster/public/p480747492.jpg" width="100"/>
  </a>
  </div>
  <div class="info">
  <div class="hd">
  <a class="" href="https://movie.douban.com/subject/1292052/">
  <span class="title">肖申克的救赎</span>
  <span class="title"> / The Shawshank Redemption</span>
  <span class="other"> / 月黑高飞(港)  /  刺激1995(台)</span>
  </a>
  <span class="playable">[可播放]</span>
  </div>
  <div class="bd">
  <p class="">
                              导演: 弗兰克·德拉邦特 Frank Darabont   主演: 蒂姆·罗宾斯 Tim Robbins /...<br/>
                              1994 / 美国 / 犯罪 剧情
                          </p>
  <div class="star">
  <span class="rating5-t"></span>
  <span class="rating_num" property="v:average">9.7</span>
  <span content="10.0" property="v:best"></span>
  <span>2115000人评价</span>
  </div>
  <p class="quote">
  <span class="inq">希望让人自由。</span>
  </p>
  </div>
  </div>
  </div>
  ```

  1. We can obtain the information of link by:

  ```html
  <a href="https://movie.douban.com/subject/1292052/">
  ```

  The corresponding regular expression is:

  ```python
  findlink = re.compile(r'<a href="(.*?)">')  
  ```

  2. link of image:

  ```html
  <a class="" href="https://movie.douban.com/subject/1292052/">
  ```

  The corresponding regular expression is:

  ```python
  findImgSrc = re.compile(r'<img.*src="(.*?)"', re.S) 
  ```

  3. name:

  ```html
  <span class="title">肖申克的救赎</span>
  <span class="title"> / The Shawshank Redemption</span>
  <span class="other"> / 月黑高飞(港)  /  刺激1995(台)</span>
  ```

  The corresponding regular expression is:

  ```python
  findTitle = re.compile(r'<span class="title">(.*)</span>')
  ```

  4. other information:

  ```html
  <p class="">
                              导演: 弗兰克·德拉邦特 Frank Darabont   主演: 蒂姆·罗宾斯 Tim Robbins /...<br/>
                              1994 / 美国 / 犯罪 剧情
                          </p>
  ```

  The corresponding regular expression is:

  ```python
  findBd = re.compile(r'<p class="">(.*?)</p>', re.S)
  ```

  5. rating:

  ```html
  <span class="rating_num" property="v:average">9.7</span>
  ```

  The corresponding regular expression is:

  ```python
  findRating = re.compile(r'<span class="rating_num" property="v:average">(.*?)</span>')
  ```

  6. rating number:

  ```html
  <span>2115000人评价</span>
  ```

  The corresponding regular expression is:

  ```python
  findJudge = re.compile(r'<span>(\d*)人评价</span>')
  ```

  7. relating judgment:

  ```html
  <span class="inq">希望让人自由。</span>
  ```

  The corresponding regular expression is:

  ```python
  findInq = re.compile(r'<span class="inq">(.*?)</span>')
  ```

  

+ After  gaining the information of  each movie, we can match and store the information of each movie in the `data[]` by the regular expression, and finally add the information of each movie into the `datalist[]`.

  
### Save Data

#### Save via Excel 

  ```python
savepath = ".\\doubanTop250.xls"

def saveData(datalist, savepath):
    print("save....")
    book = xlwt.Workbook(encoding='utf-8', style_compression=0)  # create a Excel workbook through utf-8
    sheet = book.add_sheet('doubanTop250', cell_overwrite_ok=True)  # create a sheet
    col = ("Movie Link", "Image Link", "Chinese Name", "Foreign Name", "Rating", "Rating Number", "General Description", "Info")  
    for i in range (0,8):
        sheet.write(0, i, col[i])
    for i in range(0,250):
        print("The %d th pieces"%(i+1))
        data = datalist[i]
        for j in range(0,8):
            sheet.write(i+1, j, data[j])

    book.save(savepath)
  ```

after saving, the excel file is:

![excel](https://img-blog.csdnimg.cn/20200817171443379.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hoaGhoYW9oYW8=,size_16,color_FFFFFF,t_70#pic_center)

  
==To be continued==
  

  

  

  

  

  

  