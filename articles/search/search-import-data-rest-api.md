---
title: 'Caricare dati: API REST e Ricerca di Azure | Microsoft Docs'
description: Informazioni su come caricare dati in un indice di Ricerca di Azure tramite l'API REST.
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
editor: 
tags: 
ms.assetid: 8d0749fb-6e08-4a17-8cd3-1a215138abc6
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 12/08/2016
ms.author: ashmaka
ms.openlocfilehash: f22a33ed86fbfc46dfa732239263a49f34c4afee
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="upload-data-to-azure-search-using-the-rest-api"></a>Caricare dati in Ricerca di Azure tramite l'API REST
> [!div class="op_single_selector"]
>
> * [Panoramica](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
>
>

Questo articolo illustra come usare l' [API REST di Ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice/) per importare dati in un indice di Ricerca di Azure.

Prima di iniziare questa procedura dettagliata, è necessario avere [creato un indice di Ricerca di Azure](search-what-is-an-index.md).

Per effettuare il push dei documenti nell'indice con l'API REST, inviare una richiesta HTTP POST all'endpoint dell'URL dell'indice. Il corpo della richiesta HTTP è un oggetto JSON che contiene i documenti da aggiungere, modificare o eliminare.

## <a name="identify-your-azure-search-services-admin-api-key"></a>Identificare la chiave API amministratore del servizio Ricerca di Azure
Quando si inviano richieste HTTP al servizio tramite l'API REST, *ogni* richiesta API deve includere la chiave API generata per il servizio di ricerca di cui è stato effettuato il provisioning. La presenza di una chiave valida stabilisce una relazione di trust, in base alle singole richieste, tra l'applicazione che invia la richiesta e il servizio che la gestisce.

1. Per trovare le chiavi API del servizio, è possibile accedere al [portale di Azure](https://portal.azure.com/)
2. Passare al pannello del servizio Ricerca di Azure.
3. Fare clic sull'icona "Chiavi".

Il servizio avrà *chiavi amministratore* e *chiavi di query*.

* Le *chiavi amministratore* primarie e secondarie concedono diritti completi a tutte le operazioni, inclusa la possibilità di gestire il servizio, creare ed eliminare indici, indicizzatori e origini dati. Sono disponibili due chiavi, quindi è possibile continuare a usare la chiave secondaria se si decide di rigenerare la chiave primaria e viceversa.
* Le *chiavi di query* concedono l'accesso in sola lettura agli indici e ai documenti e vengono in genere distribuite alle applicazioni client che inviano richieste di ricerca.

Per l'importazione di dati in un indice è possibile usare la chiave amministratore primaria o secondaria.

## <a name="decide-which-indexing-action-to-use"></a>Decidere quale azione di indicizzazione usare
Quando si usa l'API REST, si inviano richieste HTTP POST con corpi delle richieste JSON all'URL dell'endpoint dell'indice di Ricerca di Azure. L'oggetto JSON nel corpo della richiesta HTTP contiene una matrice JSON denominata "value" che include oggetti JSON che rappresentano i documenti da aggiungere all'indice, aggiornare o eliminare.

Ogni oggetto JSON nella matrice "value" rappresenta un documento da indicizzare. Ognuno di questi oggetti contiene la chiave del documento e specifica l'azione di indicizzazione desiderata, ovvero caricamento, unione, eliminazione e così via. A seconda delle azioni scelte tra le seguenti, per ogni documento devono essere inclusi solo campi specifici:

| @search.action | Descrizione | Campi necessari per ogni documento | Note |
| --- | --- | --- | --- |
| `upload` |L'azione `upload` è simile a "upsert", in cui il documento viene inserito se è nuovo e aggiornato o sostituito se esiste già. |chiave, oltre a tutti gli altri campi da definire |Quando si aggiorna o si sostituisce un documento esistente, qualsiasi campo non specificato nella richiesta avrà il campo impostato su `null`. Ciò si verifica anche quando il campo è stato precedentemente impostato su un valore diverso da null. |
| `merge` |Aggiorna un documento esistente con i campi specificati. Se il documento non esiste nell'indice, l'unione non riuscirà. |chiave, oltre a tutti gli altri campi da definire |I campi specificati in un'azione di unione sostituiscono i campi esistenti nel documento. Sono inclusi anche i campi di tipo `Collection(Edm.String)`. Ad esempio, se il documento contiene un campo `tags` con valore `["budget"]` e si esegue un'unione con valore `["economy", "pool"]` per `tags`, il valore finale del campo `tags` sarà `["economy", "pool"]` e non `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Questa azione si comporta come `merge` se nell'indice esiste già un documento con la chiave specificata. Se il documento non esiste, si comporta come `upload` con un nuovo documento. |chiave, oltre a tutti gli altri campi da definire |- |
| `delete` |Rimuove il documento specificato dall'indice. |solo campo chiave |Tutti i campi diversi dal campo chiave specificati verranno ignorati. Se si vuole rimuovere un singolo campo da un documento, usare invece `merge` e impostare il campo su Null in modo esplicito. |

## <a name="construct-your-http-request-and-request-body"></a>Creare la richiesta HTTP e il corpo della richiesta
Ora che sono stati raccolti i valori dei campi necessari per le operazioni sull'indice, si è pronti per creare la richiesta HTTP effettiva e il corpo della richiesta JSON per importare i dati.

#### <a name="request-and-request-headers"></a>Richiesta e intestazioni della richiesta
Nell'URL è necessario fornire il nome del servizio, il nome dell'indice, in questo caso "hotels", nonché la versione corretta dell'API. Al momento della pubblicazione di questo documento la versione dell'API corrente è `2016-09-01`. È necessario definire le intestazioni della richiesta `Content-Type` e `api-key`. Nel secondo caso, usare una delle chiavi amministratore del servizio.

    POST https://[search service].search.windows.net/indexes/hotels/docs/index?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

#### <a name="request-body"></a>Request Body
```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
            "description_fr": "Meilleur hôtel en ville",
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
            "description_fr": "Hôtel le moins cher en ville",
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
        },
        {
            "@search.action": "delete",
            "hotelId": "6"
        }
    ]
}
```

In questo caso, si useranno `upload`, `mergeOrUpload` e `delete` come azioni di ricerca.

Si supponga che l'indice "hotels" di esempio sia già popolato con alcuni documenti. Si noti che non è stato necessario specificare tutti i possibili campi dei documenti quando si usa `mergeOrUpload` e come viene specificata solo la chiave del documento (`hotelId`) quando si usa `delete`.

Si noti anche che è possibile includere solo fino a 1000 documenti, o 16 MB, in una singola richiesta di indicizzazione.

## <a name="understand-your-http-response-code"></a>Informazioni sul codice di risposta HTTP
#### <a name="200"></a>200
Dopo l'invio di una richiesta di indicizzazione riuscita, si riceverà una risposta HTTP con codice di stato `200 OK`. Il corpo JSON della risposta HTTP sarà il seguente:

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": true,
            "errorMessage": null
        },
        ...
    ]
}
```

#### <a name="207"></a>207
Quando almeno un elemento non è stato indicizzato correttamente, viene restituito il codice di stato `207` . Il corpo JSON della risposta HTTP contiene informazioni sui documenti la cui richiesta di indicizzazione non è riuscita.

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": false,
            "errorMessage": "The search service is too busy to process this document. Please try again later."
        },
        ...
    ]
}
```

> [!NOTE]
> Spesso questo significa che il carico sul servizio di ricerca sta per raggiungere un punto in cui le richieste di indicizzazione inizieranno a restituire risposte `503`. In questo caso, è consigliabile interrompere l'invio del codice client e attendere prima di riprovare. Il sistema avrà così tempo per recuperare, aumentando le probabilità che le future richieste siano soddisfatte. La ripetizione rapida delle richieste prolungherà semplicemente il tempo necessario per risolvere la situazione.
>
>

#### <a name="429"></a>429
Il codice di stato `429` viene restituito quando è stata superata la quota del numero di documenti per indice.

#### <a name="503"></a>503
Il codice di stato `503` viene restituito se nessuno degli elementi nella richiesta è stato indicizzato. Questo errore indica che il sistema è sovraccarico e non può elaborare la richiesta in questo momento.

> [!NOTE]
> In questo caso, è consigliabile interrompere l'invio del codice client e attendere prima di riprovare. Il sistema avrà così tempo per recuperare, aumentando le probabilità che le future richieste siano soddisfatte. La ripetizione rapida delle richieste prolungherà semplicemente il tempo necessario per risolvere la situazione.
>
>

Per altre informazioni su azioni sui documenti e risposte di esito positivo/errore, vedere [Aggiungere, aggiornare o eliminare documenti](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents). Per altre informazioni su altri codici di stato HTTP che possono essere restituiti in caso di errore, vedere [Codici di stato HTTP (Ricerca di Azure)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

## <a name="next-steps"></a>Passaggi successivi
Dopo il popolamento dell'indice di Ricerca di Azure, si potrà iniziare a eseguire una query per la ricerca di documenti. Per informazioni dettagliate, vedere [Eseguire query su un indice di Ricerca di Azure](search-query-overview.md) .
