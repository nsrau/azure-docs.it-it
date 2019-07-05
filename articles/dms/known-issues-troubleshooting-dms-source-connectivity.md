---
title: Articolo sulla risoluzione di problemi/errori associati alla connessione servizio migrazione del Database a database di origine noti | Microsoft Docs
description: Informazioni su come risolvere i problemi/errori noti associati alla connessione servizio migrazione del Database a database di origine.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: a4ebd1d4c85631cc6ebc1f5787e7545b78d62b5e
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462850"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>Risolvere gli errori di migrazione del database durante la connessione al database di origine

L'articolo seguente fornisce dettagli su come risolvere eventuali problemi che potrebbero verificarsi durante la connessione di Azure Database Migration Service (DMS) al database di origine. Ogni sezione seguente si riferisce a un tipo specifico di database di origine, elencare l'errore potrebbe verificarsi insieme ai dettagli e collegamenti a informazioni su come risolvere i problemi di connettività.

## <a name="sql-server"></a>SQL Server

Potenziali problemi associati alla connessione a un database di SQL Server di origine e nella tabella seguente vengono forniti come risolverli.

| Tipi di errore         | Causa e risoluzione dei problemi di dettaglio |
| ------------- | ------------- |
| Connessione SQL non è riuscita. Si è verificato un errore di rete o specifico dell'istanza mentre veniva stabilita la connessione a SQL Server. Il server non è stato trovato o non è accessibile. Verificare che il nome dell'istanza sia corretto e che SQL Server è configurato per consentire le connessioni remote.<br> | Questo errore si verifica se il servizio non è possibile individuare il server di origine. Per risolvere il problema, vedere l'articolo [errore durante la connessione a SQL Server di origine quando si utilizzano porte dinamiche o istanza denominata](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance). |
| **Errore 53** -connessione SQL non è riuscita. (Inoltre, per errore codici di 1, 2, 5, 53, 233, 258, 1225, 11001)<br><br> | Questo errore si verifica se il servizio non è possibile connettersi al server di origine. Per risolvere il problema, consulta le risorse seguenti e quindi riprovare. <br><br>  [Guida interattiva dell'utente per risolvere il problema di connettività](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [Prerequisiti per la migrazione a SQL Server al Database SQL di Azure](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [Prerequisiti per la migrazione a SQL Server a un'istanza gestita di Database SQL di Azure](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **Errore 18456** -accesso non riuscito.<br> | Questo errore si verifica se il servizio non è possibile connettersi al database di origine con le credenziali fornite T-SQL. Per risolvere il problema, verificare le credenziali immesse. È anche possibile fare riferimento a [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) o ai documenti sulla risoluzione dei problemi elencati nella nota sotto questa tabella e quindi riprovare. |
| Corretto valore AccountName '{0}' fornito. Il formato previsto per AccountName è Nomedominio\nomeutente<br> | Questo errore si verifica se l'utente seleziona l'autenticazione di Windows, ma fornisce il nome utente in un formato non valido. Per risolvere il problema, fornire nome utente nel formato corretto per l'autenticazione di Windows o select **autenticazione di SQL Server**. |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

Potenziali problemi associati alla connessione a un database di origine AWS RDS MySQL e come risolverli vengono forniti nella tabella seguente.

| Tipi di errore         | Causa e risoluzione dei problemi di dettaglio |
| ------------- | ------------- |
| **Errori [2003]** [HY000] - connessione non è riuscita. ERRORI [HY000] [MySQL] [ODBC driver x.x(w)] non è possibile connettersi al server MySQL '{server}' (10060) | Questo errore si verifica se il driver ODBC MySQL non è possibile connettersi al server di origine. Per risolvere il problema, consultare la risoluzione dei problemi elencati nella nota sotto questa tabella e quindi riprovare.<br> |
| **Errori [2005]** [HY000] - connessione non è riuscita. ERRORI [HY000] host del server MySQL sconosciuto [MySQL] [ODBC driver x.x(w)] '{server}' | Questo errore si verifica se il servizio non è possibile trovare l'host di origine in Servizi Desktop remoto. Il problema è possibile che l'origine elencato non esiste o si è verificato un problema con l'infrastruttura di servizi desktop remoto. Per risolvere il problema, consultare la risoluzione dei problemi elencati nella nota sotto questa tabella e quindi riprovare.<br> |
| **Errori [1045]** [HY000] - connessione non è riuscita. ERRORI [HY000] [MySQL] [ODBC driver x.x(w)] accesso negato per l'utente '{user}'@'{server}' (con password: YES) | Questo errore si verifica se il driver ODBC MySQL non è possibile connettersi al server di origine a causa di credenziali non valide. Verificare le credenziali immesso. Se il problema persiste, verificare che tale computer di origine ha le credenziali corrette. Potrebbe essere necessario reimpostare la password nella console. Se il problema persiste, fare riferimento ai documenti sulla risoluzione dei problemi elencati nella nota sotto questa tabella e quindi ripetere l'operazione.<br> |
| **Errore [9002]** [HY000] - connessione non è riuscita. ERRORI [HY000] [MySQL] [ODBC driver x.x(w)] la stringa di connessione potrebbe non essere adatte. Visitare il portale per i riferimenti.| Questo errore si verifica se la connessione ha esito negativo a causa di un problema con la stringa di connessione. Verificare la stringa di connessione fornita sia valida. Per risolvere il problema, consultare la risoluzione dei problemi elencati nella nota sotto questa tabella e quindi riprovare.<br> |
| **Errore nella registrazione binaria. La variabile binlog_format ha valore '{value}'. Modificarlo in 'row'.** | Questo errore si verifica se si verifica un errore nella registrazione binaria; la variabile binlog_format ha il valore errato. Per risolvere il problema, modificare il binlog_format nel gruppo di parametri per 'ROW' e quindi riavviare l'istanza. Per altre informazioni, vedere [delle opzioni di registrazione binario e le variabili](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) oppure [documentazione i file di Log del Database di AWS RDS MySQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html).<br> |

> [!NOTE]
> Per altre informazioni sulla risoluzione dei problemi correlati alla connessione a un database di origine AWS RDS MySQL, vedere le risorse seguenti:
> * [Risoluzione dei problemi per problemi di connettività di servizi desktop remoto di Amazon](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Come si risolvono problemi di connessione alla mia istanza di database di servizi desktop remoto Amazon?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>PostgreSQL RDS di AWS

Potenziali problemi associati alla connessione a un database di origine PostgreSQL RDS di AWS e le relative soluzioni sono disponibili nella tabella seguente.

| Tipi di errore         | Causa e risoluzione dei problemi di dettaglio |
| ------------- | ------------- |
| **Errori [101]** [08001] - connessione non è riuscita. Timeout di errore [08001] è scaduto. | Questo errore si verifica se il driver Postgres non può connettersi al server di origine. Per risolvere il problema, consultare la risoluzione dei problemi elencati nella nota sotto questa tabella e quindi riprovare. |
| **Errore: Wal_level parametro ha valore '{value}'. Modificarlo in 'logical' per consentire la replica.** | Questo errore si verifica se il parametro wal_level ha il valore errato. Per risolvere il problema, modificare il rds.logical_replication nel gruppo di parametri a 1 e quindi riavviare l'istanza. Per altre informazioni, vedere [prerequisiti per la migrazione a Azure PostgreSQL usando il servizio migrazione del database](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites) oppure [PostgreSQL in Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html). |

> [!NOTE]
> Per altre informazioni sulla risoluzione dei problemi correlati alla connessione a un database di origine PostgreSQL RDS di AWS, vedere le risorse seguenti:
> * [Risoluzione dei problemi per problemi di connettività di servizi desktop remoto di Amazon](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Come si risolvono problemi di connessione alla mia istanza di database di servizi desktop remoto Amazon?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS per SQL Server

Potenziali problemi associati alla connessione a un database di AWS RDS SQL Server di origine e nella tabella seguente vengono forniti come risolverli.

| Tipi di errore         | Causa e risoluzione dei problemi di dettaglio |
| ------------- | ------------- |
| **Errore 53** -connessione SQL non è riuscita. Si è verificato un errore di rete o specifico dell'istanza mentre veniva stabilita la connessione a SQL Server. Il server non è stato trovato o non è accessibile. Verificare che il nome dell'istanza sia corretto e che SQL Server è configurato per consentire le connessioni remote. (provider: Provider named pipe, errore: 40 - Impossibile aprire una connessione a SQL Server | Questo errore si verifica se il servizio non è possibile connettersi al server di origine. Per risolvere il problema, consultare la risoluzione dei problemi elencati nella nota sotto questa tabella e quindi riprovare. |
| **Errore 18456** -accesso non riuscito. Accesso non riuscito per l'utente '{user}' | Questo errore si verifica se il servizio non è possibile connettersi al database di origine con le credenziali di T-SQL specificate. Per risolvere il problema, verificare le credenziali immesse. È anche possibile fare riferimento a [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) o ai documenti sulla risoluzione dei problemi elencati nella nota sotto questa tabella, quindi riprovare. |
| **Errore 87** -stringa di connessione non è valida. Si è verificato un errore di rete o specifico dell'istanza mentre veniva stabilita la connessione a SQL Server. Il server non è stato trovato o non è accessibile. Verificare che il nome dell'istanza sia corretto e che SQL Server è configurato per consentire le connessioni remote. (provider: Interfacce di rete SQL, errore: 25 - stringa di connessione non è valida) | Questo errore si verifica se il servizio non è possibile connettersi al server di origine a causa di una stringa di connessione non è valido. Per risolvere il problema, verificare la stringa di connessione specificata. Se il problema persiste, consultare la risoluzione dei problemi elencati nella nota sotto questa tabella e quindi ripetere l'operazione. |
| **Errore - certificato Server non attendibile.** Una connessione è stata stabilita con il server, ma poi si è verificato un errore durante il processo di accesso. (provider: Provider SSL, errore: 0: la catena di certificati è stato emesso da un'autorità non attendibile.) | Questo errore si verifica se il certificato usato non è attendibile. Per risolvere il problema, è necessario trovare un certificato che può essere considerato attendibile e quindi abilitarlo nel server. In alternativa, è possibile selezionare l'opzione Considera attendibile certificato durante la connessione. Eseguire questa operazione solo se si ha familiarità con il certificato utilizzato e si ritiene attendibile. <br> Le connessioni SSL crittografate usando un certificato autofirmato non offrono sicurezza avanzata - siano soggetti ad attacchi man-in-the-middle. È opportuno non affidarsi al SSL usando certificati autofirmati in un ambiente di produzione o nei server connessi a internet. <br> Per altre informazioni, vedere [uso di SSL con un'istanza di database di Microsoft SQL Server](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) o [esercitazione: Eseguire la migrazione di servizi desktop remoto SQL Server in Azure con servizio migrazione del database](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites). |
| **Errore 300** -utente non dispone delle autorizzazioni necessarie. È stata negata l'autorizzazione VIEW SERVER STATE per l'oggetto '{server}', database '{database}' | Questo errore si verifica se l'utente non dispone dell'autorizzazione per eseguire la migrazione. Per risolvere il problema, fare riferimento a [Concedi autorizzazioni Server - Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017) o [esercitazione: Eseguire la migrazione di servizi desktop remoto SQL Server in Azure con servizio migrazione del database](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites) per altri dettagli. |

> [!NOTE]
> Per altre informazioni sulla risoluzione dei problemi correlati alla connessione a un'origine SQL Server RDS di AWS, vedere le risorse seguenti:
>
> * [Risoluzione degli errori di connettività di SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)
> * [Come si risolvono problemi di connessione alla mia istanza di database di servizi desktop remoto Amazon?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>Problemi noti

* [Limitazioni di problemi noti/migrazione con le migrazioni online al Database SQL di Azure](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Limitazioni di migrazione/problemi noti con migrazioni online al Database di Azure per MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Limitazioni di migrazione/problemi noti con migrazioni online al Database di Azure per PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Passaggi successivi

* Visualizzare l'articolo [PowerShell per Azure Database Migration Service](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Visualizzare l'articolo [come configurare i parametri del server nel Database di Azure per MySQL usando il portale di Azure](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* Visualizzare l'articolo [panoramica dei prerequisiti per usare servizio migrazione del Database](https://docs.microsoft.com/azure/dms/pre-reqs).
* Vedere le [domande frequenti sull'uso di Azure Database Migration Service](https://docs.microsoft.com/azure/dms/faq).
