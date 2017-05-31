---
title: Creare un database Oracle Database 12c in una macchina virtuale di Azure | Microsoft Docs
description: Ottenere rapidamente un database Oracle 12c operativo nell&quot;ambiente Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tonyguid
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/26/2017
ms.author: rclaus
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 38cb5c4c1beb0e50c8a6395afb118c63b2750210
ms.contentlocale: it-it
ms.lasthandoff: 05/05/2017

---

# <a name="create-an-oracle-database-12c-database-in-an-azure-virtual-machine"></a>Creare un database Oracle Database 12c in una macchina virtuale di Azure

È possibile usare l'interfaccia della riga di comando di Azure per creare e gestire risorse di Azure nel prompt dei comandi o in script. In questo articolo vengono usati script nell'interfaccia della riga di comando di Azure per distribuire un database Oracle Database 12c da un'immagine della raccolta di Azure Marketplace.

Prima di iniziare, assicurarsi che l'interfaccia della riga di comando di Azure sia installata. Per altre informazioni, vedere la [guida all'installazione dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

## <a name="sign-in-to-azure"></a>Accedere ad Azure 

Per accedere alla sottoscrizione di Azure nell'interfaccia della riga di comando di Azure, usare il comando [az login](/cli/azure/#login). Seguire quindi le istruzioni visualizzate sullo schermo.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Per creare un gruppo di risorse, usare il comando [az group create](/cli/azure/group#create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella località `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-vm"></a>Creare una macchina virtuale

Per crea una macchina virtuale (VM), usare il comando [az vm create](/cli/azure/vm#create). 

L'esempio seguente crea una VM denominata `myVM`. Crea anche le chiavi SSH se non esistono già in un percorso predefinito. Per usare un set specifico di chiavi, utilizzare l'opzione `--ssh-key-value`.  

```azurecli
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys
```

In seguito alla creazione della VM, l'interfaccia della riga di comando di Azure visualizza informazioni simili a quelle dell'esempio seguente. Notare il valore di `publicIpAddress`. Questo indirizzo verrà usato per accedere alla VM.

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

## <a name="connect-to-the-vm"></a>Connettersi alla VM

Per creare una sessione SSH con la VM, usare il comando seguente. Sostituire l'indirizzo IP con il valore di `publicIpAddress` della VM.

```bash 
ssh <publicIpAddress>
```

## <a name="create-the-database"></a>Creare il database

Il software Oracle è già installato nell'immagine del Marketplace. Installare quindi il database. 

1.  Usare l'utente con privilegi avanzati *oracle* e quindi inizializzare il listener per la registrazione:

    ```bash
    sudo su - oracle
    [oracle@myVM /]$ lsnrctl start
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
    [oracle@myVM /]$ dbca -silent \
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
    Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for further details.
    ```

## <a name="prepare-for-connectivity"></a>Preparare la connettività 
Per garantire la corretta inizializzazione del database, testare la connettività locale. Il modo più semplice per eseguire questa operazione consiste nel connettersi con `sqlplus`.  

Prima della connessione, è necessario configurare due variabili di ambiente: ORACLE_HOME e ORACLE_SID.

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME

ORACLE_SID=cdb1; export ORACLE_SID
```

È anche possibile aggiungere ORACLE_HOME e ORACLE_SID al file con estensione bashrc. In questo modo, le variabili di ambiente vengono salvate per gli accessi successivi.

```
# Add ORACLE_HOME.
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1

# Add ORACLE_SID.
export ORACLE_SID=cdb1

```

## <a name="oracle-em-express-connectivity"></a>Connettività a Oracle EM Express

Per ottenere uno strumento di gestione dell'interfaccia utente grafica da usare per esplorare il database, configurare Oracle EM Express. Per connettersi a Oracle EM Express, prima di tutto è necessario configurare la porta in Oracle:

```bash
$ sudo su - oracle

sqlplus / as sysdba

SQL*Plus: Release 12.1.0.2.0 Production on Fri Apr 7 13:16:30 2017

Copyright (c) 1982, 2014, Oracle.  All rights reserved.


Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL> select con_id, name, open_mode from v$pdbs;

    CON_ID NAME                           OPEN_MODE
---------- ------------------------------ ----------
         2 PDB$SEED                       READ ONLY
         3 PDB1                           MOUNT

SQL> alter session set container=pdb1;

Session altered.

SQL> alter database open;

database opened.

SQL> alter session set container=pdb1;

Session altered.

SQL> exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);

PL/SQL procedure successfully completed.
```

## <a name="automate-database-startup-and-shutdown"></a>Automatizzare l'avvio e l'arresto del database

Per impostazione predefinita, il database Oracle non viene avviato automaticamente quando si avvia la VM. Per configurare il database Oracle perché venga avviato quando si avvia la VM, accedere prima di tutto come utente ROOT. Quindi creare e aggiornare alcuni file di sistema.

1.  Accedere come utente ROOT:

    ```bash
    # sudo su -
    ```

2.  Modificare il file /etc/oratab dal valore predefinito `N` a `Y`:

    ```
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  Creare il file /etc/init.d/dbora:

    ```bash
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

4.  Modificare le autorizzazioni:

    ```bash
    # chgrp dba /etc/init.d/dbora
    # chmod 750 /etc/init.d/dbora
    ```

5.  Creare collegamenti simbolici per l'avvio e l'arresto:

    ```bash
    # ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    # ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    # ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  Per testare le modifiche, riavviare la VM:

    ```bash
    # reboot
    ```

## <a name="open-ports-for-connectivity"></a>Aprire le porte per la connettività

Il passaggio finale consiste nel configurare alcuni endpoint esterni. Per configurare il gruppo di sicurezza di rete di Azure che protegge la VM, prima di tutto chiudere la sessione nella VM. 

1.  Per aprire l'endpoint usato per accedere al database Oracle in remoto, eseguire il comando seguente: 

    ```azurecli
    az network nsg rule create --resource-group myResourceGroup\
        --nsg-name myVmNSG --name allow-oracle\
        --protocol tcp --direction inbound --priority 999 \
        --source-address-prefix '*' --source-port-range '*' \
        --destination-address-prefix '*' --destination-port-range 1521 --access allow
    ```

    Il risultato dovrebbe essere simile all'esempio seguente:

    ```
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

2.  Per aprire l'endpoint usato per accedere a Oracle EM Express in remoto, eseguire il comando seguente:

    ```azurecli
    az network nsg rule create --resource-group myResourceGroup\
        --nsg-name myVmNSG --name allow-oracle-EM\
        --protocol tcp --direction inbound --priority 1001 \
        --source-address-prefix '*' --source-port-range '*' \
        --destination-address-prefix '*' --destination-port-range 5502 --access allow
    ```

    Il risultato dovrebbe essere simile all'esempio seguente:

    ```azurecli
    {
    "access": "Allow",
    "description": null,
    "destinationAddressPrefix": "*",
    "destinationPortRange": "5502",
    "direction": "Inbound",
    "etag": "W/\"06c68b5e-1b3f-4ae0-bcf6-59b3b981d685\"",
    "id": "/subscriptions/2dad32d6-b188-49e6-9437-ca1d51cec4dd/resourceGroups/kennyRG/providers/Microsoft.Network/networkSecurityGroups/kennyVM1NSG/securityRules/allow-oracle-EM",
    "name": "allow-oracle-EM",
    "priority": 1001,
    "protocol": "Tcp",
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "sourceAddressPrefix": "*",
    "sourcePortRange": "*"
    }
    ```

3.  Connettersi a EM Express dal browser: 

    ```
    https://<VM hostname>:5502/em
    ```

È possibile accedere usando l'account SYS con la password configurata durante l'installazione.


## <a name="delete-the-vm"></a>Eliminare la VM

Quando la VM non è più necessaria, è possibile usare il comando seguente per rimuovere il gruppo di risorse, la VM e tutte le risorse correlate:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione: Creare VM a disponibilità elevata](../../linux/create-cli-complete.md)

[Esplorare gli esempi dell'interfaccia della riga di comando di Azure per la distribuzione della VM](../../linux/cli-samples.md)

