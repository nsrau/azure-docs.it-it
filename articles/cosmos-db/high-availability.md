---
title: Disponibilità elevata in Azure Cosmos DB
description: In questo articolo viene descritto come Azure Cosmos DB garantisce disponibilità elevata
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 3f882375197fa45cfbc74ff7a80ed33fd33f33a3
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400299"
---
# <a name="how-does-azure-cosmos-db-provide-high-availability"></a>In che modo Azure Cosmos DB fornisce disponibilità elevata? 

Azure Cosmos DB replica in modo trasparente i dati in tutte le aree di Azure associate all'account Azure Cosmos. Azure Cosmos DB usa più livelli di ridondanza per i dati, come illustrato nell'immagine seguente:

:::image type="content" source="./media/high-availability/cosmosdb-data-redundancy.png" alt-text="Partizionamento fisico" border="false":::

- I dati nei contenitori di Azure Cosmos sono [partizionati orizzontalmente](partitioning-overview.md).

- Un set di partizioni è una raccolta di più set di repliche. All'interno di ogni area, ogni partizione è protetta da un set di repliche con tutte le scritture replicate e sottoposte a commit permanente dalla maggior parte delle repliche. Le repliche sono distribuite in 10-20 domini di errore.

- Ogni partizione in tutte le aree viene replicata. Ogni area contiene tutte le partizioni di dati di un contenitore di Azure Cosmos e può accettare Scritture e gestire le letture.  

Se il tuo account Azure Cosmos è distribuito in *N* aree di Azure, saranno presenti almeno *n* x 4 copie di tutti i dati. In genere, un account Azure Cosmos in più di 2 aree migliora la disponibilità dell'applicazione e fornisce bassa latenza tra le aree associate. 

## <a name="slas-for-availability"></a>Contratti di servizio per la disponibilità

Come database distribuito a livello globale, Azure Cosmos DB offre contratti di contratto completi che includono velocità effettiva, latenza al 99 ° percentile, coerenza e disponibilità elevata. Nella tabella seguente vengono illustrate le garanzie per la disponibilità elevata fornite da Azure Cosmos DB per gli account con una o più aree. Per la disponibilità elevata, configurare sempre gli account Azure Cosmos in modo da avere più aree di scrittura, denominate anche multi-master.

|Tipo di operazione  | Area singola |Più aree (scrittura in area singola)|Più aree (scrittura in più aree) |
|---------|---------|---------|-------|
|Scritture    | 99,99    |99,99   |99,999|
|Letture     | 99,99    |99,999  |99,999|

> [!NOTE]
> In pratica, la disponibilità di scrittura effettiva per i modelli di decadimento ristretto, sessione, prefisso coerente e coerenza finale è significativamente superiore ai contratti di contratto pubblicati. La disponibilità effettiva di lettura per tutti i livelli di coerenza è notevolmente superiore a quella prevista dai contratti di servizio pubblicati.

## <a name="high-availability-with-azure-cosmos-db-in-the-event-of-regional-outages"></a>Disponibilità elevata con Azure Cosmos DB in caso di interruzioni a livello di area

Per i rari casi di interruzione a livello di area, Azure Cosmos DB garantisce che il database sia sempre a disponibilità elevata. I dettagli seguenti acquisiscono Azure Cosmos DB comportamento durante un'interruzione, a seconda della configurazione dell'account Azure Cosmos:

- Con Azure Cosmos DB, prima che un'operazione di scrittura venga riconosciuta al client, i dati vengono sottoposte a commit durevole da un quorum di repliche all'interno dell'area che accetta le operazioni di scrittura.

- Gli account in più aree configurati con aree a più scritture/multi-master saranno a disponibilità elevata per scritture e letture. I failover a livello di area vengono visualizzati immediatamente e non richiedono modifiche dall'applicazione.

- È possibile che gli account in area singola perdano disponibilità in caso di interruzione a livello di area. È sempre consigliabile configurare almeno **due aree** (preferibilmente, almeno due aree di scrittura) con l'account Azure Cosmos per garantire la disponibilità elevata in qualsiasi momento.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Account a più aree con un'area a scrittura singola (interruzione dell'area di scrittura)

- Durante un'interruzione dell'area di scrittura, l'account Azure Cosmos promuoverà automaticamente un'area secondaria come nuova area di scrittura primaria quando l' **Abilitazione del failover automatico** è configurata nell'account Azure Cosmos. Se abilitata, il failover viene eseguito in un'altra area nell'ordine di priorità dell'area specificato.
- Quando l'area interessata in precedenza è di nuovo online, i dati di scrittura non replicati in caso di errore nell'area vengono resi disponibili tramite il [feed dei conflitti](how-to-manage-conflicts.md#read-from-conflict-feed). Le applicazioni possono leggere il feed dei conflitti, risolvere i conflitti in base alla logica specifica dell'applicazione e scrivere di nuovo i dati aggiornati nel contenitore Azure Cosmos nel modo appropriato.
- Una volta che viene ripristinata l'area di scrittura interessata in precedenza, diventa automaticamente disponibile come area di lettura. È possibile tornare all'area ripristinata come area di scrittura. È possibile cambiare le aree usando PowerShell, l'interfaccia della riga di comando di [Azure o portale di Azure](how-to-manage-database-account.md#manual-failover). Non sono presenti **dati o perdite di disponibilità** prima di, durante o dopo l'attivazione dell'area di scrittura e l'applicazione continua a essere a disponibilità elevata.

> [!IMPORTANT]
> Si consiglia vivamente di configurare gli account Azure Cosmos usati per i carichi di lavoro di produzione per **abilitare il failover automatico**. Il failover manuale richiede la connettività tra l'area di scrittura secondaria e quella primaria per completare una verifica della coerenza per assicurarsi che non si verifichino perdite di dati durante il failover. Se l'area primaria non è disponibile, la verifica di coerenza non è completa e il failover manuale non riesce, causando la perdita della disponibilità di scrittura.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Account a più aree con un'area a scrittura singola (interruzione dell'area di lettura)

- Durante un'interruzione dell'area di lettura, gli account Azure Cosmos che usano un livello di coerenza o una coerenza assoluta con tre o più aree di lettura rimarranno a disponibilità elevata per operazioni di lettura e scrittura.
- Gli account Azure Cosmos che usano la coerenza assoluta con due o meno aree di lettura (che includono l'area di lettura & scrittura) perderanno la disponibilità di lettura durante un'interruzione dell'area di lettura.
- L'area interessata viene disconnessa automaticamente e verrà contrassegnata come offline. Il [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) reindirizza le chiamate di lettura all'area successiva disponibile nell'elenco delle aree preferite.
- Se nessuna delle aree presenti nell'elenco è disponibile, viene eseguito il fallback automatico delle chiamate all'area di scrittura corrente.
- Non sono necessarie modifiche nel codice dell'applicazione per gestire le interruzioni di lettura a livello di area. Quando l'area di lettura interessata è di nuovo online, viene sincronizzata automaticamente con l'area di scrittura corrente e sarà disponibile di nuovo per gestire le richieste di lettura.
- Le letture successive vengono reindirizzate all'area ripristinata senza richiedere alcuna modifica del codice dell'applicazione. Durante il failover e il join di un'area precedentemente non riuscita, le garanzie di coerenza di lettura continuano a essere rispettate dal Azure Cosmos DB.
- Anche in un evento raro e sfortunato quando l'area di Azure è irreversibile definitivamente, non si verifica alcuna perdita di dati se l'account Azure Cosmos in *più* aree è configurato con coerenza assoluta. In caso di un'area di scrittura irreversibile in modo permanente, un account Azure Cosmos a più aree configurato con la coerenza con obsolescenza associata, la potenziale finestra di perdita di dati è limitata alla finestra di obsolescenza (*k* o *t*) in cui k = 100000 aggiornamenti e T = 5 minuti. Per la sessione, il prefisso coerente e i livelli di coerenza finali, la finestra potenziale di perdita di dati è limitata a un massimo di 15 minuti. Per altre informazioni sulle destinazioni RTO e RPO per Azure Cosmos DB, vedere [livelli di coerenza e durabilità dei dati](consistency-levels-tradeoffs.md#rto)

## <a name="availability-zone-support"></a>Supporto per la zona di disponibilità

Oltre alla resilienza tra aree, è ora possibile abilitare la **ridondanza della zona** quando si seleziona un'area da associare al database di Azure Cosmos.

Con il supporto della zona di disponibilità, Azure Cosmos DB garantisce che le repliche vengano posizionate in più zone all'interno di una determinata area per garantire disponibilità elevata e resilienza durante gli errori di zona. Non sono state apportate modifiche alla latenza e ad altri contratti di classe in questa configurazione. In caso di errore di una singola zona, la ridondanza della zona garantisce la durabilità dei dati completa con RPO = 0 e la disponibilità con RTO = 0.

La ridondanza della zona è una *funzionalità aggiuntiva* per la [replica nella funzionalità di scrittura in più aree](how-to-multi-master.md) . Non è possibile fare affidamento solo sulla ridondanza della zona per ottenere la resilienza a livello di area. Ad esempio, in caso di interruzioni a livello di area o di accesso a bassa latenza tra le aree, si consiglia di avere più aree di scrittura oltre alla ridondanza della zona.

Quando si configurano le Scritture in più aree per l'account Azure Cosmos, è possibile acconsentire esplicitamente alla ridondanza della zona senza costi aggiuntivi. In caso contrario, vedere la nota riportata di seguito relativa ai prezzi per il supporto della ridondanza della zona. È possibile abilitare la ridondanza della zona in un'area esistente dell'account Azure Cosmos rimuovendo l'area e aggiungendola nuovamente con la ridondanza della zona abilitata.

Questa funzionalità è disponibile in: *Regno Unito meridionale, Asia sudorientale, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti centrali, Europa occidentale, Stati Uniti occidentali 2, Giappone orientale, Europa settentrionale, Francia centrale, Australia orientale, Stati Uniti orientali 2 EUAP* .

> [!NOTE]
> L'abilitazione di zone di disponibilità per un account Azure Cosmos con una sola area comporterà addebiti equivalenti all'aggiunta di un'area aggiuntiva al proprio account. Per informazioni dettagliate sui prezzi, vedere la pagina relativa ai [prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/) e il costo per più [aree in Azure Cosmos DB](optimize-cost-regions.md) articoli.

Nella tabella seguente sono riepilogate le funzionalità di disponibilità elevata di diverse configurazioni di account:

|KPI  |Singola area senza zone di disponibilità (non AZ)  |Area singola con zone di disponibilità (AZ)  |Scritture in più aree con zone di disponibilità (AZ, 2 Regions): impostazione consigliata |
|---------|---------|---------|---------|
|SLA sulla disponibilità di scrittura | 99,99% | 99,99% | 99,999% |
|SLA per la disponibilità di lettura  | 99,99% | 99,99% | 99,999% |
|Prezzo | Tariffa di fatturazione per area singola | Frequenza di fatturazione della zona di disponibilità in una singola area | Frequenza di fatturazione per più aree |
|Errori zona-perdita di dati | Perdita di dati | Senza perdita di dati | Senza perdita di dati |
|Errori zona-disponibilità | Perdita di disponibilità | Nessuna perdita di disponibilità | Nessuna perdita di disponibilità |
|Latenza lettura | Area geografica | Area geografica | Basso |
|Latenza di scrittura | Area geografica | Area geografica | Basso |
|Interruzione a livello di area-perdita di dati | Perdita di dati |  Perdita di dati | Perdita di dati <br/><br/> Quando si usa la coerenza con obsolescenza associata con più aree di scrittura e più di un'area, la perdita di dati è limitata al decadimento delimitato configurato per l'account <br /><br />È possibile evitare la perdita di dati durante un'interruzione a livello di area configurando la coerenza assoluta con più aree. Questa opzione comprende compromessi che influiscono sulla disponibilità e sulle prestazioni. Può essere configurata solo per gli account configurati per le Scritture in una singola area. |
|Interruzione a livello di area: disponibilità | Perdita di disponibilità | Perdita di disponibilità | Nessuna perdita di disponibilità |
|Velocità effettiva | Velocità effettiva con provisioning X ur/s | Velocità effettiva con provisioning X ur/s | velocità effettiva con provisioning di 2X ur/s <br/><br/> Questa modalità di configurazione richiede due volte la quantità di velocità effettiva rispetto a una singola area con zone di disponibilità perché sono presenti due aree geografiche. |

> [!NOTE]
> Per abilitare il supporto per la zona di disponibilità per un account Azure Cosmos a più aree, è necessario che l'account disponga di scritture di scrittura in più aree abilitate.

È possibile abilitare la ridondanza della zona quando si aggiunge un'area a account Azure Cosmos nuovi o esistenti. Per abilitare la ridondanza della zona nell'account Azure Cosmos, è necessario impostare il `isZoneRedundant` flag su `true` per una posizione specifica. È possibile impostare questo flag nella proprietà Locations. Ad esempio, il frammento di codice PowerShell seguente abilita la ridondanza della zona per l'area Asia sud-orientale:

```powershell
$locations = @(
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" },
    @{ "locationName"="East US"; "failoverPriority"=1; "isZoneRedundant"= "true" }
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
  --locations regionName=WestUS2 failoverPriority=1 isZoneRedundant=True
```

È possibile abilitare zone di disponibilità usando portale di Azure quando si crea un account Azure Cosmos. Quando si crea un account, assicurarsi di abilitare la **ridondanza geografica**, le **Scritture**in più aree e scegliere un'area in cui zone di disponibilità sono supportati:

:::image type="content" source="./media/high-availability/enable-availability-zones-using-portal.png" alt-text="Abilitare zone di disponibilità usando portale di Azure"::: 

## <a name="building-highly-available-applications"></a>Compilazione di applicazioni a disponibilità elevata

- Esaminare il [comportamento previsto di Azure Cosmos SDK](troubleshoot-sdk-availability.md) durante questi eventi e quali sono le configurazioni che lo interessano.

- Per garantire una disponibilità elevata per la scrittura e la lettura, configurare l'account Azure Cosmos in modo che si estenda ad almeno due aree con aree con più Scritture. Questa configurazione fornirà la massima disponibilità, la latenza più bassa e la migliore scalabilità per letture e scritture supportate da contratti di classe. Per altre informazioni, vedere come [configurare l'account Azure Cosmos con più aree di scrittura](tutorial-global-distribution-sql-api.md).

- Per gli account Azure Cosmos a più aree configurati con un'area a scrittura singola, [abilitare il failover automatico usando l'interfaccia della riga di comando di Azure o portale di Azure](how-to-manage-database-account.md#automatic-failover). Dopo aver abilitato il failover automatico, quando si verifica un'emergenza a livello di aera Cosmos DB esegue automaticamente il failover dell'account.  

- Anche se l'account Azure Cosmos è a disponibilità elevata, è possibile che l'applicazione non sia progettata correttamente per restare a disponibilità elevata. Per testare la disponibilità elevata end-to-end dell'applicazione, durante il test delle applicazioni o il ripristino di emergenza, disabilitare temporaneamente il failover automatico per l'account, richiamare il [failover manuale usando PowerShell, l'interfaccia della riga di comando di Azure o portale di Azure](how-to-manage-database-account.md#manual-failover), quindi monitorare il failover dell'applicazione. Al termine, è possibile eseguire il failback nell'area primaria e ripristinare il failover automatico per l'account.

- All'interno di un ambiente di database distribuito a livello globale, esiste una relazione diretta tra il livello di coerenza e la durabilità dei dati in presenza di un'interruzione a livello di area. Quando si sviluppa il piano di continuità aziendale, è necessario conoscere il tempo massimo accettabile prima che l'applicazione venga ripristinata completamente dopo un evento di arresto improvviso. Il tempo necessario per il ripristino completo di un'applicazione è noto come obiettivo del tempo di ripristino (RTO). È anche necessario conoscere la perdita massima di aggiornamenti di dati recenti che l'applicazione è in grado di tollerare durante il ripristino dopo un evento di arresto improvviso. Il periodo di tempo degli aggiornamenti che è possibile perdere è noto come obiettivo del punto di ripristino (RPO). Per l'obiettivo del punto di ripristino (RPO) e l'obiettivo del tempo di ripristino (RTO) per Azure Cosmos DB, vedere [Livelli di coerenza e durabilità dei dati](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Passaggi successivi

Successivamente si possono leggere gli articoli seguenti:

- [Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza](consistency-levels-tradeoffs.md)
- [Ridimensionamento a livello globale della velocità effettiva sottoposta a provisioning](scaling-throughput.md)
- [Distribuzione globale - Informazioni sul funzionamento](global-dist-under-the-hood.md)
- [Livelli di coerenza in Azure Cosmos DB](consistency-levels.md)
- [Come configurare l'account Cosmos con più aree di scrittura](how-to-multi-master.md)
- [Comportamento dell'SDK in ambienti multiarea](troubleshoot-sdk-availability.md)