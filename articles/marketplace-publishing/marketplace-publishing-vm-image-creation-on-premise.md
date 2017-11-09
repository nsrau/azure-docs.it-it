---
title: Creazione di un'immagine di macchina virtuale in locale per Azure Marketplace | Microsoft Docs
description: Comprendere ed eseguire i passaggi per creare un'immagine di VM in locale e distribuire in Azure Marketplace per l'acquisto da parte di altri utenti.
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 26dfbd5a-8685-4b19-987e-c20ca60540ec
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: hascipio; v-divte
ms.openlocfilehash: 8f6b9a9293dc149586e6e5fd55028170ea825b07
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="develop-an-on-premises-virtual-machine-image-for-the-azure-marketplace"></a>Sviluppare l'immagine di una macchina virtuale in locale per Azure Marketplace
Si consiglia di sviluppare i dischi rigidi virtuali (VHD, Virtual Hard Disk) di Azure direttamente nel cloud utilizzando Remote Desktop Protocol. Tuttavia, se necessario, è possibile scaricare un VHD e svilupparlo utilizzando l'infrastruttura locale.  

Per lo sviluppo locale, è necessario scaricare il VHD del sistema operativo della macchina virtuale creata. Questi passaggi avverrebbero come parte del passaggio 3.3 precedente.  

## <a name="download-a-vhd-image"></a>Scaricare un'immagine di VHD 
### <a name="locate-a-blob-url"></a>Individuare l'URL BLOB
Per scaricare il VHD, individuare innanzitutto l'URL BLOB per il disco del sistema operativo.

Individuare l'URL BLOB dal nuovo [portale di Microsoft Azure](https://portal.azure.com):

1. Passare a **Sfoglia** > **VM** e selezionare la VM distribuita.
2. In **Configura** selezionare il riquadro **Dischi**, che apre il pannello Dischi.
   
   ![disegno](media/marketplace-publishing-vm-image-creation-on-premise/img01.png)
3. Selezionare il **disco del sistema operativo**. Verrà aperto un altro pannello che visualizza le proprietà del disco, compresa la posizione del VHD.
4. Copiare questo URL blob.
   
   ![disegno](media/marketplace-publishing-vm-image-creation-on-premise/img02.png)
5. A questo punto, eliminare la macchina virtuale distribuita senza eliminare i dischi di backup. È inoltre possibile arrestare la macchina virtuale, anziché eliminarla. Non scaricare il VHD del sistema operativo quando la macchina virtuale è in esecuzione.
   
   ![disegno](media/marketplace-publishing-vm-image-creation-on-premise/img03.png)

### <a name="download-a-vhd"></a>Scaricare il VHD
Quando si conosce l'URL BLOB, è possibile scaricare il VHD utilizzando il [portale di Azure](http://manage.windowsazure.com/) o PowerShell.  

> [!NOTE]
> Al momento della creazione della guida, la funzionalità per scaricare un VHD non è ancora presente nel nuovo portale di Microsoft Azure.  
> 
> 

**Scaricare il VHD del sistema operativo tramite il [portale di Azure](http://manage.windowsazure.com/)** corrente

1. Accedere al portale di Azure, se questa operazione non è già stata eseguita.
2. Fare clic sulla scheda **Archiviazione** .
3. Selezionare l'account di archiviazione in cui è archiviato il VHD.
   
   ![disegno](media/marketplace-publishing-vm-image-creation-on-premise/img04.png)
4. Questo visualizza le proprietà dell'account di archiviazione. Selezionare la scheda **Contenitori** .
   
   ![disegno](media/marketplace-publishing-vm-image-creation-on-premise/img05.png)
5. Selezionare il contenitore in cui è archiviato il VHD. Per impostazione predefinita, quando creato dal portale, il VHD viene archiviato in un contenitore di VHD.
   
   ![disegno](media/marketplace-publishing-vm-image-creation-on-premise/img06.png)
6. Selezionare il VHD del sistema operativo corretto confrontando l'URL con quello salvato.
7. Fare clic su **Download**.
   
   ![disegno](media/marketplace-publishing-vm-image-creation-on-premise/img07.png)

### <a name="download-a-vhd-by-using-powershell"></a>Scaricare il VHD mediante PowerShell
Oltre al portale di Azure, è possibile usare il cmdlet [Save-AzureVhd](http://msdn.microsoft.com/library/dn495297.aspx) per scaricare il VHD del sistema operativo.

        Save-AzureVhd –Source <storageURIOfVhd> `
        -LocalFilePath <diskLocationOnWorkstation> `
        -StorageKey <keyForStorageAccount>
Ad esempio, Save-AzureVhd -Source “https://baseimagevm.blob.core.windows.net/vhds/BaseImageVM-6820cq00-BaseImageVM-os-1411003770191.vhd” -LocalFilePath “C:\Users\Administrator\Desktop\baseimagevm.vhd” -StorageKey <String>

> [!NOTE]
> **Save-AzureVhd** dispone inoltre dell'opzione **NumberOfThreads**, che può essere usata per aumentare il parallelismo per un uso ottimale della larghezza di banda disponibile per il download.
> 
> 

## <a name="upload-vhds-to-an-azure-storage-account"></a>Caricare VHD in un account di archiviazione di Azure
Se i VHD sono stati preparati in locale, è necessario caricarli in un account di archiviazione in Azure. Questo passaggio avviene dopo la creazione del VHD locale, ma prima di ottenere una certificazione per l'immagine della macchina virtuale.

### <a name="create-a-storage-account-and-container"></a>Creare un account di archiviazione e un contenitore
Si consiglia di caricare i VHD in un account di archiviazione in un'area negli Stati Uniti. Tutti i VHD per un unico codice SKU devono essere posizionati in un singolo contenitore all'interno di un singolo account di archiviazione.

Per creare un account di archiviazione è possibile utilizzare il [portale di Microsoft Azure](https://portal.azure.com/), PowerShell o lo strumento da riga di comando per Linux.  

**Creare un account di archiviazione dal portale di Microsoft Azure**

1. Fare clic su **New**.
2. Selezionare **Archiviazione**.
3. Immettere il nome dell’account di archiviazione e quindi selezionare il percorso.
   
   ![disegno](media/marketplace-publishing-vm-image-creation-on-premise/img08.png)
4. Fare clic su **Crea**.
5. Il pannello dell'account di archiviazione creato deve essere aperto. In caso contrario, selezionare **Sfoglia** > **Account di archiviazione**. Nel pannello Account di archiviazione selezionare l'account di archiviazione creato.
6. Selezionare **Contenitori**.
   
   ![disegno](media/marketplace-publishing-vm-image-creation-on-premise/img09.png) 
7. Nel pannello Contenitori selezionare **Aggiungi**e quindi immettere un nome e le autorizzazioni per il contenitore. Selezionare **Privato** per le autorizzazioni del contenitore.

> [!TIP]
> Si consiglia di creare un contenitore per ogni SKU che si intende pubblicare.
> 
> 

  ![disegno](media/marketplace-publishing-vm-image-creation-on-premise/img10.png)

### <a name="create-a-storage-account-by-using-powershell"></a>Creare un account di archiviazione tramite PowerShell
Usando PowerShell, creare un account di archiviazione con il cmdlet [New-AzureStorageAccount](http://msdn.microsoft.com/library/dn495115.aspx) .

        New-AzureStorageAccount -StorageAccountName “mystorageaccount” -Location “West US”

Sarà quindi possibile creare un contenitore in tale account di archiviazione usando il cmdlet [NewAzureStorageContainer](http://msdn.microsoft.com/library/dn495291.aspx) .

        New-AzureStorageContainer -Name “containername” -Permission “Off”

> [!NOTE]
> Tali comandi presuppongono che il contesto dell’account di archiviazione corrente sia già stato impostato in PowerShell.   Per ulteriori dettagli sulla configurazione di PowerShell fare riferimento a [Configurazione di Azure PowerShell](marketplace-publishing-powershell-setup.md) .  
> 
> ### <a name="create-a-storage-account-by-using-the-command-line-tool-for-mac-and-linux"></a>Creare un account di archiviazione con lo strumento da riga di comando per Mac e Linux
> Dallo [strumento da riga di comando per Linux](../virtual-machines/linux/cli-manage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)creare un account di archiviazione come segue.
> 
> 

        azure storage account create mystorageaccount --location "West US"

Creare un contenitore come segue.

        azure storage container create containername --account-name mystorageaccount --accountkey <accountKey>

## <a name="upload-a-vhd"></a>Caricare il VHD
Dopo aver creato l'account di archiviazione e il contenitore, è possibile caricare i VHD preparati. È possibile usare PowerShell, lo strumento da riga di comando per Linux o altri strumenti di gestione di Archiviazione di Azure.

### <a name="upload-a-vhd-via-powershell"></a>Caricare un VHD tramite PowerShell
Usare il cmdlet [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx) .

        Add-AzureVhd –Destination “http://mystorageaccount.blob.core.windows.net/containername/vmsku.vhd” -LocalFilePath “C:\Users\Administrator\Desktop\vmsku.vhd”

### <a name="upload-a-vhd-by-using-the-command-line-tool-for-mac-and-linux"></a>Caricare un VHD utilizzando lo strumento da riga di comando per Mac e Linux
Con lo [strumento da riga di comando per Linux](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2), usare il comando indicato di seguito: azure vm image create <image name> --location <Location of the data center> --OS Linux <LocationOfLocalVHD>

## <a name="see-also"></a>Vedere anche
* [Creazione di un'immagine di macchina virtuale per Marketplace](marketplace-publishing-vm-image-creation.md)
* [Configurazione di Azure PowerShell](marketplace-publishing-powershell-setup.md)

