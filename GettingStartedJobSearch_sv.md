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
Vi kommer alltid att arbeta med att förbättra träffarna för frisökning med förhoppning om att du får mindre och mindre användning för filtrering.


### Typeahead
/complete?q={typed string}

Om du vill hjälpa dina slutanvändare med förslag kan du använda typeahead-funktionen, som returnerar vanliga termer som finns i jobbannonserna. Detta ska fungera bra med en auto completefunktion i din sökruta. Om du gör en request för ...

	https://jobsearch.api.jobtechdev.se/complete?q=stor
	
... får du storkök, storhushåll, storesupport och storage eftersom de är de vanligaste termerna som börjar med "stor *" i annonser.

Om du har ett mellanslag i slutet av 

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
| 404 | Missing ad | Annonsen du försökte hämta är inte tillgänglig |
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
* [Anpassa resultatet](#Anpassa-resultatet)
* [Hämta alla jobb mellan viss tid och datum](#Hämta-alla-jobb-mellan-viss-tid-och-datum)
* [Enkel fritext sökning](#Enkel-fritext-sökning)


#### Sökning med Wildcard
För vissa termer är det enklaste sättet att hitta allt du vill ha genom ett jokertecken. Ett exempel från en användare som begärde denna typ av sökning var för museijobb där både sökningar efter "museum" och de olika jobbtitlar som börjar med "musei" skulle vara relevanta träffar något som informationsstrukturen för närvarande inte slår samman så bra. Från version 1.8.0

Request URL
	
	https://jobsearch.api.jobtechdev.se/search?q=muse*

#### Frassökning
För att söka en fras i annonstexten, använd q parametern och skriv frasen inom situationstecken: "den här frasen"
För att anropa den behöver du omvandla till HTML koden %22

Request URL

	https://jobsearch.api.jobtechdev.se/search?q=%22search%20for%20this%20phrase%22

#### Sökning efter en specifik jobbtitel
Enklaste sättet att få annonser som innehåller ett specifikt ord som tillexempel en jobbtitel, är att använda fritext (q) söking tillsammans med  _search_ endpoint.
Resultatet kommer då att bli annonser som innehåller det specifika ordet i endera rubriken, anninsbeskrivningen eller i arbetsplatsnamnet.

Request URL

	https://jobsearch.api.jobtechdev.se/search?q=souschef


Om du vill vara säker på att annonsen gäller en "souschef" och inte bara namner ordet "souschef" - kan du använda "occupation ID" i fältet "occupation".
Om annonsen har registrerats av arbetsgivaren med "souschef" i fältet "occupation", kommer annonsen visas i denna sökning.
För att göra denna sökfråga, behöver du använda både [Taxonomy API](https://jobtechdev.se/docs/apis/taxonomy/)  och _search_ endpoint. 
Först av allt behöver du hitta "occupation ID" för "souschef". Det gör du med hjälp av [Taxonomy API](https://jobtechdev.se/docs/apis/taxonomy/)  för termen i den högra kategorin (occupation-name).
	
**OBS! den gamla endpoint (~~jobsearch.api.jobtechdev.se/taxonomy/~~) är utfasad. Använd [Taxonomy API](https://jobtechdev.se/docs/apis/taxonomy/) istället** 

Nu kan du använda conceptId (iugg_Qq9_QHH) i _search_ för att hämta annonser som är registrerade med termen "souschef" i occupation-name fältet:

Request URL
	
	https://jobsearch.api.jobtechdev.se/search?occupation-name=iugg_Qq9_QHH
	
Det här kommer att ge ett mindre antal resultat men med en högre träffsäkerhet att det verkligen eftersöks en "souschef", men detta resultat kommer troligen att missa en del annonser, eftersom fältet occupation-name inte alltid är ifyllt av arbetsgivaren. Du kommer att upptäcka att ett större set är mer användbart eftersom flera sorteringsfaktorer som arbetar för att visa de mest relevanta träffarna först. Vi jobbar hela tiden för att förbättra APIet när det gäller ostrukturerad data.

### Sökning inom specifikt yrkesområde
Först, använd [Taxonomy API](https://jobtechdev.se/docs/apis/taxonomy/) för att få Id Data/IT (occupation field). Gör sedan en fritext sökning på "IT" för att specificera sökningen till occupation-fältet.

I svaret hittar du conceptId (apaJ_2ja_LuF)för termen Data/IT. Använd detta tilsammans med "search" endpoint för att definera vad du vill ha.
Så nu vill jag kombinera det här med mitt favorit programmeringsspråk utan alla liknande jobb förstör min sökning.

Request URL

	https://jobsearch.api.jobtechdev.se/search?occupation-field=apaJ_2ja_LuF&q=python
	
På liknande sätt kan du använda [Taxonomy API](https://jobtechdev.se/docs/apis/taxonomy/) för att hitta conceptId för parametrarna  _occupation-group_ och _occupation-collection_.

_occupation-collection_ kan användas i kombination med _occupation-name_, _occupation-field_ och _occupation-group_ och sökningen kommer att visa annonser som finns i alla.

	
### Filtrera arbetsgivare utifrån organisationsnummer
Om du vill lista alla jobb hos en specifik arbetsgivare kan du använda det svenska organisationsnummret från Bolagsverket.Tillexempel är det möjligt att ta Arbetsförmedlingens nummer 2021002114 helt enkelt använda det som ett filter.

Request URL
	
	https://jobsearch.api.jobtechdev.se/search?employer=2021002114
	
Filtret gör en prefix sökning som default, ungefär som en sökning med "wild card" utan att du behöver ange en asterix. Så ett gott exempel av användbarheten är fördelena att alla statliga arbetsgivare i sverige, har ett organisationsnummer som börjar med 2. Så du kan göra en förfrågan för Java jobb i den offentliga sektor så här:

Request URL

	https://jobsearch.api.jobtechdev.se/search?employer=2&q=java


### Hitta jobb nära dig
Du kan filtrera på geografiska termer som du hämtat från Taxonomi APIet på samma sätt som du kan med occupation-titles och occupation-fields. (Concept_id fungerar inte överallt ännu, men du kan använda  numeriska id'n som är oficiella och risken är liten att de förändras, som färdigheter och yrken ibland gör.
Om du vill söka efter jobb i norge, kan du hitta conceptId för "Norge" i [Taxonomy API](https://jobtechdev.se/docs/apis/taxonomy/) 

Och lägga till parametern conceptId (QJgN_Zge_BzJ) i country fältet.

Request URL

	https://jobsearch.api.jobtechdev.se/search?country=QJgN_Zge_BzJ

Om du gör en sökning med två geografiska filter kommer den mest lokala att visas först. Som i detta fall, du söker efter lärare och använde kommunkoden för Haparanda (tfRE_hXa_eq7) och regionskoden för Norbottens län (9hXe_F4g_eTG). Jobben som finns i Haparanda kommer att vara de som visas först i listan. 

	https://jobsearch.api.jobtechdev.se/search?municipality=tfRE_hXa_eq7&region=9hXe_F4g_eTG&q=l%C3%A4rare


Du kan också använda latitude, longitude koordinater och radie i kilometer om du vill.

Request URL

	https://jobsearch.api.jobtechdev.se/search?position=59.3,17.6&position.radius=10


### Negativ sökning
Så, det här är väldigt enkelt om du använder q-fältet. Tillexempel, du vill hitta Unix jobb.

Request URL

	https://jobsearch.api.jobtechdev.se/search?q=unix

Men du upptäcker att du får flera träffar för Linux jobb, vilket du inte alls vill. Det enda du behöver göra är att sätta ett - tecken och det ord du vill utesluta


Request URL

	https://jobsearch.api.jobtechdev.se/search?q=unix%20-linux

### Hitta svenskspråkiga jobb utomlands
Ibland kan ett filter bli för brett och då är det enklare att använda en negativ sökning för att ta bort specifika resultat som du inte vill ha.
I det här fallet visar vi dig hur du kan filtrera bort alla jobb i sverige. Istället för att lägga till ett - tecken i q fältet "-sverige" kan du använda landskoden och country field i sökningen.  Så först letar du rätt på landskoden för sverige i[Taxonomy API](https://jobtechdev.se/docs/apis/taxonomy/) .

Till svar får du conceptId i46j_HmG_v64 för "Sverige" och conceptId zSLA_vw2_FXN för "Svenska".

Request URL för att få svenskspråkiga jobb yutanför sverige.

	https://jobsearch.api.jobtechdev.se/search?language=zSLA_vw2_FXN&country=-i46j_HmG_v64


### Anpassa resultatet
Det finns flera anledningar till att du kanske vill ha mindre fält i din resultatlista. I det här fallet är idén en sökning som visar jobben på en karta beroende vad användaren söker. Det som behövs är GPS koordinaterna för markeringen på kartan och id, arbetsgivare och rubriken på annonsen så mer information kan hämtas när användare klickar på annonsmarkeringen. Antagligen vill du också vet totala antalet annonser.
I Swagger GUI är det möjligt att använda X-fälten för att definera vilka fält som ska tas med i resultatet.

 	total{value}, hits{id, headline, workplace_address{coordinates}, employer{name}}

 Det här skapar en extra header som visas i curl example i Swagger. Så, det här exemplet kommer att se ut så här:

 	curl "https://jobsearch.api.jobtechdev.se/search?q=skogsarbetare" -H "accept: application/json" -H "api-key: <proper_key>" -H "X-Fields: total{value}, hits{id, headline, workplace_address{coordinates}, employer{name}}"



### Hämta alla jobb mellan viss tid och datum
Ett väldigt vanligt abvändarfall är att hämta ALLA ANNONSER. Vi vill inte att du använde Job Search API för detta. Det tar mycket bandbredd, CPU och utvecklingstid och det är inte ens garanterat att du får alla annonser. Om du vill hämta alla annonser rekomenderar vi att du använder [Stream API](https://jobstream.api.jobtechdev.se).


### Enkel fritext sökning
För att inaktivera de smarta sökfunktionerna sätt header `x-feature-disable-smart-freetext` till `true`. Resultatet blir att q fältet kommer att fungera som en enkel textsökning i annonsens rubrik och beskrivningsfält.


# Vad händer härnäst
Förutom det ständigt pågående arbetet med att förbättra sök-algoritmen, så jobbar vi på ett söktrends API om publicerade annonser och gjorda sökningar.

