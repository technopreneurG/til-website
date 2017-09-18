---
layout: post
title:  "Web scraping - Scrapy"
created_at: 2017-09-18 09:17:18 +0530
updated_at: 2017-09-18 09:17:18 +0530
categories: til
---
# About Scrapy

Quoting from [scrapy](https://scrapy.org/) website:

`
An open source and collaborative framework for extracting the data 
you need from websites. In a fast, simple, yet extensible way.
`

`
Scrapy is an application framework for writing web spiders 
that crawl web sites and extract data from them. 
`

# Installing Scrapy

```shell
$ mkdir learn_scrapy
$ cd learn_scrapy
$ virtualenv scrapyenv
$ source scrapyenv/bin/activate
$ pip install Scrapy
$ python -V
Python 2.7.6
$ pip -V
pip 1.5.4 from /.../learn_scrapy/scrapyenv/local/lib/python2.7/site-packages (python 2.7)
$ pip freeze
Automat==0.6.0
PyDispatcher==2.0.5
Scrapy==1.4.0
Twisted==17.5.0
argparse==1.2.1
asn1crypto==0.22.0
attrs==17.2.0
cffi==1.11.0
constantly==15.1.0
cryptography==2.0.3
cssselect==1.0.1
enum34==1.1.6
hyperlink==17.3.1
idna==2.6
incremental==17.5.0
ipaddress==1.0.18
lxml==4.0.0
parsel==1.2.0
pyOpenSSL==17.3.0
pyasn1==0.3.5
pyasn1-modules==0.1.4
pycparser==2.18
queuelib==1.4.2
service-identity==17.0.0
six==1.10.0
w3lib==1.18.0
wsgiref==0.1.2
zope.interface==4.4.2
```

# Scrapy code
{% highlight python %}
import scrapy

class TestSpider(scrapy.Spider):
  name="testspider"
  start_urls = ["http://www.moneycontrol.com/news/business/personal-finance/"]


  def parse(self, response):
    for li in response.css('li.clearfix h2'):
      yield { 'title': li.css('a ::text').extract_first() }

{% endhighlight %}

# Scrape

Print the output to console
```shell
$ scrapy runspider testspider.py 

2017-09-17 18:35:21 [scrapy.utils.log] INFO: Scrapy 1.4.0 started (bot: scrapybot)
2017-09-17 18:35:21 [scrapy.utils.log] INFO: Overridden settings: {'SPIDER_LOADER_WARN_ONLY': True}

2017-09-17 18:35:23 [scrapy.core.engine] DEBUG: Crawled (200) <GET http://www.moneycontrol.com/news/business/personal-finance/> (referer: None)
2017-09-17 18:35:23 [scrapy.core.scraper] DEBUG: Scraped from <200 http://www.moneycontrol.com/news/business/personal-finance/>
{'title': u'Importance of goal-based investing; top 5 things to consider before you invest'}
2017-09-17 18:35:23 [scrapy.core.scraper] DEBUG: Scraped from <200 http://www.moneycontrol.com/news/business/personal-finance/>
{'title': u'How is your mutual fund performing? Triggers that should alert you to exit'}
2017-09-17 18:35:23 [scrapy.core.scraper] DEBUG: Scraped from <200 http://www.moneycontrol.com/news/business/personal-finance/>
{'title': u'Money Money Money: Planning for retirement'}
...
...
...
{'title': u'8 reasons why you should invest in equity mutual funds'}
2017-09-17 18:35:23 [scrapy.core.scraper] DEBUG: Scraped from <200 http://www.moneycontrol.com/news/business/personal-finance/>
{'title': u'How fixed deposits and FMPs bolster your retirement portfolio'}
2017-09-17 18:35:23 [scrapy.core.engine] INFO: Closing spider (finished)
...
...
...


```


Save output to json file
```
$ scrapy runspider testspider.py -o test.json
2017-09-17 18:39:01 [scrapy.utils.log] INFO: Scrapy 1.4.0 started (bot: scrapybot)
2017-09-17 18:39:01 [scrapy.utils.log] INFO: Overridden settings: {'SPIDER_LOADER_WARN_ONLY': True, 'FEED_FORMAT': 'json', 'FEED_URI': 'test.json'}

2017-09-17 18:39:02 [scrapy.core.engine] DEBUG: Crawled (200) <GET http://www.moneycontrol.com/news/business/personal-finance/> (referer: None)
2017-09-17 18:39:03 [scrapy.core.scraper] DEBUG: Scraped from <200 http://www.moneycontrol.com/news/business/personal-finance/>
{'title': u'Importance of goal-based investing; top 5 things to consider before you invest'}
2017-09-17 18:39:03 [scrapy.core.scraper] DEBUG: Scraped from <200 http://www.moneycontrol.com/news/business/personal-finance/>
{'title': u'How is your mutual fund performing? Triggers that should alert you to exit'}
2017-09-17 18:39:03 [scrapy.core.scraper] DEBUG: Scraped from <200 http://www.moneycontrol.com/news/business/personal-finance/>
{'title': u'Money Money Money: Planning for retirement'}
...
...
...
2017-09-17 18:39:03 [scrapy.core.scraper] DEBUG: Scraped from <200 http://www.moneycontrol.com/news/business/personal-finance/>
{'title': u'How fixed deposits and FMPs bolster your retirement portfolio'}
2017-09-17 18:39:03 [scrapy.core.engine] INFO: Closing spider (finished)
2017-09-17 18:39:03 [scrapy.extensions.feedexport] INFO: Stored json feed (25 items) in: test.json

```

```json
$ cat test.json
[
{"title": "Importance of goal-based investing; top 5 things to consider before you invest"},
{"title": "How is your mutual fund performing? Triggers that should alert you to exit"},
{"title": "Money Money Money: Planning for retirement"},
{"title": "Buying life insurance? Why premium rate should not be only deciding factor"},
{"title": "Edelweiss Tokio Life launches new plan 'Smart Lifestyle'"},
{"title": "Investing in mutual funds for the first time? Here are 10 funds that could work"},
{"title": "Top-up home loans: How do they fare against other options?"},
{"title": "Personal finance this week: SC provides relief to homebuyers, NBCC gives hope"},
{"title": "Mobile payments: Here's how to ensure safety of your transaction"},
{"title": "How to benefit from Systematic Withdrawal Plans of mutual funds"},
{"title": "Retirement relief: Pension regulator to raise NPS entry age limit to 65 years"},
{"title": "Planning to retire early? 5 things you should keep in mind"},
{"title": "MoS Annual Conference: Experts discuss financialisation of savings"},
{"title": "Experts discuss challenges facing Indian financials at MoS global investor conf"},
{"title": "Investment steps you should take for a secure future"},
{"title": "5 factors to consider while buying term insurance and how much they cost"},
{"title": "Buying car insurance? Here's how you can save up to 60% on premium amount"},
{"title": "Gold could witness one of the most volatile periods; Should you invest?"},
{"title": "Aadhaar-PAN linking deadline is almost here! Here is what you need to know"},
{"title": "Money Money Money: Your guide to insurance claims related to flooding"},
{"title": "How availing train travel insurance through IRCTC will help you"},
{"title": "Five money things you must do this festive season"},
{"title": "Is your vehicle damaged in arson? Here\u2019s how to get claims settled smoothly"},
{"title": "8 reasons why you should invest in equity mutual funds"},
{"title": "How fixed deposits and FMPs bolster your retirement portfolio"}
```

# Next
Scrapy comes with an interactive shell, use it to quickly debug and test the parsing logic.
{% highlight python %}

$ scrapy shell
2017-09-17 18:44:09 [scrapy.utils.log] INFO: Scrapy 1.4.0 started (bot: scrapybot)
2017-09-17 18:44:09 [scrapy.utils.log] INFO: Overridden settings: {'LOGSTATS_INTERVAL': 0, 'DUPEFILTER_CLASS': 'scrapy.dupefilters.BaseDupeFilter'}
2017-09-17 18:44:10 [scrapy.extensions.telnet] DEBUG: Telnet console listening on 127.0.0.1:6023
[s] Available Scrapy objects:
[s]   scrapy     scrapy module (contains scrapy.Request, scrapy.Selector, etc)
[s]   crawler    <scrapy.crawler.Crawler object at 0x7fa878a17cd0>
[s]   item       {}
[s]   settings   <scrapy.settings.Settings object at 0x7fa878a17c50>
[s] Useful shortcuts:
[s]   fetch(url[, redirect=True]) Fetch URL and update local objects (by default, redirects are followed)
[s]   fetch(req)                  Fetch a scrapy.Request and update local objects 
[s]   shelp()           Shell help (print this help)
[s]   view(response)    View response in a browser
>>> fetch("http://www.moneycontrol.com/news/business/personal-finance/")
2017-09-17 18:44:22 [scrapy.core.engine] INFO: Spider opened
2017-09-17 18:44:23 [scrapy.core.engine] DEBUG: Crawled (200) <GET http://www.moneycontrol.com/news/business/personal-finance/> (referer: None)
>>> 
>>> response.text
u'                                            \r\n   \r\n<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">\r\n<html>\r\n<head>\r\n<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0" />\r\n
...
...
...
\r\n</body></html>'
>>> li=response.css("li.clearfix h2") 
>>> li.css("a ::text").extract()
[u'Importance of goal-based investing; top 5 things to consider before you invest', u'How is your mutual fund performing? Triggers that should alert you to exit', u'Money Money Money: Planning for retirement', u'Buying life insurance? Why premium rate should not be only deciding factor', u"Edelweiss Tokio Life launches new plan 'Smart Lifestyle'", u'Investing in mutual funds for the first time? Here are 10 funds that could work', u'Top-up home loans: How do they fare against other options?', u'Personal finance this week: SC provides relief to homebuyers, NBCC gives hope', u"Mobile payments: Here's how to ensure safety of your transaction", u'How to benefit from Systematic Withdrawal Plans of mutual funds', u'Retirement relief: Pension regulator to raise NPS entry age limit to 65 years', u'Planning to retire early? 5 things you should keep in mind', u'MoS Annual Conference: Experts discuss financialisation of savings', u'Experts discuss challenges facing Indian financials at MoS global investor conf', u'Investment steps you should take for a secure future', u'5 factors to consider while buying term insurance and how much they cost', u"Buying car insurance? Here's how you can save up to 60% on premium amount", u'Gold could witness one of the most volatile periods; Should you invest?', u'Aadhaar-PAN linking deadline is almost here! Here is what you need to know', u'Money Money Money: Your guide to insurance claims related to flooding', u'How availing train travel insurance through IRCTC will help you', u'Five money things you must do this festive season', u'Is your vehicle damaged in arson? Here\u2019s how to get claims settled smoothly', u'8 reasons why you should invest in equity mutual funds', u'How fixed deposits and FMPs bolster your retirement portfolio']
>>> 
>>>

{% endhighlight %}

