---
title: Eseguire operazioni dell'API di tabella di Azure Cosmos DB con PowerShell | Microsoft Docs
description: Come eseguire operazioni dell'API di tabella di Azure Cosmos DB con PowerShell
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/15/2017
ms.author: robinsh
ms.openlocfilehash: d0f835db8a9fbe3833a9c7931ad1d8b4a778f016
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="perform-azure-cosmos-db-table-api-operations-with-azure-powershell"></a>Eseguire operazioni dell'API di tabella di Azure Cosmos DB con Azure PowerShell 

>[!NOTE]
>L'API di tabella di Azure Cosmos DB offre funzionalità Premium per l'archiviazione di tabelle, ad esempio distribuzione globale chiavi in mano, operazioni di lettura e scrittura a bassa latenza, indicizzazione secondaria automatica e velocità effettiva dedicata. Nella maggior parte dei casi i comandi di PowerShell descritti in questo articolo sono validi sia per l'API di tabella di Azure Cosmos DB che per Archiviazione tabelle di Azure. Tuttavia, in questo articolo si fa specifico riferimento all'API di tabella di Azure Cosmos DB. Se si usa Archiviazione tabelle di Azure, vedere [Eseguire operazioni di Archiviazione tabelle di Azure con Azure PowerShell](table-storage-how-to-use-powershell.md).
>

L'API di tabella di Azure Cosmos DB consente di archiviare ed eseguire query su grandi set di dati strutturati non relazionali. I componenti principali del servizio sono tabelle, entità e proprietà. una tabella è una raccolta di entità. Un'entità è un set di proprietà. Ogni entità può avere fino a 252 proprietà, che corrispondono tutte a coppie nome-valore. Questo articolo presuppone la conoscenza dei concetti relativi all'API di tabella di Azure Cosmos DB. Per informazioni dettagliate, vedere [Introduzione all'API di tabella di Azure Cosmos DB](table-introduction.md) e [Creare un'applicazione .NET tramite l'API di tabella](create-table-dotnet.md).

Questa procedura illustra le operazioni più comuni nell'API di tabella. Si apprenderà come: 

> [!div class="checklist"]
> * Creare una tabella
> * Recuperare una tabella
> * Aggiungere le entità di tabella
> * Eseguire query su una tabella
> * Eliminare le entità di tabella

## <a name="prerequisites"></a>Prerequisiti

Questo esempio richiede il modulo Azure PowerShell 4.4.0 o versioni successive. In una finestra di PowerShell eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se non ci sono risultati, è necessario eseguire l'aggiornamento. Vedere in proposito come [installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps). 

Dopo avere installato o aggiornato Azure PowerShell, è necessario installare il modulo **AzureRmStorageTable**, che include i comandi per la gestione delle entità. Per installare questo modulo, eseguire PowerShell come amministratore e usare il comando **Install-Module**.

```powershell
Install-Module AzureRmStorageTable
```

Per usare i cmdlet di PowerShell, installare gli assembly di Azure Cosmos DB localmente. Per le istruzioni su come procedere, vedere [Azure RM Storage Tables PowerShell module for Cosmos DB Tables](https://blogs.technet.microsoft.com/paulomarques/2017/05/23/azure-rm-storage-tables-powershell-module-now-includes-support-for-cosmos-db-tables/) (Modulo PowerShell delle tabelle di archiviazione di Azure RM per le tabelle di Cosmos DB).

Per provare gli esercizi seguenti, è necessario un account di database Azure Cosmos DB. Se non è già presente, creare un nuovo account Azure Cosmos DB usando il [portale di Azure](https://portal.azure.com). Per informazioni sulla creazione di un nuovo account di database, vedere [Azure Cosmos DB: creare un account di database](create-table-dotnet.md#create-a-database-account).

Ottenere il gruppo di risorse e il nome dell'account di database dal portale. È necessario inserire questi valori nello script per accedere alle tabelle. 

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando `Login-AzureRmAccount` e seguire le istruzioni visualizzate.

```powershell
Login-AzureRmAccount
```

## <a name="create-a-table-or-reference-a-table"></a>Creare una tabella o fare riferimento a una tabella

Per creare una tabella o per ottenere un riferimento a una tabella, usare **Get-AzureStorageTableTable**. Se si chiama questo cmdlet con il nome di una tabella che non esiste, viene creata una nuova tabella con tale nome e viene restituito un riferimento alla nuova tabella. Se la tabella esiste, viene restituito un riferimento alla tabella esistente.

```powershell
# set the name of the resource group in which your Cosmos DB Account resides.
$resourceGroup = "contosocosmosrg"
# if you want to make sure the resource group is valid, try this command
Get-AzureRmResourceGroup -Name $resourceGroup

# set the Cosmos DB account name 
$cosmosDBAccountName = "contosocosmostbl" 

# set the table name 
$tableName = "contosotable1"

# Get a reference to a table. This creates the table if it doesn't exist.
$storageTable = Get-AzureStorageTableTable `
  -resourceGroup $resourceGroup `
  -tableName $tableName `
  -cosmosDbAccount $cosmosDBAccountName 
```

Non è possibile elencare le tabelle nell'account Azure Cosmos DB usando PowerShell, ma è possibile accedere al portale e visualizzare la tabella. Verrà ora illustrato come gestire le entità nella tabella.

[!INCLUDE [storage-table-entities-powershell-include](../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Eliminare una tabella 

PowerShell non supporta l'eliminazione delle tabelle da Azure Cosmos DB. Per eliminare una tabella, andare al [portale di Azure](https://azure.portal.com), individuare l'account Azure Cosmos DB usato, quindi trovare ed eliminare la tabella. 

## <a name="clean-up-resources"></a>Pulire le risorse

Se per questa esercitazione sono stati creati un nuovo gruppo di risorse e un nuovo account Azure Cosmos DB in tale gruppo, è possibile rimuovere tutti gli asset creati rimuovendo il gruppo di risorse. Questo comando elimina tutte le risorse contenute nel gruppo di risorse e il gruppo stesso.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo con procedure sono state illustrate le operazioni dell'API di tabella con PowerShell, tra cui: 

> [!div class="checklist"]
> * Creare una tabella
> * Recuperare una tabella
> * Aggiungere le entità di tabella
> * Eseguire query su una tabella
> * Eliminare le entità di tabella

Per altre informazioni, vedere gli articoli seguenti:

* [Uso delle tabelle di Archiviazione di Azure con PowerShell](https://blogs.technet.microsoft.com/paulomarques/2017/01/17/working-with-azure-storage-tables-from-powershell/)

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) è un'app autonoma gratuita di Microsoft che consente di rappresentare facilmente dati di Archiviazione di Azure in Windows, macOS e Linux.