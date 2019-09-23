---
title: Disponibilità elevata in Azure Cosmos DB
description: In questo articolo viene descritto come Azure Cosmos DB garantisce disponibilità elevata
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ab6544e4535f2d2c2e88284f61251f177d457a84
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146672"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Disponibilità elevata con Azure Cosmos DB

Azure Cosmos DB replica in modo trasparente i dati in tutte le aree di Azure associate all'account Cosmos in uso. Cosmos DB usa più livelli di ridondanza per i dati, come mostrato nell'immagine seguente:

![Partizionamento fisico](./media/high-availability/cosmosdb-data-redundancy.png)

- I dati nei contenitori Cosmos sono [partizionati orizzontalmente](partitioning-overview.md).

- All'interno di ogni area, ogni partizione è protetta da un set di repliche con tutte le scritture replicate e sottoposte a commit permanente dalla maggior parte delle repliche. Le repliche sono distribuite in 10-20 domini di errore.

- Ogni partizione in tutte le aree viene replicata. Ogni area contiene tutte le partizioni di dati di un contenitore Cosmos e può accettare operazioni di scrittura e rendere disponibili le letture.  

Se l'account Cosmos è distribuito in *N* aree di Azure, saranno presenti almeno *n* x 4 copie di tutti i dati. Oltre a fornire l'accesso ai dati a bassa latenza e a ridimensionare la velocità effettiva di lettura/scrittura tra le aree associate all'account Cosmos, la presenza di più aree (maggiore di *N*) migliora ulteriormente la disponibilità.  

## <a name="slas-for-availability"></a>Contratti di servizio per la disponibilità

Essendo un database distribuito a livello globale, Cosmos DB offre contratti di servizio completi che includono velocità effettiva, latenza al 99° percentile, coerenza e disponibilità elevata. Nella tabella seguente vengono descritte le garanzie relative alla disponibilità elevata fornita da Cosmos DB per gli account in aree singole e in più aree. Per la disponibilità elevata, configurare sempre gli account Cosmos per avere più aree di scrittura.

|Tipo di operazione  | Area singola |Più aree (scrittura in area singola)|Più aree (scrittura in più aree) |
|---------|---------|---------|-------|
|Scritture    | 99,99    |99,99   |99,999|
|Letture     | 99,99    |99,999  |99,999|

> [!NOTE]
> In pratica, la disponibilità di scrittura effettiva per i modelli di decadimento ristretto, sessione, prefisso coerente e coerenza finale è significativamente superiore ai contratti di contratto pubblicati. La disponibilità effettiva di lettura per tutti i livelli di coerenza è notevolmente superiore a quella prevista dai contratti di servizio pubblicati.

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>Disponibilità elevata con Cosmos DB in caso di interruzioni a livello di area

Le interruzioni a livello di area sono abbastanza comuni e Azure Cosmos DB garantisce che il database sia sempre altamente disponibile. I dettagli seguenti acquisiscono Cosmos DB comportamento durante un'interruzione, a seconda della configurazione dell'account Cosmos:

- Con Cosmos DB, prima della conferma di un'operazione di scrittura al client, un quorum di repliche esegue il commit permanente dei dati all'interno dell'area accettando le operazioni di scrittura.

- Gli account in più aree configurati con aree a scrittura multipla rimarranno a disponibilità elevata sia per le operazioni di scrittura che per le operazioni di lettura. I failover a livello di area vengono visualizzati immediatamente e non richiedono modifiche dall'applicazione.

- **Account a più aree con un'area a scrittura singola (interruzione dell'area di scrittura):** 
  * durante un'interruzione del servizio in un'area di scrittura, questi account mantengono la disponibilità elevata per le letture. Affinché le richieste di scrittura abbiano esito positivo, è necessario attivare l'opzione **Abilita failover automatico** nell'account Azure Cosmos. L'abilitazione di questa opzione consente di eseguire il failover dell'area interessata in un'altra area nell'ordine di priorità dell'area specificata. 
  * Quando l'area interessata in precedenza è di nuovo online, tutti i dati di scrittura che non sono stati replicati quando l'area ha avuto esito negativo vengono resi disponibili tramite il [feed dei conflitti](how-to-manage-conflicts.md#read-from-conflict-feed). Le applicazioni possono leggere il feed dei conflitti, risolvere i conflitti in base alla logica specifica dell'applicazione e scrivere di nuovo i dati aggiornati nel contenitore Azure Cosmos nel modo appropriato. 
  * Una volta che viene ripristinata l'area di scrittura interessata in precedenza, diventa automaticamente disponibile come area di lettura. È possibile tornare all'area ripristinata come area di scrittura. È possibile cambiare le aree usando l'interfaccia della riga di comando di [Azure o portale di Azure](how-to-manage-database-account.md#manual-failover). Non sono presenti **dati o perdite di disponibilità** prima di, durante o dopo l'attivazione dell'area di scrittura e l'applicazione continua a essere a disponibilità elevata. 

- **Account a più aree con un'area a scrittura singola (interruzione dell'area di lettura):** 
  * durante un'interruzione del servizio in un'area di lettura, questi account mantengono la disponibilità elevata per le letture e le scritture. 
  * L’area interessata viene disconnessa automaticamente dall'area di scrittura e verrà contrassegnata offline. Il [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) reindirizza le chiamate di lettura all'area successiva disponibile nell'elenco delle aree preferite. 
  * Se nessuna delle aree presenti nell'elenco è disponibile, viene eseguito il fallback automatico delle chiamate all'area di scrittura corrente. 
  * Non sono necessarie modifiche nel codice dell'applicazione per gestire le interruzioni di lettura a livello di area. Quando alla fine l'area interessata è in di nuovo online, l'area di lettura interessata in precedenza viene sincronizzata automaticamente con l'area di scrittura corrente ed è disponibile anche in questo caso per servire le richieste di lettura. 
  * Le letture successive vengono reindirizzate all'area ripristinata senza richiedere alcuna modifica del codice dell'applicazione. Durante il failover e il join di un'area precedentemente non riuscita, le garanzie di coerenza di lettura continuano a essere rispettate dal Cosmos DB.

- È possibile che gli account in area singola perdano disponibilità in caso di interruzione a livello di area. È sempre consigliabile impostare almeno **due aree** (preferibilmente, almeno due aree di scrittura) con l'account Cosmos per garantire la disponibilità elevata in qualsiasi momento.

- Anche in un evento raro e sfortunato quando l'area di Azure è irreversibile in modo permanente, non si verifica alcuna perdita di dati se l'account Cosmos multiarea è configurato con il livello di coerenza predefinito di *Strong*. In caso di un'area di scrittura irreversibile in modo permanente, per gli account Cosmos a più aree configurati con la coerenza con obsolescenza associata, la finestra di perdita di dati potenziale è limitata alla finestra di obsolescenza (*K* o *T*); per la sessione, il prefisso coerente e i livelli di coerenza finali, la finestra potenziale di perdita di dati è limitata a un massimo di cinque secondi. 

## <a name="availability-zone-support"></a>Supporto per la zona di disponibilità

Azure Cosmos DB è un servizio di database multimaster distribuito a livello globale che garantisce disponibilità elevata e resilienza in caso di interruzioni a livello di area. Oltre alla resilienza tra aree, è ora possibile abilitare la **ridondanza della zona** quando si seleziona un'area da associare al database di Azure Cosmos. 

Con il supporto della zona di disponibilità, Azure Cosmos DB garantisce che le repliche vengano posizionate in più zone all'interno di una determinata area per garantire disponibilità elevata e resilienza durante gli errori di zona. Non sono state apportate modifiche alla latenza e ad altri contratti di classe in questa configurazione. In caso di errore di una singola zona, la ridondanza della zona garantisce la durabilità dei dati completa con RPO = 0 e la disponibilità con RTO = 0. 

La ridondanza della zona è una *funzionalità aggiuntiva* per la funzionalità di [replica multimaster](how-to-multi-master.md) . La sola ridondanza della zona non è tuttavia sufficiente per ottenere la resilienza a livello di area. Ad esempio, in caso di interruzioni a livello di area o di accesso a bassa latenza tra le aree, si consiglia di avere più aree di scrittura oltre alla ridondanza della zona. 

Quando si configurano le Scritture in più aree per l'account Azure Cosmos, è possibile acconsentire esplicitamente alla ridondanza della zona senza costi aggiuntivi. In caso contrario, vedere la nota riportata di seguito relativa ai prezzi per il supporto della ridondanza della zona. Puoi abilitare la ridondanza della zona in un'area esistente del tuo account di Azure Cosmos DB rimuovendo l'area e aggiungendola di nuovo con la ridondanza della zona abilitata.

Questa funzionalità è disponibile nelle aree di Azure seguenti:

* Regno Unito meridionale
* Asia sud-orientale 
* East US
* Stati Uniti orientali 2 
* Stati Uniti centrali
* Europa occidentale
* Stati Uniti occidentali 2

> [!NOTE] 
> L'abilitazione di zone di disponibilità per un account Azure Cosmos con una sola area comporterà addebiti equivalenti all'aggiunta di un'area aggiuntiva al proprio account. Per informazioni dettagliate sui prezzi, vedere la pagina relativa ai [prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/) e il costo per più [aree in Azure Cosmos DB](optimize-cost-regions.md) articoli. 

Nella tabella seguente sono riepilogate le funzionalità di disponibilità elevata di diverse configurazioni di account: 

|Indicatore KPI  |Singola area senza zone di disponibilità (non AZ)  |Area singola con zone di disponibilità (AZ)  |Scritture in più aree con zone di disponibilità (AZ, 2 Regions): impostazione consigliata |
|---------|---------|---------|---------|
|SLA sulla disponibilità di scrittura     |   99,99%      |    99,99%     |  99,999%  |
|SLA per la disponibilità di lettura   |   99,99%      |   99,99%      |  99,999%       |
|Prezzo  |  Tariffa di fatturazione per area singola |  Frequenza di fatturazione della zona di disponibilità in una singola area |  Frequenza di fatturazione per più aree       |
|Errori zona-perdita di dati   |  Perdita di dati  |   Nessuna perdita di dati |   Nessuna perdita di dati  |
|Errori zona-disponibilità |  Perdita di disponibilità  | Nessuna perdita di disponibilità  |  Nessuna perdita di disponibilità  |
|Latenza lettura    |  Area geografica    |   Area geografica   |    Basso  |
|Latenza di scrittura    |   Area geografica   |  Area geografica    |   Basso   |
|Interruzione a livello di area-perdita di dati    |   Perdita di dati      |  Perdita di dati       |   Perdita di dati <br/><br/> Quando si usa la coerenza con obsolescenza associata con più master e più aree, la perdita di dati è limitata al decadimento delimitato configurato per l'account. <br/><br/> È possibile evitare la perdita di dati durante un'interruzione a livello di area configurando la coerenza assoluta con più aree. Questa opzione presenta compromessi che influiscano sulla disponibilità e sulle prestazioni.      |
|Interruzione a livello di area: disponibilità  |  Perdita di disponibilità       |  Perdita di disponibilità       |  Nessuna perdita di disponibilità  |
|Velocità effettiva    |  Velocità effettiva con provisioning X ur/s      |  Velocità effettiva con provisioning X ur/s       |  velocità effettiva con provisioning di 2X ur/s <br/><br/> Questa modalità di configurazione richiede due volte la quantità di velocità effettiva rispetto a una singola area con zone di disponibilità perché sono presenti due aree geografiche.   |

> [!NOTE] 
> Per abilitare il supporto per la zona di disponibilità per un account Azure Cosmos a più aree, è necessario che nell'account siano abilitate le Scritture multimaster.


È possibile abilitare la ridondanza della zona quando si aggiunge un'area a account Azure Cosmos nuovi o esistenti. Per abilitare la ridondanza della zona nell'account Azure Cosmos, è necessario impostare `isZoneRedundant` il `true` flag su per una posizione specifica. È possibile impostare questo flag nella proprietà Locations. Ad esempio, il frammento di codice PowerShell seguente abilita la ridondanza della zona per l'area Asia sud-orientale:

```powershell
$locations = @( 
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" }, 
    @{ "locationName"="East US"; "failoverPriority"=1 } 
) 
```

Il comando seguente mostra come abilitare la ridondanza della zona per le aree "Eastus" e "WestUS2":

```azurecli-interactive
az cosmosdb create \
  --name mycosmosdbaccount \
  --resource-group myResourceGroup \
  --kind GlobalDocumentDB \
  --default-consistency-level Session \
  --locations regionName=EastUS failoverPriority=0 isZoneRedundant=True \
  --locations regionName=WestUS2 failoverPriority=1 isZoneRedundant=True \
  --enable-multiple-write-locations
```

È possibile abilitare zone di disponibilità usando portale di Azure quando si crea un account Azure Cosmos. Quando si crea un account, assicurarsi di abilitare la **ridondanza geografica**, le Scritture in più **aree**e scegliere un'area in cui zone di disponibilità sono supportati: 

![Abilitare zone di disponibilità usando portale di Azure](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>Compilazione di applicazioni a disponibilità elevata

- Per garantire una disponibilità elevata di scrittura e lettura, configurare il proprio account Cosmos fino a occupare almeno due aree con aree a scrittura multipla. Questa configurazione fornirà la massima disponibilità, la latenza più bassa e la migliore scalabilità per letture e scritture supportate da contratti di classe. Per altre informazioni, vedere come [configurare l'account Cosmos con più aree di scrittura](tutorial-global-distribution-sql-api.md).

- Per gli account Cosmos in più aree che sono configurati con un'area di scrittura singola, abilitare il [failover automatico usando l'interfaccia della riga di comando di Azure o il portale di Azure](how-to-manage-database-account.md#automatic-failover). Dopo aver abilitato il failover automatico, quando si verifica un'emergenza a livello di aera Cosmos DB esegue automaticamente il failover dell'account.  

- Anche se l'account Cosmos è a disponibilità elevata, l'applicazione potrebbe non essere correttamente progettata per garantire disponibilità elevata. Per testare la disponibilità elevata end-to-end dell'applicazione, richiamare periodicamente il [failover manuale usando l'interfaccia della riga di comando di Azure o portale di Azure](how-to-manage-database-account.md#manual-failover), come parte del test dell'applicazione o del ripristino di emergenza.

- All'interno di un ambiente di database distribuito a livello globale, esiste una relazione diretta tra il livello di coerenza e la durabilità dei dati in presenza di un'interruzione a livello di area. Quando si sviluppa il piano di continuità aziendale, è necessario conoscere il tempo massimo accettabile prima che l'applicazione venga ripristinata completamente dopo un evento di arresto improvviso. Il tempo necessario per il ripristino completo di un'applicazione è noto come obiettivo del tempo di ripristino (RTO). È anche necessario conoscere la perdita massima di aggiornamenti di dati recenti che l'applicazione è in grado di tollerare durante il ripristino dopo un evento di arresto improvviso. Il periodo di tempo degli aggiornamenti che è possibile perdere è noto come obiettivo del punto di ripristino (RPO). Per l'obiettivo del punto di ripristino (RPO) e l'obiettivo del tempo di ripristino (RTO) per Azure Cosmos DB, vedere [Livelli di coerenza e durabilità dei dati](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Passaggi successivi

Successivamente si possono leggere gli articoli seguenti:

* [Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza](consistency-levels-tradeoffs.md)
* [Ridimensionamento a livello globale della velocità effettiva sottoposta a provisioning](scaling-throughput.md)
* [Distribuzione globale - Informazioni sul funzionamento](global-dist-under-the-hood.md)
* [Livelli di coerenza in Azure Cosmos DB](consistency-levels.md)
* [Come configurare l'account Cosmos con più aree di scrittura](how-to-multi-master.md)
