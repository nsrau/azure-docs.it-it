---
title: Configurare la replica dei dati in ingresso in Database di Azure per MySQL.
description: Questo articolo descrive come configurare la replica dei dati in ingresso in Database di Azure per MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 08/31/2018
ms.openlocfilehash: 7d56d7f8fcbd53d4f69863d260591ef80f3d7188
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459080"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Come configurare la replica dei dati in ingresso in Database di Azure per MySQL

In questo articolo si apprenderà come configurare la Replica dei dati in ingresso nel servizio Database di Azure per MySQL eseguendo la configurazione del server master e di un server di replica. La funzione Replica dei dati in ingresso consente di sincronizzare i dati da un server MySQL master, eseguito in locale, in macchine virtuali o servizi di database ospitati da altri provider cloud in una replica nel servizio Database di Azure per MySQL. 

Per eseguire le procedure descritte in questo articolo è necessario avere già un certo livello di esperienza con i server e i database MySQL.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Creare un server MySQL da usare come replica

1. Creare un server di Database di Azure per MySQL

   Creare un server MySQL, ad esempio "replica.mysql.database.azure.com". Per istruzioni su come creare il server, vedere [Creare un server di Database di Azure per MySQL usando il portale di Azure](quickstart-create-mysql-server-database-using-azure-portal.md). Questo è il server di "replica" per la replica dei dati in ingresso.

   > [!IMPORTANT]
   > Il server di Database di Azure per MySQL deve essere creato con il piano tariffario per utilizzo generico o ottimizzato per la memoria.
   > 

2. Creare gli stessi account utente e i privilegi corrispondenti

   Gli account utente non vengono replicati dal server master al server di replica. Se si prevede di specificare gli utenti con accesso al server di replica, è necessario creare manualmente tutti gli account e i privilegi corrispondenti sul nuovo server di Database di Azure per MySQL.

## <a name="configure-the-master-server"></a>Configurare il server master
I passaggi seguenti consentono di preparare e configurare il server MySQL ospitato in locale, in una macchina virtuale o un servizio di database ospitato da altri provider di cloud per la replica dei dati in ingresso. Questo è il server "master" per la replica dei dati in ingresso. 

1. Attivare la registrazione binaria

   Verificare se la registrazione binaria è stata abilitata sul server master eseguendo questo comando: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Se la variabile [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) viene restituita con il valore "ON", la registrazione binaria è abilitata sul server. 

   Se `log_bin` viene restituito con il valore "OFF", abilitare la registrazione binaria modificando il file my.cnf con `log_bin=ON` e riavviare il server per rendere effettive le modifiche.

2. Impostazioni del server master

   Per la Replica dei dati in ingresso è necessario che il parametro `lower_case_table_names` sia coerente tra il server master e quello di replica. Per impostazione predefinita, in Database di Azure per MySQL questo parametro è impostato su 1. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Creare un nuovo ruolo di replica e configurare le autorizzazioni

   Sul server master creare un account utente configurato con i privilegi di replica. Questa operazione può essere eseguita tramite comandi SQL o uno strumento come MySQL Workbench. Valutare se si prevede di eseguire la replica con SSL, poiché è necessario specificare questa impostazione quando si crea l'utente. Per istruzioni su come [aggiungere account utente](https://dev.mysql.com/doc/refman/5.7/en/adding-users.html) sul server master, vedere la documentazione di MySQL. 

   Nei comandi seguenti il nuovo ruolo di replica creato è in grado di accedere al server master da qualsiasi computer, non solo dal computer che ospita il server master stesso. Questa operazione viene eseguita specificando "syncuser@'%'" nel comando per la creazione dell'utente. Per altre informazioni su come [specificare i nomi degli account](https://dev.mysql.com/doc/refman/5.7/en/account-names.html), vedere la documentazione di MySQL.

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

   Eseguire il comando [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) per determinare l'offset e il nome del file di log binario corrente.
    
   ```sql
   show master status;
   ```
   Si otterranno risultati simili ai seguenti. Prendere nota del nome del file binario poiché sarà necessario specificarlo nei passaggi successivi.

   ![Risultati stato master](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-master-server"></a>Eseguire il dump e il ripristino del server master

1. Eseguire il dump di tutti i database dal server master

   Per eseguire il dump dei database dal server master è possibile usare mysqldump. Per informazioni dettagliate, vedere [Dump e ripristino](concepts-migrate-dump-restore.md). Non è necessario eseguire il dump della libreria MySQL e della libreria di test.

2. Impostare il server master in modalità di lettura/scrittura

   Dopo avere eseguito il dump del database, ripristinare la modalità di lettura/scrittura sul server MySQL master.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Ripristinare il file di dump nel nuovo server

   Ripristinare il file di dump nel server creato nel servizio Database di Azure per MySQL. Per informazioni su come ripristinare un file di dump in un server MySQL, vedere [Dump e ripristino](concepts-migrate-dump-restore.md). Se il file di dump ha grandi dimensioni, caricarlo in una macchina virtuale in Azure nella stessa area del server di replica. Ripristinare quindi il file nel server di Database di Azure per MySQL dalla macchina virtuale.

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>Collegare il server master e quello di replica per avviare la Replica dei dati in ingresso

1. Impostare il server master

   Tutte le funzioni di replica dei dati in ingresso vengono eseguite tramite stored procedure. Per informazioni su tali procedure, vedere [Stored procedure per la replica dei dati in ingresso](reference-data-in-stored-procedures.md). Le stored procedure possono essere eseguite nella shell di MySQL o in MySQL Workbench. 

   Per collegare due server e avviare la replica, accedere al server di replica di destinazione nel servizio Database di Azure per MySQL e impostare l'istanza esterna come server master. Questa operazione viene eseguita tramite la stored procedure `mysql.az_replication_change_master` nel server di Database di Azure per MySQL.

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

> [!NOTE]
> Se il server master è ospitato in una macchina virtuale di Azure, attivare l'opzione che consente di accedere ai servizi di Azure in modo che il server master e i server di replica possano comunicare tra loro. Questa impostazione può essere modificata dalle opzioni di **sicurezza delle connessioni**. Per altre informazioni, vedere l'articolo sulla [gestione delle regole del firewall con il portale](howto-manage-firewall-using-portal.md).

   **esempi**

   *Replica con SSL*

   La variabile `@cert` viene creata eseguendo i comandi MySQL seguenti: 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   La replica con SSL viene configurata tra un server master ospitato nel dominio "companya.com" e un server di replica ospitato in Database di Azure per MySQL. Sulla replica viene eseguita questa stored procedure. 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
   ```
   *Replica senza SSL*

   La replica senza SSL viene configurata tra un server master ospitato nel dominio "companya.com" e un server di replica ospitato in Database di Azure per MySQL. Sulla replica viene eseguita questa stored procedure.

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
   ```

1. Avviare la replica

   Chiamare la stored procedure `mysql.az_replication_start` per avviare la replica.

   ```sql
   CALL mysql.az_replication_start;
   ```

1. Verificare lo stato della replica

   Chiamare il comando [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) sul server di replica per visualizzare lo stato della replica.
    
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
- Altre informazioni sulla [replica dei dati in ingresso](concepts-data-in-replication.md) in Database di Azure per MySQL. 
