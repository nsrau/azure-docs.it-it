---
title: Introduzione all'archivio conoscenze (anteprima)
titleSuffix: Azure Cognitive Search
description: Inviare documenti arricchiti ad archiviazione di Azure, in cui è possibile visualizzare, modificare la forma e utilizzare documenti arricchiti in Azure ricerca cognitiva e in altre applicazioni. Questa funzionalità è disponibile in anteprima pubblica.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 2cde70579e61d1911008f7c8126ad7bda132eac1
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563497"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Introduzione agli archivi conoscenze in Ricerca cognitiva di Azure

> [!IMPORTANT] 
> L'archivio conoscenze è attualmente disponibile in anteprima pubblica. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Queste funzionalità di anteprima vengono fornite dall'[API REST versione 2019-05-06-Preview](search-api-preview.md). Il supporto del portale è attualmente limitato e non è disponibile alcun supporto per .NET SDK.

L'archivio delle informazioni è una funzionalità di Azure ricerca cognitiva che rende permanente l'output di una [pipeline di arricchimento di intelligenza artificiale](cognitive-search-concept-intro.md) per l'analisi indipendente o l'elaborazione downstream. Un *documento arricchito* è l'output di una pipeline, creato da contenuto che è stato estratto, strutturato e analizzato tramite processi di intelligenza artificiale. In una pipeline di intelligenza artificiale standard, i documenti arricchiti sono temporanei, ovvero vengono usati solo durante l'indicizzazione per poi essere rimossi. Con l'archivio conoscenze, i documenti arricchiti vengono conservati. 

Se in passato sono state usate competenze cognitive, si sa già che *skillsets* sposta un documento attraverso una sequenza di arricchimenti. Il risultato può essere rappresentato da un indice di ricerca o (novità in questa anteprima) da proiezioni in un archivio conoscenze. I due output, l'indice di ricerca e l'archivio delle informazioni, sono prodotti della stessa pipeline. derivato dagli stessi input, ma risultante nell'output strutturato, archiviato e usato in modi molto diversi.

Fisicamente, un archivio conoscenze è una [risorsa di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview), ad esempio una risorsa di archiviazione tabelle di Azure, di archiviazione BLOB di Azure o entrambe. Qualsiasi strumento o processo in grado di connettersi ad archiviazione di Azure può usare il contenuto di un archivio conoscenze.

![Archivio conoscenze nel diagramma della pipeline](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Archivio conoscenze nel diagramma della pipeline")

## <a name="benefits-of-knowledge-store"></a>Vantaggi del knowledge store

Un archivio informazioni fornisce la struttura, il contesto e i contenuti effettivi ottenuti da file di dati non strutturati e semistrutturati, come i BLOB, i file di immagine sottoposti ad analisi o persino i dati strutturati, rimodellati in nuovi moduli. In una [procedura dettagliata](knowledge-store-howto.md)è possibile vedere prima di tutto come un documento JSON denso viene partizionato in sottostrutture, ricostituito in nuove strutture e reso disponibile per i processi downstream come Machine Learning e carichi di lavoro Data Science.

Sebbene sia utile vedere cosa può produrre una pipeline di arricchimento di intelligenza artificiale, il potenziale reale di un archivio informazioni è la possibilità di modificare la forma dei dati. È possibile iniziare con un set di competenze di base e quindi iterarlo per aggiungere livelli crescenti di struttura, che è possibile combinare in nuove strutture, utilizzabili in altre app oltre a Ricerca cognitiva di Azure.

Di seguito vengono elencati i vantaggi del knowledge store:

+ Utilizzare documenti arricchiti in [strumenti di analisi e creazione report](#tools-and-apps) diversi dalla ricerca. Power BI con Power Query è una scelta interessante, ma qualsiasi strumento o app in grado di connettersi ad archiviazione di Azure può effettuare il pull da un archivio informazioni creato dall'utente.

+ Affinare una pipeline di indicizzazione basata su intelligenza artificiale mentre si eseguono il debug delle fasi e delle definizioni dei set di competenze. Un knowledge store mostra il prodotto di una definizione di set di competenze in una pipeline di indicizzazione basata su intelligenza artificiale. È possibile usare questi risultati per progettare un set di competenze migliori, perché è possibile vedere esattamente come sono gli arricchimenti. È possibile usare [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) in archiviazione di Azure per visualizzare il contenuto di un archivio informazioni.

+ Modellare i dati in nuove forme. Il rimodellamento è codificato in set di competenze, ma il punto è che un set di competenze può ora fornire questa funzionalità. La [competenza Shaper](cognitive-search-skill-shaper.md) in Ricerca cognitiva di Azure è stata estesa per supportare questa attività. Il rimodellamento consente di definire una proiezione che si allinea con l'uso previsto dei dati, preservando al tempo stesso le relazioni.

> [!Note]
> Nuove funzionalità di intelligenza artificiale e competenze cognitive? Ricerca cognitiva di Azure si integra con le funzionalità Visione artificiale e Lingua di Servizi cognitivi per estrarre e arricchire i dati di origine tramite il Riconoscimento ottico dei caratteri (OCR) su file di immagine, riconoscimento di entità ed estrazione di frasi chiave da file di testo e altro ancora. Per altre informazioni, vedere [Arricchimento tramite intelligenza artificiale in Ricerca cognitiva di Azure](cognitive-search-concept-intro.md).

## <a name="physical-storage"></a>Archiviazione fisica

L'espressione fisica di un archivio informazioni viene articolata tramite l'elemento `projections` di una definizione di `knowledgeStore` in un Skillt. La proiezione definisce una struttura dell'output in modo che corrisponda all'utilizzo previsto.

Le proiezioni possono essere articolate come tabelle, oggetti o file.

```json
"knowledgeStore": { 
    "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
    "projections": [ 
        { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        },
                { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        }
```

Il tipo di proiezione specificato in questa struttura determina il tipo di archiviazione utilizzato dall'archivio delle informazioni.

+ L'archiviazione tabelle viene utilizzata quando si definisce `tables`. Definire una proiezione di tabella quando è necessario disporre di strutture di report tabulari per gli input di strumenti analitici o l'esportazione come frame di dati in altri archivi dati. È possibile specificare più `tables` per ottenere un subset o una sezione incrociata di documenti arricchiti. All'interno dello stesso gruppo di proiezione, le relazioni tra tabelle vengono conservate in modo che sia possibile utilizzarle tutte.

+ L'archiviazione BLOB viene usata quando si definisce `objects` o `files`. La rappresentazione fisica di un `object` è una struttura JSON gerarchica che rappresenta un documento arricchito. Un `file` è un'immagine estratta da un documento, trasferita intatta nell'archivio BLOB.

Un singolo oggetto di proiezione contiene un set di `tables`, `objects`, `files`e per molti scenari, la creazione di una proiezione potrebbe essere sufficiente. 

Tuttavia, è possibile creare più set di `table`-`object`-proiezioni di `file` e questa operazione può essere eseguita se si desiderano relazioni tra dati differenti. All'interno di un set, i dati sono correlati, supponendo che tali relazioni esistano e possano essere rilevate. Se si creano set aggiuntivi, i documenti in ogni gruppo non sono mai correlati. Un esempio di utilizzo di più gruppi di proiezione potrebbe essere se si desidera che gli stessi dati proiettati per l'utilizzo con il sistema online e che debbano essere rappresentati in modo specifico, si desidera che gli stessi dati proiettati vengano utilizzati in una pipeline di data science rappresentata in modo diverso.

## <a name="requirements"></a>Requisiti 

[Archiviazione di Azure](https://docs.microsoft.com/azure/storage/) è obbligatoria. Fornisce l'archiviazione fisica. È possibile usare l'archiviazione BLOB, l'archiviazione tabelle o entrambi. L'archiviazione BLOB viene usata per documenti arricchiti intatti, in genere quando l'output passa ai processi downstream. L'archiviazione tabelle è destinata a sezioni di documenti arricchiti, comunemente utilizzati per l'analisi e la creazione di report.

Il livello di [competenze](cognitive-search-working-with-skillsets.md) è obbligatorio. Contiene la definizione di `knowledgeStore` e determina la struttura e la composizione di un documento arricchito. Non è possibile creare un archivio informazioni utilizzando un oggetto con competenze vuote. È necessario disporre di almeno una competenza in un skillt.

L' [indicizzatore](search-indexer-overview.md) è obbligatorio. Un indicizzatore viene richiamato da un indicizzatore che guida l'esecuzione. Gli indicizzatori sono dotati di un proprio set di requisiti e attributi. Molti di questi attributi hanno un impatto diretto su un archivio informazioni:

+ Gli indicizzatori richiedono un' [origine dati di Azure supportata](search-indexer-overview.md#supported-data-sources) (la pipeline che in definitiva crea l'archivio informazioni inizia con il pull dei dati da un'origine supportata in Azure). 

+ Gli indicizzatori richiedono un indice di ricerca. Per un indicizzatore è necessario fornire uno schema di indice, anche se non si prevede di usarlo. Un indice minimo ha un campo stringa, designato come chiave.

+ Gli indicizzatori forniscono mapping di campi facoltativi, usati per l'alias di un campo di origine in un campo di destinazione. Se è necessario modificare un mapping di campi predefinito (per usare un nome o un tipo diverso), è possibile creare un [mapping dei campi](search-indexer-field-mappings.md) all'interno di un indicizzatore. Per l'output dell'archivio delle informazioni, la destinazione può essere un campo in un oggetto BLOB o in una tabella.

+ Gli indicizzatori hanno pianificazioni e altre proprietà, ad esempio i meccanismi di rilevamento delle modifiche fornite da varie origini dati, possono anche essere applicati a un archivio delle informazioni. Ad esempio, è possibile [pianificare](search-howto-schedule-indexers.md) l'arricchimento a intervalli regolari per aggiornare il contenuto. 

## <a name="how-to-create-a-knowledge-store"></a>Come creare un archivio informazioni

Per creare un archivio informazioni, usare il portale o l'API REST di anteprima (`api-version=2019-05-06-Preview`).

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

L' **importazione guidata dati** include opzioni per la creazione di un archivio informazioni. Per l'esplorazione iniziale, [creare il primo archivio informazioni in quattro passaggi](knowledge-store-connect-power-bi.md).

1. Selezionare un'origine dati supportata.

1. Specificare l'arricchimento: alleghi una risorsa, selezionare competenze e specificare un archivio informazioni. 

1. Creare uno schema di indice. La procedura guidata lo richiede e può dedurre uno per l'utente.

1. Eseguire la procedura guidata. L'estrazione, l'arricchimento e l'archiviazione si verificano nell'ultimo passaggio.

### <a name="use-create-skillset-and-the-preview-rest-api"></a>Usare create Skills e l'API REST di anteprima

Un `knowledgeStore` viene definito all'interno di un oggetto [Skills](cognitive-search-working-with-skillsets.md), che a sua volta viene richiamato da un [indicizzatore](search-indexer-overview.md). Durante l'arricchimento, Azure ricerca cognitiva crea uno spazio nell'account di archiviazione di Azure e proietta i documenti arricchiti come BLOB o in tabelle, a seconda della configurazione.

Attualmente, l'API REST di anteprima è l'unico meccanismo mediante il quale è possibile creare un archivio informazioni a livello di codice. Un modo semplice per esplorare è [creare il primo archivio informazioni usando l'API REST e l'API REST](knowledge-store-create-rest.md).

Il contenuto di riferimento per questa funzionalità di anteprima si trova nella sezione informazioni di [riferimento sulle API](#kstore-rest-api) di questo articolo. 

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>Come connettersi con strumenti e app

Una volta che gli arricchimenti sono presenti nell'archiviazione, è possibile usare qualsiasi strumento o tecnologia che si connette ad Archiviazione BLOB di Azure o da Archiviazione tabelle di Azure per esplorare, analizzare o utilizzare i contenuti. È possibile iniziare con l'elenco seguente:

+ [Storage Explorer](knowledge-store-view-storage-explorer.md) per visualizzare la struttura e il contenuto dei documenti arricchiti. Considerare questo strumento come lo strumento di base per visualizzare i contenuti del knowledge store.

+ [Power bi](knowledge-store-connect-power-bi.md) per la creazione di report e l'analisi. 

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) per ulteriori elaborazioni.

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>Informazioni di riferimento sulle API

Questa sezione è una versione del documento di riferimento [create Skills (API REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset) , modificata per includere una definizione di `knowledgeStore`. 

### <a name="example---knowledgestore-embedded-in-a-skillset"></a>Esempio: knowledgeStore incorporato in un Skillt

Nell'esempio seguente viene illustrato `knowledgeStore` nella parte inferiore di una definizione di Skills. 

* Utilizzare **post** o **put** per formulare la richiesta.
* Usare la versione `api-version=2019-05-06-Preview` dell'API REST per accedere alla funzionalità dell'archivio informazioni. 

```http
POST https://[servicename].search.windows.net/skillsets?api-version=2019-05-06-Preview
api-key: [admin key]
Content-Type: application/json
```

Il corpo della richiesta è un documento JSON che definisce un insieme di competenze, che include `knowledgeStore`.

```json
{
  "name": "my-skillset-name",
  "description": "Extract organization entities and generate a positive-negative sentiment score from each document.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
  ],
  "cognitiveServices": 
    {
    "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
    "description": "mycogsvcs resource in West US 2",
    "key": "<YOUR-COGNITIVE-SERVICES-KEY>"
    },
    "knowledgeStore": { 
        "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
        "projections": [ 
            { 
                "tables": [  
                { "tableName": "Organizations", "generatedKeyName": "OrganizationId", "source": "/document/organizations*"}, 
                { "tableName": "Sentiment", "generatedKeyName": "SentimentId", "source": "/document/mySentiment"}
                ], 
                "objects": [ ], 
                "files": [  ]       
            }    
        ]     
    } 
}
```

### <a name="request-body-syntax"></a>Sintassi del corpo della richiesta  

Il codice JSON seguente specifica un `knowledgeStore`, che fa parte di un [`skillset`](https://docs.microsoft.com/rest/api/searchservice/create-skillset), che viene richiamato da un `indexer` (non illustrato). Se si ha già familiarità con l'arricchimento di intelligenza artificiale, un insieme di competenze determina la composizione di un documento arricchito. Un set di competenze deve contenere almeno una competenza, molto probabilmente una competenza Shaper se si stanno modulando strutture di dati.

La sintassi per la strutturazione del payload della richiesta è la seguente:

```json
{   
    "name" : "Required for POST, optional for PUT requests which sets the name on the URI",  
    "description" : "Optional. Anything you want, or null",  
    "skills" : "Required. An array of skills. Each skill has an odata.type, name, input and output parameters",
    "cognitiveServices": "A key to Cognitive Services, used for billing.",
    "knowledgeStore": { 
        "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
        "projections": [ 
            { 
                "tables": [ 
                    { "tableName": "<NAME>", "generatedKeyName": "<FIELD-NAME>", "source": "<DOCUMENT-PATH>" },
                    { "tableName": "<NAME>", "generatedKeyName": "<FIELD-NAME>", "source": "<DOCUMENT-PATH>" },
                    . . .
                ], 
                "objects": [ 
                    {
                    "storageContainer": "<BLOB-CONTAINER-NAME>", 
                    "source": "<DOCUMENT-PATH>", 
                    }
                ], 
                "files": [ 
                    {
                    "storageContainer": "<BLOB-CONTAINER-NAME>",
                    "source": "/document/normalized_images/*"
                    }
                ]  
            },
            {
                "tables": [ ],
                "objects": [ ],
                "files":  [ ]
            }  
        ]     
    } 
}
```

Un `knowledgeStore` dispone di due proprietà: una `storageConnectionString` a un account di archiviazione di Azure e `projections` che definisce l'archiviazione fisica. È possibile usare qualsiasi account di archiviazione, ma è conveniente usare i servizi nella stessa area.

Una raccolta di `projections` contiene oggetti di proiezione. Ogni oggetto di proiezione deve avere `tables`, `objects`, `files` (uno di ciascuno), che sono specificati o null. La sintassi precedente mostra due oggetti, uno completamente specificato e l'altro completamente null. All'interno di un oggetto Projection, una volta espressa nell'archivio, verranno mantenute tutte le relazioni tra i dati, se rilevate. 

Creare il numero di oggetti proiezione necessari per supportare l'isolamento e scenari specifici, ad esempio le strutture di dati usate per l'esplorazione, rispetto a quelle necessarie in un carico di lavoro data science. È possibile ottenere l'isolamento e la personalizzazione per scenari specifici impostando `source` e `storageContainer` o `table` a valori diversi all'interno di un oggetto. Per ulteriori informazioni ed esempi, vedere [utilizzo delle proiezioni in un archivio informazioni](knowledge-store-projection-overview.md).

|Proprietà      | Si applica a | Description|  
|--------------|------------|------------|  
|`storageConnectionString`| `knowledgeStore` | Obbligatorio. In questo formato: `DefaultEndpointsProtocol=https;AccountName=<ACCOUNT-NAME>;AccountKey=<ACCOUNT-KEY>;EndpointSuffix=core.windows.net`|  
|`projections`| `knowledgeStore` | Obbligatorio. Raccolta di oggetti proprietà costituita da `tables`, `objects``files` e dalle rispettive proprietà. Le proiezioni inutilizzate possono essere impostate su null.|  
|`source`| Tutte le proiezioni| Percorso del nodo della struttura ad albero di arricchimento che è la radice della proiezione. Questo nodo è l'output di una qualsiasi delle competenze del. I percorsi iniziano con `/document/`, che rappresenta il documento arricchito, ma possono essere estesi per `/document/content/` o ai nodi all'interno della struttura ad albero del documento. Esempi: `/document/countries/*` (tutti i paesi) o `/document/countries/*/states/*` (tutti gli stati in tutti i paesi). Per ulteriori informazioni sui percorsi dei documenti, vedere [concetti e composizione di competenze](cognitive-search-working-with-skillsets.md).|
|`tableName`| `tables`| Tabella da creare nell'archivio tabelle di Azure. |
|`storageContainer`| `objects`, `files`| Nome di un contenitore da creare nell'archivio BLOB di Azure. |
|`generatedKeyName`| `tables`| Colonna creata nella tabella che identifica in modo univoco un documento. La pipeline di arricchimento popola questa colonna con i valori generati.|


### <a name="response"></a>Risposta  

 In caso di richiesta eseguita correttamente, viene visualizzato il codice di stato "201 - Creato". Per impostazione predefinita, il corpo della risposta contiene il codice JSON per la definizione del set di competenze creata. Ricordare che l'archivio informazioni non viene creato fino a quando non si richiama un indicizzatore che fa riferimento a questo.

## <a name="next-steps"></a>Passaggi successivi

L'Archivio conoscenze offre la persistenza dei documenti arricchiti, utile durante la progettazione di un set di competenze o la creazione di nuove strutture e contenuto per l'utilizzo da parte di qualsiasi applicazione client in grado di accedere a un account di archiviazione di Azure.

L'approccio più semplice per la creazione di documenti arricchiti è [tramite il portale](knowledge-store-create-portal.md), ma è anche possibile usare l'API REST e l'API REST, che risulta più utile se si vuole approfondire la creazione e il riferimento agli oggetti.

> [!div class="nextstepaction"]
> [Creazione di un archivio informazioni con i post e REST](knowledge-store-create-rest.md)
