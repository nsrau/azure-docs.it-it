---
title: Disponibilità elevata in Azure Cosmos DB
description: In questo articolo viene descritto come Azure Cosmos DB garantisce disponibilità elevata
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 3/24/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 1e866560ceab342f08a98ba3db05980a2b0947d2
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407556"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Disponibilità elevata con Azure Cosmos DB

Azure Cosmos DB replica in modo trasparente i dati in tutte le aree di Azure associate all'account Cosmos in uso. Cosmos DB usa più livelli di ridondanza per i dati, come mostrato nell'immagine seguente:

![Partizionamento fisico](./media/high-availability/cosmosdb-data-redundancy.png)

- I dati all'interno dei contenitori Cosmos [partizionati orizzontalmente](partitioning-overview.md).

- All'interno di ogni area, ogni partizione è protetta da un set di repliche con tutte le scritture replicate e sottoposte a commit permanente dalla maggior parte delle repliche. Le repliche sono distribuite in 10-20 domini di errore.

- Ogni partizione in tutte le aree viene replicata. Ogni area contiene tutte le partizioni di dati di un contenitore Cosmos e può accettare operazioni di scrittura e rendere disponibili le letture.  

Se l'account Cosmos viene distribuito tra *N* aree di Azure, sia almeno *N* x 4 copie di tutti i dati. Oltre a fornire l'accesso ai dati a bassa latenza e scalabilità della velocità effettiva di lettura/scrittura tra le aree associate all'account Cosmos, avere più aree (superiore *N*) migliora ulteriormente la disponibilità.  

## <a name="slas-for-availability"></a>Contratti di servizio per la disponibilità

Essendo un database distribuito a livello globale, Cosmos DB offre contratti di servizio completi che includono velocità effettiva, latenza al 99° percentile, coerenza e disponibilità elevata. Nella tabella seguente vengono descritte le garanzie relative alla disponibilità elevata fornita da Cosmos DB per gli account in aree singole e in più aree. Per la disponibilità elevata, configurare sempre gli account Cosmos per più aree di scrittura.

|Tipo di operazione  | Area singola |Più aree (scrittura in area singola)|Più aree (scrittura in più aree) |
|---------|---------|---------|-------|
|Scritture    | 99,99    |99,99   |99,999|
|Letture     | 99,99    |99,999  |99,999|

> [!NOTE]
> In pratica, la disponibilità effettiva di scrittura per il decadimento ristretto, sessione, prefisso coerente e modelli di coerenza finale è notevolmente superiore i contratti di servizio pubblicate. La disponibilità effettiva di lettura per tutti i livelli di coerenza è notevolmente superiore a quella prevista dai contratti di servizio pubblicati.

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>Disponibilità elevata con Cosmos DB in caso di interruzioni a livello di area

Interruzioni a livello di area sono abbastanza comuni e Azure Cosmos DB garantisce che il database sia sempre a disponibilità elevata. I dettagli seguenti acquisire il comportamento di Cosmos DB durante un'interruzione, a seconda della configurazione di account Cosmos:

- Con Cosmos DB, prima della conferma di un'operazione di scrittura al client, un quorum di repliche esegue il commit permanente dei dati all'interno dell'area accettando le operazioni di scrittura.

- Gli account in più aree configurati con aree a scrittura multipla rimarranno a disponibilità elevata sia per le operazioni di scrittura che per le operazioni di lettura. I failover a livello di area vengono visualizzati immediatamente e non richiedono modifiche dall'applicazione.

- **Account in più aree con un'area singola e scrittura (interruzione di area di scrittura):** durante un'interruzione del servizio in un'area di scrittura, questi account mantengono la disponibilità elevata per le letture. Per le scritture è tuttavia necessario **"abilitare il failover automatico"** il Cosmos account per eseguire il failover l'area interessata da un'altra area. Il failover verrà eseguito nell'ordine di priorità dell'area specificato. Quando l'area interessata è in modalità online, i dati non replicati presenti nell'area di scrittura interessata durante l'interruzione del servizio vengono resi disponibili tramite il [conflitti feed](how-to-manage-conflicts.md#read-from-conflict-feed). Le applicazioni possono leggere i conflitti del feed, risolvere i conflitti in base alla logica specifica dell'applicazione e scrivono nuovamente i dati aggiornati nel contenitore Cosmos come appropriato. Una volta che viene ripristinata l'area di scrittura interessata in precedenza, diventa automaticamente disponibile come area di lettura. È possibile richiamare un failover manuale e configurare l'area interessata come area di scrittura. Anche in questo caso è possibile eseguire il failover manuale tramite [portale di Azure o Azure CLI](how-to-manage-database-account.md#manual-failover). Non si verificano **perdite di dati o disponibilità** prima, durante o dopo il failover manuale. L'applicazione continua a offrire disponibilità elevata. 

- **Account in più aree con un'area singola e scrittura (interruzione di area di lettura):** durante un'interruzione del servizio in un'area di lettura, questi account mantengono la disponibilità elevata per le letture e le scritture. L’area interessata viene disconnessa automaticamente dall'area di scrittura e verrà contrassegnata offline. Il [SDK di Cosmos DB](sql-api-sdk-dotnet.md) reindirizzamento leggerà le chiamate all'area successiva disponibile nell'elenco delle aree preferite. Se nessuna delle aree presenti nell'elenco è disponibile, viene eseguito il fallback automatico delle chiamate all'area di scrittura corrente. Non sono necessarie modifiche nel codice dell'applicazione per gestire le interruzioni di lettura a livello di area. Quando alla fine l'area interessata è in di nuovo online, l'area di lettura interessata in precedenza viene sincronizzata automaticamente con l'area di scrittura corrente ed è disponibile anche in questo caso per servire le richieste di lettura. Le letture successive vengono reindirizzate all'area ripristinata senza richiedere alcuna modifica del codice dell'applicazione. Durante il failover e la nuova partecipazione a di un'area precedentemente non riuscita, continua a essere rispettati da Cosmos DB garanzie di coerenza di lettura.

- È possibile che gli account in area singola perdano disponibilità in caso di interruzione a livello di area. È sempre consigliabile configurare **almeno due aree** (preferibilmente in almeno due scrittura aree) con l'account Cosmos per garantire la disponibilità elevata in qualsiasi momento.

- Anche in un evento estremamente raro e sfortunato quando l'area di Azure è irrecuperabile in modo permanente, non è senza perdita di dati se l'account Cosmos in più aree è configurato con il livello di coerenza predefinito *sicuro*. In caso di un'area di scrittura in modo permanente irreversibile, per gli account Cosmos multiarea configurata con coerenza con obsolescenza associata, la finestra di perdita di dati potenziale è limitata alla finestra di decadimento ristretto (*K* o*T*); per sessione, livelli di coerenza con prefisso coerente e finale, la finestra di perdita di dati potenziale è limitata a un massimo di cinque secondi.

## <a name="building-highly-available-applications"></a>Compilazione di applicazioni a disponibilità elevata

- Per garantire una disponibilità elevata di scrittura e lettura, configurare il proprio account Cosmos fino a occupare almeno due aree con aree a scrittura multipla. Questa configurazione fornirà la massima disponibilità, latenza più bassa e garantire la massima scalabilità per le letture e scritture sono supportate da contratti di servizio. Per altre informazioni, vedere come [configurare l'account Cosmos con più aree di scrittura](tutorial-global-distribution-sql-api.md).

- Per gli account Cosmos in più aree che sono configurati con un'area di scrittura singola, abilitare il [failover automatico usando l'interfaccia della riga di comando di Azure o il portale di Azure](how-to-manage-database-account.md#automatic-failover). Dopo aver abilitato il failover automatico, quando si verifica un'emergenza a livello di aera Cosmos DB esegue automaticamente il failover dell'account.  

- Anche se l'account Cosmos è a disponibilità elevata, l'applicazione potrebbe non essere correttamente progettata per garantire disponibilità elevata. Per testare la disponibilità elevata end-to-end dell'applicazione, chiamare periodicamente il [failover manuale tramite il portale di Azure o Azure CLI](how-to-manage-database-account.md#manual-failover), come parte del test dell'applicazione o del ripristino di emergenza (DR) drill.

- All'interno di un ambiente di database distribuito a livello globale sussiste una relazione diretta tra il livello di coerenza e la durabilità dei dati in presenza di un'interruzione a livello di area. Quando si sviluppa il piano di continuità aziendale, è necessario conoscere il tempo massimo accettabile prima che l'applicazione venga ripristinata completamente dopo un evento di arresto improvviso. Il tempo necessario per il ripristino completo di un'applicazione è noto come obiettivo del tempo di ripristino (RTO). È anche necessario conoscere la perdita massima di aggiornamenti di dati recenti che l'applicazione è in grado di tollerare durante il ripristino dopo un evento di arresto improvviso. Il periodo di tempo degli aggiornamenti che è possibile perdere è noto come obiettivo del punto di ripristino (RPO). Per l'obiettivo del punto di ripristino (RPO) e l'obiettivo del tempo di ripristino (RTO) per Azure Cosmos DB, vedere [Livelli di coerenza e durabilità dei dati](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Passaggi successivi

Successivamente è possibile leggere gli articoli seguenti:

* [Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza](consistency-levels-tradeoffs.md)
* [Ridimensionamento a livello globale della velocità effettiva sottoposta a provisioning](scaling-throughput.md)
* [Distribuzione globale - Informazioni sul funzionamento](global-dist-under-the-hood.md)
* [Livelli di coerenza in Azure Cosmos DB](consistency-levels.md)
* [Come configurare l'account Cosmos con più aree di scrittura](how-to-multi-master.md)
