
# RDP News Images

## Introduction

"A picture is worth a thousand words"- they say.  

But be that as it may, we find that pictures or, nowdays, digital images, capture of our life.  As life is refelected in news, and images are an indelible part of news, images also are an important part of RDP news.  

Refinitiv Data Platform (RDP) provides access to a broad range of content including rich slef-described news and images that are part of news.

Next, we are going to talk about requesting images that are a part of RDP news.  Currently, RDP Top News and RDP News Reports Online are the two types of news that are made available with images.

## Prepare to Access RDP News

To interact with RDP platform we require valid RDP credentials and setup:
1. Import required libraries and define RDP endpoint paths and constants.
2. Load valid permissioned RDP credentials
3. Authenticate with RDP using credentials to obtain a valid token
These steps are included in the companion code examples hosted on GitHub (see References section) and are described in detail in many RDP articles, for example [https://developers.refinitiv.com/en/article-catalog/article/exploring-news-metadata-refinitiv-data-platform-and-python](https://developers.refinitiv.com/en/article-catalog/article/exploring-news-metadata-refinitiv-data-platform-and-python), therefore, we ommit the detailed discussion of these steps here and next next we focus on requesting RDP images.

## Request RDP Images
Let us first look at requesting  images that are made available with Top News

### Request Top News Hierarchy
```
def getTopNews():
    news_category_URL = "/data/news"
    topnews_endpoint_URL = "/top-news"

    REQUEST_URL = base_URL + news_category_URL + RDP_version + topnews_endpoint_URL 

    accessToken = getToken();
    print("Requesting: ",REQUEST_URL)
    
    acceptValue = "application/json"
    dResp = requests.get(REQUEST_URL, headers = {"Authorization": "Bearer " + accessToken, "Accept": acceptValue});
    if dResp.status_code != 200:
        print("Unable to get data. Code %s, Message: %s" % (dResp.status_code, dResp.text));
        if dResp.status_code != 401:   # error other then token expired
            return("") 
        accessToken = getToken();     # token refresh on token expired
    else:
        print("Resource access successful")
        return dResp.text
    
txt = getTopNews()
jResp = json.loads(txt);
print(json.dumps(jResp, indent=2));
```
Resulting in output:
![TopNewsHierarchy.gif](https://zfarberov.github.io/RDPNewsImages/TopNewsHierarchy.gif)

### Select Top News Package Id
From the hierarchy, we select the news package of interest to us, for example, 
Main -> World News:
```
myTopNewsId = jResp['data'][0].get('pages')[3].get('topNewsId')
```
![newsPackageId.gif](https://zfarberov.github.io/RDPNewsImages/newsPackageId.gif)

### Request Top News Item Per Package Id
```
def getTopNewsPerId(id):
    news_category_URL = "/data/news"
    topnews_endpoint_URL = "/top-news/"

    REQUEST_URL = base_URL + news_category_URL + RDP_version + topnews_endpoint_URL + id

    accessToken = getToken();
    print("Requesting: ",REQUEST_URL)
    
    acceptValue = "application/json"
    dResp = requests.get(REQUEST_URL, headers = {"Authorization": "Bearer " + accessToken, "Accept": acceptValue});
    if dResp.status_code != 200:
        print("Unable to get data. Code %s, Message: %s" % (dResp.status_code, dResp.text));
        if dResp.status_code != 401:   # error other then token expired
            return("") 
        accessToken = getToken();     # token refresh on token expired
    else:
        print("Resource access successful")
        return dResp.text
    
txt = getTopNewsPerId(myTopNewsId)
jResp2 = json.loads(txt);
print(json.dumps(jResp2, indent=2));
```
Resulting in:
![topNewsItem.gif](https://zfarberov.github.io/RDPNewsImages/topNewsItem.gif)
Let us look closely at top news item's "data", and observe that it contains "image" that contains "id.
So next we
### Select News Image ID
In this example, we select first- [0]
```
myImageId = jResp2['data'][0].get('image').get('id')
myImageId
```
![topNewsImageId.gif](https://zfarberov.github.io/RDPNewsImages/topNewsImageId.gif)

And now we are ready to
### Request Top News Image
We request with selected Image ID, store into a file as well as display
```
def getTopNewsImage(imageId):
    news_category_URL = "/data/news"
    image_endpoint_URL = "/images/"

    REQUEST_URL = base_URL + news_category_URL + RDP_version + image_endpoint_URL + imageId

    accessToken = getToken();
    print("Requesting: ",REQUEST_URL)
    
    acceptValue = "image/jpeg"
    dResp = requests.get(REQUEST_URL, headers = {"Authorization": "Bearer " + accessToken, "Accept": acceptValue});
    if dResp.status_code != 200:
        print("Unable to get data. Code %s, Message: %s" % (dResp.status_code, dResp.text));
        if dResp.status_code != 401:   # error other then token expired
            return("") 
        accessToken = getToken();     # token refresh on token expired
    else:
        print("Resource access successful")
        return dResp.content
    
imgContent = getTopNewsImage(myImageId)
file = open(myImageId+'.jpg', "wb")
file.write(imgContent)
file.close()

from IPython.display import Image
Image(filename=myImageId+'.jpg') 
```
![topNewsImage.gif](https://zfarberov.github.io/RDPNewsImages/topNewsImage.gif)

## References

* The code examples referenced in this article:  
[https://github.com/Refinitiv-API-Samples/Example.RDPAPI.Python.NewsTopImages](https://github.com/Refinitiv-API-Samples/Example.RDPAPI.Python.NewsTopImages)
[https://github.com/Refinitiv-API-Samples/Example.RDPAPI.Python.NewsOnlineReportsImages](https://github.com/Refinitiv-API-Samples/Example.RDPAPI.Python.NewsOnlineReportsImages)
* RDP News API User and Design Guide: 
[https://developers.refinitiv.com/en/api-catalog/refinitiv-data-platform/refinitiv-data-platform-apis/documentation#news-user-guide](https://developers.refinitiv.com/en/api-catalog/refinitiv-data-platform/refinitiv-data-platform-apis/documentation#news-user-guide)
