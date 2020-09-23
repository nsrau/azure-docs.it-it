---
title: Risolvere il danneggiamento del database-database di Azure per MySQL
description: Informazioni su come risolvere i problemi di danneggiamento del database per database di Azure per MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 815b24d25e674e5460cc50d7eb6871f740994893
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938935"
---
# <a name="troubleshoot-database-corruption-on-azure-database-for-mysql"></a>Risolvere i problemi di danneggiamento del database nel database di Azure per MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Il danneggiamento del database può causare tempi di inattività per l'applicazione ed è anche fondamentale risolvere il problema nel tempo per evitare la perdita di dati. Quando si verifica un danneggiamento del database, nel server viene registrato questo errore **InnoDB: il danneggiamento della pagina del database su disco o a non è riuscito**.

In questa guida si apprenderà come correggere se il database o la tabella è danneggiata. Database di Azure per MySQL usa il motore InnoDB ed è dotato di operazioni automatiche di controllo e ripristino del danneggiamento. InnoDB verifica la presenza di pagine danneggiate eseguendo checksum in ogni pagina che legge e se rileva una discrepanza di checksum, arresterà automaticamente il server MySQL.

Provare a eseguire una di queste opzioni per attenuare rapidamente i problemi di danneggiamento del database.

## <a name="restart-your-mysql-server"></a>Riavviare il server MySQL

Si noti generalmente che un database o una tabella è danneggiata quando l'applicazione accede al database ro della tabella. Poiché InnoDB presenta un meccanismo di ripristino di emergenza che consente di risolvere la maggior parte dei problemi quando il server viene riavviato. Di conseguenza, il riavvio del server deve consentire il ripristino del server da un arresto anomalo che ha causato lo stato non valido del database.

##  <a name="resolve-data-corruption-with-dump-and-restore-method"></a>Risolvere il danneggiamento dei dati con il metodo dump e Restore

Si consiglia di risolvere il problema di danneggiamento con un **metodo di dump e ripristino**. Ciò implica l'accesso alla tabella danneggiata, usando l'utilità **mysqldump** per creare un backup logico della tabella, che conserverà la struttura della tabella e i dati al suo interno, quindi ricaricare la tabella nel database.

### <a name="backup-your-database-or-tables"></a>Eseguire il backup del database o delle tabelle

> [!Important]
> - Configurare una regola del firewall per accedere al server dal computer client. Vedere come configurare una [regola del firewall in un server singolo](howto-manage-firewall-using-portal.md) e [in una regola firewall in un server flessibile](flexible-server/how-to-connect-tls-ssl.md).
> - Usa l'opzione SSL ```--ssl-cert``` per **MYSQLDUMP** se SSL è abilitato

Creare un file di backup dalla riga di comando usando mysqldump con questo comando

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

I parametri da specificare sono:
- [SSL-cert =/Path/to/PEM] Scaricare il certificato SSL nel computer client e impostare il percorso nel comando. Non usare questo protocollo SSL è disabilitato.
- [host] è il database di Azure per il server MySQL
- [uname] è il nome utente dell'amministratore del server
- [pass] è la password per l'utente amministratore
- [dbname] è il nome del database
- [backupfile. SQL] se il nome file per il backup del database

> [!Important]
> - Per server singolo usare il formato ```admin-user@servername``` da sostituire ```myserveradmin``` nei comandi seguenti.
> - Per server flessibile usare il formato ```admin-user``` da sostituire ```myserveradmin``` nei comandi seguenti.

Se una tabella specifica è danneggiata, selezionare tabelle specifiche nel database per eseguire il backup usando questo esempio
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

Per eseguire il backup di uno o più database, utilizzare l'opzione--database ed elencare i nomi di database separati da spazi.

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

###  <a name="restore-your-database-or-tables"></a>Ripristinare il database o le tabelle

I passaggi seguenti illustrano il modo in cui TP ripristina il database o le tabelle. Una volta creato il file di backup, è possibile ripristinare la tabella o i database usando l'utilità ***MySQL** . Eseguire il comando come illustrato di seguito:

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Di seguito è riportato un esempio del ripristino ```testdb``` dal file di backup creato con **mysqldump**. 

> [!Important]
> - Per server singolo usare il formato ```admin-user@servername``` da sostituire ```myserveradmin``` nel comando seguente.
> - Per server flessibile usare il formato ```admin-user``` da sostituire ```myserveradmin``` nel comando seguente. 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>Passaggi successivi
Se i passaggi precedenti non consentono di risolvere il problema, è sempre possibile ripristinare l'intero server.
- [Ripristinare un server singolo di database di Azure per MySQL](howto-restore-server-portal.md)
- [Ripristinare un server flessibile di database di Azure per MySQL](flexible-server/how-to-restore-server-portal.md)



