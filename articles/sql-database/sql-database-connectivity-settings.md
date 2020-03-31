---
title: Impostazioni di connettività per il database SQL di Azure e il data warehouse
description: Questo documento illustra la scelta della versione TLS e l'impostazione Proxy e Reindirizzamento per SQL di AzureThis document explains TLS version choice and Proxy vs Redirect setting for Azure SQL
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: d18fdee85bd0fbabe68fe9890c4a2dc74366041d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366088"
---
# <a name="azure-sql-connectivity-settings"></a>Impostazioni di connettività SQL di AzureAzure SQL Connectivity Settings
> [!NOTE]
> Questo articolo è applicabile al server SQL di Azure e ai database SQL e di SQL Data Warehouse creati nel server SQL di Azure. Per semplicità, "database SQL" viene usato per fare riferimento sia al database SQL che al database di SQL Data Warehouse.

> [!IMPORTANT]
> Questo articolo non si applica **all'istanza gestita del database SQL** di AzureThis article does *not* apply to Azure SQL Database Managed Instance

Questo articolo introduce le impostazioni che controllano la connettività al database SQL di Azure a livello di server. Queste impostazioni si applicano a **tutti i** database del database SQL e del data warehouse SQL associati al server.

> [!NOTE]
> Una volta applicate, queste impostazioni **hanno effetto immediato** e possono causare la perdita di connessione per i client se non soddisfano i requisiti per ogni impostazione.

Le impostazioni di connettività sono accessibili dal pannello **Firewall e reti virtuali,** come illustrato nella schermata seguente:The connectivity settings are accessible from the Firewalls and virtual networks blade as shown in the screenshot below:

 ![Screenshot delle impostazioni di connettività][1]


## <a name="deny-public-network-access"></a>Nega accesso alla rete pubblica
Nel portale di Azure, quando l'impostazione **Nega accesso alla rete pubblica** è impostata su **Sì**, sono consentite solo le connessioni tramite endpoint privati. Quando questa impostazione è impostata su **No**, i client possono connettersi utilizzando l'endpoint privato o pubblico.

Dopo aver impostato **Nega accesso alla rete pubblica su** **Sì**, i tentativi di accesso dai client che utilizzano endpoint pubblici avranno esito negativo con il seguente errore:

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. The public network interface on this server is not accessible. To connect to this server, use the Private Endpoint from inside your virtual network.
```

## <a name="change-public-network-access-via-powershell"></a>Modificare l'accesso alla rete pubblica tramite PowerShellChange Public Network Access via PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo di PowerShell Azure Resource Manager è ancora supportato dal database SQL di Azure, ma tutto lo sviluppo futuro è per il modulo Az.Sql.The PowerShell Azure Resource Manager module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. Per questi cmdlet, vedere [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo Az e nei moduli di AzureRm sono sostanzialmente identici. Lo script seguente richiede il [modulo di Azure PowerShell](/powershell/azure/install-az-ps).

Lo script di PowerShell `Get` `Set` seguente mostra come e la proprietà **Accesso alla rete pubblica** a livello di server logico:

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Enabled" 
```

## <a name="change-public-network-access-via-cli"></a>Modifica dell'accesso alla rete pubblica tramite l'interfaccia della riga di comando
> [!IMPORTANT]
> Tutti gli script in questa sezione richiedono [l'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/install-azure-cli)di Azure.

### <a name="azure-cli-in-a-bash-shell"></a>Interfaccia della riga di comando di Azure in una shell bashAzure CLI in a bash shell
Il seguente script CLI mostra come modificare **l'accesso alla rete pubblica** in una shell bash:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```


## <a name="connection-policy"></a>Criteri di connessione
[I criteri di connessione](sql-database-connectivity-architecture.md#connection-policy) determinano la modalità di connessione dei client a SQL Server di Azure.Connection policy determines how clients connect to Azure SQL Server. 

## <a name="change-connection-policy-via-powershell"></a>Modificare i criteri di connessione tramite PowerShellChange Connection policy via PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo di PowerShell Azure Resource Manager è ancora supportato dal database SQL di Azure, ma tutto lo sviluppo futuro è per il modulo Az.Sql.The PowerShell Azure Resource Manager module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. Per questi cmdlet, vedere [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo Az e nei moduli di AzureRm sono sostanzialmente identici. Lo script seguente richiede il [modulo di Azure PowerShell](/powershell/azure/install-az-ps).

The following PowerShell script shows how to change the connection policy using PowerShell:

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id).Properties.connectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="change-connection-policy-via-azure-cli"></a>Modificare i criteri di connessione tramite l'interfaccia della riga di comando di AzureChange Connection policy via
> [!IMPORTANT]
> Tutti gli script in questa sezione richiedono [l'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/install-azure-cli)di Azure.

### <a name="azure-cli-in-a-bash-shell"></a>Interfaccia della riga di comando di Azure in una shell bashAzure CLI in a bash shell
Il seguente script CLI mostra come modificare i criteri di connessione in una shell bash: 

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
ids="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $ids

# Update connection policy
az resource update --ids $ids --set properties.connectionType=Proxy
```

### <a name="azure-cli-from-a-windows-command-prompt"></a>Interfaccia della riga di comando di Azure da un prompt dei comandi di WindowsAzure CLI from a Windows command prompt
Lo script dell'interfaccia della riga di comando seguente mostra come modificare i criteri di connessione da un prompt dei comandi di Windows (con l'interfaccia della riga di comando di Azure installato).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Passaggi successivi
- Per una panoramica del funzionamento della connettività nel database SQL di Azure, vedere Architettura della [connettività SQL](sql-database-connectivity-architecture.md) di AzureFor an overview of how connectivity works in Azure SQL Database, refer to Azure SQL Connectivity Architecture
- Per informazioni su come modificare il criterio di connessione del database SQL di Azure per un server di database SQL di Azure, vedere [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/manage-connectivity-settings.png
