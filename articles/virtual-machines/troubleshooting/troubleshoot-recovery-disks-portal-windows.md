---
title: Risolvere i problemi relativi a una macchina virtuale Windows nel portale di Azure | Microsoft Docs
description: Informazioni su come risolvere i problemi relativi alle macchine virtuali Windows in Azure connettendo il disco del sistema operativo a una VM di ripristino tramite il portale di Azure.
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
ms.openlocfilehash: 04adf3903cd925f4507e94347251c762a576ff93
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94735229"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-through-the-azure-portal"></a>Risolvere i problemi di una VM Windows connettendo il disco del sistema operativo a una VM di ripristino tramite il portale di Azure
Se la macchina virtuale (VM) Windows in Azure rileva un errore di avvio o del disco, potrebbe essere necessario eseguire passaggi di risoluzione dei problemi nel disco rigido virtuale (VHD). Un esempio comune è un aggiornamento di un'applicazione non riuscita che impedisce l'avvio corretto della macchina virtuale. Questo articolo illustra come usare la portale di Azure per connettere il disco rigido virtuale a un'altra VM Windows per risolvere eventuali errori e quindi ricreare la VM originale. 

## <a name="recovery-process-overview"></a>Panoramica del processo di ripristino
I passaggi per la risoluzione dei problemi sono i seguenti:

1. Arrestare la VM interessata.
1. Creare uno snapshot per il disco del sistema operativo della VM.
1. Creare un disco rigido virtuale dallo snapshot.
1. Collegare e montare il disco rigido virtuale in un'altra VM Windows per risolvere i problemi riscontrati.
1. Connettersi alla macchina virtuale usata per la risoluzione dei problemi. Modificare i file o eseguire qualsiasi strumento per risolvere i problemi nel disco rigido virtuale originale.
1. Smontare e scollegare il disco rigido virtuale dalla macchina virtuale usata per la risoluzione dei problemi.
1. Scambiare il disco del sistema operativo per la macchina virtuale.

> [!NOTE]
> Questo articolo non si applica alle macchine virtuali con dischi non gestiti.

## <a name="take-a-snapshot-of-the-os-disk"></a>Acquisire uno snapshot del disco del sistema operativo
Uno snapshot è una copia completa di sola lettura di un disco rigido virtuale. È consigliabile arrestare la macchina virtuale prima di creare uno snapshot per cancellare tutti i processi in corso. Per creare uno snapshot di un disco del sistema operativo, attenersi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **macchine virtuali** dalla barra laterale, quindi selezionare la macchina virtuale in cui si è verificato il problema.
1. Nel riquadro sinistro selezionare **dischi** e quindi selezionare il nome del disco del sistema operativo.
    ![Screenshot che mostra il nome del disco del sistema operativo nelle impostazioni del disco.](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. Nella pagina **Panoramica** del disco del sistema operativo selezionare **Crea snapshot**.
1. Creare uno snapshot nella stessa posizione del disco del sistema operativo.

## <a name="create-a-disk-from-the-snapshot"></a>Creare un disco dallo snapshot
Per creare un disco dallo snapshot, attenersi alla procedura seguente:

1. Selezionare **cloud Shell** dalla portale di Azure.

    ![Screenshot che mostra il pulsante per l'apertura Azure Cloud Shell.](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. Eseguire i comandi di PowerShell riportati di seguito per creare un disco gestito dallo snapshot. Sostituire i nomi di esempio con i nomi appropriati.

    ```powershell
    #Provide the name of your resource group.
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create managed disks.
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of the managed disk.
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in gigabytes. It should be greater than the VHD file size. In this example, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for the managed disk: Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'
    
    #Provide the Azure region (for example, westus) where the managed disks will be located.
    #This location should be the same as the snapshot location.
    #Get all the Azure locations by using this command:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. Se i comandi vengono eseguiti correttamente, il nuovo disco verrà visualizzato nel gruppo di risorse specificato.

## <a name="attach-the-disk-to-another-vm"></a>Connetti il disco a un'altra macchina virtuale
Nei passaggi successivi viene utilizzata un'altra macchina virtuale per la risoluzione dei problemi. Dopo aver collegato il disco alla macchina virtuale per la risoluzione dei problemi, è possibile esplorare e modificare il contenuto del disco. Questo processo consente di correggere eventuali errori di configurazione o di esaminare ulteriori file di registro di sistema o dell'applicazione. Per aggiungere il disco a un'altra macchina virtuale, seguire questa procedura:

1. Selezionare il gruppo di risorse dal portale e quindi selezionare la macchina virtuale per la risoluzione dei problemi. Selezionare i **dischi**  >  **modifica**  >  **Aggiungi disco dati**.

    ![Screenshot che mostra le selezioni per il fissaggio di un disco esistente nel portale.](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Nell'elenco **dischi dati** selezionare il disco del sistema operativo della macchina virtuale identificata. Se il disco del sistema operativo non viene visualizzato, assicurarsi che la macchina virtuale per la risoluzione dei problemi e il disco del sistema operativo si trovino nella stessa area (località). 
3. Selezionare **Save (Salva** ) per applicare le modifiche.

## <a name="mount-the-attached-data-disk-to-the-vm"></a>Montare il disco dati collegato alla macchina virtuale

1. Aprire una connessione Desktop remoto alla macchina virtuale per la risoluzione dei problemi. 
2. Nella VM per la risoluzione dei problemi aprire **Server Manager**, quindi selezionare **Servizi file e archiviazione**. 

    ![Screenshot che mostra la selezione di servizi file e archiviazione all'interno Server Manager.](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. Il disco dati viene automaticamente rilevato e collegato. Per visualizzare un elenco dei dischi connessi, selezionare **Dischi**. È possibile selezionare il disco dati per visualizzare informazioni sul volume, inclusa la lettera di unità. Nell'esempio seguente viene illustrato il disco dati collegato e viene utilizzata l'unità **F**.

    ![Screenshot che mostra un disco collegato e informazioni sul volume in Server Manager.](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)

## <a name="fix-problems-on-the-original-virtual-hard-disk"></a>Risolvere i problemi nel disco rigido virtuale originale
Dopo aver montato il disco rigido virtuale eseguire tutte le operazioni di manutenzione e i passaggi necessari per la risoluzione dei problemi. Dopo aver risolto tutti gli errori, continuare con i passaggi seguenti.

## <a name="unmount-and-detach-the-original-virtual-hard-disk"></a>Smontare e scollegare il disco rigido virtuale originale
Scollegare il disco rigido virtuale esistente dalla VM per la risoluzione dei problemi. Non è possibile usare il disco rigido virtuale con altre VM finché non viene rilasciato il lease che connette il disco rigido virtuale alla macchina virtuale per la risoluzione dei problemi.

1. Dalla sessione Desktop remoto alla VM, aprire **Server Manager** e quindi selezionare **Servizi file e archiviazione**.

    ![Screenshot che mostra la selezione di servizi file e archiviazione in Server Manager.](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Selezionare **dischi**, fare clic con il pulsante destro del mouse sul disco dati, quindi scegliere **porta offline**.

    ![Screenshot che mostra l'impostazione del disco dati come offline in Server Manager.](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Scollegare il disco rigido virtuale dalla macchina virtuale. Selezionare la macchina virtuale nella portale di Azure, quindi selezionare **dischi**. 
4. Selezionare **modifica**, selezionare il disco del sistema operativo collegato, quindi selezionare **Elimina**.

    ![Screenshot che mostra le selezioni per lo scollegamento di un disco rigido virtuale esistente.](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Prima di continuare, attendere il completamento dell'eliminazione del disco dati nella macchina virtuale.

## <a name="swap-the-os-disk-for-the-vm"></a>Scambiare il disco del sistema operativo per la macchina virtuale

Portale di Azure supporta ora la modifica del disco del sistema operativo della macchina virtuale. A tale scopo, attenersi alla seguente procedura:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **macchine virtuali** dalla barra laterale, quindi selezionare la macchina virtuale in cui si è verificato il problema.
1. Nel riquadro sinistro selezionare **dischi** e quindi fare clic su **Scambia disco del sistema operativo**.
   
    ![Screenshot che mostra il pulsante per lo scambio di un disco del sistema operativo nel portale di Azure.](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Scegliere il nuovo disco che è stato riparato, quindi immettere il nome della macchina virtuale per confermare la modifica. Se il disco non viene visualizzato nell'elenco, attendere da 10 a 15 minuti dopo lo scollegamento del disco dalla macchina virtuale per la risoluzione dei problemi. Assicurarsi inoltre che il disco si trovi nello stesso percorso della macchina virtuale.
1. Selezionare **OK**.

## <a name="next-steps"></a>Passaggi successivi
Se si verificano problemi di connessione alla VM, vedere [risolvere i problemi relativi alle connessioni Desktop remoto a una macchina virtuale di Azure](troubleshoot-rdp-connection.md). Per problemi relativi all'accesso alle applicazioni in esecuzione nella VM, vedere risolvere i problemi di [connettività delle applicazioni in una macchina virtuale Windows](troubleshoot-app-connection.md).

Per ulteriori informazioni sull'utilizzo di Azure Resource Manager, vedere la [Panoramica di Azure Resource Manager](../../azure-resource-manager/management/overview.md).


