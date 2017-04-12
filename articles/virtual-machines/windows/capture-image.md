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
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 114e97bcd38fc69d62960c3305dc7f75ce13b66b
ms.lasthandoff: 04/03/2017


---
# <a name="how-to-capture-a-vm-image-from-a-generalized-azure-vm"></a>Come acquisire un'immagine di VM da una VM generalizzata di Azure
Questo articolo illustra come usare Azure PowerShell per creare un'immagine di una VM generalizzata di Azure. È quindi possibile usare l'immagine per creare un'altra VM. Questa immagine include il disco del sistema operativo e i dischi dati collegati alla macchina virtuale. L'immagine non include le risorse della rete virtuale, quindi è necessario configurare queste risorse quando si crea la nuova VM. 

## <a name="prerequisites"></a>Prerequisiti
* È necessario disporre già della [VM generalizzata](generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Generalizzando una VM si rimuovono anche tutte le informazioni personali sull'account e si prepara la macchina da usare come immagine. È possibile anche generalizzare una VM Linux tramite `sudo waagent -deprovision+user` e quindi usare PowerShell per acquisire la VM. Per informazioni sull'uso dell'interfaccia della riga di comando per acquisire una VM, vedere [Come generalizzare e acquisire una macchina virtuale Linux con l'interfaccia della riga di comando di Azure](../linux/capture-image.md)
* È necessario che sia installato Azure PowerShell 1.0.x o una versione più recente. Se non è già stato installato PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs) per la procedura di installazione.

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


