---
title: Configurare la replica dei dati in - Database di Azure per MariaDBConfigure data-in Replication - Azure Database for MariaDB
description: Questo articolo descrive come configurare la replica dei dati nel database di Azure per MariaDB.This article describes how to set up Data-in Replication in Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 51b800dde140affd222f2bdb341c0fbf3a57d8cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530156"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Configurare la replica dei dati nel database di Azure per MariaDBConfigure Data-in Replication in Azure Database for MariaDB

In questo articolo viene descritto come configurare la replica dei dati nel database di Azure per MariaDB configurando i server master e di replica. In questo articolo si presuppone che si dispone di una certa esperienza precedente con i server e i database MariaDB.

Per creare una replica nel servizio Database di Azure per MariaDB, La replica dei dati sincronizza i dati da un server MariaDB master in locale, nelle macchine virtuali (VM) o nei servizi di database cloud.

> [!NOTE]
> Se il server master è versione 10.2 o successiva, è consigliabile configurare la replica dei dati utilizzando [l'ID transazione globale](https://mariadb.com/kb/en/library/gtid/).


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Creare un server MariaDB da utilizzare come replicaCreate a MariaDB server to use as a replica

1. Creare un nuovo database di Azure per il server MariaDB,ad esempio, replica.mariadb.database.azure.com. Il server è il server di replica nella replica dei dati.

    Per altre informazioni sulla creazione di server, vedere Creare un database di Azure per il [server MariaDB tramite il portale](quickstart-create-mariadb-server-database-using-azure-portal.md)di Azure.

   > [!IMPORTANT]
   > È necessario creare il database di Azure per il server MariaDB nei livelli dei prezzi Scopo generale o Ottimizzazione della memoria.

2. Creare account utente identici e privilegi corrispondenti.
    
    Gli account utente non vengono replicati dal server master al server di replica. Per fornire l'accesso utente al server di replica, è necessario creare manualmente tutti gli account e i privilegi corrispondenti nel database di Azure appena creato per il server MariaDB.

## <a name="configure-the-master-server"></a>Configurare il server master

I passaggi seguenti preparano e configurano il server MariaDB ospitato in locale, in una macchina virtuale o in un servizio di database cloud per la replica dei dati. Il server MariaDB è il server master nella replica dei dati.

1. Attivare la registrazione binaria.
    
    Per verificare se la registrazione binaria è abilitata sul master, immettere il comando seguente:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Se la [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) variabile `ON`restituisce il valore , la registrazione binaria è abilitata sul server.

   Se `log_bin` restituisce `OFF`il valore , modificare il `log_bin=ON` file **my.cnf** in modo che risulti binario. Riavviare il server per rendere effettiva la modifica.

2. Configurare le impostazioni del server master.

    La replica dei dati `lower_case_table_names` richiede che il parametro sia coerente tra il server master e i server di replica. Il `lower_case_table_names` parametro `1` è impostato su per impostazione predefinita nel database di Azure per MariaDB.The parameter is set to by default in Azure Database for MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Creare un nuovo ruolo di replica e impostare le autorizzazioni.

   Creare un account utente nel server master configurato con privilegi di replica. È possibile creare un account utilizzando i comandi SQL o MySQL Workbench. Se si prevede di eseguire la replica con SSL, è necessario specificarlo quando si crea l'account utente.
   
   Per informazioni su come aggiungere account utente nel server master, vedere la documentazione di [MariaDB](https://mariadb.com/kb/en/library/create-user/).

   Utilizzando i comandi seguenti, il nuovo ruolo di replica può accedere al master da qualsiasi computer, non solo al computer che ospita il master stesso. Per questo accesso, specificare **syncuser\@'%'** nel comando per creare un utente.
   
   Per ulteriori informazioni sulla documentazione di MariaDB, vedere [Specifica dei nomi degli account](https://mariadb.com/kb/en/library/create-user/#account-names).

   **Comando SQL**

   - Replica con SSL

       Per richiedere SSL per tutte le connessioni utente, immettere il comando seguente per creare un utente:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - Replica senza SSL

       Se SSL non è necessario per tutte le connessioni, immettere il comando seguente per creare un utente:
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   Per creare il ruolo di replica in MySQL Workbench, nel riquadro **Gestione** selezionare **Utenti e privilegi**. Quindi selezionare **Aggiungi account**.
 
   ![Utenti e privilegi](./media/howto-data-in-replication/users_privileges.png)

   Immettere un nome utente nel campo **Nome di accesso.**

   ![Sincronizzazione utente](./media/howto-data-in-replication/syncuser.png)
 
   Selezionare il pannello **Ruoli amministrativi,** quindi nell'elenco **Dei privilegi globali**selezionare Slave della **replica**. Selezionare **Applica** per creare il ruolo di replica.

   ![Slave di replica](./media/howto-data-in-replication/replicationslave.png)


4. Impostare il server master sulla modalità di sola lettura.

   Prima di eseguire il dump di un database, il server deve essere messo in modalità di sola lettura. In modalità di sola lettura, il master non può elaborare transazioni di scrittura. Per evitare l'impatto aziendale, pianificare la finestra di sola lettura durante un orario non di punta.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Ottenere il nome e l'offset del file di registro binario corrente.

   Per determinare il nome e l'offset [`show master status`](https://mariadb.com/kb/en/library/show-master-status/)del file di registro binario corrente, eseguire il comando .
    
   ```sql
   show master status;
   ```
   I risultati dovrebbero essere simili alla tabella seguente:The results should be similar to the following table:
   
   ![Risultati stato master](./media/howto-data-in-replication/masterstatus.png)

   Prendere nota del nome del file binario, perché verrà utilizzato nei passaggi successivi.
   
6. Ottenere la posizione GTID (opzionale, necessaria per la replica con GTID).

   Eseguire la [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) funzione per ottenere la posizione GTID per il nome e l'offset del file binlog corrispondente.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>Eseguire il dump e il ripristino del server master

1. Eseguire il dump di tutti i database dal server master.

   Utilizzare mysqldump per eseguire il dump di tutti i database dal server master. Non è necessario eseguire il dump della libreria MySQL e della libreria di test.

    Per ulteriori informazioni, vedere [Dump e ripristino](howto-migrate-dump-restore.md).

2. Impostare il server master sulla modalità di lettura/scrittura.

   Dopo aver eseguito il dump del database, modificare il server MariaDB master in modalità di lettura/scrittura.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Ripristinare il file di dump nel nuovo server.

   Ripristinare il file di dump nel server creato nel servizio Database di Azure per MariaDB. Vedere [Dump & ripristino](howto-migrate-dump-restore.md) per informazioni su come ripristinare un file di dump in un server MariaDB.

   Se il file di dump è di grandi dimensioni, caricarlo in una macchina virtuale in Azure all'interno della stessa area del server di replica. Ripristinarlo nel database di Azure per il server MariaDB dalla macchina virtuale.

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>Collegare i server master e di replica per avviare la replica dei dati

1. Impostare il server master.

   Tutte le funzioni di replica dei dati in ingresso vengono eseguite tramite stored procedure. Per informazioni su tali procedure, vedere [Stored procedure per la replica dei dati in ingresso](reference-data-in-stored-procedures.md). Le stored procedure possono essere eseguite nella shell MySQL o MySQL Workbench.

   Per collegare due server e avviare la replica, accedere al server di replica di destinazione nel servizio di azure DB per MariaDB. Impostare quindi l'istanza esterna come `mysql.az_replication_change_master` server `mysql.az_replication_change_master_with_gtid` master usando la o la stored procedure nel server di database di Azure per MariaDB.Next, set the external instance as the master server by using the or stored procedure on the Azure DB for MariaDB server.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   o
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: nome host del server master
   - master_user: nome utente del server master
   - master_password: password per il server master
   - master_log_file: nome del file di log binario da `show master status` in esecuzione
   - master_log_pos: posizione del file di log binario da `show master status` in esecuzione
   - master_gtid_pos: posizione GTID dall'esecuzione`select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: contesto del certificato CA. Se non si utilizza SSL, passare una stringa vuota.
    
    
    : è consigliabile passare il parametro master_ssl_ca come variabile. Per altre informazioni, vedere gli esempi seguenti.

   **Esempi**

   - Replica con SSL

       Creare la `@cert` variabile eseguendo i comandi seguenti:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE'S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       La replica con SSL viene impostata tra un server master ospitato nel companya.com di dominio e un server di replica ospitato nel database di Azure per MariaDB. Sulla replica viene eseguita questa stored procedure.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - Replica senza SSL

       La replica senza SSL viene impostata tra un server master ospitato nel dominio companya.com e un server di replica ospitato nel database di Azure per MariaDB. Sulla replica viene eseguita questa stored procedure.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Avviare la replica.

   Chiamare `mysql.az_replication_start` la stored procedure per avviare la replica.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Controllare lo stato della replica.

   Chiamare [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) il comando sul server di replica per visualizzare lo stato della replica.
    
   ```sql
   show slave status;
   ```

   Se `Slave_IO_Running` `Slave_SQL_Running` e sono `yes`nello stato e `Seconds_Behind_Master` `0`il valore di è , la replica sta funzionando. `Seconds_Behind_Master` indica il ritardo della replica. Se il valore `0`non è , la replica elabora gli aggiornamenti.

4. Aggiornare le variabili server corrispondenti per rendere più sicura la replica dei dati (necessaria solo per la replica senza GTID).
    
    A causa di una limitazione della [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) replica [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) nativa in MariaDB, è necessario impostare e le variabili sulla replica senza lo scenario GTID.

    Controllare le variabili `sync_master_info` e `sync_relay_log_info` le variabili del server slave per assicurarsi che `1`la replica dei dati sia stabile e impostare le variabili su .
    
## <a name="other-stored-procedures"></a>Altre stored procedure

### <a name="stop-replication"></a>Arrestare la replica

Per arrestare la replica tra il server master e quello di replica, usare la stored procedure seguente:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>Rimuovere la relazione di replica

Per rimuovere la relazione tra il server master e il server di replica, utilizzare la stored procedure seguente:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>Ignorare l'errore di replica

Per ignorare un errore di replica e consentire la replica, utilizzare la stored procedure seguente:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Passaggi successivi
Leggere altre informazioni sulla [replica dei dati in ingresso](concepts-data-in-replication.md) per Database di Azure per MariaDB.
