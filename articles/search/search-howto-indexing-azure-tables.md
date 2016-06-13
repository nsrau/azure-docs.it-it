<properties
pageTitle="Indicizzazione in Archiviazione tabelle di Azure con Ricerca di Azure"
description="Scoprire come indicizzare i dati archiviati nelle tabelle di Azure con Ricerca di Azure"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="05/28/2016"
ms.author="eugenesh" />

# Indicizzazione in Archiviazione tabelle di Azure con Ricerca di Azure

In questo articolo viene illustrato come usare Ricerca di Azure per indicizzare i dati archiviati in Archiviazione tabelle di Azure. Con il nuovo indicizzatore di tabelle di Ricerca di Azure, questo processo diventa rapido e facile.

> [AZURE.IMPORTANT] Questa funzionalità è attualmente in anteprima. È disponibile solo nell'API REST con la versione **2015-02-28-Preview**. Si ricordi che le API di anteprima servono per il test e la valutazione e non devono essere usate negli ambienti di produzione.

## Configurazione dell'indicizzazione delle tabelle di Azure

Per installare e configurare un indicizzatore di tabelle di Azure, è possibile usare l'API REST di Ricerca di Azure per creare e gestire **indicizzatori** e **origini dati** come descritto in [Operazioni dell'indicizzatore](https://msdn.microsoft.com/library/azure/dn946891.aspx). In futuro, il supporto per l'indicizzazione di tabelle sarà aggiunto a .NET SDK di Ricerca di Azure e al portale di Azure.

Un'origine dati specifica i dati da indicizzare, le credenziali necessarie per accedere ai dati e i criteri che consentono a Ricerca di Azure di identificare in modo efficace le modifiche apportate ai dati (righe nuove, modificate o eliminate).

Un indicizzatore legge i dati da un'origine dati e li carica in un indice di ricerca di destinazione.

Per configurare l'indicizzazione delle tabelle:

1. Creare un'origine dati
	- Impostare il parametro `type` su `azuretable`
	- Passare la stringa di connessione dell'account di archiviazione come parametro `credentials.connectionString`
	- Specificare il nome della tabella usando il parametro `container.name`
	- Specificare facoltativamente una query usando il parametro `container.query`. Se possibile, usare un filtro sul parametro PartitionKey per ottimizzare le prestazioni. Qualsiasi altra query comporterà un'analisi completa della tabella e il conseguente peggioramento delle prestazioni per tabelle di grandi dimensioni.
2. Creare un indice di ricerca con lo schema corrispondente alle colonne della tabella che si desidera indicizzare. 
3. Creare l'indicizzatore connettendo l'origine dati all'indice di ricerca.

### Creare un'origine dati

	POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "table-datasource",
	    "type" : "azuretable",
	    "credentials" : { "connectionString" : "<my storage connection string>" },
	    "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
	}   

Per altre informazioni sull'API di creazione dell'origine dati, vedere [Creare un'origine dati](search-api-indexers-2015-02-28-preview.md#create-data-source).

### Creare un indice 

	POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
	Content-Type: application/json
	api-key: [admin key]

	{
  		"name" : "my-target-index",
  		"fields": [
    		{ "name": "key", "type": "Edm.String", "key": true, "searchable": false },
    		{ "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
  		]
	}

Per altre informazioni sull'API di creazione di un indice, vedere [Create Index](https://msdn.microsoft.com/library/dn798941.aspx) (Creare un indice)

### Creare un indicizzatore 

Infine creare l'indicizzatore che fa riferimento all'origine dati e all'indice di destinazione. Ad esempio:

	POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "name" : "table-indexer",
	  "dataSourceName" : "table-datasource",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" }
	}

Per altre informazioni sull'API di creazione di un indicizzatore, vedere [Creare un indicizzatore](search-api-indexers-2015-02-28-preview.md#create-indexer).

Questo è tutto il necessario per un'indicizzazione perfetta.

## Gestione di nomi campo diversi

I nomi campo nell'indice esistente saranno spesso diversi dai nomi proprietà nella tabella. È possibile usare i **mapping dei campi** per eseguire il mapping dei nomi di proprietà forniti dalla tabella ai nomi di campo nell'indice di ricerca. Per altre informazioni sui mapping dei campi, vedere [I mapping dei campi dell'indicizzatore di Ricerca di Azure colmano le differenze tra le origini dati e gli indici di ricerca](search-indexer-field-mappings.md).

## Gestione delle chiavi del documento

In Ricerca di Azure la chiave del documento identifica un documento in modo univoco. Ogni indice di ricerca deve avere esclusivamente un campo chiave di tipo `Edm.String`. Il campo chiave è necessario per ogni documento da aggiungere all'indice ed è l'unico campo obbligatorio.

La chiave delle righe è composta. Ricerca di Azure genera pertanto un campo sintetico denominato `Key`, vale a dire una concatenazione di valori di chiave di partizione e chiave di riga. Ad esempio, se il parametro PartitionKey di una riga è `PK1` e il parametro RowKey è `RK1`, il valore del campo `Key` sarà `PK1RK1`.

> [AZURE.NOTE] Il valore `Key` può contenere caratteri non validi nelle chiavi del documento, ad esempio i trattini. È possibile risolvere i problemi legati ai caratteri non validi usando la [funzione del mapping dei campi](search-indexer-field-mappings.md#base64EncodeFunction) `base64Encode`. In questo caso, ricordarsi di usare la codifica Base64 sicura per gli URL quando si passano le chiavi dei documenti nelle chiamate API, ad esempio in una ricerca.

## Indicizzazione incrementale e rilevamento delle eliminazioni
 
Quando un indicizzatore di tabelle viene configurato per l'esecuzione in base a una pianificazione, vengono indicizzate nuovamente solo le righe nuove o aggiornate, come definito dal valore `Timestamp` di una riga. Non è necessario specificare un criterio di rilevamento delle modifiche perché l'indicizzazione incrementale viene abilitata automaticamente.

Per indicare che alcuni documenti devono essere rimossi dall'indice, è consigliabile usare una strategia di eliminazione temporanea, anziché eliminare una riga, quindi aggiungere una proprietà per indicare che sono stati eliminati e configurare un criterio di rilevamento dell'eliminazione temporanea nell'origine dati. Il criterio illustrato sotto, ad esempio, indica che una riga sarà eliminata se contiene una proprietà `IsDeleted` con il valore `"true"`:

	PUT https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]
	
	{
	    "name" : "my-table-datasource",
	    "type" : "azuretable",
	    "credentials" : { "connectionString" : "<your storage connection string>" },
	    "container" : { "name" : "table name", "query" : "query" },
	    "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
	}   


## Come contribuire al miglioramento di Ricerca di Azure

Se si hanno domande sulle funzionalità o idee per apportare miglioramenti, contattare Microsoft sul [sito UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

<!---HONumber=AcomDC_0601_2016-->