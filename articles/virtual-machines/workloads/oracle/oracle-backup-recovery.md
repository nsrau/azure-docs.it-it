---
title: Eseguire backup e ripristino di un database Oracle Database 12c in una macchina virtuale Linux di Azure | Microsoft Docs
description: Informazioni su come eseguire rapidamente backup e ripristino di un database Oracle Database 12c nell'ambiente Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 5/17/2017
ms.author: rclaus
ms.openlocfilehash: 9a2293f13b90e9a4cb11b4169fad969dd622a9a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Eseguire backup e ripristino di un database Oracle Database 12c in una macchina virtuale Linux di Azure

È possibile usare l'interfaccia della riga di comando di Azure per creare e gestire risorse di Azure a un prompt dei comandi o per creare script. In questo articolo vengono usati script dell'interfaccia della riga di comando di Azure per distribuire un database Oracle Database 12c da un'immagine della raccolta di Azure Marketplace.

Prima di iniziare, assicurarsi che l'interfaccia della riga di comando di Azure sia installata. Per altre informazioni, consultare la [guida all'installazione dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Preparare l'ambiente

### <a name="step-1-prerequisites"></a>Passaggio 1: Prerequisiti

*   Per eseguire il processo di backup e ripristino, è necessario innanzitutto creare una macchina virtuale Linux con un'istanza installata di Oracle Database 12c. L'immagine di Marketplace usata per creare la macchina virtuale viene denominata *Oracle:Oracle-Database-Ee:12.1.0.2:latest*.

    Per informazioni su come creare un database Oracle, vedere [Creare un database Oracle Database](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create).


### <a name="step-2-connect-to-the-vm"></a>Passaggio 2: Eseguire la connessione alla VM

*   Per creare una sessione Secure Shell, SSH con la macchina virtuale, usare il comando seguente. Sostituire l'indirizzo IP e la combinazione di nome host con il valore `publicIpAddress` per la macchina virtuale.

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>Passaggio 3: Preparare il database

1.  Questo passaggio presuppone che si abbia un'istanza di Oracle, cdb1, in esecuzione in una macchina virtuale denominata *myVM*.

    Usare la radice del superuser *oracle* e inizializzare il listener:

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    Copyright (c) 1991, 2014, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Log messages written to /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))

    Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Start Date                23-MAR-2017 15:32:08
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Log File         /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening Endpoints Summary...
    (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))
    The listener supports no services
    The command completed successfully
    ```

2.  Assicurarsi che il database sia in modalità di log di archiviazione (facoltativo):

    ```bash
    $ sqlplus / as sysdba
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG

    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```
3.  Creare una tabella per testare il commit (facoltativo):

    ```bash
    SQL> alter session set "_ORACLE_SCRIPT"=true ;
    Session altered.
    SQL> create user scott identified by tiger;
    User created.
    SQL> grant create session to scott;
    Grant succeeded.
    SQL> grant create table to scott;
    Grant succeeded.
    SQL> alter user scott quota 100M on users;
    User altered.
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    Table created.
    SQL> insert into scott_Table VALUES(1,'Line 1');
    1 row created.
    SQL> commit;
    Commit complete.
    ```
4.  Verificare o modificare il percorso e le dimensioni del file di backup:

    ```bash
    $ sqlplus / as sysdba
    SQL> show parameter db_recovery
    NAME                                 TYPE        VALUE
    ------------------------------------ ----------- ------------------------------
    db_recovery_file_dest                string      /u01/app/oracle/fast_recovery_area
    db_recovery_file_dest_size           big integer 4560M
    ```
5. Usare Oracle Recovery Manager (RMAN) per il backup del database:

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>Passaggio 4: Backup coerente con l'applicazione per le macchine virtuali Linux

I backup coerenti con l'applicazione sono una nuova funzionalità di Backup di Azure. È possibile creare e selezionare gli script da eseguire prima e dopo lo snapshot della macchina virtuale, ovvero pre-snapshot e post-snapshot.

1. Scaricare il file JSON.

    Scaricare VMSnapshotScriptPluginConfig.json da https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig. Il contenuto del file dovrebbe essere simile a quello riportato di seguito:

    ```azurecli
    {
        "pluginName" : "ScriptRunner",
        "preScriptLocation" : "",
        "postScriptLocation" : "",
        "preScriptParams" : ["", ""],
        "postScriptParams" : ["", ""],
        "preScriptNoOfRetries" : 0,
        "postScriptNoOfRetries" : 0,
        "timeoutInSeconds" : 30,
        "continueBackupOnFailure" : true,
        "fsFreezeEnabled" : true
    }
    ```

2. Creare la cartella /etc/azure nella macchina virtuale:

    ```bash
    $ sudo su -
    # mkdir /etc/azure
    # cd /etc/azure
    ```

3. Copiare il file JSON.

    Copiare il file VMSnapshotScriptPluginConfig.json nella cartella /etc/azure.

4. Modificare il file JSON.

    Modificare il file VMSnapshotScriptPluginConfig.json per includere i parametri `PreScriptLocation` e `PostScriptlocation`. ad esempio:

    ```azurecli
    {
        "pluginName" : "ScriptRunner",
        "preScriptLocation" : "/etc/azure/pre_script.sh",
        "postScriptLocation" : "/etc/azure/post_script.sh",
        "preScriptParams" : ["", ""],
        "postScriptParams" : ["", ""],
        "preScriptNoOfRetries" : 0,
        "postScriptNoOfRetries" : 0,
        "timeoutInSeconds" : 30,
        "continueBackupOnFailure" : true,
        "fsFreezeEnabled" : true
    }
    ```

5. Creare file di script di pre-snapshot e post-snapshot.

    Ecco un esempio di script di pre-snapshot e post-snapshot per un "backup a freddo", un backup offline, con arresto e riavvio:

    Per /etc/azure/pre_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" > /etc/azure/pre_script_$v_date.log
    ```

    Per /etc/azure/post_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" > /etc/azure/post_script_$v_date.log
    ```

    Ecco un esempio di script di pre-snapshot e post-snapshot per un "backup a caldo", un backup online:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/pre_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    Per /etc/azure/post_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/post_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    Per /etc/azure/pre_script.sql, è necessario modificare il contenuto del file in base ai requisiti:

    ```bash
    alter tablespace SYSTEM begin backup;
    ...
    ...
    alter system switch logfile;
    alter system archive log stop;
    ```

    Per /etc/azure/post_script.sql, è necessario modificare il contenuto del file in base ai requisiti:

    ```bash
    alter tablespace SYSTEM end backup;
    ...
    ...
    alter system archive log start;
    ```

6. Modificare le autorizzazioni del file:

    ```bash
    # chmod 600 /etc/azure/VMSnapshotScriptPluginConfig.json
    # chmod 700 /etc/azure/pre_script.sh
    # chmod 700 /etc/azure/post_script.sh
    ```

7. Testare gli script.

    Per testare gli script accedere innanzitutto come radice. Assicurarsi quindi che non ci siano errori:

    ```bash
    # /etc/azure/pre_script.sh
    # /etc/azure/post_script.sh
    ```

Per altre informazioni, vedere [Backup coerente delle applicazioni per le macchine virtuali Linux](https://azure.microsoft.com/en-us/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/).


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>Passaggio 5: Usare gli insiemi di credenziali dei servizi di ripristino di Azure per eseguire il backup della VM

1.  Nel portale di Azure cercare gli **insiemi di credenziali di Servizi di ripristino**.

    ![Pagina degli insiemi di credenziali dei servizi di ripristino](./media/oracle-backup-recovery/recovery_service_01.png)

2.  Nel pannello **Insiemi di credenziali dei servizi di ripristino** fare clic su **Aggiungi** per aggiungere un nuovo insieme di credenziali.

    ![Pagina di aggiunta degli insiemi di credenziali dei servizi di ripristino](./media/oracle-backup-recovery/recovery_service_02.png)

3.  Per continuare, fare clic su **myVault**.

    ![Pagina di dettaglio degli insiemi di credenziali dei servizi di ripristino](./media/oracle-backup-recovery/recovery_service_03.png)

4.  Nel pannello **myVault** fare clic su **Backup**.

    ![Pagina di backup degli insiemi di credenziali dei servizi di ripristino](./media/oracle-backup-recovery/recovery_service_04.png)

5.  Nel pannello **Obiettivo del backup** usare i valori predefiniti **Azure** e **Macchina virtuale**. Fare clic su **OK**.

    ![Pagina di dettaglio degli insiemi di credenziali dei servizi di ripristino](./media/oracle-backup-recovery/recovery_service_05.png)

6.  Per **Criterio di backup** usare **DefaultPolicy** o selezionare **Crea un nuovo criterio**. Fare clic su **OK**.

    ![Pagina di dettaglio dei criteri di backup degli insiemi di credenziali dei servizi di ripristino](./media/oracle-backup-recovery/recovery_service_06.png)

7.  Nel pannello **Seleziona macchine virtuali** selezionare la casella di controllo **myVM1** e quindi fare clic su **OK**. Fare clic sul pulsante **Abilita backup**.

    ![Pagina di dettaglio degli elementi per il backup degli insiemi di credenziali dei servizi di ripristino](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > Dopo aver fatto clic su **Abilita backup**, il processo di backup non viene avviato fino all'orario pianificato. Per configurare un backup immediato, completare il passaggio successivo.

8.  Nel pannello **myVault - Elementi di backup** in **CONTEGGIO DEGLI ELEMENTI DI BACKUP** selezionare il numero di backup degli elementi.

    ![Pagina di dettaglio degli insiemi di credenziali myVault dei servizi di ripristino](./media/oracle-backup-recovery/recovery_service_08.png)

9.  Nel pannello **Backup Items (Azure Virtual Machine)** (Elementi di backup: macchina virtuale di Azure), sul lato destro della pagina, fare clic sui puntini di sospensione (**...**) e su **Backup now** (Esegui il backup ora).

    ![Comando Backup now (Esegui il backup ora) degli insiemi di credenziali dei servizi di ripristino](./media/oracle-backup-recovery/recovery_service_09.png)

10. Fare clic sul pulsante **Backup**. Attendere il completamento del processo di backup. Quindi passare al [passaggio 6: Rimuovere i file di database](#step-6-remove-the-database-files).

    Per visualizzare lo stato del processo di backup, fare clic su **Processi**.

    ![Pagina dei processi degli insiemi di credenziali dei servizi di ripristino](./media/oracle-backup-recovery/recovery_service_10.png)

    Lo stato del processo di backup viene visualizzato nella figura seguente:

    ![Pagina dei processi degli insiemi di credenziali dei servizi di ripristino con stato](./media/oracle-backup-recovery/recovery_service_11.png)

11. Per un backup coerente con l'applicazione, risolvere gli eventuali errori nel file di log. Il file di log si trova in /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0.

### <a name="step-6-remove-the-database-files"></a>Passaggio 6: Rimuovere i file di database 
Più avanti in questo articolo verrà illustrato come testare il processo di ripristino. Prima di testare il processo di ripristino, è necessario rimuovere i file di database.

1.  Rimuovere lo spazio di tabella e i file di backup:

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  Arrestare l'istanza di Oracle (facoltativo):

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-the-recovery-services-vaults"></a>Ripristinare i file eliminati dagli insiemi di credenziali dei servizi di ripristino
Per ripristinare i file eliminati, completare la procedura seguente:

1. Nel portale di Azure e cercare l'elemento degli insiemi di credenziali dei servizi di ripristino *myVault*. Nel pannello **Panoramica** in **Elementi di backup** selezionare il numero di elementi.

    ![Elementi di backup myVault degli insiemi di credenziali dei servizi di ripristino](./media/oracle-backup-recovery/recovery_service_12.png)

2. In **CONTEGGIO DEGLI ELEMENTI DI BACKUP** fare clic sul numero di elementi.

    ![Numero di elementi di backup di macchine virtuali di Azure in insiemi di credenziali di servizi di ripristino](./media/oracle-backup-recovery/recovery_service_13.png)

3. Nel pannello **myvm1** fare clic su **Ripristino di file (anteprima)**.

    ![Schermata della pagina di ripristino dei file degli insiemi di credenziali dei servizi di ripristino](./media/oracle-backup-recovery/recovery_service_14.png)

4. Nel riquadro **Ripristino di file (anteprima)** fare clic su **Scarica script**. Quindi, salvare il file di download con estensione SH in una cartella nel computer client.

    ![Opzioni di salvataggio e download del file di script](./media/oracle-backup-recovery/recovery_service_15.png)

5. Copiare il file con estensione sh nella VM.

    Nell'esempio seguente viene illustrato come usare un comando di copia di sicurezza (scp) per spostare il file nella macchina virtuale. È possibile anche copiare il contenuto negli Appunti e incollarlo in un nuovo file configurato nella macchina virtuale.

    > [!IMPORTANT]
    > Nell'esempio seguente assicurarsi di aggiornare i valori dell'indirizzo IP e della cartella. I valori devono corrispondere a quelli della cartella in cui è stato salvato il file.

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```
6. Modificare il file in modo che appartenga alla radice.

    Nell'esempio seguente modificare il file in modo che appartenga a una radice. Modificare quindi le autorizzazioni.

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```
    L'esempio seguente illustra ciò che dovrebbe venire visualizzato dopo aver eseguito lo script precedente. Quando viene chiesto di continuare, immettere **Y**.

    ```bash
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    The script requires 'open-iscsi' and 'lshw' to run.
    Do you want us to install 'open-iscsi' and 'lshw' on this machine?
    Please press 'Y' to continue with installation, 'N' to abort the operation. : Y
    Installing 'open-iscsi'....
    Installing 'lshw'....

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sde  |  /dev/sde1  |  /root/myVM-20170517093913/Volume1

    2)  | /dev/sde  |  /dev/sde2  |  /root/myVM-20170517093913/Volume2

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

7. L'accesso ai volumi montati è stato confermato.

    Per uscire, immettere **q** e cercare i volumi montati. Per creare un elenco dei volumi aggiunti, a un prompt dei comandi, immettere **df -k**.

    ![Comando df-k](./media/oracle-backup-recovery/recovery_service_16.png)

8. Usare lo script seguente per copiare nuovamente i file mancanti nelle cartelle:

    ```bash
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cp *.bkp /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cd /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # chown oracle:oinstall *.bkp
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/oradata/cdb1
    # cp *.dbf /u01/app/oracle/oradata/cdb1
    # cd /u01/app/oracle/oradata/cdb1
    # chown oracle:oinstall *.dbf
    ```
9. Nello script seguente usare RMAN per ripristinare il database:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```
    
10. Smontare il disco.

    Nel portale di Azure, nel pannello **Ripristino file (anteprima)** fare clic su **Unmount Disks** (Smonta dischi).

    ![Comando Unmount disks](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>Ripristinare l'intera VM

Anziché ripristinare i file eliminati dagli insiemi di credenziali dei servizi di ripristino, è possibile ripristinare l'intera macchina virtuale.

### <a name="step-1-delete-myvm"></a>Passaggio 1: Eliminare myVM

*   Nel portale di Azure passare all'insieme di credenziali **myVM1** e selezionare **Elimina**.

    ![Comando Elimina insieme di credenziali](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>Passaggio 2: Ripristinare la VM

1.  Passare a **Insiemi di credenziali dei servizi di ripristino** e selezionare **myVault**.

    ![voce myVault](./media/oracle-backup-recovery/recover_vm_02.png)

2.  Nel pannello **Panoramica** in **Elementi di backup** selezionare il numero di elementi.

    ![elementi di backup myVault](./media/oracle-backup-recovery/recover_vm_03.png)

3.  Nel pannello **Backup Items (Azure Virtual Machine)** (Elementi di backup: macchina virtuale di Azure) selezionare **myvm1**.

    ![Pagina di ripristino della macchina virtuale](./media/oracle-backup-recovery/recover_vm_04.png)

4.  Nel pannello **myvm1** fare clic sui puntini di sospensione (**...**) e su **Ripristina macchina virtuale**.

    ![Comando Ripristina macchina virtuale](./media/oracle-backup-recovery/recover_vm_05.png)

5.  Nel pannello **Seleziona punto di ripristino** selezionare l'elemento che si vuole ripristinare e fare clic su **OK**.

    ![Selezionare il punto di ripristino](./media/oracle-backup-recovery/recover_vm_06.png)

    Se è stato abilitato il backup coerente con l'applicazione, viene visualizzata una barra verticale di colore blu.

6.  Nel pannello **Configurazione di ripristino** selezionare il nome della macchina virtuale, il gruppo di risorse e fare clic su **OK**.

    ![Valori di configurazione del ripristino](./media/oracle-backup-recovery/recover_vm_07.png)

7.  Per ripristinare la macchina virtuale, fare clic sul pulsante **Ripristina**.

8.  Per visualizzare lo stato del processo di ripristino, fare clic su **Processi** e quindi su **Processi di backup**.

    ![Comando dello stato dei processi di backup](./media/oracle-backup-recovery/recover_vm_08.png)

    L'immagine seguente mostra lo stato del processo di ripristino:

    ![Stato del processo di ripristino](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>Passaggio 3: Impostare l'indirizzo IP pubblico
Dopo il ripristino della macchina virtuale, configurare l'indirizzo IP pubblico.

1.  Nella casella di ricerca inserire l'**indirizzo IP pubblico**.

    ![Elenco degli indirizzi IP pubblici](./media/oracle-backup-recovery/create_ip_00.png)

2.  Nel pannello**Indirizzi IP pubblici** fare clic su **Aggiungi**. Nel pannello **Crea indirizzo IP pubblico** per **Nome** selezionare il nome dell'indirizzo IP pubblico. Per **Gruppo di risorse** selezionare **Usa esistente**. Fare quindi clic su **Crea**.

    ![Creare un indirizzo IP](./media/oracle-backup-recovery/create_ip_01.png)

3.  Per associare l'indirizzo IP pubblico all'interfaccia di rete per la macchina virtuale, cercare e selezionare **myVMip**. Quindi fare clic su **Associa**.

    ![Associare l'indirizzo IP](./media/oracle-backup-recovery/create_ip_02.png)

4.  Per **Tipo di risorsa** selezionare **Interfaccia di rete**. Selezionare l'interfaccia di rete usata dall'istanza di myVM e fare clic su **OK**.

    ![Selezionare i valori Tipo di risorsa e Interfaccia di rete](./media/oracle-backup-recovery/create_ip_03.png)

5.  Cercare e aprire l'istanza di myVM che viene trasferita dal portale. L'indirizzo IP associato alla macchina virtuale viene visualizzato nel pannello **Panoramica** di myVM.

    ![Valore dell'indirizzo IP](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>Passaggio 4: Eseguire la connessione alla VM

*   Per eseguire la connessione alla macchina virtuale, usare lo script seguente:

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>Passaggio 5: Verificare se il database è accessibile
*   Per testare l'accessibilità, usare lo script seguente:

    ```bash 
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

    > [!IMPORTANT]
    > Se il comando **startup** del database genera un errore, per ripristinare il database, vedere [Passaggio 6: Usare RMAN per ripristinare il database (facoltativo)](#step-6-optional-use-rman-to-recover-the-database).

### <a name="step-6-optional-use-rman-to-recover-the-database"></a>Passaggio 6: Usare RMAN per ripristinare il database (facoltativo)
*   Usare lo script seguente per ripristinare il database:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

L'esecuzione di backup e ripristino del database Oracle Database 12c su una macchina virtuale Linux di Azure è stata completata.

## <a name="delete-the-vm"></a>Eliminare la VM

Quando la macchina virtuale non è più necessaria, è possibile usare il comando seguente per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione: Creare VM a disponibilità elevata](../../linux/create-cli-complete.md)

[Esplorare gli esempi dell'interfaccia della riga di comando di Azure per la distribuzione della VM](../../linux/cli-samples.md)



