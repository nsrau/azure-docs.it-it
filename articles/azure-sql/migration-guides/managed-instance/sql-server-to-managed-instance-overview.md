---
title: Panoramica della migrazione da SQL Server a SQL Istanza gestita
description: Informazioni sui diversi strumenti e opzioni disponibili per eseguire la migrazione dei database di SQL Server al Istanza gestita SQL di Azure.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 878d59445dde839ca9e702ac0c49af676e48a42f
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531191"
---
# <a name="migration-overview-sql-server-to-sql-managed-instance"></a>Panoramica della migrazione: SQL Server a SQL Istanza gestita
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

Informazioni sulle diverse opzioni e considerazioni relative alla migrazione per eseguire la migrazione del SQL Server al Istanza gestita SQL di Azure. 

È possibile eseguire la migrazione di SQL Server in esecuzione in locale o in: 

- SQL Server in macchine virtuali  
- Amazon Web Services (AWS) EC2 
- Servizio database relazionale di Amazon (AWS RDS) 
- Motore di calcolo (Google Cloud Platform-GCP)  
- SQL cloud per SQL Server (Google Cloud Platform-GCP) 

Per altri scenari, vedere la [Guida alla migrazione del database](https://datamigration.microsoft.com/). 

## <a name="overview"></a>Panoramica

[Azure SQL istanza gestita](../../managed-instance/sql-managed-instance-paas-overview.md) è un'opzione di destinazione consigliata per carichi di lavoro SQL Server che richiedono un servizio completamente gestito senza dover gestire le macchine virtuali o i sistemi operativi. SQL Istanza gestita ti permette di trasferire in Azure le tue applicazioni locali con modifiche minime all'applicazione o al database, con un isolamento completo delle istanze con supporto per la rete virtuale nativa (VNet). 

## <a name="considerations"></a>Considerazioni 

I fattori chiave da considerare quando si valutano le opzioni di migrazione dipendono da: 
- Numero di server e database
- Dimensioni dei database
- Tempo di inattività aziendale accettabile durante il processo di migrazione 

Uno dei principali vantaggi della migrazione di SQL Server a SQL Istanza gestita è che è possibile scegliere di eseguire la migrazione dell'intera istanza o solo di un subset di singoli database. Pianificare con attenzione l'inclusione di quanto segue nel processo di migrazione: 
- Tutti i database che devono essere posizionati nella stessa istanza 
- Oggetti a livello di istanza richiesti per l'applicazione, inclusi gli account di accesso, le credenziali, i processi e gli operatori di SQL Agent e i trigger a livello di server. 

> [!NOTE]
> Azure SQL Istanza gestita garantisce una disponibilità del 99,99% anche negli scenari critici, quindi il sovraccarico causato da alcune funzionalità in SQL MI non può essere disabilitato. Per ulteriori informazioni, vedere le [cause principali che potrebbero causare prestazioni diverse nel Blog di SQL Server e istanza gestita di Azure SQL](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) . 


## <a name="choose-appropriate-target"></a>Scegliere la destinazione appropriata

Alcune linee guida generali che consentono di scegliere il livello di servizio e le caratteristiche di SQL Istanza gestita più appropriati per soddisfare i requisiti di base per le [prestazioni](sql-server-to-managed-instance-performance-baseline.md): 

- Utilizzare la baseline utilizzo CPU per eseguire il provisioning di un'istanza gestita corrispondente al numero di core utilizzati dall'istanza di SQL Server. Potrebbe essere necessario scalare le risorse in base alle [caratteristiche di generazione dell'hardware](../../managed-instance/resource-limits.md#hardware-generation-characteristics). 
- Utilizzare la linea di base utilizzo memoria per scegliere un' [opzione vCore](../../managed-instance/resource-limits.md#service-tier-characteristics) che corrisponda in modo appropriato all'allocazione di memoria. 
- Usare la latenza i/o di base del sottosistema file per scegliere tra per utilizzo generico (latenza maggiore di 5 ms) e business critical (latenza inferiore a 3 ms) livelli di servizio. 
- Utilizzare la velocità effettiva di base per preallocare le dimensioni dei file di dati e di log per ottenere le prestazioni di i/o previste. 

È possibile scegliere risorse di calcolo e di archiviazione durante la distribuzione e quindi modificarle dopo aver usato il [portale di Azure](../../database/scale-resources.md) senza incorrere in tempi di inattività per l'applicazione. 

> [!IMPORTANT]
> Eventuali discrepanze nei [requisiti della rete virtuale dell'istanza gestita](../../managed-instance/connectivity-architecture-overview.md#network-requirements) possono impedire la creazione di nuove istanze o l'utilizzo di quelle esistenti. Altre informazioni sulla [creazione di nuove](../../managed-instance/virtual-network-subnet-create-arm-template.md)   reti e sulla configurazione di reti [esistenti](../../managed-instance/vnet-existing-add-subnet.md?branch=release-ignite-arc-data)   . 

### <a name="sql-server-vm-alternative"></a>Alternativa SQL Server VM

L'azienda può avere requisiti che rendono SQL Server le macchine virtuali di Azure una destinazione più adatta rispetto a Istanza gestita SQL di Azure. 

Se si applicano le condizioni seguenti all'azienda, provare a eseguire il passaggio a una macchina virtuale SQL Server: 

- Se è necessario l'accesso diretto al sistema operativo o file system, ad esempio per installare agenti di terze parti o personalizzati nella stessa macchina virtuale con SQL Server. 
- Se si ha una dipendenza rigorosa da funzionalità che non sono ancora supportate, ad esempio FileStream/FileTable, polibase e transazioni tra istanze. 
- Se è assolutamente necessario rimanere in una versione specifica di SQL Server (2012, ad esempio). 
- Se i requisiti di calcolo sono molto inferiori rispetto alle offerte di istanze gestite (un vCore, ad esempio) e il consolidamento dei database non è un'opzione accettabile. 


## <a name="migration-tools"></a>Strumenti di migrazione


Gli strumenti consigliati per la migrazione sono il Data Migration Assistant e il servizio migrazione del database di Azure. Sono disponibili anche altre opzioni di migrazione alternative. 

### <a name="recommended-tools"></a>Strumenti consigliati

La tabella seguente elenca gli strumenti di migrazione consigliati: 

|Tecnologia | Descrizione|
|---------|---------|
|[Servizio Migrazione del database di Azure (DMS)](../../../dms/tutorial-sql-server-to-managed-instance.md)  | Servizio di Azure di terze parti che supporta la migrazione in modalità offline per le applicazioni che possono permettersi tempi di inattività durante il processo di migrazione. A differenza della migrazione continua in modalità online, la migrazione in modalità offline esegue un ripristino unico di un backup completo del database dall'origine alla destinazione. | 
|[Backup e ripristino nativi](../../managed-instance/restore-sample-database-quickstart.md) | SQL Istanza gestita supporta il ripristino dei backup di database SQL Server nativi (file con estensione bak), rendendola l'opzione di migrazione più semplice per i clienti che possono fornire backup completi del database nell'archiviazione di Azure. Anche i backup completi e differenziali sono supportati e documentati nella [sezione asset di migrazione](#migration-assets) più avanti in questo articolo.| 
| | |

### <a name="alternative-tools"></a>Strumenti alternativi

La tabella seguente elenca gli strumenti di migrazione alternativi: 

|Tecnologia |Descrizione  |
|---------|---------|
|[Replica transazionale](../../managed-instance/replication-transactional-overview.md) | Replicare i dati dalle tabelle di database di origine SQL Server a SQL Istanza gestita fornendo un'opzione di migrazione del tipo di Sottoscrittore del server di pubblicazione mantenendo la coerenza transazionale. |  |
|[Copia bulk](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| L' [utilità del programma per la copia bulk (BCP)](/sql/tools/bcp-utility) copia i dati da un'istanza di SQL Server in un file di dati. Usare l'utilità BCP per esportare i dati dall'origine e importare il file di dati nel Istanza gestita SQL di destinazione.</br></br> Per le operazioni di copia bulk ad alta velocità per spostare i dati nel database SQL di Azure, è possibile usare [lo strumento per la copia bulk intelligente](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) per ottimizzare le velocità di trasferimento sfruttando le attività di copia parallele. | 
|[Importazione/esportazione guidata/BACPAC](../../database/database-import.md?tabs=azure-powershell)| [BacPac](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) è un file di Windows con un' `.bacpac` estensione che incapsula lo schema e i dati di un database. BACPAC può essere usato per esportare i dati da un SQL Server di origine e per importare il file di nuovo in Azure SQL Istanza gestita.  |  
|[Azure Data Factory](../../../data-factory/connector-azure-sql-managed-instance.md)| L' [attività di copia](../../../data-factory/copy-activity-overview.md) in Azure Data Factory esegue la migrazione dei dati da database SQL Server di origine a SQL istanza gestita usando connettori predefiniti e un [Integration Runtime](../../../data-factory/concepts-integration-runtime.md).</br> </br> ADF supporta un'ampia gamma di [connettori](../../../data-factory/connector-overview.md) per spostare i dati da origini SQL Server a SQL istanza gestita. |
| | |

## <a name="compare-migration-options"></a>Confrontare le opzioni di migrazione

Confrontare le opzioni di migrazione per scegliere il percorso appropriato alle esigenze aziendali. 

### <a name="recommended-options"></a>Opzioni consigliate

Nella tabella seguente vengono confrontate le opzioni di migrazione consigliate: 

|Opzione di migrazione  |Utilizzo  |Considerazioni  |
|---------|---------|---------|
|[Servizio Migrazione del database di Azure (DMS)](../../../dms/tutorial-sql-server-to-managed-instance.md) | -Migrare database singoli o più database su larga scala. </br> -Può supportare i tempi di inattività durante il processo di migrazione. </br> </br> Origini supportate: </br> -SQL Server (2005-2019) in locale o in una VM di Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP calcolo della macchina virtuale SQL Server |  -Le migrazioni su larga scala possono essere automatizzate tramite [PowerShell](../../../dms/howto-sql-server-to-azure-sql-mi-powershell.md). </br> -Il tempo necessario per completare la migrazione dipende dalle dimensioni del database e dal tempo di backup e ripristino. </br> -Può essere necessario un tempo di inattività sufficiente. |
|[Backup e ripristino nativi](../../managed-instance/restore-sample-database-quickstart.md) | -Migrare i singoli database delle applicazioni line-of-business.  </br> -Migrazione rapida e semplice senza uno strumento o un servizio di migrazione separato.  </br> </br> Origini supportate: </br> -SQL Server (2005-2019) in locale o in una VM di Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP calcolo della macchina virtuale SQL Server | -Il backup del database usa più thread per ottimizzare il trasferimento dei dati nell'archiviazione BLOB di Azure, ma le dimensioni del database e la larghezza di banda ISV possono avere effetti sulla velocità </br> -Il tempo di inattività deve adattarsi al tempo necessario per eseguire un backup completo e un ripristino, ovvero un'operazione di dimensione dei dati.| 
| | | |

### <a name="alternative-options"></a>Opzioni alternative

Nella tabella seguente vengono confrontate le opzioni di migrazione alternative: 

|Metodo/tecnologia |Utilizzo |Considerazioni  |
|---------|---------|---------|
|[Replica transazionale](../../managed-instance/replication-transactional-overview.md) | -Esegue la migrazione pubblicando continuamente le modifiche dalle tabelle di database di origine alle tabelle di database SQL Istanza gestita di destinazione. </br> -Migrazioni di database complete o parziali di tabelle selezionate (subset di database).  </br> </br> Origini supportate: </br> -SQL Server (2012-2019) con alcune limitazioni </br> -AWS EC2  </br> -GCP calcolo della macchina virtuale SQL Server | </br> -Il programma di installazione è relativamente complesso rispetto ad altre opzioni di migrazione.   </br> -Fornisce un'opzione di replica continua per eseguire la migrazione dei dati (senza portare i database offline).</br> -La replica transazionale presenta alcune limitazioni da considerare quando si configura il server di pubblicazione nel SQL Server di origine. Per altre informazioni, vedere [limitazioni relative alla pubblicazione di oggetti](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) .  </br> -È disponibile la funzionalità di [monitoraggio dell'attività di replica](/sql/relational-databases/replication/monitor/monitoring-replication) .    |
|[Copia bulk](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| -Migrazione di migrazioni di dati complete o parziali. </br> -Può contenere tempi di inattività. </br> </br> Origini supportate: </br> -SQL Server (2005-2019) in locale o in una VM di Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP calcolo della macchina virtuale SQL Server   | -Richiede tempi di inattività per l'esportazione dei dati dall'origine e l'importazione nella destinazione. </br> -I formati di file e i tipi di dati utilizzati nell'esportazione/importazione devono essere coerenti con gli schemi di tabella. |
|[Importazione/esportazione guidata/BACPAC](../../database/database-import.md)| -Migrare i singoli database delle applicazioni line-of-business. </br>-Adatto per database più piccoli.  </br>  Non richiede uno strumento o un servizio di migrazione separato. </br> </br> Origini supportate: </br> -SQL Server (2005-2019) in locale o in una VM di Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP calcolo della macchina virtuale SQL Server  |   </br> -Richiede tempi di inattività perché i dati devono essere esportati nell'origine e importati nella destinazione.   </br> -I formati di file e i tipi di dati utilizzati nell'esportazione/importazione devono essere coerenti con gli schemi di tabella per evitare errori di mancata corrispondenza del tipo di dati o di troncamento. </br> -Il tempo impiegato per esportare un database con un numero elevato di oggetti può essere significativamente superiore. |
|[Azure Data Factory](../../../data-factory/connector-azure-sql-managed-instance.md)| -Migrazione e/o trasformazione dei dati dai database di origine SQL Server.</br> -Unione di dati da più origini di dati in Azure SQL Istanza gestita in genere per carichi di lavoro di Business Intelligence (BI).   </br> -Richiede la creazione di pipeline di spostamento dei dati in ADF per spostare i dati dall'origine alla destinazione.   </br> - Il [costo](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) è una considerazione importante ed è basato sui trigger della pipeline, sulle esecuzioni delle attività, sulla durata dello spostamento dei dati e così via. |
| | | |

## <a name="feature-interoperability"></a>Interoperabilità delle funzionalità 

Quando si esegue la migrazione di carichi di lavoro basati su altre funzionalità di SQL Server, è necessario tenere presenti ulteriori considerazioni. 

#### <a name="sql-server-integration-services"></a>SQL Server Integration Services

Eseguire la migrazione di progetti e pacchetti di SQL Server Integration Services (SSIS) in SSISDB al Istanza gestita SQL di Azure tramite il [servizio migrazione del database di Azure (DMS)](../../../dms/how-to-migrate-ssis-packages-managed-instance.md). 

Solo i pacchetti SSIS in SSISDB che iniziano con SQL Server 2012 sono supportati per la migrazione. Converte i pacchetti SSIS legacy prima della migrazione. Per ulteriori informazioni, vedere l' [esercitazione sulla conversione dei progetti](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model) . 


#### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services

È possibile eseguire la migrazione dei report di SQL Server Reporting Services (SSRS) in report impaginati di Power BI. Utilizzare lo [strumento di migrazione RDL](https://github.com/microsoft/RdlMigration) per preparare ed eseguire la migrazione dei report. Questo strumento è stato sviluppato da Microsoft per aiutare i clienti a eseguire la migrazione dei report RDL dai server SSRS in Power BI. È disponibile su GitHub e documenta una procedura dettagliata end-to-end dello scenario di migrazione. 

#### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services

È possibile eseguire la migrazione di SQL Server Analysis Services modelli tabulari da SQL Server 2012 e versioni successive a Azure Analysis Services, ovvero un modello di distribuzione PaaS per Analysis Services modello tabulare in Azure. Per altre informazioni sulla migrazione di modelli locali a Azure Analysis Services in questa [esercitazione video](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/), vedere.

In alternativa, è anche possibile eseguire la migrazione dei modelli tabulari Analysis Services locali per [Power bi Premium utilizzando i nuovi endpoint di lettura/scrittura XMLA](/power-bi/admin/service-premium-connect-tools). 
> [!NOTE]
> Power BI funzionalità degli endpoint di lettura/scrittura XMLA è attualmente disponibile in anteprima pubblica e non deve essere considerata per i carichi di lavoro di produzione fino a quando la funzionalità non diventa disponibile a livello generale.

#### <a name="high-availability"></a>Disponibilità elevata

Le funzionalità di disponibilità elevata SQL Server Always On istanze del cluster di failover e Gruppi di disponibilità Always On diventano obsolete nell'Istanza gestita SQL di Azure di destinazione perché l'architettura a disponibilità elevata è già incorporata in [per utilizzo generico (modello di disponibilità standard)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) e [business critical (modello di disponibilità premium)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) SQL istanza gestita. Il modello di disponibilità Premium fornisce anche la scalabilità in lettura che consente la connessione a uno dei nodi secondari per finalità di sola lettura.     

Oltre all'architettura a disponibilità elevata inclusa in SQL Istanza gestita, è disponibile anche la funzionalità [gruppi di failover automatico](../../database/auto-failover-group-overview.md) che consente di gestire la replica e il failover dei database in un'istanza gestita in un'altra area. 

#### <a name="sql-agent-jobs"></a>Processi di SQL Agent

Usare l'opzione offline del servizio migrazione del database di Azure (DMS) per eseguire la migrazione dei [processi di SQL Agent](../../../dms/howto-sql-server-to-azure-sql-mi-powershell.md#offline-migrations). In caso contrario, creare uno script per i processi in Transact-SQL (T-SQL) utilizzando SQL Server Management Studio e quindi ricrearli manualmente sul Istanza gestita SQL di destinazione. 

> [!IMPORTANT]
> Attualmente, Azure DMS supporta solo i processi con i passaggi del sottosistema T-SQL. È necessario eseguire manualmente la migrazione dei processi con i passaggi del pacchetto SSIS. 

#### <a name="logins-and-groups"></a>Account di accesso e gruppi

È possibile spostare gli account di accesso SQL dal SQL Server di origine in Azure SQL Istanza gestita usando il servizio migrazione del database in modalità offline.  Usare il pannello **[Seleziona account di accesso](../../../dms/tutorial-sql-server-to-managed-instance.md#select-logins)** nella **migrazione guidata** per eseguire la migrazione degli account di accesso al istanza gestita SQL di destinazione. 

Per impostazione predefinita, Servizio Migrazione del database di Azure supporta solo la migrazione di account di accesso SQL. È però possibile abilitare la migrazione degli account di accesso Windows seguendo questa procedura:

Verificare che il Istanza gestita SQL di destinazione disponga Azure AD accesso in lettura, che può essere configurato tramite la portale di Azure da parte di un utente con il ruolo di **amministratore della società** o **amministratore globale**.
Configurare l'istanza di Servizio Migrazione del database di Azure in modo da abilitare le migrazioni degli account di accesso utente/gruppo di Windows, che può essere impostato nella pagina Configurazione del portale di Azure. Dopo aver abilitato questa impostazione, riavviare il servizio per rendere effettive le modifiche.

Dopo il riavvio del servizio, gli account di accesso utente/gruppo di Windows vengono visualizzati nell'elenco degli account di accesso disponibili per la migrazione. Per tutti gli account di accesso utente/gruppo di Windows di cui si esegue la migrazione viene richiesto di specificare il nome di dominio associato. Gli account utente del servizio (account con nome di dominio NT AUTHORITY) e gli account utente virtuali (account con nome di dominio NT SERVICE) non sono supportati.

Per altre informazioni, vedere [come eseguire la migrazione di utenti e gruppi di Windows in un'istanza di SQL Server al istanza gestita SQL di Azure con T-SQL](../../managed-instance/migrate-sql-server-users-to-instance-transact-sql-tsql-tutorial.md).

In alternativa, è possibile usare lo [strumento utilità di PowerShell](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) appositamente progettato da Microsoft Data Migration Architects. L'utilità USA PowerShell per creare uno script T-SQL per ricreare gli account di accesso e selezionare gli utenti del database dall'origine alla destinazione. Lo strumento esegue automaticamente il mapping degli account di Windows ad agli account Azure AD ed è in grado di eseguire una ricerca UPN per ogni account di accesso rispetto al Active Directory di origine. Tramite lo strumento vengono scritti ruoli del server e del database personalizzati, nonché l'appartenenza al ruolo, il ruolo del database e le autorizzazioni utente. I database indipendenti non sono attualmente supportati e viene inserito in uno script solo un subset delle autorizzazioni di SQL Server possibili. 

#### <a name="encryption"></a>Crittografia

Quando si esegue la migrazione dei database protetti da  [Transparent Data Encryption](../../database/transparent-data-encryption-tde-overview.md)   a un'istanza gestita mediante l'opzione di ripristino nativo, [eseguire la migrazione del certificato corrispondente](../../managed-instance/tde-certificate-migrate.md) dal SQL Server di origine al istanza gestita SQL di destinazione *prima* del ripristino del database. 

#### <a name="system-databases"></a>Database di sistema

Il ripristino di database di sistema non è supportato. Per eseguire la migrazione degli oggetti a livello di istanza (archiviati nei database master o msdb), crearne uno script usando Transact-SQL (T-SQL) e quindi ricrearli nell'istanza gestita di destinazione. 

## <a name="leverage-advanced-features"></a>Sfruttare le funzionalità avanzate 

Assicurarsi di sfruttare le funzionalità avanzate basate sul cloud offerte da SQL Istanza gestita. Ad esempio, non è più necessario preoccuparsi di gestire i backup quando il servizio esegue questa operazione. È possibile eseguire il ripristino in qualsiasi [punto nel tempo entro il periodo di conservazione](../../database/recovery-using-backups.md#point-in-time-restore). Non è inoltre necessario preoccuparsi della configurazione della disponibilità elevata, in quanto la [disponibilità elevata è incorporata in](../../database/high-availability-sla.md). 

Per rafforzare la sicurezza, è consigliabile usare [Azure Active Directory autenticazione](../../database/authentication-aad-overview.md), il [controllo](../../managed-instance/auditing-configure.md), il [rilevamento delle minacce](../../database/azure-defender-for-sql.md), la [sicurezza a livello di riga](/sql/relational-databases/security/row-level-security)e la [maschera dati dinamica](/sql/relational-databases/security/dynamic-data-masking).

Oltre alle funzionalità avanzate di gestione e sicurezza, SQL Istanza gestita fornisce un set di strumenti avanzati che consentono di [monitorare e ottimizzare il carico di lavoro](../../database/monitor-tune-overview.md). [Analisi SQL di Azure](../../../azure-monitor/insights/azure-sql.md) consente di monitorare un ampio set di istanze gestite in modo centralizzato.  [Ottimizzazione automatica](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   nelle istanze gestite monitora continuamente le prestazioni delle statistiche di esecuzione del piano SQL e corregge automaticamente i problemi di prestazioni identificati. 

Alcune funzionalità sono disponibili solo quando il [livello di compatibilità del database](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) viene impostato sul livello di compatibilità più recente (150). 

## <a name="migration-assets"></a>Risorse per la migrazione 

Per ulteriore assistenza, vedere le risorse seguenti sviluppate per i progetti di migrazione reali.

|Asset  |Descrizione  |
|---------|---------|
|[Strumento e modello di valutazione dei carichi di lavoro dei dati](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Questo strumento indica le piattaforme di destinazione "più idonee" suggerite, la preparazione per il cloud e il livello di correzione di applicazioni/database per un determinato carico di lavoro. Offre funzionalità semplici e accessibili con un solo clic per l'esecuzione di calcoli e la generazione di report, che consentono di accelerare le valutazioni in ambienti estesi grazie a un processo decisionale automatizzato e uniforme per la piattaforma di destinazione.|
|[Utilità DBLoader](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|DBLoader può essere usato per caricare dati da file di testo delimitati in SQL Server. Questa utilità console di Windows usa l'interfaccia Bulkload di Native Client SQL Server, che funziona in tutte le versioni di SQL Server, incluso Azure SQL MI.|
|[Utilità per spostare gli account di accesso SQL Server locali in Azure SQL Istanza gestita](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Uno script di PowerShell che crea uno script di comando T-SQL per ricreare gli account di accesso e selezionare gli utenti del database da SQL Server locali ad Azure SQL Istanza gestita. Lo strumento consente il mapping automatico degli account di Windows ad per Azure AD account, nonché la migrazione facoltativa di SQL Server account di accesso nativi.|
|[Automazione della raccolta dati Perfmon con logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Strumento che raccoglie i dati per comprendere le prestazioni di base che assistono nella raccomandazione della destinazione della migrazione. Questo strumento utilizza logman.exe per creare il comando per la creazione, l'avvio, l'arresto e l'eliminazione dei contatori delle prestazioni impostati in una SQL Server remota.|
|[White paper-migrazione di database in Azure SQL Istanza gestita tramite il ripristino di backup completi e differenziali](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20to%20Azure%20SQL%20DB%20Managed%20Instance%20-%20%20Restore%20with%20Full%20and%20Differential%20backups.pdf)|Questo white paper fornisce linee guida e procedure per accelerare le migrazioni da SQL Server ad Azure SQL Istanza gestita se si dispone solo di backup completi e differenziali (e nessuna funzionalità di backup del log).|

Queste risorse sono state sviluppate come parte del programma Data SQL Ninja, sponsorizzato dal team di progettazione Azure Data Group. Obiettivo principale del programma Data SQL Ninja è rendere disponibili opportunità per velocizzare progetti di modernizzazione complessi e la migrazione delle piattaforme dati alla piattaforma dati di Microsoft Azure. Se si ritiene che l'organizzazione possa essere interessata a partecipare al programma Data SQL Ninja, contattare il team dell'account per richiedere l'invio di una candidatura.


## <a name="next-steps"></a>Passaggi successivi

Per avviare la migrazione del SQL Server al Istanza gestita SQL di Azure, vedere la [Guida alla migrazione da SQL Server a sql istanza gestita](sql-server-to-managed-instance-guide.md).

- Per una matrice di servizi e strumenti di Microsoft e di terze parti disponibili per supportare diversi scenari di migrazione di database e dati, nonché per le attività speciali, vedere [servizio e strumenti per la migrazione dei dati](../../../dms/dms-tools-matrix.md).

- Per ulteriori informazioni su Azure SQL Istanza gestita vedere:
   - [Livelli di servizio in Azure SQL Istanza gestita](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Differenze tra SQL Server e Azure SQL Istanza gestita](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Calcolatore costo totale di proprietà di Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Per ulteriori informazioni sul Framework e il ciclo di adozione per le migrazioni cloud, vedere
   -  [Cloud Adoption Framework per Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Procedure consigliate per il costo e il ridimensionamento dei carichi di lavoro migrazione ad Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Per valutare il livello di accesso dell'applicazione, vedere [Data Access Migration Toolkit (anteprima)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Per informazioni dettagliate su come eseguire i test A/B di livello di accesso ai dati, vedere [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).