---
title: Che cos'è Istanza gestita di SQL di Azure?
description: Informazioni su come Istanza gestita di SQL di Azure offra una compatibilità quasi totale con la versione più recente del motore di database di SQL Server (Enterprise Edition)
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: overview
author: bonova
ms.author: bonova
ms.reviewer: sstein, vanto
ms.date: 08/14/2020
ms.openlocfilehash: e515df0ff8c7cd3794efb4db567ef7146ccb7a03
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424236"
---
# <a name="what-is-azure-sql-managed-instance"></a>Che cos'è Istanza gestita di SQL di Azure?
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Istanza gestita di SQL di Azure è il servizio di database cloud intelligente e scalabile che combina la massima compatibilità del motore di database di SQL Server con tutti i vantaggi di una classica piattaforma distribuita come servizio completamente gestita. Istanza gestita di SQL ha quasi il 100% di compatibilità con il più recente motore di database di SQL Server (Enterprise Edition), fornendo un'implementazione della [rete virtuale nativa](../../virtual-network/virtual-networks-overview.md) che risolve problemi di sicurezza comuni e un [modello aziendale](https://azure.microsoft.com/pricing/details/sql-database/) favorevole per i clienti di SQL Server esistenti. Istanza gestita di SQL consente ai clienti di SQL Server esistenti di spostare nel cloud le proprie applicazioni locali con modifiche minime nelle applicazioni e nel database. Allo stesso tempo, consente di mantenere tutte le funzionalità PaaS (applicazione automatica di patch e aggiornamenti di versione, [backup automatizzati](../database/automated-backups-overview.md), [disponibilità elevata](../database/high-availability-sla.md)) che riducono drasticamente il carico di gestione e il costo totale di proprietà.

Se non si ha familiarità con Istanza gestita di SQL di Azure, vedere il video *Istanza gestita di SQL di Azure* della [serie di video approfonditi su SQL di Azure](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/Azure-SQL-Managed-Instance-Overview-6-of-61/player]

> [!IMPORTANT]
> Per un elenco delle aree in cui Istanza gestita di SQL è attualmente disponibile, vedere [Aree supportate](resource-limits.md#supported-regions).

Nel diagramma seguente vengono descritte le funzionalità principali di Istanza gestita di SQL:

![Funzionalità principali](./media/sql-managed-instance-paas-overview/key-features.png)

Istanza gestita di SQL di Azure è stato pensato per i clienti che vogliono eseguire la migrazione di un numero elevato di app da un ambiente locale o IaaS creato personalmente oppure da un ambiente fornito da un ISV a un ambiente cloud PaaS completamente gestito, con il minor numero possibile di operazioni di migrazione. Usando il [Servizio Migrazione del database di Azure](../../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) completamente automatizzato, i clienti possono trasferire l'istanza di SQL Server esistente in Istanza gestita di SQL, il che garantisce la compatibilità con SQL Server e il completo isolamento delle istanze dei clienti grazie al supporto delle reti virtuali native.  Con Software Assurance è possibile scambiare le licenze esistenti con tariffe scontate per Istanza gestita di SQL usando il [Vantaggio Azure Hybrid per SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Istanza gestita di SQL è la destinazione di migrazione migliore nel cloud per le istanze di SQL Server che richiedono un livello di sicurezza elevato e una superficie di programmazione avanzata.

## <a name="key-features-and-capabilities"></a>Funzionalità e le caratteristiche chiave

Istanza gestita di SQL combina le migliori funzionalità disponibili sia nel database SQL di Azure che nel motore di database di SQL Server.

> [!IMPORTANT]
> Istanza gestita di SQL viene eseguita con tutte le funzionalità della versione più recente di SQL Server, incluse le operazioni online, le correzioni automatiche del piano e altri miglioramenti delle prestazioni Enterprise. Per un confronto tra le funzionalità disponibili, vedere [Confronto tra le funzionalità: Istanza gestita di SQL di Azure e SQL Server](../database/features-comparison.md).

| **Vantaggi di PaaS** | **Continuità aziendale** |
| --- | --- |
|Acquisto e gestione di hardware non necessari <br>Nessun sovraccarico per la gestione dell'infrastruttura sottostante <br>Provisioning rapido e scalabilità del servizio <br>Applicazione automatica di patch e aggiornamento della versione <br>Integrazione con altri servizi dati PaaS |Contratto di servizio relativo al tempo di attività 99,99%  <br>[Disponibilità elevata](../database/high-availability-sla.md) predefinita <br>Dati protetti con [backup automatici](../database/automated-backups-overview.md) <br>Periodo di conservazione dei backup configurabile dal cliente <br>[Backup](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current&preserve-view=true) avviati dall'utente <br>Funzionalità di [ripristino temporizzato di un database](../database/recovery-using-backups.md#point-in-time-restore) |
|**Sicurezza e conformità** | **Gestione**|
|Ambiente isolato ([integrazione della rete virtuale](connectivity-architecture-overview.md), servizio a tenant singolo, calcolo e archiviazione dedicati) <br>[Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Autenticazione di Azure Active Directory (Azure AD)](../database/authentication-aad-overview.md), supporto di Single Sign-On <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Entità server (account di accesso) di Azure AD</a>  <br>Soddisfa gli standard di conformità del database SQL di Azure <br>[Controllo SQL](auditing-configure.md) <br>[Advanced Threat Protection](threat-detection-configure.md) |API di Azure Resource Manager per l'automazione del provisioning e della scalabilità del servizio <br>Funzionalità del portale di Azure per provisioning e scalabilità del servizio manuali <br>Servizio di migrazione dei dati

> [!IMPORTANT]
> Istanza gestita di SQL di Azure è certificato rispetto a diversi standard di conformità. Per altre informazioni, vedere le [offerte di conformità di Microsoft Azure](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuideV3?command=Download&downloadType=Document&downloadId=44bbae63-bf4d-4e3b-9d3d-c96fb25ec363&tab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb&docTab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb_FAQ_and_White_Papers), dove è possibile trovare l'elenco più recente di certificazioni di conformità di Istanza gestita di SQL di Azure, nella sezione relativa al **database SQL**.

Nella tabella seguente sono elencate le principali funzionalità di Istanza gestita di SQL:

|Funzionalità | Descrizione|
|---|---|
| Versione/Build di SQL Server | Motore di database di SQL Server (ultima versione stabile) |
| Backup automatici gestiti | Sì |
| Monitoraggio predefinito e metriche dell'istanza e del database | Sì |
| Applicazione automatica di patch software | Sì |
| Funzionalità più recenti del motore di database | Sì |
| Numero di file di dati (RIGHE) per il database | Più elementi |
| Numero di file di log (LOG) per il database | 1 |
| Rete virtuale: distribuzione di Azure Resource Manager | Sì |
| Rete virtuale: modello di distribuzione classica | No |
| Supporto del portale | Sì|
| Integration Services (SSIS) incorporato | No - SSIS fa parte di [Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| Analysis Services (SSAS) incorporato | No - SSAS fa parte di una [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) distinta |
| Reporting Services (SSRS) incorporato | No - usare [report impaginati di Power BI](https://docs.microsoft.com/power-bi/paginated-reports/paginated-reports-report-builder-power-bi) o ospitare SSRS in una macchina virtuale di Azure. Istanza gestita di SQL non può eseguire SSRS come servizio, ma può ospitare i [database di catalogo di SSRS](https://docs.microsoft.com/sql/reporting-services/install-windows/ssrs-report-server-create-a-report-server-database#database-server-version-requirements) per un server di report installato nella macchina virtuale di Azure, usando l'autenticazione di SQL Server. |
|||

## <a name="vcore-based-purchasing-model"></a>Modello di acquisto basato su vCore

Il [modello di acquisto basato su vCore](../database/service-tiers-vcore.md) per Istanza gestita di SQL offre flessibilità, controllo e trasparenza, consentendo di convertire con facilità i requisiti dei carichi di lavoro locali per il cloud. Questo modello consente di cambiare le risorse di calcolo, memoria e archiviazione in base ai requisiti dei carichi di lavoro. Il modello basato su vCore dà anche diritto a un risparmio massimo del 55% con il [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) per SQL Server.

Nel modello basato su vCore è possibile scegliere tra diverse generazioni di hardware.

- Le CPU logiche di **quarta generazione** sono basate su processori Intel® E5-2673 v3 (Haswell) da 2,4 GHz, unità SSD collegata, core fisici, 7 GB di RAM per core e dimensioni di calcolo comprese tra 8 e 24 vCore.
- Le CPU logiche di **quinta generazione** sono basate su processori Intel® E5-2673 v4 (Broadwell) da 2,3 GHz, Intel® SP-8160 (Skylake) e Intel® 8272CL (Cascade Lake) da 2,5 GHz, unità SSD NVMe veloce, core logico con hyperthreading e dimensioni di calcolo comprese tra 4 e 80 core.

Per altre informazioni sulle differenze tra le generazioni di hardware, vedere [Limiti delle risorse per Istanza gestita di SQL](resource-limits.md#hardware-generation-characteristics).

## <a name="service-tiers"></a>Livelli di servizio

L'istanza gestita di SQL è disponibile in due livelli di servizio:

- **Utilizzo generico**: progettato per applicazioni con prestazioni tipiche e requisiti di latenza di I/O comuni.
- **Business Critical**: progettato per applicazioni con requisiti di latenza di I/O bassa e un impatto minimo di operazioni di manutenzione sottostanti sul carico di lavoro.

Entrambi i livelli di servizio garantiscono una disponibilità del 99,99% e consentono di selezionare le dimensioni di archiviazione e capacità di calcolo in modo indipendente. Per altre informazioni sull'architettura di disponibilità elevata di Istanza gestita di SQL di Azure, vedere [Disponibilità elevata e Istanza gestita di SQL di Azure](../database/high-availability-sla.md).

### <a name="general-purpose-service-tier"></a>Livello di servizio Utilizzo generico

L'elenco seguente descrive le caratteristiche principali del livello di servizio Utilizzo generico:

- È stato progettato per la maggior parte delle applicazioni aziendali con requisiti di prestazioni tipici
- Archiviazione BLOB di Azure ad alte prestazioni (8 TB)
- [Disponibilità elevata](../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) predefinita basata sull'affidabilità di Archiviazione BLOB di Azure e su [Azure Service Fabric](../../service-fabric/service-fabric-overview.md)

Per altre informazioni, vedere [Storage layer in the General purpose tier](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) (Livello Archiviazione nel livello di servizio Utilizzo generico) e [Procedure consigliate e considerazioni sulle prestazioni di archiviazione per Istanza gestita di SQL (Utilizzo generico)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

Per altre informazioni sulle differenze tra i livelli di servizio, vedere [Limiti delle risorse per Istanza gestita di SQL](resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Livello di servizio business critical

Il livello di servizio business critical è progettato per applicazioni con requisiti di I/O elevati. Offre la massima resilienza agli errori usando diverse repliche isolate.

L'elenco seguente descrive le caratteristiche principali del livello di servizio per business critical:

- Progettato per applicazioni aziendali con le prestazioni e i requisiti di disponibilità più elevati
- Viene fornito con l'archiviazione SSD locale estremamente rapida (fino a 1 TB nella quarta generazione e fino a 4 TB nella quinta generazione)
- [Disponibilità elevata](../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) predefinita in base ai [gruppi di disponibilità Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) e ad [Azure Service Fabric](../../service-fabric/service-fabric-overview.md)
- [Replica di database di sola lettura](../database/read-scale-out.md) integrata aggiuntiva che può essere usata per la creazione di report e altri carichi di lavoro di sola lettura
- [OLTP in memoria](../in-memory-oltp-overview.md) che può essere usato per il carico di lavoro con requisiti di prestazioni elevate  

Per altre informazioni sulle differenze tra i livelli di servizio, vedere [Limiti delle risorse per Istanza gestita di SQL](resource-limits.md#service-tier-characteristics).

## <a name="management-operations"></a>Operazioni di gestione

Istanza gestita di SQL di Azure include operazioni di gestione che è possibile usare per distribuire automaticamente nuove istanze gestite, aggiornare le proprietà delle istanze ed eliminare le istanze quando non sono più necessarie. Per una spiegazione dettagliata delle operazioni di gestione, vedere la pagina [Panoramica delle operazioni di gestione di Istanza gestita di SQL di Azure](management-operations-overview.md).

## <a name="advanced-security-and-compliance"></a>Sicurezza e conformità avanzate

Istanza gestita di SQL offre funzionalità avanzate di sicurezza fornite dalla piattaforma Azure e il motore di database di SQL Server.

### <a name="security-isolation"></a>Isolamento della sicurezza

Istanza gestita di SQL offre isolamento di sicurezza aggiuntivo dagli altri tenant nella piattaforma Azure. L'isolamento di sicurezza include:

- [Implementazione della rete virtuale nativa](connectivity-architecture-overview.md) e connettività all'ambiente locale mediante Azure ExpressRoute o Gateway VPN.
- In una distribuzione predefinita, l'endpoint SQL viene esposto solo tramite un indirizzo IP privato, che consente la connettività sicura da reti private di Azure o ibride.
- Tenant singolo con infrastruttura sottostante dedicata (calcolo, archiviazione).

Il diagramma seguente illustra diverse opzioni di connettività per le applicazioni:

![Disponibilità elevata](./media/sql-managed-instance-paas-overview/application-deployment-topologies.png)  

Per altre informazioni dettagliate sull'integrazione della rete virtuale e sull'applicazione di criteri di rete a livello di subnet, vedere [Architettura della rete virtuale per le istanze gestite](connectivity-architecture-overview.md) e [Connettere l'applicazione a un'istanza gestita](connect-application-instance.md).

> [!IMPORTANT]
> Inserire più istanze gestite nella stessa subnet, ogniqualvolta tale operazione è consentita dai requisiti di sicurezza, perché comporterà vantaggi aggiuntivi. La condivisione del percorso delle istanze nella stessa subnet semplificherà notevolmente la manutenzione dell'infrastruttura di rete e ridurrà il tempo di provisioning dell'istanza, perché una durata prolungata di provisioning è associata al costo della distribuzione della prima istanza gestita in una subnet.

### <a name="security-features"></a>Funzionalità di sicurezza

Istanza gestita di SQL di Azure fornisce un set di funzionalità di sicurezza avanzate che possono essere usate per proteggere i dati.

- Il [controllo di Istanza gestita di SQL](auditing-configure.md) tiene traccia degli eventi del database e li registra in un file di log di controllo nell'account di archiviazione di Azure. Il controllo consente di agevolare la conformità alle normative, comprendere le attività del database e ottenere informazioni su eventuali discrepanze e anomalie che potrebbero indicare problemi aziendali o sospette violazioni della sicurezza.
- Crittografia dei dati in transito: Istanza gestita di SQL protegge i dati fornendo la crittografia per i dati in transito tramite Transport Layer Security. Oltre alla sicurezza a livello di trasporto, Istanza gestita di SQL offre protezione per i dati sensibili in movimento, inattivi e durante l'elaborazione di query con [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted offre funzionalità di sicurezza dei dati per la protezione da violazioni che implicano il furto di dati critici. Con Always Encrypted, ad esempio, i numeri delle carte di credito sono sempre archiviati in forma crittografata nel database, anche durante l'elaborazione di query, e la decrittografia è consentita nella posizione di utilizzo da parte di personale o applicazioni autorizzati che devono elaborare tali dati.
- [Advanced Threat Protection](threat-detection-configure.md) è complementare al [controllo](auditing-configure.md) perché offre un livello aggiuntivo di informazioni sulla sicurezza integrata nel servizio, rilevando tentativi insoliti e potenzialmente dannosi di accesso ai database o uso degli stessi. L'utente viene avvisato di attività sospette, vulnerabilità potenziali e attacchi SQL injection, nonché di modelli anomali di accesso al database. Gli avvisi di Advanced Threat Protection possono essere visualizzati dal [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/), Includono dettagli sulle attività sospette e consigliano azioni per l'analisi e la mitigazione della minaccia.  
- La funzione [Maschera dati dinamica](/sql/relational-databases/security/dynamic-data-masking) limita l'esposizione dei dati sensibili, nascondendoli agli utenti senza privilegi. La maschera dati dinamica impedisce l'accesso non autorizzato ai dati sensibili consentendo di definire la quantità di dati sensibili da rivelare, con un impatto minimo sul livello dell'applicazione. Si tratta di una funzionalità di sicurezza basata su criteri che consente di nascondere i dati sensibili nel set di risultati di una query in campi di database designati, senza modificare i dati nel database.
- La [sicurezza a livello di riga](/sql/relational-databases/security/row-level-security) consente di controllare l'accesso alle righe in una tabella di database in base alle caratteristiche dell'utente che esegue una query, ad esempio l'appartenenza a un gruppo o il contesto di esecuzione. La sicurezza a livello di riga semplifica la progettazione e la codifica della sicurezza nell'applicazione. Consente di implementare limitazioni per l'accesso alle righe di dati, assicurando ad esempio che i collaboratori possano accedere solo alle righe di dati pertinenti per il proprio reparto o limitando l'accesso ai dati ai soli dati di pertinenti.
- La funzione [Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) esegue la crittografia dei file di dati di Istanza gestita di SQL, nota anche come crittografia dei dati inattivi. TDE esegue la crittografia e la decrittografia delle operazioni di I/O di file di dati e log in tempo reale. La crittografia usa una chiave di crittografia del database (DEK) che viene archiviata nel record di avvio del database per la disponibilità durante il ripristino. È possibile proteggere tutti i database in un'istanza gestita con la crittografia dei dati trasparente. TDE è la tecnologia di crittografia dei dati inattivi collaudata in SQL Server, richiesta da molti standard di conformità per la protezione in caso di furto di supporti di archiviazione.

La migrazione di un database crittografato in Istanza gestita di SQL è supportata tramite il Servizio Migrazione del database di Azure o il ripristino nativo. Se si decide di eseguire la migrazione di un database crittografato usando il ripristino nativo, la migrazione del certificato TDE esistente dall'istanza di SQL Server a Istanza gestita di SQL è un passaggio obbligatorio. Per altre informazioni sui vari metodi di migrazione, vedere [Migrazione di SQL Server a Istanza gestita di SQL](migrate-to-instance-from-sql-server.md).

## <a name="azure-active-directory-integration"></a>Integrazione di Azure Active Directory

Istanza gestita di SQL supporta i tradizionali account di accesso del motore di database di SQL server e gli account di accesso integrati con Azure AD. Le entità server (account di accesso) di Azure AD (**anteprima pubblica**) sono una versione cloud di Azure degli account di accesso ai database di Windows usati nell'ambiente locale. Le entità server (account di accesso) di Azure AD consentono di specificare utenti e gruppi dal tenant di Azure AD come entità principali reali con ambito nell'istanza, in grado di eseguire qualsiasi operazione a livello di istanza, comprese le query tra database all'interno della stessa istanza gestita.

È stata introdotta una nuova sintassi per creare le entità server (account di accesso) di Azure AD, ovvero **FROM EXTERNAL PROVIDER**. Per altre informazioni sulla sintassi, vedere <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a> e consultare l'articolo [Effettuare il provisioning di un amministratore di Azure Active Directory per Istanza gestita di SQL](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integrazione in Azure Active Directory e autenticazione a più fattori

Istanza gestita di SQL consente di gestire a livello centralizzato le identità degli utenti di database e altri servizi Microsoft grazie all'[integrazione in Azure Active Directory](../database/authentication-aad-overview.md). Questa funzionalità semplifica la gestione delle autorizzazioni e ottimizza la sicurezza. Azure Active Directory supporta la funzionalità [Multi-Factor Authentication](../database/authentication-mfa-ssms-configure.md) per aumentare la sicurezza di dati e applicazioni supportando al tempo stesso un processo Single Sign-On.

### <a name="authentication"></a>Authentication

Per autenticazione di Istanza gestita di SQL si intende il modo in cui l'utente dimostra la propria identità durante la connessione al database. Istanza gestita di SQL supporta due tipi di autenticazione:  

- **Autenticazione SQL**:

  Questo metodo di autenticazione usa nome utente e password.
- **Autenticazione di Azure Active Directory**:

  Questo metodo di autenticazione usa identità gestite da Azure Active Directory ed è supportato per domini gestiti e integrati. [Quando possibile](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode), usare l'autenticazione di Active Directory (sicurezza integrata).

### <a name="authorization"></a>Autorizzazione

Per autorizzazione si intendono le operazioni che l'utente può eseguire in un database in Istanza gestita di SQL di Azure, che sono controllate dalle appartenenze ai ruoli del database e dalle autorizzazioni a livello di oggetto dell'account utente. Istanza gestita di SQL ha le stesse funzionalità di autorizzazione di SQL Server 2017.

## <a name="database-migration"></a>Migrazione del database

Il servizio Istanza gestita di SQL è destinato a scenari utente con migrazione di massa di database da implementazioni di database locali o IaaS. Istanza gestita di SQL supporta diverse opzioni di migrazione di database:

### <a name="backup-and-restore"></a>Backup e ripristino  

L'approccio di migrazione sfrutta i backup di SQL per l'archiviazione BLOB di Azure. I backup archiviati in un BLOB di archiviazione di Azure possono essere ripristinati direttamente in un'istanza gestita usando il [comando T-SQL RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current&preserve-view=true).

- Per una guida di avvio rapido su come ripristinare il file di backup di database Wide World Importers - Standard, vedere [Restore a backup file to a managed instance](restore-sample-database-quickstart.md) (Ripristinare un file di backup in un'istanza gestita). Questo argomento di avvio rapido mostra che è necessario caricare un file di backup in un archivio BLOB di Azure e proteggerlo usando una chiave di firma di accesso condiviso.
- Per informazioni sul ripristino dall'URL, vedere [Ripristino nativo da URL](migrate-to-instance-from-sql-server.md#native-restore-from-url).

> [!IMPORTANT]
> I backup da un'istanza gestita possono essere ripristinati solo in un'altra istanza gestita. Non possono essere ripristinati in un'istanza di SQL Server o nel database SQL di Azure.

### <a name="database-migration-service"></a>Servizio Migrazione del database

Il Servizio Migrazione del database di Azure è un servizio completamente gestito, progettato per abilitare le migrazioni senza interruzioni da più origini di database alle piattaforme di dati di Azure con tempi di inattività minimi. Questo servizio semplifica le attività necessarie per spostare database di SQL Server e di terze parti nel database SQL di Azure, in Istanza gestita di SQL di Azure e in SQL Server nella macchina virtuale di Azure. Vedere [Come eseguire la migrazione del database locale in Istanza gestita di SQL usando il Servizio Migrazione del database](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Funzionalità di SQL supportate

Istanza gestita di SQL mira a offrire una compatibilità della superficie di attacco prossima al 100% con l'ultima versione di SQL Server tramite un piano di rilascio a fasi. Per un elenco di confronto delle funzionalità, vedere [Confronto tra le funzionalità di Istanza gestita di SQL](../database/features-comparison.md) e per un elenco delle differenze di T-SQL in Istanza gestita di SQL rispetto a SQL Server, vedere [Differenze di T-SQL tra Istanza gestita di SQL e SQL Server](transact-sql-tsql-differences-sql-server.md).

Istanza gestita di SQL supporta la compatibilità con le versioni precedenti per i database di SQL Server 2008. È supportata la migrazione diretta da server di database di SQL Server 2005 e il livello di compatibilità per i database di SQL Server 2005 di cui viene eseguita la migrazione viene aggiornato a SQL Server 2008.
  
Il diagramma seguente illustra la compatibilità della superficie di attacco in Istanza gestita di SQL:  

![Compatibilità della superficie di attacco](./media/sql-managed-instance-paas-overview/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-sql-managed-instance"></a>Differenze principali tra SQL Server in locale e Istanza gestita di SQL

Istanza gestita di SQL offre vantaggi derivanti dall'essere sempre aggiornati nel cloud, mentre alcune funzionalità di SQL Server possono essere obsolete oppure venire ritirate o sostituite con alternative. In alcuni casi specifici gli strumenti devono riconoscere che una determinata funzionalità opera in modo leggermente diverso o che il servizio è in esecuzione in un ambiente di cui non si ha il controllo completo.

Alcune differenze principali:

- La disponibilità elevata è predefinita e preconfigurata tramite una tecnologia simile a [Gruppi di disponibilità Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Sono disponibili solo backup automatizzati e ripristino temporizzato. I clienti possono avviare backup `copy-only` che non interferiscono con la catena di backup automatica.
- L'uso di percorsi fisici completi non è supportato, quindi tutti gli scenari corrispondenti devono essere supportati in modo diverso: RESTORE DB non supporta WITH MOVE, CREATE DB non consente i percorsi fisici, BULK INSERT funziona solo con i BLOB di Azure e così via.
- Istanza gestita di SQL supporta l'[autenticazione di Azure AD](../database/authentication-aad-overview.md) come alternativa cloud all'autenticazione di Windows.
- Istanza gestita di SQL gestisce automaticamente i filegroup XTP e i file per i database contenenti oggetti di OLTP in memoria.
- Istanza gestita di SQL supporta SQL Server Integration Services e può ospitare un catalogo SSIS, ovvero SSISDB, che archivia i pacchetti SSIS, che però vengono eseguiti in un'istanza gestita di Azure-SSIS Integration Runtime in Azure Data Factory. Vedere [Creare Azure-SSIS IR in Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Per confrontare le funzionalità SSIS, vedere [Confrontare il database SQL con Istanza gestita di SQL](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).

### <a name="administration-features"></a>Funzionalità di amministrazione

Istanza gestita di SQL consente agli amministratori di sistema di risparmiare tempo nelle attività amministrative perché vengono automatizzate o semplificate dal servizio. Ad esempio, l'[installazione del sistema operativo/sistema di gestione di database relazionali e l'applicazione di patch](../database/high-availability-sla.md), il [ridimensionamento dinamico e la configurazione delle istanze](../database/single-database-scale.md), i [backup](../database/automated-backups-overview.md), la [replica di database](replication-between-two-instances-configure-tutorial.md) (inclusi i database di sistema), la [configurazione con disponibilità elevata](../database/high-availability-sla.md) e la configurazione di flussi di dati di [monitoraggio delle prestazioni](../../azure-monitor/insights/azure-sql.md) e dell'integrità.

Per altre informazioni, vedere [un elenco di funzionalità di Istanza gestita di SQL supportate e non supportate](../database/features-comparison.md) e [Differenze di T-SQL tra Istanza gestita di SQL e SQL Server](transact-sql-tsql-differences-sql-server.md).

### <a name="programmatically-identify-a-managed-instance"></a>Identificare un'istanza gestita a livello di codice

La tabella seguente mostra diverse proprietà, accessibili tramite Transact-SQL, che è possibile usare per rilevare se l'applicazione funziona con Istanza gestita di SQL e recuperare proprietà importanti.

|Proprietà|Valore|Commento|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Questo valore è uguale a quello del database SQL. Questo **non** si riferisce alla versione 12 (SQL Server 2014) del motore SQL. Istanza gestita di SQL esegue sempre la versione stabile più recente del motore SQL, che è uguale o successiva alla versione RTM più recente disponibile di SQL Server.  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|Questo valore è uguale a quello del database SQL.|
|`SERVERPROPERTY('EngineEdition')`|8|Questo valore identifica l'istanza gestita in modo univoco.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Nome DNS completo dell'istanza nel formato seguente:`<instanceName>`.`<dnsPrefix>`.database.Windows.net, dove `<instanceName>` è il nome fornito dal cliente, mentre `<dnsPrefix>` è la parte del nome generata automaticamente che garantisce l'univocità del DNS globale (ad esempio "wcus17662feb9ce98")|Esempio: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come creare la prima istanza gestita, vedere [Guida di avvio rapido](instance-create-quickstart.md).
- Per un elenco di confronto delle funzionalità, vedere [Confronto tra le funzionalità: database SQL di Azure e SQL Server](../database/features-comparison.md).
- Per altre informazioni sulla configurazione della rete virtuale, vedere [Configurazione di una rete virtuale per Istanza gestita di SQL](connectivity-architecture-overview.md).
- Per una guida di avvio rapido sulla creazione di un'istanza gestita e il ripristino di un database da un file di backup, vedere [Creare un'istanza gestita](instance-create-quickstart.md).
- Per un'esercitazione sull'uso del Servizio Migrazione del database di Azure, vedere [Migrazione a Istanza gestita di SQL con il Servizio Migrazione del database](../../dms/tutorial-sql-server-to-managed-instance.md).
- Per informazioni sul monitoraggio avanzato delle prestazioni del database dell'istanza gestita di SQL con intelligence predefinita per la risoluzione dei problemi, vedere [Monitorare l'istanza gestita di SQL di Azure usando Analisi SQL di Azure](../../azure-monitor/insights/azure-sql.md).
- Per informazioni sui prezzi, vedere [Database SQL - Prezzi](https://azure.microsoft.com/pricing/details/sql-database/managed/).
