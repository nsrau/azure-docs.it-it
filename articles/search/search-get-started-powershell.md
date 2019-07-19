---
title: 'Guida introduttiva di PowerShell: Creare, caricare ed eseguire query sugli indici usando le API REST di ricerca di Azure-ricerca di Azure'
description: Viene illustrato come creare un indice, caricare i dati ed eseguire query usando Invoke-RestMethod di PowerShell e l'API REST di ricerca di Azure.
ms.date: 07/11/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 6bff2c84a4bfd81b94054b85744c17a1cd217756
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67847056"
---
# <a name="quickstart-create-an-azure-search-index-in-powershell-using-rest-apis"></a>Avvio rapido: Creare un indice di ricerca di Azure in PowerShell usando le API REST
> [!div class="op_single_selector"]
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postazione (REST)](search-get-started-postman.md)
> * [Python](search-get-started-python.md)
> * [Portale](search-create-index-portal.md)
> 

Questo articolo illustra il processo di creazione, caricamento ed esecuzione di query su un indice di ricerca di Azure tramite PowerShell e le [API REST di ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice/). Questo articolo illustra come eseguire i comandi di PowerShell in modo interattivo. In alternativa, è possibile [scaricare ed eseguire uno script di PowerShell](https://github.com/Azure-Samples/azure-search-powershell-samples/tree/master/Quickstart) che esegue le stesse operazioni.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per questa Guida introduttiva sono necessari i servizi e gli strumenti seguenti. 

+ [PowerShell 5,1 o versioni successive](https://github.com/PowerShell/PowerShell), uso di [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) per la procedura sequenziale e interattiva.

+ [Creare un servizio Ricerca di Azure](search-create-service-portal.md) o [trovare un servizio esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nella sottoscrizione corrente. È possibile usare un servizio gratuito per questo avvio rapido. 

## <a name="get-a-key-and-url"></a>Ottenere una chiave e un URL

Le chiamate REST richiedono l'URL del servizio e una chiave di accesso per ogni richiesta. Con entrambi gli elementi viene creato un servizio di ricerca, quindi se si è aggiunto Ricerca di Azure alla sottoscrizione, seguire questi passaggi per ottenere le informazioni necessarie:

1. [Accedere al portale di Azure](https://portal.azure.com/) e ottenere l'URL nella pagina **Panoramica** del servizio di ricerca. Un endpoint di esempio potrebbe essere simile a `https://mydemo.search.windows.net`.

2. In **Impostazioni** > **Chiavi** ottenere una chiave amministratore per diritti completi sul servizio. Sono disponibili due chiavi amministratore interscambiabili, fornite per continuità aziendale nel caso in cui sia necessario eseguire il rollover di una di esse. È possibile usare la chiave primaria o secondaria nelle richieste per l'aggiunta, la modifica e l'eliminazione di oggetti.

![Ottenere una chiave di accesso e un endpoint HTTP](media/search-get-started-postman/get-url-key.png "Ottenere una chiave di accesso e un endpoint HTTP")

Per ogni richiesta inviata al servizio è necessario specificare una chiave API. La presenza di una chiave valida stabilisce una relazione di trust, in base alle singole richieste, tra l'applicazione che invia la richiesta e il servizio che la gestisce.

## <a name="connect-to-azure-search"></a>Connettersi a Ricerca di Azure

1. In PowerShell creare un oggetto **$headers** per archiviare il tipo di contenuto e la chiave API. Sostituire la chiave API di amministrazione (YOUR-ADMIN-API-KEY) con una chiave valida per il servizio di ricerca. È necessario impostare questa intestazione una sola volta per la durata della sessione, ma verrà aggiunta a ogni richiesta. 

    ```powershell
    $headers = @{
    'api-key' = '<YOUR-ADMIN-API-KEY>'
    'Content-Type' = 'application/json' 
    'Accept' = 'application/json' }
    ```

2. Creare un **$URL** oggetto che specifica la raccolta di indici del servizio. Sostituire il nome del servizio (il nome-ricerca-servizio) con un servizio di ricerca valido.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06&$select=name"
    ```

3. Eseguire **Invoke-RestMethod** per inviare una richiesta GET al servizio e verificare la connessione. Aggiungere **ConvertTo-JSON** in modo che sia possibile visualizzare le risposte restituite dal servizio.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

   Se il servizio è vuoto e non dispone di indici, i risultati sono simili all'esempio seguente. In caso contrario, verrà visualizzata una rappresentazione JSON delle definizioni degli indici.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
        "value":  [

                ]
    }
    ```

## <a name="1---create-an-index"></a>1 - Creare un indice

A meno che non si stia usando il portale, è necessario che nel servizio esista un indice prima di poter caricare i dati. Questo passaggio definisce l'indice e lo inserisce nel servizio. Per questo passaggio viene usata l' [API REST create index](https://docs.microsoft.com/rest/api/searchservice/create-index) .

Gli elementi necessari di un indice includono un nome e una raccolta di campi. La raccolta Fields definisce la struttura di un *documento*. Ogni campo ha un nome, un tipo e attributi che ne determinano la modalità di utilizzo, ad esempio se si tratta di una ricerca full-text, filtrabile o recuperabile nei risultati della ricerca. All'interno di un indice, uno dei campi di `Edm.String` tipo deve essere designato come *chiave* per l'identità del documento.

Questo indice è denominato "Hotels-Quickstart" e include le definizioni di campo visualizzate di seguito. Si tratta di un subset di un [Indice degli Alberghi](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) più grande usato in altre procedure dettagliate. In questa Guida introduttiva è stato tagliato per brevità.

1. Incollare questo esempio in PowerShell per creare un **$Body** oggetto contenente lo schema dell'indice.

    ```powershell
    $body = @"
    {
        "name": "hotels-quickstart",  
        "fields": [
            {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
            {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
            {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
            {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
            {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
            {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Address", "type": "Edm.ComplexType", 
            "fields": [
            {"name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true},
            {"name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true}
            ]
         }
      ]
    }
    "@
    ```

2. Impostare l'URI sulla raccolta Indexes nel servizio e l'indice *Hotels-QuickStart* .

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06"
    ```

3. Eseguire il comando con **$URL**, **$headers**e **$Body** per creare l'indice nel servizio. 

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
    ```

    I risultati dovrebbero essere simili a questo (troncato ai primi due campi per brevità):

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
        "@odata.etag":  "\"0x8D6EDE28CFEABDA\"",
        "name":  "hotels-quickstart",
        "defaultScoringProfile":  null,
        "fields":  [
                    {
                        "name":  "HotelId",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  true,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  true,
                        "key":  true,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
                    {
                        "name":  "HotelName",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  false,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  false,
                        "key":  false,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
    . . .
    ```

> [!Tip]
> Per la verifica, è anche possibile controllare l'elenco degli indici nel portale.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Caricare i documenti

Per eseguire il push dei documenti, usare una richiesta HTTP POST all'endpoint dell'URL dell'indice. L'API REST per questa attività è [aggiunta, aggiornamento o eliminazione di documenti](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

1. Incollare questo esempio in PowerShell per creare un **$Body** oggetto contenente i documenti che si vuole caricare. 

    Questa richiesta include due record completi e uno parziale. Il record parziale dimostra che è possibile caricare documenti incompleti. Il `@search.action` parametro specifica il modo in cui viene eseguita l'indicizzazione. I valori validi includono upload, merge, mergeOrUpload ed Delete. Il comportamento di mergeOrUpload consente di creare un nuovo documento per hoteld = 3 o di aggiornare il contenuto, se già esistente.

    ```powershell
    $body = @"
    {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": 
            {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": 
            {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    "@
    ```

1. Impostare l'endpoint sulla raccolta di docs *Hotels-QuickStart* e includere l'operazione sull'indice (indici/Alberghi-QuickStart/docs/index).

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06"
    ```

1. Eseguire il comando con **$URL**, **$headers**e **$Body** per caricare i documenti nell'indice delle guide introduttive di Hotels.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
    ```
    I risultati dovrebbero essere simili all'esempio seguente. Verrà visualizzato un [codice di stato 201](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#Collection(Microsoft.Azure.Search.V2019_05_06.IndexResult)",
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
                    },
                    {
                        "key":  "4",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    }
                ]
    }
    ```

## <a name="3---search-an-index"></a>3 - Eseguire la ricerca in un indice

Questo passaggio illustra come eseguire una query su un indice usando l' [API cerca documenti](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Assicurarsi di usare le virgolette singole nei $urls di ricerca. Le stringhe di **$** query includono caratteri ed è possibile ometterle se l'intera stringa è racchiusa tra virgolette singole.

1. Impostare l'endpoint per la raccolta di docs degli *Hotel-QuickStart* e aggiungere un parametro di **ricerca** da passare in una stringa di query. 
  
   Questa stringa esegue una ricerca vuota (Search = *), restituendo un elenco non classificato (score di ricerca = 1,0) di documenti arbitrari. Per impostazione predefinita, ricerca di Azure restituisce 50 corrispondenze alla volta. Come strutturato, questa query restituisce l'intera struttura del documento e i valori. Aggiungere **$count = true** per ottenere un conteggio di tutti i documenti nei risultati.

    ```powershell
    $url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=*&$count=true'
    ```

1. Eseguire il comando per inviare il **$URL** al servizio.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

    I risultati dovrebbero essere simili all'output seguente.

    ```
    {
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#docs(*)",
    "@odata.count":  4,
    "value":  [
                  {
                      "@search.score":  0.1547872,
                      "HotelId":  "2",
                      "HotelName":  "Twin Dome Motel",
                      "Description":  "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                      "Category":  "Boutique",
                      "Tags":  "pool free wifi concierge",
                      "ParkingIncluded":  false,
                      "LastRenovationDate":  "1979-02-18T00:00:00Z",
                      "Rating":  3.6,
                      "Address":  "@{StreetAddress=140 University Town Center Dr; City=Sarasota; StateProvince=FL; PostalCode=34243; Country=USA}"
                  },
                  {
                      "@search.score":  0.009068266,
                      "HotelId":  "3",
                      "HotelName":  "Triple Landscape Hotel",
                      "Description":  "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel\u0027s restaurant services.",
                      "Category":  "Resort and Spa",
                      "Tags":  "air conditioning bar continental breakfast",
                      "ParkingIncluded":  true,
                      "LastRenovationDate":  "2015-09-20T00:00:00Z",
                      "Rating":  4.8,
                      "Address":  "@{StreetAddress=3393 Peachtree Rd; City=Atlanta; StateProvince=GA; PostalCode=30326; Country=USA}"
                  },
                . . . 
    ```

Provare altri esempi di query per ottenere un'idea della sintassi. È possibile eseguire una ricerca di stringhe, Verbatim $filter query, limitare il set di risultati, definire l'ambito della ricerca per campi specifici e altro ancora.

```powershell
# Query example 1
# Search the entire index for the terms 'restaurant' and 'wifi'
# Return only the HotelName, Description, and Tags fields
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=restaurant wifi&$count=true&$select=HotelName,Description,Tags'

# Query example 2 
# Apply a filter to the index to find hotels rated 4 or highter
# Returns the HotelName and Rating. Two documents match.
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=*&$filter=Rating gt 4&$select=HotelName,Rating'

# Query example 3
# Take the top two results, and show only HotelName and Category in the results
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=boutique&$top=2&$select=HotelName,Category'

# Query example 4
# Sort by a specific field (Address/City) in ascending order

$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating'
```
## <a name="clean-up"></a>Eseguire la pulizia 

Quando si lavora nella propria sottoscrizione, alla fine di un progetto è opportuno verificare se le risorse create sono ancora necessarie. L'esecuzione continua delle risorse può avere un costo. È possibile eliminare le singole risorse oppure il gruppo di risorse per eliminare l'intero set di risorse.

Per trovare e gestire le risorse nel portale, usare il collegamento **Tutte le risorse** o **Gruppi di risorse** nel riquadro di spostamento a sinistra.

Se si usa un servizio gratuito, tenere presente che il numero di indicizzatori e origini dati è limitato a tre. Per non superare il limite, è possibile eliminare i singoli elementi nel portale. 

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stato usato PowerShell per esaminare il flusso di lavoro di base per la creazione e l'accesso al contenuto in ricerca di Azure. Tenendo presenti i concetti, è consigliabile procedere a scenari più avanzati, ad esempio l'indicizzazione da origini dati di Azure.

> [!div class="nextstepaction"]
> [Esercitazione REST: Indicizzare e cercare dati semi-strutturati (BLOB JSON) in ricerca di Azure](search-semi-structured-data.md)