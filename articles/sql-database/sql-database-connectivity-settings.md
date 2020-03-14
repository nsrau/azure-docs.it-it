---
title: Impostazioni di connettività per il database SQL di Azure e data warehouse
description: Questo documento illustra la scelta della versione di TLS e l'impostazione del proxy e del reindirizzamento per SQL di Azure
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: cd239106bfd3ac785cffbf1365f298da565179ec
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79366088"
---
# <a name="azure-sql-connectivity-settings"></a>Impostazioni di connettività SQL di Azure
> [!NOTE]
> Le funzionalità sono disponibili solo negli **Stati Uniti occidentali 2, Stati Uniti orientali, Stati Uniti centro-meridionali** con altre aree presto disponibili

> [!NOTE]
> Questo articolo è applicabile al server SQL di Azure e ai database SQL e di SQL Data Warehouse creati nel server SQL di Azure. Per semplicità, "database SQL" viene usato per fare riferimento sia al database SQL che al database di SQL Data Warehouse.

> [!IMPORTANT]
> Questo articolo *non* si applica a **istanza gestita di database SQL di Azure**

Questo articolo presenta le impostazioni che controllano la connettività al database SQL di Azure a livello di server. Queste impostazioni si applicano a **tutti i** database SQL e SQL data warehouse database associati al server.

> [!NOTE]
> Quando queste impostazioni vengono applicate, diventano **effettive immediatamente e possono comportare** la perdita della connessione per i client se non soddisfano i requisiti per ogni impostazione.

Le impostazioni di connettività sono accessibili dal pannello **firewall e reti virtuali** , come illustrato nella schermata seguente:

 ![Screenshot delle impostazioni di connettività][1]


## <a name="deny-public-network-access"></a>Nega accesso alla rete pubblica
Nel portale di Azure, quando l'impostazione **Nega accesso pubblico alla rete** è impostata su **Sì**, sono consentite solo le connessioni tramite endpoint privati. Quando questa impostazione è impostata su **No**, i client possono connettersi usando l'endpoint privato o pubblico.

Dopo aver impostato **Nega accesso alla rete pubblica** su **Sì**, i tentativi di accesso dai client che usano l'endpoint pubblico avranno esito negativo con l'errore seguente:

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. The public network interface on this server is not accessible. To connect to this server, use the Private Endpoint from inside your virtual network.
```

## <a name="change-public-network-access-via-powershell"></a>Modificare l'accesso alla rete pubblica tramite PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato dal database SQL di Azure, ma tutte le attività di sviluppo future sono per il modulo AZ. SQL. Per questi cmdlet, vedere [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo AZ e nei moduli AzureRm sono sostanzialmente identici. Lo script seguente richiede il [modulo Azure PowerShell](/powershell/azure/install-az-ps).

Lo script di PowerShell seguente illustra come `Get` e `Set` la proprietà di **accesso alla rete pubblica** a livello di server logico:

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Enabled" 
```

## <a name="change-public-network-access-via-cli"></a>Modificare l'accesso alla rete pubblica tramite CLI
> [!IMPORTANT]
> Tutti gli script in questa sezione richiedono l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>INTERFACCIA della riga di comando di Azure in una shell bash
Il seguente script dell'interfaccia della riga di comando Mostra come modificare l' **accesso alla rete pubblica** in una shell bash:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```


## <a name="connection-policy"></a>Criteri di connessione
I [criteri di connessione](sql-database-connectivity-architecture.md#connection-policy) determinano la modalità di connessione dei client ad Azure SQL Server. 

## <a name="change-connection-policy-via-powershell"></a>Modificare i criteri di connessione tramite PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato dal database SQL di Azure, ma tutte le attività di sviluppo future sono per il modulo AZ. SQL. Per questi cmdlet, vedere [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo AZ e nei moduli AzureRm sono sostanzialmente identici. Lo script seguente richiede il [modulo Azure PowerShell](/powershell/azure/install-az-ps).

Lo script di PowerShell seguente mostra come modificare i criteri di connessione usando PowerShell:

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

## <a name="change-connection-policy-via-azure-cli"></a>Modificare i criteri di connessione tramite l'interfaccia della riga di comando
> [!IMPORTANT]
> Tutti gli script in questa sezione richiedono l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>INTERFACCIA della riga di comando di Azure in una shell bash
Il seguente script dell'interfaccia della riga di comando Mostra come modificare i criteri di connessione in una shell bash: 

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

### <a name="azure-cli-from-a-windows-command-prompt"></a>CLI di Azure da un prompt dei comandi di Windows
Lo script CLI seguente mostra come modificare i criteri di connessione da un prompt dei comandi di Windows (con l'interfaccia della riga di comando di Azure installata).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Passaggi successivi
- Per una panoramica del funzionamento della connettività nel database SQL di Azure, vedere [architettura della connettività SQL di Azure](sql-database-connectivity-architecture.md)
- Per informazioni su come modificare il criterio di connessione del database SQL di Azure per un server di database SQL di Azure, vedere [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/manage-connectivity-settings.png
