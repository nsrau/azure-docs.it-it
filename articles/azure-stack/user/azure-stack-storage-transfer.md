---
title: Strumenti per l'archiviazione di Azure Stack | Documenti Microsoft
description: Informazioni sui dati di archiviazione di Azure Stack gli strumenti di trasferimento
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: 3d9bd187a70e8b8292e9c47497c2c6b13764045d
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604727"
---
# <a name="use-data-transfer-tools-for-azure-stack-storage"></a>Utilizzare gli strumenti di trasferimento dei dati per l'archiviazione di Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Stack di Microsoft Azure fornisce un set di servizi di archiviazione per i dischi, BLOB, tabelle, code e le funzioni di gestione di account. Se si desidera gestire oppure spostare i dati in o da archiviazione di Azure Stack, è possibile utilizzare un set di strumenti di archiviazione di Azure. In questo articolo viene fornita una panoramica degli strumenti disponibili.

I requisiti di determinano quale dei seguenti strumenti più adatta:

* [AzCopy](#azcopy)

    Un'utilità della riga di comando, specifici dell'archiviazione che è possibile scaricare per copiare i dati da un oggetto a un altro oggetto all'interno dell'account di archiviazione o tra gli account di archiviazione.

* [Azure PowerShell](#azure-powershell)

    Una shell da riga di comando, basato su attività e un linguaggio di scripting progettato specificamente per l'amministrazione del sistema.

* [Interfaccia della riga di comando di Azure](#azure-cli)

    Uno strumento open source, multipiattaforma che fornisce un set di comandi per l'utilizzo con le piattaforme di Azure e Azure Stack.

* [Esplora archivi Microsoft](#microsoft-azure-storage-explorer)

    Un'app autonoma da usare con un'interfaccia utente.

A causa delle differenze servizi di archiviazione tra Azure e Azure Stack, potrebbero essere presenti alcuni requisiti specifici per ogni strumento descritto nelle sezioni seguenti. Per un confronto tra Azure Stack archiviazione e di archiviazione di Azure, vedere [archiviazione di Azure Stack: considerazioni e le differenze](azure-stack-acs-differences.md).

## <a name="azcopy"></a>AzCopy

AzCopy è un'utilità della riga di comando progettata per copiare dati in e da archiviazione di blob e tabelle di Microsoft Azure tramite i comandi semplici con prestazioni ottimali. È possibile copiare dati da un oggetto a un altro all'interno dell'account di archiviazione o tra account di archiviazione.

### <a name="download-and-install-azcopy"></a>Scaricare e installare AzCopy

Sono disponibili due versioni dell'utilità AzCopy: AzCopy in Windows e AzCopy in Linux.

 - **AzCopy in Windows**
    - Scaricare la versione supportata di AzCopy per lo Stack di Azure. È possibile installare e usare AzCopy nello Stack di Azure nello stesso modo di Azure. Per altre informazioni, vedere [AzCopy in Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy).
        - Per 1802 update o versioni più recenti [scaricare AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417).
        - Per le versioni precedenti, [scaricare AzCopy 5.0.0](https://aka.ms/azcopyforazurestack20170417).

 - **AzCopy in Linux**

    - AzCopy in Linux supporta Azure Stack 1802 update o versioni più recenti. È possibile installare e usare AzCopy nello Stack di Azure nello stesso modo di Azure. Per altre informazioni, vedere [AzCopy in Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux).

### <a name="azcopy-command-examples-for-data-transfer"></a>Esempi di comandi AzCopy per il trasferimento di dati

Negli esempi seguenti seguono gli scenari tipici per copiare dati da e verso i BLOB di Azure Stack. Per altre informazioni, vedere [AzCopy in Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) e [AzCopy in Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux).

### <a name="download-all-blobs-to-a-local-disk"></a>Scaricare tutti i BLOB in un disco locale

**Windows**

````AzCopy
AzCopy.exe /source:https://myaccount.blob.local.azurestack.external/mycontainer /dest:C:\myfolder /sourcekey:<key> /S
````

**Linux**

````AzCopy
azcopy \
    --source https://myaccount.blob.local.azurestack.external/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
````

### <a name="upload-single-file-to-virtual-directory"></a>Caricare un singolo file nella directory virtuale

**Windows**

```AzCopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.local.azurestack.external/mycontainer/vd /DestKey:key /Pattern:abc.txt
```

**Linux**

````AzCopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.local.azurestack.external/mycontainer/vd/abc.txt \
    --dest-key <key>
````

### <a name="move-data-between-azure-and-azure-stack-storage"></a>Spostare i dati tra l'archiviazione di Azure e Azure Stack

Trasferimento asincrono dei dati tra archiviazione di Azure e Azure Stack non è supportata. È necessario specificare il trasferimento con il **/SyncCopy** oppure **-sync-copia** opzione.

**Windows**

````AzCopy
Azcopy /Source:https://myaccount.blob.local.azurestack.external/mycontainer /Dest:https://myaccount2.blob.core.windows.net/mycontainer2 /SourceKey:AzSKey /DestKey:Azurekey /S /SyncCopy
````

**Linux**

````AzCopy
azcopy \
    --source https://myaccount1.blob.local.azurestack.external/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --sync-copy
````

### <a name="azcopy-known-issues"></a>Azcopy problemi noti

 - Qualsiasi operazione AzCopy su un archivio di file non è disponibile perché l'archiviazione di file non è ancora disponibile nello Stack di Azure.
 - Trasferimento asincrono dei dati tra archiviazione di Azure e Azure Stack non è supportata. È possibile specificare il trasferimento tramite il **/SyncCopy** opzione per copiare i dati.
 - La versione Linux di Azcopy supporta solo 1802 update o versioni successive. E non supporta il servizio tabelle.

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell è un modulo che fornisce i cmdlet per la gestione dei servizi in Azure sia Azure Stack. È una shell da riga di comando, basato su attività e un linguaggio di scripting progettato specificamente per l'amministrazione del sistema.

### <a name="install-and-configure-powershell-for-azure-stack"></a>Installare e configurare PowerShell per Azure Stack

Moduli di Azure PowerShell compatibili Stack Azure necessarie per lavorare con lo Stack di Azure. Per ulteriori informazioni, vedere [installare PowerShell per Azure Stack](azure-stack-powershell-install.md) e [configurare l'ambiente di PowerShell dell'utente Azure Stack](azure-stack-powershell-configure-user.md) per altre informazioni.

### <a name="powershell-sample-script-for-azure-stack"></a>Script di esempio di PowerShell per Azure Stack 

In questo esempio si supponga di avere completato [installato PowerShell per Azure Stack](azure-stack-powershell-install.md). Questo script consente di completare la configurazione e chiedere il tenant di Azure Stack credenziali per aggiungere l'account nell'ambiente di PowerShell locale. Quindi, lo script verrà impostato il valore predefinito di sottoscrizione di Azure, creare un nuovo account di archiviazione in Azure, creare un nuovo contenitore in questo nuovo account di archiviazione e caricare un file di immagine esistente (blob) in tale contenitore. Dopo che lo script sono elencati tutti i BLOB nel contenitore, verrà creare una nuova directory di destinazione nel computer locale e scaricare il file di immagine.

1. Installare [moduli di PowerShell Azure compatibile Stack Azure](azure-stack-powershell-install.md).
2. Scaricare il [gli strumenti necessari per lavorare con Azure Stack](azure-stack-powershell-download.md).
3. Aprire **Windows PowerShell ISE** e **Esegui come amministratore**, fare clic su **File** > **New** per creare un nuovo file script.
4. Copiare lo script seguente e incollare nel nuovo file di script.
5. Aggiornare le variabili di script in base alle impostazioni di configurazione.
   > [!NOTE]
   > Questo script deve essere eseguito nella directory radice **AzureStack_Tools**.

```PowerShell  
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack environemnt name
$ARMEndPoint = "https://management.local.azurestack.external" 
$GraphAudiance = "https://graph.windows.net/" 
$AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com" 

$SubscriptionName = "basic" # Update with the name of your subscription.
$ResourceGroupName = "myTestRG" # Give a name to your new resource group.
$StorageAccountName = "azsblobcontainer" # Give a name to your new storage account. It must be lowercase.
$Location = "Local" # Choose "Local" as an example.
$ContainerName = "photo" # Give a name to your new container.
$ImageToUpload = "C:\temp\Hello.jpg" # Prepare an image file and a source directory in your local computer.
$DestinationFolder = "C:\temp\downlaod" # A destination directory in your local computer.

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
````

### <a name="powershell-known-issues"></a>Problemi noti di PowerShell

La versione del modulo Azure PowerShell compatibile corrente per lo Stack di Azure è 1.3.0. È diversa dalla versione più recente di Azure PowerShell. Questa differenza influisce sull'operazione di servizi di archiviazione:

* Il formato del valore restituito del `Get-AzureRmStorageAccountKey` nella versione 1.3.0 ha due proprietà: `Key1` e `Key2`, mentre la versione corrente di Azure restituisce una matrice che contiene tutte le chiavi dell'account.

   ```
   # This command gets a specific key for a storage account, 
   # and works for Azure PowerShell version 1.4, and later versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Value[0]

   # This command gets a specific key for a storage account, 
   # and works for Azure PowerShell version 1.3.2, and previous versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Key1

   ```

   Per ulteriori informazioni, vedere [Get AzureRmStorageAccountKey](https://docs.microsoft.com/powershell/module/azurerm.storage/Get-AzureRmStorageAccountKey?view=azurermps-4.1.0).

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

L'interfaccia CLI di Azure è l'esperienza della riga di comando di Azure per la gestione delle risorse di Azure. È possibile installarlo in Windows, Linux e macOS ed eseguirlo dalla riga di comando.

CLI di Azure è ottimizzato per gestire e amministrare le risorse di Azure dalla riga di comando e per la creazione di script di automazione che funzionano con Gestione risorse di Azure. Offre inoltre molte delle funzioni disponibili nel portale di Azure Stack, incluso l'accesso a dati complessi.

Stack di Azure richiede Azure CLI versione 2.0. Per ulteriori informazioni sull'installazione e configurazione CLI di Azure con lo Stack di Azure, vedere [installare e configurare Azure Stack CLI](azure-stack-version-profiles-azurecli2.md). Per ulteriori informazioni su come usare l'interfaccia CLI di Azure 2.0 per eseguire diverse attività di utilizzo delle risorse nell'account di archiviazione Azure Stack, vedere [utilizzando il CLI2.0 Azure con archiviazione di Azure](../../storage/storage-azure-cli.md)

### <a name="azure-cli-sample-script-for-azure-stack"></a>Script di esempio CLI Azure per lo Stack di Azure

Dopo aver completato l'installazione di CLI e la configurazione, è possibile provare la procedura seguente per funzionare con uno script di esempio piccola della shell per interagire con le risorse di archiviazione di Azure Stack. Lo script consente di completare le azioni seguenti:

* Crea un nuovo contenitore nell'account di archiviazione.
* Carica un file esistente (come un blob) nel contenitore.
* Elenca tutti i BLOB nel contenitore.
* Scarica il file a una destinazione nel computer locale specificato.

Prima di eseguire questo script, assicurarsi che è possibile correttamente connettersi e accedere alla destinazione dello Stack di Azure.

1. Aprire un editor di testo quindi copiare e incollare lo script precedente nell'editor.
2. Aggiornare le variabili dello script in modo da riflettere le impostazioni di configurazione.
3. Dopo aver aggiornato le variabili necessarie, salvare lo script e chiudere l'editor. I passaggi successivi presuppongono che allo sript sia stato assegnato il nome **my_storage_sample.sh**.
4. Contrassegnare lo script come eseguibile, se necessario:`chmod +x my_storage_sample.sh`
5. Eseguire lo script. Ad esempio, in Bash: `./my_storage_sample.sh`

```bash
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
````

## <a name="microsoft-azure-storage-explorer"></a>Esplora archivi Microsoft Azure

Microsoft Azure storage explorer è un'app autonoma da Microsoft. È possibile utilizzare facilmente con l'archiviazione di Azure e Azure Stack l'archiviazione dei dati in Windows, macOS e i computer Linux. Se si desidera un modo semplice per gestire i dati di archiviazione Azure Stack, quindi utilizzare Esplora archivi Microsoft Azure.

* Per ulteriori informazioni sulla configurazione di soluzioni di archiviazione di Azure per funzionare con lo Stack di Azure, vedere [Esplora archivi Connetti a una sottoscrizione di Azure Stack](azure-stack-storage-connect-se.md).
* Per altre informazioni su Esplora archivi Microsoft Azure, vedere [iniziare a usare Esplora archivi](../../vs-azure-tools-storage-manage-with-storage-explorer.md)

## <a name="next-steps"></a>Passaggi successivi

* [Esplora archivi di connettersi a una sottoscrizione di Azure Stack](azure-stack-storage-connect-se.md)
* [Introduzione a Esplora archivi](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Archiviazione di Azure-coerente: considerazioni e le differenze](azure-stack-acs-differences.md)
* [Introduzione all'archiviazione di Microsoft Azure](../../storage/common/storage-introduction.md)
