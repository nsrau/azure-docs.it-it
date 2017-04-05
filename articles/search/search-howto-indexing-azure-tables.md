---
title: Indicizzazione in Archiviazione tabelle di Azure con Ricerca di Azure
description: Scoprire come indicizzare i dati archiviati nelle tabelle di Azure con Ricerca di Azure
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
ms.date: 01/18/2017
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 66e6fec16aab7764b05b616efc0fccbfb2d0595e
ms.lasthandoff: 03/30/2017

---

# <a name="indexing-azure-table-storage-with-azure-search"></a>Indicizzazione in Archiviazione tabelle di Azure con Ricerca di Azure
In questo articolo viene illustrato come usare Ricerca di Azure per indicizzare i dati archiviati in Archiviazione tabelle di Azure. Con il nuovo indicizzatore di tabelle di Ricerca di Azure, questo processo diventa rapido e facile.

## <a name="setting-up-azure-table-indexing"></a>Configurazione dell'indicizzazione delle tabelle di Azure
Per installare e configurare un indicizzatore di tabelle di Azure, è possibile usare l'API REST di Ricerca di Azure per creare e gestire **indicizzatori** e **origini dati** come descritto in [Indexer operations](https://msdn.microsoft.com/library/azure/dn946891.aspx) (Operazioni dell'indicizzatore). È possibile anche usare la [versione 2.0-preview](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx) di .NET SDK. In futuro, il supporto per l'indicizzazione di tabelle sarà aggiunto al portale di Azure.

Un'origine dati specifica i dati da indicizzare, le credenziali necessarie per accedere ai dati e i criteri che consentono a Ricerca di Azure di identificare in modo efficace le modifiche apportate ai dati (righe nuove, modificate o eliminate).

Un indicizzatore legge i dati da un'origine dati e li carica in un indice di ricerca di destinazione.

Per configurare l'indicizzazione delle tabelle:

1. Creare un'origine dati
   * Impostare il parametro `type` su `azuretable`
   * Passare la stringa di connessione dell'account di archiviazione come parametro `credentials.connectionString`. Per informazioni dettagliate, vedere [Come specificare le credenziali](#Credentials) più avanti.
   * Specificare il nome della tabella usando il parametro `container.name`
   * Specificare facoltativamente una query usando il parametro `container.query`. Se possibile, usare un filtro sul parametro PartitionKey per ottimizzare le prestazioni. Qualsiasi altra query comporterà un'analisi completa della tabella e il conseguente peggioramento delle prestazioni per tabelle di grandi dimensioni.
2. Creare un indice di ricerca con lo schema corrispondente alle colonne della tabella che si desidera indicizzare.
3. Creare l'indicizzatore connettendo l'origine dati all'indice di ricerca.

### <a name="create-data-source"></a>Creare un'origine dati
    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Per altre informazioni sull'API di creazione dell'origine dati, vedere [Creare un'origine dati](https://msdn.microsoft.com/library/azure/dn946876.aspx).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Come specificare le credenziali ####

Per specificare le credenziali per la tabella, sono disponibili questi modi: 

- **Stringa di connessione dell'account di archiviazione per accesso completo**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`. Per ottenere la stringa di connessione dal portale di Azure, passare al pannello dell'account di archiviazione e quindi selezionare Impostazioni > Chiavi (per gli account di archiviazione della versione classica) oppure Impostazioni > Chiavi di accesso (per gli account di archiviazione di Azure Resource Manager).
- Stringa di connessione della **firma di accesso condiviso dell'account di archiviazione**: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl`. La firma di accesso condiviso deve avere le autorizzazioni per le operazioni di elenco e lettura per i contenitori (tabelle) e gli oggetti (righe di tabella).
-  **Firma di accesso condiviso per la tabella**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r`. La firma di accesso condiviso deve avere le autorizzazioni per le operazioni di query (lettura) sulla tabella.

Per altre informazioni sulle firme di accesso condiviso per l'archiviazione, vedere [Uso delle firme di accesso condiviso](../storage/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Se si usano le credenziali di firma di accesso condiviso, sarà necessario aggiornare periodicamente le credenziali dell'origine dati con firme rinnovate per impedire che scadano. Se le credenziali di firma di accesso condiviso scadono, l'indicizzatore avrà esito negativo e restituirà un messaggio di errore simile al seguente: `Credentials provided in the connection string are invalid or have expired.`.  

### <a name="create-index"></a>Creare un indice
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

Per altre informazioni sull'API di creazione di un indice, vedere [Create Index](https://msdn.microsoft.com/library/dn798941.aspx)

### <a name="create-indexer"></a>Creare un indicizzatore
Infine creare l'indicizzatore che fa riferimento all'origine dati e all'indice di destinazione. Ad esempio:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Per altre informazioni sull'API di creazione di un indicizzatore, vedere [Creare un indicizzatore](https://msdn.microsoft.com/library/azure/dn946899.aspx).

Questo è tutto il necessario per un'indicizzazione perfetta.

## <a name="dealing-with-different-field-names"></a>Gestione di nomi campo diversi
I nomi campo nell'indice esistente saranno spesso diversi dai nomi proprietà nella tabella. È possibile usare i **mapping dei campi** per eseguire il mapping dei nomi di proprietà forniti dalla tabella ai nomi di campo nell'indice di ricerca. Per altre informazioni sui mapping dei campi, vedere [I mapping dei campi dell'indicizzatore di Ricerca di Azure colmano le differenze tra le origini dati e gli indici di ricerca](search-indexer-field-mappings.md).

## <a name="handling-document-keys"></a>Gestione delle chiavi del documento
In Ricerca di Azure la chiave del documento identifica un documento in modo univoco. Ogni indice di ricerca deve avere esclusivamente un campo chiave di tipo `Edm.String`. Il campo chiave è necessario per ogni documento da aggiungere all'indice ed è l'unico campo obbligatorio.

La chiave delle righe è composta. Ricerca di Azure genera pertanto un campo sintetico denominato `Key`, vale a dire una concatenazione di valori di chiave di partizione e chiave di riga. Ad esempio, se il parametro PartitionKey di una riga è `PK1` e il parametro RowKey è `RK1`, il valore del campo `Key` sarà `PK1RK1`.

> [!NOTE]
> Il valore `Key` può contenere caratteri non validi nelle chiavi del documento, ad esempio i trattini. È possibile risolvere i problemi legati ai caratteri non validi usando la `base64Encode` [funzione di mapping dei campi](search-indexer-field-mappings.md#base64EncodeFunction). In questo caso, ricordarsi di usare la codifica Base64 sicura per gli URL quando si passano le chiavi dei documenti nelle chiamate API, ad esempio in una ricerca.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Indicizzazione incrementale e rilevamento delle eliminazioni
Quando un indicizzatore di tabelle viene configurato per l'esecuzione in base a una pianificazione, vengono indicizzate nuovamente solo le righe nuove o aggiornate, come definito dal valore `Timestamp` di una riga. Non è necessario specificare un criterio di rilevamento delle modifiche perché l'indicizzazione incrementale viene abilitata automaticamente.

Per indicare che alcuni documenti devono essere rimossi dall'indice, è consigliabile usare una strategia di eliminazione temporanea, anziché eliminare una riga, quindi aggiungere una proprietà per indicare che sono stati eliminati e configurare un criterio di rilevamento dell'eliminazione temporanea nell'origine dati. Il criterio illustrato sotto, ad esempio, indica che una riga sarà eliminata se contiene una proprietà `IsDeleted` con il valore `"true"`:

    PUT https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "query" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   


## <a name="help-us-make-azure-search-better"></a>Come contribuire al miglioramento di Ricerca di Azure
Se si hanno domande sulle funzionalità o idee per apportare miglioramenti, contattare Microsoft sul [sito UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

