---
title: Indicizzazione di BLOB JSON con l'indicizzatore di BLOB di Ricerca di Azure
description: Indicizzazione di BLOB JSON con l'indicizzatore di BLOB di Ricerca di Azure
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
ms.date: 09/07/2017
ms.author: eugenesh
ms.openlocfilehash: 2dac2c5980970946a6b9c26ee6ee8ac0f0344144
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2017
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Indicizzazione di BLOB JSON con l'indicizzatore di BLOB di Ricerca di Azure
Questo articolo illustra come configurare un indicizzatore di BLOB di Ricerca di Azure per estrarre contenuto strutturato dai BLOB JSON in Archiviazione BLOB di Azure.

I BLOB JSON in Archiviazione BLOB di Azure consistono in genere in un singolo documento JSON o in una matrice JSON. L'indicizzatore di BLOB di Ricerca di Azure è in grado di analizzare entrambi i tipi di costruzione, a seconda dell'impostazione del parametro **parsingMode** nella richiesta.

| Documento JSON | parsingMode | DESCRIZIONE | Disponibilità |
|--------------|-------------|--------------|--------------|
| Un solo documento per BLOB | `json` | Analizza i BLOB JSON come un singolo blocco di testo. Ogni BLOB JSON diventa un singolo documento di Ricerca di Azure. | Disponibile a livello generale nelle API [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) e [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer). |
| Più documenti per BLOB | `jsonArray` | Analizza una matrice JSON nel BLOB, dove ogni elemento della matrice diventa un documento separato di Ricerca di Azure.  | In anteprima, in [REST api-version=`2016-09-01-Preview`](search-api-2016-09-01-preview.md) e nella [versione di anteprima di .NET SDK](https://aka.ms/search-sdk-preview). |

> [!Note]
> Le API di anteprima sono destinate alle attività di test e valutazione e non devono essere usate negli ambienti di produzione.
>

## <a name="setting-up-json-indexing"></a>Configurazione dell'indicizzazione JSON
L'indicizzazione dei BLOB JSON è simile all'estrazione normale dei documenti in un flusso di lavoro in tre fasi comune a tutti gli indicizzatori di Ricerca di Azure.

### <a name="step-1-create-a-data-source"></a>Passaggio 1: Creare un'origine dati

Il primo passaggio consiste nel fornire le informazioni sulla connessione all'origine dati che vengono usate dall'indicizzatore. Il tipo di origine dati, qui specificato come `azureblob`, determina i comportamenti di estrazione dei dati che vengono richiamati dall'indicizzatore. Per l'indicizzazione di BLOB JSON, la definizione dell'origine dati è identica sia per le matrici che per i documenti JSON. 

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="step-2-create-a-target-search-index"></a>Passaggio 2: Creare un indice di ricerca di destinazione 

Gli indicizzatori sono associati allo schema di un indice. Se si usa l'API, anziché il portale, preparare un indice in anticipo in modo poterlo specificare per l'operazione dell'indicizzatore. 

> [!Note]
> Gli indicizzatori vengono esposti nel portale tramite l'azione **Importa** per un numero limitato di indicizzatori disponibili a livello generale. Il flusso di lavoro di importazione può spesso costruire un indice preliminare in base ai metadati presenti nell'origine. Per altre informazioni, vedere [Importare dati in Ricerca di Azure tramite il portale](search-import-data-portal.md).

### <a name="step-3-configure-and-run-the-indexer"></a>Passaggio 3: Configurare ed eseguire l'indicizzatore

Fino a questo momento, le definizioni per l'origine dati e l'indice non sono state influenzate dal parametro parsingMode. Tuttavia, nel passaggio 3 relativo alla configurazione dell'indicizzatore, la procedura cambia a seconda di come si vuole analizzare e strutturare il contenuto dei BLOB JSON in un indice di Ricerca di Azure.

Quando si chiama l'indicizzatore, eseguire queste operazioni:

+ Impostare il parametro **parsingMode** su `json`, per indicizzare ogni BLOB come un singolo documento, oppure su `jsonArray`, se i BLOB contengono matrici JSON ed è necessario trattare ogni elemento di una matrice come un documento separato.

+ Facoltativamente, usare i **mapping dei campi** per scegliere quali proprietà del documento JSON di origine vengono usate per popolare l'indice di ricerca di destinazione. Per le matrici JSON, se la matrice esiste come proprietà di livello inferiore, è possibile impostare un elemento radice dei documenti che indica il punto in cui la matrice viene posizionata all'interno del BLOB.

> [!IMPORTANT]
> Quando si usa la modalità di analisi `json` o `jsonArray`, Ricerca di Azure presuppone che tutti i BLOB nell'origine dati siano di tipo JSON. Se è necessario supportare una combinazione di BLOB di tipo JSON e non JSON nella stessa origine dati, comunicare questa esigenza sul [sito UserVoice](https://feedback.azure.com/forums/263029-azure-search).


## <a name="how-to-parse-single-json-blobs"></a>Come analizzare i singoli BLOB JSON

Per impostazione predefinita, l' [indicizzatore BLOB di Ricerca di Azure](search-howto-indexing-azure-blob-storage.md) analizza i BLOB di tipo JSON come blocco singolo di testo. È spesso possibile che si voglia preservare la struttura dei documenti JSON. Si supponga, ad esempio, che in Archiviazione BLOB di Azure sia presente il documento JSON seguente:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

### <a name="indexer-definition-for-single-json-blobs"></a>Definizione dell'indicizzatore per singoli BLOB JSON

Se si usa l'indicizzatore di BLOB di Ricerca di Azure, un documento JSON simile all'esempio precedente viene analizzato in un singolo documento di Ricerca di Azure. Per caricare un indice, l'indicizzatore stabilisce una corrispondenza tra i campi "text", "datePublished" e "tags" dell'origine e i campi di destinazione tipizzati con lo stesso nome.

La configurazione viene specificata nel corpo di un'operazione dell'indicizzatore. Tenere presente che l'oggetto origine dati, definito in precedenza, specifica le informazioni relative alla connessione e al tipo dell'origine dati. L'indice di destinazione deve inoltre essere definito come contenitore vuoto nel servizio. I campi "schedule" e "parameters" sono facoltativi, ma se vengono omessi, l'indicizzatore viene eseguito immediatamente in modalità di analisi `json`.

Una richiesta completamente specificata può avere l'aspetto seguente:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Come già indicato, i mapping dei campi non sono obbligatori. Dato un indice con i campi "text", "datePublished" e "tag", l'indicizzatore di BLOB è in grado di dedurre il mapping corretto senza che sia definito un mapping di campi nella richiesta.

## <a name="how-to-parse-json-arrays-preview"></a>Come analizzare le matrici JSON (anteprima)

In alternativa, è possibile scegliere la funzionalità di anteprima per le matrici JSON. Questa funzionalità è utile quando i BLOB contengono una *matrice di oggetti JSON* e si vuole che ogni elemento della matrice diventi un documento separato di Ricerca di Azure. Ad esempio, dato il BLOB JSON seguente, è possibile popolare l'indice di Ricerca di Azure con tre documenti separati, ognuno con i campi "id" e "text".  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

### <a name="indexer-definition-for-a-json-array"></a>Definizione dell'indicizzatore per una matrice JSON

Per una matrice JSON, la richiesta dell'indicizzatore usa l'API di anteprima e la modalità di analisi `jsonArray`. Questi sono gli unici due requisiti specifici delle matrici per l'indicizzazione di BLOB JSON.

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Anche in questo caso, è opportuno notare che i mapping dei campi non sono obbligatori. Dato un indice con i campi "id" e "text", l'indicizzatore di BLOB è in grado di dedurre il mapping corretto senza un elenco di mapping di campi.

### <a name="nested-json-arrays"></a>Matrici JSON annidate
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

Usare questa configurazione per indicizzare la matrice contenuta nella proprietà `level2`:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="using-field-mappings-to-build-search-documents"></a>Uso dei mapping dei campi per creare documenti di ricerca

Quando i campi di origine e di destinazione non sono perfettamente allineati, è possibile definire una sezione di mapping dei campi nel corpo della richiesta per esplicitare le associazioni tra i campi.

Ricerca di Azure non può attualmente indicizzare direttamente documenti JSON arbitrari, perché supporta solo tipi di dati primitivi, matrici di stringhe e punti GeoJSON. È tuttavia possibile usare i **mapping dei campi** per selezionare parti del documento JSON e "portarle" nei campi di livello superiore del documento di ricerca. Per nozioni fondamentali sui mapping dei campi, vedere [Mapping dei campi negli indicizzatori di Ricerca di Azure](search-indexer-field-mappings.md).

Tornando al documento JSON di esempio:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Si supponga di avere un indice di ricerca con i campi seguenti: `text` di tipo `Edm.String`, `date` di tipo `Edm.DateTimeOffset` e `tags` di tipo `Collection(Edm.String)`. Si noti la discrepanza tra "datePublished" nell'origine e il campo `date` nell'indice. Per eseguire il mapping del file JSON per ottenere la forma desiderata, usare i mapping dei campi seguenti:

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

## <a name="example-indexer-request-with-field-mappings"></a>Esempio: richiesta dell'indicizzatore con mapping dei campi

L'esempio seguente mostra un payload di indicizzatore completo con mapping dei campi:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
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

## <a name="see-also"></a>Vedere anche 

+ [Indicizzatori in Ricerca di Azure](search-indexer-overview.md)
+ [Indicizzazione di Archiviazione BLOB di Azure con Ricerca di Azure](search-howto-index-json-blobs.md)
+ [Indicizzazione di BLOB CSV con l'indicizzatore di BLOB di Ricerca di Azure](search-howto-index-csv-blobs.md)
+ [Esercitazione: Cercare dati semistrutturati in Archiviazione BLOB di Azure ](search-semi-structured-data.md)