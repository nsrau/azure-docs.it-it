---
title: Copiare i dati nel cloud usando PowerShell
description: Lo script PowerShell copia i dati da una posizione nell'archiviazione BLOB di Azure a un'altra posizione nella stessa archiviazione BLOB.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.custom: seo-lt-2019
ms.date: 09/12/2017
ms.openlocfilehash: abc372355ce1a5350d205bce72a093fbfb1622e1
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929852"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-in-the-cloud"></a>Usare PowerShell per creare una pipeline di Data Factory per copiare dati nel cloud

In questo esempio si usa PowerShell per creare una pipeline di Azure Data Factory che copia i dati da una posizione a un'altra in un archivio BLOB di Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Prerequisiti
* **Account di archiviazione di Azure**. Usare l'archivio BLOB come archivio dati di **origine** e **sink**. Se non si ha un account di archiviazione di Azure, vedere [Creare un account di archiviazione](../../storage/common/storage-quickstart-create-account.md) per informazioni su come crearne uno. 
* Creare un **contenitore BLOB** in Archiviazione BLOB, creare una **cartella** di input nel contenitore e caricare alcuni file nella cartella. È possibile usare strumenti come [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) per connettersi all'archivio BLOB di Azure, creare un contenitore BLOB, caricare il file di input e verificare il file di output.

## <a name="sample-script"></a>Script di esempio

> [!IMPORTANT]
> Questo script crea file JSON che definiscono entità di Data Factory (servizio collegato, set di dati e pipeline) nella cartella c:\ del disco rigido.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-azure-blob-to-blob/copy-from-azure-blob-to-blob.ps1 "Copy from Blob Storage -> Blob Storage")]


## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo aver eseguito lo script di esempio, è possibile eseguire il comando seguente per rimuovere il gruppo di risorse e tutte le risorse a esso associate:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Per rimuovere la data factory dal gruppo di risorse, eseguire il comando seguente: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti: 

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Creare una data factory. |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Crea un servizio collegato nella data factory. Un servizio collegato collega un archivio dati o un ambiente di calcolo a una data factory. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Crea un set di dati nella data factory. Il set di dati rappresenta l'input e l'output per un'attività in una pipeline. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Crea una pipeline nella data factory. Una pipeline contiene una o più attività che eseguono un'operazione specifica. In questa pipeline l'attività di copia esegue la copia dei dati da una posizione a un'altra in un'archiviazione BLOB di Azure. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Crea un'esecuzione per la pipeline. In altre parole, esegue la pipeline. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Ottiene i dettagli dell'esecuzione dell'attività nella pipeline. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/).

Altri esempi di script PowerShell di Azure Data Factory sono disponibili in [Azure Data Factory PowerShell samples](../samples-powershell.md) (Esempi di PowerShell per Azure Data Factory).
