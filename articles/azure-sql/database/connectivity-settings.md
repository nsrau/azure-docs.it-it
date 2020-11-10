---
title: Impostazioni di connettività per il database SQL di Azure e Azure sinapsi Analytics
description: Questo articolo illustra la scelta della versione Transport Layer Security (TLS) e le impostazioni del proxy e del reindirizzamento per il database SQL di Azure e l'analisi delle sinapsi di Azure.
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and Azure Synapse Analytics (formerly SQL Data Warehouse)
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 07/06/2020
ms.openlocfilehash: 9856d71a6398bcea5b979788846afce17e7955f7
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412988"
---
# <a name="azure-sql-connectivity-settings"></a>Impostazioni di connettività SQL di Azure
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Questo articolo presenta le impostazioni che controllano la connettività al server per il database SQL di Azure e l'analisi delle sinapsi di Azure. Queste impostazioni si applicano a tutti i database SQL e ad Azure sinapsi Analytics associati al server.

> [!IMPORTANT]
> Questo articolo non si applica al Istanza gestita SQL di Azure.

Le impostazioni di connettività sono accessibili dalla schermata **Firewall e reti virtuali** , come illustrato nello screenshot seguente:

 ![Screenshot della finestra impostazioni di connettività.][1]

> [!NOTE]
> Queste impostazioni hanno effetto immediatamente dopo l'applicazione. I clienti potrebbero riscontrare perdite di connessione se non soddisfano i requisiti per ogni impostazione.

## <a name="deny-public-network-access"></a>Nega l'accesso alla rete pubblica

Se **l'opzione Nega accesso alla rete pubblica** è impostata su **Sì** , sono consentite solo le connessioni tramite endpoint privati. Quando questa impostazione è **No** (impostazione predefinita), i clienti possono connettersi usando endpoint pubblici (con regole del firewall basate su IP o con regole del firewall basate su rete virtuale) o endpoint privati (usando il collegamento privato di Azure), come descritto nella [Panoramica di accesso alla rete](network-access-controls-overview.md).

 ![Diagramma che mostra la connettività quando l'opzione Nega accesso alla rete pubblica è impostata su Sì rispetto a quando l'opzione Nega accesso alla rete pubblica è impostata su No.][2]

Eventuali tentativi di impostare **Nega accesso alla rete pubblica** su **Sì** senza endpoint privati esistenti nel server logico avranno esito negativo con un messaggio di errore simile al seguente:  

```output
Error 42102
Unable to set Deny Public Network Access to Yes since there is no private endpoint enabled to access the server.
Please set up private endpoints and retry the operation.
```

> [!NOTE]
> Per definire le regole del firewall della rete virtuale in un server logico già configurato con endpoint privati, impostare **Nega accesso alla rete pubblica** su **No**.

Se **l'opzione Nega accesso alla rete pubblica** è impostata su **Sì** , sono consentite solo le connessioni tramite endpoint privati. Tutte le connessioni tramite endpoint pubblici verranno negate con un messaggio di errore simile al seguente:  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

Quando **l'opzione Nega accesso alla rete pubblica** è impostata su **Sì** , qualsiasi tentativo di aggiungere o aggiornare le regole del firewall verrà negato con un messaggio di errore simile al seguente:

```output
Error 42101
Unable to create or modify firewall rules when public network interface for the server is disabled. 
To manage server or database level firewall rules, please enable the public network interface.
```

## <a name="change-public-network-access-via-powershell"></a>Modificare l'accesso alla rete pubblica tramite PowerShell

> [!IMPORTANT]
> Il database SQL di Azure supporta ancora il modulo Azure Resource Manager di PowerShell, ma tutte le attività di sviluppo future sono per il modulo AZ. SQL. Per informazioni su questi cmdlet, vedere [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici. Per lo script seguente è necessario il [modulo di Azure PowerShell](/powershell/azure/install-az-ps).

Lo script di PowerShell seguente mostra come `Get` e `Set` la proprietà di **accesso alla rete pubblica** a livello di server:

```powershell
# Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Disabled"
```

## <a name="change-public-network-access-via-cli"></a>Modificare l'accesso alla rete pubblica tramite CLI

> [!IMPORTANT]
> Tutti gli script in questa sezione richiedono l'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>INTERFACCIA della riga di comando di Azure in una shell bash

Il seguente script dell'interfaccia della riga di comando Mostra come modificare l'impostazione di **accesso alla rete pubblica** in una shell bash:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"
```

## <a name="minimal-tls-version"></a>Versione minima di TLS 

L'impostazione della versione [Transport Layer Security minima (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) consente ai clienti di scegliere la versione di TLS utilizzata dal database SQL.

Attualmente sono supportati TLS 1,0, 1,1 e 1,2. L'impostazione di una versione minima di TLS garantisce che siano supportate le versioni più recenti di TLS. Se ad esempio si sceglie una versione di TLS maggiore di 1,1 significa che vengono accettate solo le connessioni con TLS 1,1 e 1,2 e le connessioni con TLS 1,0 vengono rifiutate. Dopo aver testato per confermare che le applicazioni lo supportano, è consigliabile impostare la versione minima di TLS su 1,2. Questa versione include correzioni per le vulnerabilità nelle versioni precedenti ed è la versione più recente di TLS supportata nel database SQL di Azure.

> [!IMPORTANT]
> Il valore predefinito per la versione minima di TLS è quello di consentire tutte le versioni. Dopo aver applicato una versione di TLS, non è possibile ripristinare il valore predefinito.

Per i clienti con applicazioni che si basano su versioni precedenti di TLS, è consigliabile impostare la versione minima di TLS in base ai requisiti delle applicazioni. Per i clienti che si basano sulle applicazioni per la connessione tramite una connessione non crittografata, è consigliabile non impostare alcuna versione minima di TLS.

Per altre informazioni, vedere [Considerazioni su TLS per la connettività a Database SQL](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

Dopo aver impostato la versione minima di TLS, i tentativi di accesso da parte dei clienti che usano una versione di TLS inferiore alla versione minima di TLS del server avranno esito negativo con l'errore seguente:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-the-minimal-tls-version-via-powershell"></a>Impostare la versione minima di TLS tramite PowerShell

> [!IMPORTANT]
> Il database SQL di Azure supporta ancora il modulo Azure Resource Manager di PowerShell, ma tutte le attività di sviluppo future sono per il modulo AZ. SQL. Per informazioni su questi cmdlet, vedere [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici. Per lo script seguente è necessario il [modulo di Azure PowerShell](/powershell/azure/install-az-ps).

Lo script di PowerShell seguente illustra come usare `Get` e `Set` per ottenere e impostare la proprietà **Versione minima TLS** a livello di server logico:

```powershell
# Get the Minimal TLS Version property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).MinimalTlsVersion

# Update Minimal TLS Version to 1.2
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-the-minimal-tls-version-via-the-azure-cli"></a>Impostare la versione minima di TLS tramite l'interfaccia della riga di comando di Azure

> [!IMPORTANT]
> Tutti gli script in questa sezione richiedono l'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>INTERFACCIA della riga di comando di Azure in una shell bash

Il seguente script dell'interfaccia della riga di comando Mostra come modificare l'impostazione **minima della versione di TLS** in una shell bash:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
```

## <a name="change-the-connection-policy"></a>Modificare i criteri di connessione

I [criteri di connessione](connectivity-architecture.md#connection-policy) determinano la modalità di connessione dei clienti al database SQL di Azure.

## <a name="change-the-connection-policy-via-powershell"></a>Modificare i criteri di connessione tramite PowerShell

> [!IMPORTANT]
> Il database SQL di Azure supporta ancora il modulo Azure Resource Manager di PowerShell, ma tutte le attività di sviluppo future sono per il modulo AZ. SQL. Per informazioni su questi cmdlet, vedere [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici. Per lo script seguente è necessario il [modulo di Azure PowerShell](/powershell/azure/install-az-ps).

Lo script di PowerShell seguente mostra come modificare i criteri di connessione usando PowerShell:

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

## <a name="change-the-connection-policy-via-the-azure-cli"></a>Modificare i criteri di connessione tramite l'interfaccia della riga di comando di Azure

> [!IMPORTANT]
> Tutti gli script in questa sezione richiedono l'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli).

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

### <a name="azure-cli-from-a-windows-command-prompt"></a>Interfaccia della riga di comando di Azure da un prompt dei comandi di Windows

Lo script CLI seguente mostra come modificare i criteri di connessione da un prompt dei comandi di Windows (con l'interfaccia della riga di comando di Azure installata):

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica del funzionamento della connettività nel database SQL di Azure, vedere l'articolo relativo all' [architettura della connettività](connectivity-architecture.md).
- Per informazioni su come modificare i criteri di connessione per un server, vedere [conn-Policy](/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: media/single-database-create-quickstart/manage-connectivity-settings.png
[2]: media/single-database-create-quickstart/manage-connectivity-flowchart.png
