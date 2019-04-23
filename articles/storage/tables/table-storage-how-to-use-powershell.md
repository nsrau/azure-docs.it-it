---
title: Eseguire operazioni di Archiviazione tabelle di Azure con PowerShell | Microsoft Docs
description: Eseguire operazioni di Archiviazione tabelle di Azure con PowerShell.
services: cosmos-db
author: roygara
ms.service: cosmos-db
ms.topic: article
ms.date: 04/05/2019
ms.author: rogarana
ms.subservice: cosmosdb-table
ms.openlocfilehash: b1cae7dc553ce324349e66f1bcb8a281d7c7c7e0
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995606"
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Eseguire operazioni di Archiviazione tabelle di Azure con Azure PowerShell 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Il servizio Archiviazione tabelle di Azure è un archivio dati NoSQL, che è possibile usare per archiviare ed eseguire query su grandi set di dati strutturati non relazionali. I componenti principali del servizio sono tabelle, entità e proprietà. una tabella è una raccolta di entità. Un'entità è un set di proprietà. Ogni entità può avere fino a 252 proprietà, che corrispondono tutte a coppie nome-valore. Questo articolo presuppone la conoscenza dei concetti relativi al servizio Archiviazione tabelle di Azure. Per informazioni dettagliate, vedere[Understanding the Table Service Data Model](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) (Informazioni sul modello di dati del servizio tabelle) e [Introduzione all'archiviazione tabelle di Azure con .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md).

In questo articolo con procedure sono illustrate le operazioni più comuni del servizio Archiviazione tabelle di Azure. Si apprenderà come: 

> [!div class="checklist"]
> * Creare una tabella
> * Recuperare una tabella
> * Aggiungere le entità di tabella
> * Eseguire query su una tabella
> * Eliminare le entità di tabella
> * Eliminare una tabella

In questo articolo con procedure viene illustrato come creare un nuovo account di archiviazione di Azure in un nuovo gruppo di risorse in modo da semplificarne la rimozione al termine della procedura. Se lo si preferisce, è possibile usare un account di archiviazione esistente.

Gli esempi richiedono i moduli di PowerShell di Az `Az.Storage (1.1.0 or greater)` e `Az.Resources (1.2.0 or greater)`. In una finestra di PowerShell eseguire `Get-Module -ListAvailable Az*` per trovare la versione. Se non ci sono risultati, è necessario eseguire l'aggiornamento. Vedere in proposito come [installare il modulo Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Se si usa questa funzionalità di Azure da PowerShell, è necessario che sia installato il modulo `Az`. La versione corrente di `AzTable` non è compatibile con il modulo AzureRM meno recente.
> Seguire le [istruzioni per installare il modulo di Az di installazione più recente](/powershell/azure/install-az-ps) se necessario.

Dopo che Azure PowerShell è installato o aggiornato, è necessario installare un modulo **AzTable**, che include i comandi per gestire le entità. Per installare questo modulo, eseguire PowerShell come amministratore e usare il comando **Install-Module**.

> [!IMPORTANT]
> Per il modulo compatibilità nome motivi di questo stesso modulo sotto il nome precedente ancora sta per essere pubblicata `AzureRmStorageTables` in PowerShell Gallery. Questo documento farà riferimento solo il nuovo nome.

```powershell
Install-Module AzTable
```

## <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere alla sottoscrizione di Azure con il comando `Add-AzAccount` e seguire le istruzioni visualizzate.

```powershell
Add-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Recuperare un elenco di posizioni

Se non si sa quale posizione usare, è possibile elencare le posizioni disponibili. Nell'elenco visualizzato trovare la posizione da usare. Questi esempi usano **eastus**. Archiviare questo valore nella variabile **location** per uso futuro.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Archiviare il nome del gruppo di risorse in una variabile per uso futuro. In questo esempio viene creato un gruppo di risorse denominato *pshtablesrg* nell'area *eastus*.

```powershell
$resourceGroup = "pshtablesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Crea account di archiviazione

Creare un account di archiviazione standard per utilizzo generico con archiviazione con ridondanza locale mediante [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Assicurarsi di specificare un nome di account di archiviazione univoco. Successivamente, ottenere il contesto che rappresenta l'account di archiviazione. Quando si usa un account di archiviazione, è possibile fare riferimento al contesto anziché fornire ripetutamente le credenziali.

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>Crea una nuova tabella

Per creare una tabella, usare il [New-AzStorageTable](/powershell/module/az.storage/New-AzStorageTable) cmdlet. In questo esempio la tabella viene denominata `pshtesttable`.

```powershell
$tableName = "pshtesttable"
New-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>Recuperare un elenco di tabelle nell'account di archiviazione

Recuperare un elenco di tabelle nell'account di archiviazione con [Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
Get-AzStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Recuperare un riferimento a una tabella specifica

Per eseguire operazioni su una tabella, è necessario un riferimento a una tabella specifica. Ottenere un riferimento con [Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
$storageTable = Get-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="reference-cloudtable-property-of-a-specific-table"></a>Fare riferimento a proprietà CloudTable di una tabella specifica

> [!IMPORTANT]
> Utilizzo di CloudTable è obbligatorio quando si lavora **AzTable** modulo di PowerShell. Chiamare il **Get-AzTableTable** comando per ottenere il riferimento a questo oggetto. Questo comando crea la tabella anche se non esiste già.

Per eseguire operazioni su una tabella utilizzando **AzTable**, è necessario un riferimento a proprietà CloudTable di una tabella specifica.

```powershell
$cloudTable = (Get-AzStorageTable –Name $tableName –Context $ctx).CloudTable
```

[!INCLUDE [storage-table-entities-powershell-include](../../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Eliminare una tabella

Per eliminare una tabella, usare [Remove-AzStorageTable](/powershell/module/az.storage/Remove-AzStorageTable). Questo cmdlet rimuove la tabella, inclusi tutti i relativi dati.

```powershell
Remove-AzStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se per questa esercitazione sono stati creati un nuovo gruppo di risorse e un account di archiviazione, è possibile rimuovere tutti gli asset creati rimuovendo il gruppo di risorse. Questo comando elimina tutte le risorse contenute nel gruppo di risorse e il gruppo stesso.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
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

* [Cmdlet di PowerShell per l'archiviazione](/powershell/module/az.storage#storage)

* [Utilizzo di tabelle di Azure da PowerShell - AzureRmStorageTable/AzTable PS modulo v2.0](https://paulomarquesc.github.io/working-with-azure-storage-tables-from-powershell)

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) è un'app autonoma gratuita di Microsoft che consente di rappresentare facilmente dati di Archiviazione di Azure in Windows, macOS e Linux.
