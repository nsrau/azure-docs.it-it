---
title: Risoluzione dei problemi relativi a Synapse SQL
description: Risoluzione dei problemi relativi a Synapse SQL in Azure Synapse Analytics.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 6f697cf205af9bdfaadfe20e123bcf0b4935c90f
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83829988"
---
# <a name="troubleshooting-synapse-sql-in-azure-synapse-analytics"></a>Risoluzione dei problemi relativi a Synapse SQL in Azure Synapse Analytics

Questo articolo illustra la risoluzione dei problemi comuni in Synapse SQL.

## <a name="connecting"></a>Connecting

| Problema                                                        | Risoluzione                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Accesso non riuscito per l'utente 'NT AUTHORITY\ANONYMOUS LOGON'. (Microsoft SQL Server, Errore: 18456) | Questo errore si verifica quando un utente di Azure AD prova a connettersi al database master, ma non ha un utente in tale database.  Per risolvere questo problema specificare il pool SQL a cui connettersi al momento della connessione o aggiungere l'utente al database master.  Per ulteriori dettagli, vedere l'articolo [Panoramica della sicurezza](sql-data-warehouse-overview-manage-security.md) . |
| L'entità server "MyUserName" non può accedere al database "master" nel contesto di sicurezza corrente. Impossibile aprire il database utente predefinito. Accesso non riuscito. Accesso non riuscito per l'utente 'MyUserName'. (Microsoft SQL Server, Errore: 916) | Questo errore si verifica quando un utente di Azure AD prova a connettersi al database master, ma non ha un utente in tale database.  Per risolvere questo problema specificare il pool SQL a cui connettersi al momento della connessione o aggiungere l'utente al database master.  Per ulteriori dettagli, vedere l'articolo [Panoramica della sicurezza](sql-data-warehouse-overview-manage-security.md) . |
| Errore CTAIP                                                  | Questo errore può verificarsi quando è stato creato un account di accesso nel database master di SQL Server, ma non nel database SQL.  Se si verifica questo errore, vedere l'articolo [Panoramica della sicurezza](sql-data-warehouse-overview-manage-security.md) .  Questo articolo illustra come creare un account di accesso e un utente in un database master e come creare un utente nel database SQL. |
| Blocco da parte del firewall                                          | I pool SQL sono protetti da firewall per garantire che solo gli indirizzi IP noti abbiano accesso a un database. I firewall sono protetti per impostazione predefinita, il che significa che è necessario abilitare in modo esplicito un indirizzo IP o un intervallo di indirizzi prima di potersi connettere.  Per configurare il firewall per l'accesso, seguire la procedura descritta in [Configurare l'accesso al firewall del server per l'indirizzo IP del client](create-data-warehouse-portal.md) nella [Procedura di configurazione del provisioning](create-data-warehouse-portal.md). |
| Impossibile connettersi con lo strumento o il driver                           | Per eseguire query sui dati nel pool SQL Synapse è consigliabile usare [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [SSDT per Visual Studio](sql-data-warehouse-install-visual-studio.md) o [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md). Per altre informazioni sui driver e sulla connessione ad Azure Synapse, vedere gli articoli [Driver per Azure Synapse](sql-data-warehouse-connection-strings.md) e [connettersi ad Azure Synapse](sql-data-warehouse-connect-overview.md). |

## <a name="tools"></a>Strumenti

| Problema                                                        | Risoluzione                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| In Esplora oggetti di Visual Studio mancano utenti di Azure AD           | Questo è un problema noto  Come soluzione alternativa è possibile visualizzare gli utenti in [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Per altre informazioni sull'uso di Azure Active Directory con il pool SQL Synapse, vedere [Autenticazione in Azure Synapse](sql-data-warehouse-authentication.md). |
| L'esecuzione manuale di script, l'uso della creazione guidata script o la connessione tramite SSMS sono lenti, si bloccano o generano errori | Assicurarsi che gli utenti siano stati creati nel database master. Nelle opzioni di scripting, assicurarsi inoltre che l'edizione del motore impostata sia "Edizione Microsoft Azure SQL Data Warehouse" e che il tipo di motore sia "Database SQL di Microsoft Azure". |
| Errore della generazione di script in SSMS                               | La generazione di uno script per il pool SQL Synapse ha esito negativo se l'opzione "Genera script per oggetti dipendenti" è impostata su "True". Per risolvere il problema, gli utenti devono passare manualmente a **Strumenti -> Opzioni -> Esplora oggetti di SQL Server -> Genera script per oggetti dipendenti e impostare l'opzione su false** |

## <a name="performance"></a>Prestazioni

| Problema                                                        | Risoluzione                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Risoluzione dei problemi di prestazioni delle query                            | Se si sta cercando di risolvere i problemi relativi a una determinata query, un ottimo punto di partenza è l'articolo su come [imparare a monitorare le query](sql-data-warehouse-manage-monitor.md#monitor-query-execution). |
| Problemi di spazio di TempDB | [Monitorare l'utilizzo dello spazio](sql-data-warehouse-manage-monitor.md#monitor-tempdb) di TempDB.  Le cause più comuni dell'esaurimento dello spazio di TempDB sono:<br>- Risorse allocate alla query insufficienti, che causano l'espansione dei dati in TempDB.  Vedere [Gestione dei carichi di lavoro](resource-classes-for-workload-management.md) <br>- Statistiche mancanti o non aggiornate, che causano uno spostamento eccessivo dei dati.  Per informazioni dettagliate su come creare statistiche, vedere [Gestione delle statistiche nelle tabelle](sql-data-warehouse-tables-statistics.md)<br>- Spazio TempDB allocato per livello di servizio.  [Il ridimensionamento del pool SQL](sql-data-warehouse-manage-compute-overview.md#scaling-compute) a un'impostazione DWU superiore consente di allocare più spazio a TempDB.|
| Piani e prestazioni delle query di scarsa qualità sono spesso causati dalla mancanza di statistiche | La causa più comune di prestazioni di scarsa qualità è la mancanza di statistiche per le tabelle.  Per conoscere i dettagli su come creare statistiche e sui motivi per cui sono fondamentali per le prestazioni, vedere [Gestione delle statistiche nelle tabelle](sql-data-warehouse-tables-statistics.md). |
| Concorrenza bassa/query in coda                             | Comprendere la [gestione del carico di lavoro](resource-classes-for-workload-management.md) è importante per capire come bilanciare l'allocazione di memoria con la concorrenza. |
| Come implementare le procedure consigliate                              | L'articolo [Synapse SQL pool best practices](sql-data-warehouse-best-practices.md) (Procedure consigliate per il pool SQL Synapse) è un ottimo punto di partenza per informazioni su come migliorare le prestazioni delle query. |
| Come migliorare le prestazioni con la scalabilità                      | La soluzione per aumentare le prestazioni consiste a volte nell'aggiungere semplicemente maggiore potenza di calcolo alle query [ridimensionando il pool SQL](sql-data-warehouse-manage-compute-overview.md). |
| Scarse prestazioni delle query a causa di scarsa qualità degli indici     | A volte le query possono rallentare a causa della [scarsa qualità degli indici columnstore](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality).  Vedere questo articolo per ulteriori informazioni e per capire come [ricompilare gli indici per migliorare la qualità dei segmenti](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality). |

## <a name="system-management"></a>Gestione del sistema

| Problema                                                        | Risoluzione                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Messaggio 40847: Non è stato possibile eseguire l'operazione perché il server avrebbe superato la quota di DTU consentita di 45000. | Ridurre il [DWU](what-is-a-data-warehouse-unit-dwu-cdwu.md) del database che si sta tentando di creare oppure [richiedere un aumento della quota](sql-data-warehouse-get-started-create-support-ticket.md). |
| Analisi dell'uso dello spazio                              | Per comprendere l'uso dello spazio nel sistema, vedere la [tabella sulle dimensioni](sql-data-warehouse-tables-overview.md#table-size-queries) . |
| Aiuto nella gestione delle tabelle                                    | Per informazioni su come gestire le tabelle, vedere la [Panoramica sulle tabelle](sql-data-warehouse-tables-overview.md).  Questo articolo contiene anche collegamenti ad articoli più dettagliati, ad esempio relativi a [tipi di dati delle tabelle](sql-data-warehouse-tables-data-types.md), [distribuzione di una tabella](sql-data-warehouse-tables-distribute.md), [indicizzazione di una tabella](sql-data-warehouse-tables-index.md), [partizionamento di una tabella](sql-data-warehouse-tables-partition.md), [gestione delle statistiche delle tabelle](sql-data-warehouse-tables-statistics.md) e [tabelle temporanee](sql-data-warehouse-tables-temporary.md). |
| L'indicatore di stato TDE (Transparent Data Encryption) non viene aggiornato nel portale di Azure | È possibile visualizzare lo stato di TDE tramite [powershell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). |

## <a name="differences-from-sql-database"></a>Differenze rispetto al database SQL

| Problema                                 | Risoluzione                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Funzionalità non supportate del database SQL     | Vedere [Funzionalità non supportate delle tabelle](sql-data-warehouse-tables-overview.md#unsupported-table-features). |
| Tipi di dati non supportati del database SQL   | Vedere [Tipi di dati non supportati](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types).        |
| Limitazioni DELETE e UPDATE         | Vedere [Soluzioni alternative UPDATE](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements), [Soluzioni alternative DELETE](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements) e [Uso di CTAS per ovviare a sintassi UPDATE e DELETE non supportate](sql-data-warehouse-develop-ctas.md). |
| Istruzione MERGE non supportata      | Vedere [Soluzioni alternative MERGE](sql-data-warehouse-develop-ctas.md#replace-merge-statements).                  |
| Limitazioni delle stored procedure          | Per capire alcune limitazioni delle stored procedure, vedere [Limitazioni delle stored procedure](sql-data-warehouse-develop-stored-procedures.md#limitations) . |
| Le UDF non supportano istruzioni SELECT | Si tratta di una limitazione corrente delle UDF.  Per conoscere la sintassi supportata, vedere [CREATE FUNCTION](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) . |

## <a name="next-steps"></a>Passaggi successivi

Se non si riesce a trovare una soluzione al problema, ecco alcune altre risorse che è possibile provare.

* [Blog](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Richieste di funzionalità](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Video](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Creare un ticket di supporto](sql-data-warehouse-get-started-create-support-ticket.md)
* [Pagina delle domande di Domande e risposte Microsoft](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)
* [Forum su Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)
