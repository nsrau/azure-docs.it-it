---
title: Risoluzione dei problemi
description: Risoluzione dei problemi di Azure Synapse Analytics.Troubleshooting Azure Synapse Analytics.
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
ms.openlocfilehash: b24706943cdf59fba89a8007c4914b628b9e34d5
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632972"
---
# <a name="troubleshooting-sql-analytics-in-azure-synapse"></a>Risoluzione dei problemi di ANALISI SQL in Azure SynapseTroubleshooting SQL Analytics in Azure Synapse

In questo articolo sono elencate le domande frequenti relative alla risoluzione dei problemi.

## <a name="connecting"></a>Connecting

| Problema                                                        | Risoluzione                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Accesso non riuscito per l'utente 'NT AUTHORITY\ANONYMOUS LOGON'. (Microsoft SQL Server, Errore: 18456) | Questo errore si verifica quando un utente di Azure AD tenta di connettersi al database master, ma non dispone di un utente nel database master.  Per risolvere questo problema, specificare il pool SQL a cui si desidera connettersi al momento della connessione o aggiungere l'utente al database master.  Per ulteriori informazioni, vedere [l'articolo Panoramica della sicurezza.](sql-data-warehouse-overview-manage-security.md) |
| L'entità server "MyUserName" non può accedere al database "master" nel contesto di sicurezza corrente. Impossibile aprire il database utente predefinito. Accesso non riuscito. Accesso non riuscito per l'utente 'MyUserName'. (Microsoft SQL Server, Errore: 916) | Questo errore si verifica quando un utente di Azure AD tenta di connettersi al database master, ma non dispone di un utente nel database master.  Per risolvere questo problema, specificare il pool SQL a cui si desidera connettersi al momento della connessione o aggiungere l'utente al database master.  Per ulteriori informazioni, vedere [l'articolo Panoramica della sicurezza.](sql-data-warehouse-overview-manage-security.md) |
| Errore CTAIP                                                  | Questo errore può verificarsi quando è stato creato un account di accesso nel database master del server SQL, ma non nel database SQL.  Se si verifica questo errore, vedere l'articolo [Panoramica della sicurezza](sql-data-warehouse-overview-manage-security.md) .  In questo articolo viene illustrato come creare un account di accesso e un utente nel database master e quindi come creare un utente nel database SQL. |
| Blocco da parte del firewall                                          | I pool SQL sono protetti da firewall per garantire che solo gli indirizzi IP noti abbiano accesso a un database. I firewall sono protetti per impostazione predefinita, il che significa che è necessario abilitare in modo esplicito un indirizzo IP o un intervallo di indirizzi prima di potersi connettere.  Per configurare il firewall per l'accesso, seguire la procedura descritta in [Configurare l'accesso al firewall del server per l'indirizzo IP del client](create-data-warehouse-portal.md) nella [Procedura di configurazione del provisioning](create-data-warehouse-portal.md). |
| Impossibile connettersi con lo strumento o il driver                           | Il pool SQL Synapse consiglia di utilizzare [SSMS,](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15) [SSDT per Visual Studio](sql-data-warehouse-install-visual-studio.md)o [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) per eseguire query sui dati. Per altre informazioni sui driver e sulla connessione ad Azure Synapse, vedere gli articoli relativi ai driver per Azure Synapse e [Connettersi ad Azure Synapse.For](sql-data-warehouse-connect-overview.md) more information on drivers and connecting to [Azure Synapse, see Drivers for Azure Synapse](sql-data-warehouse-connection-strings.md) and Connect to Azure Synapse articles. |

## <a name="tools"></a>Strumenti

| Problema                                                        | Risoluzione                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Esplora oggetti di Visual Studio mancano gli utenti di Azure AD           | Questo è un problema noto  Come soluzione alternativa è possibile visualizzare gli utenti in [sys.database_principals](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?view=sql-server-ver15).  Per altre informazioni sull'uso di Azure Active Directory con il pool SQL Synapse, vedere [Autenticazione in Azure Synapse.See Authentication to Azure Synapse](sql-data-warehouse-authentication.md) to learn more about using Azure Active Directory with Synapse SQL pool. |
| Lo scripting manuale, l'utilizzo della procedura guidata di scripting o la connessione tramite SSMS è lento, la mancato rispetto o la produzione di errori | Assicurarsi che gli utenti siano stati creati nel database master. Nelle opzioni di scripting, assicurarsi inoltre che l'edizione del motore sia impostata come "Microsoft Azure SQL Data Warehouse Edition" e il tipo di motore sia "Database SQL di Microsoft Azure". |
| Errore della generazione di script in SSMS                               | La generazione di uno script per il pool SQL Synapse ha esito negativo se l'opzione "Genera script per oggetti dipendenti" è impostata su "True". Per risolvere il problema, gli utenti devono passare manualmente a **Strumenti -> Opzioni ->Esplora oggetti di SQL Server -> Genera script per** le opzioni dipendenti e impostato su false |

## <a name="performance"></a>Prestazioni

| Problema                                                        | Risoluzione                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Risoluzione dei problemi di prestazioni delle query                            | Se si sta cercando di risolvere i problemi relativi a una determinata query, vedere l'articolo su come [imparare a monitorare le query](sql-data-warehouse-manage-monitor.md#monitor-query-execution). |
| Problemi di spazio TempDB | Monitorare l'utilizzo dello spazio [TempDB.](sql-data-warehouse-manage-monitor.md#monitor-tempdb)  Le cause più comuni per l'esaurimento dello spazio TempDB sono:<br>- Risorse non sufficienti allocate alla query che causano la fuoriuscita di dati in TempDB.  Vedere [Gestione del carico di lavoroSee Workload management](resource-classes-for-workload-management.md) <br>- Le statistiche sono mancanti o non aggiornate causando un eccessivo spostamento dei dati.  Vedere [Gestione delle statistiche delle tabelle](sql-data-warehouse-tables-statistics.md) per informazioni dettagliate su come creare statistiche<br>- Lo spazio TempDB viene allocato per ogni livello di servizio.  [La scalabilità del pool SQL](sql-data-warehouse-manage-compute-overview.md#scaling-compute) a un'impostazione DWU superiore alloca più spazio TempDB.|
| Piani e prestazioni delle query di scarsa qualità sono spesso causati dalla mancanza di statistiche | La causa più comune di prestazioni di scarsa qualità è la mancanza di statistiche per le tabelle.  Per conoscere i dettagli su come creare statistiche e sui motivi per cui sono fondamentali per le prestazioni, vedere [Conservazione delle statistiche delle tabelle](sql-data-warehouse-tables-statistics.md). |
| Concorrenza bassa/query in coda                             | Comprendere la [gestione del carico di lavoro](resource-classes-for-workload-management.md) è importante per capire come bilanciare l'allocazione di memoria con la concorrenza. |
| Come implementare le procedure consigliate                              | Il punto migliore per iniziare a imparare modi per migliorare le prestazioni delle query è l'articolo delle procedure consigliate per [il pool SQL Synapse.](sql-data-warehouse-best-practices.md) |
| Come migliorare le prestazioni con la scalabilità                      | A volte la soluzione per migliorare le prestazioni consiste nell'aggiungere semplicemente più potenza di calcolo alle query [scalando il pool SQL](sql-data-warehouse-manage-compute-overview.md). |
| Scarse prestazioni delle query a causa di scarsa qualità degli indici     | A volte le query possono rallentare a causa della [scarsa qualità degli indici columnstore](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality).  Per altre informazioni e per capire come [ricompilare gli indici per migliorare la qualità dei segmenti](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality), vedere questo articolo. |

## <a name="system-management"></a>Gestione del sistema

| Problema                                                        | Risoluzione                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Messaggio 40847: Non è stato possibile eseguire l'operazione perché il server avrebbe superato la quota di DTU consentita di 45000. | Ridurre il [DWU](what-is-a-data-warehouse-unit-dwu-cdwu.md) del database che si sta tentando di creare oppure [richiedere un aumento della quota](sql-data-warehouse-get-started-create-support-ticket.md). |
| Analisi dell'uso dello spazio                              | Per comprendere l'uso dello spazio nel sistema, vedere [Dimensioni delle tabelle](sql-data-warehouse-tables-overview.md#table-size-queries) . |
| Aiuto nella gestione delle tabelle                                    | Per informazioni su come gestire le tabelle, vedere [Panoramica delle tabelle](sql-data-warehouse-tables-overview.md).  Questo articolo contiene anche collegamenti ad articoli più dettagliati, ad esempio relativi a [tipi di dati delle tabelle](sql-data-warehouse-tables-data-types.md), [distribuzione di una tabella](sql-data-warehouse-tables-distribute.md), [indicizzazione di una tabella](sql-data-warehouse-tables-index.md),  [partizionamento di una tabella](sql-data-warehouse-tables-partition.md), [gestione delle statistiche delle tabelle](sql-data-warehouse-tables-statistics.md) e [tabelle temporanee](sql-data-warehouse-tables-temporary.md). |
| L'indicatore di stato tDE (Transparent Data Encryption) non viene aggiornato nel portale di AzureTransparent data encryption (TDE) progress bar is not updating in the Azure portal | È possibile visualizzare lo stato di TDE tramite [powershell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption). |

## <a name="differences-from-sql-database"></a>Differenze rispetto al database SQL

| Problema                                 | Risoluzione                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Funzionalità non supportate del database SQL     | Vedere [Funzionalità non supportate delle tabelle](sql-data-warehouse-tables-overview.md#unsupported-table-features). |
| Tipi di dati non supportati del database SQL   | Vedere [Tipi di dati non supportati](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types).        |
| Limitazioni DELETE e UPDATE         | Vedere [Soluzioni alternative UPDATE](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements), [Soluzioni alternative DELETE](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements) e [Uso di CTAS per ovviare a sintassi UPDATE e DELETE non supportate](sql-data-warehouse-develop-ctas.md). |
| Istruzione MERGE non supportata      | Vedere [Soluzioni alternative MERGE](sql-data-warehouse-develop-ctas.md#replace-merge-statements).                  |
| Limitazioni delle stored procedure          | Per capire alcune limitazioni delle stored procedure, vedere [Limitazioni delle stored procedure](sql-data-warehouse-develop-stored-procedures.md#limitations). |
| Le UDF non supportano istruzioni SELECT | Si tratta di una limitazione corrente delle UDF.  Per conoscere la sintassi supportata, vedere [CREATE FUNCTION](https://docs.microsoft.com/sql/t-sql/statements/create-function-sql-data-warehouse?view=aps-pdw-2016-au7). |

## <a name="next-steps"></a>Passaggi successivi

Se non si riesce a trovare una soluzione al problema, ecco alcune altre risorse che è possibile provare.

* [Blog](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Richieste di funzionalità](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Video](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Creare un ticket di supporto](sql-data-warehouse-get-started-create-support-ticket.md)
* [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse)
* [Forum su Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)
