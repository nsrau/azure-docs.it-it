---
title: Disponibilità elevata in Azure Cosmos DB
description: In questo articolo viene descritto come Azure Cosmos DB garantisce disponibilità elevata
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 0f024bac535ed792d8480c991e470cf5d85932b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247423"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Disponibilità elevata con Azure Cosmos DB

Azure Cosmos DB replica in modo trasparente i dati in tutte le aree di Azure associate all'account Cosmos in uso. Cosmos DB usa più livelli di ridondanza per i dati, come mostrato nell'immagine seguente:

![Partizionamento fisico](./media/high-availability/cosmosdb-data-redundancy.png)

- I dati all'interno dei contenitori Cosmos sono [partizionati orizzontalmente.](partitioning-overview.md)

- All'interno di ogni area, ogni partizione è protetta da un set di repliche con tutte le scritture replicate e sottoposte a commit permanente dalla maggior parte delle repliche. Le repliche sono distribuite in 10-20 domini di errore.

- Ogni partizione in tutte le aree viene replicata. Ogni area contiene tutte le partizioni di dati di un contenitore Cosmos e può accettare operazioni di scrittura e rendere disponibili le letture.  

Se l'account Cosmos è distribuito tra *n* aree di Azure, saranno presenti almeno *N* x 4 copie di tutti i dati. Oltre a fornire accesso ai dati a bassa latenza e scalabilità della velocità effettiva di scrittura/lettura nelle aree associate all'account Cosmos, avere più regioni *(N*più elevate) migliora ulteriormente la disponibilità.  

## <a name="slas-for-availability"></a>Contratti di servizio per la disponibilità

Essendo un database distribuito a livello globale, Cosmos DB offre contratti di servizio completi che includono velocità effettiva, latenza al 99° percentile, coerenza e disponibilità elevata. Nella tabella seguente vengono descritte le garanzie relative alla disponibilità elevata fornita da Cosmos DB per gli account in aree singole e in più aree. Per la disponibilità elevata, configurare sempre gli account Cosmos in modo che dispongano di più aree di scrittura.

|Tipo di operazione  | Area singola |Più aree (scrittura in area singola)|Più aree (scrittura in più aree) |
|---------|---------|---------|-------|
|Scritture    | 99,99    |99,99   |99,999|
|Letture     | 99,99    |99,999  |99,999|

> [!NOTE]
> In pratica, la disponibilità effettiva di scrittura per l'obsolescenza delimitata, la sessione, il prefisso coerente e l'eventuale modello di coerenza è notevolmente superiore rispetto ai contratti di servizio pubblicati. La disponibilità effettiva di lettura per tutti i livelli di coerenza è notevolmente superiore a quella prevista dai contratti di servizio pubblicati.

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>Disponibilità elevata in Cosmos DB in caso di interruzioni a livello di area

Le interruzioni a livello di area sono abbastanza comuni e Azure Cosmos DB garantisce che il database sia sempre altamente disponibile. I dettagli seguenti acquisiscono il comportamento di Cosmos DB durante un'interruzione, a seconda della configurazione dell'account Cosmos:

- Con Cosmos DB, prima della conferma di un'operazione di scrittura al client, un quorum di repliche esegue il commit permanente dei dati all'interno dell'area accettando le operazioni di scrittura.

- Gli account in più aree configurati con aree a scrittura multipla rimarranno a disponibilità elevata sia per le operazioni di scrittura che per le operazioni di lettura. I failover a livello di area vengono visualizzati immediatamente e non richiedono modifiche dall'applicazione.

- È possibile che gli account in area singola perdano disponibilità in caso di interruzione a livello di area. È sempre consigliabile configurare **almeno due aree** (preferibilmente, almeno due aree di scrittura) con il tuo account Cosmos per garantire la disponibilità elevata in ogni momento.

- **Account con più aree con un'area di scrittura singola (interruzione dell'area di scrittura):Multi-region accounts with a single-write region (write region outage):**
  - Durante un'interruzione dell'area di scrittura, l'account Cosmos promuoverà automaticamente un'area secondaria a nuova area di scrittura primaria quando **l'abilitazione** del failover automatico viene configurata nell'account Cosmos di Azure.During a write region outage, the Cosmos account will automatically promote a secondary region to be the new primary write region when enable automatic failover is configured on the Azure Cosmos account. Se abilitato, il failover verrà eseguito in un'altra area nell'ordine di priorità dell'area specificato.
  - I clienti possono anche scegliere di utilizzare il **failover manuale** e monitorare l'URL dell'endpoint di scrittura Cosmos da soli utilizzando un agente creato da soli. Per i clienti con esigenze di monitoraggio dello stato complesse e sofisticate, questo può fornire un RTO ridotto in caso di errore nell'area di scrittura.
  - Quando l'area interessata in precedenza è di nuovo online, tutti i dati di scrittura non replicati in caso di errore dell'area vengono resi disponibili tramite il [feed Conflitti.](how-to-manage-conflicts.md#read-from-conflict-feed) Le applicazioni possono leggere il feed dei conflitti, risolvere i conflitti in base alla logica specifica dell'applicazione e scrivere i dati aggiornati nel contenitore Cosmos di Azure in base alle esigenze.
  - Una volta che viene ripristinata l'area di scrittura interessata in precedenza, diventa automaticamente disponibile come area di lettura. È possibile tornare all'area ripristinata come area di scrittura. È possibile cambiare area usando l'interfaccia della riga di comando di Azure o il portale di [Azure.](how-to-manage-database-account.md#manual-failover) Non vi sono **dati o perdita** di disponibilità prima, durante o dopo il passaggio all'area di scrittura e l'applicazione continua a essere a disponibilità elevata.

- **Account con più aree con un'area di scrittura singola (interruzione dell'area di lettura):Multi-region accounts with a single-write region (read region outage):**
  - durante un'interruzione del servizio in un'area di lettura, questi account mantengono la disponibilità elevata per le letture e le scritture.
  - L'area interessata viene disconnessa automaticamente e verrà contrassegnata come non in linea. Gli [SDK di Azure Cosmos DB](sql-api-sdk-dotnet.md) reindirizzeranno le chiamate di lettura alla successiva area disponibile nell'elenco delle aree preferite.
  - Se nessuna delle aree presenti nell'elenco è disponibile, viene eseguito il fallback automatico delle chiamate all'area di scrittura corrente.
  - Non sono necessarie modifiche nel codice dell'applicazione per gestire le interruzioni di lettura a livello di area. Quando alla fine l'area interessata è in di nuovo online, l'area di lettura interessata in precedenza viene sincronizzata automaticamente con l'area di scrittura corrente ed è disponibile anche in questo caso per servire le richieste di lettura.
  - Le letture successive vengono reindirizzate all'area ripristinata senza richiedere alcuna modifica del codice dell'applicazione. Durante il failover e il rejoin di un'area precedentemente non riuscita, la coerenza di lettura garantisce che Cosmos DB continui a essere rispettata.

- Anche in un evento raro e sfortunato quando l'area di Azure è permanentemente irrecuperabile, non si verifica alcuna perdita di dati se l'account Cosmos in più aree è configurato con coerenza *forte.* In caso di area di scrittura permanentemente irrecuperabile, un account Cosmos in più aree configurato con coerenza di stantio delimitato, la finestra di potenziale perdita di dati è limitata alla finestra di stantio (*K* o *T*) in cui k -100.000 viene aggiornato e t.5 minuti. Per la sessione, il prefisso coerente e i livelli di coerenza finale, la finestra di potenziale perdita di dati è limitata a un massimo di 15 minuti. Per altre informazioni sugli obiettivi RTO e RPO per Azure Cosmos DB, vedere Livelli di [coerenza e durata dei datiFor](consistency-levels-tradeoffs.md#rto) more information on RTO and RPO targets for Azure Cosmos DB, see Consistency levels and data durability

## <a name="availability-zone-support"></a>Supporto per la zona di disponibilitàAvailability zone support

Oltre alla resilienza tra aree, è ora possibile abilitare la **ridondanza** delle aree quando si seleziona un'area da associare al database Cosmos di Azure.In addition to cross region resiliency, you can now enable zone redundancy when selecting a region to associate with your Azure Cosmos database.

Con il supporto della zona di disponibilità, Azure Cosmos DB garantirà che le repliche vengano posizionate in più zone all'interno di una determinata area per garantire disponibilità elevata e resilienza durante gli errori di zona. Non sono state apportate modifiche alla latenza e ad altri contratti di servizio in questa configurazione. In caso di errore di una singola zona, la ridondanza della zona offre la massima durabilità dei dati con RPO 0 e disponibilità con RTO.

La ridondanza di zona è una *funzionalità supplementare* per la funzionalità di [replica multimaster.](how-to-multi-master.md) La ridondanza di zona da sola non può essere invocata per ottenere la resilienza regionale. Ad esempio, in caso di interruzioni regionali o di accesso a bassa latenza tra le aree, si consiglia di disporre di più aree di scrittura oltre alla ridondanza delle zone.

Quando si configurano scritture in più aree per l'account Cosmos di Azure, è possibile scegliere la ridondanza delle zone senza costi aggiuntivi. In caso contrario, vedere la nota seguente relativa ai prezzi per il supporto della ridondanza di zona. È possibile abilitare la ridondanza di zona in un'area esistente dell'account Cosmos di Azure rimuovendo l'area e aggiungendola di nuovo con la ridondanza della zona abilitata.

Questa funzionalità è disponibile nelle aree di Azure seguenti:This feature is available in following Azure regions:

- Regno Unito meridionale

- Asia sud-orientale

- Stati Uniti orientali

- Stati Uniti orientali 2

- Stati Uniti centrali

- Europa occidentale

- Stati Uniti occidentali 2

> [!NOTE]
> L'abilitazione delle zone di disponibilità per un account Cosmos di Azure a singola area comporterà addebiti equivalenti all'aggiunta di un'area aggiuntiva all'account. Per informazioni dettagliate sui prezzi, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/) e il costo in più aree negli articoli di Azure [Cosmos DB.](optimize-cost-regions.md)

Nella tabella seguente sono riepilogate le funzionalità di disponibilità elevata delle varie configurazioni di account:

|KPI  |Area singola senza zone di disponibilità (non in a)  |Area singola con zone di disponibilità (A)  |Scritture in più aree geografiche con zone di disponibilità (A, 2 aree) - Impostazione più consigliata |
|---------|---------|---------|---------|
|Scrivi sLA disponibilità | 99,99% | 99,99% | 99,999% |
|Leggi il sLA di disponibilitàRead availability SLA  | 99,99% | 99,99% | 99,999% |
|Price | Tasso di fatturazione per singola area geografica | Tariffa di fatturazione della zona di disponibilità a area singola | Tasso di fatturazione in più aree geografiche |
|Errori di zona : perdita di dati | Perdita di dati | Senza perdita di dati | Senza perdita di dati |
|Errori di zona – disponibilità | Perdita di disponibilità | Nessuna perdita di disponibilità | Nessuna perdita di disponibilità |
|Latenza di lettura | Regione trasversale | Regione trasversale | Basso |
|Latenza di scrittura | Regione trasversale | Regione trasversale | Basso |
|Interruzione regionale – perdita di dati | Perdita di dati |  Perdita di dati | Perdita di dati <br/><br/> Quando si utilizza la coerenza di stantio delimitata con multi master e più di un'area, la perdita di dati è limitata alla stantia delimitata configurata nell'account <br /><br />È possibile evitare la perdita di dati durante un'interruzione a livello di area configurando una coerenza elevata con più aree. Questa opzione include compromessi che influiscono sulla disponibilità e sulle prestazioni. Può essere configurato solo per gli account configurati per le scritture a area singola. |
|Interruzione regionale – disponibilità | Perdita di disponibilità | Perdita di disponibilità | Nessuna perdita di disponibilità |
|Velocità effettiva | Velocità effettiva con provisioning di X RU/s | Velocità effettiva con provisioning di X RU/s | Velocità effettiva con provisioning di 2X RU/s <br/><br/> Questa modalità di configurazione richiede il doppio della velocità effettiva rispetto a una singola area con zone di disponibilità perché sono presenti due aree. |

> [!NOTE]
> Per abilitare il supporto della zona di disponibilità per un account Cosmos di Azure in più aree, l'account deve avere le scritture multimaster abilitate.

È possibile abilitare la ridondanza delle aree quando si aggiunge un'area ad account di Azure Cosmos nuovi o esistenti. Per abilitare la ridondanza delle zone `isZoneRedundant` nell'account Cosmos di Azure, è necessario impostare `true` il flag su per una posizione specifica. È possibile impostare questo flag all'interno della proprietà locations. Ad esempio, il frammento di PowerShell seguente abilita la ridondanza di zona per l'area "Southeast Asia":

```powershell
$locations = @(
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" },
    @{ "locationName"="East US"; "failoverPriority"=1 }
)
```

Il comando seguente mostra come abilitare la ridondanza delle zone per le aree "EastUS" e "WestUS2":

```azurecli-interactive
az cosmosdb create \
  --name mycosmosdbaccount \
  --resource-group myResourceGroup \
  --kind GlobalDocumentDB \
  --default-consistency-level Session \
  --locations regionName=EastUS failoverPriority=0 isZoneRedundant=True \
  --locations regionName=WestUS2 failoverPriority=1 isZoneRedundant=True
```

È possibile abilitare le zone di disponibilità usando il portale di Azure durante la creazione di un account Cosmos di Azure.You can enable Availability zones by using Azure portal when creating an Azure Cosmos account. Quando si crea un account, assicurarsi di abilitare la **ridondanza geografica**, **Scritture in più aree**e scegliere un'area in cui sono supportate le zone di disponibilità:

![Abilitare le zone di disponibilità tramite il portale di AzureEnable Availability Zones using Azure portal](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>Compilazione di applicazioni a disponibilità elevata

- Per garantire una disponibilità elevata di scrittura e lettura, configurare il proprio account Cosmos fino a occupare almeno due aree con aree a scrittura multipla. Questa configurazione fornirà la massima disponibilità, la latenza più bassa e la migliore scalabilità per entrambe le letture e scritture supportate dai contratti di servizio. Per altre informazioni, vedere come [configurare l'account Cosmos con più aree di scrittura](tutorial-global-distribution-sql-api.md).

- Per gli account Cosmos in più aree configurati con un'area a scrittura singola, abilitare il [failover automatico tramite l'interfaccia della riga di comando di Azure o il portale](how-to-manage-database-account.md#automatic-failover)di Azure. Dopo aver abilitato il failover automatico, quando si verifica un'emergenza a livello di aera Cosmos DB esegue automaticamente il failover dell'account.  

- Anche se l'account Cosmos è a disponibilità elevata, l'applicazione potrebbe non essere correttamente progettata per garantire disponibilità elevata. Per testare la disponibilità elevata end-to-end dell'applicazione, come parte dei test dell'applicazione o delle esercitazioni di ripristino di emergenza, disabilitare temporaneamente il failover automatico per l'account, richiamare il [failover manuale tramite l'interfaccia della riga di comando di Azure o il portale](how-to-manage-database-account.md#manual-failover)di Azure, quindi monitorare il failover dell'applicazione. Al termine, è possibile eseguire il failback nell'area primaria e ripristinare il failover automatico per l'account.

- All'interno di un ambiente di database distribuito a livello globale, esiste una relazione diretta tra il livello di coerenza e la durata dei dati in presenza di un'interruzione a livello di area. Quando si sviluppa il piano di continuità aziendale, è necessario conoscere il tempo massimo accettabile prima che l'applicazione venga ripristinata completamente dopo un evento di arresto improvviso. Il tempo necessario per il ripristino completo di un'applicazione è noto come obiettivo del tempo di ripristino (RTO). È anche necessario conoscere la perdita massima di aggiornamenti di dati recenti che l'applicazione è in grado di tollerare durante il ripristino dopo un evento di arresto improvviso. Il periodo di tempo degli aggiornamenti che è possibile perdere è noto come obiettivo del punto di ripristino (RPO). Per l'obiettivo del punto di ripristino (RPO) e l'obiettivo del tempo di ripristino (RTO) per Azure Cosmos DB, vedere [Livelli di coerenza e durabilità dei dati](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Passaggi successivi

Successivamente si possono leggere gli articoli seguenti:

- [Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza](consistency-levels-tradeoffs.md)
- [Ridimensionamento a livello globale della velocità effettiva sottoposta a provisioning](scaling-throughput.md)
- [Distribuzione globale - Informazioni sul funzionamento](global-dist-under-the-hood.md)
- [Livelli di coerenza in Azure Cosmos DB](consistency-levels.md)
- [Come configurare il tuo account Cosmos con più regioni di scrittura](how-to-multi-master.md)
