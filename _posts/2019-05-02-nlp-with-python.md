---
layout: post
title: "NLP with Python"
modified:
category: Python
excerpt: Use Python to autosummarise a Washington Post news article using rule-based methods, and classify blog posts with scikit-learn
tags: [Python, Programming, NLP]
image:
  feature:
date: 2019-05-02T11:28:51+01:00
---

[Github repo](https://github.com/yanniey/NLP)

## Part 1: Autosummarise a Washington Post news article using rule-based methods


```python
import requests
from bs4 import BeautifulSoup
```


```python
def getTextWaPo(url):
    r = requests.get(url)
    r.encoding = 'utf-8'
    html = r.text
    soup = BeautifulSoup(html,"lxml")
    text = " ".join(map(lambda p:p.text, soup.find_all('article')))
    return text

url = "https://www.washingtonpost.com/technology/2019/05/02/facebook-bans-extremist-leaders-including-louis-farrakhan-alex-jones-milo-yiannopoulos-being-dangerous/?utm_term=.c8fe12bd52c7"
text = getTextWaPo(url)
```

**soup.find()** only returns the first element that matches the **article** tag
    
**soup.findall()** returns all


```python
from nltk.tokenize import sent_tokenize,word_tokenize
from nltk.corpus import stopwords
from string import punctuation
```


```python
sents = sent_tokenize(text)
word_sent = word_tokenize(text.lower())

# remove stopwords
_stopwords = set(stopwords.words("english") + list(punctuation)+['’','“','”','—',"window.powaBoot"])
word_sent = [word for word in word_sent if word not in _stopwords]
```


```python
# find the most frequent words in the article
from nltk.probability import FreqDist
freq = FreqDist(word_sent)
```


```python
from heapq import nlargest
# use nlargest to find the top 10 most frequent keywords in the article
nlargest(10,freq,key=freq.get)
```




    ['facebook',
     'said',
     'hate',
     'banned',
     'jones',
     'white',
     'company',
     'infowars',
     'speech',
     'platforms']




```python
from collections import defaultdict
from heapq import nlargest
ranking  = defaultdict(int)

# find the most sentences with the most frequent words, and store result into a defaultdict where the keys are the indices of the sentences, and the values are the significance scores for the sentences (which is the sum of the importance of words in that sentence)
for i, sent in enumerate(sents):
    for w in word_tokenize(sent.lower()):
        if w in freq:
            ranking[i] +=freq[w]
            
# find the top 3 sentences from the ranking dictionary

sent_index = nlargest(3, ranking,key=ranking.get)
sent_index
[sents[j] for j in sorted(sent_index)]

```




    ['Facebook said on Thursday it has permanently banned several far-right and anti-Semitic figures and organizations, including Nation of Islam leader Louis Farrakhan, Infowars host Alex Jones, Milo Yiannopoulos and Laura Loomer, for being “dangerous,” a sign that the social network is more aggressively enforcing its hate speech policies under pressure from civil rights groups.',
     'Angelo Carusone, president of Media Matters, an organization that has long advocated for more enforcement against white supremacists, said Facebook has been lax against enforcing its policies against hate speech on these accounts because the company doesn’t want to deal with the right-wing blowback.',
     'Madihha Ahussain, special counsel for anti-Muslim bigotry with the advocacy group Muslim Advocates, said that individuals like Loomer, Jones and Yiannopoulos have used social media platforms to broadcast dangerous hate speech and conspiracies targeting Muslims, Jews and others.']




```python
# putting everything together in one function:
from nltk.tokenize import sent_tokenize,word_tokenize
from nltk.corpus import stopwords
from nltk.probability import FreqDist
from string import punctuation
from collections import defaultdict
from heapq import nlargest

def summarize(text,n):
    sents = sent_tokenize(text)
    
    assert n <= len(sents) # this checks whether the number of summary lines is smaller than the # of sentences in the article
    word_sent = word_tokenize(text.lower())
    _stopwords = set(stopwords.words("english") + list(punctuation)+['’','“','”','—'])
    
    word_sent = [word for word in word_sent if word not in _stopwords]
    freq = FreqDist(word_sent)
    
    ranking = defaultdict(int)
    
    for i, sent in enumerate(sents):
        for w in word_tokenize(sent.lower()):
            if w in freq:
                ranking[i]+= freq[w]
    
    sent_index = nlargest(4, ranking,key=ranking.get)
    return [sents[j] for j in sorted(sent_index)]
    
    
```


```python
summarize(text,3)
```




    ['Facebook said on Thursday it has permanently banned several far-right and anti-Semitic figures and organizations, including Nation of Islam leader Louis Farrakhan, Infowars host Alex Jones, Milo Yiannopoulos and Laura Loomer, for being “dangerous,” a sign that the social network is more aggressively enforcing its hate speech policies under pressure from civil rights groups.',
     'Facebook had removed the accounts, fan pages, and groups affiliated with these individuals after it reevaluated the content that they had posted previously, or had reexamined their activities outside of Facebook, the company said.',
     'Angelo Carusone, president of Media Matters, an organization that has long advocated for more enforcement against white supremacists, said Facebook has been lax against enforcing its policies against hate speech on these accounts because the company doesn’t want to deal with the right-wing blowback.',
     'Madihha Ahussain, special counsel for anti-Muslim bigotry with the advocacy group Muslim Advocates, said that individuals like Loomer, Jones and Yiannopoulos have used social media platforms to broadcast dangerous hate speech and conspiracies targeting Muslims, Jews and others.']





## Part 2: Classifying a text using Machine Learning


* Feature extraction with bag of words, and k-means clustering on themes
* Objective: to build text corpus through collecting artciles from a blog


## Step 1: create a link of all posts in this blogspot site


```python
import requests
from bs4 import BeautifulSoup

def getAllDoxyDonkeyPosts(url,links):
    r = requests.get(url)
    r.encoding = "utf-8"
    html = r.text
    soup = BeautifulSoup(html,"lxml")
    for a in soup.findAll("a"): # find all links on the blog pages
        try:
            url = a["href"]
            title = a["title"]
            if title == "Older Posts":
                print(title, url)
                links.append(url)
                getAllDoxyDonkeyPosts(url,links)
        except:
            title = ""
    return links


blogURL = "https://doxydonkey.blogspot.com/"
links = []
getAllDoxyDonkeyPosts(blogURL, links)
                



```




## Step 2: crawl content of all posts on this site


```python
def getDoxyDonkeyText(URL):
    r = requests.get(URL)
    r.encoding = "utf-8"
    html = r.text
    soup = BeautifulSoup(html,"lxml")
    divs = soup.findAll("div",{"class":"post-body"}) #find all divs on the blog post page which has the class name "post-body"
    posts = []
    for div in divs:
        for i in div.findAll("span"):
            if len(i)>5: # only keep articles which are > 5 words
                posts.append(i.text.replace("?"," ")) 
    return posts


allPosts = []

for link in links:
    allPosts +=getDoxyDonkeyText(link)
```


```python
allPosts[:10]
```




    ['Candid, Comedic and Macabre YouTube Stars Feel an Advertising Pinch:\xa0Tim Wood sat on a chair inside a house in Hinsdale, N.Y., long rumored to be haunted. He had\xa0a Ouija\xa0board in his lap and was\xa0livestreaming\xa0the experience to a group of fans on YouTube. “You’re not ever supposed to do Ouija alone, let alone in a place that had an exorcism done in it,” he said to the empty room. As he filmed last month, the comments rolled in, some admiring (“You are one brave ghost hunter”), others fearing for his safety (“Tim, don’t summon what u can’t banish”). Mr. Wood, 39, has amassed a small but loyal following by making online videos of ghost hunts and paranormal activity, using YouTube to broadcast his work since about 2013. Automatically placed advertisements on his channel, LiveScifi, which has about 470,000 subscribers, have allowed him to turn the videos into a full-time job.But in the wake of a recent advertiser exodus from YouTube, prompted by major brands discovering they were showing up on videos promoting hate speech and terrorism, his earnings have plunged. Mr. Wood, who lives in San Francisco with his fiancée and their infant, said his channel had brought in at least $6,000 a month in revenue last year — which helped pay for travel to site locations, the production of his videos and his other day-to-day bills. In January, his estimated revenue was about $3,900. In February and March, he was alarmed to see that drop below $3,000. Last month, he saw around $1,600 and has been using crowdfunding to cover his shooting costs.In February and March, a slew of advertisers yanked their money from the platform, prompting YouTube to tighten its default settings for where ads appear and to offer new ways for brands to manually and automatically avoid material that violates its guidelines. “Rates are a lot lower and hurting a lot of folks,” said Krishna Subramanian, a founder of Captiv8, a firm that connects brands to social media influencers. His firm recently conducted a survey of 100 YouTube creators and said that channels focused on comedy and gaming experienced the sharpest drops in revenue last month compared with February. At the same time, creators in food,\xa0beauty\xa0and fashion, and family and parenting had\xa0increases. YouTube shares ad revenue with creators, who keep more than half.\xa0Some creators are “looking at a video saying, ‘It got a million views, but I only got $700 — I used to get $2,500,’” Mr. Subramanian said.',
     'Ofo\'s Zhang Sees China Bike Bubble But Says Startup Will Survive:\xa0The co-founder of Ofo Inc., China’s biggest bike-sharing startup, sees a bubble in the industry but says his\xa0multibillion dollar\xa0business has the scale needed to survive any\xa0bust.\xa0Ofo plans to expand to 20 countries this year and 200 cities across China, Zhang Siding said Saturday in a Bloomberg Television interview in Zhengzhou, China. He said the company is valued at more than $2 billion.Ofo’s ubiquitous canary-yellow bikes are among more than 25 services now crowding China’s sidewalks. None are seen as profitable thanks to subsidies and low costs, yet together they’ve raised billions of dollars from venture capitalists hoping to cash in on the craze.\xa0China’s bike-sharing pioneers are gearing up to compete globally, with arch-rival\xa0Mobike\xa0previously telling Bloomberg News it wants to enter 100 cities with several foreign locations already in the works. But the flood of bikes has led to angst among China’s local governments and anger from residents. The services typically allow users to park the bikes wherever they like, jamming up the sidewalks. Ofo’s daily revenue is about 10 million yuan ($1.45 million) and it has raised about $650 million since its inception, co-founder Dai Wei said last week in remarks confirmed by the company.\xa0Zhang said Ofo is profitable in two cities, but added this wasn’t a major goal for the company. Instead, the priority is to improve the user experience and boost its brand.\xa0"There will be a bubble for the industry," he said. "But as long as we continue to do practical things, then there won’t be a bubble.”',
     'Microsoft\'s Nadella banks on LinkedIn data to challenge Salesforce:\xa0Microsoft Corp (MSFT.O) is rolling out upgrades to its sales software that integrates data from LinkedIn, an initiative that Microsoft CEO Satya Nadella told Reuters was central to the company\'s long-term strategy for building specialized business software. The improvements to Dynamics 365, as Microsoft\'s sales software is called, are a challenge to market leader Salesforce.com (CRM.N) and represent the first major product initiative to spring from Microsoft\'s $26 billion acquisition of LinkedIn, the business-focused social network.\xa0The new features will comb through a salesperson\'s email,\xa0calendar\xa0and LinkedIn relationships to help gauge how warm their relationship is with a potential customer. The system will recommend ways to save an at-risk deal, like calling in a co-worker who is connected to the potential customer on LinkedIn.\xa0The enhancements, which will be available this summer, will require Microsoft Dynamics customers to also be LinkedIn customers.\xa0The artificial intelligence, or AI, capabilities of the software would be central, Nadella said. "I want to be able to democratize AI so that any customer using these products is able to, in fact, take their own data and load it into AI for themselves," he said.\xa0While Microsoft is a behemoth in the market for operating systems and productivity software like Office, it is a small player in sales software. The company ranks fourth - far behind Salesforce.com and other rivals Oracle Corp (ORCL.N) and SAP (SAPG.DE) - with just 4.3 percent of the market in 2015, the most recent year for which figures are available, according to research firm Gartner.Nadella is under pressure to show that the pricey LinkedIn acquisition in mid-2016 was worthwhile. R "Ray" Wang,\xa0founder\xa0of analyst firm Constellation Research, said LinkedIn-powered features, combined with popular programs like Office and Skype, could help.',
     'Jeff Bezos Says He Is Selling $1 Billion a Year in Amazon Stock to Finance Race to Space:\xa0Standing against the backdrop of his New Shepard rocket booster and a full-scale mock capsule for carrying humans into space, Jeff Bezos revealed on Wednesday that he was selling about $1 billion in Amazon stock a year to finance his Blue Origin rocket company.\xa0Mr. Bezos, the billionaire founder of Amazon, showed off the reusable rocket booster and the mock-up of the capsule that will take people up for panoramic views back down at earth, during a symposium here.\xa0Mr. Bezos, who hopes to build Blue Origin into a commercial and tourist venture, also disclosed that it would cost about $2.5 billion to develop an even bigger rocket, New Glenn, capable of lifting satellites and, eventually, people into orbit.\xa0Like his fellow technology titan Elon Musk of SpaceX and Tesla, Mr. Bezos has identified reusable rocket parts as a key to lowering the price of admission to the field, which he said on Wednesday would lead to a “golden age of space exploration.” Last month, Mr. Bezos announced the\xa0first-paying\xa0customer,\xa0Eutelstat, a satellite company, for New Glenn, whose commercial flights would help offset costs. New Glenn is expected to fly by 2020, he said, but humans will not be passengers on the heavy-lift rocket until many years after that. Mr. Bezos has repeatedly expressed caution about setting timetables for the start of Blue Origin’s commercial or passenger trips, and he did not diverge from that on Wednesday. He would not say when New Shepard would undergo its next round of test flights, or set a specific date as a goal, merely mentioning next year for possible tourist trips.',
     "SoftBank moots Snapdeal sale to Flipkart, proposed deal set to be biggest in Indian e-commerce:\xa0SoftBank, the largest shareholder in Snapdeal, held boardroom discussions on the proposed sale of the online marketplace to rival Flipkart on Tuesday, according to two people aware of the development.\xa0According to the terms proposed by the Japanese media and telecom conglomerate, Snapdeal shareholders will get one share of Flipkart for every ten they own, said the people cited above. Early investors in Snapdeal — Kalaari Capital and Nexus Venture Partners — have also asked\xa0for about $100 million each from the sale, the sources said.\xa0The proposed sale could see SoftBank pick up a 20% stake in the country's largest\xa0ecommerce\xa0company for about $1.5 billion, in the process buying out $500 million to $1 billion worth of Tiger Global's holding in Flipkart, according to two people aware of the matter. \xa0Alibaba-backed Paytm E-commerce has also discussed a potential acquisition of Snapdeal, but the valuation offered was much lower than that offered by Flipkart, added another source. \xa0The meeting signals easing of tensions between\xa0Kalaari\xa0and Nexus, and SoftBank, said one of the sources mentioned above.\xa0",
     'Cloudera’s IPO will test unicorn valuations:\xa0Cloudera filed to go public mid-day last Friday, releasing a set of financial numbers that were the locus of anticipation: How would the company’s recent performance stack up to its $4.1 billion\xa0valuation\xa0set three years ago \xa0Before Cloudera published its S-1 document — detailing its recent quarterly results and several years of financial data — reports indicated that the company would pursue a $4.1 billion valuation in its IPO, flat from its last private round when Intel poured $740 million into the company.\xa0That Cloudera might aim for a level valuation with a dozen or so quarters of additional growth under its belt was notable. (Crunchbase News reached out to Cloudera regarding the valuation figure. The company declined to comment.) The situation raises an obvious question: Did Cloudera’s investors incorrectly estimate the potential future value of the company in 2014 if it intends to secure a flat valuation today \xa0It is likely fair to say that Cloudera’s investors were at least partially incorrect about where the company’s value would end up by the first quarter of 2017. No one alive deploys three-quarters of a billion dollars in capital for a flat return over a multi-year period. And doubly, Cloudera may in fact still be overvalued at the proposed $4.1 billion\xa0valuation\xa0when compared to certain public market comps.',
     'BlackRock cuts fees and jobs;\xa0stockpicking\xa0goes high-tech:\xa0BlackRock Inc on Tuesday said it would overhaul its actively managed equities business, cutting jobs, dropping fees and relying more on computers to pick stocks in a move that highlights how difficult it has become for humans to beat the market.\xa0The world\'s biggest money manager has faced active stock fund withdrawals and the revamp is its biggest attempt yet to engineer a turnaround.\xa0Last May, BlackRock said it had recruited Mark Wiseman, the head of Canada\'s biggest public pension fund, to oversee the\xa0stockpicking\xa0operations after he revamped that fund\'s operations to embrace data-mining and other technological approaches to investing. BlackRock is rebranding or adjusting investment strategies on about 11 percent of its $275 billion active stock fund business, putting a greater emphasis on technology-driven investing approaches in the largest set of sweeping changes for the business since transformational mergers that allowed it to grow to manage more than $5 trillion in assets.\xa0Among the changes, BlackRock is removing some seven\xa0traditionalist\xa0"Fundamental" portfolio managers from their current assignments, according to a source familiar with the matter. More than 40 employees are being laid off, including some of the portfolio managers, according to another source.',
     'Facebook pivots into Stories:\xa0In its biggest change in a decade, Facebook is evolving from text and link-focused sharing to the visual communication format it admits “Snapchat has really pioneered.” Starting today, all users will soon have access to the new Facebook Camera feature that lets them overlay special effects on photos and videos. They can then share this content to a Snapchat clone called Facebook Stories that appears above News Feed on mobile and works similarly to Instagram’s 24-hour ephemeral slideshows. Users also may share these posts to News Feed, individual friends through the new Facebook Direct private visual messages that disappear once digested or any combination thereof.\xa0But really it was the rapid ascent of Instagram Stories to 150 million daily users that he says inspired Facebook to start testing its own Stories in\xa0January,\xa0and keep expanding it to 12 countries before today’s rollout. That’s the worst news for Snapchat and\xa0best\xa0news for Facebook since the world’s biggest social network adopted the strategy of copying the competitor that refused its acquisition offer. If Facebook Stories clearly cannibalized News Feed sharing and consumption, it would have to demolish its most popular and lucrative feature to make way for the future where the camera is the new keyboard. And if users saw Facebook, Instagram, Messenger and WhatsApp’s Stories features as uncool clones or redundant as a set, it might have had to limit its attack on Snapchat to just one of its core apps.\xa0Instead, Facebook can charge in\xa0full-speed, attacking Snap from every angle without much penalty to its existing business. And with its enormous engineering and design teams, plus billions in profit each quarter, it can throw more resources at Camera, Stories and Direct visual messaging than Snap can. That product development strength is on display with today’s launch, and apparent from Facebook’s insistence on showing reporters forthcoming special effects that one-up Snapchat’s iconic lenses.\xa0Until now, Facebook was just running missile tests and fighting skirmishes on the frontier. Today Facebook declares total war on Snapchat.',
     'What happened to tablet sales \xa0In the past month, both Apple and Samsung have refreshed their flagship tablets for the first time since 2014. A lot has changed in the space in the intervening years — mostly for the worse, as overall sales have continued to slip. In Q4 of last year, IDC reported that shipments had dropped 20 percent, year over year, while Strategic Analytics has the number at half that. There’s room for debate as far as precisely how down the overall market is (not to mention what precisely qualifies a device as a tablet), but there seems to at least be a consensus that early predictions of the tablet space eclipsing PCs missed the mark.\xa0The space\xa0has suffered for a variety of reasons. Among them, the fact that users simply aren’t refreshing tablets at a rate many manufacturers no doubt predicted.\xa0“The iPad 2 is still in use today,” IDC Senior Analyst Jitesh Ubrani tells TechCrunch. “The [original] iPad Minis and Air are all still in use today. They were being supported by Apple until very recently. People have been hanging onto these devices and they’re finding that they work just as well as they did when they were released.”\xa0There are a few reasons for this. For one thing, users haven’t been conditioned to upgrade slates on the same cycle as smartphone — something that’s been hammered into consumers in almost Pavlovian fashion through carrier upgrade cycles.\xa0There’s also the simple fact that we tend not to put the devices through the same sort of day to day wear and tear of smartphones, or even laptops, with many users simply leaving their devices at home and breaking them out when it’s time to watch some Netflix. The growing size of smartphone displays has gone\xa0a ways\xa0toward cannibalizing tablet sales, as well, limiting the need for a much larger device when so many handsets are now within the six-inch range.\xa0',
     'Tiger Global may be part-exiting Flipkart with 3x return:\xa0Tiger Global, the biggest investor in Flipkart, may have struck a deal with Microsoft and other new investors to sell a part of its stake in the e-commerce company in the latest\xa0fund-raising\xa0round that valued it at $9.3 billion pre-money, a person familiar with the conversations told VCCircle. While US online retailer eBay, Chinese tech major Tencent and Microsoft are investing $500 million each in the round, the part-sale of Tiger’s shares would mean Flipkart is still short of the $1.5 billion it was targeting to raise in this round. That means either one of the three would put in additional money, or a fourth investor—potentially Google Capital—would help Flipkart close the round within a few weeks, added the person cited above. The part-sale of its Flipkart stake aligns with Tiger Global’s strategy to book some gains before actively investing in the country again, VCCircle had reported earlier. It is part of its broader plans to monetise stakes in its major bets, such as Flipkart,\xa0Ola\xa0and Quikr, in the near term.\xa0The secondary transaction of Flipkart shares between Tiger Global and Microsoft would result in the former’s stake in the company decreasing to around 25%, from the current 33-35%, depending on the deal size. This would also mean Tiger getting some of its money back—the firm is believed to have invested around $1 billion in Flipkart.\xa0This part-exit would mean three-fold return for Tiger Global’s biggest investment in India, better than some of its poor exits such as Caratlane last year, where it practically made no gains at all. Except MakeMyTrip and JustDial, Tiger has seen no impressive exits in India yet.']



## Step 3: Identify underlying themes among the crawled articles using clustering


```python
# use scikit-learn to do TF-IDF and k-means clustering
from sklearn.feature_extraction.text import TfidfVectorizer
vectorizer = TfidfVectorizer(max_df=0.5, min_df = 2, stop_words="english")
```


```python
X = vectorizer.fit_transform(allPosts)
```


```python
from sklearn.cluster import KMeans

km = KMeans(n_clusters=3,init="k-means++", max_iter=100, n_init=1, verbose =True)
km.fit(X)
```

    Initialization complete
    Iteration  0, inertia 137.317
    Iteration  1, inertia 72.015
    Iteration  2, inertia 71.748
    Iteration  3, inertia 71.597
    Iteration  4, inertia 71.502
    Converged at iteration 4: center shift 0.000000e+00 within tolerance 5.977035e-08





    KMeans(algorithm='auto', copy_x=True, init='k-means++', max_iter=100,
        n_clusters=3, n_init=1, n_jobs=1, precompute_distances='auto',
        random_state=None, tol=0.0001, verbose=True)




```python
import numpy as np
np.unique(km.labels_,return_counts=True)
```




    (array([0, 1, 2], dtype=int32), array([32, 15, 33]))



The above section returns the articles divided into 3 different clusters, each composed of 32, 15 and 33 articles. Next we'll try to find out what are the themes for the three clusters.


```python
text = {}

# aggregate text in each cluster
for i, cluster in enumerate(km.labels_):
    eachDocument = allPosts[i]
    if cluster not in text.keys():
        text[cluster]= eachDocument
    else:
        text[cluster] += eachDocument
```

## Find out most frequent words using NLTK


```python
from nltk.tokenize import sent_tokenize, word_tokenize
from nltk.corpus import stopwords
from nltk.probability import FreqDist
from collections import defaultdict
from string import punctuation
from heapq import nlargest
import nltk
```


```python
_stopwords = set(stopwords.words("english")+list(punctuation)+["million","year","billion","\xa0","millions","billions","year","company","'","s","new","one","percent",'’',"'s","said","would","also"])
```

## Find top 50 keywords in each cluster and their counts


```python
keywords = {}
counts = {}
for cluster in range(3):
    word_sent = word_tokenize(text[cluster].lower())
    word_sent = [word for word in word_sent if word not in _stopwords]
    freq = FreqDist(word_sent)
    keywords[cluster] = nlargest(50,freq,key=freq.get)
    counts[cluster]=freq

counts
```




    {0: FreqDist({'twitter': 34, 'china': 26, 'biggest': 22, 'users': 22, 'facebook': 22, 'investors': 21, 'us': 21, 'alibaba': 21, 'revenue': 19, 'valuation': 18, ...}),
     1: FreqDist({'uber': 40, '“': 32, '”': 32, 'apple': 30, 'kalanick': 17, 'kamel': 16, 'program': 12, 'patent': 11, 'iphone': 10, 'app': 9, ...}),
     2: FreqDist({'amazon': 83, 'google': 60, 'like': 45, 'app': 45, 'facebook': 36, '—': 32, 'ads': 30, 'data': 29, 'product': 29, 'users': 29, ...})}



## Find top 10 keywords which are unique to each cluster


```python
unique_keys={}
for cluster in range(3):
    other_clusters = list(set(range(3))-set([cluster]))
    keys_other_clusters = set(keywords[other_clusters[0]]).union(set(keywords[other_clusters[1]]))
    unique = set(keywords[cluster]) - keys_other_clusters
    unique_keys[cluster] = nlargest(10,unique,key=counts[cluster].get)
    
```


```python
unique_keys
```




    {0: ['twitter',
      'biggest',
      'us',
      'alibaba',
      'investors',
      'revenue',
      'valuation',
      'market',
      'world',
      'financial'],
     1: ['uber',
      'kalanick',
      'kamel',
      'program',
      'patent',
      'iphone',
      'delivery',
      'device',
      'watch',
      'greyball'],
     2: ['ads',
      'data',
      'product',
      'home',
      'customers',
      'mobile',
      'products',
      'platform',
      'ad',
      'buy']}



## Step 3: assign a theme to an article based on its cluster


```python
# select a random article
article = "Alibaba Group Holding Ltd.’s six-year-old excursion into mobile operating systems is faltering in China, casting doubt over software that bears billionaire-founder Jack Ma’s name and was once touted as key to countering Tencent Holdings Ltd. China’s largest e-commerce company debuted YunOS in 2011, a system that underpins search, shopping and browsing that its executives last year said could attain as much as 25 percent domestic market share by the end of 2016 -- surpassing Apple Inc.’s iOS. Six years on, YunOS’ slice of China software installations stands at just 2.2 percent while its share of 2016 shipments was 10 percent, researchers Canalys and Counterpoint estimate, respectively. Alibaba disputes those numbers. Alibaba managers have grown increasingly unhappy with its sluggish adoption and have begun an internal debate around the software’s future, a person familiar with the matter said. No conclusions have been reached, the person said, asking not to be named discussing a confidential matter. Yet the talks reflect the inability of a once-vaunted initiative to forestall Tencent’s dominance in the mobile arena, secured through the utility of WeChat -- a universal app that melds messaging, payments, media, shopping and on-demand services."
```


```python
from sklearn.neighbors import KNeighborsClassifier
classifier = KNeighborsClassifier(n_neighbors=10)
classifier.fit(X,km.labels_) # training phase
```




    KNeighborsClassifier(algorithm='auto', leaf_size=30, metric='minkowski',
               metric_params=None, n_jobs=1, n_neighbors=10, p=2,
               weights='uniform')




```python
test = vectorizer.transform([article])
```


```python
test
```




    <1x1570 sparse matrix of type '<class 'numpy.float64'>'
    	with 55 stored elements in Compressed Sparse Row format>




```python
classifier.predict(test) # test phase
```




    array([0], dtype=int32)



#### Result:  this specific article has been assigned to cluster 0  

（‐＾▽＾‐） - Anyi
