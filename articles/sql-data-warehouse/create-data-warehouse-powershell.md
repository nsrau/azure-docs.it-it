---
title: "Guida introduttiva: Creare un'istanza di Azure SQL Data Warehouse - Azure PowerShell | Microsoft Docs"
description: Creare rapidamente un server logico di database SQL, una regola del firewall a livello di server e un data warehouse con Azure PowerShell.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: development
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: cdaa932a4996d559b5974e39e20cf75acd6571c5
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873777"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-with-azure-powershell"></a>Guida introduttiva: Creare un'istanza di Azure SQL Data Warehouse ed eseguirvi una query con Azure PowerShell

Creare rapidamente un'istanza di Azure SQL Data Warehouse con Azure PowerShell.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

> [!NOTE]
> La creazione di un'istanza di SQL Data Warehouse può dare luogo a un nuovo servizio fatturabile.  Per altre informazioni, vedere [SQL Data Warehouse Prezzi](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) e seguire le indicazioni visualizzate.

```powershell
Connect-AzAccount
```

Per vedere quale sottoscrizione si sta usando, eseguire [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription).

```powershell
Get-AzSubscription
```

Se è necessario usare una sottoscrizione diversa da quella predefinita, eseguire [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```


## <a name="create-variables"></a>Creare le variabili

Definire le variabili da usare negli script di questa guida introduttiva.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehosue"
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) con il comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo. Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella posizione `westeurope`.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>Creare un server logico

Creare un [server logico SQL di Azure](../sql-database/sql-database-logical-servers.md) con il comando [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver). Un server logico contiene un gruppo di database gestiti come gruppo. L'esempio seguente crea un server con un nome casuale nel gruppo di risorse con un utente amministratore denominato `ServerAdmin` e la password `ChangeYourAdminPassword1`. Sostituire questi valori predefiniti con quelli desiderati.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Configurare una regola del firewall del server

Creare una [regola del firewall a livello di server SQL di Azure](../sql-database/sql-database-firewall-configure.md) con il comando [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule). Una regola del firewall a livello di server consente a un'applicazione esterna, ad esempio SQL Server Management Studio o l'utility SQLCMD, di connettersi a un data warehouse SQL tramite il firewall del servizio di SQL Data Warehouse. Nell'esempio seguente, il firewall è aperto solo per altre risorse di Azure. Per abilitare la connettività esterna, modificare l'indirizzo IP in un indirizzo appropriato per l'ambiente. Per aprire tutti gli indirizzi IP, usare 0.0.0.0 come indirizzo IP iniziale e 255.255.255.255 come indirizzo finale.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> Il database SQL ed SQL Data Warehouse comunicano sulla porta 1433. Se si sta tentando di connettersi da una rete aziendale, il traffico in uscita attraverso la porta 1433 potrebbe non essere autorizzato dal firewall della rete. In questo caso, sarà possibile connettersi al server SQL di Azure solo se il reparto IT apre la porta 1433.
>


## <a name="create-a-data-warehouse"></a>Creare un data warehouse
Questo esempio crea un data warehouse usando le variabili definite in precedenza.  Specifica l'obiettivo di servizio come DW100c, ovvero un punto iniziale a basso costo per il data warehouse. 

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

* **RequestedServiceObjectiveName**: quantità di [unità di data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md) richiesta. L'aumento di questa quantità comporta l'aumento dei costi di calcolo. Per un elenco di valori supportati, vedere [Memory and concurrency limits](memory-and-concurrency-limits.md) (Limiti di memoria e concorrenza).
* **DatabaseName**: nome dell'istanza di SQL Data Warehouse che si sta creando.
* **ServerName**: nome del server che si sta usando per la creazione.
* **ResourceGroupName**: gruppo di risorse in uso. Per trovare i gruppi di risorse disponibili nella sottoscrizione, usare Get-AzureResource.
* **Edition**: per creare un'istanza di SQL Data Warehouse, deve essere "DataWarehouse".

I parametri facoltativi sono:

- **CollationName**: se non specificate, le regole di confronto predefinite sono SQL_Latin1_General_CP1_CI_AS. Non è possibile modificare le regole di confronto in un database.
- **MaxSizeBytes**: la dimensione massima predefinita di un database è 240 TB. Le dimensioni massime limitano i dati rowstore. Lo spazio di archiviazione dei dati a colonne è illimitato.

Per altre informazioni sulle opzioni dei parametri, vedere [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase).


## <a name="clean-up-resources"></a>Pulire le risorse

Altre esercitazioni introduttive della raccolta si basano su questa guida introduttiva. 

> [!TIP]
> Se si prevede di continuare con le esercitazioni introduttive successive, non eseguire la pulizia delle risorse create in questo avvio rapido. Se non si prevede di continuare, seguire questa procedura per eliminare tutte le risorse create da questo avvio rapido nel portale di Azure.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Passaggi successivi

Sono stati creati un data warehouse e una regola del firewall, è stata stabilita una connessione al data warehouse e sono state eseguite alcune query. Per altre informazioni su Azure SQL Data Warehouse, continuare con l'esercitazione per il caricamento dei dati.
> [!div class="nextstepaction"]
>[Caricare i dati in SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
