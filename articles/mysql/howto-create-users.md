---
title: Creare database e utenti-database di Azure per MySQL
description: Questo articolo descrive come creare nuovi account utente per interagire con un database di Azure per il server MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 4/2/2020
ms.openlocfilehash: 9b79a0f21135e91ab72a4c8a9e604b84b67df0a9
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90902813"
---
# <a name="create-databases-and-users-in-azure-database-for-mysql-server"></a>Creare database e utenti nel database di Azure per il server MySQL

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Questo articolo descrive come creare utenti in un database di Azure per il server MySQL.

> [!NOTE]
> Comunicazione senza distorsione
>
> Microsoft supporta un ambiente eterogeneo e di inclusione. Questo articolo contiene riferimenti alla parola _slave_. La [Guida di stile Microsoft per la comunicazione senza distorsione](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) riconosce questo aspetto come una parola di esclusione. La parola viene usata in questo articolo per coerenza perché è attualmente la parola che viene visualizzata nel software. Quando il software viene aggiornato per rimuovere la parola, questo articolo verrà aggiornato in modo da essere allineato.
>

Quando si è creato il database di Azure per MySQL, si sono specificati un nome utente e una password per l'accesso come amministratore del server. Per altre informazioni, è possibile seguire le istruzioni della [Guida introduttiva](quickstart-create-mysql-server-database-using-azure-portal.md). È possibile trovare il nome utente per l'accesso come amministratore del server nel portale di Azure.

L'utente amministratore del server ottiene determinati privilegi per il server come indicato di seguito: 

   SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER


Dopo aver creato il database di Azure per il server MySQL, è possibile usare il primo account utente amministratore del server per creare altri utenti e concedere loro l'accesso con privilegi di amministratore. Inoltre, l'account amministratore del server può essere usato per creare utenti con privilegi meno elevati che possono accedere a singoli schemi di database.

> [!NOTE]
> Il privilegio SUPER e il ruolo DBA non sono supportati. Esaminare i [privilegi](concepts-limits.md#privilege-support) nell'articolo limitazioni per comprendere ciò che non è supportato nel servizio.

## <a name="how-to-create-database-with-non-admin-user-in-azure-database-for-mysql"></a>Come creare un database con un utente non amministratore nel database di Azure per MySQL

1. Ottenere le informazioni per la connessione e il nome dell'utente amministratore.
   Per connettersi al server di database, è necessario il nome del server completo e le credenziali di accesso amministratore. È possibile trovare facilmente il nome del server e le informazioni di accesso nella pagina **Proprietà** o nella pagina **Panoramica** del server nel portale di Azure.

2. Usare l'account amministratore e la password per connettersi al server di database. Usare lo strumento client preferito, ad esempio MySQL Workbench, mysql.exe, HeidiSQL o altri.
   Se non si è certi di come connettersi, vedere come usare MySQL Workbench per [connettersi ed eseguire query sui dati per un singolo server](./connect-workbench.md) o per [connettersi ed eseguire query sui dati per server flessibili](./flexible-server/connect-workbench.md)

3. Modificare ed eseguire il codice SQL seguente. Sostituire il valore segnaposto `db_user` con il nuovo nome utente che si intende creare e il valore segnaposto `testdb` con il nome del database.

   Questa sintassi di codice SQL crea un nuovo database denominato testdb a scopo di esempio e quindi crea un nuovo utente nel servizio di MySQL e concede tutti i privilegi di accesso al nuovo schema di database (testdb.\*) per tale utente.

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

# <a name="single-server"></a>[Server singolo](#tab/single-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
# <a name="flexible-server"></a>[Server flessibile](#tab/flexible-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user -p
   ```
 ---

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>Come creare altri utenti amministratore in Database di Azure per MySQL

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

## <a name="next-steps"></a>Passaggi successivi

Aprire il firewall per gli indirizzi IP dei computer dei nuovi utenti per consentire loro di connettersi:
- [Creare e gestire le regole del firewall in un singolo server](howto-manage-firewall-using-portal.md) 
- [ Creare e gestire le regole del firewall in un server flessibile](flexible-server/how-to-connect-tls-ssl.md)

Per altre informazioni sulla gestione degli account utente, vedere la documentazione di MySQL per la [gestione degli account utente](https://dev.mysql.com/doc/refman/5.7/en/access-control.html), la [sintassi di GRANT](https://dev.mysql.com/doc/refman/5.7/en/grant.html) e i [privilegi](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
