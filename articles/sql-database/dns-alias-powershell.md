---
title: PowerShell per l'alias DNS
description: I cmdlet di PowerShell, ad esempio New-AzSqlServerDNSAlias, consentono di reindirizzare nuove connessioni client a un server di database SQL di Azure diverso, senza dover toccare alcuna configurazione client.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74420396"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>PowerShell per l'alias DNS per il database SQL di Azure

Questo articolo fornisce uno script di PowerShell che illustra come gestire un alias DNS per il database SQL di Azure.

> [!NOTE]
> Questo articolo è stato aggiornato per usare il modulo Az di Azure PowerShell o l'interfaccia della riga di comando di Azure.This article has been updated to use either the Azure PowerShell Az module or Azure CLI. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020.
>
> Per altre informazioni sul modulo Az e sulla compatibilità con AzureRM, vedere [Introduzione al modulo Azure PowerShell Az.](/powershell/azure/new-azureps-module-az) Per istruzioni sull'installazione, vedere Installare Azure PowerShell o [Installare l'interfaccia della riga di comando di Azure.For installation instructions,](/cli/azure/install-azure-cli)see [Install Azure PowerShell](/powershell/azure/install-az-ps) or Install Azure CLI .

## <a name="dns-alias-in-connection-string"></a>Alias DNS nella stringa di connessione

Per connettere un determinato server di database SQL di Azure, un client come SQL Server Management Studio (SSMS) può specificare il nome dell'alias DNS al posto del nome reale del server. Nella stringa server di esempio seguente, l'alias *any-unique-alias-name* sostituisce il primo nodo delimitato da punto nella stringa server di quattro nodi:

   `<yourServer>.database.windows.net`

## <a name="prerequisites"></a>Prerequisiti

Se si vuole eseguire lo script di PowerShell di esempio di questo articolo, sono necessari i prerequisiti seguenti:

- Una sottoscrizione e un account di Azure, per la versione di valutazione gratuita, vedere Versioni di valutazione di AzureAn Azure subscription and account, for free trial, see [Azure trials](https://azure.microsoft.com/free/)
- Due server di database SQL di Azure

## <a name="example"></a>Esempio

L'esempio di codice seguente inizia assegnando valori letterali a diverse variabili.

Per eseguire il codice, modificare i valori segnaposto in modo che corrispondano ai valori reali nel sistema.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

I cmdlet utilizzati sono i seguenti:

- [New-AzSqlServerDNSAlias:](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias)crea un alias DNS nel sistema del servizio database SQL di Azure.New-AzSqlServerDNSAlias : Creates a DNS alias in the Azure SQL Database service system. L'alias fa riferimento al server di database 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Ottenere ed elencare tutti gli alias assegnati al server di database SQL 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Modifica il nome del server a cui l'alias è configurato per fare riferimento, dal server 1 al server 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Rimuove l'alias dal server di database 2, utilizzando il nome dell'alias.

Per eseguire l'installazione o l'aggiornamento, vedere [Installare il modulo di Azure PowerShell](/powershell/azure/install-az-ps).

Utilizzare `Get-Module -ListAvailable Az` in *powershell\_ise.exe*, per trovare la versione.

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

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

I comandi utilizzati sono i seguenti:

- [az sql server dns-alias create](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Crea un alias DNS nel sistema del servizio di database SQL di Azure.az sql server dns-alias create : Creates a DNS alias in the Azure SQL Database service system. L'alias fa riferimento al server di database 1.
- [az sql server dns-alias show](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Ottenere ed elencare tutti gli alias assegnati al server di database SQL 1.
- [az sql server dns-alias set](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Modifica il nome del server a cui l'alias è configurato per fare riferimento, dal server 1 al server 2.
- [az sql server dns-alias delete](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Rimuove l'alias dal server di database 2, utilizzando il nome dell'alias.

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

Per una spiegazione completa della funzionalità alias DNS per il database SQL, vedere Alias DNS per il database SQL di [Azure.](dns-alias-overview.md)
