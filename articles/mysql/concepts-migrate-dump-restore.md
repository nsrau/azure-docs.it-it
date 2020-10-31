---
title: Eseguire la migrazione tramite dump e ripristino - Database di Azure per MySQL
description: Questo articolo illustra due modi comuni per eseguire il backup e il ripristino dei database nel database di Azure per MySQL usando strumenti come mysqldump, MySQL Workbench e PHPMyAdmin.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 10/30/2020
ms.openlocfilehash: 336021792b7e5340e35a0c59e0f113d4dad9307d
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128964"
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Eseguire la migrazione del database MySQL nel database di Azure mediante dump e ripristino

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Questo articolo illustra due modi comuni per eseguire il backup e il ripristino dei database nel database di Azure per MySQL
- Dump e ripristino dalla riga di comando( tramite mysqldump)
- Dump e ripristino con PHPMyAdmin

È anche possibile fare riferimento alla [Guida alla migrazione del database](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide) per informazioni dettagliate e casi d'uso sulla migrazione di database al database di Azure per MySQL. Questa guida fornisce indicazioni che comporteranno la corretta pianificazione e l'esecuzione di una migrazione di MySQL in Azure.

## <a name="before-you-begin"></a>Prima di iniziare
Per proseguire con questa guida è necessario:
- [Creare un database di Azure per il server MySQL - portale di Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- Avere installato su un computer l'utilità della riga di comando [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html).
- [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) o un altro strumento MySQL di terze parti per eseguire i comandi dump e Restore.

> [!TIP]
> Se si sta cercando di eseguire la migrazione di database di grandi dimensioni con dimensioni di database maggiori di 1 TBs, è consigliabile usare strumenti della community come il **Dumper/il caricatore** che supporta l'esportazione e l'importazione parallele. Informazioni [su come eseguire la migrazione di database MySQL di grandi dimensioni](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699).


## <a name="common-use-cases-for-dump-and-restore"></a>Casi d'uso comuni per dump e ripristino

I casi d'uso più comuni sono:

- Il **passaggio da un altro provider di servizi gestiti** : la maggior parte dei provider di servizi gestiti potrebbe non consentire l'accesso al file di archiviazione fisica per motivi di sicurezza, in modo da consentire la migrazione di backup e ripristino logici.
- **Migrazione da un ambiente locale o da una macchina virtuale** : database di Azure per MySQL non supporta il ripristino di backup fisici che rendono il backup logico e il ripristino come unico approccio.
- **Lo spazio di archiviazione di backup dall'archiviazione con ridondanza locale a quella con ridondanza geografica** : database di Azure per MySQL consente la configurazione dell'archiviazione con ridondanza locale o con ridondanza geografica per il backup solo durante la creazione del Dopo il provisioning del server, non è possibile modificare l'opzione di ridondanza per l'archivio di backup. Per spostare l'archivio di backup dall'archiviazione con ridondanza locale all'archiviazione con ridondanza geografica, è possibile eseguire dump e ripristino. 
-  **Migrazione da motori di archiviazione alternativi a InnoDB** -database di Azure per MySQL supporta solo il motore di archiviazione InnoDB e pertanto non supporta motori di archiviazione alternativi. Se le tabelle sono configurate con motori di archiviazione alternativi, convertirli nel formato di motore InnoDB prima della migrazione al database di Azure per MySQL.

    Se ad esempio si possiede WordPress o WebApp con le tabelle MyISAM, convertire le tabelle eseguendo la migrazione nel formato InnoDB prima del ripristino al database di Azure per MySQL. Usare la clausola `ENGINE=InnoDB` per impostare il motore usato per la creazione di una nuova tabella e quindi trasferire i dati nella tabella compatibile prima del ripristino.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
> [!Important]
> - Per evitare eventuali problemi di compatibilità, verificare che venga usata la stessa versione di MySQL nei sistemi di origine e di destinazione durante il dump dei database. Ad esempio, se il server MySQL esistente è versione 5.7, è necessario eseguire la migrazione al database di Azure per MySQL configurato per l'esecuzione della versione 5.7. Il comando `mysql_upgrade` non funziona in un database di Azure per il server MySQL e non è supportato. 
> - Se è necessario eseguire l'aggiornamento in tutte le versioni di MySQL, eseguire prima il dump o esportare il database di una versione inferiore in una versione successiva di MySQL nel proprio ambiente. Eseguire quindi `mysql_upgrade` prima di tentare la migrazione in un database di Azure per MySQL.

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni
Per ottimizzare le prestazioni, tenere presenti le considerazioni seguenti durante il dump di database di grandi dimensioni:
-   Usare l'opzione `exclude-triggers` in mysqldump durante il dump dei database. Escludere i trigger dai file di dump per evitare l'attivazione dei comandi di trigger durante il ripristino dei dati.
-   Usare l'opzione `single-transaction` per impostare la modalità di isolamento della transazione su REPEATABLE READ e inviare un'istruzione SQL START TRANSACTION al server prima di creare un dump dei dati. Il dump di molte tabelle all'interno di una singola transazione causa l'uso di spazio di archiviazione aggiuntivo durante il ripristino. Le opzioni `single-transaction` e `lock-tables` si escludono a vicenda in quanto LOCK TABLES causa il commit implicito di eventuali transazioni in sospeso. Per eseguire il dump di tabelle di grandi dimensioni, combinare l'opzione `single-transaction` con l'opzione `quick`.
-   Usare la sintassi a più righe `extended-insert` che include vari elenchi VALUES. Ciò consente di ottenere un file di dump più piccolo e di velocizzare gli inserimenti quando il file viene ricaricato.
-  Usare l'opzione `order-by-primary` in mysqldump durante il dump dei database, in modo che i dati vengano inseriti nello script nell'ordine delle chiavi primarie.
-   Usare l'opzione `disable-keys` in mysqldump durante il dump dei dati, per disabilitare i vincoli della chiave esterna prima del caricamento. La disabilitazione dei controlli della chiave esterna offre miglioramenti delle prestazioni. Abilitare i vincoli e verificare i dati dopo il caricamento per garantire l'integrità referenziale.
-   Usare le tabelle partizionate quando appropriato.
-   Caricare i dati in parallelo. Evitare un eccessivo parallelismo che comporterebbe il raggiungimento del limite di risorse e monitorare le risorse con le metriche offerta nel portale di Azure.
-   Usare l'opzione `defer-table-indexes` in mysqlpump durante il dump dei database, in modo che la creazione dell'indice venga eseguita dopo il caricamento dei dati delle tabelle.
-   Utilizzare l'opzione `skip-definer` in mysqlpump per omettere le clausole DEFINER e SQL SECURITY dalle istruzioni CREATE per le viste e le stored procedure.  Quando si ricarica il file di dump, vengono creati oggetti che usano i valori predefiniti DEFINER e SQL SECURITY.
-   Copiare i file di backup in un archivio/BLOB di Azure ed eseguire il ripristino da tale posizione. In questo modo, l'operazione dovrebbe essere eseguita in modo molto più veloce rispetto al ripristino attraverso Internet.

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Creare un database sul database di Azure per il server MySQL di destinazione
Creare un database vuoto nel database di Azure per il server MySQL di destinazione in cui si vuole eseguire la migrazione dei dati. Usare uno strumento come MySQL Workbench o mysql.exe per creare il database. Il database può avere lo stesso nome del database che contiene i dati di dump; in alternativa, è possibile creare un database con un nome diverso.

Per la connessione, individuare le informazioni di connessione nella pagina **Panoramica** del database di Azure per MySQL.

:::image type="content" source="./media/concepts-migrate-dump-restore/1_server-overview-name-login.png" alt-text="Trovare le informazioni di connessione nel portale di Azure":::

Aggiungere le informazioni di connessione in MySQL Workbench.

:::image type="content" source="./media/concepts-migrate-dump-restore/2_setup-new-connection.png" alt-text="Trovare le informazioni di connessione nel portale di Azure":::

## <a name="preparing-the-target-azure-database-for-mysql-server-for-fast-data-loads"></a>Preparazione del database di Azure di destinazione per il server MySQL per i caricamenti rapidi di dati
Per preparare il database di Azure di destinazione per il server MySQL per caricamenti di dati più veloci, è necessario modificare i parametri del server e la configurazione seguenti.
- max_allowed_packet: impostare su 1073741824 (ad esempio 1 GB) per evitare problemi di overflow dovuti a righe lunghe.
- slow_query_log: impostare su OFF per disattivare il log di query lento. In questo modo si eliminerà l'overhead causato dalla registrazione lenta di query durante i caricamenti dei dati.
- query_store_capture_mode: impostare su NONE per disattivare l'Query Store. In questo modo si eliminerà l'overhead causato dalle attività di campionamento di Query Store.
- innodb_buffer_pool_size: aumentare le prestazioni del server a 32 SKU con ottimizzazione per la memoria vCore dal piano tariffario del portale durante la migrazione per aumentare innodb_buffer_pool_size. È possibile aumentare innodb_buffer_pool_size solo aumentando il calcolo per il database di Azure per il server MySQL.
- innodb_io_capacity & innodb_io_capacity_max: passare a 9000 dai parametri del server in portale di Azure per migliorare l'utilizzo di IO per ottimizzare la velocità di migrazione.
- innodb_write_io_threads & innodb_write_io_threads: modificare in 4 i parametri del server in portale di Azure per migliorare la velocità di migrazione.
- Aumentare il livello di archiviazione: le operazioni di I/O al secondo per il database di Azure per il server MySQL aumentano progressivamente con l'aumento del livello di archiviazione. Per caricamenti più veloci è consigliabile aumentare il livello di archiviazione per aumentare le operazioni di I/O al secondo di cui è stato eseguito il provisioning. Tenere presente che l'archiviazione può essere solo aumentata, non ridotta.

Al termine della migrazione, è possibile ripristinare i valori precedenti dei parametri del server e della configurazione del livello di calcolo.

## <a name="dump-and-restore-using-mysqldump-utility"></a>Dump e ripristino con l'utilità mysqldump

### <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Creare un file di backup dalla riga di comando tramite mysqldump
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

Ad esempio, per eseguire il backup di un database denominato "testdb" nel proprio server MySQL con il nome utente "testuser" e senza password in un file testdb_backup.sql, usare il comando seguente. Il comando esegue il backup del database `testdb` in un file denominato `testdb_backup.sql`, che contiene tutte le istruzioni SQL necessarie per ricreare il database. Verificare che il nome utente ' testuser ' includa almeno il privilegio SELECT per le tabelle di cui è stato eseguito il dump, visualizzare la vista per le viste di cui è stato eseguito il dump, attivare per i trigger di cui è stato eseguito il dump e bloccare le tabelle se non si utilizza l'opzione--singola transazione

```bash
GRANT SELECT, LOCK TABLES, SHOW VIEW ON *.* TO 'testuser'@'hostname' IDENTIFIED BY 'password';
```
Eseguire ora mysqldump per creare il backup del `testdb` database

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

### <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Ripristinare il database MySQL mediante una riga di comando o MySQL Workbench
Dopo avere creato il database di destinazione è possibile usare il comando mysql o MySQL Workbench per ripristinare i dati nel database appena creato specificatamente dal file di dump.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
In questo esempio, ripristinare i dati nel database appena creato nel database di Azure per il server MySQL di destinazione.

Di seguito è riportato un esempio di come usare **MySQL** per un **server singolo** :

```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```
Di seguito è riportato un esempio di come usare **MySQL** per il **server flessibile** :

```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin -p testdb < testdb_backup.sql
```
---

## <a name="dump-and-restore-using-phpmyadmin"></a>Dump e ripristino con PHPMyAdmin
Seguire questa procedura per eseguire il dump e il ripristino di un database con PHPMyadmin.

> [!NOTE]
> Per il server singolo, il nome utente deve avere il formato ' username@servername ', ma per server flessibile è possibile usare solo ' username ' se si usa ' username@servername ' per il server flessibile, la connessione avrà esito negativo.

### <a name="export-with-phpmyadmin"></a>Esportare con PHPMyadmin
Per l'esportazione è possibile usare lo strumento comune phpMyAdmin, che potrebbe essere già installato in locale nel proprio ambiente. Per esportare il database MySQL mediante PHPMyAdmin:
1. Aprire phpMyAdmin.
2. Selezionare il database. Fare clic sul nome del database nell'elenco a sinistra.
3. Fare clic sul collegamento **Export** (Esporta). Viene visualizzata una nuova pagina per eseguire il dump del database.
4. Nell'area Export (Esporta) fare clic sul collegamento **Select All** (Seleziona tutto) per scegliere le tabelle nel database.
5. Nell'area delle opzioni SQL, fare clic sulle opzioni appropriate.
6. Fare clic sull'opzione **Save as file** (Salva come file) e sull'opzione di compressione corrispondente e quindi fare clic sul pulsante **Go** (Vai). Verrà visualizzata una finestra di dialogo che richiede di salvare il file in locale.

### <a name="import-using-phpmyadmin"></a>Importazione mediante PHPMyAdmin
L'importazione del database è simile all'esportazione. Procedere come segue:
1. Aprire phpMyAdmin.
2. Nella pagina di impostazione di phpMyAdmin fare clic su **Add** (Aggiungi) per aggiungere il database di Azure per il server MySQL. Specificare i dettagli della connessione e le informazioni di accesso.
3. Creare un database denominato in modo appropriato e selezionarlo a sinistra della schermata. Per riscrivere il database esistente, fare clic sul nome del database, selezionare tutte le caselle di controllo accanto ai nomi delle tabelle e selezionare **Drop** (Elimina) per eliminare le tabelle esistenti.
4. Fare clic sul collegamento **SQL** per visualizzare la pagina in cui è possibile digitare i comandi SQL o caricare il file SQL.
5. Usare il pulsante **Browse** (Sfoglia) per trovare il file nel database.
6. Fare clic sul pulsante **Go** (Vai) per esportare il backup, eseguire i comandi SQL e ricreare il database.

## <a name="known-issues"></a>Problemi noti
Per problemi noti, suggerimenti e consigli, è consigliabile consultare il [blog techcommunity](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/tips-and-tricks-in-using-mysqldump-and-mysql-restore-to-azure/ba-p/916912).

## <a name="next-steps"></a>Passaggi successivi
- [Connettere le applicazioni a Database di Azure per MySQL](./howto-connection-string.md).
- Per altre informazioni sulla migrazione dei database in Database di Azure per MySQL, vedere [Database Migration Guide](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide) (Guida alla migrazione di database).
- Se si sta cercando di eseguire la migrazione di database di grandi dimensioni con dimensioni di database maggiori di 1 TBs, è consigliabile usare strumenti della community come il **Dumper/il caricatore** che supporta l'esportazione e l'importazione parallele. Informazioni [su come eseguire la migrazione di database MySQL di grandi dimensioni](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699).
