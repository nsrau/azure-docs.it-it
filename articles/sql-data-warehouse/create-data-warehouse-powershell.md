---
title: "Guida introduttiva: Creare un'istanza di Azure SQL Data Warehouse - Azure PowerShell | Microsoft Docs"
description: Creare rapidamente un server logico di database SQL, una regola del firewall a livello di server e un data warehouse con Azure PowerShell.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/01/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 72ed9e921d96faea155c1da88dd32fcbd467d549
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414001"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-with-azure-powershell"></a>Guida introduttiva: Creare un'istanza di Azure SQL Data Warehouse ed eseguirvi una query con Azure PowerShell

Creare rapidamente un'istanza di Azure SQL Data Warehouse con Azure PowerShell.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

Questa esercitazione richiede il modulo Azure PowerShell 5.1.1 o versioni successive. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione attualmente in uso. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps). 


> [!NOTE]
> La creazione di un'istanza di SQL Data Warehouse può dare luogo a un nuovo servizio fatturabile.  Per altre informazioni, vedere [SQL Data Warehouse Prezzi](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) e seguire le istruzioni visualizzate.

```powershell
Add-AzureRmAccount
```

Per vedere quale sottoscrizione si sta usando, eseguire [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

```powershell
Get-AzureRmSubscription
```

Per usare una sottoscrizione diversa da quella predefinita, eseguire [Select-AzureRmSubscription](/powershell/module/azurerm.profile/select-azurermsubscription).

```powershell
Select-AzureRmSubscription -SubscriptionName "MySubscription"
```


## <a name="create-variables"></a>Creare le variabili

Definire le variabili da usare negli script di questa guida introduttiva.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (do not capitalize)
$servername = "server-$(Get-Random)"
# Set an admin login and password for your database
# The login information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehosue"
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) con il comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo. Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella posizione `westeurope`.

```powershell
New-AzureRmResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>Creare un server logico

Creare un [server logico di SQL di Azure](../sql-database/sql-database-logical-servers.md) con il comando [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver). Un server logico contiene un gruppo di database gestiti come gruppo. L'esempio seguente crea un server con un nome casuale nel gruppo di risorse con un account di accesso amministratore denominato `ServerAdmin` e la password `ChangeYourAdminPassword1`. Sostituire questi valori predefiniti con quelli desiderati.

```powershell
New-AzureRmSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Configurare una regola del firewall del server

Creare una [regola del firewall a livello di server di SQL di Azure](../sql-database/sql-database-firewall-configure.md) con il comando [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule). Una regola del firewall a livello di server consente a un'applicazione esterna, ad esempio SQL Server Management Studio o l'utility SQLCMD, di connettersi a un data warehouse SQL tramite il firewall del servizio di SQL Data Warehouse. Nell'esempio seguente, il firewall è aperto solo per altre risorse di Azure. Per abilitare la connettività esterna, modificare l'indirizzo IP in un indirizzo appropriato per l'ambiente. Per aprire tutti gli indirizzi IP, usare 0.0.0.0 come indirizzo IP iniziale e 255.255.255.255 come indirizzo finale.

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> Il database SQL ed SQL Data Warehouse comunicano sulla porta 1433. Se si sta tentando di connettersi da una rete aziendale, il traffico in uscita attraverso la porta 1433 potrebbe non essere autorizzato dal firewall della rete. In questo caso, non sarà possibile connettersi al server SQL di Azure, a meno che il reparto IT non apra la porta 1433.
>


## <a name="create-a-data-warehouse-with-sample-data"></a>Creare un data warehouse con dati di esempio
Questo esempio crea un data warehouse usando le variabili definite in precedenza.  Specifica l'obiettivo di servizio come DW400, ovvero un punto iniziale a basso costo per il data warehouse. 

```Powershell
New-AzureRmSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW400" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

I parametri obbligatori sono:

* **RequestedServiceObjectiveName**: quantità di [unità di data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md) richiesta. L'aumento di questa quantità comporta l'aumento dei costi di calcolo. Per un elenco di valori supportati, vedere [Memory and concurrency limits](memory-and-concurrency-limits.md) (Limiti di memoria e concorrenza).
* **DatabaseName**: il nome dell'istanza di SQL Data Warehouse che si sta creando.
* **ServerName**: nome del server che si sta usando per la creazione.
* **ResourceGroupName**: il gruppo di risorse in uso. Per trovare i gruppi di risorse disponibili nella sottoscrizione, usare Get-AzureResource.
* **Edition**: per creare un'istanza di SQL Data Warehouse, deve essere "DataWarehouse".

I parametri facoltativi sono:

- **CollationName**: se non specificate, le regole di confronto predefinite sono SQL_Latin1_General_CP1_CI_AS. Non è possibile modificare le regole di confronto in un database.
- **MaxSizeBytes**: la dimensione massima predefinita di un database è 10 GB.

Per altre informazioni sulle opzioni dei parametri, vedere [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase).


## <a name="clean-up-resources"></a>Pulire le risorse

Altre esercitazioni introduttive della raccolta si basano su questa guida introduttiva. 

> [!TIP]
> Se si prevede di continuare a usare le esercitazioni introduttive successive, non pulire le risorse create in questa guida di avvio rapido. Se non si prevede di continuare, seguire questa procedura per eliminare tutte le risorse create da questa guida di avvio rapido nel portale di Azure.
>

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Passaggi successivi

Sono stati creati un data warehouse e una regola del firewall, è stata stabilita una connessione al data warehouse e sono state eseguite alcune query. Per altre informazioni su Azure SQL Data Warehouse, continuare con l'esercitazione per il caricamento dei dati.
> [!div class="nextstepaction"]
>[Caricare i dati in SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
