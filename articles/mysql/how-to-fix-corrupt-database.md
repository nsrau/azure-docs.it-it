---
title: Risolvere il danneggiamento del database-database di Azure per MySQL
description: Questo articolo illustra come risolvere i problemi di danneggiamento del database nel database di Azure per MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 21e4189e56f704129710da5b1d39613c4e1b1df5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91766896"
---
# <a name="troubleshoot-database-corruption-in-azure-database-for-mysql"></a>Risolvere i problemi di danneggiamento del database nel database di Azure per MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Il danneggiamento del database può causare tempi di inattività per l'applicazione. È anche fondamentale risolvere i problemi di danneggiamento nel tempo per evitare la perdita di dati. Quando si verifica un danneggiamento del database, questo errore viene visualizzato nei log del server: `InnoDB: Database page corruption on disk or a failed.`

In questo articolo si apprenderà come risolvere i problemi di danneggiamento del database o della tabella. Il database di Azure per MySQL usa il motore InnoDB. Include operazioni automatiche di controllo e ripristino dei danneggiamenti. InnoDB verifica la presenza di pagine danneggiate eseguendo checksum in ogni pagina che legge. Se rileva una discrepanza di checksum, arresterà automaticamente il server MySQL.

Provare le opzioni seguenti per attenuare rapidamente i problemi di danneggiamento del database.

## <a name="restart-your-mysql-server"></a>Riavviare il server MySQL

In genere si noterà che un database o una tabella è danneggiata quando l'applicazione accede alla tabella o al database. InnoDB offre un meccanismo di ripristino di emergenza che consente di risolvere la maggior parte dei problemi quando il server viene riavviato. Il riavvio del server può quindi consentire il ripristino del server da un arresto anomalo che ha causato lo stato non valido del database.

## <a name="use-the-dump-and-restore-method"></a>Usare il metodo dump e Restore

È consigliabile risolvere i problemi di danneggiamento usando un metodo di *dump e ripristino* . Questo metodo implica:
1. Accesso alla tabella danneggiata.
1. Utilizzo dell'utilità mysqldump per creare un backup logico della tabella. Il backup manterrà la struttura della tabella e i dati al suo interno.
1. Ricarica della tabella nel database.

### <a name="back-up-your-database-or-tables"></a>Eseguire il backup del database o delle tabelle

> [!Important]
> - Assicurarsi di aver configurato una regola del firewall per accedere al server dal computer client. Per altre informazioni, vedere [configurare una regola del firewall in un singolo server](howto-manage-firewall-using-portal.md) e [configurare una regola del firewall in un server flessibile](flexible-server/how-to-connect-tls-ssl.md).
> - Usare l'opzione SSL `--ssl-cert` per mysqldump se SSL è abilitato.

Creare un file di backup dalla riga di comando usando mysqldump. Usare questo comando:

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Descrizioni parametri:
- `[ssl-cert=/path/to/pem]`: Il percorso del certificato SSL. Scaricare il certificato SSL nel computer client e impostare il percorso nel comando. Non usare questo parametro se SSL è disabilitato.
- `[host]`: Database di Azure per il server MySQL.
- `[uname]`: Nome utente dell'amministratore del server.
- `[pass]`: Password dell'utente amministratore.
- `[dbname]`: Nome del database.
- `[backupfile.sql]`: Nome file del backup del database.

> [!Important]
> - Per server singolo, utilizzare il formato `admin-user@servername` da sostituire `myserveradmin` nei comandi seguenti.
> - Per server flessibili, utilizzare il formato `admin-user` da sostituire `myserveradmin` nei comandi seguenti.

Se una tabella specifica è danneggiata, selezionare le tabelle specifiche del database di cui eseguire il backup:
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

Per eseguire il backup di uno o più database, utilizzare l' `--database` opzione ed elencare i nomi dei database separati da spazi:

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

### <a name="restore-your-database-or-tables"></a>Ripristinare il database o le tabelle

Nei passaggi seguenti viene illustrato come ripristinare il database o le tabelle. Dopo aver creato il file di backup, è possibile ripristinare le tabelle o i database usando l'utilità MySQL. Eseguire questo comando:

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Di seguito è riportato un esempio che ripristina `testdb` da un file di backup creato con mysqldump: 

> [!Important]
> - Per server singolo, utilizzare il formato `admin-user@servername` da sostituire `myserveradmin` nel comando seguente.
> - Per server flessibili, utilizzare il formato ```admin-user``` da sostituire `myserveradmin` nel comando seguente. 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>Passaggi successivi
Se i passaggi precedenti non consentono di risolvere il problema, è sempre possibile ripristinare l'intero server:
- [Ripristinare un server in database di Azure per MySQL-server singolo](howto-restore-server-portal.md)
- [Ripristinare un server in database di Azure per MySQL-server flessibile](flexible-server/how-to-restore-server-portal.md)



