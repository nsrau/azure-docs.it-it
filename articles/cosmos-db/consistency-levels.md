---
title: Livelli di coerenza in Azure Cosmos DB
description: Cosmos DB ha cinque livelli di coerenza per consentire di compensare scompensi di coerenza, disponibilità e latenza finale.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.openlocfilehash: 742ff2e6cff4569b5b7eeb131cd4394277b6c3cd
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100457"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Livelli di coerenza in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

I database distribuiti che si basano sulla replica per la disponibilità elevata, la bassa latenza o entrambi, devono avere un compromesso fondamentale tra la coerenza di lettura, la disponibilità, la latenza e la velocità effettiva in base a quanto definito dal [teorema PACLC](https://en.wikipedia.org/wiki/PACELC_theorem). Il della linearità del modello di coerenza forte è lo standard Gold di programmabilità dei dati. Ma aggiunge un prezzo ripido dalle latenze di scrittura più elevate a causa della necessità di replicare ed eseguire il commit di una grande quantità di dati. La coerenza assoluta può anche risentirne la disponibilità ridotta (durante gli errori), perché i dati non possono essere replicati ed eseguito il commit in ogni area La coerenza finale offre una maggiore disponibilità e prestazioni migliori, ma è più difficile programmare le applicazioni perché i dati potrebbero non essere completamente coerenti in tutte le aree.

La maggior parte dei database NoSQL distribuiti disponibili in commercio attualmente disponibili nel mercato offre solo la coerenza assoluta e finale. Azure Cosmos DB offre cinque livelli ben definiti. Dal più forte al più debole, i livelli sono:

- *Assoluta*
- *Obsolescenza associata*
- *Sessione*
- *Prefisso coerente*
- *Eventuali*

Ogni livello offre compromessi in merito a disponibilità e prestazioni. Nell'immagine seguente vengono illustrati i diversi livelli di coerenza come uno spettro.

:::image type="content" source="./media/consistency-levels/five-consistency-levels.png" alt-text="La coerenza come spettro" border="false" :::

I livelli di coerenza sono indipendenti dall'area e sono garantiti per tutte le operazioni, indipendentemente dall'area da cui vengono gestite le operazioni di lettura e scrittura, dal numero di aree associate all'account Azure Cosmos o dal fatto che l'account sia configurato con una o più aree di scrittura.

## <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Livelli di coerenza e API di Azure Cosmos DB

Azure Cosmos DB fornisce il supporto nativo per le API compatibili con il protocollo wire per i database più diffusi. Sono inclusi MongoDB, Apache Cassandra, Gremlin e archiviazione tabelle di Azure. Quando si usa l'API Gremlin e API Tabella, viene usato il livello di coerenza predefinito configurato nell'account Azure Cosmos. Per informazioni dettagliate sul mapping del livello di coerenza tra API Cassandra o l'API per MongoDB e i livelli di coerenza di Azure Cosmos DB, vedere [API Cassandra mapping di coerenza](cassandra-consistency.md) e [API per il mapping di coerenza di MongoDB](mongodb-consistency.md).

## <a name="scope-of-the-read-consistency"></a>Ambito della coerenza di lettura

La coerenza di lettura si applica a una singola operazione di lettura con ambito all'interno di una partizione logica. L'operazione di lettura può essere generata da un client remoto o da una stored procedure.

## <a name="configure-the-default-consistency-level"></a>Configurare il livello di coerenza predefinito

È possibile configurare il livello di coerenza predefinito nell'account Azure Cosmos in qualsiasi momento. Il livello di coerenza predefinito configurato per l'account si applica a tutti i database e contenitori di Azure Cosmos con tale account. Per impostazione predefinita, tutte le operazioni di lettura e le query eseguite su un contenitore o un database usano il livello di coerenza specificato. Per altre informazioni, vedere come [configurare il livello di coerenza predefinito](how-to-manage-consistency.md#configure-the-default-consistency-level). È anche possibile eseguire l'override del livello di coerenza predefinito per una richiesta specifica. per ulteriori informazioni, vedere come [sostituire l'articolo del livello di coerenza predefinito](how-to-manage-consistency.md?#override-the-default-consistency-level) .

## <a name="guarantees-associated-with-consistency-levels"></a>Garanzie associate ai livelli di coerenza

Azure Cosmos DB garantisce che il 100% delle richieste di lettura soddisfi la garanzia di coerenza per il livello di coerenza scelto. Le definizioni esatte dei cinque livelli di coerenza in Azure Cosmos DB usando il linguaggio di specifica di TLA + sono disponibili nel repository [Azure-Cosmos-TLA](https://github.com/Azure/azure-cosmos-tla) github.

La semantica dei cinque livelli di coerenza è descritta qui:

- **Forte** : la coerenza assoluta offre una garanzia della linearità. Della linearità si riferisce alle richieste di servizio simultaneamente. È garantito che le letture restituiscano sempre la versione di un elemento di cui sia stato eseguito il commit più di recente. Un client non visualizza mai una scrittura parziale o di cui non sia stato eseguito il commit. Gli utenti possono sempre essere certi di leggere la scrittura più recente sottoposta a commit.

  Il grafico seguente illustra la coerenza assoluta con le note musicali. Dopo che i dati sono stati scritti nell'area "Stati Uniti occidentali 2", quando si leggono i dati da altre aree, si ottiene il valore più recente:

  :::image type="content" source="media/consistency-levels/strong-consistency.gif" alt-text="La coerenza come spettro" può essere configurato in due modi:

- Numero di versioni ( *K* ) dell'elemento
- Le letture dell'intervallo di tempo ( *T* ) potrebbero ritardare dietro le Scritture

Per un account a singola area, il valore minimo di *K* e *T* è 10 operazioni di scrittura o 5 secondi. Per gli account in più aree il valore minimo di *K* e *T* è 100.000 operazioni di scrittura o 300 secondi.

L'obsolescenza associata offre un ordine globale totale esterno alla "finestra di obsolescenza". Quando un client esegue le operazioni di lettura all'interno di un'area che accetta Scritture, le garanzie fornite dalla coerenza con decadimento ristretto sono identiche a quelle garantite dalla coerenza assoluta. Con l'avvicinarsi della finestra di obsolescenza per il tempo o gli aggiornamenti, a seconda del valore più vicino, il servizio limiterà le nuove scritture per consentire alla replica di aggiornarsi e rispettare la garanzia di coerenza.

All'interno della finestra di obsolescenza, il decadimento associato fornisce le garanzie di coerenza seguenti:

- Coerenza per i client nella stessa area per un account con un'area di scrittura singola = Strong
- Coerenza per i client in aree diverse per un account con un'area di scrittura singola = prefisso coerente
- Coerenza dei client che scrivono in una singola area per un account con più aree di scrittura = prefisso coerente
- Coerenza dei client che scrivono in aree diverse per un account con più aree di scrittura = eventuale

  Il decadimento ristretto viene spesso scelto da applicazioni distribuite a livello globale che prevedono latenze di scrittura ridotte, ma che richiedono la garanzia totale degli ordini globali Il decadimento ristretto è ideale per le applicazioni che includono la collaborazione e la condivisione di gruppi, il titolo di borsa, la pubblicazione-sottoscrizione/Accodamento e così via. Il grafico seguente illustra la coerenza con decadimento ristretto con le note musicali. Dopo che i dati sono stati scritti nell'area "Stati Uniti occidentali 2", le aree "Stati Uniti orientali 2" e "Australia orientale" leggono il valore scritto in base al tempo massimo di ritardo configurato o al numero massimo di operazioni:

  :::image type="content" source="media/consistency-levels/bounded-staleness-consistency.gif" alt-text="La coerenza come spettro" o si condivide il token di sessione per più writer.

I client al di fuori della sessione che esegue le Scritture vedranno le garanzie seguenti:

- Coerenza per i client nella stessa area per un account con un'area di scrittura singola = prefisso coerente
- Coerenza per i client in aree diverse per un account con un'area di scrittura singola = prefisso coerente
- Coerenza dei client che scrivono in una singola area per un account con più aree di scrittura = prefisso coerente
- Coerenza per la scrittura dei client in più aree per un account con più aree di scrittura = eventuale

  La coerenza di sessione è il livello di coerenza più diffuso per l'area singola e per le applicazioni distribuite a livello globale. Fornisce latenze di scrittura, disponibilità e velocità effettiva di lettura paragonabili a quelle della coerenza finale, ma fornisce anche le garanzie di coerenza in base alle esigenze delle applicazioni scritte per funzionare nel contesto di un utente. Il grafico seguente illustra la coerenza della sessione con le note musicali. "West US 2 Writer" e "West US 2 Reader" usano la stessa sessione (Session A) in modo che entrambi leggano gli stessi dati nello stesso momento. Mentre l'area "Australia orientale" utilizza "sessione B", riceve i dati in un secondo momento, ma nello stesso ordine delle Scritture.

  :::image type="content" source="media/consistency-levels/session-consistency.gif" alt-text="La coerenza come spettro":::

- **Prefisso coerente** : gli aggiornamenti restituiti contengono un prefisso di tutti gli aggiornamenti, senza gap. Il livello di coerenza del prefisso coerente garantisce che le letture non visualizzino mai le Scritture non ordinate.

Se le Scritture sono state eseguite nell'ordine `A, B, C` , un client può vedere `A` , `A,B` o `A,B,C` , ma non le permutazioni non ordinate come `A,C` o `B,A,C` . Il prefisso coerente fornisce latenze di scrittura, disponibilità e velocità effettiva di lettura paragonabili a quelle della coerenza finale, ma fornisce anche le garanzie di ordine che soddisfano le esigenze degli scenari in cui l'ordine è importante.

Di seguito sono riportate le garanzie di coerenza per il prefisso coerente:

- Coerenza per i client nella stessa area per un account con un'area di scrittura singola = prefisso coerente
- Coerenza per i client in aree diverse per un account con un'area di scrittura singola = prefisso coerente
- Coerenza per la scrittura dei client in una singola area per un account con più aree di scrittura = prefisso coerente
- Coerenza per la scrittura dei client in più aree per un account con più aree di scrittura = eventuale

Il grafico seguente illustra la coerenza del prefisso di coerenza con le note musicali. In tutte le aree, le letture non visualizzano mai le Scritture non in ordine:

  :::image type="content" source="media/consistency-levels/consistent-prefix.gif" alt-text="La coerenza come spettro":::

- **Eventuale** : non esiste alcuna garanzia di ordinamento per le letture. In assenza di ulteriori operazioni di scrittura, le repliche alla fine convergeranno.  
La coerenza finale è la forma di coerenza più debole, perché un client può leggere i valori più vecchi di quelli precedentemente letti. La coerenza finale è ideale in cui l'applicazione non richiede alcuna garanzia di ordinamento. Gli esempi includono il numero di Retweet, mi piace o commenti non thread. Il grafico seguente illustra la coerenza finale con le note musicali.

  :::image type="content" source="media/consistency-levels/eventual-consistency.gif" alt-text="La coerenza come spettro":::

## <a name="consistency-guarantees-in-practice"></a>Garanzie di coerenza in pratica

In pratica, è possibile che si ottengano spesso garanzie di coerenza più complesse. Per un'operazione di lettura, le garanzie di coerenza corrispondono al livello di aggiornamento e ordinamento dello stato del database richiesto. La coerenza di lettura è associata all'ordinamento e alla propagazione delle operazioni di scrittura/aggiornamento.  

Se non sono presenti operazioni di scrittura nel database, è probabile che un'operazione di lettura con livelli di coerenza **finale** , **sessione** o **prefisso coerente** produca gli stessi risultati di un'operazione di lettura con un livello di coerenza forte.

Se l'account Azure Cosmos è configurato con un livello di coerenza diverso da quello della coerenza assoluta, è possibile determinare la probabilità che i client ottengano letture solide e coerenti per i carichi di lavoro esaminando la metrica di *obsolescenza* (PBS) con decadimento ristretto. Questa metrica viene esposta nel portale di Azure. Per altre informazioni, vedere [Monitorare la metrica del decadimento ristretto probabilistico (Probabilistic Bounded Staleness, PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Il decadimento ristretto probabilistico mostra il livello di finalità della coerenza finale. Questa metrica fornisce informazioni dettagliate sulla frequenza con cui è possibile ottenere una coerenza più avanzata rispetto al livello di coerenza attualmente configurato nell'account Azure Cosmos. In altre parole, è possibile visualizzare la probabilità (misurata in millisecondi) di ottenere letture con coerenza assoluta per una combinazione di aree di scrittura e lettura.

## <a name="consistency-levels-and-latency"></a>Livelli di coerenza e latenza

La latenza di lettura per tutti i livelli di coerenza è sempre minore ai 10 millisecondi al 99° percentile. La latenza di lettura media, al cinquantesimo percentile, è in genere di 4 millisecondi o meno.

La latenza di scrittura per tutti i livelli di coerenza è sempre inferiore a 10 millisecondi al 99 ° percentile. La latenza di scrittura media, al 50° percentile, è in genere uguale o inferiore ai 5 millisecondi. Gli account di Azure Cosmos che si estendono su più aree e sono configurati con coerenza assoluta costituiscono un'eccezione a questa garanzia.

### <a name="write-latency-and-strong-consistency"></a>Latenza di scrittura e coerenza assoluta

Per gli account Azure Cosmos configurati con coerenza assoluta con più di un'area, la latenza di scrittura è pari a due volte il tempo di round trip (RTT) tra una delle due aree più lontane, più 10 millisecondi al 99 ° percentile. Un RTT di rete elevato tra le aree verrà convertito in una latenza più elevata per le richieste di Cosmos DB perché la coerenza assoluta completa un'operazione solo dopo aver verificato che sia stato eseguito il commit in tutte le aree all'interno di un account.

La latenza RTT esatta è una funzione della velocità della luce e la topologia di rete di Azure. La rete di Azure non fornisce contratti di contratto di latenza per il RTT tra due aree di Azure, ma pubblica le [statistiche sulla latenza di round trip di rete di Azure](../networking/azure-network-latency.md). Per l'account Azure Cosmos, le latenze di replica vengono visualizzate nel portale di Azure. Per monitorare le latenze di replica tra le varie aree associate all'account Azure Cosmos, è possibile usare il portale di Azure (passare al pannello metriche, selezionare scheda coerenza).

> [!IMPORTANT]
> Per impostazione predefinita, la coerenza assoluta per gli account con aree che si estendono su più di 5000 miglia (8000 km) è bloccata a causa della latenza di scrittura elevata. Per abilitare questa funzionalità, contattare il supporto tecnico.

## <a name="consistency-levels-and-throughput"></a>Livelli di coerenza e velocità effettiva

- Per un decadimento forte e con decadimento ristretto, le letture vengono eseguite su due repliche in un set di quattro repliche (quorum minoritario) per fornire garanzie di coerenza. Sessione, prefisso coerente e eventuale lettura di una singola replica. Il risultato è che, per lo stesso numero di unità di richiesta, la velocità effettiva di lettura per l'obsolescenza forte e ristretta è la metà degli altri livelli di coerenza.

- Per un determinato tipo di operazione di scrittura (come inserimento, sostituzione, upsert ed eliminazione), la velocità effettiva di scrittura per le unità di richieste è identica per tutti i livelli di coerenza.

|**Livello di coerenza**|**Letture quorum**|**Scritture quorum**|
|--|--|--|
|**Assoluta**|Minoranza locale|Maggioranza globale|
|**Obsolescenza associata**|Minoranza locale|Maggioranza locale|
|**Sessione**|Replica singola (usando il token di sessione)|Maggioranza locale|
|**Prefisso coerente**|Replica singola|Maggioranza locale|
|**Eventuali**|Replica singola|Maggioranza locale|

> [!NOTE]
> Il costo delle UR/s delle letture per le letture di minoranza locale è due volte inferiore a quello dei livelli di coerenza più vulnerabili, in quanto le letture vengono effettuate da due repliche per garantire la coerenza per l'obsolescenza forte e limitata.

## <a name="consistency-levels-and-data-durability"></a><a id="rto"></a>Livelli di coerenza e durabilità dei dati

All'interno di un ambiente di database distribuito a livello globale sussiste una relazione diretta tra il livello di coerenza e la durabilità dei dati in presenza di un'interruzione a livello di area. Quando si sviluppa il piano di continuità aziendale, è necessario conoscere il tempo massimo accettabile prima che l'applicazione venga ripristinata completamente dopo un evento di arresto improvviso. Il tempo necessario per il ripristino completo di un'applicazione è noto come **obiettivo del tempo di ripristino** ( **RTO** ). È anche necessario conoscere la perdita massima di aggiornamenti di dati recenti che l'applicazione è in grado di tollerare durante il ripristino dopo un evento di arresto improvviso. Il periodo di tempo degli aggiornamenti che è possibile perdere è noto come **obiettivo del punto di ripristino** ( **RPO** ).

La tabella seguente definisce la relazione tra il modello di coerenza e la durabilità dei dati in presenza di un'interruzione a livello di area. È importante notare che in un sistema distribuito, anche con coerenza assoluta, non è possibile avere un database distribuito con un RPO e RTO zero a causa del [teorema Cap](https://en.wikipedia.org/wiki/CAP_theorem).

|**Aree geografiche**|**Modalità di replica**|**Livello di coerenza**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Singole o più aree di scrittura|Qualsiasi livello di coerenza|< 240 minuti|<1 settimana|
|>1|Singola area di scrittura|Sessione, Prefisso coerente, Finale|< 15 minuti|< 15 minuti|
|>1|Singola area di scrittura|Decadimento ristretto|*K*  &  *T*|< 15 minuti|
|>1|Singola area di scrittura|Assoluta|0|< 15 minuti|
|>1|Più aree di scrittura|Sessione, Prefisso coerente, Finale|< 15 minuti|0|
|>1|Più aree di scrittura|Decadimento ristretto|*K*  &  *T*|0|

*K* = numero di versioni *"k"* , ovvero aggiornamenti, di un elemento.

*T* = intervallo di tempo *"t"* dall'ultimo aggiornamento.

Per un account a singola area, il valore minimo di *K* e *T* è 10 operazioni di scrittura o 5 secondi. Per gli account in più aree il valore minimo di *K* e *T* è 100.000 operazioni di scrittura o 300 secondi. Definisce il RPO minimo per i dati quando si usa il decadimento delimitato.

## <a name="strong-consistency-and-multiple-write-regions"></a>Coerenza assoluta e più aree di scrittura

Gli account Cosmos configurati con più aree di scrittura non possono essere configurati per coerenza assoluta, perché non è possibile che un sistema distribuito fornisca un RPO di zero e un RTO pari a zero. Inoltre, non ci sono vantaggi della latenza di scrittura sull'uso della coerenza assoluta con più aree di scrittura, perché una scrittura in qualsiasi area deve essere replicata e impegnata in tutte le aree configurate all'interno dell'account. In questo modo si ottiene la stessa latenza di scrittura di un singolo account di area di scrittura.

## <a name="additional-reading"></a>Altre letture

Per altre informazioni sui concetti di coerenza, vedere gli articoli seguenti:

- [High-level TLA+ specifications for the five consistency levels offered by Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla) (Specifiche TLA+ di alto livello per i cinque livelli di coerenza offerti da Azure Cosmo DB)
- [Coerenza dei dati replicati illustrata attraverso il baseball (video) di Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Coerenza dei dati replicati illustrata attraverso il baseball (white paper) di Doug Terry](https://www.microsoft.com/research/publication/replicated-data-consistency-explained-through-baseball/)
- [Session Guarantees for weakly consistent replicated data](https://dl.acm.org/citation.cfm?id=383631) (Garanzie di sessione per i dati replicati con coerenza debole)
- [Consistency Tradeoffs in Modern Distributed Database Systems Design: CAP is Only Part of the Story](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k) (Compromessi sulla coerenza nella progettazione di sistemi di database distribuiti moderni: CAP è solo una parte della storia)
- [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf) (Decadimento ristretto probabilistico per quorum parziali pratici)
- [Eventually Consistent - Revisited](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html) (Coerenza finale - Rivisitato)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui livelli di coerenza in Azure Cosmo DB, vedere gli articoli seguenti:

- [Configurare il livello di coerenza predefinito](how-to-manage-consistency.md#configure-the-default-consistency-level)
- [Sostituire il livello di coerenza predefinito](how-to-manage-consistency.md#override-the-default-consistency-level)
- [Contratto di servizio Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)