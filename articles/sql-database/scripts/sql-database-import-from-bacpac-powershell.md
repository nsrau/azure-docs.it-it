---
title: 'Script di Azure PowerShell: eseguire l&quot;importazione da BACPAC in un database SQL | Microsoft Docs'
description: 'Esempio di script di Azure PowerShell: eseguire l&quot;importazione da un file BACPAC in un database SQL usando PowerShell'
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: PowerShell
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/07/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 6ca1560d6d3fa313bd24cb6226cf1e78992af257
ms.lasthandoff: 03/10/2017

---

# <a name="import-from-a-bacpac-into-a-sql-database-using-powershell"></a>Eseguire l'importazione da un file BACPAC in un database SQL usando PowerShell

Questo esempio di script di PowerShell importa un database da un file BACPAC.  

Prima di eseguire questo script, verificare che sia stata creata una connessione con Azure tramite il cmdlet `Add-AzureRmAccount`.

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/sql-database/import-from-bacpac/import-from-bacpac.ps1 "Creare database SQL")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo l'esecuzione dello script di esempio, è possibile usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse ad esso associate.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzureRmResourceGroup]() | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzureRmSqlServer]() | Consente di creare un server logico che ospita il database SQL. |
| [New-AzureRmSqlServerFirewallRule]() | Consente di creare una regola del firewall per consentire l'accesso a tutti i database SQL presenti sul server dall'intervallo di indirizzi IP immesso. |
| [New-AzureRmSqlDatabase]() | Consente di creare il database SQL nel server logico. |
| [Remove-AzureRmResourceGroup]() | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/).

Per altri esempi, vedere tra gli [script di PowerShell per database SQL di Azure](../sql-database-powershell-samples.md).
