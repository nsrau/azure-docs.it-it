---
title: Indicizzare i BLOB JSON da un indicizzatore di BLOB di Azure per la ricerca full-text - Ricerca di Azure
description: Ricercare per indicizzazione i BLOB JSON di Azure per il contenuto di testo usando l'indicizzatore di BLOB di Ricerca di Azure. Gli indicizzatori automatizzano l'inserimento di dati per le origini dati selezionate, ad esempio l'archiviazione BLOB di Azure.
ms.date: 05/02/2019
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 5b04cabe734b97436421595dbb0ab7584efd4911
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024950"
---
# <a name="how-to-index-json-blobs-using-azure-search-blob-indexer"></a>Come indicizzare i BLOB JSON con l'indicizzatore di Blob di ricerca di Azure
Questo articolo illustra come configurare un blob di ricerca di Azure [indicizzatore](search-indexer-overview.md) per estrarre contenuto strutturato dai documenti JSON in archiviazione Blob di Azure e rendere disponibili per la ricerca in ricerca di Azure. Questo flusso di lavoro viene creato un indice di ricerca di Azure e lo carica con il testo esistente estratto dai BLOB JSON. 

Per indicizzare il contenuto JSON è possibile usare il [portale](#json-indexer-portal), le [API REST](#json-indexer-rest) o [.NET SDK](#json-indexer-dotnet). Comuni a tutti gli approcci sono che si trovano i documenti JSON in un contenitore blob in un account di archiviazione di Azure. Per istruzioni su come eseguire il push di documenti JSON da altre piattaforme non Azure, vedere [Indicizzazione dei dati esterni per le query in Ricerca di Azure](search-what-is-data-import.md).

I BLOB JSON in archiviazione Blob di Azure sono in genere un singolo documento JSON o una raccolta di entità JSON. Per le raccolte JSON, il blob potrebbe avere un **matrice** degli elementi JSON ben formati. Anche i BLOB è stato composto da più entità JSON singole separate da un carattere di nuova riga. L'indicizzatore di blob in ricerca di Azure consente di analizzare tali costruzione, a seconda del modo in cui impostato il **parsingMode** parametro nella richiesta.

JSON di tutte le modalità di analisi (`json`, `jsonArray`, `jsonLines`) sono ora disponibili a livello generale. 

> [!NOTE]
> Seguire le indicazioni di configurazione dell'indicizzatore nelle [indicizzazione di uno-a-molti](search-howto-index-one-to-many-blobs.md) per visualizzare più documenti di ricerca da un blob di Azure.

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Usare il portale

Il metodo più semplice di indicizzazione dei documenti JSON consiste nell'usare una procedura guidata nel [portale di Azure](https://portal.azure.com/). Analizzando i metadati nel contenitore BLOB di Azure, la procedura guidata [**Importa dati**](search-import-data-portal.md) può creare un indice predefinito, eseguire il mapping dei campi di origine ai campi di indice di destinazione e caricare l'indice con una sola operazione. A seconda delle dimensioni e della complessità dei dati di origine, si può ottenere un indice di ricerca full-text operativo in pochi minuti.

È consigliabile usare la stessa sottoscrizione di Azure per ricerca di Azure e archiviazione di Azure, preferibilmente nella stessa area.

### <a name="1---prepare-source-data"></a>1 - Preparare i dati di origine

1. [Accedere al portale di Azure](https://portal.azure.com/).

1. [Creare un contenitore Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) per contenere i dati. Il livello di accesso pubblico può essere impostato su uno qualsiasi dei relativi valori validi.

È necessario il nome di account di archiviazione, nome del contenitore e una chiave di accesso per recuperare i dati nel **importare dati** procedura guidata.

### <a name="2---start-import-data-wizard"></a>2 - Avviare la procedura guidata Importa dati

Nella pagina Panoramica del servizio di ricerca di Azure, è possibile [avviare la procedura guidata](search-import-data-portal.md) dalla barra dei comandi o facendo clic **Aggiungi ricerca di Azure** nel **servizio Blob** sezione il account di archiviazione a sinistra del riquadro di spostamento.

   ![Comando Importa dati nel portale](./media/search-import-data-portal/import-data-cmd2.png "Avviare la procedura guidata Importa dati")

### <a name="3---set-the-data-source"></a>3 - Impostare l'origine dati

Nella pagina **Nuova origine dati** l'origine deve essere **Archivio BLOB di Azure**, con le specifiche seguenti:

+ **Dati da estrarre** deve essere impostato su *Contenuto e metadati*. La selezione di questa opzione consente alla procedura guidata di dedurre uno schema di indice e di eseguire il mapping dei campi per l'importazione.
   
+ **Modalità di analisi** deve essere impostata su *JSON*, *matrice JSON* oppure *righe JSON*. 

  *JSON* articola ogni BLOB come singolo documento di ricerca, che viene visualizzato come elemento indipendente nei risultati della ricerca. 

  *Matrice JSON* è per i blob che contengono dati JSON ben formati, il codice JSON ben formato corrisponde a una matrice di oggetti o ha una proprietà che è una matrice di oggetti e si vuole che ogni elemento articolata come autonomo, documento di ricerca indipendente. Se in presenza di BLOB complessi non si seleziona *Matrice JSON*, l'intero BLOB viene inserito come un solo documento.

  *Le righe JSON* è per i BLOB è costituita da più entità JSON separate da una nuova riga, in cui si desidera ogni entità articolata come un documento di ricerca indipendente autonomo. Se i BLOB sono complessi e non si sceglie *righe JSON* analisi-modalità, quindi l'intero blob vengono inseriti come un singolo documento.
   
+ In **Contenitore di archiviazione** occorre specificare l'account di archiviazione e il contenitore oppure una stringa di connessione che si risolve nel contenitore. È possibile ottenere le stringhe di connessione nella pagina del portale del servizio BLOB.

   ![Definizione dell'origine dati BLOB](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 - Saltare la pagina "Aggiungi ricerca cognitiva" nella procedura guidata

L'aggiunta di funzionalità di ricerca cognitiva non è necessaria per l'importazione di documenti JSON. A meno che non si abbia la specifica esigenza di [includere trasformazioni e API Servizi cognitivi](cognitive-search-concept-intro.md) alla pipeline di indicizzazione, saltare questo passaggio.

Per ignorare il passaggio, passare alla pagina successiva.

   ![Pulsante Pagina successiva per la ricerca cognitiva](media/search-get-started-portal/next-button-add-cog-search.png)

Da tale pagina è possibile passare direttamente alla personalizzazione dell'indice.

   ![Ignorare il passaggio delle competenze cognitive](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5 - Impostare gli attributi dell'indice

Nella pagina **Indice** dovrebbe essere presente un elenco di campi con un tipo di dati e una serie di caselle di controllo per l'impostazione degli attributi di indice. La procedura guidata può generare un elenco di campi in base a metadati e tramite il campionamento dei dati di origine. 

È possibile selezionare in massa attributi facendo clic sulla casella di controllo nella parte superiore di una colonna di attributo. Scegli **recuperabile** e **ricercabile** per ogni campo che deve essere restituito da un'app client e soggetti a elaborazione della ricerca full-text. Si noterà che i numeri interi non sono full-text o fuzzy ricercabili (i numeri vengono valutati come verbatim e sono spesso utili nei filtri).

Esaminare la descrizione della [attributi dell'indice](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) e [analizzatori di lingua](https://docs.microsoft.com/rest/api/searchservice/language-support) per altre informazioni. 

Dedicare qualche momento alla revisione delle selezioni. Con l'esecuzione della procedura guidata vengono create strutture dei dati fisiche e non è possibile eliminare questi campi senza eliminare e ricreare tutti gli oggetti.

   ![Definizione di indice BLOB](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 - Creare l'indicizzatore

Nella sua specifica completa, la procedura guidata crea tre oggetti distinti nel servizio di ricerca. Un oggetto origine dati e un oggetto indice vengono salvati come risorse denominate nel servizio Ricerca di Azure. L'ultimo passaggio crea un oggetto indicizzatore. La denominazione dell'indicizzatore ne consente l'esistenza come risorsa autonoma, che può essere pianificata e gestita in modo indipendente dagli oggetti indice e origine dati, creati nella stessa sequenza della procedura guidata.

Per chi non lo sapesse, un *indicizzatore* è una risorsa di Ricerca di Azure che esegue una ricerca per indicizzazione in un'origine dati esterna per individuare contenuto ricercabile. L'output della procedura guidata **Importa dati** è un indicizzatore che esegue una ricerca per indicizzazione nell'origine dati JSON, estrae il contenuto ricercabile e lo importa in un indice in Ricerca di Azure.

   ![Definizione di indicizzatore BLOB](media/search-howto-index-json/import-wizard-json-indexer.png)

Fare clic su **OK** per eseguire la procedura guidata e creare tutti gli oggetti. L'indicizzazione inizia immediatamente.

È possibile monitorare l'importazione dei dati nelle pagine del portale. Le notifiche di stato indicano lo stato dell'indicizzazione e il numero di documenti caricati. 

Al termine dell'indicizzazione, è possibile usare [Esplora ricerche](search-explorer.md) per eseguire query sull'indice.

> [!NOTE]
> Se non viene visualizzato i dati che previsti, si potrebbe essere necessario impostare altri attributi in altri campi. Eliminare l'indice e l'indicizzatore appena creato, quindi eseguire la procedura guidata nuovo, modificando le selezioni effettuate per gli attributi dell'indice nel passaggio 5. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>Usare le API REST

È possibile usare l'API REST per indicizzare i BLOB JSON, seguendo un flusso di lavoro in tre parti comune a tutti gli indicizzatori in ricerca di Azure: creare un'origine dati, creare un indice, creare un indicizzatore. Estrazione dei dati dall'archiviazione blob si verifica quando si invia la richiesta di creare un indicizzatore. Dopo aver completata questa richiesta, si avrà un indice sottoponibili a query. 

È possibile esaminare [il codice di esempio REST](#rest-example) alla fine di questa sezione che illustra come creare tutti e tre gli oggetti. In questa sezione sono contenuti inoltre dettagli sulle [modalità di analisi JSON](#parsing-modes), [singolo BLOB](#parsing-single-blobs), [matrici JSON](#parsing-arrays), e [annidati matrici](#nested-json-arrays).

Per l'indicizzazione basata su codice JSON, usare [Postman](search-fiddler.md) e l'API REST per creare questi oggetti:

+ [index](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Origine dati](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

Ordine delle operazioni è necessario creare e chiamare gli oggetti nell'ordine indicato. A differenza del flusso di lavoro del portale, un approccio di codice richiede un indice disponibile per accettare i documenti JSON inviati tramite il **creare un indicizzatore** richiesta.

I BLOB JSON in archiviazione Blob di Azure sono in genere un singolo documento JSON o un file JSON "matrice". L'indicizzatore di BLOB di Ricerca di Azure è in grado di analizzare entrambi i tipi di costruzione, a seconda dell'impostazione del parametro **parsingMode** nella richiesta.

| Documento JSON | parsingMode | DESCRIZIONE | Disponibilità |
|--------------|-------------|--------------|--------------|
| Un solo documento per BLOB | `json` | Analizza i BLOB JSON come un singolo blocco di testo. Ogni BLOB JSON diventa un singolo documento di Ricerca di Azure. | Disponibile a livello generale in entrambe [resto](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API e [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Più documenti per BLOB | `jsonArray` | Analizza una matrice JSON nel BLOB, dove ogni elemento della matrice diventa un documento separato di Ricerca di Azure.  | Disponibile a livello generale in entrambe [resto](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API e [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Più documenti per BLOB | `jsonLines` | Analizza un blob che contiene più entità JSON ("matrice") separate da un carattere di nuova riga, in cui ogni entità diventa un documento separato di ricerca di Azure. | Disponibile a livello generale in entrambe [resto](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API e [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |

### <a name="1---assemble-inputs-for-the-request"></a>1 - assemblare gli input per la richiesta

Per ogni richiesta, è necessario fornire il nome del servizio e chiave di amministrazione per ricerca di Azure (nell'intestazione POST) e il nome di account di archiviazione e la chiave per l'archiviazione blob. È possibile usare [Postman](search-fiddler.md) per inviare richieste HTTP in ricerca di Azure.

Copiare i quattro valori seguenti nel blocco note, in modo da poterli incollare in una richiesta:

+ Nome di servizio ricerca di Azure
+ Azure chiave di amministrazione di ricerca
+ Nome dell'account di archiviazione di Azure
+ Chiave dell'account di archiviazione di Azure

È possibile trovare questi valori nel portale:

1. Nelle pagine del portale per la ricerca di Azure, copiare l'URL del servizio di ricerca dalla pagina di panoramica.

2. Nel riquadro di spostamento a sinistra, fare clic su **chiavi** e quindi copiare la chiave primaria o secondaria (sono equivalenti).

3. Passare alle pagine del portale dell'account di archiviazione. Nel riquadro di spostamento a sinistra, sotto **le impostazioni**, fare clic su **chiavi di accesso**. Questa pagina fornisce il nome dell'account e la chiave. Nel blocco note, copiare il nome di account di archiviazione e una delle chiavi.

### <a name="2---create-a-data-source"></a>2 - creare un'origine dati

Questo passaggio vengono fornite informazioni di connessione origine dati usate dall'indicizzatore. L'origine dati è un oggetto specifico in ricerca di Azure che rende persistenti le informazioni di connessione. Tipo di origine dati `azureblob`, determina quali comportamenti di estrazione dei dati vengono richiamati dall'indicizzatore. 

Sostituire i valori validi per nome del servizio, chiave amministratore, account di archiviazione e i segnaposto di chiavi dell'account.

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="3---create-a-target-search-index"></a>3 - creare un indice di ricerca di destinazione 

Gli indicizzatori sono associati allo schema di un indice. Se si usa l'API, anziché il portale, preparare un indice in anticipo in modo poterlo specificare per l'operazione dell'indicizzatore.

L'indice archivia il contenuto ricercabile in Ricerca di Azure. Per creare un indice, fornire uno schema che specifichi i campi in un documento, gli attributi e altri costrutti che danno forma all'esperienza di ricerca. Se si crea un indice con gli stessi nomi di campo e gli stessi tipi di dati dell'origine, l'indicizzatore stabilirà la corrispondenza tra i campi di origine e i campi di destinazione, evitando all'utente di dover eseguire il mapping esplicito dei campi.

L'esempio seguente illustra una richiesta [Crea indice](https://docs.microsoft.com/rest/api/searchservice/create-index). L'indice avrà un campo `content` ricercabile in cui archiviare il testo estratto dai BLOB:   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="4---configure-and-run-the-indexer"></a>4: configurare ed eseguire l'indicizzatore

Come con un indice e un tipo di dati di origine e dell'indicizzatore è anche un oggetto denominato dell'oggetto che viene creato e riutilizzare in un servizio di ricerca di Azure. Una richiesta completamente specificata per creare un indicizzatore può apparire come segue:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Configurazione dell'indicizzatore è nel corpo della richiesta. È necessaria un'origine dati e un indice di destinazione vuoto che esiste già in ricerca di Azure. 

Pianificazione e i parametri sono facoltativi. Se vengono omessi, l'indicizzatore viene eseguito immediatamente usando `json` come la modalità di analisi.

Questo indicizzatore particolare non include i mapping dei campi. All'interno della definizione dell'indicizzatore, è possibile tralasciare **mapping dei campi** se le proprietà del documento JSON di origine corrispondono ai campi dell'indice di ricerca di destinazione. 


### <a name="rest-example"></a>Esempio REST

In questa sezione è riportato un riepilogo di tutte le richieste per la creazione di oggetti. Per una descrizione delle parti di componente, vedere le sezioni precedenti in questo articolo.

### <a name="data-source-request"></a>Richiesta di origine dati

Tutti gli indicizzatori richiedono un oggetto origine dati che fornisce informazioni sulla connessione ai dati esistenti. 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  


### <a name="index-request"></a>Richiesta di indice

Tutti gli indicizzatori richiedono un indice di destinazione che riceve i dati. Il corpo della richiesta definisce lo schema dell'indice, costituita da campi, attribuiti in modo da supportare i comportamenti desiderati in un indice ricercabile. Questo indice deve essere vuoto quando si esegue l'indicizzatore. 

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="indexer-request"></a>Richiesta dell'indicizzatore

Questa richiesta Mostra un indicizzatore specificato completamente. Include i mapping di campo, che sono stati omessi negli esempi precedenti. È importante ricordare che "schedule", "parameters", e "fieldMappings" sono facoltativi, purché vi sia una predefinite disponibili. L'omissione "pianificazione" fa in modo che l'indicizzatore per l'esecuzione immediata. L'omissione "parsingMode" fa in modo che l'indice da utilizzare il valore predefinito "json".

Creare l'indicizzatore in ricerca di Azure attiva l'importazione dei dati. Viene eseguito immediatamente e, successivamente in base a una pianificazione se è stato fornito uno.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

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

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>Modalità di analisi

I BLOB JSON possono presupporre che più moduli. Il **parsingMode** parametro nell'indicizzatore JSON determina come il contenuto di blob JSON è analizzare e strutturare un indice di ricerca di Azure:

| parsingMode | DESCRIZIONE |
|-------------|-------------|
| `json`  | Indicizzare ogni blob come singolo documento. Questa è la modalità predefinita. |
| `jsonArray` | Scegliere questa modalità se i BLOB sono costituiti da matrici JSON ed è necessario ogni elemento della matrice diventi un documento separato in ricerca di Azure. |
|`jsonLines` | Scegliere questa modalità se i BLOB sono costituiti da più entità JSON, che sono separate da una nuova riga, ed è necessario ogni entità diventi un documento separato in ricerca di Azure. |

Un documento può essere considerato come un singolo elemento nei risultati della ricerca. Se si desidera che ogni elemento della matrice visualizzato nei risultati della ricerca come un elemento indipendente, quindi usare il `jsonArray` o `jsonLines` opzione come appropriato.

Se si vuole, all'interno della definizione dell'indicizzatore è possibile usare i [mapping dei campi](search-indexer-field-mappings.md) per scegliere quali proprietà del documento JSON di origine vengono usate per popolare l'indice di ricerca di destinazione. Per `jsonArray` analisi della modalità, se la matrice esiste come proprietà di livello inferiore, è possibile impostare la radice di un documento che indica dove la matrice viene posizionata all'interno del blob.

> [!IMPORTANT]
> Quando si usa `json`, `jsonArray` o `jsonLines` modalità di analisi, ricerca di Azure presuppone che tutti i BLOB presenti nell'origine dati contengano codice JSON. Se è necessario supportare una combinazione di BLOB di tipo JSON e non JSON nella stessa origine dati, comunicare questa esigenza sul [sito UserVoice](https://feedback.azure.com/forums/263029-azure-search).


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>Analizzare i singoli BLOB JSON

Per impostazione predefinita, l' [indicizzatore BLOB di Ricerca di Azure](search-howto-indexing-azure-blob-storage.md) analizza i BLOB di tipo JSON come blocco singolo di testo. È spesso possibile che si voglia preservare la struttura dei documenti JSON. Si supponga, ad esempio, che in Archiviazione BLOB di Azure sia presente il documento JSON seguente:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

L'indicizzatore BLOB analizza il documento JSON in un singolo documento di Ricerca di Azure. Per caricare un indice, l'indicizzatore stabilisce una corrispondenza tra i campi "text", "datePublished" e "tags" dell'origine e i campi di indice di destinazione tipizzati con lo stesso nome.

Come già indicato, i mapping dei campi non sono obbligatori. Dato un indice con i campi "text", "datePublished" e "tag", l'indicizzatore di BLOB è in grado di dedurre il mapping corretto senza che sia definito un mapping di campi nella richiesta.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Analizzare le matrici JSON

In alternativa, è possibile utilizzare l'opzione di matrice JSON. Questa opzione è utile quando i BLOB contengono una *matrice di oggetti JSON ben formati*, e si vuole che ogni elemento diventi un documento separato di ricerca di Azure. Ad esempio, dato il BLOB JSON seguente, è possibile popolare l'indice di Ricerca di Azure con tre documenti separati, ognuno con i campi "id" e "text".  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

Per una matrice JSON, la definizione di indicizzatore dovrebbe avere un aspetto simile all'esempio seguente. Si noti che il parametro parsingMode specifica il parser `jsonArray`. Specifica il parser a destra e con i dati corretti input sono solo due requisiti specifici delle matrici per l'indicizzazione di BLOB JSON.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
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

## <a name="parse-nested-arrays"></a>Analizzare le matrici nidificate
Per JSON matrici con elementi nidificati, è possibile specificare un `documentRoot` per indicare una struttura a più livelli. Ad esempio, se i BLOB sono simili a questo:

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

## <a name="parse-blobs-separated-by-newlines"></a>Analizzare i BLOB separati da caratteri di nuova riga

Se il blob contiene più entità JSON separate da un carattere di nuova riga e si vuole che ogni elemento diventi un documento separato di ricerca di Azure, è possibile scegliere per l'opzione delle linee di JSON. Ad esempio, dato il blob seguente (dove sono disponibili tre diverse entità JSON), è possibile popolare l'indice di ricerca di Azure con 3 documenti separati, ognuno con i campi "id" e "text".

    { "id" : "1", "text" : "example 1" }
    { "id" : "2", "text" : "example 2" }
    { "id" : "3", "text" : "example 3" }

Per le righe JSON, la definizione dell'indicizzatore dovrebbe essere simile all'esempio seguente. Si noti che il parametro parsingMode specifica il parser `jsonLines`. 

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }

Anche in questo caso, si noti che i mapping dei campi può essere omesso, simile al `jsonArray` modalità di analisi.

## <a name="add-field-mappings"></a>Aggiungere i mapping dei campi

Quando i campi di origine e di destinazione non sono perfettamente allineati, è possibile definire una sezione di mapping dei campi nel corpo della richiesta per esplicitare le associazioni tra i campi.

Attualmente, ricerca di Azure non è possibile indicizzare documenti JSON arbitrari direttamente, perché supporta solo tipi di dati primitivi, matrici di stringhe e punti GeoJSON. È tuttavia possibile usare i **mapping dei campi** per selezionare parti del documento JSON e "portarle" nei campi di livello superiore del documento di ricerca. Per nozioni fondamentali sui mapping dei campi, vedere [Mapping dei campi negli indicizzatori di Ricerca di Azure](search-indexer-field-mappings.md).

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

## <a name="see-also"></a>Vedere anche 

+ [Indicizzatori in Ricerca di Azure](search-indexer-overview.md)
+ [Indicizzazione di Archiviazione BLOB di Azure con Ricerca di Azure](search-howto-index-json-blobs.md)
+ [Indicizzazione di BLOB CSV con l'indicizzatore di BLOB di Ricerca di Azure](search-howto-index-csv-blobs.md)
+ [Esercitazione: Cercare dati semistrutturati in Archiviazione BLOB di Azure](search-semi-structured-data.md)
