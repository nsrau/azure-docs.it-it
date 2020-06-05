---
title: Impostazioni di connettività per Database SQL di Azure e Data Warehouse
description: Questo documento illustra la scelta della versione di TLS e il confronto tra l'impostazione proxy e reindirizzamento per SQL di Azure
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: ef846b23bf6c9da2b7dd64927eac7cc1a1704dae
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684946"
---
# <a name="azure-sql-connectivity-settings"></a>Impostazioni di connettività per SQL di Azure
> [!NOTE]
> Questo articolo si applica al server SQL logico di Azure usato per i database di Database SQL di Azure e di SQL Data Warehouse creati al suo interno. Per semplicità, "database SQL" viene usato per fare riferimento sia al database SQL che al database di SQL Data Warehouse.

> [!IMPORTANT]
> Questo articolo *non* si applica a **Istanza gestita di Database SQL di Azure**

Vengono illustrate le impostazioni che controllano la connettività a Database SQL di Azure a livello di server. Queste impostazioni si applicano a **tutti** i database di Database SQL e di SQL Data Warehouse associati al server.

> [!NOTE]
> Una volta applicate, queste impostazioni **diventano effettive immediatamente** e possono comportare la perdita della connessione per i client che non ne soddisfano i requisiti.

Le impostazioni di connettività sono accessibili dalla schermata **Firewall e reti virtuali**, come illustrato nello screenshot seguente:

 ![Screenshot delle impostazioni di connettività][1]


## <a name="deny-public-network-access"></a>Nega l'accesso alla rete pubblica

I clienti possono connettersi al database SQL tramite endpoint pubblici (regole del firewall basate su IP e basate su rete virtuale) o endpoint privati (con Collegamento privato), come descritto nella [panoramica dell'accesso alla rete](sql-database-networkaccess-overview.md). 

Se l'opzione **Nega l'accesso alla rete pubblica** è impostata su **Sì**, sono consentite solo le connessioni tramite endpoint privati, mentre tutte quelle tramite endpoint pubblici vengono negate con un messaggio di errore analogo al seguente:  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

## <a name="change-public-network-access-via-powershell"></a>Cambiare l'accesso alla rete pubblica tramite PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato da Database SQL di Azure, ma tutte le attività di sviluppo future sono incentrate sul modulo Az.Sql. Per informazioni su questi cmdlet, vedere [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici. Per lo script seguente è necessario il [modulo di Azure PowerShell](/powershell/azure/install-az-ps).

Lo script di PowerShell seguente illustra come usare `Get` e `Set` per ottenere e impostare la proprietà **Accesso alla rete pubblica** a livello di server logico:

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Enabled" 
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

Al momento sono supportati TLS 1.0, 1.1 e TLS 1.2. L'impostazione Versione minima TLS assicura il supporto delle nuove versioni di TLS. Ad esempio, la scelta di una versione di TLS successiva a 1.1 indica che vengono accettate solo le connessioni con TLS 1.1 e 1.2, mentre quelle con TLS 1.0 vengono rifiutate. Dopo aver verificato che le applicazioni lo supportano, è consigliabile impostare la versione minima di TLS su 1.2, perché include correzioni per le vulnerabilità rilevate nelle versioni precedenti ed è la versione più recente di TLS supportata in Database SQL di Azure.

Per i clienti con applicazioni che si basano su versioni precedenti di TLS, è consigliabile impostare la versione minima di TLS in base ai requisiti delle applicazioni. Per i clienti le cui applicazioni usano connessioni non crittografate, è consigliabile non impostare una versione minima di TLS. 

Per altre informazioni, vedere [Considerazioni su TLS per la connettività a Database SQL](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity).

Dopo aver impostato la versione minima di TLS, i tentativi di accesso dei client che usano una versione di TLS inferiore a quella minima del server avranno esito negativo, con l'errore seguente:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>Impostare la versione minima di TLS tramite PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato da Database SQL di Azure, ma tutte le attività di sviluppo future sono incentrate sul modulo Az.Sql. Per informazioni su questi cmdlet, vedere [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici. Per lo script seguente è necessario il [modulo di Azure PowerShell](/powershell/azure/install-az-ps).

Lo script di PowerShell seguente illustra come usare `Get` e `Set` per ottenere e impostare la proprietà **Versione minima TLS** a livello di server logico:

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
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

## <a name="connection-policy"></a>Criteri di connessione

L'impostazione [Criteri di connessione](sql-database-connectivity-architecture.md#connection-policy) determina il modo in cui i client si connettono a SQL Server di Azure. 

## <a name="change-connection-policy-via-powershell"></a>Cambiare i criteri di connessione tramite PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato da Database SQL di Azure, ma tutte le attività di sviluppo future sono incentrate sul modulo Az.Sql. Per informazioni su questi cmdlet, vedere [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici. Per lo script seguente è necessario il [modulo di Azure PowerShell](/powershell/azure/install-az-ps).

Lo script di PowerShell seguente mostra come cambiare i criteri di connessione tramite PowerShell:

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

- Per una panoramica del funzionamento della connettività in Database SQL di Azure, vedere [Architettura della connettività di SQL di Azure](sql-database-connectivity-architecture.md)
- Per informazioni su come modificare il criterio di connessione del database SQL di Azure per un server di database SQL di Azure, vedere [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/manage-connectivity-settings.png
