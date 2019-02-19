---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 1f8f8d314a8bb37a08b3696f597b395a8a4beb8e
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213087"
---
## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando `Connect-AzAccount` e seguire le istruzioni visualizzate.

```powershell
Connect-AzAccount
```

Se non si sa quale posizione usare, è possibile elencare le posizioni disponibili. Per visualizzare l'elenco di posizioni e trovare quella desiderata, usare il codice di esempio seguente. Questo esempio usa **eastus**. Archiviare la posizione in una variabile e usare la variabile in modo da poter eseguire la modifica in un'unica posizione.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

```powershell
$resourceGroup = "myResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

Creare un account di archiviazione standard per uso generico con replica dell'archiviazione con ridondanza locale usando [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Ottenere quindi il contesto che definisce l'account di archiviazione da usare. Quando si usa un account di archiviazione, si può fare riferimento al contesto anziché passare ripetutamente le credenziali. Usare l'esempio seguente per creare un account di archiviazione denominato *mystorageaccount* con archiviazione con ridondanza locale e crittografia BLOB (abilitata per impostazione predefinita).

```powershell
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -SkuName Standard_LRS `
  -Location $location `

$ctx = $storageAccount.Context
```
