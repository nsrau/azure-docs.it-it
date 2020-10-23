---
title: Configurare la replica dei dati-nel database di Azure per MariaDB
description: Questo articolo descrive come configurare Replica dei dati in ingresso nel database di Azure per MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 9/29/2020
ms.openlocfilehash: 21a0aaaa9e10a7c3e445145eb178b50b446ba6ae
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92425997"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Configurare Replica dei dati in ingresso nel database di Azure per MariaDB

Questo articolo descrive come configurare [replica dei dati in ingresso](concepts-data-in-replication.md) nel database di Azure per MariaDB configurando i server di origine e di replica. Questo articolo presuppone che l'utente disponga di un'esperienza precedente con i server e i database di MariaDB.

Per creare una replica nel database di Azure per il servizio MariaDB, [replica dei dati in ingresso](concepts-data-in-replication.md) sincronizza i dati da un server MariaDB di origine in locale, in macchine virtuali (VM) o in servizi di database cloud. La replica dei dati in ingresso si basa sulla replica nativa di MariaDB in base alla posizione di file di log binari (binlog). Per altre informazioni su questo tipo di replica, vedere [binlog replication overview](https://mariadb.com/kb/en/library/replication-overview/) (Panoramica della replica basata su binlog).

Esaminare le [limitazioni e i requisiti](concepts-data-in-replication.md#limitations-and-considerations) di replica dei dati prima di eseguire i passaggi descritti in questo articolo.

> [!NOTE]
> Se la versione del server di origine è 10,2 o successiva, è consigliabile configurare Replica dei dati in ingresso usando l' [ID transazione globale](https://mariadb.com/kb/en/library/gtid/).


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Creare un server MariaDB da usare come replica

1. Creare un nuovo database di Azure per il server MariaDB, ad esempio replica.mariadb.database.azure.com. Il server è il server di replica in Replica dei dati in ingresso.

    Per informazioni sulla creazione del server, vedere [creare un database di Azure per il server MariaDB usando il portale di Azure](quickstart-create-mariadb-server-database-using-azure-portal.md).

   > [!IMPORTANT]
   > È necessario creare il database di Azure per il server MariaDB nei piani tariffari per utilizzo generico o con ottimizzazione per la memoria.

2. Creare account utente identici e privilegi corrispondenti.
    
    Gli account utente non vengono replicati dal server di origine al server di replica. Per fornire l'accesso utente al server di replica, è necessario creare manualmente tutti gli account e i privilegi corrispondenti nel database di Azure per il server MariaDB appena creato.

3. Aggiungere l'indirizzo IP del server di origine alle regole del firewall della replica. 

   Aggiornare le regole firewall usando il [portale di Azure](howto-manage-firewall-portal.md) o l'[interfaccia della riga di comando di Azure](howto-manage-firewall-cli.md).

> [!NOTE]
> Comunicazione senza distorsione
>
> Microsoft supporta un ambiente diversificato ed inclusivo. Questo articolo contiene riferimenti alla parola _slave_. La [guida di stile Microsoft per la comunicazione senza pregiudizi](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) la riconosce come parola di esclusione. La parola viene usata in questo articolo per coerenza perché è attualmente la parola usata nel software. Quando il software verrà aggiornato per rimuovere la parola, questo articolo verrà aggiornato di conseguenza.
>

## <a name="configure-the-source-server"></a>Configurare il server di origine

I passaggi seguenti preparano e configurano il server MariaDB ospitato in locale, in una macchina virtuale o in un servizio di database cloud per Replica dei dati in ingresso. Il server MariaDB è il codice sorgente in Replica dei dati in ingresso.

1. Prima di procedere, esaminare i [requisiti del server master](concepts-data-in-replication.md#requirements) . 

2. Verificare che il server di origine consenta il traffico in ingresso e in uscita sulla porta 3306 e che il server di origine disponga di un **indirizzo IP pubblico**, che il DNS sia accessibile pubblicamente o disponga di un nome di dominio completo (FQDN). 
   
   Testare la connettività al server di origine provando a connettersi da uno strumento come la riga di comando MySQL ospitata in un altro computer o dalla [Azure cloud Shell](../cloud-shell/overview.md) disponibile nel portale di Azure.

   Se l'organizzazione dispone di rigidi criteri di sicurezza e non consente a tutti gli indirizzi IP nel server di origine di abilitare la comunicazione da Azure al server di origine, è possibile usare il comando seguente per determinare l'indirizzo IP del database di Azure per il server MariaDB.
    
   1. Accedere al database di Azure per MariaDB usando uno strumento come la riga di comando di MySQL.
   2. Eseguire la query seguente.
      ```bash
      mysql> SELECT @@global.redirect_server_host;
      ```
      Di seguito è riportato un output di esempio:
      ```bash 
      +-----------------------------------------------------------+
      | @@global.redirect_server_host                             |
      +-----------------------------------------------------------+
      | e299ae56f000.tr1830.westus1-a.worker.database.windows.net |
       +-----------------------------------------------------------+
      ```
   3. Uscire dalla riga di comando di MySQL.
   4. Eseguire il comando seguente nell'utilità ping per ottenere l'indirizzo IP.
      ```bash
      ping <output of step 2b>
      ``` 
      Ad esempio: 
      ```bash      
      C:\Users\testuser> ping e299ae56f000.tr1830.westus1-a.worker.database.windows.net
      Pinging tr1830.westus1-a.worker.database.windows.net (**11.11.111.111**) 56(84) bytes of data.
      ```

   5. Configurare le regole del firewall del server di origine per includere l'indirizzo IP dell'output del passaggio precedente sulla porta 3306.

   > [!NOTE]
   > Questo indirizzo IP può cambiare a causa di operazioni di manutenzione/distribuzione. Questo metodo di connettività è solo per i clienti che non possono permettersi di consentire tutti gli indirizzi IP sulla porta 3306.

2. Attivare la registrazione binaria.
    
    Per verificare se nel database master è abilitata la registrazione binaria, immettere il comando seguente:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Se la variabile [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) restituisce il valore `ON` , la registrazione binaria è abilitata nel server.

   Se `log_bin` restituisce il valore `OFF` , modificare il file **My. cnf** in modo che `log_bin=ON` accenda la registrazione binaria. Riavviare il server per rendere effettiva la modifica.

3. Configurare le impostazioni del server di origine.

    Replica dei dati in ingresso richiede che il parametro `lower_case_table_names` sia coerente tra i server di origine e di replica. Il `lower_case_table_names` parametro è impostato su per `1` impostazione predefinita nel database di Azure per MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

4. Creare un nuovo ruolo di replica e impostare le autorizzazioni.

   Creare un account utente nel server di origine configurato con privilegi di replica. È possibile creare un account usando i comandi SQL o MySQL Workbench. Se si prevede di eseguire la replica con SSL, è necessario specificarlo quando si crea l'account utente.
   
   Per informazioni su come aggiungere gli account utente nel server di origine, vedere la [documentazione di MariaDB](https://mariadb.com/kb/en/library/create-user/).

   Usando i comandi seguenti, il nuovo ruolo di replica può accedere all'origine da qualsiasi computer, non solo il computer che ospita l'origine stessa. Per questo accesso, specificare **syncuser \@ '%'** nel comando per creare un utente.
   
   Per ulteriori informazioni sulla documentazione di MariaDB, vedere [specifica dei nomi degli account](https://mariadb.com/kb/en/library/create-user/#account-names).

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

   Per creare il ruolo di replica in MySQL Workbench, nel riquadro **gestione** selezionare **utenti e privilegi**. Quindi selezionare **Aggiungi account**.
 
   ![Utenti e privilegi](./media/howto-data-in-replication/users_privileges.png)

   Immettere un nome utente nel campo **nome account di accesso** .

   ![Sincronizzazione utente](./media/howto-data-in-replication/syncuser.png)
 
   Selezionare il pannello **ruoli amministrativi** , quindi nell'elenco dei **privilegi globali**selezionare **slave di replica**. Selezionare **applica** per creare il ruolo di replica.

   ![Slave di replica](./media/howto-data-in-replication/replicationslave.png)


5. Impostare il server di origine sulla modalità di sola lettura.

   Prima di eseguire il dump di un database, il server deve essere inserito in modalità di sola lettura. In modalità di sola lettura, l'origine non è in grado di elaborare transazioni di scrittura. Per evitare l'effetto aziendale, pianificare la finestra di sola lettura durante un'ora non di punta.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

6. Ottiene il nome e l'offset del file di log binario corrente.

   Per determinare il nome e l'offset del file di log binario corrente, eseguire il comando [`show master status`](https://mariadb.com/kb/en/library/show-master-status/) .
    
   ```sql
   show master status;
   ```
   I risultati dovrebbero essere simili a quelli della tabella seguente:
   
   ![Risultati stato master](./media/howto-data-in-replication/masterstatus.png)

   Si noti il nome del file binario, perché verrà usato nei passaggi successivi.
   
7. Ottenere la posizione GTID (facoltativa, necessaria per la replica con GTID).

   Eseguire la funzione [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) per ottenere la posizione GTID per il nome e l'offset del file binlog corrispondente.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-source-server"></a>Dump e ripristino del server di origine

1. Dump di tutti i database dal server di origine.

   Utilizzare mysqldump per eseguire il dump di tutti i database dal server di origine. Non è necessario eseguire il dump della libreria MySQL e della libreria di test.

    Per ulteriori informazioni, vedere [dump e ripristino](howto-migrate-dump-restore.md).

2. Impostare il server di origine sulla modalità di lettura/scrittura.

   Al termine del dump del database, riportare il server di MariaDB di origine in modalità di lettura/scrittura.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Ripristinare il file dump nel nuovo server.

   Ripristinare il file di dump nel server creato nel servizio Database di Azure per MariaDB. Vedere [Dump & Restore](howto-migrate-dump-restore.md) per informazioni su come ripristinare un file dump in un server MariaDB.

   Se il file di dump è di grandi dimensioni, caricarlo in una macchina virtuale di Azure all'interno della stessa area del server di replica. Ripristinarlo nel database di Azure per il server MariaDB dalla macchina virtuale.

## <a name="link-the-source-and-replica-servers-to-start-data-in-replication"></a>Collegare i server di origine e di replica per avviare Replica dei dati in ingresso

1. Impostare il server di origine.

   Tutte le funzioni di replica dei dati in ingresso vengono eseguite tramite stored procedure. Per informazioni su tali procedure, vedere [Stored procedure per la replica dei dati in ingresso](reference-stored-procedures.md). Le stored procedure possono essere eseguite in MySQL Shell o MySQL Workbench.

   Per collegare due server e avviare la replica, accedere al server di replica di destinazione nel database di Azure per il servizio MariaDB. Impostare quindi l'istanza esterna come server di origine usando il `mysql.az_replication_change_master` `mysql.az_replication_change_master_with_gtid` stored procedure o nel database di Azure per il server MariaDB.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   oppure
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: nome host del server di origine
   - master_user: nome utente per il server di origine
   - master_password: password per il server di origine
   - master_log_file: nome del file di log binario da `show master status` in esecuzione
   - master_log_pos: posizione del file di log binario da `show master status` in esecuzione
   - master_gtid_pos: la posizione GTID non è in esecuzione `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: contesto del certificato della CA. Se non si usa SSL, passare una stringa vuota. *
    
    
    * È consigliabile passare il parametro master_ssl_ca come variabile. Per altre informazioni, vedere gli esempi seguenti.

   **esempi**

   - Replica con SSL

       Creare la variabile eseguendo `@cert` i comandi seguenti:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE\'S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       La replica con SSL è configurata tra un server di origine ospitato nel dominio companya.com e un server di replica ospitato nel database di Azure per MariaDB. Sulla replica viene eseguita questa stored procedure.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - Replica senza SSL

       La replica senza SSL è configurata tra un server di origine ospitato nel dominio companya.com e un server di replica ospitato nel database di Azure per MariaDB. Sulla replica viene eseguita questa stored procedure.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Avviare la replica.

   Chiamare il `mysql.az_replication_start` stored procedure per avviare la replica.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Controllare lo stato della replica.

   Chiamare il [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) comando sul server di replica per visualizzare lo stato della replica.
    
   ```sql
   show slave status;
   ```

   Se `Slave_IO_Running` e `Slave_SQL_Running` sono nello stato `yes` e il valore di `Seconds_Behind_Master` è, la `0` replica funziona. `Seconds_Behind_Master` indica il ritardo della replica. Se il valore non è `0` , la replica sta elaborando gli aggiornamenti.

4. Aggiornare le variabili server corrispondenti per rendere più sicura la replica dei dati (necessaria solo per la replica senza GTID).
    
    A causa di una limitazione della replica nativa in MariaDB, è necessario impostare  [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) le variabili e nella replica senza lo scenario GTID.

    Controllare le variabili e del server slave `sync_master_info` `sync_relay_log_info` per assicurarsi che la replica dei dati sia stabile e impostare le variabili su `1` .
    
## <a name="other-stored-procedures"></a>Altre stored procedure

### <a name="stop-replication"></a>Arrestare la replica

Per arrestare la replica tra il server di origine e quello di replica, usare i stored procedure seguenti:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>Rimuovere la relazione di replica

Per rimuovere la relazione tra il server di origine e quello di replica, usare la stored procedure seguente:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>Ignora l'errore di replica

Per ignorare un errore di replica e consentire la replica, usare i stored procedure seguenti:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Passaggi successivi
Leggere altre informazioni sulla [replica dei dati in ingresso](concepts-data-in-replication.md) per Database di Azure per MariaDB.
