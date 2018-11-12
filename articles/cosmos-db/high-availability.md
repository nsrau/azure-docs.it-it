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
ms.openlocfilehash: fdf1bf227ed21111c1aa0754a8423c1ee6aa151b
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243926"
---
# <a name="high-availability-in-azure-cosmos-db"></a>Disponibilità elevata in Azure Cosmos DB

Azure Cosmos DB replica in modo trasparente i dati in tutte le aree Azure associate al proprio account Cosmos.  Cosmos DB utilizza più livelli di ridondanza per i propri dati. Come illustrato dalla figura riportata di seguito:

- I dati all'interno dei contenitori Cosmos sono partizionati orizzontalmente.
- All'interno di ogni area, ogni partizione è protetta da un set di repliche con tutte le scritture replicate e con un commit permanente dalla maggior parte delle repliche. Le repliche sono distribuite in 10-20 domini di errore.
- In tutte le aree, vengono replicate tutte le partizioni. Ogni area contiene tutte le partizioni di dati di un contenitore Cosmos e può accettare operazioni di scrittura e rendere disponibili le letture.  

![Partizionamento delle risorse](./media/high-availability/figure1.png) 

Se il proprio account Cosmos viene distribuito in N aree di Azure, ci saranno almeno N x 4 copie di tutti i propri dati. Oltre a fornire un accesso a bassa latenza ai propri dati e a scalare la velocità effettiva di scrittura e lettura nelle regioni associate al proprio account Cosmos, avere più regioni (N più alto) migliora anche la disponibilità.  

## <a name="availability-slas"></a>Contratti di servizio relativi alla disponibilità  

Essendo un database distribuito a livello globale, Cosmos DB offre contratti di servizio completi che includono velocità effettiva, latenza al 99° percentile, coerenza e disponibilità elevata. Nella tabella riportata di seguito vengono descritte le garanzie relative alla disponibilità elevata fornita da Cosmos DB per gli account in aree singole e in più aree. Per la massima disponibilità, è consigliabile configurare gli account Cosmos per più aree di scrittura.

|Tipo di operazione  | Area singola |Più aree (scrittura in area singola)|Più aree (scrittura in più aree)|
|---------|---------|---------|-------|
|Scritture    | 99,99    |99,99   |99,999|
|Letture     | 99,99    |99,999  |99,999|

> [!NOTE]
> In pratica, la disponibilità effettiva di scrittura per i modelli di decadimento ristretto, sessione, prefisso coerente e coerenza finale è notevolmente superiore rispetto ai contratti di servizi pubblicati. E, in pratica, la disponibilità effettiva di lettura per tutti i livelli di coerenza è notevolmente superiore rispetto ai contratti di servizio pubblicati.

## <a name="regional-outages"></a>Interruzioni a livello di area

Le interruzioni a livello di area sono abbastanza comuni e Azure Cosmos DB garantisce che il database sia sempre disponibile. Di seguito viene descritto il comportamento del Cosmos DB durante un'interruzione, a seconda della configurazione dell'account Cosmos: 

- Gli account in più aree configurati con regioni a scrittura multipla rimarranno a disponibilità elevata sia per le scritture che per le letture durante un'interruzione a livello di area. I failover a livello di area vengono visualizzati immediatamente e non richiedono modifiche dall'applicazione.

- Gli account in più aree con un'area di scrittura singola durante un'interruzione a livello di area di scrittura rimarrà a disponibilità elevata per le letture. Tuttavia, per le scritture è necessario "abilitare il failover automatico" nel proprio account Cosmos per consentire a Cosmos DB di eseguire il failover dell'area interessata in un'altra area associata al proprio account Cosmos. Il failover verrà eseguito nell'ordine di priorità dell'area specificato. Alla fine, quando l'area interessata è di nuovo online, i dati non replicati presenti nell'area di scrittura interessata durante l'interruzione vengono resi disponibili attraverso il feed dei conflitti. Le applicazioni possono leggere il feed in conflitto, risolvere i conflitti in base alla logica specifica dell'applicazione e scrivere di nuovo i dati aggiornati nel contenitore Cosmos come appropriato. Una volta che viene ripristinata l'area di scrittura interessata in precedenza, diventa automaticamente disponibile come area di lettura. È possibile richiamare un failover manuale e ripristinare l'area interessata come area di scrittura. È possibile eseguire un failover manuale utilizzando l’interfaccia della riga di comando di Azure o il portale di Azure.  

- Gli account in più aree con un'area di scrittura singola durante un'interruzione a livello di area di lettura rimarrà a disponibilità elevata per le letture e le scritture. L’area interessata viene disconnessa automaticamente dall'area di scrittura e verrà contrassegnata offline. Gli SDK di Cosmos DB consentono di reindirizzare le chiamate di lettura all'area successiva disponibile nell'elenco delle aree preferite. Se nessuna delle aree presenti nell'elenco è disponibile, viene eseguito il fallback automatico delle chiamate all'area di scrittura corrente. Non sono necessarie modifiche nel codice dell'applicazione per gestire le interruzioni di lettura a livello di area. Infine, quando l'area interessata è in di nuovo online, l'area di lettura interessata in precedenza verrà sincronizzata automaticamente con l'area di scrittura corrente e sarà disponibile anche in questo caso per rendere disponibili le letture. Le letture successive vengono reindirizzate all'area ripristinata senza richiedere alcuna modifica del codice dell'applicazione. Sia durante il failover che durante il ricongiungimento di un’area che ha riscontrato errori in precedenza, le garanzie di coerenza di lettura continuano a essere rispettate da Cosmos DB.

- Gli account in area singola potrebbero perdere la disponibilità in caso di interruzione a livello di area. È altamente consigliabile configurare almeno due aree (preferibilmente in almeno due aree di scrittura) con il proprio account Cosmos per garantire la disponibilità elevata in qualsiasi momento.

## <a name="building-highly-available-applications"></a>Compilazione di applicazioni a disponibilità elevata

- Per garantire una disponibilità elevata di scrittura e lettura, configurare il proprio account Cosmos fino a occupare almeno due aree con aree a scrittura multipla. Questa configurazione garantisce la migliore disponibilità, più bassa latenza e scalabilità per letture e scritture, supportate da contratti di servizio. Vedere come [configurare l'account Cosmos con più aree di scrittura-](tutorial-global-distribution-sql-api.md).

- Per gli account Cosmos in più aree che sono configurati con un'area di scrittura singola, abilitare il "failover automatico" utilizzando l’interfaccia della riga di comando di Azure o il portale di Azure.  Dopo aver abilitato il failover automatico, quando si verifica un'emergenza a livello di aera Cosmos DB esegue automaticamente il failover dell'account.  

- Anche se l'account Cosmos è a disponibilità elevata, l'applicazione potrebbe non essere correttamente progettata per garantire disponibilità elevata. Per garantire la disponibilità elevata end-to-end per l'applicazione, richiamare periodicamente il "failover manuale" usando l’interfaccia della riga di comando di Azure o il portale di Azure come parte del test dell'applicazione o il drill di ripristino di emergenza (DR). Per altre informazioni, vedere la documentazione relativa alla modifica delle priorità a livello di area per l'account Cosmos.  

## <a name="next-steps"></a>Passaggi successivi

Di seguito è possibile apprendere come eseguire il ridimensionamento della velocità effettiva nel seguente articolo:

* [Ridimensionamento velocità effettiva](scaling-throughput.md)

* [Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza](consistency-levels-tradeoffs.md)

* [Ridimensionamento della velocità effettiva a livello globale](scaling-throughput.md)

* [Distribuzione globale - Informazioni sul funzionamento](global-dist-under-the-hood.md)

* [Livelli di coerenza in Azure Cosmos DB](consistency-levels.md)


