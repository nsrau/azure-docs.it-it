# Panoramica

## [Che cos'è SQL Data Warehouse?](sql-data-warehouse-overview-what-is.md)
## [Carico di lavoro del data warehouse](sql-data-warehouse-overview-workload.md)

# Attività iniziali

## [Esercitazione per principianti](sql-data-warehouse-get-started-tutorial.md)
## [Esercitazione sulla query elastica](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md)
## [Procedure consigliate](sql-data-warehouse-best-practices.md)
## [Manage](sql-data-warehouse-overview-manage.md)

# Procedure

## Funzionalità del servizio
### [Architettura MPP](massively-parallel-processing-mpp-architecture.md)
### [Livelli di prestazioni](performance-tiers.md)
### [Unità Data Warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md)
### [Domande frequenti](sql-data-warehouse-overview-faq.md)

## Backup e ripristino

### [Panoramica del servizio Backup](sql-data-warehouse-backups.md)
### [Panoramica del servizio di ripristino](sql-data-warehouse-restore-database-overview.md)
#### [Portale di Azure](sql-data-warehouse-restore-database-portal.md)
#### [Azure PowerShell](sql-data-warehouse-restore-database-powershell.md)
#### [REST](sql-data-warehouse-restore-database-rest-api.md)

## Connettere

### [Panoramica](sql-data-warehouse-connect-overview.md)
### [SSMS](sql-data-warehouse-query-ssms.md)
### [Visual Studio](sql-data-warehouse-query-visual-studio.md)
### [Installare Visual Studio](sql-data-warehouse-install-visual-studio.md)
### [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
### [Stringhe di connessione](sql-data-warehouse-connection-strings.md)

## Create
### [Portale di Azure](sql-data-warehouse-get-started-provision.md)
### [Azure PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
### [T-SQL](sql-data-warehouse-get-started-create-database-tsql.md)

## Sviluppare

### [Panoramica](sql-data-warehouse-overview-develop.md)

### Tabelle

#### [Panoramica](sql-data-warehouse-tables-overview.md)
#### [CTAS](sql-data-warehouse-develop-ctas.md)
#### [Tipi di dati](sql-data-warehouse-tables-data-types.md)
#### [Tabelle con distribuzione](sql-data-warehouse-tables-distribute.md)
#### [Indici](sql-data-warehouse-tables-index.md)
#### [Identità](sql-data-warehouse-tables-identity.md)
#### [Partizioni](sql-data-warehouse-tables-partition.md)
#### [Tabelle replicate](design-guidance-for-replicated-tables.md)
#### [Statistiche](sql-data-warehouse-tables-statistics.md)
#### [Temporanee](sql-data-warehouse-tables-temporary.md)

### Query

#### [SQL dinamico](sql-data-warehouse-develop-dynamic-sql.md)
#### [Opzioni di raggruppamento](sql-data-warehouse-develop-group-by-options.md)
#### [Etichette](sql-data-warehouse-develop-label.md)

### Elementi del linguaggio T-SQL

#### [Cicli](sql-data-warehouse-develop-loops.md)
#### [Stored procedure](sql-data-warehouse-develop-stored-procedures.md)
#### [Transazioni](sql-data-warehouse-develop-transactions.md)
#### [Procedure consigliate per le transazioni](sql-data-warehouse-develop-best-practices-transactions.md)
#### [Schemi definiti dall'utente](sql-data-warehouse-develop-user-defined-schemas.md)
#### [Assegnazione di variabili](sql-data-warehouse-develop-variable-assignment.md)
#### [Visualizzazioni](sql-data-warehouse-develop-views.md)

## Integrare

### [Panoramica](sql-data-warehouse-overview-integrate.md)
### [Data Factory](sql-data-warehouse-integrate-azure-data-factory.md)
### [Machine Learning](sql-data-warehouse-integrate-azure-machine-learning.md)
### [Esercitazione di Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
### [Power BI](sql-data-warehouse-integrate-power-bi.md)
### [Visualizzazione di Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
### [Analisi di flusso](sql-data-warehouse-integrate-azure-stream-analytics.md)

### [Query elastica con database SQL e SQL Data Warehouse](how-to-use-elastic-query-with-sql-data-warehouse.md)

## Caricamento

### Concetti
#### [Panoramica](sql-data-warehouse-overview-load.md)
#### [Linee guida di PolyBase](sql-data-warehouse-load-polybase-guide.md)

### Esercitazioni
#### [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)

### Guide alle procedure
#### [Dati di esempio](sql-data-warehouse-load-sample-databases.md)
#### [Archivio Data Lake di Azure](sql-data-warehouse-load-from-azure-data-lake-store.md)
#### [BCP](sql-data-warehouse-load-with-bcp.md)
#### [Data Factory](sql-data-warehouse-load-with-data-factory.md)
#### [PolyBase da archiviazione BLOB](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)
#### [PolyBase da SQL Server](sql-data-warehouse-load-from-sql-server-with-polybase.md)
#### [RedGate](sql-data-warehouse-load-with-redgate.md)
#### [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)

## Eseguire la migrazione

### [Panoramica](sql-data-warehouse-overview-migrate.md)
### [Utilità per la migrazione](sql-data-warehouse-migrate-migration-utility.md)
### [Eseguire la migrazione dello schema](sql-data-warehouse-migrate-schema.md)
### [Eseguire la migrazione del codice](sql-data-warehouse-migrate-code.md)
### [Eseguire la migrazione dei dati](sql-data-warehouse-migrate-data.md)
### [Eseguire la migrazione ad Archiviazione Premium](sql-data-warehouse-migrate-to-premium-storage.md)

## Gestire il calcolo

### [Panoramica](sql-data-warehouse-manage-compute-overview.md)
### [Portale di Azure](sql-data-warehouse-manage-compute-portal.md)
### [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
### [API REST](sql-data-warehouse-manage-compute-rest-api.md)
### [T-SQL](sql-data-warehouse-manage-compute-tsql.md)
### [Automatizzare i livelli di calcolo](manage-compute-with-azure-functions.md)

### Classi di risorse
#### [Linee guida](resource-classes-for-workload-management.md)
#### [Analizzare il carico di lavoro](analyze-your-workload.md)

## Prestazioni

### [Panoramica](sql-data-warehouse-overview-manage-user-queries.md)
### [Compressione columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)
### [Monitoraggio](sql-data-warehouse-manage-monitor.md)

## Sicurezza

### [Panoramica](sql-data-warehouse-overview-manage-security.md)
### [Controllo](sql-data-warehouse-auditing-overview.md)
### [Controllo per client di livello inferiore](sql-data-warehouse-auditing-downlevel-clients.md)
### [Autenticazione](sql-data-warehouse-authentication.md)
### [Crittografia](sql-data-warehouse-encryption-tde.md)
### [Crittografia con T-SQL](sql-data-warehouse-encryption-tde-tsql.md)
### [Introduzione al rilevamento delle minacce](sql-data-warehouse-security-threat-detection.md)

## Risoluzione dei problemi
### [Risoluzione dei problemi](sql-data-warehouse-troubleshoot.md)

# Riferimento

## [Limiti di capacità](sql-data-warehouse-service-capacity-limits.md)
## T-SQL
### [Informazioni di riferimento complete](https://docs.microsoft.com/sql/t-sql/language-reference/)
### [Elementi del linguaggio SQL Data Warehouse](sql-data-warehouse-reference-tsql-language-elements.md)
### [Istruzioni di SQL Data Warehouse](sql-data-warehouse-reference-tsql-statements.md)
## [Viste di sistema](sql-data-warehouse-reference-tsql-system-views.md)
## [Cmdlet di PowerShell](sql-data-warehouse-reference-powershell-cmdlets.md)

# Risorse
## [Roadmap per Azure](https://azure.microsoft.com/roadmap/?category=databases)
## [Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSQLDataWarehouse)
## [Prezzi](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)
## [Calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/)
## [Richieste di funzionalità](https://feedback.azure.com/forums/307516-sql-data-warehouse/)
## [Aggiornamenti del servizio](https://azure.microsoft.com/updates/?product=sql-data-warehouse)
## [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/)
## [Supporto](sql-data-warehouse-get-started-create-support-ticket.md)
## [Video](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

## Partner
### [Business intelligence](sql-data-warehouse-partner-business-intelligence.md)
### [Integrazione dei dati](sql-data-warehouse-partner-data-integration.md)
### [Gestione dei dati](sql-data-warehouse-partner-data-management.md)
