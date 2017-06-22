---
title: Creare e caricare un&quot;immagine di VM con Powershell | Microsoft Docs
description: Informazioni su come creare e caricare un&quot;immagine Windows Server generalizzata (VHD) utilizzando il modello di distribuzione classica e Azure Powershell.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 8c4a08fe-7714-4bf0-be87-c728a7806d3f
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 1d2b51f2a8a03a94b7ce92af54f89128bcfc9471
ms.contentlocale: it-it
ms.lasthandoff: 05/26/2017


---
# <a name="create-and-upload-a-windows-server-vhd-to-azure"></a>Creazione e caricamento di un disco rigido virtuale con Windows Server in Azure
Questo articolo illustra come caricare la propria immagine VM generalizzata come un disco rigido virtuale (VHD) in modo da usarlo per la creazione di macchine virtuali. Per informazioni dettagliate sui dischi e sui dischi rigidi virtuali in Microsoft Azure, vedere [Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali](../../../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!IMPORTANT]
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. È anche possibile [caricare](../upload-generalized-managed.md) una macchina virtuale usando il modello di Resource Manager.

## <a name="prerequisites"></a>Prerequisiti
Questo articolo presuppone che l'utente abbia:

* **Una sottoscrizione di Azure** : se non si ha tale sottoscrizione, [è possibile aprire un account Azure per ottenerne gratuitamente una](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
* **[Microsoft Azure PowerShell](/powershell/azure/overview)**: è necessario che il modulo di Microsoft Azure PowerShell sia installato e configurato per l'uso della sottoscrizione.
* **Un file .VHD** : una versione supportata del sistema operativo Windows archiviata in un file con .vhd e collegata a una macchina virtuale. Verificare inoltre che i ruoli del server in esecuzione sul disco rigido virtuale siano supportati da Sysprep. Per ulteriori informazioni, vedere [Supporto Sysprep per i ruoli server](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

    > [!IMPORTANT]
    > il formato VHDX non è supportato in Microsoft Azure. È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il [cmdlet Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx). Per informazioni dettagliate, vedere questo [post di blog](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).

## <a name="step-1-prep-the-vhd"></a>Passaggio 1: Preparare il disco rigido virtuale
Prima di caricare il disco rigido virtuale in Azure, è necessario generalizzarlo usando lo strumento Sysprep, che prepara il disco rigido virtuale in modo che possa essere usato come immagine. Per altre informazioni su Sysprep, vedere [Come usare Sysprep: Introduzione](http://technet.microsoft.com/library/bb457073.aspx). Eseguire il backup della VM prima di eseguire Sysprep.

Dalla macchina virtuale su cui è stato installato il sistema operativo, completare la seguente procedura:

1. Accedere al sistema operativo.
2. Aprire una finestra del prompt dei comandi come amministratore. Impostare la directory su **%windir%\system32\sysprep**, quindi eseguire `sysprep.exe`.

    ![Apertura della finestra del Prompt dei comandi](./media/createupload-vhd/sysprep_commandprompt.png)
3. Verrà visualizzata la finestra di dialogo **Utilità preparazione sistema** .

   ![Avvio di Sysprep](./media/createupload-vhd/sysprepgeneral.png)
4. In **Utilità preparazione sistema** selezionare **Passare alla Configurazione guidata** e assicurarsi che l'opzione **Generalizza** sia selezionata.
5. In **Opzioni di arresto del sistema** selezionare **Arresta il sistema**.
6. Fare clic su **OK**.

## <a name="step-2-create-a-storage-account-and-a-container"></a>Passaggio 2: Creare un account di archiviazione e un contenitore
È necessario un account di archiviazione di Azure in cui caricare il file con estensione vhd. Questo passaggio illustra come creare un account o come ottenere le informazioni necessarie da un account esistente. Sostituire le variabili tra &lsaquo; parentesi &rsaquo; con le proprie informazioni.

1. Login

    ```powershell
    Add-AzureAccount
    ```

2. Impostare la sottoscrizione di Azure.

    ```powershell
    Select-AzureSubscription -SubscriptionName <SubscriptionName>
    ```

3. Creare un nuovo account di archiviazione. Il nome dell'account di archiviazione deve essere univoco e composto da minimo 3 e massimo 24 caratteri. Il nome può essere qualsiasi combinazione di lettere e numeri. È inoltre necessario specificare una posizione come "East US"

    ```powershell
    New-AzureStorageAccount –StorageAccountName <StorageAccountName> -Location <Location>
    ```

4. Impostare il nuovo account di archiviazione come predefinito.

    ```powershell
    Set-AzureSubscription -CurrentStorageAccountName <StorageAccountName> -SubscriptionName <SubscriptionName>
    ```

5. Creare un nuovo contenitore.

    ```powershell
    New-AzureStorageContainer -Name <ContainerName> -Permission Off
    ```

## <a name="step-3-upload-the-vhd-file"></a>Passaggio 3: Caricare il file .vhd
Usare [Add-AzureVhd](https://docs.microsoft.com/en-us/powershell/module/azure/add-azurevhd) per caricare il file VHD.

Nella finestra di Azure PowerShell usata nel passaggio precedente, digitare il comando seguente e sostituire le variabili tra &lsaquo; parentesi &rsaquo; con le proprie informazioni.

```powershell
Add-AzureVhd -Destination "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -LocalFilePath <LocalPathtoVHDFile>
```

## <a name="step-4-add-the-image-to-your-list-of-custom-images"></a>Passaggio 4: Aggiungere l'immagine all'elenco di immagini personalizzate
Usare il cmdlet [Add-AzureVMImage](https://docs.microsoft.com/en-us/powershell/module/azure/add-azurevmimage) per aggiungere l'immagine all'elenco di immagini personalizzate.

```powershell
Add-AzureVMImage -ImageName <ImageName> -MediaLocation "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -OS "Windows"
```

## <a name="next-steps"></a>Passaggi successivi
È ora possibile [creare una macchina virtuale personalizzata](createportal.md) usando l'immagine caricata.

