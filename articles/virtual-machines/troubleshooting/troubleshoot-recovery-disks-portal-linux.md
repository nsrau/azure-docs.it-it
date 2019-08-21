---
title: Uso di una macchina virtuale Linux per la risoluzione dei problemi nel portale di Azure | Documentazione Microsoft
description: Informazioni su come risolvere i problemi delle macchine virtuali Linux connettendo il disco del sistema operativo a una macchina virtuale di ripristino nel portale di Azure
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/19/2019
ms.author: genli
ms.openlocfilehash: 21122847c1b417b00cfe8c69b8324a2f73bf31ea
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69641121"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Risolvere i problemi relativi a una macchina virtuale Linux collegando il disco del sistema operativo a una macchina virtuale di ripristino nel portale di Azure
Se nella VM Linux viene rilevato un errore di avvio o del disco, potrebbe essere necessario eseguire dei passaggi per la risoluzione dei problemi sul disco rigido virtuale stesso. Un esempio comune è una voce non valida in `/etc/fstab` che impedisce il corretto avvio della macchina virtuale. Questo articolo illustra come usare il portale di Azure per connettere il disco rigido virtuale a un'altra VM Linux per risolvere eventuali errori e quindi ricreare la VM originale.

## <a name="recovery-process-overview"></a>Panoramica del processo di ripristino
I passaggi per la risoluzione dei problemi sono i seguenti:

1. Arrestare la VM interessata.
1. Eseguire uno snapshot per il disco del sistema operativo della macchina virtuale.
1. Creare un disco rigido virtuale dallo snapshot.
1. Collegare e montare il disco rigido virtuale in un'altra VM Windows per risolvere i problemi riscontrati.
1. Connettersi alla macchina virtuale usata per la risoluzione dei problemi. Modificare i file o eseguire eventuali strumenti per risolvere i problemi nel disco rigido virtuale originale.
1. Smontare e scollegare il disco rigido virtuale dalla macchina virtuale usata per la risoluzione dei problemi.
1. Scambiare il disco del sistema operativo per la macchina virtuale.

> [!NOTE]
> Questo articolo non si applica alla macchina virtuale con disco non gestito.

## <a name="determine-boot-issues"></a>Individuare i problemi di avvio
Esaminare la diagnostica di avvio e la schermata della VM per determinare perché la macchina virtuale non è in grado di avviarsi correttamente. Un esempio comune è una voce non valida in `/etc/fstab`, oppure l'eliminazione o lo spostamento di un disco rigido virtuale sottostante.

Nel portale, selezionare la macchina virtuale e quindi scorrere verso il basso fino alla sezione **Supporto e risoluzione dei problemi**. Fare clic su **Diagnostica di avvio** per i visualizzare i messaggi della console originati dalla VM. Esaminare i log della console per cercare di determinare la causa del problema riscontrato nella macchina virtuale. L'esempio seguente mostra una macchina virtuale bloccata in modalità di manutenzione che richiede l'intervento manuale:

![Visualizzazione dei log della console nella diagnostica di avvio della macchina virtuale](./media/troubleshoot-recovery-disks-portal-linux/boot-diagnostics-error.png)

È anche possibile fare clic su **Schermata** nella parte superiore del log della diagnostica di avvio per scaricare una schermata della macchina virtuale.

## <a name="take-a-snapshot-of-the-os-disk"></a>Eseguire uno snapshot del disco del sistema operativo
Uno snapshot è una copia completa di sola lettura di un disco rigido virtuale. È consigliabile arrestare la macchina virtuale prima di creare uno snapshot per cancellare tutti i processi in corso. Per creare uno snapshot di un disco del sistema operativo, attenersi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **macchine virtuali** dalla barra laterale, quindi selezionare la macchina virtuale con problemi.
1. Nel riquadro sinistro selezionare **dischi**e quindi selezionare il nome del disco del sistema operativo.
    ![Immagine sul nome del disco del sistema operativo](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. Nella pagina **Panoramica** del disco del sistema operativo, quindi selezionare **Crea snapshot**.
1. Creare uno snapshot nella stessa posizione del disco del sistema operativo.

## <a name="create-a-disk-from-the-snapshot"></a>Creare un disco dallo snapshot
Per creare un disco dallo snapshot, attenersi alla procedura seguente:

1. Selezionare **cloud Shell** dalla portale di Azure.

    ![Immagine delle Cloud Shell aperte](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. Eseguire i comandi di PowerShell riportati di seguito per creare un disco gestito dallo snapshot. È necessario sostituire questi nomi di esempio con i nomi appropriati.

    ```powershell
    #Provide the name of your resource group
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create Managed Disks
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of theManaged Disk
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in GB. It should be greater than the VHD file size. In this sample, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for Managed Disk. PremiumLRS or StandardLRS.
    $storageType = 'StandardLRS'
    
    #Provide the Azure region (e.g. westus) where Managed Disks will be located.
    #This location should be same as the snapshot location
    #Get all the Azure location using command below:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. Se i comandi vengono eseguiti correttamente, il nuovo disco viene visualizzato nel gruppo di risorse specificato.

## <a name="attach-disk-to-another-vm"></a>Connetti disco a un'altra macchina virtuale
Nei passaggi successivi viene utilizzata un'altra macchina virtuale per la risoluzione dei problemi. Dopo aver collegato il disco alla macchina virtuale per la risoluzione dei problemi, è possibile esplorare e modificare il contenuto del disco. Questo processo consente di correggere eventuali errori di configurazione o di esaminare ulteriori file di registro di sistema o dell'applicazione. Per aggiungere il disco a un'altra macchina virtuale, seguire questa procedura:

1. Nel portale, selezionare il gruppo di risorse e quindi la macchina virtuale da usare per la risoluzione dei problemi. Selezionare **dischi**, selezionare **modifica**e quindi fare clic su **Aggiungi disco dati**:

    ![Collegare un disco esistente nel portale](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Nell'elenco **dischi dati** selezionare il disco del sistema operativo della macchina virtuale identificata. Se il disco del sistema operativo non viene visualizzato, assicurarsi che la risoluzione dei problemi della macchina virtuale e del disco del sistema operativo si trovi nella stessa area (località). 
3. Selezionare **Save (Salva** ) per applicare le modifiche.

## <a name="mount-the-attached-data-disk"></a>Montare il disco dati collegato

> [!NOTE]
> Gli esempi seguenti mostrano in dettaglio i passaggi necessari in una VM Ubuntu. Se si usa una diversa distribuzione Linux, ad esempio Red Hat Enterprise Linux o SUSE, i percorsi dei file di log e i comandi `mount` potrebbero differire. Consultare la documentazione relativa alla distribuzione specifica per le opportune modifiche ai comandi.

1. Eseguire SSH nella macchina virtuale di cui risolvere i problemi usando le credenziali appropriate. Se questo disco è il primo disco dati collegato alla macchina virtuale di cui risolvere i problemi, è probabilmente connesso a `/dev/sdc`. Usare `dmseg` per elencare i dischi collegati:

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
Dopo aver risolto gli errori, scollegare il disco rigido virtuale esistente dalla macchina virtuale usata per la risoluzione dei problemi. Non è possibile usare il disco rigido virtuale con altre macchine virtuali finché non viene rilasciato il lease che collega il disco rigido virtuale alla macchina virtuale usata per la risoluzione dei problemi.

1. Dalla sessione SSH nella macchina virtuale usata per la risoluzione dei problemi, smontare il disco rigido virtuale esistente. Modificare innanzitutto la directory padre del punto di montaggio:

    ```bash
    cd /
    ```

    A questo punto smontare il disco rigido virtuale esistente. Nell'esempio viene smontato il dispositivo in `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Scollegare il disco rigido virtuale dalla macchina virtuale. Selezionare la macchina virtuale nel portale, quindi fare clic su **Dischi**. Selezionare il disco rigido virtuale esistente quindi fare clic su **Scollega**:

    ![Scollegare il disco rigido virtuale esistente](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Attendere che il disco dati sia completamente scollegato dalla macchina virtuale prima di continuare.

## <a name="swap-the-os-disk-for-the-vm"></a>Scambiare il disco del sistema operativo per la macchina virtuale

Portale di Azure supporta ora la modifica del disco del sistema operativo della macchina virtuale. A tale scopo, effettuare le operazioni seguenti:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **macchine virtuali** dalla barra laterale, quindi selezionare la macchina virtuale con problemi.
1. Nel riquadro sinistro selezionare **dischi**e quindi fare clic su **Scambia disco del sistema operativo**.
        ![Immagine sul disco del sistema operativo di scambio in portale di Azure](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Scegliere il nuovo disco che è stato riparato, quindi digitare il nome della macchina virtuale per confermare la modifica. Se il disco non è visualizzato nell'elenco, attendere 10 ~ 15 minuti dopo lo scollegamento del disco dalla macchina virtuale per la risoluzione dei problemi. Assicurarsi inoltre che il disco si trovi nello stesso percorso della macchina virtuale.
1. Fare clic su OK.

## <a name="next-steps"></a>Passaggi successivi
Se si sono verificati problemi durante la connessione alla macchina virtuale, vedere l'articolo sulla [risoluzione dei problemi di connessione SSH a una macchina virtuale di Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Per problemi relativi all'accesso alle applicazioni in esecuzione nella macchina virtuale, vedere [Risolvere i problemi di connettività delle applicazioni in una macchina virtuale di Azure per Linux](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Per altre informazioni sull'uso di Resource Manager, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
