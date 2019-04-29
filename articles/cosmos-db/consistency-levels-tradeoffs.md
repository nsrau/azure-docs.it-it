---
title: Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza in Azure Cosmos DB
description: Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza in Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: a76e277bf56861bcaefb5bf7f8b3b3bc03ad1164
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60894029"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Compromessi tra coerenza, disponibilità e prestazioni 

I database distribuiti che si basano sulla replica per la disponibilità elevata, la bassa latenza o entrambe devono attuare dei compromessi. Tali compromessi sono tra la coerenza di lettura e la disponibilità, latenza e velocità effettiva.

Azure Cosmos DB affronta la coerenza dei dati offrendo uno spettro di scelte. Questo approccio include più opzioni rispetto ai due estremi della coerenza assoluta ed eventuale. È possibile scegliere tra cinque modelli ben definiti nella gamma della coerenza. Dal più forte al più debole, i modelli sono:

- *Assoluto*
- *Obsolescenza associata*
- *Sessione*
- *Coerenza del prefisso*
- *Finale*

Ogni modello offre compromessi tra prestazioni e disponibilità e supportata da contratti di servizio completi.

## <a name="consistency-levels-and-latency"></a>Livelli di coerenza e latenza

La latenza di lettura per tutti i livelli di coerenza è sempre minore ai 10 millisecondi al 99° percentile. Questa latenza di lettura è supportata dal contratto di servizio. La latenza di lettura media, al 50° percentile, è in genere uguale o inferiore ai 2 millisecondi. Gli account di Azure Cosmos che si estendono su più aree e sono configurati con coerenza assoluta costituiscono un'eccezione a questa garanzia.

La latenza di scrittura per tutti i livelli di coerenza è sempre essere minore di 10 millisecondi al 99 ° percentile. Questa latenza di scrittura è supportata dal contratto di servizio. La latenza di scrittura media, al 50° percentile, è in genere uguale o inferiore ai 5 millisecondi.

Per gli account Azure Cosmos configurati con coerenza assoluta, con più di un'area, la latenza di scrittura è necessariamente minore di due volte il round trip time (RTT) tra una delle due aree di sovrapposizione, oltre a 10 millisecondi al 99 ° percentile.

La latenza RTT esatta è una funzione della velocità della luce e la topologia di rete di Azure. La rete di Azure non fornisce nessun contratto di servizio di latenza per il tempo RTT tra due aree di Azure. Per l'account Azure Cosmos, le latenze di replica vengono visualizzate nel portale di Azure. È possibile usare il portale di Azure (passare al pannello delle metriche) per monitorare le latenze di replica tra varie aree associate all'account Azure Cosmos.

## <a name="consistency-levels-and-throughput"></a>Livelli di coerenza e velocità effettiva

- Per la stessa quantità di unità di richieste, i livelli di coerenza sessione, prefisso coerente e finale forniscono una velocità effettiva di lettura quasi due volte maggiore in confronto ai livelli assoluto e con decadimento ristretto.

- Per un determinato tipo di operazione di scrittura (come inserimento, sostituzione, upsert ed eliminazione), la velocità effettiva di scrittura per le unità di richieste è identica per tutti i livelli di coerenza.

## <a id="rto"></a>Livelli di coerenza e durabilità dei dati

All'interno di un ambiente di database distribuito a livello globale sussiste una relazione diretta tra il livello di coerenza e la durabilità dei dati in presenza di un'interruzione a livello di area. Quando si sviluppa il piano di continuità aziendale, è necessario conoscere il tempo massimo accettabile prima che l'applicazione venga ripristinata completamente dopo un evento di arresto improvviso. Il tempo necessario per un'applicazione per un ripristino completo è detta **obiettivo tempo di ripristino** (**RTO**). È anche necessario conoscere la perdita massima di aggiornamenti di dati recenti che l'applicazione è in grado di tollerare durante il ripristino dopo un evento di arresto improvviso. Il periodo di tempo degli aggiornamenti che si potrebbero permettersi di perdere è detta **obiettivo del punto di ripristino** (**RPO**).

Nella tabella seguente definisce la relazione tra coerenza del modello e durabilità dei dati in presenza di un'interruzione a livello di area. È importante notare che in un sistema distribuito, anche con coerenza assoluta, non è possibile disporre di un database distribuito con un RPO e RTO pari a zero a causa di the CAP Theorem. Per altre informazioni sui motivi per cui, vedere [livelli di coerenza in Azure Cosmos DB](consistency-levels.md).

|**Area/e**|**Modalità di replica**|**Livello di coerenza**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Master singolo o multimaster|Qualsiasi livello di coerenza|< 240 minuti|<1 settimana|
|>1|Master singolo|Sessione, Prefisso coerente, Finale|< 15 minuti|< 15 minuti|
|>1|Master singolo|Decadimento ristretto|*K* & *T*|< 15 minuti|
|>1|Multimaster|Sessione, Prefisso coerente, Finale|< 15 minuti|0|
|>1|Multimaster|Decadimento ristretto|*K* & *T*|0|
|>1|Master singolo o multimaster|Assoluta|0|< 15 minuti|

*K* = numero di *"K"* versioni, ad esempio, gli aggiornamenti, di un elemento.

*T* = l'intervallo di tempo *"T"* dopo l'ultimo aggiornamento.

## <a name="next-steps"></a>Passaggi successivi

Sono disponibili altre informazioni relative ai compromessi tra coerenza generale e distribuzione globale nei sistemi distribuiti. Vedere gli articoli seguenti:

- [Compromessi sulla coerenza nella progettazione di sistemi di database distribuiti moderni](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Disponibilità elevata](high-availability.md)
- [Contratto di servizio Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
