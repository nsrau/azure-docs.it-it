---
title: Introduzione all'arricchimento tramite intelligenza artificiale
titleSuffix: Azure Cognitive Search
description: Estrazione dei contenuti, elaborazione del linguaggio naturale ed elaborazione di immagini per creare contenuti ricercabili nell'indicizzazione di Ricerca cognitiva di Azure tramite competenze cognitive e algoritmi di intelligenza artificiale.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 11/04/2019
ms.openlocfilehash: 97622df578b6c1357601b32a22c806e9eef77c96
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466865"
---
# <a name="introduction-to-ai-in-azure-cognitive-search"></a>Introduzione all'intelligenza artificiale nella ricerca cognitiva di Azure

L'arricchimento tramite intelligenza artificiale è una funzionalità dell'indicizzazione di Ricerca cognitiva di Azure usata per estrarre il testo da immagini, BLOB e altre origini dati non strutturate, arricchendo il contenuto per renderlo maggiormente ricercabile in un indice o un archivio conoscenze. Estrazione e arricchimento vengono implementati tramite *competenze cognitive* associate a una pipeline di indicizzazione. Le competenze cognitive predefinite del servizio rientrano nelle categorie seguenti: 

+ Le competenze di **elaborazione in linguaggio naturale** includono il [riconoscimento di entità](cognitive-search-skill-entity-recognition.md), il [rilevamento della lingua](cognitive-search-skill-language-detection.md), l'[estrazione delle frasi chiave](cognitive-search-skill-keyphrases.md), la modifica del testo e il [rilevamento delle valutazioni](cognitive-search-skill-sentiment.md). Con queste competenze, il testo non strutturato può assumere nuove forme, eseguendone il mapping come campi ricercabili e filtrabili in un indice.

+ Le competenze di **elaborazione delle immagini** includono il [riconoscimento ottico dei caratteri (OCR)](cognitive-search-skill-ocr.md) e l'identificazione delle [caratteristiche visive](cognitive-search-skill-image-analysis.md), ad esempio il rilevamento del viso, l'interpretazione delle immagini o il loro riconoscimento (persone o luoghi famosi) o attributi come colori e orientamento. È possibile creare rappresentazioni testuali dei contenuti di immagini, ricercabili tramite tutte le funzionalità di query di Ricerca cognitiva di Azure.

![Diagramma della pipeline di arricchimento](./media/cognitive-search-intro/cogsearch-architecture.png "panoramica della pipeline di arricchimento")

Le competenze cognitive in Ricerca cognitiva di Azure si basano sui modelli di Machine Learning per le API Servizi cognitivi: [Visione artificiale](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) e [Analisi del testo](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview). 

Il linguaggio naturale e l'elaborazione delle immagini vengono applicati durante la fase di inserimento dei dati e i risultati diventano parte della composizione di un documento in un indice ricercabile in Ricerca cognitiva di Azure. I dati vengono originati come set di dati di Azure e quindi attraverso una pipeline di indicizzazione che usa le [competenze predefinite](cognitive-search-predefined-skills.md) necessarie. L'architettura è estendibile per permettere di creare e associare [competenze personalizzate](cognitive-search-create-custom-skill-example.md) per integrare l'elaborazione personalizzata nel caso in cui le competenze predefinite non siano sufficienti. Esempi possono essere un modulo di entità personalizzato o un classificatore di documenti destinato a un dominio specifico, ad esempio pubblicazioni finanziarie, scientifiche o mediche.

> [!NOTE]
> Se si espande l'ambito aumentando la frequenza di elaborazione, aggiungendo più documenti oppure aggiungendo altri algoritmi di intelligenza artificiale, sarà necessario [collegare una risorsa fatturabile di Servizi cognitivi](cognitive-search-attach-cognitive-services.md). Gli addebiti si accumulano quando si chiamano le API in Servizi cognitivi e per l'estrazione di immagini come parte della fase di cracking dei documenti in Ricerca cognitiva di Azure. Non sono previsti addebiti per l'estrazione di testo dai documenti.
>
> L'esecuzione delle competenze predefinite viene addebitata secondo gli attuali [prezzi con pagamento in base al consumo dei Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/). I prezzi per l'estrazione di immagini sono descritti nella [pagina dei prezzi di Ricerca cognitiva di Azure](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="when-to-use-cognitive-skills"></a>Quando usare le competenze cognitive

È consigliabile usare le competenze cognitive predefinite se il contenuto non elaborato è un testo non strutturato, un contenuto di immagini o un contenuto che richiede il rilevamento e la traduzione della lingua. L'applicazione di intelligenza artificiale attraverso le competenze cognitive predefinite può sbloccare questo contenuto, aumentandone il valore e l'utilità nelle app di ricerca e data science. 

Inoltre, è consigliabile aggiungere una competenza personalizzata se si dispone di codice open source interno o di terze parti da integrare nella pipeline. I modelli di classificazione che identificano le caratteristiche salienti di vari tipi di documenti rientrano in questa categoria, ma è anche possibile usare qualsiasi pacchetto che aggiunga valore al contenuto.

### <a name="more-about-built-in-skills"></a>Altre informazioni sulle competenze predefinite

Un set di competenze assemblato usando competenze predefinite è particolarmente indicato per gli scenari di applicazione seguenti:

+ Documenti analizzati (JPEG) che si vogliono rendere disponibili per la ricerca full-text. È possibile collegare una competenza di riconoscimento ottico dei caratteri (OCR) per identificare, estrarre e inserire testo da file JPEG.

+ PDF con una combinazione di immagini e testo. È possibile estrarre il testo nei file PDF durante l'indicizzazione senza usare passaggi di arricchimento, ma l'aggiunta dell'elaborazione di immagini e linguaggio naturale consente in genere di ottenere un risultato migliore rispetto a un'indicizzazione standard.

+ Contenuto multilingue per cui si vuole applicare il rilevamento della lingua e possibilmente una traduzione testuale.

+ Documenti non strutturati o semistrutturati con contenuto che ha un significato intrinseco o un contesto nascosto nel documento più grande. 

  I BLOB in particolare contengono spesso un corpo di contenuto di grandi dimensioni che viene compresso in un singolo "campo". Collegando competenze di elaborazione di immagini e linguaggio naturale all'indicizzatore, è possibile creare nuove informazioni ancora esistenti nel contenuto non elaborato ma non altrimenti visualizzate come campi distinti. Alcune competenze cognitive predefinite che possono essere utili: estrazione di frasi chiave, analisi del sentiment e riconoscimento di entità (persone, organizzazioni e località).

  Inoltre, le competenze predefinite possono essere usate anche per ristrutturare il contenuto tramite operazioni di divisione, unione e modellazione del testo.

### <a name="more-about-custom-skills"></a>Altre informazioni sulle competenze personalizzate

Le competenze personalizzate possono supportare scenari più complessi, ad esempio il riconoscimento di moduli o il rilevamento di entità personalizzate tramite un modello fornito e inserito nell'[interfaccia Web di competenze personalizzate](cognitive-search-custom-skill-interface.md). Alcuni esempi di competenze personalizzate includono [Riconoscimento modulo](/azure/cognitive-services/form-recognizer/overview), l'integrazione dell'[API Ricerca entità Bing](https://docs.microsoft.com/azure/search/cognitive-search-create-custom-skill-example) e il [riconoscimento di entità personalizzate](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).


## <a name="components-of-an-enrichment-pipeline"></a>Componenti di una pipeline di arricchimento

Una pipeline di arricchimento si basa su [*indicizzatori*](search-indexer-overview.md) che effettuano ricerche per indicizzazione nelle origini dati e garantiscono l'elaborazione degli indici end-to-end. Le competenze sono ora associate agli indicizzatori, intercettando e arricchendo i documenti in base al set di competenze definito. Terminata l'indicizzazione è possibile accedere al contenuto tramite richieste di ricerca usando tutti i [tipi di query supportati da Ricerca cognitiva di Azure](search-query-overview.md).  Se non si ha familiarità con gli indicizzatori, in questa sezione viene illustrata la procedura da seguire.

### <a name="step-1-connection-and-document-cracking-phase"></a>Passaggio 1: Fase di connessione e individuazione dei documenti

All'inizio della pipeline è presente un testo non strutturato o un contenuto non testuale, come ad esempio immagini e file JPEG digitalizzati. I dati devono esistere in un servizio di archiviazione dati di Azure a cui è possibile accedere tramite un indicizzatore. Gli indicizzatori possono "individuare" i documenti di origine per estrarre il testo dai dati di origine.

![Fase di cracking dei documenti](./media/cognitive-search-intro/document-cracking-phase-blowup.png "cracking di documenti")

 Le origini supportate includono archiviazione BLOB di Azure, archiviazione tabelle di Azure, database SQL di Azure e Azure Cosmos DB. I contenuti testuali possono essere estratti dai seguenti tipi di file: PDF, Word, PowerPoint e CSV. Per un elenco completo, vedere [Formati supportati](search-howto-indexing-azure-blob-storage.md#supported-document-formats).

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>Passaggio 2: Competenze cognitive e fase di arricchimento

L'arricchimento avviene tramite *competenze cognitive* che eseguono operazioni atomiche. Ad esempio, dopo aver creato un contenuto testuale da un file PDF, è possibile applicare il rilevamento del linguaggio di riconoscimento di entità o l'estrazione frasi chiave per produrre nuovi campi nell'indice che non sono disponibili in modo nativo nell'origine. Nel complesso, la raccolta di competenze usata nella pipeline viene chiamata *set di competenze*.  

![Fase di arricchimento](./media/cognitive-search-intro/enrichment-phase-blowup.png "fase di arricchimento")

Un set di competenze si basa su [competenze cognitive predefinite](cognitive-search-predefined-skills.md) o [competenze personalizzate](cognitive-search-create-custom-skill-example.md) fornite e connesse al set di competenze. Un set di competenze può essere minimamente o particolarmente complesso e determina non solo il tipo di elaborazione, ma anche l'ordine delle operazioni. Un set di competenze, assieme ai mapping dei campi definiti come parte di un indicizzatore, specifica l'intera pipeline di arricchimento. Per altre informazioni sul raggruppamento di tutte queste parti, vedere [Definire un set di competenze](cognitive-search-defining-skillset.md).

Internamente, la pipeline genera una raccolta di documenti arricchiti. È possibile decidere quali parti dei documenti arricchiti devono essere mappate ai campi indicizzabili contenuti nell'indice di ricerca. Ad esempio, se sono state applicate le competenze di estrazione frasi chiave e riconoscimento di entità, i nuovi campi diventano parte del documento arricchito e possono essere mappati ai campi nell'indice. Vedere [Annotazioni](cognitive-search-concept-annotations-syntax.md) per altre informazioni sulle formazioni in ingresso/uscita.

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>Aggiungere un elemento knowledgeStore per salvare gli arricchimenti

L'[API REST di ricerca versione 2019-05-06-Preview](search-api-preview.md) estende i set di competenze con una definizione di `knowledgeStore` che fornisce una connessione all'archiviazione di Azure e proiezioni che descrivono la modalità di archiviazione degli arricchimenti. 

L'aggiunta di un knowledge store a un set di competenze consente di proiettare una rappresentazione degli arricchimenti per scenari diversi da quelli della ricerca full-text. Per altre informazioni, vedere [Che cos'è il knowledge store in Ricerca di Azure?](knowledge-store-concept-intro.md)

### <a name="step-3-search-index-and-query-based-access"></a>Passaggio 3: Indice di ricerca e accesso basato su query

Al termine dell'elaborazione, si avrà un indice di ricerca costituito da documenti arricchiti in cui è possibile eseguire ricerche di testo in Ricerca cognitiva di Azure. [L'esecuzione di query dell'indice](search-query-overview.md) consente a utenti e sviluppatori di accedere al contenuto arricchito generato dalla pipeline. 

![Indice con icona di ricerca](./media/cognitive-search-intro/search-phase-blowup.png "Indice con icona di ricerca")

L'indice è simile a qualsiasi altro indice creato per Ricerca cognitiva di Azure: è possibile integrare analizzatori personalizzati, richiamare query di ricerca fuzzy, aggiungere ricerche con filtri o sperimentare profili di punteggio per rimodellare i risultati della ricerca.

Gli indici vengono generati da uno schema dell'indice che definisce i campi, gli attributi e altri costrutti allegati a un indice specifico, come ad esempio profili di punteggio e mapping dei sinonimi. Una volta definito e popolato un indice, è possibile eseguire l'indicizzazione in modo incrementale per caricare documenti di origine nuovi e aggiornati. Alcune modifiche apportate richiedono una ricompilazione completa. È consigliabile usare un set di dati ridotto fino a quando la struttura dello schema non è stabile. Per altre informazioni, vedere [How to rebuild an index](search-howto-reindex.md) (Procedura per ricompilare un indice).

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>Funzionalità e concetti principali

| Concetto | DESCRIZIONE| Collegamenti |
|---------|------------|-------|
| Set di competenze | Risorsa denominata di primo livello contenente una raccolta di competenze. Un set di competenze è la pipeline di arricchimento. Viene richiamata durante l'indicizzazione da parte di un indicizzatore. | Vedere [Definire un set di competenze](cognitive-search-defining-skillset.md) |
| Competenza cognitiva | Trasformazione atomica in una pipeline di arricchimento. Spesso si tratta di un componente che consente di estrarre o dedurre la struttura e pertanto aumenta il riconoscimento dei dati di input. Quasi sempre, l'output è testuale e l'elaborazione è del linguaggio naturale o di immagini e consente di estrarre o generare testo dagli input delle immagini. L'output di una competenza può essere mappato a un campo di un indice o usato come input per un arricchimento a valle. Una competenza può essere predefinita e fornita da Microsoft o personalizzata, ossia creata e distribuita dall'utente. | [Competenze cognitive predefinite](cognitive-search-predefined-skills.md) |
| Estrazione dei dati | Include una vasta gamma di tipologie di elaborazione, comunque riferibili all'arricchimento tramite intelligenza artificiale; la competenza di riconoscimento entità è generalmente usata per estrarre dati (entità) da un'origine che non fornisce tali informazioni in modo nativo. | Vedere [Competenza di riconoscimento entità](cognitive-search-skill-entity-recognition.md)| 
| Elaborazione di immagini | Deduce il testo da un'immagine, ad esempio la possibilità di riconoscere un riferimento, oppure estrae il testo da un'immagine. Alcuni esempi comuni includono l'OCR per l'estrazione di caratteri da un file digitalizzato (JPEG) o il riconoscimento del nome di una via in una foto che contiene un cartello stradale. | Vedere [Competenza di analisi delle immagini](cognitive-search-skill-image-analysis.md) o [Competenza OCR](cognitive-search-skill-ocr.md)
| Elaborazione del linguaggio naturale | Elaborazione di un testo per estrarre informazioni dettagliate e informazioni sugli input di testo. Rilevamento lingua, analisi del sentiment ed estrazione frasi chiave sono competenze che rientrano nell'elaborazione del linguaggio naturale.  | Vedere [Competenza di estrazione frasi chiave](cognitive-search-skill-keyphrases.md), [Competenza di rilevamento lingua](cognitive-search-skill-language-detection.md), [Competenza di analisi del sentiment](cognitive-search-skill-sentiment.md) |
| Individuazione di documenti | Processo di estrazione o creazione di contenuti di testo da origini non testuali durante l'indicizzazione. La funzione di riconoscimento ottico dei caratteri (Optical Character Recognition - OCR) ne è un esempio, ma in genere si fa riferimento alla funzionalità di base dell'indicizzatore, che consente di estrarre contenuti da file dell'applicazione. L'origine dati che fornisce il percorso del file di origine e la definizione dell'indicizzatore che fornisce il mapping dei campi sono entrambi fattori chiave nell'individuazione di documenti. | Vedere [Panoramica degli indicizzatori](search-indexer-overview.md) |
| Shaping | Consolidare frammenti di testo in una struttura più ampia o viceversa suddividere i blocchi di testo più grandi in blocchi di dimensioni gestibili per un'altra elaborazione a valle. | Vedere [Competenza Shaper](cognitive-search-skill-shaper.md), [Competenza di unione testo](cognitive-search-skill-textmerger.md), [Competenze di divisione testo](cognitive-search-skill-textsplit.md) |
| Documenti arricchiti | Struttura interna transitoria, generata durante l'elaborazione, con l'output finale riflesso in un indice di ricerca. Un set di competenze determina i miglioramenti eseguiti. Il mapping dei campi determina gli elementi dati aggiunti all'indice. È facoltativamente possibile creare un knowledge store per rendere persistenti ed esplorare i documenti arricchiti usando strumenti come Storage Explorer, Power BI o qualsiasi altro strumento che si connette ad Archiviazione BLOB di Azure. | Vedere [Che cos'è il knowledge store in Ricerca di Azure?](knowledge-store-concept-intro.md) |
| Indexer |  Crawler che estrae dati e metadati che supportano la ricerca da un'origine dati esterna e popola un indice in base a mapping da campo a campo tra l'indice e l'origine dati per l'individuazione dei documenti. Per gli arricchimenti tramite intelligenza artificiale, l'indicizzatore richiama un set di competenze e contiene i mapping dei campi che associano gli output di arricchimento ai campi di destinazione nell'indice. La definizione dell'indicizzatore contiene tutte le istruzioni e i riferimenti per le operazioni della pipeline, che viene a sua volta richiamata quando si esegue l'indicizzatore. | [Indicizzatori](search-indexer-overview.md) |
| origine dati  | Oggetto usato da un indicizzatore per la connessione a un'origine dati esterna di tipi supportati in Azure. | Vedere [Panoramica degli indicizzatori](search-indexer-overview.md) |
| Indice | Indice di ricerca persistente in Ricerca cognitiva di Azure, compilato in base a uno schema dell'indice che definisce la struttura dei campi e l'utilizzo. | Vedere [Creare un indice di base](search-what-is-an-index.md) | 

<a name="where-do-i-start"></a>

## <a name="where-do-i-start"></a>Dove iniziare?

**Passaggio 1: [Creare una risorsa di Ricerca cognitiva di Azure](search-create-service-portal.md)** 

**Passaggio 2: Provare alcune guide di avvio rapido e alcuni esempi per acquisire esperienza pratica**

+ [Guida introduttiva (portale)](cognitive-search-quickstart-blob.md)
+ [Esercitazione (richieste HTTP)](cognitive-search-tutorial-blob.md)
+ [Esempio: Creazione di una competenza personalizzata per l'arricchimento tramite intelligenza artificiale (C#)](cognitive-search-create-custom-skill-example.md)

Il servizio gratuito è consigliabile a scopi formativi, ma tenere presente che il numero di transazioni gratuite è limitato a 20 documenti al giorno. Per eseguire la guida di avvio rapido e l'esercitazione nello stesso giorno, usare un set di file più piccolo (10 documenti), in modo da far rientrare entrambi gli esercizi, oppure eliminare l'indicizzatore usato nella guida di avvio rapido o nell'esercitazione per azzerare il contatore.

**Passaggio 3: Esaminare l'API**

È possibile usare REST `api-version=2019-05-06` sulle richieste o su .NET SDK. Se si sta esplorando l'archivio conoscenze, usare invece l'API REST di anteprima (`api-version=2019-05-06-Preview`).

In questo passaggio vengono usate API REST per creare una soluzione di arricchimento tramite intelligenza artificiale. Per l'arricchimento tramite intelligenza artificiale vengono aggiunte o estese solo due API. Altre API hanno la stessa sintassi delle versioni generalmente disponibili.

| API REST | DESCRIZIONE |
|-----|-------------|
| [Creare un'origine dati](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Risorsa che identifica un'origine dati esterna che fornisce dati di origine usati per creare documenti arricchiti.  |
| [Creare un set di competenze (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Questa API è specifica dell'arricchimento tramite intelligenza artificiale. È una risorsa che coordina l'utilizzo di [competenze predefinite](cognitive-search-predefined-skills.md) e [competenze cognitive personalizzate](cognitive-search-custom-skill-interface.md) usate in una pipeline di arricchimento durante l'indicizzazione. |
| [Creare un indice](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Schema che esprime un indice di Ricerca cognitiva di Azure. Viene eseguito il mapping dei campi nell'indice con i campi nei dati di origine o i campi prodotti durante la fase di arricchimento (ad esempio, un campo per i nomi dell'organizzazione creati dal riconoscimento entità). |
| [Creare un indicizzatore (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Risorsa che definisce i componenti usati durante l'indicizzazione, incluso un'origine dati, un set di competenze, associazioni di campi dalle strutture dei dati di origine e intermedie all'indice di destinazione, oltre all'indice stesso. L'esecuzione dell'indicizzatore è il trigger di inserimento dati e arricchimento. L'output è un indice di ricerca basato sullo schema dell'indice, popolato con dati di origine e arricchito da set di competenze. Questa API esistente viene estesa per gli scenari di ricerca cognitiva con l'inclusione di una proprietà skillset. |

**Elenco di controllo: un flusso di lavoro tipico**

1. Includere in un sottoinsieme i dati di origine di Azure in un campione rappresentativo. L'indicizzazione richiede tempo; si consiglia quindi di iniziare con un set di dati piccolo ma rappresentativo, per poi compilarlo in modo incrementale con l'evolversi della soluzione.

1. Creare un [oggetto di origine dati](https://docs.microsoft.com/rest/api/searchservice/create-data-source) in Ricerca cognitiva di Azure per fornire una stringa di connessione per il recupero dati.

1. Creare un [set di competenze](https://docs.microsoft.com/rest/api/searchservice/create-skillset) con una procedura di arricchimento.

1. Definire lo [schema dell'indice](https://docs.microsoft.com/rest/api/searchservice/create-index). La raccolta *Campi* include campi provenienti dall'origine dati. È necessario anche disattivare i campi aggiuntivi per mantenere i valori generati per il contenuto creato durante l'arricchimento.

1. Definire l'[indicizzatore](https://docs.microsoft.com/rest/api/searchservice/create-skillset) che fa riferimento all'origine dati, al set di competenze e all'indice.

1. All'interno dell'indicizzatore, aggiungere *outputFieldMappings*. In questa sezione viene eseguito il mapping dell'output dal set di competenze (nel passaggio 3) ai campi di input nello schema dell'indice (nel passaggio 4).

1. Inviare la richiesta *Crea l'indicizzatore* appena creata (una richiesta POST con una definizione dell'indicizzatore nel corpo della richiesta) per esprimere l'indicizzatore in Ricerca cognitiva di Azure. Questo passaggio descrive come viene eseguito l'indicizzatore, richiamando la pipeline.

1. Eseguire query per valutare i risultati e modificare il codice per aggiornare i set di competenze, lo schema o la configurazione dell'indicizzatore.

1. [Reimpostare l'indicizzatore](search-howto-reindex.md) prima di ricompilare la pipeline.

Per altre informazioni su domande o problemi specifici, vedere [Suggerimenti per la risoluzione dei problemi](cognitive-search-concept-troubleshooting.md).

## <a name="next-steps"></a>Passaggi successivi

+ [Collegamenti alla documentazione per l'arricchimento tramite intelligenza artificiale](cognitive-search-resources-documentation.md)
+ [Guida introduttiva: Creare una pipeline di arricchimento con intelligenza artificiale usando competenze cognitive in Ricerca di Azure](cognitive-search-quickstart-blob.md)
+ [Esercitazione: Definire la struttura di "contenuto non strutturato" con la ricerca cognitiva](cognitive-search-tutorial-blob.md)
+ [Che cos'è il knowledge store in Ricerca di Azure?](knowledge-store-concept-intro.md)
+ [Procedura dettagliata su Knowledge Store](knowledge-store-howto.md)
