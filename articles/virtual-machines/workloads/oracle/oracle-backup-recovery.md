---
title: Eseguire backup e ripristino di un database Oracle Database 12c in una macchina virtuale Linux di Azure | Microsoft Docs
description: Informazioni su come eseguire rapidamente backup e ripristino di un database Oracle Database 12c nell&quot;ambiente Azure.
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
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 7a53ca814170a6651fc499c41395fe088a6f869a
ms.contentlocale: it-it
ms.lasthandoff: 06/15/2017

---

# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Eseguire backup e ripristino di un database Oracle Database 12c in una macchina virtuale Linux di Azure

È possibile usare l'interfaccia della riga di comando di Azure per creare e gestire risorse di Azure a un prompt dei comandi o per creare script. In questo articolo vengono usati script dell'interfaccia della riga di comando di Azure per distribuire un database Oracle Database 12c da un'immagine della raccolta di Azure Marketplace.

Prima di iniziare, assicurarsi che l'interfaccia della riga di comando di Azure sia installata. Per altre informazioni, vedere [Azure CLI installation guide](https://docs.microsoft.com/cli/azure/install-azure-cli) (Guida all'installazione dell'interfaccia della riga di comando di Azure).

## <a name="prepare-the-environment"></a>Preparare l'ambiente
### <a name="step-1-assumptions"></a>Passaggio 1: Presupposti

*   Per eseguire il processo di backup e ripristino, è necessario creare una VM Linux con un'istanza installata di Oracle Database 12c. L'immagine di Marketplace usata per creare le VM viene denominata *Oracle:Oracle-Database-Ee:12.1.0.2:latest*.

    Per istruzioni su come creare un database Oracle, vedere [Creare un database Oracle Database 12c in una macchina virtuale di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create).


### <a name="step-2-connect-to-the-vm"></a>Passaggio 2: Eseguire la connessione alla VM

*   Per creare una sessione di Secure Shell (SSH) con la VM, usare il comando seguente. Sostituire la combinazione di indirizzo IP e nome host con il valore `publicIpAddress` per la macchina virtuale.

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>Passaggio 3: Preparare il database

1.  Questo passaggio presuppone che si abbia un'istanza di Oracle (cdb1) in esecuzione in una macchina virtuale denominata *myVM*.

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

2.  Assicurarsi che il database sia in modalità di log di archiviazione (passaggio facoltativo):

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
3.  Creare una tabella per testare il commit (passaggio facoltativo):

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

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>Passaggio 4: Backup coerente delle applicazioni per VM Linux

Si tratta di una nuova funzionalità del backup di Azure, che consente agli utenti di specificare gli script di pre e post-backup da eseguire prima e dopo lo snapshot della VM.

#### <a name="1-download-vmsnapshotscriptpluginconfigjson-from-httpsgithubcommicrosoftazurebackupvmsnapshotpluginconfig-content-should-be-similar-to-following"></a>1. Scaricare VMSnapshotScriptPluginConfig.json da https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig. Il contenuto deve essere simile a quanto segue.

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

#### <a name="2-create-etcazure-folder-on-vm"></a>2. Creare la cartella /etc/azure nella VM

```bash
$ sudo su -
# mkdir /etc/azure
# cd /etc/azure
```

#### <a name="3-copy-vmsnapshotscriptpluginconfigjson-file-to-folder-etcazure"></a>3. Copiare il file VMSnapshotScriptPluginConfig.json nella cartella /etc/azure.

#### <a name="4-edit-the-vmsnapshotscriptpluginconfigjson-file-to-included-the-prescriptlocation-and-postscriptlocation-parameters-for-example"></a>4. Modificare il file VMSnapshotScriptPluginConfig.json in modo che includa i parametri PreScriptLocation e PostScriptlocation. ad esempio:
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
#### <a name="5-create-the-pre-and-post-script-files"></a>5. Creare i file di script di pre e post-backup

Di seguito è riportato un esempio di script di pre e post-backup per un backup a freddo (arresto e riavvio)

Per /etc/azure/pre_script.sh

```bash
v_date=`date +%Y%m%d%H%M`
ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
ORA_OWNER=oracle
su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" > /etc/azure/pre_script_$v_date.log
```

Per /etc/azure/post_script.sh

```bash
v_date=`date +%Y%m%d%H%M`
ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
ORA_OWNER=oracle
su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" > /etc/azure/post_script_$v_date.log
```

Di seguito è riportato un esempio di script di pre e post-backup per un backup a caldo

```bash
v_date=`date +%Y%m%d%H%M`
ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
ORA_OWNER=oracle
su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/pre_script.sql" > /etc/azure/pre_script_$v_date.log
```

Per /etc/azure/post_script.sh

```bash
v_date=`date +%Y%m%d%H%M`
ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
ORA_OWNER=oracle
su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/post_script.sql" > /etc/azure/pre_script_$v_date.log
```

Per /etc/azure/pre_script.sql, è necessario modificare il contenuto del file in base ai requisiti

```bash
alter tablespace SYSTEM begin backup;
...
...
alter system switch logfile;
alter system archive log stop;
```

Per /etc/azure/post_script.sql, è necessario modificare il contenuto del file in base ai requisiti

```bash
alter tablespace SYSTEM end backup;
...
...
alter system archive log start;
```

#### <a name="6-change-file-permissions"></a>6. Modificare le autorizzazioni del file

```bash
# chmod 600 /etc/azure/VMSnapshotScriptPluginConfig.json
# chmod 700 /etc/azure/pre_script.sh
# chmod 700 /etc/azure/post_script.sh
```

#### <a name="7-test-the-scripts-sign-in-as-root-make-sure-no-errors"></a>7. Testare gli script accedendo come radice e assicurarsi che non vi sia alcun errore

```bash
# /etc/azure/pre_script.sh
# /etc/azure/post_script.sh
```

Per altre informazioni, vedere [Announcing application consistent backup for Linux VMs using Azure Backup](https://azure.microsoft.com/en-us/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/) (Backup coerente delle applicazioni per VM Linux che usano il backup di Azure).


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>Passaggio 5: Usare gli insiemi di credenziali dei servizi di ripristino di Azure per eseguire il backup della VM

1.  Accedere al portale di Azure e cercare un'istanza di insiemi di credenziali dei servizi di ripristino.
![Pagina degli insiemi di credenziali dei servizi di ripristino](./media/oracle-backup-recovery/recovery_service_01.png)

2.  Fare clic sul pulsante **Aggiungi** per aggiungere un nuovo insieme di credenziali.
![Pagina di aggiunta degli insiemi di credenziali dei servizi di ripristino](./media/oracle-backup-recovery/recovery_service_02.png)

3.  Per continuare, fare clic su **myVault**. Viene visualizzata la pagina di dettaglio.
![Pagina di dettaglio degli insiemi di credenziali dei servizi di ripristino](./media/oracle-backup-recovery/recovery_service_03.png)

4.  Fare clic sul pulsante **Backup**. In seguito, aggiungere l'obiettivo di backup, i criteri e gli elementi di backup.
![Pagina di backup degli insiemi di credenziali dei servizi di ripristino](./media/oracle-backup-recovery/recovery_service_04.png)

5.  Per **Obiettivo del backup**, usare i valori predefiniti **Azure** e **Macchina virtuale**. Per continuare, fare clic su **OK**.
![Pagina di dettaglio degli insiemi di credenziali dei servizi di ripristino](./media/oracle-backup-recovery/recovery_service_05.png)

6.  Per **Criterio di backup**, usare **DefaultPolicy** o **Crea un nuovo criterio**. Per continuare, fare clic su **OK**.
![Pagina di dettaglio dei criteri di backup degli insiemi di credenziali dei servizi di ripristino](./media/oracle-backup-recovery/recovery_service_06.png)

7.  Selezionare la casella di controllo **myVM1**, fare clic su **OK** e fare clic su **Abilita backup**.
![Pagina di dettaglio degli elementi per il backup degli insiemi di credenziali dei servizi di ripristino](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > Dopo aver fatto clic su **Abilita backup**, il processo di backup non viene avviato fino all'orario pianificato. Per configurare un backup immediato, completare il passaggio successivo.

8.  Fare clic su **Elementi di backup** e sotto **Conteggio degli elementi di backup** fare clic sul numero di backup dell'elemento.

    ![Pagina di dettaglio degli insiemi di credenziali myVault dei servizi di ripristino](./media/oracle-backup-recovery/recovery_service_08.png)

9.  Sul lato destro della pagina, fare clic sui puntini di sospensione (**...** ) e su **Esegui backup**.

    ![Comando Esegui backup degli insiemi di credenziali dei servizi di ripristino](./media/oracle-backup-recovery/recovery_service_09.png)

10. Fare clic sul pulsante **Backup**, attendere il completamento del processo di backup e procedere con il "Passaggio 6: Rimuovere i file di database."
Per visualizzare lo stato del processo di backup, fare clic su **Processi**.
![Pagina dei processi degli insiemi di credenziali dei servizi di ripristino](./media/oracle-backup-recovery/recovery_service_10.png)

    Lo stato del processo di backup viene visualizzato nella figura seguente.

    ![Pagina dei processi degli insiemi di credenziali dei servizi di ripristino con stato](./media/oracle-backup-recovery/recovery_service_11.png)

11. Per il backup coerente delle applicazioni. Risolvere gli eventuali errori nel file di log /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0.

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
    
2.  Arrestare l'istanza di Oracle (passaggio facoltativo):

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-recovery-services-vaults"></a>Ripristinare i file eliminati dagli insiemi di credenziali dei servizi di ripristino
Per ripristinare i file eliminati, completare la procedura seguente:

1. Accedere al portale di Azure e cercare l'elemento degli insiemi di credenziali dei servizi di ripristino *myVault*. Nell'angolo in alto a destra sotto **Elementi di backup** fare clic sul numero di elementi.
![Elementi di backup myVault degli insiemi di credenziali dei servizi di ripristino](./media/oracle-backup-recovery/recovery_service_12.png)

2. Sotto **Conteggio degli elementi di backup** fare clic sul numero di elementi.
![Numero di elementi di backup di macchine virtuali di Azure in insiemi di credenziali di servizi di ripristino](./media/oracle-backup-recovery/recovery_service_13.png)

3. Fare clic su **Ripristino di file (anteprima)**.
![Schermata della pagina di ripristino dei file degli insiemi di credenziali dei servizi di ripristino](./media/oracle-backup-recovery/recovery_service_14.png)

4. Fare clic su **Scarica script** e salvare il file di download con estensione sh in una cartella nel computer client.
![Opzioni di salvataggio e download del file di script](./media/oracle-backup-recovery/recovery_service_15.png)

5. Copiare il file con estensione sh nella VM.

    Nell'esempio seguente viene illustrato come usare un comando di copia di sicurezza (scp) per spostare il file nella macchina virtuale. È possibile anche copiare il contenuto negli Appunti e incollarlo in un nuovo file configurato nella VM.

    > [!IMPORTANT]
    > Nell'esempio seguente assicurarsi di aggiornare i valori di indirizzo IP e cartella. I valori devono corrispondere a quelli della cartella in cui è stato salvato il file.

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```
6. Modificare il file in modo che appartenga alla radice.

    Nell'esempio seguente il file viene modificato in modo che appartenga alla radice e in seguito viene modificata l'autorizzazione.

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```
    L'esempio successivo illustra ciò che dovrebbe venire visualizzato dopo aver eseguito lo script precedente. Quando viene chiesto di continuare, immettere **Y**.

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

    ![comando df-k](./media/oracle-backup-recovery/recovery_service_16.png)

8. Usare lo script seguente per copiare nuovamente i file mancanti nelle cartelle.

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

    Nel portale di Azure fare clic su **Unmount Disks** (Smonta dischi).

    ![Comando Unmount disks](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>Ripristinare l'intera VM

Anziché ripristinare i file eliminati dagli insiemi di credenziali dei servizi di ripristino, è possibile ripristinare l'intera VM.

### <a name="step-1-drop-the-myvm"></a>Passaggio 1: Eliminare myVM

*   Accedere al portale di Azure, passare all'insieme di credenziali *myVM* e selezionare **Elimina**.

    ![Comando Elimina](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>Passaggio 2: Ripristinare la VM

1.  Passare a **Insiemi di credenziali dei servizi di ripristino** e selezionare **myVault**.
![Elemento myVault visualizzato nell'interfaccia utente](./media/oracle-backup-recovery/recover_vm_02.png)

2.  Nell'angolo in alto a destra sotto **Elementi di backup** fare clic sul numero di elementi.
![Elementi di backup myVault](./media/oracle-backup-recovery/recover_vm_03.png)

3.  Sotto **Conteggio degli elementi di backup** fare clic sul numero di elementi.
![Pagina di ripristino della VM](./media/oracle-backup-recovery/recover_vm_04.png)

4.  Sul lato destro della pagina fare clic sui puntini di sospensione (**...** ) e su **Ripristina macchina virtuale**.
![Comando Ripristina macchina virtuale](./media/oracle-backup-recovery/recover_vm_05.png)

5.  Selezionare l'elemento che si vuole ripristinare e fare clic su **OK**.
![Seleziona punto di ripristino](./media/oracle-backup-recovery/recover_vm_06.png): deve essere visualizzata una barra verticale blu se è stato abilitato il backup coerente delle applicazioni.

6.  Selezionare **Nome macchina virtuale**, selezionare **Gruppo di risorse** e fare clic su **OK**.
![Valori di configurazione del ripristino](./media/oracle-backup-recovery/recover_vm_07.png)

7.  Per ripristinare la VM, nell'angolo in basso a sinistra dell'interfaccia utente (vedere l'immagine precedente) fare clic su **Ripristina**.

8.  Per visualizzare lo stato del processo di ripristino, fare clic su **Processi**.
![Comando dello stato dei processi di backup](./media/oracle-backup-recovery/recover_vm_08.png)

    L'immagine seguente visualizza lo stato del processo di ripristino.

    ![Stato del processo di ripristino](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>Passaggio 3: Impostare l'indirizzo IP pubblico
Dopo il ripristino della VM, configurare l'indirizzo IP pubblico.

1.  Usare la casella di ricerca per trovare gli **Indirizzi IP pubblici**.

    ![Elenco degli indirizzi IP pubblici](./media/oracle-backup-recovery/create_ip_00.png)

2.  Nell'angolo in alto a sinistra fare clic su **Aggiungi**. Per **Nome**, selezionare il nome dell'IP pubblico, per **Gruppo di risorse**, selezionare **Utilizza esistente**. Fare clic su **Crea**.

    ![Creare un indirizzo IP](./media/oracle-backup-recovery/create_ip_01.png)

3.  Per associare l'indirizzo IP pubblico alla scheda dell'interfaccia di rete per la VM, cercare e selezionare **myVMip** e fare clic su **Associa**.

    ![Associare l'indirizzo IP](./media/oracle-backup-recovery/create_ip_02.png)

4.  Per **Tipo di risorsa** selezionare **Interfaccia di rete**, selezionare la scheda dell'interfaccia di rete usata dall'istanza di myVM e fare clic su **OK**.

    ![Selezionare i valori Tipo di risorsa e Interfaccia di rete](./media/oracle-backup-recovery/create_ip_03.png)

5.  Cercare e aprire l'istanza di myVM che viene trasferita dal portale. L'indirizzo IP che è associato alla VM viene visualizzato nell'angolo in alto a destra.

    ![Valore dell'indirizzo IP](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>Passaggio 4: Eseguire la connessione alla VM

*   Per eseguire la connessione, usare lo script seguente:

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
> Se il comando **startup** del database genera un errore, per ripristinare il database, vedere "Passaggio 6: Usare RMAN per ripristinare il database."

### <a name="step-6-use-rman-to-recover-the-database-optional-step"></a>Passaggio 6: Usare RMAN per ripristinare il database (passaggio facoltativo)
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

Quando la VM non è più necessaria, è possibile usare il comando seguente per rimuovere il gruppo di risorse, la VM e tutte le risorse correlate:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione: Creare VM a disponibilità elevata](../../linux/create-cli-complete.md)

[Esplorare gli esempi dell'interfaccia della riga di comando di Azure per la distribuzione della VM](../../linux/cli-samples.md)

