---
title: Introduzione all'archivio conoscenze (anteprima)
titleSuffix: Azure Cognitive Search
description: Inviare documenti arricchiti ad Archiviazione di Azure in cui è possibile visualizzare, rimodellare e usare documenti arricchiti in Ricerca cognitiva di Azure e in altre applicazioni. Questa funzionalità è disponibile in anteprima pubblica.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 0ad780c04954c09ddfd432b3c7de3dc65f0841bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942987"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Introduzione agli archivi conoscenze in Ricerca cognitiva di Azure

> [!IMPORTANT] 
> L'archivio conoscenze è attualmente disponibile in anteprima pubblica. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Queste funzionalità di anteprima vengono fornite dall'[API REST versione 2019-05-06-Preview](search-api-preview.md). Il supporto del portale è attualmente limitato e non è disponibile alcun supporto per .NET SDK.

Archivio informazioni è una funzionalità di Ricerca cognitiva di Azure che mantiene l'output da una pipeline di [arricchimento dell'iaformazione per](cognitive-search-concept-intro.md) l'analisi indipendente o l'elaborazione a valle. Un *documento arricchito* è l'output di una pipeline, creato da contenuto che è stato estratto, strutturato e analizzato tramite processi di intelligenza artificiale. In una pipeline di intelligenza artificiale standard, i documenti arricchiti sono temporanei, ovvero vengono usati solo durante l'indicizzazione per poi essere rimossi. Con l'archivio conoscenze, i documenti arricchiti vengono conservati. 

Se in passato sono state usate le competenze cognitive, si sa già che i set di *competenze* spostano un documento attraverso una sequenza di arricchimenti. Il risultato può essere rappresentato da un indice di ricerca o (novità in questa anteprima) da proiezioni in un archivio conoscenze. I due output, l'indice di ricerca e il Knowledge Store, sono prodotti della stessa pipeline; derivadagli stessi input, ma con conseguente output strutturato, archiviato e utilizzato in modi molto diversi.

Fisicamente, un archivio conoscenze è una [risorsa di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview), ad esempio una risorsa di archiviazione tabelle di Azure, di archiviazione BLOB di Azure o entrambe. Qualsiasi strumento o processo in grado di connettersi ad archiviazione di Azure può usare il contenuto di un archivio conoscenze.

![Archivio conoscenze nel diagramma della pipeline](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Archivio conoscenze nel diagramma della pipeline")

## <a name="benefits-of-knowledge-store"></a>Vantaggi del knowledge store

Un archivio informazioni offre la struttura, il contesto e il contenuto effettivo, rimodellati da file di dati non strutturati e semistrutturati come BLOB, file di immagine sottoposti ad analisi o persino dati strutturati, rimodellati in nuove forme. In una [procedura dettagliata,](knowledge-store-create-rest.md)è possibile vedere in prima persona come un documento JSON denso viene partizionato in sottostrutture, ricostituito in nuove strutture e reso altrimenti disponibile per i processi a valle come i carichi di lavoro di machine learning e data science.

Anche se è utile vedere cosa può produrre una pipeline di arricchimento AI, il potenziale reale di un archivio di conoscenze è la capacità di rimodellare i dati. È possibile iniziare con un set di competenze di base e quindi iterarlo per aggiungere livelli crescenti di struttura, che è possibile combinare in nuove strutture, utilizzabili in altre app oltre a Ricerca cognitiva di Azure.

Di seguito vengono elencati i vantaggi del knowledge store:

+ Utilizzare documenti arricchiti in [strumenti di analisi e creazione report](#tools-and-apps) diversi dalla ricerca. Power BI con Power Query è una scelta interessante, ma qualsiasi strumento o app che può connettersi ad Archiviazione di Azure può eseguire il pull da un Archivio informazioni creato.

+ Affinare una pipeline di indicizzazione basata su intelligenza artificiale mentre si eseguono il debug delle fasi e delle definizioni dei set di competenze. Un knowledge store mostra il prodotto di una definizione di set di competenze in una pipeline di indicizzazione basata su intelligenza artificiale. È possibile usare questi risultati per progettare un set di competenze migliori, perché è possibile vedere esattamente come sono gli arricchimenti. È possibile usare [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) in Archiviazione di Azure per visualizzare il contenuto di un archivio informazioni.

+ Modellare i dati in nuove forme. Il rimodellamento è codificato in set di competenze, ma il punto è che un set di competenze può ora fornire questa funzionalità. La [competenza Shaper](cognitive-search-skill-shaper.md) in Ricerca cognitiva di Azure è stata estesa per supportare questa attività. Il rimodellamento consente di definire una proiezione che si allinea con l'uso previsto dei dati, preservando al tempo stesso le relazioni.

> [!Note]
> Sei nuovo nell'arricchimento dell'IA e nelle capacità cognitive? Ricerca cognitiva di Azure si integra con le funzionalità Visione artificiale e Lingua di Servizi cognitivi per estrarre e arricchire i dati di origine tramite il Riconoscimento ottico dei caratteri (OCR) su file di immagine, riconoscimento di entità ed estrazione di frasi chiave da file di testo e altro ancora. Per altre informazioni, vedere [Arricchimento tramite intelligenza artificiale in Ricerca cognitiva di Azure](cognitive-search-concept-intro.md).

## <a name="physical-storage"></a>Archiviazione fisica

L'espressione fisica di un knowledge `projections` store è `knowledgeStore` articolata attraverso l'elemento di una definizione in un Skillset. La proiezione definisce una struttura dell'output in modo che corrisponda all'uso previsto.

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

Il tipo di proiezione specificato in questa struttura determina il tipo di archiviazione utilizzato dal Knowledge Store.

+ L'archiviazione tabelle viene `tables`utilizzata quando si definisce l'estensione . Definire una proiezione di tabella quando sono necessarie strutture di report tabulari per gli input in strumenti analitici o esportarli come frame di dati in altri archivi dati. È possibile `tables` specificare più di più per ottenere un sottoinsieme o una sezione trasversale di documenti arricchiti. All'interno dello stesso gruppo di proiezione, le relazioni tra tabelle vengono mantenute in modo da poterle utilizzare tutte.

+ L'archiviazione BLOB viene `objects` `files`usata quando si definisce o . La rappresentazione `object` fisica di un è una struttura JSON gerarchica che rappresenta un documento arricchito. Un `file` è un'immagine estratta da un documento, trasferita intatta all'archiviazione BLOB.

Un singolo oggetto di `tables`proiezione contiene un insieme di , `objects`, `files`, e per molti scenari, la creazione di una proiezione potrebbe essere sufficiente. 

Tuttavia, è possibile creare `table` - `object` - `file` più set di proiezioni ed è possibile farlo se si desidera relazioni dati diverse. All'interno di un set, i dati sono correlati, presupponendo che tali relazioni esistano e possano essere rilevate. Se si creano set aggiuntivi, i documenti di ogni gruppo non sono mai correlati. Un esempio di utilizzo di più gruppi di proiezione potrebbe essere se si desidera che gli stessi dati vengano utilizzati con il sistema online e che devono essere rappresentati in modo specifico, si desidera anche che gli stessi dati vengano proiettati per l'utilizzo in una pipeline di data science rappresentata Diversamente.

## <a name="requirements"></a>Requisiti 

[Archiviazione di Azure](https://docs.microsoft.com/azure/storage/) è obbligatoria. Fornisce archiviazione fisica. È possibile usare l'archiviazione BLOB, l'archiviazione tabelle o entrambi. L'archiviazione BLOB viene usata per i documenti arricchiti intatti, in genere quando l'output viene generato da processi a valle. L'archiviazione tabelle è per le sezioni di documenti arricchiti, comunemente utilizzati per l'analisi e la creazione di report.

[Il set di competenze](cognitive-search-working-with-skillsets.md) è obbligatorio. Contiene la `knowledgeStore` definizione e determina la struttura e la composizione di un documento arricchito. Non è possibile creare un archivio informazioni utilizzando un set di competenze vuoto. È necessario disporre di almeno una competenza in un set di competenze.

[L'indicizzatore](search-indexer-overview.md) è obbligatorio. Un set di competenze viene richiamato da un indicizzatore, che guida l'esecuzione. Gli indicizzatori sono dotati di un proprio set di requisiti e attributi. Molti di questi attributi hanno un impatto diretto su un archivio di conoscenze:

+ Gli indicizzatori richiedono [un'origine dati di Azure supportata,](search-indexer-overview.md#supported-data-sources) ovvero la pipeline che crea infine l'avvio del archivio informazioni eseguendo il pull dei dati da un'origine supportata in Azure.Indexers require a supported Azure data source (the pipeline that ultimately creates the knowledge store starts by pulling data from a supported source on Azure). 

+ Gli indicizzatori richiedono un indice di ricerca. Un indicizzatore richiede di fornire uno schema di indice, anche se non si prevede mai di usarlo. Un indice minimo ha un campo stringa, designato come chiave.

+ Gli indicizzatori forniscono mapping di campo facoltativi, utilizzati per l'alias di un campo di origine a un campo di destinazione. Se un mapping dei campi predefinito richiede modifiche (per utilizzare un nome o un tipo diverso), è possibile creare un [mapping dei campi](search-indexer-field-mappings.md) all'interno di un indicizzatore. Per l'output dell'archivio informazioni, la destinazione può essere un campo in un oggetto BLOB o in una tabella.

+ Gli indicizzatori dispongono di pianificazioni e altre proprietà, ad esempio i meccanismi di rilevamento delle modifiche forniti da varie origini dati, possono essere applicate anche a un archivio informazioni. Ad esempio, è possibile [pianificare](search-howto-schedule-indexers.md) l'arricchimento a intervalli regolari per aggiornare il contenuto. 

## <a name="how-to-create-a-knowledge-store"></a>Come creare un archivio informazioni

Per creare l'archivio informazioni, usare`api-version=2019-05-06-Preview`il portale o l'API REST di anteprima ( ).

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

**L'Importazione** guidata dati include opzioni per la creazione di un archivio informazioni. Per l'esplorazione iniziale, [creare il primo archivio informazioni in quattro passaggi.](knowledge-store-connect-power-bi.md)

1. Selezionare un'origine dati supportata.

1. Specificare l'arricchimento: associare una risorsa, selezionare le competenze e specificare un archivio informazioni. 

1. Creare uno schema di indice. La procedura guidata lo richiede e può dedurne uno per te.

1. Eseguire la procedura guidata. L'estrazione, l'arricchimento e lo stoccaggio si verificano in quest'ultimo passaggio.

### <a name="use-create-skillset-and-the-preview-rest-api"></a>Usare Crea set di competenze e l'API REST di anteprimaUse Create Skillset and the preview REST API

A `knowledgeStore` viene definito all'interno di un [set di competenze](cognitive-search-working-with-skillsets.md), che a sua volta viene richiamato da un [indicizzatore](search-indexer-overview.md). Durante l'arricchimento, Ricerca cognitiva di Azure crea uno spazio nell'account di Archiviazione di Azure e proietta i documenti arricchiti come BLOB o in tabelle, a seconda della configurazione.

Attualmente, l'API REST di anteprima è l'unico meccanismo con cui è possibile creare un archivio informazioni a livello di codice. Un modo semplice per esplorare consiste [nel creare il primo archivio informazioni usando Postman e l'API REST.](knowledge-store-create-rest.md)

Il contenuto di riferimento per questa funzionalità di anteprima si trova nella sezione [di riferimento dell'API](#kstore-rest-api) di questo articolo. 

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>Come connettersi con strumenti e app

Una volta che gli arricchimenti sono presenti nell'archiviazione, è possibile usare qualsiasi strumento o tecnologia che si connette ad Archiviazione BLOB di Azure o da Archiviazione tabelle di Azure per esplorare, analizzare o utilizzare i contenuti. È possibile iniziare con l'elenco seguente:

+ [Storage Explorer](knowledge-store-view-storage-explorer.md) per visualizzare la struttura e il contenuto dei documenti arricchiti. Considerare questo strumento come lo strumento di base per visualizzare i contenuti del knowledge store.

+ [Power BI](knowledge-store-connect-power-bi.md) per la creazione di report e l'analisi. 

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) per ulteriori elaborazioni.

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>API (riferimento)

La versione `2019-05-06-Preview` dell'API REST fornisce il Knowledge Store tramite definizioni aggiuntive sui set di competenze. Oltre al riferimento, vedere [Creare un archivio informazioni usando Postman](knowledge-store-create-rest.md) per informazioni dettagliate su come chiamare le API.

+ [Creare un set di competenze (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-skillset) 
+ [Set di competenze di aggiornamento (api-version-2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) 


## <a name="next-steps"></a>Passaggi successivi

L'Archivio conoscenze offre la persistenza dei documenti arricchiti, utile durante la progettazione di un set di competenze o la creazione di nuove strutture e contenuto per l'utilizzo da parte di qualsiasi applicazione client in grado di accedere a un account di archiviazione di Azure.

L'approccio più semplice per la creazione di documenti arricchiti è [tramite il portale](knowledge-store-create-portal.md), ma è anche possibile usare Postman e l'API REST, che è più utile se si desidera informazioni dettagliate su come vengono creati e referenziati gli oggetti.

> [!div class="nextstepaction"]
> [Creare un archivio informazioni utilizzando Postman e RESTCreate a knowledge store using Postman and REST](knowledge-store-create-rest.md)

Per ulteriori informazioni sulle proiezioni, le funzionalità e la modalità [di definizione in un set di competenze](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Proiezioni in un archivio di conoscenze](knowledge-store-projection-overview.md)

Per un tutorial che copre i concetti di proiezioni avanzate come sezionamento, inline shaping e relazioni, iniziare con [definire le proiezioni in un archivio di conoscenze](knowledge-store-projections-examples.md)

> [!div class="nextstepaction"]
> [Definire proiezioni in un archivio informazioni](knowledge-store-projections-examples.md)