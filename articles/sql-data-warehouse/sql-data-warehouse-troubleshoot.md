---
title: Risoluzione dei problemi relativi ad Azure SQL Data Warehouse | Documentazione Microsoft
description: Risoluzione dei problemi relativi a SQL Data Warehouse di Azure.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 51f1e444-9ef7-4e30-9a88-598946c45196
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 5d3bcc3c1434b16279778573ccf3034f9ac28a4d
ms.openlocfilehash: ecc008469ba754af12583d350706f626ec6690b5


---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Risoluzione dei problemi relativi a SQL Data Warehouse di Azure
Questo argomento elenca alcune delle domande più comuni per la risoluzione dei problemi che vengono poste dai clienti.Questo argomento elenca alcune delle domande più comuni per la risoluzione dei problemi che vengono poste dai clienti.

## <a name="connecting"></a>Connessione
| Problema | Risoluzione |
|:--- |:--- |
| Accesso non riuscito per l'utente 'NT AUTHORITY\ANONYMOUS LOGON'. (Microsoft SQL Server, Errore: 18456) |Questo errore si verifica quando un utente AAD tenta di connettersi al database master, ma non dispone di un utente nel database master.  Per risolvere questo problema specificare che l'SQL Data Warehouse a cui si desidera connettersi al momento della connessione o aggiungere l'utente al database master.  Per altri dettagli, vedere l'articolo [Panoramica della sicurezza][Security overview]. |
| L'entità server "MyUserName" non può accedere al database "master" nel contesto di sicurezza corrente. Impossibile aprire il database predefinito dell'utente. Accesso non riuscito. Accesso non riuscito per l'utente 'MyUserName'. (Microsoft SQL Server, Errore: 916) |Questo errore si verifica quando un utente AAD tenta di connettersi al database master, ma non dispone di un utente nel database master.  Per risolvere questo problema specificare che l'SQL Data Warehouse a cui si desidera connettersi al momento della connessione o aggiungere l'utente al database master.  Per altri dettagli, vedere l'articolo [Panoramica della sicurezza][Security overview]. |
| Errore CTAIP |Questo errore può verificarsi quando è stato creato un account di accesso nel database master di SQL Server, ma non nel database di SQL Data Warehouse.  Se si verifica questo errore, vedere l'articolo [Panoramica della sicurezza][Security overview].  Questo articolo illustra come creare un account di accesso e un utente in un database master e come creare un utente nel database di SQL Data Warehouse. |
| Blocco da parte del firewall |I database SQL di Azure sono protetti da firewall a livello di server e di database per garantire che solo gli indirizzi IP noti accedano a un database. I firewall sono protetti per impostazione predefinita, il che significa che è necessario abilitare in modo esplicito un indirizzo IP o un intervallo di indirizzi prima di potersi connettere.  Per configurare il firewall per l'accesso, seguire la procedura descritta in [Configurare l'accesso al firewall del server per l'indirizzo IP del client][Configure server firewall access for your client IP] nella [Procedura di configurazione del provisioning][Provisioning instructions]. |
| Impossibile connettersi con lo strumento o il driver |SQL Data Warehouse consiglia di usare [SSMS][SSMS], [SSDT per Visual Studio 2015][SSDT for Visual Studio 2015] o [sqlcmd][sqlcmd] per eseguire query sui dati. Per altre informazioni sui driver e sulla connessione a SQL Data Warehouse, vedere gli articoli [Driver per Azure SQL Data Warehouse][Drivers for Azure SQL Data Warehouse] e [Connettersi ad Azure SQL Data Warehouse][Connect to Azure SQL Data Warehouse]. |

## <a name="tools"></a>Strumenti
| Problema | Risoluzione |
|:--- |:--- |
| Esplora oggetti di Visual Studio non riconosce gli utenti di AAD |Questo è un problema noto.  Come soluzione alternativa è possibile visualizzare gli utenti in [sys.database_principals][sys.database_principals].  Per altre informazioni sull'uso di Azure Active Directory con SQL Data Warehouse, vedere [Autenticazione in Azure SQL Data Warehouse][Authentication to Azure SQL Data Warehouse]. |

## <a name="performance"></a>Prestazioni
| Problema | Risoluzione |
|:--- |:--- |
| Risoluzione dei problemi di prestazioni delle query |Se si sta cercando di risolvere i problemi relativi a una determinata query, vedere l'articolo su come [imparare a monitorare le query][Learning how to monitor your queries]. |
| Piani e prestazioni delle query di scarsa qualità sono spesso causati dalla mancanza di statistiche |La causa più comune di prestazioni di scarsa qualità è la mancanza di statistiche per le tabelle.  Per conoscere i dettagli su come creare statistiche e sui motivi per cui sono fondamentali per le prestazioni, vedere [Conservazione delle statistiche delle tabelle][Statistics]. |
| Concorrenza bassa/query in coda |Comprendere la [gestione del carico di lavoro][Workload management] è importante per capire come bilanciare l'allocazione di memoria con la concorrenza. |
| Come implementare le procedure consigliate |L'articolo [Procedure consigliate per SQL Data Warehouse][SQL Data Warehouse best practices] è un ottimo punto di partenza per informazioni su come migliorare le prestazioni delle query. |
| Come migliorare le prestazioni con la scalabilità |La soluzione per aumentare le prestazioni consiste a volte nell'aggiungere semplicemente maggiore potenza di calcolo alle query [ridimensionando SQL Data Warehouse][Scaling your SQL Data Warehouse]. |
| Scarse prestazioni delle query a causa di scarsa qualità degli indici |A volte le query possono rallentare a causa della [scarsa qualità degli indici columnstore][Poor columnstore index quality].  Per altre informazioni e per capire come [ricompilare gli indici per migliorare la qualità dei segmenti][Rebuild indexes to improve segment quality], vedere questo articolo. |

## <a name="system-management"></a>Gestione del sistema
| Problema | Risoluzione |
|:--- |:--- |
| Messaggio 40847: Non è stato possibile eseguire l'operazione perché il server avrebbe superato la quota di DTU consentita di 45000. |Ridurre il [DWU][DWU] del database che si sta tentando di creare oppure [richiedere un aumento della quota][request a quota increase]. |
| Analisi dell'uso dello spazio |Per comprendere l'uso dello spazio nel sistema, vedere [Dimensioni delle tabelle][Table sizes] . |
| Aiuto nella gestione delle tabelle |Per informazioni su come gestire le tabelle, vedere [Panoramica delle tabelle][Overview].  Questo articolo contiene anche collegamenti ad articoli più dettagliati, ad esempio relativi a [tipi di dati delle tabelle][Data types], [distribuzione di una tabella][Distribute], [indicizzazione di una tabella][Index],  [partizionamento di una tabella][Partition], [gestione delle statistiche delle tabelle][Statistics] e [tabelle temporanee][Temporary]. |

## <a name="polybase"></a>PolyBase
| Problema | Risoluzione |
|:--- |:--- |
| Errore UTF-8 |Il presente PolyBase supporta solo il caricamento di file di dati che sono stati codificati con UTF-8.  Per informazioni su come risolvere questa limitazione, vedere [Risolvere il requisito PolyBase UTF-8][Working around the PolyBase UTF-8 requirement] . |
| Caricamento non riuscito a causa di un elevato numero di righe |Attualmente PolyBase non supporta un elevato numero di righe.  Ciò significa che se la tabella contiene VARCHAR(MAX), NVARCHAR(MAX) o VARBINARY(MAX), le tabelle esterne non possono essere usate per caricare i dati.  Il caricamento di un elevato numero di righe è attualmente supportato solo attraverso Data Factory di Azure (con BCP), analisi di flusso di Azure, SSIS, BCP o la classe .NET SQLBulkCopy. Il supporto di un elevato numero di righe in PolyBase verrà aggiunto in una versione futura. |
| Esito negativo del caricamento bcp della tabella con tipo di dati MAX |Esiste un problema noto che richiede che venga inserito VARCHAR(MAX), NVARCHAR(MAX) o VARBINARY(MAX) alla fine della tabella in alcuni scenari.  Provare a spostare le colonne MAX alla fine della tabella. |

## <a name="differences-from-sql-database"></a>Differenze rispetto al database SQL
| Problema | Risoluzione |
|:--- |:--- |
| Funzionalità non supportate del database SQL |Vedere [Funzionalità non supportate delle tabelle][Unsupported table features]. |
| Tipi di dati non supportati del database SQL |Vedere [Tipi di dati non supportati][Unsupported data types]. |
| Limitazioni DELETE e UPDATE |Vedere [Soluzioni alternative UPDATE][UPDATE workarounds], [Soluzioni alternative DELETE][DELETE workarounds] e [Uso di CTAS per ovviare a sintassi UPDATE e DELETE non supportate][Using CTAS to work around unsupported UPDATE and DELETE syntax]. |
| Istruzione MERGE non supportata |Vedere [Soluzioni alternative MERGE][MERGE workarounds]. |
| Limitazioni delle stored procedure |Per capire alcune limitazioni delle stored procedure, vedere [Limitazioni delle stored procedure][Stored procedure limitations]. |
| Le UDF non supportano istruzioni SELECT |Si tratta di una limitazione corrente delle UDF.  Per conoscere la sintassi supportata, vedere [CREATE FUNCTION][CREATE FUNCTION]. |

## <a name="next-steps"></a>Passaggi successivi
Se non si riesce a trovare una soluzione al problema precedente, ecco alcune altre risorse che è possibile provare.

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
[Security overview]: ./sql-data-warehouse-overview-manage-security.md
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx
[SSDT for Visual Studio 2015]: ./sql-data-warehouse-install-visual-studio.md
[Drivers for Azure SQL Data Warehouse]: ./sql-data-warehouse-connection-strings.md
[Connect to Azure SQL Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[Creare un ticket di supporto]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Scaling your SQL Data Warehouse]: ./sql-data-warehouse-manage-compute-overview.md
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[request a quota increase]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Learning how to monitor your queries]: ./sql-data-warehouse-manage-monitor.md
[Provisioning instructions]: ./sql-data-warehouse-get-started-provision.md
[Configure server firewall access for your client IP]: ./sql-data-warehouse-get-started-provision.md#create-a-server-level-firewall-rule-in-the-azure-portal
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[Table sizes]: ./sql-data-warehouse-tables-overview.md#table-size-queries
[Unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuild indexes to improve segment quality]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Workload management]: ./sql-data-warehouse-develop-concurrency.md
[Using CTAS to work around unsupported UPDATE and DELETE syntax]: ./sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
[UPDATE workarounds]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[DELETE workarounds]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[MERGE workarounds]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Stored procedure limitations]: ./sql-data-warehouse-develop-stored-procedures.md#limitations
[Authentication to Azure SQL Data Warehouse]: ./sql-data-warehouse-authentication.md
[Working around the PolyBase UTF-8 requirement]: ./sql-data-warehouse-load-polybase-guide.md#working-around-the-polybase-utf-8-requirement

<!--MSDN references-->
[sys.database_principals]: https://msdn.microsoft.com/library/ms187328.aspx
[CREATE FUNCTION]: https://msdn.microsoft.com/library/mt203952.aspx
[sqlcmd]: https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-get-started-connect-sqlcmd/

<!--Other Web references-->
[Blog]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blog del team CAT]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Richieste di funzionalità]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum MSDN]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Forum su Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Video]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse



<!--HONumber=Dec16_HO1-->


