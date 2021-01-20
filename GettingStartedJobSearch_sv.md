# Sök API för jobbannonser - Kom igång

Syftet med denna text är att gå igenom vad du ser i Swagger GUI, för att ge dig en uppfattning om vad du kan göra med Job Search API. Om du bara letar efter ett sätt att hämta alla annonser, använd vårt Stream-API. 
Sök-API: et är avsett för sökningar inte för att ladda ner alla jobbannonser. Vi kan komma att ogiltigförklara din API-nyckel om du gör för stora mängder anrop som inte passar det avsedda syftet med detta API.

Ett dåligt exempel innebär tillexempel att du gör en sökning efter alla jobb i alla regioner var femte minut. 
Ett bra exempel innebär att du gör många olika anrop initierade av riktiga användare.

# Innehållsförteckning 
* [Autentisering ](#Autentisering )
* [Endpoints](#Endpoints)
* [Resultat](#Resultat)
* [Fel](#Fel)
* [Användarfall](#Användarfall)
* [Vad händer](#vad-händer)


## Kort introduktion

Endpoints i API:et är:
* [search](#Ad-Search) - returnerar annonser som matchar en sökfras.
* [complete](#Typeahead) - returnerar vanliga ord som matchar en sökfras. Användbar för autocomplete.
* [ad](#Ad) - returnerar annonsen som matchar ett id.
* [logo](#Logo) -returnerar logotypen för en annons.

Det enklaste sättet att testa API: et är att gå till  [Swagger-GUI](https://jobsearch.api.jobtechdev.se/).
Men först behöver du en nyckel för att autentisera dig själv.

## Autentisering
För detta API behöver du registrera din egen API-nyckel på [apirequest.jobtechdev.se](https://apirequest.jobtechdev.se)

## Endpoints
Nedan visar vi bara webbadresserna. Om du föredrar curl-kommandot skriver du det som:

	curl "{URL}" -H "accept: application/json" -H "api-key: {proper_key}"
	
### Ad search 
/search?q={search text}

Search endpointen i det första avsnittet returnerar jobbannonser som för närvarande är öppna för ansökan. API: et är avsett för sökning, vi vill erbjuda dig möjligheten att bara bygga ditt eget anpassade GUI ovanpå vårt fritextfält "q" i / search så här ...

	https://jobsearch.api.jobtechdev.se/search?q=Flen
	
Det betyder att du inte behöver oroa dig för hur du bygger en avancerad logik för att hjälpa användarna att hitta de mest relevanta annonser för, låt oss säga, Flen. 
Sökmotorn kommer att göra detta åt dig. 
Om du vill begränsa sökresultatet på andra sätt än via frisök kan du använda tillgängliga sökfilter. 

Vissa av filtren behöver id som inmatning för att söka strukturerad data. 
Id finns i  [Taxonomy API](https://jobtechdev.se/docs/apis/taxonomy/). 
Dessa ID hjälper dig att få skarpare träffar för strukturerad data. 
Vi kommer alltid att arbeta med att förbättra träffarna för frisökningmed förhoppning om att du får mindre och mindre användning för filtrering.


### Typeahead
/complete?q={typed string}

Om du vill hjälpa dina slutanvändare med förslag kan du använda typeahead-funktionen, som returnerar vanliga termer som finns i jobbannonserna. Detta ska fungera bra med en auto completefunktion i din sökruta. Om du begär ...

	https://jobsearch.api.jobtechdev.se/complete?q=stor
	
... får du storkök, storhushåll, storesupport och storage eftersom de är de vanligaste termerna som börjar med "stor *" i annonser.

Om du har ett mellanslag i slutet av begäran

	https://jobsearch.api.jobtechdev.se/complete?q=storage%20s

... får du sverige, stockholms län, stockholm, svenska och script eftersom de är de vanligaste termerna som börjar med "s" för annonser som innehåller ordet "storage”

### Ad
/ad/{id} 

Denna endpoint används för att hämta specifika jobbannonser med all tillgänglig metadata, efter deras annons-ID-nummer. ID-numret kan hittas genom att göra en sökfråga.

	https://jobsearch.api.jobtechdev.se/ad/8430129

### Logo
/ad/{id}/logo

Den här endpointen returnerar logotypen för en given annons id-nummer.

	https://jobsearch.api.jobtechdev.se/ad/8430129/logo

Om det inte finns någon logotyp returneras en vit bild på 1x1 pixelstorlek.


### Kodexempel
Kodexempel för åtkomst till api finns i 'getting-started-code-examples'
https://github.com/JobtechSwe/getting-started-code-examples


### Jobtech-Taxonomi
Om du behöver hjälp med att hitta de officiella namnen för yrken, färdigheter eller geografiska platser hittar du dem i vårt Taxonomi API.

 [Taxonomy API](https://jobtechdev.se/docs/apis/taxonomy/)

## Resultat
Resultaten av dina frågor kommer att vara i  [JSON](https://en.wikipedia.org/wiki/JSON) format. Vi försöker inte förklara attribut för attribut i detta dokument. 
Istället har vi bestämt oss för att försöka inkludera detta i datamodellen som du hittar i vårt
[Swagger-GUI](https://jobsearch.api.jobtechdev.se).

Lyckade frågor har en svarskod på 200 och ger dig en resultatuppsättning som består av:
1. Viss metadata om din sökning, t.ex. antal träffar och den tid det tog att utföra frågan och
2. Annonserna som matchade din sökning.


## Fel
Misslyckade frågor får följande responkoder:

| HTTP Status code | Reason | Explanation |
| ------------- | ------------- | -------------|
| 400 | Bad Request | Något fel i frågan |
| 401 | Unauthorized | Du använder inte en giltig API-nyckel eller använder den på fel sätt|
| 404 | Missing ad | Annonsen du begärde är inte tillgänglig |
| 429 | Rate limit exceeded | Du har skickat för många förfrågningar på en viss tid|
| 500 | Internal Server Error | Något fel på serversidan |



## Användarfall
För att hjälp dig framåt, så finns några exempel på användarfall:


* [Sökning med wildcard](#Sökning-med-wildcard)
* [Fras sökning](#Fras-sökning)
* [Sökning efter specifik jobbtitel](#Sökning-efter-specifik-jobbtitel)
* [Sökning inom specifikt arbetsområdet](#Sökning-inom-specifikt-arbetsområde)
* [Filtrera arbetsgivare utifrån organisationsnummer](#Filtrera-arbetsgivare-utifrån-organisationsnumer)
* [Hitta jobb nära dig](#Hitta-jobb-nära-dig)
* [Negativ sökning](#Negativ-sökning)
* [Hitta svenskspråkiga jobb utomlands](#Hitta-svenskspråkiga-jobb-utomlands)
* [Customise the result set](#Customise-the-result-set)
* [Hämta alla jobb mellan viss tid och datum](#Hämta-alla-jobb-mellan-viss-tid-och-datum)
* [Enkel fritext sökning](#Enkel-fritext-sökning)


#### Sökning med Wildcard
För vissa termer är det enklaste sättet att hitta allt du vill ha genom ett jokertecken. Ett exempel från en användare som begärde denna typ av sökning var för museijobb där både sökningar efter "museum" och de olika jobbtitlar som börjar med "musei" skulle vara relevanta träffar något som informationsstrukturen för närvarande inte slår samman så bra. Från version 1.8.0

Request URL
	
	https://jobsearch.api.jobtechdev.se/search?q=muse*

#### Frassökning
För att söka en fras i annonstexten, använd q parametern och skriv frasen inom situationstecken, såhär: "den här frasen"
För att anropa den behöver du omvanlda till HTML koden %22

Request URL

	https://jobsearch.api.jobtechdev.se/search?q=%22search%20for%20this%20phrase%22

#### Sökning efter en specifik jobtitel
Enklaste sättet att få annonser som innehåller ett specifikt ord som tillexempel en jobbtitel, är att använda fritext (q) söking tillsammans med  _search_ endpoint.
Resultatet kommer då att bli annonser som innehåller det specifika ordet i endera rubriken, anninsbeskrivningen eller i arbetsplatsnamnet.

Request URL

	https://jobsearch.api.jobtechdev.se/search?q=souschef


If you want to be certain that the ad is for a "souschef" - and not just mentions a "souschef" - you can use the occupation ID in the field "occupation". If the ad has been registered by the recruiter with the occupation field set to "souschef", the ad will show up in this search. To do this query you use both the [Taxonomy API](https://jobtechdev.se/docs/apis/taxonomy/)  and the _search_ endpoint. First of all, you need to find the occupation ID for "souschef" in the [Taxonomy API](https://jobtechdev.se/docs/apis/taxonomy/)  for the term in the right category (occupation-name).
	
**NB! the old endpoint (~~jobsearch.api.jobtechdev.se/taxonomy/~~) is deprecated and will be removed. Use our [Taxonomy API](https://jobtechdev.se/docs/apis/taxonomy/) instead** 

Now you can use the conceptId (iugg_Qq9_QHH) in _search_ to fetch the ads registered with the term "souschef" in the occupation-name field:

Request URL
	
	https://jobsearch.api.jobtechdev.se/search?occupation-name=iugg_Qq9_QHH
	
This will give a smaller result set with a higher certainty of actually being for a "souschef", however the result set will likely miss a few relevant ads since the occupation-name field isn't always set by employers. You should find that a larger set is more useful since there are multiple sorting factors working to show the most relevant hits first. We're also working to always improve the API in regards to unstructured data.

### Searching only within a specific field of work
Firstly, use the [Taxonomy API](https://jobtechdev.se/docs/apis/taxonomy/) to get the Id for Data/IT (occupation field). You'll then make a free text search on the term "IT" narrowing down the search to occupation-field

In the response body you’ll find the conceptId (apaJ_2ja_LuF)for the term Data/IT. Use this with the search endpoint to define the field in which you want to get. So now I want to combine this with my favorite programming language without all those snake related jobs ruining my search.

Request URL

	https://jobsearch.api.jobtechdev.se/search?occupation-field=apaJ_2ja_LuF&q=python
	
In a similar way, you can use the [Taxonomy API](https://jobtechdev.se/docs/apis/taxonomy/) to find conceptIds for the parameters _occupation-group_ and _occupation-collection_

_occupation-collection_ can be used in combination with _occupation-name_, _occupation-field_ and _occupation-group_ and the search will show ads that are in ALL (AND condition between parameters)

	
### Filtering employers using organisation number
If you want to list all the jobs with just one employer you can use the swedish organisation number from Bolagsverket. For example its possible to take Arbetsförmedlingens number 2021002114 and basically use that as a filter

Request URL
	
	https://jobsearch.api.jobtechdev.se/search?employer=2021002114
	
The filter makes a preix search as a default, like a wild card search without the need for an asterix. So a good example of the usefulness of this is to take advantage of the fact that all governmental employers in sweden have org numbers that start with a 2. So you could make a request for Java jobs within the public sector like this.

Request URL

	https://jobsearch.api.jobtechdev.se/search?employer=2&q=java


### Finding jobs near you
You can filter your search on geographical terms picked up from the Taxonomy just the same way you can with occupation-titles and occupation-fields. (Concept_id doesn't work everywhere at the time of writing but you can use the numeral id's, they are very official and way less likely to change as skills and occupations sometimes do)
If you want to search for jobs in Norway you can find the conceptId for "Norge" in the [Taxonomy API](https://jobtechdev.se/docs/apis/taxonomy/) 

And add that parameter conceptId (QJgN_Zge_BzJ) to the country field

Request URL

	https://jobsearch.api.jobtechdev.se/search?country=QJgN_Zge_BzJ

If I make a query which includes 2 different geographical filters the most local one will be promoted. As in this case where I'm searching for "lärare" using the municipality code for Haparanda (tfRE_hXa_eq7) and the region code for Norrbottens Län (9hXe_F4g_eTG). The jobs that are in Haparanda will be the first ones in the result set.

	https://jobsearch.api.jobtechdev.se/search?municipality=tfRE_hXa_eq7&region=9hXe_F4g_eTG&q=l%C3%A4rare


You can also use latitude, longitude coordinates and a radius in kilometers if you want.

Request URL

	https://jobsearch.api.jobtechdev.se/search?position=59.3,17.6&position.radius=10


### Negative search
So, this is very simple using our q-field. Let's say you want to find Unix jobs

Request URL

	https://jobsearch.api.jobtechdev.se/search?q=unix

But you find that you get a lot of jobs expecting you to work with Linux which you don't want. All that's needed is to use the minus symbol and the word you want to exclude.

Request URL

	https://jobsearch.api.jobtechdev.se/search?q=unix%20-linux

### Finding Swedish speaking jobs abroad
Sometimes a filter can work too broadly and then it's easier to use a negative search to remove specific results you don't want. In this case we will show you how to filter out all the jobs in Sweden. Rather than adding a minus Sweden in the q field "-sverige" you can use the country code and the country field in the search. So first you get the country code for "Sverige" from the [Taxonomy API](https://jobtechdev.se/docs/apis/taxonomy/) .

As return we get conceptId i46j_HmG_v64 for "Sverige" and conceptId zSLA_vw2_FXN for "Svenska".

Request URL to get jobs in Swedish outside Sweden

	https://jobsearch.api.jobtechdev.se/search?language=zSLA_vw2_FXN&country=-i46j_HmG_v64


### Customise the result set
There's a lot of reasons you might want less fields for your search result set. In this case the idea is a map-based job search that plots needles where the jobs can be found based on a user search. Everything needed is the GPS coordinates for the needle and the id, employer, and headline for the ad so more info can be fetched once the user clicks on the needle. Probably, you also like to know total number of ads.
In the Swagger GUI it's possible to use the X-fields to define which fields to include in result set. This mask will look like this

 	total{value}, hits{id, headline, workplace_address{coordinates}, employer{name}}

 This will create an extra header displayed in the curl example in Swagger. So, this example will look like this

 	curl "https://jobsearch.api.jobtechdev.se/search?q=skogsarbetare" -H "accept: application/json" -H "api-key: <proper_key>" -H "X-Fields: total{value}, hits{id, headline, workplace_address{coordinates}, employer{name}}"



### Getting all the jobs since date and time
A very common use case is COLLECT ALL THE ADS. We don't want you to use the search API for this. It's expensive in terms of band width, CPU cycles and development time and it's not even guaranteed you'll get everything. Instead we'd like you to use our [Stream API](https://jobstream.api.jobtechdev.se).


### Simple freetext search
To disable the smart search features of the q-field, set the header `x-feature-disable-smart-freetext` to `true`. The result will be that the q-field will work like a simple text search in the ads' header and description fields.


# Whats next
What's up for job ads - What we plan working on

Besides the forever ongoing work of improving the search algorithm we are right now working on building a statistic API around published ads and made searches. Stay tuned for updates.
# docs
Documentation and getting starteds
