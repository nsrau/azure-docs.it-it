---
title: Limiti del servizio per livelli e skusService limits for tiers and skus
titleSuffix: Azure Cognitive Search
description: Limiti del servizio usati per la pianificazione della capacità e i limiti massimi di richieste e risposte per Ricerca cognitiva di Azure.Service limits used for capacity planning and maximum limits on requests and responses for Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 6ee339cb709a5d825b39b4accf294761c99ee41a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282978"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Limiti del servizio in Ricerca cognitiva di Azure

I limiti massimi per l'archiviazione, i carichi di lavoro e le quantità di indici e altri oggetti dipendono dal provisioning di [Ricerca cognitiva](search-create-service-portal.md) di Azure nei livelli di prezzo **Gratuito**, **Basic,** **Standard**o **Storage Optimized.**

+ **gratuito** è un servizio condiviso multi-tenant fornito con la sottoscrizione di Azure. Le richieste di indicizzazione e query vengono eseguite su repliche e partizioni utilizzate da altri tenant.

+ **Basic** fornisce risorse di elaborazione dedicate per carichi di lavoro di produzione su scala ridotta, ma condivide alcune infrastrutture di rete con altri tenant.

+ **Standard** funziona su macchine dedicate con più capacità di stoccaggio ed elaborazione a tutti i livelli. Il piano Standard è disponibile in quattro livelli: S1, S2, S3 ed S3 HD.

+ **Storage Optimized** viene eseguito su computer dedicati con più spazio di archiviazione totale, larghezza di banda di archiviazione e memoria rispetto a **Standard**. Storage Optimized è disponibile in due livelli: L1 e L2

> [!NOTE]
> A partire dal 1 luglio, tutti i livelli sono generalmente disponibili, incluso il livello Ottimizzazione archiviazione. Tutti i prezzi sono disponibili nella pagina [Dettagli prezzo.](https://azure.microsoft.com/pricing/details/search/)

  S3 Alta densità (S3 HD) è progettato per carichi di lavoro specifici: [multi-tenancy](search-modeling-multitenant-saas-applications.md) e grandi quantità di indici di dimensioni ridotte (un milione di documenti per ogni indice, tremila indici per ogni servizio). Questo livello non contiene la [funzionalità indicizzatore](search-indexer-overview.md). In S3 HD, l'inserimento dati deve sfruttare l'approccio push, usando le chiamate API per eseguire il push dei dati dall'origine all'indice. 

> [!NOTE]
> Il provisioning di un servizio viene eseguito in base al piano tariffario specifico. Il passaggio a un nuovo piano tariffario per ottenere più capacità prevede il provisioning di un nuovo servizio (non esiste alcun aggiornamento sul posto). Per altre informazioni, vedere [Scegliere uno SKU o un piano tariffario](search-sku-tier.md). Per altre informazioni sulla regolazione delle capacità all'interno di un servizio di cui è già stato effettuato il provisioning, vedere [Ridimensionare i livelli di risorse per le query e l'indicizzazione dei carichi di lavoro](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Limiti delle sottoscrizioni
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Limiti relativi ad Archiviazione
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Limiti per gli indici

| Risorsa | Gratuito | Basic&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Numero massimo di indici |3 |5 o 15 |50 |200 |200 |1000 per partizione o 3000 per servizio |10 |10 |
| Numero massimo di campi semplici per indiceMaximum simple fields per index |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Numero massimo di campi di raccolta complessi per indiceMaximum complex collection fields per index |40 |40 |40 |40 |40 |40 |40 |40 |
| Numero massimo di elementi&nbsp;in tutte le raccolte complesse per documento<sup>2</sup> |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Profondità massima di campi complessi |10 |10 |10 |10 |10 |10 |10 |10 |
| Numero massimo di [suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggesters) per indice |1 |1 |1 |1 |1 |1 |1 |1 |
| Numero massimo di [profili di punteggio](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) per indice |100 |100 |100 |100 |100 |100 |100 |100 |
| Numero massimo di funzioni per profilo |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> I servizi di base creati prima di dicembre 2017 hanno limiti inferiori (5 anziché 15) sugli indici. Il livello Basic è l'unico SKU con un limite inferiore a 100 campi per indice.

<sup>2</sup> La presenza di un numero molto elevato di elementi in raccolte complesse per documento causa attualmente un utilizzo elevato dello spazio di archiviazione. Questo è un problema noto Nel frattempo, un limite di 3000 è un limite superiore sicuro per tutti i livelli di servizio. Questo limite viene applicato solo per le operazioni di indicizzazione che utilizzano la versione API`2019-05-06`generalmente disponibile (GA) meno recente che supporta i campi di tipo complesso ( ) in poi. Per non interrompere i client che potrebbero usare versioni precedenti dell'API di anteprima (che supportano campi di tipo complesso), non applicheremo questo limite per le operazioni di indicizzazione che usano queste versioni dell'API di anteprima. Si noti che le versioni delle API di anteprima non devono essere usate per gli scenari di produzione ed è consigliabile che i clienti spostino l'ultima versione dell'API GA.

<a name="document-limits"></a>

## <a name="document-limits"></a>Limiti per i documenti 

A partire da ottobre 2018, non vi sono più limiti per i documenti per i nuovi servizi creati in qualsiasi livello fatturabile (Basic, S1, S2, S3, S3 HD) in qualsiasi area. Mentre la maggior parte delle regioni ha avuto un numero illimitato di documenti da novembre/dicembre 2017, ci sono state alcune regioni che hanno continuato a imporre limiti di documento dopo tale data. A seconda di dove e quando è stato creato un servizio di ricerca, è possibile che si stia eseguendo un servizio ancora soggetto a limiti sui documenti.

Per determinare se il servizio dispone di limiti di documenti, utilizzare [l'API REST GET Service Statistics](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics). I limiti del documento si `null` riflettono nella risposta, senza indicare alcun limite.

> [!NOTE]
> Anche se non sono presenti limiti specifici dei documenti SKU, ogni indice è ancora soggetto a un limite massimo di sicurezza per garantire la stabilità del servizio. Questo limite proviene da Lucene. Ogni documento di Ricerca cognitiva di Azure viene indicizzato internamente come uno o più documenti Lucene.Every Azure Cognitive Search document is internally indexed as one or more Lucene documents. Il numero di documenti Lucene per documento di ricerca dipende dal numero totale di elementi nei campi di raccolta complessi. Ogni elemento viene indicizzato come documento Lucene separato. Ad esempio, un documento con 3 elementi in un campo di raccolta complesso, verrà indicizzato come 4 documenti Lucene - 1 per il documento stesso e 3 per gli elementi. Il numero massimo di documenti Lucene è di circa 25 miliardi per indice.

### <a name="regions-previously-having-document-limits"></a>Aree che in precedenza avevano limiti sui documenti

Se il portale indica un limite di documenti, il servizio è stato creato prima della fine del 2017 o è stato creato in un data center usando cluster con capacità inferiore per l'hosting dei servizi Ricerca cognitiva di Azure:If the portal indicates a document limit, your service was either created before late 2017, or it was created on a data center using lower-capacity clusters for hosting Azure Cognitive Search services:

+ Australia orientale
+ Asia orientale
+ India centrale
+ Giappone occidentale
+ Stati Uniti centro-occidentali

Per i servizi soggetti a limiti per i documenti, si applicano i limiti massimi seguenti:

|  Gratuito | Basic | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10,000 |1&nbsp;milione di abitanti |15 milioni per partizione o 180 milioni per servizio |60 milioni per partizione o 720 milioni per servizio |120 milioni per partizione o 1,4 miliardi per servizio |1 milione per indice o 200 milioni per partizione |

Se il servizio ha dei limiti che costituiscono un blocco, creare un nuovo servizio e pubblicarvi nuovamente tutti i contenuti. Non è previsto alcun meccanismo automatico per eseguire nuovamente il provisioning del servizio nel nuovo hardware in background.

> [!Note] 
> Per i servizi S3 ad alta densità creati dopo la fine del 2017, è stato rimosso il limite di 200 milioni di documenti per partizione, ma rimane il limite di 1 milione di documenti per indice.


### <a name="document-size-limits-per-api-call"></a>Limiti di dimensioni dei documenti per chiamate all'API

La dimensione massima del documento quando si chiama un'API di indice è di circa 16 MB.

La dimensione del documento costituisce effettivamente un limite alla dimensione del corpo della richiesta dell'API di indice. Poiché è possibile passare all'API di indice un batch costituito da più documenti in una sola volta, il limite della dimensione dipende in effetti dal numero di documenti presenti nel batch. Per un batch con un solo documento, la dimensione massima del documento è di 16 MB di JSON.

Per limitare la dimensione del documento, è necessario escludere dalla richiesta i dati non disponibili per query. Le immagini e altri dati binari non sono direttamente disponibili per query e non devono quindi essere archiviati nell'indice. Per integrare i dati non disponibili per query nei risultati della ricerca, definire un campo non disponibile per la ricerca che consenta l'archiviazione di un riferimento URL nella risorsa.

## <a name="indexer-limits"></a>Limiti dell'indicizzatore

Sono disponibili tempi di esecuzione massimi per garantire equilibrio e stabilità al servizio nel suo complesso, ma i set di dati più grandi potrebbero richiedere più tempo di indicizzazione di quanto il massimo consenta. Se un processo di indicizzazione non può essere completato nel tempo massimo consentito, provare a eseguirlo in una pianificazione. L'utilità di pianificazione tiene traccia dello stato di indicizzazione. Se un processo di indicizzazione pianificato viene interrotto per qualsiasi motivo, alla successiva esecuzione pianificata l'indicizzatore può riprendere dall'ultima interruzione.


| Risorsa | Gratuito&nbsp;<sup>1</sup> | Basic&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Numero massimo di indicizzatori |3 |5 o 15|50 |200 |200 |N/D |10 |10 |
| Numero massimo di origini dati |3 |5 o 15 |50 |200 |200 |N/D |10 |10 |
| Numero massimo di set di competenze <sup>4</sup> |3 |5 o 15 |50 |200 |200 |N/D |10 |10 |
| Carico di indicizzazione massimo per chiamata |10.000 documenti |Limitato solo da numero massimo di documenti |Limitato solo da numero massimo di documenti |Limitato solo da numero massimo di documenti |Limitato solo da numero massimo di documenti |N/D |Nessun limite |Nessun limite |
| Pianificazione minima | 5 minuti |5 minuti |5 minuti |5 minuti |5 minuti |5 minuti |5 minuti | 5 minuti |
| Tempo massimo di esecuzione <sup>5</sup> | 1-3 minuti |24 ore |24 ore |24 ore |24 ore |N/D  |24 ore |24 ore |
| Tempo massimo di esecuzione per set di competenze di ricerca cognitiva o indicizzazione BLOB con analisi delle immagini <sup>5</sup> | 3-10 minuti |2 ore |2 ore |2 ore |2 ore |N/D  |2 ore |2 ore |
| Indicizzatore BLOB: dimensioni massime per un BLOB, MB |16 |16 |128 |256 |256 |N/D  |256 |256 |
| Indicizzatore BLOB: numero massimo di caratteri di contenuto estratti da un BLOB |32.000 |64.000 |4&nbsp;milioni di abitanti |8&nbsp;milioni di abitanti |16&nbsp;milioni di abitanti |N/D |4&nbsp;milioni di abitanti |4&nbsp;milioni di abitanti |

<sup>1</sup> I servizi del livello Gratuito hanno un tempo massimo di esecuzione degli indicizzatori di 3 minuti per le origini BLOB e di 1 minuto per tutte le altre origini dati. Per l'indicizzazione di iaformazione AI che chiama in Servizi cognitivi, i servizi gratuiti sono limitati a 20 transazioni gratuite al giorno, in cui una transazione è definita come un documento che passa correttamente attraverso la pipeline di arricchimento.

<sup>2</sup> I servizi di base creati prima di dicembre 2017 hanno limiti inferiori (5 anziché 15) per indicizzatori, origini dati e set di competenze.

<sup>3</sup> I servizi del livello S3 HD non includono il supporto per l'indicizzatore.

<sup>4</sup> Numero massimo di 30 competenze per set di competenze.

<sup>5</sup> I carichi di lavoro di ricerca cognitiva e l'analisi delle immagini nell'indicizzazione BLOB di Azure presentano tempi di esecuzione più brevi rispetto all'indicizzazione di testo normale. L'analisi delle immagini e l'elaborazione del linguaggio naturale sono attività complesse e utilizzano una quantità estremamente elevata di potenza di elaborazione disponibile. Il tempo di esecuzione è stato ridotto per offrire la possibilità di eseguire altri processi nella coda.  

> [!NOTE]
> Come indicato nei limiti dell'indice , gli indicizzatori applicheranno anche il limite superiore di 3000 elementi`2019-05-06`in tutte le raccolte complesse per documento a partire dall'ultima versione dell'API GA che supporta i tipi complessi ( ) in poi. [Index limits](#index-limits) Ciò significa che se l'indicizzatore è stato creato con una versione precedente dell'API, non sarai soggetto a questo limite. Per mantenere la massima compatibilità, un indicizzatore creato con una `2019-05-06` versione precedente dell'API e quindi aggiornato con una versione API o successiva verrà comunque **escluso** dai limiti. I clienti devono essere consapevoli dell'impatto negativo della presenza di raccolte complesse molto grandi (come indicato in precedenza) ed è consigliabile creare nuovi indicizzatori con l'ultima versione dell'API GA.

## <a name="synonym-limits"></a>Limiti dei sinonimi

Il numero massimo di mappe dei sinonimi consentite varia in base al piano tariffario. Ogni regola può avere fino a 20 espansioni, dove un'espansione è un termine equivalente. Ad esempio, dato "gatto", associazione con "kitty", "felino" e "felis" (il genere per gatti) conterebbe come 3 espansioni.

| Risorsa | Gratuito | Basic | S1 | S2 | S3 | S3-HD |L1 | L2 |
| -------- | -----|------ |----|----|----|-------|---|----|
| Numero massimo di mappe dei sinonimi |3 |3|5 |10 |20 |20 | 10 | 10 |
| Numero massimo di regole per mappa |5000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="queries-per-second-qps"></a>Query al secondo

Le stime di query al secondo devono essere sviluppate in modo indipendente da ogni cliente. Le dimensioni e la complessità dell'indice, le dimensioni e la complessità della query e la quantità di traffico sono i fattori principali in base ai quali è possibile determinare il numero di query al secondo. Non è possibile fornire stime significative se questi fattori sono sconosciuti.

Le stime sono più prevedibili se vengono calcolate su servizi in esecuzione su risorse dedicate (livelli Basic e Standard). In questo caso, infatti, è possibile stimare in modo più preciso il numero di query al secondo, poiché si ha il controllo di un numero maggiore di parametri. Per indicazioni su come affrontare la stima, vedere [Ottimizzazione e prestazioni](search-performance-optimization.md)di Ricerca cognitiva di Azure .

Per i livelli ottimizzati di archiviazione, è necessario prevedere una velocità effettiva delle query inferiore e una latenza superiore rispetto ai livelli Standard.For the Storage Optimized tiers, you should expect a lower query throughput and higher latenztency than the Standard tiers.  La metodologia per la stima delle prestazioni delle query è la stessa dei livelli Standard.The methodology for stimating the query performance you'll experience is the same as the Standard tiers.

## <a name="data-limits-ai-enrichment"></a>Limiti dei dati (arricchimento AI)

Una pipeline di [arricchimento AI](cognitive-search-concept-intro.md) che effettua chiamate a una risorsa Analisi del testo per il riconoscimento delle [entità, l'estrazione](cognitive-search-skill-entity-recognition.md)di [frasi chiave,](cognitive-search-skill-keyphrases.md) [l'analisi del sentiment,](cognitive-search-skill-sentiment.md)il [rilevamento](cognitive-search-skill-language-detection.md)della lingua e il [rilevamento delle informazioni personali](cognitive-search-skill-pii-detection.md) è soggetta a limiti di dati. La dimensione massima di un record deve essere di [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)50.000 caratteri misurata da . Se è necessario suddividere i dati prima di inviarli all'analizzatore di valutazione, usare la [competenza cognitiva di divisione del testo](cognitive-search-skill-textsplit.md).

## <a name="throttling-limits"></a>Limiti di limitazione

Le richieste di query e indicizzazione di ricerca vengono limitate man mano che il sistema si avvicina alla capacità di picco. La limitazione si comporta in modo diverso per le API diverse. Le API di query (Ricerca/Suggerisci/Completamento automatico) e le API di indicizzazione limitano dinamicamente in base al carico sul servizio. Le API dell'indice hanno limiti di frequenza delle richieste statici. 

Limiti delle richieste di tasso statico per le operazioni correlate a un indice:Static rate request limits for operations related to an index:

+ Elenca indici (GET /indexes): 5 al secondo per unità di ricerca
+ Ottieni indice (GET /indexes/myindex): 10 al secondo per unità di ricercaGet Index (GET /indexes/myindex): 10 per second per search unit
+ Crea indice (POST /indici): 12 al minuto per unità di ricerca
+ Crea o aggiorna indice (PUT /indexes/myindex): 6 al secondo per unità di ricerca
+ Elimina indice (DELETE /indexes/myindex): 12 al minuto per unità di ricerca 

## <a name="api-request-limits"></a>Limiti delle richieste API
* 16 MB al massimo per <sup>1</sup> richiesta
* 8 KB al massimo per la lunghezza dell'URL
* 1000 documenti al massimo per ogni batch di carichi, unioni o eliminazioni di indice
* 32 campi al massimo nella clausola $orderby
* 32.766 byte (32 KB meno 2 byte) di testo con codifica UTF-8 per la dimensione massima del termine di ricerca

<sup>1</sup> In Ricerca cognitiva di Azure, il corpo di una richiesta è soggetto a un limite superiore di 16 MB, imponendo un limite pratico al contenuto di singoli campi o raccolte che non sono altrimenti vincolati da limiti teorici (vedere Tipi di [dati supportati](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) per ulteriori informazioni sulla composizione e le restrizioni dei campi).

## <a name="api-response-limits"></a>Limiti di risposta API
* 1000 documenti al massimo restituiti per pagina di risultati della ricerca
* 100 suggerimenti al massimo restituiti per richiesta di API di suggerimento

## <a name="api-key-limits"></a>Limiti delle chiavi API
Le chiavi API vengono utilizzate per l'autenticazione del servizio. Sono disponibili due tipi. Le chiavi amministratore, specificate nell'intestazione della richiesta, consentono l'accesso completo in lettura e scrittura al servizio. Le chiavi di query sono di sola lettura, sono specificate nell'URL e in genere sono distribuite ad applicazioni client.

* 2 chiavi di amministrazione al massimo per ogni servizio
* 50 chiavi di query al massimo per ogni servizio