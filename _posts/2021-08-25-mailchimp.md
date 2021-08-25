---
title:  "Semi-automating Mailchimp daily newsletter"
date: 2021-08-25
permalink: /posts/2021/08/mailchimp/
categories: 
  - short
tags:
  - python
---
I am in charge of uploading a Mailchimp daily email newsletter. Here is some code to pre-fill everything except the content.
Technically you can automate the content upload, too, but it requires more html-fu than I have.

First, a setup:
```python
from mailchimp_marketing import Client
import mailchimp_marketing as MailchimpMarketing
from mailchimp_marketing.api_client import ApiClientError
import pprint
pp = pprint.PrettyPrinter(indent=4)
from datetime import datetime  
from datetime import timedelta  
import dateutil.parser

def convert_list_of_campaigns_to_id_title_dict(r):
    id_title_dict = {}
    for i in r['campaigns']:
        id_title_dict[i['id']] = i['settings']['title']
    return dict(sorted(id_title_dict.items(), key=lambda item: item[1]))

api_key = "YOUR_API_KEY"
server_prefix = "YOUR_SERVER_PREFIX"

client = MailchimpMarketing.Client()
client.set_config({
"api_key": api_key,
"server": server_prefix
})
```

Extract some info from an existing campaign that has the same parameters as the ones you are automating.
Use pretty print to save your eyes. For my newsletter, I look for:
- `list_id`
- `list_name`
- `template_id`

```python
# Count is the number of campaigns returned
r = client.campaigns.list(count=1)
pp.pprint(r)
```

Then we are ready to create the campaigns. 
Create a date:title dict first. My campaign title is just YYYYMMDD date, so my dict looks like this: 

```python
titles ={
'0901':'lorem',
'0902':'ipsum',
'0903':'dolor',
}

# create campaigns
for date in titles:    
    client.campaigns.create({
        "type": "regular",
        'recipients': {'list_id': 'YOUR_LIST_ID', 
                       'list_is_active': True, 
                       'list_name': 'YOUR_RECIPIENT_LIST', 
                       'segment_text': '', 
                       },
        'settings': {'subject_line': "MY DAILY NEWSLETTER – "+titles[date], 
                     'title': "2021"+date, 'from_name': 'YOUR_NAME', 
                     'reply_to': 'YOUR_EMAIL', 
                     'to_name': '*|FNAME|* *|LNAME|*', 
                     'template_id': YOUR_TEMPLATE_ID,},
    })
```

Now let's schedule the campaigns. Create a campaign_id:title dict, then just loop over it:

```python
# fetch all unscheduled campaigns (status='save')
r = client.campaigns.list(fields=['campaigns.id','campaigns.settings.title'],status='save',count=50)

# check that output is only your campaigns (there might be other saved drafts from the past)
pp.pprint(r)

# convert output to dict
to_schedule = convert_list_of_campaigns_to_id_title_dict(r)

# schedule. Here the timing that I want is actually 1 day before the date in the title
for i in to_schedule:
    # send_date = title_date minus 24hr
    a = dateutil.parser.isoparse('2021-09-'+schedule[i][-2:]+'T16:15:00+00:00')
    send_date = a + timedelta(days=-1)

    client.campaigns.schedule(i, {"schedule_time": send_date.isoformat()})
    # client.campaigns.unschedule(i) # unschedule with this if you made a mistake
```
