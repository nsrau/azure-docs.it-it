---
title: Usare una macchina virtuale per la risoluzione dei problemi relativi a Linux con l'interfaccia della riga di comando di Azure 2.0 | Documentazione Microsoft
description: Informazioni su come risolvere i problemi delle macchine virtuali Linux connettendo il disco del sistema operativo a una macchina virtuale di ripristino tramite l'interfaccia della riga di comando di Azure 2.0
services: virtual-machines-linux
documentationCenter: 
authors: iainfoulds
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: iainfou
ms.openlocfilehash: 318f322196b0028e42268b8d6d003457869d1117
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli-20"></a>Risolvere i problemi relativi a una VM Linux collegando il disco del sistema operativo a una VM di ripristino tramite l'interfaccia della riga di comando di Azure 2.0
Se nella VM Linux viene rilevato un errore di avvio o del disco, potrebbe essere necessario eseguire dei passaggi per la risoluzione dei problemi sul disco rigido virtuale stesso. Un esempio comune è una voce non valida in `/etc/fstab` che impedisce il corretto avvio della macchina virtuale. Questo articolo illustra come usare l'interfaccia della riga di comando di Azure 2.0 per connettere il disco rigido virtuale a un'altra VM Linux al fine di risolvere eventuali errori e quindi ricreare la VM originale. È possibile anche eseguire questi passaggi tramite l'[interfaccia della riga di comando di Azure 1.0](troubleshoot-recovery-disks-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="recovery-process-overview"></a>Panoramica del processo di ripristino
I passaggi per la risoluzione dei problemi sono i seguenti:

1. Eliminare la macchina virtuale su cui si riscontrano i problemi, mantenendo i dischi rigidi virtuali.
2. Collegare e montare il disco rigido virtuale in un'altra VM Linux per risolvere i problemi riscontrati.
3. Connettersi alla macchina virtuale usata per la risoluzione dei problemi. Modificare i file o eseguire eventuali strumenti per risolvere i problemi nel disco rigido virtuale originale.
4. Smontare e scollegare il disco rigido virtuale dalla macchina virtuale usata per la risoluzione dei problemi.
5. Creare una VM usando il disco rigido virtuale originale.

Per eseguire questi passaggi per la risoluzione dei problemi, è necessario aver installato la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e aver eseguito l'accesso a un account Azure con il comando [az login](/cli/azure/#az_login).

Negli esempi seguenti sostituire i nomi dei parametri con i valori desiderati. Alcuni esempi di nomi dei parametri sono `myResourceGroup`, `mystorageaccount` e `myVM`.


## <a name="determine-boot-issues"></a>Individuare i problemi di avvio
Esaminare l'output seriale per determinare perché la macchina virtuale non è in grado di avviarsi correttamente. Un esempio comune è una voce non valida in `/etc/fstab`, oppure l'eliminazione o lo spostamento del disco rigido virtuale sottostante.

Ottenere i log di avvio con il comando [az vm boot-diagnostics get-boot-log](/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_get_boot_log). L'esempio seguente ottiene l'output seriale dalla macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`:

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Esaminare l'output seriale per determinare perché la macchina virtuale non è in grado di avviarsi correttamente. Se l'output seriale non fornisce alcuna indicazione, potrebbe essere necessario esaminare i file di log in `/var/log` dopo aver collegato il disco rigido virtuale a una macchina virtuale per la risoluzione dei problemi.


## <a name="view-existing-virtual-hard-disk-details"></a>Visualizzare i dettagli del disco rigido virtuale esistente
Prima di collegare il disco rigido virtuale (VHD) a un'altra macchina virtuale, è necessario identificare l'URI del disco del sistema operativo. 

Visualizzare le informazioni sulla macchina virtuale con il comando [az vm show](/cli/azure/vm#az_vm_show). Usare il flag `--query` per estrarre l'URI nel disco del sistema operativo. Nell'esempio seguente si ottengono le informazioni sul disco per la macchina virtuale denominata `myVM` nel gruppo di risorse denominato `myResourceGroup`:

```azurecli
az vm show --resource-group myResourceGroup --name myVM \
    --query [storageProfile.osDisk.vhd.uri] --output tsv
```

L'URI è analogo a **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd**.

## <a name="delete-existing-vm"></a>Eliminare la VM esistente
In Azure, i dischi rigidi virtuali e le macchine virtuali sono due risorse distinte. In un disco rigido virtuale sono archiviati il sistema operativo, le applicazioni e le configurazioni. La macchina virtuale è invece costituita da metadati che definiscono le dimensioni o il percorso, e da risorse di riferimento, ad esempio un disco rigido virtuale o una scheda di interfaccia di rete virtuale. A ogni disco rigido virtuale associato a una macchina virtuale viene assegnato un lease. È possibile collegare e scollegare i dischi dati anche quando la macchina virtuale è in esecuzione, mentre non è possibile scollegare il disco del sistema operativo, a meno che la risorsa di macchina non sia stata eliminata. Il lease continua ad associare il disco del sistema operativo e la macchina virtuale anche quando questa viene arrestata e deallocata.

Il primo passaggio per ripristinare la macchina virtuale consiste nell'eliminare la risorsa della macchina virtuale stessa. Anche se si elimina la macchina virtuale, i dischi rigidi virtuali restano nell'account di archiviazione. Dopo aver eliminato la macchina virtuale, il disco rigido virtuale viene collegato a un'altra macchina virtuale per diagnosticare e risolvere gli errori.

Eliminare la macchina virtuale con il comando [az vm delete](/cli/azure/vm#az_vm_delete). L'esempio seguente elimina la macchina virtuale denominata `myVM` dal gruppo di risorse `myResourceGroup`:

```azurecli
az vm delete --resource-group myResourceGroup --name myVM 
```

Attendere il completamento dell'eliminazione della macchina virtuale prima di collegare il disco rigido virtuale a un'altra macchina virtuale. Il lease del disco rigido virtuale che lo associa alla macchina virtuale deve essere rilasciato prima di poter collegare il disco a un'altra macchina.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Collegare il disco rigido virtuale esistente a un'altra macchina virtuale
Nei passaggi successivi viene utilizzata un'altra macchina virtuale per la risoluzione dei problemi. Il disco rigido virtuale esistente viene collegato alla macchina virtuale usata per la risoluzione dei problemi, grazie alla quale è possibile individuare e modificare il contenuto del disco. Questo processo consente, ad esempio, di correggere eventuali errori di configurazione, di esaminare applicazioni aggiuntive o file del registro di sistema. Scegliere o creare un'altra macchina virtuale da usare per la risoluzione dei problemi.

Collegare il disco rigido virtuale con il comando [az vm unmanaged-disk attach](/cli/azure/vm/unmanaged-disk#az_vm_unmanaged_disk_attach). Quando si collega il disco rigido virtuale esistente, specificare l'URI del disco ottenuto con il comando `az vm show` precedente. Nell'esempio seguente il disco rigido virtuale esistente viene collegato alla macchina virtuale usata per la risoluzione dei problemi denominata `myVMRecovery` nel gruppo di risorse `myResourceGroup`:

```azurecli
az vm unmanaged-disk attach --resource-group myResourceGroup --vm-name myVMRecovery \
    --vhd-uri https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
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


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Risolvere i problemi del disco rigido virtuale originale
Dopo aver montato il disco rigido virtuale eseguire tutte le operazioni di manutenzione e i passaggi necessari per la risoluzione dei problemi. Dopo avere risolto i problemi, continuare con la procedura seguente.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Smontare e scollegare il disco rigido virtuale originale
Dopo aver risolto gli errori, smontare e scollegare il disco rigido virtuale esistente dalla macchina virtuale usata per la risoluzione dei problemi. Non è possibile usare il disco rigido virtuale con altre macchine virtuali finché non viene rilasciato il lease che collega il disco rigido virtuale alla macchina virtuale usata per la risoluzione dei problemi.

1. Dalla sessione SSH nella macchina virtuale usata per la risoluzione dei problemi, smontare il disco rigido virtuale esistente. Modificare innanzitutto la directory padre del punto di montaggio:

    ```bash
    cd /
    ```

    A questo punto smontare il disco rigido virtuale esistente. Nell'esempio viene smontato il dispositivo in `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Scollegare il disco rigido virtuale dalla macchina virtuale. Chiudere la sessione SSH nella macchina virtuale usata per la risoluzione dei problemi. Elencare i dischi dati collegati alla macchina virtuale per la risoluzione dei problemi con il comando [az vm unmanaged-disk list](/cli/azure/vm/unmanaged-disk#az_vm_unmanaged_disk_list). L'esempio seguente elenca i dischi dati collegati alla macchina virtuale denominata `myVMRecovery`nel gruppo di risorse `myResourceGroup`:

    ```azurecli
    azure vm unmanaged-disk list --resource-group myResourceGroup --vm-name myVMRecovery \
        --query '[].{Disk:vhd.uri}' --output table
    ```

    Annotare il valore del disco rigido virtuale esistente. Il nome di un disco con l'URI **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd** è, ad esempio, **myVHD**. 

    Scollegare il disco dati dalla macchina virtuale con il comando [az vm unmanaged-disk detach](/cli/azure/vm/unmanaged-disk#az_vm_unmanaged_disk_detach). Nell'esempio seguente il disco denominato `myVHD` viene scollegato dalla macchina virtuale denominata `myVMRecovery` nel gruppo di risorse `myResourceGroup`:

    ```azurecli
    az vm unmanaged-disk detach --resource-group myResourceGroup --vm-name myVMRecovery \
        --name myVHD
    ```


## <a name="create-vm-from-original-hard-disk"></a>Creare una macchina virtuale dal disco rigido originale
Per creare una macchina virtuale dal disco rigido virtuale originale, usare [questo modello di Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd). Il modello JSON effettivo è disponibile all'indirizzo seguente:

- https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json

Il modello distribuisce una macchina virtuale usando l'URI del disco rigido virtuale dal comando precedente. Distribuire il modello con il comando [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create). Specificare l'URI per il disco rigido virtuale originale e quindi specificare il tipo di sistema operativo e le dimensioni e il nome della macchina virtuale nel modo seguente:

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeployment \
  --parameters '{"osDiskVhdUri": {"value": "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"},
    "osType": {"value": "Linux"},
    "vmSize": {"value": "Standard_DS1_v2"},
    "vmName": {"value": "myDeployedVM"}}' \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json
```

## <a name="re-enable-boot-diagnostics"></a>Riabilitare la diagnostica di avvio
Quando si crea la macchina virtuale dal disco rigido virtuale esistente, la diagnostica di avvio potrebbe non essere abilitata automaticamente. Abilitare la diagnostica di avvio con il comando [az vm boot-diagnostics enable](/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_enable). L'esempio seguente abilita l'estensione di diagnostica nella macchina virtuale denominata `myDeployedVM` nel gruppo di risorse `myResourceGroup`:

```azurecli
az vm boot-diagnostics enable --resource-group myResourceGroup --name myDeployedVM
```

## <a name="next-steps"></a>Passaggi successivi
Se si sono verificati problemi durante la connessione alla macchina virtuale, vedere l'articolo sulla [risoluzione dei problemi di connessione SSH a una macchina virtuale di Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Per problemi relativi all'accesso alle applicazioni in esecuzione nella macchina virtuale, vedere [Risolvere i problemi di connettività delle applicazioni in una macchina virtuale di Azure per Linux](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).