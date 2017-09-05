---
title: Come usare PowerShell per gestire Archiviazione file di Azure| Microsoft Docs
description: Informazioni su come usare PowerShell per gestire Archiviazione file di Azure.
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: ce62d4423ce711a6902aed7b8174ff4e827f6083
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---
# <a name="how-to-use-powershell-to-manage-azure-file-storage"></a>Come usare PowerShell per gestire Archiviazione file di Azure
È possibile usare Azure PowerShell per creare e gestire le condivisioni file.

## <a name="install-the-powershell-cmdlets-for-azure-storage"></a>Installare i cmdlet di PowerShell per l'archiviazione di Azure
Per prepararsi all'uso di PowerShell, scaricare e installare i cmdlet di Azure PowerShell. Vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs) per le istruzioni relative al punto di installazione e all’installazione.

> [!NOTE]
> Si consiglia di scaricare e installare oppure aggiornare il modulo alla versione di Azure PowerShell più recente.
> 
> 

Per aprire una finestra di Azure PowerShell, fare clic su **Start** e digitare **Windows PowerShell**. La finestra di PowerShell carica automaticamente il modulo Azure PowerShell.

## <a name="create-a-context-for-your-storage-account-and-key"></a>Creare un contesto per l'account e la chiave di archiviazione
Creare il contesto dell'account di archiviazione. Il contesto incapsula il nome e la chiave dell'account di archiviazione. Per istruzioni sulla copia della chiave dell'account dal [portale di Azure](https://portal.azure.com), vedere [Visualizzare e copiare le chiavi di accesso alle risorse di archiviazione](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#view-and-copy-storage-access-keys).

Sostituire `storage-account-name` e `storage-account-key` con il nome e la chiave dell'account di archiviazione nell'esempio seguente.

```powershell
# create a context for account and key
$ctx=New-AzureStorageContext storage-account-name storage-account-key
```

## <a name="create-a-new-file-share"></a>Creare una nuova condivisione file
Creare la nuova condivisione, denominata `logs`.

```powershell
# create a new share
$s = New-AzureStorageShare logs -Context $ctx
```

A questo punto si ha una condivisione file nell'archiviazione file. Vengono quindi aggiunti un file e una directory.

> [!IMPORTANT]
> Il nome della condivisione file deve essere composto solo da caratteri minuscoli. Per dettagli su come denominare condivisioni e file, vedere [Denominazione e riferimento a condivisioni, directory, file e metadati](https://msdn.microsoft.com/library/azure/dn167011.aspx).
> 
> 

## <a name="create-a-directory-in-the-file-share"></a>Creare una directory nella condivisione file
Creare una directory nella condivisione. Nell'esempio seguente la directory è denominata `CustomLogs`.

```powershell
# create a directory in the share
New-AzureStorageDirectory -Share $s -Path CustomLogs
```

## <a name="upload-a-local-file-to-the-directory"></a>Caricare un file locale nella directory
Caricare un file locale nella directory. Nel seguente esempio viene caricato un file da `C:\temp\Log1.txt`. Modificare il percorso del file in modo che punti a un file valido nel computer locale.

```powershell
# upload a local file to the new directory
Set-AzureStorageFileContent -Share $s -Source C:\temp\Log1.txt -Path CustomLogs
```

## <a name="list-the-files-in-the-directory"></a>Elencare i file nella directory
Per visualizzare un file nella directory, è possibile elencare tutti i file della directory. Questo comando restituisce file e sottodirectory, se presenti, nella directory CustomLogs.

```powershell
# list files in the new directory
Get-AzureStorageFile -Share $s -Path CustomLogs | Get-AzureStorageFile
```

Get-AzureStorageFile restituisce un elenco di file e directory per qualsiasi oggetto di directory passato. "Get-AzureStorageFile -Share $s" restituisce un elenco di file e directory nella directory radice. Per ottenere un elenco di file in una sottodirectory, è necessario passare la sottodirectory a Get-AzureStorageFile. La prima parte del comando fino alla pipe restituisce un'istanza di directory della sottodirectory CustomLogs. Il comando viene quindi passato a Get-AzureStorageFile, che restituisce i file e le directory in CustomLogs.

## <a name="copy-files"></a>Copiare i file
A partire dalla versione 0.9.7 di Azure PowerShell, è possibile copiare un file in un altro file, un file in un BLOB o un BLOB in un file. Di seguito viene illustrato come eseguire queste operazioni di copia con i cmdlet di PowerShell.

```powershell
# copy a file to the new directory
Start-AzureStorageFileCopy -SrcShareName srcshare -SrcFilePath srcdir/hello.txt -DestShareName destshare -DestFilePath destdir/hellocopy.txt -Context $srcCtx -DestContext $destCtx

# copy a blob to a file directory
Start-AzureStorageFileCopy -SrcContainerName srcctn -SrcBlobName hello2.txt -DestShareName hello -DestFilePath hellodir/hello2copy.txt -DestContext $ctx -Context $ctx
```
## <a name="next-steps"></a>Passaggi successivi
Vedere i collegamenti seguenti per ulteriori informazioni sull'archiviazione file di Azure.

* [Domande frequenti](../storage-files-faq.md)
* [Risoluzione dei problemi in Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Risoluzione dei problemi in Linux](storage-troubleshoot-linux-file-connection-problems.md)    
