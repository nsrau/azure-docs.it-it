---
title: Eseguire operazioni di Archiviazione tabelle di Azure con PowerShell | Microsoft Docs
description: Eseguire operazioni di Archiviazione tabelle di Azure con PowerShell
services: cosmos-db
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: robinsh
ms.openlocfilehash: c09809e9cf513dbb9420f675bbf431c176f740bd
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Eseguire operazioni di Archiviazione tabelle di Azure con Azure PowerShell 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Il servizio Archiviazione tabelle di Azure è un archivio dati NoSQL, che è possibile usare per archiviare ed eseguire query su grandi set di dati strutturati non relazionali. I componenti principali del servizio sono tabelle, entità e proprietà. una tabella è una raccolta di entità. Un'entità è un set di proprietà. Ogni entità può avere fino a 252 proprietà, che corrispondono tutte a coppie nome-valore. Questo articolo presuppone la conoscenza dei concetti relativi al servizio Archiviazione tabelle di Azure. Per informazioni dettagliate, vedere[Understanding the Table Service Data Model](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) (Informazioni sul modello di dati del servizio tabelle) e [Introduzione all'archiviazione tabelle di Azure con .NET](table-storage-how-to-use-dotnet.md).

In questo articolo con procedure sono illustrate le operazioni più comuni del servizio Archiviazione tabelle di Azure. Si apprenderà come: 

> [!div class="checklist"]
> * Creare una tabella
> * Recuperare una tabella
> * Aggiungere le entità di tabella
> * Eseguire query su una tabella
> * Eliminare le entità di tabella
> * Eliminare una tabella

In questo articolo con procedure viene illustrato come creare un nuovo account di archiviazione di Azure in un nuovo gruppo di risorse in modo da semplificarne la rimozione al termine della procedura. Se lo si preferisce, è possibile usare un account di archiviazione esistente.

Questo esempio richiede il modulo Azure PowerShell 4.4.0 o versioni successive. In una finestra di PowerShell eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se non ci sono risultati, è necessario eseguire l'aggiornamento. Vedere in proposito come [installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps). 

Dopo avere installato o aggiornato Azure PowerShell, è necessario installare il modulo **AzureRmStorageTable**, che include i comandi per la gestione delle entità. Per installare questo modulo, eseguire PowerShell come amministratore e usare il comando **Install-Module**.

```powershell
Install-Module AzureRmStorageTable
```

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando `Login-AzureRmAccount` e seguire le istruzioni visualizzate.

```powershell
Login-AzureRmAccount
```

## <a name="retrieve-list-of-locations"></a>Recuperare un elenco di posizioni

Se non si sa quale posizione usare, è possibile elencare le posizioni disponibili. Nell'elenco visualizzato trovare la posizione da usare. Questi esempi usano **eastus**. Archiviare questo valore nella variabile **location** per uso futuro.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/New-AzureRmResourceGroup). 

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Archiviare il nome del gruppo di risorse in una variabile per uso futuro. In questo esempio viene creato un gruppo di risorse denominato *pshtablesrg* nell'area *eastus*.

```powershell
$resourceGroup = "pshtablesrg"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Crea account di archiviazione

Creare un account di archiviazione standard per uso generico con archiviazione con ridondanza locale mediante [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Ottenere il contesto che definisce l'account di archiviazione da usare. Quando si usa un account di archiviazione, si può fare riferimento al contesto anziché fornire ripetutamente le credenziali.

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>Crea una nuova tabella

Per creare una tabella, utilizzare il cmdlet [New-AzureStorageTable](/powershell/module/azure.storage/New-AzureStorageTable). In questo esempio la tabella viene denominata `pshtesttable`.

```powershell
$tableName = "pshtesttable"
New-AzureStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>Recuperare un elenco di tabelle nell'account di archiviazione

Recuperare un elenco di tabelle nell'account di archiviazione con [Get-AzureStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
Get-AzureStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Recuperare un riferimento a una tabella specifica

Per eseguire operazioni su una tabella, è necessario un riferimento a una tabella specifica. Ottenere un riferimento con [Get-AzureStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable). 

```powershell
$storageTable = Get-AzureStorageTable –Name $tableName –Context $ctx
```

[!INCLUDE [storage-table-entities-powershell-include](../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Eliminare una tabella

Per eliminare una tabella, usare [Remove-AzureStorageTable](/powershell/module/azure.storage/Remove-AzureStorageTable). Questo cmdlet rimuove la tabella, inclusi tutti i relativi dati.

```powershell
Remove-AzureStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzureStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se per questa esercitazione sono stati creati un nuovo gruppo di risorse e un account di archiviazione, è possibile rimuovere tutti gli asset creati rimuovendo il gruppo di risorse. Questo comando elimina tutte le risorse contenute nel gruppo di risorse e il gruppo stesso.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo con procedure sono state illustrate le operazioni di Archiviazione tabelle di Azure con PowerShell, tra cui: 

> [!div class="checklist"]
> * Creare una tabella
> * Recuperare una tabella
> * Aggiungere le entità di tabella
> * Eseguire query su una tabella
> * Eliminare le entità di tabella
> * Eliminare una tabella

Per altre informazioni, vedere gli articoli seguenti:

* [Cmdlet di PowerShell per l'archiviazione](/powershell/module/azurerm.storage#storage)

* [Uso delle tabelle di Archiviazione di Azure con PowerShell](https://blogs.technet.microsoft.com/paulomarques/2017/01/17/working-with-azure-storage-tables-from-powershell/)

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) è un'app autonoma gratuita di Microsoft che consente di rappresentare facilmente dati di Archiviazione di Azure in Windows, macOS e Linux.