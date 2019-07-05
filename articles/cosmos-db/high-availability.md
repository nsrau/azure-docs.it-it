---
title: Disponibilità elevata in Azure Cosmos DB
description: In questo articolo viene descritto come Azure Cosmos DB garantisce disponibilità elevata
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 928c943e21e7d00b87ac1e506b98d47107ac4348
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508566"
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

- Anche in un evento raro e sfortunato quando l'area di Azure è irrecuperabile in modo permanente, non è senza perdita di dati se l'account Cosmos in più aree è configurato con il livello di coerenza predefinito *sicuro*. In caso di un'area di scrittura in modo permanente irreversibile, per gli account Cosmos multiarea configurata con coerenza con obsolescenza associata, la finestra di perdita di dati potenziale è limitata alla finestra di decadimento ristretto (*K* o*T*); per sessione, livelli di coerenza con prefisso coerente e finale, la finestra di perdita di dati potenziale è limitata a un massimo di cinque secondi. 

## <a name="availability-zone-support"></a>Supporto delle Zone di disponibilità

Azure Cosmos DB è un servizio di database multimaster distribuiti a livello globale che offre disponibilità elevata e resilienza durante le interruzioni a livello di area. Inoltre per attraversare la resilienza di area, è ora possibile abilitare **ridondanza della zona** quando si seleziona un'area da associare il database di Azure Cosmos. 

Grazie al supporto di zona di disponibilità, Azure Cosmos DB assicura le repliche vengono posizionate in più zone all'interno di una determinata area per fornire disponibilità elevata e resilienza durante gli errori di zona. Sono state apportate modifiche alla latenza e di altri contratti di servizio in questa configurazione. In caso di errore una singola zona, ridondanza di zona fornisce durabilità completa dei dati con valore RPO pari a 0 e disponibilità con RTO = 0. 

Ridondanza di zona è una *capacità supplementare* per il [replica multimaster](how-to-multi-master.md) funzionalità. Ridondanza di zona da solo non può essere ritenuta affidabile per ottenere la resilienza a livello di area. In caso di interruzioni a livello di area o l'accesso a bassa latenza tra le aree, ad esempio, è consigliabile avere più aree di scrittura oltre alla ridondanza di zona. 

Quando si configurano le scritture tra più aree per l'account Azure Cosmos, è possibile scegliere la ridondanza di zona senza costi aggiuntivi. In caso contrario, vedere la nota sotto riguardanti il piano tariffario per il supporto di ridondanza di zona. È possibile abilitare la ridondanza di zona in un'area esistente dell'account di Azure Cosmos rimuovendo l'area e aggiungerlo nuovamente con la ridondanza della zona abilitata.

Questa funzionalità è disponibile nelle aree di Azure seguenti:

* Regno Unito meridionale
* Asia sud-orientale 
* East US
* Stati Uniti orientali 2 
* Stati Uniti centrali

> [!NOTE] 
> L'abilitazione di zone di disponibilità per una singola area account di Azure Cosmos comporterà addebiti equivalenti all'aggiunta di aree geografiche aggiuntive all'account. Per altre informazioni sui prezzi, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/) e il [costi tra più aree in Azure Cosmos DB](optimize-cost-regions.md) articoli. 

La tabella seguente riepiloga le funzionalità a disponibilità elevata delle varie configurazioni di account: 

|KPI  |Singola area senza zone di disponibilità (Non-AZ)  |In una singola area con zone di disponibilità (AZ)  |Scrive in più aree con le zone di disponibilità (AZ, 2 aree): l'impostazione consigliata nella maggior parte |
|---------|---------|---------|---------|
|Contratto di servizio con disponibilità di scrittura     |   99,99%      |    99,99%     |  99,999%  |
|Contratto di servizio con disponibilità in lettura   |   99,99%      |   99,99%      |  99,999%       |
|Prezzo  |  Tariffa di fatturazione in una singola area |  Tariffa di fatturazione di un'area singola zona di disponibilità |  Tariffa di fatturazione in più aree       |
|Errori della zona – perdita di dati   |  Perdita di dati  |   Nessuna perdita di dati |   Nessuna perdita di dati  |
|Errori della zona-disponibilità |  Perdita di disponibilità  | Senza perdita di disponibilità  |  Senza perdita di disponibilità  |
|Latenza di lettura    |  Tra aree    |   Tra aree   |    Basso  |
|Latenza di scrittura    |   Tra aree   |  Tra aree    |   Basso   |
|Interruzione dell'alimentazione locale – perdita di dati    |   Perdita di dati      |  Perdita di dati       |   Perdita di dati <br/><br/> Se uso limitato della coerenza a decadimento ristretto con più aree e multimaster, perdita di dati è limitata al decadimento ristretto configurato per l'account. <br/><br/> Perdita di dati durante l'interruzione dell'alimentazione locale può essere evitata configurando una coerenza assoluta in più aree. Questa opzione include compromessi che influiscono sulle prestazioni e disponibilità.      |
|Interruzione dell'alimentazione locale-disponibilità  |  Perdita di disponibilità       |  Perdita di disponibilità       |  Senza perdita di disponibilità  |
|Velocità effettiva    |  Provisioning di UR X della velocità effettiva      |  Provisioning di UR X della velocità effettiva       |  2 x velocità effettiva con provisioning di UR/sec <br/><br/> Questa modalità di configurazione richiede due volte la quantità di velocità effettiva rispetto a una singola area con zone di disponibilità perché sono presenti due aree.   |

> [!NOTE] 
> Per abilitare il supporto di zona di disponibilità, l'account Azure Cosmos DB deve avere multi-master/a più aree operazioni di scrittura abilitati. 

Quando si aggiunge un'area per gli account Cosmos Azure nuovi o esistenti, è possibile abilitare la ridondanza di zona. Attualmente, è possibile abilitare solo la ridondanza di zona usando Azure portale, i modelli di Azure Resource Manager e PowerShell. Per abilitare la ridondanza di zona per l'account Cosmos Azure, è consigliabile impostare il `isZoneRedundant` flag per `true` per una località specifica. È possibile impostare questo flag all'interno della proprietà di percorsi. Ad esempio, il frammento di powershell seguente consente la ridondanza di zona per l'area "Asia sud-orientale":

```powershell
$locations = @( 
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" }, 
    @{ "locationName"="East US"; "failoverPriority"=1 } 
) 
```

È possibile abilitare le zone di disponibilità usando il portale di Azure durante la creazione di un account Azure Cosmos. Quando si crea un account, assicurarsi di abilitare la **ridondanza geografica**, **multiarea scrive**e scegliere un'area in cui sono supportate le zone di disponibilità: 

![Abilitare le zone di disponibilità usando il portale di Azure](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>Compilazione di applicazioni a disponibilità elevata

- Per garantire una disponibilità elevata di scrittura e lettura, configurare il proprio account Cosmos fino a occupare almeno due aree con aree a scrittura multipla. Questa configurazione fornirà la massima disponibilità, latenza più bassa e garantire la massima scalabilità per le letture e scritture sono supportate da contratti di servizio. Per altre informazioni, vedere come [configurare l'account Cosmos con più aree di scrittura](tutorial-global-distribution-sql-api.md).

- Per gli account Cosmos in più aree che sono configurati con un'area di scrittura singola, abilitare il [failover automatico usando l'interfaccia della riga di comando di Azure o il portale di Azure](how-to-manage-database-account.md#automatic-failover). Dopo aver abilitato il failover automatico, quando si verifica un'emergenza a livello di aera Cosmos DB esegue automaticamente il failover dell'account.  

- Anche se l'account Cosmos è a disponibilità elevata, l'applicazione potrebbe non essere correttamente progettata per garantire disponibilità elevata. Per testare la disponibilità elevata end-to-end dell'applicazione, chiamare periodicamente il [failover manuale tramite il portale di Azure o Azure CLI](how-to-manage-database-account.md#manual-failover), come parte del test dell'applicazione o del ripristino di emergenza (DR) drill.

- All'interno di un ambiente di database distribuito globalmente, è presente una relazione diretta tra la durabilità di livello e i dati di coerenza in presenza di un'interruzione a livello di area. Quando si sviluppa il piano di continuità aziendale, è necessario conoscere il tempo massimo accettabile prima che l'applicazione venga ripristinata completamente dopo un evento di arresto improvviso. Il tempo necessario per il ripristino completo di un'applicazione è noto come obiettivo del tempo di ripristino (RTO). È anche necessario conoscere la perdita massima di aggiornamenti di dati recenti che l'applicazione è in grado di tollerare durante il ripristino dopo un evento di arresto improvviso. Il periodo di tempo degli aggiornamenti che è possibile perdere è noto come obiettivo del punto di ripristino (RPO). Per l'obiettivo del punto di ripristino (RPO) e l'obiettivo del tempo di ripristino (RTO) per Azure Cosmos DB, vedere [Livelli di coerenza e durabilità dei dati](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Passaggi successivi

Successivamente si possono leggere gli articoli seguenti:

* [Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza](consistency-levels-tradeoffs.md)
* [Ridimensionamento a livello globale della velocità effettiva sottoposta a provisioning](scaling-throughput.md)
* [Distribuzione globale - Informazioni sul funzionamento](global-dist-under-the-hood.md)
* [Livelli di coerenza in Azure Cosmos DB](consistency-levels.md)
* [Come configurare l'account Cosmos con più aree di scrittura](how-to-multi-master.md)
