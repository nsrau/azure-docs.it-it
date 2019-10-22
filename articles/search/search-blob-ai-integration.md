---
title: Usare l'intelligenza artificiale per comprendere i dati di archiviazione BLOB
titleSuffix: Azure Search
description: Aggiungere un'analisi semantica, di elaborazione del linguaggio naturale e di immagini ai BLOB di Azure usando una pipeline di arricchimento di intelligenza artificiale in ricerca di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 1a8840aca71daacc08cf85b178668e42ff9f796c
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678424"
---
# <a name="use-ai-to-understand-blob-storage-data"></a>Usare l'intelligenza artificiale per comprendere i dati di archiviazione BLOB

I dati nell'archiviazione BLOB di Azure spesso sono un'ampia gamma di contenuto non strutturato, ad esempio immagini, testo lungo, PDF e documenti di Office. Usando le funzionalità di intelligenza artificiale in ricerca di Azure, è possibile comprendere ed estrarre informazioni utili dai BLOB in diversi modi. Esempi di applicazione dell'intelligenza artificiale al contenuto BLOB includono:

+ Estrai il testo dalle immagini usando il riconoscimento ottico dei caratteri (OCR)
+ Produrre una descrizione della scena o i tag da una foto
+ Rilevare la lingua e tradurre il testo in lingue diverse
+ Elaborare il testo con il riconoscimento delle entità denominate (NER) per trovare riferimenti a persone, date, posizioni o organizzazioni 

Sebbene possa essere necessaria solo una di queste funzionalità di intelligenza artificiale, è comune combinare più di essi nella stessa pipeline (ad esempio, estraendo testo da un'immagine digitalizzata e quindi individuando tutte le date e le posizioni a cui viene fatto riferimento). 

L'arricchimento di intelligenza artificiale crea nuove informazioni, acquisite come testo, memorizzate nei campi. Dopo l'arricchimento, è possibile accedere a queste informazioni da un indice di ricerca tramite la ricerca full-text oppure inviare documenti arricchiti ad archiviazione di Azure per potenziare le nuove esperienze che includono l'esplorazione dei dati per gli scenari di rilevamento o di analisi. 

In questo articolo viene illustrato come arricchire l'intelligenza artificiale tramite un grandangolo, in modo che sia possibile comprendere rapidamente l'intero processo, dalla trasformazione dei dati non elaborati nei BLOB, alle informazioni Queryable in un indice di ricerca o in un archivio informazioni.

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>Cosa significa "arricchire" i dati BLOB con intelligenza artificiale

L' *arricchimento di intelligenza artificiale* fa parte dell'architettura di indicizzazione di ricerca di Azure che integra l'intelligenza artificiale integrata di Microsoft o di intelligenza artificiale personalizzata fornita dall'utente. Consente di implementare scenari end-to-end in cui è necessario elaborare i BLOB (sia quelli esistenti che quelli nuovi Man mano che vengono aggiornati), crack aprono tutti i formati di file per estrarre immagini e testo, estrarre le informazioni desiderate usando varie funzionalità di intelligenza artificiale e indicizzarli in un indice di ricerca di Azure per la ricerca veloce, il recupero e l'esplorazione. 

Gli input sono i BLOB, in un singolo contenitore, nell'archivio BLOB di Azure. I BLOB possono essere quasi qualsiasi tipo di dati di testo o di immagine. 

L'output è sempre un indice di ricerca di Azure, usato per la ricerca di testo veloce, il recupero e l'esplorazione nelle applicazioni client. Inoltre, l'output può essere anche un *Archivio informazioni* che proietta documenti arricchiti in BLOB di Azure o tabelle di Azure per l'analisi downstream in strumenti come Power bi o in carichi di lavoro Data Science.

In between è l'architettura della pipeline stessa. La pipeline è basata sulla funzionalità *indicizzatore* , a cui è possibile assegnare un *skillt*, che è costituita da una o più *competenze* che forniscono l'intelligenza artificiale. Lo scopo della pipeline è quello di produrre *documenti arricchiti* che vengono immessi come contenuti non elaborati, ma prelevano strutture, contesto e informazioni aggiuntive mentre si passano attraverso la pipeline. I documenti arricchiti vengono utilizzati durante l'indicizzazione per creare indici invertiti e altre strutture utilizzate nella ricerca full-text o nell'esplorazione e nell'analisi.

## <a name="start-with-services"></a>Inizia a usare i servizi

Sono necessari ricerca di Azure e l'archiviazione BLOB di Azure. All'interno dell'archiviazione BLOB è necessario un contenitore che fornisca contenuto di origine.

È possibile iniziare direttamente nella pagina del portale dell'account di archiviazione. Nella pagina di spostamento a sinistra, in **servizio BLOB** fare clic su **Aggiungi ricerca di Azure** per creare un nuovo servizio o selezionarne uno esistente. 

Dopo l'aggiunta di ricerca di Azure all'account di archiviazione, è possibile seguire il processo standard per arricchire i dati in qualsiasi origine dati di Azure. È consigliabile usare la procedura guidata **Importa dati** in ricerca di Azure per una semplice introduzione iniziale all'arricchimento di intelligenza artificiale. Questa Guida introduttiva illustra la procedura [di creazione di una pipeline di arricchimento ai nel portale](cognitive-search-quickstart-blob.md). 

Nelle sezioni seguenti verranno esaminati altri componenti e concetti.

## <a name="use-a-blob-indexer"></a>Usare un indicizzatore BLOB

L'arricchimento AI è un componente aggiuntivo di una pipeline di indicizzazione e in ricerca di Azure queste pipeline sono basate su un *indicizzatore*. Un indicizzatore è un sottoservizio in grado di riconoscere le origini dati dotato di logica interna per il campionamento dei dati, la lettura dei dati dei metadati, il recupero dei dati e la serializzazione dei dati dai formati nativi ai documenti JSON per l'importazione successiva. Gli indicizzatori vengono spesso usati da soli per l'importazione, separati dall'intelligenza artificiale, ma se si vuole creare una pipeline di arricchimento intelligenza artificiale, è necessario un indicizzatore e un insieme di competenze da usare. In questa sezione viene evidenziato l'indicizzatore; la sezione successiva è incentrata su skillsets.

I BLOB in archiviazione di Azure vengono indicizzati usando l' [indicizzatore di archiviazione BLOB di ricerca di Azure](search-howto-indexing-azure-blob-storage.md). È possibile richiamare questo indicizzatore usando la procedura guidata **Importa dati** , un'API REST o .NET SDK. Nel codice, questo indicizzatore viene usato impostando il tipo e fornendo le informazioni di connessione che includono un account di archiviazione di Azure insieme a un contenitore BLOB. È possibile sottoscrivere i BLOB creando una directory virtuale, che può quindi essere passata come parametro o filtrando in base a un'estensione del tipo di file.

Un indicizzatore esegue il "cracking del documento", aprendo un BLOB per esaminare il contenuto. Dopo la connessione all'origine dati, è il primo passaggio della pipeline. Per i dati BLOB, questo è il percorso in cui vengono rilevati PDF, documenti di Office, immagini e altri tipi di contenuto. Il cracking dei documenti con estrazione del testo non prevede alcun addebito. Il cracking di documenti con estrazione di immagini viene addebitato in base alle tariffe disponibili nella [pagina dei prezzi](https://azure.microsoft.com/pricing/details/search/)di ricerca di Azure.

Sebbene tutti i documenti vengano violati, l'arricchimento si verifica solo se si specificano in modo esplicito le competenze necessarie. Ad esempio, se la pipeline è costituita esclusivamente dall'analisi delle immagini, il testo del contenitore o dei documenti viene ignorato.

L'indicizzatore BLOB viene fornito con i parametri di configurazione e supporta il rilevamento delle modifiche se i dati sottostanti forniscono informazioni sufficienti. È possibile ottenere altre informazioni sulle funzionalità di base nell' [indicizzatore di archiviazione BLOB di ricerca di Azure](search-howto-indexing-azure-blob-storage.md).

## <a name="add-ai-components"></a>Aggiungere componenti di intelligenza artificiale

L'arricchimento di intelligenza artificiale si riferisce a moduli che cercano modelli o caratteristiche, quindi eseguono un'operazione di conseguenza. I riconoscimenti facciali nelle foto, le descrizioni di testo delle foto, il rilevamento di frasi chiave in un documento e l'OCR (o il riconoscimento di testo stampato o scritto in file binari) sono esempi illustrativi.

In ricerca di Azure le *competenze* sono i singoli componenti dell'elaborazione di intelligenza artificiale che è possibile usare autonomamente o in combinazione con altre competenze. 

+ Le competenze predefinite sono supportate da servizi cognitivi, con analisi delle immagini basate su Visione artificiale e elaborazione del linguaggio naturale basata su Analisi del testo. È possibile esaminare l'elenco completo delle competenze predefinite in competenze predefinite [per l'arricchimento del contenuto](cognitive-search-predefined-skills.md).

+ Le competenze personalizzate sono codice personalizzato, racchiuso in una [definizione di interfaccia](cognitive-search-custom-skill-interface.md) che consente l'integrazione nella pipeline. Nelle soluzioni dei clienti, è prassi comune usare entrambi i moduli, con competenze personalizzate che forniscono moduli open source, di terze parti o della prima parte.

Un insieme di *competenze* è la raccolta di competenze usate in una pipeline e viene richiamato dopo che la fase di cracking del documento rende disponibile il contenuto. Un indicizzatore può utilizzare esattamente un solo skillt, ma tale skillt esiste indipendentemente da un indicizzatore, in modo che sia possibile riutilizzarlo in altri scenari.

Le competenze personalizzate possono sembrare complesse ma possono essere semplici e semplici in termini di implementazione. Se sono presenti pacchetti che forniscono criteri di ricerca o modelli di classificazione, il contenuto estratto dai BLOB potrebbe essere passato a questi modelli per l'elaborazione. Poiché l'arricchimento AI è basato su Azure, il modello deve essere anche in Azure. Alcune metodologie di hosting comuni includono l'uso di funzioni o [contenitori](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)di [Azure](cognitive-search-create-custom-skill-example.md) .

Le competenze predefinite supportate dai servizi cognitivi richiedono una chiave di sottoscrizione all-in-One [connessa di servizi cognitivi](cognitive-search-attach-cognitive-services.md) che consente di accedere alla risorsa. Una chiave all-in-One ti offre analisi delle immagini, rilevamento della lingua, traduzione testuale e analisi del testo. Altre competenze predefinite sono funzionalità di ricerca di Azure e non richiedono un servizio o una chiave aggiuntiva. Il formato di testo, la barra di divisione e la fusione sono esempi di competenze di supporto talvolta necessarie per la progettazione della pipeline.

Se si usano solo competenze personalizzate e competenze di utilità predefinite, non esistono dipendenze o costi correlati ai servizi cognitivi.

<!-- ## Order of operations

Now we've covered indexers, content extraction, and skills, we can take a closer look at pipeline mechanisms and order of operations.

A skillset is a composition of one or more skills. When multiple skills are involved, the skillset operates as sequential pipeline, producing dependency graphs, where output from one skill becomes input to another. 

For example, given a large blob of unstructured text, a sample order of operations for text analytics might be as follows:

1. Use Text Splitter to break the blob into smaller parts.
1. Use Language Detection to determine if content is English or another language.
1. Use Text Translator to get all text into a common language.
1. Run Entity Recognition, Key Phrase Extraction, or Sentiment Analysis on chunks of text. In this step, new fields are created and populated. Entities might be location, people, organization, dates. Key phrases are short combinations of words that appear to belong together. Sentiment score is a rating on continuum of negative (0) to positive (1) sentiment.
1. Use Text Merger to reconstitute the document from the smaller chunks. -->

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>Utilizzare l'output arricchito con intelligenza artificiale nelle soluzioni downstream

L'output dell'arricchimento AI è costituito da un indice di ricerca in ricerca di Azure o da un [Archivio informazioni](knowledge-store-concept-intro.md) in archiviazione di Azure.

In ricerca di Azure un indice di ricerca viene usato per l'esplorazione interattiva usando testo libero e query filtrate in un'app client. I documenti arricchiti creati tramite intelligenza artificiale vengono formattati in JSON e indicizzati nello stesso modo in cui tutti i documenti vengono indicizzati in ricerca di Azure, sfruttando tutti i vantaggi offerti da un indicizzatore. Ad esempio, durante l'indicizzazione, l'indicizzatore BLOB fa riferimento ai parametri di configurazione e alle impostazioni per utilizzare i mapping dei campi o la logica di rilevamento delle modifiche. Tali impostazioni sono completamente disponibili per l'indicizzazione regolare e i carichi di lavoro arricchiti con intelligenza artificiale. Dopo l'indicizzazione, quando il contenuto viene archiviato in ricerca di Azure, è possibile compilare query e espressioni di filtro avanzate per comprendere il contenuto.

In archiviazione di Azure, in un archivio informazioni sono presenti due manifesti: un contenitore BLOB o tabelle nell'archivio tabelle. 

+ Un contenitore BLOB acquisisce interamente i documenti arricchiti, che risulta utile se si desidera eseguire il feed in altri processi. 

+ L'archiviazione tabelle può invece contenere proiezioni fisiche di documenti arricchiti. È possibile creare sezioni o livelli di documenti arricchiti che includono o escludono parti specifiche. Per l'analisi in Power BI, le tabelle nell'archiviazione tabelle di Azure diventano l'origine dati per un'ulteriore visualizzazione ed esplorazione.

Un documento arricchito alla fine della pipeline è diverso dalla versione di input originale dalla presenza di campi aggiuntivi contenenti nuove informazioni estratte o generate durante l'arricchimento. Di conseguenza, è possibile utilizzare una combinazione di contenuto originale e creato, indipendentemente dalla struttura di output utilizzata.

## <a name="next-steps"></a>Passaggi successivi

È possibile eseguire molte altre operazioni con l'arricchimento di intelligenza artificiale per sfruttare al meglio i dati in archiviazione di Azure, tra cui combinare servizi cognitivi in modi diversi e creare competenze personalizzate per i casi in cui non esiste un servizio cognitivo esistente per lo scenario. Per altre informazioni, vedere i collegamenti seguenti.

+ [Caricare, scaricare ed elencare BLOB con il portale di Azure (archiviazione BLOB di Azure)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Configurare un indicizzatore BLOB (ricerca di Azure)](search-howto-indexing-azure-blob-storage.md) 
+ [Panoramica dell'arricchimento di intelligenza artificiale (ricerca di Azure)](cognitive-search-concept-intro.md) 
+ [Creare un skillt (ricerca di Azure)](cognitive-search-defining-skillset.md)
+ [Mappare i nodi in un albero delle annotazioni (ricerca di Azure)](cognitive-search-output-field-mapping.md)
