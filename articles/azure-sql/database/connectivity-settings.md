---
title: Impostazioni di connettività per Database SQL di Azure e Data Warehouse
description: Questo documento illustra la scelta della versione Transport Layer Security (TLS) e l'impostazione del proxy e del reindirizzamento per il database SQL di Azure e Azure sinapsi Analytics
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and Azure Synapse Analytics (formerly SQL Data Warehouse)
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 07/06/2020
ms.openlocfilehash: a3ceb78a85546e5e75c4c484f131b67ff7fc9249
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91824143"
---
# <a name="azure-sql-connectivity-settings"></a>Impostazioni di connettività per SQL di Azure
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Questo articolo presenta le impostazioni che controllano la connettività al server per il database SQL di Azure e l'analisi delle sinapsi di Azure. Queste impostazioni si applicano a **tutti i** database SQL e ai database di Azure sinapsi associati al server.

> [!IMPORTANT]
> Questo articolo *non* si applica a **Istanza gestita di SQL di Azure**.

Le impostazioni di connettività sono accessibili dalla schermata **Firewall e reti virtuali**, come illustrato nello screenshot seguente:

 ![Screenshot delle impostazioni di connettività][1]

> [!NOTE]
> Una volta applicate, queste impostazioni **diventano effettive immediatamente** e possono comportare la perdita della connessione per i client che non ne soddisfano i requisiti.

## <a name="deny-public-network-access"></a>Nega l'accesso alla rete pubblica

Quando **l'impostazione Nega accesso pubblico alla rete** è impostata su **Sì**, sono consentite solo le connessioni tramite endpoint privati. Quando questa impostazione è impostata su **No** (impostazione predefinita), i client possono connettersi usando gli endpoint pubblici (regole del firewall basate su IP, le regole del firewall basate su VNET) o gli endpoint privati (usando il collegamento privato), come descritto nella [Panoramica di accesso alla rete](network-access-controls-overview.md). 

 ![Screenshot della connettività con Nega accesso alla rete pubblica][2]

Eventuali tentativi di impostare **l'opzione Nega accesso alla rete pubblica** su **Sì** senza endpoint privati esistenti nel server logico avranno esito negativo con un messaggio di errore simile al seguente:  

> [!NOTE]
> Per definire le regole del firewall della rete virtuale in un server logico già configurato con endpoint privati, impostare **Nega accesso alla rete pubblica** su **No**.

```output
Error 42102
Unable to set Deny Public Network Access to Yes since there is no private endpoint enabled to access the server. 
Please set up private endpoints and retry the operation. 
```

Se l'opzione **Nega l'accesso alla rete pubblica** è impostata su **Sì**, sono consentite solo le connessioni tramite endpoint privati, mentre tutte quelle tramite endpoint pubblici vengono negate con un messaggio di errore analogo al seguente:  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

Quando **l'impostazione Nega accesso pubblico alla rete** è impostata su **Sì**, qualsiasi tentativo di aggiungere o aggiornare le regole del firewall verrà negato con un messaggio di errore simile al seguente:

```output
Error 42101
Unable to create or modify firewall rules when public network interface for the server is disabled. 
To manage server or database level firewall rules, please enable the public network interface.
```

## <a name="change-public-network-access-via-powershell"></a>Cambiare l'accesso alla rete pubblica tramite PowerShell

> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato da Database SQL di Azure, ma tutte le attività di sviluppo future sono incentrate sul modulo Az.Sql. Per informazioni su questi cmdlet, vedere [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici. Per lo script seguente è necessario il [modulo di Azure PowerShell](/powershell/azure/install-az-ps).

Lo script di PowerShell seguente mostra come `Get` e `Set` la proprietà di **accesso alla rete pubblica** a livello di server:

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Disabled"
```

## <a name="change-public-network-access-via-cli"></a>Cambiare l'accesso alla rete pubblica tramite interfaccia della riga di comando

> [!IMPORTANT]
> Tutti gli script di questa sezione richiedono l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Interfaccia della riga di comando di Azure in una shell Bash

Il seguente script dell'interfaccia della riga di comando mostra come cambiare l'impostazione **Accesso alla rete pubblica** in una shell Bash:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```

## <a name="minimal-tls-version"></a>Versione minima TLS 

L'impostazione [Versione minima TLS](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) (Transport Layer Security) consente ai clienti di controllare la versione di TLS usata dalla loro istanza di Database SQL di Azure.

Attualmente sono supportati TLS 1,0, 1,1 e 1,2. L'impostazione Versione minima TLS assicura il supporto delle nuove versioni di TLS. Ad esempio, scegliendo una versione di TLS maggiore di 1,1. indica che vengono accettate solo le connessioni con TLS 1.1 e 1.2, mentre quelle con TLS 1.0 vengono rifiutate. Dopo il test per confermare che le applicazioni lo supportano, è consigliabile impostare una versione minima di TLS su 1,2, perché include correzioni per le vulnerabilità rilevate nelle versioni precedenti ed è la versione più recente di TLS supportata nel database SQL di Azure.

> [!IMPORTANT]
> Il valore predefinito per la versione minima di TLS è consentire tutte le versioni. Tuttavia, dopo aver applicato una versione di TLS, non è possibile ripristinare il valore predefinito.

Per i clienti con applicazioni che si basano su versioni precedenti di TLS, è consigliabile impostare la versione minima di TLS in base ai requisiti delle applicazioni. Per i clienti le cui applicazioni usano connessioni non crittografate, è consigliabile non impostare una versione minima di TLS.

Per altre informazioni, vedere [Considerazioni su TLS per la connettività a Database SQL](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

Dopo aver impostato la versione minima di TLS, i tentativi di accesso dei client che usano una versione di TLS inferiore a quella minima del server avranno esito negativo, con l'errore seguente:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>Impostare la versione minima di TLS tramite PowerShell

> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato da Database SQL di Azure, ma tutte le attività di sviluppo future sono incentrate sul modulo Az.Sql. Per informazioni su questi cmdlet, vedere [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici. Per lo script seguente è necessario il [modulo di Azure PowerShell](/powershell/azure/install-az-ps).

Lo script di PowerShell seguente illustra come usare `Get` e `Set` per ottenere e impostare la proprietà **Versione minima TLS** a livello di server logico:

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).MinimalTlsVersion

# # Update Minimal TLS Version to 1.2
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>Impostare la versione minima di TLS tramite l'interfaccia della riga di comando di Azure

> [!IMPORTANT]
> Tutti gli script di questa sezione richiedono l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Interfaccia della riga di comando di Azure in una shell Bash

Il seguente script dell'interfaccia della riga di comando mostra come cambiare l'impostazione **Versione minima TLS** in una shell Bash:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
```


## <a name="change-connection-policy"></a>Modificare i criteri di connessione

I [criteri di connessione](connectivity-architecture.md#connection-policy) determinano la modalità di connessione dei client al database SQL di Azure.


## <a name="change-connection-policy-via-powershell"></a>Cambiare i criteri di connessione tramite PowerShell

> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato da Database SQL di Azure, ma tutte le attività di sviluppo future sono incentrate sul modulo Az.Sql. Per informazioni su questi cmdlet, vedere [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici. Per lo script seguente è necessario il [modulo di Azure PowerShell](/powershell/azure/install-az-ps).

Lo script di PowerShell seguente mostra come cambiare i criteri di connessione tramite PowerShell:

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id -ApiVersion 2014-04-01 -Verbose).Properties.ConnectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="change-connection-policy-via-azure-cli"></a>Cambiare i criteri di connessione tramite l'interfaccia della riga di comando di Azure

> [!IMPORTANT]
> Tutti gli script di questa sezione richiedono l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Interfaccia della riga di comando di Azure in una shell Bash

Il seguente script dell'interfaccia della riga di comando mostra come cambiare i criteri di connessione in una shell Bash:

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

### <a name="azure-cli-from-a-windows-command-prompt"></a>Interfaccia della riga di comando di Azure da un prompt dei comandi di Windows

Il seguente script dell'interfaccia della riga di comando mostra come cambiare i criteri di connessione da un prompt dei comandi di Windows con l'interfaccia della riga di comando di Azure installata.

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica del funzionamento della connettività nel database SQL di Azure, vedere [architettura della connettività](connectivity-architecture.md)
- Per informazioni su come modificare i criteri di connessione per un server, vedere [conn-Policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: media/single-database-create-quickstart/manage-connectivity-settings.png
[2]: media/single-database-create-quickstart/manage-connectivity-flowchart.png
