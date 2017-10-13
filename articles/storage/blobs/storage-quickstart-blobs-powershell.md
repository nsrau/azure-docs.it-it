---
title: Guida rapida di Azure - Trasferire oggetti da e verso Archiviazione BLOB di Azure con PowerShell | Microsoft Docs
description: Apprendere rapidamente a trasferire oggetti da e verso Archiviazione BLOB di Azure con PowerShell
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/19/2017
ms.author: robinsh
ms.openlocfilehash: 1a9941b21b92c70dd0a46ce2e4c75142e1786650
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-azure-powershell"></a>Trasferire oggetti da e verso la risorsa di archiviazione BLOB di Azure usando Azure PowerShell

Il modulo Azure PowerShell viene usato per creare e gestire le risorse di Azure dalla riga di comando di PowerShell o negli script. Questa guida descrive l'uso di PowerShell per trasferire file tra dischi locali e l'archiviazione BLOB di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Questa guida introduttiva richiede il modulo Azure PowerShell 3.6 o versioni successive. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Creare un contenitore

Gli elementi BLOB vengono sempre caricati in un contenitore. Ciò consente di organizzare i gruppi di BLOB come si organizzano i file in cartelle sul computer.

Impostare il nome del contenitore, quindi creare il contenitore usando [New-AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer), impostando le autorizzazioni su "Blob"per consentire l'accesso pubblico dei file. Il nome del contenitore in questo esempio è *quickstartblobs*.

```powershell
$containerName = "quickstartblobs"
New-AzureStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Caricare i BLOB nel contenitore

L'archiviazione BLOB supporta BLOB in blocchi, BLOB di aggiunta e BLOB di pagine. I file VHD usati per il backup di macchine virtuali IaaS sono BLOB di pagine. I BLOB di accodamento sono usati per la registrazione, ad esempio quando si vuole scrivere in un file e poi continuare ad aggiungere altre informazioni. La maggior parte dei file presenti nell'archiviazione BLOB è costituita da BLOB in blocchi. 

Per caricare un file in un BLOB in blocchi, ottenere un riferimento a un contenitore e quindi un riferimento al BLOB in blocchi nel contenitore. Dopo aver creato il riferimento al BLOB, è possibile caricarvi i dati con [Set-AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent). Questa operazione consente di creare il BLOB se non esiste o di sovrascriverlo se esiste già.

Gli esempi seguenti caricano i file Image001.jpg e Image002.png dalla cartella D:\\_TestImages sul disco locale del contenitore appena creato.

```powershell
# upload a file
Set-AzureStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload another file
Set-AzureStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx
```

Caricare tutti i file desiderati prima di continuare.

## <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

Ottenere un elenco di BLOB nel contenitore usando [Get AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob). Questo esempio mostra solo i nomi dei blob caricati.

```powershell
Get-AzureStorageBlob -Container $ContainerName -Context $ctx | select Name 
```

## <a name="download-blobs"></a>Scaricare BLOB

Scaricare i BLOB sul disco locale. Per ogni BLOB da scaricare, impostare il nome e usare [Get-AzureStorageBlobContent](/powershell/module/azure.storage/get-azurestorageblobcontent).

Questo esempio scarica i BLOB in D:\\_TestImages\Downloads sul disco locale. 

```powershell
# download first blob
Get-AzureStorageBlobContent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzureStorageBlobContent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 
```

## <a name="data-transfer-with-azcopy"></a>Trasferimento dati con AzCopy

L'utility [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) è un'altra opzione per il trasferimento di dati script ad alte prestazioni per Archiviazione di Azure. È possibile usare AzCopy per trasferire i dati da e verso risorse di archiviazione BLOB, file e tabelle.

Questo esempio rapido visualizza il comando AzCopy per il caricamento del file *myfile.txt* nel contenitore *mystoragecontainer* da una finestra di PowerShell.

```PowerShell
./AzCopy `
    /Source:C:\myfolder `
    /Dest:https://mystorageaccount.blob.core.windows.net/mystoragecontainer `
    /DestKey:<storage-account-access-key> `
    /Pattern:"myfile.txt"
```

## <a name="clean-up-resources"></a>Pulire le risorse

Rimuovere tutte le risorse create. Il modo più semplice per eseguire questa operazione consiste nell'eliminare il gruppo di risorse. Così facendo vengono eliminate anche tutte le risorse in esso contenute. In questo caso, verranno rimossi l'account di archiviazione creato e il gruppo di risorse stesso.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida rapida è stato descritto il trasferimento file tra il disco locale e un'archiviazione BLOB di Azure. Per altre informazioni sull'uso dell'archiviazione BLOB, continuare la procedura relativa all'archiviazione BLOB.

> [!div class="nextstepaction"]
> [Procedura relativa alle operazioni di archiviazione BLOB](storage-how-to-use-blobs-powershell.md)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Riferimenti cmdlet di archiviazione per Microsoft Azure PowerShell
* [Cmdlet di PowerShell per l'archiviazione](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) è un'app autonoma gratuita di Microsoft che consente di rappresentare facilmente dati di Archiviazione di Azure in Windows, macOS e Linux.