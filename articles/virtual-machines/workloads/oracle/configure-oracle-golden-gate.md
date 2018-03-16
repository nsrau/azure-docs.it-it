---
title: Implementare Oracle Golden Gate in una VM Linux di Azure | Microsoft Docs
description: Implementare rapidamente Oracle Golden Gate nell'ambiente Azure.
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
ms.date: 05/19/2017
ms.author: rclaus
ms.openlocfilehash: c99023d794dfb3b78b26ef721d89302e126f5cb1
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="implement-oracle-golden-gate-on-an-azure-linux-vm"></a>Implementare Oracle Golden Gate in una VM Linux di Azure 

L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questa guida descrive nei dettagli come usare l'interfaccia della riga di comando di Azure per distribuire un database Oracle 12c dall'immagine della raccolta di Azure Marketplace. 

Questo documento descrive dettagliatamente come creare, installare e configurare Oracle Golden Gate in una VM Azure.

Prima di iniziare, verificare che l'interfaccia della riga di comando di Azure sia stata installata. Per altre informazioni, vedere [Azure CLI installation guide](https://docs.microsoft.com/cli/azure/install-azure-cli) (Guida all'installazione dell'interfaccia della riga di comando di Azure).

## <a name="prepare-the-environment"></a>Preparare l'ambiente

Per installare Oracle Golden Gate è necessario creare due VM Azure nello stesso set di disponibilità. L'immagine del Marketplace usata per creare le VM è **Oracle:Oracle-Database-Ee:12.1.0.2:latest**.

È anche necessario avere familiarità con l'editor Unix e nozioni di base di x11 (X Windows).

Di seguito è riportato un riepilogo della configurazione dell'ambiente:
> 
> |  | **Sito primario** | **Sito di replica** |
> | --- | --- | --- |
> | **Versione di Oracle** |Oracle 12c Release 2 – (12.1.0.2) |Oracle 12c Release 2 – (12.1.0.2)|
> | **Nome computer** |myVM1 |myVM2 |
> | **Sistema operativo** |Oracle Linux 6.x |Oracle Linux 6.x |
> | **SID Oracle** |CDB1 |CDB1 |
> | **Schema di replica** |TEST|TEST |
> | **Proprietario/replica Golden Gate** |C##GGADMIN |REPUSER |
> | **Processo Golden Gate** |EXTORA |REPORA|


### <a name="sign-in-to-azure"></a>Accedere ad Azure 

Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/reference-index#az_login). Seguire quindi le istruzioni visualizzate sullo schermo.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella posizione `westus`.

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>Creare un set di disponibilità

Il passaggio seguente è facoltativo ma consigliato. Per altre informazioni, vedere [Linee guida per i set di disponibilità di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines).

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Creare una VM con il comando [az vm create](/cli/azure/vm#az_vm_create). 

Nell'esempio seguente vengono create due VM chiamate `myVM1` e `myVM2`. Creare le chiavi SSH, se non esistono già in una posizione predefinita. Per usare un set specifico di chiavi, utilizzare l'opzione `--ssh-key-value`.

#### <a name="create-myvm1-primary"></a>Creare myVM1 (primaria):
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

Dopo aver creato la VM, l'interfaccia della riga di comando di Azure mostra informazioni simili all'esempio seguente. Prendere nota di `publicIpAddress`. Questo indirizzo viene usato per accedere alla VM.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

#### <a name="create-myvm2-replicate"></a>Creare myVM2 (replica):
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

Prendere nota anche di `publicIpAddress` dopo la creazione.

### <a name="open-the-tcp-port-for-connectivity"></a>Aprire la porta TCP per la connettività

Il passaggio successivo consiste nel configurare gli endpoint esterni, che consentono di accedere al database Oracle in modalità remota. Per configurare gli endpoint esterni, eseguire questi comandi.

#### <a name="open-the-port-for-myvm1"></a>Aprire la porta per myVM1:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

I risultati saranno simili alla risposta seguente:

```bash
{
  "access": "Allow",
  "description": null,
  "destinationAddressPrefix": "*",
  "destinationPortRange": "1521",
  "direction": "Inbound",
  "etag": "W/\"bd77dcae-e5fd-4bd6-a632-26045b646414\"",
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myVmNSG/securityRules/allow-oracle",
  "name": "allow-oracle",
  "priority": 999,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

#### <a name="open-the-port-for-myvm2"></a>Aprire la porta per myVM2:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm2NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-the-virtual-machine"></a>Connettersi alla macchina virtuale

Usare il comando seguente per creare una sessione SSH con la macchina virtuale. Sostituire l'indirizzo IP con `publicIpAddress` della macchina virtuale.

```bash 
ssh <publicIpAddress>
```

### <a name="create-the-database-on-myvm1-primary"></a>Creare il database in myVM1 (primaria)

Il software Oracle è già installato nell'immagine di Marketplace, pertanto il passaggio successivo è installare il database. 

Eseguire il software come utente con privilegi avanzati 'oracle':

```bash
sudo su - oracle
```

Creare il database:

```bash
$ dbca -silent \
   -createDatabase \
   -templateName General_Purpose.dbc \
   -gdbname cdb1 \
   -sid cdb1 \
   -responseFile NO_VALUE \
   -characterSet AL32UTF8 \
   -sysPassword OraPasswd1 \
   -systemPassword OraPasswd1 \
   -createAsContainerDatabase true \
   -numberOfPDBs 1 \
   -pdbName pdb1 \
   -pdbAdminPassword OraPasswd1 \
   -databaseType MULTIPURPOSE \
   -automaticMemoryManagement false \
   -storageType FS \
   -ignorePreReqs
```
Gli output saranno simili alla risposta seguente:

```bash
Copying database files
1% complete
2% complete
8% complete
13% complete
19% complete
27% complete
Creating and starting Oracle instance
29% complete
32% complete
33% complete
34% complete
38% complete
42% complete
43% complete
45% complete
Completing Database Creation
48% complete
51% complete
53% complete
62% complete
70% complete
72% complete
Creating Pluggable Databases
78% complete
100% complete
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for more details.
```

Impostare le variabili ORACLE_SID e ORACLE_HOME.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

È anche possibile aggiungere le variabili ORACLE_HOME e ORACLE_SID al file con estensione bashrc, in modo da salvare queste impostazioni per un accesso futuro:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Avviare il listener Oracle
```bash
$ sudo su - oracle
$ lsnrctl start
```

### <a name="create-the-database-on-myvm2-replicate"></a>Creare il database in myVM2 (replica)

```bash
sudo su - oracle
```
Creare il database:

```bash
$ dbca -silent \
   -createDatabase \
   -templateName General_Purpose.dbc \
   -gdbname cdb1 \
   -sid cdb1 \
   -responseFile NO_VALUE \
   -characterSet AL32UTF8 \
   -sysPassword OraPasswd1 \
   -systemPassword OraPasswd1 \
   -createAsContainerDatabase true \
   -numberOfPDBs 1 \
   -pdbName pdb1 \
   -pdbAdminPassword OraPasswd1 \
   -databaseType MULTIPURPOSE \
   -automaticMemoryManagement false \
   -storageType FS \
   -ignorePreReqs
```
Impostare le variabili ORACLE_SID e ORACLE_HOME.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

È anche possibile aggiungere le variabili ORACLE_HOME e ORACLE_SID al file con estensione bashrc, in modo da salvare queste impostazioni per un accesso futuro.

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Avviare il listener Oracle
```bash
$ sudo su - oracle
$ lsnrctl start
```

## <a name="configure-golden-gate"></a>Configurare Golden Gate 
Per configurare Golden Gate, eseguire i passaggi di questa sezione.

### <a name="enable-archive-log-mode-on-myvm1-primary"></a>Abilitare la modalità archivelog in myVM1 (primaria)

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
```
Abilitare la registrazione forzata e verificare che sia presente almeno un file di registro.

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
SQL> ALTER SYSTEM set enable_goldengate_replication=true;
SQL> ALTER PLUGGABLE DATABASE PDB1 OPEN;
SQL> ALTER SESSION SET CONTAINER=PDB1;
SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
SQL> EXIT;
```

### <a name="download-golden-gate-software"></a>Scaricare il software Golden Gate
Per scaricare e preparare il software Oracle Golden Gate, completare la procedura seguente:

1. Scaricare il file **fbo_ggs_Linux_x64_shiphome.zip** dalla [pagina di download di Oracle Golden Gate](http://www.oracle.com/technetwork/middleware/goldengate/downloads/index.html). Sotto il titolo di download **Oracle GoldenGate 12.x.x.x for Oracle Linux x86-64** è presente un set di file ZIP da scaricare.

2. Dopo aver scaricato i file ZIP nel computer client, usare il protocollo per la copia di sicurezza, SCP, per copiare i file nella macchina virtuale:

  ```bash
  $ scp fbo_ggs_Linux_x64_shiphome.zip <publicIpAddress>:<folder>
  ```

3. Spostare i file ZIP nella cartella **/opt**. Modificare quindi il proprietario dei file come segue:

  ```bash
  $ sudo su -
  # mv <folder>/*.zip /opt
  ```

4. Decomprimere i file, per farlo installare l'utilità di decompressione di Linux se non è già installata:

  ```bash
  # yum install unzip
  # cd /opt
  # unzip fbo_ggs_Linux_x64_shiphome.zip
  ```

5. Modificare l'autorizzazione:

  ```bash
  # chown -R oracle:oinstall /opt/fbo_ggs_Linux_x64_shiphome
  ```

### <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>Preparare il client e la VM per l'esecuzione di x11, solo per i client di Windows
Si tratta di un passaggio facoltativo. Può essere ignorato se si usa un client Linux o se x11 è già configurato.

1. Scaricare PuTTY e Xming sul computer Windows:

  * [Scaricare PuTTY](http://www.putty.org/)
  * [Scaricare Xming](https://xming.en.softonic.com/)

2.  Dopo aver installato PuTTY, nella cartella PuTTY, ad esempio, C:\Programmi\PuTTY, eseguire puttygen.exe, il generatore di chiavi PuTTY.

3.  Nel generatore di chiavi PuTTY:

  - Per generare una chiave, selezionare il pulsante **Genera**.
  - Copiare il contenuto della chiave con **Ctrl+C**.
  - Selezionare il pulsante **Save private key** (Salva la chiave privata).
  - Ignorare l'avviso che viene visualizzato e quindi selezionare **OK**.

    ![Schermata della pagina del generatore di chiavi PuTTY](./media/oracle-golden-gate/puttykeygen.png)

4.  Nella macchina virtuale eseguire questi comandi:

  ```bash
  # sudo su - oracle
  $ mkdir .ssh (if not already created)
  $ cd .ssh
  ```

5. Creare un file denominato **authorized_keys**. Incollare il contenuto della chiave in questo file e quindi salvare il file.

  > [!NOTE]
  > La chiave deve contenere la stringa `ssh-rsa`. In aggiunta, il contenuto della chiave deve essere una singola riga di testo.
  >  

6. Avviare PuTTY. Nel pannello **Categoria** selezionare **Connessione** > **SSH** > **Autenticazione**. Nella casella **Private key file for authentication** (File della chiave privata per l'autenticazione) individuare la chiave generata in precedenza.

  ![Schermata della pagina di impostazione della chiave privata](./media/oracle-golden-gate/setprivatekey.png)

7. Nel pannello **Categoria** selezionare **Connessione** > **SSH** > **X11**. Selezionare quindi la casella **Enable X11 forwarding** (Abilita l'inoltro di X11).

  ![Schermata della pagina di abilitazione di X11](./media/oracle-golden-gate/enablex11.png)

8. Nel pannello **Categoria** andare in **Sessione**. Immettere le informazioni sull'host e quindi selezionare **Apri**.

  ![Screenshot della pagina della sessione](./media/oracle-golden-gate/puttysession.png)

### <a name="install-golden-gate-software"></a>Installare il software Golden Gate

Per installare Oracle Golden Gate seguire questa procedura:

1. Accedere come oracle. L'accesso non dovrebbe richiedere una password. Assicurarsi che Xming sia in esecuzione prima di iniziare l'installazione.
 
  ```bash
  $ cd /opt/fbo_ggs_Linux_x64_shiphome/Disk1
  $ ./runInstaller
  ```
2. Selezionare 'Oracle GoldenGate for Oracle Database 12c'. Selezionare quindi **Next** (Avanti) per continuare.

  ![Screenshot della pagina Select Installation Option (Selezionare l'opzione di installazione) del programma di installazione](./media/oracle-golden-gate/golden_gate_install_01.png)

3. Modificare il percorso del software. Selezionare quindi **Start Manager** (Gestione avvio) e immettere il percorso del database. Selezionare **Avanti** per continuare.

  ![Screenshot della pagina di selezione dell'installazione](./media/oracle-golden-gate/golden_gate_install_02.png)

4. Modificare la directory di inventario e quindi selezionare **Next** (Avanti) per continuare.

  ![Screenshot della pagina di selezione dell'installazione](./media/oracle-golden-gate/golden_gate_install_03.png)

5. Nella schermata **Summary** (Riepilogo) selezionare **Install** (Installa) per continuare.

  ![Screenshot della pagina Select Installation Option (Selezionare l'opzione di installazione) del programma di installazione](./media/oracle-golden-gate/golden_gate_install_04.png)

6. Potrebbe essere richiesto di eseguire uno script come 'root'. In questo caso aprire una sessione separata, connettersi alla VM tramite SSH, passare a root tramite sudo e quindi eseguire lo script. Selezionare **OK** per continuare.

  ![Screenshot della pagina di selezione dell'installazione](./media/oracle-golden-gate/golden_gate_install_05.png)

7. Al termine dell'installazione, selezionare **Close** (Chiudi) per completare il processo.

  ![Screenshot della pagina di selezione dell'installazione](./media/oracle-golden-gate/golden_gate_install_06.png)

### <a name="set-up-service-on-myvm1-primary"></a>Configurare il servizio in myVM1 (primaria)

1. Creare o aggiornare il file tnsnames.ora:

  ```bash
  $ cd $ORACLE_HOME/network/admin
  $ vi tnsnames.ora

  cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

  pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
  ```

2. Creare gli account proprietario e utente di Golden Gate.

  > [!NOTE]
  > L'account proprietario deve avere il prefisso C##.
  >

    ```bash
    $ sqlplus / as sysdba
    SQL> CREATE USER C##GGADMIN identified by ggadmin;
    SQL> EXEC dbms_goldengate_auth.grant_admin_privilege('C##GGADMIN',container=>'ALL');
    SQL> GRANT DBA to C##GGADMIN container=all;
    SQL> connect C##GGADMIN/ggadmin
    SQL> ALTER SESSION SET CONTAINER=PDB1;
    SQL> EXIT;
    ```

3. Creare l'account dell'utente di test di Golden Gate:

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ sqlplus system/OraPasswd1@pdb1
  SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
  SQL> GRANT connect, resource, dba TO test;
  SQL> ALTER USER test QUOTA 100M on USERS;
  SQL> connect test/test@pdb1
  SQL> @demo_ora_create
  SQL> @demo_ora_insert
  SQL> EXIT;
  ```

4. Configurare il file dei parametri EXTRACT.

 Avviare l'interfaccia della riga di comando di Golden Gate (ggsci):

  ```bash
  $ sudo su - oracle
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  GGSCI> DBLOGIN USERID test@pdb1, PASSWORD test
  Successfully logged into database  pdb1
  GGSCI>  ADD SCHEMATRANDATA pdb1.test
  2017-05-23 15:44:25  INFO    OGG-01788  SCHEMATRANDATA has been added on schema test.
  2017-05-23 15:44:25  INFO    OGG-01976  SCHEMATRANDATA for scheduling columns has been added on schema test.

  GGSCI> EDIT PARAMS EXTORA
  ```
5. Aggiungere quanto segue al file dei parametri EXTRACT usando i comandi vi. Premere il tasto Esc, ':wq!' per salvare il file. 

  ```bash
  EXTRACT EXTORA
  USERID C##GGADMIN, PASSWORD ggadmin
  RMTHOST 10.0.0.5, MGRPORT 7809
  RMTTRAIL ./dirdat/rt  
  DDL INCLUDE MAPPED
  DDLOPTIONS REPORT 
  LOGALLSUPCOLS
  UPDATERECORDFORMAT COMPACT
  TABLE pdb1.test.TCUSTMER;
  TABLE pdb1.test.TCUSTORD;
  ```
6. REGISTER EXTRACT - estrazione integrata:

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci

  GGSCI> dblogin userid C##GGADMIN, password ggadmin
  Successfully logged into database CDB$ROOT.

  GGSCI> REGISTER EXTRACT EXTORA DATABASE CONTAINER(pdb1)

  2017-05-23 15:58:34  INFO    OGG-02003  Extract EXTORA successfully registered with database at SCN 1821260.

  GGSCI> exit
  ```
7. Configurare i checkpoint di estrazione e avviare l'estrazione in tempo reale:

  ```bash
  $ ./ggsci
  GGSCI>  ADD EXTRACT EXTORA, INTEGRATED TRANLOG, BEGIN NOW
  EXTRACT (Integrated) added.

  GGSCI>  ADD RMTTRAIL ./dirdat/rt, EXTRACT EXTORA, MEGABYTES 10
  RMTTRAIL added.

  GGSCI>  START EXTRACT EXTORA

  Sending START request to MANAGER ...
  EXTRACT EXTORA starting

  GGSCI > info all

  Program     Status      Group       Lag at Chkpt  Time Since Chkpt

  MANAGER     RUNNING
  EXTRACT     RUNNING     EXTORA      00:00:11      00:00:04
  ```
In questo passaggio è presente il parametro SCN iniziale, che verrà usato in un secondo momento in un'altra sezione:

  ```bash
  $ sqlplus / as sysdba
  SQL> alter session set container = pdb1;
  SQL> SELECT current_scn from v$database;
  CURRENT_SCN
  -----------
      1857887
  SQL> EXIT;
  ```

  ```bash
  $ ./ggsci
  GGSCI> EDIT PARAMS INITEXT
  ```

  ```bash
  EXTRACT INITEXT
  USERID C##GGADMIN, PASSWORD ggadmin
  RMTHOST 10.0.0.5, MGRPORT 7809
  RMTTASK REPLICAT, GROUP INITREP
  TABLE pdb1.test.*, SQLPREDICATE 'AS OF SCN 1857887'; 
  ```

  ```bash
  GGSCI> ADD EXTRACT INITEXT, SOURCEISTABLE
  ```

### <a name="set-up-service-on-myvm2-replicate"></a>Configurare il servizio in myVM2 (replica)


1. Creare o aggiornare il file tnsnames.ora:

  ```bash
  $ cd $ORACLE_HOME/network/admin
  $ vi tnsnames.ora

  cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

  pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
  ```

2. Creare un account di replica:

  ```bash
  $ sqlplus / as sysdba
  SQL> alter session set container = pdb1;
  SQL> create user repuser identified by rep_pass container=current;
  SQL> grant dba to repuser;
  SQL> exec dbms_goldengate_auth.grant_admin_privilege('REPUSER',container=>'PDB1');
  SQL> connect repuser/rep_pass@pdb1 
  SQL> EXIT;
  ```

3. Creare un account dell'utente di test di Golden Gate:

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ sqlplus system/OraPasswd1@pdb1
  SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
  SQL> GRANT connect, resource, dba TO test;
  SQL> ALTER USER test QUOTA 100M on USERS;
  SQL> connect test/test@pdb1
  SQL> @demo_ora_create
  SQL> EXIT;
  ```

4. File dei parametri REPLICAT per replicare le modifiche: 

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  GGSCI> EDIT PARAMS REPORA  
  ```
  Contenuto del file dei parametri REPORA:

  ```bash
  REPLICAT REPORA
  ASSUMETARGETDEFS
  DISCARDFILE ./dirrpt/repora.dsc, PURGE, MEGABYTES 100
  DDL INCLUDE MAPPED
  DDLOPTIONS REPORT
  DBOPTIONS INTEGRATEDPARAMS(parallelism 6)
  USERID repuser@pdb1, PASSWORD rep_pass
  MAP pdb1.test.*, TARGET pdb1.test.*;
  ```

5. Configurare un checkpoint replicat:

  ```bash
  GGSCI> ADD REPLICAT REPORA, INTEGRATED, EXTTRAIL ./dirdat/rt
  GGSCI> EDIT PARAMS INITREP

  ```

  ```bash
  REPLICAT INITREP
  ASSUMETARGETDEFS
  DISCARDFILE ./dirrpt/tcustmer.dsc, APPEND
  USERID repuser@pdb1, PASSWORD rep_pass
  MAP pdb1.test.*, TARGET pdb1.test.*;   
  ```

  ```bash
  GGSCI> ADD REPLICAT INITREP, SPECIALRUN
  ```

### <a name="set-up-the-replication-myvm1-and-myvm2"></a>Configurare la replica (myVM1 e myVM2)

#### <a name="1-set-up-the-replication-on-myvm2-replicate"></a>1. Configurare la replica in myVM2 (replica)

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  GGSCI> EDIT PARAMS MGR
  ```
Aggiornare il file con il contenuto seguente:

  ```bash
  PORT 7809
  ACCESSRULE, PROG *, IPADDR *, ALLOW
  ```
Riavviare quindi il servizio di gestione:

  ```bash
  GGSCI> STOP MGR
  GGSCI> START MGR
  GGSCI> EXIT
  ```

#### <a name="2-set-up-the-replication-on-myvm1-primary"></a>2. Configurare la replica in myVM1 (primaria)

Avviare il caricamento iniziale e verificare gli errori:

```bash
$ cd /u01/app/oracle/product/12.1.0/oggcore_1
$ ./ggsci
GGSCI> START EXTRACT INITEXT
GGSCI> VIEW REPORT INITEXT
```
#### <a name="3-set-up-the-replication-on-myvm2-replicate"></a>3. Configurare la replica in myVM2 (replica)

Sostituire il numero SCN con il numero ottenuto in precedenza:

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  START REPLICAT REPORA, AFTERCSN 1857887
  ```
La replica è iniziata ed è possibile testarla inserendo nuovi record nelle tabelle TEST.


### <a name="view-job-status-and-troubleshooting"></a>Visualizzare lo stato del processo e le informazioni di risoluzione dei problemi

#### <a name="view-reports"></a>Visualizzazione dei report
Per visualizzare i report in myVM1, eseguire questi comandi:

  ```bash
  GGSCI> VIEW REPORT EXTORA 
  ```
 
Per visualizzare i report in myVM2, eseguire questi comandi:

  ```bash
  GGSCI> VIEW REPORT REPORA
  ```

#### <a name="view-status-and-history"></a>Visualizzare stato e cronologia
Per visualizzare stato e cronologia in myVM1, eseguire questi comandi:

  ```bash
  GGSCI> dblogin userid c##ggadmin, password ggadmin 
  GGSCI> INFO EXTRACT EXTORA, DETAIL
  ```

Per visualizzare stato e cronologia in myVM2, eseguire questi comandi:

  ```bash
  GGSCI> dblogin userid repuser@pdb1 password rep_pass 
  GGSCI> INFO REP REPORA, DETAIL
  ```
Questa operazione completa l'installazione e la configurazione di Golden Gate nell'ambiente Oracle Linux.


## <a name="delete-the-virtual-machine"></a>Eliminare la macchina virtuale

Il comando seguente consente di rimuovere il gruppo di risorse, la VM e tutte le risorse correlate quando non sono più necessari.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

[Creare esercitazioni per macchine virtuali a disponibilità elevata](../../linux/create-cli-complete.md)

[Esplorare gli esempi dell'interfaccia della riga di comando per la distribuzione della VM](../../linux/cli-samples.md)
