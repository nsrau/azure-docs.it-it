---
title: Tutti gli argomenti per il servizio SQL Data Warehouse | Documentazione Microsoft
description: Tabella di tutti gli argomenti per il servizio di Azure denominato SQL Data Warehouse presente in http://azure.microsoft.com/documentation/articles/, titolo e descrizione.
services: sql-data-warehouse
documentationcenter: 
author: barbkess
manager: jhubbard
editor: 
ms.assetid: a26a6dec-9c08-4415-8f58-4ee1dd41f718
ms.service: sql-data-warehouse
ms.workload: sql-data-warehouse
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ab5b0c283d1bd3fd1b734082d5ccffa4125f6de4


---
# <a name="all-topics-for-azure-sql-data-warehouse-service"></a>Tutti gli argomenti per il servizio SQL Data Warehouse di Azure
Questo argomento elenca tutti gli argomenti che si applicano direttamente al servizio **SQL Data Warehouse** di Azure. È possibile cercare la pagina Web per le parole chiave utilizzando **Ctrl+F**, per trovare gli argomenti di interesse corrente.

## <a name="new"></a>Nuovo
| &nbsp; | Titolo | Descrizione |
| ---:|:--- |:--- |
| 1 |[Backup di SQL Data Warehouse](sql-data-warehouse-backups.md) |Informazioni sui backup dei database predefiniti di SQL Data Warehouse che consentono di ripristinare SQL Data Warehouse di Azure in un punto di ripristino o in un'area geografica diversa. |

## <a name="updated-articles-sql-data-warehouse"></a>Articoli aggiornati, SQL Data Warehouse
Questa sezione elenca gli articoli aggiornati di recente, in cui l'aggiornamento è stato importante o significativo. Per ogni articolo aggiornato, viene visualizzato un frammento di codice del testo di markdown aggiunto. Gli articoli sono stati aggiornati nell'intervallo di date dal **22-08-2016** al **05-10-2016**.

| &nbsp; | Articolo | Testo aggiornato, frammento di codice | Data aggiornamento |
| ---:|:--- |:--- |:--- |
| 2 |[Caricare dati dall’archiviazione BLOB di Azure in un SQL Data Warehouse (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) |/- Per tenere traccia dei byte e dei file SELECT  r.command,  s.request_id,  r.status,  count(distinct input_name) as nbr_files,  sum(s.bytes_processed)/1024/1024 as gb_processed FROM  sys.dm_pdw_exec_requests r  inner join sys.dm_pdw_dms_external_work s  on r.request_id = s.request_id WHERE  r. label  = 'CTAS : Load  cso . DimProduct  '  OR r. label  = 'CTAS : Load  cso . FactOnlineSales  ' GROUP BY  r.command,  s.request_id,  r.status ORDER BY  nbr_files desc,  gb_processed desc; |2016-09-07 |
| 3 |[Ripristino di SQL Data Warehouse](sql-data-warehouse-restore-database-overview.md) |** È possibile ripristinare un data warehouse in pausa?** Per ripristinare un data warehouse in pausa è necessario innanzitutto riportarlo in linea. Dopo che il data warehouse è tornato in linea, si hanno sette giorni di punti di ripristino tra cui scegliere. ** Ripristinare a un'area con ridondanza geografica** Se si usa l'archiviazione con ridondanza geografica è possibile ripristinare il data warehouse al data center associato in un'area geografica diversa. Il data warehouse viene ripristinato dall'ultimo backup giornaliero. ** Ripristino della sequenza temporale** È possibile ripristinare un database a qualsiasi punto di ripristino degli ultimi sette giorni. Gli snapshot vengono eseguiti ogni quattro-otto ore e sono disponibili per sette giorni. Quando uno snapshot supera i sette giorni di vita, scade e il relativo punto di ripristino non è più disponibile. ** Costi di ripristino** Il costo di archiviazione per il data warehouse ripristinato viene fatturato alla tariffa di archiviazione Premium di Azure. Se si mette in pausa un data warehouse ripristinato, l'archiviazione viene fatturata alla tariffa di archiviazione Premium di Azure. Con la messa in pausa la fatturazione viene sospesa |2016-09-29 |

## <a name="get-started"></a>Introduzione
| &nbsp; | Titolo | Descrizione |
| ---:|:--- |:--- |
| 4 |[Autenticazione in Azure SQL Data Warehouse](sql-data-warehouse-authentication.md) |Autenticazione di Azure Active Directory (AAD) e SQL Server in Azure SQL Data Warehouse. |
| 5 |[Procedure consigliate per Azure SQL Data Warehouse](sql-data-warehouse-best-practices.md) |Indicazioni e procedure consigliate da conoscere per lo sviluppo di soluzioni per Azure SQL Data Warehouse. Queste indicazioni aiuteranno a svolgere al meglio il lavoro. |
| 6 |[Driver per Azure SQL Data Warehouse](sql-data-warehouse-connection-strings.md) |Stringhe di connessione e driver per SQL Data Warehouse |
| 7 |[Connettersi ad Azure SQL Data Warehouse](sql-data-warehouse-connect-overview.md) |Come trovare il nome del server e la stringa di connessione per Azure SQL Data Warehouse |
| 8 |[Analizzare i dati con Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md) |Usare Azure Machine Learning per creare un modello predittivo di apprendimento automatico basato sui dati archiviati in Azure SQL Data Warehouse. |
| 9 |[Eseguire query in Azure SQL Data Warehouse (sqlcmd)](sql-data-warehouse-get-started-connect-sqlcmd.md) |Eseguire query in Azure SQL Data Warehouse con l'utilità della riga di comando sqlcmd. |
| 10 |[Creare un database di SQL Data Warehouse usando Transact-SQL (TSQL)](sql-data-warehouse-get-started-create-database-tsql.md) |Informazioni su come creare un'istanza di Azure SQL Data Warehouse con TSQL |
| 11 |[Come creare un ticket di supporto per SQL Data Warehouse](sql-data-warehouse-get-started-create-support-ticket.md) |Come creare un ticket di supporto per SQL Data Warehouse di Azure. |
| 12 |[Caricare i dati con Azure Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md) |Informazioni su come caricare i dati con Data factory di Azure |
| 13 |[Caricare dati con PolyBase in SQL Data Warehouse](sql-data-warehouse-get-started-load-with-polybase.md) |Informazioni su PolyBase e sul relativo uso per scenari di data warehousing. |
| 14 |[Creare un Azure SQL Data Warehouse](sql-data-warehouse-get-started-provision.md) |Informazioni su come creare un database SQL Data Warehouse nel portale di Azure |
| 15 |[Creare SQL Data Warehouse con PowerShell](sql-data-warehouse-get-started-provision-powershell.md) |Creare un'istanza di SQL Data Warehouse con PowerShell |
| 16 |[Visualizzare i dati con Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) |Visualizzare i dati di SQL Data Warehouse con Power BI |
| 17 |[Eseguire query in Azure SQL Data Warehouse (Visual Studio)](sql-data-warehouse-query-visual-studio.md) |Eseguire query in SQL Data Warehouse con Visual Studio. |

## <a name="develop"></a>Sviluppare
| &nbsp; | Titolo | Descrizione |
| ---:|:--- |:--- |
| 18 |[Ottimizzazione delle transazioni per SQL Data Warehouse](sql-data-warehouse-develop-best-practices-transactions.md) |Indicazioni sulle procedure consigliate per la scrittura di aggiornamenti di transazioni efficienti in Azure SQL Data Warehouse |
| 19 |[Gestione della concorrenza e del carico di lavoro in SQL Data Warehouse](sql-data-warehouse-develop-concurrency.md) |Informazioni sulla gestione della concorrenza e del carico di lavoro nel Data Warehouse di SQL Azure per lo sviluppo di soluzioni. |
| 20 |[Create Table As Select (CTAS) in SQL Data Warehouse](sql-data-warehouse-develop-ctas.md) |Suggerimenti per la codifica con l’istruzione create table as select (CTAS) in SQL Data Warehouse di Azure per lo sviluppo di soluzioni. |
| 21 |[SQL dinamico in SQL Data Warehouse](sql-data-warehouse-develop-dynamic-sql.md) |Suggerimenti per l’utilizzo di SQL dinamico in SQL Data Warehouse di Azure per lo sviluppo di soluzioni. |
| 22 |[Opzioni Group by in SQL Data Warehouse](sql-data-warehouse-develop-group-by-options.md) |Suggerimenti per l’implementazione delle opzioni group by in SQL Data Warehouse di Azure per lo sviluppo di soluzioni. |
| 23 |[Usare etichette per instrumentare query in SQL Data Warehouse](sql-data-warehouse-develop-label.md) |Suggerimenti per l'uso di etichette per instrumentare query in Azure SQL Data Warehouse per lo sviluppo di soluzioni. |
| 24 |[Cicli in SQL Data Warehouse](sql-data-warehouse-develop-loops.md) |Suggerimenti sui di cicli Transact-SQL e sulla sostituzione di cursori in Azure SQL Data Warehouse per lo sviluppo di soluzioni. |
| 25 |[Stored procedure in SQL Data Warehouse](sql-data-warehouse-develop-stored-procedures.md) |Suggerimenti per l'implementazione delle stored procedure in Azure SQL Data Warehouse per lo sviluppo di soluzioni. |
| 26 |[Transazioni in SQL Data Warehouse](sql-data-warehouse-develop-transactions.md) |Suggerimenti per l'implementazione di transazioni in Azure SQL Data Warehouse per lo sviluppo di soluzioni. |
| 27 |[Schemi definiti dall'utente in SQL Data Warehouse](sql-data-warehouse-develop-user-defined-schemas.md) |Suggerimenti per l'uso di schemi Transact-SQL in Azure SQL Data Warehouse per lo sviluppo di soluzioni. |
| 28 |[Assegnare variabili in SQL Data Warehouse](sql-data-warehouse-develop-variable-assignment.md) |Suggerimenti per l'assegnazione di variabili Transact-SQL in Azure SQL Data Warehouse per lo sviluppo di soluzioni. |
| 29 |[Viste in SQL Data Warehouse](sql-data-warehouse-develop-views.md) |Suggerimenti per l'uso di viste Transact-SQL in Azure SQL Data Warehouse per lo sviluppo di soluzioni. |
| 30 |[Decisioni di progettazione e tecniche di codifica per SQL Data Warehouse](sql-data-warehouse-overview-develop.md) |Concetti di sviluppo, decisioni di progettazione, suggerimenti e tecniche di codifica per SQL Data Warehouse. |

## <a name="manage"></a>Manage
| &nbsp; | Titolo | Descrizione |
| ---:|:--- |:--- |
| 31 |[Gestire la potenza di calcolo in Azure SQL Data Warehouse (Panoramica)](sql-data-warehouse-manage-compute-overview.md) |Funzionalità relative alla scalabilità orizzontale delle prestazioni in Azure SQL Data Warehouse. Eseguire il ridimensionamento modificando le impostazioni DWU o sospendendo e riavviando le risorse di calcolo per ridurre i costi. |
| 32 |[Gestire la potenza di calcolo in Azure SQL Data Warehouse (portale di Azure)](sql-data-warehouse-manage-compute-portal.md) |Attività del portale di Azure per la gestione della potenza di calcolo. Ridimensionare le risorse di calcolo cambiando il numero di DWU. In alternativa, sospendere e riprendere le risorse di calcolo per ridurre i costi. |
| 33 |[Gestire la potenza di calcolo in Azure SQL Data Warehouse (PowerShell)](sql-data-warehouse-manage-compute-powershell.md) |Attività di PowerShell per la gestione della potenza di calcolo. Ridimensionare le risorse di calcolo cambiando il numero di DWU. In alternativa, sospendere e riprendere le risorse di calcolo per ridurre i costi. |
| 34 |[Gestire la potenza di calcolo in Azure SQL Data Warehouse (REST)](sql-data-warehouse-manage-compute-rest-api.md) |Attività di PowerShell per la gestione della potenza di calcolo. Ridimensionare le risorse di calcolo cambiando il numero di DWU. In alternativa, sospendere e riprendere le risorse di calcolo per ridurre i costi. |
| 35 |[Gestire la potenza di calcolo in Azure SQL Data Warehouse (T-SQL)](sql-data-warehouse-manage-compute-tsql.md) |Attività di Transact-SQL (T-SQL) per scalare orizzontalmente le prestazioni cambiando il numero delle DWU. Risparmiare sui costi eseguendo una scalabilità orizzontale durante le ore non di punta. |
| 36 |[Monitoraggio del carico di lavoro mediante DMV](sql-data-warehouse-manage-monitor.md) |Informazioni sul monitoraggio del carico di lavoro mediante DMV. |
| 37 |[Gestire i database in Azure SQL Data Warehouse](sql-data-warehouse-overview-manage.md) |Panoramica della gestione dei database di SQL Data Warehouse. Include strumenti di gestione, prestazioni di DWU e di scalabilità orizzontale, risoluzione dei problemi di prestazioni delle query, definizione dei criteri di protezione e ripristino di un database da un danneggiamento dei dati o da un'interruzione dell'alimentazione locale. |
| 38 |[Monitorare le query utente in Azure SQL Data Warehouse](sql-data-warehouse-overview-manage-user-queries.md) |Panoramica delle considerazioni, delle procedure consigliate e delle attività per il monitoraggio delle query utente in Azure SQL Data Warehouse. |
| 39 |[Ripristino di SQL Data Warehouse](sql-data-warehouse-restore-database-overview.md) |Panoramica delle opzioni di ripristino del database per ripristinare un database in Azure SQL Data Warehouse. |
| 40 |[Ripristinare un'istanza di Azure SQL Data Warehouse (portale)](sql-data-warehouse-restore-database-portal.md) |Attività del portale di Azure per il ripristino di un'istanza di Azure SQL Data Warehouse. |
| 41 |[Ripristinare un'istanza di Azure SQL Data Warehouse (PowerShell)](sql-data-warehouse-restore-database-powershell.md) |Attività di PowerShell per il ripristino di un'istanza di Azure SQL Data Warehouse. |
| 42 |[Ripristinare un'istanza di Azure SQL Data Warehouse (API REST)](sql-data-warehouse-restore-database-rest-api.md) |Attività dell'API REST per il ripristino di un'istanza di Azure SQL Data Warehouse. |

## <a name="tables-and-indexes"></a>Tabelle e indici
| &nbsp; | Titolo | Descrizione |
| ---:|:--- |:--- |
| 43 |[Tipi di dati per le tabelle in SQL Data Warehouse](sql-data-warehouse-tables-data-types.md) |Introduzione ai tipi di dati per le tabelle di Azure SQL Data Warehouse. |
| 44 |[Distribuzione di tabelle in SQL Data Warehouse](sql-data-warehouse-tables-distribute.md) |Introduzione alla distribuzione di tabelle in SQL Data Warehouse di Azure. |
| 45 |[Indicizzazione di tabelle in SQL Data Warehouse](sql-data-warehouse-tables-index.md) |Introduzione all'indicizzazione delle tabelle in Azure SQL Data Warehouse. |
| 46 |[Panoramica delle tabelle in SQL Data Warehouse](sql-data-warehouse-tables-overview.md) |Introduzione alle tabelle di SQL Data Warehouse di Azure. |
| 47 |[Tabelle di partizionamento in SQL Data Warehouse](sql-data-warehouse-tables-partition.md) |Introduzione al partizionamento delle tabelle di SQL Data Warehouse di Azure. |
| 48 |[Gestione delle statistiche nelle tabelle in SQL Data Warehouse](sql-data-warehouse-tables-statistics.md) |Introduzione alle statistiche nelle tabelle di SQL Data Warehouse di Azure. |
| 49 |[Tabelle temporanee in SQL Data Warehouse](sql-data-warehouse-tables-temporary.md) |Introduzione alle tabelle temporanee di SQL Data Warehouse di Azure. |

## <a name="integrate"></a>Integrare
| &nbsp; | Titolo | Descrizione |
| ---:|:--- |:--- |
| 50 |[Usare Data factory di Azure con SQL Data Warehouse](sql-data-warehouse-integrate-azure-data-factory.md) |Suggerimenti per l'uso di Data factory di Azure con Azure SQL Data Warehouse per lo sviluppo di soluzioni. |
| 51 |[Usare Azure Machine Learning con SQL Data Warehouse](sql-data-warehouse-integrate-azure-machine-learning.md) |Suggerimenti per l'uso di Azure Machine Learning con Azure SQL Data Warehouse per lo sviluppo di soluzioni. |
| 52 |[Usare Analisi di flusso di Azure con SQL Data Warehouse](sql-data-warehouse-integrate-azure-stream-analytics.md) |Suggerimenti per l'uso di Analisi di flusso di Azure con Azure SQL Data Warehouse per lo sviluppo di soluzioni. |
| 53 |[Usare Power BI con SQL Data Warehouse](sql-data-warehouse-integrate-power-bi.md) |Suggerimenti per l'uso di Power BI con Azure SQL Data Warehouse per lo sviluppo di soluzioni. |
| 54 |[Utilizzare altri servizi con SQL Data Warehouse](sql-data-warehouse-overview-integrate.md) |Strumenti e partner con soluzioni che interagiscono con SQL Data Warehouse. |

## <a name="load"></a>chiudi
| &nbsp; | Titolo | Descrizione |
| ---:|:--- |:--- |
| 55 |[Caricare i dati dall'archivio BLOB di Azure in Azure SQL Data Warehouse (Azure Data Factory)](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md) |Informazioni su come caricare i dati con Data factory di Azure |
| 56 |[Caricare dati dall’archiviazione BLOB di Azure in un SQL Data Warehouse (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) |Informazioni su come utilizzare PolyBase per caricare i dati dall'archivio BLOB di Azure in SQL Data Warehouse. Caricare alcune tabelle dai dati pubblici nello schema del Data Warehouse di Contoso Retail. |
| 57 |[Caricare dati da SQL Server in Azure SQL Data Warehouse (AZCopy)](sql-data-warehouse-load-from-sql-server-with-azcopy.md) |Uso di bcp per esportare dati da SQL Server a file flat, di AZCopy per importare dati nell'archivio BLOB di Azure e di PolyBase per inserire i dati in Azure SQL Data Warehouse. |
| 58 |[Caricare dati da SQL Server in Azure SQL Data Warehouse (file flat)](sql-data-warehouse-load-from-sql-server-with-bcp.md) |Per dimensioni ridotte dei dati è possibile usare bcp per esportare i dati da SQL Server a file flat e importare i dati direttamente in Azure SQL Data Warehouse. |
| 59 |[Caricare dati da SQL Server in Azure SQL Data Warehouse (SSIS)](sql-data-warehouse-load-from-sql-server-with-integration-services.md) |Illustra come creare un pacchetto di SQL Server Integration Services (SSIS) per spostare dati da una vasta gamma di origini dati in SQL Data Warehouse. |
| 60 |[Caricare dati con PolyBase in SQL Data Warehouse](sql-data-warehouse-load-from-sql-server-with-polybase.md) |Uso di bcp per esportare dati da SQL Server a file flat, di AZCopy per importare dati nell'archivio BLOB di Azure e di PolyBase per inserire i dati in Azure SQL Data Warehouse. |
| 61 |[Guida per l'uso di PolyBase in SQL Data Warehouse](sql-data-warehouse-load-polybase-guide.md) |Linee guida e consigli per l'uso di PolyBase in scenari di SQL Data Warehouse. |
| 62 |[Caricare i dati di esempio in SQL Data Warehouse](sql-data-warehouse-load-sample-databases.md) |Caricare i dati di esempio in SQL Data Warehouse |
| 63 |[Caricare dati con bcp](sql-data-warehouse-load-with-bcp.md) |Informazioni su bcp e sul relativo uso per scenari di data warehousing. |
| 64 |[Caricare i dati in Azure SQL Data Warehouse](sql-data-warehouse-overview-load.md) |Informazioni sugli scenari comuni per il caricamento dei dati in SQL Data Warehouse. Questi includono l'uso di PolyBase, dell’archiviazione BLOB di Azure, di file flat e l’invio dei dischi. È anche possibile usare strumenti di terze parti. |

## <a name="migrate"></a>Migrazione
| &nbsp; | Titolo | Descrizione |
| ---:|:--- |:--- |
| 65 |[Eseguire la migrazione del codice SQL in SQL Data Warehouse](sql-data-warehouse-migrate-code.md) |Suggerimenti per la migrazione del codice SQL in Azure SQL Data Warehouse per lo sviluppo di soluzioni. |
| 66 |[Eseguire la migrazione dei dati](sql-data-warehouse-migrate-data.md) |Suggerimenti per la migrazione dei dati in Azure SQL Data Warehouse per lo sviluppo di soluzioni. |
| 67 |[Utilità di migrazione per data warehouse (anteprima)](sql-data-warehouse-migrate-migration-utility.md) |Eseguire la migrazione a SQL Data Warehouse. |
| 68 |[Eseguire la migrazione dello schema in SQL Data Warehouse](sql-data-warehouse-migrate-schema.md) |Suggerimenti per la migrazione dello schema in Azure SQL Data Warehouse per lo sviluppo di soluzioni. |
| 69 |[Eseguire la migrazione della soluzione in SQL Data Warehouse](sql-data-warehouse-overview-migrate.md) |Indicazioni sulla migrazione per spostare la soluzione nella piattaforma Azure SQL Data Warehouse. |

## <a name="partners"></a>Partner
| &nbsp; | Titolo | Descrizione |
| ---:|:--- |:--- |
| 70 |[Partner di business intelligence per SQL Data Warehouse](sql-data-warehouse-partner-business-intelligence.md) |Elenco di partner di business intelligence di terze parti con soluzioni che supportano SQL Data Warehouse. |
| 71 |[Partner di integrazione di dati di SQL Data Warehouse](sql-data-warehouse-partner-data-integration.md) |Elenco di partner di terze parti con soluzioni per l'integrazione dei dati che supportano Azure SQL Data Warehouse. |
| 72 |[Partner di gestione di dati di SQL Data Warehouse](sql-data-warehouse-partner-data-management.md) |Elenco di partner di gestione dati di terze parti con soluzioni che supportano SQL Data Warehouse. |

## <a name="reference"></a>riferimento
| &nbsp; | Titolo | Descrizione |
| ---:|:--- |:--- |
| 73 |[Argomenti di riferimento per SQL Data Warehouse](sql-data-warehouse-overview-reference.md) |Collegamenti a contenuto di riferimento per SQL Data Warehouse. |
| 74 |[Usare i cmdlet di PowerShell e le API REST con SQL Data Warehouse](sql-data-warehouse-reference-powershell-cmdlets.md) |I cmdlet principali di PowerShell per Azure SQL Data Warehouse, compresi quelli per sospendere e riavviare un database. |
| 75 |[Elementi del linguaggio](sql-data-warehouse-reference-tsql-language-elements.md) |Elenco di collegamenti a contenuto di riferimento per gli elementi del linguaggio Transact-SQL usati per SQL Data Warehouse. |
| 76 |[Argomenti Transact-SQL](sql-data-warehouse-reference-tsql-statements.md) |Collegamenti a contenuto di riferimento per gli argomenti Transact-SQL usati da SQL Data Warehouse. |
| 77 |[Viste di sistema](sql-data-warehouse-reference-tsql-system-views.md) |Collegamenti al contenuto relativo alle viste di sistema per SQL Data Warehouse. |

## <a name="security"></a>Sicurezza
| &nbsp; | Titolo | Descrizione |
| ---:|:--- |:--- |
| 78 |[SQL Data Warehouse - Supporto client di livello inferiore per controllo e maschera dati dinamica](sql-data-warehouse-auditing-downlevel-clients.md) |Informazioni sul supporto di client di livello inferiore di SQL Data Warehouse per il controllo dati |
| 79 |[Servizio di controllo di Azure SQL Data Warehouse](sql-data-warehouse-auditing-overview.md) |Introduzione al servizio di controllo di Azure SQL Data Warehouse |
| 80 |[Introduzione a Transparent Data Encryption (TDE) di SQL Data Warehouse](sql-data-warehouse-encryption-tde.md) |Transparent Data Encryption (TDE) in SQL Data Warehouse. |
| 81 |[Introduzione a Transparent Data Encryption (TDE)](sql-data-warehouse-encryption-tde-tsql.md) |Transparent Data Encryption (TDE) in SQL Data Warehouse (T-SQL). |
| 82 |[Proteggere un database in SQL Data Warehouse](sql-data-warehouse-overview-manage-security.md) |Suggerimenti per proteggere un database in Azure SQL Data Warehouse per lo sviluppo di soluzioni. |

## <a name="miscellaneous"></a>Miscellaneous
| &nbsp; | Titolo | Descrizione |
| ---:|:--- |:--- |
| 83 |[Installare Visual Studio 2015 e SSDT per SQL Data Warehouse](sql-data-warehouse-install-visual-studio.md) |Installare Visual Studio e SQL Server Data Tools (SSDT) per Azure SQL Data Warehouse |
| 84 |[Dettagli sulla migrazione ad Archiviazione Premium](sql-data-warehouse-migrate-to-premium-storage.md) |Istruzioni per la migrazione di un SQL Data Warehouse esistente all’archiviazione Premium |
| 85 |[Introduzione al rilevamento delle minacce](sql-data-warehouse-security-threat-detection.md) |Attività iniziali per il rilevamento delle minacce |
| 86 |[Limiti di capacità di SQL Data Warehouse](sql-data-warehouse-service-capacity-limits.md) |I valori massimi per le connessioni, i database, le tabelle e le query per SQL Data Warehouse. |
| 87 |[Risoluzione dei problemi relativi a SQL Data Warehouse di Azure](sql-data-warehouse-troubleshoot.md) |Risoluzione dei problemi relativi a SQL Data Warehouse di Azure. |




<!--HONumber=Nov16_HO3-->


