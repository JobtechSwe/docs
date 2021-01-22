# Search API for job links - getting started

The aim of this text is to walk you through what you're seeing in the [Swagger-GUI](https://links.api.jobtechdev.se) to give you a bit of orientation on what can be done with the Job Search API. If you are just looking for a way to fetch all ads please use our [Stream API](https://jobtechdev.se/docs/apis/jobstream/)
The search API is intended for user search not downloading all job ads. We may invalidate your API Key if you make excessive amounts of calls that don't fit the intended purpose of this API.

A bad practice typically means searching for every job of every region every fifth minute.
A good practice means making lots of varied calls initiated by real users.

# Table of Contents
* [Endpoints](#Endpoints)
* [Results](#Results)
* [Errors](#Errors)
* [Use cases](#Use-cases)
* [Whats up](#whats-next)


## Short introduction

The endpoints for the ads search API are:
* [search](#Ad-Search) - returning ads matching a search phrase.
* [ad](#Ad) - returning the ad matching an id.

The easiest way to try out the API is to go to the [Swagger-GUI](https://links.api.jobtechdev.se).



## Endpoints
Below we only show the URL's. If you prefer the curl command, you type it like:

	curl "{URL}" -H "accept: application/json" -H "api-key: {proper_key}"
	
### Ad search 
/search?q={search text}

The search endpoint in the first section will return job ads that are currently open for applications.
The API is meant for searching, we want to offer you the possibility to just build your own customized GUI on top of our free text query field "q" in /search like this ...

	https://links.api.jobtechdev.se/joblinks?q=Flen
	
This means you don't need to worry about how to build an advanced logic to help the users finding the most relevant ads for, let's say, Flen. The search engine will do this for you.
If you want to narrow down the search result in other ways than the free query offers, you can use the available search filters. Some of the filters need id-keys as input for searching structured data. The ids can be found in the [Taxonomy API](https://jobtechdev.se/docs/apis/taxonomy/). These ids will help you get sharper hits for structured data. We will always work on improving the hits for free queries hoping you'll have less and less use for filtering.


### Ad
/ad/{id} 

This endpoint is used for fetching specific job ads with all available meta data, by their ad ID number. The ID number can be found by doing a search query.

	https://links.api.jobtechdev.se/ad/8430129


### Code examples
Code examples for accessing the api can be found in the 'getting-started-code-examples' repository on Github: 
https://github.com/JobtechSwe/getting-started-code-examples


### Jobtech-Taxonomy 
If you need help finding the official names for occupations, skills, or geographic locations you will find them in our [Taxonomy API](https://jobtechdev.se/docs/apis/taxonomy/)

## Results
The results of your queries will be in [JSON](https://en.wikipedia.org/wiki/JSON) format. We won't attempt to explain this attribute by attribute in this document. Instead we've decided to try to include this in the data model which you can find in our [Swagger-GUI](https://jobsearch.api.jobtechdev.se).

Successful queries will have a response code of 200 and give you a result set that consists of:
1. Some meta data about your search such as number of hits and the time it took to execute the query and 
2. The ads that matched your search. 


## Errors
Unsuccessful queries will have a response code of:

| HTTP Status code | Reason | Explanation |
| ------------- | ------------- | -------------|
| 400 | Bad Request | Something wrong in the query |
| 404 | Missing ad | The ad you requested is not available |
| 500 | Internal Server Error | Something wrong on the server side |



## Use cases 
To help you find your way forward, here are some example of use cases:

* [Searching using Wildcard](#Searching-using-Wildcard)
* [Phrase search](#Phrase-search)
* [Searching for a particular job title](#Searching-for-a-particular-job-title)
* [Searching only within a specific field of work](#Searching-only-within-a-specific-field-of-work)
* [Filtering employers using organisation number](#Filtering-employers-using-organisation-number)
* [Finding jobs near you](#Finding-jobs-near-you)
* [Negative search](#Negative-search)
* [Finding Swedish speaking jobs abroad](#Finding-Swedish-speaking-jobs-abroad)
* [Getting all the jobs since date and time](#Getting-all-the-jobs-since-date-and-time)
* [Simple freetext search](#Simple-freetext-search)


#### Searching using Wildcard
For some terms the easiest way to find everything you want is through a wildcard search. An example from a user requesting this kind of search was for museum jobs where both searches for "museum" and the various job titles starting with "musei" would be  relevant hits which the information structure currently dont merge very well. From version 1.8.0

Request URL
	
	https://links.api.jobtechdev.se/joblinks?q=muse*

#### Phrase search
To search in the ad text for a phrase, use the q parameter and surround the phrase with double quotes (%22).

Request URL

	https://links.api.jobtechdev.se/joblinks?q=%22search%20for%20this%20phrase%22

#### Searching for a particular job title
The easiest way to get the ads that contain a specific word like a job title is to use a free text query (q) with the _search_ endpoint. This will give you ads with the specified word in either headline, ad description or place of work.

Request URL

	https://links.api.jobtechdev.se/joblinks?q=souschef


If you want to be certain that the ad is for a "souschef" - and not just mentions a "souschef" - you can use the occupation ID in the field "occupation". If the ad has been registered by the recruiter with the occupation field set to "souschef", the ad will show up in this search. To do this query you use both the [Taxonomy API](https://jobtechdev.se/docs/apis/taxonomy/)  and the _search_ endpoint. First of all, you need to find the occupation ID for "souschef" in the [Taxonomy API](https://jobtechdev.se/docs/apis/taxonomy/)  for the term in the right category (occupation-name).
	
**NB! the old endpoint (~~jobsearch.api.jobtechdev.se/taxonomy/~~) is deprecated and will be removed. Use our [Taxonomy API](https://jobtechdev.se/docs/apis/taxonomy/) instead** 

Now you can use the conceptId (iugg_Qq9_QHH) in _search_ to fetch the ads registered with the term "souschef" in the occupation-name field:

Request URL
	
	https://links.api.jobtechdev.se/joblinks?occupation-name=iugg_Qq9_QHH
	
This will give a smaller result set with a higher certainty of actually being for a "souschef", however the result set will likely miss a few relevant ads since the occupation-name field isn't always set by employers. You should find that a larger set is more useful since there are multiple sorting factors working to show the most relevant hits first. We're also working to always improve the API in regards to unstructured data.

### Searching only within a specific field of work
Firstly, use the [Taxonomy API](https://jobtechdev.se/docs/apis/taxonomy/) to get the Id for Data/IT (occupation field). You'll then make a free text search on the term "IT" narrowing down the search to occupation-field

In the response body you’ll find the conceptId (apaJ_2ja_LuF)for the term Data/IT. Use this with the search endpoint to define the field in which you want to get. So now I want to combine this with my favorite programming language without all those snake related jobs ruining my search.

Request URL

	https://links.api.jobtechdev.se/joblinks?occupation-field=apaJ_2ja_LuF&q=python
	
In a similar way, you can use the [Taxonomy API](https://jobtechdev.se/docs/apis/taxonomy/) to find conceptIds for the parameters _occupation-group_ and _occupation-collection_

_occupation-collection_ can be used in combination with _occupation-name_, _occupation-field_ and _occupation-group_ and the search will show ads that are in ALL (AND condition between parameters)

	
### Filtering employers using organisation number
If you want to list all the jobs with just one employer you can use the swedish organisation number from Bolagsverket. For example its possible to take Arbetsförmedlingens number 2021002114 and basically use that as a filter

Request URL
	
	https://links.api.jobtechdev.se/joblinks?employer=2021002114
	
The filter makes a prefix search as a default, like a wild card search without the need for an asterix. So a good example of the usefulness of this is to take advantage of the fact that all governmental employers in sweden have org numbers that start with a 2. So you could make a request for Java jobs within the public sector like this.

Request URL

	https://links.api.jobtechdev.se/joblinks?employer=2&q=java


### Finding jobs near you
You can filter your search on geographical terms picked up from the Taxonomy just the same way you can with occupation-titles and occupation-fields. (Concept_id doesn't work everywhere at the time of writing but you can use the numeral id's, they are very official and way less likely to change as skills and occupations sometimes do)
If you want to search for jobs in Norway you can find the conceptId for "Norge" in the [Taxonomy API](https://jobtechdev.se/docs/apis/taxonomy/) 

And add that parameter conceptId (QJgN_Zge_BzJ) to the country field

Request URL

	https://links.api.jobtechdev.se/joblinks?country=QJgN_Zge_BzJ

If I make a query which includes 2 different geographical filters the most local one will be promoted. As in this case where I'm searching for "lärare" using the municipality code for Haparanda (tfRE_hXa_eq7) and the region code for Norrbottens Län (9hXe_F4g_eTG). The jobs that are in Haparanda will be the first ones in the result set.

	https://links.api.jobtechdev.se/joblinks?municipality=tfRE_hXa_eq7&region=9hXe_F4g_eTG&q=l%C3%A4rare




### Negative search
So, this is very simple using our q-field. Let's say you want to find Unix jobs

Request URL

	https://links.api.jobtechdev.se/joblinks?q=unix

But you find that you get a lot of jobs expecting you to work with which you don't want. All that's needed is to use the minus symbol and the word you want to exclude.

Request URL

	https://links.api.jobtechdev.se/joblinks?q=unix%20-linux

### Finding Swedish speaking jobs abroad
Sometimes a filter can work too broadly and then it's easier to use a negative search to remove specific results you don't want. In this case we will show you how to filter out all the jobs in Sweden. Rather than adding a minus Sweden in the q field "-sverige" you can use the country code and the country field in the search. So first you get the country code for "Sverige" from the [Taxonomy API](https://jobtechdev.se/docs/apis/taxonomy/) .

As return we get conceptId i46j_HmG_v64 for "Sverige" and conceptId zSLA_vw2_FXN for "Svenska".

Request URL to get jobs in Swedish outside Sweden

	https://links.api.jobtechdev.se/joblinks?language=zSLA_vw2_FXN&country=-i46j_HmG_v64




