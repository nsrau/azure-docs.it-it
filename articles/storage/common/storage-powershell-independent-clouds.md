---
title: Gestione dell'archiviazione nei cloud indipendenti di Azure con Azure PowerShell | Microsoft Docs
description: Gestione dell'archiviazione nel cloud per la Cina, nel cloud per la Germania e nel cloud per enti pubblici con Azure PowerShell
services: storage
documentationcenter: na
author: robinsh
manager: timlt
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: robinsh
ms.openlocfilehash: 08e1af929d7ddc30c7dc149f6305ca1ca0bc22ae
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2017
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Gestione dell'archiviazione nei cloud indipendenti di Azure con PowerShell

La maggior parte delle persone usa il cloud pubblico di Azure per la distribuzione globale di Azure. Per motivi di sovranità e altro, sono disponibili anche alcune distribuzioni indipendenti di Microsoft Azure, denominate "ambienti". L'elenco seguente illustra in dettaglio i cloud indipendenti attualmente disponibili.

* [Cloud di Azure per enti pubblici](https://azure.microsoft.com/features/gov/)
* [Cloud di Azure per la Cina gestito da 21Vianet in Cina](http://www.windowsazure.cn/)
* [Cloud di Azure per la Germania](../../germany/germany-welcome.md)

## <a name="using-an-independent-cloud"></a>Uso di un cloud indipendente 

Per usare Archiviazione di Azure in uno dei cloud indipendenti, ci si connette a tale cloud anziché al cloud pubblico di Azure. Per usare uno dei cloud indipendenti anziché il cloud pubblico di Azure:

* Specificare l'*ambiente* a cui connettersi.
* Determinare e usare le aree disponibili.
* Usare il suffisso corretto dell'endpoint, che è diverso rispetto al cloud pubblico di Azure.

Questo esempio richiede il modulo Azure PowerShell 4.4.0 o versioni successive. In una finestra di PowerShell eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se non ci sono risultati, è necessario eseguire l'aggiornamento. Vedere in proposito come [installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps). 

## <a name="log-in-to-azure"></a>Accedere ad Azure

Eseguire il cmdlet [Get-AzureEnvironment](/powershell/module/azure/Get-AzureRmEnvironment) per visualizzare gli ambienti Azure disponibili:
   
```powershell
Get-AzureRmEnvironment
```

Accedere all'account che ha accesso al cloud a cui ci si vuole connettere e impostare l'ambiente. Questo esempio illustra come accedere a un account che usa il cloud di Azure per enti pubblici.   

```powershell
Login-AzureRmAccount –Environment AzureUSGovernment
```

Per accedere al cloud per la Cina, usare l'ambiente **AzureChinaCloud**. Per accedere al cloud per la Germania, usare **AzureGermanCloud**.

A questo punto, se è necessario l'elenco di località per creare un account di archiviazione o un'altra risorsa, è possibile eseguire query sulle località disponibili per il cloud selezionato tramite [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation).

```powershell
Get-AzureRmLocation | select Location, DisplayName
```

La tabella seguente mostra le località restituite per il cloud per la Germania.

|Percorso | DisplayName |
|----|----|
| germanycentral | Germania centrale|
| germanynortheast | Germania nord-orientale | 


## <a name="endpoint-suffix"></a>Suffisso dell'endpoint

Il suffisso dell'endpoint per ognuno di questi ambienti è diverso dall'endpoint pubblico di Azure. Ad esempio, il suffisso dell'endpoint BLOB del cloud pubblico di Azure è **blob.core.windows.net**. Per il cloud per enti pubblici, il suffisso dell'endpoint BLOB è **blob.core.usgovcloudapi.net**. 

### <a name="get-endpoint-using-get-azurermenvironment"></a>Ottenere l'endpoint tramite Get-AzureRMEnvironment 

Recuperare il suffisso dell'endpoint con [Get-AzureRMEnvironment](/powershell/module/azurerm.profile/get-azurermenvironment). L'endpoint è la proprietà *StorageEndpointSuffix* dell'ambiente. I frammenti di codice seguenti mostrano come eseguire questa operazione. Tutti questi comandi restituiscono un valore "core.cloudapp.net" o "core.cloudapi.de" e così via. Aggiungere tale valore al servizio di archiviazione per accedere al servizio. Ad esempio, "queue.core.cloudapi.de" accede al servizio di accodamento del cloud per la Germania.

Questo frammento di codice recupera tutti gli ambienti e il suffisso dell'endpoint per ognuno di essi.

```powershell
Get-AzureRmEnvironment | select Name, StorageEndpointSuffix 
```

Questo comando restituisce i risultati seguenti.

| Nome| StorageEndpointSuffix|
|----|----|
|AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | core.usgov.cloudapi.net |


Per recuperare tutte le proprietà per l'ambiente specificato, chiamare **Get-AzureRmEnvironment** e specificare il nome del cloud. Questo frammento di codice restituisce un elenco di proprietà. Cercare **StorageEndpointSuffix** nell'elenco. L'esempio seguente si riferisce al cloud per la Germania.

```powershell
Get-AzureRmEnvironment -Name AzureGermanCloud 
```

I risultati sono simili a quanto segue:

|Nome proprietà|Valore|
|----|----|
| Nome | AzureGermanCloud |
| EnableAdfsAuthentication | False |
| ActiveDirectoryServiceEndpointResourceI | http://management.core.cloudapi.de/ |
| GalleryURL | https://gallery.cloudapi.de/ |
| ManagementPortalUrl | https://portal.microsoftazure.de/ | 
| ServiceManagementUrl | https://manage.core.cloudapi.de/ |
| PublishSettingsFileUrl| https://manage.microsoftazure.de/publishsettings/index |
| ResourceManagerUrl | http://management.microsoftazure.de/ |
| SqlDatabaseDnsSuffix | .database.cloudapi.de |
| **StorageEndpointSuffix** | core.cloudapi.de |
| ... | ... | 

Per recuperare solo la proprietà suffisso dell'endpoint di archiviazione, recuperare il cloud specifico e richiedere solo tale proprietà.

```powershell
$environment = Get-AzureRmEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix 
```

Vengono restituite le informazioni seguenti.

```
Storage Endpoint Suffix = core.cloudapi.de
```

### <a name="get-endpoint-from-a-storage-account"></a>Ottenere l'endpoint da un account di archiviazione

È anche possibile esaminare le proprietà di un account di archiviazione per recuperare gli endpoint. Ciò può essere utile se si usa già un account di archiviazione nello script di PowerShell, perché è possibile recuperare solo l'endpoint necessario. 

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzureRmStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

Per un account di archiviazione nel cloud per enti pubblici, viene restituito quanto segue: 

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>Dopo aver configurato l'ambiente

Da questo punto in poi è possibile usare la stessa istanza di PowerShell usata per gestire gli account di archiviazione e accedere al piano dati, come descritto nell'articolo [Uso di Azure PowerShell con Archiviazione di Azure](storage-powershell-guide-full.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Se per questa esercitazione sono stati creati un nuovo gruppo di risorse e un account di archiviazione, è possibile rimuovere tutti gli risorsa creati rimuovendo il gruppo di risorse. Così facendo vengono eliminate anche tutte le risorse in esso contenute. In questo caso, rimuove l'account di archiviazione creato e il gruppo di risorse stesso.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

* [Persistenza degli accessi utente tra le sessioni di PowerShell](/powershell/azure/context-persistence)
* [Archiviazione di Azure per enti pubblici](../../azure-government/documentation-government-services-storage.md)
* [Guida per sviluppatori di soluzioni Microsoft Azure per enti pubblici](../../azure-government/documentation-government-developer-guide.md)
* [Note per gli sviluppatori di applicazioni Azure per la Cina](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Documentazione di Azure per la Germania](../../germany/germany-welcome.md)