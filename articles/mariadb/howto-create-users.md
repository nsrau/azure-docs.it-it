---
title: Creare utenti-database di Azure per MariaDB
description: Questo articolo descrive come creare nuovi account utente per interagire con un Database di Azure per il server MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: 882c8365bda87e97bfbc3bee9bdd320b312b4114
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542712"
---
# <a name="create-users-in-azure-database-for-mariadb"></a>Creare utenti in Database di Azure per MariaDB 
Questo articolo descrive come creare utenti in Database di Azure per MariaDB.

> [!NOTE]
> Comunicazione senza distorsione
>
> Microsoft supporta un ambiente diversificato ed inclusivo. Questo articolo contiene riferimenti alla parola _slave_. La [guida di stile Microsoft per la comunicazione senza pregiudizi](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) la riconosce come parola di esclusione. La parola viene usata in questo articolo per coerenza perché è attualmente la parola usata nel software. Quando il software verrà aggiornato per rimuovere la parola, questo articolo verrà aggiornato di conseguenza.
>

Quando si è creato il Database di Azure per MariaDB, si sono specificati un nome utente e una password per l'accesso come amministratore del server. Per altre informazioni, è possibile seguire le istruzioni della [Guida introduttiva](quickstart-create-mariadb-server-database-using-azure-portal.md). È possibile trovare il nome utente per l'accesso come amministratore del server nel portale di Azure.

L'utente amministratore del server ottiene determinati privilegi per il server, come elencato di seguito: SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER

Dopo aver creato il Database di Azure per il server MariaDB, è possibile usare il primo account utente amministratore del server per creare altri utenti e concedere loro l'accesso con privilegi di amministratore. Inoltre, l'account amministratore del server può essere usato per creare utenti con privilegi meno elevati che possono accedere a singoli schemi di database.

> [!NOTE]
> Il privilegio SUPER e il ruolo DBA non sono supportati. Esaminare i [privilegi](concepts-limits.md#privileges--data-manipulation-support) nell'articolo limitazioni per comprendere ciò che non è supportato nel servizio.<br><br>
> I plug-in per le password, ad esempio "validate_password" e "caching_sha2_password", non sono supportati dal servizio.

## <a name="create-additional-admin-users"></a>Creare altri utenti amministratore
1. Ottenere le informazioni per la connessione e il nome dell'utente amministratore.
   Per connettersi al server di database, è necessario il nome del server completo e le credenziali di accesso amministratore. È possibile trovare facilmente il nome del server e le informazioni di accesso nella pagina **Proprietà** o nella pagina **Panoramica** del server nel portale di Azure. 

2. Usare l'account amministratore e la password per connettersi al server di database. Usare lo strumento client preferito, ad esempio MySQL Workbench, mysql.exe, HeidiSQL o altri. 
   Se non si è certi della modalità di connessione, vedere [Usare MySQL Workbench per connettersi ed eseguire query sui dati](./connect-workbench.md).

3. Modificare ed eseguire il codice SQL seguente. Sostituire il valore segnaposto `new_master_user` con il nuovo nome utente. Questa sintassi concede i privilegi indicati su tutti gli schemi di database ( *.* ) all'utente specificato (new_master_user in questo esempio). 

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

## <a name="azure_superuser"></a>azure_superuser

Tutti i server di database di Azure per MySQL vengono creati con un utente denominato "azure_superuser". Si tratta di un account di sistema creato da Microsoft per gestire il server per eseguire il monitoraggio, i backup e altre attività di manutenzione periodiche. I tecnici di chiamata possono utilizzare questo account anche per accedere al server durante un evento imprevisto con l'autenticazione del certificato e devono richiedere l'accesso tramite processi JIT (just-in-Time).

## <a name="next-steps"></a>Passaggi successivi
Aprire il firewall per gli indirizzi IP dei computer dei nuovi utenti per consentire loro di connettersi: [Creare e gestire regole del firewall di Database di Azure per MariaDB con il portale di Azure](howto-manage-firewall-portal.md)  

<!--or [Azure CLI](howto-manage-firewall-using-cli.md).-->
