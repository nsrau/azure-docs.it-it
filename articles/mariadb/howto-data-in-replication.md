---
title: Configurare la replica dei dati in ingresso per replicare dati in Database di Azure per MariaDB.
description: Questo articolo descrive come configurare la replica dei dati in ingresso per Database di Azure per MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 3897c402e45962836880ccebbeb252d189188d3c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61038586"
---
# <a name="how-to-configure-azure-database-for-mariadb-data-in-replication"></a>Come configurare Database di Azure per MariaDB per la replica dei dati in ingresso

In questo articolo viene descritto come configurare la replica dei dati in ingresso nel servizio Database di Azure per MariaDB configurando i server master e di replica. La replica dei dati in ingresso permette di sincronizzare i dati da un server MariaDB master, eseguito in locale, in macchine virtuali o servizi di database ospitati da altri provider di servizi cloud in una replica nel servizio Database di Azure per MariaDB. 

Questo articolo presuppone che l'utente abbia già acquisito una certa esperienza con server e database MariaDB.

## <a name="create-a-mariadb-server-to-be-used-as-replica"></a>Creare un server MariaDB da usare come replica

1. Creare un nuovo server di Database di Azure per MariaDB

   Creare un nuovo server MariaDB, ad esempio "replica.mariadb.database.azure.com". Per informazioni sulla creazione del server, vedere [Creare un server di Database di Azure per MariaDB usando il portale di Azure](quickstart-create-mariadb-server-database-using-azure-portal.md). Questo è il server di "replica" per la replica dei dati in ingresso.

   > [!IMPORTANT]
   > Il server di Database di Azure per MariaDB deve essere creato con il piano tariffario per utilizzo generico o quello con ottimizzazione per la memoria.
   > 

2. Creare gli stessi account utente e i privilegi corrispondenti

   Gli account utente non vengono replicati dal server master al server di replica. Se si prevede di fornire agli utenti accesso al server di replica, è necessario creare manualmente tutti gli account e i privilegi corrispondenti nel nuovo server di Database di Azure per MariaDB creato.

## <a name="configure-the-master-server"></a>Configurare il server master
I passaggi seguenti preparano e configurano il server MariaDB ospitato in locale in una macchina virtuale o un servizio di database ospitato da altri provider di servizi cloud per la replica dei dati in ingresso. Questo è il server "master" per la replica dei dati in ingresso. 

1. Attivare la registrazione binaria

   Verificare se la registrazione binaria è stata abilitata sul server master eseguendo questo comando: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Se la variabile [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) viene restituita con il valore "ON", la registrazione binaria è abilitata sul server. 

   Se `log_bin` viene restituito con il valore "OFF", abilitare la registrazione binaria modificando il file my.cnf con `log_bin=ON` e riavviare il server per rendere effettive le modifiche.

2. Impostazioni del server master

   Per la Replica dei dati in ingresso è necessario che il parametro `lower_case_table_names` sia coerente tra il server master e quello di replica. Per impostazione predefinita, questo parametro è impostato su 1 in Database di Azure per MariaDB. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Creare un nuovo ruolo di replica e configurare le autorizzazioni

   Sul server master creare un account utente configurato con i privilegi di replica. Questa operazione può essere eseguita tramite comandi SQL o uno strumento come MySQL Workbench. Valutare se si prevede di eseguire la replica con SSL, poiché è necessario specificare questa impostazione quando si crea l'utente. Per istruzioni su come [aggiungere account utente](https://mariadb.com/kb/en/library/create-user/) nel server master, fare riferimento alla documentazione di MariaDB. 

   Nei comandi seguenti il nuovo ruolo di replica creato è in grado di accedere al server master da qualsiasi computer, non solo dal computer che ospita il server master stesso. Questa operazione viene eseguita specificando "syncuser\@'%'" nel comando per la creazione dell'utente. Per altre informazioni su come [specificare i nomi degli account](https://mariadb.com/kb/en/library/create-user/#account-names), vedere la documentazione di MariaDB.

   **Comando SQL**

   *Replica con SSL*

   Se SSL deve essere obbligatorio per tutte le connessioni utente, quando si crea un utente usare il comando seguente: 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replica senza SSL*

   Se SSL non è obbligatorio per tutte le connessioni utente, quando si crea un utente usare il comando seguente:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   Per creare il ruolo di replica in MySQL Workbench, aprire il pannello **Users and Privileges** (Utenti e privilegi) dal pannello **Management** (Gestione). Fare quindi clic su **Add Account** (Aggiungi account). 
 
   ![Utenti e privilegi](./media/howto-data-in-replication/users_privileges.png)

   Digitare il nome utente nel campo **Login Name** (ID di accesso). 

   ![Sincronizzazione utente](./media/howto-data-in-replication/syncuser.png)
 
   Fare clic sul pannello **Administrative Roles** (Ruoli amministrativi) e quindi selezionare **Replication Slave** (Slave di replica) dall'elenco **Global Privileges** (Privilegi globali). Fare quindi clic su **Apply** (Applica) per creare il ruolo di replica.

   ![Slave di replica](./media/howto-data-in-replication/replicationslave.png)


4. Impostare il server master in modalità di sola lettura

   Prima di avviare il dump del database, il server deve essere impostato in modalità di sola lettura. In questa modalità, il server master non sarà in grado di elaborare alcuna transazione di scrittura. Valutare l'impatto che questa impostazione può avere sulle attività aziendali e pianificare l'intervallo di impostazione in sola lettura in un orario di minore attività, se necessario.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Ottenere l'offset e il nome del file di log binario

   Eseguire il comando [`show master status`](https://mariadb.com/kb/en/library/show-master-status/) per determinare l'offset e il nome del file di log binario corrente.
    
   ```sql
   show master status;
   ```
   Si otterranno risultati simili ai seguenti. Prendere nota del nome del file binario poiché sarà necessario specificarlo nei passaggi successivi.

   ![Risultati stato master](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-master-server"></a>Eseguire il dump e il ripristino del server master

1. Eseguire il dump di tutti i database dal server master

   Per eseguire il dump dei database dal server master è possibile usare mysqldump. Per informazioni dettagliate, vedere [Dump e ripristino](howto-migrate-dump-restore.md). Non è necessario eseguire il dump della libreria MySQL e della libreria di test.

2. Impostare il server master in modalità di lettura/scrittura

   Dopo avere eseguito il dump del database, riportare il server MariaDB master in modalità di lettura/scrittura.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Ripristinare il file di dump nel nuovo server

   Ripristinare il file di dump nel server creato nel servizio Database di Azure per MariaDB. Per informazioni su come ripristinare un file di dump in un server MariaDB, vedere [Dump e ripristino](howto-migrate-dump-restore.md). Se il file di dump ha grandi dimensioni, caricarlo in una macchina virtuale in Azure nella stessa area del server di replica. Ripristinare quindi il file nel server di Database di Azure per MariaDB dalla macchina virtuale.

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>Collegare il server master e quello di replica per avviare la Replica dei dati in ingresso

1. Impostare il server master

   Tutte le funzioni di replica dei dati in ingresso vengono eseguite tramite stored procedure. Per informazioni su tali procedure, vedere [Stored procedure per la replica dei dati in ingresso](reference-data-in-stored-procedures.md). Le stored procedure possono essere eseguite nella shell di MySQL o in MySQL Workbench.

   Per collegare due server e avviare la replica, accedere al server di replica di destinazione nel servizio Database di Azure per MariaDB e impostare l'istanza esterna come server master. A questo scopo, è possibile usare la stored procedure `mysql.az_replication_change_master` nel server di Database di Azure per MariaDB.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: nome host del server master
   - master_user: nome utente del server master
   - master_password: password per il server master
   - master_log_file: nome del file di log binario da `show master status` in esecuzione
   - master_log_pos: posizione del file di log binario da `show master status` in esecuzione
   - master_ssl_ca: contesto del certificato della CA. Se non si usa SSL, passare una stringa vuota.
       - È consigliabile passare questo parametro sotto forma di variabile. Per altre informazioni, vedere gli esempi seguenti.

   **esempi**

   *Replica con SSL*

   La variabile `@cert` viene creata eseguendo i comandi seguenti: 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   La replica con SSL viene configurata tra un server master ospitato nel dominio "companya.com" e un server di replica ospitato in Database di Azure per MariaDB. Sulla replica viene eseguita questa stored procedure. 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
   ```
   *Replica senza SSL*

   La replica senza SSL viene configurata tra un server master ospitato nel dominio "companya.com" e un server di replica ospitato in Database di Azure per MariaDB. Sulla replica viene eseguita questa stored procedure.

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
   ```

2. Avviare la replica

   Chiamare la stored procedure `mysql.az_replication_start` per avviare la replica.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Verificare lo stato della replica

   Chiamare il comando [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) sul server di replica per visualizzare lo stato della replica.
    
   ```sql
   show slave status;
   ```

   Se lo stato di `Slave_IO_Running` e `Slave_SQL_Running` è "yes" e il valore di `Seconds_Behind_Master` è "0", la replica funziona correttamente. `Seconds_Behind_Master` indica il ritardo della replica. Se il valore non è "0", significa che la replica sta elaborando gli aggiornamenti. 

## <a name="other-stored-procedures"></a>Altre stored procedure

### <a name="stop-replication"></a>Arrestare la replica

Per arrestare la replica tra il server master e quello di replica, usare la stored procedure seguente:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Rimuovere la relazione di replica

Per rimuovere la relazione di replica tra il server master e quello di replica, usare la stored procedure seguente:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Ignorare un errore di replica

Per ignorare un errore di replica e consentire alla replica di proseguire, usare la stored procedure seguente:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Passaggi successivi
- Leggere altre informazioni sulla [replica dei dati in ingresso](concepts-data-in-replication.md) per Database di Azure per MariaDB.
