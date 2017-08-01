---
title: Come creare una condivisione file di Azure | Microsoft Docs
description: Come creare una condivisione file di Azure in Archiviazione file di Azure usando il portale di Azure, PowerShell e l'interfaccia della riga di comando di Azure.
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
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 10da4d903eaab290a6cca2c4f674548a43a70c53
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="create-a-file-share-in-azure-file-storage"></a>Creare una condivisione file in Archiviazione file di Azure
È possibile creare condivisioni file di Azure usando il [portale di Azure](https://portal.azure.com/), i cmdlet di PowerShell per Archiviazione di Azure, le librerie client di Archiviazione di Azure o l'API REST di Archiviazione di Azure. In questa esercitazione verrà illustrato:
* [Come creare una condivisione file di Azure usando il portale di Azure](#Create file share through the Portal)
* [Come creare una condivisione file di Azure usando PowerShell](#Create file share using PowerShell)
* [Come creare una condivisione file di Azure usando l'interfaccia della riga di comando](#create-file-share-using-command-line-interface-cli)

## <a name="prerequisites"></a>Prerequisiti
Per creare una condivisione file di Azure, è possibile usare un account di archiviazione già esistente o [creare un nuovo account di archiviazione di Azure](storage-create-storage-account.md). Per creare una condivisione file di Azure con PowerShell, saranno necessari la chiave dell'account e il nome dell'account di archiviazione. La chiave dell'account di archiviazione sarà necessaria se si prevede di usare Powershell o l'interfaccia della riga di comando.

## <a name="create-file-share-through-the-portal"></a>Creare la condivisione file tramite il portale
1. **Andare al pannello Account di archiviazione nel portale di Azure**:    
    ![Pannello Account di archiviazione](media/storage-file-how-to-create-file-share/create-file-share-portal1.png)

2. **Fare clic sul pulsante Aggiungi condivisione file**:    
    ![Fare clic sul pulsante Aggiungi condivisione file](media/storage-file-how-to-create-file-share/create-file-share-portal2.png)

3. **Specificare il nome e la quota. La quota attualmente non può essere superiore a 5 TB**:    
    ![Specificare un nome e una quota desiderata per la nuova condivisione file](media/storage-file-how-to-create-file-share/create-file-share-portal3.png)

4. **Visualizzare la nuova condivisione file**: ![Visualizzare la nuova condivisione file](media/storage-file-how-to-create-file-share/create-file-share-portal4.png)

5. **Caricare un file**: ![Caricare un file](media/storage-file-how-to-create-file-share/create-file-share-portal5.png)

6. **Esplorare la condivisione file e gestire le directory e i file**: ![Esplorare la condivisione file](media/storage-file-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>Creare la condivisione file tramite PowerShell
Per prepararsi all'uso di PowerShell, scaricare e installare i cmdlet di Azure PowerShell. Vedere [Come installare e configurare Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) per le istruzioni relative al punto di installazione e all’installazione.

> [!Note]  
> Si consiglia di scaricare e installare oppure aggiornare il modulo alla versione di Azure PowerShell più recente.

1. **Creare un contesto per l'account di archiviazione e la chiave** Il contesto incapsula il nome dell'account di archiviazione e la chiave dell'account. Per istruzioni sulla copia della chiave dell'account dal [portale di Azure](https://portal.azure.com/), vedere [Visualizzare e copiare le chiavi di accesso alle risorse di archiviazione](storage-create-storage-account.md#view-and-copy-storage-access-keys).

    ```powershell
    $storageContext = New-AzureStorageContext <storage-account-name> <storage-account-key>
    ```
    
2. **Creare una nuova condivisione file**:    
    
    ```powershell
    $share = New-AzureStorageShare logs -Context $storageContext
    ```

> [!Note]  
> Il nome della condivisione file deve essere composto solo da caratteri minuscoli. Per dettagli su come denominare condivisioni e file, vedere [Denominazione e riferimento a condivisioni, directory, file e metadati](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="create-file-share-through-command-line-interface-cli"></a>Creare una condivisione file tramite l'interfaccia della riga di comando
1. **Per prepararsi a usare un'interfaccia della riga di comando, scaricare e installare l'interfaccia della riga di comando di Azure.**  
    Vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2.md) e [Introduzione all'interfaccia della riga di comando di Azure 2.0](/cli/azure/get-started-with-azure-cli.md).

2. **Creare una stringa di connessione all'account di archiviazione in cui si vuole creare la condivisione.**  
    Sostituire ```<storage-account>``` e ```<resource_group>``` con il nome e il gruppo di risorse dell'account di archiviazione nell'esempio seguente.

   ```azurecli
    current_env_conn_string = $(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

3. **Creare la condivisione file**
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string 1 > /dev/null
    ```

## <a name="next-steps"></a>Passaggi successivi
* [Connettersi e montare una condivisione file: Windows](storage-file-how-to-use-files-windows.md)
* [Connettersi e montare una condivisione file: Linux](storage-how-to-use-files-linux.md)
* [Connettersi e montare una condivisione file: macOS](storage-file-how-to-use-files-mac.md)

Vedere i collegamenti seguenti per ulteriori informazioni sull'archiviazione file di Azure.

* [Domande frequenti](storage-files-faq.md)
* [Risoluzione dei problemi](storage-troubleshoot-file-connection-problems.md)
