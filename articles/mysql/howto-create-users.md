---
title: Creare database e utenti-database di Azure per MySQL
description: Questo articolo descrive come creare nuovi account utente per interagire con un database di Azure per il server MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: 3e1f24b3ae6133241660751293f52fec63dfbe73
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91766867"
---
# <a name="create-databases-and-users-in-azure-database-for-mysql"></a>Creare database e utenti in database di Azure per MySQL

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Questo articolo descrive come creare utenti in database di Azure per MySQL.

> [!NOTE]
> **Comunicazione senza distorsione**
>
> Microsoft supporta un ambiente eterogeneo e di inclusione. Questo articolo contiene riferimenti alla parola *slave*. La [Guida di stile Microsoft per la comunicazione senza distorsione](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) riconosce questo aspetto come una parola di esclusione. La parola viene usata in questo articolo per coerenza perché è la parola attualmente visualizzata nel software. Quando il software viene aggiornato per rimuovere la parola, questo articolo verrà aggiornato in modo da essere allineato.
>

Al momento della creazione del database di Azure per il server MySQL, sono stati specificati un nome utente e una password di amministratore del server. Per ulteriori informazioni, vedere questa [Guida introduttiva](quickstart-create-mysql-server-database-using-azure-portal.md). È possibile determinare il nome utente dell'amministratore del server nel portale di Azure.

L'utente amministratore del server dispone di questi privilegi: 

   SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER


Dopo aver creato un database di Azure per il server MySQL, è possibile usare il primo account amministratore del server per creare altri utenti e concedere loro l'accesso amministrativo. È anche possibile usare l'account amministratore del server per creare utenti con privilegi meno elevati che hanno accesso ai singoli schemi di database.

> [!NOTE]
> Il privilegio SUPER e il ruolo DBA non sono supportati. Esaminare i [privilegi](concepts-limits.md#privileges--data-manipulation-support) nell'articolo limitazioni per comprendere ciò che non è supportato nel servizio.
>
> I plug-in di password come `validate_password` e `caching_sha2_password` non sono supportati dal servizio.


## <a name="to-create-a-database-with-a-non-admin-user-in-azure-database-for-mysql"></a>Per creare un database con un utente non amministratore nel database di Azure per MySQL

1. Ottenere le informazioni per la connessione e il nome dell'utente amministratore.
   Per connettersi al server di database, è necessario il nome del server completo e le credenziali di accesso amministratore. È possibile trovare facilmente il nome del server e le informazioni di accesso nella pagina **Panoramica** del server o nella pagina delle **Proprietà** della portale di Azure.

2. Usare l'account amministratore e la password per connettersi al server di database. Usare lo strumento client preferito, ad esempio MySQL Workbench, mysql.exe o HeidiSQL.
   
   Se non si è certi della modalità di connessione, vedere [connettersi ed eseguire query sui dati per un singolo server](./connect-workbench.md) o [connettersi ed eseguire query sui dati per server flessibili](./flexible-server/connect-workbench.md).

3. Modificare ed eseguire il codice SQL seguente. Sostituire il valore del segnaposto `db_user` con il nuovo nome utente desiderato. Sostituire il valore del segnaposto `testdb` con il nome del database.

   Questo codice SQL crea un nuovo database denominato TestDB. Crea quindi un nuovo utente nel servizio MySQL e concede a tale utente tutti i privilegi per il nuovo schema del database (testdb \* ).

   ```sql
   CREATE DATABASE testdb;

   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';

   FLUSH PRIVILEGES;
   ```

4. Verificare le concessioni nel database:

   ```sql
   USE testdb;

   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Accedere al server, specificando il database designato e usando il nuovo nome utente e la nuova password. Questo esempio illustra la riga di comando mysql. Quando si usa questo comando, verrà richiesto di immettere la password dell'utente. Usare il nome del server, il nome del database e il nome utente.

   # <a name="single-server"></a>[Server singolo](#tab/single-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
   # <a name="flexible-server"></a>[Server flessibile](#tab/flexible-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user -p
   ```
 ---

## <a name="to-create-additional-admin-users-in-azure-database-for-mysql"></a>Per creare altri utenti amministratore in database di Azure per MySQL

1. Ottenere le informazioni per la connessione e il nome dell'utente amministratore.
   Per connettersi al server di database, è necessario il nome del server completo e le credenziali di accesso amministratore. È possibile trovare facilmente il nome del server e le informazioni di accesso nella pagina **Panoramica** del server o nella pagina delle **Proprietà** della portale di Azure.

2. Usare l'account amministratore e la password per connettersi al server di database. Usare lo strumento client preferito, ad esempio MySQL Workbench, mysql.exe o HeidiSQL.
   
   Se non si è certi della modalità di connessione, vedere [usare MySQL Workbench per connettersi ed eseguire query sui dati](./connect-workbench.md).

3. Modificare ed eseguire il codice SQL seguente. Sostituire il valore del segnaposto `new_master_user` con il nuovo nome utente. Questa sintassi concede i privilegi elencati a tutti gli schemi di database (*.*) all'utente ( `new_master_user` in questo esempio).

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION;

   FLUSH PRIVILEGES;
   ```

4. Verificare le concessioni:

   ```sql
   USE sys;

   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="azure_superuser"></a>azure_superuser

Tutti i server di database di Azure per MySQL vengono creati con un utente denominato "azure_superuser". Si tratta di un account di sistema creato da Microsoft per gestire il server per eseguire il monitoraggio, i backup e altre attività di manutenzione periodiche. I tecnici di chiamata possono utilizzare questo account anche per accedere al server durante un evento imprevisto con l'autenticazione del certificato e devono richiedere l'accesso tramite processi JIT (just-in-Time).

## <a name="next-steps"></a>Passaggi successivi

Aprire il firewall per gli indirizzi IP dei computer dei nuovi utenti per consentire loro di connettersi:
- [Creare e gestire le regole del firewall in un singolo server](howto-manage-firewall-using-portal.md) 
- [ Creare e gestire le regole del firewall in un server flessibile](flexible-server/how-to-connect-tls-ssl.md)

Per ulteriori informazioni sulla gestione degli account utente, vedere la documentazione del prodotto MySQL relativa alla [gestione degli account utente](https://dev.mysql.com/doc/refman/5.7/en/access-control.html), alla [sintassi Grant](https://dev.mysql.com/doc/refman/5.7/en/grant.html)e ai [privilegi](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
