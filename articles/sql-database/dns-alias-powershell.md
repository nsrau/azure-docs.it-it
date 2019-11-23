---
title: PowerShell for DNS alias
description: PowerShell cmdlets such as New-AzSqlServerDNSAlias enable you to redirect new client connections to a different Azure SQL Database server, without having to touch any client configuration.
keywords: DNS per il database SQL
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: 9232a99ddd29201e6743c09455d79e9ba22b3b9c
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420396"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>PowerShell per l'alias DNS per il database SQL di Azure

Questo articolo fornisce uno script di PowerShell che illustra come gestire un alias DNS per il database SQL di Azure.

> [!NOTE]
> This article has been updated to use either the Azure PowerShell Az module or Azure CLI. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020.
>
> To learn more about the Az module and AzureRM compatibility, see [Introducing the Azure PowerShell Az module](/powershell/azure/new-azureps-module-az). For installation instructions, see [Install Azure PowerShell](/powershell/azure/install-az-ps) or [Install Azure CLI](/cli/azure/install-azure-cli).

## <a name="dns-alias-in-connection-string"></a>Alias DNS nella stringa di connessione

Per connettere un determinato server di database SQL di Azure, un client come SQL Server Management Studio (SSMS) può specificare il nome dell'alias DNS al posto del nome reale del server. Nella stringa server di esempio seguente, l'alias *any-unique-alias-name* sostituisce il primo nodo delimitato da punto nella stringa server di quattro nodi:

   `<yourServer>.database.windows.net`

## <a name="prerequisites"></a>Prerequisiti

Se si vuole eseguire lo script di PowerShell di esempio di questo articolo, sono necessari i prerequisiti seguenti:

- An Azure subscription and account, for free trial, see [Azure trials](https://azure.microsoft.com/free/)
- Two Azure SQL database servers

## <a name="example"></a>Esempio

The following code example starts by assigning literal values to several variables.

To run the code, edit the placeholder values to match real values in your system.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

The cmdlets used are the following:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Creates a DNS alias in the Azure SQL Database service system. The alias refers to database server 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Get and list all the aliases assigned to SQL DB server 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Modifies the server name that the alias is configured to refer to, from server 1 to server 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Remove the alias from database server 2, by using the name of the alias.

Per eseguire l'installazione o l'aggiornamento, vedere [Installare il modulo di Azure PowerShell](/powershell/azure/install-az-ps).

Use `Get-Module -ListAvailable Az` in *powershell\_ise.exe*, to find the version.

```powershell
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
Connect-AzAccount -SubscriptionName $subscriptionName;
$subscriptionId = Get-AzSubscription -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
New-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName `
    -Name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
Set-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -TargetServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName `
    -SourceServerResourceGroup $resourceGroupName -SourceServerName $sqlServerName `
    -SourceServerSubscriptionId $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
Remove-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName;
```

# <a name="azure-clitabazure-cli"></a>[interfaccia della riga di comando di Azure](#tab/azure-cli)

The commands used are the following:

- [az sql server dns-alias create](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Creates a DNS alias in the Azure SQL Database service system. The alias refers to database server 1.
- [az sql server dns-alias show](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Get and list all the aliases assigned to SQL DB server 1.
- [az sql server dns-alias set](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Modifies the server name that the alias is configured to refer to, from server 1 to server 2.
- [az sql server dns-alias delete](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Remove the alias from database server 2, by using the name of the alias.

Per installare o eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

```azurecli-interactive
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
az login -SubscriptionName $subscriptionName;
$subscriptionId = az account list[0].i -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
az sql server dns-alias create –-resource-group $resourceGroupName --server $sqlServerName `
    --name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
az sql server dns-alias show –-resource-group $resourceGroupName --server $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
az sql server dns-alias set –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName `
    --original-resource-group $resourceGroupName --original-server $sqlServerName `
    --original-subscription-id $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
az sql server dns-alias show –-resource-group $resourceGroupName2 --server $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
az sql server dns-alias delete –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName;
```

* * *

## <a name="next-steps"></a>Passaggi successivi

For a full explanation of the DNS alias feature for SQL Database, see [DNS alias for Azure SQL database](dns-alias-overview.md).
