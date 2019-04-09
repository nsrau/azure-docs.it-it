---
title: "Guida introduttiva: Creare, caricare ed eseguire query su un indice usando PowerShell e l'API REST - ricerca di Azure"
description: Creare, caricare ed eseguire query su un indice usando PowerShell Invoke-RestMethod e l'API REST di ricerca di Azure.
ms.date: 04/08/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 2deba4bf941d561fcef7c2dff804646732e7ce24
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59268025"
---
# <a name="quickstart-create-an-azure-search-index-using-powershell-and-the-rest-api"></a>Guida introduttiva: Creare un indice di ricerca di Azure con PowerShell e l'API REST
> [!div class="op_single_selector"]
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-fiddler.md)
> * [Portale](search-create-index-portal.md)
> 

Questo articolo illustra il processo di creazione, caricamento e l'esecuzione di query su una ricerca di Azure [indice](search-what-is-an-index.md) tramite PowerShell e il [API REST del servizio ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice/). La definizione dell'indice e il contenuto ricercabile viene fornito nel corpo della richiesta come contenuto JSON ben formato.

## <a name="prerequisites"></a>Prerequisiti

I servizi e gli strumenti seguenti vengono usati in questa Guida introduttiva. 

[Creare un servizio Ricerca di Azure](search-create-service-portal.md) o [trovare un servizio esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nella sottoscrizione corrente. È possibile usare un servizio gratuito per questo avvio rapido. 

[PowerShell 5.1 o versione successiva](https://github.com/PowerShell/PowerShell), usando [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) per i passaggi sequenziali e interattivi.

## <a name="get-a-key-and-url"></a>Ottenere una chiave e URL

Le chiamate REST richiedono l'URL del servizio e una chiave di accesso per ogni richiesta. Con entrambi gli elementi viene creato un servizio di ricerca, quindi se si è aggiunto Ricerca di Azure alla sottoscrizione, seguire questi passaggi per ottenere le informazioni necessarie:

1. [Accedere al portale di Azure](https://portal.azure.com/)e nel servizio di ricerca **Cenni preliminari su** pagina, ottenere l'URL. Un endpoint di esempio potrebbe essere simile a `https://mydemo.search.windows.net`.

2. In **Impostazioni** > **Chiavi** ottenere una chiave amministratore per diritti completi sul servizio. Sono disponibili due chiavi amministratore interscambiabili, fornite per continuità aziendale nel caso in cui sia necessario eseguire il rollover di una di esse. È possibile usare la chiave primaria o secondaria nelle richieste per l'aggiunta, la modifica e l'eliminazione di oggetti.

![Ottenere una chiave di accesso e un endpoint HTTP](media/search-fiddler/get-url-key.png "Ottenere una chiave di accesso e un endpoint HTTP")

Per ogni richiesta inviata al servizio è necessario specificare una chiave API. La presenza di una chiave valida stabilisce una relazione di trust, in base alle singole richieste, tra l'applicazione che invia la richiesta e il servizio che la gestisce.

## <a name="connect-to-azure-search"></a>Connettersi a ricerca di Azure

In PowerShell, creare un **$headers** oggetto usato per archiviare il tipo di contenuto e la chiave API. È sufficiente impostare questa intestazione di una volta per la durata della sessione, ma si aggiungerà a ogni richiesta. 

```powershell
$headers = @{
   'api-key' = '<your-admin-api-key>'
   'Content-Type' = 'application/json' 
   'Accept' = 'application/json' }
```

Creare un **$url** che specifica il servizio raccolta di indici. Il `mydemo` nome del servizio è da intendersi come segnaposto. Sostituirlo con un servizio di ricerca valida in una sottoscrizione corrente in tutto l'esempio.

```powershell
$url = "https://mydemo.search.windows.net/indexes?api-version=2017-11-11"
```

Eseguire **Invoke-RestMethod** per inviare una richiesta GET al servizio e verificare la connessione. Aggiungere **ConvertTo-Json** in modo che sia possibile visualizzare le risposte inviate dal servizio.

```powershell
Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
```

Se il servizio è vuoto e non include indici, i risultati sono simili all'esempio seguente. In caso contrario, si noterà una rappresentazione JSON delle definizioni di indice.

```
{
    "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
    "value":  [

              ]
}
```

## <a name="1---create-an-index"></a>1 - Creare un indice

A meno che non si usa il portale, deve esistere un indice nel servizio prima di caricare i dati. Questo passaggio definisce l'indice e ne esegue il push al servizio. Il [Create Index (API REST)](https://docs.microsoft.com/rest/api/searchservice/create-index) viene usato per questo passaggio.

Gli elementi necessari di un indice includono un nome e una raccolta di campi. Raccolta fields definisce la struttura di un *documento*. Ogni campo ha un nome, tipo e gli attributi che determinano la modalità di utilizzo (ad esempio, se è full-text ricercabile, filtrabile oppure recuperabile nei risultati della ricerca). All'interno di un indice, uno dei campi di tipo `Edm.String` deve essere designato come il *chiave* per identità del documento.

Questo indice è denominato "hotels" e contiene le definizioni di campo visualizzato di seguito. La definizione di indice specifica un [analizzatore della lingua](index-add-language-analyzers.md) per il `description_fr` campo perché viene utilizzato per archiviare testo in francese, che verrà aggiunto in un esempio più avanti.

Incollare questo esempio di PowerShell per creare un **$body** oggetto contenente lo schema dell'indice.

```powershell
$body = @"
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
"@
```

Impostare l'URI per l'insieme degli indici nel servizio e il *hotel* indice.

```powershell
$url = "https://mydemo.search.windows.net/indexes/hotels?api-version=2017-11-11"
```

Eseguire il comando con **$url**, **$headers**, e **$body** per creare l'indice nel servizio. 

```powershell
Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
```
Risultati dovrebbero essere simili al seguente (troncato per i primi due campi per brevità):

```
{
    "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag":  "\"0x8D6A99E2DED96B0\"",
    "name":  "hotels",
    "defaultScoringProfile":  null,
    "fields":  [
                   {
                       "name":  "hotelId",
                       "type":  "Edm.String",
                       "searchable":  false,
                       "filterable":  true,
                       "retrievable":  true,
                       "sortable":  false,
                       "facetable":  false,
                       "key":  true,
                       "indexAnalyzer":  null,
                       "searchAnalyzer":  null,
                       "analyzer":  null,
                       "synonymMaps":  ""
                   },
                   {
                       "name":  "baseRate",
                       "type":  "Edm.Double",
                       "searchable":  false,
                       "filterable":  true,
                       "retrievable":  true,
                       "sortable":  true,
                       "facetable":  true,
                       "key":  false,
                       "indexAnalyzer":  null,
                       "searchAnalyzer":  null,
                       "analyzer":  null,
                       "synonymMaps":  ""
                   },
. . .
```

> [!Tip]
> Per la verifica, si potrebbe anche controllare l'elenco di indici nel portale di, o eseguire di nuovo il comando utilizzato per verificare la connessione del servizio per visualizzare il *hotel* indice elencato nella raccolta di indici.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Caricare i documenti

Per eseguire il push documenti, usare una richiesta HTTP POST all'endpoint dell'URL dell'indice. L'API REST per questa attività viene [aggiungere, aggiornare o eliminare documenti](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

Incollare questo esempio di PowerShell per creare un **$body** oggetto contenente i documenti da caricare. 

Questa richiesta include due completo e un unico record parziale. Il record parziale viene illustrato che è possibile caricare i documenti non completi. Il `@search.action` parametro specifica come l'indicizzazione viene eseguita. I valori validi includono upload, merge, mergeOrUpload e delete. Il comportamento mergeOrUpload crea un nuovo documento hotelId = 3 o aggiorna il contenuto, se esiste già.

```powershell
$body = @"
{
    "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
            "hotelName": "Fancy Stay",
            "category": "Luxury",
            "tags": ["pool", "view", "wifi", "concierge"],
            "parkingIncluded": false,
            "smokingAllowed": false,
            "lastRenovationDate": "2010-06-27T00:00:00Z",
            "rating": 5,
            "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
            "@search.action": "upload",
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags": ["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        }
    ]
}
"@
```

Impostare l'endpoint il *hotel* raccolta di documentazione e includono l'operazione di index (indici/hotel/docs/index).

```powershell
$url = "https://mydemo.search.windows.net/indexes/hotels/docs/index?api-version=2017-11-11"
```

Eseguire il comando con **$url**, **$headers**, e **$body** per caricare i documenti nell'indice degli hotel.

```powershell
Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
```
Risultati dovrebbero essere simili all'esempio seguente. Verrà visualizzato un codice di stato di 201. Per una descrizione di tutti i codici di stato, vedere [codici di stato HTTP (ricerca di Azure)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

```
{
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels\u0027)/$metadata#Collection(Microsoft.Azure.Search.V2017_11_11.IndexResult)",
    "value":  [
                  {
                      "key":  "1",
                      "status":  true,
                      "errorMessage":  null,
                      "statusCode":  201
                  },
                  {
                      "key":  "2",
                      "status":  true,
                      "errorMessage":  null,
                      "statusCode":  201
                  },
                  {
                      "key":  "3",
                      "status":  true,
                      "errorMessage":  null,
                      "statusCode":  201
                  }
              ]
}
```

## <a name="3---search-an-index"></a>3 - Eseguire la ricerca in un indice

Questo passaggio illustra come eseguire query su un indice con il [API di ricerca di documenti](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Impostare l'endpoint il *hotels* raccolta di documentazione e aggiungere un **ricerca** parametro per includere le stringhe di query. Stringa che rappresenta una ricerca vuota e restituisce un elenco di tutti i documenti unranked.

```powershell
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=*'
```

Eseguire il comando per inviare le **$url** al servizio.

```powershell
Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
```

Risultati dovrebbero essere simili all'output seguente.

```
{
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels\u0027)/$metadata#docs(*)",
    "value":  [
                  {
                      "@search.score":  1.0,
                      "hotelId":  "1",
                      "baseRate":  199.0,
                      "description":  "Best hotel in town",
                      "description_fr":  null,
                      "hotelName":  "Fancy Stay",
                      "category":  "Luxury",
                      "tags":  "pool view wifi concierge",
                      "parkingIncluded":  false,
                      "smokingAllowed":  false,
                      "lastRenovationDate":  "2010-06-27T00:00:00Z",
                      "rating":  5,
                      "location":  "@{type=Point; coordinates=System.Object[]; crs=}"
                  },
                  {
                      "@search.score":  1.0,
                      "hotelId":  "2",
                      "baseRate":  79.99,
                      "description":  "Cheapest hotel in town",
                      "description_fr":  null,
                      "hotelName":  "Roach Motel",
                      "category":  "Budget",
                      "tags":  "motel budget",
                      "parkingIncluded":  true,
                      "smokingAllowed":  true,
                      "lastRenovationDate":  "1982-04-28T00:00:00Z",
                      "rating":  1,
                      "location":  "@{type=Point; coordinates=System.Object[]; crs=}"
                  },
                  {
                      "@search.score":  1.0,
                      "hotelId":  "3",
                      "baseRate":  129.99,
                      "description":  "Close to town hall and the river",
                      "description_fr":  null,
                      "hotelName":  null,
                      "category":  null,
                      "tags":  "",
                      "parkingIncluded":  null,
                      "smokingAllowed":  null,
                      "lastRenovationDate":  null,
                      "rating":  null,
                      "location":  null
                  }
              ]
}
```

Provare ad alcuni altri esempi di query per acquisire familiarità con la sintassi. È possibile eseguire una ricerca di stringhe, le query $filter verbatim, limitare il set di risultati, l'ambito di ricerca a campi specifici e così via.

```powershell
# Query example 1
# Search the entire index for the term 'budget'
# Return only the `hotelName` field, "Roach hotel"
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=budget&$select=hotelName'

# Query example 2 
# Apply a filter to the index to find hotels cheaper than $150 per night
# Returns the `hotelId` and `description`. Two documents match.
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=*&$filter=baseRate lt 150&$select=hotelId,description'

# Query example 3
# Search the entire index, order by a specific field (`lastRenovationDate`) in descending order
# Take the top two results, and show only `hotelName` and `lastRenovationDate`
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate'
```
## <a name="clean-up"></a>Eseguire la pulizia 

Se è non è più necessario, è necessario eliminare l'indice. Un servizio gratuito è limitato a tre indici. È possibile eliminare gli indici che non si usa attivamente, in modo che è possibile eseguire le altre esercitazioni.

```powershell
# Set the URI to the hotel index
$url = 'https://mydemo.search.windows.net/indexes/hotels?api-version=2017-11-11'

# Delete the index
Invoke-RestMethod -Uri $url -Headers $headers -Method Delete
```

## <a name="next-steps"></a>Passaggi successivi

Provare ad aggiungere descrizioni francese all'indice. Nell'esempio seguente include le stringhe in francese e vengono illustrate le azioni di ricerca aggiuntive. Usare mergeOrUpload per creare o aggiungere ai campi esistenti. Le stringhe seguenti devono essere con codifica UTF-8.

```json
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "1",
            "description_fr": "Meilleur hôtel en ville"
        },
        {
            "@search.action": "merge",
            "hotelId": "2",
            "description_fr": "Hôtel le moins cher en ville"
        }
    ]
}
```
