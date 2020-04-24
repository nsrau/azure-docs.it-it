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

L'archivio delle informazioni è una funzionalità di Azure ricerca cognitiva che rende permanente l'output di una [pipeline di arricchimento di intelligenza artificiale](cognitive-search-concept-intro.md) per l'analisi indipendente o l'elaborazione downstream. Un *documento arricchito* è l'output di una pipeline, creato da contenuto che è stato estratto, strutturato e analizzato tramite processi di intelligenza artificiale. In una pipeline di intelligenza artificiale standard, i documenti arricchiti sono temporanei, ovvero vengono usati solo durante l'indicizzazione per poi essere rimossi. Con l'archivio conoscenze, i documenti arricchiti vengono conservati. 

Se in passato sono state usate competenze cognitive, si sa già che *skillsets* sposta un documento attraverso una sequenza di arricchimenti. Il risultato può essere rappresentato da un indice di ricerca o (novità in questa anteprima) da proiezioni in un archivio conoscenze. I due output, l'indice di ricerca e l'archivio delle informazioni, sono prodotti della stessa pipeline. derivato dagli stessi input, ma risultante nell'output strutturato, archiviato e usato in modi molto diversi.

Fisicamente, un archivio conoscenze è una [risorsa di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview), ad esempio una risorsa di archiviazione tabelle di Azure, di archiviazione BLOB di Azure o entrambe. Qualsiasi strumento o processo in grado di connettersi ad archiviazione di Azure può usare il contenuto di un archivio conoscenze.

![Archivio conoscenze nel diagramma della pipeline](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Archivio conoscenze nel diagramma della pipeline")

## <a name="benefits-of-knowledge-store"></a>Vantaggi del knowledge store

Un archivio informazioni fornisce la struttura, il contesto e i contenuti effettivi ottenuti da file di dati non strutturati e semistrutturati, come i BLOB, i file di immagine sottoposti ad analisi o persino i dati strutturati, rimodellati in nuovi moduli. In una [procedura dettagliata](knowledge-store-create-rest.md)è possibile vedere prima di tutto come un documento JSON denso viene partizionato in sottostrutture, ricostituito in nuove strutture e reso disponibile per i processi downstream come Machine Learning e carichi di lavoro Data Science.

Sebbene sia utile vedere cosa può produrre una pipeline di arricchimento di intelligenza artificiale, il potenziale reale di un archivio informazioni è la possibilità di modificare la forma dei dati. È possibile iniziare con un set di competenze di base e quindi iterarlo per aggiungere livelli crescenti di struttura, che è possibile combinare in nuove strutture, utilizzabili in altre app oltre a Ricerca cognitiva di Azure.

Di seguito vengono elencati i vantaggi del knowledge store:

+ Utilizzare documenti arricchiti in [strumenti di analisi e creazione report](#tools-and-apps) diversi dalla ricerca. Power BI con Power Query è una scelta interessante, ma qualsiasi strumento o app in grado di connettersi ad archiviazione di Azure può effettuare il pull da un archivio informazioni creato dall'utente.

+ Affinare una pipeline di indicizzazione basata su intelligenza artificiale mentre si eseguono il debug delle fasi e delle definizioni dei set di competenze. Un knowledge store mostra il prodotto di una definizione di set di competenze in una pipeline di indicizzazione basata su intelligenza artificiale. È possibile usare questi risultati per progettare un set di competenze migliori, perché è possibile vedere esattamente come sono gli arricchimenti. È possibile usare [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) in archiviazione di Azure per visualizzare il contenuto di un archivio informazioni.

+ Modellare i dati in nuove forme. Il rimodellamento è codificato in set di competenze, ma il punto è che un set di competenze può ora fornire questa funzionalità. La [competenza Shaper](cognitive-search-skill-shaper.md) in Ricerca cognitiva di Azure è stata estesa per supportare questa attività. Il rimodellamento consente di definire una proiezione che si allinea con l'uso previsto dei dati, preservando al tempo stesso le relazioni.

> [!Note]
> Nuove funzionalità di intelligenza artificiale e competenze cognitive? Ricerca cognitiva di Azure si integra con le funzionalità Visione artificiale e Lingua di Servizi cognitivi per estrarre e arricchire i dati di origine tramite il Riconoscimento ottico dei caratteri (OCR) su file di immagine, riconoscimento di entità ed estrazione di frasi chiave da file di testo e altro ancora. Per altre informazioni, vedere [Arricchimento tramite intelligenza artificiale in Ricerca cognitiva di Azure](cognitive-search-concept-intro.md).

## <a name="physical-storage"></a>Archiviazione fisica

L'espressione fisica di un archivio informazioni viene articolata tramite l' `projections` elemento di una `knowledgeStore` definizione in un skillt. La proiezione definisce una struttura dell'output in modo che corrisponda all'utilizzo previsto.

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

+ L'archiviazione tabelle viene utilizzata quando si `tables`definisce. Definire una proiezione di tabella quando è necessario disporre di strutture di report tabulari per gli input di strumenti analitici o l'esportazione come frame di dati in altri archivi dati. È possibile specificare più `tables` per ottenere un subset o una sezione incrociata di documenti arricchiti. All'interno dello stesso gruppo di proiezione, le relazioni tra tabelle vengono conservate in modo che sia possibile utilizzarle tutte.

+ L'archiviazione BLOB viene usata quando si `objects` definisce `files`o. La rappresentazione fisica di un `object` oggetto è una struttura JSON gerarchica che rappresenta un documento arricchito. Un `file` oggetto è un'immagine estratta da un documento, trasferita intatto nell'archivio BLOB.

Un singolo oggetto di proiezione contiene un set `tables`di `objects`, `files`, e per molti scenari, la creazione di una proiezione potrebbe essere sufficiente. 

Tuttavia, è possibile creare più set di `table` - `object` - `file` proiezioni e questa operazione può essere eseguita se si desiderano relazioni tra dati differenti. All'interno di un set, i dati sono correlati, supponendo che tali relazioni esistano e possano essere rilevate. Se si creano set aggiuntivi, i documenti in ogni gruppo non sono mai correlati. Un esempio di utilizzo di più gruppi di proiezione potrebbe essere se si desidera che gli stessi dati proiettati per l'utilizzo con il sistema online e che debbano essere rappresentati in modo specifico, si desidera che gli stessi dati proiettati vengano utilizzati in una pipeline di data science rappresentata in modo diverso.

## <a name="requirements"></a>Requisiti 

[Archiviazione di Azure](https://docs.microsoft.com/azure/storage/) è obbligatoria. Fornisce l'archiviazione fisica. È possibile usare l'archiviazione BLOB, l'archiviazione tabelle o entrambi. L'archiviazione BLOB viene usata per documenti arricchiti intatti, in genere quando l'output passa ai processi downstream. L'archiviazione tabelle è destinata a sezioni di documenti arricchiti, comunemente utilizzati per l'analisi e la creazione di report.

Il livello di [competenze](cognitive-search-working-with-skillsets.md) è obbligatorio. Contiene la `knowledgeStore` definizione e determina la struttura e la composizione di un documento arricchito. Non è possibile creare un archivio informazioni utilizzando un oggetto con competenze vuote. È necessario disporre di almeno una competenza in un skillt.

L' [indicizzatore](search-indexer-overview.md) è obbligatorio. Un indicizzatore viene richiamato da un indicizzatore che guida l'esecuzione. Gli indicizzatori sono dotati di un proprio set di requisiti e attributi. Molti di questi attributi hanno un impatto diretto su un archivio informazioni:

+ Gli indicizzatori richiedono un' [origine dati di Azure supportata](search-indexer-overview.md#supported-data-sources) (la pipeline che in definitiva crea l'archivio informazioni inizia con il pull dei dati da un'origine supportata in Azure). 

+ Gli indicizzatori richiedono un indice di ricerca. Per un indicizzatore è necessario fornire uno schema di indice, anche se non si prevede di usarlo. Un indice minimo ha un campo stringa, designato come chiave.

+ Gli indicizzatori forniscono mapping di campi facoltativi, usati per l'alias di un campo di origine in un campo di destinazione. Se è necessario modificare un mapping di campi predefinito (per usare un nome o un tipo diverso), è possibile creare un [mapping dei campi](search-indexer-field-mappings.md) all'interno di un indicizzatore. Per l'output dell'archivio delle informazioni, la destinazione può essere un campo in un oggetto BLOB o in una tabella.

+ Gli indicizzatori hanno pianificazioni e altre proprietà, ad esempio i meccanismi di rilevamento delle modifiche fornite da varie origini dati, possono anche essere applicati a un archivio delle informazioni. Ad esempio, è possibile [pianificare](search-howto-schedule-indexers.md) l'arricchimento a intervalli regolari per aggiornare il contenuto. 

## <a name="how-to-create-a-knowledge-store"></a>Come creare un archivio informazioni

Per creare un archivio informazioni, usare il portale o l'API REST di`api-version=2019-05-06-Preview`anteprima ().

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

L' **importazione guidata dati** include opzioni per la creazione di un archivio informazioni. Per l'esplorazione iniziale, [creare il primo archivio informazioni in quattro passaggi](knowledge-store-connect-power-bi.md).

1. Selezionare un'origine dati supportata.

1. Specificare l'arricchimento: alleghi una risorsa, selezionare competenze e specificare un archivio informazioni. 

1. Creare uno schema di indice. La procedura guidata lo richiede e può dedurre uno per l'utente.

1. Eseguire la procedura guidata. L'estrazione, l'arricchimento e l'archiviazione si verificano nell'ultimo passaggio.

### <a name="use-create-skillset-and-the-preview-rest-api"></a>Usare create Skills e l'API REST di anteprima

Un `knowledgeStore` oggetto viene definito all'interno di un oggetto [skillt](cognitive-search-working-with-skillsets.md), che a sua volta viene richiamato da un [indicizzatore](search-indexer-overview.md). Durante l'arricchimento, Azure ricerca cognitiva crea uno spazio nell'account di archiviazione di Azure e proietta i documenti arricchiti come BLOB o in tabelle, a seconda della configurazione.

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

La versione `2019-05-06-Preview` dell'API REST fornisce l'archivio delle informazioni tramite definizioni aggiuntive in skillsets. Per informazioni dettagliate su come chiamare le API, vedere l'articolo relativo alla [creazione di un archivio informazioni con il post](knowledge-store-create-rest.md) .

+ [Creare un set di competenze (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-skillset) 
+ [Update Skills (API-Version = 2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) 


## <a name="next-steps"></a>Passaggi successivi

L'Archivio conoscenze offre la persistenza dei documenti arricchiti, utile durante la progettazione di un set di competenze o la creazione di nuove strutture e contenuto per l'utilizzo da parte di qualsiasi applicazione client in grado di accedere a un account di archiviazione di Azure.

L'approccio più semplice per la creazione di documenti arricchiti è [tramite il portale](knowledge-store-create-portal.md), ma è anche possibile usare l'API REST e l'API REST, che risulta più utile se si vuole approfondire la creazione e il riferimento agli oggetti.

> [!div class="nextstepaction"]
> [Creazione di un archivio informazioni con i post e REST](knowledge-store-create-rest.md)

Per altre informazioni sulle proiezioni, sulle funzionalità e sul modo [in cui vengono definite in un skillt](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Proiezioni in un archivio informazioni](knowledge-store-projection-overview.md)

Per un'esercitazione che illustra concetti di proiezioni avanzati, ad esempio sezionamento, data shaping e relazioni inline, iniziare a [definire proiezioni in un archivio informazioni](knowledge-store-projections-examples.md)

> [!div class="nextstepaction"]
> [Definire le proiezioni in un archivio informazioni](knowledge-store-projections-examples.md)