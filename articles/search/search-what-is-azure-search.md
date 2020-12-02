---
title: Introduzione alla ricerca cognitiva di Azure
titleSuffix: Azure Cognitive Search
description: Ricerca cognitiva di Azure è un servizio di ricerca cloud completamente gestito di Microsoft. Descrizioni dei casi d'uso, flusso di lavoro di sviluppo, confronto con altri prodotti per la ricerca di Microsoft e informazioni per iniziare.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 11/24/2020
ms.custom: contperfq1
ms.openlocfilehash: 19be1155476ca7c295e2d0311e8285bc2128dd1d
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030765"
---
# <a name="what-is-azure-cognitive-search"></a>Che cos'è la ricerca cognitiva di Azure?

Ricerca cognitiva di Azure ([precedentemente nota come "Ricerca di Azure"](whats-new.md#new-service-name)) è un servizio di ricerca cloud che offre agli sviluppatori le API e gli strumenti per creare un'esperienza di ricerca avanzata su contenuti eterogenei e privati nelle applicazioni Web, per dispositivi mobili e aziendali. 

Quando si crea un servizio di Ricerca cognitiva, si ottiene:

+ Un motore di ricerca che esegue l'indicizzazione e le query
+ Un'analisi incentrata su intelligenza artificiale e la trasformazione di immagini e testo indifferenziato durante l'indicizzazione
+ Uno spazio di archiviazione persistente per gli indici di ricerca creati e gestiti
+ Un linguaggio di query per la composizione di query semplici o complesse

A livello di architettura, un servizio di ricerca risiede tra gli archivi dati esterni che contengono i dati non indicizzati e un'app client che invia richieste di query a un indice di ricerca e gestisce la risposta.

![Architettura di Ricerca cognitiva di Azure](media/search-what-is-azure-search/azure-search-diagram.svg "Architettura di Ricerca cognitiva di Azure")

Esternamente, un servizio di ricerca si integra con altri servizi di Azure sotto forma di *indicizzatori*, che automatizzano l'inserimento o il recupero dei dati dalle origini dati di Azure, e *set di competenze*, che incorporano l'intelligenza artificiale utilizzabile da Servizi cognitivi, ad esempio l'analisi di immagini e testo oppure l'intelligenza artificiale personalizzata creata in Azure Machine Learning o incorporata all'interno di Funzioni di Azure.

Nel servizio di ricerca stesso i due carichi di lavoro principali sono *indicizzazione* ed *esecuzione di query*. 

+ L'indicizzazione porta il testo nel servizio di ricerca e lo rende ricercabile. Internamente, il testo in ingresso viene elaborato in token e archiviato in indici invertiti per consentire analisi rapide. 

  Durante l'indicizzazione si ha la possibilità di aggiungere *arricchimento tramite intelligenza artificiale* con [competenze cognitive](cognitive-search-working-with-skillsets.md), che possono essere quelle predefinite di Microsoft o altre personalizzate appositamente create. L'analisi e le trasformazioni successive possono generare nuove informazioni e strutture che non esistevano in precedenza, risultando così particolarmente utili per molti scenari di ricerca e di data mining.

+ Dopo che l'indice è stato popolato con dati ricercabili, l'app client invia richieste di query a un servizio di ricerca e gestisce le risposte. Tutte le query vengono eseguite su un indice di ricerca creato dall'utente e di sua proprietà che viene archiviato nel servizio. Nell'app client l'esperienza di ricerca viene definita usando le API di Ricerca cognitiva di Azure e può includere l'ottimizzazione della pertinenza, il completamento automatico, la corrispondenza dei sinonimi, la corrispondenza fuzzy, i criteri di ricerca, il filtro e l'ordinamento.

La funzionalità viene esposta tramite una semplice [API REST](/rest/api/searchservice/) o un [SDK .NET](search-howto-dotnet-sdk.md) che maschera la complessità intrinseca del recupero delle informazioni. È anche possibile usare il portale di Azure per l'amministrazione del servizio e la gestione dei contenuti, con strumenti per la creazione di prototipi e per l'esecuzione di query su indici e set di competenze. Poiché il servizio viene eseguito nel cloud, disponibilità e infrastruttura sono gestite da Microsoft.

## <a name="why-use-cognitive-search"></a>Perché usare Ricerca cognitiva

Ricerca cognitiva di Azure è una soluzione particolarmente adatta agli scenari di applicazione seguenti:

+ Consolidamento di contenuti eterogenei in un indice di ricerca privato definito dall'utente. È possibile popolare un indice con flussi di documenti JSON da qualsiasi origine. Per le origini supportate in Azure, usare un *indicizzatore* per automatizzare l'indicizzazione. Il controllo sullo schema dell'indice e sulla pianificazione degli aggiornamenti è uno dei vantaggi principali di Ricerca cognitiva.

+ Facile implementazione delle funzionalità correlate alla ricerca. Le API di ricerca semplificano la costruzione di query, l'esplorazione in base a facet, i filtri (inclusa la ricerca geospaziale), il mapping dei sinonimi, il completamento automatico e l'ottimizzazione della pertinenza. Grazie alle funzionalità predefinite, è possibile soddisfare le aspettative degli utenti finali per un'esperienza di ricerca simile ai motori di ricerca Web commerciali.

+ Il contenuto non elaborato è costituito da testo non differenziato, file di immagine o file applicazione di grandi dimensioni archiviati nell'archiviazione BLOB di Azure o in Cosmos DB. È possibile applicare le [competenze cognitive](cognitive-search-concept-intro.md) durante l'indicizzazione per identificare ed estrarre testo, creare una struttura o nuove informazioni, ad esempio testo tradotto o entità.

+ Per il contenuto è necessaria un'analisi del testo linguistica o personalizzata. Per il contenuto non in lingua inglese, Ricerca cognitiva di Azure supporta sia gli analizzatori Lucene che i processori di linguaggio naturale Microsoft. È anche possibile configurare gli analizzatori per ottenere un'elaborazione specializzata di contenuto non elaborato, ad esempio l'esclusione tramite filtro dei segni diacritici o il riconoscimento e la conservazione dei criteri nelle stringhe.

Per altre informazioni sulle funzionalità specifiche, vedere [Funzionalità di Ricerca cognitiva di Azure](search-features-list.md)

## <a name="how-to-get-started"></a>Operazioni preliminari

Per un'esplorazione end-to-end delle principali funzionalità di ricerca, seguire questi quattro passaggi:

1. [**Creare un servizio di ricerca**](search-create-service-portal.md) nel livello gratuito condiviso con altri sottoscrittori o scegliere un [livello a pagamento](https://azure.microsoft.com/pricing/details/search/) per avere risorse dedicate usate solo dal proprio servizio. Tutte le guide introduttive e le esercitazioni possono essere completate nel servizio gratuito.

1. [**Creare un indice di ricerca**](search-what-is-an-index.md) usando il portale, l'[API REST](/rest/api/searchservice/create-index), [.NET SDK](search-howto-dotnet-sdk.md) un altro SDK. Lo schema di indice definisce la struttura del contenuto ricercabile.

1. [**Caricare il contenuto**](search-what-is-data-import.md) nell'indice. Usare il [modello "push"](tutorial-optimize-indexing-push-api.md) per eseguire il push di documenti JSON provenienti da qualsiasi origine oppure usare il [modello "pull" (indicizzatori)](search-indexer-overview.md) se i dati di origine si trovano in Azure.

1. [**Eseguire query su un indice**](search-query-overview.md) usando [Esplora ricerche](search-explorer.md) nel portale, l'[API REST](search-get-started-rest.md), [.NET SDK](/dotnet/api/azure.search.documents.searchclient.search) o un altro SDK.

> [!TIP]
> Consolidare i passaggi iniziando dalla [**procedura guidata Importa dati**](search-get-started-portal.md) e da un'origine dati di Azure per creare, caricare ed eseguire query su un indice in pochi minuti.

## <a name="how-it-compares"></a>Confronto

Spesso i clienti chiedono quali siano le differenze tra Ricerca cognitiva di Azure e le altre soluzioni di ricerca. Nella tabella seguente sono riepilogate le principali differenze.

| Confronto con | Differenze principali |
|-------------|-----------------|
| Microsoft Search | [Microsoft Search](https://docs.microsoft.com/microsoftsearch/overview-microsoft-search) è destinato agli utenti autenticati di Microsoft 365 che devono eseguire query sui contenuti in SharePoint. Viene offerto come esperienza di ricerca pronta per l'uso, abilitata e configurata da amministratori, con la possibilità di accettare contenuto esterno tramite connettori Microsoft e di altre origini. Se questa descrizione corrisponde al proprio scenario, Microsoft Search con Microsoft 365 è un'opzione interessante da esplorare.<br/><br/>Ricerca cognitiva di Azure, invece, esegue le query su un indice definito dall'utente, popolato con dati e documenti di cui l'utente è proprietario e spesso aventi origini diverse. Ricerca cognitiva di Azure offre funzionalità di ricerca per indicizzazione per alcune origini dati di Azure tramite gli [indicizzatori](search-indexer-overview.md), ma è possibile eseguire il push di qualsiasi documento JSON conforme allo schema di indicizzazione in un'unica risorsa ricercabile consolidata. È anche possibile personalizzare la pipeline di indicizzazione per includere funzionalità di Machine Learning e analizzatori lessicali. Poiché Ricerca cognitiva è progettato come componente plug-in in soluzioni di dimensioni maggiori, è possibile integrare la ricerca in quasi tutte le app, su qualsiasi piattaforma.|
|Bing | [API Ricerca Web Bing](../cognitive-services/bing-web-search/index.yml) ricerca i termini corrispondenti inviati negli indici in Bing.com. Gli indici sono generati da contenuti Web HTML, XML e di altro tipo nei siti pubblici. Con la medesima base, [Ricerca personalizzata Bing ](/azure/cognitive-services/bing-custom-search/) offre la stessa tecnologia di ricerca Web per tipi di contenuto Web aventi come ambito siti Web specifici.<br/><br/>In Ricerca cognitiva è possibile definire e popolare l'indice. È possibile usare gli [indicizzatori](search-indexer-overview.md) per eseguire la ricerca per indicizzazione dei dati in origini dati di Azure o eseguire il push di qualsiasi documento JSON conforme all'indice nel servizio di ricerca. |
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