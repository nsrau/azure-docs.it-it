# Panoramica
## [Informazioni sul database SQL](sql-database-technical-overview.md)
### [Livelli di servizio](sql-database-service-tiers.md)
### [DTUs and eDTUs](sql-database-what-is-a-dtu.md) (DTU ed EDTU)
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
### Formazione
#### [Server](sql-database-server-overview.md)
#### [Database singoli](sql-database-overview.md)
#### [Multiple databases](sql-database-elastic-scale-introduction.md) (Più database)
##### Mapping tenants (Mapping dei tenant)
###### [Elastic client library](sql-database-elastic-database-client-library.md) (Libreria di client elastici)
###### [Gestore mappe partizioni](sql-database-elastic-scale-shard-map-management.md)
###### [Routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md)
###### [Gestire le credenziali](sql-database-elastic-scale-manage-credentials.md)
###### [Esecuzione di query su più partizioni](sql-database-elastic-scale-multishard-querying.md)
##### Pool elastici (pool di risorse)
###### [Che cos'è un pool elastico?](sql-database-elastic-pool.md)
###### [Quando usare un pool elastico](sql-database-elastic-pool-guidance.md)
###### [Prezzi del pool elastico](sql-database-elastic-pool-price.md)
##### Database partizionati
###### [Glossario sugli strumenti elastici](sql-database-elastic-scale-glossary.md)
###### [Spostamento di dati tra partizioni](sql-database-elastic-scale-overview-split-and-merge.md)
###### [Domande frequenti sugli strumenti elastici](sql-database-elastic-scale-faq.md)
##### Query elastica (query tra database)
###### [Che cos'è una query elastica?](sql-database-elastic-query-overview.md)
##### Transazioni elastiche (transazioni distribuite)
###### [Transactions across cloud databases](sql-database-elastic-transactions-overview.md) (Transazioni tra database cloud)
##### Processi elastici (processi tra database)
###### [Che cos'è un processo elastico?](sql-database-elastic-jobs-overview.md)
#### [Using Azure RemoteApp to connect to SQL Database](sql-database-ssms-remoteapp.md) (Uso di Azure RemoteApp per connettersi al database SQL)
#### [Gestire i database SQL usando il servizio Automazione di Azure](sql-database-manage-automation.md)
### Procedura consigliata
#### [Creare un database singolo usando il Portale di Azure](sql-database-get-started.md)
#### [Creare un database singolo con PowerShell](sql-database-get-started-powershell.md)
#### [Creare un database singolo con C#](sql-database-get-started-csharp.md)
#### [Creare un'applicazione partizionata](sql-database-elastic-scale-get-started.md)
#### [Spostare i dati tra partizioni](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
#### [Introduzione ai processi elastici](sql-database-elastic-jobs-getting-started.md)
#### [Introduzione alle query elastiche](sql-database-elastic-query-getting-started-vertical.md)
#### [Creare report con le query elastiche](sql-database-elastic-query-getting-started.md)
#### [Eseguire query in database con schemi diversi](sql-database-elastic-query-vertical-partitioning.md)
#### [Creazione di report tra database con aumento di istanze](sql-database-elastic-query-horizontal-partitioning.md)
## Eseguire la migrazione e lo spostamento dei dati
### Formazione
#### [Eseguire la migrazione di un database](sql-database-cloud-migrate.md)
#### [Replica transazionale](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
#### [Sincronizzazione dei dati](sql-database-get-started-sql-data-sync.md)
#### [Copiare un database SQL](sql-database-copy.md)
## Regole del firewall, autenticazione e autorizzazione
### Formazione
#### [Panoramica](sql-database-security.md)
#### [Linee guida sulla sicurezza](sql-database-security-guidelines.md)
#### [Firewall](sql-database-firewall-configure.md)
#### [Gestire gli accessi](sql-database-manage-logins.md)
### Procedura consigliata
#### [Autenticazione e autorizzazione di SQL](sql-database-get-started-security.md)
## Garantire sicurezza e protezione dei dati
### Formazione
#### Controllo
##### [Controllo](sql-database-auditing-get-started.md)
##### [Supporto dei client di livello inferiore e modifiche all'endpoint IP per il controllo](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)
#### [Introduzione al rilevamento delle minacce](sql-database-threat-detection-get-started.md)
#### Crittografare i dati
##### [Insieme di credenziali delle chiavi di Azure](sql-database-always-encrypted-azure-key-vault.md)
##### [Transparent Data Encryption](https://msdn.microsoft.com/library/azure/dn948096)
##### [Crittografia delle colonne](https://msdn.microsoft.com/library/azure/ms179331)
#### Mascherare i dati
##### Maschera dati dinamica
###### [portale di Azure](sql-database-dynamic-data-masking-get-started.md)
### Procedura consigliata
#### [Maschera dati dinamica con il portale di Azure](sql-database-dynamic-data-masking-get-started.md)
##### [Crittografia sempre attiva con l'archivio certificati di Windows](sql-database-always-encrypted.md)
## Continuità aziendale
### Formazione
#### [Panoramica](sql-database-business-continuity.md)
#### [Backup del database](sql-database-automated-backups.md)
#### [Conservazione a lungo termine](sql-database-long-term-retention.md)
#### [Ripristino di database tramite backup](sql-database-recovery-using-backups.md)
#### [Ripristinare una singola tabella](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
#### [Eseguire il ripristino in seguito un'interruzione del data center](sql-database-disaster-recovery.md)
#### [Requisiti di autenticazione per il ripristino di emergenza](sql-database-geo-replication-security-config.md)
#### [Scenari di progettazione della continuità aziendale](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [Strategie di ripristino di emergenza con i pool elastici](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [Aggiornamenti in sequenza](sql-database-manage-application-rolling-upgrade.md)
#### [Eseguire le esercitazioni sul ripristino di emergenza](sql-database-disaster-recovery-drills.md)
#### [Panoramica della replica geografica attiva](sql-database-geo-replication-overview.md)
### Procedura consigliata
#### [Get started with SQL Database backup and restore](sql-database-get-started-backup-recovery.md) (Introduzione a backup e ripristino del database SQL)
## Sviluppo di app
### Formazione
#### [Panoramica dello sviluppo dell'applicazione di database](sql-database-develop-overview.md)
#### [Librerie di connettività](sql-database-libraries.md)
#### [Applicazioni SaaS multi-tenant](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### [Ridimensionamento di applicazioni multi-tenant SaaS con sicurezza a livello di riga](sql-database-elastic-tools-multi-tenant-row-level-security.md)
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
#### [Usare Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [Uso della libreria client con Dapper](sql-database-elastic-scale-working-with-dapper.md)
### Implementazioni dei clienti
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
#### [Ottimizzazione in memoria](sql-database-in-memory.md)
### Procedura consigliata
#### [Sviluppo di SQL Server](https://msdn.microsoft.com/library/ms179422.aspx)
#### [Adottare OLTP in memoria](sql-database-in-memory-oltp-migration.md)
## Monitoraggio e ottimizzazione
### Formazione
#### [Database singoli](sql-database-single-database-monitor.md)
#### [Panoramica di Advisor per database SQL](sql-database-advisor.md)
#### [Informazioni aggiuntive sui database singoli](sql-database-performance-guidance.md)
#### [Informazioni dettagliate sul carico di lavoro nel portale di Azure](sql-database-performance.md)
#### [Usare l'invio in batch](sql-database-use-batching-to-improve-performance.md)
#### [Eventi estesi](sql-database-xevent-db-diff-from-svr.md)
## Database SQL V11
### [Ritiro dell'edizione Web e Business](sql-database-web-business-sunset-faq.md)
### [Service Tier Advisor](sql-database-service-tier-advisor.md)
### [Strumento di valutazione del pool elastico](sql-database-elastic-pool-database-assessment-powershell.md)
### [Aggiornamento alla versione 12](sql-database-v12-plan-prepare-upgrade.md)
#### [Aggiornamento con il portale di Azure](sql-database-upgrade-server-portal.md)
#### [Aggiornamento con PowerShell](sql-database-upgrade-server-powershell.md)
# Procedure
## Creare e gestire
### [Gestire un database SQL tramite il Portale di Azure](sql-database-manage-portal.md)
### [Manage SQL Database using PowerShell](sql-database-manage-powershell.md) (Gestire un database SQL tramite PowerShell)
### [Gestire un database SQL con SSMS](sql-database-manage-azure-ssms.md)
### Server
#### [Create servers](sql-database-create-servers.md) (Creare server)
#### [View or update server settings](sql-database-view-update-server-settings.md) (Visualizzare o aggiornare le impostazioni del server)
### Database singoli
#### [Create single databases](sql-database-create-databases.md) (Creare database singoli)
#### [View or update database settings](sql-database-view-update-database-settings.md) (Visualizzare o aggiornare le impostazioni del database)
### Regole del firewall
#### [Create firewall rules using the Azure portal](sql-database-configure-firewall-settings.md) (Creare regole firewall con il Portale di Azure)
#### [Create firewall rules using PowerShell](sql-database-configure-firewall-settings-powershell.md) (Creare regole firewall con PowerShell)
#### [Create firewall rules using REST API](sql-database-configure-firewall-settings-rest.md) (Creare regole firewall con l'API REST)
#### [Create firewall rules using T-SQL](sql-database-configure-firewall-settings-tsql.md) (Creare regole firewall con T-SQL)
### Più database
#### [Aggiornare la libreria client nelle applicazioni client](sql-database-elastic-scale-upgrade-client-library.md)
#### Database partizionati
##### [Security configuration](sql-database-elastic-scale-split-merge-security-configuration.md) (Configurazione di sicurezza)
##### [Aggiungere una partizione](sql-database-elastic-scale-add-a-shard.md)
##### [Fix shard map problems](sql-database-elastic-database-recovery-manager.md) (Risolvere problemi relativi alle mappe di partizioni)
##### [Eseguire la migrazione dei database esistenti con aumento delle istanze a database partizionati](sql-database-elastic-convert-to-use-elastic-tools.md)
##### [Creare contatori delle prestazioni per Gestore mappe partizioni](sql-database-elastic-database-perf-counters.md)
#### Processi elastici
##### [Come si installa il servizio di processi elastici?](sql-database-elastic-jobs-service-installation.md)
##### [Creare e gestire processi elastici usando PowerShell ](sql-database-elastic-jobs-powershell.md) 
##### [Creare e gestire processi elastici con il Portale di Azure](sql-database-elastic-jobs-create-and-manage.md)
##### [Come si disinstallano i processi elastici?](sql-database-elastic-jobs-uninstall.md)
#### Pool elastici
##### [Creare usando il portale di Azure](sql-database-elastic-pool-create-portal.md)
##### [Creare usando PowerShell](sql-database-elastic-pool-create-powershell.md)
##### [Creare usando C#](sql-database-elastic-pool-create-csharp.md)
##### [Gestire usando il portale di Azure](sql-database-elastic-pool-manage-portal.md)
##### [Gestire usando PowerShell](sql-database-elastic-pool-manage-powershell.md)
##### [Gestire usando C#](sql-database-elastic-pool-manage-csharp.md)
##### [Gestire usando T-SQL](sql-database-elastic-pool-manage-tsql.md)
##  Autenticare e autorizzare
### [Autenticazione di Azure AD](sql-database-aad-authentication.md)
### [Autenticazione a più fattori](sql-database-ssms-mfa-authentication.md)
## Crittografare i dati
### [Transparent Data Encryption](https://msdn.microsoft.com/library/azure/dn948096)
### [Crittografia delle colonne](https://msdn.microsoft.com/library/azure/ms179331)
## Eseguire la migrazione dei database
### Determinare la compatibilità
#### [Determinare la compatibilità usando l'utilità pacchetti SQL](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
#### [Determinare la compatibilità usando SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
### Risolvere i problemi di compatibilità
#### [Risolvere problemi di compatibilità usando SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
#### [Risolvere problemi di compatibilità usando SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
#### [Risolvere problemi di compatibilità usando SMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
### [Eseguire la migrazione usando la procedura guidata di migrazione SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
## Monitorare e ottimizzare
### [Query Performance Insight](sql-database-query-performance.md)
### [Advisor per database SQL](sql-database-advisor-portal.md)
### [Viste a gestione dinamica](sql-database-monitoring-with-dmvs.md)
### [Livelli di compatibilità](sql-database-compatibility-level-query-performance-130.md)
### [Suggerimenti per l'ottimizzazione delle prestazioni](sql-database-troubleshoot-performance.md)
### Cambiare i livelli di servizio e di prestazioni
#### [Modificare i livelli di servizio con il Portale di Azure](sql-database-scale-up.md)
#### [Modificare i livelli di servizio con PowerShell](sql-database-scale-up-powershell.md)
### [Creare avvisi](sql-database-insights-alerts-portal.md)
#### [Monitorare l'archiviazione OLTP in memoria](sql-database-in-memory-oltp-monitoring.md)
### Archivio query
#### [Monitoraggio delle prestazioni mediante l'archivio query](https://msdn.microsoft.com/library/dn817826.aspx)
#### [Scenari di utilizzo dell'archivio query](https://msdn.microsoft.com/library/mt614796.aspx)
#### [Uso dell'archivio query](sql-database-operate-query-store.md)
### Eventi estesi
#### [Codice di destinazione del file dell'evento](sql-database-xevent-code-event-file.md)
#### [Codice di destinazione del buffer circolare](sql-database-xevent-code-ring-buffer.md)
## Spostare dati
### Copiare un database SQL
#### [Copiare usando il portale di Azure](sql-database-copy-portal.md)
#### [Copiare usando PowerShell](sql-database-copy-powershell.md)
#### [Copiare usando T-SQL](sql-database-copy-transact-sql.md)
### Esportare il database in un BACPAC
#### [Esportare con il Portale di Azure](sql-database-export.md)
#### [Esportare con SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [Esportare con l'utilità pacchetti SQL](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
#### [Esportare con PowerShell](sql-database-export-powershell.md)
### Importare il database da un BACPAC
#### [Importare con il Portale di Azure](sql-database-import.md)
#### [Importare con PowerShell](sql-database-import-powershell.md)
#### [Importare con SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
#### [Importare con l'utilità pacchetti SQL](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
### [Caricare dal file CSV usando BCP](sql-database-load-from-csv-with-bcp.md)
## Query
### [Query using SSMS](sql-database-connect-query-ssms.md) (Eseguire query con SSMS)
## Backup e ripristino
### Conservazione del backup a lungo termine
#### [Configure long-term backup retention](sql-database-configure-long-term-retention.md) (Configurare la conservazione del backup a lungo termine)
#### [View backups in a Recovery Services vault](sql-database-view-backups-in-vault.md) (Visualizzare i backup in un insieme di credenziali dei Servizi di ripristino)
#### [Restore from long-term backup retention](sql-database-restore-from-long-term-retention.md) (Ripristinare dalla conservazione del backup a lungo termine)
### Ripristinare un database eliminato
#### [Ripristinare un database eliminato con il Portale di Azure](sql-database-restore-deleted-database-portal.md)
#### [Ripristinare un database eliminato con PowerShell](sql-database-restore-deleted-database-powershell.md)
### Ripristino temporizzato
#### [Restore to a point in time](sql-database-point-in-time-restore.md) (Ripristino temporizzato)
#### [View oldest restore point](sql-database-view-oldest-restore-point.md) (Visualizzare il punto di ripristino meno recente)
### Ripristino geografico
#### [Geo-restore using the Azure portal](sql-database-geo-restore-portal.md) (Ripristino geografico con il Portale di Azure)
#### [Geo-restore using PowerShell](sql-database-geo-restore-powershell.md) (Ripristino geografico con PowerShell)
## Replica geografica attiva
### [Configurare con il Portale di Azure](sql-database-geo-replication-portal.md)
### [Configurare con PowerShell](sql-database-geo-replication-powershell.md)
### [Configurare con T-SQL](sql-database-geo-replication-transact-sql.md)
### [Failover con il Portale di Azure](sql-database-geo-replication-failover-portal.md)
### [Failover con PowerShell](sql-database-geo-replication-failover-powershell.md)
### [Failover con T-SQL](sql-database-geo-replication-failover-transact-sql.md)
## Risoluzione dei problemi
### [Problemi di connessione](sql-database-troubleshoot-common-connection-issues.md)
### [Errore di connessione temporaneo](sql-database-troubleshoot-connection.md)
### [Diagnosticare e prevenire](sql-database-connectivity-issues.md)
### [Autorizzazioni](sql-database-troubleshoot-permissions.md)
### [Spostamento di database](sql-database-troubleshoot-moving-data.md)
# Riferimento
## [PowerShell](/powershell/resourcemanager/azurerm.sql/v2.3.0/azurerm.sql)
## [PowerShell (database elastico)](/powershell/elasticdatabasejobs/v0.8.33/elasticdatabasejobs)
## [.NET](/dotnet/api/microsoft.azure.management.sql.models)
## [Java](/java/api/com.microsoft.azure.management.sql)
## [Node.js](https://msdn.microsoft.com/library/mt652093.aspx)
## [Python](https://msdn.microsoft.com/library/mt652092.aspx)
## [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)
## [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
## [T-SQL](https://msdn.microsoft.com/library/bb510741.aspx)
## [REST](https://msdn.microsoft.com/library/azure/mt163571.aspx)

# Risorse correlate
## Libreria di gestione del database SQL
### [Ottenere il pacchetto della libreria di gestione del database SQL](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
## [Driver di SQL Server](https://msdn.microsoft.com/library/mt654049.aspx)
### [ADO.NET](https://msdn.microsoft.com/library/mt657768.aspx)
### [JDBC](https://msdn.microsoft.com/library/mt484311.aspx)
### [ODBC](https://msdn.microsoft.com/library/mt654048.aspx)

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


<!--HONumber=Dec16_HO3-->


