---
title: Creare utenti in Database di Azure per il server MariaDB
description: Questo articolo descrive come creare nuovi account utente per interagire con un Database di Azure per il server MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: ed373cfa0ac755d56e7bc2601c65e0e6482ff6d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61038872"
---
# <a name="create-users-in-azure-database-for-mariadb"></a>Creare utenti in Database di Azure per MariaDB 
Questo articolo descrive come creare utenti in Database di Azure per MariaDB.

Quando si è creato il Database di Azure per MariaDB, si sono specificati un nome utente e una password per l'accesso come amministratore del server. Per altre informazioni, è possibile seguire le istruzioni della [Guida introduttiva](quickstart-create-mariadb-server-database-using-azure-portal.md). È possibile trovare il nome utente per l'accesso come amministratore del server nel portale di Azure.

L'utente amministratore del server ottiene determinati privilegi per il server come indicato di seguito: SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER

Dopo aver creato il Database di Azure per il server MariaDB, è possibile usare il primo account utente amministratore del server per creare altri utenti e concedere loro l'accesso con privilegi di amministratore. Inoltre, l'account amministratore del server può essere usato per creare utenti con privilegi meno elevati che possono accedere a singoli schemi di database.

## <a name="create-additional-admin-users"></a>Creare altri utenti amministratore
1. Ottenere le informazioni per la connessione e il nome dell'utente amministratore.
   Per connettersi al server di database, è necessario il nome del server completo e le credenziali di accesso amministratore. È possibile trovare facilmente il nome del server e le informazioni di accesso nella pagina **Proprietà** o nella pagina **Panoramica** del server nel portale di Azure. 

2. Usare l'account amministratore e la password per connettersi al server di database. Usare lo strumento client preferito, ad esempio MySQL Workbench, mysql.exe, HeidiSQL o altri. 
   Se non si è certi della modalità di connessione, vedere [Usare MySQL Workbench per connettersi ed eseguire query sui dati](./connect-workbench.md).

3. Modificare ed eseguire il codice SQL seguente. Sostituire il valore segnaposto `new_master_user` con il nuovo nome utente. Questa sintassi concede i privilegi indicati su tutti gli schemi di database (*.*) all'utente specificato (new_master_user in questo esempio). 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. Verificare i privilegi concessi. 
   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="create-database-users"></a>Creare gli utenti del database

1. Ottenere le informazioni per la connessione e il nome dell'utente amministratore.
   Per connettersi al server di database, è necessario il nome del server completo e le credenziali di accesso amministratore. È possibile trovare facilmente il nome del server e le informazioni di accesso nella pagina **Proprietà** o nella pagina **Panoramica** del server nel portale di Azure. 

2. Usare l'account amministratore e la password per connettersi al server di database. Usare lo strumento client preferito, ad esempio MySQL Workbench, mysql.exe, HeidiSQL o altri. 
   Se non si è certi della modalità di connessione, vedere [Usare MySQL Workbench per connettersi ed eseguire query sui dati](./connect-workbench.md).

3. Modificare ed eseguire il codice SQL seguente. Sostituire il valore segnaposto `db_user` con il nuovo nome utente che si intende creare e il valore segnaposto `testdb` con il nome del database.

   Questa sintassi di codice SQL crea un nuovo database denominato testdb a scopo di esempio e quindi crea un nuovo utente nel servizio Database di Azure per MariaDB e concede tutti i privilegi di accesso al nuovo schema di database (testdb.\*) per tale utente. 

   ```sql
   CREATE DATABASE testdb;
   
   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';
   
   FLUSH PRIVILEGES;
   ```

4. Verificare i privilegi concessi all'interno del database.
   ```sql
   USE testdb;
   
   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Accedere al server, specificando il database designato, con il nuovo nome utente e la nuova password. Questo esempio illustra la riga di comando mysql. Con questo comando, viene chiesto di specificare la password per il nome utente. Sostituire i valori segnaposto con il nome del server, il nome del database e il nome utente effettivi.

   ```bash
   mysql --host mydemoserver.mariadb.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
   Per altre informazioni sulla gestione degli account utente, vedere la documentazione di MariaDB per la [gestione degli account utente](https://mariadb.com/kb/en/library/user-account-management/), la [sintassi di GRANT](https://mariadb.com/kb/en/library/grant/) e i [privilegi](https://mariadb.com/kb/en/library/grant/#privilege-levels).

## <a name="next-steps"></a>Passaggi successivi
Aprire il firewall per gli indirizzi IP dei computer dei nuovi utenti per consentire loro di connettersi: [Creare e gestire le regole del firewall di Database di Azure per MariaDB con il portale di Azure](howto-manage-firewall-portal.md)  

<!--or [Azure CLI](howto-manage-firewall-using-cli.md).-->
