---
title: Panoramica dell'istanza gestita SQLSQL managed instance overview
description: Questo articolo descrive l'istanza gestita del database SQL di Azure.This article describes Azure SQL Database managed instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
ms.date: 01/21/2020
ms.openlocfilehash: b9fdd1b25e53e1cdc8aa76564304a61adaa8d804
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268782"
---
# <a name="what-is-azure-sql-database-managed-instance"></a>Che cos'è l'istanza gestita del database SQL di Azure?

L'istanza gestita è una nuova opzione di distribuzione del database SQL di Azure che offre quasi il 100% di compatibilità con il più recente motore di database SQL Server in locale (Enterprise Edition), fornendo un'implementazione della [rete virtuale](../virtual-network/virtual-networks-overview.md) nativa che risolve problemi di sicurezza comuni e un [modello aziendale](https://azure.microsoft.com/pricing/details/sql-database/) favorevole per i clienti di SQL Server in locale. Il modello di distribuzione dell'istanza gestita consente ai clienti di SQL Server esistenti di spostare nel cloud le proprie applicazioni locali con modifiche minime nelle applicazioni e nel database. Allo stesso tempo, l'opzione di distribuzione dell'istanza gestita consente di mantenere tutte le funzionalità PaaS (applicazione automatica di patch e aggiornamenti di versione, [backup automatici](sql-database-automated-backups.md), [disponibilità elevata](sql-database-high-availability.md)), che riducono drasticamente il carico di gestione e il costo totale di proprietà.

> [!IMPORTANT]
> Per un elenco delle aree in cui l'opzione di distribuzione dell'istanza gestita è attualmente disponibile, vedere [Aree supportate](sql-database-managed-instance-resource-limits.md#supported-regions).

Nel diagramma seguente vengono descritte le funzionalità principali delle istanze gestite:

![funzionalità principali](./media/sql-database-managed-instance/key-features.png)

Il modello di distribuzione dell'istanza gestita è progettato per i clienti che desiderano eseguire la migrazione di un numero maggiore di app da un ambiente locale o IaaS, creato personalmente o un ambiente fornito da un ISV per la gestione completa dell'ambiente cloud PaaS, con il minor numero di operazioni di migrazione possibile. Utilizzando il servizio di migrazione dei [dati (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) completamente automatizzato in Azure, i clienti possono sollevare e spostare SQL Server locale in un'istanza gestita che offre compatibilità con SQL Server locale e l'isolamento completo delle istanze dei clienti con il supporto della rete virtuale nativa.  Con Software Assurance, è possibile scambiare le licenze esistenti con tariffe scontate in un'istanza gestita usando il [vantaggio Azure Hybrid per SQL Server.](https://azure.microsoft.com/pricing/hybrid-benefit/)  Un'istanza gestita è la destinazione di migrazione migliore nel cloud per le istanze di SQL Server che richiedono un livello di sicurezza elevato e una superficie di programmazione avanzata.

L'opzione di distribuzione dell'istanza gestita mira a offrire una compatibilità della superficie di attacco prossima al 100% con l'ultima versione di SQL Server in locale tramite un piano di rilascio a fasi.

Per la scelta tra le opzioni di distribuzione di database SQL di Azure, database singolo, database in pool, istanza gestita e SQL Server ospitati in macchine virtuali, vedere [come scegliere la versione corretta di SQL Server in Azure](sql-database-paas-vs-sql-server-iaas.md).

## <a name="key-features-and-capabilities"></a>Funzionalità e le caratteristiche chiave

L'istanza gestita combina le migliori funzionalità disponibili sia nel database SQL di Azure che nel motore di database di SQL Server.

> [!IMPORTANT]
> Un'istanza gestita viene eseguita con tutte le funzionalità della versione più recente di SQL Server, incluse le operazioni online, le correzioni automatiche del piano e altri miglioramenti delle prestazioni Enterprise. Per un confronto tra le funzionalità disponibili, vedere [Confronto tra le funzionalità: database SQL di Azure e SQL Server](sql-database-features.md).

| **Vantaggi di PaaS** | **Continuità aziendale** |
| --- | --- |
|Acquisto e gestione di hardware non necessari <br>Nessun sovraccarico per la gestione dell'infrastruttura sottostante <br>Provisioning rapido e scalabilità del servizio <br>Applicazione automatica di patch e aggiornamento della versione <br>Integrazione con altri servizi dati PaaS |Contratto di servizio relativo al tempo di attività 99,99%  <br>[Disponibilità elevata](sql-database-high-availability.md) integrata <br>Dati protetti con [backup automatici](sql-database-automated-backups.md) <br>Periodo di conservazione dei backup configurabile dal cliente <br>[Backup](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) avviati dall'utente <br>Funzionalità di [ripristino temporizzato di un database](sql-database-recovery-using-backups.md#point-in-time-restore) |
|**Sicurezza e conformità** | **dispositivi mobili**|
|Ambiente isolato ([integrazione della rete virtuale](sql-database-managed-instance-connectivity-architecture.md), servizio a tenant singolo, calcolo e archiviazione dedicati) <br>[Crittografia dei dati trasparente (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Autenticazione di Azure AD](sql-database-aad-authentication.md), supporto di Single Sign-On <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Entità server di Azure AD (account di accesso)Azure AD server principals (logins)</a>  <br>Soddisfa gli standard di conformità del database SQL di Azure <br>[Controllo SQL](sql-database-managed-instance-auditing.md) <br>[Advanced Threat Protection](sql-database-managed-instance-threat-detection.md) |API di Azure Resource Manager per l'automazione del provisioning e della scalabilità del servizio <br>Funzionalità del portale di Azure per provisioning e scalabilità del servizio manuali <br>Servizio di migrazione dei dati

> [!IMPORTANT]
> Il database SQL di Azure (tutte le opzioni di distribuzione) è stato certificato in base a una serie di standard di conformità. Per altre informazioni, vedere il [Centro protezione di Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) in cui è possibile trovare l'elenco più aggiornato delle certificazioni di conformità del database SQL.

Nella tabella seguente sono elencate le principali funzionalità delle istanze gestite:

|Funzionalità | Descrizione|
|---|---|
| Versione/build di SQL Server | Motore di database di SQL Server (ultima versione stabile) |
| Backup automatici gestiti | Sì |
| Monitoraggio predefinito e metriche dell'istanza e del database | Sì |
| Applicazione automatica di patch software | Sì |
| Funzionalità più recenti del motore di database | Sì |
| Numero di file di dati (RIGHE) per il database | Multipli |
| Numero di file di log (LOG) per il database | 1 |
| Rete virtuale: distribuzione di Azure Resource Manager | Sì |
| Rete virtuale: modello di distribuzione classica | No |
| Supporto del portale | Sì|
| Integration Services (SSIS) incorporato | No - SSIS fa parte di [Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| Analysis Services (SSAS) incorporato | No - SSAS fa parte di una [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) distinta |
| Reporting Services (SSRS) incorporato | No - usare Power BI o SSRS IaaS |
|||

## <a name="vcore-based-purchasing-model"></a>Modello di acquisto basato su vCore

Il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md) per le istanze gestite offre flessibilità, controllo e trasparenza, consentendo di convertire con facilità i requisiti dei carichi di lavoro locali per il cloud. Questo modello consente di cambiare le risorse di calcolo, memoria e archiviazione in base ai requisiti dei carichi di lavoro. Il modello vCore è inoltre idoneo per un risparmio massimo del 55% con il [vantaggio Azure Hybrid per SQL Server.](https://azure.microsoft.com/pricing/hybrid-benefit/)

Nel modello vCore è possibile scegliere tra diverse generazioni di hardware.

- **Gen4** Le CPU logiche sono basate su processori Intel E5-2673 v3 (Haswell) da 2,4 GHz, SSD collegati, core fisici, 7 GB di RAM per core e dimensioni di calcolo comprese tra 8 e 24 vCore.
- **Gen5** Le CPU logiche sono basate su processori Intel E5-2673 v4 (Broadwell) 2.3-GHz e Intel SP-8160 (Skylake), veloci SSD NVMe, core logici con hyperthreading e dimensioni di calcolo comprese tra 4 e 80 core.

Ulteriori informazioni sulla differenza tra le generazioni di hardware nei [limiti delle risorse dell'istanza gestita](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).

## <a name="managed-instance-service-tiers"></a>Livelli di servizio dell'istanza gestita

L'istanza gestita è disponibile in due livelli di servizio:

- **Scopo generale**: Progettato per applicazioni con prestazioni tipiche e requisiti di latenza di I/O.General purpose : Designed for applications with typical performance and IO latency requirements.
- **Business critical:** progettato per applicazioni con bassi requisiti di latenza di I/O e impatto minimo delle operazioni di manutenzione sottostanti sul carico di lavoro.

Entrambi i livelli di servizio garantiscono una disponibilità del 99,99% e consentono di selezionare le dimensioni di archiviazione e capacità di calcolo in modo indipendente. Per altre informazioni sull'architettura di disponibilità elevata del database SQL di Azure, vedere [High Availability and Azure SQL Database](sql-database-high-availability.md) (Disponibilità elevata e database SQL di Azure).

### <a name="general-purpose-service-tier"></a>Livello di servizio Utilizzo generico

L'elenco seguente descrive le caratteristiche principali del livello di servizio Utilizzo generico:

- Progettazione della maggior parte delle applicazioni aziendali con requisiti di prestazioni tipici
- Archiviazione BLOB di Azure ad alte prestazioni (8 TB)
- [Disponibilità elevata](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) basata sull'affidabilità di Archiviazione BLOB di Azure e su [Azure Service Fabric](../service-fabric/service-fabric-overview.md)

Per altre informazioni, vedere [storage layer in general purpose tier](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) (Livello Archiviazione nel livello di servizio Utilizzo generico) e [storage performance best practices and considerations for managed instances (general purpose)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/) (Procedure consigliate e considerazioni sulle prestazioni di archiviazione per le istanze gestite (Utilizzo generico)).

Ulteriori informazioni sulla differenza tra i livelli di servizio nei [limiti delle risorse dell'istanza gestita](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Livello di servizio business critical

Il livello di servizio business critical è progettato per applicazioni con requisiti I/O elevati. Offre la massima resilienza agli errori tramite diverse repliche isolate.

L'elenco seguente descrive le caratteristiche principali del livello di servizio per business critical:

- Progettato per applicazioni aziendali con le prestazioni e i requisiti di disponibilità più elevati
- Viene fornito con l'archiviazione SSD locale estremamente rapida (fino a 1 TB nella quarta generazione e fino a 4 TB nella quinta generazione)
- Disponibilità elevata integrata basata su Gruppi di [disponibilità AlwaysOn AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) e Azure Service Fabric.Built-in [high availability](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) based on Always On Availability Groups and Azure [Service Fabric.](../service-fabric/service-fabric-overview.md)
- Replica di [database](sql-database-read-scale-out.md) aggiuntiva di sola lettura incorporata che può essere usata per la creazione di report e altri carichi di lavoro di sola lettura
- [OLTP in memoria](sql-database-in-memory.md) che può essere usato per il carico di lavoro con requisiti di prestazioni elevate  

Ulteriori informazioni sulla differenza tra i livelli di servizio nei [limiti delle risorse dell'istanza gestita](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).


## <a name="managed-instance-management-operations"></a>Operazioni di gestione delle istanze gestite

Il database SQL di Azure consente di eseguire operazioni di gestione per distribuire automaticamente nuove istanze gestite, aggiornare le proprietà di un'istanza ed eliminare le istanze quando non sono più necessarie. In questa sezione vengono fornite informazioni sulle operazioni di gestione e sulle relative durate tipiche.

Per supportare [le distribuzioni all'interno](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) di reti virtuali di Azure e fornire isolamento e sicurezza per i clienti, l'istanza gestita si basa su [cluster virtuali,](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture)che rappresentano un set dedicato di macchine virtuali isolate distribuite all'interno della subnet della rete virtuale del cliente. Essenzialmente, ogni distribuzione di istanze gestite in una subnet vuota comporta la creazione di un nuovo cluster virtuale.

Le operazioni successive sulle istanze gestite distribuite potrebbero anche avere effetti sul cluster virtuale sottostante. Ciò influisce sulla durata delle operazioni di gestione, poiché la distribuzione di macchine virtuali aggiuntive comporta un sovraccarico che deve essere considerato quando si pianificano nuove distribuzioni o aggiornamenti alle istanze gestite esistenti.

Tutte le operazioni di gestione possono essere suddivise per categoria, come indicato di seguito:

- Distribuzione dell'istanza (creazione di una nuova istanza). 
- Aggiornamento dell'istanza (modifica delle proprietà dell'istanza, ad esempio vCore o archiviazione riservata.
- Eliminazione dell'istanza.

In genere, le operazioni sui cluster virtuali richiedono più tempo. La durata delle operazioni nei cluster virtuali varia: di seguito sono riportati i valori che in genere ci si può prevedere, in base ai dati di telemetria del servizio esistenti:

- Creazione di cluster virtuali. Si tratta di un passaggio sincrono nelle operazioni di gestione delle istanze. **Il 90% delle operazioni termina in 4 ore.**
- Ridimensionamento del cluster virtuale (espansione o riduzione). L'espansione è un passaggio sincrono, mentre la riduzione viene eseguita in modo asincrono (senza alcun impatto sulla durata delle operazioni di gestione delle istanze). **Il 90% delle espansioni dei cluster termina in meno di 2,5 ore.**
- Eliminazione del cluster virtuale. L'eliminazione è un passaggio asincrono, ma può anche essere [avviata manualmente](sql-database-managed-instance-delete-virtual-cluster.md) in un cluster virtuale vuoto, nel qual caso viene eseguita in modo sincrono. **Il 90% delle eliminazioni del cluster virtuale termina in 1,5 ore.**

Inoltre, la gestione delle istanze può includere anche una delle operazioni sui database ospitati, che comporta durate più lunghe:

- Collegamento di file di database da Archiviazione di Azure.Attaching database files from Azure Storage. Si tratta di un passaggio sincrono, ad esempio il calcolo (vCore) o la scalabilità verticale o verso il basso dell'archiviazione nel livello di servizio Scopo generale. **Il 90% di queste operazioni termina in 5 minuti.**
- Eseguire il seeding del gruppo di disponibilità AlwaysOnAlways On availability group seeding. Si tratta di un passaggio sincrono, ad esempio il calcolo (vCore) o la scalabilità dell'archiviazione nel livello del servizio Business Critical, nonché la modifica del livello di servizio da Uso generale a Business Critical (o viceversa). La durata di questa operazione è proporzionale alle dimensioni totali del database e all'attività corrente del database (numero di transazioni attive). L'attività del database durante l'aggiornamento di un'istanza può introdurre una varianza significativa della durata totale. **90% di queste operazioni eseguite a 220 GB / ora o superiore**.

Nella tabella seguente sono riepilogate le operazioni e le durate complessive tipiche:

|Category  |Operazione  |Segmento a lunga durata  |Durata stimata  |
|---------|---------|---------|---------|
|**Distribuzione** |Prima istanza in una subnet vuota|Creazione di cluster virtuali|Il 90% delle operazioni termina in 4 ore|
|Distribuzione |Prima istanza di un'altra generazione di hardware in una subnet non vuota (ad esempio, prima istanza di generazione 5 in una subnet con istanze di generazione 4)First instance of another hardware generation in a non-empty subnet (for example, first Gen 5 instance in a subnet with Gen 4 instances)|Creazione di cluster virtuali|Il 90% delle operazioni termina in 4 ore|
|Distribuzione |Creazione della prima istanza di 4 vCore, in una subnet vuota o non vuota|Creazione di cluster virtuali|Il 90% delle operazioni termina in 4 ore|
|Distribuzione |Creazione successiva dell'istanza all'interno della subnet non vuota (2a, 3a e così via)|Ridimensionamento del cluster virtualeVirtual cluster resizing|90% delle operazioni terminano in 2,5 ore|
|**Aggiornamento** |Modifica della proprietà dell'istanza (password amministratore, account di accesso AAD, flag di benefit Azure ibrido)Instance property change (admin password, AAD login, Azure Hybrid Benefit flag)|N/D|Fino a 1 minuto|
|Aggiornamento |Scalabilità verticale/denella dell'archiviazione dell'istanza (livello di servizio Scopo generale)Instance storage scaling up/down (General Purpose service tier)|Collegamento di file di database|90% delle operazioni terminano in 5 minuti|
|Aggiornamento |Scalabilità verticale/verso l'alto o verso il basso dell'archiviazione dell'istanza (livello di servizio Business Critical)Instance storage scaling up/down (Business Critical service tier)|- Ridimensionamento del cluster virtuale<br>- Eseguire il seeding del gruppo di disponibilità Always On-Always On availability group seeding|90% delle operazioni terminano in 2,5 ore : tempo per eseguire il seeding di tutti i database (220 GB / ora)|
|Aggiornamento |Scalabilità verticale e verticale del calcolo dell'istanza (vCores)|- Ridimensionamento del cluster virtuale<br>- Collegamento di file di database|90% delle operazioni terminano in 2,5 ore|
|Aggiornamento |Scalabilità verticale e verticale del calcolo dell'istanza (vCores)|- Ridimensionamento del cluster virtuale<br>- Eseguire il seeding del gruppo di disponibilità Always On-Always On availability group seeding|90% delle operazioni terminano in 2,5 ore : tempo per eseguire il seeding di tutti i database (220 GB / ora)|
|Aggiornamento |Scalabilità ridotta dell'istanza a 4 vCore (scopo generale)Instance scale down to 4 vCores (General Purpose)|- Ridimensionamento del cluster virtuale (se eseguito per la prima volta, potrebbe essere necessario la creazione di cluster virtuali)<br>- Collegamento di file di database|90% delle operazioni terminano in 4 h 5 min|
|Aggiornamento |Scalabilità ridotta dell'istanza a 4 vCore (Business Critical)|- Ridimensionamento del cluster virtuale (se eseguito per la prima volta, potrebbe essere necessario la creazione di cluster virtuali)<br>- Eseguire il seeding del gruppo di disponibilità Always On-Always On availability group seeding|90% delle operazioni terminano in 4 ore : tempo per eseguire il seeding di tutti i database (220 GB / ora)|
|Aggiornamento |Modifica del livello di servizio dell'istanza (scopo generale a Business Critical e viceversa)Instance service tier change (General Purpose to Business Critical and versas)|- Ridimensionamento del cluster virtuale<br>- Eseguire il seeding del gruppo di disponibilità Always On-Always On availability group seeding|90% delle operazioni terminano in 2,5 ore : tempo per eseguire il seeding di tutti i database (220 GB / ora)|
|**Eliminazione**|Eliminazione di un'istanza|Backup della coda del log per tutti i database|90% di operazioni terminano fino a 1 minuto.<br>Nota: se l'ultima istanza nella subnet viene eliminata, questa operazione pianifica l'eliminazione del cluster virtuale dopo 12 ore.|
|Eliminazione|Eliminazione del cluster virtuale (come operazione avviata dall'utente)Virtual cluster deletion (as user-initiated operation)|Eliminazione del cluster virtualeVirtual cluster deletion|90% delle operazioni terminano fino a 1,5 ore|

\*Il cluster virtuale viene creato per ogni generazione di hardware.

\*\*L'opzione di distribuzione 4 vCores è stata rilasciata nel giugno 2019 e richiede una nuova versione del cluster virtuale. Se nella subnet di destinazione sono presenti istanze che sono state tutte create prima del 12 giugno, un nuovo cluster virtuale verrà distribuito automaticamente per ospitare 4 istanze vCore.

\*\*\*12 ore è la configurazione corrente, ma che potrebbe cambiare in futuro, quindi non prendere una dura dipendenza da esso. Se è necessario eliminare un cluster virtuale in precedenza (per rilasciare la subnet, ad esempio), vedere [Eliminare una subnet dopo l'eliminazione di un'istanza gestita del database SQL](sql-database-managed-instance-delete-virtual-cluster.md)di Azure.If you need to delete a virtual cluster earlier (to release the subnet for example), see Delete a subnet after deleting an Azure SQL Database managed instance .

### <a name="instance-availability-during-management"></a>Disponibilità dell'istanza durante la gestione

Le istanze gestite non sono disponibili per le applicazioni client durante le operazioni di distribuzione ed eliminazione.

Le istanze gestite sono disponibili durante le operazioni di aggiornamento, ma il timeout di inattività è causato dal failover che si verifica alla fine degli aggiornamenti che in genere dura fino a 10 secondi. L'eccezione è l'aggiornamento dello spazio di archiviazione riservato nel livello di servizio Generic Purpose che non comporta failover né influisce sulla disponibilità dell'istanza.

> [!IMPORTANT]
> La durata di un failover può variare in modo significativo in caso di transazioni a esecuzione prolungata che si verificano nei database a causa di un tempo di [recupero prolungato.](sql-database-accelerated-database-recovery.md#the-current-database-recovery-process) Non è quindi consigliabile ridimensionare l'archiviazione o il calcolo del database SQL di Azure o modificare contemporaneamente il livello di servizio con le transazioni a esecuzione prolungata (importazione di dati, processi di elaborazione dati, ricompilazione dell'indice e così via). Il failover del database che verrà eseguito al termine dell'operazione annullerà le transazioni in corso e comporterà tempi di recupero prolungati.

> [!TIP]
> L'aggiornamento dello spazio di archiviazione riservato nel livello di servizio Generic Purpose non comporta il failover né influisce sulla disponibilità dell'istanza.

[Il ripristino accelerato](sql-database-accelerated-database-recovery.md) del database non è attualmente disponibile per le istanze gestite del database SQL di Azure.Accelerated database recovery is not currently available for Azure SQL Database managed instances. Una volta abilitata, questa funzionalità ridurrà significativamente la variabilità del tempo di failover, anche in caso di transazioni a esecuzione prolungata.

### <a name="canceling-management-operations"></a>Annullamento delle operazioni di gestione

Nella tabella seguente viene riepilogata la possibilità di annullare specifiche operazioni di gestione e le durate complessive tipiche:

Category  |Operazione  |Annullabile  |Durata dell'annullamento stimata  |
|---------|---------|---------|---------|
|Distribuzione |Creazione dell'istanza |No |  |
|Aggiornamento |Scalabilità verticale/riduzione dell'archiviazione dell'istanza (scopo generale)Instance storage scaling up/down (General Purpose) |No |  |
|Aggiornamento |Scalabilità verticale/riduzione dell'archiviazione dell'istanza (Business Critical)Instance storage scaling up/down (Business Critical) |Sì |90% delle operazioni terminano in 5 minuti |
|Aggiornamento |Scalabilità verticale e verticale del calcolo dell'istanza (vCores) |Sì |90% delle operazioni terminano in 5 minuti |
|Aggiornamento |Scalabilità verticale e verticale del calcolo dell'istanza (vCores) |Sì |90% delle operazioni terminano in 5 minuti |
|Aggiornamento |Modifica del livello di servizio dell'istanza (scopo generale a Business Critical e viceversa)Instance service tier change (General Purpose to Business Critical and versas) |Sì |90% delle operazioni terminano in 5 minuti |
|Delete |Eliminazione di un'istanza |No |  |
|Delete |Eliminazione del cluster virtuale (come operazione avviata dall'utente)Virtual cluster deletion (as user-initiated operation) |No |  |

Per annullare l'operazione di gestione, passare al pannello Panoramica e fare clic sulla casella di notifica dell'operazione in corso. Sul lato destro, apparirà una schermata con il funzionamento in corso e ci sarà un pulsante per annullare l'operazione. Dopo il primo clic, ti verrà chiesto di fare di nuovo clic e confermare che desideri annullare l'operazione.

[![](./media/sql-database-managed-instance/canceling-operation.png)](./media/sql-database-managed-instance/canceling-operation.png#lightbox)

Dopo che la richiesta di annullamento è stata inviata ed elaborata, riceverai una notifica se l'invio di annullamento ha avuto esito positivo o meno. 

In caso di annullamento riuscito, l'operazione di gestione verrà annullata in un paio di minuti con conseguente errore.

![annullamento del risultato dell'operazione](./media/sql-database-managed-instance/canceling-operation-result.png)

Se la richiesta di annullamento ha esito negativo o il pulsante Annulla non è attivo, significa che l'operazione di gestione è entrata nello stato non annullabile e che verrà completata in un paio di minuti. L'operazione di gestione continuerà l'esecuzione fino al completamento.

> [!IMPORTANT]
> L'annullamento dell'operazione è attualmente supportato solo nel portale.

## <a name="advanced-security-and-compliance"></a>Sicurezza e conformità avanzate

L'opzione di distribuzione dell'istanza gestita combina le funzionalità avanzate di sicurezza fornite dal cloud di Azure e il motore di database di SQL Server.

### <a name="managed-instance-security-isolation"></a>Isolamento di sicurezza dell'istanza gestita

Un'istanza gestita offre isolamento di sicurezza aggiuntivo dagli altri tenant nel cloud di Azure. L'isolamento di sicurezza include:

- [Implementazione](sql-database-managed-instance-connectivity-architecture.md) della rete virtuale nativa e connettività all'ambiente locale tramite Azure Express Route o gateway VPN.
- In una distribuzione predefinita, l'endpoint SQL viene esposto solo tramite un indirizzo IP privato, consentendo la connettività sicura da Azure privato o da reti ibride.
- Tenant singolo con infrastruttura sottostante dedicata (calcolo, archiviazione).

Il diagramma seguente illustra diverse opzioni di connettività per le applicazioni:

![disponibilità elevata](./media/sql-database-managed-instance/application-deployment-topologies.png)  

Per altre informazioni dettagliate sull'integrazione della rete virtuale e sull'applicazione di criteri di rete a livello di subnet, vedere [Architettura della rete virtuale per le istanze gestite](sql-database-managed-instance-connectivity-architecture.md) e [Connettere l'applicazione a un'istanza gestita](sql-database-managed-instance-connect-app.md).

> [!IMPORTANT]
> Inserire più istanze gestite nella stessa subnet, ogniqualvolta tale operazione è consentita dai requisiti di sicurezza, in quanto comporterà vantaggi aggiuntivi. La collocazione di istanze nella stessa subnet semplificherà notevolmente la manutenzione dell'infrastruttura di rete e ridurrà il tempo di provisioning dell'istanza, poiché una durata prolungata di provisioning è associata al costo della distribuzione della prima istanza gestita in una subnet.

### <a name="azure-sql-database-security-features"></a>Funzionalità di sicurezza del database SQL di Azure

Database SQL di Azure fornisce un set di funzionalità di sicurezza avanzato che può essere usato per proteggere i dati.

- Il controllo delle [istanze gestite](sql-database-managed-instance-auditing.md) tiene traccia degli eventi del database e li scrive in un file di log di controllo inserito nell'account di archiviazione di Azure.Managed instance auditing tracks database events and writes them to an audit log file placed in your Azure storage account. Il controllo consente di agevolare la conformità alle normative, comprendere le attività del database e ottenere informazioni su eventuali discrepanze e anomalie che potrebbero indicare problemi aziendali o sospette violazioni della sicurezza.
- Crittografia dei dati in transito: un'istanza gestita protegge i dati fornendo la crittografia per i dati in transito tramite Transport Layer Security. Oltre alla sicurezza a livello di trasporto, l'opzione di distribuzione dell'istanza gestita offre protezione per i dati sensibili in movimento, inattivi e durante l'elaborazione di query con [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted è una tecnologia leader del settore che offre un livello di sicurezza dei dati senza uguali, per la protezione da violazioni che implicano il furto di dati critici. Con Always Encrypted, ad esempio, i numeri delle carte di credito sono sempre archiviati in forma crittografata nel database, anche durante l'elaborazione di query, e la decrittografia è consentita nella posizione di utilizzo da parte di personale o applicazioni autorizzati che devono elaborare tali dati.
- [Advanced Threat Protection](sql-database-managed-instance-threat-detection.md) integra [il controllo](sql-database-managed-instance-auditing.md) fornendo un ulteriore livello di intelligence di sicurezza integrato nel servizio che rileva tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database. L'utente viene avvisato di attività sospette, vulnerabilità potenziali e attacchi SQL injection, nonché di modelli anomali di accesso al database. Gli avvisi di Advanced Threat Protection possono essere visualizzati dal [Centro sicurezza](https://azure.microsoft.com/services/security-center/) di Azure e forniscono dettagli sulle attività sospette e consigliano azioni su come analizzare e ridurre la minaccia.  
- [Il mascheramento dinamico](/sql/relational-databases/security/dynamic-data-masking) dei dati limita l'esposizione dei dati sensibili mascherandoli agli utenti senza privilegi. La maschera dati dinamica impedisce l'accesso non autorizzato ai dati sensibili consentendo di definire la quantità di dati sensibili da rivelare, con un impatto minimo sul livello dell'applicazione. Si tratta di una funzionalità di sicurezza basata su criteri che consente di nascondere i dati sensibili nel set di risultati di una query in campi di database designati, senza modificare i dati nel database.
- La [sicurezza a livello di riga](/sql/relational-databases/security/row-level-security) consente di controllare l'accesso alle righe in una tabella di database in base alle caratteristiche dell'utente che esegue una query, ad esempio l'appartenenza a un gruppo o il contesto di esecuzione. La sicurezza a livello di riga semplifica la progettazione e la codifica della sicurezza nell'applicazione. Consente di implementare limitazioni per l'accesso alle righe di dati, assicurando ad esempio che i collaboratori possano accedere solo alle righe di dati pertinenti per il proprio reparto o limitando l'accesso ai dati ai soli dati di pertinenti.
- La funzione [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) esegue la crittografia dei file di dati delle istanze gestite, noti anche come dati inattivi crittografati. TDE esegue la crittografia e la decrittografia delle operazioni di I/O di file di dati e log in tempo reale. La crittografia usa una chiave di crittografia del database (DEK) che viene archiviata nel record di avvio del database per la disponibilità durante il ripristino. È possibile proteggere tutti i database in un'istanza gestita con la crittografia dei dati trasparente. TDE è la tecnologia di crittografia dei dati inattivi collaudata di SQL Server, richiesta da molti standard di conformità per la protezione in caso di furto di supporti di archiviazione.

La migrazione di un database crittografato in un'istanza gestita è supportata tramite il Servizio Migrazione del database di Azure o il ripristino nativo. Se si prevede di eseguire la migrazione di un database crittografato utilizzando il ripristino nativo, la migrazione del certificato TDE esistente da SQL Server locale o SQL Server in una macchina virtuale in un'istanza gestita è un passaggio obbligatorio. Per altre informazioni sui vari metodi di migrazione, vedere [Migrazione di un'istanza di SQL Server a un'istanza gestita](sql-database-managed-instance-migrate.md).

## <a name="azure-active-directory-integration"></a>Integrazione di Azure Active Directory

L'opzione di distribuzione dell'istanza gestita supporta i tradizionali account di accesso del motore di database di SQL Server e gli account di accesso integrati con Azure Active Directory (AAD). Le entità server (account di accesso) di Azure AD (**anteprima pubblica**) sono una versione cloud di Azure degli account di accesso ai database di Windows usati nell'ambiente locale. Le entità server (account di accesso) di Azure AD consentono di specificare utenti e gruppi dal tenant di Azure Active Directory come vere entità con ambito istanza, in grado di eseguire qualsiasi operazione a livello di istanza, incluse le query tra database all'interno della stessa gestione Istanza.

È stata introdotta una nuova sintassi per creare entità server di Azure AD (account di accesso), **FROM EXTERNAL PROVIDER**. Per altre informazioni sulla sintassi, vedere <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>ed esaminare l'articolo Effettuare il provisioning di un amministratore di Azure Active Directory [per l'istanza gestita.](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integrazione in Azure Active Directory e autenticazione a più fattori

L'opzione di distribuzione dell'istanza gestita consente di gestire a livello centralizzato le identità degli utenti di database e altri servizi Microsoft grazie all'[integrazione in Azure Active Directory](sql-database-aad-authentication.md). Questa funzionalità semplifica la gestione delle autorizzazioni e ottimizza la sicurezza. Azure Active Directory supporta l'[autenticazione a più fattori](sql-database-ssms-mfa-authentication-configure.md) (MFA) per una maggiore sicurezza di dati e applicazioni, supportando allo stesso tempo un processo di accesso singolo.

### <a name="authentication"></a>Authentication

Per autenticazione dell'istanza gestita si intende il modo in cui l'utente dimostra la propria identità durante la connessione al database. Il database SQL supporta due tipi di autenticazione:  

- **Autenticazione SQL**:

  Questo metodo di autenticazione usa nome utente e password.
- **Autenticazione di Azure Active Directory**:

  Questo metodo di autenticazione usa identità gestite da Azure Active Directory ed è supportato per domini gestiti e integrati. [Quando possibile](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode), usare l'autenticazione di Active Directory (sicurezza integrata).

### <a name="authorization"></a>Autorizzazione

Per autorizzazione si intendono le operazioni che l'utente può eseguire in un database SQL di Azure, che sono controllate dalle appartenenze ai ruoli del database e dalle autorizzazioni a livello di oggetto dell'account utente. Un'istanza gestita ha le stesse funzionalità di autorizzazione di SQL Server 2017.

## <a name="database-migration"></a>Migrazione di database

L'opzione di distribuzione dell'istanza gestita è destinata a scenari utente con migrazione di massa di database da implementazioni di database locali o IaaS. L'istanza gestita supporta diverse opzioni di migrazione di database:

### <a name="back-up-and-restore"></a>Backup e ripristino  

L'approccio di migrazione sfrutta i backup di SQL per l'archiviazione BLOB di Azure. I backup archiviati in BLOB del servizio di archiviazione di Azure possono essere ripristinati direttamente in un'istanza gestita usando il [comando T-SQL RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current).

- Per una guida introduttiva che illustra come ripristinare il file di backup del database Wide World Importers - Standard, vedere Ripristinare un file di backup in [un'istanza gestita.](sql-database-managed-instance-get-started-restore.md) Questa guida introduttiva illustra come caricare un file di backup nell'archiviazione BLOB di Azure e proteggerlo usando una chiave di firma di accesso condiviso.
- Per informazioni sul ripristino dall'URL, vedere [Ripristino nativo da URL](sql-database-managed-instance-migrate.md#native-restore-from-url).

> [!IMPORTANT]
> I backup da un'istanza gestita possono essere ripristinati solo in un'altra istanza gestita. Non possono essere ripristinati in SQL Server locale o in database singolo o pool elastico.

### <a name="data-migration-service"></a>Servizio di migrazione dei dati

Il Servizio Migrazione del database di Azure è un servizio completamente gestito progettato per abilitare le migrazioni senza interruzioni da più origini di database alle piattaforme di dati di Azure con tempi di inattività minimi. Questo servizio semplifica le attività necessarie per spostare database di SQL Server e di terze parti nel database SQL di Azure (database singoli, database in pool di pool elastici e database dell'istanza in un'istanza gestita) e SQL Server nella macchina virtuale di Azure. Vedere [come eseguire la migrazione del database locale in un'istanza gestita tramite Servizio Migrazione del database](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Funzionalità di SQL supportate

L'opzione di distribuzione dell'istanza gestita mira a offrire una compatibilità della superficie di attacco prossima al 100% con SQL Server in locale disponibile a fasi fino alla disponibilità generale del servizio. Per un elenco di funzionalità ed elenco di confronto, vedere Confronto tra le funzionalità [del database SQL](sql-database-features.md)e per un elenco delle differenze tra Istanze gestite e SQL Server, vedere [Differenze T-SQL dell'istanza gestita rispetto a SQL Server](sql-database-managed-instance-transact-sql-information.md).

L'opzione di distribuzione dell'istanza gestita supporta la compatibilità con le versioni precedenti per i database di SQL 2008. È supportata la migrazione diretta da server di database di SQL 2005, il livello di compatibilità per i database di SQL 2005 migrati viene aggiornato a SQL 2008.
  
Il diagramma seguente illustra la compatibilità della superficie di attacco in un'istanza gestita:  

![migrazione](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-in-a-managed-instance"></a>Differenze principali tra SQL Server in locale e in un'istanza gestita

L'opzione di distribuzione dell'istanza gestita offre vantaggi derivanti dall'essere sempre aggiornati nel cloud, mentre alcune funzionalità di SQL Server in locale possono essere obsolete, essere state ritirate o sostituite con alternative. Esistono casi specifici in cui gli strumenti devono riconoscere che una particolare funzionalità funziona in modo leggermente diverso o che il servizio è in esecuzione in un ambiente che non controlla completamente:There are specific cases when tools need to recognize that a particular feature works in a slightly different way or that the service is running in an environment you do not fully control:

- La disponibilità elevata è integrata e preconfigurata mediante una tecnologia simile a [Gruppi di disponibilità AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Backup automatici e ripristino temporizzato. Il cliente può avviare backup `copy-only` che non interferiscono con la catena di backup automatica.
- L'istanza gestita non consente di specificare percorsi fisici completi, pertanto tutti gli scenari corrispondenti devono essere supportati in modo diverso: RESTORE DB non supporta WITH MOVE, CREATE DB non consente percorsi fisici, BULK INSERT funziona solo con BLOB di Azure e così via.
- L'istanza gestita supporta [l'autenticazione](sql-database-aad-authentication.md) di Azure AD come alternativa cloud all'autenticazione di Windows.Managed instance supports Azure AD authentication as cloud alternative to Windows authentication.
- L'istanza gestita gestisce automaticamente filegroup XTP e i file per i database contenenti oggetti di OLTP in memoria
- L'istanza gestita supporta SQL Server Integration Services e può ospitare il catalogo SSIS, ovvero SSISDB, che archivia i pacchetti SSIS, che però vengono eseguiti in un'istanza gestita di Azure-SSIS Integration Runtime in Azure Data Factory. Per informazioni, vedere [Creare Azure-SSIS Integration Runtime in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Per confrontare le funzionalità SSIS nel database SQL, vedere [Confrontare un singolo database, un pool elastico e un'istanza gestita](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)di un database SQL di Azure.

### <a name="managed-instance-administration-features"></a>Funzionalità di amministrazione dell'istanza gestita

L'opzione di distribuzione dell'istanza gestita consente all'amministratore di sistema di risparmiare tempo nelle attività amministrative perché vengono automatizzate o semplificate dal servizio database SQL. Ad esempio, [l'installazione e l'applicazione di patch a OS/RDBMS](sql-database-high-availability.md), il ridimensionamento e la configurazione delle [istanze dinamiche,](sql-database-single-database-scale.md) [i backup](sql-database-automated-backups.md), la [replica del database](replication-with-sql-database-managed-instance.md) (inclusi i database di sistema), la configurazione a disponibilità [elevata](sql-database-high-availability.md)e la configurazione dei flussi di dati relativi all'integrità e al monitoraggio [delle prestazioni.](../azure-monitor/insights/azure-sql.md)

> [!IMPORTANT]
> Per un elenco delle funzionalità supportate, parzialmente supportate e non supportate, vedere [Funzionalità del Database SQL](sql-database-features.md). Per un elenco delle differenze T-SQL nelle istanze gestite rispetto a SQL Server, vedere [le differenze T-SQL dell'istanza gestita rispetto a SQL ServerFor](sql-database-managed-instance-transact-sql-information.md) a list of T-SQL differences in managed instances versus SQL Server, see managed instance T-SQL differences from SQL Server

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Come identificare un'istanza gestita a livello di codice

La tabella seguente mostra diverse proprietà, accessibili tramite Transact SQL, che è possibile usare per rilevare se l'applicazione funziona con l'istanza gestita e recuperare proprietà importanti.

|Proprietà|valore|Comment|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Questo valore è uguale a quello del database SQL. Ciò **non** indica SQL engine versione 12 (SQL Server 2014). L'istanza gestita esegue sempre la versione più recente del motore SQL stabile, che è uguale o superiore all'ultima versione RTM disponibile di SQL Server.  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|Questo valore è uguale a quello del database SQL.|
|`SERVERPROPERTY('EngineEdition')`|8|Questo valore identifica l'istanza gestita in modo univoco.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Nome DNS completo dell'istanza nel formato seguente:`<instanceName>`.`<dnsPrefix>`.database.Windows.net, dove `<instanceName>` è il nome fornito dal cliente, mentre `<dnsPrefix>` è la parte del nome generata automaticamente che garantisce l'univocità del DNS globale (ad esempio "wcus17662feb9ce98")|Esempio: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come creare la prima istanza gestita, vedere [Guida introduttiva](sql-database-managed-instance-get-started.md).
- Per un elenco di confronto delle funzionalità, vedere [Confronto tra le funzionalità: database SQL di Azure e SQL Server](sql-database-features.md).
- Per altre informazioni sulla configurazione della rete virtuale, vedere l'articolo relativo alla [configurazione di una rete virtuale per l'istanza gestita](sql-database-managed-instance-connectivity-architecture.md).
- Per una guida introduttiva che crea un'istanza gestita e ripristina un database da un file di backup, vedere [creare un'istanza gestita.](sql-database-managed-instance-get-started.md)
- Per un'esercitazione sull'uso del servizio di migrazione del database di Azure per la migrazione, vedere Migrazione di [istanze gestite tramite DMS.](../dms/tutorial-sql-server-to-managed-instance.md)
- Per un monitoraggio avanzato delle prestazioni del database delle istanze gestite con la funzionalità di informazioni per la risoluzione dei problemi incorporata, vedere Monitorare il database SQL di Azure con Azure SQL Analytics.For advanced monitoring of managed instance database performance with built-in troubleshooting intelligence, see [Monitor Azure SQL Database using Azure SQL Analytics.](../azure-monitor/insights/azure-sql.md)
- Per informazioni sui prezzi, vedere Determinazione dei prezzi delle [istanze gestite dal database SQL.](https://azure.microsoft.com/pricing/details/sql-database/managed/)
