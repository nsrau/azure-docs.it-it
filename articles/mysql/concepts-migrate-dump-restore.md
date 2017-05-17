---
title: Eseguire la migrazione del database MySQL mediante dump e ripristino nel database di Azure per MySQL | Microsoft Docs
description: Introduce la migrazione del database di Azure per MySQL.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: portal
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c09a6fa947d235189ab0137b074b6d7d9c925827
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---

# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Eseguire la migrazione del database MySQL nel database di Azure mediante dump e ripristino
Questo articolo illustra due modi comuni per eseguire il backup e ripristino dei database nel database di Azure per MySQL
- Backup e ripristino dalla riga di comando (tramite mysqldump) 
- Backup e ripristino con PHPMyAdmin 

## <a name="before-you-begin"></a>Prima di iniziare
Per proseguire con questa guida è necessario:
- [Creare un database di Azure per il server MySQL - portale di Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- Avere installato su un computer l'utilità della riga di comando [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html)
- MySQL Workbench [MySQL Workbench Download](https://dev.mysql.com/downloads/workbench/), Toad, Navicat o qualsiasi altro strumento MySQL di terze parti

## <a name="use-common-tools"></a>Usare strumenti comuni
Usare strumenti comuni, come ad esempio MySQL Workbench, mysqldump, Toad o Navicat per connettersi in modalità remota e ripristinare i dati nel database di Azure per MySQL. Usare tali strumenti sul computer client con una connessione internet per connettersi al database di Azure per MySQL. Usare una connessione SSL crittografata per le procedure di sicurezza consigliate. Vedere anche [Configure SSL connectivity in Azure Database for MySQL](concepts-ssl-connection-security.md) (Configurare la connettività SSL nel database di Azure per MySQL). Durante la migrazione al database di Azure per MySQL non è necessario spostare i file di dump in alcun percorso cloud speciale. 

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Creare un file di backup dalla riga di comando tramite mysqldump
Per eseguire il backup di un database MySQL in locale o su una macchina virtuale, eseguire questo comando: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

I parametri da specificare sono:
- [uname] Username del database 
- [pass] Password del database (si noti che non è presente alcuno spazio vuoto tra -p e la password) 
- [dbname] Nome del database 
- [backupfile.sql] Filename per il backup del database 
- [-opt] Opzione mysqldump 

Ad esempio, per eseguire il backup di un database denominato "testdb" con lo username "testuser" e senza password in un file testdb_backup.sql, usare il comando seguente. Il comando eseguirà il backup del database "testdb" in un file denominato testdb_backup.sql, che conterrà tutte le istruzioni SQL necessarie per ricreare il database. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Per selezionare tabelle specifiche nel database di cui si deve eseguire il backup, elencare i nomi delle tabelle separati da spazi. Ad esempio, per eseguire il backup delle sole tabelle table1 e table2 in "testdb", seguire questo esempio: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```

Per eseguire il backup di più database contemporaneamente, usare lo switch -database ed elencare i nomi dei database separati da spazi. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```
Per eseguire il backup di tutti i database del server contemporaneamente è necessario usare l'opzione --all-databases (tutti i database).
```
$ mysqldump -u root -p --all-databases > alldb_backup.sql 
```

## <a name="upload-files"></a>Caricamento di file
Con WinSCP è possibile caricare e gestire facilmente l'importazione o il dump dei file di un ambiente MySQL Azure o non Azure sul computer locale tramite protocollo SFTP o FTPS a scopo di esportazione.

## <a name="create-a-database-on-the-target-azure-mysql-server"></a>Creare un database sul server MySQL di Azure di destinazione
È necessario creare un database vuoto nel database di Azure per il server MySQL di destinazione in cui si desidera migrare i dati con MySQL Workbench, Toad, Navicat o qualsiasi altro strumento di terze parti per MySQL. Il database può avere lo stesso nome del database che contiene i dati di dump; in alternativa, è possibile creare un database con un nome diverso.

![Stringa di connessione del database di Azure per MySQL](./media/concepts-migrate-import-export/p5.png)

![Stringa di connessione MySQL Workbench](./media/concepts-migrate-import-export/p4.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Ripristinare il database MySQL mediante una riga di comando o MySQL Workbench
Dopo avere creato il database di destinazione è possibile usare il comando mysql o MySQL Workbench per ripristinare i dati nel database appena creato specificatamente dal file di dump.
```bash
mysql -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
In questo esempio, si ripristinerà i dati nel database testdb3 appena creato nel server di destinazione.
```bash
$ mysql -u root -p testdb3 < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Esportazione mediante PHPMyAdmin
Per l'esportazione è possibile usare lo strumento comune phpMyAdmin, che potrebbe essere già installato in locale nel proprio ambiente. Per esportare il database MySQL mediante PHPMyAdmin:
- Aprire phpMyAdmin.
- Selezionare il database facendo clic sul nome nell'elenco a sinistra della schermata. 
- Fare clic sul collegamento Esporta. Si dovrebbe visualizzare una nuova schermata indicante "View dump of database" (Visualizza dump del database). 
- Nell'area Esporta, fare clic sul collegamento Seleziona tutto per selezionare tutte le tabelle nel database. 
- Nell'area delle opzioni SQL, fare clic sulle opzioni desiderate. 
- Fare clic sull'opzione "Save as file" (Salva file con nome) e sull'opzione di compressione corrispondente, quindi fare clic sul pulsante "Vai". Verrà visualizzata una finestra di dialogo che richiede di salvare il file in locale.

## <a name="import-using-phpmyadmin"></a>Importazione mediante PHPMyAdmin
L'importazione del database è simile all'esportazione. Procedere come segue:
- Aprire phpMyAdmin. 
- Creare un database denominato in modo appropriato e selezionarlo facendo clic sul nome nell'elenco a sinistra della schermata. Per riscrivere l'importazione di un database esistente fare clic sul nome del database, selezionare tutte le caselle di controllo accanto ai nomi di tabella e selezionare Rilascia per eliminare tutte le tabelle esistenti nel database. 
- Fare clic sul collegamento SQL. Si dovrebbe visualizzare una nuova schermata in cui è possibile digitare nei comandi SQL o caricare il file SQL. 
- Usare il pulsante Sfoglia per trovare il file nel database. 
- Fare clic sul pulsante Vai. Così facendo si esporterà il backup, si eseguiranno i comandi SQL e si ricreerà il database.

## <a name="next-steps"></a>Passaggi successivi

[Creare un database di Azure per il server MySQL tramite il portale di Azure](quickstart-create-mysql-server-database-using-azure-portal.md) 
[Creare un database di Azure per il server MySQL tramite l'interfaccia della riga di comando di Azure](quickstart-create-mysql-server-database-using-azure-cli.md)

