---
title: risoluzione dei problemi
description: Risoluzione dei problemi relativi a SQL Data Warehouse di Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/25/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b2a9a7b0b759f5853d83a4b1999887414fd5f430
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483208"
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Risoluzione dei problemi relativi a SQL Data Warehouse di Azure
In questo articolo sono elencate le domande frequenti relative alla risoluzione dei problemi.

## <a name="connecting"></a>Connessione
| Problema                                                        | Risoluzione                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Accesso non riuscito per l'utente 'NT AUTHORITY\ANONYMOUS LOGON'. (Microsoft SQL Server, Errore: 18456) | Questo errore si verifica quando un utente AAD tenta di connettersi al database master, ma non dispone di un utente nel database master.  Per risolvere questo problema specificare l'istanza di SQL Data Warehouse a cui si desidera connettersi al momento della connessione o aggiungere l'utente al database master.  Per ulteriori dettagli, vedere l'articolo [Panoramica della sicurezza][Security overview] . |
| L'entità server "MyUserName" non può accedere al database "master" nel contesto di sicurezza corrente. Impossibile aprire il database predefinito dell'utente. Accesso non riuscito. Accesso non riuscito per l'utente 'MyUserName'. (Microsoft SQL Server, Errore: 916) | Questo errore si verifica quando un utente AAD tenta di connettersi al database master, ma non dispone di un utente nel database master.  Per risolvere questo problema specificare l'istanza di SQL Data Warehouse a cui si desidera connettersi al momento della connessione o aggiungere l'utente al database master.  Per ulteriori dettagli, vedere l'articolo [Panoramica della sicurezza][Security overview] . |
| Errore CTAIP                                                  | Questo errore può verificarsi quando è stato creato un account di accesso nel database master di SQL Server, ma non nel database di SQL Data Warehouse.  Se si verifica questo errore, vedere l'articolo [Panoramica della sicurezza][Security overview] .  Questo articolo illustra come creare un account di accesso e un utente in un database master e come creare un utente nel database di SQL Data Warehouse. |
| Blocco da parte del firewall                                          | I database SQL di Azure sono protetti da firewall a livello di server e di database per garantire che solo gli indirizzi IP noti accedano a un database. I firewall sono protetti per impostazione predefinita, il che significa che è necessario abilitare in modo esplicito un indirizzo IP o un intervallo di indirizzi prima di potersi connettere.  To configure your firewall for access, follow the steps in [Configure server firewall access for your client IP][Configure server firewall access for your client IP] in the [Provisioning instructions][Provisioning instructions]. |
| Impossibile connettersi con lo strumento o il driver                           | SQL Data Warehouse recommends using [SSMS][SSMS], [SSDT for Visual Studio][SSDT for Visual Studio], or [sqlcmd][sqlcmd] to query your data. For more information on drivers and connecting to SQL Data Warehouse, see [Drivers for Azure SQL Data Warehouse][Drivers for Azure SQL Data Warehouse] and [Connect to Azure SQL Data Warehouse][Connect to Azure SQL Data Warehouse] articles. |

## <a name="tools"></a>Strumenti
| Problema                                                        | Risoluzione                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Esplora oggetti di Visual Studio non riconosce gli utenti di AAD           | Questo è un problema noto.  Come soluzione alternativa è possibile visualizzare gli utenti in [sys.database_principals][sys.database_principals].  Per maggiori informazioni sull'uso di Azure Active Directory con SQL Data Warehouse, vedere [Autenticazione in Azure SQL Data Warehouse][Authentication to Azure SQL Data Warehouse] . |
| Manual scripting, using the scripting wizard, or connecting via SSMS is slow, not responding, or producing errors | Assicurarsi che gli utenti siano stati creati nel database master. Nelle opzioni di scripting, assicurarsi inoltre che l'edizione del motore sia impostata come "Edizione Microsoft Azure SQL Data Warehouse" e che il tipo di motore sia "Database SQL di Microsoft Azure". |
| Errore della generazione di script in SSMS                               | Generating a script for SQL Data Warehouse fails if the option "Generate script for dependent objects" option is set to "True." Per risolvere il problema, gli utenti devono passare manualmente a Strumenti -> Opzioni -> Esplora oggetti di SQL Server -> Genera script per oggetti dipendenti e impostare l'opzione su false |

## <a name="performance"></a>Performance
| Problema                                                        | Risoluzione                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Risoluzione dei problemi di prestazioni delle query                            | Se si sta cercando di risolvere i problemi relativi a una determinata query, un ottimo punto di partenza è l'articolo su come [imparare a monitorare le query][Learning how to monitor your queries]. |
| TempDB space issues | [Monitor TempDB](sql-data-warehouse-manage-monitor.md#monitor-tempdb) space usage.  Common causes for running out of TempDB space are:<br>- Not enough resources allocated to the query causing data to spill to TempDB.  See [Workload management](resource-classes-for-workload-management.md) <br>- Statistics are missing or out of date causing excessive data movement.  See [Maintaining table statistics][Statistics] for details on how to create statistics<br>- TempDB space is allocated per service level.  [Scaling your SQL Data Warehouse][Scaling your SQL Data Warehouse] to a higher DWU setting allocates more TempDB space.|
| Piani e prestazioni delle query di scarsa qualità sono spesso causati dalla mancanza di statistiche | La causa più comune di prestazioni di scarsa qualità è la mancanza di statistiche per le tabelle.  See [Maintaining table statistics][Statistics] for details on how to create statistics and why they are critical to your performance. |
| Concorrenza bassa/query in coda                             | Comprendere la [gestione del carico di lavoro][Workload management] è importante per capire come bilanciare l'allocazione di memoria con la concorrenza. |
| Come implementare le procedure consigliate                              | L'articolo [Procedure consigliate per SQL Data Warehouse][SQL Data Warehouse best practices] è un ottimo punto di partenza per informazioni su come migliorare le prestazioni delle query. |
| Come migliorare le prestazioni con la scalabilità                      | La soluzione per aumentare le prestazioni consiste a volte nell'aggiungere semplicemente maggiore potenza di calcolo alle query [ridimensionando SQL Data Warehouse][Scaling your SQL Data Warehouse]. |
| Scarse prestazioni delle query a causa di scarsa qualità degli indici     | Some times queries can slow down because of [Poor columnstore index quality][Poor columnstore index quality].  Vedere questo articolo per ulteriori informazioni e per capire come [ricompilare gli indici per migliorare la qualità dei segmenti][Rebuild indexes to improve segment quality]. |

## <a name="system-management"></a>Gestione del sistema
| Problema                                                        | Risoluzione                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Messaggio 40847: Non è stato possibile eseguire l'operazione perché il server avrebbe superato la quota di DTU consentita di 45000. | Either reduce the [DWU][DWU] of the database you are trying to create or [request a quota increase][request a quota increase]. |
| Analisi dell'uso dello spazio                              | Per comprendere l'uso dello spazio nel sistema, vedere la [tabella sulle dimensioni][Table sizes] . |
| Aiuto nella gestione delle tabelle                                    | See the [Table overview][Overview] article for help with managing your tables.  This article also includes links into more detailed topics like [Table data types][Data types], [Distributing a table][Distribute], [Indexing a table][Index],  [Partitioning a table][Partition], [Maintaining table statistics][Statistics] and [Temporary tables][Temporary]. |
| Transparent data encryption (TDE) progress bar is not updating in the Azure portal | È possibile visualizzare lo stato di TDE tramite [powershell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption). |


## <a name="differences-from-sql-database"></a>Differenze rispetto al database SQL
| Problema                                 | Risoluzione                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Funzionalità non supportate del database SQL     | Vedere [Funzionalità non supportate delle tabelle][Unsupported table features]. |
| Tipi di dati non supportati del database SQL   | Vedere [Tipi di dati non supportati][Unsupported data types].        |
| Limitazioni DELETE e UPDATE         | See [UPDATE workarounds][UPDATE workarounds], [DELETE workarounds][DELETE workarounds] and [Using CTAS to work around unsupported UPDATE and DELETE syntax][Using CTAS to work around unsupported UPDATE and DELETE syntax]. |
| Istruzione MERGE non supportata      | Vedere [Soluzioni alternative MERGE][MERGE workarounds].                  |
| Limitazioni delle stored procedure          | Per capire alcune limitazioni delle stored procedure, vedere [Limitazioni delle stored procedure][Stored procedure limitations] . |
| Le UDF non supportano istruzioni SELECT | Si tratta di una limitazione corrente delle UDF.  Per conoscere la sintassi supportata, vedere [CREATE FUNCTION][CREATE FUNCTION] . |

## <a name="next-steps"></a>Passaggi successivi
Se non si riesce a trovare una soluzione al problema, ecco alcune altre risorse che è possibile provare.

* [Blog]
* [Richieste di funzionalità]
* [Video]
* [Blog del team CAT]
* [Creare un ticket di supporto]
* [Forum MSDN]
* [Forum su Stack Overflow]
* [Twitter]

<!--Image references-->

<!--Article references-->
[Security overview]: sql-data-warehouse-overview-manage-security.md
[SSMS]: /sql/ssms/download-sql-server-management-studio-ssms
[SSDT for Visual Studio]: sql-data-warehouse-install-visual-studio.md
[Drivers for Azure SQL Data Warehouse]: sql-data-warehouse-connection-strings.md
[Connect to Azure SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Creare un ticket di supporto]: sql-data-warehouse-get-started-create-support-ticket.md
[Scaling your SQL Data Warehouse]: sql-data-warehouse-manage-compute-overview.md
[DWU]: sql-data-warehouse-overview-what-is.md
[request a quota increase]: sql-data-warehouse-get-started-create-support-ticket.md
[Learning how to monitor your queries]: sql-data-warehouse-manage-monitor.md
[Provisioning instructions]: sql-data-warehouse-get-started-provision.md
[Configure server firewall access for your client IP]: sql-data-warehouse-get-started-provision.md
[SQL Data Warehouse best practices]: sql-data-warehouse-best-practices.md
[Table sizes]: sql-data-warehouse-tables-overview.md#table-size-queries
[Unsupported table features]: sql-data-warehouse-tables-overview.md#unsupported-table-features
[Unsupported data types]: sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: sql-data-warehouse-tables-overview.md
[Data types]: sql-data-warehouse-tables-data-types.md
[Distribute]: sql-data-warehouse-tables-distribute.md
[Index]: sql-data-warehouse-tables-index.md
[Partition]: sql-data-warehouse-tables-partition.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[Temporary]: sql-data-warehouse-tables-temporary.md
[Poor columnstore index quality]: sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuild indexes to improve segment quality]: sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Workload management]: resource-classes-for-workload-management.md
[Using CTAS to work around unsupported UPDATE and DELETE syntax]: sql-data-warehouse-develop-ctas.md
[UPDATE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[DELETE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[MERGE workarounds]: sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Stored procedure limitations]: sql-data-warehouse-develop-stored-procedures.md#limitations
[Authentication to Azure SQL Data Warehouse]: sql-data-warehouse-authentication.md


<!--MSDN references-->
[sys.database_principals]: /sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql
[CREATE FUNCTION]: /sql/t-sql/statements/create-function-sql-data-warehouse
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Blog]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blog del team CAT]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Richieste di funzionalità]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum MSDN]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Forum su Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Video]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[Databricks]: https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse#load-data-into-azure-sql-data-warehouse
