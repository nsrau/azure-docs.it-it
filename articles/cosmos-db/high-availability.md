---
title: Disponibilità elevata in Azure Cosmos DB
description: In questo articolo viene descritto come Azure Cosmos DB garantisce disponibilità elevata
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 5dc43aa5b98f097bd8bdf927f40b2d3efc878d48
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283496"
---
# <a name="high-availability-in-azure-cosmos-db"></a>Disponibilità elevata in Azure Cosmos DB

Azure Cosmos DB replica in modo trasparente i dati in tutte le aree di Azure associate all'account Cosmos in uso. Cosmos DB usa più livelli di ridondanza per i dati, come mostrato nell'immagine seguente:

![Partizionamento delle risorse](./media/high-availability/figure1.png) 

- I dati all'interno dei contenitori Cosmos sono partizionati orizzontalmente.

- All'interno di ogni area, ogni partizione è protetta da un set di repliche con tutte le scritture replicate e sottoposte a commit permanente dalla maggior parte delle repliche. Le repliche sono distribuite in 10-20 domini di errore.

- Ogni partizione in tutte le aree viene replicata. Ogni area contiene tutte le partizioni di dati di un contenitore Cosmos e può accettare operazioni di scrittura e rendere disponibili le letture.  

Se il proprio account Cosmos viene distribuito in N aree di Azure, ci saranno almeno N x 4 copie di tutti i propri dati. Oltre ad assicurare un accesso ai dati a bassa latenza e a scalare la velocità effettiva di scrittura e lettura nelle aree associate all'account Cosmos, avere più aree (N più alto) migliora anche la disponibilità.  

## <a name="slas-for-availability"></a>Contratti di servizio per la disponibilità   

Essendo un database distribuito a livello globale, Cosmos DB offre contratti di servizio completi che includono velocità effettiva, latenza al 99° percentile, coerenza e disponibilità elevata. Nella tabella riportata di seguito vengono descritte le garanzie relative alla disponibilità elevata fornita da Cosmos DB per gli account in aree singole e in più aree. Per la disponibilità elevata, configurare gli account Cosmos per più aree di scrittura.

|Tipo di operazione  | Area singola |Più aree (scrittura in area singola)|Più aree (scrittura in più aree) |
|---------|---------|---------|-------|
|Scritture    | 99,99    |99,99   |99,999|
|Letture     | 99,99    |99,999  |99,999|

> [!NOTE]
> In pratica, la disponibilità effettiva di scrittura per i modelli di decadimento ristretto, sessione, prefisso coerente e coerenza finale è notevolmente superiore rispetto ai contratti di servizi pubblicati. La disponibilità effettiva di lettura per tutti i livelli di coerenza è notevolmente superiore a quella prevista dai contratti di servizio pubblicati.

## <a name="regional-outages"></a>Interruzioni a livello di area

Le interruzioni a livello di area sono abbastanza comuni e Azure Cosmos DB garantisce che il database sia sempre disponibile. I dettagli seguenti mostrano il comportamento di Cosmos DB durante un'interruzione del servizio, a seconda della configurazione dell'account Cosmos: 

- Gli account in più aree configurati con aree a scrittura multipla rimarranno a disponibilità elevata sia per le operazioni di scrittura che per le operazioni di lettura. I failover a livello di area vengono visualizzati immediatamente e non richiedono modifiche dall'applicazione.

- Account in più aree con un'area di scrittura singola: durante un'interruzione del servizio in un'area di scrittura, questi account mantengono la disponibilità elevata per le letture. Per le scritture è invece necessario "abilitare il failover automatico" nell'account Cosmos per consentire il failover dell'area interessata in un'altra area associata. Il failover verrà eseguito nell'ordine di priorità dell'area specificato. Quando alla fine l'area interessata è di nuovo online, i dati non replicati presenti nell'area di scrittura interessata durante l'interruzione vengono resi disponibili attraverso il feed dei conflitti. Le applicazioni possono leggere il feed in conflitto, risolvere i conflitti in base alla logica specifica dell'applicazione e scrivere di nuovo i dati aggiornati nel contenitore Cosmos come appropriato. Una volta che viene ripristinata l'area di scrittura interessata in precedenza, diventa automaticamente disponibile come area di lettura. È possibile richiamare un failover manuale e ripristinare l'area interessata come area di scrittura. È possibile eseguire un failover manuale tramite l'[interfaccia della riga di comando di Azure o il portale di Azure](how-to-manage-database-account.md#enable-manual-failover-for-your-cosmos-account).  

- Account in più aree con un'area di scrittura singola: durante un'interruzione del servizio a livello di area di lettura, questi account mantengono la disponibilità elevata per le letture e le scritture. L’area interessata viene disconnessa automaticamente dall'area di scrittura e verrà contrassegnata offline. Gli SDK di Cosmos DB consentono di reindirizzare le chiamate di lettura all'area successiva disponibile nell'elenco delle aree preferite. Se nessuna delle aree presenti nell'elenco è disponibile, viene eseguito il fallback automatico delle chiamate all'area di scrittura corrente. Non sono necessarie modifiche nel codice dell'applicazione per gestire le interruzioni di lettura a livello di area. Quando alla fine l'area interessata è in di nuovo online, l'area di lettura interessata in precedenza viene sincronizzata automaticamente con l'area di scrittura corrente ed è disponibile anche in questo caso per servire le richieste di lettura. Le letture successive vengono reindirizzate all'area ripristinata senza richiedere alcuna modifica del codice dell'applicazione. Sia durante il failover che durante il ricongiungimento di un’area che ha riscontrato errori in precedenza, le garanzie di coerenza di lettura continuano a essere rispettate da Cosmos DB.

- Gli account in area singola potrebbero perdere la disponibilità in caso di interruzione a livello di area. È consigliabile configurare almeno due aree (preferibilmente almeno due aree di scrittura) con l'account Cosmos in uso per garantire la disponibilità elevata in qualsiasi momento.

## <a name="building-highly-available-applications"></a>Compilazione di applicazioni a disponibilità elevata

- Per garantire una disponibilità elevata di scrittura e lettura, configurare il proprio account Cosmos fino a occupare almeno due aree con aree a scrittura multipla. Questa configurazione garantisce disponibilità, latenza minima e scalabilità sia per le operazioni di lettura che per le operazioni di scrittura supportate dai contratti di servizio. Per altre informazioni, vedere come [configurare l'account Cosmos con più aree di scrittura](tutorial-global-distribution-sql-api.md).

- Per gli account Cosmos in più aree che sono configurati con un'area di scrittura singola, abilitare il [failover automatico usando l'interfaccia della riga di comando di Azure o il portale di Azure](how-to-manage-database-account.md#enable-automatic-failover-for-your-cosmos-account). Dopo aver abilitato il failover automatico, quando si verifica un'emergenza a livello di aera Cosmos DB esegue automaticamente il failover dell'account.  

- Anche se l'account Cosmos è a disponibilità elevata, l'applicazione potrebbe non essere correttamente progettata per garantire disponibilità elevata. Per garantire la disponibilità elevata end-to-end per l'applicazione, richiamare periodicamente il [failover manuale usando l'interfaccia della riga di comando di Azure o il portale di Azure](how-to-manage-database-account.md#enable-manual-failover-for-your-cosmos-account) come parte dei test dell'applicazione o i drill di ripristino di emergenza (DR). 

## <a name="next-steps"></a>Passaggi successivi

Di seguito è possibile apprendere come eseguire il ridimensionamento della velocità effettiva nel seguente articolo:

* [Ridimensionamento velocità effettiva](scaling-throughput.md)

* [Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza](consistency-levels-tradeoffs.md)

* [Scaling provisioned throughput globally](scaling-throughput.md) (Ridimensionamento a livello globale della velocità effettiva sottoposta a provisioning)

* [Distribuzione globale - Informazioni sul funzionamento](global-dist-under-the-hood.md)

* [Livelli di coerenza in Azure Cosmos DB](consistency-levels.md)


