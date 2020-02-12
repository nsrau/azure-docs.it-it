---
title: Risoluzione dei problemi
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
ms.openlocfilehash: 4eec340a04b9cdbc85a2c8712a11d31132766206
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153367"
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Risoluzione dei problemi relativi a SQL Data Warehouse di Azure
In questo articolo sono elencate le domande frequenti relative alla risoluzione dei problemi.

## <a name="connecting"></a>Connessione in corso
| Problema                                                        | Risoluzione                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Accesso non riuscito per l'utente 'NT AUTHORITY\ANONYMOUS LOGON'. (Microsoft SQL Server, Errore: 18456) | Questo errore si verifica quando un utente AAD tenta di connettersi al database master, ma non dispone di un utente nel database master.  Per risolvere questo problema specificare l'istanza di SQL Data Warehouse a cui si desidera connettersi al momento della connessione o aggiungere l'utente al database master.  Per ulteriori dettagli, vedere l'articolo [Panoramica della sicurezza](sql-data-warehouse-overview-manage-security.md) . |
| L'entità server "MyUserName" non può accedere al database "master" nel contesto di sicurezza corrente. Impossibile aprire il database utente predefinito. Accesso non riuscito. Accesso non riuscito per l'utente 'MyUserName'. (Microsoft SQL Server, Errore: 916) | Questo errore si verifica quando un utente AAD tenta di connettersi al database master, ma non dispone di un utente nel database master.  Per risolvere questo problema specificare l'istanza di SQL Data Warehouse a cui si desidera connettersi al momento della connessione o aggiungere l'utente al database master.  Per ulteriori dettagli, vedere l'articolo [Panoramica della sicurezza](sql-data-warehouse-overview-manage-security.md) . |
| Errore CTAIP                                                  | Questo errore può verificarsi quando è stato creato un account di accesso nel database master di SQL Server, ma non nel database di SQL Data Warehouse.  Se si verifica questo errore, vedere l'articolo [Panoramica della sicurezza](sql-data-warehouse-overview-manage-security.md) .  Questo articolo illustra come creare un account di accesso e un utente in un database master e come creare un utente nel database di SQL Data Warehouse. |
| Blocco da parte del firewall                                          | I database SQL di Azure sono protetti da firewall a livello di server e di database per garantire che solo gli indirizzi IP noti accedano a un database. I firewall sono protetti per impostazione predefinita, il che significa che è necessario abilitare in modo esplicito un indirizzo IP o un intervallo di indirizzi prima di potersi connettere.  Per configurare il firewall per l'accesso, seguire la procedura descritta in [configurare l'accesso al firewall del server per l'indirizzo IP client](sql-data-warehouse-get-started-provision.md) nelle [istruzioni di provisioning](sql-data-warehouse-get-started-provision.md). |
| Impossibile connettersi con lo strumento o il driver                           | SQL Data Warehouse consiglia di usare [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15), [SSDT per Visual Studio](sql-data-warehouse-install-visual-studio.md)o [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md) per eseguire query sui dati. Per ulteriori informazioni sui driver e sulla connessione a SQL Data Warehouse, vedere [driver per Azure SQL data warehouse](sql-data-warehouse-connection-strings.md) e [connettersi agli articoli di Azure SQL data warehouse](sql-data-warehouse-connect-overview.md) . |

## <a name="tools"></a>Strumenti
| Problema                                                        | Risoluzione                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Esplora oggetti di Visual Studio non riconosce gli utenti di AAD           | Questo è un problema noto  Come soluzione alternativa è possibile visualizzare gli utenti in [sys.database_principals](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?view=sql-server-ver15).  Per maggiori informazioni sull'uso di Azure Active Directory con SQL Data Warehouse, vedere [Autenticazione in Azure SQL Data Warehouse](sql-data-warehouse-authentication.md) . |
| La creazione manuale di script, l'utilizzo di script o la connessione tramite SSMS è lenta, non risponde o produce errori | Assicurarsi che gli utenti siano stati creati nel database master. Nelle opzioni di scripting, assicurarsi inoltre che l'edizione del motore sia impostata come "Edizione Microsoft Azure SQL Data Warehouse" e che il tipo di motore sia "Database SQL di Microsoft Azure". |
| Errore della generazione di script in SSMS                               | La generazione di uno script per SQL Data Warehouse ha esito negativo se l'opzione "genera script per oggetti dipendenti" è impostata su "true". Come soluzione alternativa, gli utenti devono passare manualmente a **strumenti-> Opzioni-> Esplora oggetti di SQL Server-> generare uno script per le opzioni dipendenti e impostare su false** |

## <a name="performance"></a>Prestazioni
| Problema                                                        | Risoluzione                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Risoluzione dei problemi di prestazioni delle query                            | Se si sta cercando di risolvere i problemi relativi a una determinata query, un ottimo punto di partenza è l'articolo su come [imparare a monitorare le query](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md#monitor-query-execution). |
| Problemi di spazio TempDB | [Monitorare](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md#monitor-tempdb) l'utilizzo dello spazio tempdb.  Le cause più comuni dell'esaurimento dello spazio di TempDB sono:<br>-Risorse insufficienti allocate alla query che causano la fuoriuscita dei dati in TempDB.  Vedere [gestione del carico di lavoro](resource-classes-for-workload-management.md) <br>-Le statistiche sono mancanti o non aggiornate causando un spostamento eccessivo dei dati.  Per informazioni dettagliate su come creare statistiche, vedere [gestione delle statistiche di tabella](sql-data-warehouse-tables-statistics.md)<br>-Lo spazio TempDB viene allocato per ogni livello di servizio.  [Il ridimensionamento del SQL data warehouse](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#scaling-compute) a un'impostazione DWU superiore consente di allocare più spazio tempdb.|
| Piani e prestazioni delle query di scarsa qualità sono spesso causati dalla mancanza di statistiche | La causa più comune di prestazioni di scarsa qualità è la mancanza di statistiche per le tabelle.  Per informazioni dettagliate su come creare statistiche e sul motivo per cui le prestazioni sono fondamentali, vedere [gestione delle statistiche delle tabelle](sql-data-warehouse-tables-statistics.md) . |
| Concorrenza bassa/query in coda                             | Comprendere la [gestione del carico di lavoro](resource-classes-for-workload-management.md) è importante per capire come bilanciare l'allocazione di memoria con la concorrenza. |
| Come implementare le procedure consigliate                              | L'articolo [Procedure consigliate per SQL Data Warehouse](sql-data-warehouse-best-practices.md) è un ottimo punto di partenza per informazioni su come migliorare le prestazioni delle query. |
| Come migliorare le prestazioni con la scalabilità                      | La soluzione per aumentare le prestazioni consiste a volte nell'aggiungere semplicemente maggiore potenza di calcolo alle query [ridimensionando SQL Data Warehouse](sql-data-warehouse-manage-compute-overview.md). |
| Scarse prestazioni delle query a causa di scarsa qualità degli indici     | Alcune volte le query possono rallentare a causa della [scarsa qualità degli indici columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality).  Vedere questo articolo per ulteriori informazioni e per capire come [ricompilare gli indici per migliorare la qualità dei segmenti](../sql-data-warehouse/sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality). |

## <a name="system-management"></a>Gestione del sistema
| Problema                                                        | Risoluzione                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Messaggio 40847: Non è stato possibile eseguire l'operazione perché il server avrebbe superato la quota di DTU consentita di 45000. | Ridurre il valore di [DWU](what-is-a-data-warehouse-unit-dwu-cdwu.md) del database che si sta tentando di creare oppure [richiedere un aumento della quota](sql-data-warehouse-get-started-create-support-ticket.md). |
| Analisi dell'uso dello spazio                              | Per comprendere l'uso dello spazio nel sistema, vedere la [tabella sulle dimensioni]( ../sql-data-warehouse/sql-data-warehouse-tables-overview.md#table-size-queries) . |
| Aiuto nella gestione delle tabelle                                    | Per informazioni sulla gestione delle tabelle, vedere l'articolo [Panoramica della tabella](../sql-data-warehouse/sql-data-warehouse-tables-overview.md) .  Questo articolo include inoltre collegamenti ad argomenti più dettagliati, ad esempio [tipi di dati di tabella](sql-data-warehouse-tables-data-types.md), [distribuzione di una](sql-data-warehouse-tables-distribute.md)tabella, [indicizzazione di una tabella](sql-data-warehouse-tables-index.md), [partizionamento di una tabella](sql-data-warehouse-tables-partition.md), [gestione delle statistiche](sql-data-warehouse-tables-statistics.md) delle tabelle e [tabelle temporanee](sql-data-warehouse-tables-temporary.md). |
| L'indicatore di stato Transparent Data Encryption (Transparent Data Encryption) non viene aggiornato nella portale di Azure | È possibile visualizzare lo stato di TDE tramite [powershell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption). |


## <a name="differences-from-sql-database"></a>Differenze rispetto al database SQL
| Problema                                 | Risoluzione                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Funzionalità non supportate del database SQL     | Vedere [Funzionalità non supportate delle tabelle](../sql-data-warehouse/sql-data-warehouse-tables-overview.md#unsupported-table-features). |
| Tipi di dati non supportati del database SQL   | Vedere [Tipi di dati non supportati](../sql-data-warehouse/sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types).        |
| Limitazioni DELETE e UPDATE         | Vedere le [soluzioni alternative](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements)per gli aggiornamenti, [eliminare le soluzioni alternative](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements) e [usare CTAs per aggirare la sintassi di aggiornamento e eliminazione non supportata](sql-data-warehouse-develop-ctas.md). |
| Istruzione MERGE non supportata      | Vedere [Soluzioni alternative MERGE](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md#replace-merge-statements).                  |
| Limitazioni delle stored procedure          | Per capire alcune limitazioni delle stored procedure, vedere [Limitazioni delle stored procedure](../sql-data-warehouse/sql-data-warehouse-develop-stored-procedures.md#limitations) . |
| Le UDF non supportano istruzioni SELECT | Si tratta di una limitazione corrente delle UDF.  Per conoscere la sintassi supportata, vedere [CREATE FUNCTION](https://docs.microsoft.com/sql/t-sql/statements/create-function-sql-data-warehouse?view=aps-pdw-2016-au7) . |

## <a name="next-steps"></a>Passaggi successivi
Se non si riesce a trovare una soluzione al problema, ecco alcune altre risorse che è possibile provare.

* [Blog](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Richieste di funzionalità](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Video](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Creare un ticket di supporto](sql-data-warehouse-get-started-create-support-ticket.md)
* [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse)
* [Forum su Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)
