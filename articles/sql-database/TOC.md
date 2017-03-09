# Panoramica
## [Informazioni sul database SQL](sql-database-technical-overview.md)
### [Livelli di servizio](sql-database-service-tiers.md)
### [DTUs and eDTUs](sql-database-what-is-a-dtu.md) (DTU ed EDTU)
### [Panoramica del benchmark DTU](sql-database-benchmark-overview.md)
### [Limiti delle risorse](sql-database-resource-limits.md)
### [Funzionalità](sql-database-features.md)
### [Domande frequenti sul database SQL](sql-database-faq.md)
## Confronti
### [Confronto tra database SQL e SQL in una macchina virtuale](sql-database-paas-vs-sql-server-iaas.md)
### [Differenze con T-SQL](sql-database-transact-sql-information.md)
### [SQL e NoSQL](../documentdb/documentdb-nosql-vs-sql.md)
## [Strumenti del database SQL](sql-database-manage-overview.md)
## [Esercitazioni sul database SQL](sql-database-explore-tutorials.md)
## Sicurezza
### [Panoramica della sicurezza](sql-database-security-overview.md)
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
#### [Esportare un database](sql-database-export.md)
#### [Sincronizzazione dei dati](sql-database-get-started-sql-data-sync.md)
#### [Copiare un database SQL](sql-database-copy.md)
## Regole del firewall, autenticazione e autorizzazione
### Formazione
#### [Controllo di accesso](sql-database-control-access.md)
#### [Regole del firewall](sql-database-firewall-configure.md)
#### [Gestire gli accessi](sql-database-manage-logins.md)
#### [Autenticazione di Azure AD](sql-database-aad-authentication.md)
#### [Autenticazione a più fattori](sql-database-ssms-mfa-authentication.md)
### Procedura consigliata
#### [Autenticazione e autorizzazione di SQL](sql-database-control-access-sql-authentication-get-started.md)
#### [Autenticazione e autorizzazione di Azure AD](sql-database-control-access-aad-authentication-get-started.md)
## Garantire sicurezza e protezione dei dati
### Formazione
#### Controllo
##### [Controllo](sql-database-auditing.md)
##### [Supporto dei client di livello inferiore e modifiche all'endpoint IP per il controllo](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)
#### [Introduzione al rilevamento delle minacce](sql-database-threat-detection.md)
#### Crittografare i dati
##### [Transparent Data Encryption](https://msdn.microsoft.com/library/azure/dn948096)
##### [Crittografia delle colonne](https://msdn.microsoft.com/library/azure/ms179331)
#### Mascherare i dati
##### [Maschera dati dinamica](sql-database-dynamic-data-masking-get-started.md)
### Procedura consigliata
#### [Maschera dati dinamica con il portale di Azure](sql-database-dynamic-data-masking-get-started-portal.md)
#### [Configurare il controllo usando il portale di Azure](sql-database-auditing-portal.md)
#### [Configurare il controllo usando PowerShell](sql-database-auditing-powershell.md)
#### [Configurare il controllo usando l'API REST](sql-database-auditing-rest.md)
#### [Always Encrypted con i certificati di Windows](sql-database-always-encrypted.md)
#### [Always Encrypted con Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md)
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
#### [Portale di Azure: backup e ripristino](sql-database-get-started-backup-recovery-portal.md)
#### [PowerShell: backup e ripristino](sql-database-get-started-backup-recovery-powershell.md)
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
## Monitorare e ottimizzare
### Formazione
#### [Database singoli](sql-database-single-database-monitor.md)
#### [Panoramica di Advisor per database SQL](sql-database-advisor.md)
#### [Informazioni aggiuntive sui database singoli](sql-database-performance-guidance.md)
#### [Informazioni dettagliate sulle prestazioni: portale di Azure](sql-database-performance.md)
#### [Usare l'invio in batch](sql-database-use-batching-to-improve-performance.md)
#### [Eventi estesi](sql-database-xevent-db-diff-from-svr.md)
## Database SQL V11
### [Ritiro dell'edizione Web e Business](sql-database-web-business-sunset-faq.md)
### [Service Tier Advisor](sql-database-service-tier-advisor.md)
### [Strumento di valutazione del pool elastico](sql-database-elastic-pool-database-assessment-powershell.md)
### [Aggiornamento alla versione&12;](sql-database-v12-plan-prepare-upgrade.md)
#### [Aggiornamento con il portale di Azure](sql-database-upgrade-server-portal.md)
#### [Aggiornamento con PowerShell](sql-database-upgrade-server-powershell.md)
# Procedure
## Creare e gestire
### [Gestire usando il portale di Azure](sql-database-manage-portal.md)
### [Gestire usando PowerShell](sql-database-manage-powershell.md)
### [Gestire usando SSMS](sql-database-manage-azure-ssms.md)
### Server
#### [Gestire usando il portale di Azure](sql-database-manage-servers-portal.md)
#### [Gestire usando PowerShell](sql-database-manage-servers-powershell.md)
### Database singoli
#### [Gestire usando il portale di Azure](sql-database-manage-single-databases-portal.md)
#### [Gestire usando PowerShell](sql-database-manage-single-databases-powershell.md)
#### [Gestire usando Transact-SQL](sql-database-manage-single-databases-tsql.md)
### Regole del firewall
#### [Creare usando il portale di Azure](sql-database-configure-firewall-settings.md)
#### [Creare usando PowerShell](sql-database-configure-firewall-settings-powershell.md)
#### [Creare usando l'API REST](sql-database-configure-firewall-settings-rest.md)
#### [Creare usando T-SQL](sql-database-configure-firewall-settings-tsql.md)
### Più database
#### [Aggiornare la libreria client nelle applicazioni client](sql-database-elastic-scale-upgrade-client-library.md)
#### Database partizionati
##### [Security configuration](sql-database-elastic-scale-split-merge-security-configuration.md) (Configurazione di sicurezza)
##### [Aggiungere una partizione](sql-database-elastic-scale-add-a-shard.md)
##### [Fix shard map problems](sql-database-elastic-database-recovery-manager.md) (Risolvere problemi relativi alle mappe di partizioni)
##### [Eseguire la migrazione a database partizionati](sql-database-elastic-convert-to-use-elastic-tools.md)
##### [Creare contatori delle prestazioni per Gestore mappe partizioni](sql-database-elastic-database-perf-counters.md)
#### Processi elastici
##### [Come si esegue l'installazione?](sql-database-elastic-jobs-service-installation.md)
##### [Gestire usando PowerShell](sql-database-elastic-jobs-powershell.md) 
##### [Gestire usando il portale di Azure](sql-database-elastic-jobs-create-and-manage.md)
##### [Come si esegue la disinstallazione?](sql-database-elastic-jobs-uninstall.md)
#### Pool elastici
##### [Gestire usando il portale di Azure](sql-database-elastic-pool-manage-portal.md)
##### [Gestire usando PowerShell](sql-database-elastic-pool-manage-powershell.md)
##### [Gestire usando C#](sql-database-elastic-pool-manage-csharp.md)
##### [Gestire usando T-SQL](sql-database-elastic-pool-manage-tsql.md)
## Autenticare e autorizzare
### [Configurare l'autenticazione di Azure AD](sql-database-aad-authentication-configure.md)
### [Configurare l'autenticazione a più fattori](sql-database-ssms-mfa-authentication-configure.md)
## Configurare il rilevamento delle minacce
### [Configurare il rilevamento delle minacce usando il portale di Azure](sql-database-threat-detection-portal.md)
## Crittografare i dati
### [Transparent Data Encryption](https://msdn.microsoft.com/library/azure/dn948096)
### [Crittografia delle colonne](https://msdn.microsoft.com/library/azure/ms179331)
## Monitorare e ottimizzare
### [Query Performance Insight](sql-database-query-performance.md)
### [Advisor per database SQL](sql-database-advisor-portal.md)
### [Viste a gestione dinamica](sql-database-monitoring-with-dmvs.md)
### [Livelli di compatibilità](sql-database-compatibility-level-query-performance-130.md)
### [Suggerimenti per l'ottimizzazione delle prestazioni](sql-database-troubleshoot-performance.md)
### [Creare avvisi](sql-database-insights-alerts-portal.md)
### [Monitorare l'archiviazione OLTP in memoria](sql-database-in-memory-oltp-monitoring.md)
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
#### [Esportare con il Portale di Azure](sql-database-export-portal.md)
#### [Esportare con l'utilità pacchetti SQL](sql-database-export-sqlpackage.md)
#### [Esportare con PowerShell](sql-database-export-powershell.md)
#### [Esportare con SSMS](sql-database-export-ssms.md)
## Importa dati
### [Importare con il Portale di Azure](sql-database-import-portal.md)
### [Importare con PowerShell](sql-database-import-powershell.md)
### [Importazione con SQLPackage](sql-database-import-sqlpackage.md)
### [Caricare dal file CSV usando BCP](sql-database-load-from-csv-with-bcp.md)
## Query
### [Query using SSMS](sql-database-connect-query-ssms.md) (Eseguire query con SSMS)
## Backup e ripristino
### Conservazione del backup a lungo termine
#### [Gestire la conservazione del backup a lungo termine usando il portale di Azure](sql-database-manage-long-term-backup-retention-portal.md)
#### [Gestire la conservazione del backup a lungo termine usando PowerShell](sql-database-manage-long-term-backup-retention-powershell.md)
### Ripristinare un database eliminato
#### [Ripristinare un database eliminato con il Portale di Azure](sql-database-restore-deleted-database-portal.md)
#### [Ripristinare un database eliminato con PowerShell](sql-database-restore-deleted-database-powershell.md)
### Ripristino temporizzato di un database
#### [Ripristino temporizzato con il portale di Azure](sql-database-point-in-time-restore-portal.md)
#### [Ripristino temporizzato di un database con PowerShell](sql-database-point-in-time-restore-powershell.md)
### Eseguire il ripristino da un backup con ridondanza geografica
### [Geo-restore using the Azure portal](sql-database-geo-restore-portal.md) (Ripristino geografico con il Portale di Azure)
### [Geo-restore using PowerShell](sql-database-geo-restore-powershell.md) (Ripristino geografico con PowerShell)
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
## [Interfaccia della riga di comando di Azure 2.0](/cli/azure/appservice)
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
