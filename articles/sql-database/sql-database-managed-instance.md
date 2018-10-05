---
title: Panoramica di Istanza gestita di database SQL di Azure | Microsoft Docs
description: Questo argomento descrive un'istanza gestita di Azure SQL Database e spiega come funziona e in cosa differisce da un database singolo nel database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
manager: craigg
ms.date: 09/26/2018
ms.openlocfilehash: 626dd362248027831c78d1505662ca12d2ff334d
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392823"
---
# <a name="what-is-a-managed-instance"></a>Informazioni su Istanza gestita

L’Istanza gestita di database SQL di Azure è un nuovo modello di distribuzione del database SQL di Azure che offre quasi il 100% di compatibilità con il più recente motore di database SQL Server in locale (Enterprise Edition), fornendo un'implementazione della [rete virtuale (VNet)](../virtual-network/virtual-networks-overview.md) nativa che risolve problemi di sicurezza comuni e un [modello aziendale](https://azure.microsoft.com/pricing/details/sql-database/) favorevole per i clienti di SQL Server in locale. Istanza gestita consente ai clienti di SQL Server esistenti di spostare nel cloud le proprie applicazioni locali con modifiche minime nelle applicazioni e nel database. Allo stesso tempo, consente di mantenere tutte le funzionalità PaaS (applicazione automatica di patch e aggiornamenti di versione, [backup automatici](sql-database-automated-backups.md), [disponibilità elevata](sql-database-high-availability.md)), che riducono drasticamente il carico di gestione e il costo totale di proprietà.

> [!IMPORTANT]
> Per un elenco delle aree geografiche in cui è attualmente disponibile Istanza gestita, vedere [Migrate your databases to a fully managed service with Azure SQL Database Managed Instance](https://azure.microsoft.com/blog/migrate-your-databases-to-a-fully-managed-service-with-azure-sql-database-managed-instance/) (Eseguire la migrazione dei database a un servizio completamente gestito con Istanza gestita di database SQL di Azure).
 
Nel diagramma seguente vengono descritte le funzionalità principali di Istanza gestita:

![funzionalità principali](./media/sql-database-managed-instance/key-features.png)

L’Istanza gestita di database SQL di Azure per i clienti che desiderano eseguire la migrazione di un numero maggiore di app da un ambiente locale o IaaS, creato personalmente o un ambiente fornito da un ISV per la gestione completa dell’ambiente cloud PaaS, con il minor numero di operazioni di migrazione possibile. Tramite il [Servizio Migrazione del database (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) completamente automatico di Azure, i clienti possono trasferire il proprio server SQL locale in un'istanza gestita che garantisce la compatibilità con SQL Server in locale e il completo isolamento delle istanze del cliente con il supporto delle reti virtuali native.  Con Software Assurance, è possibile scambiare le licenze esistenti con tariffe scontate per un'istanza gestita di database SQL tramite l'[offerta per uso ibrido di Azure per SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).  Istanza gestita di database SQL è la destinazione di migrazione migliore nel cloud per le istanze di SQL Server che richiedono un livello di sicurezza elevato e una superficie di programmazione avanzata. 

In base alla disponibilità generale, Istanza gestita mira a offrire una compatibilità della superficie di attacco prossima al 100% con l'ultima versione di SQL Server in locale tramite un piano di rilascio a fasi. 

Per la scelta tra Database singolo Database SQL di Azure, istanza gestita di Database di Azure SQL e SQL Server in IaaS ospitate in macchine virtuali, vedere [come scegliere la versione corretta di SQL Server nel cloud Azure](sql-database-paas-vs-sql-server-iaas.md).

## <a name="key-features-and-capabilities"></a>Funzionalità e le caratteristiche chiave 

L’istanza gestita del Database SQL Azure combina le migliori funzionalità disponibili sia nel Database SQL di Azure e il motore di Database di SQL Server.

> [!IMPORTANT]
> Un'istanza gestita viene eseguita con tutte le funzionalità della versione più recente di SQL Server, incluse le operazioni online, le correzioni automatiche del piano e altri miglioramenti delle prestazioni Enterprise. 

| **Vantaggi di PaaS** | **Continuità aziendale** |
| --- | --- |
|Acquisto e gestione di hardware non necessari <br>Nessun sovraccarico per la gestione dell'infrastruttura sottostante <br>Provisioning rapido e scalabilità del servizio <br>Applicazione automatica di patch e aggiornamento della versione <br>Integrazione con altri servizi dati PaaS |Contratto di servizio relativo al tempo di attività 99,99%  <br>[Disponibilità elevata](sql-database-high-availability.md) integrata <br>Dati protetti con [backup automatici](sql-database-automated-backups.md) <br>Periodo di conservazione dei backup configurabile dal cliente (fissato a 7 giorni nell'anteprima pubblica) <br>[Backup](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) avviati dall'utente <br>Funzionalità di [ripristino temporizzato di un database](sql-database-recovery-using-backups.md#point-in-time-restore) |
|**Sicurezza e conformità** | **Gestione**|
|Ambiente isolato ([integrazione della rete virtuale](sql-database-managed-instance-vnet-configuration.md), servizio a tenant singolo, calcolo e archiviazione dedicati) <br>[Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Autenticazione di Azure AD](sql-database-aad-authentication.md), supporto di Single Sign-On <br>Soddisfa gli standard di conformità del database SQL di Azure <br>[Controllo SQL](sql-database-managed-instance-auditing.md) <br>[Rilevamento delle minacce](sql-database-managed-instance-threat-detection.md) |API di Azure Resource Manager per l'automazione del provisioning e della scalabilità del servizio <br>Funzionalità del portale di Azure per provisioning e scalabilità del servizio manuali <br>Servizio di migrazione dei dati 

## <a name="vcore-based-purchasing-model"></a>Modello di acquisto in base ai vCore

Il [modello di acquisto in base ai vCore](sql-database-service-tiers-vcore.md) offre flessibilità, controllo e trasparenza, consentendo di convertire con facilità i requisiti dei carichi di lavoro locali per il cloud. Questo modello consente di ridimensionare le risorse di calcolo, memoria e archiviazione in base ai requisiti dei carichi di lavoro. Il modello in base ai vCore dà anche diritto a un risparmio fino al 30% sul [Vantaggio Azure Hybrid Use per SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

La memoria centrale virtuale rappresenta la CPU logica offerta con la possibilità di scegliere tra generazioni di hardware diverse.
- Le CPU logiche Generazione 4 si basano su processori Intel E5-2673 v3 (Haswell) a 2,4 GHz.
- Le CPU logiche Generazione 5 si basano su processori Intel E5-2673 v4 (Broadwell) a 2,3 GHz.

La tabella seguente aiuta a capire come selezionare la configurazione ottimale delle risorse di calcolo, memoria, archiviazione e I/O.

||Generazione 4|Generazione 5|
|----|------|-----|
|Hardware|Processori Intel E5-2673 v3 (Haswell) a 2,4 GHz, con unità vCore SSD = 1 PP (core fisico)|Processori Intel E5-2673 v4 (Broadwell) a 2,3 GHz, unità SSD eNVM veloce, vCore = 1 LP (hyperthread)|
|Le dimensioni di calcolo|8, 16, 24 vCore|8, 16, 24, 32, 40, 64, 80 vCore|
|Memoria|7 GB per vCore|5,5 GB per vCore|
||||

## <a name="managed-instance-service-tiers"></a>Livelli di servizio di Istanza gestita

Istanza gestita è disponibile in due livelli di servizio:
- **Utilizzo generico**: progettato per applicazioni con prestazioni tipiche e requisiti di latenza di I/O comuni.
- **Opzione business critical (anteprima)**: progettata per applicazioni con requisiti di latenza dei I/O bassi e un impatto minimo di operazioni di manutenzione sottostanti sul carico di lavoro.

Entrambi i livelli di servizio garantiscono una disponibilità del 99,99% e consentono di selezionare le dimensioni di archiviazione e capacità di calcolo in modo indipendente. Per altre informazioni sull'architettura di disponibilità elevata del database SQL di Azure, vedere [High Availability and Azure SQL Database](sql-database-high-availability.md) (Disponibilità elevata e database SQL di Azure).

> [!IMPORTANT]
> La modifica del livello di servizio da utilizzo generico a business critical o viceversa non è supportata in anteprima pubblica. Se si desidera eseguire la migrazione dei database a un'istanza in un livello di servizio diverso, è possibile creare una nuova istanza, ripristinare i database con ripristino temporizzato dall'istanza originale e quindi rilasciare l'istanza originale se non è più necessaria. 

### <a name="general-purpose-service-tier"></a>Livello di servizio Utilizzo generico

L'elenco seguente descrive le caratteristiche principali del livello di servizio Utilizzo generico: 

- Progettazione della maggior parte delle applicazioni aziendali con requisiti di prestazioni tipici 
- Archiviazione Premium di Azure ad alte prestazioni (8 TB) 
- 100 database per istanza 

L'elenco seguente descrive le caratteristiche principali del livello di servizio per utilizzo generico:

|Funzionalità | DESCRIZIONE|
|---|---|
| Numero di vCore* | 8, 16, 24 (Generazione 4)<br>8, 16, 24, 32, 40, 64, 80 (5° generazione)|
| Versione/build di SQL Server | Motore di database di SQL Server (ultima versione stabile) |
| Dimensione minima archiviazione | 32 GB |
| Dimensione massima archiviazione | 8 TB |
| Quantità massima di risorse di archiviazione per database | Determinata dalla dimensione massima di archiviazione per ogni istanza |
| Archiviazione IOPS prevista | 500-7500 IOPS per ogni file di dati (dipende dal file di dati). Vedere [Archiviazione Premium](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes) |
| Numero di file di dati (RIGHE) per il database | Multipli | 
| Numero di file di log (LOG) per il database | 1 | 
| Backup automatici gestiti | Yes |
| DISPONIBILITÀ ELEVATA | Dati archiviati in Archiviazione di Azure e [Azure Service Fabric](../service-fabric/service-fabric-overview.md) |
| Monitoraggio predefinito e metriche dell'istanza e del database | Yes |
| Applicazione automatica di patch software | Yes |
| Rete virtuale: distribuzione di Azure Resource Manager | Yes |
| Rete virtuale: modello di distribuzione classica | No  |
| Supporto del portale | Yes|
|||

\* La memoria centrale virtuale rappresenta la CPU logica offerta con la possibilità di scegliere tra generazioni di hardware. Le CPU logiche di quarta generazione sono basate su processori Intel E5-2673 v3 (Haswell) a 2,4 GHz e le CPU logiche di quinta generazione sono basate su processori Intel E5-2673 v4 (Broadwell) a 2,3 GHz. 

Per ulteriori informazioni, vedere [disponibilità Standard/utilizzo generico e architettura](sql-database-high-availability.md#standardgeneral-purpose-availability) nel Database SQL di Azure.

### <a name="business-critical-service-tier-preview"></a>Livello di servizio business critical (anteprima)

Il livello di servizio business critical è progettato per applicazioni con requisiti I/O elevati. Offre la massima resilienza agli errori tramite diverse repliche Always On isolate. 

L'elenco seguente descrive le caratteristiche principali del livello di servizio per business critical: 
-   Progettato per applicazioni aziendali con le prestazioni e i requisiti di disponibilità più elevati 
-   Viene fornito con l'archiviazione SSD estremamente rapida (fino a 1 TB nella quarta generazione e fino a 4 TB nella quinta generazione)
-   Supporta fino a 100 database per istanza 
- Istanza predefinita di sola lettura che può essere utilizzata per la creazione di report e altri carichi di lavoro di sola lettura
- [OLTP in memoria](sql-database-in-memory.md) che può essere utilizzato per il carico di lavoro con requisiti di prestazione elevata  

|Funzionalità | DESCRIZIONE|
|---|---|
| Numero di vCore* | 8, 16, 24, 32 (quarta generazione)<br>8, 16, 24, 32, 40, 64, 80 (5° generazione)|
| Versione/build di SQL Server | SQL Server (versione più recente disponibile) |
| Funzionalità aggiuntive | [OLTP in memoria](sql-database-in-memory.md)<br> 1 replica di sola lettura ([scalabilità in lettura](sql-database-read-scale-out.md))
| Dimensione minima archiviazione | 32 GB |
| Dimensione massima archiviazione | 4° generazione: 1 TB (tutte le dimensioni vCore)<br> 5° generazione:<ul><li>1 TB per 8, 16 vCore</li><li>2 TB per 24 vCore</li><li>4 TB per 32, 40, 64, 80 vCore</ul>|
| Quantità massima di risorse di archiviazione per database | Determinata dalla dimensione massima di archiviazione per ogni istanza |
| Numero di file di dati (RIGHE) per il database | Multipli | 
| Numero di file di log (LOG) per il database | 1 | 
| Backup automatici gestiti | Yes |
| DISPONIBILITÀ ELEVATA | Dati archiviati in un'unità SSD locale e uso di [gruppi di disponibilità AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) e [Azure Service Fabric](../service-fabric/service-fabric-overview.md) |
| Monitoraggio predefinito e metriche dell'istanza e del database | Yes |
| Applicazione automatica di patch software | Yes |
| Rete virtuale: distribuzione di Azure Resource Manager | Yes |
| Rete virtuale: modello di distribuzione classica | No  |
| Supporto del portale | Yes|
|||

Per ulteriori informazioni, vedere [disponibilità Premium/Business Critical e architettura](sql-database-high-availability.md#premiumbusiness-critical-availability) nel Database SQL di Azure.

> [!IMPORTANT]
> Il livello di servizio **business critical** è in anteprima.

## <a name="advanced-security-and-compliance"></a>Sicurezza e conformità avanzate 

L’istanza gestita del Database SQL Azure combina le funzionalità avanzate di sicurezza fornite dal cloud di Azure e il motore di Database di SQL Server. 

### <a name="managed-instance-security-isolation-in-azure-cloud"></a>Isolamento di sicurezza dell’istanza gestita in Azure Cloud 

Istanza gestita offre isolamento di sicurezza aggiuntivo dagli altri tenant nel cloud di Azure. L'isolamento di sicurezza include: 

- L'[implementazione della rete virtuale nativa](sql-database-managed-instance-vnet-configuration.md) e la connettività all'ambiente locale mediante Express Route di Azure o Gateway VPN 
- L'endpoint SQL viene esposto solo tramite un indirizzo IP privato, che consente la connettività sicura da reti private di Azure o ibride
- Tenant singolo con infrastruttura sottostante dedicata (calcolo, archiviazione)

Il diagramma seguente illustra diverse opzioni di connettività per le applicazioni: 

![disponibilità elevata](./media/sql-database-managed-instance/application-deployment-topologies.png)  

Per altre informazioni dettagliate sull'integrazione della rete virtuale e sull'applicazione di criteri di rete a livello di subnet, vedere [Configurare una rete virtuale per Istanza gestita di database SQL di Azure](sql-database-managed-instance-vnet-configuration.md) e [Connettere l'applicazione a Istanza gestita di database SQL di Azure](sql-database-managed-instance-connect-app.md). 

> [!IMPORTANT]
> Inserire più istanze gestite nella stessa subnet, ogniqualvolta tale operazione è consentita dai requisiti di sicurezza, in quanto comporterà vantaggi aggiuntivi. La collocazione di istanze nella stessa subnet semplificherà notevolmente la manutenzione dell'infrastruttura di rete e ridurrà il tempo di provisioning dell'istanza, poiché una durata prolungata di provisioning è associata al costo della distribuzione di una prima istanza gestita in una subnet.

### <a name="azure-sql-database-security-features"></a>Funzionalità di sicurezza del database SQL di Microsoft Azure

Database SQL di Azure fornisce un set di funzionalità di sicurezza avanzato che può essere usato per proteggere i dati.

- Il [controllo di istanza gestita](sql-database-managed-instance-auditing.md) tiene traccia degli eventi del database e li registra in un log di controllo nell'account di archiviazione di Azure. Il controllo consente di agevolare la conformità alle normative, comprendere le attività del database e ottenere informazioni su eventuali discrepanze e anomalie che potrebbero indicare problemi aziendali o sospette violazioni della sicurezza. 
- Crittografia dei dati in transito: istanza gestita che protegge i dati fornendo la crittografia per i dati in transito tramite Transport Layer Security. Oltre alla sicurezza a livello di trasporto, Istanza gestita di database SQL offre protezione per i dati sensibili in movimento, inattivi e durante l'elaborazione di query con [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted è una tecnologia leader del settore che offre un livello di sicurezza dei dati senza uguali, per la protezione da violazioni che implicano il furto di dati critici. Con Always Encrypted, ad esempio, i numeri delle carte di credito sono sempre archiviati in forma crittografata nel database, anche durante l'elaborazione di query, e la decrittografia è consentita nella posizione di utilizzo da parte di personale o applicazioni autorizzati che devono elaborare tali dati. 
- Il [rilevamento delle minacce](sql-database-managed-instance-threat-detection.md) è complementare al [controllo di Istanza gestita](sql-database-managed-instance-auditing.md) perché offre un livello aggiuntivo di informazioni sulla sicurezza integrata nel servizio che rileva tentativi insoliti e potenzialmente dannosi di accesso o exploit del database. L'utente viene avvisato di attività sospette, vulnerabilità potenziali e attacchi SQL injection, nonché di modelli anomali di accesso al database. Gli avvisi di Rilevamento minacce sono disponibili nel [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/), forniscono i dettagli delle attività sospette e raccomandano azioni per individuare e ridurre la minaccia.  
- La funzione [Maschera dati dinamica](/sql/relational-databases/security/dynamic-data-masking) limita l'esposizione dei dati sensibili, nascondendoli agli utenti senza privilegi. La maschera dati dinamica impedisce l'accesso non autorizzato ai dati sensibili consentendo di definire la quantità di dati sensibili da rivelare, con un impatto minimo sul livello dell'applicazione. Si tratta di una funzionalità di sicurezza basata su criteri che consente di nascondere i dati sensibili nel set di risultati di una query in campi del database designati, senza alcuna modifica dei dati contenuti nel database. 
- La [sicurezza a livello di riga](/sql/relational-databases/security/row-level-security) consente di controllare l'accesso alle righe in una tabella di database in base alle caratteristiche dell'utente che esegue una query, ad esempio l'appartenenza a un gruppo o il contesto di esecuzione. La sicurezza a livello di riga semplifica la progettazione e la codifica della sicurezza nell'applicazione. Consente di implementare limitazioni per l'accesso alle righe di dati, assicurando ad esempio che i collaboratori possano accedere solo alle righe di dati pertinenti per il proprio reparto o limitando l'accesso ai dati ai soli dati di pertinenti. 
- La funzione [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) esegue la crittografia dei file di dati dell'istanza gestita di database SQL di Azure, note anche come dati inattivi crittografati. TDE esegue la crittografia e la decrittografia delle operazioni di I/O di file di dati e log in tempo reale. La crittografia usa una chiave di crittografia del database (DEK) che viene archiviata nel record di avvio del database per la disponibilità durante il ripristino. È possibile proteggere tutti i database nell'istanza gestita con la crittografia dei dati trasparente. TDE è la tecnologia di crittografia dei dati inattivi collaudata di SQL, richiesta da molti standard di conformità per la protezione in caso di furto di supporti di archiviazione. Durante l'anteprima pubblica, il modello di gestione automatica delle chiavi è supportato (eseguito dalla piattaforma PaaS). 

La migrazione di un database crittografato in Istanza gestita SQL è supportata tramite il servizio Migrazione del database di Azure(DMS) o il ripristino nativo. Se si decide di eseguire la migrazione del database crittografato tramite ripristino nativo, la migrazione del certificato TDE esistente dal server SQL locale o dalla macchina virtuale del server SQL a Istanza gestita è un passaggio necessario. Per altre informazioni sui vari metodi di migrazione, vedere [Migrazione di un'istanza di SQL Server a Istanza gestita di database SQL di Azure](sql-database-managed-instance-migrate.md).

## <a name="azure-active-directory-integration"></a>Integrazione di Azure Active Directory

L’istanza gestita del Database SQL Azure supporta i tradizionali account di accesso SQL server Database engine e gli account di accesso integrato con Azure Active Directory (AAD). Gli account di accesso di AAD sono una versione cloud di Azure di account di accesso di database di Windows usati nell'ambiente locale.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integrazione in Azure Active Directory e autenticazione a più fattori 

L’istanza gestita consente di gestire a livello centralizzato le identità degli utenti di database e altri servizi Microsoft grazie all'[integrazione in Azure Active Directory](sql-database-aad-authentication.md). Questa funzionalità semplifica la gestione delle autorizzazioni e ottimizza la sicurezza. Azure Active Directory supporta l'[autenticazione a più fattori](sql-database-ssms-mfa-authentication-configure.md) (MFA) per una maggiore sicurezza di dati e applicazioni, supportando allo stesso tempo un processo di accesso singolo. 

### <a name="authentication"></a>Authentication 
Per autenticazione dell’istanza gestita si intende il modo in cui l'utente dimostra la propria identità durante la connessione al database. Il database SQL supporta due tipi di autenticazione:  

- L'autenticazione SQL, che usa un nome utente e una password.
- L'autenticazione di Azure Active Directory, che usa identità gestite da Azure Active Directory ed è supportata per i domini gestiti e integrati. 

### <a name="authorization"></a>Authorization

Per autorizzazione si intendono le operazioni che l'utente può eseguire in un database SQL di Azure, che sono controllate dalle appartenenze ai ruoli del database e dalle autorizzazioni a livello di oggetto dell'account utente. Istanza gestita ha le stesse funzionalità di autorizzazione di SQL Server 2017. 

## <a name="database-migration"></a>Migrazione di database 

Il servizio Istanza gestita è destinato a scenari utente con migrazione di massa di database da implementazioni di database locali o IaaS. Istanza gestita supporta diverse opzioni di migrazione di database: 

### <a name="backup-and-restore"></a>Backup e ripristino  

L'approccio di migrazione sfrutta i backup di SQL per l'archiviazione di BLOB di Azure. I backup archiviati in BLOB di archiviazione di Azure possono essere ripristinati direttamente in Istanza gestita usando il [comando T-SQL RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current). 
  - Per una guida introduttiva su come ripristinare il file di backup di database Wide World Importers - Standard, vedere [Restore a backup file to a Managed Instance](sql-database-managed-instance-get-started-restore.md) (Ripristinare un file di backup in un'istanza gestita). Questa guida introduttiva illustra come caricare un file di backup in una risorsa di archiviazione BLOB di Azure e proteggerlo usando una chiave di firma di accesso condiviso.
  - Per informazioni sul ripristino dall'URL, vedere [Ripristino nativo da URL](sql-database-managed-instance-migrate.md#native-restore-from-url).

> [!IMPORTANT]
> I backup da un'istanza gestita possono essere ripristinati solo in un'altra istanza gestita. Non possono essere ripristinati in un Server SQL in locale o in un database singolo o in pool di server logico del database SQL di Azure.

### <a name="data-migration-service"></a>Servizio di migrazione dei dati

Il Servizio Migrazione del database di Azure è un servizio completamente gestito progettato per abilitare le migrazioni senza interruzioni da più origini di database alle piattaforme di dati di Azure con tempi di inattività minimi. Questo servizio semplifica le attività necessarie per spostare database di SQL Server e di terze parti in Azure. Le opzioni di distribuzione includono il database SQL di Azure, Istanza gestita e SQL Server in una macchina virtuale di Azure nell'anteprima pubblica. Vedere [come eseguire la migrazione del database locale in Istanza gestita tramite DMS](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Funzionalità di SQL supportate 

Istanza gestita mira a offrire una compatibilità della superficie di attacco prossima al 100% con SQL Server in locale disponibile a fasi fino alla disponibilità generale del servizio. Per un elenco delle funzionalità e di [confronto vedere confronto delle funzionalità del database SQL](sql-database-features.md) e per l’elenco delle differenze di T-SQL nelle istanze gestite rispetto a SQL Server, vedere [Differenze di T-SQL in Istanza gestita rispetto a SQL Server](sql-database-managed-instance-transact-sql-information.md).
 
Istanza gestita supporta la compatibilità con le versioni precedenti per i database di SQL 2008. È supportata la migrazione diretta da server di database di SQL 2005, il livello di compatibilità per i database di SQL 2005 migrati viene aggiornato a SQL 2008. 
 
Il diagramma seguente illustra la compatibilità della superficie di attacco in Istanza gestita:  

![migrazione](./media/sql-database-managed-instance/migration.png) 

### <a name="key-differences-between-sql-server-on-premises-and-managed-instance"></a>Differenze principali tra SQL Server in locale e Istanza gestita 

Istanza gestita offre vantaggi derivanti dall'essere sempre aggiornati nel cloud, mentre alcune funzionalità di SQL Server in locale possono essere obsolete, essere state ritirate o sostituite con alternative. In alcuni casi specifici gli strumenti devono riconoscere che una determinata funzionalità funziona in modo leggermente diverso o che il servizio non funziona in un ambiente di cui non si ha il controllo completo: 

- La disponibilità elevata è incorporata e preconfigurata. Le funzionalità di disponibilità elevata Always On non sono esposte nello stesso modo delle implementazioni IaaS SQL 
- Backup automatici e ripristino temporizzato. Il cliente può avviare backup `copy-only` che non interferiscono con la catena di backup automatica. 
- Istanza gestita non consente l'uso di percorsi fisici completi, pertanto tutti gli scenari corrispondenti devono essere supportati in modo diverso: RESTORE DB non supporta WITH MOVE, CREATE DB non consente i percorsi fisici, BULK INSERT funziona solo con i BLOB di Azure e così via. 
- Istanza gestita supporta l'[autenticazione di Azure AD](sql-database-aad-authentication.md) come alternativa cloud all'autenticazione di Windows. 
- Istanza gestita gestisce automaticamente filegroup XTP e i file per i database contenenti oggetti di OLTP in memoria
- Istanza gestita supporta SQL Server Integration Services e può ospitare il catalogo SSIS, ovvero SSISDB, che archivia i pacchetti SSIS, che però vengono eseguiti in un runtime di integrazione SSIS di Azure gestito in Azure Data Factory, vedere [Creare il runtime di integrazione SSIS di Azure in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Per confrontare le funzionalità SSIS nel database SQL e in Istanza gestita, vedere [Confrontare il server logico del Database SQL e Istanza gestita](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-logical-server-and-sql-database-managed-instance).

### <a name="managed-instance-administration-features"></a>Funzionalità di amministrazione di Istanza gestita  

Istanza gestita consente all'amministratore di sistema di concentrarsi sugli aspetti più importanti per l'azienda. Molte attività dell'amministratore di sistema o di database non sono necessarie o sono semplici. Ad esempio, l'installazione del sistema operativo o di RDBMS e l'applicazione di patch, il ridimensionamento dinamico e la configurazione delle istanze, i backup, la [replica di database](replication-with-sql-database-managed-instance.md) (inclusi i database di sistema), la configurazione a elevata disponibilità e la configurazione di flussi di dati di monitoraggio dell'integrità e delle prestazioni. 

> [!IMPORTANT]
> Per un elenco delle funzionalità supportate, parzialmente supportate e non supportate, vedere [Funzionalità del Database SQL](sql-database-features.md). Per un elenco delle differenze di T-SQL nelle istanze gestite rispetto a SQL Server, vedere [Differenze di T-SQL in Istanza gestita rispetto a SQL Server](sql-database-managed-instance-transact-sql-information.md)

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Come identificare un'istanza gestita a livello di codice

La tabella seguente mostra diverse proprietà, accessibili tramite Transact SQL, che è possibile usare per rilevare se l'applicazione funziona con Istanza gestita e recuperare proprietà importanti.

|Proprietà|Valore|Comment|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Questo valore è uguale a quello del database SQL.|
|`SERVERPROPERTY ('Edition')`|SQL Azure|Questo valore è uguale a quello del database SQL.|
|`SERVERPROPERTY('EngineEdition')`|8|Questo valore identifica l'istanza gestita in modo univoco.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Nome DNS completo dell'istanza nel formato seguente:<instanceName>.<dnsPrefix>. database.Windows.NET, dove <instanceName> è il nome fornito dal cliente, mentre <dnsPrefix> è la parte del nome generata automaticamente che garantisce l'univocità del DNS globale (ad esempio "wcus17662feb9ce98")|Esempio: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come creare la prima istanza gestita, vedere [Guida introduttiva](sql-database-managed-instance-get-started.md).
- Per un elenco di confronto delle funzionalità, vedere [Confronto tra le funzionalità: database SQL di Azure e SQL Server](sql-database-features.md).
- Per altre informazioni sulla configurazione della rete virtuale, vedere l'articolo relativo alla [configurazione di una rete virtuale per Istanza gestita](sql-database-managed-instance-vnet-configuration.md).
- Per una guida introduttiva sulla creazione di un'istanza gestita e il ripristino di un database da un file di backup, vedere [Creare un'istanza gestita](sql-database-managed-instance-get-started.md).
- Per un'esercitazione sull'uso di Servizio Migrazione del database di Azure per la migrazione, vedere l'articolo relativo alla [migrazione a Istanza gestita con Servizio Migrazione del database](../dms/tutorial-sql-server-to-managed-instance.md).
- Per informazioni sui prezzi, vedere [Prezzi di Istanza gestita di database SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).
