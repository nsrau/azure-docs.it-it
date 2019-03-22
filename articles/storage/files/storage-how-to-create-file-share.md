---
title: Come creare una condivisione file di Azure | Microsoft Docs
description: Come creare una condivisione file di Azure in File di Azure usando il portale di Azure, PowerShell e l'interfaccia della riga di comando di Azure.
services: storage
author: RenaShahMSFT
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: renash
ms.subservice: files
ms.openlocfilehash: 1620683f4bc02d607afd8e0371e8d4a5461612b3
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772331"
---
# <a name="create-a-file-share-in-azure-files"></a>Creare una condivisione file in File di Azure
Per creare condivisioni file di Azure, è possibile usare il  [portale di Azure](https://portal.azure.com/), i cmdlet di PowerShell per Archiviazione di Azure, le librerie client di Archiviazione di Azure o l'API REST di Archiviazione di Azure. In questa esercitazione si apprenderà:
* Come creare una condivisione file di Azure usando il portale di Azure
* [Come creare una condivisione file di Azure usando PowerShell](#create-file-share-through-powershell)
* [Come creare una condivisione file di Azure usando l'interfaccia della riga di comando](#create-file-share-through-command-line-interface-cli)

## <a name="prerequisites"></a>Prerequisiti
Per creare una condivisione file di Azure, è possibile usare un account di archiviazione già esistente oppure [creare un nuovo account di archiviazione di Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Per creare una condivisione file di Azure con PowerShell, saranno necessari la chiave dell'account e il nome dell'account di archiviazione. È necessaria una chiave dell'account di archiviazione se si prevede di usare Powershell o l'interfaccia della riga di comando.

## <a name="create-a-file-share-through-the-azure-portal"></a>Creare una condivisione file tramite il portale di Azure
1. **Passare al pannello Account di archiviazione nel portale di Azure**:    
    ![Pannello Account di archiviazione](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

2. **Fare clic sul pulsante Aggiungi condivisione file**:    
    ![Fare clic sul pulsante Aggiungi condivisione file](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

3. **Specificare il nome e la quota. Il valore massimo corrente per la quota è 5 TB**:    
    ![Specificare un nome e una quota desiderata per la nuova condivisione file](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

4. **Visualizzare la nuova condivisione file**:  ![Visualizzare la nuova condivisione file](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

5. **Caricare un file**:  ![Caricare un file](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

6. **Esplorare la condivisione file e gestire le directory e i file**:  ![Esplorare la condivisione file](./media/storage-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>Creare la condivisione file tramite PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Per prepararsi all'uso di PowerShell, scaricare e installare i cmdlet di Azure PowerShell. Vedere [Come installare e configurare Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) per le istruzioni relative al punto di installazione e all'installazione.

> [!Note]  
> Si consiglia di scaricare e installare oppure aggiornare il modulo alla versione di Azure PowerShell più recente.

1. **Creare un contesto per l'account di archiviazione e la chiave** Il contesto incapsula il nome dell'account di archiviazione e la chiave dell'account. Per istruzioni sulla copia della chiave dell'account dal  [portale di Azure](https://portal.azure.com/), vedere  [Chiavi di accesso all'account di archiviazione](../common/storage-account-manage.md#access-keys).

    ```powershell
    $storageContext = New-AzStorageContext <storage-account-name> <storage-account-key>
    ```
    
2. **Creare una nuova condivisione file**:    
    
    ```powershell
    $share = New-AzStorageShare logs -Context $storageContext
    ```

> [!Note]  
> Il nome della condivisione file deve essere composto solo da caratteri minuscoli. Per informazioni dettagliate su come denominare le condivisioni e i file, vedere  [Naming and referencing shares, directories, files, and metadata](https://msdn.microsoft.com/library/azure/dn167011.aspx) (Denominazione e riferimento a condivisioni, directory, file e metadati).

## <a name="create-file-share-through-command-line-interface-cli"></a>Creare una condivisione file tramite l'interfaccia della riga di comando
1. **Per prepararsi a usare l'interfaccia della riga di comando, scaricare e installare l'interfaccia della riga di comando di Azure.**  
    Vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e [Introduzione all'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

2. **Creare una stringa di connessione all'account di archiviazione in cui si vuole creare la condivisione.**  
    Sostituire  ```<storage-account>``` e ```<resource_group>``` con il nome e il gruppo di risorse dell'account di archiviazione nell'esempio seguente:

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
* [Connettersi e montare una condivisione file: Windows](storage-how-to-use-files-windows.md)
* [Connettersi e montare una condivisione file: Linux](../storage-how-to-use-files-linux.md)
* [Connettersi e montare una condivisione file: macOS](storage-how-to-use-files-mac.md)

Per altre informazioni su File di Azure, vedere i collegamenti seguenti.

* [Domande frequenti](../storage-files-faq.md)
* [Risoluzione dei problemi in Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Risoluzione dei problemi in Linux](storage-troubleshoot-linux-file-connection-problems.md)   
