---
title: Creare un&quot;immagine di VM da una VM generalizzata di Azure | Microsoft Docs
description: Informazioni su come creare un&quot;immagine di VM da una VM generalizzata di Azure creata nel modello di distribuzione di Resource Manager
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: afdae4a1-6dfb-47b4-902a-f327f9bfe5b4
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 8f1d488fd8f71bf90c8bf7b7c1544445ffbd7686
ms.contentlocale: it-it
ms.lasthandoff: 05/26/2017


---
# <a name="how-to-capture-a-vm-image-from-a-generalized-azure-vm"></a>Come acquisire un'immagine di VM da una VM generalizzata di Azure
Questo articolo illustra come usare Azure PowerShell per creare un'immagine di una VM generalizzata di Azure. È quindi possibile usare l'immagine per creare un'altra VM. Questa immagine include il disco del sistema operativo e i dischi dati collegati alla macchina virtuale. L'immagine non include le risorse della rete virtuale, quindi è necessario configurare queste risorse quando si crea la nuova VM. 

## <a name="prerequisites"></a>Prerequisiti
È necessario che sia installato Azure PowerShell 1.0.x o una versione più recente. Se non è già stato installato PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview) per la procedura di installazione.

## <a name="generalize-the-windows-vm-using-sysprep"></a>Generalizzare la macchina virtuale Windows con Sysprep

Sysprep rimuove anche tutte le informazioni sull'account personale e prepara la VM da usare come immagine. Per altre informazioni su Sysprep, vedere [Come usare Sysprep: Introduzione](http://technet.microsoft.com/library/bb457073.aspx).

Assicurarsi che i ruoli server in esecuzione sulla macchina siano supportati da Sysprep. Per ulteriori informazioni, vedere [Supporto Sysprep per i ruoli server](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Se si esegue Sysprep prima di caricare il disco rigido virtuale in Azure per la prima volta, verificare di aver [preparato la VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) prima di eseguire Sysprep. 
> 
> 

1. Accedere alla macchina virtuale Windows.
2. Aprire la finestra del prompt dei comandi come amministratore. Impostare la directory su **%windir%\system32\sysprep**, quindi eseguire `sysprep.exe`.
3. Nella finestra di dialogo **Utilità preparazione sistema** selezionare **Passare alla Configurazione guidata** e verificare che la casella di controllo **Generalizza** sia selezionata.
4. In **Opzioni di arresto del sistema** selezionare **Arresta il sistema**.
5. Fare clic su **OK**.
   
    ![Avvio di Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Al termine, Sysprep arresta la macchina virtuale. Non riavviare la VM.


È possibile anche generalizzare una VM Linux tramite `sudo waagent -deprovision+user` e quindi usare PowerShell per acquisire la VM. Per informazioni sull'uso dell'interfaccia della riga di comando per acquisire una VM, vedere [Come generalizzare e acquisire una macchina virtuale Linux con l'interfaccia della riga di comando di Azure](../linux/capture-image.md)

## <a name="log-in-to-azure-powershell"></a>Accedere ad Azure PowerShell
1. Aprire Azure PowerShell e accedere al proprio account di Azure.
   
    ```powershell
    Login-AzureRmAccount
    ```
   
    Verrà visualizzata una finestra popup in cui immettere le credenziali dell'account Azure.
2. Ottenere l'ID di sottoscrizione per le sottoscrizioni disponibili.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Impostare la sottoscrizione corretta utilizzandone l'ID.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Deallocare la VM e impostare lo stato generalizzato
1. Deallocare le risorse della VM.
   
    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    Nel portale di Azure lo *stato* della VM passa da **Arrestato** ad **Arrestato (deallocato)**.
2. Impostare lo stato della macchina virtuale su **Generalizzato**. 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Controllare lo stato della VM. Nella sezione **OSState/generalized** per la VM, il valore **DisplayStatus** dovrebbe essere impostato su **Macchina virtuale generalizzata**.  
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Creare l'immagine
1. Copiare l'immagine della macchina virtuale nel contenitore di archiviazione di destinazione usando questo comando. L'immagine viene creata nello stesso account di archiviazione della macchina virtuale originale. Il parametro `-Path` salva una copia del modello JSON in locale. Il parametro `-DestinationContainerName` è il nome del contenitore in cui si vuole salvare le immagini. Se il contenitore non esiste, verrà creato.
   
    ```powershell
    Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
        -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
        -Path <C:\local\Filepath\Filename.json>
    ```
   
    È possibile ottenere l'URL dell'immagine dal modello del file JSON. Passare alla sezione **resources** > **storageProfile** > **osDisk** > **image** > **uri** per il percorso completo dell'immagine. L'URL dell'immagine è simile a questo: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
   
    È anche possibile verificare l'URI nel portale. L'immagine viene copiata in un contenitore denominato **system** nell'account di archiviazione. 

## <a name="next-steps"></a>Passaggi successivi
* È ora possibile [creare una VM dall'immagine](create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


