---
title: Coerenza, disponibilità e comprosta di azure Cosmos DB
description: Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza in Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.reviewer: sngun
ms.openlocfilehash: 7cdaa9699b15000359c438bcc410e300415b759a
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81379953"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Compromessi tra coerenza, disponibilità e prestazioni

I database distribuiti che si basano sulla replica per la disponibilità elevata, la bassa latenza o entrambe devono attuare dei compromessi. Tali compromessi sono tra la coerenza di lettura e la disponibilità, latenza e velocità effettiva.

Azure Cosmos DB affronta la coerenza dei dati offrendo uno spettro di scelte. Questo approccio include più opzioni rispetto ai due estremi della coerenza assoluta ed eventuale. È possibile scegliere tra cinque livelli ben definiti nello spettro di coerenza. Dal più forte al più debole, i livelli sono:

- *Forte*
- *Stantio delimitato*
- *sessione*
- *Prefisso coerente*
- *Eventuale*

Ogni livello offre compromessi di disponibilità e prestazioni ed è supportato da contratti di servizio completi.

## <a name="consistency-levels-and-latency"></a>Livelli di coerenza e latenza

La latenza di lettura per tutti i livelli di coerenza è sempre minore ai 10 millisecondi al 99° percentile. Questa latenza di lettura è supportata dal contratto di servizio. La latenza media di lettura, al 50esimo percentile, è in genere di 4 millisecondi o meno.

La latenza di scrittura per tutti i livelli di coerenza è sempre garantita per essere inferiore a 10 millisecondi al 99esimo percentile. Questa latenza di scrittura è supportata dal contratto di servizio. La latenza di scrittura media, al 50° percentile, è in genere uguale o inferiore ai 5 millisecondi. Gli account di Azure Cosmos che si estendono su più aree e sono configurati con coerenza assoluta costituiscono un'eccezione a questa garanzia.

### <a name="write-latency-and-strong-consistency"></a>Latenza di scrittura e coerenza forte

Per gli account Cosmos di Azure configurati con una forte coerenza con più di un'area, la latenza di scrittura è pari a due volte il tempo di andata e ritorno (RTT) tra una delle due aree più lontane, più 10 millisecondi al 99esimo percentile. Un'elevata rtT di rete tra le aree si tradurrà in una latenza più elevata per le richieste Cosmos DB poiché una forte coerenza completa un'operazione solo dopo aver verificato che sia stata impegnata in tutte le aree all'interno di un account.

La latenza RTT esatta è una funzione della velocità della luce e la topologia di rete di Azure. La rete di Azure non fornisce nessun contratto di servizio di latenza per il tempo RTT tra due aree di Azure. Per l'account Azure Cosmos, le latenze di replica vengono visualizzate nel portale di Azure. È possibile usare il portale di Azure (passare al pannello Metriche, selezionare la scheda Coerenza) per monitorare le latenze di replica tra le varie aree associate all'account Cosmos di Azure.You can use the Azure portal (go to the Metrics blade, select Consistency tab) to monitor the replication latencies between various regions that are associated with your Azure Cosmos account.

> [!IMPORTANT]
> La coerenza elevata per gli account con aree che si estendono per più di 8000 chilometri è bloccata per impostazione predefinita a causa dell'elevata latenza di scrittura. Per abilitare questa funzionalità, contattare il supporto tecnico.

## <a name="consistency-levels-and-throughput"></a>Livelli di coerenza e velocità effettiva

- Per una situazione di verifica avanzata e limitata, le letture vengono eseguite su due repliche in un set di quattro repliche (quorum di minoranza) per garantire la coerenza. Sessione, prefisso coerente ed eventuale eseguire letture di repliche singole. Il risultato è che, per lo stesso numero di unità di richiesta, la velocità effettiva di lettura per una stantia forte e limitata è la metà degli altri livelli di coerenza.

- Per un determinato tipo di operazione di scrittura (come inserimento, sostituzione, upsert ed eliminazione), la velocità effettiva di scrittura per le unità di richieste è identica per tutti i livelli di coerenza.

|**Livello di coerenza**|**Letture quorum**|**Scritture quorum**|
|--|--|--|
|**Forte**|Minoranza locale|Maggioranza globale|
|**Decadimento ristretto**|Minoranza locale|Maggioranza locale|
|**sessione**|Replica singola (tramite token di sessione)|Maggioranza locale|
|**Coerenza del prefisso**|Replica singola|Maggioranza locale|
|**Eventuale**|Replica singola|Maggioranza locale|

## <a name="consistency-levels-and-data-durability"></a><a id="rto"></a>Livelli di coerenza e durabilità dei dati

All'interno di un ambiente di database distribuito a livello globale sussiste una relazione diretta tra il livello di coerenza e la durabilità dei dati in presenza di un'interruzione a livello di area. Quando si sviluppa il piano di continuità aziendale, è necessario conoscere il tempo massimo accettabile prima che l'applicazione venga ripristinata completamente dopo un evento di arresto improvviso. Il tempo necessario per il ripristino completo di un'applicazione è noto come **obiettivo** del tempo di ripristino (**RTO**). È anche necessario conoscere la perdita massima di aggiornamenti di dati recenti che l'applicazione è in grado di tollerare durante il ripristino dopo un evento di arresto improvviso. Il periodo di tempo degli aggiornamenti che si potrebbe permettere di perdere è noto come **obiettivo del punto** di ripristino (**RPO**).

La tabella seguente definisce la relazione tra il modello di coerenza e la durata dei dati in presenza di un'interruzione a livello di area. È importante notare che in un sistema distribuito, anche con una forte coerenza, è impossibile avere un database distribuito con un RPO e un RTO pari a zero a causa del teorema CAP. Per altre informazioni sui motivi, vedere Livelli di [coerenza in Azure Cosmos DB](consistency-levels.md).

|**Regione/e**|**Modalità di replica**|**Livello di coerenza**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Master singolo o multimaster|Qualsiasi livello di coerenza|< 240 minuti|<1 settimana|
|>1|Master singolo|Sessione, Prefisso coerente, Finale|< 15 minuti|< 15 minuti|
|>1|Master singolo|Decadimento ristretto|*K* & *T*|< 15 minuti|
|>1|Master singolo|Assoluta|0|< 15 minuti|
|>1|Multimaster|Sessione, Prefisso coerente, Finale|< 15 minuti|0|
|>1|Multimaster|Decadimento ristretto|*K* & *T*|0|

*K* - Il numero di versioni *"K"* (cioè aggiornamenti) di un elemento.

*T* - L'intervallo di tempo *"T"* dall'ultimo aggiornamento.

## <a name="strong-consistency-and-multi-master"></a>Forte coerenza e multi-master

Gli account Cosmo configurati per più master non possono essere configurati per una coerenza forte in quanto non è possibile per un sistema distribuito fornire un RPO pari a zero e un RTO pari a zero. Inoltre, non esistono vantaggi di latenza di scrittura per l'utilizzo di coerenza forte con più master in quanto qualsiasi scrittura in qualsiasi area deve essere replicata e impegnata in tutte le aree configurate all'interno dell'account. Ciò comporta la stessa latenza di scrittura di un singolo account master.

## <a name="next-steps"></a>Passaggi successivi

Sono disponibili altre informazioni relative ai compromessi tra coerenza generale e distribuzione globale nei sistemi distribuiti. Vedere gli articoli seguenti:

- [Compromessi di coerenza nella progettazione di moderni sistemi di database distribuiti](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Disponibilità elevata](high-availability.md)
- [Contratto di servizio Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
