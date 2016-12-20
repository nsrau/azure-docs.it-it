---
title: Indicizzazione di BLOB JSON con l&quot;indicizzatore di BLOB di Ricerca di Azure
description: Indicizzazione di BLOB JSON con l&quot;indicizzatore di BLOB di Ricerca di Azure
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 57e32e51-9286-46da-9d59-31884650ba99
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 12/15/2016
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: fc2f30569acc49dd383ba230271989eca8a14423
ms.openlocfilehash: de7af5419aa423734ad06b236e0edf61fbb0cad1

---

# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Indicizzazione di BLOB JSON con l'indicizzatore di BLOB di Ricerca di Azure
In questo articolo viene illustrato come configurare un indicizzatore BLOB di Ricerca di Azure per estrarre il contenuto strutturato dai BLOB che contengono JSON.

## <a name="scenarios"></a>Scenari
Per impostazione predefinita, l' [indicizzatore BLOB di Ricerca di Azure](search-howto-indexing-azure-blob-storage.md) analizza i BLOB di tipo JSON come blocco singolo di testo. È spesso possibile che si voglia preservare la struttura dei documenti JSON. Ad esempio, nel caso del documento JSON seguente:

    {
        "article" : {
             "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

È possibile che si voglia analizzarlo nel documento di Ricerca di Azure con i campi "text", "datePublished" e "tags".

In alternativa, quando i BLOB contengono una **matrice di oggetti JSON**, è consigliabile che ogni elemento della matrice diventi un documento separato di Ricerca di Azure. Ad esempio, considerato un BLOB con questo JSON:  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

è possibile popolare l'indice di Ricerca di Azure con 3 documenti separati, ognuno con i campi "id" e "text".

> [!IMPORTANT]
> Questa funzionalità è attualmente disponibile in anteprima. È disponibile solo nell'API REST con la versione **2015-02-28-Preview**. Si ricordi che le API di anteprima servono per il test e la valutazione e non devono essere usate negli ambienti di produzione.
>
>

## <a name="setting-up-json-indexing"></a>Configurazione dell'indicizzazione JSON
Per indicizzare i BLOB di tipo JSON, impostare il parametro di configurazione `parsingMode` su `json`, se si vuole indicizzare ogni BLOB come singolo documento, oppure su `jsonArray`, se il BLOB contiene una matrice JSON:

    {
      "name" : "my-json-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "json" | "jsonArray" } }
    }

Se necessario, usare i **mapping dei campi** per recuperare le proprietà del documento JSON di origine usato per popolare l'indice di ricerca di destinazione.  Questa procedura verrà descritta in dettaglio più avanti.

> [!IMPORTANT]
> Quando si usa la modalità di analisi su `json` o `jsonArray`, Ricerca di Azure presuppone che tutti i BLOB nell'origine dati siano di tipo JSON. Se è necessario supportare una combinazione di BLOB di tipo JSON e non JSON nella stessa origine dati, comunicare questa esigenza sul [sito UserVoice](https://feedback.azure.com/forums/263029-azure-search).
>
>

## <a name="using-field-mappings-to-build-search-documents"></a>Uso dei mapping dei campi per creare documenti di ricerca
Ricerca di Azure non può attualmente indicizzare direttamente documenti JSON arbitrari, perché supporta solo tipi di dati primitivi, matrici di stringhe e punti GeoJSON. È tuttavia possibile usare i **mapping dei campi** per selezionare parti del documento JSON e "portarle" nei campi di livello superiore del documento di ricerca. Per informazioni sugli elementi fondamentali del mapping dei campi, vedere [I mapping dei campi dell'indicizzatore di Ricerca di Azure colmano le differenze tra le origini dati e gli indici di ricerca](search-indexer-field-mappings.md).

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

I nomi dei campi di origine nei mapping vengono specificati mediante la notazione di tipo [Puntatore JSON](http://tools.ietf.org/html/rfc6901) . Iniziare con una barra per fare riferimento alla radice del documento JSON, quindi passare alla proprietà desiderata, a un livello di annidamento arbitrario, usando un percorso separato da barre.

È anche possibile fare riferimento a singoli elementi della matrice usando un indice a base zero. Ad esempio, per selezionare il primo elemento della matrice "tags" dall'esempio precedente, usare un mapping dei campi analogo al seguente:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Se un nome del campo di origine in un percorso di mapping dei campi fa riferimento a una proprietà inesistente nel file JSON, il mapping verrà ignorato senza errori. Ciò consente di supportare documenti con schemi diversi, ovvero un caso di utilizzo comune. Poiché non è disponibile alcuna convalida, sarà necessario evitare accuratamente gli errori di ortografia nella specifica dei mapping dei campi.
>
>

Se i documenti JSON contengono solo semplici proprietà di livello superiore, è possibile che i mapping dei campi non siano necessari. Ad esempio, se il file JSON ha un aspetto analogo al seguente, le proprietà "text", "datePublished" e "tags" di livello superiore verranno associate direttamente ai campi corrispondenti nell'indice di ricerca:

    {
       "text" : "A hopefully useful article explaining how to parse JSON blobs",
       "datePublished" : "2016-04-13"
       "tags" : [ "search", "storage", "howto" ]    
     }

## <a name="indexing-nested-json-arrays"></a>Indicizzazione delle matrici JSON annidate
Se si vuole indicizzare una matrice di oggetti JSON, ma questa matrice è annidata in un punto qualsiasi all'interno del documento? È possibile scegliere la proprietà che contiene la matrice usando la proprietà di configurazione `documentRoot` . Ad esempio, se i BLOB sono simili a questo:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Usare questa configurazione per indicizzare la matrice contenuta nella proprietà "level2":

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }


## <a name="request-examples"></a>Esempi di richiesta
Per concludere, ecco gli esempi completi dei payload.

Origine dati:

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
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
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }

## <a name="help-us-make-azure-search-better"></a>Come contribuire al miglioramento di Ricerca di Azure
Se si hanno domande sulle funzionalità o idee per apportare miglioramenti, contattare Microsoft sul [sito UserVoice](https://feedback.azure.com/forums/263029-azure-search/).



<!--HONumber=Nov16_HO3-->


