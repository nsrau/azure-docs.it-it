<properties
pageTitle="Indicizzazione di BLOB JSON con l'indicizzatore di BLOB di Ricerca di Azure"
description="Informazioni su come indicizzare BLOB JSON con Ricerca di Azure"
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
ms.date="04/20/2016"
ms.author="eugenesh" />

# Indicizzazione di BLOB JSON con l'indicizzatore di BLOB di Ricerca di Azure 

Per impostazione predefinita, l'[indicizzatore di BLOB di Ricerca di Azure](search-howto-indexing-azure-blob-storage.md) analizza i BLOB JSON come blocco singolo di testo. È spesso possibile che si voglia preservare la struttura dei documenti JSON. Ad esempio, nel caso del documento JSON seguente:

	{ 
		"article" : {
	 		"text" : "A hopefully useful article explaining how to parse JSON blobs",
	        "datePublished" : "2016-04-13" 
			"tags" : [ "search", "storage", "howto" ]    
	    }
	}

È possibile che si voglia analizzarlo nei campi "text", "datePublished" e "tags" nell'indice di ricerca.

Questo articolo illustra come configurare l'indicizzatore di BLOB di Ricerca di Azure per l'analisi JSON e procedere all'indicizzazione.

> [AZURE.IMPORTANT] Questa funzionalità è attualmente disponibile in anteprima. È disponibile solo nell'API REST con la versione **2015-02-28-Preview**. Si ricordi che le API di anteprima servono per il test e la valutazione e non devono essere usate negli ambienti di produzione.

## Configurazione dell'indicizzazione JSON

Per indicizzare i BLOB JSON, usare l'indicizzatore di BLOB in modalità di "analisi JSON". Abilitare l'impostazione di configurazione `useJsonParser` nella definizione della proprietà `parameters` dell'indicizzatore:

	{
	  "name" : "my-json-indexer",
	  ... other indexer properties
	  "parameters" : { "configuration" : { "useJsonParser" : true } }
	}

Se necessario, usare i **mapping dei campi** per recuperare le proprietà del documento JSON di origine usato per popolare l'indice di ricerca di destinazione. Questa procedura verrà descritta in dettaglio più avanti.

> [AZURE.IMPORTANT] Quando si usa la modalità di analisi JSON, Ricerca di Azure presuppone che tutti i BLOB nell'origine dati siano di tipo JSON. Se è necessario supportare una combinazione di BLOB di tipo JSON e non JSON nella stessa origine dati, comunicare questa esigenza sul [sito UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## Uso dei mapping dei campi per creare documenti di ricerca 

Ricerca di Azure non può attualmente indicizzare direttamente documenti JSON arbitrari, perché supporta solo tipi di dati primitivi, matrici di stringhe e punti GeoJSON. È tuttavia possibile usare i **mapping dei campi** per selezionare parti del documento JSON e "portarle" nei campi di livello superiore del documento di ricerca. Per informazioni sui concetti di base relativi ai mapping dei campi, vedere [Personalizzazione dell'indicizzatore di Ricerca di Azure](search-indexers-customization.md).

Tornando all'esempio di documento JSON:

    { 
		"article" : {
	 		"text" : "A hopefully useful article explaining how to parse JSON blobs",
	        "datePublished" : "2016-04-13" 
			"tags" : [ "search", "storage", "howto" ]    
	    }
	}

Si supponga che sia disponibile un indice di ricerca con i campi seguenti: `text` di tipo Edm.String, `date` di tipo Edm.DateTimeOffset e `tags` di tipo Collection(Edm.String). Per eseguire il mapping del file JSON per ottenere la forma desiderata, usare i mapping dei campi seguenti:

	"fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
  	]

I nomi dei campi di origine nei mapping vengono specificati mediante la notazione di tipo [Puntatore JSON](http://tools.ietf.org/html/rfc6901). Iniziare con una barra per fare riferimento alla radice del documento JSON, quindi passare alla proprietà desiderata, a un livello di annidamento arbitrario, usando un percorso separato da barre.

È anche possibile fare riferimento a singoli elementi della matrice usando un indice a base zero. Ad esempio, per selezionare il primo elemento della matrice "tags" dall'esempio precedente, usare un mapping dei campi analogo al seguente:

	{ "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [AZURE.NOTE] Se un nome del campo di origine in un percorso di mapping dei campi fa riferimento a una proprietà inesistente nel file JSON, il mapping verrà ignorato senza errori. Ciò consente di supportare documenti con schemi diversi, ovvero un caso di utilizzo comune. Poiché non è disponibile alcuna convalida, sarà necessario evitare accuratamente gli errori di ortografia nella specifica dei mapping dei campi.

Se i documenti JSON contengono solo semplici proprietà di livello superiore, è possibile che i mapping dei campi non siano necessari. Ad esempio, se il file JSON ha un aspetto analogo al seguente, le proprietà "text", "datePublished" e "tags" di livello superiore verranno associate direttamente ai campi corrispondenti nell'indice di ricerca:
 
	{ 
	   "text" : "A hopefully useful article explaining how to parse JSON blobs",
	   "datePublished" : "2016-04-13" 
       "tags" : [ "search", "storage", "howto" ]    
 	}

> [AZURE.NOTE] Ricerca di Azure supporta attualmente solo l'analisi di un BLOB JSON in un documento di ricerca. Se i BLOB contengono matrici JSON da analizzare in più documenti di ricerca, votare [questo suggerimento in UserVoice](https://feedback.azure.com/forums/263029-azure-search/suggestions/13431384-parse-blob-containing-a-json-array-into-multiple-d) per contribuire ad assegnare la priorità corretta alle operazioni.

## Esempi di richiesta

Per concludere, ecco gli esempi completi dei payload.

Origine dati:

	POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "my-blob-datasource",
	    "type" : "azureblob",
	    "credentials" : { "connectionString" : "<my storage connection string>" },
	    "container" : { "name" : "my-container", "query" : "optional, my-folder" }
	}   

Indicizzatore:

	POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "name" : "my-json-indexer",
	  "dataSourceName" : "my-blob-datasource",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "useJsonParser" : true } }, 
      "fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
  	  ]
	}

## Come contribuire al miglioramento di Ricerca di Azure

Se si hanno domande sulle funzionalità o idee per apportare miglioramenti, contattare Microsoft sul [sito UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

<!---HONumber=AcomDC_0420_2016-->