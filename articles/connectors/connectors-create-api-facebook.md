<properties
	pageTitle="Aggiungere l'API di Facebook alle app per la logica | Microsoft Azure"
	description="Panoramica dell'API di Facebook con i parametri dell'API REST."
	services=""
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="05/16/2016"
   ms.author="mandia"/>

# Introduzione all'API di Facebook
Connettersi a Facebook e pubblicare un post in una sequenza temporale, recuperare il feed di una pagina e così via. L'API di Facebook può essere usata da:

- App per la logica trattate in questo argomento
- PowerApps. Vedere l'[elenco delle connessioni PowerApps](https://powerapps.microsoft.com/tutorials/connections-list/) per l'elenco completo.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.


Con Facebook è possibile:

- Creare il flusso aziendale in base ai dati ottenuti da Facebook. 
- Usare un trigger quando si riceve un nuovo post.
- Usare azioni per pubblicare un post nel diario, ottenere il feed di una pagina e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando nel diario è presente un nuovo post, è possibile selezionare tale post ed eseguirne il push al feed di Twitter. 

Per aggiungere un'operazione nelle app per la logica, vedere [Creare una nuova app per la logica che connette servizi SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni
L'API di Facebook include i trigger e le azioni seguenti.

| Trigger | Azioni|
| --- | --- |
| <ul><li>When there is a new post on my timeline</li></ul> |<ul><li>Get feed from my timeline</li><li>Post to my timeline</li><li>When there is a new post on my timeline</li><li>Get page feed</li><li>Get user timeline</li><li>Post to page</li></ul>

Tutte le API supportano i dati nei formati JSON e XML.

## Creare una connessione a Facebook
Quando si aggiunge questa API alle app per la logica, è necessario autorizzare le app per la logica a connettersi a Facebook.

1. Accedere al proprio account Facebook
2. Selezionare **Autorizza** e consentire alle app per la logica di connettersi e usare Facebook. 

>[AZURE.INCLUDE [Passaggi per creare una connessione a Facebook](../../includes/connectors-create-api-facebook.md)]

>[AZURE.TIP] È possibile usare la stessa connessione di Facebook in altre app per la logica.

## Informazioni di riferimento sulle API REST Swagger
Si applica alla versione 1.0.

### Ottieni feed dal diario
Ottiene i feed dalla sequenza temporale dell'utente connesso. ```GET: /me/feed```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|fields|string|no|query|nessuno |Specifica i campi da restituire, ad esempio ID, nome o immagine.|
|limit|integer|no|query| nessuno|Numero massimo di post da recuperare|
|con|string|no|query| nessuno|Limita l'elenco dei post solo a quelli con percorso collegato.|
|filter|string|no|query| nessuno|Recupera solo i post che corrispondono a un filtro di flusso specifico.|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|500|Internal Server Error|
|default|Operazione non riuscita.|


### Pubblica post nel diario
Pubblica un messaggio di stato nella sequenza temporale dell'utente connesso. ```POST: /me/feed```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|post|string |sì|body|nessuno |Nuovo messaggio da pubblicare|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|500|Internal Server Error|
|default|Operazione non riuscita.|


### Quando nel diario è presente un nuovo post
Attiva un nuovo flusso in presenza di un nuovo post nella sequenza temporale dell'utente connesso ```GET: /trigger/me/feed```

Non sono disponibili parametri.

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|500|Internal Server Error|
|default|Operazione non riuscita.|


### Ottieni feed della pagina
Ottiene i post dal feed di una pagina specificata. ```GET: /{pageId}/feed```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|pageId|string|yes|path| nessuno|ID della pagina da cui devono essere recuperati i post.|
|limit|integer|no|query| nessuno|Numero massimo di post da recuperare|
|include\_hidden|boolean|no|query|nessuno |Include o esclude i post che sono stati nascosti dalla pagina|
|fields|string|no|query|nessuno |Specifica i campi da restituire, ad esempio ID, nome o immagine.|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|500|Internal Server Error|
|default|Operazione non riuscita.|


### Ottieni diario utente
Ottiene i post dalla sequenza temporale di un utente. ```GET: /{userId}/feed```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|userId|string|yes|path|nessuno |ID dell'utente la cui cronologia deve essere recuperata.|
|limit|integer|no|query|nessuno |Numero massimo di post da recuperare|
|con|string|no|query|nessuno |Limita l'elenco dei post solo a quelli con percorso collegato.|
|filter|string|no|query| nessuno|Recupera solo i post che corrispondono a un filtro di flusso specifico.|
|fields|string|no|query| nessuno|Specifica i campi da restituire, ad esempio ID, nome o immagine.|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|500|Internal Server Error|
|default|Operazione non riuscita.|


### Pubblica post nella pagina
Pubblica un messaggio in una pagina Facebook come utente connesso. ```POST: /{pageId}/feed```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|pageId|string|yes|path|nessuno |ID della pagina da pubblicare.|
|post|many |yes|body|nessuno |Nuovo messaggio da pubblicare.|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|500|Internal Server Error|
|default|Operazione non riuscita.|


## Definizioni oggetto

#### GetFeedResponse

|Nome proprietà | Tipo di dati | Obbligatorio|
|---|---|---|
|data|array|no|

#### TriggerFeedResponse

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|data|array|no|

#### PostItem: una singola voce nel feed di un profilo
Il profilo può essere un utente, una pagina, un'app o un gruppo.

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|id|string|no|
|admin\_creator|array|no|
|caption|string|no|
|created\_time|string|no|
|description|string|no|
|feed\_targeting|non definito|no|
|from|non definito|no|
|icon|string|no|
|is\_hidden|boolean|no|
|is\_published|boolean|no|
|link|string|no|
|message|string|no|
|name|string|no|
|object\_id|string|no|
|picture|string|no|
|place|non definito|no|
|privacy|non definito|no|
|properties|array|no|
|source|string|no|
|status\_type|string|no|
|story|string|no|
|targeting|non definito|no|
|to|array|no|
|type|string|no|
|updated\_time|string|no|
|with\_tags|non definito|no|

#### TriggerItem: una singola voce nel feed di un profilo
Il profilo può essere un utente, una pagina, un'app o un gruppo.

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|id|string|no|
|created\_time|string|no|
|from|non definito|no|
|message|string|no|
|type|string|no|

#### AdminItem

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|id|string|no|
|link|string|no|

#### PropertyItem

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|name|string|no|
|text|string|no|
|href|string|no|

#### UserPostFeedRequest

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|message|string|sì|
|link|string|no|
|picture|string|no|
|name|string|no|
|caption|string|no|
|description|string|no|
|place|string|no|
|tags|string|no|
|privacy|non definito|no|
|object\_attachment|string|no|

#### PagePostFeedRequest

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|message|string|sì|
|link|string|no|
|picture|string|no|
|name|string|no|
|caption|string|no|
|description|string|no|
|actions|array|no|
|place|string|no|
|tags|string|no|
|object\_attachment|string|no|
|targeting|non definito|no|
|feed\_targeting|non definito|no|
|published|boolean|no|
|scheduled\_publish\_time|string|no|
|backdated\_time|string|no|
|backdated\_time\_granularity|string|no|
|child\_attachments|array|no|
|multi\_share\_end\_card|boolean|no|

#### PostFeedResponse

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|id|string|no|

#### ProfileCollection

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|data|array|no|

#### UserItem

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|id|string|no|
|first\_name|string|no|
|last\_name|string|no|
|name|string|no|
|gender|string|no|
|about|string|no|

#### ActionItem

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|name|string|no|
|link|string|no|

#### TargetItem

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|countries|array|no|
|locales|array|no|
|regions|array|no|
|cities|array|no|

#### FeedTargetItem: oggetto che controlla i newsfeed di destinazione per questo post
I membri di questi gruppi hanno più probabilità di vedere questo post, gli altri utenti hanno meno probabilità. Si applica solo alle pagine.

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|countries|array|no|
|regions|array|no|
|cities|array|no|
|age\_min|integer|no|
|age\_max|integer|no|
|genders|array|no|
|relationship\_statuses|array|no|
|interested\_in|array|no|
|college\_years|array|no|
|interests|array|no|
|relevant\_until|integer|no|
|education\_statuses|array|no|
|locales|array|no|

#### PlaceItem

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|id|string|no|
|name|string|no|
|overall\_rating|number|no|
|location|non definito|no|

#### LocationItem

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|city|string|no|
|country|string|no|
|latitudine|number|no|
|located\_in|string|no|
|longitudine|number|no|
|name|string|no|
|region|string|no|
|state|string|no|
|street|string|no|
|zip|string|no|

#### PrivacyItem

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|description|string|no|
|value|string|sì|
|allow|string|no|
|deny|string|no|
|friends|string|no|

#### ChildAttachmentsItem

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|link|string|no|
|picture|string|no|
|image\_hash|string|no|
|name|string|no|
|description|string|no|

#### PostPhotoRequest

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|URL|string|sì|
|caption|string|no|

#### PostPhotoResponse

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|id|string|sì|
|post\_id|string|sì|

#### PostVideoRequest

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|videoData|string|sì|
|description|string|sì|
|title|string|sì|
|uploadedVideoName|string|no|

#### GetPhotoResponse

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|data|non definito|sì|

#### GetPhotoResponseItem

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|URL|string|sì|
|is\_silhouette|boolean|sì|
|height|string|no|
|width|string|no|

#### GetEventResponse

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|data|array|sì|

#### GetEventResponseItem

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|id|string|sì|
|name|string|sì|
|start\_time|string|no|
|end\_time|string|no|
|timezone|string|no|
|location|string|no|
|description|string|no|
|ticket\_uri|string|no|
|rsvp\_status|string|yes|


## Passaggi successivi

[Creare una nuova app per la logica che connette servizi SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0518_2016-->