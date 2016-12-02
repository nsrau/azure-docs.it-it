# Panoramica
## [Informazioni sul database SQL](sql-database-technical-overview.md)
### [Livelli di servizio](sql-database-service-tiers.md)
### [Unità di transazione di database](sql-database-what-is-a-dtu.md)
### [Panoramica del benchmark DTU](sql-database-benchmark-overview.md)
### [Limiti delle risorse](sql-database-resource-limits.md)
### [Funzionalità](sql-database-features.md)
### [Domande frequenti sul database SQL](sql-database-faq.md)
## Vantaggi
### [È in grado di apprendere e di adattarsi](sql-database-learn-and-adapt.md)
### [Si ridimensiona immediatamente](sql-database-scale-on-the-fly.md)
### [Crea app multi-tenant](sql-database-build-multi-tenant-apps.md)
### [Garantisce sicurezza e protezione](sql-database-helps-secures-and-protects.md)
### [È compatibile con l'ambiente](sql-database-works-in-your-environment.md)
## Confronti
### [Confronto tra database SQL e SQL in una macchina virtuale](sql-database-paas-vs-sql-server-iaas.md)
### [Differenze con T-SQL](sql-database-transact-sql-information.md)
### [SQL e NoSQL](../documentdb/documentdb-nosql-vs-sql.md)
## [Strumenti del database SQL](sql-database-manage-overview.md)
## [Esercitazioni sul database SQL](sql-database-explore-tutorials.md)
## [Procedure di avvio rapido per le soluzioni](sql-database-solution-quick-starts.md)
## Sicurezza
### [Centro sicurezza di Azure per il database SQL di Azure](https://azure.microsoft.com/documentation/articles/security-center-sql-database/)
### [Centro sicurezza SQL](https://msdn.microsoft.com/library/azure/bb510589)
# Attività iniziali
## Database e server
### Database singoli
#### Formazione
##### [Esercitazione sul portale di Azure: Creare un database SQL di Azure usando il portale di Azure](sql-database-get-started.md)
#### Procedura consigliata
##### [Esercitazione sul portale di Azure: Creare un database SQL di Azure usando il portale di Azure](sql-database-get-started.md)
##### [Esercitazione sul portale di Azure: Creare un database SQL di Azure usando PowerShell](sql-database-get-started-powershell.md)
##### [Esercitazione sul portale di Azure: Creare un database SQL di Azure usando C#](sql-database-get-started-csharp.md)
### Pool di database elastici
#### Formazione
##### [Pool di database elastici](sql-database-elastic-pool.md)
##### [Quando usare un pool di database elastici](sql-database-elastic-pool-guidance.md)
##### [Prezzi del pool elastico](sql-database-elastic-pool-price.md)
#### Procedura consigliata
##### [Creare usando il portale di Azure](sql-database-elastic-pool-create-portal.md)
##### [Creare usando PowerShell](sql-database-elastic-pool-create-powershell.md)
##### [Creare usando C#](sql-database-elastic-pool-create-csharp.md)
### Aumento del numero di istanze
#### Formazione
##### [Aumento del numero di istanze](sql-database-elastic-scale-introduction.md)
##### [Scalabilità elastica](sql-database-elastic-scale-get-started.md)
##### [Creazione di database cloud scalabili](sql-database-elastic-database-client-library.md)
##### [Processi tra database](sql-database-elastic-jobs-overview.md)
##### [Domande frequenti sugli strumenti elastici](sql-database-elastic-scale-faq.md)
##### [Glossario sugli strumenti di database elastici](sql-database-elastic-scale-glossary.md)
##### [Spostamento di dati tra database cloud con scalabilità orizzontale](sql-database-elastic-scale-overview-split-and-merge.md)
#### Procedura consigliata
##### [Processi elastici](sql-database-elastic-jobs-getting-started.md)
##### [Report tra database](sql-database-elastic-query-getting-started.md)
##### [Query tra database](sql-database-elastic-query-getting-started-vertical.md)
##### [Disinstallare processi elastici](sql-database-elastic-jobs-uninstall.md)
## Eseguire la migrazione e lo spostamento dei dati
### Formazione
#### [Eseguire la migrazione di un database](sql-database-cloud-migrate.md)
#### [Replica transazionale](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
#### [Sincronizzazione dei dati](sql-database-get-started-sql-data-sync.md)
#### [Copiare un database SQL](sql-database-copy.md)
### Procedura consigliata
#### Determinare la compatibilità del database
##### [Utilità pacchetti SQL](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)
#### Risolvere i problemi di compatibilità del database
##### [SQL Server Data Tools](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
##### [Migrazione guidata SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md)
##### [Migrazione guidata di SQL Server Management Studio](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
#### Copiare un database
##### [Copiare usando il portale di Azure](sql-database-copy-portal.md)
##### [Copiare usando PowerShell](sql-database-copy-powershell.md)
##### [Copiare usando T-SQL](sql-database-copy-transact-sql.md)
#### Esportare il database in un BACPAC
##### [Portale di Azure](sql-database-export.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
##### [Utilità pacchetti SQL](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
##### [PowerShell](sql-database-export-powershell.md)
#### Importare il database da un BACPAC
##### [Portale di Azure](sql-database-import.md)
##### [PowerShell](sql-database-import-powershell.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
##### [Utilità pacchetti SQL](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
#### [Caricare dal file CSV usando BCP](sql-database-load-from-csv-with-bcp.md)
## Autenticare e concedere l'accesso
### Formazione
#### [Panoramica](sql-database-security.md)
#### [Linee guida sulla sicurezza](sql-database-security-guidelines.md)
#### [Gestire gli accessi](sql-database-manage-logins.md)
### Procedura consigliata
### [Esercitazione: Autenticazione SQL e accesso](sql-database-get-started-security.md)
## Garantire sicurezza e protezione dei dati
### Formazione
#### Controllo
##### [Controllo](sql-database-auditing-get-started.md)
##### [Supporto dei client di livello inferiore e modifiche all'endpoint IP per il controllo](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)
#### [Introduzione al rilevamento delle minacce](sql-database-threat-detection-get-started.md)
#### Crittografare i dati
##### [Insieme di credenziali delle chiavi di Azure](sql-database-always-encrypted-azure-key-vault.md)
##### [Archivio certificati di Windows](sql-database-always-encrypted.md)
##### [Transparent Data Encryption](https://msdn.microsoft.com/library/azure/dn948096)
##### [Crittografia delle colonne](https://msdn.microsoft.com/library/azure/ms179331)
#### Mascherare i dati
##### Maschera dati dinamica
###### [portale di Azure](sql-database-dynamic-data-masking-get-started.md)
### Procedura consigliata
#### [Maschera dati dinamica con il portale di Azure](sql-database-dynamic-data-masking-get-started.md)
## Continuità aziendale
### Formazione
#### [Panoramica](sql-database-business-continuity.md)
#### [Backup del database](sql-database-automated-backups.md)
#### [Conservazione a lungo termine](sql-database-long-term-retention.md)
#### [Ripristino di database tramite backup](sql-database-recovery-using-backups.md)
#### [Requisiti di autenticazione per il ripristino di emergenza](sql-database-geo-replication-security-config.md)
#### [Scenari di progettazione della continuità aziendale](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [Strategie di ripristino di emergenza con i pool elastici](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [Aggiornamenti in sequenza](sql-database-manage-application-rolling-upgrade.md)
### Procedura consigliata
#### Ripristinare un database eliminato
##### [Portale di Azure](sql-database-restore-deleted-database-portal.md)
##### [PowerShell](sql-database-restore-deleted-database-powershell.md)
#### Ripristino temporizzato
##### [portale di Azure](sql-database-point-in-time-restore-portal.md)
##### [PowerShell](sql-database-point-in-time-restore-powershell.md)
#### Ripristino geografico dei database
##### [portale di Azure](sql-database-geo-restore-portal.md)
##### [PowerShell](sql-database-geo-restore-powershell.md)
#### [Singola tabella](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
#### [Eseguire il ripristino in seguito un'interruzione del data center](sql-database-disaster-recovery.md)
#### [Eseguire le esercitazioni sul ripristino di emergenza](sql-database-disaster-recovery-drills.md)
### Replicare i database
#### [Panoramica della replica geografica attiva](sql-database-geo-replication-overview.md)
#### Configurare la replica geografica attiva
##### [portale di Azure](sql-database-geo-replication-portal.md)
##### [PowerShell](sql-database-geo-replication-powershell.md)
##### [T-SQL](sql-database-geo-replication-transact-sql.md)
#### Failover con la replica geografica attiva
##### [portale di Azure](sql-database-geo-replication-failover-portal.md)
##### [PowerShell](sql-database-geo-replication-failover-powershell.md)
##### [T-SQL](sql-database-geo-replication-failover-transact-sql.md)
## Sviluppo di app
### Formazione
#### [Panoramica dello sviluppo dell'applicazione di database](sql-database-develop-overview.md)
#### [Librerie di connettività](sql-database-libraries.md)
#### [Applicazioni SaaS multi-tenant](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### [Usare le porte successive alla 1433 per ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md)
#### [Ottenere i valori obbligatori per l'autenticazione di un'applicazione](sql-database-client-id-keys.md)
### Procedura consigliata
#### Connettere un'applicazione
##### [.NET](sql-database-develop-dotnet-simple.md)
##### [C e C++](sql-database-develop-cplusplus-simple.md)
##### [Java](sql-database-develop-java-simple.md)
##### [Node.js](sql-database-develop-nodejs-simple.md)
##### [PHP](sql-database-develop-php-simple.md)
##### [Python](sql-database-develop-python-simple.md)
##### [Ruby](sql-database-develop-ruby-simple.md)
##### [Excel](sql-database-connect-excel.md)
#### [Connettersi a Visual Studio](sql-database-connect-query.md)
#### [Compilare un'applicazione client](https://www.microsoft.com/sql-server/developer-get-started)
#### [Uso dei messaggi di errore](sql-database-develop-error-messages.md)
### Implementazioni dei clienti di software come un servizio
#### [Daxko/CSI Software](sql-database-implementation-daxko.md)
#### [GEP](sql-database-implementation-gep.md)
#### [SnelStart](sql-database-implementation-snelstart.md)
#### [Umbraco](sql-database-implementation-umbraco.md)
## Sviluppo di database
### Formazione
#### Tabelle temporali
##### [Tabelle temporali](sql-database-temporal-tables.md)
##### [Criteri di conservazione](sql-database-temporal-tables-retention-policy.md)
#### [Dati JSON](sql-database-json-features.md)
#### [In memoria](sql-database-in-memory.md)
### Procedura consigliata
#### [Sviluppo di SQL Server](https://msdn.microsoft.com/library/ms179422.aspx)
## Monitoraggio e ottimizzazione
### Formazione
#### [Database singoli](sql-database-single-database-monitor.md)
#### [Panoramica di Advisor per database SQL](sql-database-advisor.md)
#### [Linee guida per i database singoli](sql-database-performance-guidance.md)
#### [Informazioni dettagliate sul carico di lavoro nel portale di Azure](sql-database-performance.md)
#### [Usare l'invio in batch](sql-database-use-batching-to-improve-performance.md)
## Database SQL V11
### [Ritiro dell'edizione Web e Business](sql-database-web-business-sunset-faq.md)
### [Service Tier Advisor](sql-database-service-tier-advisor.md)
### [Strumento di valutazione del pool elastico](sql-database-elastic-pool-database-assessment-powershell.md)
### [Aggiornamento alla versione 12](sql-database-v12-plan-prepare-upgrade.md)
#### [Aggiornamento con il portale di Azure](sql-database-upgrade-server-portal.md)
#### [Aggiornamento con PowerShell](sql-database-upgrade-server-powershell.md)
#### [Indicazioni sul piano tariffario](sql-database-service-tier-advisor.md)
# Procedure
## Creare e gestire
### Server e database
#### [Database singoli](sql-database-manage-portal.md)
#### [portale di Azure](sql-database-get-started.md)
#### [C#](sql-database-get-started-csharp.md)
#### [PowerShell](sql-database-manage-powershell.md)
#### [SQL Server Management Studio](sql-database-manage-azure-ssms.md)
### Pool di database elastici
#### [Creare usando il portale di Azure](sql-database-elastic-pool-create-portal.md)
#### [Creare usando PowerShell](sql-database-elastic-pool-create-powershell.md)
#### [Creare usando C#](sql-database-elastic-pool-create-csharp.md)
#### [Gestire usando il portale di Azure](sql-database-elastic-pool-manage-portal.md)
#### [Gestire usando PowerShell](sql-database-elastic-pool-manage-powershell.md)
#### [Gestire usando C#](sql-database-elastic-pool-manage-csharp.md)
#### [Gestire usando T-SQL](sql-database-elastic-pool-manage-tsql.md)
### Database partizionati
#### [Usare Gestore mappe partizioni](sql-database-elastic-scale-shard-map-management.md)
#### [Configurazione della sicurezza per il servizio di divisione/unione](sql-database-elastic-scale-split-merge-security-configuration.md)
#### [Uso di Dapper](sql-database-elastic-scale-working-with-dapper.md)
#### [Usare Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [Routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md)
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
### Processi e automazione
#### [Installazione di un servizio](sql-database-elastic-jobs-service-installation.md)
#### [Creare e gestire processi elastici nel portale di Azure](sql-database-elastic-jobs-create-and-manage.md)
#### [Creare e gestire processi elastici usando PowerShell ](sql-database-elastic-jobs-powershell.md)
#### [Aggiornare la libreria client](sql-database-elastic-scale-upgrade-client-library.md)
#### [Gestire i database SQL usando il servizio Automazione di Azure](sql-database-manage-automation.md)
### [Firewall](sql-database-firewall-configure.md)
## Crittografare i dati
### [Panoramica di Always Encrypted](sql-database-always-encrypted.md)
### [Transparent Data Encryption](https://msdn.microsoft.com/library/azure/dn948096)
### [Crittografia delle colonne](https://msdn.microsoft.com/library/azure/ms179331)
## Migrazione
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
### [Query Performance Insight](sql-database-query-performance.md)
### [Advisor per database SQL](sql-database-advisor-portal.md)
### [Viste a gestione dinamica](sql-database-monitoring-with-dmvs.md)
### [Livelli di compatibilità](sql-database-compatibility-level-query-performance-130.md)
### [Contatori delle prestazioni per Gestore mappe partizioni](sql-database-elastic-database-perf-counters.md)
### [Suggerimenti per l'ottimizzazione delle prestazioni](sql-database-troubleshoot-performance.md)
### Cambiare i livelli di servizio e di prestazioni
#### [Uso del portale di Azure](sql-database-scale-up.md)
#### [Uso di PowerShell](sql-database-scale-up-powershell.md)
### [Creare avvisi](sql-database-insights-alerts-portal.md)
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
## [Cmdlet del database SQL di Azure](/powershell/resourcemanager/AzureRM.Sql/v1.0.12/AzureRM.Sql)
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
## [Forum MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/sql-azure)
## [Video](https://azure.microsoft.com/documentation/videos/index/?services=sql-database)
## [Aggiornamenti del servizio](https://azure.microsoft.com/updates/?service=sql-database)
## [Strumenti di SQL Server](https://msdn.microsoft.com/library/mt238365.aspx)
## [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
## [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
## [BCP](https://msdn.microsoft.com/library/ms162802.aspx)
## [SQLCMD](https://msdn.microsoft.com/library/ms162773.aspx)
## [SqlPackage](https://msdn.microsoft.com/hh550080.aspx)


<!--HONumber=Nov16_HO4-->


