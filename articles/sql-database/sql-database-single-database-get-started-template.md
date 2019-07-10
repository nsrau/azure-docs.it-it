---
title: 'Azure Resource Manager: Creare un database singolo - Database SQL di Azure | Microsoft Docs'
description: Creare un database singolo nel database SQL di Azure usando il modello di Azure Resource Manager.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: carlrab
manager: craigg
ms.date: 06/28/2019
ms.openlocfilehash: 4ef0f9ff6f8620109f2ef6f6bd5f549281b4de54
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67472153"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>Guida introduttiva: Creare un database singolo nel database SQL di Azure usando il modello di Azure Resource Manager

La creazione di un [database singolo](sql-database-single-database.md) è l'opzione di distribuzione più semplice e rapida per la creazione di database nel database SQL di Azure. Questo argomento di avvio rapido descrive come creare un database singolo usando il modello di Azure Resource Manager. Per altre informazioni, vedere la [documentazione di Azure Resource Manager](/azure/azure-resource-manager/).

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Creare un database singolo

Un database singolo include un set definito di risorse di calcolo, memoria, I/O e archiviazione basate su uno dei due [modelli di acquisto](sql-database-purchase-models.md). Quando si crea un database singolo, si definisce anche un [server di database SQL](sql-database-servers.md) per gestirlo e lo si inserisce all'interno di un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) in un'area geografica specificata.

Il file JSON seguente è il modello usato in questo articolo. Il modello è archiviato in [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/SQLServerAndDatabase/azuredeploy.json). Altri esempi di modelli di database SQL di Azure sono disponibili in [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

[!code-json[create-azure-sql-database-server-and-database](~/resourcemanager-templates/SQLServerAndDatabase/azuredeploy.json)]

1. Selezionare **Provalo** dal blocco di codice PowerShell seguente per aprire Azure Cloud Shell.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
    $adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
    $adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

    $resourceGroupName = "${projectName}rg"


    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "D:\GitHub\azure-docs-json-samples\SQLServerAndDatabase\azuredeploy.json" -projectName $projectName -adminUser $adminUser -adminPassword $adminPassword

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

1. Selezionare **Copia** per copiare lo script di PowerShell negli appunti.
1. Fare clic con il pulsante destro del mouse sul riquadro della shell e quindi scegliere **Incolla**.

    Sono necessari alcuni minuti per creare il server di database e il database.

## <a name="query-the-database"></a>Eseguire query sul database

Per eseguire query sul database, vedere [Eseguire query sul database](./sql-database-single-database-get-started.md#query-the-database).

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole procedere con i [Passaggi successivi](#next-steps), conservare il gruppo di risorse, il server di database e il database singolo. I passaggi successivi illustrano come connettersi al database ed eseguire query con diversi metodi.

Eliminare il gruppo di risorse mediante Azure PowerShell:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Passaggi successivi

- Creare una regola del firewall a livello di server per connettersi al database singolo da strumenti locali o remoti. Per altre informazioni, vedere [Creare una regola di firewall a livello di server](sql-database-server-level-firewall-rule.md).
- Dopo aver creato una regola del firewall a livello di server, [connettersi al database ed eseguire query](sql-database-connect-query.md) usando diversi strumenti e linguaggi.
  - [Connettersi ed eseguire query usando SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Connettersi ed eseguire query usando Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Per creare un database singolo usando l'interfaccia della riga di comando di Azure, vedere [Esempi di interfaccia della riga di comando di Azure](sql-database-cli-samples.md).
- Per creare un database singolo usando Azure PowerShell, vedere [Esempi di Azure PowerShell](sql-database-powershell-samples.md).
