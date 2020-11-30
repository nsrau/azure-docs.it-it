---
title: SQL Server al database SQL-Panoramica della migrazione
description: Informazioni sui diversi strumenti e opzioni disponibili per eseguire la migrazione dei database di SQL Server al database SQL di Azure.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 46216fe06e3d3425d5b237cdbb7326eed596945a
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326931"
---
# <a name="migration-overview-sql-server-to-sql-database"></a>Panoramica della migrazione: SQL Server al database SQL
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Informazioni sulle diverse opzioni e considerazioni relative alla migrazione per eseguire la migrazione del SQL Server al database SQL di Azure. 

È possibile eseguire la migrazione di SQL Server in esecuzione in locale o in: 

- SQL Server in macchine virtuali  
- Amazon Web Services (AWS) EC2 
- Servizio database relazionale di Amazon (AWS RDS) 
- Motore di calcolo (Google Cloud Platform-GCP)  
- SQL cloud per SQL Server (Google Cloud Platform-GCP) 

Per altri scenari, vedere la [Guida alla migrazione del database](https://datamigration.microsoft.com/). 

## <a name="overview"></a>Panoramica

Il [database SQL di Azure](../../database/sql-database-paas-overview.md) è un'opzione di destinazione consigliata per carichi di lavoro SQL Server che richiedono una piattaforma distribuita come servizio (PaaS) completamente gestita. Il database SQL gestisce la maggior parte delle funzioni di gestione del database, oltre a disponibilità elevata, elaborazione intelligente delle query, scalabilità e funzionalità per le prestazioni integrate in base a molti tipi di applicazioni diversi. 

Il database SQL offre flessibilità con più [modelli di distribuzione](../../database/sql-database-paas-overview.md#deployment-models) e livelli di [servizio](../../database/service-tiers-vcore.md#service-tiers) che soddisfano i diversi tipi di applicazioni o carichi di lavoro.

Uno dei principali vantaggi della migrazione al database SQL è la possibilità di modernizzare l'applicazione sfruttando le funzionalità PaaS ed eliminando eventuali dipendenze dai componenti tecnici che hanno come ambito il livello di istanza, ad esempio i processi di SQL Agent.

È anche possibile risparmiare sui costi eseguendo la migrazione delle licenze locali SQL Server al database SQL di Azure usando il [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) per SQL Server scegliere il modello di [acquisto basato su vCore](../../database/service-tiers-vcore.md).

Questa guida mira a chiarire le opzioni e le considerazioni sulla migrazione durante la preparazione della migrazione dei database di SQL Server al database SQL di Azure.  

## <a name="considerations"></a>Considerazioni 

I fattori chiave da considerare quando si valutano le opzioni di migrazione dipendono da: 

- Numero di server e database
- Dimensioni dei database
- Tempo di inattività aziendale accettabile durante il processo di migrazione 

Le opzioni di migrazione elencate in questa guida prendono in considerazione questi fattori. Per la migrazione dei dati logici al database SQL di Azure, il tempo di migrazione può dipendere sia dal numero di oggetti in un database che dalle dimensioni del database. 

Sono disponibili strumenti diversi per diversi carichi di lavoro e preferenze utente. Alcuni strumenti possono essere utilizzati per eseguire una migrazione rapida di un database singolo utilizzando uno strumento basato su interfaccia utente mentre altri strumenti possono eseguire la migrazione di più database che possono essere automatizzati per gestire migrazioni su larga scala. 

## <a name="choose-appropriate-target"></a>Scegliere la destinazione appropriata

Prendere in considerazione le linee guida generali che consentono di scegliere il modello di distribuzione e il livello di servizio appropriati per il database SQL di Azure. È possibile scegliere risorse di calcolo e di archiviazione durante la distribuzione e quindi modificarle successivamente usando il  [portale di Azure](../../database/scale-resources.md)  senza incorrere in tempi di inattività per l'applicazione.


**Modelli di distribuzione**: comprendere il carico di lavoro dell'applicazione e il modello di utilizzo per scegliere tra un singolo database o un pool elastico. 

- Un [database singolo](../../database/single-database-overview.md) rappresenta un database completamente gestito adatto per la maggior parte dei microservizi e delle applicazioni cloud moderne.
- Un [pool elastico](../../database/elastic-pool-overview.md) è una raccolta di database singoli con un set condiviso di risorse come CPU o memoria e adatto per combinare i database in un pool con modelli di utilizzo prevedibili che possono condividere in modo efficace lo stesso set di risorse.

**Modelli di acquisto**: scegliere tra il modello di acquisto Vcore, DTU o senza server. 

- Il [modello vCore](../../database/service-tiers-vcore.md) consente di scegliere il numero di Vcore per il database SQL di Azure, rendendolo la scelta più semplice durante la conversione da SQL Server locali. Questa è l'unica opzione che supporta il risparmio sul costo della licenza con il [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/). 
- Il [modello DTU](../../database/service-tiers-dtu.md) astrae le risorse di calcolo, memoria e i/o sottostanti per fornire un DTU misto. 
- Il [modello senza server](../../database/serverless-tier-overview.md) è destinato ai carichi di lavoro che richiedono il ridimensionamento automatico su richiesta con risorse di calcolo fatturate al secondo. Il livello di calcolo senza Server sospende automaticamente i database durante i periodi di inattività (in cui vengono fatturate solo le risorse di archiviazione) e riprende automaticamente i database al ritorno dell'attività. 

**Livelli di servizio**: scegliere tra tre livelli di servizio progettati per diversi tipi di applicazioni.

- Il [livello di servizio per utilizzo generico/standard](../../database/service-tier-general-purpose.md) offre un'opzione orientata ai budget bilanciata con calcolo e archiviazione idonei per fornire applicazioni di livello inferiore, con ridondanza incorporata a livello di archiviazione per il recupero dagli errori. Progettato per la maggior parte dei carichi di lavoro del database. 
- Il [livello di servizio business critical/Premium](../../database/service-tier-business-critical.md) è per applicazioni di livello elevato che richiedono tassi di transazione elevati, i/o a bassa latenza e un elevato livello di resilienza con le repliche secondarie disponibili per il failover e per l'offload dei carichi di lavoro di lettura.
- Il [livello di servizio iperscalabile](../../database/service-tier-hyperscale.md) è per i database che hanno volumi di dati in continua crescita ed è necessario scalare automaticamente fino a dimensioni del database di 100 TB. Progettato per database di dimensioni molto grandi. 

> [!IMPORTANT]
> [La velocità del log delle transazioni è regolata](../../database/resource-limits-logical-server.md#transaction-log-rate-governance) nel database SQL di Azure per limitare le velocità di inserimento elevate. Di conseguenza, durante la migrazione, potrebbe essere necessario ridimensionare le risorse del database di destinazione (VCore/DTU) per semplificare la pressione sulla CPU o sulla velocità effettiva. Scegliere il database di destinazione di dimensioni appropriate, ma pianificare la scalabilità delle risorse per la migrazione, se necessario. 


### <a name="sql-server-on-azure-vm-alternative"></a>SQL Server in una macchina virtuale di Azure alternativa

L'azienda può avere requisiti che rendono [SQL Server in macchine virtuali di Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) una destinazione più appropriata rispetto al database SQL di Azure. 

Se si applicano le condizioni seguenti alla propria azienda, provare a eseguire il passaggio a un SQL Server nella macchina virtuale di Azure: 

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
|[Data Migration Assistant (DMA)](/sql/dma/dma-migrateonpremsqltosqldb)|Il Data Migration Assistant è uno strumento desktop che fornisce valutazioni senza problemi di SQL Server e migrazioni al database SQL di Azure (sia dello schema che dei dati). Lo strumento può essere installato in un server locale o nel computer locale con connettività ai database di origine. Il processo di migrazione è uno spostamento di dati logici tra oggetti nel database di origine e di destinazione. </br> -Migrare i database singoli (schema e dati)|
|[Servizio Migrazione del database di Azure (DMS)](../../../dms/tutorial-sql-server-to-azure-sql.md)|Un servizio di Azure di terze parti in grado di eseguire la migrazione dei database di SQL Server al database SQL di Azure usando il portale di Azure o automatizzato con PowerShell. Il servizio DMS di Azure richiede la selezione di una rete virtuale di Azure preferita (VNet) durante il provisioning per garantire la connettività ai database di origine SQL Server. </br> -Migrare singoli database o su larga scala. |
| | |


### <a name="alternative-tools"></a>Strumenti alternativi

La tabella seguente elenca gli strumenti di migrazione alternativi: 

|Tecnologia |Descrizione  |
|---------|---------|
|[Replica transazionale](../../database/replication-to-sql-database.md)|Replicare i dati dalle tabelle di database di origine SQL Server al database SQL fornendo un'opzione di migrazione del tipo di Sottoscrittore del server di pubblicazione mantenendo la coerenza transazionale. Le modifiche incrementali dei dati vengono propagate ai Sottoscrittori non appena si verificano nei Publisher.|
|[Import Export Service/BACPAC](../../database/database-import.md)|[BacPac](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) è un file di Windows con un' `.bacpac` estensione che incapsula lo schema e i dati di un database. BACPAC può essere usato per esportare i dati da un SQL Server di origine e per importare i dati nel database SQL di Azure. Il file BACPAC può essere importato in un nuovo database SQL di Azure usando il portale di Azure. </br></br> Per la scalabilità e le prestazioni con database di grandi dimensioni o un numero elevato di database, è consigliabile utilizzare l'utilità della riga di comando [SqlPackage](../../database/database-import.md#using-sqlpackage) per esportare e importare i database.|
|[Copia bulk](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)|L' [utilità del programma per la copia bulk (BCP)](/sql/tools/bcp-utility) copia i dati da un'istanza di SQL Server in un file di dati. Usare l'utilità BCP per esportare i dati dall'origine e importare il file di dati nel database SQL di destinazione. </br></br> Per le operazioni di copia bulk ad alta velocità per spostare i dati nel database SQL di Azure, è possibile usare [lo strumento per la copia bulk intelligente](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) per ottimizzare la velocità di trasferimento sfruttando le attività di copia parallele.|
|[Azure Data Factory](../../../data-factory/connector-azure-sql-database.md)|L' [attività di copia](../../../data-factory/copy-activity-overview.md) in Azure Data Factory esegue la migrazione dei dati dai database SQL Server di origine al database SQL usando connettori predefiniti e un [Integration Runtime](../../../data-factory/concepts-integration-runtime.md).</br> </br> ADF supporta un'ampia gamma di [connettori](../../../data-factory/connector-overview.md) per spostare i dati dalle origini SQL Server al database SQL.|
|[Sincronizzazione dati SQL](../../database/sql-data-sync-data-sql-server-sql-database.md)|Sincronizzazione dati SQL è un servizio basato sul database SQL di Azure che consente di sincronizzare i dati selezionati in modo bidirezionale tra più database, sia in locale che nel cloud.</br>La sincronizzazione dei dati è utile nei casi in cui i dati devono essere mantenuti aggiornati in diversi database nel database SQL di Azure o in SQL Server.|
| | |

## <a name="compare-migration-options"></a>Confrontare le opzioni di migrazione

Confrontare le opzioni di migrazione per scegliere il percorso appropriato alle esigenze aziendali. 

### <a name="recommended-options"></a>Opzioni consigliate

Nella tabella seguente vengono confrontate le opzioni di migrazione consigliate: 

|Opzione di migrazione  |Utilizzo  |Considerazioni  |
|---------|---------|---------|
|[Data Migration Assistant (DMA)](/sql/dma/dma-migrateonpremsqltosqldb) | -Migrare i database singoli (schema e dati).  </br> -Può supportare i tempi di inattività durante il processo di migrazione dei dati. </br> </br> Origini supportate: </br> -SQL Server (2005-2019) in locale o in una VM di Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP calcolo della macchina virtuale SQL Server | -L'attività di migrazione esegue lo spostamento dei dati tra oggetti di database (dall'origine alla destinazione) ed è quindi consigliabile eseguirli durante i periodi di minore attività. </br> -DMA segnala lo stato della migrazione per ogni oggetto di database, incluso il numero di righe di cui è stata eseguita la migrazione.  </br> -Per migrazioni di grandi dimensioni (numero di database/dimensioni del database), usare il servizio migrazione del database di Azure indicato di seguito.|
|[Servizio Migrazione del database di Azure (DMS)](../../../dms/tutorial-sql-server-to-azure-sql.md)| -Migrare singoli database o su larga scala. </br> -Può supportare i tempi di inattività durante il processo di migrazione. </br> </br> Origini supportate: </br> -SQL Server (2005-2019) in locale o in una VM di Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP calcolo della macchina virtuale SQL Server | -Le migrazioni su larga scala possono essere automatizzate tramite [PowerShell](../../../dms/howto-sql-server-to-azure-sql-powershell.md). </br> -Il tempo necessario per completare la migrazione dipende dalle dimensioni del database e dal numero di oggetti nel database. </br> -Richiede che il database di origine sia impostato come di sola lettura. |
| | | |

### <a name="alternative-options"></a>Opzioni alternative

Nella tabella seguente vengono confrontate le opzioni di migrazione alternative: 

|Metodo/tecnologia |Utilizzo    |Considerazioni  |
|---------|---------|---------|
|[Replica transazionale](../../database/replication-to-sql-database.md)| -Esegue la migrazione pubblicando continuamente le modifiche dalle tabelle di database di origine alle tabelle del database SQL di destinazione. </br> -Migrazioni di database complete o parziali di tabelle selezionate (subset di database).  </br> </br> Origini supportate: </br> - [SQL Server (2016-2019) con alcune limitazioni](/sql/relational-databases/replication/replication-backward-compatibility) </br> -AWS EC2  </br> -GCP calcolo della macchina virtuale SQL Server  | -Il programma di installazione è relativamente complesso rispetto ad altre opzioni di migrazione.   </br> -Fornisce un'opzione di replica continua per eseguire la migrazione dei dati (senza portare i database offline).  </br> -La replica transazionale presenta alcune limitazioni da considerare quando si configura il server di pubblicazione nel SQL Server di origine. Per altre informazioni, vedere [limitazioni relative alla pubblicazione di oggetti](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) . </br>-È possibile monitorare l' [attività di replica](/sql/relational-databases/replication/monitor/monitoring-replication).    |
|[Import Export Service/BACPAC](../../database/database-import.md)| -Migrare i singoli database delle applicazioni line-of-business. </br>-Adatto per database più piccoli.  </br>  -Non richiede uno strumento o un servizio di migrazione separato. </br> </br> Origini supportate: </br> -SQL Server (2005-2019) in locale o in una VM di Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP calcolo della macchina virtuale SQL Server  |  -Richiede tempi di inattività perché i dati devono essere esportati nell'origine e importati nella destinazione.   </br> -I formati di file e i tipi di dati utilizzati nell'esportazione/importazione devono essere coerenti con gli schemi di tabella per evitare errori di mancata corrispondenza del tipo di dati o di troncamento.  </br> -Il tempo impiegato per esportare un database con un numero elevato di oggetti può essere significativamente superiore.       |
|[Copia bulk](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| -Esegue la migrazione di migrazioni di dati complete o parziali. </br> -Può contenere tempi di inattività. </br> </br> Origini supportate: </br> -SQL Server (2005-2019) in locale o in una VM di Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP calcolo della macchina virtuale SQL Server   | -Richiede tempi di inattività per l'esportazione dei dati dall'origine e l'importazione nella destinazione. </br> -I formati di file e i tipi di dati utilizzati nell'esportazione/importazione devono essere coerenti con gli schemi di tabella. |
|[Azure Data Factory](../../../data-factory/connector-azure-sql-database.md)| -Eseguire la migrazione e/o trasformare i dati dai database di origine SQL Server. </br> -Unione di dati da più origini di dati al database SQL di Azure in genere per carichi di lavoro di Business Intelligence (BI).  |  -Richiede la creazione di pipeline di spostamento dei dati in ADF per spostare i dati dall'origine alla destinazione.   </br> - Il [costo](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) è una considerazione importante ed è basato sui trigger della pipeline, sulle esecuzioni delle attività, sulla durata dello spostamento dei dati e così via. |
|[Sincronizzazione dati SQL](../../database/sql-data-sync-data-sql-server-sql-database.md)| -Sincronizzare i dati tra i database di origine e di destinazione.</br> -Adatto per eseguire la sincronizzazione continua tra il database SQL di Azure e SQL Server locali in un flusso bidirezionale. | -Il database SQL di Azure deve essere il database hub per la sincronizzazione con il database di SQL Server locale come database membro.</br> -Rispetto alla replica transazionale, sincronizzazione dati SQL supporta la sincronizzazione bidirezionale dei dati tra l'istanza locale e il database SQL di Azure. </br> -Può avere un maggiore effetto sulle prestazioni a seconda del carico di lavoro.|
| | | |

## <a name="feature-interoperability"></a>Interoperabilità delle funzionalità 

Quando si esegue la migrazione di carichi di lavoro basati su altre funzionalità di SQL Server, è necessario tenere presenti ulteriori considerazioni.

#### <a name="sql-server-integration-services"></a>SQL Server Integration Services
Eseguire la migrazione di pacchetti di SQL Server Integration Services (SSIS) ad Azure ridistribuendo i pacchetti in Azure-SSIS Runtime in [Azure Data Factory](../../../data-factory/introduction.md). Azure Data Factory [supporta la migrazione di pacchetti SSIS](../../../data-factory/scenario-ssis-migration-overview.md#azure-sql-database-as-database-workload-destination) fornendo un runtime compilato per l'esecuzione di pacchetti SSIS in Azure. In alternativa, è anche possibile riscrivere la logica ETL SSIS in modo nativo in ADF usando i [flussi](../../../data-factory/concepts-data-flow-overview.md)di file.


#### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
Eseguire la migrazione dei report SQL Server Reporting Services (SSRS) a report impaginati in Power BI. Utilizzare lo [strumento di migrazione RDL](https://github.com/microsoft/RdlMigration) per preparare ed eseguire la migrazione dei report. Questo strumento è stato sviluppato da Microsoft per aiutare i clienti a eseguire la migrazione dei report RDL dai server SSRS in Power BI. È disponibile su GitHub e documenta una procedura dettagliata end-to-end dello scenario di migrazione. 

#### <a name="high-availability"></a>Disponibilità elevata
L'installazione manuale di SQL Server funzionalità di disponibilità elevata, ad esempio Always On istanze del cluster di failover e Gruppi di disponibilità Always On diventata obsoleta nel database SQL di Azure di destinazione perché l'architettura a disponibilità elevata è già incorporata nel database SQL di [per utilizzo generico (modello di disponibilità standard)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) e [business critical (modello di disponibilità Premium)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) . Il livello di servizio business critical/Premium fornisce anche la scalabilità in lettura che consente la connessione a uno dei nodi secondari per finalità di sola lettura. 

Oltre all'architettura a disponibilità elevata inclusa nel database SQL, è disponibile anche la funzionalità [gruppi di failover automatico](../../database/auto-failover-group-overview.md) che consente di gestire la replica e il failover dei database in un'istanza gestita in un'altra area. 

#### <a name="sql-agent-jobs"></a>Processi di SQL Agent
I processi di SQL Agent non sono direttamente supportati nel database SQL di Azure e dovranno essere distribuiti nei [processi di database elastici (anteprima)](../../database/job-automation-overview.md#elastic-database-jobs-preview).

#### <a name="logins-and-groups"></a>Account di accesso e gruppi
Spostare gli account di accesso SQL dalla SQL Server di origine al database SQL di Azure usando il servizio migrazione del database in modalità offline.  Utilizzare il pannello **accessi selezionati** della **migrazione guidata** per eseguire la migrazione degli account di accesso al database SQL di destinazione. 

È anche possibile eseguire la migrazione di utenti e gruppi di Windows tramite DMS abilitando l'interruttore corrispondente nella pagina di configurazione DMS. 

In alternativa, è possibile usare lo [strumento utilità di PowerShell](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) appositamente progettato da Microsoft Data Migration Architects. L'utilità USA PowerShell per creare uno script Transact-SQL (T-SQL) per ricreare gli account di accesso e selezionare gli utenti del database dall'origine alla destinazione. Lo strumento esegue automaticamente il mapping degli account di Windows ad agli account Azure AD ed è in grado di eseguire una ricerca UPN per ogni account di accesso rispetto al Active Directory di origine. Tramite lo strumento vengono scritti ruoli del server e del database personalizzati, nonché l'appartenenza al ruolo, il ruolo del database e le autorizzazioni utente. I database indipendenti non sono ancora supportati e viene creato uno script solo un subset delle autorizzazioni di SQL Server possibili. 


#### <a name="system-databases"></a>Database di sistema
Per il database SQL di Azure, gli unici database di sistema applicabili sono [Master](/sql/relational-databases/databases/master-database) e tempdb. Per altre informazioni, vedere [tempdb nel database SQL di Azure](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).

## <a name="leverage-advanced-features"></a>Sfruttare le funzionalità avanzate 

Assicurarsi di sfruttare le funzionalità avanzate basate sul cloud offerte dal database SQL. Ad esempio, non è più necessario preoccuparsi di gestire i backup quando il servizio esegue questa operazione. È possibile eseguire il ripristino in qualsiasi [punto nel tempo entro il periodo di conservazione](../../database/recovery-using-backups.md#point-in-time-restore). 

Per rafforzare la sicurezza, è consigliabile usare [Azure Active Directory autenticazione](../../database/authentication-aad-overview.md), il [controllo](../../database/auditing-overview.md), il [rilevamento delle minacce](../../database/azure-defender-for-sql.md), la [sicurezza a livello di riga](/sql/relational-databases/security/row-level-security)e la [maschera dati dinamica](/sql/relational-databases/security/dynamic-data-masking).

Oltre alle funzionalità avanzate di gestione e sicurezza, il database SQL offre un set di strumenti avanzati che consentono di [monitorare e ottimizzare il carico di lavoro](../../database/monitor-tune-overview.md). [Analisi SQL di Azure (anteprima)](../../../azure-monitor/insights/azure-sql.md) è una soluzione di monitoraggio cloud avanzata per il monitoraggio delle prestazioni di tutti i database nel database SQL di Azure su larga scala e tra più sottoscrizioni in un'unica visualizzazione. Analisi SQL di Azure raccoglie e visualizza le metriche delle prestazioni chiave con l'intelligence predefinita per la risoluzione dei problemi relativi alle prestazioni.

[Ottimizzazione automatica](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   monitora continuamente le prestazioni delle statistiche del piano di esecuzione SQL e corregge automaticamente i problemi di prestazioni identificati. 


## <a name="migration-assets"></a>Risorse per la migrazione 

Per ulteriore assistenza, vedere le risorse seguenti sviluppate per i progetti di migrazione reali.

|Asset  |Descrizione  |
|---------|---------|
|[Strumento e modello di valutazione dei carichi di lavoro dei dati](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Questo strumento indica le piattaforme di destinazione "più idonee" suggerite, la preparazione per il cloud e il livello di correzione di applicazioni/database per un determinato carico di lavoro. Offre semplici calcoli con un solo clic e la generazione di report che consentono di accelerare le valutazioni di grandi dimensioni fornendo un processo decisionale di piattaforma di destinazione automatizzato e uniforme.|
|[Utilità DBLoader](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|DBLoader può essere usato per caricare dati da file di testo delimitati in SQL Server. Questa utilità console di Windows usa l'interfaccia Bulkload di Native Client SQL Server, che funziona in tutte le versioni di SQL Server, incluso il database SQL di Azure.|
|[Creazione di database in blocco con PowerShell](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Database%20Creation%20with%20PowerShell)|Questo include un set di tre script PowerShell che creano un gruppo di risorse (create_rg.ps1), il [server logico in Azure](../../database/logical-servers.md) (create_sqlserver.ps1) e il database SQL di azure (create_sqldb.ps1). Gli script includono funzionalità di ciclo, in modo da poter scorrere e creare tutti i server e i database necessari.|
|[Distribuzione dello schema bulk con MSSQL-Scripter & PowerShell](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Schema%20Deployment%20with%20MSSQL-Scripter%20&%20PowerShell)|Questo asset crea un gruppo di risorse, uno o più [server logici in Azure](../../database/logical-servers.md) per ospitare il database SQL di Azure, esporta ogni schema da un SQL Server locale (o più server SQL (2005 +) e lo importa nel database SQL di Azure.|
|[Convertire SQL Server Agent processi in processi di database elastici](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Convert%20SQL%20Server%20Agent%20Jobs%20into%20Elastic%20Database%20Jobs)|Questo script esegue la migrazione dei processi di SQL Server Agent di origine ai processi di database elastici.|
|[Invia messaggi dal database SQL di Azure](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)|Questo fornisce una soluzione come alternativa alla funzionalità SendMail disponibile in SQL Server locali. La soluzione USA funzioni di Azure e il servizio Azure SendGrid per inviare messaggi di posta elettronica dal database SQL di Azure.|
|[Utilità per spostare gli account di accesso SQL Server locali nel database SQL di Azure](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Uno script di PowerShell che crea uno script di comando T-SQL per ricreare gli account di accesso e selezionare gli utenti del database dall'SQL Server locale al database SQL di Azure. Lo strumento consente il mapping automatico degli account di Windows ad per Azure AD account, nonché la migrazione facoltativa di SQL Server account di accesso nativi.|
|[Automazione della raccolta dati PerfMon con logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Strumento che raccoglie i dati di PerMon per comprendere le prestazioni della linea di base e agevola le raccomandazioni relative alla migrazione della destinazione. Questo strumento usa logman.exe per creare il comando per la creazione, l'avvio, l'arresto e l'eliminazione dei contatori delle prestazioni impostati in una SQL Server remota|
|[Whitepaper-migrazione del database al database SQL di Azure con BACPAC](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20-%20Benchmarks%20and%20Steps%20to%20Import%20to%20Azure%20SQL%20DB%20Single%20Database%20from%20BACPAC.pdf)|Questo white paper fornisce istruzioni e procedure per accelerare le migrazioni da SQL Server al database SQL di Azure tramite file BACPAC.|

Queste risorse sono state sviluppate come parte del programma Data SQL Ninja, sponsorizzato dal team di progettazione Azure Data Group. Obiettivo principale del programma Data SQL Ninja è rendere disponibili opportunità per velocizzare progetti di modernizzazione complessi e la migrazione delle piattaforme dati alla piattaforma dati di Microsoft Azure. Se si ritiene che l'organizzazione possa essere interessata a partecipare al programma Data SQL Ninja, contattare il team dell'account per richiedere l'invio di una candidatura.


## <a name="next-steps"></a>Passaggi successivi

Per avviare la migrazione del SQL Server al database SQL di Azure, vedere la [Guida alla migrazione da SQL Server a database SQL](sql-server-to-sql-database-guide.md).

- Per una matrice di servizi e strumenti di Microsoft e di terze parti disponibili per supportare diversi scenari di migrazione di database e dati, nonché per le attività speciali, vedere [servizio e strumenti per la migrazione dei dati](../../../dms/dms-tools-matrix.md).

- Per ulteriori informazioni sul database SQL, vedere:
   - [Panoramica del database SQL di Azure](../../database/sql-database-paas-overview.md)
   - [Calcolatore costo totale di proprietà di Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Per ulteriori informazioni sul Framework e il ciclo di adozione per le migrazioni cloud, vedere
   -  [Cloud Adoption Framework per Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Procedure consigliate per il costo e il ridimensionamento dei carichi di lavoro migrazione ad Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Per valutare il livello di accesso dell'applicazione, vedere [Data Access Migration Toolkit (anteprima)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Per informazioni dettagliate su come eseguire i test A/B di livello di accesso ai dati, vedere [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).