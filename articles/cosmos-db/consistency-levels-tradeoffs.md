---
title: Azure Cosmos DB coerenza, disponibilità e compromessi delle prestazioni
description: Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza in Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/23/2020
ms.reviewer: sngun
ms.openlocfilehash: 4de696e2538bf1fa4823aafe30f931b7852535a7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82191737"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Compromessi tra coerenza, disponibilità e prestazioni

I database distribuiti che si basano sulla replica per la disponibilità elevata, la bassa latenza o entrambe devono attuare dei compromessi. Tali compromessi sono tra la coerenza di lettura e la disponibilità, latenza e velocità effettiva.

Azure Cosmos DB affronta la coerenza dei dati offrendo uno spettro di scelte. Questo approccio include più opzioni rispetto ai due estremi della coerenza assoluta ed eventuale. È possibile scegliere tra cinque livelli ben definiti nello spettro di coerenza. Dal più forte al più debole, i livelli sono:

- *Forte*
- *Obsolescenza associata*
- *Sessione*
- *Prefisso coerente*
- *Eventuali*

Ogni livello garantisce compromessi a livello di disponibilità e prestazioni ed è supportato da contratti di contratto completi.

## <a name="consistency-levels-and-latency"></a>Livelli di coerenza e latenza

La latenza di lettura per tutti i livelli di coerenza è sempre minore ai 10 millisecondi al 99° percentile. Questa latenza di lettura è supportata dal contratto di servizio. La latenza di lettura media, al cinquantesimo percentile, è in genere di 4 millisecondi o meno.

La latenza di scrittura per tutti i livelli di coerenza è sempre inferiore a 10 millisecondi al 99 ° percentile. Questa latenza di scrittura è supportata dal contratto di servizio. La latenza di scrittura media, al 50° percentile, è in genere uguale o inferiore ai 5 millisecondi. Gli account di Azure Cosmos che si estendono su più aree e sono configurati con coerenza assoluta costituiscono un'eccezione a questa garanzia.

### <a name="write-latency-and-strong-consistency"></a>Latenza di scrittura e coerenza assoluta

Per gli account Azure Cosmos configurati con coerenza assoluta con più di un'area, la latenza di scrittura è pari a due volte il tempo di round trip (RTT) tra una delle due aree più lontane, più 10 millisecondi al 99 ° percentile. Un RTT di rete elevato tra le aree verrà convertito in una latenza più elevata per le richieste di Cosmos DB perché la coerenza assoluta completa un'operazione solo dopo aver verificato che sia stato eseguito il commit in tutte le aree all'interno di un account.

La latenza RTT esatta è una funzione della velocità della luce e la topologia di rete di Azure. La rete di Azure non fornisce nessun contratto di servizio di latenza per il tempo RTT tra due aree di Azure. Per l'account Azure Cosmos, le latenze di replica vengono visualizzate nel portale di Azure. Per monitorare le latenze di replica tra le varie aree associate all'account Azure Cosmos, è possibile usare il portale di Azure (passare al pannello metriche, selezionare scheda coerenza).

> [!IMPORTANT]
> Per impostazione predefinita, la coerenza assoluta per gli account con aree che si estendono su più di 5000 miglia (8000 km) è bloccata a causa della latenza di scrittura elevata. Per abilitare questa funzionalità, contattare il supporto tecnico.

## <a name="consistency-levels-and-throughput"></a>Livelli di coerenza e velocità effettiva

- Per un decadimento forte e con decadimento ristretto, le letture vengono eseguite su due repliche in un set di quattro repliche (quorum minoritario) per fornire garanzie di coerenza. Sessione, prefisso coerente e eventuale lettura di una singola replica. Il risultato è che, per lo stesso numero di unità di richiesta, la velocità effettiva di lettura per l'obsolescenza forte e ristretta è la metà degli altri livelli di coerenza.

- Per un determinato tipo di operazione di scrittura (come inserimento, sostituzione, upsert ed eliminazione), la velocità effettiva di scrittura per le unità di richieste è identica per tutti i livelli di coerenza.

|**Livello di coerenza**|**Letture quorum**|**Scritture quorum**|
|--|--|--|
|**Forte**|Minoranza locale|Maggioranza globale|
|**Decadimento ristretto**|Minoranza locale|Maggioranza locale|
|**Sessione**|Replica singola (usando il token di sessione)|Maggioranza locale|
|**Coerenza del prefisso**|Replica singola|Maggioranza locale|
|**Eventuali**|Replica singola|Maggioranza locale|

## <a name="consistency-levels-and-data-durability"></a><a id="rto"></a>Livelli di coerenza e durabilità dei dati

All'interno di un ambiente di database distribuito a livello globale sussiste una relazione diretta tra il livello di coerenza e la durabilità dei dati in presenza di un'interruzione a livello di area. Quando si sviluppa il piano di continuità aziendale, è necessario conoscere il tempo massimo accettabile prima che l'applicazione venga ripristinata completamente dopo un evento di arresto improvviso. Il tempo necessario per il ripristino completo di un'applicazione è noto come **obiettivo del tempo di ripristino** (**RTO**). È anche necessario conoscere la perdita massima di aggiornamenti di dati recenti che l'applicazione è in grado di tollerare durante il ripristino dopo un evento di arresto improvviso. Il periodo di tempo degli aggiornamenti che è possibile perdere è noto come **obiettivo del punto di ripristino** (**RPO**).

La tabella seguente definisce la relazione tra il modello di coerenza e la durabilità dei dati in presenza di un'interruzione a livello di area. È importante notare che in un sistema distribuito, anche con coerenza assoluta, non è possibile avere un database distribuito con un RPO e RTO zero a causa del teorema CAP. Per altre informazioni sui motivi, vedere [livelli di coerenza in Azure Cosmos DB](consistency-levels.md).

|**Aree geografiche**|**Modalità di replica**|**Livello di coerenza**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Master singolo o multimaster|Qualsiasi livello di coerenza|< 240 minuti|<1 settimana|
|>1|Master singolo|Sessione, Prefisso coerente, Finale|< 15 minuti|< 15 minuti|
|>1|Master singolo|Decadimento ristretto|*K*  &  *T*|< 15 minuti|
|>1|Master singolo|Assoluta|0|< 15 minuti|
|>1|Multimaster|Sessione, Prefisso coerente, Finale|< 15 minuti|0|
|>1|Multimaster|Decadimento ristretto|*K*  &  *T*|0|

*K* = numero di versioni *"k"* , ovvero aggiornamenti, di un elemento.

*T* = intervallo di tempo *"t"* dall'ultimo aggiornamento.

## <a name="strong-consistency-and-multi-master"></a>Coerenza assoluta e multimaster

Gli account Cosmos configurati per il multimaster non possono essere configurati per la coerenza assoluta, perché non è possibile che un sistema distribuito fornisca un RPO di zero e un RTO pari a zero. Inoltre, non ci sono vantaggi della latenza di scrittura per l'uso della coerenza assoluta con il multimaster perché qualsiasi scrittura in qualsiasi area deve essere replicata e impegnata in tutte le aree configurate all'interno dell'account. In questo modo si ottiene la stessa latenza di scrittura di un singolo account master.

## <a name="next-steps"></a>Passaggi successivi

Sono disponibili altre informazioni relative ai compromessi tra coerenza generale e distribuzione globale nei sistemi distribuiti. Vedere gli articoli seguenti:

- [Compromessi sulla coerenza nella progettazione di sistemi di database distribuiti moderni](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Disponibilità elevata](high-availability.md)
- [Contratto di servizio Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
