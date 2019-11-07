---
title: Introduzione a Knowledge Store (anteprima)
titleSuffix: Azure Cognitive Search
description: Inviare documenti arricchiti ad archiviazione di Azure, in cui è possibile visualizzare, modificare la forma e utilizzare documenti arricchiti in Azure ricerca cognitiva e in altre applicazioni. Questa funzionalità è disponibile in anteprima pubblica.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a1c6f2d869d8d7ad865005ebd319beac56bdbacd
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720088"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Introduzione agli archivi delle informazioni in Azure ricerca cognitiva

> [!IMPORTANT] 
> Archivio informazioni è attualmente disponibile in anteprima pubblica. La funzionalità di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). L' [API REST versione 2019-05-06-Preview](search-api-preview.md) fornisce funzionalità di anteprima. Il supporto del portale è attualmente limitato e non è disponibile alcun supporto per .NET SDK.

Archivio informazioni è una funzionalità di Azure ricerca cognitiva che rende permanente l'output di una [pipeline di arricchimento ai](cognitive-search-concept-intro.md) per analisi successive o altre elaborazioni downstream. Un *documento arricchito* è l'output di una pipeline, creato da contenuto che è stato estratto, strutturato e analizzato tramite i processi di intelligenza artificiale. In una pipeline di intelligenza artificiale standard i documenti arricchiti sono transitori, usati solo durante l'indicizzazione e quindi eliminati. Con archivio informazioni vengono conservati i documenti arricchiti. 

Se in passato sono state usate competenze cognitive con Azure ricerca cognitiva, si sa già che *skillsets* sposta un documento attraverso una sequenza di arricchimenti. Il risultato può essere un indice di ricerca o (nuovo in questa anteprima) proiezioni in un archivio informazioni. I due output, l'indice di ricerca e l'archivio informazioni condividono lo stesso contenuto, ma vengono archiviati e usati in modi molto diversi.

Fisicamente, un archivio informazioni è [archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview), l'archiviazione tabelle di Azure, l'archiviazione BLOB di Azure o entrambi. Qualsiasi strumento o processo in grado di connettersi ad archiviazione di Azure può usare il contenuto di un archivio conoscenze.

![Archivio informazioni nel diagramma pipeline](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Archivio informazioni nel diagramma pipeline")

Per usare il knowledge store, aggiungere un elemento `knowledgeStore` a un set di competenze che definisce le operazioni graduali in una pipeline di indicizzazione. Durante l'esecuzione, Azure ricerca cognitiva crea uno spazio nell'account di archiviazione di Azure e proietta i documenti arricchiti come BLOB o in tabelle, a seconda della configurazione.

## <a name="benefits-of-knowledge-store"></a>Vantaggi del knowledge store

Un knowledge store offre una struttura, un contesto e un contenuto reale, ricavato da file di dati non strutturati e semi-strutturati come BLOB, file di immagini che sono stati sottoposti ad analisi, o anche dati strutturati che vengono rimodellati in nuove forme. In una [procedura dettagliata](knowledge-store-howto.md)è possibile vedere in prima persona come partizionare un documento JSON denso in sottostrutture, ricostituito in nuove strutture e reso disponibile per i processi downstream come machine learning e Data Science carichi.

Sebbene sia utile vedere che cosa può produrre una pipeline di arricchimento dell'intelligenza artificiale, la reale potenza dell'archivio delle informazioni è la possibilità di modificare i dati. È possibile iniziare con un insieme di competenze di base e quindi scorrerlo per aggiungere livelli di struttura crescenti, che è quindi possibile combinare in nuove strutture, utilizzabili in altre app oltre ad Azure ricerca cognitiva.

Di seguito vengono elencati i vantaggi del knowledge store:

+ Utilizzare documenti arricchiti in [strumenti di analisi e creazione report](#tools-and-apps) diversi dalla ricerca. Power BI con Power Query è una scelta interessante, ma qualsiasi strumento o app che può connettersi all'archiviazione di Azure può eseguire il pull da un knowledge store creato.

+ Affinare una pipeline di indicizzazione basata su intelligenza artificiale mentre si eseguono il debug delle fasi e delle definizioni dei set di competenze. Un knowledge store mostra il prodotto di una definizione di set di competenze in una pipeline di indicizzazione basata su intelligenza artificiale. È possibile usare questi risultati per progettare un set di competenze migliori, perché è possibile vedere esattamente come sono gli arricchimenti. È possibile usare [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) nell'archiviazione di Azure per visualizzare il contenuto di un knowledge store.

+ Modellare i dati in nuove forme. Il rimodellamento è codificato in set di competenze, ma il punto è che un set di competenze può ora fornire questa funzionalità. La [capacità di shaper](cognitive-search-skill-shaper.md) in Azure ricerca cognitiva è stata estesa per supportare questa attività. Il rimodellamento consente di definire una proiezione che si allinea con l'uso previsto dei dati, preservando al tempo stesso le relazioni.

> [!Note]
> Nuove funzionalità di intelligenza artificiale e competenze cognitive? Azure ricerca cognitiva si integra con la visione e le funzionalità del linguaggio di servizi cognitivi per estrarre e arricchire i dati di origine usando il riconoscimento ottico dei caratteri (OCR) su file di immagine, il riconoscimento delle entità e l'estrazione di frasi chiave da file di testo e altro ancora Per altre informazioni, vedere [arricchimento di intelligenza artificiale in Azure ricerca cognitiva](cognitive-search-concept-intro.md).

## <a name="creating-a-knowledge-store"></a>Creazione di un archivio informazioni

Un archivio informazioni fa parte di un insieme di [competenze](cognitive-search-working-with-skillsets.md), che a sua volta fa parte di un [indicizzatore](search-indexer-overview.md). 

In questa versione di anteprima è possibile creare un archivio informazioni usando l'API REST e `api-version=2019-05-06-Preview`o tramite la procedura guidata **Importa dati** nel portale.

### <a name="json-representation-of-a-knowledge-store"></a>Rappresentazione JSON di un archivio informazioni

Il codice JSON seguente specifica un oggetto `knowledgeStore`, che fa parte di un set di competenze, che viene richiamato da un indicizzatore (non mostrato). Se si ha già familiarità con l'arricchimento di intelligenza artificiale, un insieme di competenze determina la creazione, l'organizzazione e la sostanza di ogni documento arricchito. Un skillt deve contenere almeno un'abilità, molto probabilmente un'abilità shaper se si stanno modulando strutture di dati.

Un `knowledgeStore` è costituito da una connessione e dalle proiezioni. 

+ La connessione è a un account di archiviazione nella stessa area del ricerca cognitiva di Azure. 

+ Le proiezioni sono coppie di oggetti Tables. `Tables` definire l'espressione fisica dei documenti arricchiti nell'archiviazione tabelle di Azure. `Objects` definire gli oggetti fisici nell'archivio BLOB di Azure.

```json
{
  "name": "my-new-skillset",
  "description": "Example showing knowledgeStore placement in a skillset.",
  "skills":
  [
    {
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document/content/phrases/*",
    "inputs": [
        {
        "name": "text",
        "source": "/document/content/phrases/*"
        },
        {
        "name": "sentiment",
        "source": "/document/content/phrases/*/sentiment"
        }
    ],
    "outputs": [
        {
        "name": "output",
        "targetName": "analyzedText"
        }
    ]
    },
  ],
  "cognitiveServices": 
    {
    "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
    "description": "mycogsvcs resource in West US 2",
    "key": "<your key goes here>"
    },
  "knowledgeStore": { 
    "storageConnectionString": "<your connection string goes here>", 
    "projections": [ 
        { 
            "tables": [  
            { "tableName": "Reviews", "generatedKeyName": "ReviewId", "source": "/document/Review" , "sourceContext": null, "inputs": []}, 
            { "tableName": "Sentences", "generatedKeyName": "SentenceId", "source": "/document/Review/Sentences/*", "sourceContext": null, "inputs": []}, 
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/Review/Sentences/*/KeyPhrases", "sourceContext": null, "inputs": []}, 
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/Review/Sentences/*/Entities/*" ,"sourceContext": null, "inputs": []} 

            ], 
            "objects": [ 
               
            ]      
        },
        { 
            "tables": [ 
            ], 
            "objects": [ 
                { 
                "storageContainer": "Reviews", 
                "format": "json", 
                "source": "/document/Review", 
                "key": "/document/Review/Id" 
                } 
            ]      
        }        
    ]     
    } 
}
```

### <a name="sources-of-data-for-a-knowledge-store"></a>Origini dei dati per un archivio informazioni

Se un archivio informazioni viene restituito da una pipeline di arricchimento intelligenza artificiale, quali sono gli input? I dati originali che si desidera estrarre, arricchire e infine salvare in un archivio informazioni possono provenire da qualsiasi origine dati di Azure supportata dagli indicizzatori di ricerca: 

* [Azure Cosmos DB](search-howto-index-cosmosdb.md)

* [Archivio BLOB di Azure](search-howto-indexing-azure-blob-storage.md)

* [Archivio tabelle di Azure](search-howto-indexing-azure-tables.md)

* [SQL di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Gli indicizzatori e skillsets vengono creati per estrarre e arricchire o trasformare il contenuto come parte di un carico di lavoro di indicizzazione e quindi salvare i risultati in un archivio informazioni.

### <a name="rest-apis-used-in-creation-of-a-knowledge-store"></a>API REST usate per la creazione di un archivio informazioni

Sono necessarie solo due API per la creazione di un archivio informazioni (create Skills e create Indexer). Altre API vengono usate così come sono.

| Oggetto | API REST | Descrizione |
|--------|----------|-------------|
| Origine dati | [Creare un'origine dati](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Risorsa che identifica un'origine dati esterna di Azure che fornisce dati di origine usati per creare documenti arricchiti.  |
| Set di competenze | [Creare un set di competenze (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Risorsa che coordina l'utilizzo di [competenze predefinite](cognitive-search-predefined-skills.md) e [competenze cognitive personalizzate](cognitive-search-custom-skill-interface.md) usate in una pipeline di arricchimento durante l'indicizzazione. Un skillt dispone di una definizione `knowledgeStore` come elemento figlio. |
| index | [Creare un indice](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Schema che esprime un indice di ricerca. Viene eseguito il mapping dei campi nell'indice con i campi nei dati di origine o i campi prodotti durante la fase di arricchimento (ad esempio, un campo per i nomi dell'organizzazione creati dal riconoscimento entità). |
| Indicizzatore | [Creare un indicizzatore (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Risorsa che definisce i componenti usati durante l'indicizzazione, incluso un'origine dati, un set di competenze, associazioni di campi dalle strutture dei dati di origine e intermedie all'indice di destinazione, oltre all'indice stesso. L'esecuzione dell'indicizzatore è il trigger di inserimento dati e arricchimento. L'output è un indice di ricerca basato sullo schema dell'indice, popolato con dati di origine e arricchito da set di competenze.  |

### <a name="physical-composition-of-a-knowledge-store"></a>Composizione fisica di un archivio informazioni

 Una *proiezione*, che è un elemento di una definizione `knowledgeStore`, articola lo schema e la struttura dell'output in modo che corrisponda all'utilizzo previsto. Se sono presenti applicazioni che utilizzano i dati in formati e forme diversi, è possibile definire più proiezioni. 

Le proiezioni possono essere articolate come oggetti o tabelle:

+ Come oggetto, la proiezione esegue il mapping all'archiviazione BLOB, in cui la proiezione viene salvata in un contenitore, all'interno del quale di trovano gli oggetti o le rappresentazioni gerarchiche in formato JSON per scenari come ad esempio una pipeline di data science.

+ Come tabella, la proiezione esegue il mapping all'archiviazione tabelle. Una rappresentazione tabulare mantiene le relazioni per scenari come l'analisi dei dati o l'esportazione come frame di dati per machine learning. Le proiezioni arricchite possono quindi essere facilmente importate in altri archivi di dati. 

È possibile creare più proiezioni in un knowledge store per supportare le varie categorie all'interno dell'organizzazione. Uno sviluppatore potrebbe aver bisogno di accedere alla rappresentazione JSON completa di un documento arricchito, mentre data scientists o analisti di dati potrebbero volere strutture di dati granulari o modulari modellati in base a un set di competenze.

Ad esempio, se uno degli obiettivi del processo di arricchimento consiste nel creare anche un set di dati usato per eseguire il training di un modello, proiettare i dati nell'archivio oggetti sarebbe un modo per usare i dati nelle pipeline di data science. In alternativa, se si vuole creare un dashboard di Power BI rapido basato sui documenti arricchiti, la proiezione tabulare funziona bene.

<a name="tools-and-apps"></a>

## <a name="connecting-with-tools-and-apps"></a>Connessione con strumenti e app

Una volta che gli arricchimenti sono presenti nell'archiviazione, è possibile usare qualsiasi strumento o tecnologia che si connette ad Archiviazione BLOB di Azure o da Archiviazione tabelle di Azure per esplorare, analizzare o utilizzare i contenuti. È possibile iniziare con l'elenco seguente:

+ [Storage Explorer](knowledge-store-view-storage-explorer.md) per visualizzare la struttura e il contenuto dei documenti arricchiti. Considerare questo strumento come lo strumento di base per visualizzare i contenuti del knowledge store.

+ [Power bi](knowledge-store-connect-power-bi.md) per gli strumenti di creazione di report e analisi se si dispone di dati numerici.

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) per ulteriori elaborazioni.

## <a name="next-steps"></a>Passaggi successivi

Archivio informazioni offre la persistenza dei documenti arricchiti, utile quando si progetta un competenze o la creazione di nuove strutture e contenuti per l'utilizzo da parte di qualsiasi applicazione client in grado di accedere a un account di archiviazione di Azure.

L'approccio più semplice per la creazione di documenti arricchiti è tramite la procedura guidata **Importa dati** , ma è anche possibile usare l'API REST e l'API REST, che risulta più utile se si vuole approfondire la creazione e il riferimento agli oggetti.

> [!div class="nextstepaction"]
> [Creazione di un archivio informazioni tramite il portale](knowledge-store-create-portal.md)
> [creare un archivio informazioni con l'API REST e l'API REST](knowledge-store-create-rest.md)
