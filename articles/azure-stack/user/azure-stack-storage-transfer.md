---
title: Strumenti per l'archiviazione di Azure Stack
description: Informazioni sui dati di archiviazione di Azure Stack gli strumenti di trasferimento
services: azure-stack
documentationcenter: 
author: xiaofmao
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: xiaofmao
ms.openlocfilehash: 9799498a11449a9ed496d0fdb40312603eda064e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="tools-for-azure-stack-storage"></a>Strumenti per l'archiviazione di Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Stack di Microsoft Azure fornisce un set di servizi di archiviazione per i dischi, BLOB, tabelle, code e funzionalità di gestione di account. Se si desidera gestire oppure spostare i dati da o verso dello Stack di archiviazione di Azure, è possibile utilizzare un set di strumenti di archiviazione di Azure. Questo articolo fornisce una rapida panoramica degli strumenti disponibili.

Lo strumento adatto si dipende dai requisiti:
* [AzCopy](#azcopy)

    Utilità della riga di comando specifici dell'archiviazione che è possibile scaricare per copiare i dati da un oggetto a un altro all'interno dell'account di archiviazione o tra gli account di archiviazione.

* [Azure PowerShell](#azure-powershell)

    Una shell da riga di comando basata su attività e un linguaggio di scripting progettato specificamente per l'amministrazione del sistema.

* [Interfaccia della riga di comando di Azure](#azure-cli)

    Uno strumento open source, multipiattaforma che fornisce un set di comandi per l'utilizzo con le piattaforme di Azure e Azure Stack.

* [Esplorazione dell'archiviazione di Microsoft (anteprima)](#microsoft-azure-storage-explorer)

    Un'app autonoma facile da utilizzare con un'interfaccia utente.

A causa delle differenze di servizi di archiviazione tra Azure e Azure Stack, potrebbero essere presenti alcuni requisiti specifici per ogni strumento descritto nelle sezioni seguenti. Per un confronto tra l'archiviazione di Azure Stack e l'archiviazione di Azure, vedere [dello Stack di archiviazione di Azure: considerazioni e le differenze](azure-stack-acs-differences.md).


## <a name="azcopy"></a>AzCopy
AzCopy è un'utilità della riga di comando progettata per copiare i dati da e verso i Blob di Microsoft Azure e l'archiviazione tabelle utilizzando comandi semplici con prestazioni ottimali. È possibile copiare dati da un oggetto a un altro all'interno dell'account di archiviazione o tra account di archiviazione. Esistono due versioni di AzCopy: AzCopy in Windows e AzCopy in Linux. Stack di Azure supporta solo la versione di Windows. 
 
### <a name="download-and-install-azcopy"></a>Scaricare e installare AzCopy 
[Scaricare](https://aka.ms/azcopyforazurestack) la versione supportata di Windows di AzCopy per lo Stack di Azure. È possibile installare e usare AzCopy nello Stack di Azure nello stesso modo di Azure. Per ulteriori informazioni, vedere [trasferire i dati con l'utilità della riga di comando di AzCopy](../../storage/common/storage-use-azcopy.md). 

### <a name="azcopy-command-examples-for-data-transfer"></a>Esempi di comandi AzCopy per il trasferimento di dati
Gli esempi seguenti illustrano alcuni scenari tipici per la copia dei dati da e verso i BLOB di Azure Stack. Per ulteriori informazioni, vedere [trasferire i dati con l'utilità della riga di comando di AzCopy](../../storage/storage-use-azcopy.md). 
#### <a name="download-all-blobs-to-local-disk"></a>Scaricare tutti i BLOB in un disco locale
```azcopy
AzCopy.exe /source:https://myaccount.blob.local.azurestack.external/mycontainer /dest:C:\myfolder /sourcekey:<key> /S
```
#### <a name="upload-single-file-to-virtual-directory"></a>Caricare un singolo file nella directory virtuale 
```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.local.azurestack.external/mycontainer/vd /DestKey:key /Pattern:abc.txt
```
#### <a name="move-data-between-azure-and-azure-stack-storage"></a>Spostare dati tra Azure e Stack di archiviazione di Azure 
Trasferimento asincrono dei dati tra l'archiviazione di Azure e Azure Stack non è supportato. è necessario specificare il trasferimento tramite il `/SyncCopy` opzione. 
```azcopy 
Azcopy /Source:https://myaccount.blob.local.azurestack.external/mycontainer /Dest:https://myaccount2.blob.core.windows.net/mycontainer2 /SourceKey:AzSKey /DestKey:Azurekey /S /SyncCopy
```

### <a name="azcopy-known-issues"></a>Problemi noti di Azcopy
* Qualsiasi operazione AzCopy su archiviazione di File non è disponibile perché l'archiviazione di File non è ancora disponibile nello Stack di Azure.
* Trasferimento asincrono dei dati tra l'archiviazione di Azure e Azure Stack non è supportato. È possibile specificare il trasferimento tramite il `/SyncCopy` opzione per copiare i dati.
* Non è la versione Linux di Azcopy dello Stack di archiviazione di Azure. 

## <a name="azure-powershell"></a>Azure PowerShell
Azure PowerShell è un modulo che fornisce i cmdlet per la gestione dei servizi in Azure sia Azure Stack. Si tratta di una shell da riga di comando basata su attività e di un linguaggio di scripting progettato appositamente per gli amministratori di sistema.

### <a name="install-and-configure-powershell-for-azure-stack"></a>Installare e configurare PowerShell per Azure Stack
Moduli di Azure PowerShell compatibili Stack Azure necessarie per lavorare con lo Stack di Azure. Per ulteriori informazioni, vedere [installare PowerShell per Azure Stack](azure-stack-powershell-install.md) e [configurare l'ambiente di PowerShell dell'utente Azure Stack](azure-stack-powershell-configure-user.md) per altre informazioni.

### <a name="powershell-sample-script-for-azure-stack"></a>Script di esempio di PowerShell per Azure Stack 
In questo esempio si supponga di avere correttamente [installare PowerShell per Azure Stack](azure-stack-powershell-install.md). Questo script è conplete la configurazione di Guida e chiedere il tenant di Azure Stack credenziali per aggiungere l'account per il Queue il relativo ambiente PowerShell locale. Quindi, lo script verrà impostato il valore predefinito di sottoscrizione di Azure, creare un nuovo account di archiviazione in Azure, creare un nuovo contenitore in questo nuovo account di archiviazione e caricare un file di immagine esistente (blob) in tale contenitore. Dopo aver elencato tutti i BLOB nel contenitore, lo script crea una nuova directory di destinazione nel computer locale e scarica il file di immagine.

1. Installare [moduli di PowerShell Azure compatibile Stack Azure](azure-stack-powershell-install.md).  
2. Scaricare il [gli strumenti necessari per lavorare con Azure Stack](azure-stack-powershell-download.md).  
3. Aprire **Windows PowerShell ISE** e **Esegui come amministratore**, fare clic su **File** > **New** per creare un nuovo file script.
4. Copiare lo script seguente e incollare nel nuovo file di script.
5. Aggiornare le variabili di script in base alle impostazioni di configurazione. 
6. Nota: questo script deve essere eseguito nella radice del scaricato **AzureStack_Tools**. 

```PowerShell 
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack environemnt name
$ARMEndPoint = "https://management.local.azurestack.external" 
$GraphAudiance = "https://graph.windows.net/" 
$AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com" 

$SubscriptionName = "basic" # Update with the name of your subscription.
$ResourceGroupName = "myTestRG" # Give a name to your new resource group.
$StorageAccountName = "azsblobcontainer" # Give a name to your new storage account. It must be lowercase!
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
Set-AzureRmEnvironment -Name $ARMEvnName -GraphEndpoint $GraphAudiance

# Login
$TenantID = Get-AzsDirectoryTenantId -AADTenantName $AADTenantName -EnvironmentName $ARMEvnName
Login-AzureRmAccount -EnvironmentName $ARMEvnName -TenantId $TenantID 

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
La versione del modulo Azure PowerShell compatibile corrente per lo Stack di Azure è 1.2.10. È diversa dalla versione più recente di Azure PowerShell. Questa differenza influisce sull'operazione di servizi di archiviazione:

* Il formato del valore restituito di `Get-AzureRmStorageAccountKey` nella versione 1.2.10 ha due proprietà: `Key1` e `Key2`, mentre la versione corrente di Azure restituisce una matrice contenente tutte le chiavi dell'account.
   ```
   # This command gets a specific key for a Storage account, 
   # and works for Azure PowerShell version 1.4, and later versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Value[0]

   # This command gets a specific key for a Storage account, 
   # and works for Azure PowerShell version 1.3.2, and previous versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Key1

   ```
   Per ulteriori informazioni, vedere [Get AzureRmStorageAccountKey](https://docs.microsoft.com/powershell/module/azurerm.storage/Get-AzureRmStorageAccountKey?view=azurermps-4.1.0).

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
L'interfaccia CLI di Azure è l'esperienza della riga di comando di Azure per la gestione delle risorse di Azure. È possibile installarlo in Windows, Linux e macOS ed eseguirlo dalla riga di comando. 

CLI di Azure è ottimizzato per gestire e amministrare le risorse di Azure dalla riga di comando e per la creazione di script di automazione che funzionano con Gestione risorse di Azure. Offre inoltre molte delle funzioni disponibili nel portale di Azure Stack, incluso l'accesso a dati complessi.

Stack di Azure richiede Azure CLI versione 2.0. Per ulteriori informazioni sull'installazione e configurazione CLI di Azure con lo Stack di Azure, vedere [installare e configurare Azure Stack CLI](azure-stack-connect-cli.md). Per ulteriori informazioni su come usare l'interfaccia CLI di Azure 2.0 per eseguire diverse attività di utilizzo delle risorse nell'account di archiviazione di Azure Stack, vedere [utilizzando CLI2.0 di Azure con l'archiviazione di Azure](../../storage/storage-azure-cli.md)

### <a name="azure-cli-sample-script-for-azure-stack"></a>Script di esempio CLI Azure per lo Stack di Azure 
Dopo aver completato l'installazione di CLI e la configurazione, è possibile provare la procedura seguente per lavorare con uno script di esempio piccola shell per interagire con le risorse di archiviazione di Azure dello Stack. Lo script crea innanzitutto un nuovo contenitore nell'account di archiviazione, quindi carica un file esistente (come un blob) in tale contenitore, Elenca tutti i BLOB nel contenitore e, infine, scarica il file a una destinazione nel computer locale specificato. Prima di eseguire questo script, assicurarsi che ci si connette correttamente e account di accesso per la destinazione dello Stack di Azure. 
1. Aprire un editor di testo quindi copiare e incollare lo script precedente nell'editor.
2. Aggiornare le variabili dello script in modo da riflettere le impostazioni di configurazione. 
3. Dopo aver aggiornato le variabili necessarie, salvare lo script e chiudere l'editor. I passaggi successivi si supponga che è stato denominato il my_storage_sample.sh di script.
4. Contrassegnare lo script come eseguibile, se necessario:`chmod +x my_storage_sample.sh`
5. Eseguire lo script. Ad esempio, in Bash: `./my_storage_sample.sh`

```bash
#!/bin/bash
# A simple Azure Stack Storage example script

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

## <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

Microsoft Azure Storage Explorer è un'app autonoma da Microsoft. È possibile utilizzare facilmente con archiviazione di Azure e Azure Stack archiviazione dati in Windows, macOS e Linux. Se si desidera un modo semplice per gestire i dati dello Stack di archiviazione di Azure, utilizzare Microsoft Azure Storage Explorer.

Per ulteriori informazioni sulla configurazione di Azure Storage Explorer per lavorare con lo Stack di Azure, vedere [connettere Esplora archivi a una sottoscrizione di Azure Stack](azure-stack-storage-connect-se.md).

Per ulteriori informazioni su Microsoft Azure Storage Explorer, vedere [Introduzione a soluzioni di archiviazione (anteprima)](../../vs-azure-tools-storage-manage-with-storage-explorer.md)

## <a name="next-steps"></a>Passaggi successivi
* [Esplora archivi di connettersi a una sottoscrizione di Azure Stack](azure-stack-storage-connect-se.md)
* [Introduzione a soluzioni di archiviazione (anteprima)](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Archiviazione di Azure-coerente: considerazioni e le differenze](azure-stack-acs-differences.md)
* [Introduzione ad Archiviazione di Microsoft Azure](../../storage/common/storage-introduction.md)

