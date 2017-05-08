---
title: Indicizzazione in Archiviazione tabelle di Azure con Ricerca di Azure | Microsoft Docs
description: Informazioni su come indicizzare i dati archiviati in Archiviazione tabelle di Azure con Ricerca di Azure
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 1cc27411-d0cc-40ed-8aed-c7cb9ab402b9
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/10/2017
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: 7679aa86aa24396d9cd7cf84a8cafe7950ad6d62
ms.lasthandoff: 04/20/2017

---

# <a name="index-azure-table-storage-with-azure-search"></a>Indicizzare in Archiviazione tabelle di Azure con Ricerca di Azure
In questo articolo viene illustrato come usare Ricerca di Azure per indicizzare i dati archiviati in Archiviazione tabelle di Azure.

## <a name="set-up-azure-table-storage-indexing"></a>Configurare l'indicizzazione in Archiviazione tabelle di Azure

È possibile configurare un indicizzatore dell'Archiviazione tabelle di Azure mediante queste risorse:

* [Portale di Azure](https://ms.portal.azure.com)
* [API REST](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations) Ricerca di Azure
* [.NET SDK](https://aka.ms/search-sdk) Ricerca di Azure

In questo caso viene illustrato il flusso tramite l'API REST. 

### <a name="step-1-create-a-datasource"></a>Passaggio 1: Creare un'origine dati

Un'origine dati specifica i dati da indicizzare, le credenziali necessarie per accedere ai dati e le norme che consentono a Ricerca di Azure di identificare in modo efficace le modifiche apportate ai dati.

Per l'indicizzazione delle tabelle, l'origine dati deve possedere le proprietà seguenti:

- Il parametro **name** è il nome univoco dell'origine dati all'interno del servizio di ricerca.
- **type** deve essere `azuretable`.
- Il parametro **credentials** contiene la stringa di connessione all'account di archiviazione. Per altre informazioni, vedere [Specificare le credenziali](#Credentials).
- Il parametro **container** imposta il nome della tabella e una query facoltativa.
    - Specificare il nome della tabella usando il parametro `name`.
    - Specificare facoltativamente una query usando il parametro `query`. 

> [!IMPORTANT] 
> Se possibile, usare un filtro sul parametro PartitionKey per ottimizzare le prestazioni. Qualsiasi altra query comporterà un'analisi completa delle tabelle e un conseguente peggioramento delle prestazioni in caso di tabelle di grandi dimensioni. Vedere [Considerazioni sulle prestazioni](#Performance).


Per creare un'origine dati:

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Per altre informazioni sull'API di creazione dell'origine dati, vedere [Creare un'origine dati](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>Metodi per specificare le credenziali ####

Per specificare le credenziali per la tabella, sono disponibili questi modi: 

- **Stringa di connessione dell'account di archiviazione per accesso completo**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`.Per ottenere la stringa di connessione dal portale di Azure**, passare al pannello dell'account di archiviazione e quindi selezionare**  > **Impostazioni** > **Chiavi** (per gli account di archiviazione della versione classica) oppure **Impostazioni** > **Chiavi di accesso** (per gli account di archiviazione di Azure Resource Manager).
- **Stringa di connessione della firma di accesso condiviso dell'account di archiviazione**: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` la firma di accesso condiviso deve avere le autorizzazioni per le operazioni di elenco e lettura per i contenitori (tabelle) e gli oggetti (righe di tabella).
-  **Firma di accesso condiviso tabella**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` la firma di accesso condiviso deve disporre delle autorizzazioni di query (lettura) nella tabella.

Per altre informazioni sulle firme di accesso condiviso, vedere [Uso delle firme di accesso condiviso](../storage/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Se si usano le credenziali di firma di accesso condiviso, sarà necessario aggiornare periodicamente le credenziali dell'origine dati con firme rinnovate per impedire che scadano. Se le credenziali di firma di accesso condiviso scadono, l'indicizzatore ha esito negativo con un messaggio di errore simile a "Le credenziali specificate nella stringa di connessione non sono valide o sono scadute".  

### <a name="step-2-create-an-index"></a>Passaggio 2: Creare un indice
L'indice consente di specificare i campi in un documento, gli attributi e altri costrutti che danno forma all'esperienza della ricerca.

Per creare un indice:

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }

Per altre informazioni sulla creazione di indici, vedere [Creare un indice](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-3-create-an-indexer"></a>Passaggio 3: Creare un indicizzatore
Un indicizzatore si connette a un'origine dati con un indice di ricerca di destinazione e consente di pianificare l'automatizzazione dell'aggiornamento dei dati. 

Dopo aver creato l'indice e l'origine dati, è possibile creare l'indicizzatore:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

L'indicizzatore verrà eseguito ogni due ore. L'intervallo di pianificazione è impostato su "PT2H". Per eseguire un indicizzatore ogni 30 minuti, impostare l'intervallo su "PT30M". L'intervallo minimo supportato è di cinque minuti. La pianificazione è facoltativa; se omessa, l'indicizzatore viene eseguito una sola volta al momento della creazione. Tuttavia, è possibile eseguire un indicizzatore su richiesta in qualsiasi momento.   

Per altre informazioni sull'API di creazione dell'indicizzatore dati, vedere [Creare un indicizzatore](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

## <a name="deal-with-different-field-names"></a>Gestire nomi campo diversi
I nomi campo nell'indice esistente sono talvolta diversi dai nomi proprietà nella tabella. È possibile usare i mapping dei campi per eseguire il mapping dei nomi di proprietà forniti dalla tabella ai nomi di campo nell'indice di ricerca. Per altre informazioni sui mapping dei campi, vedere [I mapping dei campi dell'indicizzatore di Ricerca di Azure colmano le differenze tra le origini dati e gli indici di ricerca](search-indexer-field-mappings.md).

## <a name="handle-document-keys"></a>Gestire le chiavi del documento
In Ricerca di Azure la chiave del documento identifica un documento in modo univoco. Ogni indice di ricerca deve avere esclusivamente un campo chiave di tipo `Edm.String`. Il campo chiave è necessario per ogni documento da aggiungere all'indice. È l'unico campo obbligatorio.

La chiave delle righe è composta. Ricerca di Azure genera pertanto un campo sintetico denominato `Key`, vale a dire una concatenazione di valori di chiave di partizione e chiave di riga. Se ad esempio il parametro PartitionKey di una riga è `PK1` e il parametro RowKey è `RK1`, il valore del campo `Key` è `PK1RK1`.

> [!NOTE]
> Il valore `Key` può contenere caratteri non validi nelle chiavi del documento, ad esempio i trattini. È possibile risolvere i problemi legati ai caratteri non validi usando la `base64Encode` [funzione di mapping dei campi](search-indexer-field-mappings.md#base64EncodeFunction). In questo caso, ricordarsi di usare la codifica Base64 sicura per gli URL quando si passano le chiavi dei documenti nelle chiamate API, ad esempio in una ricerca.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Indicizzazione incrementale e rilevamento delle eliminazioni
Quando un indicizzatore di tabelle viene configurato per l'esecuzione in base a una pianificazione, vengono indicizzate nuovamente solo le righe nuove o aggiornate, come definito dal valore `Timestamp` di una riga. Non è necessario specificare una norma di rilevamento delle modifiche. L'indicizzazione incrementale viene abilitata automaticamente.

Per indicare che alcuni documenti specifici devono essere rimossi dall'indice, è possibile usare una strategia di eliminazione temporanea. Invece di eliminare una riga, aggiungere una proprietà che ne indica l'eliminazione e impostare norme di rilevamento dell'eliminazione temporanea nell'origine dati. Il tipo di norme seguente, ad esempio, indica che una riga viene eliminata se la proprietà `IsDeleted` della riga è impostata sul valore `"true"`:

    PUT https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "<query>" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   

<a name="Performance"></a>
## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

Ricerca di Azure usa per impostazione predefinita il filtro di query seguente: `Timestamp >= HighWaterMarkValue`. Poiché le tabelle di Azure non dispongono di un indice secondario nel campo `Timestamp`, questo tipo di query richiede un'analisi completa delle tabelle e risulta pertanto lenta nell'analisi delle tabelle di grandi dimensioni.


Ecco due possibili approcci per migliorare le prestazioni dell'indicizzazione delle tabelle. Entrambi gli approcci si basano sull'utilizzo delle partizioni delle tabelle: 

- Se i dati possono essere partizionati naturalmente in diversi intervalli della partizione, creare un'origine dati e un indicizzatore corrispondente per ogni intervallo della partizione. Ogni indicizzatore deve a questo punto elaborare un solo intervallo specifico della partizione e ciò va a vantaggio delle prestazioni della query. Se i dati da indicizzare hanno un numero ridotto di partizioni fisse, è ancora meglio, perché ciascun indicizzatore analizza solo una partizione. Per creare ad esempio un'origine dati per l'elaborazione di un intervallo della partizione con le chiavi da `000` a `100`, usare una query simile alla seguente: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Se i dati sono partizionati in base all'ora (ad esempio, si crea una nuova partizione ogni giorno o ogni settimana), considerare l'approccio seguente: 
    - Usare una query nel formato: `(PartitionKey ge <TimeStamp>) and (other filters)`. 
    - Monitorare lo stato dell'indicizzatore usando l'[API Get Indexer Status](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) (Ottenere lo stato dell'indicizzatore) e aggiornare periodicamente la condizione `<TimeStamp>` della query in base al valore limite massimo corretto più recente. 
    - Con questo approccio, se è necessario attivare una reindicizzazione completa, occorre reimpostare la query dell'origine dati oltre a reimpostare l'indicizzatore. 


## <a name="help-us-make-azure-search-better"></a>Come contribuire al miglioramento di Ricerca di Azure
Se si hanno domande sulle funzionalità o idee per apportare miglioramenti, contattare Microsoft sul [sito UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

