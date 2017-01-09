---
title: Eseguire query su un indice di Ricerca di Azure con l&quot;API REST | Documentazione Microsoft
description: Compilare una query di ricerca in Ricerca di Azure e usare i parametri di ricerca per filtrare e ordinare i risultati della ricerca.
services: search
documentationcenter: 
manager: jhubbard
author: ashmaka
ms.assetid: 8b3ca890-2f5f-44b6-a140-6cb676fc2c9c
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 12/08/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 455c4847893175c1091ae21fa22215fd1dd10c53
ms.openlocfilehash: 96e8177f57977f88c5a4a1ec0b9243b5b348f078

---

# <a name="query-your-azure-search-index-using-the-rest-api"></a>Eseguire query su un indice di Ricerca di Azure con l'API REST
> [!div class="op_single_selector"]
>
> * [Panoramica](search-query-overview.md)
> * [Portale](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
>
>

Questo articolo illustra come eseguire query su un indice con l' [API REST di Ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice/).

Prima di iniziare questa procedura dettagliata, è necessario avere [creato un indice di Ricerca di Azure](search-what-is-an-index.md) e [averlo popolato con dati](search-what-is-data-import.md).

## <a name="i-identify-your-azure-search-services-query-api-key"></a>I. Identificare la chiave API di query del servizio Ricerca di Azure
Un componente chiave di ogni operazione di ricerca con l'API REST di Ricerca di Azure è la *chiave API* generata per il servizio di cui è stato effettuato il provisioning. La presenza di una chiave valida stabilisce una relazione di trust, in base alle singole richieste, tra l'applicazione che invia la richiesta e il servizio che la gestisce.

1. Per trovare le chiavi API del servizio, è necessario accedere al [portale di Azure](https://portal.azure.com/)
2. Passare al pannello del servizio Ricerca di Azure.
3. Fare clic sull'icona "Chiavi".

Il servizio avrà *chiavi amministratore* e *chiavi di query*.

* Le *chiavi amministratore* primarie e secondarie concedono diritti completi a tutte le operazioni, inclusa la possibilità di gestire il servizio, creare ed eliminare indici, indicizzatori e origini dati. Sono disponibili due chiavi, quindi è possibile continuare a usare la chiave secondaria se si decide di rigenerare la chiave primaria e viceversa.
* Le *chiavi di query* concedono l'accesso in sola lettura agli indici e ai documenti e vengono in genere distribuite alle applicazioni client che inviano richieste di ricerca.

Ai fini di una query su un indice, è possibile usare una delle chiavi di query. Si possono anche usare le chiavi amministratore per le query, ma è necessario usare una chiave di query nel codice dell'applicazione, perché questo approccio è più coerente con il [principio del privilegio minimo](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## <a name="ii-formulate-your-query"></a>II. Formulare la query
Esistono due modi per [eseguire una ricerca nell'indice usando l'API REST](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Uno consiste nell'inviare una richiesta HTTP POST in cui i parametri di query verranno definiti in un oggetto JSON nel corpo della richiesta. L'altro consiste nell'inviare una richiesta HTTP GET in cui i parametri di query verranno definiti nell'URL della richiesta. Si noti che POST ha [limiti più ridotti](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) per quanto riguarda le dimensioni dei parametri di query rispetto a GET. Per questo motivo, è consigliabile usare POST, a meno di non avere circostanze particolari in cui l'uso di GET potrebbe essere più conveniente.

Sia per POST che per GET è necessario fornire il *nome del servizio*, il *nome dell'indice* e la *versione dell'API* corretta nell'URL della richiesta. Al momento della pubblicazione di questo documento la versione dell'API corrente è `2016-09-01`. Per GET i parametri di query devono essere forniti nella *stringa di query* alla fine dell'URL. Per il formato dell'URL, vedere di seguito:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2016-09-01

Il formato per POST è lo stesso, ma con solo la versione dell'API nei parametri della stringa di query.

#### <a name="example-queries"></a>Query di esempio
Ecco alcuni esempi di query su un indice denominato "hotels". Queste query vengono visualizzate in formato GET e POST.

Eseguire una ricerca del termine "budget" nell'intero indice e restituire solo il campo `hotelName`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=budget&$select=hotelName&api-version=2016-09-01

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
{
    "search": "budget",
    "select": "hotelName"
}
```

Applicare un filtro all'indice per trovare gli hotel con un prezzo inferiore a 150 dollari a notte e restituire `hotelId` e `description`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2016-09-01

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

Eseguire una ricerca nell'intero indice, ordinata per campo specifico (`lastRenovationDate`) in ordine decrescente, acquisire i primi due risultati e mostrare solo `hotelName` e `lastRenovationDate`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate&api-version=2016-09-01

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
{
    "search": "*",
    "orderby": "lastRenovationDate desc",
    "select": "hotelName,lastRenovationDate",
    "top": 2
}
```

## <a name="iii-submit-your-http-request"></a>III. Inviare la richiesta HTTP
Dopo aver formulato la query come parte dell'URL della richiesta HTTP (per GET) o del corpo (per POST), è possibile definire le intestazioni delle richieste e inviare la query.

#### <a name="request-and-request-headers"></a>Richiesta e intestazioni della richiesta
È necessario definire due intestazioni della richiesta per GET o tre per POST:

1. L'intestazione `api-key` deve essere impostata sulla chiave di query trovata nel passaggio I precedente. Si noti che è anche possibile usare una chiave amministratore come intestazione `api-key` , ma è consigliabile usare una chiave di query perché concederà esclusivamente l'accesso in sola lettura a indici e documenti.
2. L'intestazione `Accept` deve essere impostata su `application/json`.
3. Solo per POST l'intestazione `Content-Type` deve essere impostata anche su `application/json`.

Veder di seguito per una richiesta HTTP GET per la ricerca nell'indice "hotels" con l'API REST di Ricerca di Azure, usando una query semplice che consente di cercare il termine "motel":

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=motel&api-version=2016-09-01
Accept: application/json
api-key: [query key]
```

Di seguito è riportata la stessa query di esempio, questa volta usando HTTP POST:

```
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
Content-Type: application/json
Accept: application/json
api-key: [query key]

{
    "search": "motel"
}
```

Una richiesta di query riuscita restituirà un codice di stato `200 OK` e i risultati della ricerca saranno restituiti in formato JSON nel corpo della risposta. Ecco i risultati per la query precedente, supponendo che l'indice "hotels" sia popolato con i dati di esempio in [Importazione di dati in Ricerca di Azure tramite l'API REST](search-import-data-rest-api.md). Si noti che JSON è stato formattato per maggiore chiarezza.

```JSON
{
    "value": [
        {
            "@search.score": 0.59600675,
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags":["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": {
                "type": "Point",
                "coordinates": [-122.131577, 49.678581],
                "crs": {
                    "type":"name",
                    "properties": {
                        "name": "EPSG:4326"
                    }
                }
            }
        }
    ]
}
```

Per altre informazioni, vedere la sezione "Risposta" di [Eseguire ricerche nei documenti](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Per altre informazioni su altri codici di stato HTTP che possono essere restituiti in caso di errore, vedere [Codici di stato HTTP (Ricerca di Azure)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).



<!--HONumber=Dec16_HO2-->


