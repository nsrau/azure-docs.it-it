---
title: Indicizzare i BLOB JSON da un indicizzatore di BLOB di Azure per la ricerca full-text - Ricerca di Azure
description: Ricercare per indicizzazione i BLOB JSON di Azure per il contenuto di testo usando l'indicizzatore di BLOB di Ricerca di Azure. Gli indicizzatori automatizzano l'inserimento di dati per le origini dati selezionate, ad esempio l'archiviazione BLOB di Azure.
ms.date: 12/21/2018
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 6df8d9a5c1ca1e587834ea08f73b3dd9498f8537
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2018
ms.locfileid: "53753150"
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Indicizzazione di BLOB JSON con l'indicizzatore di BLOB di Ricerca di Azure
Questo articolo illustra come configurare un indicizzatore di BLOB di Ricerca di Azure per estrarre contenuto strutturato dai BLOB JSON in Archiviazione BLOB di Azure.

Per indicizzare il contenuto JSON è possibile usare il [portale](#json-indexer-portal), le [API REST](#json-indexer-rest) o [.NET SDK](#json-indexer-dotnet). I documenti JSON inclusi in un contenitore BLOB in un account di archiviazione di Azure sono validi per tutti gli approcci. Per istruzioni su come eseguire il push di documenti JSON da altre piattaforme non Azure, vedere [Indicizzazione dei dati esterni per le query in Ricerca di Azure](search-what-is-data-import.md).

I BLOB JSON in Archiviazione BLOB di Azure consistono in genere in un singolo documento JSON o in una matrice JSON. L'indicizzatore di BLOB di Ricerca di Azure è in grado di analizzare entrambi i tipi di costruzione, a seconda dell'impostazione del parametro **parsingMode** nella richiesta.

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Usare il portale

Il metodo più semplice di indicizzazione dei documenti JSON consiste nell'usare una procedura guidata nel [portale di Azure](https://portal.azure.com/). Analizzando i metadati nel contenitore BLOB di Azure, la procedura guidata [**Importa dati**](search-import-data-portal.md) può creare un indice predefinito, eseguire il mapping dei campi di origine ai campi di indice di destinazione e caricare l'indice con una sola operazione. A seconda delle dimensioni e della complessità dei dati di origine, si può ottenere un indice di ricerca full-text operativo in pochi minuti.

### <a name="1---prepare-source-data"></a>1 - Preparare i dati di origine

Sono necessari un account di archiviazione di Azure, con archivio BLOB, e un contenitore di documenti JSON. Se non si ha familiarità con queste attività, vedere la sezione "Configurare il servizio BLOB di Azure e caricare i dati di esempio" in [Guida introduttiva: creare una pipeline di ricerca cognitiva con competenze e dati di esempio](cognitive-search-quickstart-blob.md#set-up-azure-blob-service-and-load-sample-data).

### <a name="2---start-import-data-wizard"></a>2 - Avviare la procedura guidata Importa dati

È possibile [avviare la procedura guidata](search-import-data-portal.md) dalla barra dei comandi nella pagina del servizio Ricerca di Azure oppure facendo clic su **Aggiungi Ricerca di Azure** nella sezione **Servizio BLOB** del riquadro di spostamento sinistro del proprio account di archiviazione.

### <a name="3---set-the-data-source"></a>3 - Impostare l'origine dati

Nella pagina **Nuova origine dati** l'origine deve essere **Archivio BLOB di Azure**, con le specifiche seguenti:

+ **Dati da estrarre** deve essere impostato su *Contenuto e metadati*. La selezione di questa opzione consente alla procedura guidata di dedurre uno schema di indice e di eseguire il mapping dei campi per l'importazione.
   
+ **Modalità di analisi** deve essere impostato su *JSON* o *Matrice JSON*. 

  *JSON* articola ogni BLOB come singolo documento di ricerca, che viene visualizzato come elemento indipendente nei risultati della ricerca. 

  *Matrice JSON* vale per i BLOB composti da più elementi, in cui ogni elemento deve essere articolato come un documento di ricerca autonomo e indipendente. Se in presenza di BLOB complessi non si seleziona *Matrice JSON*, l'intero BLOB viene inserito come un solo documento.
   
+ In **Contenitore di archiviazione** occorre specificare l'account di archiviazione e il contenitore oppure una stringa di connessione che si risolve nel contenitore. È possibile ottenere le stringhe di connessione nella pagina del portale del servizio BLOB.

   ![Definizione dell'origine dati BLOB](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 - Saltare la pagina "Aggiungi ricerca cognitiva" nella procedura guidata

L'aggiunta di funzionalità di ricerca cognitiva non è necessaria per l'importazione di documenti JSON. A meno che non si abbia la specifica esigenza di [includere trasformazioni e API Servizi cognitivi](cognitive-search-concept-intro.md) alla pipeline di indicizzazione, saltare questo passaggio.

Per saltare il passaggio, fare clic sulla pagina successiva **Personalizza indice di destinazione**.

### <a name="5---set-index-attributes"></a>5 - Impostare gli attributi dell'indice

Nella pagina **Indice** dovrebbe essere presente un elenco di campi con un tipo di dati e una serie di caselle di controllo per l'impostazione degli attributi di indice. La procedura guidata può generare un indice predefinito in base ai metadati e tramite il campionamento dei dati di origine. 

Le impostazioni predefinite producono spesso una soluzione pratica: la selezione di un campo (cast come stringa) che funga da chiave o ID documento per identificare ogni documento in modo univoco, oltre a campi che rappresentano buoni candidati per la ricerca full-text e sono recuperabili in un set di risultati. Per i BLOB, il campo `content` è il miglior candidato per il contenuto ricercabile.

È possibile accettare le impostazioni predefinite oppure rivedere la descrizione di [attributi di indice](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) e [analizzatori del linguaggio](https://docs.microsoft.com/rest/api/searchservice/language-support) per eseguire l'override o completare i valori iniziali. 

Dedicare qualche momento alla revisione delle selezioni. Con l'esecuzione della procedura guidata vengono create strutture dei dati fisiche e non è possibile eliminare questi campi senza eliminare e ricreare tutti gli oggetti.

   ![Definizione di indice BLOB](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 - Creare l'indicizzatore

Nella sua specifica completa, la procedura guidata crea tre oggetti distinti nel servizio di ricerca. Un oggetto origine dati e un oggetto indice vengono salvati come risorse denominate nel servizio Ricerca di Azure. L'ultimo passaggio crea un oggetto indicizzatore. La denominazione dell'indicizzatore ne consente l'esistenza come risorsa autonoma, che può essere pianificata e gestita in modo indipendente dagli oggetti indice e origine dati, creati nella stessa sequenza della procedura guidata.

Per chi non lo sapesse, un *indicizzatore* è una risorsa di Ricerca di Azure che esegue una ricerca per indicizzazione in un'origine dati esterna per individuare contenuto ricercabile. L'output della procedura guidata **Importa dati** è un indicizzatore che esegue una ricerca per indicizzazione nell'origine dati JSON, estrae il contenuto ricercabile e lo importa in un indice in Ricerca di Azure.

   ![Definizione di indicizzatore BLOB](media/search-howto-index-json/import-wizard-json-indexer.png)

Fare clic su **OK** per eseguire la procedura guidata e creare tutti gli oggetti. L'indicizzazione inizia immediatamente.

È possibile monitorare l'importazione dei dati nelle pagine del portale. Le notifiche di stato indicano lo stato dell'indicizzazione e il numero di documenti caricati. Al termine dell'indicizzazione, è possibile usare [Esplora ricerche](search-explorer.md) per eseguire query sull'indice.

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>Usare le API REST

L'indicizzazione dei BLOB JSON è simile alla normale estrazione dei documenti in un flusso di lavoro in tre fasi comune a tutti gli indicizzatori di Ricerca di Azure: creazione di un'origine dati, creazione di un indice, creazione di un indicizzatore.

Per l'indicizzazione JSON basata su codice è possibile usare l'API REST con le API per [indicizzatori](https://docs.microsoft.com/rest/api/searchservice/create-indexer), [origini dati](https://docs.microsoft.com/rest/api/searchservice/create-data-source) e [indici](https://docs.microsoft.com/rest/api/searchservice/create-index). A differenza della procedura guidata del portale, l'approccio basato su codice richiede la presenza di un indice pronto per accettare i documenti JSON quando si invia la richiesta **Crea indicizzatore**.

I BLOB JSON in Archiviazione BLOB di Azure consistono in genere in un singolo documento JSON o in una matrice JSON. L'indicizzatore di BLOB di Ricerca di Azure è in grado di analizzare entrambi i tipi di costruzione, a seconda dell'impostazione del parametro **parsingMode** nella richiesta.

| Documento JSON | parsingMode | DESCRIZIONE | Disponibilità |
|--------------|-------------|--------------|--------------|
| Un solo documento per BLOB | `json` | Analizza i BLOB JSON come un singolo blocco di testo. Ogni BLOB JSON diventa un singolo documento di Ricerca di Azure. | Disponibile a livello generale nelle API [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) e [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer). |
| Più documenti per BLOB | `jsonArray` | Analizza una matrice JSON nel BLOB, dove ogni elemento della matrice diventa un documento separato di Ricerca di Azure.  | Disponibile a livello generale nelle API [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) e [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer). |


### <a name="step-1-create-a-data-source"></a>Passaggio 1: Creare un'origine dati

Il primo passaggio consiste nel fornire le informazioni sulla connessione all'origine dati che vengono usate dall'indicizzatore. Il tipo di origine dati, qui specificato come `azureblob`, determina i comportamenti di estrazione dei dati che vengono richiamati dall'indicizzatore. Per l'indicizzazione di BLOB JSON, la definizione dell'origine dati è identica sia per le matrici che per i documenti JSON. 

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
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

L'indice archivia il contenuto ricercabile in Ricerca di Azure. Per creare un indice, fornire uno schema che specifichi i campi in un documento, gli attributi e altri costrutti che danno forma all'esperienza di ricerca. Se si crea un indice con gli stessi nomi di campo e gli stessi tipi di dati dell'origine, l'indicizzatore stabilirà la corrispondenza tra i campi di origine e i campi di destinazione, evitando all'utente di dover eseguire il mapping esplicito dei campi.

L'esempio seguente illustra una richiesta [Crea indice](https://docs.microsoft.com/rest/api/searchservice/create-index). L'indice avrà un campo `content` ricercabile in cui archiviare il testo estratto dai BLOB:   

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="step-3-configure-and-run-the-indexer"></a>Passaggio 3: Configurare ed eseguire l'indicizzatore

Fino a questo momento, le definizioni per l'origine dati e l'indice non sono state influenzate dal parametro parsingMode. Tuttavia, nel passaggio 3 relativo alla configurazione dell'indicizzatore, la procedura cambia a seconda di come si vuole analizzare e strutturare il contenuto dei BLOB JSON in un indice di Ricerca di Azure. Le scelte possibili sono `json` e `jsonArray`:

+ Impostare **parsingMode** su `json` per indicizzare ogni BLOB come singolo documento.

+ Impostare **parsingMode** su `jsonArray` se i BLOB sono costituiti da matrici JSON e ogni elemento della matrice deve diventare un documento separato in Ricerca di Azure. Un documento può essere considerato come un singolo elemento nei risultati della ricerca. Se si vuole che ogni elemento nella matrice venga visualizzato come un elemento indipendente nei risultati della ricerca, usare l'opzione `jsonArray`.

Se si vuole, all'interno della definizione dell'indicizzatore è possibile usare i **mapping dei campi** per scegliere quali proprietà del documento JSON di origine vengono usate per popolare l'indice di ricerca di destinazione. Per le matrici JSON, se la matrice esiste come proprietà di livello inferiore, è possibile impostare un elemento radice dei documenti che indica il punto in cui è posizionata la matrice all'interno del BLOB.

> [!IMPORTANT]
> Quando si usa la modalità di analisi `json` o `jsonArray`, Ricerca di Azure presuppone che tutti i BLOB nell'origine dati siano di tipo JSON. Se è necessario supportare una combinazione di BLOB di tipo JSON e non JSON nella stessa origine dati, comunicare questa esigenza sul [sito UserVoice](https://feedback.azure.com/forums/263029-azure-search).


### <a name="how-to-parse-single-json-blobs"></a>Come analizzare i singoli BLOB JSON

Per impostazione predefinita, l' [indicizzatore BLOB di Ricerca di Azure](search-howto-indexing-azure-blob-storage.md) analizza i BLOB di tipo JSON come blocco singolo di testo. È spesso possibile che si voglia preservare la struttura dei documenti JSON. Si supponga, ad esempio, che in Archiviazione BLOB di Azure sia presente il documento JSON seguente:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

L'indicizzatore BLOB analizza il documento JSON in un singolo documento di Ricerca di Azure. Per caricare un indice, l'indicizzatore stabilisce una corrispondenza tra i campi "text", "datePublished" e "tags" dell'origine e i campi di indice di destinazione tipizzati con lo stesso nome.

La configurazione viene specificata nel corpo di un'operazione dell'indicizzatore. Tenere presente che l'oggetto origine dati, definito in precedenza, specifica le informazioni relative alla connessione e al tipo dell'origine dati. L'indice di destinazione deve inoltre essere definito come contenitore vuoto nel servizio. I campi "schedule" e "parameters" sono facoltativi, ma se vengono omessi, l'indicizzatore viene eseguito immediatamente in modalità di analisi `json`.

Una richiesta completamente specificata può avere l'aspetto seguente:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
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

### <a name="how-to-parse-json-arrays"></a>Come analizzare le matrici JSON

In alternativa, è possibile scegliere la funzionalità per le matrici JSON. Questa funzionalità è utile quando i BLOB contengono una *matrice di oggetti JSON* e si vuole che ogni elemento della matrice diventi un documento separato di Ricerca di Azure. Ad esempio, dato il BLOB JSON seguente, è possibile popolare l'indice di Ricerca di Azure con tre documenti separati, ognuno con i campi "id" e "text".  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

Per una matrice JSON, la definizione di indicizzatore dovrebbe avere un aspetto simile all'esempio seguente. Si noti che il parametro parsingMode specifica il parser `jsonArray`. La specifica del parser corretto e l'input dei dati corretti sono gli unici due requisiti specifici della matrice per l'indicizzazione dei BLOB JSON.

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Anche in questo caso il mapping dei campi può essere omesso. Dato un indice con i campi "id" e "text" con nome identico, l'indicizzatore di BLOB è in grado di dedurre il mapping corretto senza un elenco di mapping dei campi esplicito.

<a name="nested-json-arrays"></a>

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

### <a name="using-field-mappings-to-build-search-documents"></a>Uso dei mapping dei campi per creare documenti di ricerca

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

I nomi dei campi di origine nei mapping vengono specificati mediante la notazione di tipo [Puntatore JSON](https://tools.ietf.org/html/rfc6901) . Iniziare con una barra per fare riferimento alla radice del documento JSON, quindi passare alla proprietà desiderata, a un livello di annidamento arbitrario, usando un percorso separato da barre.

È anche possibile fare riferimento a singoli elementi della matrice usando un indice a base zero. Ad esempio, per selezionare il primo elemento della matrice "tags" dall'esempio precedente, usare un mapping dei campi analogo al seguente:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Se un nome del campo di origine in un percorso di mapping dei campi fa riferimento a una proprietà inesistente nel file JSON, il mapping verrà ignorato senza errori. Ciò consente di supportare documenti con schemi diversi, ovvero un caso di utilizzo comune. Poiché non è disponibile alcuna convalida, sarà necessario evitare accuratamente gli errori di ortografia nella specifica dei mapping dei campi.
>
>

### <a name="rest-example-indexer-request-with-field-mappings"></a>Esempio REST: Richiesta dell'indicizzatore con mapping dei campi

L'esempio seguente mostra un payload di indicizzatore completo con mapping dei campi:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
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

<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>Usare .NET SDK

.NET SDK è del tutto equivalente all'API REST. È consigliabile rivedere la sezione relativa dall'API REST per apprenderne i concetti, il flusso di lavoro e i requisiti. Consultare quindi la seguente documentazione di riferimento sull'API .NET per implementare un indicizzatore JSON nel codice gestito.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

## <a name="see-also"></a>Vedere anche 

+ [Indicizzatori in Ricerca di Azure](search-indexer-overview.md)
+ [Indicizzazione di Archiviazione BLOB di Azure con Ricerca di Azure](search-howto-index-json-blobs.md)
+ [Indicizzazione di BLOB CSV con l'indicizzatore di BLOB di Ricerca di Azure](search-howto-index-csv-blobs.md)
+ [Esercitazione: Cercare dati semistrutturati in Archiviazione BLOB di Azure](search-semi-structured-data.md)
