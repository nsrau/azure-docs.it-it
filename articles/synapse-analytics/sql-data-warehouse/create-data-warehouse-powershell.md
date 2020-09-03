---
title: Creare un pool Synapse SQL con Azure PowerShell
description: Creare rapidamente un pool Synapse SQL con una regola del firewall a livello di server usando Azure PowerShell.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse    , devx-track-azurepowershell
ms.openlocfilehash: 098b33491076de5e7e71fed7439cc6090e0183bb
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89076795"
---
# <a name="quickstart-create-a-synapse-sql-pool-with-azure-powershell"></a>Avvio rapido: Creare un pool Synapse SQL con Azure PowerShell

Creare un pool SQL di Synapse (data warehouse) in Azure Synapse Analytics usando Azure PowerShell.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

> [!IMPORTANT]
> La creazione di un pool SQL può risultare in un nuovo servizio fatturabile.  Per altre informazioni, vedere [Prezzi di Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) e seguire le indicazioni visualizzate.

```powershell
Connect-AzAccount
```

Per vedere quale sottoscrizione si sta usando, eseguire [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Get-AzSubscription
```

Se è necessario usare una sottoscrizione diversa da quella predefinita, eseguire [Set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="create-variables"></a>Creare le variabili

Definire le variabili da usare negli script di questa guida introduttiva.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehouse"
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un [gruppo di risorse di Azure](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) con il comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Un gruppo di risorse è un contenitore in cui le risorse di Azure vengono distribuite e gestite come gruppo. L'esempio seguente consente di creare un gruppo di risorse denominato `myResourceGroup` nell'area `westeurope`.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```

## <a name="create-a-server"></a>Creare un server

Creare un [server SQL logico](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) con il comando [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Un server contiene un gruppo di database gestiti come gruppo. L'esempio seguente crea un server con un nome casuale nel gruppo di risorse con un utente amministratore denominato `ServerAdmin` e la password `ChangeYourAdminPassword1`. Sostituire questi valori predefiniti con quelli desiderati.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-level-firewall-rule"></a>Configurare una regola del firewall a livello di server

Creare una [regola del firewall a livello di server](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) con il comando [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Una regola del firewall a livello di server consente a un'applicazione esterna, ad esempio SQL Server Management Studio o l'utilità SQLCMD, di connettersi a un pool SQL tramite il firewall del servizio pool SQL.

Nell'esempio seguente, il firewall è aperto solo per altre risorse di Azure. Per abilitare la connettività esterna, modificare l'indirizzo IP in un indirizzo appropriato per l'ambiente. Per aprire tutti gli indirizzi IP, usare 0.0.0.0 come indirizzo IP iniziale e 255.255.255.255 come indirizzo finale.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> Gli endpoint SQL comunicano sulla porta 1433. Se si sta tentando di connettersi da una rete aziendale, il traffico in uscita attraverso la porta 1433 potrebbe non essere autorizzato dal firewall della rete. In questo caso, non sarà possibile connettersi al server a meno che il reparto IT non apra la porta 1433.
>

## <a name="create-a-sql-pool"></a>Creare un pool SQL

L'esempio seguente crea un pool SQL usando le variabili definite in precedenza.  Specifica l'obiettivo di servizio come DW100c, ovvero un punto iniziale a basso costo per il pool SQL.

```Powershell
New-AzSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW100c" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

I parametri obbligatori sono:

* **RequestedServiceObjectiveName**: quantità di [unità di data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md) richiesta. L'aumento di questa quantità comporta l'aumento dei costi di calcolo. Per un elenco di valori supportati, vedere [Memory and concurrency limits](memory-concurrency-limits.md) (Limiti di memoria e concorrenza).
* **DatabaseName**: il nome del pool SQL da creare.
* **ServerName**: nome del server che si sta usando per la creazione.
* **ResourceGroupName**: gruppo di risorse in uso. Per trovare i gruppi di risorse disponibili nella sottoscrizione, usare Get-AzureResource.
* **Edition**: per creare un pool SQL, deve essere "DataWarehouse".

I parametri facoltativi sono:

* **CollationName**: se non specificate, le regole di confronto predefinite sono SQL_Latin1_General_CP1_CI_AS. Non è possibile modificare le regole di confronto in un database.
* **MaxSizeBytes**: la dimensione massima predefinita di un database è 240 TB. Le dimensioni massime limitano i dati rowstore. Lo spazio di archiviazione dei dati a colonne è illimitato.

Per altre informazioni sulle opzioni dei parametri, vedere [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="clean-up-resources"></a>Pulire le risorse

Altre esercitazioni introduttive della raccolta si basano su questa guida introduttiva.

> [!TIP]
> Se si prevede di continuare con le esercitazioni introduttive successive, non eseguire la pulizia delle risorse create in questo avvio rapido. Se non si prevede di continuare, seguire questa procedura per eliminare tutte le risorse create da questo avvio rapido nel portale di Azure.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Passaggi successivi

Sono stati creati un pool SQL e una regola del firewall ed è stata stabilita una connessione al pool SQL. Per altre informazioni, continuare con l'articolo [Caricare i dati in un pool SQL](load-data-from-azure-blob-storage-using-polybase.md).
