---
title: Usare PowerShell per gestire i dati nei cloud indipendenti di Azure
titleSuffix: Azure Storage
description: Gestione dell'archiviazione nel cloud cinese, nel cloud per enti pubblici e nel cloud tedesco con Azure PowerShell.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5fa515515c06466e121a5c0ee925fd4d14245363
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895244"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Gestione dell'archiviazione nei cloud indipendenti di Azure con PowerShell

La maggior parte delle persone usa il cloud pubblico di Azure per la distribuzione globale di Azure. Per motivi di sovranità e altro, sono disponibili anche alcune distribuzioni indipendenti di Microsoft Azure, denominate "ambienti". L'elenco seguente illustra in dettaglio i cloud indipendenti attualmente disponibili.

* [Cloud di Azure per enti pubblici](https://azure.microsoft.com/features/gov/)
* [Azure China 21Vianet cloud gestito da 21Vianet in Cina](http://www.windowsazure.cn/)
* [Cloud di Azure per la Germania](../../germany/germany-welcome.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="using-an-independent-cloud"></a>Uso di un cloud indipendente

Per usare Archiviazione di Azure in uno dei cloud indipendenti, ci si connette a tale cloud anziché al cloud pubblico di Azure. Per usare uno dei cloud indipendenti anziché il cloud pubblico di Azure:

* Specificare l'*ambiente* a cui connettersi.
* Determinare e usare le aree disponibili.
* Usare il suffisso corretto dell'endpoint, che è diverso rispetto al cloud pubblico di Azure.

Questi esempi richiedono il modulo Azure PowerShell Az 0.7 o versioni successive. In una finestra di PowerShell eseguire `Get-Module -ListAvailable Az` per trovare la versione. Se non ci sono risultati, è necessario eseguire l'aggiornamento. Vedere in proposito come [installare il modulo Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="log-in-to-azure"></a>Accedere ad Azure

Eseguire il cmdlet [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment) per visualizzare gli ambienti Azure disponibili:

```powershell
Get-AzEnvironment
```

Accedere all'account che ha accesso al cloud a cui ci si vuole connettere e impostare l'ambiente. Questo esempio illustra come accedere a un account che usa il cloud di Azure per enti pubblici.   

```powershell
Connect-AzAccount –Environment AzureUSGovernment
```

Per accedere al cloud per la Cina, usare l'ambiente **AzureChinaCloud**. Per accedere al cloud per la Germania, usare **AzureGermanCloud**.

A questo punto, se è necessario l'elenco di località per creare un account di archiviazione o un'altra risorsa, è possibile eseguire query sulle località disponibili per il cloud selezionato tramite [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

```powershell
Get-AzLocation | select Location, DisplayName
```

La tabella seguente mostra le località restituite per il cloud per la Germania.

|Località | Nome visualizzato |
|----|----|
| `germanycentral` | Germania centrale|
| `germanynortheast` | Germania nord-orientale |


## <a name="endpoint-suffix"></a>Suffisso dell'endpoint

Il suffisso dell'endpoint per ognuno di questi ambienti è diverso dall'endpoint pubblico di Azure. Ad esempio, il suffisso dell'endpoint BLOB del cloud pubblico di Azure è **blob.core.windows.net**. Per il cloud per enti pubblici, il suffisso dell'endpoint BLOB è **blob.core.usgovcloudapi.net**.

### <a name="get-endpoint-using-get-azenvironment"></a>Ottenere l'endpoint tramite Get-AzEnvironment

Recuperare il suffisso dell'endpoint con [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment). L'endpoint è la proprietà *StorageEndpointSuffix* dell'ambiente.

Nei frammenti di codice seguenti viene illustrato come recuperare il suffisso dell'endpoint. Tutti questi comandi restituiscono un valore simile a "core.cloudapp.net" o "core.cloudapi.de" e così via. Aggiungere il suffisso al servizio di archiviazione per accedere al servizio. Ad esempio, "queue.core.cloudapi.de" accede al servizio di accodamento del cloud per la Germania.

Questo frammento di codice recupera tutti gli ambienti e il suffisso dell'endpoint per ognuno di essi.

```powershell
Get-AzEnvironment | select Name, StorageEndpointSuffix 
```

Questo comando restituisce i risultati seguenti.

| name| StorageEndpointSuffix|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | core.usgovcloudapi.net |

Per recuperare tutte le proprietà per l'ambiente specificato, chiamare **Get-AzEnvironment** e specificare il nome del cloud. Questo frammento di codice restituisce un elenco di proprietà. Cercare **StorageEndpointSuffix** nell'elenco. L'esempio seguente si riferisce al cloud per la Germania.

```powershell
Get-AzEnvironment -Name AzureGermanCloud
```

I risultati sono simili ai valori seguenti:

|Nome proprietà|Value|
|----|----|
| name | `AzureGermanCloud` |
| EnableAdfsAuthentication | `False` |
| ActiveDirectoryServiceEndpointResourceI | `http://management.core.cloudapi.de/` |
| GalleryURL | `https://gallery.cloudapi.de/` |
| ManagementPortalUrl | `https://portal.microsoftazure.de/` |
| ServiceManagementUrl | `https://manage.core.cloudapi.de/` |
| PublishSettingsFileUrl| `https://manage.microsoftazure.de/publishsettings/index` |
| ResourceManagerUrl | `http://management.microsoftazure.de/` |
| SqlDatabaseDnsSuffix | `.database.cloudapi.de` |
| **StorageEndpointSuffix** | `core.cloudapi.de` |
| ... | ... |
Per recuperare solo la proprietà suffisso dell'endpoint di archiviazione, recuperare il cloud specifico e richiedere solo tale proprietà.

```powershell
$environment = Get-AzEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix
```

Questo comando restituisce le informazioni seguenti:

`Storage Endpoint Suffix = core.cloudapi.de`

### <a name="get-endpoint-from-a-storage-account"></a>Ottenere l'endpoint da un account di archiviazione

È anche possibile esaminare le proprietà di un account di archiviazione per recuperare gli endpoint:

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

Per un account di archiviazione nel cloud per enti pubblici, questo comando restituisce l'output seguente:

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>Dopo aver configurato l'ambiente

Da questo punto in poi è possibile usare la stessa istanza di PowerShell usata per gestire gli account di archiviazione e accedere al piano dati, come descritto nell'articolo [Uso di Azure PowerShell con Archiviazione di Azure](storage-powershell-guide-full.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Se sono stati creati un nuovo gruppo di risorse e un account di archiviazione per questo esercizio, è possibile rimuovere entrambi gli asset eliminando il gruppo di risorse. Se si elimina un gruppo di risorse, vengono eliminate tutte le risorse contenute in esso.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

* [Persistenza degli accessi utente tra le sessioni di PowerShell](/powershell/azure/context-persistence)
* [Archiviazione di Azure per enti pubblici](../../azure-government/documentation-government-services-storage.md)
* [Guida per sviluppatori di soluzioni Microsoft Azure per enti pubblici](../../azure-government/documentation-government-developer-guide.md)
* [Note per gli sviluppatori per le applicazioni Azure Cina 21Vianet](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Documentazione di Azure per la Germania](../../germany/germany-welcome.md)
