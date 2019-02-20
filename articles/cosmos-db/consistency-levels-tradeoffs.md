---
title: Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza in Azure Cosmos DB
description: Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza in Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ee0dc1bec39bf95cbf4f3bf7ecea92b877a78b88
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56113754"
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

- La latenza di lettura per tutti i livelli di coerenza è sempre minore ai 10 millisecondi al 99° percentile. Questa latenza di lettura è supportata dal contratto di servizio. La latenza di lettura media, al 50° percentile, è in genere uguale o inferiore ai 2 millisecondi. Gli account di Azure Cosmos che si estendono su più aree e sono configurati con coerenza assoluta costituiscono un'eccezione a questa garanzia.

- La latenza di scrittura per i livelli di coerenza rimanenti è sempre minore ai 10 millisecondi al 99° percentile. Questa latenza di scrittura è supportata dal contratto di servizio. La latenza di scrittura media, al 50° percentile, è in genere uguale o inferiore ai 5 millisecondi.

Alcuni account di Azure Cosmos potrebbero avere molte aree configurate con coerenza assoluta. In questo caso, viene garantito che la latenza di scrittura sia inferiore alla metà del tempo di round trip (RTT) più 10 millisecondi al 99° percentile. Il tempo RTT tra due delle regioni più distanti tra loro è associato all'account Azure Cosmos. È pari aI tempo RTT tra due delle regioni più distanti tra loro associate all'account Azure Cosmos. Questa opzione è attualmente in fase di anteprima.

La latenza RTT esatta è una funzione della velocità della luce e la topologia di rete di Azure. La rete di Azure non fornisce nessun contratto di servizio di latenza per il tempo RTT tra due aree di Azure. Per l'account Azure Cosmos, le latenze di replica vengono visualizzate nel portale di Azure. È possibile usare il portale di Azure per monitorare le latenze di replica tra varie aree associate all'account.

## <a name="consistency-levels-and-throughput"></a>Livelli di coerenza e velocità effettiva

- Per la stessa quantità di unità di richieste, i livelli di coerenza sessione, prefisso coerente e finale forniscono una velocità effettiva di lettura quasi due volte maggiore in confronto ai livelli assoluto e con decadimento ristretto.

- Per un determinato tipo di operazione di scrittura (come inserimento, sostituzione, upsert ed eliminazione), la velocità effettiva di scrittura per le unità di richieste è identica per tutti i livelli di coerenza.

## <a name="consistency-levels-and-data-durability"></a>Livelli di coerenza e durabilità dei dati

All'interno di un ambiente di database distribuito a livello globale sussiste una relazione diretta tra il livello di coerenza e la durabilità dei dati in presenza di un'interruzione a livello di area. La tabella definisce la relazione tra il modello di coerenza e la durabilità dei dati in presenza di un'interruzione a livello di area. È importante notare che in un sistema distribuito, anche con coerenza assoluta, non è possibile disporre di un database distribuito con RPO e RTO pari a zero per il teorema CAP. Per altre informazioni sul motivo di questa affermazione, vedere  [Livelli di coerenza in Azure Cosmos DB](consistency-levels.md).

|**Area/e**|**Modalità di replica**|**Livello di coerenza**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Master singolo o multimaster|Qualsiasi livello di coerenza|< 240 minuti|<1 settimana|
|>1|Master singolo|Sessione, Prefisso coerente, Finale|< 15 minuti|< 15 minuti|
|>1|Master singolo|Decadimento ristretto|K e T*|< 15 minuti|
|>1|Multimaster|Sessione, Prefisso coerente, Finale|< 15 minuti|0|
|>1|Multimaster|Decadimento ristretto|K e T*|0|
|>1|Master singolo o multimaster|Assoluta|0|< 15 minuti|

* K e T = numero di versioni "K" (aggiornamenti) di un elemento. O intervallo di tempo "T".



## <a name="next-steps"></a>Passaggi successivi

Sono disponibili altre informazioni relative ai compromessi tra coerenza generale e distribuzione globale nei sistemi distribuiti. Vedere gli articoli seguenti:

- [Compromessi sulla coerenza nella progettazione di sistemi di database distribuiti moderni](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
- [Disponibilità elevata](high-availability.md)
- [Contratto di servizio Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
