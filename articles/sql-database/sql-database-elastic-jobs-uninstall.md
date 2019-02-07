---
title: Come disinstallare lo strumento processi di database elastico
description: Informazioni su come disinstallare i componenti dei processi di database elastico usando il portale di Azure o PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-scale
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 2131aa63f0f5cdbbc9e0b4eb7c1f57604fe75cdb
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754955"
---
# <a name="uninstall-elastic-database-jobs-components"></a>Disinstallare i componenti dei processi di database elastici


[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]


I componenti dei **processi di database elastico** possono essere disinstallati utilizzando il portale di Azure o PowerShell.

## <a name="uninstall-elastic-database-jobs-components-using-the-azure-portal"></a>Disinstallare i componenti dei processi di database elastici utilizzando il portale di Azure
1. Aprire il [portale di Azure](https://portal.azure.com/).
2. Passare alla sottoscrizione che contiene i componenti dei **processi di database elastici** , vale a dire la sottoscrizione in cui i componenti dei processi di database elastici sono stati installati.
3. Fare clic su **Sfoglia** e quindi su **Gruppi di risorse**.
4. Selezionare il gruppo di risorse denominato "__ElasticDatabaseJob".
5. Eliminare il gruppo di risorse.

## <a name="uninstall--elastic-database-jobs-components-using-powershell"></a>Disinstallare i componenti dei processi dei database elastici tramite PowerShell
1. Avviare una finestra di comando di Microsoft Azure PowerShell e passare alla sottodirectory tools nella cartella Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x: Digitare **cd tools**.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools
2. Eseguire lo script .\UninstallElasticDatabaseJobs.ps1 di PowerShell.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\UninstallElasticDatabaseJobs.ps1   PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\UninstallElasticDatabaseJobs.ps1

O semplicemente, eseguire lo script seguente, supponendo che i valori predefiniti se utilizzati nell'installazione dei componenti:

```powershell
$ResourceGroupName = "__ElasticDatabaseJob"
Switch-AzureMode AzureResourceManager

$resourceGroup = Get-AzureResourceGroup -Name $ResourceGroupName
if(!$resourceGroup)
{
     Write-Host "The Azure Resource Group: $ResourceGroupName has already been deleted.  Elastic database job components are uninstalled."
     return
}

Write-Host "Removing the Azure Resource Group: $ResourceGroupName.  This may take a few minutes.”
Remove-AzureResourceGroup -Name $ResourceGroupName -Force
Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job components are now uninstalled."
```

## <a name="next-steps"></a>Passaggi successivi
Per reinstallare i processi di database elastici, vedere [Installazione del servizio processo di database elastico](sql-database-elastic-jobs-service-installation.md)

Per ulteriori informazioni sui processi dei database elastici, vedere [Panoramica dei processi di database elastici](sql-database-elastic-jobs-overview.md).
