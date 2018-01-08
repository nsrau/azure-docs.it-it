---
title: Guida introduttiva di Azure - Creare un account di archiviazione con PowerShell | Microsoft Docs
description: Apprendere a creare rapidamente un nuovo account di archiviazione con PowerShell
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/29/2017
ms.author: tamram
ms.openlocfilehash: 87f179d2cf3802af3eca58e0adf57b07069bfcc6
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2017
---
# <a name="create-a-storage-account-using-powershell"></a>Creare un account di archiviazione con PowerShell

Il modulo Azure PowerShell viene usato per creare e gestire le risorse di Azure dalla riga di comando di PowerShell o negli script. Questa guida descrive l'uso di PowerShell per creare un account di Archiviazione di Azure. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Questa guida introduttiva richiede il modulo Azure PowerShell 3.6 o versioni successive. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando `Login-AzureRmAccount` e seguire le istruzioni visualizzate.

```powershell
Login-AzureRmAccount
```

Se non si sa quale posizione usare, è possibile elencare le posizioni disponibili. Nell'elenco visualizzato trovare la posizione da usare. Questo esempio usa **eastus**. Archiviare questo valore in una variabile e usare la variabile in modo da poter eseguire la modifica in un'unica posizione.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

```powershell
# put resource group in a variable so you can use the same group name going forward
#   without hardcoding it repeatedly
$resourceGroup = "contoso-storage-accounts"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 
```

## <a name="create-a-general-purpose-storage-account"></a>Creare un account di archiviazione per utilizzo generico

Esistono diversi tipi di account di archiviazione, a seconda della modalità d'uso prevista e del servizio associato (BLOB, file, tabelle o code). Questa tabella visualizza le diverse possibilità.

|**Tipo di account di archiviazione**|**Utilizzo generico Standard**|**Utilizzo generico Premium**|**Archiviazione BLOB, livelli ad accesso frequente e ad accesso sporadico**|
|-----|-----|-----|-----|
|**Servizi supportati**| Servizi BLOB, file, tabella, coda | Servizio BLOB | Servizio BLOB|
|**Tipi di BLOB supportati**|BLOB in blocchi, BLOB di pagine e BLOB di aggiunta | BLOB di pagine | BLOB in blocchi e BLOB di aggiunta|

Usare [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount) per creare un account di archiviazione per utilizzo generico da usare per tutti e quattro i servizi. Assegnare all'account di archiviazione il nome *contosomvcstandard* e configurarlo con Archiviazione con ridondanza locale e con la crittografia BLOB abilitata.

```powershell
New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "contosomvcstandard" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse correlate non sono più necessari, è possibile usare il comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuoverli. In questo caso viene rimosso l'account di archiviazione creato.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stato creato un account di archiviazione standard per uso generico. Per informazioni su come caricare e scaricare BLOB con l'account di archiviazione, vedere la guida introduttiva dell'archiviazione BLOB.
> [!div class="nextstepaction"]
> [Trasferire oggetti da e verso la risorsa di archiviazione BLOB di Azure usando PowerShell](../blobs/storage-quickstart-blobs-powershell.md)