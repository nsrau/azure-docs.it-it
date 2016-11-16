# Panoramica
## [Informazioni sul database SQL](sql-database-technical-overview.md)
## [Domande frequenti sul database SQL](sql-database-faq.md)
## Funzionalità
### [Livelli di servizio](sql-database-service-tiers.md)
### [Unità di transazione di database](sql-database-what-is-a-dtu.md)
### [Panoramica del benchmark DTU](sql-database-benchmark-overview.md)
### [Strumenti di gestione](sql-database-manage-overview.md)
## Considerazioni e limitazioni
### [Confronto tra database SQL e SQL in una macchina virtuale](sql-database-paas-vs-sql-server-iaas.md)
### [Differenze con T-SQL](sql-database-transact-sql-information.md)
### [Limiti delle risorse](sql-database-resource-limits.md)
### [Limitazioni generali](sql-database-general-limitations.md)
### [Linee guida sulla sicurezza](sql-database-security-guidelines.md)

## Vantaggi
### [È in grado di apprendere e di adattarsi](sql-database-learn-and-adapt.md)
### [Si ridimensiona immediatamente](sql-database-scale-on-the-fly.md)
### [Crea app multi-tenant](sql-database-build-multi-tenant-apps.md)
### [Garantisce sicurezza e protezione](sql-database-helps-secures-and-protects.md)
### [È compatibile con l'ambiente](sql-database-works-in-your-environment.md)

## Scenari

### Server, pool, database e firewall
#### [Quando usare un pool di database elastici](sql-database-elastic-pool-guidance.md)
#### [Pool di database elastici](sql-database-elastic-pool.md)
#### [Automazione](sql-database-manage-automation.md)

### Database con numero maggiore di istanze
#### [Panoramica](sql-database-elastic-scale-introduction.md)
#### [Creare database cloud scalabili](sql-database-elastic-database-client-library.md)
#### [Processi tra database](sql-database-elastic-jobs-overview.md)
#### [Glossario sugli strumenti di database elastici](sql-database-elastic-scale-glossary.md)
#### [Domande frequenti](sql-database-elastic-scale-faq.md)

### Accesso e autorizzazioni
#### [Panoramica](sql-database-security.md)
#### [Centro sicurezza di Azure per il database SQL di Azure](../security-center/security-center-sql-database.md?toc=%2fazure%2fsql-database%2ftoc.json)
#### [Centro sicurezza SQL](https://msdn.microsoft.com/library/azure/bb510589)

### Continuità aziendale
#### [Panoramica](sql-database-business-continuity.md)
#### [Backup del database](sql-database-automated-backups.md)
#### [Ripristino di database tramite backup](sql-database-recovery-using-backups.md)
#### [Requisiti di autenticazione per il ripristino di emergenza](sql-database-geo-replication-security-config.md)
#### [Scenari di progettazione della continuità aziendale](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [Strategie di ripristino di emergenza con i pool elastici](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [Aggiornamenti in sequenza](sql-database-manage-application-rolling-upgrade.md)

### [Sviluppo di database](sql-database-develop-overview.md)
### [Eseguire la migrazione di database SQL Server](sql-database-cloud-migrate.md)

## Implementazioni dei clienti
### [Daxko/CSI Software](sql-database-implementation-daxko.md)
### [GEP](sql-database-implementation-gep.md)
### [SnelStart](sql-database-implementation-snelstart.md)
### [Umbraco](sql-database-implementation-umbraco.md)


# Attività iniziali
## [Procedure di avvio rapido per le soluzioni](sql-database-solution-quick-starts.md)
## Creare un database SQL
### [Portale di Azure](sql-database-get-started.md)
### [C#](sql-database-get-started-csharp.md)
### [PowerShell](sql-database-get-started-powershell.md)
## Creare e gestire database con aumento del numero di istanze
### [Scalabilità elastica](sql-database-elastic-scale-get-started.md)
### [Processi elastici](sql-database-elastic-jobs-getting-started.md)
### [Report tra database](sql-database-elastic-query-getting-started.md)
### [Query tra database](sql-database-elastic-query-getting-started-vertical.md)
## Monitorare e ottimizzare i database
### [Panoramica di Advisor per database SQL](sql-database-advisor.md)
### [Informazioni dettagliate sul carico di lavoro nel portale di Azure](sql-database-performance.md)
## [Creare e gestire l'accesso e le autorizzazioni](sql-database-get-started-security.md)
## [Ottimizzazione in memoria](sql-database-in-memory.md)
## [Sincronizzazione dei dati](sql-database-get-started-sql-data-sync.md)

# Procedure

## Creare e gestire
### Server e database
#### [Database singoli](sql-database-manage-portal.md)
#### [portale di Azure](sql-database-get-started.md)
#### [C#](sql-database-get-started-csharp.md)
#### [PowerShell](sql-database-manage-powershell.md)
#### [SQL Server Management Studio](sql-database-manage-azure-ssms.md)
### Pool di database elastici
#### [portale di Azure](sql-database-elastic-pool-create-portal.md)
#### [PowerShell](sql-database-elastic-pool-create-powershell.md)
#### [C#](sql-database-elastic-pool-create-csharp.md)
#### [T-SQL](sql-database-elastic-pool-manage-tsql.md)
### Database partizionati
#### [Usare Gestore mappe partizioni](sql-database-elastic-scale-shard-map-management.md)
#### [Configurazione della sicurezza per il servizio di divisione/unione](sql-database-elastic-scale-split-merge-security-configuration.md)
#### [Uso di Dapper](sql-database-elastic-scale-working-with-dapper.md)
#### [Usare Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [Routing dipendente dei dati](sql-database-elastic-scale-data-dependent-routing.md)
#### [Sicurezza a livello di riga multi-tenant](sql-database-elastic-tools-multi-tenant-row-level-security.md)
#### [Gestire le credenziali](sql-database-elastic-scale-manage-credentials.md)
#### [Distribuire un servizio di divisione e unione](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
#### [Aggiungere una partizione](sql-database-elastic-scale-add-a-shard.md)
#### [Uso della classe RecoveryManager per correggere i problemi delle mappe partizioni](sql-database-elastic-database-recovery-manager.md)
###  Autenticazione
#### [Autenticazione di Azure AD](sql-database-aad-authentication.md)
#### [Multi-Factor Authentication](sql-database-ssms-mfa-authentication.md)
### Regole del firewall
#### [Portale di Azure](sql-database-configure-firewall-settings.md)
#### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
#### [API REST](sql-database-configure-firewall-settings-rest.md)
#### [T-SQL](sql-database-configure-firewall-settings-tsql.md)
### Processi
#### [Installazione di un servizio](sql-database-elastic-jobs-service-installation.md)
#### [Portale di Azure](sql-database-elastic-jobs-create-and-manage.md)
#### [PowerShell](sql-database-elastic-jobs-powershell.md)
#### [Aggiornare la libreria client](sql-database-elastic-scale-upgrade-client-library.md)
### [Accessi](sql-database-manage-logins.md)

## Sviluppare
### [Panoramica](https://msdn.microsoft.com/library/mt763826.aspx)
### [Librerie di connettività](sql-database-libraries.md)
### Scenari
#### [Applicazioni SaaS multi-tenant](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### [Dati JSON](sql-database-json-features.md)
#### [Tabelle temporali](sql-database-temporal-tables.md)
#### [Criteri di conservazione](sql-database-temporal-tables-retention-policy.md)
### Connettere un'applicazione
#### [.NET](sql-database-develop-dotnet-simple.md)
#### [Java](sql-database-develop-java-simple.md)
#### [Node.js](sql-database-develop-nodejs-simple.md)
#### [PHP](sql-database-develop-php-simple.md)
#### [Python](sql-database-develop-python-simple.md)
#### [Ruby](sql-database-develop-ruby-simple.md)
#### [Excel](sql-database-connect-excel.md)
#### [Visual Studio](sql-database-connect-query.md)

### [Creare database](sql-database-get-started-powershell.md)
### Gestire i pool elastici
#### [PowerShell](sql-database-elastic-pool-manage-powershell.md)
#### [C#](sql-database-elastic-pool-manage-csharp.md)
### [Ottenere i valori obbligatori per l'autenticazione di un'applicazione](sql-database-client-id-keys.md)
### [Usare le porte successive alla 1433 per ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md)
### [Uso dei messaggi di errore](sql-database-develop-error-messages.md)
### [Usare l'invio in batch](sql-database-use-batching-to-improve-performance.md)

### riferimento
#### [.NET (concetti)](https://msdn.microsoft.com/library/kb9s9ks0.aspx)
#### [.NET (informazioni di riferimento sulle API)](https://msdn.microsoft.com/library/system.data.sqlclient.aspx)
#### [Entity Framework](https://www.nuget.org/packages/EntityFramework/)
#### [Azure SDK (download)](https://www.visualstudio.com/vs/azure-tools/)
#### [Azure SDK (documentazione)](../dotnet-sdk.md)
#### [Cmdlet di PowerShell per Gestione dei servizi](https://msdn.microsoft.com/library/azure/dn546723.aspx)
#### REST
##### [REST (Gestione risorse)](https://msdn.microsoft.com/library/azure/mt420159)
##### [REST (Gestione dei servizi)](https://msdn.microsoft.com/library/azure/dn505719.aspx)

## Rilevare le minacce
### [Introduzione al rilevamento delle minacce](sql-database-threat-detection-get-started.md)
### [Firewall](sql-database-firewall-configure.md)

## Crittografare i dati
### [Panoramica di Always Encrypted](sql-database-always-encrypted.md)
### [Insieme di credenziali delle chiavi di Azure per Always Encrypted](sql-database-always-encrypted-azure-key-vault.md)
### [Transparent Data Encryption](https://msdn.microsoft.com/library/azure/dn948096)
### [Crittografia delle colonne](https://msdn.microsoft.com/library/azure/ms179331)

## Mascherare i dati
### [Maschera dati dinamica](sql-database-dynamic-data-masking-get-started-portal.md)
### [Client di livello inferiore](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)

## Eseguire la migrazione
### Determinare la compatibilità
#### [Utilità pacchetti SQL](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)
### Risolvere i problemi di compatibilità
#### [SQL Server Data Tools](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
#### [Migrazione guidata SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md)
### [Usare la Migrazione guidata di SQL Server Management Studio](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
### [Usare la replica transazionale](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
### [Eseguire la migrazione dei database esistenti con più istanze per aumentare il numero di istanze](sql-database-elastic-convert-to-use-elastic-tools.md)

## Monitorare e ottimizzare
### [Database singoli](sql-database-performance-guidance.md)
### [Query Performance Insight](sql-database-query-performance.md)
### [Advisor per database SQL](sql-database-advisor-portal.md)
### [Prestazioni del database](sql-database-single-database-monitor.md)
### [Viste a gestione dinamica](sql-database-monitoring-with-dmvs.md)
### [Livelli di compatibilità](sql-database-compatibility-level-query-performance-130.md)
### [Controllo degli eventi](sql-database-auditing-get-started.md)
### [Contatori delle prestazioni per Gestore mappe partizioni](sql-database-elastic-database-perf-counters.md)
### [Suggerimenti per l'ottimizzazione delle prestazioni](sql-database-troubleshoot-performance.md)
### Cambiare i livelli di servizio e di prestazioni
#### [Portale di Azure](sql-database-scale-up.md)
#### [PowerShell](sql-database-scale-up-powershell.md)
### OLTP in memoria
#### [Adottare OLTP in memoria](sql-database-in-memory-oltp-migration.md)
#### [Monitorare l'archiviazione OLTP in memoria](sql-database-in-memory-oltp-monitoring.md)
### Archivio query
#### [Monitoraggio delle prestazioni mediante l'archivio query](https://msdn.microsoft.com/library/dn817826.aspx)
#### [Scenari di utilizzo dell'archivio query](https://msdn.microsoft.com/library/mt614796.aspx)
#### [Uso dell'archivio query](sql-database-operate-query-store.md)
### Eventi estesi
#### [Eventi estesi](sql-database-xevent-db-diff-from-svr.md)
#### [Codice di destinazione del file dell'evento](sql-database-xevent-code-event-file.md)
#### [Codice di destinazione del buffer circolare](sql-database-xevent-code-ring-buffer.md)

## Spostare dati
### [Copiare un database SQL](sql-database-copy.md)
#### [portale di Azure](sql-database-copy-portal.md)
#### [PowerShell](sql-database-copy-powershell.md)
#### [T-SQL](sql-database-copy-transact-sql.md)
### Esportare il database in un BACPAC
#### [Portale di Azure](sql-database-export.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [Utilità pacchetti SQL](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
#### [PowerShell](sql-database-export-powershell.md)
### Importare il database da un BACPAC
#### [Portale di Azure](sql-database-import.md)
#### [PowerShell](sql-database-import-powershell.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
#### [Utilità pacchetti SQL](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
### [Caricare dal file CSV usando BCP](sql-database-load-from-csv-with-bcp.md)
### [Spostamento di dati tra database cloud con aumento del numero di istanze](sql-database-elastic-scale-overview-split-and-merge.md)

## Query
### [SQL Server Management Studio](sql-database-connect-query-ssms.md)
### [Esecuzione di query su più partizioni](sql-database-elastic-scale-multishard-querying.md)
### Query tra database
#### [Panoramica](sql-database-elastic-query-overview.md)
#### [Esecuzione di query tra database con schemi diversi](sql-database-elastic-query-vertical-partitioning.md)
#### [Creazione di report tra database](sql-database-elastic-query-horizontal-partitioning.md)
#### [Transazioni distribuite in database cloud](sql-database-elastic-transactions-overview.md)

## Ripristino
### Ripristinare un database eliminato
#### [Portale di Azure](sql-database-restore-deleted-database-portal.md)
#### [PowerShell](sql-database-restore-deleted-database-powershell.md)
### Ripristino temporizzato
#### [Portale di Azure](sql-database-point-in-time-restore-portal.md)
#### [PowerShell](sql-database-point-in-time-restore-powershell.md)
### Ripristino geografico
#### [Portale di Azure](sql-database-geo-restore-portal.md)
#### [PowerShell](sql-database-geo-restore-powershell.md)
#### [Singola tabella](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
### [Eseguire il ripristino in seguito un'interruzione del data center](sql-database-disaster-recovery.md)
### [Eseguire le esercitazioni sul ripristino di emergenza](sql-database-disaster-recovery-drills.md)

## Replica
### [Panoramica della replica geografica attiva](sql-database-geo-replication-overview.md)
### Configurare
#### [PowerShell](sql-database-geo-replication-powershell.md)
#### [T-SQL](sql-database-geo-replication-transact-sql.md)
### Failover
#### [Portale di Azure](sql-database-geo-replication-failover-portal.md)
#### [PowerShell](sql-database-geo-replication-failover-powershell.md)
#### [T-SQL](sql-database-geo-replication-failover-transact-sql.md)

## Risoluzione dei problemi
### [Problemi di connessione](sql-database-troubleshoot-common-connection-issues.md)
### [Errore di connessione temporaneo](sql-database-troubleshoot-connection.md)
### [Diagnosticare e prevenire](sql-database-connectivity-issues.md)
### [Autorizzazioni](sql-database-troubleshoot-permissions.md)
### [Spostamento di database](sql-database-troubleshoot-moving-data.md)

# Riferimento
## [PowerShell](/powershell/azureps-cmdlets-docs/)
## [PowerShell - Classica](/powershell/servicemanagement/)
## [Java](/java/api/)
## [.NET](/dotnet/api/)
## [T-SQL](https://msdn.microsoft.com/library/azure/bb510741.aspx)
## [Azure SQL Database Cmdlets](https://msdn.microsoft.com/library/azure/mt574084(v=azure.300\).aspx)
## [Cmdlet di SQL Server](https://msdn.microsoft.com/library/mt740629.aspx)
## [REST](/rest/api/sql/)

## Libreria di gestione del database SQL
### [Informazioni di riferimento sulla libreria di gestione del database SQL](https://msdn.microsoft.com/library/azure/mt349017.aspx)
### [Ottenere il pacchetto della libreria di gestione del database SQL](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
## [Driver di SQL Server](https://msdn.microsoft.com/library/mt654049.aspx)
### [ADO.NET](https://msdn.microsoft.com/library/mt657768.aspx)
### [JDBC](https://msdn.microsoft.com/library/mt484311.aspx)
### [Node.js](https://msdn.microsoft.com/library/mt652093.aspx)
### [ODBC](https://msdn.microsoft.com/library/mt654048.aspx)
### [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
### [Python](https://msdn.microsoft.com/library/mt652092.aspx)
### [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)

# Risorse
## [Prezzi](https://azure.microsoft.com/pricing/details/sql-database/)
## [Aggiornamenti del servizio](https://azure.microsoft.com/updates/?service=sql-database)
## [Strumenti di SQL Server](https://msdn.microsoft.com/library/mt238365.aspx)
## [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
## [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
## [BCP](https://msdn.microsoft.com/library/ms162802.aspx)
## [SQLCMD](https://msdn.microsoft.com/library/ms162773.aspx)
## [SqlPackage](https://msdn.microsoft.com/hh550080.aspx)
## [Forum](https://social.msdn.microsoft.com/Forums/home?forum=ssdsgetstarted)
## [Video](https://azure.microsoft.com/documentation/videos/index/?services=sql-database)


<!--HONumber=Nov16_HO2-->


