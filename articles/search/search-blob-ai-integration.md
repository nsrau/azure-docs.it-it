---
title: Usare l'iaaper comprendere i dati di archiviazione BLOBUse AI to understand Blob storage data
titleSuffix: Azure Cognitive Search
description: Aggiungere l'elaborazione semantica del linguaggio naturale e l'analisi delle immagini ai BLOB di Azure usando una pipeline di arricchimento dell'iaformazione ai coramite io in Ricerca cognitiva di Azure.Add semantic, natural language processing and image analysis to Azure blobs using an AI enrichment pipeline in Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 591437eb3951164d53388b6164103948e9ad65e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73496444"
---
# <a name="use-ai-to-understand-blob-storage-data"></a>Usare l'iaaper comprendere i dati di archiviazione BLOBUse AI to understand Blob storage data

I dati nell'archiviazione BLOB di Azure sono spesso una varietà di contenuto non strutturato, ad esempio immagini, testo lungo, PDF e documenti di Office.Data in Azure Blob storage is often a variety of unstructured content such as images, long text, PDF, and Office documents. Usando le funzionalità di iA in Ricerca cognitiva di Azure, è possibile comprendere ed estrarre informazioni preziose dai BLOB in diversi modi. Esempi di applicazione dell'IA al contenuto BLOB includono:Examples of applying AI to blob content include:

+ Estrarre testo dalle immagini utilizzando il riconoscimento ottico dei caratteri (OCR)
+ Produrre una descrizione della scena o i tag da una foto
+ Rilevare la lingua e tradurre il testo in lingue diverse
+ Elaborare il testo con il riconoscimento delle entità denominate (NER) per trovare riferimenti a persone, date, luoghi o organizzazioni 

Mentre potrebbe essere necessaria solo una di queste funzionalità di iA, è comune combinarne più di esse nella stessa pipeline (ad esempio, l'estrazione di testo da un'immagine digitalizzata e quindi la ricerca di tutte le date e i luoghi a cui si fa riferimento). 

L'arricchimento dell'iA crea nuove informazioni, acquisite come testo, memorizzate nei campi. Dopo l'arricchimento, è possibile accedere a queste informazioni da un indice di ricerca tramite la ricerca full-text oppure inviare documenti arricchiti all'archiviazione di Azure per alimentare le nuove esperienze applicative che includono l'esplorazione dei dati per scenari di individuazione o analisi. 

In questo articolo viene visualizzato l'arricchimento dell'iA tramite un'ampia lente in modo da poter comprendere rapidamente l'intero processo, dalla trasformazione dei dati non elaborati nei BLOB, alle informazioni queryable in un indice di ricerca o in un archivio informazioni.

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>Cosa significa "arricchire" i dati BLOB con l'IA

*L'arricchimento dell'iaaalogica* fa parte dell'architettura di indicizzazione di Ricerca cognitiva di Azure che integra l'ia incorporata di Microsoft o l'ia dell'iatura personalizzata fornita. Consente di implementare scenari end-to-end in cui è necessario elaborare blob (sia quelli esistenti che quelli nuovi man mano che vengono aggiornati, aprire tutti i formati di file per estrarre immagini e testo, estrarre le informazioni desiderate utilizzando varie funzionalità di IA e indicizzarli in un indice di ricerca per la ricerca veloce, il recupero e l'esplorazione. 

Gli input sono i BLOB, in un singolo contenitore, nell'archiviazione BLOB di Azure.Inputs are your blobs, in a single container, in Azure Blob storage. I BLOB possono essere quasi qualsiasi tipo di dati di tipo text o image. 

L'output è sempre un indice di ricerca, utilizzato per la ricerca rapida di testo, il recupero e l'esplorazione nelle applicazioni client. Inoltre, l'output può anche essere un *archivio informazioni* che proietta documenti arricchiti in BLOB di Azure o tabelle di Azure per l'analisi downstream in strumenti come Power BI o nei carichi di lavoro di analisi scientifica dei dati.

In mezzo è l'architettura della pipeline stessa. La pipeline si basa sulla funzionalità *dell'indicizzatore,* a cui è possibile assegnare un set di *competenze,* composto da una o più *competenze* che forniscono l'IA. Lo scopo della pipeline è produrre *documenti arricchiti* che entrano come contenuto non elaborato ma raccolgono strutture, contesto e informazioni aggiuntive durante lo spostamento nella pipeline. I documenti arricchiti vengono utilizzati durante l'indicizzazione per creare indici invertiti e altre strutture utilizzate nella ricerca full-text o nell'esplorazione e nell'analisi.

## <a name="start-with-services"></a>Inizia con i servizi

Sono necessari Ricerca cognitiva di Azure e archiviazione BLOB di Azure.You need Azure Cognitive Search and Azure Blob storage. All'interno dell'archiviazione BLOB è necessario un contenitore che fornisce contenuto di origine.

È possibile iniziare direttamente nella pagina del portale dell'account di archiviazione. Nella pagina di spostamento sinistra, in **Servizio BLOB** fare clic su Aggiungi **Ricerca cognitiva** di Azure per creare un nuovo servizio o selezionarne uno esistente. 

Dopo aver aggiunto Ricerca cognitiva di Azure all'account di archiviazione, è possibile seguire il processo standard per arricchire i dati in qualsiasi origine dati di Azure.Once you add Azure Cognitive Search to your storage account, you can follow the standard process to enrich data in any Azure data source. È consigliabile la procedura guidata **Importazione dati** in Ricerca cognitiva di Azure per una semplice introduzione iniziale all'arricchimento dell'ialivello. Questa guida introduttiva illustra i passaggi: Creare una pipeline di [arricchimento AI nel portale.](cognitive-search-quickstart-blob.md) 

Nelle sezioni seguenti verranno esaminati altri componenti e concetti.

## <a name="use-a-blob-indexer"></a>Usare un indicizzatore BLOBUse a Blob indexer

L'arricchimento dell'iA è un componente aggiuntivo per una pipeline di indicizzazione e in Ricerca cognitiva di Azure tali pipeline vengono compilate sopra un *indicizzatore.* Un indicizzatore è un servizio secondario in grado di riconoscere le origini dati dotato di logica interna per il campionamento dei dati, la lettura dei dati dei metadati, il recupero dei dati e la serializzazione dei dati da formati nativi in documenti JSON per l'importazione successiva. Gli indicizzatori vengono spesso utilizzati da soli per l'importazione, separati dall'ia-, ma se si desidera creare una pipeline di arricchimento AI, sarà necessario un indicizzatore e un set di competenze per condirlo. In questa sezione viene evidenziato l'indicizzatore. la sezione successiva si concentra sui set di competenze.

I BLOB in Archiviazione di Azure vengono indicizzati usando [l'indicizzatore](search-howto-indexing-azure-blob-storage.md)di archiviazione BLOB di Ricerca cognitiva di Azure.Blobs in Azure Storage are indexed using the Azure Cognitive Search Blob storage indexer . È possibile richiamare questo indicizzatore usando la procedura guidata **Importa dati,** un'API REST o .NET SDK. Nel codice si usa questo indicizzatore impostando il tipo e fornendo informazioni di connessione che includono un account di Archiviazione di Azure insieme a un contenitore BLOB. È possibile sottoinsiemi dei BLOB creando una directory virtuale, che è quindi possibile passare come parametro, oppure filtrando in base a un'estensione di file.

Un indicizzatore esegue il "cracking del documento", aprendo un BLOB per esaminare il contenuto. Dopo la connessione all'origine dati, è il primo passaggio nella pipeline. Per i dati BLOB, è qui che vengono rilevati PDF, documenti d'ufficio, immagini e altri tipi di contenuto. Il cracking di documenti con l'estrazione del testo è gratuito. Documento cracking con l'estrazione dell'immagine è addebitato a i tassi che si possono trovare sulla pagina dei [prezzi](https://azure.microsoft.com/pricing/details/search/).

Anche se tutti i documenti saranno incrinati, l'arricchimento si verifica solo se fornisci esplicitamente le competenze per farlo. Ad esempio, se la pipeline è costituita esclusivamente dall'analisi dell'immagine, il testo nel contenitore o nei documenti viene ignorato.

L'indicizzatore BLOB viene fornito con i parametri di configurazione e supporta il rilevamento delle modifiche se i dati sottostanti forniscono informazioni sufficienti. Per altre informazioni sulle funzionalità di base [dell'indicizzatore](search-howto-indexing-azure-blob-storage.md)di archiviazione BLOB di Ricerca cognitiva di Azure, vedere .

## <a name="add-ai-components"></a>Aggiungere componenti AI

L'arricchimento dell'IA si riferisce ai moduli che cercano modelli o caratteristiche e quindi eseguono un'operazione di conseguenza. Il riconoscimento facciale nelle foto, le descrizioni di testo delle foto, il rilevamento di frasi chiave in un documento e l'OCR (o il riconoscimento di testo stampato o scritto a mano in file binari) sono esempi illustrativi.

In Ricerca cognitiva di Azure *le competenze* sono i singoli componenti dell'elaborazione dell'iA che è possibile usare in modo autonomo o in combinazione con altre competenze. 

+ Le competenze integrate sono supportate da Servizi cognitivi, con analisi delle immagini basate su Visione artificiale e elaborazione del linguaggio naturale basata su Analisi del testo. Per l'elenco completo, consultate [Competenze predefinite per l'arricchimento](cognitive-search-predefined-skills.md)dei contenuti.

+ Le competenze personalizzate sono codice personalizzato, racchiuso in una definizione di interfaccia che consente [l'integrazione](cognitive-search-custom-skill-interface.md) nella pipeline. Nelle soluzioni dei clienti, è pratica comune utilizzare entrambi, con competenze personalizzate che forniscono moduli di ia'open-source, di terze parti o di prima parte.

Un set di *competenze* è la raccolta di competenze utilizzate in una pipeline e viene richiamato dopo che la fase di cracking del documento rende disponibile il contenuto. Un indicizzatore può utilizzare esattamente un set di competenze, ma tale set di competenze esiste indipendentemente da un indicizzatore in modo da poterlo riutilizzare in altri scenari.

Le competenze personalizzate possono sembrare complesse, ma possono essere semplici e semplici in termini di implementazione. Se si dispone di pacchetti esistenti che forniscono modelli di criteri di ricerca o modelli di classificazione, il contenuto estratto dai BLOB potrebbe essere passato a questi modelli per l'elaborazione. Poiché l'arricchimento dell'iaaala è basato su Azure, anche il modello deve essere in Azure.Since AI enrichment is Azure-based, your model should be on Azure also. Alcune metodologie di hosting comuni includono l'uso di funzioni di [Azure](cognitive-search-create-custom-skill-example.md) o [contenitori](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).

Le competenze predefinite supportate da Servizi cognitivi richiedono un codice di sottoscrizione all-in-one [di Servizi cognitivi collegato](cognitive-search-attach-cognitive-services.md) che consente di accedere alla risorsa. Un tasto all-in-one offre l'analisi delle immagini, il rilevamento della lingua, la traduzione del testo e l'analisi del testo. Altre competenze predefinite sono funzionalità di Ricerca cognitiva di Azure e non richiedono alcun servizio o chiave aggiuntiva. Lo shaper di testo, la barra di divisione e la fusione sono esempi di competenze di supporto che talvolta sono necessarie durante la progettazione della pipeline.

Se si utilizzano solo competenze personalizzate e competenze di utilità incorporate, non vi è alcuna dipendenza o costi correlati ai servizi cognitivi.

<!-- ## Order of operations

Now we've covered indexers, content extraction, and skills, we can take a closer look at pipeline mechanisms and order of operations.

A skillset is a composition of one or more skills. When multiple skills are involved, the skillset operates as sequential pipeline, producing dependency graphs, where output from one skill becomes input to another. 

For example, given a large blob of unstructured text, a sample order of operations for text analytics might be as follows:

1. Use Text Splitter to break the blob into smaller parts.
1. Use Language Detection to determine if content is English or another language.
1. Use Text Translator to get all text into a common language.
1. Run Entity Recognition, Key Phrase Extraction, or Sentiment Analysis on chunks of text. In this step, new fields are created and populated. Entities might be location, people, organization, dates. Key phrases are short combinations of words that appear to belong together. Sentiment score is a rating on continuum of negative (0) to positive (1) sentiment.
1. Use Text Merger to reconstitute the document from the smaller chunks. -->

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>Consumare output arricchito con arità di iA in soluzioni a valle

L'output dell'arricchimento dell'iaformazione aipreliminari è un indice di ricerca in Ricerca cognitiva di Azure o un [archivio informazioni](knowledge-store-concept-intro.md) in Archiviazione di Azure.The output of AI enrichment is either a search index on Azure Cognitive Search, or a knowledge store in Azure Storage.

In Ricerca cognitiva di Azure viene usato un indice di ricerca per l'esplorazione interattiva usando testo libero e query filtrate in un'app client. I documenti arricchiti creati tramite l'iaacreata vengono formattati in JSON e indicizzati nello stesso modo in cui tutti i documenti vengono indicizzati in Ricerca cognitiva di Azure, sfruttando tutti i vantaggi offerti da un indicizzatore. Ad esempio, durante l'indicizzazione, l'indicizzatore BLOB fa riferimento ai parametri e alle impostazioni di configurazione per usare qualsiasi mapping dei campi o logica di rilevamento delle modifiche. Tali impostazioni sono completamente disponibili per l'indicizzazione regolare e i carichi di lavoro arricchiti aimateriali. Dopo l'indicizzazione, quando il contenuto viene archiviato in Ricerca cognitiva di Azure, è possibile creare query avanzate ed espressioni di filtro per comprendere il contenuto.

In Archiviazione di Azure un archivio informazioni ha due manifestazioni: un contenitore BLOB o tabelle nell'archiviazione tabelle. 

+ Un contenitore BLOB acquisisce i documenti arricchiti nella loro interezza, il che è utile se si vuole inserire altri processi. 

+ Al contrario, l'archiviazione tabelle può contenere proiezioni fisiche di documenti arricchiti. È possibile creare sezioni o livelli di documenti arricchiti che includono o escludono parti specifiche. Per l'analisi in Power BI, le tabelle nell'archiviazione tabelle di Azure diventano l'origine dati per un'ulteriore visualizzazione ed esplorazione.

Un documento arricchito alla fine della pipeline differisce dalla versione di input originale per la presenza di campi aggiuntivi contenenti nuove informazioni estratte o generate durante l'arricchimento. Di conseguenza, è possibile lavorare con una combinazione di contenuto originale e creato, indipendentemente dalla struttura di output utilizzata.

## <a name="next-steps"></a>Passaggi successivi

L'arricchimento dell'iaformazione a livello di io è molto più possibile per ottenere il massimo dai dati in Archiviazione di Azure, inclusa la combinazione di servizi cognitivi in modi diversi e la creazione di competenze personalizzate per i casi in cui non esiste alcun servizio cognitivo esistente per lo scenario. Per saperne di più, seguire i link qui sotto.

+ [Caricare, scaricare ed elencare BLOB con il portale di Azure (Archiviazione BLOB di Azure)Upload, download, and list blobs with the Azure portal (Azure Blob storage)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Configurare un indicizzatore BLOB (Ricerca cognitiva di Azure)Set up a blob indexer (Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md) 
+ [Panoramica dell'arricchimento dell'iA (Ricerca cognitiva di Azure)AI enrichment overview (Azure Cognitive Search)](cognitive-search-concept-intro.md) 
+ [Creare un set di competenze (Ricerca cognitiva di Azure)Create a skillset (Azure Cognitive Search)](cognitive-search-defining-skillset.md)
+ [Eseguire la mappa dei nodi in un albero delle annotazioni (Ricerca cognitiva di Azure)Map nodes in an annotation tree (Azure Cognitive Search)](cognitive-search-output-field-mapping.md)
