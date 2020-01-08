---
title: Problemi di connessione dei database di origine
titleSuffix: Azure Database Migration Service
description: Informazioni su come risolvere i problemi noti o gli errori associati alla connessione del servizio migrazione del database di Azure ai database di origine.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: b697faeded4177381f70ebb9d1f93d928b25d7b1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437801"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>Risolvere gli errori DMS durante la connessione ai database di origine

L'articolo seguente fornisce informazioni dettagliate su come risolvere i potenziali problemi che possono verificarsi durante la connessione del servizio migrazione del database di Azure al database di origine. Ogni sezione seguente si riferisce a un tipo specifico di database di origine, elencando l'errore che si può incontrare insieme ai dettagli e i collegamenti alle informazioni su come risolvere i problemi di connettività.

## <a name="sql-server"></a>SQL Server

Nella tabella seguente sono riportati i potenziali problemi associati alla connessione a un database di SQL Server di origine e come risolverli.

| Errore         | Dettagli su cause e risoluzione dei problemi |
| ------------- | ------------- |
| Connessione SQL non riuscita. Si è verificato un errore di rete o specifico dell'istanza mentre veniva stabilita la connessione a SQL Server. Il server non è stato trovato o non è accessibile. Verificare che il nome dell'istanza sia corretto e che SQL Server sia configurato per consentire le connessioni remote.<br> | Questo errore si verifica se il servizio non è in grado di individuare il server di origine. Per risolvere il problema, vedere l'articolo [errore durante la connessione all'origine SQL Server quando si usa una porta dinamica o un'istanza denominata](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance). |
| **Errore 53** -connessione SQL non riuscita. (Anche per i codici di errore 1, 2, 5, 53, 233, 258, 1225, 11001)<br><br> | Questo errore si verifica se il servizio non è in grado di connettersi al server di origine. Per risolvere il problema, fare riferimento alle risorse seguenti, quindi riprovare. <br><br>  [Guida dell'utente interattivo per la risoluzione del problema di connettività](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [Prerequisiti per la migrazione di SQL Server al database SQL di Azure](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [Prerequisiti per la migrazione di SQL Server a un'istanza gestita di database SQL di Azure](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **Errore 18456** -accesso non riuscito.<br> | Questo errore si verifica se il servizio non è in grado di connettersi al database di origine utilizzando le credenziali T-SQL fornite. Per risolvere il problema, verificare le credenziali immesse. È anche possibile fare riferimento a [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) o ai documenti di risoluzione dei problemi elencati nella nota sotto questa tabella, quindi riprovare. |
| Il valore '{0}' specificato per l'account non è valido. Il formato previsto per AccountName è NomeDominio\NomeUtente<br> | Questo errore si verifica se l'utente seleziona l'autenticazione di Windows ma fornisce il nome utente in un formato non valido. Per risolvere il problema, specificare il nome utente nel formato corretto per l'autenticazione di Windows o selezionare **autenticazione SQL**. |

## <a name="aws-rds-mysql"></a>MySQL RDS di AWS

Nella tabella seguente sono riportati i potenziali problemi associati alla connessione a un database MySQL RDS di origine e come risolverli.

| Errore         | Dettagli su cause e risoluzione dei problemi |
| ------------- | ------------- |
| **Errore [2003]** [HY000]-connessione non riuscita. ERRORE [HY000] [MySQL] [driver ODBC x. x (w)] non è possibile connettersi al server MySQL su' {server}' (10060) | Questo errore si verifica se il driver ODBC MySQL non è in grado di connettersi al server di origine. Per risolvere il problema, fare riferimento ai documenti di risoluzione dei problemi elencati nella nota sotto questa tabella, quindi riprovare.<br> |
| **Errore [2005]** [HY000]-connessione non riuscita. ERRORE [HY000] [MySQL] [driver ODBC x. x (w)] host del server MySQL ' {server}' sconosciuto | Questo errore si verifica se il servizio non riesce a trovare l'host di origine su Servizi Desktop remoto. È possibile che il problema sia dovuto al fatto che l'origine elencata non esiste o si è verificato un problema con l'infrastruttura di Servizi Desktop remoto. Per risolvere il problema, fare riferimento ai documenti di risoluzione dei problemi elencati nella nota sotto questa tabella, quindi riprovare.<br> |
| **Errore [1045]** [HY000]-connessione non riuscita. ERRORE [HY000] [MySQL] [driver ODBC x. x (w)] accesso negato per l'utente ' {User}' @' {server}' (con password: Sì) | Questo errore si verifica se il driver ODBC MySQL non è in grado di connettersi al server di origine a causa di credenziali non valide. Verificare le credenziali immesse. Se il problema persiste, verificare che il computer di origine disponga delle credenziali corrette. Potrebbe essere necessario reimpostare la password nella console di. Se il problema persiste, fare riferimento ai documenti di risoluzione dei problemi elencati nella nota sotto questa tabella, quindi riprovare.<br> |
| **Errore [9002]** [HY000]-connessione non riuscita. ERRORE [HY000] [MySQL] [driver ODBC x. x (w)] la stringa di connessione potrebbe non essere corretta. Visitare il portale per i riferimenti.| Questo errore si verifica se la connessione ha esito negativo a causa di un problema con la stringa di connessione. Verificare che la stringa di connessione specificata sia valida. Per risolvere il problema, fare riferimento ai documenti di risoluzione dei problemi elencati nella nota sotto questa tabella, quindi riprovare.<br> |
| **Errore nella registrazione binaria. Il valore della variabile binlog_format è' {value}'. Modificarlo in "Row".** | Questo errore si verifica se si verifica un errore nella registrazione binaria. il valore della variabile binlog_format non è corretto. Per risolvere il problema, modificare il binlog_format nel gruppo di parametri in ' ROW ', quindi riavviare l'istanza. Per ulteriori informazioni, vedere la documentazione relativa alle [Opzioni e alle variabili di registrazione binaria](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) o ai [file di log del database di AWS RDS MySQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html).<br> |

> [!NOTE]
> Per ulteriori informazioni sulla risoluzione dei problemi relativi alla connessione a un database MySQL RDS di origine, vedere le risorse seguenti:
> * [Risoluzione dei problemi relativi alla connettività di Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Ricerca per categorie risolvere i problemi di connessione a un'istanza di database Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>PostgreSQL RDS per AWS

Nella tabella seguente sono riportati i potenziali problemi associati alla connessione a un database di origine RDS RDS di AWS e come risolverli.

| Errore         | Dettagli su cause e risoluzione dei problemi |
| ------------- | ------------- |
| **Errore [101]** [08001]-connessione non riuscita. ERRORE [08001] timeout scaduto. | Questo errore si verifica se il driver Postgres non riesce a connettersi al server di origine. Per risolvere il problema, fare riferimento ai documenti di risoluzione dei problemi elencati nella nota sotto questa tabella, quindi riprovare. |
| **Errore: il valore del parametro wal_level è' {value}'. Impostarla su "Logical" per consentire la replica.** | Questo errore si verifica se il valore del parametro wal_level non è corretto. Per risolvere il problema, modificare RDS. logical_replication nel gruppo di parametri in 1, quindi riavviare l'istanza. Per altre informazioni, vedere la pagina relativa ai [prerequisiti per la migrazione ad Azure PostgreSQL con DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites) o [PostgreSQL in Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html). |

> [!NOTE]
> Per ulteriori informazioni sulla risoluzione dei problemi relativi alla connessione a un database PostgreSQL RDS di origine, vedere le risorse seguenti:
> * [Risoluzione dei problemi relativi alla connettività di Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Ricerca per categorie risolvere i problemi di connessione a un'istanza di database Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS per SQL Server

Nella tabella seguente sono riportati i potenziali problemi associati alla connessione a un database di SQL Server RDS di AWS di origine e come risolverli.

| Errore         | Dettagli su cause e risoluzione dei problemi |
| ------------- | ------------- |
| **Errore 53** -connessione SQL non riuscita. Si è verificato un errore di rete o specifico dell'istanza mentre veniva stabilita la connessione a SQL Server. Il server non è stato trovato o non è accessibile. Verificare che il nome dell'istanza sia corretto e che SQL Server sia configurato per consentire le connessioni remote. (provider: provider named pipe, errore: 40-Impossibile aprire una connessione a SQL Server | Questo errore si verifica se il servizio non è in grado di connettersi al server di origine. Per risolvere il problema, fare riferimento ai documenti di risoluzione dei problemi elencati nella nota sotto questa tabella, quindi riprovare. |
| **Errore 18456** -accesso non riuscito. Accesso non riuscito per l'utente ' {User}' | Questo errore si verifica se il servizio non è in grado di connettersi al database di origine con le credenziali T-SQL fornite. Per risolvere il problema, verificare le credenziali immesse. È anche possibile fare riferimento a [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) o ai documenti di risoluzione dei problemi elencati nella nota sotto questa tabella, quindi riprovare. |
| **Errore 87** -la stringa di connessione non è valida. Si è verificato un errore di rete o specifico dell'istanza mentre veniva stabilita la connessione a SQL Server. Il server non è stato trovato o non è accessibile. Verificare che il nome dell'istanza sia corretto e che SQL Server sia configurato per consentire le connessioni remote. (provider: interfacce di rete SQL, errore: 25-la stringa di connessione non è valida) | Questo errore si verifica se il servizio non è in grado di connettersi al server di origine a causa di una stringa di connessione non valida. Per risolvere il problema, verificare la stringa di connessione specificata. Se il problema persiste, fare riferimento ai documenti di risoluzione dei problemi elencati nella nota sotto questa tabella, quindi riprovare. |
| **Errore: certificato server non attendibile.** La connessione al server è stata stabilita con esito positivo, ma si è verificato un errore durante il processo di accesso. (provider: provider SSL, errore: 0-la catena di certificati è stata emessa da un'autorità non attendibile). | Questo errore si verifica se il certificato utilizzato non è attendibile. Per risolvere il problema, è necessario trovare un certificato che può essere considerato attendibile e quindi abilitarlo nel server. In alternativa, è possibile selezionare l'opzione attendibilità del certificato durante la connessione. Eseguire questa azione solo se si ha familiarità con il certificato usato e considerato attendibile. <br> Le connessioni SSL crittografate con un certificato autofirmato non garantiscono una sicurezza elevata, ma sono soggette ad attacchi man-in-the-Middle. Non fare affidamento su SSL mediante certificati autofirmati in un ambiente di produzione o su server connessi a Internet. <br> Per altre informazioni, vedere la pagina relativa all' [uso di SSL con un'istanza di Microsoft SQL Server DB o un'](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) [esercitazione: eseguire la migrazione di SQL server RDS ad Azure con DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites). |
| **Errore 300** -l'utente non dispone delle autorizzazioni necessarie. Autorizzazione VIEW SERVER STATE negata per l'oggetto ' {server}', database ' {database}' | Questo errore si verifica se l'utente non dispone delle autorizzazioni necessarie per eseguire la migrazione. Per risolvere il problema, vedere concedere le autorizzazioni per il [Server-Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017) o [esercitazione: eseguire la SQL Server migrazione di Servizi Desktop remoto in Azure con DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites) per altri dettagli. |

> [!NOTE]
> Per ulteriori informazioni sulla risoluzione dei problemi relativi alla connessione a un'origine RDS di AWS SQL Server, vedere le risorse seguenti:
>
> * [Solving Connectivity errors to SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) (Risoluzione di errori di connettività a SQL Server)
> * [Ricerca per categorie risolvere i problemi di connessione a un'istanza di database Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>Problemi noti

* [Problemi noti/limitazioni della migrazione con migrazioni online al database SQL di Azure](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Problemi noti/limitazioni della migrazione con migrazioni online al database di Azure per MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Problemi noti/limitazioni della migrazione con migrazioni online al database di Azure per PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Passaggi successivi

* Vedere l'articolo [PowerShell del servizio migrazione del database di Azure](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Vedere l'articolo [come configurare i parametri del server in database di Azure per MySQL usando il portale di Azure](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* Vedere l'articolo [Panoramica dei prerequisiti per l'uso del servizio migrazione del database di Azure](https://docs.microsoft.com/azure/dms/pre-reqs).
* Vedere le [domande frequenti sull'uso del servizio migrazione del database di Azure](https://docs.microsoft.com/azure/dms/faq).
