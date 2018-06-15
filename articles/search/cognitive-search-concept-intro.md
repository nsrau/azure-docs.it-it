---
title: Ricerca cognitiva per l'estrazione di dati, elaborazione del linguaggio naturale in Ricerca di Azure | Microsoft Docs
description: Estrazione dei dati, elaborazione del linguaggio naturale (NLP) ed elaborazione di immagini per creare contenuti che supportano la ricerca nell'indicizzazione di Ricerca di Azure tramite competenze cognitive.
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: heidist
ms.openlocfilehash: ca6c285348208a7ad24faf966073d641810039fc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641112"
---
# <a name="what-is-cognitive-search"></a>Che cos'è la ricerca cognitiva?

La ricerca cognitiva è una funzionalità di anteprima di [Ricerca di Azure](search-what-is-azure-search.md) disponibile a tutti i livelli negli Stati Uniti centro-meridionali e in Europa occidentale, che aggiunge IA a carichi di lavoro di indicizzazione. Estrazione dei dati, elaborazione del linguaggio naturale ed elaborazione di immagini durante l'indicizzazione rilevano le informazioni latenti in contenuti non strutturati o che non supportano la ricerca e rendono possibile eseguirvi ricerche in Ricerca di Azure.

L'integrazione IA avviene tramite *competenze cognitive* che arricchiscono i documenti di origine tramite processi sequenziali, indirizzati verso un indice di ricerca. 

![Diagramma della pipeline di ricerca cognitiva](./media/cognitive-search-intro/cogsearch-architecture.png "Panoramica della pipeline di ricerca cognitiva")

Le competenze usate durante l'indicizzazione possono essere predefinite o personalizzate:

+ Le [competenze predefinite](cognitive-search-predefined-skills.md) si basano sugli stessi algoritmi IA usati nelle API Servizi cognitivi: [Riconoscimento di entità denominate](cognitive-search-skill-named-entity-recognition.md), [Estrazione frasi chiave](cognitive-search-skill-keyphrases.md) e [OCR](cognitive-search-skill-ocr.md) sono soltanto alcune di esse. 

+ Le [Competenze personalizzate](cognitive-search-create-custom-skill-example.md) possono essere sviluppate dall'utente per operazioni di elaborazione specializzate. Esempi di competenze personalizzate possono essere un modulo di entità personalizzato o un classificatore di documenti destinato a un dominio specifico, ad esempio pubblicazioni finanziarie, scientifiche o mediche.

> [!NOTE]
> La Ricerca cognitiva è disponibile in anteprima pubblica e l'esecuzione del set di competenze è attualmente offerta gratuitamente. Il prezzo per questa funzionalità verrà annunciato in un momento successivo.

## <a name="components-of-cognitive-search"></a>Componenti della ricerca cognitiva

La pipeline della ricerca cognitiva si basa su indicizzatori di [Ricerca di Azure *che effettuano*](search-indexer-overview.md) ricerche nelle origini dati e garantiscono l'elaborazione degli indici end-to-end. Le competenze sono ora associate agli indicizzatori, intercettando e arricchendo i documenti in base al set di competenze definito. Terminata l'indicizzazione è possibile accedere al contenuto tramite richieste di ricerca attraverso tutti i [tipi di query supportati da Ricerca di Azure](search-query-overview.md).  Se non si ha familiarità con gli indicizzatori, in questa sezione viene illustrata la procedura da seguire.

### <a name="source-data-and-document-cracking-phase"></a>Fase di individuazione di documenti e dati di origine

All'inizio della pipeline è presente un testo non strutturato o un contenuto non testuale, come ad esempio immagini e file JPEG digitalizzati. I dati devono esistere in un servizio di archiviazione dati di Azure a cui è possibile accedere tramite un indicizzatore. Gli indicizzatori possono "individuare" i documenti di origine per estrarre il testo dai dati di origine.

![Fase di individuazione dei documenti](./media/cognitive-search-intro/document-cracking-phase-blowup.png "individuazione di documenti")

 Le origini supportate includono archiviazione BLOB di Azure, archiviazione tabelle di Azure, database SQL di Azure e Azure Cosmos DB. I contenuti testuali possono essere estratti dai seguenti tipi di file: PDF, Word, PowerPoint e file CSV. Per un elenco completo, vedere [Formati supportati](search-howto-indexing-azure-blob-storage.md#supported-document-formats).

### <a name="cognitive-skills-and-enrichment-phase"></a>Competenze cognitive e fase di arricchimento

L'arricchimento avviene tramite *competenze cognitive* che eseguono operazioni atomiche. Ad esempio, dopo aver creato un contenuto testuale da un file PDF, è possibile applicare il rilevamento del linguaggio di riconoscimento di entità o l'estrazione frasi chiave per produrre nuovi campi nell'indice che non sono disponibili in modo nativo nell'origine. Nel complesso, la raccolta di competenze usata nella pipeline viene chiamata *set di competenze*.  

![Fase di arricchimento](./media/cognitive-search-intro/enrichment-phase-blowup.png "fase di arricchimento")

Un set di competenze si basa su [competenze cognitive predefinite](cognitive-search-predefined-skills.md) o [competenze personalizzate](cognitive-search-create-custom-skill-example.md) fornite e connesse al set di competenze. Un set di competenze può essere minimamente o particolarmente complesso e determina non solo il tipo di elaborazione, ma anche l'ordine delle operazioni. Un set di competenze, assieme ai mapping dei campi definiti come parte di un indicizzatore, specifica l'intera pipeline di arricchimento. Per altre informazioni sul raggruppamento di tutte queste parti, vedere [Definire un set di competenze](cognitive-search-defining-skillset.md).

Internamente, la pipeline genera una raccolta di documenti arricchiti. È possibile decidere quali parti dei documenti arricchiti devono essere mappate ai campi indicizzabili contenuti nell'indice di ricerca. Ad esempio, se sono state applicate le competenze di estrazione frasi chiave e riconoscimento di entità, i nuovi campi diventano parte del documento arricchito e possono essere mappati ai campi nell'indice. Vedere [Annotazioni](cognitive-search-concept-annotations-syntax.md) per altre informazioni sulle formazioni in ingresso/uscita.

### <a name="search-index-and-query-based-access"></a>Indice di ricerca e accesso basato su query

Terminata l'elaborazione, si avrà un set di ricerche costituito da documenti arricchiti in cui è possibile eseguire ricerche in Ricerca di Azure. [L'esecuzione di query dell'indice](search-query-overview.md) consente a utenti e sviluppatori di accedere al contenuto arricchito generato dalla pipeline. 

![Indice con icona di ricerca](./media/cognitive-search-intro/search-phase-blowup.png "indice con icona di ricerca")

L'indice è simile a qualsiasi altro indice creato per la Ricerca di Azure: è possibile integrare analizzatori personalizzati, richiamare query di ricerca fuzzy, aggiungere ricerche con filtri o sperimentare profili di punteggio per rielaborare i risultati della ricerca.

Gli indici vengono generati da uno schema dell'indice che definisce i campi, gli attributi e altri costrutti allegati a un indice specifico, come ad esempio profili di punteggio e mapping dei sinonimi. Una volta definito e popolato un indice, è possibile eseguire l'indicizzazione in modo incrementale per caricare documenti di origine nuovi e aggiornati. Alcune modifiche apportate richiedono una ricompilazione completa. È consigliabile usare un set di dati ridotto fino a quando la struttura dello schema non è stabile. Per altre informazioni, vedere [How to rebuild an index](search-howto-reindex.md) (Procedura per ricompilare un indice).

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>Funzionalità e concetti principali

| Concetto | DESCRIZIONE| Collegamenti |
|---------|------------|-------|
| Set di competenze | Risorsa denominata di primo livello contenente una raccolta di competenze. Un set di competenze è la pipeline di arricchimento. Viene richiamata durante l'indicizzazione da parte di un indicizzatore. | [Definire un set di competenze](cognitive-search-defining-skillset.md) |
| Competenza cognitiva | Trasformazione atomica in una pipeline di arricchimento. Spesso si tratta di un componente che consente di estrarre o dedurre la struttura e pertanto aumenta il riconoscimento dei dati di input. Quasi sempre, l'output è testuale e l'elaborazione è del linguaggio naturale o di immagini e consente di estrarre o generare testo dagli input delle immagini. L'output di una competenza può essere mappato a un campo di un indice o usato come input per un arricchimento a valle. Una competenza può essere predefinita e fornita da Microsoft o personalizzata, ossia creata e distribuita dall'utente. | [Competenze predefinite](cognitive-search-predefined-skills.md) |
| Estrazione dei dati | Include una vasta gamma di tipologie di elaborazione, comunque riferibili alla ricerca cognitiva; la competenza di riconoscimento entità denominata è generalmente usata per estrarre dati (entità) da un'origine che non fornisce tali informazioni in modo nativo. | [Competenza di riconoscimento entità denominata](cognitive-search-skill-named-entity-recognition.md)| 
| Elaborazione di immagini | Deduce il testo da un'immagine, ad esempio la possibilità di riconoscere un riferimento, oppure estrae il testo da un'immagine. Alcuni esempi comuni includono l'OCR per l'estrazione di caratteri da un file digitalizzato (JPEG) o il riconoscimento del nome di una via in una foto che contiene un cartello stradale. | [Competenza di analisi delle immagini](cognitive-search-skill-image-analysis.md) o [Competenza OCR](cognitive-search-skill-ocr.md)
| Elaborazione del linguaggio naturale | Elaborazione di un testo per estrarre informazioni dettagliate e informazioni sugli input di testo. Rilevamento lingua, analisi del sentiment ed estrazione frasi chiave sono competenze che rientrano nell'elaborazione del linguaggio naturale.  | [Competenza di estrazione frasi chiave](cognitive-search-skill-keyphrases.md), [Competenza di rilevamento lingua](cognitive-search-skill-language-detection.md), [Competenza di analisi del sentiment](cognitive-search-skill-sentiment.md) |
| Individuazione di documenti | Processo di estrazione o creazione di contenuti di testo da origini non testuali durante l'indicizzazione. La funzione di riconoscimento ottico dei caratteri (Optical Character Recognition - OCR) ne è un esempio, ma in genere si fa riferimento alla funzionalità di base dell'indicizzatore, che consente di estrarre contenuti da file dell'applicazione. L'origine dati che fornisce il percorso del file di origine e la definizione dell'indicizzatore che fornisce il mapping dei campi sono entrambi fattori chiave nell'individuazione di documenti. | Vedere [Indicizzatori](search-indexer-overview.md) |
| Shaping | Consolidare frammenti di testo in una struttura più ampia o viceversa suddividere i blocchi di testo più grandi in blocchi di dimensioni gestibili per un'altra elaborazione a valle. | [Competenza di shaping](cognitive-search-skill-shaper.md), [Competenza di unione testo](cognitive-search-skill-textmerger.md), [Competenze di divisione testo](cognitive-search-skill-textsplit.md) |
| Documenti arricchiti | Una struttura interna transitoria, non direttamente accessibile nel codice. I documenti arricchiti vengono generati durante l'elaborazione, ma solo gli output finali sono persistenti in un indice di ricerca. Il mapping dei campi determina gli elementi dati aggiunti all'indice. | Vedere [Accessing enriched documents](cognitive-search-tutorial-blob.md#access-enriched-document) (Accesso a documenti arricchiti). |
| Indexer |  Crawler che estrae dati e metadati che supportano la ricerca da un'origine dati esterna e popola un indice in base a mapping da campo a campo tra l'indice e l'origine dati per l'individuazione dei documenti. Per l'arricchimento della ricerca cognitiva, l'indicizzatore richiama un set di competenze e contiene i mapping dei campi che associano gli output di arricchimento ai campi di destinazione nell'indice. La definizione dell'indicizzatore contiene tutte le istruzioni e i riferimenti per le operazioni della pipeline, che viene a sua volta richiamata quando si esegue l'indicizzatore. | [Indicizzatori](search-indexer-overview.md) |
| origine dati  | Oggetto usato da un indicizzatore per la connessione a un'origine dati esterna di tipi supportati in Azure. | Vedere [Indicizzatori](search-indexer-overview.md) |
| Indice | Insieme di ricerche persistente in Ricerca di Azure, compilato in base a uno schema dell'indice che definisce la struttura di campo e di utilizzo. | [Indici in Ricerca di Azure](search-what-is-an-index.md) | 


## <a name="where-do-i-start"></a>Dove iniziare?

**Passaggio 1: Creare un servizio di ricerca in un'area che fornisce le API** 

+ Stati Uniti centro-meridionali
+ Europa occidentale

**Passaggio 2: Esperienza pratica per gestire il flusso di lavoro**

+ [Guida introduttiva (portale)](cognitive-search-quickstart-blob.md)
+ [Esercitazione (richieste HTTP)](cognitive-search-tutorial-blob.md)
+ [Esempio di competenze personalizzate (C#)](cognitive-search-create-custom-skill-example.md)

**Passaggio 3: Verifica dell'API (solo REST)**

Attualmente, vengono fornite solo API REST. Usare `api-version=2017-11-11-Preview` per tutte le richieste. Usare le API seguenti per compilare una soluzione di ricerca cognitiva. Per la ricerca cognitiva vengono aggiunte o estese solo due API. Altre API hanno la stessa sintassi delle versioni generalmente disponibili.

| API REST | DESCRIZIONE |
|-----|-------------|
| [Creare un'origine dati](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Risorsa che identifica un'origine dati esterna che fornisce dati di origine usati per creare documenti arricchiti.  |
| [Creare un set di competenze (api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Risorsa che coordina l'utilizzo di [competenze predefinite](cognitive-search-predefined-skills.md) e [competenze cognitive personalizzate](cognitive-search-custom-skill-interface.md) usate in una pipeline di arricchimento durante l'indicizzazione. |
| [Creare un indice](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Schema che esprime un indice di Ricerca di Azure. Viene eseguito il mapping dei campi nell'indice con i campi nei dati di origine o i campi prodotti durante la fase di arricchimento (ad esempio, un campo per i nomi dell'organizzazione creati dal riconoscimento entità). |
| [Creare un indicizzatore (api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Risorsa che definisce i componenti usati durante l'indicizzazione, incluso un'origine dati, un set di competenze, associazioni di campi dalle strutture dei dati di origine e intermedie all'indice di destinazione, oltre all'indice stesso. L'esecuzione dell'indicizzatore è il trigger di inserimento dati e arricchimento. L'output è un insieme di ricerche basato sullo schema dell'indice, popolato con dati di origine e arricchito tramite insiemi di competenze.  |

**Elenco di controllo: un flusso di lavoro tipico**

1. Includere in un sottoinsieme i dati di origine di Azure in un campione rappresentativo. L'indicizzazione richiede tempo; si consiglia quindi di iniziare con un set di dati piccolo ma rappresentativo, per poi compilarlo in modo incrementale con l'evolversi della soluzione.

1. Creare un [oggetto di origine dati](https://docs.microsoft.com/rest/api/searchservice/create-data-source) in Ricerca di Azure per fornire una stringa di connessione per il recupero dati.

1. Creare un [set di competenze](https://docs.microsoft.com/rest/api/searchservice/create-skillset) con una procedura di arricchimento.

1. Definire lo [schema dell'indice](https://docs.microsoft.com/rest/api/searchservice/create-index). La raccolta *Campi* include campi provenienti dall'origine dati. È necessario anche disattivare i campi aggiuntivi per mantenere i valori generati per il contenuto creato durante l'arricchimento.

1. Definire l'[indicizzatore](https://docs.microsoft.com/rest/api/searchservice/create-skillset) che fa riferimento all'origine dati, al set di competenze e all'indice.

1. All'interno dell'indicizzatore, aggiungere *outputFieldMappings*. In questa sezione viene eseguito il mapping dell'output dal set di competenze (nel passaggio 3) ai campi di input nello schema dell'indice (nel passaggio 4).

1. Inviare la richiesta *Crea l'indicizzatore* appena creata (una richiesta POST con una definizione dell'indicizzatore nel corpo della richiesta) per esprimere l'indicizzatore in Ricerca di Azure. Questo passaggio descrive come viene eseguito l'indicizzatore, richiamando la pipeline.

1. Eseguire query per valutare i risultati e modificare il codice per aggiornare i set di competenze, lo schema o la configurazione dell'indicizzatore.

1. [Reimpostare l'indicizzatore](search-howto-reindex.md) prima di ricompilare la pipeline.

Per altre informazioni su domande o problemi specifici, vedere [Suggerimenti per la risoluzione dei problemi](cognitive-search-concept-troubleshooting.md).

## <a name="next-steps"></a>Passaggi successivi

+ [Documentazione sulla ricerca cognitiva](cognitive-search-resources-documentation.md)
+ [Guida introduttiva: Provare la ricerca cognitiva in una procedura dettagliata per il portale](cognitive-search-quickstart-blob.md)
+ [Esercitazione: Informazioni sull'API di ricerca cognitiva](cognitive-search-tutorial-blob.md)