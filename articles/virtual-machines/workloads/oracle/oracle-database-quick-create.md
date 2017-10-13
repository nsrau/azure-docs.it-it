---
title: Creare un database Oracle in una VM di Azure | Microsoft Docs
description: Ottenere rapidamente un database Oracle 12c operativo nell'ambiente Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/17/2017
ms.author: rclaus
ms.openlocfilehash: 8683b016c4db2c66fb1dd994405b70c3d137a7fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Creare un database Oracle in una VM di Azure

Questa guida descrive nei dettagli l'uso dell'interfaccia della riga di comando di Azure per distribuire una macchina virtuale di Azure dall'[immagine della raccolta Marketplace di Oracle](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) per creare un database Oracle 12c. Dopo avere distribuito il server, verrà effettuata la connessione via SSH per configurare il database Oracle. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessario eseguire la versione 2.0.4 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
## <a name="create-virtual-machine"></a>Crea macchina virtuale

Per crea una macchina virtuale (VM), usare il comando [az vm create](/cli/azure/vm#create). 

L'esempio seguente crea una VM denominata `myVM`. Crea anche le chiavi SSH se non esistono già in un percorso predefinito. Per usare un set specifico di chiavi, utilizzare l'opzione `--ssh-key-value`.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

In seguito alla creazione della VM, l'interfaccia della riga di comando di Azure visualizza informazioni simili a quelle dell'esempio seguente. Notare il valore di `publicIpAddress`. Questo indirizzo verrà usato per accedere alla VM.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-the-vm"></a>Connettersi alla VM

Per creare una sessione SSH con la VM, usare il comando seguente. Sostituire l'indirizzo IP con il valore di `publicIpAddress` della VM.

```bash 
ssh <publicIpAddress>
```

## <a name="create-the-database"></a>Creare il database

Il software Oracle è già installato nell'immagine del Marketplace. Creare un database di esempio come segue. 

1.  Passare all'utente con privilegi avanzati *oracle*, quindi inizializzare il listener per la registrazione:

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    ```

    L'output è simile al seguente:

    ```bash
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

2.  Creare il database:

    ```bash
    dbca -silent \
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

    La creazione del database richiede alcuni minuti.

3. Impostare le variabili Oracle

Prima della connessione, è necessario configurare due variabili di ambiente: *ORACLE_HOME* e *ORACLE_SID*.

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
ORACLE_SID=cdb1; export ORACLE_SID
```
È anche possibile aggiungere le variabili ORACLE_HOME e ORACLE_SID al file con estensione bashrc. In questo modo, le variabili di ambiente verranno salvate per gli accessi successivi. Verificare che le seguenti istruzioni siano state aggiunte al file `~/.bashrc` usando l'editor scelto.

```bash
# Add ORACLE_HOME. 
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1 
# Add ORACLE_SID. 
export ORACLE_SID=cdb1 
```

## <a name="oracle-em-express-connectivity"></a>Connettività a Oracle EM Express

Per ottenere uno strumento di gestione dell'interfaccia utente grafica da usare per esplorare il database, configurare Oracle EM Express. Per connettersi a Oracle EM Express, prima di tutto è necessario configurare la porta in Oracle. 

1. Connettersi al database usando sqlplus:

    ```bash
    sqlplus / as sysdba
    ```

2. Dopo la connessione, impostare la porta 5502 per EM Express

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3. Aprire il contenitore PDB1 se non è già aperto, ma prima controllare lo stato:

    ```bash
    select con_id, name, open_mode from v$pdbs;
    ```

    L'output è simile al seguente:

    ```bash
      CON_ID NAME                           OPEN_MODE 
      ----------- ------------------------- ---------- 
      2           PDB$SEED                  READ ONLY 
      3           PDB1                      MOUNT
    ```

4. Se OPEN_MODE per `PDB1` non è READ WRITE, usare i comandi seguenti per aprire PDB1:

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

È necessario digitare `quit` per terminare la sessione sqlplus e `exit` per disconnettere l'utente Oracle.

## <a name="automate-database-startup-and-shutdown"></a>Automatizzare l'avvio e l'arresto del database

Per impostazione predefinita, il database Oracle non viene avviato automaticamente quando si riavvia la VM. Per configurare il database Oracle perché venga avviato automaticamente, accedere prima di tutto come utente ROOT. Quindi creare e aggiornare alcuni file di sistema.

1. Accedere come utente ROOT
    ```bash
    sudo su -
    ```

2.  Usando l'editor preferito modificare il file `/etc/oratab` e il valore predefinito `N` in `Y`:

    ```bash
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  Creare un file denominato `/etc/init.d/dbora` e incollare i contenuti seguenti:

    ```
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle

    case "$1" in
    'start')
        # Start the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        # Remove "&" if you don't want startup as a background process.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
        touch /var/lock/subsys/dbora
        ;;

    'stop')
        # Stop the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
        rm -f /var/lock/subsys/dbora
        ;;
    esac
    ```

4.  Modificare le autorizzazioni nei file con *chmod* come segue:

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  Creare collegamenti simbolici per l'avvio e l'arresto come segue:

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  Per testare le modifiche, riavviare la VM:

    ```bash
    reboot
    ```

## <a name="open-ports-for-connectivity"></a>Aprire le porte per la connettività

Il passaggio finale consiste nel configurare alcuni endpoint esterni. Per configurare il gruppo di sicurezza di rete di Azure che protegge la VM, prima di tutto chiudere la sessione SSH nella VM, anche se la sessione dovrebbe essere stata chiusa durante il riavvio nel passaggio precedente. 

1.  Per aprire l'endpoint usato per accedere al database Oracle in modalità remota, creare una regola del gruppo di sicurezza di rete con [az network nsg rule create](/cli/azure/network/nsg/rule#create) come segue: 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  Per aprire l'endpoint usato per accedere a Oracle EM Express in modalità remota, creare una regola del gruppo di sicurezza di rete con [az network nsg rule create](/cli/azure/network/nsg/rule#create) come segue:

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. Se necessario, ottenere di nuovo l'indirizzo IP pubblico della VM con [az network public-ip show](/cli/azure/network/public-ip#show).

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  Connettersi a EM Express dal browser. Verificare che il browser sia compatibile con EM Express. È necessario installare Flash. 

    ```
    https://<VM ip address or hostname>:5502/em
    ```

È possibile eseguire l'accesso usando l'account **SYS** e selezionare la casella di controllo **as sysdba**. Usare la password **OraPasswd1** impostata durante l'installazione. 

![Screenshot della pagina di accesso a Oracle OEM Express](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'esplorazione di un primo database Oracle in Azure e quando la macchina virtuale non è più necessaria, è possibile usare il comando [az group delete](/cli/azure/group#delete) per rimuovere il gruppo di risorse la macchina virtuale e tutte le risorse correlate.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su altre [soluzioni Oracle in Azure](oracle-considerations.md). 

Provare a eseguire l'esercitazione [Installing and Configuring Oracle Automated Storage Management (Installazione e configurazione di Oracle Automated Storage Management)](configure-oracle-asm.md).