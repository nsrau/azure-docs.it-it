---
title: Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza in Azure Cosmos DB
description: Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza in Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 2/13/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: cf3dc71e96dac96a6406c97a433398b31a370869
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57571168"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Compromessi tra coerenza, disponibilità e prestazioni 

I database distribuiti che si basano sulla replica per la disponibilità elevata, la bassa latenza o entrambe devono attuare dei compromessi. Tali compromessi sono tra la coerenza di lettura e la disponibilità, latenza e velocità effettiva.

Azure Cosmos DB affronta la coerenza dei dati offrendo uno spettro di scelte. Questo approccio include più opzioni rispetto ai due estremi della coerenza assoluta ed eventuale. È possibile scegliere tra cinque modelli ben definiti nella gamma della coerenza. Dal più forte al più debole, i modelli sono:

- Assoluta
- Obsolescenza associata
- sessione
- Prefisso coerente
- Finale

Ogni modello prevede compromessi di disponibilità e prestazioni ed è supportato da un contratto di servizio completo.

## <a name="consistency-levels-and-latency"></a>Livelli di coerenza e latenza

La latenza di lettura per tutti i livelli di coerenza è sempre minore ai 10 millisecondi al 99° percentile. Questa latenza di lettura è supportata dal contratto di servizio. La latenza di lettura media, al 50° percentile, è in genere uguale o inferiore ai 2 millisecondi. Gli account di Azure Cosmos che si estendono su più aree e sono configurati con coerenza assoluta costituiscono un'eccezione a questa garanzia.

La latenza di scrittura per tutti i livelli di coerenza è sempre essere minore di 10 millisecondi al 99 ° percentile. Questa latenza di scrittura è supportata dal contratto di servizio. La latenza di scrittura media, al 50° percentile, è in genere uguale o inferiore ai 5 millisecondi.

Per gli account Azure Cosmos configurati con coerenza assoluta, con più di un'area, la latenza di scrittura è necessariamente minore di due volte il round trip time (RTT) tra una delle due aree di sovrapposizione, oltre a 10 millisecondi al 99 ° percentile. Questa opzione è attualmente in fase di anteprima.

La latenza RTT esatta è una funzione della velocità della luce e la topologia di rete di Azure. La rete di Azure non fornisce nessun contratto di servizio di latenza per il tempo RTT tra due aree di Azure. Per l'account Azure Cosmos, le latenze di replica vengono visualizzate nel portale di Azure. È possibile usare il portale di Azure per monitorare le latenze di replica tra varie aree associate all'account.

## <a name="consistency-levels-and-throughput"></a>Livelli di coerenza e velocità effettiva

- Per la stessa quantità di unità di richieste, i livelli di coerenza sessione, prefisso coerente e finale forniscono una velocità effettiva di lettura quasi due volte maggiore in confronto ai livelli assoluto e con decadimento ristretto.

- Per un determinato tipo di operazione di scrittura (come inserimento, sostituzione, upsert ed eliminazione), la velocità effettiva di scrittura per le unità di richieste è identica per tutti i livelli di coerenza.

## <a id="rto"></a>Livelli di coerenza e durabilità dei dati

All'interno di un ambiente di database distribuito a livello globale sussiste una relazione diretta tra il livello di coerenza e la durabilità dei dati in presenza di un'interruzione a livello di area. Quando si sviluppa il piano di continuità aziendale, è necessario conoscere il tempo massimo accettabile prima che l'applicazione venga ripristinata completamente dopo un evento di arresto improvviso. Il tempo necessario per il ripristino completo di un'applicazione è noto come obiettivo del tempo di ripristino (RTO). È anche necessario conoscere la perdita massima di aggiornamenti di dati recenti che l'applicazione è in grado di tollerare durante il ripristino dopo un evento di arresto improvviso. Il periodo di tempo degli aggiornamenti che è possibile perdere è noto come obiettivo del punto di ripristino (RPO).

La tabella definisce la relazione tra coerenza del modello e durabilità dei dati in presenza di un'interruzione a livello di area. È importante notare che in un sistema distribuito, anche con coerenza assoluta, non è possibile avere un database distribuito con RPO e RTO pari a zero per il teorema CAP. Per altre informazioni sul motivo di questa affermazione, vedere  [Livelli di coerenza in Azure Cosmos DB](consistency-levels.md).

|**Area/e**|**Modalità di replica**|**Livello di coerenza**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Master singolo o multimaster|Qualsiasi livello di coerenza|< 240 minuti|<1 settimana|
|>1|Master singolo|Sessione, Prefisso coerente, Finale|< 15 minuti|< 15 minuti|
|>1|Master singolo|Decadimento ristretto|K e T|< 15 minuti|
|>1|Multimaster|Sessione, Prefisso coerente, Finale|< 15 minuti|0|
|>1|Multimaster|Decadimento ristretto|K e T|0|
|>1|Master singolo o multimaster|Assoluta|0|< 15 minuti|

K = numero di versioni "K" (aggiornamenti) di un elemento.
T = intervallo di tempo "T" dall'ultimo aggiornamento.

## <a name="next-steps"></a>Passaggi successivi

Sono disponibili altre informazioni relative ai compromessi tra coerenza generale e distribuzione globale nei sistemi distribuiti. Vedere gli articoli seguenti:

- [Compromessi sulla coerenza nella progettazione di sistemi di database distribuiti moderni](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Disponibilità elevata](high-availability.md)
- [Contratto di servizio Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
