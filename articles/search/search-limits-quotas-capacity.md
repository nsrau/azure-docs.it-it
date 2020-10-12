---
title: Limiti del servizio per piani e SKU
titleSuffix: Azure Cognitive Search
description: Limiti del servizio usati per la pianificazione della capacità e limiti massimi per richieste e risposte in Ricerca cognitiva di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 7e3a5fcdcead402d59e680fcba270e80b4eda8bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843431"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Limiti del servizio in Ricerca cognitiva di Azure

I limiti massimi per archiviazione, carichi di lavoro e quantità di indici e altri oggetti dipendono dal [piano tariffario scelto per Ricerca cognitiva di Azure](search-create-service-portal.md): **Gratuito**, **Basic**, **Standard** o **Ottimizzato per l'archiviazione**.

+ **gratuito** è un servizio condiviso multi-tenant fornito con la sottoscrizione di Azure. 

+ Il piano **Basic** fornisce risorse di calcolo dedicate per i carichi di lavoro di produzione su scala più ridotta, ma condivide alcune infrastrutture di rete con altri tenant.

+ Il piano **Standard** prevede computer dedicati con maggiore capacità di elaborazione e archiviazione a ogni livello. Il piano Standard è disponibile in quattro livelli: S1, S2, S3 ed S3 HD. S3 Alta densità (S3 HD) è progettato per configurazioni [multi-tenancy](search-modeling-multitenant-saas-applications.md) e grandi quantità di indici di dimensioni ridotte (tremila indici per ogni servizio). S3 HD non fornisce la [funzionalità dell'indicizzatore](search-indexer-overview.md) e l'inserimento dei dati deve sfruttare le API che eseguono il push dei dati dall'origine all'indice. 

+ Il piano **Ottimizzato per l'archiviazione** viene eseguito su computer dedicati con una maggiore quantità totale di archiviazione, larghezza di banda di archiviazione e memoria rispetto al piano **Standard**. Questo piano è destinato agli indici di grandi dimensioni e a modifica lenta. Il piano Ottimizzato per l'archiviazione comprende due livelli: L1 e L2.

## <a name="subscription-limits"></a>Limiti delle sottoscrizioni
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Limiti relativi ad Archiviazione
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Limiti per gli indici

| Risorsa | Gratuito | Basic&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Numero massimo di indici |3 |5 o 15 |50 |200 |200 |1000 per partizione o 3000 per servizio |10 |10 |
| Numero massimo di campi semplici per indice |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Numero massimo di campi di raccolta complessi per indice |40 |40 |40 |40 |40 |40 |40 |40 |
| Numero massimo di elementi in tutte le raccolte complesse per documento&nbsp;<sup>2</sup> |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Profondità massima dei campi complessi |10 |10 |10 |10 |10 |10 |10 |10 |
| Numero massimo di [suggerimenti](/rest/api/searchservice/suggesters) per indice |1 |1 |1 |1 |1 |1 |1 |1 |
| Numero massimo di [profili di punteggio](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) per indice |100 |100 |100 |100 |100 |100 |100 |100 |
| Numero massimo di funzioni per profilo |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> I servizi Basic creati prima di dicembre 2017 hanno limiti inferiori (5 anziché 15) per gli indici. Il livello Basic è l'unico SKU con un limite inferiore a 100 campi per indice.

<sup>2</sup> esiste un limite superiore per gli elementi perché la presenza di un numero elevato di tali elementi aumenta significativamente lo spazio di archiviazione necessario per l'indice. Un elemento di una raccolta complessa viene definito come membro di tale raccolta. Si supponga, ad esempio, che un documento di un [Hotel con una raccolta di chat](search-howto-complex-data-types.md#indexing-complex-types)room, ogni stanza della raccolta room sia considerata un elemento. Durante l'indicizzazione, il motore di indicizzazione può elaborare in modo sicuro un massimo di 3000 elementi nell'intero documento. [Questo limite](search-api-migration.md#upgrade-to-2019-05-06) è stato introdotto in `api-version=2019-05-06` e si applica solo a raccolte complesse, non a raccolte di stringhe o a campi complessi.

<a name="document-limits"></a>

## <a name="document-limits"></a>Limiti per i documenti 

A partire da ottobre 2018, non vi sono più limiti relativi al numero di documenti per i nuovi servizi creati in qualsiasi livello fatturabile (Basic, S1, S2, S3, S3 HD) in qualsiasi area. I servizi meno recenti creati prima di ottobre 2018 possono comunque essere soggetti a limiti per il numero di documenti.

Per determinare se il servizio dispone di limiti per i documenti, usare l'[API REST GET Service Statistics](/rest/api/searchservice/get-service-statistics). I limiti per i documenti sono riportati nella risposta, con `null` che indica nessun limite.

> [!NOTE]
> Sebbene non esistano limiti per i documenti applicati dal servizio, è previsto un limite di partizionamento pari a circa 24 miliardi di documenti per indice nei servizi di ricerca Basic, S1, S2 e S3. Per S3 HD, il limite di partizionamento è di 2 miliardi documenti per indice. Ogni elemento di una raccolta complessa viene conteggiato come un documento distinto in termini di limiti di partizionamento.

### <a name="document-size-limits-per-api-call"></a>Limiti di dimensioni dei documenti per chiamate all'API

La dimensione massima del documento quando si chiama un'API di indice è di circa 16 MB.

La dimensione del documento costituisce effettivamente un limite alla dimensione del corpo della richiesta dell'API di indice. Poiché è possibile passare all'API di indice un batch costituito da più documenti in una sola volta, il limite della dimensione dipende in effetti dal numero di documenti presenti nel batch. Per un batch con un solo documento, la dimensione massima del documento è di 16 MB di JSON.

Quando si stimano le dimensioni del documento, ricordare di considerare solo i campi che possono essere usati da un servizio di ricerca. I dati binari o di tipo image nei documenti di origine devono essere omessi dai calcoli.  

## <a name="indexer-limits"></a>Limiti dell'indicizzatore

Sono previsti tempi di esecuzione massimi per garantire il bilanciamento e la stabilità del servizio nel complesso, ma i set di dati di maggiori dimensioni potrebbero richiedere più tempo di indicizzazione rispetto al massimo consentito. Se un processo di indicizzazione non può essere completato nel tempo massimo consentito, provare a eseguirlo in una pianificazione. L'utilità di pianificazione tiene traccia dello stato di indicizzazione. Se un processo di indicizzazione pianificato viene interrotto per qualsiasi motivo, alla successiva esecuzione pianificata l'indicizzatore può riprendere dall'ultima interruzione.


| Risorsa | Gratuito&nbsp;<sup>1</sup> | Basic&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Numero massimo di indicizzatori |3 |5 o 15|50 |200 |200 |N/D |10 |10 |
| Numero massimo di origini dati |3 |5 o 15 |50 |200 |200 |N/D |10 |10 |
| Numero massimo di set di competenze <sup>4</sup> |3 |5 o 15 |50 |200 |200 |N/D |10 |10 |
| Carico di indicizzazione massimo per chiamata |10.000 documenti |Limitato solo da numero massimo di documenti |Limitato solo da numero massimo di documenti |Limitato solo da numero massimo di documenti |Limitato solo da numero massimo di documenti |N/D |Nessun limite |Nessun limite |
| Pianificazione minima | 5 minuti |5 minuti |5 minuti |5 minuti |5 minuti |5 minuti |5 minuti | 5 minuti |
| Tempo massimo di esecuzione| 1-3 minuti |24 ore |24 ore |24 ore |24 ore |N/D  |24 ore |24 ore |
| Tempo massimo di esecuzione per gli indicizzatori con un insieme di competenze <sup>5</sup> | 3-10 minuti |2 ore |2 ore |2 ore |2 ore |N/D  |2 ore |2 ore |
| Indicizzatore BLOB: dimensioni massime per un BLOB, MB |16 |16 |128 |256 |256 |N/D  |256 |256 |
| Indicizzatore BLOB: numero massimo di caratteri di contenuto estratti da un BLOB |32.000 |64.000 |4&nbsp;milioni |8&nbsp;milioni |16&nbsp;milioni |N/D |4&nbsp;milioni |4&nbsp;milioni |

<sup>1</sup> I servizi del livello Gratuito hanno un tempo massimo di esecuzione degli indicizzatori di 3 minuti per le origini BLOB e di 1 minuto per tutte le altre origini dati. Per l'indicizzazione di intelligenza artificiale che effettua chiamate in Servizi cognitivi, i servizi gratuiti sono limitati a 20 transazioni gratuite al giorno, dove una transazione è definita come un documento che passa correttamente attraverso la pipeline di arricchimento.

<sup>2</sup> I servizi Basic creati prima di dicembre 2017 hanno limiti inferiori (5 anziché 15) per gli indicizzatori, le origini dati e i set di competenze.

<sup>3</sup> I servizi del livello S3 HD non includono il supporto per l'indicizzatore.

<sup>4</sup> Numero massimo di 30 competenze per set di competenze.

<sup>5</sup> L'arricchimento tramite intelligenza artificiale e l'analisi delle immagini sono attività complesse e usano una quantità estremamente elevata di potenza di elaborazione disponibile. Il tempo di esecuzione per questi carichi di lavoro è stato abbreviato per offrire maggiori possibilità di eseguire altri processi nella coda.

> [!NOTE]
> Come indicato in [Limiti per gli indici](#index-limits), gli indicizzatori applicheranno anche il limite superiore di 3000 elementi in tutte le raccolte complesse per documento a partire dalla versione più recente dell'API disponibile a livello generale che supporta i tipi complessi (`2019-05-06`). Se l'indicizzatore è stato creato con una versione precedente dell'API, non sarà soggetto a questo limite. Per mantenere la massima compatibilità, un indicizzatore creato con una versione precedente dell'API e quindi aggiornato con una versione dell'API `2019-05-06` o successiva verrà comunque **escluso** dai limiti. I clienti devono essere consapevoli dell'impatto negativo della presenza di raccolte complesse di grandi dimensioni (come indicato in precedenza) ed è consigliabile creare nuovi indicizzatori con la versione più recente dell'API disponibile a livello generale.

### <a name="shared-private-link-resource-limits"></a>Limiti delle risorse di collegamento privato condiviso

> [!NOTE]
> Gli indicizzatori possono accedere alle risorse in modo sicuro sugli endpoint privati gestiti tramite l' [API di risorse di collegamento privato condiviso](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources) , come descritto in [questa guida alle procedure](search-indexer-howto-access-private.md)

| Risorsa | Gratuito | Basic | S1 | S2 | S3 | S3 HD | L1 | L2
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Supporto dell'indicizzatore di endpoint privato | No | Sì | Sì | Sì | Sì | No | Sì | Sì |
| Supporto degli endpoint privati per gli indicizzatori con un insieme di competenze<sup>1</sup> | No | No | No | Sì | Sì | No | Sì | Sì |
| Numero massimo di endpoint privati | N/D | 10 o 30 | 100 | 400 | 400 | N/D | 20 | 20 |
| Numero massimo di tipi di risorse distinti<sup>2</sup> | N/D | 4 | 7 | 15 | 15 | N/D | 4 | 4 |

<sup>1</sup> le funzionalità di arricchimento e analisi delle immagini per intelligenza artificiale sono a elevato utilizzo di calcolo e consumano una quantità sproporzionata di potenza di elaborazione disponibile e pertanto per i livelli di servizio di ricerca più bassi che li configurano per l'esecuzione nell'ambiente privato potrebbe avere un impatto negativo sulle prestazioni e la stabilità del servizio di ricerca.

<sup>2</sup> il numero di tipi di risorse distinti viene calcolato come numero di valori univoci `groupId` usati in tutte le risorse di collegamento privato condivise per un determinato servizio di ricerca, indipendentemente dallo stato della risorsa.

## <a name="synonym-limits"></a>Limiti per i sinonimi

Il numero massimo di mappe di sinonimi varia in base al livello. Ogni regola può avere fino a 20 espansioni, dove un'espansione è un termine equivalente. Se ad esempio si specifica "gatto", l'associazione con "gattino", "felino" e "felis" (il genere dei gatti) viene conteggiata come 3 espansioni.

| Risorsa | Gratuito | Basic | S1 | S2 | S3 | S3-HD |L1 | L2 |
| -------- | -----|------ |----|----|----|-------|---|----|
| Numero massimo di mappe di sinonimi |3 |3|5 |10 |20 |20 | 10 | 10 |
| Numero massimo di regole per mappa |5000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="queries-per-second-qps"></a>Query al secondo

Le stime di query al secondo devono essere sviluppate in modo indipendente da ogni cliente. Le dimensioni e la complessità dell'indice, le dimensioni e la complessità della query e la quantità di traffico sono i fattori principali in base ai quali è possibile determinare il numero di query al secondo. Non è possibile fornire stime significative se questi fattori sono sconosciuti.

Le stime sono più prevedibili se vengono calcolate su servizi in esecuzione su risorse dedicate (livelli Basic e Standard). In questo caso, infatti, è possibile stimare in modo più preciso il numero di query al secondo, poiché si ha il controllo di un numero maggiore di parametri. Per informazioni su come eseguire la stima, vedere [Considerazioni sulle prestazioni e sull'ottimizzazione di Ricerca cognitiva di Azure](search-performance-optimization.md).

Per i livelli Ottimizzato per l'archiviazione (L1 e L2), sono previste una velocità effettiva delle query inferiore e una latenza superiore rispetto ai livelli Standard.

## <a name="data-limits-ai-enrichment"></a>Limiti dei dati (arricchimento tramite intelligenza artificiale)

Una [pipeline di arricchimento con intelligenza artificiale](cognitive-search-concept-intro.md) che effettua chiamate a una risorsa di Analisi del testo per il [riconoscimento di entità](cognitive-search-skill-entity-recognition.md), l'[estrazione di frasi chiave](cognitive-search-skill-keyphrases.md), l'[analisi del sentiment](cognitive-search-skill-sentiment.md), il [rilevamento della lingua](cognitive-search-skill-language-detection.md) e il [rilevamento di informazioni personali](cognitive-search-skill-pii-detection.md) è soggetta a limiti dei dati. Le dimensioni massime di un record devono essere di 50.000 caratteri in base alla misurazione di [`String.Length`](/dotnet/api/system.string.length). Se è necessario suddividere i dati prima di inviarli all'analizzatore di valutazione, usare la [competenza cognitiva di divisione del testo](cognitive-search-skill-textsplit.md).

## <a name="throttling-limits"></a>Limiti della limitazione delle richieste

Le richieste di ricerca e di indicizzazione vengono limitate quando il sistema si avvicina alla capacità massima. La limitazione delle richieste si comporta in modo diverso per le diverse API. Le API di query (ricerca/suggerimenti/completamento automatico) e le API di indicizzazione vengono limitate in modo dinamico in base al carico del servizio. Le API di indice hanno limiti statici per la frequenza delle richieste. 

Limiti statici per la frequenza delle richieste per le operazioni correlate a un indice:

+ Elencare gli indici (GET /indexes): 5 al secondo per unità di ricerca
+ Ottenere un indice (GET /indexes/myindex): 10 al secondo per unità di ricerca
+ Creare un indice (POST /indexes): 12 al minuto per unità di ricerca
+ Creare o aggiornare un indice (PUT /indexes/myindex): 6 al secondo per unità di ricerca
+ Eliminare un indice (DELETE /indexes/myindex): 12 al minuto per unità di ricerca 

## <a name="api-request-limits"></a>Limiti delle richieste API
* 16 MB al massimo per <sup>1</sup> richiesta
* 8 KB al massimo per la lunghezza dell'URL
* 1000 documenti al massimo per ogni batch di carichi, unioni o eliminazioni di indice
* 32 campi al massimo nella clausola $orderby
* 32.766 byte (32 KB meno 2 byte) di testo con codifica UTF-8 per la dimensione massima del termine di ricerca

<sup>1</sup> In Ricerca cognitiva di Azure il corpo di una richiesta è soggetto a un limite massimo di 16 MB, che impone un limite pratico ai contenuti di singoli campi o raccolte non vincolati a limiti teorici. Per altre informazioni sulla composizione dei campi e sulle relative restrizioni, vedere [Tipi di dati supportati](/rest/api/searchservice/supported-data-types).

## <a name="api-response-limits"></a>Limiti delle risposte API
* 1000 documenti al massimo restituiti per pagina di risultati della ricerca
* 100 suggerimenti al massimo restituiti per richiesta di API di suggerimento

## <a name="api-key-limits"></a>Limiti delle chiavi API
Le chiavi API vengono usate per l'autenticazione del servizio. Sono disponibili due tipi. Le chiavi amministratore, specificate nell'intestazione della richiesta, consentono l'accesso completo in lettura e scrittura al servizio. Le chiavi di query sono di sola lettura, sono specificate nell'URL e in genere sono distribuite ad applicazioni client.

* 2 chiavi di amministrazione al massimo per ogni servizio
* 50 chiavi di query al massimo per ogni servizio