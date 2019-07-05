---
title: Configurare la replica dei dati in ingresso nel Database di Azure per MariaDB | Microsoft Docs
description: Questo articolo descrive come configurare i dati in replica nel Database di Azure per MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 21e8a88cc6f03b4d54a6c5299b0b6be36cc32d6d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444798"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Configurare la replica dei dati in ingresso nel Database di Azure per MariaDB

Questo articolo descrive come configurare i dati in replica nel Database di Azure per MariaDB configurando il server master e di replica. Questo articolo si presuppone una certa esperienza MariaDB server e database.

Per creare una replica nel Database di Azure per MariaDB servizio, replica dei dati Sincronizza i dati da un master MariaDB server locale, in macchine virtuali (VM) o in servizi di database cloud.

> [!NOTE]
> Se il server master è 10,2 o versioni successive, è consigliabile configurare replica di dati mediante [ID di transazione globale](https://mariadb.com/kb/en/library/gtid/).


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Creare un server di MariaDB da usare come replica

1. Creare un nuovo Database di Azure per MariaDB server (ad esempio, replica.mariadb.database.azure.com). Il server è il server di replica nella replica di dati.

    Per altre informazioni sulla creazione di server, vedere [creare un Database di Azure per MariaDB server usando il portale di Azure](quickstart-create-mariadb-server-database-using-azure-portal.md).

   > [!IMPORTANT]
   > È necessario creare il Database di Azure per MariaDB server nei piani tariffari utilizzo generico o con ottimizzazione per la memoria.

2. Creare account utente identici e i privilegi corrispondenti.
    
    Gli account utente non vengono replicati dal server master per il server di replica. Per fornire l'accesso utente al server di replica, è necessario creare manualmente tutti gli account e i privilegi corrispondenti nel Database di Azure appena creata per il server di MariaDB.

## <a name="configure-the-master-server"></a>Configurare il server master

I passaggi seguenti preparare e configurare il MariaDB server ospitato in locale, in una macchina virtuale o in un servizio di database cloud per la replica di dati. Il server di MariaDB è il master nella replica di dati.

1. Attivare la registrazione binaria.
    
    Per vedere se è abilitata la registrazione binaria sul master, immettere il comando seguente:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Se la variabile [ `log_bin` ](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) restituisce il valore `ON`, nel server è abilitata la registrazione binaria.

   Se `log_bin` restituisce il valore `OFF`, modificare il **cnf** file in modo che `log_bin=ON` attiva la registrazione binaria. Riavviare il server per rendere effettive le modifiche.

2. Configurare le impostazioni del server master.

    Replica di dati richiede che il parametro `lower_case_table_names` sia coerente tra il server master e di replica. Il `lower_case_table_names` parametro è impostato su `1` per impostazione predefinita nel Database di Azure per MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Creare un nuovo ruolo di replica e impostare le autorizzazioni.

   Creare un account utente nel server master che è configurato con i privilegi di replica. È possibile creare un account usando i comandi SQL o MySQL Workbench. Se si prevede di eseguire la replica con SSL, è necessario specificare questo quando si crea l'account utente.
   
   Per informazioni su come aggiungere gli account utente nel server master, vedere la [MariaDB documentazione](https://mariadb.com/kb/en/library/create-user/).

   Usando i comandi seguenti, il nuovo ruolo di replica possa accedere al master da qualsiasi computer, non solo per la macchina che ospita il master stessa. Per l'accesso, specificare **syncuser\@'% s'** nel comando per creare un utente.
   
   Per altre informazioni sulla documentazione di MariaDB, vedere [specificando i nomi degli account](https://mariadb.com/kb/en/library/create-user/#account-names).

   **Comando SQL**

   - Replica con SSL

       Per richiedere SSL per tutte le connessioni utente, immettere il comando seguente per creare un utente:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - Replica senza SSL

       Se SSL non è necessaria per tutte le connessioni, immettere il comando seguente per creare un utente:
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   Per creare il ruolo di replica in MySQL Workbench, nella **Management** riquadro, selezionare **agli utenti e i privilegi**. Quindi selezionare **Add Account**.
 
   ![Utenti e privilegi](./media/howto-data-in-replication/users_privileges.png)

   Immettere un nome utente nel **nome account di accesso** campo.

   ![Sincronizzazione utente](./media/howto-data-in-replication/syncuser.png)
 
   Selezionare il **ruoli amministrativi** pannello e quindi nell'elenco dei **privilegi globali**, selezionare **Replication Slave**. Selezionare **applica** per creare il ruolo di replica.

   ![Slave di replica](./media/howto-data-in-replication/replicationslave.png)


4. Impostare il server master alla modalità di sola lettura.

   Prima eseguire il dump di un database, il server deve essere inserito in modalità di sola lettura. In modalità di sola lettura, il master non può elaborare eventuali transazioni di scrittura. Per evitare l'impatto aziendale, pianificare la finestra di sola lettura durante un orario di scarso traffico.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Ottiene il nome del file di registro binario corrente e l'offset.

   Per determinare il nome del file di registro binario corrente e l'offset, eseguire il comando [ `show master status` ](https://mariadb.com/kb/en/library/show-master-status/).
    
   ```sql
   show master status;
   ```
   I risultati dovrebbero essere simili alla tabella seguente:
   
   ![Risultati stato master](./media/howto-data-in-replication/masterstatus.png)

   Prendere nota del nome di file binari, perché verrà essere utilizzato nei passaggi successivi.
   
6. Ottenere la posizione GTID (facoltativo, necessario per la replica con GTID).

   Eseguire la funzione [ `BINLOG_GTID_POS` ](https://mariadb.com/kb/en/library/binlog_gtid_pos/) per ottenere la posizione GTID per il nome del file binlog corrispondente e l'offset.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>Dump e ripristino del server master

1. Eseguire il dump di tutti i database dal server master.

   Usare mysqldump per eseguire il dump di tutti i database dal server master. Non è necessario eseguire il dump della libreria di MySQL e libreria di test.

    Per altre informazioni, vedere [Dump e ripristino](howto-migrate-dump-restore.md).

2. Impostare il server master in modalità lettura/scrittura.

   Dopo che il database è stato eseguito il dump, modificare lo schema MariaDB back server in modalità lettura/scrittura.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Ripristinare il file di dump nel nuovo server.

   Ripristinare il file di dump nel server creato nel servizio Database di Azure per MariaDB. Visualizzare [Dump e ripristino](howto-migrate-dump-restore.md) per informazioni su come ripristinare un file di dump per un server di MariaDB.

   Se il file di dump è grande, è necessario caricarlo in una VM di Azure nella stessa area del server di replica. Ripristinare il Database di Azure per MariaDB server dalla macchina virtuale.

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>Collegare il server master e di replica per avviare la replica dei dati in ingresso

1. Impostare il server master.

   Tutte le funzioni di replica dei dati in ingresso vengono eseguite tramite stored procedure. Per informazioni su tali procedure, vedere [Stored procedure per la replica dei dati in ingresso](reference-data-in-stored-procedures.md). Nella shell di MySQL o MySQL Workbench, è possono eseguire le stored procedure.

   Per collegare due server e avviare la replica, accedere al server di replica di destinazione nel database di Azure per il servizio di MariaDB. Successivamente, impostare l'istanza esterno come server master usando il `mysql.az_replication_change_master` o `mysql.az_replication_change_master_with_gtid` stored procedure del database di Azure per MariaDB server.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   oppure
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: nome host del server master
   - master_user: nome utente del server master
   - master_password: password per il server master
   - master_log_file: nome del file di log binario da `show master status` in esecuzione
   - master_log_pos: posizione del file di log binario da `show master status` in esecuzione
   - master_gtid_pos: Posizione GTID esecuzione `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: contesto del certificato della CA. Se non si usa SSL, passare un string.* vuoto
    
    
    \* È consigliabile passare nel parametro master_ssl_ca come una variabile. Per altre informazioni, vedere gli esempi seguenti.

   **esempi**

   - Replica con SSL

       Creare la variabile `@cert` eseguendo i comandi seguenti:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       Configurato la replica con SSL tra un server master è ospitato in AziendaA.IT il dominio e un server di replica ospitato nel Database di Azure per MariaDB. Sulla replica viene eseguita questa stored procedure.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - Replica senza SSL

       La replica senza SSL è configurata tra un server master è ospitato in AziendaA.IT il dominio e un server di replica ospitato nel Database di Azure per MariaDB. Sulla replica viene eseguita questa stored procedure.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Avvio della replica.

   Chiamare il `mysql.az_replication_start` stored procedure per avviare la replica.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Controllare lo stato della replica.

   Chiamare il comando [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) sul server di replica per visualizzare lo stato della replica.
    
   ```sql
   show slave status;
   ```

   Se `Slave_IO_Running` e `Slave_SQL_Running` si trovano nello stato `yes`e il valore di `Seconds_Behind_Master` è `0`, funzionamento della replica. `Seconds_Behind_Master` indica il ritardo della replica. Se il valore non è `0`, quindi la replica sta elaborando gli aggiornamenti.

4. Aggiornare le variabili server corrispondente per rendere i dati replica più sicura (obbligatorio solo per la replica senza GTID).
    
    A causa di una limitazione replica nativa MariaDB, è necessario impostare [ `sync_master_info` ](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) e [ `sync_relay_log_info` ](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) variabili sulla replica di tipo senza lo scenario GTID.

    Controllo del server slave `sync_master_info` e `sync_relay_log_info` variabili per assicurarsi che la replica dei dati in ingresso è stabile e impostare le variabili `1`.
    
## <a name="other-stored-procedures"></a>Altre stored procedure

### <a name="stop-replication"></a>Arrestare la replica

Per arrestare la replica tra il server master e quello di replica, usare la stored procedure seguente:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>Rimuovere la relazione di replica

Per rimuovere la relazione tra il server master e di replica, usare la stored procedure seguente:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>Ignorare l'errore di replica

Per ignorare un errore di replica e consentire la replica, usare la stored procedure seguente:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Passaggi successivi
Leggere altre informazioni sulla [replica dei dati in ingresso](concepts-data-in-replication.md) per Database di Azure per MariaDB.
