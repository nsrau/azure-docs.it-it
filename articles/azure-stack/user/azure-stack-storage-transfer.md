---
title: Strumenti per l'archiviazione di Azure Stack | Microsoft Docs
description: Altre informazioni sui dati di archiviazione di Azure Stack gli strumenti di trasferimento
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: 4e92f2aeec21ccef5a6a553b17e099d54de7266a
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57774338"
---
# <a name="use-data-transfer-tools-for-azure-stack-storage"></a>Usare gli strumenti di trasferimento dei dati per l'archiviazione di Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Microsoft Azure Stack offre un set di servizi di archiviazione per i dischi, BLOB, tabelle, code e le funzioni di gestione di account. Se si desidera gestire oppure spostare i dati in o da archiviazione di Azure Stack, è possibile usare un set di strumenti di archiviazione di Azure. Questo articolo offre una panoramica degli strumenti disponibili.

I requisiti di determinano quale dei seguenti strumenti più adatto:

* [AzCopy](#azcopy)

    Un'utilità della riga di comando, specifici dell'archiviazione che è possibile scaricare per copiare dati da un oggetto in un altro oggetto all'interno dell'account di archiviazione o tra account di archiviazione.

* [Azure PowerShell](#azure-powershell)

    Una shell da riga di comando basata su attività e un linguaggio di scripting progettato specificamente per l'amministrazione del sistema.

* [Interfaccia della riga di comando di Azure](#azure-cli)

    Uno strumento open source e multipiattaforma che offre un set di comandi per l'utilizzo con le piattaforme Azure e Azure Stack.

* [Esplora archivi di Microsoft](#microsoft-azure-storage-explorer)

    Un'app autonoma facile da usare con un'interfaccia utente.

* [Blobfuse](#blobfuse)

    Un driver virtual file system per l'archiviazione Blob di Azure, che consente di accedere ai dati di blob in blocchi esistente nell'account di archiviazione tramite il file system Linux. 

Date le differenze di servizi di archiviazione tra Azure e Azure Stack, potrebbero essere presenti alcuni requisiti specifici per ogni strumento descritto nelle sezioni seguenti. Per un confronto tra l'archiviazione di Azure Stack e archiviazione di Azure, vedere [archiviazione di Azure Stack: Differenze e considerazioni](azure-stack-acs-differences.md).

## <a name="azcopy"></a>AzCopy

AzCopy è un'utilità della riga di comando progettata per copiare dati da e verso archiviazione di blob e tabelle di Microsoft Azure usando semplici comandi con prestazioni ottimali. È possibile copiare dati da un oggetto a un altro all'interno dell'account di archiviazione o tra account di archiviazione.

### <a name="download-and-install-azcopy"></a>Scaricare e installare AzCopy

Sono disponibili due versioni dell'utilità AzCopy: AzCopy in Windows e AzCopy in Linux.

 - **AzCopy in Windows**
    - Scaricare la versione supportata di AzCopy per Azure Stack. È possibile installare e usare AzCopy nello Stack di Azure esattamente come Azure. Per altre informazioni, vedere [AzCopy in Windows](../../storage/common/storage-use-azcopy.md).
        - Per l'aggiornamento 1811, o versioni successive, [download di AzCopy 7.3.0](https://aka.ms/azcopyforazurestack20171109).
        - Per le versioni precedenti (aggiornamento 1802 per 1809), [download di AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417).

 - **AzCopy in Linux**

    - È possibile installare e usare AzCopy nello Stack di Azure esattamente come Azure. Per altre informazioni, vedere [AzCopy in Linux](../../storage/common/storage-use-azcopy-linux.md).
    - Per le versioni precedenti (1802 per 1809 aggiornamenti), vedere la [passaggi di installazione per AzCopy 7.1 e versioni precedenti](../../storage/common/storage-use-azcopy-linux.md#installation-steps-for-azcopy-71-and-earlier-versions).

### <a name="azcopy-command-examples-for-data-transfer"></a>Esempi di comando AzCopy per il trasferimento dati

Negli esempi seguenti seguono gli scenari tipici per copiare dati da e verso BLOB di Azure Stack. Per altre informazioni, vedere [AzCopy in Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) e [AzCopy in Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux).

### <a name="download-all-blobs-to-a-local-disk"></a>Scaricare tutti i BLOB in un disco locale

**Windows**

```shell
AzCopy.exe /source:https://myaccount.blob.local.azurestack.external/mycontainer /dest:C:\myfolder /sourcekey:<key> /S
```

**Linux**

```bash
azcopy \
    --source https://myaccount.blob.local.azurestack.external/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

### <a name="upload-single-file-to-virtual-directory"></a>Caricare un singolo file nella directory virtuale

**Windows**

```shell
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.local.azurestack.external/mycontainer/vd /DestKey:key /Pattern:abc.txt
```

**Linux**

```bash
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.local.azurestack.external/mycontainer/vd/abc.txt \
    --dest-key <key>
```

### <a name="move-data-between-azure-and-azure-stack-storage"></a>Spostare dati tra l'archiviazione di Azure e Azure Stack

Trasferimento dati asincroni tra archiviazione di Azure e Azure Stack non è supportato. È necessario specificare il trasferimento con il **/SyncCopy** oppure **-sync-copia** opzione.

**Windows**

```shell
Azcopy /Source:https://myaccount.blob.local.azurestack.external/mycontainer /Dest:https://myaccount2.blob.core.windows.net/mycontainer2 /SourceKey:AzSKey /DestKey:Azurekey /S /SyncCopy
```

**Linux**

```bash
azcopy \
    --source https://myaccount1.blob.local.azurestack.external/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --sync-copy
```

### <a name="azcopy-known-issues"></a>Problemi noti di Azcopy

 - Qualsiasi operazione di AzCopy su un archivio di file non è disponibile perché la memorizzazione dei file non è ancora disponibile in Azure Stack.
 - Trasferimento dati asincroni tra archiviazione di Azure e Azure Stack non è supportato. È possibile specificare il trasferimento con il **/SyncCopy** opzione per copiare i dati.
 - La versione Linux di Azcopy supporta solo la 1802 update o versioni successive. E non supporta il servizio tabelle.

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell è un modulo che fornisce i cmdlet per la gestione dei servizi in Azure e Azure Stack. È una shell da riga di comando basata su attività e un linguaggio di scripting progettato specificamente per l'amministrazione del sistema.

### <a name="install-and-configure-powershell-for-azure-stack"></a>Installare e configurare PowerShell per Azure Stack

Azure Stack compatibile con i moduli Azure PowerShell sono necessari per lavorare con Azure Stack. Per altre informazioni, vedere [installazione di PowerShell per Azure Stack](azure-stack-powershell-install.md) e [configurare l'ambiente PowerShell dell'utente di Azure Stack](azure-stack-powershell-configure-user.md) per altre informazioni.

### <a name="powershell-sample-script-for-azure-stack"></a>Script di esempio di PowerShell per Azure Stack 

In questo esempio si supponga di avere completato [installato PowerShell per Azure Stack](azure-stack-powershell-install.md). Questo script consentirà di completare la configurazione e chiedere le credenziali per aggiungere l'account nell'ambiente PowerShell locale al tenant di Azure Stack. Quindi, lo script verrà impostato il valore predefinito di sottoscrizione di Azure, creare un nuovo account di archiviazione in Azure, creare un nuovo contenitore nell'account di archiviazione nuovo e carica un file di immagine esistente (blob) in tale contenitore. Dopo lo script Elenca tutti i BLOB nel contenitore, crea una nuova directory di destinazione nel computer locale e scaricare il file di immagine.

1. Installare [moduli di Azure Stack-compatibili con Azure PowerShell](azure-stack-powershell-install.md).
2. Scaricare il [gli strumenti necessari per lavorare con Azure Stack](azure-stack-powershell-download.md).
3. Aprire **Windows PowerShell ISE** e **Esegui come amministratore**, fare clic su **File** > **nuovo** per creare un nuovo file script.
4. Copiare lo script seguente e incollarlo nel nuovo file di script.
5. Aggiornare le variabili in base alle impostazioni di configurazione.
   > [!NOTE]
   > Questo script deve essere eseguito nella directory radice per **AzureStack_Tools**.

```PowerShell  
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack environment name
$ARMEndPoint = "https://management.local.azurestack.external" 
$GraphAudience = "https://graph.windows.net/" 
$AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com" 

$SubscriptionName = "basic" # Update with the name of your subscription.
$ResourceGroupName = "myTestRG" # Give a name to your new resource group.
$StorageAccountName = "azsblobcontainer" # Give a name to your new storage account. It must be lowercase.
$Location = "Local" # Choose "Local" as an example.
$ContainerName = "photo" # Give a name to your new container.
$ImageToUpload = "C:\temp\Hello.jpg" # Prepare an image file and a source directory in your local computer.
$DestinationFolder = "C:\temp\download" # A destination directory in your local computer.

# Import the Connect PowerShell module"
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
Import-Module .\Connect\AzureStack.Connect.psm1

# Configure the PowerShell environment
# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRmEnvironment -Name $ARMEvnName -ARMEndpoint $ARMEndPoint 

# Set the GraphEndpointResourceId value
Set-AzureRmEnvironment -Name $ARMEvnName -GraphEndpoint $GraphAudience

# Login
$TenantID = Get-AzsDirectoryTenantId -AADTenantName $AADTenantName -EnvironmentName $ARMEvnName
Add-AzureRmAccount -EnvironmentName $ARMEvnName -TenantId $TenantID 

# Set a default Azure subscription.
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Create a new Resource Group 
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Create a new storage account.
New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS

# Set a default storage account.
Set-AzureRmCurrentStorageAccount -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName 

# Create a new container.
New-AzureStorageContainer -Name $ContainerName -Permission Off

# Upload a blob into a container.
Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

# List all blobs in a container.
Get-AzureStorageBlob -Container $ContainerName

# Download blobs from the container:
# Get a reference to a list of all blobs in a container.
$blobs = Get-AzureStorageBlob -Container $ContainerName

# Create the destination directory.
New-Item -Path $DestinationFolder -ItemType Directory -Force  

# Download blobs into the local destination directory.
$blobs | Get-AzureStorageBlobContent –Destination $DestinationFolder

# end
```

### <a name="powershell-known-issues"></a>Problemi noti di PowerShell

La versione del modulo Azure PowerShell compatibile corrente per Azure Stack è 1.2.11 per le operazioni dell'utente. È diversa dalla versione più recente di Azure PowerShell. Questa differenza influisce sull'operazione di servizi di archiviazione:

Il formato del valore restituito del `Get-AzureRmStorageAccountKey` nella versione 1.2.11 ha due proprietà: `Key1` e `Key2`, mentre la versione corrente di Azure restituisce una matrice contenente tutte le chiavi dell'account.

```powershell
# This command gets a specific key for a storage account, 
# and works for Azure PowerShell version 1.4, and later versions.
(Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
-AccountName "MyStorageAccount").Value[0]

# This command gets a specific key for a storage account, 
# and works for Azure PowerShell version 1.3.2, and previous versions.
(Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
-AccountName "MyStorageAccount").Key1
```

Per altre informazioni, vedere [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/Get-AzureRmStorageAccountKey).

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Il comando di Azure è l'esperienza della riga di comando di Azure per la gestione delle risorse di Azure. È possibile installarla in macOS, Linux e Windows ed eseguirlo dalla riga di comando.

Comando di Azure è ottimizzata per la gestione e amministrazione delle risorse di Azure dalla riga di comando e per la creazione di script di automazione che funzionano con Azure Resource Manager. Offre inoltre molte delle stesse funzioni disponibili nel portale di Azure Stack, incluso l'accesso ai dati avanzati.

Azure Stack richiede Azure CLI 2.0 o versione successiva. Per altre informazioni sull'installazione e configurazione della riga di comando di Azure con Azure Stack, vedere [installare e configurare l'interfaccia della riga di comando di Azure Stack](azure-stack-version-profiles-azurecli2.md). Per altre informazioni su come usare il comando di Azure per eseguire diverse attività con le risorse nell'account di archiviazione di Azure Stack, vedere [tramite la CLI di Azure con archiviazione di Azure](../../storage/storage-azure-cli.md)

### <a name="azure-cli-sample-script-for-azure-stack"></a>Script di esempio della riga di comando Azure per Azure Stack

Dopo aver completato l'installazione dell'interfaccia della riga e la configurazione, è possibile provare i passaggi seguenti per lavorare con un piccolo esempio script della shell per interagire con le risorse di archiviazione di Azure Stack. Lo script completa le azioni seguenti:

* Crea un nuovo contenitore nell'account di archiviazione.
* Carica un file esistente (come un blob) nel contenitore.
* Elenca tutti i BLOB nel contenitore.
* Scarica il file in una destinazione nel computer locale specificato.

Prima di eseguire questo script, assicurarsi che è possibile correttamente la connessione a e accedere alla destinazione Azure Stack.

1. Aprire un editor di testo quindi copiare e incollare lo script precedente nell'editor.
2. Aggiornare le variabili dello script in modo da riflettere le impostazioni di configurazione.
3. Dopo aver aggiornato le variabili necessarie, salvare lo script e chiudere l'editor. I passaggi successivi presuppongono che allo sript sia stato assegnato il nome **my_storage_sample.sh**.
4. Contrassegnare lo script come eseguibile, se necessario:`chmod +x my_storage_sample.sh`
5. Eseguire lo script. Ad esempio, in Bash: `./my_storage_sample.sh`

```azurecli
#!/bin/bash
# A simple Azure Stack storage example script

export AZURESTACK_RESOURCE_GROUP=<resource_group_name>
export AZURESTACK_RG_LOCATION="local"
export AZURESTACK_STORAGE_ACCOUNT_NAME=<storage_account_name>
export AZURESTACK_STORAGE_CONTAINER_NAME=<container_name>
export AZURESTACK_STORAGE_BLOB_NAME=<blob_name>
export FILE_TO_UPLOAD=<file_to_upload>
export DESTINATION_FILE=<destination_file>

echo "Creating the resource group..."
az group create --name $AZURESTACK_RESOURCE_GROUP --location $AZURESTACK_RG_LOCATION

echo "Creating the storage account..."
az storage account create --name $AZURESTACK_STORAGE_ACCOUNT_NAME --resource-group $AZURESTACK_RESOURCE_GROUP --account-type Standard_LRS

echo "Creating the blob container..."
az storage container create --name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Uploading the file..."
az storage blob upload --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --file $FILE_TO_UPLOAD --name $AZURESTACK_STORAGE_BLOB_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Listing the blobs..."
az storage blob list --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --output table

echo "Downloading the file..."
az storage blob download --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --name $AZURESTACK_STORAGE_BLOB_NAME --file $DESTINATION_FILE --output table

echo "Done"
```

## <a name="microsoft-azure-storage-explorer"></a>Esplora archivi di Microsoft Azure

Microsoft Azure storage explorer è un'app autonoma di Microsoft. Consente di usare facilmente con archiviazione di Azure e archiviazione di Azure Stack i dati in computer Linux, macOS e Windows. Se si desidera un modo semplice per gestire i dati di archiviazione di Azure Stack, quindi è consigliabile usare Esplora archivi di Microsoft Azure.

* Per altre informazioni sulla configurazione di Azure storage explorer per lavorare con Azure Stack, vedere [Connect storage explorer per una sottoscrizione di Azure Stack](azure-stack-storage-connect-se.md).
* Per altre informazioni su Microsoft Azure storage explorer, vedere [Guida introduttiva a storage explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md)

## <a name="blobfuse"></a>Blobfuse 

[Blobfuse](https://github.com/Azure/azure-storage-fuse) è un driver del file system virtuale per Archiviazione BLOB di Azure, che consente di accedere ai dati BLOB in blocchi esistenti nell'account di archiviazione tramite il file system di Linux. Archiviazione BLOB di Azure è un servizio di archiviazione di oggetti e quindi non ha uno spazio dei nomi gerarchico. Blobfuse fornisce questo spazio dei nomi usando lo schema di directory virtuale con l'utilizzo della barra `/` come delimitatore. Blobfuse funziona in Azure e Azure Stack. 

Per altre informazioni sul montaggio nell'archiviazione Blob come file system con Blobfuse in Linux, vedere [come montare l'archivio Blob come file system con Blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux). 

Per Azure Stack **blobEndpoint** deve essere specificato oltre a accountName e accountKey/sasToken containerName, quando si configurano le credenziali dell'account di archiviazione nel passaggio di preparazione per il montaggio. 

Lo sviluppo di Azure Stack Kit, deve essere il blobEndpoint `myaccount.blob.local.azurestack.external`. Se non si è certi sull'endpoint nel sistema integrato Azure Stack, contattare l'amministratore cloud. 

Tenere presente che accountKey e sasToken possono solo essere configurati uno alla volta. Quando viene specificata una chiave dell'account di archiviazione, il file di configurazione delle credenziali è nel formato seguente: 

```
accountName myaccount 
accountKey myaccesskey== 
containerName mycontainer 
blobEndpoint myaccount.blob.local.azurestack.external
```

Quando si specifica il token di accesso condiviso, il file di configurazione delle credenziali è nel formato seguente:

```  
accountName myaccount 
sasToken ?mysastoken 
containerName mycontainer 
blobEndpoint myaccount.blob.local.azurestack.external
```

## <a name="next-steps"></a>Passaggi successivi

* [Connettere storage explorer a una sottoscrizione di Azure Stack](azure-stack-storage-connect-se.md)
* [Guida introduttiva a storage explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Archiviazione coerenti con Azure: differenze e considerazioni](azure-stack-acs-differences.md)
* [Introduzione ad archiviazione di Microsoft Azure](../../storage/common/storage-introduction.md)
