# API documentation

## Use of production API

To use the most recent deployed version of this API [click here to head over to RapidAPI](https://rapidapi.com/sanglin-TlEqlfGPc/api/energy-price-news) where you can sign up to recieve your `X-RapidAPI-Key` to use in your own application that wishes to make requests to this resource.

There you can also use their "API Playground" to test out endpoints, see real responses and also get excellent suggested code snippets to implement the API into projects on a number of popular languages and frameworks, including:

- Node.js
	- Axios
	- jQuery
	- fetch
- PHP
	- cURL
	- HTTPv1/v2
- Python
	- http.client

## Use in development

The purpose of this documentation is to help with working with the available endpoints while developing the project, so all endpoints used as examples from this point will refer to `localhost`. The main purpose of the API is to gather articles from various news sources that mention "Energy" and in particular "Energy Prices" and as the project has grown the list of sources has grown along with it leading to us needing to implement a few features that will be described here.

- [Endpoints](#endpoints)
	- [All articles](#all-articles)
	- [Articles by single source](#articles-by-single-source)
	- [Sources list](#sources-list)
	- [Sources by region](#sources-by-region)
	- [List of regions](#list-of-regions)
- [Features](#features)
	- [Pagination](#pagination)

> **Note**
> The following links to examples of the endpoints assume that you already have the project cloned, installed and running on your system. See the README docs for how to do that.

## Endpoints

### All articles

[/api/news](http://localhost:8000/api/news)

This is the base and most simple endpoint that will provide a JSON of all scraped articles from all sources. The structure for a returned article is as follows:
```json
  "JB3R6WYKZR": {
    "title": "How to claim the £400 energy rebate amid the cost of living crisis",
    "url": "https://12ft.io/https://www.telegraph.co.uk/money/consumer-affairs/government-energy-bill-rebate-help-pay-how-claim-savings-2022/",
    "source": "The Telegraph",
    "image": "https://raw.githubusercontent.com/MizouziE/energy-prices-api/master/public/img/energy-prices-api-socials.png"
  },
```
You get the title, the url, the name of the source and some related image (if not the default image of this project).

### Articles by single source

[/api/news/sources/{source}](http://localhost:8000/api/news/sources/skynews)

These endpoints use the name of a single news source to retrieve **only the articles from that particular news source**. In the example above it will show for Sky News, but any of the avaiable sources can be used.

> **Note**
> The name of the source must have spaces omitted, otherwise it will return a 404 error.

### Sources list

[/api/news/sources](http://localhost:8000/api/news/sources)

This endpoint provides a list and some detail about the available sources, structured as follows:
```json
  "TC4P5PN4W4": {
    "SourceName": "Zee News",
    "SourceEndPoint": "http://energy-price-news-api.herokuapp.com/api/news/zeenews",
    "Image": "https://english.cdn.zeenews.com/images/logo/placeholder_image.jpg",
    "Region": "Asia"
  },
```
You get the name, url for it's individual endpoint, an image (or default) as well as the region that news source is attributed to.

### Sources by region

[/api/news/regions/{region}](http://localhost:8000/api/news/regions/europe)

This endpoint will list sources that are attributed to a single region, Europe in the above example. The objects returned have the same structure demonstrated in the section above with details of the individual sources.

### List of regions

[/api/news/regions](http://localhost:8000/api/news/regions)

This endpoint will give a list of regions that we currently have sources from. Each region retrieved will have it's name and a url to a direct link to that region's list of sources.

## Features

### Pagination

[/api/news?page=1&limit=8](http://localhost:8000/api/news?page=1&limit=8)

In the above example, you will see page 1 with the first 8 results, plus a link to the next page (page 2) plus a list of other details tht are described below.

Here is the necessary information regaridng the pagination feature:
By adding query parameters `page=` and `limit=` any route can be paginated.

The `page=` parameter is necessary while `limit=` is optional and by default set at 5.

If neither of the parameters are passed, a list of results are returned, otherwise
a list of the following format is returned:
```json
{
	"previous": "link to the previous page if it exists", // http://localhost:8000/api/news...
	"next" : "link to the next page if it exists ", // http://localhost:8000/api/news...
	"total" : "the numbers of total items for that route ", // 47
	"pages" : "the total number of pages (total/limit)", // 5
	"limit" : "the number of object to be returned in results", // 9
	"currentPage": "current page number", // 3
	"results" : {
		"a list of results, length being equal to the limit set" // the articles listed in the usual way
	}
}
```
### Truncation of Article Titles

[/api/news/zeenews?trunc=25](http://localhost:8000/api/news/zeenews?trunc=25)

As some of the returned articles can have excessively long titles which may interfere with some frontend apps layout, it is possible to truncate (or clip) the titles returned by the API calls.

By specifying the query parameter `trunc=` you may choose the integer value max character length of the titles returned. An ellipsis (...) will be appended automatically to indicate that it is truncated, so please consider the extra 3 characters if your usage is very length specific.

A default minimum of 10 characters is set to prevent titles being totally illegible.

The above usage would create the following:
```json
{
	"PFDUUTFB4D":{
		"title":"PM Modi pointed out that ...",
		"url":"https://zeenews.india.com/india/indias-energy-sector-to-be-growth-centric-industry-friendly-environment-conscious-pm-narendra-modi-2320293.html",
		"source":"Zee News",
		"image":"https://english.cdn.zeenews.com/images/logo/placeholder_image.jpg"
		},
	"SomeID":	{
		// ...
		}
}
```

from a non-truncated:
```json
{
	"PFDUUTFB4D":{
		"title":"PM Modi pointed out that India is the third-largest and the fastest-growing aviation market in terms of domestic aviation. He stated that Indian carriers are projected to increase their fleet size from 600 to 1200 by 2024.PM said the target to increase the installed renewable energy capacity to 175 GW by 2022 has been further extended to 450 GW by 2030.",
		"url":"https://zeenews.india.com/india/indias-energy-sector-to-be-growth-centric-industry-friendly-environment-conscious-pm-narendra-modi-2320293.html",
		"source":"Zee News",
		"image":"https://english.cdn.zeenews.com/images/logo/placeholder_image.jpg"
		},
	"SomeID":	{
		// ...
		}
}
```