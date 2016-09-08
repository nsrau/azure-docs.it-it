<properties
    pageTitle="Aggiungere il connettore Facebook alle app per la logica | Microsoft Azure"
    description="Panoramica del connettore Facebook con i parametri dell'API REST."
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
   ms.date="08/18/2016"
   ms.author="mandia"/>

# Introduzione al connettore Facebook
Connettersi a Facebook e pubblicare un post in una sequenza temporale, recuperare il feed di una pagina e così via.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.


Con Facebook è possibile:

- Creare il flusso aziendale in base ai dati ottenuti da Facebook.
- Usare un trigger quando si riceve un nuovo post.
- Usare azioni per pubblicare un post nel diario, ottenere il feed di una pagina e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando nel diario è presente un nuovo post, è possibile selezionare tale post ed eseguirne il push al feed di Twitter.



Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni
Il connettore Facebook include i trigger e le azioni seguenti.

| Trigger | Azioni|
| --- | --- |
| <ul><li>When there is a new post on my timeline (Quando nel diario è presente un nuovo post)</li></ul> |<ul><li>Get feed from my timeline (Ottieni feed dal diario)</li><li>Post to my timeline (Pubblica nel diario)</li><li>When there is a new post on my timeline (Quando nel diario è presente un nuovo post)</li><li>Get page feed (Ottieni feed della pagina)</li><li>Ottieni cronologia utente</li><li>Post to page (Pubblica nella pagina)</li></ul>

Tutti i connettori supportano dati nei formati JSON e XML.

## Creare una connessione a Facebook
Quando si aggiunge questo connettore alle app per la logica, è necessario autorizzare le app per la logica per la connessione a Facebook.

1. Accedere al proprio account Facebook
2. Scegliere **Autorizza** e consentire alle app per la logica di connettersi e usare Facebook.

>[AZURE.INCLUDE [Passaggi per creare una connessione a Facebook](../../includes/connectors-create-api-facebook.md)]

>[AZURE.TIP] È possibile usare la stessa connessione di Facebook in altre app per la logica.

## Riferimento all'API REST di Swagger
Si applica alla versione 1.0.

### Ottieni feed dal diario
Ottiene i feed dal diario dell'utente connesso. ```GET: /me/feed```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|fields|stringa|no|query|nessuno |Specifica i campi da restituire, ad esempio ID, nome o immagine.|
|limit|integer|no|query| nessuno|Numero massimo di post da recuperare|
|con|stringa|no|query| nessuno|Limita l'elenco dei post solo a quelli con percorso collegato.|
|filter|stringa|no|query| nessuno|Recupera solo i post che corrispondono a un filtro di flusso specifico.|

#### Response
|Name|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|500|Internal Server Error|
|default|Operazione non riuscita.|


### Pubblica post nel diario
Pubblica un messaggio di stato nel diario dell'utente connesso. ```POST: /me/feed```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|post|string |sì|body|nessuno |Nuovo messaggio da pubblicare|

#### Response
|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|500|Internal Server Error|
|default|Operazione non riuscita.|


### Quando nel diario è presente un nuovo post
Attiva un nuovo flusso in presenza di un nuovo post nel diario dell'utente connesso ```GET: /trigger/me/feed```

Non sono disponibili parametri.

#### Response
|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|500|Internal Server Error|
|default|Operazione non riuscita.|


### Ottieni feed della pagina
Ottiene i post dal feed di una pagina specificata. ```GET: /{pageId}/feed```

| Name|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|pageId|string|yes|path| nessuno|ID della pagina da cui devono essere recuperati i post.|
|limit|integer|no|query| nessuno|Numero massimo di post da recuperare|
|include\_hidden|boolean|no|query|nessuno |Include o esclude i post che sono stati nascosti dalla pagina|
|fields|stringa|no|query|nessuno |Specifica i campi da restituire, ad esempio ID, nome o immagine.|

#### Response
|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|500|Internal Server Error|
|default|Operazione non riuscita.|


### Ottieni diario utente
Ottiene i post dal diario di un utente. ```GET: /{userId}/feed```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|userId|stringa|yes|path|nessuno |ID dell'utente la cui cronologia deve essere recuperata.|
|limit|integer|no|query|nessuno |Numero massimo di post da recuperare|
|con|stringa|no|query|nessuno |Limita l'elenco dei post solo a quelli con percorso collegato.|
|filter|stringa|no|query| nessuno|Recupera solo i post che corrispondono a un filtro di flusso specifico.|
|fields|stringa|no|query| nessuno|Specifica i campi da restituire, ad esempio ID, nome o immagine.|

#### Response
|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|500|Internal Server Error|
|default|Operazione non riuscita.|


### Pubblica post nella pagina
Pubblica un messaggio in una pagina Facebook come utente connesso. ```POST: /{pageId}/feed```

| Name|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|pageId|stringa|yes|path|nessuno |ID della pagina da pubblicare.|
|post|many |sì|body|nessuno |Nuovo messaggio da pubblicare.|

#### Response
|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|500|Internal Server Error|
|default|Operazione non riuscita.|


## Definizioni oggetti

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
|description|stringa|no|
|feed\_targeting|non definito|no|
|from|non definito|no|
|icon|stringa|no|
|is\_hidden|boolean|no|
|is\_published|boolean|no|
|link|string|no|
|message|stringa|no|
|name|string|no|
|object\_id|stringa|no|
|picture|string|no|
|place|non definito|no|
|privacy|non definito|no|
|properties|array|no|
|source|stringa|no|
|status\_type|string|no|
|story|stringa|no|
|targeting|non definito|no|
|to|array|no|
|type|stringa|no|
|updated\_time|string|no|
|with\_tags|non definito|no|

#### TriggerItem: una singola voce nel feed di un profilo
Il profilo può essere un utente, una pagina, un'app o un gruppo.

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|id|stringa|no|
|created\_time|stringa|no|
|from|non definito|no|
|message|string|no|
|type|stringa|no|

#### AdminItem

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|id|stringa|no|
|link|stringa|no|

#### PropertyItem

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|name|string|no|
|text|string|no|
|href|stringa|no|

#### UserPostFeedRequest

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|message|string|sì|
|link|stringa|no|
|picture|stringa|no|
|name|string|no|
|caption|stringa|no|
|description|string|no|
|place|string|no|
|tags|stringa|no|
|privacy|non definito|no|
|object\_attachment|stringa|no|

#### PagePostFeedRequest

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|message|stringa|sì|
|link|stringa|no|
|picture|stringa|no|
|name|string|no|
|caption|stringa|no|
|description|stringa|no|
|actions|array|no|
|place|string|no|
|tags|stringa|no|
|object\_attachment|stringa|no|
|targeting|non definito|no|
|feed\_targeting|non definito|no|
|published|boolean|no|
|scheduled\_publish\_time|stringa|no|
|backdated\_time|string|no|
|backdated\_time\_granularity|string|no|
|child\_attachments|array|no|
|multi\_share\_end\_card|boolean|no|

#### PostFeedResponse

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|id|stringa|no|

#### ProfileCollection

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|data|array|no|

#### UserItem

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|id|stringa|no|
|first\_name|stringa|no|
|last\_name|stringa|no|
|name|string|no|
|gender|stringa|no|
|about|stringa|no|

#### ActionItem

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|name|stringa|no|
|link|stringa|no|

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
|id|stringa|no|
|name|stringa|no|
|overall\_rating|number|no|
|location|non definito|no|

#### LocationItem

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|city|stringa|no|
|country|string|no|
|latitudine|number|no|
|located\_in|string|no|
|longitudine|number|no|
|name|stringa|no|
|region|stringa|no|
|state|stringa|no|
|street|stringa|no|
|zip|stringa|no|

#### PrivacyItem

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|description|stringa|no|
|value|stringa|sì|
|allow|stringa|no|
|deny|stringa|no|
|friends|stringa|no|

#### ChildAttachmentsItem

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|link|stringa|no|
|picture|stringa|no|
|image\_hash|stringa|no|
|name|stringa|no|
|description|string|no|

#### PostPhotoRequest

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|URL|stringa|sì|
|caption|stringa|no|

#### PostPhotoResponse

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|id|string|sì|
|post\_id|stringa|sì|

#### PostVideoRequest

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|videoData|stringa|sì|
|description|stringa|sì|
|title|stringa|sì|
|uploadedVideoName|stringa|no|

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
|width|stringa|no|

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
|location|stringa|no|
|description|stringa|no|
|ticket\_uri|string|no|
|rsvp\_status|stringa|sì|


## Passaggi successivi

[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0824_2016-->