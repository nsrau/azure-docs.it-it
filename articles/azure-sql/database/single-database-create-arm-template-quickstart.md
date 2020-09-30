---
title: 'Azure Resource Manager: Creare un database singolo'
description: Creare un database singolo in Database SQL di Azure usando un modello di Azure Resource Manager.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: subject-armqs sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: sstein
ms.date: 06/24/2020
ms.openlocfilehash: a3589ec9b1a4cf652e7bcce16023a83a8b41ee5f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91319300"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-an-arm-template"></a>Avvio rapido: Creare un database singolo in Database SQL di Azure usando un modello di Resource Manager

La creazione di un [database singolo](single-database-overview.md) è l'opzione più semplice e rapida per la creazione di database nel database SQL di Azure. Questo argomento di avvio rapido illustra come creare un database singolo usando un modello di Azure Resource Manager.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-database%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Rivedere il modello

Un database singolo include un set definito di risorse di calcolo, memoria, I/O e archiviazione basate su uno dei due [modelli di acquisto](purchasing-models.md). Quando si crea un database singolo, si definisce anche un [server](logical-servers.md) per gestirlo e lo si colloca all'interno di un [gruppo di risorse di Azure](../../active-directory-b2c/overview.md) in un'area specificata.

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-sql-database/).

:::code language="json" source="~/quickstart-templates/101-sql-database/azuredeploy.json":::

Nel modello sono definite queste risorse:

- [**Microsoft.Sql/servers**](/azure/templates/microsoft.sql/servers)
- [**Microsoft.Sql/servers/databases**](/azure/templates/microsoft.sql/servers/databases)

Altri esempi di modelli di database SQL di Azure sono disponibili in [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuire il modello

Selezionare **Provalo** dal blocco di codice PowerShell seguente per aprire Azure Cloud Shell.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sql-database/azuredeploy.json" -administratorLogin $adminUser -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

Per eseguire query sul database, vedere [Eseguire query sul database](single-database-create-quickstart.md#query-the-database).

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole procedere con i [Passaggi successivi](#next-steps), conservare il gruppo di risorse, il server e il database singolo. I passaggi successivi illustrano come connettersi al database ed eseguire query con diversi metodi.

Per eliminare il gruppo di risorse:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Passaggi successivi

- Creare una regola del firewall a livello di server per connettersi al database singolo da strumenti locali o remoti. Per altre informazioni, vedere [Creare una regola di firewall a livello di server](firewall-create-server-level-portal-quickstart.md).
- Dopo aver creato una regola del firewall a livello di server, [connettersi al database ed eseguire query](connect-query-content-reference-guide.md) usando diversi strumenti e linguaggi.
  - [Connettersi ed eseguire query usando SQL Server Management Studio](connect-query-ssms.md)
  - [Connettersi ed eseguire query usando Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Per creare un database singolo usando l'interfaccia della riga di comando di Azure, vedere [Esempi di interfaccia della riga di comando di Azure](az-cli-script-samples-content-guide.md).
- Per creare un database singolo usando Azure PowerShell, vedere [Esempi di Azure PowerShell](powershell-script-content-guide.md).
- Per informazioni su come creare i modelli di Resource Manager, vedere [Creare il primo modello](../../azure-resource-manager/templates/template-tutorial-create-first-template.md).
