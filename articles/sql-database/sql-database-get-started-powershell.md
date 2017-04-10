---
title: 'Azure PowerShell: creare un database SQL | Microsoft Docs'
description: Informazioni su come creare un server logico di database SQL, una regola del firewall a livello di server e database nel portale di Azure.
keywords: esercitazione sul database sql, creare un database sql
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start create
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: hero-article
ms.date: 04/03/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 90dc7e4a07f2a3c514c25b4031128f4df4e32aab
ms.lasthandoff: 04/04/2017

---

# <a name="create-a-single-azure-sql-database-using-powershell"></a>Creare un singolo database SQL di Azure usando PowerShell

PowerShell viene usato per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questa guida illustra in dettaglio l'uso di PowerShell per distribuire un database SQL di Azure in un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) in un [server logico di database SQL di Azure](sql-database-features.md).

Per completare questa esercitazione, verificare di aver installato l'ultima versione di [Azure PowerShell](/powershell/azureps-cmdlets-docs). 

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando [Add-AzureRmAccount](https://docs.microsoft.com/powershell/resourcemanager/azurerm.profile/v2.5.0/add-azurermaccount) e seguire le istruzioni visualizzate.

```powershell
Add-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) con il comando [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo. Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella posizione `westeurope`.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "westeurope"
```
## <a name="create-a-logical-server"></a>Creare un server logico

Creare un [server logico di database SQL di Azure](sql-database-features.md) con il comando [New AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver). Un server logico contiene un gruppo di database gestiti come gruppo. L'esempio seguente crea un server con un nome casuale nel gruppo di risorse con un account di accesso amministratore denominato `ServerAdmin` e la password `ChangeYourAdminPassword1`. Sostituire questi valori predefiniti con quelli desiderati.

```powershell
$servername = "server-$(Get-Random)"
New-AzureRmSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -Location "westeurope" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList "ServerAdmin", $(ConvertTo-SecureString -String "ChangeYourAdminPassword1" -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Configurare una regola del firewall del server

Creare una [regola del firewall a livello di server di database SQL di Azure](sql-database-firewall-configure.md) con il comando [New-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserverfirewallrule). Una regola del firewall a livello di server consente a un'applicazione esterna, ad esempio SQL Server Management Studio o l'utility SQLCMD, di connettersi a un database SQL tramite il firewall del servizio di database SQL. L'esempio seguente crea una regola del firewall per un intervallo di indirizzi predefiniti, che in questo esempio corrisponde all'intero intervallo possibile di indirizzi IP. Sostituire i valori predefiniti con i valori dell'intervallo di indirizzi IP esterni o di indirizzi IP. 

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "255.255.255.255"
```

## <a name="create-a-blank-database"></a>Creazione di un database vuoto

Creare un database SQL vuoto con [livello di prestazioni S0](sql-database-service-tiers.md) nel server con il comando [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase). L'esempio seguente crea un database denominato `mySampleDatabase`. Sostituire questo valore predefinito con quello desiderato.

```powershell
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MySampleDatabase" `
    -RequestedServiceObjectiveName "S0"
```

## <a name="clean-up-resources"></a>Pulire le risorse

Altre guide introduttive di questa raccolta si basano sui valori di questa guida introduttiva. Se si prevede di continuare a usare le guide introduttive successive o le esercitazioni, non eliminare le risorse create in questa guida introduttiva. Se non si prevede di continuare, usare il comando seguente per eliminare tutte le risorse create da questa guida introduttiva.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Passaggi successivi

- Per connettersi ed effettuare una query usando SQL Server Management Studio, vedere [Connettersi ed eseguire una query con SSMS](sql-database-connect-query-ssms.md)
- Per connettersi usando Visual Studio, vedere [Connettersi ed eseguire query con Visual Studio](sql-database-connect-query.md).
* Per una panoramica tecnica del database SQL, vedere [Informazioni sul servizio di database SQL](sql-database-technical-overview.md).

