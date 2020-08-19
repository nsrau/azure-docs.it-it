---
title: Che cos'è Istanza gestita SQL di Azure?
description: Informazioni sul modo in cui Azure SQL Istanza gestita offre una compatibilità quasi al 100% con il motore di database di SQL Server (Enterprise Edition) più recente
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
ms.date: 08/14/2020
ms.openlocfilehash: 72d0745e5a885ddbc57a9a849a7537a40e0b1215
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590065"
---
# <a name="what-is-azure-sql-managed-instance"></a>Che cos'è Istanza gestita SQL di Azure?
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Istanza gestita è il servizio di database cloud intelligente e scalabile che combina la più ampia compatibilità del motore di database SQL Server con tutti i vantaggi di una piattaforma distribuita come servizio completamente gestita e sempreverde. SQL Istanza gestita ha una compatibilità quasi al 100% con il motore di database di SQL Server (Enterprise Edition) più recente, offrendo un'implementazione di [rete virtuale nativa (VNet)](../../virtual-network/virtual-networks-overview.md) che risolve i problemi di sicurezza più comuni e un [modello aziendale](https://azure.microsoft.com/pricing/details/sql-database/) favorevole per i clienti esistenti di SQL Server. SQL Istanza gestita consente ai clienti SQL Server esistenti di trasferire e spostare le applicazioni locali nel cloud con modifiche minime all'applicazione e al database. Allo stesso tempo, SQL Istanza gestita conserva tutte le funzionalità di PaaS (applicazione automatica di patch e aggiornamenti della versione, [backup automatici](../database/automated-backups-overview.md), [disponibilità elevata](../database/high-availability-sla.md)) che riducono drasticamente il sovraccarico di gestione e il TCO.

> [!IMPORTANT]
> Per un elenco delle aree in cui sono attualmente disponibili SQL Istanza gestita, vedere [aree supportate](resource-limits.md#supported-regions).

Il diagramma seguente illustra le funzionalità principali di SQL Istanza gestita:

![Funzionalità principali](./media/sql-managed-instance-paas-overview/key-features.png)

Il Istanza gestita SQL di Azure è progettato per i clienti che desiderano eseguire la migrazione di un numero elevato di app da un ambiente locale o IaaS, autonomo o ISV fornito a un ambiente cloud PaaS completamente gestito, con il minor sforzo possibile per la migrazione. Grazie al servizio di [migrazione dei dati di Azure](../../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance)completamente automatizzato, i clienti possono sollevare e spostare l'istanza di SQL Server esistente a SQL istanza gestita, che offre la compatibilità con SQL Server e l'isolamento completo delle istanze del cliente con supporto VNet nativo.  Con Software Assurance puoi scambiare le tue licenze esistenti con tariffe scontate in SQL Istanza gestita usando il [vantaggio Azure Hybrid per SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). SQL Istanza gestita è la migliore destinazione di migrazione nel cloud per SQL Server istanze che richiedono una sicurezza elevata e una superficie di programmabilità avanzata.

## <a name="key-features-and-capabilities"></a>Funzionalità e le caratteristiche chiave

SQL Istanza gestita combina le funzionalità migliori disponibili sia nel database SQL di Azure che nel motore di database di SQL Server.

> [!IMPORTANT]
> SQL Istanza gestita viene eseguito con tutte le funzionalità della versione più recente di SQL Server, incluse le operazioni online, le correzioni automatiche dei piani e altri miglioramenti delle prestazioni aziendali. Un confronto delle funzionalità disponibili è illustrato in confronto tra le [funzionalità: Azure SQL istanza gestita rispetto a SQL Server](../database/features-comparison.md).

| **Vantaggi di PaaS** | **Continuità aziendale** |
| --- | --- |
|Acquisto e gestione di hardware non necessari <br>Nessun sovraccarico per la gestione dell'infrastruttura sottostante <br>Provisioning rapido e scalabilità del servizio <br>Applicazione automatica di patch e aggiornamento della versione <br>Integrazione con altri servizi dati PaaS |Contratto di servizio relativo al tempo di attività 99,99%  <br>[Disponibilità elevata](../database/high-availability-sla.md) incorporata <br>Dati protetti con [backup automatici](../database/automated-backups-overview.md) <br>Periodo di conservazione dei backup configurabile dal cliente <br>[Backup](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) avviati dall'utente <br>Funzionalità [di ripristino temporizzato del database](../database/recovery-using-backups.md#point-in-time-restore) |
|**Sicurezza e conformità** | **Gestione**|
|Ambiente isolato ([integrazione della rete virtuale](connectivity-architecture-overview.md), servizio a tenant singolo, calcolo e archiviazione dedicati) <br>[Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Autenticazione Azure Active Directory (Azure ad)](../database/authentication-aad-overview.md), supporto per Single Sign-on <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Entità di Azure AD server (account di accesso)</a>  <br>Rispetta gli standard di conformità del database SQL di Azure <br>[Controllo SQL](auditing-configure.md) <br>[Advanced Threat Protection](threat-detection-configure.md) |API di Azure Resource Manager per l'automazione del provisioning e della scalabilità del servizio <br>Funzionalità del portale di Azure per provisioning e scalabilità del servizio manuali <br>Servizio di migrazione dei dati

> [!IMPORTANT]
> Istanza gestita SQL di Azure è stata certificata in base a diversi standard di conformità. Per ulteriori informazioni, vedere le [offerte di conformità Microsoft Azure](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuideV3?command=Download&downloadType=Document&downloadId=44bbae63-bf4d-4e3b-9d3d-c96fb25ec363&tab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb&docTab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb_FAQ_and_White_Papers), in cui è possibile trovare l'elenco più aggiornato delle certificazioni di conformità SQL istanza gestita, elencate in **database SQL**.

Le funzionalità principali di SQL Istanza gestita sono illustrate nella tabella seguente:

|Funzionalità | Descrizione|
|---|---|
| SQL Server versione/compilazione | Motore di database SQL Server (versione stabile più recente) |
| Backup automatici gestiti | Sì |
| Monitoraggio predefinito e metriche dell'istanza e del database | Sì |
| Applicazione automatica di patch software | Sì |
| Funzionalità del motore di database più recenti | Sì |
| Numero di file di dati (RIGHE) per il database | Più elementi |
| Numero di file di log (LOG) per il database | 1 |
| Rete virtuale: distribuzione di Azure Resource Manager | Sì |
| Rete virtuale: modello di distribuzione classica | No |
| Supporto del portale | Sì|
| Integration Services (SSIS) incorporato | No - SSIS fa parte di [Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| Analysis Services (SSAS) incorporato | No - SSAS fa parte di una [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) distinta |
| Reporting Services (SSRS) incorporato | Non usare invece [Power bi report impaginati](https://docs.microsoft.com/power-bi/paginated-reports/paginated-reports-report-builder-power-bi) o ospitare SSRS in una macchina virtuale di Azure. Mentre SQL Istanza gestita non è in grado di eseguire SSRS come servizio, può ospitare i [database di catalogo di SSRS](https://docs.microsoft.com/sql/reporting-services/install-windows/ssrs-report-server-create-a-report-server-database#database-server-version-requirements) per un server di report installato in una macchina virtuale di Azure, usando l'autenticazione di SQL Server. |
|||

## <a name="vcore-based-purchasing-model"></a>Modello di acquisto basato su vCore

Il [modello di acquisto basato su vCore](../database/service-tiers-vcore.md) per SQL istanza gestita offre flessibilità, controllo, trasparenza e un modo semplice per tradurre i requisiti del carico di lavoro locale nel cloud. Questo modello consente di cambiare le risorse di calcolo, memoria e archiviazione in base ai requisiti dei carichi di lavoro. Il modello vCore è inoltre idoneo per un risparmio fino al 55% con la [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) per SQL Server.

Nel modello vCore è possibile scegliere tra generazioni di hardware.

- Le CPU logiche **Gen4** sono basate su processori Intel® E5-2673 V3 (Haswell) a 2,4 GHz, SSD collegati, core fisici, 7 GB di RAM per core e dimensioni di calcolo comprese tra 8 e 24 vcore.
- Le CPU logiche **quinta generazione** sono basate su processori Intel® E5-2673 V4 (Broadwell) 2,3 GHz, Intel® SP-8160 (Skylake) e Intel® 8272CL (Cascade Lake) 2,5 GHz, unità SSD Fast NVMe, core logico con Hyper-Threading e dimensioni di calcolo comprese tra 4 e 80 core.

Trovare altre informazioni sulla differenza tra le generazioni hardware nei [limiti delle risorse di SQL istanza gestita](resource-limits.md#hardware-generation-characteristics).

## <a name="service-tiers"></a>Livelli di servizio

SQL Istanza gestita è disponibile in due livelli di servizio:

- **Utilizzo generico**: progettato per applicazioni con requisiti di latenza di I/O e prestazioni tipici.
- **Business critical**: progettato per applicazioni con requisiti di latenza di I/O Bassi e l'effetto minimo delle operazioni di manutenzione sottostanti sul carico di lavoro.

Entrambi i livelli di servizio garantiscono una disponibilità del 99,99% e consentono di selezionare le dimensioni di archiviazione e capacità di calcolo in modo indipendente. Per altre informazioni sull'architettura a disponibilità elevata di Istanza gestita SQL di Azure, vedere [disponibilità elevata e istanza gestita SQL di Azure](../database/high-availability-sla.md).

### <a name="general-purpose-service-tier"></a>Livello di servizio Utilizzo generico

Nell'elenco seguente vengono descritte le caratteristiche principali del livello di servizio per utilizzo generico:

- Progettato per la maggior parte delle applicazioni aziendali con requisiti di prestazioni tipici
- Archiviazione BLOB di Azure ad alte prestazioni (8 TB)
- [Disponibilità elevata](../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-availability) predefinita basata sull'archiviazione BLOB di Azure affidabile e su [Azure Service Fabric](../../service-fabric/service-fabric-overview.md)

Per ulteriori informazioni, vedere [livello di archiviazione nelle](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) procedure consigliate per il livello di per utilizzo generico e le [prestazioni di archiviazione e considerazioni per SQL istanza gestita (per utilizzo generico)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

Per altre informazioni sulla differenza tra i livelli di servizio, vedere [limiti delle risorse di SQL istanza gestita](resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Livello di servizio business critical

Il livello di servizio business critical è compilato per le applicazioni con requisiti di I/O elevati. Offre la massima resilienza agli errori usando diverse repliche isolate.

L'elenco seguente descrive le caratteristiche principali del livello di servizio per business critical:

- Progettato per applicazioni aziendali con le prestazioni e i requisiti di disponibilità più elevati
- Viene fornito con l'archiviazione SSD locale estremamente rapida (fino a 1 TB nella quarta generazione e fino a 4 TB nella quinta generazione)
- [Disponibilità elevata](../database/high-availability-sla.md#premium-and-business-critical-service-tier-availability) predefinita basata su [Gruppi di disponibilità Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) e [Azure Service Fabric](../../service-fabric/service-fabric-overview.md)
- Replica aggiuntiva di [database](../database/read-scale-out.md) di sola lettura incorporata che può essere usata per la creazione di report e altri carichi di lavoro di sola lettura
- [OLTP in memoria](../in-memory-oltp-overview.md) che può essere usato per il carico di lavoro con requisiti di prestazioni elevate  

Trovare altre informazioni sulle differenze tra i livelli di servizio in [SQL istanza gestita limiti delle risorse](resource-limits.md#service-tier-characteristics).

## <a name="management-operations"></a>Operazioni di gestione

Istanza gestita SQL di Azure fornisce operazioni di gestione che è possibile usare per distribuire automaticamente nuove istanze gestite, aggiornare le proprietà dell'istanza ed eliminare istanze quando non sono più necessarie. Una spiegazione dettagliata delle operazioni di gestione è reperibile nella pagina [Panoramica delle operazioni di gestione delle istanze gestite](management-operations-overview.md) .

## <a name="advanced-security-and-compliance"></a>Sicurezza e conformità avanzate

SQL Istanza gestita include funzionalità di sicurezza avanzate fornite dalla piattaforma Azure e dal motore di database di SQL Server.

### <a name="security-isolation"></a>Isolamento della sicurezza

SQL Istanza gestita fornisce un isolamento di sicurezza aggiuntivo da altri tenant della piattaforma Azure. L'isolamento di sicurezza include:

- L' [implementazione della rete virtuale nativa](connectivity-architecture-overview.md) e la connettività all'ambiente locale tramite il gateway VPN o ExpressRoute di Azure.
- In una distribuzione predefinita, l'endpoint SQL viene esposto solo tramite un indirizzo IP privato, consentendo una connettività sicura da reti private di Azure o ibride.
- Tenant singolo con infrastruttura sottostante dedicata (calcolo, archiviazione).

Il diagramma seguente illustra diverse opzioni di connettività per le applicazioni:

![Disponibilità elevata](./media/sql-managed-instance-paas-overview/application-deployment-topologies.png)  

Per altre informazioni dettagliate sull'integrazione della rete virtuale e sull'applicazione di criteri di rete a livello di subnet, vedere [Architettura della rete virtuale per le istanze gestite](connectivity-architecture-overview.md) e [Connettere l'applicazione a un'istanza gestita](connect-application-instance.md).

> [!IMPORTANT]
> Inserire più istanze gestite nella stessa subnet, ovunque consentite dai requisiti di sicurezza, in modo da ottenere vantaggi aggiuntivi. Con la condivisione percorso delle istanze nella stessa subnet si semplifica notevolmente la manutenzione dell'infrastruttura di rete e si riducono i tempi di provisioning dell'istanza, perché una durata provisioning prolungata è associata al costo della distribuzione della prima istanza gestita in una subnet.

### <a name="security-features"></a>Funzionalità di sicurezza

Azure SQL Istanza gestita offre un set di funzionalità di sicurezza avanzate che possono essere usate per proteggere i dati.

- Il [controllo SQL istanza gestita](auditing-configure.md) tiene traccia degli eventi del database e li scrive in un file di log di controllo inserito nell'account di archiviazione di Azure. Il controllo consente di agevolare la conformità alle normative, comprendere le attività del database e ottenere informazioni su eventuali discrepanze e anomalie che potrebbero indicare problemi aziendali o sospette violazioni della sicurezza.
- La crittografia dei dati in Motion-SQL Istanza gestita protegge i dati fornendo la crittografia dei dati in movimento usando Transport Layer Security. Oltre a Transport Layer Security, SQL Istanza gestita offre la protezione dei dati sensibili in fase di volo, inattivi e durante l'elaborazione di query con [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted offre protezione dei dati contro le violazioni che interessano il furto di dati critici. Con Always Encrypted, ad esempio, i numeri delle carte di credito sono sempre archiviati in forma crittografata nel database, anche durante l'elaborazione di query, e la decrittografia è consentita nella posizione di utilizzo da parte di personale o applicazioni autorizzati che devono elaborare tali dati.
- [Advanced Threat Protection](threat-detection-configure.md) è complementare al [controllo](auditing-configure.md) fornendo un ulteriore livello di intelligence per la sicurezza incorporato nel servizio che rileva tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database. L'utente viene avvisato di attività sospette, vulnerabilità potenziali e attacchi SQL injection, nonché di modelli anomali di accesso al database. Gli avvisi di Advanced Threat Protection possono essere visualizzati dal [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/), Forniscono informazioni dettagliate sulle attività sospette e consigliano azioni su come analizzare e mitigare la minaccia.  
- La [maschera dati dinamica](/sql/relational-databases/security/dynamic-data-masking) limita l'esposizione dei dati sensibili mediante la maschera a utenti senza privilegi. La maschera dati dinamica impedisce l'accesso non autorizzato ai dati sensibili consentendo di definire la quantità di dati sensibili da rivelare, con un impatto minimo sul livello dell'applicazione. Si tratta di una funzionalità di sicurezza basata su criteri che consente di nascondere i dati sensibili nel set di risultati di una query in campi di database designati, senza modificare i dati nel database.
- La [sicurezza a livello di riga](/sql/relational-databases/security/row-level-security) consente di controllare l'accesso alle righe in una tabella di database in base alle caratteristiche dell'utente che esegue una query, ad esempio l'appartenenza a un gruppo o il contesto di esecuzione. RLS semplifica la progettazione e la codifica della sicurezza nell'applicazione. Consente di implementare limitazioni per l'accesso alle righe di dati, assicurando ad esempio che i collaboratori possano accedere solo alle righe di dati pertinenti per il proprio reparto o limitando l'accesso ai dati ai soli dati di pertinenti.
- [Transparent Data Encryption (Transparent Data Encryption)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) crittografa i file di dati di SQL istanza gestita, noti come crittografia dei dati inattivi. TDE esegue la crittografia e la decrittografia delle operazioni di I/O di file di dati e log in tempo reale. La crittografia usa una chiave di crittografia del database (DEK) che viene archiviata nel record di avvio del database per la disponibilità durante il ripristino. È possibile proteggere tutti i database in un'istanza gestita con la crittografia dei dati trasparente. Transparent Data Encryption è una tecnologia di crittografia inattiva e collaudata in SQL Server richiesta da molti standard di conformità per la protezione da furti di supporti di archiviazione.

La migrazione di un database crittografato a SQL Istanza gestita è supportata tramite il servizio migrazione del database di Azure o il ripristino nativo. Se si prevede di eseguire la migrazione di un database crittografato utilizzando il ripristino nativo, la migrazione del certificato Transparent Data Encryption esistente dall'istanza SQL Server a SQL Istanza gestita è un passaggio obbligatorio. Per ulteriori informazioni sulle opzioni di migrazione, vedere [SQL Server migrazione a SQL istanza gestita](migrate-to-instance-from-sql-server.md).

## <a name="azure-active-directory-integration"></a>Integrazione di Azure Active Directory

SQL Istanza gestita supporta gli account di accesso e gli account di accesso tradizionali SQL Server motore di database integrati con Azure AD. Azure AD entità del server (account di accesso) (**anteprima pubblica**) sono una versione cloud di Azure di account di accesso al database locale che si sta usando nell'ambiente locale. Azure AD entità server (account di accesso) consentono di specificare gli utenti e i gruppi dal tenant di Azure AD come entità con ambito di istanza reale, in grado di eseguire qualsiasi operazione a livello di istanza, incluse le query tra database all'interno della stessa istanza gestita.

Viene introdotta una nuova sintassi per creare Azure AD entità server (account di accesso), **dal provider esterno**. Per altre informazioni sulla sintassi, vedere <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">creare un account di accesso</a>ed esaminare l'articolo eseguire il [provisioning di un amministratore Azure Active Directory per SQL istanza gestita](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) .

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integrazione in Azure Active Directory e autenticazione a più fattori

SQL Istanza gestita consente di gestire centralmente le identità degli utenti del database e di altri servizi Microsoft con l' [integrazione Azure Active Directory](../database/authentication-aad-overview.md). Questa funzionalità semplifica la gestione delle autorizzazioni e ottimizza la sicurezza. Azure Active Directory supporta [l'autenticazione](../database/authentication-mfa-ssms-configure.md) a più fattori per aumentare la sicurezza dei dati e delle applicazioni, supportando al tempo stesso un processo Single Sign-on.

### <a name="authentication"></a>Authentication

Per autenticazione di SQL Istanza gestita si intende il modo in cui gli utenti dimostrano la propria identità durante la connessione al database SQL Istanza gestita supporta due tipi di autenticazione:  

- **Autenticazione SQL**:

  Questo metodo di autenticazione usa nome utente e password.
- **Autenticazione di Azure Active Directory**:

  Questo metodo di autenticazione usa identità gestite da Azure Active Directory ed è supportato per domini gestiti e integrati. [Quando possibile](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode), usare l'autenticazione di Active Directory (sicurezza integrata).

### <a name="authorization"></a>Autorizzazione

L'autorizzazione si riferisce alle operazioni che un utente può eseguire in un database in Azure SQL Istanza gestita ed è controllata dalle appartenenze ai ruoli del database e dalle autorizzazioni a livello di oggetto dell'account utente. SQL Istanza gestita ha le stesse funzionalità di autorizzazione di SQL Server 2017.

## <a name="database-migration"></a>Migrazione del database

SQL Istanza gestita è destinato a scenari utente con migrazione di database di massa da implementazioni di database locali o IaaS. SQL Istanza gestita supporta diverse opzioni di migrazione del database:

### <a name="backup-and-restore"></a>Backup e ripristino  

L'approccio di migrazione sfrutta i backup di SQL per l'archiviazione BLOB di Azure. I backup archiviati in un BLOB di archiviazione di Azure possono essere ripristinati direttamente in un'istanza gestita usando il [comando T-SQL RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current).

- Per una guida introduttiva che illustra come ripristinare il file di backup di database Wide World Importers, vedere [ripristinare un file di backup in un'istanza gestita](restore-sample-database-quickstart.md). Questa Guida introduttiva mostra che è necessario caricare un file di backup nell'archiviazione BLOB di Azure e proteggerlo usando una chiave di firma di accesso condiviso (SAS).
- Per informazioni sul ripristino dall'URL, vedere [Ripristino nativo da URL](migrate-to-instance-from-sql-server.md#native-restore-from-url).

> [!IMPORTANT]
> I backup da un'istanza gestita possono essere ripristinati solo in un'altra istanza gestita. Non possono essere ripristinati in un'istanza SQL Server o nel database SQL di Azure.

### <a name="database-migration-service"></a>Servizio Migrazione del database

Il Servizio Migrazione del database di Azure è un servizio completamente gestito, progettato per abilitare le migrazioni senza interruzioni da più origini di database alle piattaforme di dati di Azure con tempi di inattività minimi. Questo servizio semplifica le attività necessarie per spostare i database esistenti di terze parti e di SQL Server nel database SQL di Azure, in Azure SQL Istanza gestita e SQL Server in una macchina virtuale di Azure. Vedere [come eseguire la migrazione del database locale a SQL istanza gestita usando il servizio migrazione del database](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Funzionalità di SQL supportate

SQL Istanza gestita mira a offrire una compatibilità della superficie di attacco prossima al 100% con la versione più recente del SQL Server tramite un piano di rilascio a fasi. Per un elenco di funzionalità e di confronto, vedere [confronto delle funzionalità di sql istanza gestita](../database/features-comparison.md)e per un elenco delle differenze di t-SQL in SQL istanza gestita rispetto a SQL Server, vedere [SQL istanza gestita t-SQL differences from SQL Server](transact-sql-tsql-differences-sql-server.md).

SQL Istanza gestita supporta la compatibilità con le versioni precedenti dei database SQL Server 2008. È supportata la migrazione diretta da server di database SQL Server 2005 e il livello di compatibilità per i database SQL Server 2005 migrati viene aggiornato SQL Server 2008.
  
Il diagramma seguente illustra la compatibilità della superficie di attacco in SQL Istanza gestita:  

![Migrazione](./media/sql-managed-instance-paas-overview/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-sql-managed-instance"></a>Differenze principali tra SQL Server locali e SQL Istanza gestita

SQL Istanza gestita risulta sempre aggiornato nel cloud, il che significa che alcune funzionalità di SQL Server potrebbero essere obsolete, essere ritirate o avere alternative. Esistono casi specifici in cui gli strumenti devono riconoscere che una particolare funzionalità funziona in modo leggermente diverso o che il servizio è in esecuzione in un ambiente che non è completamente controllato.

Alcune differenze principali:

- La disponibilità elevata è incorporata e preconfigurata usando una tecnologia simile a [gruppi di disponibilità always on](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Sono disponibili solo backup automatici e ripristino temporizzato. I clienti possono avviare `copy-only` backup che non interferiscono con la catena di backup automatico.
- La specifica di percorsi fisici completi non è supportata, pertanto tutti gli scenari corrispondenti devono essere supportati in modo diverso: RESTOre DB non supporta WITH MOVE, CREATE DB non consente percorsi fisici, BULK INSERT funziona solo con i BLOB di Azure e così via.
- SQL Istanza gestita supporta [l'autenticazione Azure ad](../database/authentication-aad-overview.md) come alternativa cloud all'autenticazione di Windows.
- SQL Istanza gestita gestisce automaticamente i file e i filegroup XTP per i database contenenti oggetti OLTP in memoria.
- SQL Istanza gestita supporta SQL Server Integration Services (SSIS) ed è in grado di ospitare un catalogo SSIS (SSISDB) che archivia i pacchetti SSIS, ma viene eseguito in un Azure-SSIS Integration Runtime gestito (IR) in Azure Data Factory. Vedere [creare Azure-SSIS IR in data factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Per confrontare le funzionalità SSIS, vedere [confrontare il database SQL con sql istanza gestita](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).

### <a name="administration-features"></a>Funzionalità di amministrazione

SQL Istanza gestita consente agli amministratori di sistema di dedicare meno tempo alle attività amministrative poiché il servizio li esegue automaticamente o semplifica notevolmente tali attività. Ad esempio, [installazione e patch del sistema operativo/RDBMS](../database/high-availability-sla.md), [ridimensionamento e configurazione di istanze dinamiche](../database/single-database-scale.md), [backup](../database/automated-backups-overview.md), [replica di database](replication-between-two-instances-configure-tutorial.md) (inclusi i database di sistema), [configurazione della disponibilità elevata](../database/high-availability-sla.md)e configurazione di flussi di dati di monitoraggio dell'integrità e [delle prestazioni](../../azure-monitor/insights/azure-sql.md) .

Per ulteriori informazioni, vedere [l'elenco delle funzionalità di sql istanza gestita supportate e](../database/features-comparison.md)non supportate e le [differenze di T-sql tra sql istanza gestita e SQL Server](transact-sql-tsql-differences-sql-server.md).

### <a name="programmatically-identify-a-managed-instance"></a>Identificare un'istanza gestita a livello di codice

La tabella seguente illustra diverse proprietà, accessibili tramite Transact-SQL, che è possibile usare per rilevare che l'applicazione funziona con SQL Istanza gestita e recuperare le proprietà importanti.

|Proprietà|valore|Commento|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Questo valore è uguale a quello del database SQL. Questa operazione **non** indica il motore SQL versione 12 (SQL Server 2014). SQL Istanza gestita esegue sempre la versione più recente del motore SQL stabile, che è uguale o superiore alla versione RTM più recente disponibile di SQL Server.  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|Questo valore è uguale a quello del database SQL.|
|`SERVERPROPERTY('EngineEdition')`|8|Questo valore identifica l'istanza gestita in modo univoco.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Nome DNS completo dell'istanza nel formato seguente:`<instanceName>`.`<dnsPrefix>`.database.Windows.net, dove `<instanceName>` è il nome fornito dal cliente, mentre `<dnsPrefix>` è la parte del nome generata automaticamente che garantisce l'univocità del DNS globale (ad esempio "wcus17662feb9ce98")|Esempio: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come creare la prima istanza gestita, vedere [Guida introduttiva](instance-create-quickstart.md).
- Per un elenco di confronto delle funzionalità, vedere [Confronto tra le funzionalità: database SQL di Azure e SQL Server](../database/features-comparison.md).
- Per ulteriori informazioni sulla configurazione di VNet, vedere la pagina relativa alla [configurazione di SQL istanza gestita VNet](connectivity-architecture-overview.md).
- Per una guida introduttiva per la creazione di un'istanza gestita e il ripristino di un database da un file di backup, vedere [creare un'istanza gestita](instance-create-quickstart.md).
- Per un'esercitazione sull'uso del servizio migrazione del database di Azure per la migrazione, vedere la pagina relativa alla [migrazione istanza gestita SQL con il servizio migrazione del database](../../dms/tutorial-sql-server-to-managed-instance.md).
- Per il monitoraggio avanzato delle prestazioni del database SQL Istanza gestita con l'Intelligence per la risoluzione dei problemi incorporata, vedere [monitorare istanza gestita SQL di Azure con analisi SQL di Azure](../../azure-monitor/insights/azure-sql.md).
- Per informazioni sui prezzi, vedere [prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).
