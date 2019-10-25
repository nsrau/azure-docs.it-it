---
title: Limiti dei servizi per i livelli e gli SKU
titleSuffix: Azure Cognitive Search
description: Limiti di servizio usati per la pianificazione della capacità e limiti massimi per richieste e risposte per ricerca cognitiva di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d70812779d392cc4555c91599fad37c2d2c68ba5
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793572"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Limiti dei servizi in Azure ricerca cognitiva

I limiti massimi per archiviazione, carichi di lavoro e quantità di indici, documenti e altri oggetti variano a seconda che venga effettuato il [provisioning di Azure ricerca cognitiva](search-create-service-portal.md) ai piani tariffari **gratuito**, **Basic**, **standard**o **ottimizzato** per l'archiviazione .

+ **gratuito** è un servizio condiviso multi-tenant fornito con la sottoscrizione di Azure. Le richieste di indicizzazione e query vengono eseguite su repliche e partizioni utilizzate da altri tenant.

+ **Basic** fornisce risorse di elaborazione dedicate per carichi di lavoro di produzione su scala ridotta, ma condivide alcune infrastrutture di rete con altri tenant.

+ Il piano **Standard** prevede computer dedicati con maggiore capacità di elaborazione e archiviazione a ogni livello. Il piano Standard è disponibile in quattro livelli: S1, S2, S3 ed S3 HD.

+ L' **archiviazione ottimizzata** viene eseguita su computer dedicati con maggiore spazio di archiviazione totale, larghezza di banda di archiviazione e memoria superiore a quella **standard**. L'archiviazione ottimizzata è in due livelli: L1 e L2

> [!NOTE]
> A partire dal 1 ° luglio, tutti i livelli sono disponibili a livello generale, incluso il livello ottimizzato per l'archiviazione. Tutti i prezzi sono disponibili nella pagina [Dettagli prezzi](https://azure.microsoft.com/pricing/details/search/) .

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

| Gruppi | Gratis | Base&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Numero massimo di indici |3 |5 o 15 |50 |200 |200 |1000 per partizione o 3000 per servizio |10 |10 |
| Numero massimo di campi semplici per indice |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Numero massimo di campi di raccolta complessi per indice |40 |40 |40 |40 |40 |40 |40 |40 |
| Numero massimo di elementi in tutte le raccolte complesse per documento |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Profondità massima dei campi complessi |10 |10 |10 |10 |10 |10 |10 |10 |
| Numero massimo di [suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggesters) per indice |1 |1 |1 |1 |1 |1 |1 |1 |
| Numero massimo di [profili di punteggio](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) per indice |100 |100 |100 |100 |100 |100 |100 |100 |
| Numero massimo di funzioni per profilo |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> i servizi di base creati prima del 2017 dicembre hanno limiti inferiori (5 anziché 15) sugli indici. Il livello Basic è l'unico SKU con un limite inferiore a 100 campi per indice.

<a name="document-limits"></a>

## <a name="document-limits"></a>Limiti per i documenti 

A partire dal 2018 ottobre, non sono più presenti limiti di documento<sup>1</sup> per qualsiasi nuovo servizio creato a qualsiasi livello fatturabile (Basic, S1, S2, S3, S3 HD) in qualsiasi area. Il limite al numero di documenti è stato abolito per la maggior parte delle aree da novembre/dicembre 2017, ma cinque aree hanno continuato ad imporre limiti sui documenti. A seconda di dove e quando è stato creato un servizio di ricerca, è possibile che si stia eseguendo un servizio ancora soggetto a limiti sui documenti.

Per determinare se il servizio dispone di limiti per i documenti, controllare il riquadro Utilizzo nella pagina Panoramica del servizio. Il numero di documenti è illimitato o sottoposto a un limite in base al livello.

  ![Riquadro Utilizzo](media/search-limits-quotas-capacity/portal-usage-tile.png)

<sup>1</sup> anche se non sono previsti limiti per i documenti specifici dello SKU, ogni indice è ancora soggetto a un limite massimo sicuro per garantire la stabilità del servizio. Questo limite deriva da Lucene. Ogni documento di ricerca cognitiva di Azure viene indicizzato internamente come uno o più documenti Lucene. Il numero di documenti Lucene per ogni documento di ricerca dipende dal numero totale di elementi nei campi di raccolta complessi. Ogni elemento viene indicizzato come documento Lucene separato. Un documento con 3 elementi in un campo di raccolta complesso, ad esempio, verrà indicizzato come 4 documenti Lucene-1 per il documento stesso e 3 per gli elementi. Il numero massimo di documenti Lucene è approssimativamente 25 miliardi per indice.

### <a name="regions-previously-having-document-limits"></a>Aree che in precedenza avevano limiti sui documenti

Se il portale indica un limite di documenti, il servizio è stato creato prima della fine del 2017 o è stato creato in un data center usando cluster con capacità inferiore per l'hosting dei servizi ricerca cognitiva di Azure:

+ Australia orientale
+ Asia orientale
+ India centrale
+ Giappone occidentale
+ Stati Uniti centro-occidentali

Per i servizi soggetti a limiti per i documenti, si applicano i limiti massimi seguenti:

|  Gratis | Basic | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10,000 |1 @ no__t_0_ milioni |15 milioni per partizione o 180 milioni per servizio |60 milioni per partizione o 720 milioni per servizio |120 milioni per partizione o 1,4 miliardi per servizio |1 milione per indice o 200 milioni per partizione |

Se il servizio ha dei limiti che costituiscono un blocco, creare un nuovo servizio e pubblicarvi nuovamente tutti i contenuti. Non è previsto alcun meccanismo automatico per eseguire nuovamente il provisioning del servizio nel nuovo hardware in background.

> [!Note] 
> Per i servizi S3 ad alta densità creati dopo la fine del 2017, è stato rimosso il limite di 200 milioni di documenti per partizione, ma rimane il limite di 1 milione di documenti per indice.


### <a name="document-size-limits-per-api-call"></a>Limiti di dimensioni dei documenti per chiamate all'API

La dimensione massima del documento quando si chiama un'API di indice è di circa 16 MB.

La dimensione del documento costituisce effettivamente un limite alla dimensione del corpo della richiesta dell'API di indice. Poiché è possibile passare all'API di indice un batch costituito da più documenti in una sola volta, il limite della dimensione dipende in effetti dal numero di documenti presenti nel batch. Per un batch con un solo documento, la dimensione massima del documento è di 16 MB di JSON.

Per limitare la dimensione del documento, è necessario escludere dalla richiesta i dati non disponibili per query. Le immagini e altri dati binari non sono direttamente disponibili per query e non devono quindi essere archiviati nell'indice. Per integrare i dati non disponibili per query nei risultati della ricerca, definire un campo non disponibile per la ricerca che consenta l'archiviazione di un riferimento URL nella risorsa.

## <a name="indexer-limits"></a>Limiti dell'indicizzatore

Sono disponibili tempi di esecuzione massimi per fornire bilanciamento e stabilità al servizio nel suo complesso, ma i set di dati di dimensioni maggiori potrebbero richiedere più tempo di indicizzazione rispetto al massimo consentito. Se un processo di indicizzazione non può essere completato nel tempo massimo consentito, provare a eseguirlo in una pianificazione. L'utilità di pianificazione tiene traccia dello stato di indicizzazione. Se un processo di indicizzazione pianificato viene interrotto per qualsiasi motivo, alla successiva esecuzione pianificata l'indicizzatore può riprendere dall'ultima interruzione.


| Gruppi | Gratuito&nbsp;<sup>1</sup> | Basic&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Numero massimo di indicizzatori |3 |5 o 15|50 |200 |200 |N/D |10 |10 |
| Numero massimo di origini dati |3 |5 o 15 |50 |200 |200 |N/D |10 |10 |
| Numero massimo di set di competenze <sup>4</sup> |3 |5 o 15 |50 |200 |200 |N/D |10 |10 |
| Carico di indicizzazione massimo per chiamata |10.000 documenti |Limitato solo da numero massimo di documenti |Limitato solo da numero massimo di documenti |Limitato solo da numero massimo di documenti |Limitato solo da numero massimo di documenti |N/D |Senza limiti |Senza limiti |
| Pianificazione minima | 5 minuti |5 minuti |5 minuti |5 minuti |5 minuti |5 minuti |5 minuti | 5 minuti |
| Tempo massimo di esecuzione <sup>5</sup> | 1-3 minuti |24 ore |24 ore |24 ore |24 ore |N/D  |24 ore |24 ore |
| Tempo massimo di esecuzione per set di competenze di ricerca cognitiva o indicizzazione BLOB con analisi delle immagini <sup>5</sup> | 3-10 minuti |2 ore |2 ore |2 ore |2 ore |N/D  |2 ore |2 ore |
| Indicizzatore BLOB: dimensioni massime per un BLOB, MB |16 |16 |128 |256 |256 |N/D  |256 |256 |
| Indicizzatore BLOB: numero massimo di caratteri di contenuto estratti da un BLOB |32.000 |64.000 |4 @ no__t_0_ milioni |4 @ no__t_0_ milioni |4 @ no__t_0_ milioni |N/D |4 @ no__t_0_ milioni |4 @ no__t_0_ milioni |

<sup>1</sup> I servizi del livello Gratuito hanno un tempo massimo di esecuzione degli indicizzatori di 3 minuti per le origini BLOB e di 1 minuto per tutte le altre origini dati. Per l'indicizzazione di intelligenza artificiale che effettua chiamate in Servizi cognitivi, i servizi gratuiti sono limitati a 20 transazioni gratuite al giorno, in cui una transazione viene definita come documento che passa correttamente attraverso la pipeline di arricchimento.

<sup>2</sup> i servizi Basic creati prima del 2017 dicembre hanno limiti inferiori (5 anziché 15) per gli indicizzatori, le origini dati e skillsets.

<sup>3</sup> I servizi del livello S3 HD non includono il supporto per l'indicizzatore.

<sup>4</sup> Numero massimo di 30 competenze per set di competenze.

<sup>5</sup> I carichi di lavoro di ricerca cognitiva e l'analisi delle immagini nell'indicizzazione BLOB di Azure presentano tempi di esecuzione più brevi rispetto all'indicizzazione di testo normale. L'analisi delle immagini e l'elaborazione del linguaggio naturale sono attività complesse e utilizzano una quantità estremamente elevata di potenza di elaborazione disponibile. Il tempo di esecuzione è stato ridotto per offrire la possibilità di eseguire altri processi nella coda.  

## <a name="synonym-limits"></a>Limiti dei sinonimi

Il numero massimo di mappe sinonime consentite varia in base al piano tariffario. Ogni regola può avere fino a 20 espansioni, dove un'espansione è un termine equivalvent. Ad esempio, se si specifica "Cat", l'associazione con "Kitty", "Feline" e "Felis" (il genere per Cats) viene conteggiata come 3 espansioni.

| Gruppi | Gratis | Basic | S1 | S2 | S3 | S3-HD |L1 | L2 |
| -------- | -----|------ |----|----|----|-------|---|----|
| Mappe sinonimo massime |3 |3|5 |10 |20 |20 | 10 | 10 |
| Numero massimo di regole per mappa |5000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="queries-per-second-qps"></a>Query al secondo

Le stime di query al secondo devono essere sviluppate in modo indipendente da ogni cliente. Le dimensioni e la complessità dell'indice, le dimensioni e la complessità della query e la quantità di traffico sono i fattori principali in base ai quali è possibile determinare il numero di query al secondo. Non è possibile fornire stime significative se questi fattori sono sconosciuti.

Le stime sono più prevedibili se vengono calcolate su servizi in esecuzione su risorse dedicate (livelli Basic e Standard). In questo caso, infatti, è possibile stimare in modo più preciso il numero di query al secondo, poiché si ha il controllo di un numero maggiore di parametri. Per istruzioni su come approcciare la stima, vedere l'articolo relativo alle [prestazioni e all'ottimizzazione di Azure ricerca cognitiva](search-performance-optimization.md).

Per i livelli ottimizzati per l'archiviazione è necessario prevedere una velocità effettiva delle query inferiore e una latenza superiore rispetto ai livelli standard.  La metodologia per stimare le prestazioni delle query che si verificheranno è identica a quella dei livelli standard.

## <a name="data-limits-ai-enrichment"></a>Limiti dei dati (arricchimento AI)

Una [pipeline di arricchimento di intelligenza artificiale](cognitive-search-concept-intro.md) che effettua chiamate a una risorsa analisi del testo per il [riconoscimento di entità](cognitive-search-skill-entity-recognition.md), l' [estrazione di frasi chiave](cognitive-search-skill-keyphrases.md), l' [analisi dei sentimenti](cognitive-search-skill-sentiment.md)e il [rilevamento della lingua](cognitive-search-skill-language-detection.md) sono soggetti a limiti di dati. La dimensione massima di un record deve essere di 50.000 caratteri misurata da [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Se è necessario suddividere i dati prima di inviarli all'analizzatore di valutazione, usare la [competenza cognitiva di divisione del testo](cognitive-search-skill-textsplit.md).

## <a name="throttling-limits"></a>Limiti di limitazione

Le richieste di ricerca e di indicizzazione sono limitate perché il sistema si avvicina alla capacità massima. La limitazione delle richieste si comporta in modo diverso per le diverse API. Le API di query (ricerca/suggerimenti/Completamento automatico) e le API di indicizzazione vengono limitate in modo dinamico in base al carico del servizio. Le API di indice hanno limiti di frequenza delle richieste statiche. 

Limiti di richieste di frequenza statica per le operazioni correlate a un indice:

+ Elenca gli indici (GET/indexes): 5 al secondo per unità di ricerca
+ Get index (GET/Indexes/myIndex): 10 al secondo per unità di ricerca
+ Create index (POST/indexes): 12 al minuto per unità di ricerca
+ Crea o Aggiorna indice (PUT/Indexes/myIndex): 6 al secondo per unità di ricerca
+ Elimina indice (Elimina/Indexes/myIndex): 12 al minuto per unità di ricerca 

## <a name="api-request-limits"></a>Limiti delle richieste API
* 16 MB al massimo per <sup>1</sup> richiesta
* 8 KB al massimo per la lunghezza dell'URL
* 1000 documenti al massimo per ogni batch di carichi, unioni o eliminazioni di indice
* 32 campi al massimo nella clausola $orderby
* 32.766 byte (32 KB meno 2 byte) di testo con codifica UTF-8 per la dimensione massima del termine di ricerca

<sup>1</sup> In Azure ricerca cognitiva, il corpo di una richiesta è soggetto a un limite massimo di 16 MB, imponendo un limite pratico sul contenuto di singoli campi o raccolte che non sono altrimenti vincolati da limiti teorici (vedere [tipi di dati supportati). ](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)per ulteriori informazioni sulla composizione e sulle restrizioni dei campi.

## <a name="api-response-limits"></a>Limiti di risposta API
* 1000 documenti al massimo restituiti per pagina di risultati della ricerca
* 100 suggerimenti al massimo restituiti per richiesta di API di suggerimento

## <a name="api-key-limits"></a>Limiti delle chiavi API
Le chiavi API vengono usate per l'autenticazione del servizio. Sono disponibili due tipi. Le chiavi amministratore, specificate nell'intestazione della richiesta, consentono l'accesso completo in lettura e scrittura al servizio. Le chiavi di query sono di sola lettura, sono specificate nell'URL e in genere sono distribuite ad applicazioni client.

* 2 chiavi di amministrazione al massimo per ogni servizio
* 50 chiavi di query al massimo per ogni servizio
