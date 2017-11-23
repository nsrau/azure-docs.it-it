---
title: Scegliere uno SKU o un piano tariffario per Ricerca di Azure | Microsoft Docs
description: "È possibile effettuare il provisioning di Ricerca di Azure per questi SKU: Gratuito, Basic e Standard; il piano Standard è disponibile in più configurazioni di risorse e livelli di capacità."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 8d4b7bca-02a5-43ee-b3f8-03551dfb32fd
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/24/2016
ms.author: heidist
ms.openlocfilehash: 781683f27c943e25d5629dd846da357f51c9d4f9
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2017
---
# <a name="choose-a-sku-or-pricing-tier-for-azure-search"></a>Scegliere uno SKU o un piano tariffario per Ricerca di Azure
In Ricerca di Azure, il [provisioning del servizio](search-create-service-portal.md) viene effettuato a livello di piano tariffario o SKU specifico. Le opzioni disponibili includono **Gratuito**, **Basic** o **Standard**. Il piano **Standard** è disponibile in più configurazioni e capacità.

Lo scopo di questo articolo è aiutare a scegliere un piano tariffario. Se la capacità si rivela insufficiente, sarà necessario effettuare il provisioning di un nuovo servizio a un piano superiore e quindi ricaricare gli indici. Non è disponibile alcun aggiornamento sul posto dello stesso servizio da uno SKU a un altro.

> [!NOTE]
> Dopo aver scelto un piano e aver effettuato [il provisioning di un servizio di ricerca](search-create-service-portal.md), è possibile aumentare il numero di partizioni e repliche nel servizio. Per indicazioni, vedere [Ridimensionare i livelli di risorse per le query e l'indicizzazione dei carichi di lavoro](search-capacity-planning.md) .
>
>

## <a name="how-to-approach-a-pricing-tier-decision"></a>Come prendere una decisione relativa al piano tariffario
In Ricerca di Azure il livello determina la capacità, non la disponibilità delle funzionalità. In genere, le funzionalità, incluse le funzioni di anteprima, sono disponibili in ogni piano tariffario. L'unica eccezione è l'assenza di supporto degli indicizzatori in S3 HD.

> [!TIP]
> È consigliabile eseguire sempre il provisioning del servizio **Gratuito** (uno per sottoscrizione, senza scadenza), in modo che sia subito disponibile per progetti leggeri. Usare il servizio **Gratuito** per i test e per la valutazione e quindi creare un secondo servizio fatturabile con i piani **Basic** o **Standard** per carichi di lavoro di produzione o di test di dimensioni maggiori.
>
>

La capacità e i costi dell'esecuzione del servizio sono in stretta associazione. Le informazioni in questo articolo consentono di decidere quale SKU offra il giusto equilibrio, tuttavia è comunque necessaria una stima almeno approssimativa degli aspetti seguenti:

* Numero e dimensioni degli indici che si prevede di creare
* Numero e dimensioni dei documenti da caricare
* Stima generica del volume di query in termini di query al secondo. Per informazioni, vedere [Considerazioni sulle prestazioni e sull'ottimizzazione di Ricerca di Azure](search-performance-optimization.md).

Il numero e le dimensioni sono importanti perché i limiti massimi vengono raggiunti tramite un limite rigido relativo al numero di indici in un servizio o alle risorse (di archiviazione o repliche) usate dal servizio. Il limite effettivo per il servizio corrisponde agli elementi (risorse oppure oggetti) usati per primi.

Con le stime a disposizione, la procedura seguente semplificherà il processo:

* **Passaggio 1** Esaminare le descrizioni degli SKU seguenti per ottenere informazioni sulle opzioni disponibili.
* **Passaggio 2** Rispondere alle domande seguenti per arrivare a una decisione preliminare.
* **Passaggio 3** Prendere una decisione definitiva esaminando i limiti rigidi in termini di archiviazione e prezzi.

## <a name="sku-descriptions"></a>Descrizioni degli SKU
La tabella seguente fornisce le descrizioni di ogni piano.

| Livello | Scenari principali |
| --- | --- |
| **Free** |Servizio condiviso gratuito usato per la valutazione, l'analisi o piccoli carichi di lavoro. Dal momento che è condiviso con altri sottoscrittori, la velocità effettiva e l'indicizzazione delle query variano in base agli altri utenti che usano il servizio. La capacità è ridotta (50 MB o 3 indici con un massimo di 10.000 documenti l'uno). |
| **Basic** |Piccoli carichi di lavoro di produzione su hardware dedicato. Disponibilità elevata. La capacità prevede al massimo di 3 e 1 partizione (2 GB). |
| **S1** |Lo Standard 1 supporta combinazioni flessibili di partizioni (12) e repliche (12). Viene usato per i carichi di lavoro di produzione medi su hardware dedicato. È possibile allocare partizioni e repliche in combinazioni supportate da un numero massimo di 36 unità di ricerca fatturabili. A questo livello, le partizioni sono da 25 GB. |
| **S2** |Lo Standard 2 esegue carichi di lavoro di produzione più elevati usando le stesse 36 unità di ricerca del piano S1 ma con partizioni e repliche di dimensioni maggiori. A questo livello, le partizioni sono da 100 GB. |
| **S3** |Lo Standard 3 esegue carichi di lavoro di produzione in proporzione più elevati su sistemi di fascia superiore, in configurazioni fino a 12 partizioni o 12 repliche in meno di 36 unità di ricerca. A questo livello, le partizioni sono da 200 GB. |
| **S3 HD** |Lo Standard 3 ad alta densità è progettato per un numero elevato di indici più piccoli. È possibile avere fino a 3 partizioni, ciascuna di 200 GB.|

> [!NOTE]
> I valori massimi per le repliche e le partizioni vengono fatturati come unità di servizio (al massimo 36 unità per servizio). Ciò impone un limite effettivo inferiore rispetto al valore massimo nominale. Per usare ad esempio il numero massimo di 12 repliche si possono avere non oltre 3 partizioni perché 12 * 3 = 36 unità. Analogamente, ridurre le repliche a 3 per usare il numero di partizioni massimo. Per un grafico delle combinazioni consentite, vedere [Ridimensionare i livelli di risorse per le query e l'indicizzazione dei carichi di lavoro in Ricerca di Azure](search-capacity-planning.md).
>
>

## <a name="review-limits-per-tier"></a>Esaminare i limiti per ogni piano
Il grafico seguente è un subset dei limiti tratto da [Limiti dei servizi in Ricerca di Azure](search-limits-quotas-capacity.md). Elenca i fattori che probabilmente influiranno maggiormente sulla decisione relativa allo SKU. È possibile fare riferimento a questo grafico quando si esaminano le domande seguenti.

| Risorsa | Free | Basic | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Contratto di servizio (SLA) |No <sup>1</sup> |Sì |Sì |Sì |Sì |Sì |
| Limiti per gli indici |3 |5 |50 |200 |200 |1000 <sup>2</sup> |
| Limiti per i documenti |10.000 in totale |1 milione per servizio |15 milioni per partizione |60 milioni per partizione |120 milioni per partizione |1 milione per indice |
| Partizioni massime |N/D |1 |12 |12 |12 |3 <sup>2</sup> |
| Dimensioni della partizione |50 MB totali |2 GB per servizio |25 GB per partizione |100 GB per partizione (con un massimo di 1,2 TB per ogni servizio) |200 GB per partizione, con un massimo di 2,4 TB per ogni servizio |200 GB (con un massimo di 600 GB per ogni servizio) |
| Repliche massime |N/D |3 |12 |12 |12 |12 |

<sup>1</sup> Con il Contratto di servizio non sono incluse funzionalità di anteprima e il livello gratuito. Per tutti i livelli fatturabili, i contratti di servizio diventano effettivi quando viene effettuato il provisioning di una ridondanza sufficiente per il servizio. Per il Contratto di servizio di query (lettura) sono necessarie due o più repliche. Per il contratto di servizio di query e indicizzazione (lettura-scrittura) sono necessarie tre o più repliche. Il numero di partizioni non è un fattore di cui tiene conto il Contratto di servizio. 

<sup>2</sup> S3 e S3 HD sono supportati da un'infrastruttura identica ad alta capacità, tuttavia ciascuno raggiunge il limite massimo in modi diversi. S3 è destinato a un numero minore di indici di dimensioni molto grandi. Di conseguenza, il limite massimo è associato alle risorse (2,4 TB per ogni servizio). S3 HD è destinato a un numero elevato di indici di dimensioni molto ridotte. A 1000 indici, S3 HD raggiunge il limite in termini di vincoli di indice. Se l'utente è un cliente S3 HD che richiede più di 1000 indici, può contattare il supporto tecnico Microsoft per informazioni su come procedere.

## <a name="eliminate-skus-that-dont-meet-requirements"></a>Eliminare gli SKU che non soddisfano i requisiti
Le domande seguenti consentono di prendere la decisione corretta per gli SKU per il carico di lavoro.

1. Si hanno requisiti in termini di **contratti di servizio** ? È possibile usare qualsiasi livello fatturabile (di base o superiore), ma è necessario configurare il servizio per la ridondanza. Per il Contratto di servizio di query (lettura) sono necessarie due o più repliche. Per il contratto di servizio di query e indicizzazione (lettura-scrittura) sono necessarie tre o più repliche. Il numero di partizioni non è un fattore di cui tiene conto il Contratto di servizio.
2. **Quanti indici** sono necessari? Una delle variabili principali che influiscono sulla decisione per gli SKU è il numero di indici supportato da ogni SKU. Il supporto degli indici è notevolmente diverso nei piani tariffari più bassi. Il requisito relativo al numero di indici può essere determinante nella scelta dello SKU.
3. **Quanti documenti** verranno caricati in ogni indice? Il numero e le dimensioni dei documenti determinano le dimensioni finali dell'indice. Supponendo che si possano stimare le dimensioni previste dell'indice, è possibile confrontare tale numero con le dimensioni della partizione per ogni SKU, estese per il numero di partizioni necessarie per archiviare un indice di tali dimensioni.
4. **Qual è il carico di query previsto**? Dopo aver determinato i requisiti di archiviazione, prendere in considerazione i carichi di lavoro di query. S2 ed entrambi gli SKU S3 offrono una velocità effettiva quasi equivalente, ma i requisiti in termini di contratto di servizio escludono gli SKU di anteprima.
5. Se si stanno prendendo in considerazione i piani tariffari S2 o S3, stabilire se servono [indicizzatori](search-indexer-overview.md). Gli indicizzatori non sono ancora disponibili per il piano S3 HD. Un approccio alternativo consiste nell'usare un modello push per gli aggiornamenti dell'indice, in cui viene scritto il codice dell'applicazione per effettuare il push di un set di dati a un indice.

La maggior parte dei clienti può prendere in considerazione o escludere uno SKU specifico rispondendo alle domande sopra. Se non si sa quale SKU scegliere, è possibile pubblicare domande nei forum di MSDN o StackOverflow oppure contattare il supporto tecnico di Azure per altre indicazioni.

## <a name="decision-validation-does-the-sku-offer-sufficient-storage-and-qps"></a>Convalida della decisione: lo SKU offre una risorsa di archiviazione e un valore di query al secondo sufficienti?
Come ultimo passaggio, visitare di nuovo la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/search/) e le [sezioni per servizio e per indice nei limiti di servizio](search-limits-quotas-capacity.md) per verificare le stime rispetto ai limiti di sottoscrizione e di servizio.

Se il prezzo o i requisiti di archiviazione non rientrano nelle stime può essere opportuno effettuare il refactoring dei carichi di lavoro tra una serie di servizi più piccoli, ad esempio. A livello più granulare è possibile progettare nuovamente gli indici perché siano più piccoli oppure usare filtri per rendere le query più efficienti.

> [!NOTE]
> I requisiti di archiviazione possono apparire maggiori del necessario se i documenti contengono dati estranei. I documenti contengono idealmente solo da dati o metadati ricercabili. I dati binari non sono ricercabili e devono essere archiviati separatamente, ad esempio in una tabella o un archivio BLOB di Azure, con un campo nell'indice che contenga un riferimento URL ai dati esterni. Le dimensioni massime di un singolo documento sono pari a 16 MB o meno, se si caricano più documenti in una richiesta. Per altre informazioni, vedere [Limiti dei servizi in Ricerca di Azure](search-limits-quotas-capacity.md) .
>
>

## <a name="next-step"></a>Passaggio successivo
Dopo aver determinato lo SKU corretto, continuare con la procedura:

* [Creare un servizio di ricerca nel portale](search-create-service-portal.md)
* [Modificare l'allocazione di partizioni e repliche per la scalabilità del servizio](search-capacity-planning.md)
