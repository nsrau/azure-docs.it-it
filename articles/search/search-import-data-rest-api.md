<properties
    pageTitle="Importazione di dati in Ricerca di Azure tramite l'API REST | Microsoft Azure | Servizio di ricerca cloud ospitato"
    description="Come caricare dati in un indice di Ricerca di Azure tramite l'API REST."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager=""
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="02/29/2016"
    ms.author="ashmaka"/>

# Importare dati in Ricerca di Azure tramite l'API REST
> [AZURE.SELECTOR]
- [Panoramica](search-what-is-data-import.md)
- [Portale](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indicizzatori](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)


Questo articolo illustra come usare l'[API REST di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx) per importare dati in un indice di Ricerca di Azure. Prima di iniziare questa procedura dettagliata, è necessario avere [creato un indice di Ricerca di Azure](search-create-index-rest-api.md).

Per effettuare il push dei documenti nell'indice con l'API REST, inviare una richiesta HTTP POST all'endpoint dell'URL dell'indice. Il corpo della richiesta HTTP è un oggetto JSON che contiene i documenti da aggiungere, modificare o eliminare.

## I. Identificare la chiave API amministratore del servizio Ricerca di Azure
Quando si inviano richieste HTTP al servizio tramite l'API REST, *ogni* richiesta API deve includere la chiave API generata per il servizio di ricerca di cui è stato effettuato il provisioning. La presenza di una chiave valida stabilisce una relazione di trust, in base alle singole richieste, tra l'applicazione che invia la richiesta e il servizio che la gestisce.

1. Per trovare le chiavi API del servizio, è necessario accedere al [portale di Azure](https://portal.azure.com/).
2. Passare al pannello del servizio Ricerca di Azure.
3. Fare clic sull'icona "Chiavi".

Il servizio avrà *chiavi amministratore* e *chiavi di query*.
  * Le *chiavi amministratore* primarie e secondarie concedono diritti completi a tutte le operazioni, inclusa la possibilità di gestire il servizio, creare ed eliminare indici, indicizzatori e origini dati. Sono disponibili due chiavi, quindi è possibile continuare a usare la chiave secondaria se si decide di rigenerare la chiave primaria e viceversa.
  * Le *chiavi di query* concedono l'accesso in sola lettura agli indici e ai documenti e vengono in genere distribuite alle applicazioni client che inviano richieste di ricerca.

Per l'importazione di dati in un indice è possibile usare la chiave amministratore primaria o secondaria.

## II. Decidere quale azione di indicizzazione usare
Quando si usa l'API REST, si inviano richieste HTTP POST con corpi delle richieste JSON all'URL dell'endpoint dell'indice di Ricerca di Azure. L'oggetto JSON nel corpo della richiesta HTTP contiene una matrice JSON denominata "value" che include oggetti JSON che rappresentano i documenti da aggiungere all'indice, aggiornare o eliminare.

Ogni oggetto JSON nella matrice "value" rappresenta un documento da indicizzare. Ognuno di questi oggetti contiene la chiave del documento e specifica l'azione di indicizzazione desiderata, ovvero caricamento, unione, eliminazione e così via. A seconda delle azioni scelte tra le seguenti, per ogni documento devono essere inclusi solo campi specifici:

@search.action | Descrizione | Campi necessari per ogni documento | Note
--- | --- | --- | ---
`upload` | L'azione `upload` è simile a "upsert", in cui il documento viene inserito se è nuovo e aggiornato o sostituito se esiste già. | chiave, oltre a tutti gli altri campi da definire | Quando si aggiorna o si sostituisce un documento esistente, qualsiasi campo non specificato nella richiesta avrà il campo impostato su `null`. Ciò si verifica anche quando il campo è stato precedentemente impostato su un valore diverso da null.
`merge` | Aggiorna un documento esistente con i campi specificati. Se il documento non esiste nell'indice, l'unione non riuscirà. | chiave, oltre a tutti gli altri campi da definire | I campi specificati in un'azione di unione sostituiscono i campi esistenti nel documento. Sono inclusi anche i campi di tipo `Collection(Edm.String)`. Ad esempio, se il documento contiene un campo `tags` con valore `["budget"]` e si esegue un'unione con valore `["economy", "pool"]` per `tags`, il valore finale del campo `tags` sarà `["economy", "pool"]`. Non sarà `["budget", "economy", "pool"]`.
`mergeOrUpload` | Questa azione si comporta come `merge` se nell'indice esiste già un documento con la chiave specificata. Se il documento non esiste, si comporta come `upload` con un nuovo documento. | chiave, oltre a tutti gli altri campi da definire |- 
`delete` | Rimuove il documento specificato dall'indice. | solo chiave | Tutti i campi specificati oltre al campo della chiave verranno ignorati. Se si vuole rimuovere un singolo campo da un documento, usare invece *merge* e impostare semplicemente il campo su null in modo esplicito.

## III. Creare la richiesta HTTP e il corpo della richiesta
Ora che sono stati raccolti i valori dei campi necessari per le operazioni sull'indice, si è pronti per creare la richiesta HTTP effettiva e il corpo della richiesta JSON per importare i dati.

#### Richiesta e intestazioni della richiesta
Nell'URL è necessario fornire il nome del servizio, il nome dell'indice, in questo caso "hotels", nonché la versione corretta dell'API. Al momento della pubblicazione di questo documento la versione dell'API corrente è `2015-02-28`. È necessario definire le intestazioni della richiesta `Content-Type` e `api-key`. Nel secondo caso, usare una delle chiavi amministratore del servizio.

    POST https://[search service].search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28
    Content-Type: application/json
    api-key: [admin key]

#### Request Body

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

Si supponga che l'indice "hotels" di esempio sia già popolato con alcuni documenti. Si noti che non è stato necessario specificare tutti i possibili campi dei documenti quando si usa `merge` e come viene specificata solo la chiave del documento (`hotelId`) quando si usa `delete`.

Si noti anche che è possibile includere solo fino a 1000 documenti, o 16 MB, in una singola richiesta di indicizzazione.

## IV. Informazioni sul codice di risposta HTTP
#### 200
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

#### 207
Quando almeno un elemento non è stato indicizzato correttamente, viene restituito il codice di stato `207`. Il corpo JSON della risposta HTTP contiene informazioni sui documenti la cui richiesta di indicizzazione non è riuscita.

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

> [AZURE.NOTE] Spesso questo significa che il carico sul servizio di ricerca sta per raggiungere un punto in cui le richieste di indicizzazione inizieranno a restituire risposte `503`. In questo caso, è consigliabile interrompere l'invio del codice client e attendere prima di riprovare. Il sistema avrà così tempo per recuperare, aumentando le probabilità che le future richieste siano soddisfatte. La ripetizione rapida delle richieste prolungherà semplicemente il tempo necessario per risolvere la situazione.

#### 429
Il codice di stato `429` viene restituito quando è stata superata la quota del numero di documenti per indice.

#### 503
Il codice di stato `503` viene restituito se nessuno degli elementi nella richiesta è stato indicizzato. Questo errore indica che il sistema è sovraccarico e non può elaborare la richiesta in questo momento.

> [AZURE.NOTE] In questo caso, è consigliabile interrompere l'invio del codice client e attendere prima di riprovare. Il sistema avrà così tempo per recuperare, aumentando le probabilità che le future richieste siano soddisfatte. La ripetizione rapida delle richieste prolungherà semplicemente il tempo necessario per risolvere la situazione.

Per altre informazioni su azioni sui documenti e risposte di esito positivo/errore, vedere [questa pagina](https://msdn.microsoft.com/library/azure/dn798925.aspx). Per altre informazioni su altri codici di stato HTTP che possono essere restituiti in caso di errore, vedere [questo articolo](https://msdn.microsoft.com/library/azure/dn798925.aspx).

## Avanti
Dopo il popolamento dell'indice di Ricerca di Azure, si potrà iniziare a eseguire una query per la ricerca di documenti.

<!------HONumber=AcomDC_0302_2016-->