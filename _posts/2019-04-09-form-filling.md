---
title:  "Automated form-filling with Python"
date: 2019-04-09
permalink: /posts/2019/04/form-filling/
categories: 
  - short
tags:
  - python
---
Situation: You need to submit a lot of stuff to a website, but it only provides individual submission, not batch.

I have to mention that initially I spent a fruitless afternoon with `splinter` package, which is a higher abstraction of the `selenium` package, which is basically an API to a remote-controlled browser. Since it wasn't working, the following day I decided to switch to `selenium` with the hope that the problem might not exist at lower level. 

But it persisted. Many trips to Stack Overflow later, I finally figured out a working code. The key thing turned out to be `find_by_css` function since apparently there is an invisible layer in the website that prevents access to the text field.
  
The input data can be neatly formatted as `csv`, which I load as `pandas` dataframe and convert to dictionary. Certainly you can leave it as a dataframe but I found dictionary accessing functions are much easier and more intuitive.

For this scenario, let's say that this website lets you create projects. Upon logging in, you have to go to 'Dashboard', then you will see input fields to add a new project. There are:
- Project group id (dropdown)
- Project name (text)
- Description (text)
- Project code (dropdown)
- Active project? (radio button)
- Contact info (text)
- "Add Project" button

First you need to spend time on the website inspecting the elements and finding out the CSS identifiers of these fields. Then, you just have to tell the webdriver (this is the remote-controlled browser) that upon finding each input field, do an appropriate action correspondingly, whether to fill with text `send_keys()` or select `select_by_value()` or click `click()`. Of course before that you would have loaded your input data, packaged it in a dictionary, ready to be called (if you have numbers to be inputted don't forget to convert them to strings). In this example I have 72 projects to be added, that's why you see my `for loop` is running from index 0 to 71.

If I may say so myself, I think the code is very readable and almost reads like English. The logic is also quite straightforward since all we are doing is emulating a person logging in to a website and inputting projects one by one. Of course, to the computer, inputting 10 or 1000 don't make that much of a difference, while to a person it would be tedious and error-prone. I would definitely descend to an existential crisis by project 30 or thereabouts.   

```python 
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support.ui import Select
import os
import pandas as pd

# load input
os.chdir("/home/user/")
df = pd.read_csv('input_data.csv', sep=',')
data_dict = df.to_dict() 

# go to website
driver = webdriver.Chrome()
driver.get('https://www.website.com/login.php')

# login
driver.find_element_by_name("username").send_keys("*****")
driver.find_element_by_name("password").send_keys("*****")
driver.find_element_by_xpath("//input[@value='Login']").click()

# go to dashboard
driver.get('https://www.website.com/dashboard.php')

# loop for all 72 projects
for i in range(0,72):
    Select(driver.find_element_by_css_selector('select#project_group_id')).select_by_value('1')
    driver.find_element_by_css_selector('input#project_name.text').send_keys(data_dict['project_name'][i])
    driver.find_element_by_css_selector('textarea#description').send_keys(data_dict['description'][i])
    Select(driver.find_element_by_css_selector('select#project_code')).select_by_value(str(data_dict['project_code'][i]))
    driver.find_element_by_css_selector('input#active_1.radio').click()
    driver.find_element_by_css_selector('textarea#contact_info').send_keys("Name <email@address>")
    driver.find_element_by_xpath("//input[@value='Add Project']").click()
```
