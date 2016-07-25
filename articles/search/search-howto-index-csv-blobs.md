<properties
pageTitle="Indicizzazione di BLOB CSV con l'indicizzatore di BLOB di Ricerca di Azure | Microsoft Azure"
description="Informazioni su come indicizzare BLOB CSV con Ricerca di Azure"
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
ms.date="07/12/2016"
ms.author="eugenesh" />

# Indicizzazione di BLOB CSV con l'indicizzatore di BLOB di Ricerca di Azure 

Per impostazione predefinita, l'[indicizzatore di BLOB di Ricerca di Azure](search-howto-indexing-azure-blob-storage.md) analizza i BLOB di testo delimitato come blocco singolo di testo. Nel caso dei BLOB che contengono dati, tuttavia, è spesso consigliabile gestire ogni riga del BLOB come documento separato. Nel caso del testo delimitato da virgole seguente, ad esempio:

	id, datePublished, tags
	1, 2016-01-12, "azure-search,azure,cloud" 
	2, 2016-07-07, "cloud,mobile" 

È consigliabile analizzarlo in due documenti, ognuno dei quali contiene campi "id", "datePublished" e "tags".

In questo articolo verrà illustrato come analizzare i BLOB CSV con un indicizzatore di BLOB di Ricerca di Azure.

> [AZURE.IMPORTANT] Questa funzionalità è attualmente disponibile in anteprima. È disponibile solo nell'API REST con la versione **2015-02-28-Preview**. Si ricordi che le API di anteprima servono per il test e la valutazione e non devono essere usate negli ambienti di produzione.

## Configurazione dell'indicizzazione di CSV

Per indicizzare BLOB CSV, creare o aggiornare la definizione di un indicizzatore con la modalità di analisi `delimitedText`:

	{
	  "name" : "my-csv-indexer",
	  ... other indexer properties
	  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
	}

Per altre informazioni sull'API di creazione di un indicizzatore, vedere [Creare un indicizzatore](search-api-indexers-2015-02-28-preview.md#create-indexer).

`firstLineContainsHeaders` indica che la prima riga (non vuota) di ogni BLOB contiene un'intestazione. Se i BLOB non contengono una riga di intestazione iniziale, è necessario specificare le intestazioni nella configurazione dell'indicizzatore:

	"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

È attualmente supportata solo la codifica UTF-8. È inoltre supportato solo il carattere di virgola `','` come delimitatore. Se si necessita di supporto per altre codifiche o altri delimitatori, inviare commenti nel [sito UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [AZURE.IMPORTANT] Quando si usa la modalità di analisi di testo delimitato, Ricerca di Azure presuppone che tutti i BLOB nell'origine dati siano di tipo CSV. Se è necessario supportare una combinazione di BLOB di tipo CSV e non CSV nella stessa origine dati, comunicare questa esigenza sul [sito UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## Esempi di richiesta

Per concludere, ecco gli esempi completi del payload.

Origine dati:

	POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "my-blob-datasource",
	    "type" : "azureblob",
	    "credentials" : { "connectionString" : "<my storage connection string>" },
	    "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
	}   

Indicizzatore:

	POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "name" : "my-csv-indexer",
	  "dataSourceName" : "my-blob-datasource",
	  "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
	}

## Come contribuire al miglioramento di Ricerca di Azure

Se si hanno domande sulle funzionalità o idee per apportare miglioramenti, contattare Microsoft sul [sito UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

<!---HONumber=AcomDC_0713_2016-->