---
title: Usare una macchina virtuale per la risoluzione dei problemi relativi a Linux con l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Informazioni su come risolvere i problemi delle macchine virtuali Linux connettendo il disco del sistema operativo a una macchina virtuale di ripristino tramite l'interfaccia della riga di comando di Azure
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: faa15e9cf6288bcd4014cbc03dcf9d82a2047bde
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088363"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Risolvere i problemi relativi a una VM Linux collegando il disco del sistema operativo a una VM di ripristino con l'interfaccia della riga di comando di Azure
Se nella VM Linux viene rilevato un errore di avvio o del disco, potrebbe essere necessario eseguire dei passaggi per la risoluzione dei problemi sul disco rigido virtuale stesso. Un esempio comune è una voce non valida in `/etc/fstab` che impedisce il corretto avvio della macchina virtuale. Questo articolo illustra come usare l'interfaccia della riga di comando di Azure per connettere il disco rigido virtuale a un'altra VM Linux per risolvere eventuali errori e quindi ricreare la VM originale. 

## <a name="recovery-process-overview"></a>Panoramica del processo di ripristino
I passaggi per la risoluzione dei problemi sono i seguenti:

1. Arrestare la VM interessata.
1. Eseguire uno snapshot dal disco del sistema operativo della macchina virtuale.
1. Creare un disco dallo snapshot del disco del sistema operativo.
1. Connettere e montare il nuovo disco del sistema operativo in un'altra VM Linux per la risoluzione dei problemi.
1. Connettersi alla macchina virtuale usata per la risoluzione dei problemi. Modificare i file o eseguire qualsiasi strumento per risolvere i problemi nel nuovo disco del sistema operativo.
1. Smontare e scollegare il nuovo disco del sistema operativo dalla macchina virtuale per la risoluzione dei problemi.
1. Modificare il disco del sistema operativo per la VM interessata.

Per eseguire questi passaggi per la risoluzione dei problemi, è necessario aver installato la versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-az-cli2) e aver eseguito l'accesso a un account Azure con il comando [az login](/cli/azure/reference-index).

> [!Important]
> Gli script in questo articolo si applicano solo alle VM che usano [Managed Disks](../linux/managed-disks-overview.md). 

Negli esempi seguenti sostituire i nomi dei parametri con valori personalizzati, ad esempio `myResourceGroup` e. `myVM`

## <a name="determine-boot-issues"></a>Individuare i problemi di avvio
Esaminare l'output seriale per determinare perché la macchina virtuale non è in grado di avviarsi correttamente. Un esempio comune è una voce non valida in `/etc/fstab`, oppure l'eliminazione o lo spostamento del disco rigido virtuale sottostante.

Ottenere i log di avvio con il comando [az vm boot-diagnostics get-boot-log](/cli/azure/vm/boot-diagnostics). L'esempio seguente ottiene l'output seriale dalla macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`:

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Esaminare l'output seriale per determinare perché la macchina virtuale non è in grado di avviarsi correttamente. Se l'output seriale non fornisce alcuna indicazione, potrebbe essere necessario esaminare i file di log in `/var/log` dopo aver collegato il disco rigido virtuale a una macchina virtuale per la risoluzione dei problemi.

## <a name="stop-the-vm"></a>Arrestare la VM

L'esempio seguente arresta la VM `myVM` dal gruppo di risorse `myResourceGroup`:

```azurecli
az vm stop --resource-group MyResourceGroup --name MyVm
```
## <a name="take-a-snapshot-from-the-os-disk-of-the-affected-vm"></a>Eseguire uno snapshot dal disco del sistema operativo della macchina virtuale interessata

Uno snapshot è una copia completa di sola lettura di un disco rigido virtuale. Non può essere collegato a una VM. Nel passaggio successivo si creerà un disco da questo snapshot. L'esempio seguente crea uno snapshot denominato `mySnapshot` dal disco del sistema operativo della VM denominata "myVM". 

```azurecli
#Get the OS disk Id 
$osdiskid=(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)

#creates a snapshot of the disk
az snapshot create --resource-group myResourceGroupDisk --source "$osdiskid" --name mySnapshot
```
## <a name="create-a-disk-from-the-snapshot"></a>Creare un disco dallo snapshot

Questo script crea un disco gestito denominato `myOSDisk` dallo snapshot `mySnapshot`.  

```azurecli
#Provide the name of your resource group
$resourceGroup=myResourceGroup

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshot=mySnapshot

#Provide the name of the Managed Disk
$osDisk=myNewOSDisk

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize=128

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType=Premium_LRS

#Provide the OS type
$osType=linux

#Provide the name of the virtual machine
$virtualMachine=myVM

#Get the snapshot Id 
$snapshotId=(az snapshot show --name $snapshot --resource-group $resourceGroup --query [id] -o tsv)

# Create a new Managed Disks using the snapshot Id.

az disk create --resource-group $resourceGroup --name $osDisk --sku $storageType --size-gb $diskSize --source $snapshotId

```

Se il gruppo di risorse e lo snapshot di origine non si trovano nella stessa area, verrà visualizzato l'errore "risorsa non trovata" quando si esegue `az disk create`. In questo caso, è necessario specificare `--location <region>` per creare il disco nella stessa area dello snapshot di origine.

A questo punto si ha una copia del disco del sistema operativo originale. È possibile montare questo nuovo disco in un'altra macchina virtuale Windows per la risoluzione dei problemi.

## <a name="attach-the-new-virtual-hard-disk-to-another-vm"></a>Connetti il nuovo disco rigido virtuale a un'altra macchina virtuale
Nei passaggi successivi viene utilizzata un'altra macchina virtuale per la risoluzione dei problemi. Il disco viene collegato a questa macchina virtuale per la risoluzione dei problemi per esplorare e modificare il contenuto del disco. Questo processo consente di correggere eventuali errori di configurazione o di esaminare ulteriori file di registro di sistema o dell'applicazione.

Questo script connette il disco `myNewOSDisk` alla macchina virtuale `MyTroubleshootVM`:

```azurecli
# Get ID of the OS disk that you just created.
$myNewOSDiskid=(az vm show -g myResourceGroupDisk -n myNewOSDisk --query "storageProfile.osDisk.managedDisk.id" -o tsv)

# Attach the disk to the troubleshooting VM
az vm disk attach --disk $diskId --resource-group MyResourceGroup --size-gb 128 --sku Standard_LRS --vm-name MyTroubleshootVM
```
## <a name="mount-the-attached-data-disk"></a>Montare il disco dati collegato

> [!NOTE]
> Gli esempi seguenti mostrano in dettaglio i passaggi necessari in una VM Ubuntu. Se si usa una diversa distribuzione Linux, ad esempio Red Hat Enterprise Linux o SUSE, i percorsi dei file di log e i comandi `mount` potrebbero differire. Consultare la documentazione relativa alla distribuzione specifica per le opportune modifiche ai comandi.

1. Eseguire SSH nella macchina virtuale di cui risolvere i problemi usando le credenziali appropriate. Se questo disco è il primo disco dati collegato alla macchina virtuale usata per la risoluzione dei problemi, è probabilmente connesso a `/dev/sdc`. Usare `dmseg` per visualizzare i dischi collegati:

    ```bash
    dmesg | grep SCSI
    ```

    L'output è simile all'esempio seguente:

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    Nell'esempio precedente, il disco del sistema operativo è in `/dev/sda` e il disco temporaneo fornito per ogni macchina virtuale è in `/dev/sdb`. Se sono presenti più dischi dati, devono trovarsi in `/dev/sdd`, `/dev/sde`, e così via.

2. Creare una directory per montare il disco rigido virtuale esistente. L'esempio seguente crea una directory denominata `troubleshootingdisk`:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Se sul disco rigido virtuale esistente sono presenti più partizioni, montare la partizione richiesta. L'esempio seguente monta la prima partizione primaria in `/dev/sdc1`:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Si consiglia di montare i dischi dati nelle macchine virtuali in Azure usando l'identificatore univoco universale (UUID) del disco rigido virtuale. Per questo scenario, non è necessario montare il disco rigido virtuale usando il relativo l'UUID. Durante il normale utilizzo, invece, modificare `/etc/fstab` per montare i dischi rigidi virtuali usando il nome di dispositivo anziché l'UUID può impedire il corretto avvio della macchina virtuale.


## <a name="fix-issues-on-the-new-os-disk"></a>Risolvere i problemi nel nuovo disco del sistema operativo
Dopo aver montato il disco rigido virtuale eseguire tutte le operazioni di manutenzione e i passaggi necessari per la risoluzione dei problemi. Dopo avere risolto i problemi, continuare con la procedura seguente.


## <a name="unmount-and-detach-the-new-os-disk"></a>Smontare e scollegare il nuovo disco del sistema operativo
Dopo aver risolto gli errori, smontare e scollegare il disco rigido virtuale esistente dalla macchina virtuale usata per la risoluzione dei problemi. Non è possibile usare il disco rigido virtuale con altre macchine virtuali finché non viene rilasciato il lease che collega il disco rigido virtuale alla macchina virtuale usata per la risoluzione dei problemi.

1. Dalla sessione SSH nella macchina virtuale usata per la risoluzione dei problemi, smontare il disco rigido virtuale esistente. Modificare innanzitutto la directory padre del punto di montaggio:

    ```bash
    cd /
    ```

    A questo punto smontare il disco rigido virtuale esistente. Nell'esempio viene smontato il dispositivo in `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Scollegare il disco rigido virtuale dalla macchina virtuale. Uscire dalla sessione SSH alla macchina virtuale per la risoluzione dei problemi:

    ```azurecli
    az vm disk detach -g MyResourceGroup --vm-name MyTroubleShootVm --name myNewOSDisk
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Modificare il disco del sistema operativo per la VM interessata

È possibile usare l'interfaccia della riga di comando di Azure per scambiare dischi del sistema operativo. Non è necessario eliminare e ricreare la macchina virtuale.

Questo esempio arresta la VM `myVM` e assegna il disco `myNewOSDisk` come nuovo disco del sistema operativo.

```azurecli
# Stop the affected VM
az vm stop -n myVM -g myResourceGroup

# Get ID of the OS disk that is repaired.
$myNewOSDiskid=(az vm show -g myResourceGroupDisk -n myNewOSDisk --query "storageProfile.osDisk.managedDisk.id" -o tsv)

# Change the OS disk of the affected VM to "myNewOSDisk"
az vm update -g myResourceGroup -n myVM --os-disk $myNewOSDiskid

# Start the VM
az vm start -n myVM -g myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi
Se si sono verificati problemi durante la connessione alla macchina virtuale, vedere l'articolo sulla [risoluzione dei problemi di connessione SSH a una macchina virtuale di Azure](troubleshoot-ssh-connection.md). Per problemi relativi all'accesso alle applicazioni in esecuzione nella macchina virtuale, vedere [Risolvere i problemi di connettività delle applicazioni in una macchina virtuale di Azure per Linux](troubleshoot-app-connection.md).

