---
title: Introduzione all'archivio conoscenze (anteprima)
titleSuffix: Azure Cognitive Search
description: Inviare documenti arricchiti ad Archiviazione di Azure in cui è possibile visualizzare, rimodellare e utilizzare tali documenti in Ricerca cognitiva di Azure e in altre applicazioni. Questa funzionalità è disponibile in anteprima pubblica.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/05/2020
ms.openlocfilehash: 20819bc6ec091eddf5d65b1c0d7aa57c821b2fc1
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858808"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Introduzione agli archivi conoscenze in Ricerca cognitiva di Azure

> [!IMPORTANT] 
> L'archivio conoscenze è attualmente disponibile in anteprima pubblica. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Queste funzionalità di anteprima vengono fornite dall'[API REST versione 2019-05-06-Preview](search-api-preview.md). Il supporto del portale è attualmente limitato e non è disponibile alcun supporto per .NET SDK.

L'archivio conoscenze è una funzionalità di Ricerca cognitiva di Azure che rende persistente l'output di una [pipeline di arricchimento tramite intelligenza artificiale](cognitive-search-concept-intro.md) per analisi indipendenti o elaborazioni downstream. Un *documento arricchito* è l'output di una pipeline, creato da contenuto che è stato estratto, strutturato e analizzato tramite processi di intelligenza artificiale. In una pipeline di intelligenza artificiale standard, i documenti arricchiti sono temporanei, ovvero vengono usati solo durante l'indicizzazione per poi essere rimossi. Con l'archivio conoscenze, i documenti arricchiti vengono conservati. 

Se le competenze cognitive sono già state usate in precedenza, si saprà che i *set di competenze* consentono di spostare un documento attraverso una sequenza di arricchimenti. Il risultato può essere rappresentato da un indice di ricerca o (novità in questa anteprima) da proiezioni in un archivio conoscenze. I due output, l'indice di ricerca e l'archivio conoscenze sono prodotti della stessa pipeline e derivano dagli stessi input, ma risultano in un output che viene strutturato, archiviato e usato in modi molto diversi.

Fisicamente, un archivio conoscenze è una [risorsa di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview), ad esempio una risorsa di archiviazione tabelle di Azure, di archiviazione BLOB di Azure o entrambe. Qualsiasi strumento o processo in grado di connettersi ad archiviazione di Azure può usare il contenuto di un archivio conoscenze.


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=235&end=426]


![Archivio conoscenze nel diagramma della pipeline](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Archivio conoscenze nel diagramma della pipeline")

## <a name="benefits-of-knowledge-store"></a>Vantaggi del knowledge store

Tramite l'archivio conoscenze è possibile ottenere struttura, contesto e contenuti reali, ricavati da file di dati non strutturati e semi-strutturati come BLOB, file di immagini che sono stati sottoposti ad analisi o anche dati strutturati rimodellati in nuovi moduli. In una [proceduta dettagliata](knowledge-store-create-rest.md) è possibile vedere come un documento JSON ad alta densità viene partizionato in sottostrutture, ricostituito in nuove strutture e reso altrimenti disponibile per processi downstream come carichi di lavoro di apprendimento automatico e data science.

Sebbene sia utile per osservare il risultato generato da una pipeline di arricchimento tramite intelligenza artificiale, il vero potenziale dell'archivio conoscenze è la capacità di rimodellare i dati. È possibile iniziare con un set di competenze di base e quindi iterarlo per aggiungere livelli crescenti di struttura, che è possibile combinare in nuove strutture, utilizzabili in altre app oltre a Ricerca cognitiva di Azure.

Di seguito vengono elencati i vantaggi del knowledge store:

+ Utilizzare documenti arricchiti in [strumenti di analisi e creazione report](#tools-and-apps) diversi dalla ricerca. Power BI con Power Query rappresenta una scelta interessante, ma qualsiasi strumento o app in grado di connettersi ad Archiviazione di Azure può eseguire il pull da un archivio conoscenze creato.

+ Affinare una pipeline di indicizzazione basata su intelligenza artificiale mentre si eseguono il debug delle fasi e delle definizioni dei set di competenze. Un knowledge store mostra il prodotto di una definizione di set di competenze in una pipeline di indicizzazione basata su intelligenza artificiale. È possibile usare questi risultati per progettare un set di competenze migliori, perché è possibile vedere esattamente come sono gli arricchimenti. È possibile usare [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) in Archiviazione di Azure per visualizzare i contenuti dell'archivio conoscenze.

+ Modellare i dati in nuove forme. Il rimodellamento è codificato in set di competenze, ma il punto è che un set di competenze può ora fornire questa funzionalità. La [competenza Shaper](cognitive-search-skill-shaper.md) in Ricerca cognitiva di Azure è stata estesa per supportare questa attività. Il rimodellamento consente di definire una proiezione che si allinea con l'uso previsto dei dati, preservando al tempo stesso le relazioni.

> [!Note]
> È la prima volta che si usano l'arricchimento tramite intelligenza artificiale e le competenze cognitive? Ricerca cognitiva di Azure si integra con le funzionalità Visione artificiale e Lingua di Servizi cognitivi per estrarre e arricchire i dati di origine tramite il Riconoscimento ottico dei caratteri (OCR) su file di immagine, riconoscimento di entità ed estrazione di frasi chiave da file di testo e altro ancora. Per altre informazioni, vedere [Arricchimento tramite intelligenza artificiale in Ricerca cognitiva di Azure](cognitive-search-concept-intro.md).

## <a name="physical-storage"></a>Archiviazione fisica


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=455&end=542]


L'espressione fisica di un archivio conoscenze viene articolata tramite l'elemento `projections` di una definizione di `knowledgeStore` in un set di competenze. La proiezione definisce una struttura dell'output per renderlo adatto all'utilizzo previsto.

Le proiezioni possono essere articolate sotto forma di tabelle, oggetti o file.

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

Il tipo di proiezione specificato in questa struttura determina il tipo di archiviazione usato dall'archivio conoscenze.

+ Se si definisce `tables` viene usata l'archiviazione tabelle. Definire una proiezione di tabella se si necessita di strutture di report tabulari per gli input negli strumenti analitici o per l'esportazione come frame di dati in altri archivi dati. È possibile specificare più `tables` per ottenere un subset o una sezione trasversale dei documenti arricchiti. All'interno dello stesso gruppo di proiezione, le relazioni tra tabelle vengono conservate in modo che sia possibile utilizzarle tutte.

+ Se si definisce `objects` o `files` viene usata l'archiviazione BLOB. La rappresentazione fisica di un `object` è una struttura JSON gerarchica che rappresenta un documento arricchito. Un `file` è un'immagine estratta da un documento, trasferita intatta nell'archiviazione BLOB.

Un singolo oggetto di proiezione contiene un set di `tables`, `objects`, `files` e, per molti scenari, potrebbe essere sufficiente la creazione di una sola proiezione. 

Tuttavia, è possibile creare più set di proiezioni di `table`-`object`-`file` ed è consigliabile procedere in questo modo se si desiderano relazioni diverse tra dati. All'interno di un set i dati vengono correlati, posto che tali relazioni esistano e sia possibile rilevarle. Se si creano set aggiuntivi, i documenti in ogni gruppo non sono mai correlati. Come esempio di utilizzo di più gruppi di proiezione si potrebbe pensare a quando si desidera che gli stessi dati vengano proiettati per l'utilizzo sia con il sistema online (che è necessario rappresentare in modo specifico) che in una pipeline di data science (rappresentata in modo diverso).

## <a name="requirements"></a>Requisiti 

Il servizio [Archiviazione di Azure](https://docs.microsoft.com/azure/storage/) è obbligatorio. Corrisponde alla posizione di archiviazione fisica. È possibile usare l'archiviazione BLOB, l'archiviazione tabelle o entrambi. L'archiviazione BLOB viene usata per i documenti arricchiti intatti, di solito quando l'output è diretto ai processi downstream. L'archiviazione tabelle è destinata alle sezioni dei documenti arricchiti, comunemente utilizzati per l'analisi e la creazione di report.

Il [set di competenze](cognitive-search-working-with-skillsets.md) è obbligatorio. Contiene la definizione di `knowledgeStore` e determina la struttura e la composizione di un documento arricchito. Non è possibile creare un archivio conoscenze utilizzando un set di competenze vuoto. Il set di competenze deve contenere almeno una competenza.

L'[indicizzatore](search-indexer-overview.md) è obbligatorio. Un set di competenze viene richiamato da un indicizzatore che guida l'esecuzione. Gli indicizzatori sono dotati di un proprio set di requisiti e attributi. Molti di questi attributi hanno un impatto diretto sull'archivio conoscenze:

+ Per gli indicizzatori è necessaria un'[origine dati di Azure supportata](search-indexer-overview.md#supported-data-sources) (la pipeline che sostanzialmente crea l'archivio conoscenze inizia eseguendo il pull dei dati da un'origine supportata in Azure). 

+ Per gli indicizzatori è necessario un indice di ricerca. Occorre specificare uno schema di indice, anche se non si prevede di usarlo. Un indice di base contiene un campo di stringa, designato come chiave.

+ Gli indicizzatori forniscono mapping dei campi facoltativi, usati per l'alias di un campo di origine in un campo di destinazione. Se è necessario modificare un mapping dei campi predefinito (per usare un nome o un tipo diverso), è possibile creare un [mapping dei campi](search-indexer-field-mappings.md) all'interno di un indicizzatore. Per l'output dell'archivio conoscenze, la destinazione può essere un campo in un oggetto o in una tabella BLOB.

+ Gli indicizzatori contengono pianificazioni e le altre proprietà, come i meccanismi di rilevamento delle modifiche provenienti da varie origini dati, possono essere applicate anche agli archivi conoscenze. Ad esempio, è possibile [pianificare](search-howto-schedule-indexers.md) l'arricchimento a intervalli regolari per aggiornare i contenuti. 

## <a name="how-to-create-a-knowledge-store"></a>Come creare un archivio conoscenze

Per creare un archivio conoscenze, usare il portale o l'API REST di anteprima (`api-version=2019-05-06-Preview`).

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

La procedura guidata di **importazione dei dati** include le opzioni per la creazione di un archivio conoscenze. Per l'esplorazione iniziale, [creare il primo archivio conoscenze in quattro passaggi](knowledge-store-connect-power-bi.md).

1. Selezionare un'origine dati supportata.

1. Specificare l'arricchimento: allegare una risorsa, selezionare le competenze e specificare un archivio conoscenze. 

1. Creare uno schema di indice. Si tratta di un passaggio obbligatorio della procedura guidata ed è possibile dedurlo automaticamente.

1. Eseguire la procedura guidata. In questo ultimo passaggio vengono eseguite le operazioni di estrazione, arricchimento e archiviazione.

### <a name="use-create-skillset-and-the-preview-rest-api"></a>Usare l'API REST di creazione del set di competenze di anteprima

Un `knowledgeStore` viene definito all'interno di un [set di competenze](cognitive-search-working-with-skillsets.md), che a sua volta viene richiamato da un [indicizzatore](search-indexer-overview.md). Durante l'arricchimento, Ricerca cognitiva di Azure crea uno spazio nell'account di Archiviazione di Azure e proietta i documenti arricchiti come BLOB o tabelle, a seconda della configurazione.

Attualmente, l'API REST di anteprima è l'unico meccanismo mediante il quale è possibile creare un archivio conoscenze in modo programmatico. Una modalità semplice di esplorazione consiste nel [creare il primo archivio conoscenze con Postman e l'API REST](knowledge-store-create-rest.md).

Il contenuto di riferimento relativo a questa funzionalità di anteprima si trova nella sezione [Informazioni di riferimento sulle API](#kstore-rest-api) di questo articolo. 

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>Come eseguire la connessione con strumenti e app

Una volta che gli arricchimenti sono presenti nell'archiviazione, è possibile usare qualsiasi strumento o tecnologia che si connette ad Archiviazione BLOB di Azure o da Archiviazione tabelle di Azure per esplorare, analizzare o utilizzare i contenuti. È possibile iniziare con l'elenco seguente:

+ [Storage Explorer](knowledge-store-view-storage-explorer.md) per visualizzare la struttura e il contenuto dei documenti arricchiti. Considerare questo strumento come lo strumento di base per visualizzare i contenuti del knowledge store.

+ [Power BI](knowledge-store-connect-power-bi.md) per la creazione di report e l'analisi. 

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) per ulteriori elaborazioni.

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>Informazioni di riferimento sulle API

La versione dell'API REST `2019-05-06-Preview` fornisce l'archivio conoscenze tramite definizioni aggiuntive nei set di competenze. Oltre ai riferimenti, vedere [Creare un archivio conoscenze con Postman](knowledge-store-create-rest.md) per informazioni dettagliate su come chiamare le API.

+ [Creare un set di competenze (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-skillset) 
+ [Aggiornare un set di competenze (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) 


## <a name="next-steps"></a>Passaggi successivi

L'Archivio conoscenze offre la persistenza dei documenti arricchiti, utile durante la progettazione di un set di competenze o la creazione di nuove strutture e contenuto per l'utilizzo da parte di qualsiasi applicazione client in grado di accedere a un account di archiviazione di Azure.

L'approccio più semplice alla creazione di documenti arricchiti consiste nell'[uso del portale](knowledge-store-create-portal.md); tuttavia, è anche possibile usare Postman e l'API REST, alternativa più utile se si desidera ottenere informazioni cognitive dettagliate su come gli oggetti vengono creati e su come viene fatto loro riferimento.

> [!div class="nextstepaction"]
> [Creare un archivio conoscenze con REST e Postman](knowledge-store-create-rest.md)

Per altre informazioni sulle proiezioni, sulle funzionalità e su come [definirle in un set di competenze](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Proiezioni in un archivio conoscenze](knowledge-store-projection-overview.md)

Per un'esercitazione sui concetti avanzati delle proiezioni, come il sezionamento, la definizione della forma inline e le relazioni, iniziare con [Definire le proiezioni in un archivio conoscenze](knowledge-store-projections-examples.md)

> [!div class="nextstepaction"]
> [Definire le proiezioni in un archivio conoscenze](knowledge-store-projections-examples.md)