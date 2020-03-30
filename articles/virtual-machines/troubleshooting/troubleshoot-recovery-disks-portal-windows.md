---
title: Usare una macchina virtuale per la risoluzione dei problemi Windows nel portale di Azure | Documentazione Microsoft
description: Informazioni su come risolvere i problemi delle macchine virtuali Windows in Azure connettendo il disco del sistema operativo a una macchina virtuale di ripristino nel portale di Azure
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/19/2018
ms.author: genli
ms.openlocfilehash: e76fc2da8da2325a8bb0cda47c4405c9eb03c8f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249997"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Risolvere i problemi relativi a una macchina virtuale Windows collegando il disco del sistema operativo a una macchina virtuale di ripristino nel portale di Azure
Se nella macchina virtuale Windows in Azure viene rilevato un errore di avvio o del disco, potrebbe essere necessario eseguire alcuni passaggi per la risoluzione dei problemi sul disco rigido virtuale stesso. Un esempio comune è un aggiornamento di un'applicazione non riuscito che impedisce il corretto avvio della VM. Questo articolo illustra come usare il portale di Azure per connettere il disco rigido virtuale a un'altra VM Windows per risolvere eventuali errori e quindi ricreare la VM originale. 

## <a name="recovery-process-overview"></a>Panoramica del processo di ripristino
I passaggi per la risoluzione dei problemi sono i seguenti:

1. Arrestare la VM interessata.
1. Creare uno snapshot per il disco del sistema operativo della macchina virtuale.
1. Creare un disco rigido virtuale dallo snapshot.
1. Collegare e montare il disco rigido virtuale in un'altra VM Windows per risolvere i problemi riscontrati.
1. Connettersi alla macchina virtuale usata per la risoluzione dei problemi. Modificare i file o eseguire eventuali strumenti per risolvere i problemi nel disco rigido virtuale originale.
1. Smontare e scollegare il disco rigido virtuale dalla macchina virtuale usata per la risoluzione dei problemi.
1. Scambiare il disco del sistema operativo per la macchina virtuale.

> [!NOTE]
> Questo articolo non si applica alla macchina virtuale con disco non gestito.

## <a name="take-a-snapshot-of-the-os-disk"></a>Creare un'istantanea del disco del sistema operativo
Uno snapshot è una copia completa di sola lettura di un disco rigido virtuale. È consigliabile arrestare correttamente la macchina virtuale prima di creare uno snapshot, per eliminare tutti i processi in corso. Per creare un'istantanea di un disco del sistema operativo, attenersi alla seguente procedura:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Macchine virtuali** nella barra laterale e quindi selezionare la macchina virtuale che presenta un problema.
1. Nel riquadro sinistro selezionare **Dischi**, quindi selezionare il nome del disco del sistema operativo.
    ![Immagine sul nome del disco del sistema operativo](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. Nella pagina **Panoramica** del disco del sistema operativo selezionare **Crea snapshot**.
1. Creare uno snapshot nello stesso percorso del disco del sistema operativo.

## <a name="create-a-disk-from-the-snapshot"></a>Creare un disco dallo snapshot
Per creare un disco dallo snapshot, attenersi alla seguente procedura:

1. Selezionare Cloud Shell dal portale di Azure.Select **Cloud Shell** from the Azure portal.

    ![Immagine di Open Cloud Shell](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. Eseguire i comandi di PowerShell seguenti per creare un disco gestito dallo snapshot. È necessario sostituire questi nomi di esempio con i nomi appropriati.

    ```powershell
    #Provide the name of your resource group
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create Managed Disks
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of theManaged Disk
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in GB. It should be greater than the VHD file size. In this sample, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for Managed Disk.  Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'
    
    #Provide the Azure region (e.g. westus) where Managed Disks will be located.
    #This location should be same as the snapshot location
    #Get all the Azure location using command below:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. Se i comandi vengono eseguiti correttamente, verrà visualizzato il nuovo disco nel gruppo di risorse fornito.

## <a name="attach-the-disk-to-another-vm"></a>Collegare il disco a un'altra macchina virtualeAttach the disk to another VM
Nei passaggi successivi viene utilizzata un'altra macchina virtuale per la risoluzione dei problemi. Dopo aver collegato il disco alla macchina virtuale di risoluzione dei problemi, è possibile sfogliare e modificare il contenuto del disco. Questo processo consente di correggere eventuali errori di configurazione o di esaminare ulteriori file di registro dell'applicazione o del sistema. Per collegare il disco a un'altra macchina virtuale, attenersi alla seguente procedura:

1. Nel portale, selezionare il gruppo di risorse e quindi la macchina virtuale da usare per la risoluzione dei problemi. Selezionare **Dischi**, **selezionare Modifica**e quindi fare clic su **Aggiungi disco dati**:

    ![Collegare un disco esistente nel portale](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Nell'elenco **Dischi dati** selezionare il disco del sistema operativo della macchina virtuale identificata. Se il disco del sistema operativo non viene visualizzato, assicurarsi che la risoluzione dei problemi relativi alla macchina virtuale e al disco del sistema operativo si trovi nella stessa area (posizione). 
3. Selezionare **Salva** per applicare le modifiche.

## <a name="mount-the-attached-data-disk-to-the-vm"></a>Montare il disco dati collegato nella macchina virtuale

1. Aprire una connessione Desktop remoto alla macchina virtuale per la risoluzione dei problemi. 
2. Nella macchina virtuale per la risoluzione dei problemi, Aprire **Server Manager**, quindi selezionare Servizi file **e archiviazione**. 

    ![Selezionare Servizi file e archiviazione in Server Manager](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. Il disco dati viene automaticamente rilevato e collegato. Per visualizzare un elenco dei dischi connessi, selezionare **Dischi**. È possibile selezionare il disco dati per visualizzare informazioni sul volume, inclusa la lettera di unità. L'esempio seguente illustra il disco dati collegato e l'uso di **F:**

    ![Disco collegato e informazioni sul volume in Server Manager](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)

## <a name="fix-issues-on-original-virtual-hard-disk"></a>Risolvere i problemi del disco rigido virtuale originale
Dopo aver montato il disco rigido virtuale eseguire tutte le operazioni di manutenzione e i passaggi necessari per la risoluzione dei problemi. Dopo avere risolto i problemi, continuare con la procedura seguente.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Smontare e scollegare il disco rigido virtuale originale
Dopo aver risolto gli errori, scollegare il disco rigido virtuale esistente dalla macchina virtuale usata per la risoluzione dei problemi. Non è possibile usare il disco rigido virtuale con altre macchine virtuali finché non viene rilasciato il lease che collega il disco rigido virtuale alla macchina virtuale usata per la risoluzione dei problemi.

1. Dalla sessione RDP stabilita con la VM aprire **Server Manager**, quindi selezionare **Servizi file e archiviazione**:

    ![Selezionare Servizi file e archiviazione in Server Manager](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Selezionare **Dischi** e quindi selezionare il disco dati. Fare clic con il pulsante destro del mouse sul disco dati e selezionare **Porta offline**:

    ![Impostare il disco dati come offline in Server Manager](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Scollegare il disco rigido virtuale dalla macchina virtuale. Selezionare la macchina virtuale nel portale di Azure e fare clic su **Dischi**. 
4. Selezionare **Modifica**, selezionare il disco del sistema operativo collegato e quindi fare clic su **Scollega**:

    ![Scollegare il disco rigido virtuale esistente](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Attendere che il disco dati sia completamente scollegato dalla macchina virtuale prima di continuare.

## <a name="swap-the-os-disk-for-the-vm"></a>Scambiare il disco del sistema operativo per la macchina virtuale

Il portale di Azure supporta ora la modifica del disco del sistema operativo della macchina virtuale. A questo scopo, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Macchine virtuali** nella barra laterale e quindi selezionare la macchina virtuale che presenta un problema.
1. Nel riquadro sinistro selezionare **Dischi**, quindi **Scambia disco del sistema operativo**.
        ![Immagine su Scambia disco del sistema operativo nel portale di AzureThe image about Swap OS disk in Azure portal](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Scegliere il nuovo disco riparato e quindi digitare il nome della macchina virtuale per confermare la modifica. Se il disco non è presente nell'elenco, attendere 10 e 15 minuti dopo aver scollegato il disco dalla macchina virtuale per la risoluzione dei problemi. Assicurarsi inoltre che il disco si trova nello stesso percorso della macchina virtuale.
1. Selezionare OK.

## <a name="next-steps"></a>Passaggi successivi
Se si sono verificati problemi durante la connessione alla VM, vedere [Risolvere i problemi di connessioni RDP a una macchina virtuale di Azure](troubleshoot-rdp-connection.md). Per problemi relativi all'accesso alle applicazioni in esecuzione nella VM, vedere [Risolvere i problemi di connettività a un'applicazione in una macchina virtuale Windows](troubleshoot-app-connection.md).

Per altre informazioni sull'uso di Resource Manager, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/management/overview.md).


