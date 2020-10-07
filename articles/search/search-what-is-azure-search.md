---
title: Introduzione alla ricerca cognitiva di Azure
titleSuffix: Azure Cognitive Search
description: Ricerca cognitiva di Azure è un servizio di ricerca cloud completamente gestito di Microsoft. Descrizioni dei casi d'uso, flusso di lavoro di sviluppo, confronto con altri prodotti per la ricerca di Microsoft e informazioni per iniziare.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 09/22/2020
ms.custom: contperfq1
ms.openlocfilehash: 26a448ded06b32fef80fee06568655067a727620
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320413"
---
# <a name="what-is-azure-cognitive-search"></a>Che cos'è la ricerca cognitiva di Azure?

Ricerca cognitiva di Azure ([precedentemente nota come "Ricerca di Azure"](whats-new.md#new-service-name)) è un servizio di ricerca cloud che offre agli sviluppatori le API e gli strumenti per creare un'esperienza di ricerca avanzata su contenuti eterogenei e privati nelle applicazioni Web, per dispositivi mobili e aziendali.

Quando si crea un servizio Ricerca cognitiva, si ottiene un motore di ricerca che esegue l'indicizzazione e l'esecuzione di query, l'archiviazione permanente degli indici creati e gestiti e un linguaggio di query per la composizione di query semplici a complesse. Facoltativamente, un servizio di ricerca si integra con altri servizi di Azure sotto forma di *indicizzatori* che automatizzano l'inserimento o il recupero dei dati dalle origini dati di Azure e *set di competenze* che incorporano l'intelligenza artificiale utilizzabile da Servizi cognitivi, ad esempio l'analisi di immagini e testo o l'intelligenza artificiale personalizzata creata in Azure Machine Learning o incorporata all'interno di Funzioni di Azure.

![Architettura di Ricerca cognitiva di Azure](media/search-what-is-azure-search/azure-search-diagram.svg "Architettura di Ricerca cognitiva di Azure")

A livello di architettura, un servizio di ricerca risiede tra gli archivi dati esterni che contengono i dati non indicizzati e un'app client che invia richieste di query a un indice di ricerca e gestisce la risposta.  Uno schema di indice determina la struttura del contenuto ricercabile. 

I due carichi di lavoro primari di un servizio di ricerca sono *indicizzazione* e *query*.

+ L'indicizzazione porta il testo nel servizio di ricerca e lo rende ricercabile. Internamente, il testo in ingresso viene elaborato in token e archiviato negli indici invertiti per le analisi veloci. Durante l'indicizzazione, si ha la possibilità di aggiungere *competenze cognitive*, quelle predefinite di Microsoft o le competenze personalizzate create dall'utente. L'analisi e le trasformazioni successive possono generare nuove informazioni e strutture che non esistevano in precedenza, risultando così particolarmente utili per molti scenari di ricerca e di data mining.

+ Dopo che l'indice è stato popolato con dati ricercabili, l'app client invia richieste di query a un servizio di ricerca e gestisce le risposte. Tutte le query vengono eseguite su un indice di ricerca creato dall'utente e di sua proprietà che viene archiviato nel servizio. Nell'app client l'esperienza di ricerca viene definita usando le API di Ricerca cognitiva di Azure e può includere l'ottimizzazione della pertinenza, il completamento automatico, la corrispondenza dei sinonimi, la corrispondenza fuzzy, i criteri di ricerca, il filtro e l'ordinamento.

La funzionalità viene esposta tramite una semplice [API REST](/rest/api/searchservice/) o un [SDK .NET](search-howto-dotnet-sdk.md) che maschera la complessità intrinseca del recupero delle informazioni. È anche possibile usare il portale di Azure per l'amministrazione del servizio e la gestione dei contenuti, con strumenti per la creazione di prototipi e per l'esecuzione di query su indici e set di competenze. Poiché il servizio viene eseguito nel cloud, disponibilità e infrastruttura sono gestite da Microsoft.

## <a name="when-to-use-cognitive-search"></a>Quando usare Ricerca cognitiva

Ricerca cognitiva di Azure è una soluzione particolarmente adatta agli scenari di applicazione seguenti:

+ Consolidamento di tipi di contenuto eterogenei in un indice di ricerca privato definito dall'utente. È possibile popolare un indice con flussi di documenti JSON da qualsiasi origine. Per le origini supportate in Azure, usare un *indicizzatore* per automatizzare l'indicizzazione. Il controllo sullo schema dell'indice e sulla pianificazione degli aggiornamenti è uno dei vantaggi principali di Ricerca cognitiva.

+ Facile implementazione delle funzionalità correlate alla ricerca. Le API di ricerca semplificano la costruzione di query, l'esplorazione in base a facet, i filtri (inclusa la ricerca geospaziale), il mapping dei sinonimi, il completamento automatico e l'ottimizzazione della pertinenza. Grazie alle funzionalità predefinite, è possibile soddisfare le aspettative degli utenti finali per un'esperienza di ricerca simile ai motori di ricerca Web commerciali.

+ Il contenuto non elaborato è costituito da testo non differenziato, file di immagine o file applicazione di grandi dimensioni archiviati nell'archiviazione BLOB di Azure o in Cosmos DB. È possibile applicare le [competenze cognitive](cognitive-search-concept-intro.md) durante l'indicizzazione per identificare ed estrarre testo, creare una struttura o nuove informazioni, ad esempio testo tradotto o entità.

+ Per il contenuto è necessaria un'analisi del testo linguistica o personalizzata. Per il contenuto non in lingua inglese, Ricerca cognitiva di Azure supporta sia gli analizzatori Lucene che i processori di linguaggio naturale Microsoft. È anche possibile configurare gli analizzatori per ottenere un'elaborazione specializzata di contenuto non elaborato, ad esempio l'esclusione tramite filtro dei segni diacritici o il riconoscimento e la conservazione dei criteri nelle stringhe.

Per altre informazioni sulle funzionalità specifiche, vedere [Funzionalità di Ricerca cognitiva di Azure](search-features-list.md)

## <a name="how-to-use-cognitive-search"></a>Come usare Ricerca cognitiva

### <a name="step-1-provision-service"></a>Passaggio 1: Servizio di provisioning

È possibile [creare un servizio gratuito](search-create-service-portal.md) condiviso con altri sottoscrittori o scegliere un [livello a pagamento](https://azure.microsoft.com/pricing/details/search/) che dedica risorse usate solo dal proprio servizio. Tutte le guide introduttive e le esercitazioni possono essere completate nel servizio gratuito.

Per i livelli a pagamento, sono disponibili due dimensioni del servizio per calibrare le risorse in base ai requisiti di produzione:

+ Aggiungere repliche per aumentare la capacità di gestire carichi di lavoro elevati di query
+ Aggiungere partizioni per aumentare lo spazio di archiviazione per più documenti

### <a name="step-2-create-an-index"></a>Passaggio 2: Creare un indice

Definire lo schema dell'indice del mapping affinché rifletta la struttura dei documenti in cui eseguire le ricerche, in modo analogo ai campi in un database. Un indice di ricerca è una struttura dei dati specializzata ottimizzata per l'esecuzione rapida di query.

È possibile [creare lo schema dell'indice nel portale di Azure](search-what-is-an-index.md) o a livello di codice usando [.NET SDK](search-howto-dotnet-sdk.md) o l'[API REST](/rest/api/searchservice/).

> [!TIP]
> Iniziare con la guida dell'[Avvio rapido: Importazione guidata dei dati](search-get-started-portal.md) per creare, caricare ed eseguire query su un indice in pochi minuti.

### <a name="step-3-load-data"></a>Passaggio 3: Caricare dati

Dopo aver definito un indice, si è pronti per caricare il contenuto. È possibile usare un modello push o pull.

Il modello push esegue il push dei documenti JSON in un indice usando le API [REST](search-howto-dotnet-sdk.md) o di un [SDK](/rest/api/searchservice/addupdate-or-delete-documents). Il set di dati esterno può essere praticamente qualsiasi origine dati, purché i documenti siano nel formato JSON.

Il modello pull esegue il pull dei dati dalle origini in Azure e li invia a un indice di ricerca. È implementato tramite [*indicizzatori*](/rest/api/searchservice/Indexer-operations) che semplificano e automatizzano aspetti dell'operazione di inserimento di dati, ad esempio la connessione, la lettura e la serializzazione dei dati. Le origini dati supportate includono Azure Cosmos DB, SQL di Azure e Archiviazione di Azure.

### <a name="step-4-send-queries-and-handle-responses"></a>Passaggio 4: Inviare query e gestire le risposte

Dopo avere compilato un indice, è possibile [eseguire query di ricerca](search-query-overview.md) nell'endpoint di servizio tramite semplici richieste HTTP con l'[API REST](/rest/api/searchservice/Search-Documents) o [.NET SDK](/dotnet/api/microsoft.azure.search.idocumentsoperations).

Per creare ed estendere una pagina Web che raccoglie l'input degli utenti e gestisce i risultati, vedere [Creare la prima app di ricerca](tutorial-csharp-create-first-app.md). Per eseguire query su un indice esistente, è anche possibile usare [Postman per le chiamate REST interattive](search-get-started-postman.md) oppure la funzionalità predefinita [Esplora ricerche](search-explorer.md) nel portale di Azure.

## <a name="how-it-compares"></a>Confronto

Spesso i clienti chiedono quali siano le differenze tra Ricerca cognitiva di Azure e le altre soluzioni di ricerca. Nella tabella seguente sono riepilogate le principali differenze.

| Confronto con | Differenze principali |
|-------------|-----------------|
|Bing | [API Ricerca Web Bing](../cognitive-services/bing-web-search/index.yml) ricerca i termini corrispondenti inviati negli indici in Bing.com. Gli indici sono generati da contenuti Web HTML, XML e di altro tipo nei siti pubblici. Con la medesima base, [Ricerca personalizzata Bing ](/azure/cognitive-services/bing-custom-search/) offre la stessa tecnologia di ricerca Web per tipi di contenuto Web aventi come ambito siti Web specifici.<br/><br/>Ricerca cognitiva di Azure esegue le ricerche in un indice definito e popolato con dati e documenti di cui si è proprietari e spesso aventi origini diverse. Ricerca cognitiva di Azure dispone di funzionalità di ricerca Web per alcune origini dati tramite gli [indicizzatori](search-indexer-overview.md), ma è possibile eseguire il push di qualsiasi documento JSON conforme allo schema di indicizzazione in un'unica risorsa ricercabile consolidata. |
|Ricerca nel database | Molte piattaforme di database includono un'esperienza di ricerca predefinita. SQL Server include la [ricerca full-text](/sql/relational-databases/search/full-text-search). Cosmos DB e tecnologie simili usano indici disponibili per query. Durante la valutazione di prodotti che combinano ricerca e archiviazione, può essere difficile operare una scelta. Molte soluzioni usano entrambe le tecnologie, ovvero un sistema DBMS per l'archiviazione e Ricerca cognitiva di Azure per le funzionalità di ricerca specializzate.<br/><br/>Rispetto alla ricerca DBMS, Ricerca cognitiva di Azure archivia il contenuto da origini eterogenee e offre funzionalità di elaborazione del testo specialistiche, come l'elaborazione del testo con riconoscimento linguistico (stemming, lemmatizzazione, forme delle parole) in [56 lingue](/rest/api/searchservice/language-support). Supporta anche la correzione automatica di parole con errori di digitazione, [sinonimi](/rest/api/searchservice/synonym-map-operations), [suggerimenti](/rest/api/searchservice/suggestions), [controlli per il punteggio](/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [facet](./search-filters-facets.md)e [tokenizzazione personalizzata](/rest/api/searchservice/custom-analyzers-in-azure-search). Il [motore di ricerca full-text](search-lucene-query-architecture.md) di Ricerca cognitiva di Azure è basato su Apache Lucene, uno standard del settore per il recupero delle informazioni. Tuttavia, anche se Ricerca cognitiva di Azure salva in modo permanente i dati sotto forma di indice invertito, non costituisce un'alternativa a una vera soluzione di archiviazione dei dati e non è consigliabile usarla a tale scopo. Per altre informazioni, vedere questo [post di forum](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data). <br/><br/>L'utilizzo delle risorse è un altro punto da considerare in questo ambito. L'indicizzazione e alcune operazioni di query sono spesso onerose dal punto di vista dei calcoli. L'offload della ricerca dal sistema DBMS a una soluzione dedicata nel cloud consente di risparmiare risorse da destinare all'elaborazione delle transazioni. Con l'esternalizzazione della ricerca, inoltre, è possibile modificare facilmente la scala in modo che corrisponda al volume di query.|
|Soluzione di ricerca dedicata | Presupponendo di aver deciso per la ricerca dedicata con funzionalità complete, è opportuno un confronto finale tra soluzioni locali o servizio cloud. Molte tecnologie di ricerca consentono di controllare le pipeline di query e indicizzazione, offrono l'accesso a sintassi di query e filtro più complete, controllano la priorità e la rilevanza e sono caratterizzate da funzionalità di ricerca intelligente e mirata. <br/><br/>Un servizio cloud è la scelta giusta se si vuole una soluzione chiavi in mano con sovraccarico e manutenzione minimi e scala regolabile. <br/><br/>All'interno del paradigma di cloud, molti provider offrono funzionalità di base simili, con ricerca full-text, ricerca geografica e la possibilità di gestire un certo livello di ambiguità negli input di ricerca. In genere sono una [funzionalità specializzata](search-features-list.md) o la semplicità e la facilità d'uso generale di API, strumenti e gestione a determinare la scelta ideale. |

Tra i provider di servizi cloud, Ricerca cognitiva di Azure offre le migliori prestazioni per i carichi di lavoro con ricerca full-text in archivi di contenuti e database in Azure, per le app che fanno principalmente affidamento sulla ricerca per il recupero delle informazioni e l'esplorazione dei contenuti. 

Ecco i principali vantaggi:

+ Integrazione dei dati in Azure (crawler) al livello di indicizzazione
+ Portale di Azure per la gestione centralizzata
+ Scalabilità, affidabilità e massima disponibilità proprie di Azure
+ Elaborazione dei dati non elaborati tramite intelligenza artificiale per agevolare la ricerca, anche nel testo delle immagini, o individuazione di modelli in contenuto non strutturato.
+ Analisi linguistica e personalizzata, con analizzatori per una robusta ricerca full-text in 56 lingue
+ [Funzionalità di base comuni per le app basate sulla ricerca](search-features-list.md): assegnazione dei punteggi, esplorazione in base a facet, suggerimenti, sinonimi, ricerca geografica e così via.

Tra i clienti, i casi in grado di sfruttare la gamma più ampia di funzionalità di Ricerca cognitiva di Azure includono cataloghi online, programmi line-of-business e applicazioni di individuazione di documenti.

## <a name="watch-this-video"></a>Guardare questo video

In questo video di 15 minuti, il Program Manager Luis Cabrera presenta Ricerca cognitiva di Azure.

>[!VIDEO https://www.youtube.com/embed/kOJU0YZodVk?version=3]