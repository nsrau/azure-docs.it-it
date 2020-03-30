---
title: Problemi di connessione dei database di origine
titleSuffix: Azure Database Migration Service
description: Informazioni su come risolvere i problemi/errori noti associati alla connessione del servizio migrazione del database di Azure ai database di origine.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 3eef7476a729c7b83290b9d8d86ba06524bed72b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297094"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>Risolvere gli errori del Servizio Migrazione del database di Azure durante la connessione a database di origine

L'articolo seguente illustra in dettaglio come risolvere i potenziali problemi che potrebbero verificarsi durante la connessione del servizio DiSM (Azure Database Migration Service) al database di origine. Ogni sezione seguente si riferisce a un tipo specifico di database di origine, elencando l'errore che potrebbe verificarsi insieme ai dettagli e collegamenti a informazioni su come risolvere i problemi di connettività.

## <a name="sql-server"></a>SQL Server

Nella tabella seguente vengono forniti potenziali problemi associati alla connessione a un database di SQL Server di origine e al modo in cui risolverli.

| Errore         | Dettaglio causa e risoluzione dei problemi |
| ------------- | ------------- |
| Connessione SQL non riuscita. Si è verificato un errore di rete o specifico dell'istanza mentre veniva stabilita la connessione a SQL Server. Il server non è stato trovato o non è accessibile. Verificare che il nome dell'istanza sia corretto e che SQL Server sia configurato per consentire le connessioni remote.<br> | Questo errore si verifica se il servizio non riesce a individuare il server di origine. Per risolvere il problema, vedere l'articolo [Errore durante la connessione all'origine di SQL Server quando si utilizza la porta dinamica o l'istanza denominata](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance). |
| **Errore 53** - Connessione SQL non riuscita. (Inoltre, per i codici di errore 1, 2, 5, 53, 233, 258, 1225, 11001)<br><br> | Questo errore si verifica se il servizio non riesce a connettersi al server di origine. Per risolvere il problema, fare riferimento alle risorse seguenti e quindi riprovare. <br><br>  [Guida interattiva dell'utente per risolvere il problema di connettività](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [Prerequisiti per la migrazione di un database SQL Server a un database SQL di Azure](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [Prerequisiti per la migrazione di SQL Server in un'istanza gestita del database SQL di AzurePrerequisites for migrating SQL Server to an Azure SQL Database managed instance](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **Errore 18456** - Accesso non riuscito.<br> | Questo errore si verifica se il servizio non riesce a connettersi al database di origine utilizzando le credenziali T-SQL fornite. Per risolvere il problema, verificare le credenziali immesse. È inoltre possibile fare riferimento a [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) o ai documenti di risoluzione dei problemi elencati nella nota sotto questa tabella e quindi riprovare. |
| Valore AccountName non{0}valido ' ' fornito. Il formato previsto per NomeAccount è NomeDominio/NomeUtente<br> | Questo errore si verifica se l'utente seleziona l'autenticazione di Windows ma fornisce il nome utente in un formato non valido. Per risolvere il problema, specificare username nel formato corretto per l'autenticazione di Windows oppure selezionare **Autenticazione SQL**. |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

Nella tabella seguente vengono forniti potenziali problemi associati alla connessione a un database AWS RDS MySQL di origine e a come risolverli.

| Errore         | Dettaglio causa e risoluzione dei problemi |
| ------------- | ------------- |
| **Errore [2003]**[HY000] - connessione non riuscita. ERRORE [HY000] [MySQL][ODBC x.x(w) driver] Impossibile connettersi al server MySQL su ''server'' (10060) | Questo errore si verifica se il driver ODBC MySQL non è in grado di connettersi al server di origine. Per risolvere il problema, fare riferimento ai documenti di risoluzione dei problemi elencati nella nota sotto questa tabella e quindi riprovare.<br> |
| **Errore [2005]**[HY000] - connessione non riuscita. ERRORE [HY000] [MySQL][ODBC x.x(w) driver] Host server MySQL sconosciuto ''server'' | Questo errore si verifica se il servizio non riesce a trovare l'host di origine in RDS. Il problema potrebbe essere dovuto al fatto che l'origine elencata non esiste o si è verificato un problema con l'infrastruttura RDS. Per risolvere il problema, fare riferimento ai documenti di risoluzione dei problemi elencati nella nota sotto questa tabella e quindi riprovare.<br> |
| **Errore [1045]**[HY000] - connessione non riuscita. ERRORE [HY000] [MySQL][ODBC x.x(w) driver] Accesso negato per l'utente ''utente''''server' (utilizzando la password: YES) | Questo errore si verifica se il driver ODBC MySQL non è in grado di connettersi al server di origine a causa di credenziali non valide. Verificare le credenziali immesse. Se il problema persiste, verificare che il computer di origine disponga delle credenziali corrette. Potrebbe essere necessario reimpostare la password nella console. Se il problema persiste, fare riferimento ai documenti di risoluzione dei problemi elencati nella nota sotto questa tabella, quindi riprovare.<br> |
| **Errore [9002]**[HY000] - connessione non riuscita. ERRORE [HY000] [MySQL][ODBC x.x(w)driver] La stringa di connessione potrebbe non essere corretta. Visita il portale per le referenze.| Questo errore si verifica se la connessione non riesce a causa di un problema con la stringa di connessione. Verificare che la stringa di connessione fornita sia valida. Per risolvere il problema, fare riferimento ai documenti di risoluzione dei problemi elencati nella nota sotto questa tabella e quindi riprovare.<br> |
| **Errore nella registrazione binaria. La variabile binlog_format ha il valore ''valore'. Si prega di cambiarlo in 'riga'.** | Questo errore si verifica se si verifica un errore nella registrazione binaria; il binlog_format variabile ha il valore errato. Per risolvere il problema, modificare il binlog_format in gruppo di parametri in 'ROW'e quindi riavviare l'istanza. Per ulteriori informazioni, vedere la documentazione relativa a [Opzioni e variabili di registrazione binaria](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) o ai file di [registro del database MySQL di AWS RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html).<br> |

> [!NOTE]
> Per ulteriori informazioni sulla risoluzione dei problemi relativi alla connessione a un database AWS RDS MySQL di origine, vedere le risorse seguenti:
> * [Risoluzione dei problemi di connettività Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Come si risolvono i problemi di connessione all'istanza del database Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS PostgreSQL

Nella tabella seguente vengono forniti potenziali problemi associati alla connessione a un database AWS RDS PostgreSQL di origine e a come risolverli.

| Errore         | Dettaglio causa e risoluzione dei problemi |
| ------------- | ------------- |
| **Errore [101]**[08001] - connessione non riuscita. Errore [08001] timeout scaduto. | Questo errore si verifica se il driver Postgres non è in grado di connettersi al server di origine. Per risolvere il problema, fare riferimento ai documenti di risoluzione dei problemi elencati nella nota sotto questa tabella e quindi riprovare. |
| **Errore: il parametro wal_level ha il valore ''valore'. Si prega di cambiarlo in 'logico' per consentire la replica.** | Questo errore si verifica se il parametro wal_level ha il valore errato. Per risolvere il problema, modificare il rds.logical_replication nel gruppo di parametri su 1 e quindi riavviare l'istanza. Per altre informazioni, vedere [Prerequisiti per la migrazione a Azure PostgreSQL tramite DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites) o [PostgreSQL su Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html). |

> [!NOTE]
> Per ulteriori informazioni sulla risoluzione dei problemi relativi alla connessione a un database AWS RDS PostgreSQL di origine, vedere le risorse seguenti:
> * [Risoluzione dei problemi di connettività Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Come si risolvono i problemi di connessione all'istanza del database Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL Server

Nella tabella seguente vengono forniti potenziali problemi associati alla connessione a un database SQL Server AWS RDS di origine e a come risolverli.

| Errore         | Dettaglio causa e risoluzione dei problemi |
| ------------- | ------------- |
| **Errore 53** - Connessione SQL non riuscita. Si è verificato un errore di rete o specifico dell'istanza mentre veniva stabilita la connessione a SQL Server. Il server non è stato trovato o non è accessibile. Verificare che il nome dell'istanza sia corretto e che SQL Server sia configurato per consentire le connessioni remote. (provider: Named Pipes Provider, errore: 40 - Impossibile aprire una connessione a SQL Server | Questo errore si verifica se il servizio non riesce a connettersi al server di origine. Per risolvere il problema, fare riferimento ai documenti di risoluzione dei problemi elencati nella nota sotto questa tabella e quindi riprovare. |
| **Errore 18456** - Accesso non riuscito. Accesso non riuscito per l'utente ''utente'' | Questo errore si verifica se il servizio non riesce a connettersi al database di origine con le credenziali T-SQL fornite. Per risolvere il problema, verificare le credenziali immesse. È inoltre possibile fare riferimento a [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) o ai documenti di risoluzione dei problemi elencati nella nota sotto questa tabella e riprovare. |
| **Errore 87** - Stringa di connessione non valida. Si è verificato un errore di rete o specifico dell'istanza mentre veniva stabilita la connessione a SQL Server. Il server non è stato trovato o non è accessibile. Verificare che il nome dell'istanza sia corretto e che SQL Server sia configurato per consentire le connessioni remote. (provider: Interfacce di rete SQL, errore: 25 - Stringa di connessione non valida) | Questo errore si verifica se il servizio non riesce a connettersi al server di origine a causa di una stringa di connessione non valida. Per risolvere il problema, verificare la stringa di connessione fornita. Se il problema persiste, fare riferimento ai documenti di risoluzione dei problemi elencati nella nota sotto questa tabella e quindi riprovare. |
| **Errore - Certificato del server non attendibile.** La connessione al server è stata stabilita con esito positivo, ma si è verificato un errore durante il processo di accesso. (provider: Provider SSL, errore: 0 - La catena di certificati è stata emessa da un'autorità non attendibile.) | Questo errore si verifica se il certificato utilizzato non è attendibile. Per risolvere il problema, è necessario trovare un certificato che può essere considerato attendibile e quindi abilitarlo sul server. In alternativa, è possibile selezionare l'opzione Certificato di attendibilità durante la connessione. Eseguire questa azione solo se si ha familiarità con il certificato utilizzato e si considera attendibile. <br> Le connessioni TLS crittografate utilizzando un certificato autofirmato non forniscono una sicurezza avanzata, che sono soggette ad attacchi man-in-the-middle. Non fare affidamento su TLS utilizzando certificati autofirmati in un ambiente di produzione o su server connessi a Internet. <br> Per altre informazioni, vedere Uso di [SSL con un'istanza di](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) database di Microsoft SQL Server o Esercitazione: Eseguire la migrazione di SQL Server [RDS ad Azure tramite DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites). |
| **Errore 300** - L'utente non dispone delle autorizzazioni necessarie. L'autorizzazione VIEW SERVER STATE è stata negata per l'oggetto ''server'', database ''database'' | Questo errore si verifica se l'utente non dispone dell'autorizzazione per eseguire la migrazione. Per risolvere il problema, vedere [GRANT Server Permissions - Transact-SQLo](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017) o [Esercitazione: eseguire la migrazione di SQL Server RDS ad Azure usando DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites) per ulteriori dettagli. |

> [!NOTE]
> Per ulteriori informazioni sulla risoluzione dei problemi relativi alla connessione a un'origine AWS RDS SQL Server, vedere le risorse seguenti:
>
> * [Solving Connectivity errors to SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) (Risoluzione di errori di connettività a SQL Server)
> * [Come si risolvono i problemi di connessione all'istanza del database Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>Problemi noti

* [Problemi noti/limitazioni di migrazione con le migrazioni online al database SQL di AzureKnown issues/migration limitations with online migrations to Azure SQL Database](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Problemi noti/limitazioni di migrazione con le migrazioni online al database di Azure per MySQLKnown issues/migration limitations with online migrations to Azure Database for MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Problemi noti/limitazioni di migrazione con le migrazioni online al database di Azure per PostgreSQLKnown issues/migration limitations with online migrations to Azure Database for PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Passaggi successivi

* Vedere l'articolo [Azure Database Migration Service PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Vedere l'articolo Come configurare i parametri del server nel database di [Azure per MySQL tramite il portale](https://docs.microsoft.com/azure/mysql/howto-server-parameters)di Azure.
* Vedere l'articolo [Panoramica dei prerequisiti per l'utilizzo del servizio Migrazione del database di Azure.](https://docs.microsoft.com/azure/dms/pre-reqs)
* Vedere le [domande frequenti sull'utilizzo](https://docs.microsoft.com/azure/dms/faq)del servizio di migrazione del database di Azure .
