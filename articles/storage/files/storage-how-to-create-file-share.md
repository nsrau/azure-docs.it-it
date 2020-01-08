---
title: Creare una condivisione file di Azure
titleSuffix: Azure Files
description: Come creare una condivisione file di Azure usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ca4904940c7974e83445892b9597a9d3c36b44ee
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452880"
---
# <a name="create-a-file-share"></a>Creare una condivisione file
È possibile creare una condivisione in File di Azure usando il [portale di Azure](https://portal.azure.com/), i cmdlet di PowerShell per archiviazione di Azure, le librerie client di archiviazione di Azure o l'API REST di archiviazione di Azure. In questo articolo si apprenderà come:
- Creare una condivisione file di Azure usando il portale di Azure
- Creare una condivisione file di Azure usando PowerShell
- Creare una condivisione file di Azure usando l'interfaccia della riga di comando di Azure

## <a name="prerequisites"></a>Prerequisiti
Per creare una condivisione file di Azure, è possibile usare un account di archiviazione già esistente o [creare un nuovo account di archiviazione di Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Per creare una condivisione file di Azure con PowerShell, sono necessari la chiave dell'account e il nome dell'account di archiviazione. Se si prevede di usare PowerShell o l'interfaccia della riga di comando, è necessaria una chiave dell'account di archiviazione.

> [!NOTE]
> Per creare condivisioni file di dimensioni maggiori di 5 TiB, vedere [abilitare condivisioni file di grandi dimensioni](storage-files-how-to-create-large-file-share.md).

## <a name="create-a-file-share-through-the-azure-portal"></a>Creare una condivisione file tramite il portale di Azure

1. Passare al riquadro **account di archiviazione** nell'portale di Azure.
    riquadro dell'account di archiviazione ![](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

1. Selezionare il pulsante **+ condivisione file** .
    ![il pulsante Aggiungi condivisione file](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

1. Immettere le informazioni per **nome** e **quota**.
    ![il nome e la quota per la nuova condivisione file](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

1. Visualizzare la nuova condivisione file.
    ![la nuova condivisione file nell'interfaccia utente](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

1. Caricare un file.
    ![pulsante carica sulla barra di spostamento](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

1. Esplorare la condivisione file e gestire le directory e i file.
    visualizzazione ![cartelle delle condivisioni file](./media/storage-how-to-create-file-share/create-file-share-portal6.png)

## <a name="create-a-file-share-through-powershell"></a>Creare una condivisione file tramite PowerShell

Scaricare e installare i cmdlet di Azure PowerShell. Vedere [Come installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) per le istruzioni relative al punto di installazione e all'installazione.

> [!Note]  
> Si consiglia di scaricare e installare o eseguire l'aggiornamento a, il modulo Azure PowerShell più recente.

1. Creare un nuovo account di archiviazione.
    Un account di archiviazione è un pool condiviso di spazio di archiviazione in cui è possibile distribuire condivisioni file di Azure e risorse di archiviazione come BLOB o code.

    ```PowerShell
    $resourceGroup = "myresourcegroup"
    $storAcctName = "myuniquestorageaccount"
    $region = "westus2"
    $storAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storAcctName -SkuName Standard_LRS -Location $region -Kind StorageV2
    ```

1. Creare una nuova condivisione file.        
    ```PowerShell
    $shareName = "myshare"
    $share = New-AzStorageShare -Context $storAcct.Context -Name $shareName
    ```

> [!Note]  
> Il nome della condivisione file deve essere composto solo da caratteri minuscoli. Per informazioni complete sulla denominazione di condivisioni file e file, vedere [denominazione e riferimento a condivisioni, directory, file e metadati](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="create-a-file-share-through-the-cli"></a>Creare una condivisione file tramite l'interfaccia della riga di comando

1. Scaricare e installare l'interfaccia della riga di comando di Azure.
    Vedere [installare l'interfaccia della](https://docs.microsoft.com/cli/azure/install-azure-cli) riga di comando di Azure e iniziare [a usare l'interfaccia della riga di](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)comando.

1. Creare una stringa di connessione all'account di archiviazione in cui si vuole creare la condivisione.
    Sostituire  ```<storage-account>``` e ```<resource_group>``` con il nome e il gruppo di risorse dell'account di archiviazione nell'esempio seguente:

   ```azurecli
    current_env_conn_string=$(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

1. Creare la condivisione file.
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string > /dev/null
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Connettere e montare una condivisione file in Windows](storage-how-to-use-files-windows.md)
* [Connettere e montare una condivisione file in Linux](storage-how-to-use-files-linux.md)
* [Connettere e montare una condivisione file in macOS](storage-how-to-use-files-mac.md)

Per altre informazioni su File di Azure, vedere i collegamenti seguenti:

* [Domande frequenti sui file di archiviazione](storage-files-faq.md)
* [Risolvere i problemi relativi a File di Azure in Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Risolvere i problemi relativi a File di Azure in Linux](storage-troubleshoot-linux-file-connection-problems.md)
