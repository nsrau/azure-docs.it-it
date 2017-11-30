---
title: Eseguire la migrazione del database MySQL mediante dump e ripristino nel database di Azure per MySQL | Microsoft Docs
description: Questo articolo illustra due modi comuni per eseguire il backup e il ripristino dei database nel database di Azure per MySQL usando strumenti come mysqldump, MySQL Workbench e PHPMyAdmin.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: e962fd65244ceebfc7544dc5a1d1956dad811fea
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Eseguire la migrazione del database MySQL nel database di Azure mediante dump e ripristino
Questo articolo illustra due modi comuni per eseguire il backup e il ripristino dei database nel database di Azure per MySQL
- Dump e ripristino dalla riga di comando( tramite mysqldump) 
- Dump e ripristino con PHPMyAdmin 

## <a name="before-you-begin"></a>Prima di iniziare
Per proseguire con questa guida è necessario:
- [Creare un database di Azure per il server MySQL - portale di Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- Avere installato su un computer l'utilità della riga di comando [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html).
- MySQL Workbench [Download di MySQL Workbench](https://dev.mysql.com/downloads/workbench/), Toad, Navicat o qualsiasi altro strumento MySQL di terze parti per i comandi di dump e ripristino.

## <a name="use-common-tools"></a>Usare strumenti comuni
Usare utilità e strumenti comuni, come ad esempio MySQL Workbench, mysqldump, Toad o Navicat per connettersi in modalità remota e ripristinare i dati nel database di Azure per MySQL. Usare tali strumenti sul computer client con una connessione internet per connettersi al database di Azure per MySQL. Usare una connessione SSL crittografata per le procedure di sicurezza consigliate. Vedere anche [Configure SSL connectivity in Azure Database for MySQL](concepts-ssl-connection-security.md) (Configurare la connettività SSL nel database di Azure per MySQL). Durante la migrazione al database di Azure per MySQL non è necessario spostare i file di dump in alcun percorso cloud speciale. 

## <a name="common-uses-for-dump-and-restore"></a>Usi comuni per il dump e ripristino
È possibile usare le utilità di MySQL come mysqldump e mysqlpump per il dump e il caricamento di database in un database MySQL di Azure in diversi scenari comuni. In altri scenari è invece possibile usare l'approccio di [importazione ed esportazione](concepts-migrate-import-export.md).

- Usare i dump del database quando si esegue la migrazione dell'intero database. Questa indicazione è utile quando si sposta una grande quantità di dati di MySQL o quando si vuole ridurre al minimo l'interruzione del servizio per applicazioni o siti live. 
-  Verificare che tutte le tabelle nel database usino il motore di archiviazione InnoDB quando si caricano dati nel database di Azure per MySQL. Il database di Azure per MySQL supporta solo il motore di archiviazione InnoDB e pertanto non sono supportati motori di archiviazione alternativi. Se le tabelle sono configurate con motori di archiviazione alternativi, convertirli nel formato di motore InnoDB prima della migrazione al database di Azure per MySQL.
   Se ad esempio si possiede WordPress o WebApp con le tabelle MyISAM, convertire le tabelle eseguendo la migrazione nel formato InnoDB prima del ripristino al database di Azure per MySQL. Usare la clausola `ENGINE=InnoDB` per impostare il motore usato per la creazione di una nuova tabella e quindi trasferire i dati nella tabella compatibile prima del ripristino. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Per evitare eventuali problemi di compatibilità, verificare che venga usata la stessa versione di MySQL nei sistemi di origine e di destinazione durante il dump dei database. Ad esempio, se il server MySQL esistente è versione 5.7, è necessario eseguire la migrazione al database di Azure per MySQL configurato per l'esecuzione della versione 5.7. Il comando `mysql_upgrade` non funziona in un database di Azure per il server MySQL e non è supportato. Se è necessario eseguire l'aggiornamento in tutte le versioni di MySQL, eseguire prima il dump o esportare il database di una versione inferiore in una versione successiva di MySQL nel proprio ambiente. Eseguire quindi `mysql_upgrade` prima di tentare la migrazione in un database di Azure per MySQL.

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni
Per ottimizzare le prestazioni, tenere presenti le considerazioni seguenti durante il dump di database di grandi dimensioni:
-   Usare l'opzione `exclude-triggers` in mysqldump durante il dump dei database. Escludere i trigger dai file di dump per evitare l'attivazione dei comandi di trigger durante il ripristino dei dati. 
-   Usare l'opzione `single-transaction` per impostare la modalità di isolamento della transazione su REPEATABLE READ e inviare un'istruzione SQL START TRANSACTION al server prima di creare un dump dei dati. Il dump di molte tabelle all'interno di una singola transazione causa l'uso di spazio di archiviazione aggiuntivo durante il ripristino. L'opzione `single-transaction` e l'opzione `lock-tables` si escludono a vicenda in quanto LOCK TABLES causa il commit implicito di eventuali transazioni in sospeso. Per eseguire il dump tabelle di grandi dimensioni, combinare l'opzione `single-transaction` con l'opzione `quick`. 
-   Usare la sintassi a più righe `extended-insert` che include vari elenchi VALUES. Ciò consente di ottenere un file di dump più piccolo e di velocizzare gli inserimenti quando il file viene ricaricato.
-  Usare l'opzione `order-by-primary` in mysqldump durante il dump dei database, in modo che i dati vengano inseriti nello script nell'ordine delle chiavi primarie.
-   Usare l'opzione `disable-keys` in mysqldump durante il dump dei dati, per disabilitare i vincoli della chiave esterna prima del caricamento. La disabilitazione dei controlli della chiave esterna offre miglioramenti delle prestazioni. Abilitare i vincoli e verificare i dati dopo il caricamento per garantire l'integrità referenziale.
-   Usare le tabelle partizionate quando appropriato.
-   Caricare i dati in parallelo. Evitare un eccessivo parallelismo che comporterebbe il raggiungimento del limite di risorse e monitorare le risorse con le metriche offerta nel portale di Azure. 
-   Usare l'opzione `defer-table-indexes` in mysqlpump durante il dump dei database, in modo che la creazione dell'indice venga eseguita dopo il caricamento dei dati delle tabelle.

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Creare un file di backup dalla riga di comando tramite mysqldump
Per eseguire il backup di un database MySQL esistente nel server locale o in una macchina virtuale, eseguire il comando seguente: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

I parametri da specificare sono:
- [uname] Username del database 
- [pass] Password del database (si noti che non è presente alcuno spazio vuoto tra -p e la password) 
- [dbname] Nome del database 
- [backupfile.sql] Filename per il backup del database 
- [-opt] Opzione mysqldump 

Ad esempio, per eseguire il backup di un database denominato "testdb" nel proprio server MySQL con il nome utente "testuser" e senza password in un file testdb_backup.sql, usare il comando seguente. Il comando esegue il backup del database `testdb` in un file denominato `testdb_backup.sql`, che contiene tutte le istruzioni SQL necessarie per ricreare il database. 

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
```bash
$ mysqldump -u root -p --all-databases > alldb_backup.sql 
```

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Creare un database sul database di Azure per il server MySQL di destinazione
Creare un database vuoto nel database di Azure per il server MySQL di destinazione in cui si vuole eseguire la migrazione dei dati. Usare uno strumento come MySQL Workbench, Toad o Navicat per creare il database. Il database può avere lo stesso nome del database che contiene i dati di dump; in alternativa, è possibile creare un database con un nome diverso.

Per la connessione, individuare le informazioni di connessione nella pagina Proprietà nel database di Azure per MySQL.
![Trovare le informazioni di connessione nel portale di Azure](./media/concepts-migrate-dump-restore/1_server-properties-name-login.png)

Aggiungere le informazioni di connessione in MySQL Workbench.
![Stringa di connessione MySQL Workbench](./media/concepts-migrate-dump-restore/2_setup-new-connection.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Ripristinare il database MySQL mediante una riga di comando o MySQL Workbench
Dopo avere creato il database di destinazione è possibile usare il comando mysql o MySQL Workbench per ripristinare i dati nel database appena creato specificatamente dal file di dump.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
In questo esempio, ripristinare i dati nel database appena creato nel database di Azure per il server MySQL di destinazione.
```bash
$ mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Esportazione mediante PHPMyAdmin
Per l'esportazione è possibile usare lo strumento comune phpMyAdmin, che potrebbe essere già installato in locale nel proprio ambiente. Per esportare il database MySQL mediante PHPMyAdmin:
- Aprire phpMyAdmin.
- Selezionare il database. Fare clic sul nome del database nell'elenco a sinistra. 
- Fare clic sul collegamento **Export** (Esporta). Viene visualizzata una nuova pagina per eseguire il dump del database.
- Nell'area Export (Esporta) fare clic sul collegamento **Select All** (Seleziona tutto) per scegliere le tabelle nel database. 
- Nell'area delle opzioni SQL, fare clic sulle opzioni appropriate. 
- Fare clic sull'opzione **Save as file** (Salva come file) e sull'opzione di compressione corrispondente e quindi fare clic sul pulsante **Go** (Vai). Verrà visualizzata una finestra di dialogo che richiede di salvare il file in locale.

## <a name="import-using-phpmyadmin"></a>Importazione mediante PHPMyAdmin
L'importazione del database è simile all'esportazione. Procedere come segue:
- Aprire phpMyAdmin. 
- Nella pagina di impostazione di phpMyAdmin fare clic su **Add** (Aggiungi) per aggiungere il database di Azure per il server MySQL. Specificare i dettagli della connessione e le informazioni di accesso.
- Creare un database denominato in modo appropriato e selezionarlo a sinistra della schermata. Per riscrivere il database esistente, fare clic sul nome del database, selezionare tutte le caselle di controllo accanto ai nomi delle tabelle e selezionare **Drop** (Elimina) per eliminare le tabelle esistenti. 
- Fare clic sul collegamento **SQL** per visualizzare la pagina in cui è possibile digitare i comandi SQL o caricare il file SQL. 
- Usare il pulsante **Browse** (Sfoglia) per trovare il file nel database. 
- Fare clic sul pulsante **Go** (Vai) per esportare il backup, eseguire i comandi SQL e ricreare il database.

## <a name="next-steps"></a>Passaggi successivi
[Come connettere le applicazioni al database di Azure per MySQL](./howto-connection-string.md)
