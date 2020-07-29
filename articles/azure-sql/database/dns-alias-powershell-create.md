---
title: Alias DNS (PowerShell & interfaccia della riga di comando di Azure)
description: PowerShell e i cmdlet dell'interfaccia della riga di comando di Azure consentono di reindirizzare le nuove connessioni client a un'altra SQL Server in Azure, senza dover toccare alcuna configurazione client.
keywords: DNS per il database SQL
ms.custom: seo-lt-2019 sqldbrb=1
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: 3acbdd5c933bc2010a26e2039ece378774055d30
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84038762"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>PowerShell per l'alias DNS per il database SQL di Azure
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Questo articolo fornisce uno script di PowerShell che illustra come è possibile gestire un alias DNS per il [server SQL](logical-servers.md) che ospita il database SQL di Azure.

> [!NOTE]
> Questo articolo è stato aggiornato per usare il Azure PowerShell AZ Module o l'interfaccia della riga di comando di Azure. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020.
>
> Per ulteriori informazioni sulla compatibilità AZ Module e AzureRM, vedere la pagina relativa all' [Introduzione del modulo Azure PowerShell AZ](/powershell/azure/new-azureps-module-az). Per istruzioni sull'installazione, vedere [installare Azure PowerShell](/powershell/azure/install-az-ps) o [installare l'interfaccia](/cli/azure/install-azure-cli)della riga di comando di Azure.

## <a name="dns-alias-in-connection-string"></a>Alias DNS nella stringa di connessione

Per connettere un [server SQL logico](logical-servers.md), un client come SQL Server Management Studio (SSMS) può fornire il nome dell'alias DNS anziché il nome del server vero e proprio. Nella stringa server di esempio seguente, l'alias *any-unique-alias-name* sostituisce il primo nodo delimitato da punto nella stringa server di quattro nodi:

   `<yourServer>.database.windows.net`

## <a name="prerequisites"></a>Prerequisiti

Se si vuole eseguire lo script di PowerShell di esempio di questo articolo, sono necessari i prerequisiti seguenti:

- Una sottoscrizione e un account di Azure per una versione di valutazione gratuita, vedere le versioni di valutazione di [Azure](https://azure.microsoft.com/free/)
- Due server

## <a name="example"></a>Esempio

L'esempio di codice seguente inizia con l'assegnazione di valori letterali a diverse variabili.

Per eseguire il codice, modificare i valori segnaposto in modo che corrispondano ai valori reali nel sistema.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

I cmdlet usati sono i seguenti:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): crea un alias DNS nel sistema del servizio di database SQL di Azure. L'alias fa riferimento a server 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): ottiene ed elenca tutti gli alias assegnati al server 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): modifica il nome del server a cui è configurato l'alias, dal server 1 al server 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): rimuovere l'alias da server 2, usando il nome dell'alias.

Per eseguire l'installazione o l'aggiornamento, vedere [Installare il modulo di Azure PowerShell](/powershell/azure/install-az-ps).

Usare `Get-Module -ListAvailable Az` in *PowerShell \_ise.exe*per trovare la versione.

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

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

I comandi utilizzati sono i seguenti:

- [AZ SQL Server DNS-alias create](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): crea un alias DNS per un server. L'alias fa riferimento a server 1.
- [AZ SQL Server DNS-alias Show](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): ottiene ed elenca tutti gli alias assegnati al server 1.
- [AZ SQL Server DNS-alias set](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): modifica il nome del server a cui l'alias è configurato per fare riferimento, da server 1 a server 2.
- [AZ SQL Server DNS-alias delete](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): rimuovere l'alias da server 2 usando il nome dell'alias.

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

Per una spiegazione completa della funzionalità alias DNS per il database SQL, vedere [alias DNS per il database SQL di Azure](../../sql-database/dns-alias-overview.md).
