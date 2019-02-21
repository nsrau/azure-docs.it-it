---
title: Guida introduttiva di Azure - Creare un BLOB nell'archivio oggetti con Azure PowerShell | Microsoft Docs
description: In questa guida introduttiva si usa Azure PowerShell con l'archivio oggetti (BLOB). Si usa quindi PowerShell per caricare un BLOB in Archiviazione di Azure, scaricare un BLOB ed elencare i BLOB presenti in un contenitore.
services: storage
author: roygara
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: rogarana
ms.openlocfilehash: c3a85a668a398047e7daef8a2e07be261ff644c5
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447481"
---
# <a name="quickstart-upload-download-and-list-blobs-by-using-azure-powershell"></a>Guida introduttiva: Caricare, scaricare ed elencare BLOB con Azure PowerShell

Usare il modulo di Azure PowerShell per creare e gestire le risorse di Azure. Le risorse di Azure possono essere create e gestite tramite la riga di comando di PowerShell o mediante script. Questa guida descrive l'uso di PowerShell per trasferire file tra un disco locale e l'archivio BLOB di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per accedere ad Archiviazione di Azure è necessaria una sottoscrizione di Azure. Se non si ha già una sottoscrizione, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Per questa guida introduttiva è richiesto il modulo Azure PowerShell Az versione 0.7 o successiva. Eseguire `Get-InstalledModule -Name Az -AllVersions | select Name,Version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-Az-ps).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Creare un contenitore

Gli elementi BLOB vengono sempre caricati in un contenitore. È possibile organizzare i gruppi di BLOB in modo analogo a come si organizzano i file in cartelle sul computer.

Impostare il nome del contenitore e quindi creare il contenitore usando il comando [New-AzStorageContainer](/powershell/module/az.storage/new-AzStoragecontainer). Impostare le autorizzazioni su `blob` per consentire l'accesso pubblico ai file. Il nome del contenitore in questo esempio è *quickstartblobs*.

```powershell
$containerName = "quickstartblobs"
new-AzStoragecontainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Caricare i BLOB nel contenitore

L'archiviazione BLOB supporta BLOB in blocchi, BLOB di aggiunta e BLOB di pagine. I file VHD usati per il backup di macchine virtuali IaaS sono BLOB di pagine. Usare i BLOB di accodamento per la registrazione, ad esempio quando si vuole scrivere in un file e poi continuare ad aggiungere altre informazioni. La maggior parte dei file presenti nell'archiviazione BLOB è costituita da BLOB in blocchi. 

Per caricare un file in un BLOB in blocchi, ottenere un riferimento a un contenitore e quindi un riferimento al BLOB in blocchi nel contenitore. Dopo aver creato il riferimento al BLOB, è possibile caricarvi i dati con [Set-AzStorageBlobContent](/powershell/module/az.storage/set-AzStorageblobcontent). Questa operazione consentirà di creare il BLOB se non esistente o di sovrascriverlo se esistente.

Gli esempi seguenti caricano i file *Image001.jpg* e *Image002.png* dalla cartella *D:\\_TestImages* sul disco locale al contenitore creato.

```powershell
# upload a file
set-AzStorageblobcontent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload another file
set-AzStorageblobcontent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx
```

Caricare tutti i file desiderati prima di continuare.

## <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

Ottenere un elenco di BLOB nel contenitore usando [Get-AzStorageBlob](/powershell/module/az.storage/get-AzStorageblob). Questo esempio mostra solo i nomi dei blob caricati.

```powershell
Get-AzStorageBlob -Container $ContainerName -Context $ctx | select Name
```

## <a name="download-blobs"></a>Scaricare BLOB

Scaricare i BLOB sul disco locale. Per ogni BLOB da scaricare impostare il nome e chiamare [Get-AzStorageBlobContent](/powershell/module/az.storage/get-AzStorageblobcontent).

Questo esempio scarica i BLOB in *D:\\_TestImages\Downloads* sul disco locale. 

```powershell
# download first blob
Get-AzStorageblobcontent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzStorageblobcontent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx
```

## <a name="data-transfer-with-azcopy"></a>Trasferimento dati con AzCopy

L'utility [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) è un'altra opzione per il trasferimento di dati script ad alte prestazioni per Archiviazione di Azure. Usare AzCopy per trasferire i dati da e verso risorse di archiviazione BLOB, file e tabelle.

Questo esempio rapido visualizza il comando AzCopy per il caricamento del file *myfile.txt* nel contenitore *mystoragecontainer* da una finestra di PowerShell.

```PowerShell
./AzCopy `
    /Source:C:\myfolder `
    /Dest:https://mystorageaccount.blob.core.windows.net/mystoragecontainer `
    /DestKey:<storage-account-access-key> `
    /Pattern:"myfile.txt"
```

## <a name="clean-up-resources"></a>Pulire le risorse

Rimuovere tutte le risorse create. Il modo più semplice per eseguire questa operazione consiste nell'eliminare il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le risorse che contiene. Nell'esempio seguente la rimozione del gruppo di risorse rimuove l'account di archiviazione e il gruppo di risorse stesso.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva sono stati trasferiti file tra un disco locale e l'archivio BLOB di Azure. Per altre informazioni sull'uso dell'archivio BLOB con PowerShell, continuare con Usare Azure PowerShell con Archiviazione di Azure.

> [!div class="nextstepaction"]
> [Uso di Azure PowerShell con Archiviazione di Azure](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Riferimenti cmdlet di archiviazione per Microsoft Azure PowerShell

* [Cmdlet di PowerShell per l'archiviazione](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) è un'app autonoma gratuita di Microsoft che consente di rappresentare facilmente dati di Archiviazione di Azure in Windows, macOS e Linux.
