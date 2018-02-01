# Panoramica

## [Informazioni su SQL Data Warehouse](sql-data-warehouse-overview-what-is.md)
## [Scheda di riferimento rapido](cheat-sheet.md)

# Guide introduttive

## [Creare e connettere - portale](create-data-warehouse-portal.md)
## Sospendere e riprendere le risorse di calcolo
### [di Microsoft Azure](pause-and-resume-compute-portal.md)
### [PowerShell](pause-and-resume-compute-powershell.md)


# Esercitazioni
## [1 - Caricare dati dal BLOB](load-data-from-azure-blob-storage-using-polybase.md)

# Concetti
## Funzionalità del servizio
### [Architettura MPP](massively-parallel-processing-mpp-architecture.md)
### [Livelli di prestazioni](performance-tiers.md)
### [Unità Data Warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md)
### [Backup del data warehouse](sql-data-warehouse-backups.md)
### [Controllo](sql-data-warehouse-auditing-overview.md)
### [Limiti di capacità](sql-data-warehouse-service-capacity-limits.md)
### [DOMANDE FREQUENTI](sql-data-warehouse-overview-faq.md)

## Sicurezza
### [Panoramica](sql-data-warehouse-overview-manage-security.md)
### [Autenticazione](sql-data-warehouse-authentication.md)


## Eseguire la migrazione a SQL Data Warehouse
### [Panoramica](sql-data-warehouse-overview-migrate.md)
### [Eseguire la migrazione dello schema](sql-data-warehouse-migrate-schema.md)
### [Eseguire la migrazione del codice](sql-data-warehouse-migrate-code.md)
### [Eseguire la migrazione dei dati](sql-data-warehouse-migrate-data.md)

## Caricare e spostare dati
### [Overview](design-elt-data-loading.md)
### [Procedure consigliate](guidance-for-loading-data.md)


## Integrare
### [Panoramica](sql-data-warehouse-overview-integrate.md)
### [Query elastica del database SQL](how-to-use-elastic-query-with-sql-data-warehouse.md)


## Monitorare e ottimizzare
### [Linee guida](resource-classes-for-workload-management.md)
### [Compressione columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)
### [Monitoraggio](sql-data-warehouse-manage-monitor.md)
### [Risolvere i problemi](sql-data-warehouse-troubleshoot.md)

## Sviluppare data warehouse
### [Panoramica](sql-data-warehouse-overview-develop.md)
### [Componenti del data warehouse](sql-data-warehouse-overview-workload.md)

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

# Guide alle procedure
## Funzionalità del servizio
### [Ripristinare un data warehouse - portale](sql-data-warehouse-restore-database-portal.md)
### [Ripristinare un data warehouse - PowerShell](sql-data-warehouse-restore-database-powershell.md)
### [Ripristinare un data warehouse - API REST](sql-data-warehouse-restore-database-rest-api.md)

## Sicurezza
### [Abilitare la crittografia - portale](sql-data-warehouse-encryption-tde.md)
### [Abilitare la crittografia - T-SQL](sql-data-warehouse-encryption-tde-tsql.md)
### [Introduzione al rilevamento delle minacce](sql-data-warehouse-security-threat-detection.md)


## Caricare e spostare dati
### [Dati pubblici Contoso](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)
### [Archivio Data Lake di Azure](sql-data-warehouse-load-from-azure-data-lake-store.md)
### [BCP](sql-data-warehouse-load-with-bcp.md)
### [Data Factory](sql-data-warehouse-load-with-data-factory.md)
### [AzCopy](sql-data-warehouse-load-from-sql-server-with-polybase.md)
### [RedGate](sql-data-warehouse-load-with-redgate.md)
### [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)


## Integrare
### [Configurare la query elastica del database SQL](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md)
### [Aggiungere un processo di Analisi di flusso di Azure](sql-data-warehouse-integrate-azure-stream-analytics.md)
### [Usare Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
### [Visualizzare con Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)

## Monitorare e ottimizzare
### [Analizzare il carico di lavoro](analyze-your-workload.md)

## Scalabilità orizzontale
### [Gestire il calcolo - portale](sql-data-warehouse-manage-compute-portal.md)
### [Gestire il calcolo - PowerShell](sql-data-warehouse-manage-compute-powershell.md)
### [Gestire il calcolo - API REST](sql-data-warehouse-manage-compute-rest-api.md)
### [Automatizzare i livelli di calcolo](manage-compute-with-azure-functions.md)


# Riferimenti


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
