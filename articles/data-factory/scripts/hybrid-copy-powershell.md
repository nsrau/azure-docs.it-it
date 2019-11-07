---
title: "Script di PowerShell: copiare i dati dall'ambiente locale ad Azure usando Data Factory "
description: Lo script PowerShell consente di copiare dati da un database di SQL Server locale in un altro di Archiviazione BLOB di Azure.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2017
ms.author: jingwang
ms.openlocfilehash: d7f8d67291c0b6b2a384331c014fdd5cd247ceae
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684354"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-from-on-premises-to-azure"></a>Usare PowerShell per creare una pipeline di Data Factory per copiare dati in locale in Azure

In questo esempio si usa PowerShell per creare una pipeline di Azure Data Factory che copia i dati da un database di SQL Server locale a un database di Archiviazione BLOB di Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Prerequisiti

- **SQL Server**. In questo esempio si usa un database di SQL Server locale come archivio dati di **origine**.
- **Account di archiviazione di Azure**. In questo esempio l'archivio BLOB di Azure viene usato come archivio dati di **destinazione/sink**. Se non si ha un account di archiviazione di Azure, vedere l'articolo [Creare un account di archiviazione](../../storage/common/storage-quickstart-create-account.md) per informazioni su come crearne uno.
- **Runtime di integrazione self-hosted**. Scaricare il file con estensione msi dall'[area download](https://www.microsoft.com/download/details.aspx?id=39717) ed eseguirlo per installare un runtime di integrazione self-hosted nel computer.  

### <a name="create-sample-database-in-sql-server"></a>Creare un database di esempio in SQL Server
1. Nel database di SQL Server locale creare una tabella denominata **emp** tramite lo script SQL seguente: 

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. Inserire alcuni dati di esempio nella tabella:

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```

## <a name="sample-script"></a>Script di esempio

> [!IMPORTANT]
> Questo script crea file JSON che definiscono entità di Data Factory (servizio collegato, set di dati e pipeline) nella cartella c:\ del disco rigido.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-onprem-sql-server-to-azure-blob/copy-from-onprem-sql-server-to-azure-blob.ps1 "Copy from on-premises SQL Server -> Azure Blob Storage")]


## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo aver eseguito lo script di esempio, è possibile eseguire il comando seguente per rimuovere il gruppo di risorse e tutte le risorse associate a questo:

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
| [New-AzDataFactoryV2LinkedServiceEncryptCredential](/powershell/module/az.datafactory/new-Azdatafactoryv2linkedserviceencryptedcredential) | Consente di crittografare le credenziali in un servizio collegato e generare una nuova definizione di servizio collegato con le credenziali crittografate. 
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
