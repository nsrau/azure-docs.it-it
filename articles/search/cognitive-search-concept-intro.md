---
title: Concetti relativi all'arricchimento di intelligenza artificiale
titleSuffix: Azure Cognitive Search
description: L'estrazione del contenuto, l'elaborazione del linguaggio naturale (PNL) e l'elaborazione di immagini vengono usate per creare contenuto ricercabile in Azure ricerca cognitiva indici con competenze cognitive predefinite e algoritmi di intelligenza artificiale personalizzati.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/18/2020
ms.openlocfilehash: 09e7a39a2d97626dd01a00fdaef9bc4d711d557b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91828085"
---
# <a name="ai-enrichment-in-azure-cognitive-search"></a>Arricchimento tramite intelligenza artificiale con Ricerca cognitiva di Azure

L'arricchimento di intelligenza artificiale è un'estensione di [indicizzatori](search-indexer-overview.md) che possono essere usati per estrarre il testo da immagini, BLOB e altre origini dati non strutturate. Arricchimento ed estrazione rendono più ricercabili i contenuti negli oggetti di output dell'indicizzatore, ovvero un [indice di ricerca](search-what-is-an-index.md) o un [Archivio informazioni](knowledge-store-concept-intro.md). 

L'estrazione e l'arricchimento vengono implementate usando le *competenze cognitive* associate alla pipeline basata sull'indicizzatore. È possibile usare le competenze predefinite di Microsoft o incorporare l'elaborazione esterna in un' [*abilità personalizzata*](cognitive-search-create-custom-skill-example.md) creata dall'utente. Esempi di competenze personalizzate possono essere un modulo di entità o un classificatore di documenti personalizzato destinato a un dominio specifico, ad esempio finanza, pubblicazioni scientifiche o medicine.

Le competenze predefinite rientrano nelle categorie seguenti: 

+ Le competenze di **elaborazione in linguaggio naturale** includono il [riconoscimento di entità](cognitive-search-skill-entity-recognition.md), il [rilevamento della lingua](cognitive-search-skill-language-detection.md), l'[estrazione delle frasi chiave](cognitive-search-skill-keyphrases.md), la modifica del testo, il [rilevamento delle valutazioni](cognitive-search-skill-sentiment.md) e il [rilevamento delle informazioni personali](cognitive-search-skill-pii-detection.md). Con queste competenze, il testo non strutturato viene mappato come campi ricercabili e filtrabili in un indice.

+ Le competenze di **elaborazione delle immagini** includono il [riconoscimento ottico dei caratteri (OCR)](cognitive-search-skill-ocr.md) e l'identificazione delle [funzionalità visive](cognitive-search-skill-image-analysis.md), ad esempio il rilevamento facciale, l'interpretazione delle immagini, il riconoscimento delle immagini (persone famose e i punti di riferimento) o attributi come l' Queste competenze creano rappresentazioni di testo di contenuto immagine, rendendole disponibili per la ricerca tramite le funzionalità di query di Azure ricerca cognitiva.

![Diagramma della pipeline di arricchimento](./media/cognitive-search-intro/cogsearch-architecture.png "panoramica della pipeline di arricchimento")

Le competenze predefinite in Azure ricerca cognitiva si basano su modelli di apprendimento automatico pre-addestrati in API Servizi cognitivi: [visione artificiale](../cognitive-services/computer-vision/index.yml) e [analisi del testo](../cognitive-services/text-analytics/overview.md). È possibile aggiungere una risorsa Servizi cognitivi se si desidera sfruttare queste risorse durante l'elaborazione del contenuto.

Il linguaggio naturale e l'elaborazione delle immagini vengono applicati durante la fase di inserimento dei dati e i risultati diventano parte della composizione di un documento in un indice ricercabile in Ricerca cognitiva di Azure. I dati vengono originati come set di dati di Azure e quindi attraverso una pipeline di indicizzazione che usa le [competenze predefinite](cognitive-search-predefined-skills.md) necessarie.  

## <a name="when-to-use-ai-enrichment"></a>Quando usare l'arricchimento di intelligenza artificiale

È consigliabile usare le competenze cognitive predefinite se il contenuto non elaborato è un testo non strutturato, un contenuto di immagini o un contenuto che richiede il rilevamento e la traduzione della lingua. L'applicazione di intelligenza artificiale attraverso le competenze cognitive predefinite può sbloccare questo contenuto, aumentandone il valore e l'utilità nelle app di ricerca e data science. 

Inoltre, è consigliabile aggiungere una competenza personalizzata se si dispone di codice open source interno o di terze parti da integrare nella pipeline. I modelli di classificazione che identificano le caratteristiche salienti di diversi tipi di documenti rientrano in questa categoria, ma è possibile usare qualsiasi pacchetto che aggiunga valore al contenuto.

### <a name="more-about-built-in-skills"></a>Altre informazioni sulle competenze predefinite

Un insieme di [competenze](cognitive-search-defining-skillset.md) assemblato con competenze predefinite è particolarmente adatto per gli scenari di applicazione seguenti:

+ Documenti analizzati (JPEG) che si vogliono rendere disponibili per la ricerca full-text. È possibile collegare una competenza di riconoscimento ottico dei caratteri (OCR) per identificare, estrarre e inserire testo da file JPEG.

+ PDF con una combinazione di immagini e testo. È possibile estrarre il testo nei file PDF durante l'indicizzazione senza usare passaggi di arricchimento, ma l'aggiunta dell'elaborazione di immagini e linguaggio naturale consente in genere di ottenere un risultato migliore rispetto a un'indicizzazione standard.

+ Contenuto multilingue per cui si vuole applicare il rilevamento della lingua e possibilmente una traduzione testuale.

+ Documenti non strutturati o semistrutturati con contenuto che ha un significato intrinseco o un contesto nascosto nel documento più grande. 

  I BLOB in particolare contengono spesso un grande corpo di contenuto che viene compresso in un singolo campo. Collegando competenze di elaborazione di immagini e linguaggio naturale all'indicizzatore, è possibile creare nuove informazioni ancora esistenti nel contenuto non elaborato ma non altrimenti visualizzate come campi distinti. Alcune competenze cognitive predefinite che possono essere utili: estrazione di frasi chiave, analisi del sentiment e riconoscimento di entità (persone, organizzazioni e località).

  Inoltre, le competenze predefinite possono essere usate anche per ristrutturare il contenuto tramite operazioni di divisione, unione e modellazione del testo.

### <a name="more-about-custom-skills"></a>Altre informazioni sulle competenze personalizzate

Le competenze personalizzate possono supportare scenari più complessi, ad esempio il riconoscimento di moduli o il rilevamento di entità personalizzate tramite un modello fornito e inserito nell'[interfaccia Web di competenze personalizzate](cognitive-search-custom-skill-interface.md). Alcuni esempi di competenze personalizzate includono [Riconoscimento modulo](../cognitive-services/form-recognizer/overview.md), l'integrazione dell'[API Ricerca entità Bing](./cognitive-search-create-custom-skill-example.md) e il [riconoscimento di entità personalizzate](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).

## <a name="steps-in-an-enrichment-pipeline"></a>Passaggi in una pipeline di arricchimento <a name="enrichment-steps"></a>

Una pipeline di arricchimento è basata sugli [*indicizzatori*](search-indexer-overview.md). Gli indicizzatori popolano un indice in base ai mapping da campo a campo tra l'indice e l'origine dati per il cracking del documento. Le competenze, ora associate agli indicizzatori, intercettano e arricchiscono i documenti secondo le competenze definite. Terminata l'indicizzazione è possibile accedere al contenuto tramite richieste di ricerca usando tutti i [tipi di query supportati da Ricerca cognitiva di Azure](search-query-overview.md).  Se non si ha familiarità con gli indicizzatori, in questa sezione viene illustrata la procedura da seguire.

### <a name="step-1-connection-and-document-cracking-phase"></a>Passaggio 1: fase di connessione e di cracking del documento

All'inizio della pipeline è presente un testo non strutturato o contenuto non di testo, ad esempio immagini, documenti analizzati o file JPEG. I dati devono esistere in un servizio di archiviazione dati di Azure a cui è possibile accedere tramite un indicizzatore. Gli indicizzatori possono "individuare" i documenti di origine per estrarre il testo dai dati di origine. Il cracking dei documenti è il processo di estrazione o creazione di contenuto di testo da origini non di testo durante l'indicizzazione.

![Fase di cracking dei documenti](./media/cognitive-search-intro/document-cracking-phase-blowup.png "cracking di documenti")

 Le origini supportate includono archiviazione BLOB di Azure, archiviazione tabelle di Azure, database SQL di Azure e Azure Cosmos DB. I contenuti testuali possono essere estratti dai seguenti tipi di file: PDF, Word, PowerPoint e file CSV. Per un elenco completo, vedere [Formati supportati](search-howto-indexing-azure-blob-storage.md#SupportedFormats). L'indicizzazione richiede tempo; si consiglia quindi di iniziare con un set di dati piccolo ma rappresentativo, per poi compilarlo in modo incrementale con l'evolversi della soluzione.

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>Passaggio 2: competenze cognitive e fase di arricchimento

L'arricchimento viene eseguito con le *competenze cognitive* che eseguono operazioni atomiche. Ad esempio, dopo aver eseguito il cracking di un file PDF, è possibile applicare il riconoscimento delle entità, il rilevamento della lingua o l'estrazione della frase chiave per produrre nuovi campi nell'indice che non sono disponibili in modalità nativa nell'origine. Nel complesso, la raccolta di competenze usata nella pipeline viene chiamata *set di competenze*.  

![Fase di arricchimento](./media/cognitive-search-intro/enrichment-phase-blowup.png "fase di arricchimento")

Un set di competenze si basa su [competenze cognitive predefinite](cognitive-search-predefined-skills.md) o [competenze personalizzate](cognitive-search-create-custom-skill-example.md) fornite e connesse al set di competenze. Un set di competenze può essere minimamente o particolarmente complesso e determina non solo il tipo di elaborazione, ma anche l'ordine delle operazioni. Un set di competenze, assieme ai mapping dei campi definiti come parte di un indicizzatore, specifica l'intera pipeline di arricchimento. Per altre informazioni sul raggruppamento di tutte queste parti, vedere [Definire un set di competenze](cognitive-search-defining-skillset.md).

Internamente, la pipeline genera una raccolta di documenti arricchiti. È possibile decidere quali parti dei documenti arricchiti devono essere mappate ai campi indicizzabili contenuti nell'indice di ricerca. Se, ad esempio, è stata applicata l'estrazione della frase chiave e le competenze per il riconoscimento delle entità, i nuovi campi diventeranno parte del documento arricchito ed è possibile eseguirne il mapping ai campi dell'indice. Vedere [Annotazioni](cognitive-search-concept-annotations-syntax.md) per altre informazioni sulle formazioni in ingresso/uscita.

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>Aggiungere un elemento knowledgeStore per salvare gli arricchimenti

[Search REST API-Version = 2020-06-30](/rest/api/searchservice/) estende skillsets con una `knowledgeStore` definizione che fornisce una connessione di archiviazione di Azure e proiezioni che descrivono il modo in cui vengono archiviati gli arricchimenti. Questa operazione è aggiunta all'indice. In una pipeline di intelligenza artificiale standard, i documenti arricchiti sono temporanei, ovvero vengono usati solo durante l'indicizzazione per poi essere rimossi. Con l'archivio conoscenze, i documenti arricchiti vengono conservati. Per ulteriori informazioni, vedere [Knowledge Store](knowledge-store-concept-intro.md).

### <a name="step-3-search-index-and-query-based-access"></a>Passaggio 3: eseguire ricerche nell'indice e nell'accesso basato su query

Al termine dell'elaborazione, si avrà un indice di ricerca costituito da documenti arricchiti in cui è possibile eseguire ricerche di testo in Ricerca cognitiva di Azure. [L'esecuzione di query dell'indice](search-query-overview.md) consente a utenti e sviluppatori di accedere al contenuto arricchito generato dalla pipeline. 

![Indice con icona di ricerca](./media/cognitive-search-intro/search-phase-blowup.png "Indice con icona di ricerca")

L'indice è simile a qualsiasi altro indice creato per Ricerca cognitiva di Azure: è possibile integrare analizzatori personalizzati, richiamare query di ricerca fuzzy, aggiungere ricerche con filtri o sperimentare profili di punteggio per rimodellare i risultati della ricerca.

Gli indici vengono generati da uno schema dell'indice che definisce i campi, gli attributi e altri costrutti allegati a un indice specifico, come ad esempio profili di punteggio e mapping dei sinonimi. Una volta definito e popolato un indice, è possibile eseguire l'indicizzazione in modo incrementale per caricare documenti di origine nuovi e aggiornati. Alcune modifiche apportate richiedono una ricompilazione completa. È consigliabile usare un set di dati ridotto fino a quando la struttura dello schema non è stabile. Per altre informazioni, vedere [How to rebuild an index](search-howto-reindex.md) (Procedura per ricompilare un indice).

**Elenco di controllo: un flusso di lavoro tipico**

1. Includere in un sottoinsieme i dati di origine di Azure in un campione rappresentativo. L'indicizzazione richiede tempo; si consiglia quindi di iniziare con un set di dati piccolo ma rappresentativo, per poi compilarlo in modo incrementale con l'evolversi della soluzione.

1. Creare un [oggetto di origine dati](/rest/api/searchservice/create-data-source) in Ricerca cognitiva di Azure per fornire una stringa di connessione per il recupero dati.

1. Creare un [set di competenze](/rest/api/searchservice/create-skillset) con una procedura di arricchimento.

1. Definire lo [schema dell'indice](/rest/api/searchservice/create-index). La raccolta *Campi* include campi provenienti dall'origine dati. È necessario anche disattivare i campi aggiuntivi per mantenere i valori generati per il contenuto creato durante l'arricchimento.

1. Definire l'[indicizzatore](/rest/api/searchservice/create-indexer) che fa riferimento all'origine dati, al set di competenze e all'indice.

1. All'interno dell'indicizzatore, aggiungere *outputFieldMappings*. In questa sezione viene eseguito il mapping dell'output dal set di competenze (nel passaggio 3) ai campi di input nello schema dell'indice (nel passaggio 4).

1. Inviare la richiesta *Crea l'indicizzatore* appena creata (una richiesta POST con una definizione dell'indicizzatore nel corpo della richiesta) per esprimere l'indicizzatore in Ricerca cognitiva di Azure. Questo passaggio descrive come viene eseguito l'indicizzatore, richiamando la pipeline.

1. Eseguire query per valutare i risultati e modificare il codice per aggiornare i set di competenze, lo schema o la configurazione dell'indicizzatore.

1. [Reimpostare l'indicizzatore](search-howto-reindex.md) prima di ricompilare la pipeline.

## <a name="next-steps"></a>Passaggi successivi

+ [Collegamenti alla documentazione per l'arricchimento tramite intelligenza artificiale](cognitive-search-resources-documentation.md)
+ [Esempio: creazione di un'abilità personalizzata per l'arricchimento di intelligenza artificiale (C#)](cognitive-search-create-custom-skill-example.md)
+ [Guida introduttiva: provare a arricchire l'intelligenza artificiale in una procedura dettagliata per il portale](cognitive-search-quickstart-blob.md)
+ [Esercitazione: informazioni sulle API di arricchimento intelligenza artificiale](cognitive-search-tutorial-blob.md)
+ [Archivio conoscenze](knowledge-store-concept-intro.md)
+ [Creare un archivio conoscenze in REST](knowledge-store-create-rest.md)
+ [Suggerimenti per la risoluzione dei problemi](cognitive-search-concept-troubleshooting.md)
