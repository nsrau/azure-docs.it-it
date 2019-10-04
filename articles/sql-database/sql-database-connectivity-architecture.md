---
title: Architettura di connettività del database SQL di Azure e SQL Data Warehouse | Microsoft Docs
description: Questo documento illustra l'architettura di connettività SQL di Azure per le connessioni di database da Azure o all'esterno di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 07/02/2019
ms.openlocfilehash: f15fb46568f4ad062605b51600d3c61870b48645
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828860"
---
# <a name="azure-sql-connectivity-architecture"></a>Architettura della connettività di SQL di Azure

Questo articolo illustra non solo l'architettura della connettività del database SQL di Azure e di SQL Data Warehouse, ma anche il funzionamento dei diversi componenti per indirizzare il traffico a un'istanza di SQL di Azure. La funzione dei componenti di connettività è indirizzare il traffico di rete verso il database SQL di Azure o SQL Data Warehouse con client che si connettono dall'interno di Azure e client che si connettono dall'esterno di Azure. Questo articolo include anche alcuni esempi di script per modificare la modalità di connessione e propone alcune considerazioni sulla modifica delle impostazioni di connettività predefinite.

## <a name="connectivity-architecture"></a>Architettura della connettività

Il diagramma seguente offre una panoramica generale dell'architettura della connettività del database SQL di Azure.

![panoramica dell'architettura](./media/sql-database-connectivity-architecture/connectivity-overview.png)

I passaggi seguenti descrivono come viene stabilita una connessione a un database SQL di Azure:

- I client si connettono al gateway, che ha un indirizzo IP pubblico ed è in ascolto sulla porta 1433.
- A seconda dei criteri di connessione effettivi, il gateway reindirizza o trasmette tramite proxy il traffico al cluster di database corretto.
- All'interno del cluster di database il traffico viene inoltrato al database SQL di Azure appropriato.

## <a name="connection-policy"></a>Criteri di connessione

Il database SQL di Azure supporta le tre opzioni seguenti per l'impostazione dei criteri di connessione di un server di database SQL.

- **Redirect (scelta consigliata):** i client stabiliscono connessioni dirette al nodo che ospita il database. Per abilitare la connettività, i client devono consentire le regole del firewall in uscita a tutti gli indirizzi IP di Azure nell'area usando i gruppi di sicurezza di rete (NSG) con [tag di servizio](../virtual-network/security-overview.md#service-tags)) per le porte 11000-11999, non solo gli indirizzi IP del gateway del database SQL di Azure sulla porta 1433. I pacchetti vengono inviati direttamente al database e si verifica quindi un miglioramento di prestazioni in termini latenza e velocità effettiva.
- **Proxy:** in questa modalità, tutte le connessioni vengono trasmesse tramite proxy ai gateway del database SQL di Azure. Per abilitare la connettività, il client deve avere regole del firewall in uscita che consentano solo gli indirizzi IP dei gateway del database SQL di Azure (in genere due indirizzi IP per ogni area). Se si sceglie questa modalità, è possibile che si riscontri un aumento della latenza e una riduzione della velocità effettiva, a seconda della natura del carico di lavoro. Se si preferisce la minor latenza e la maggiore velocità effettiva possibili, quindi, si consiglia di scegliere i criteri di connessione `Redirect` anziché `Proxy`.
- **Default:** i criteri di connessione applicati in tutti i server dopo la creazione, se non esplicitamente impostati su `Proxy` o `Redirect`. I criteri applicati dipendono dall'origine delle connessioni, ossia se provengono dall'interno di Azure (`Redirect`) o dall'esterno di Azure (`Proxy`).

## <a name="connectivity-from-within-azure"></a>Connettività dall'interno di Azure

Se ci si connette dall'interno di Azure, il criterio di connessione predefinito per le connessioni è `Redirect`. Un criterio `Redirect` significa che, dopo aver stabilito la sessione TCP al database SQL di Azure, la sessione client viene reindirizzata al cluster di database corretto sostituendo l'indirizzo IP virtuale di destinazione del gateway del database SQL di Azure con quello del cluster. Tutti i pacchetti successivi passano poi direttamente al cluster, ignorando il gateway del database SQL di Azure. Il diagramma seguente illustra il flusso del traffico.

![panoramica dell'architettura](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Connettività dall'esterno di Azure

Se ci si connette dall'esterno di Azure, le connessioni usano un criterio di connessione `Proxy` per impostazione predefinita. Il criterio `Proxy` significa che la sessione TCP viene stabilita tramite il gateway del database SQL di Azure e che tutti i pacchetti successivi passano attraverso il gateway. Il diagramma seguente illustra il flusso del traffico.

![panoramica dell'architettura](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Indirizzi IP del gateway del database SQL di Azure

La tabella seguente elenca gli indirizzi IP dei gateway per area. Per connettersi a un database SQL di Azure, è necessario consentire al traffico di rete di & da **tutti** i gateway per l'area.

Nell'articolo seguente sono riportati i dettagli del modo in cui verrà eseguita la migrazione del traffico ai nuovi gateway in aree specifiche: [Migrazione del traffico del database SQL di Azure ai gateway più recenti](sql-database-gateway-migration.md)


| Nome area          | Indirizzi IP del gateway |
| --- | --- |
| Australia centrale    | 20.36.105.0 |
| Central2 Australia   | 20.36.113.0 |
| Australia orientale       | 13.75.149.87, 40.79.161.1 |
| Australia sud-orientale | 191.239.192.109, 13.73.109.251 |
| Brasile meridionale         | 104.41.11.5, 191.233.200.14 |
| Canada centrale       | 40.85.224.249      |
| Canada orientale          | 40.86.226.166      |
| Stati Uniti centrali           | 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96, 104.208.21.1 | 
| Cina orientale           | 139.219.130.35     |
| Cina orientale 2         | 40.73.82.1         |
| Cina settentrionale          | 139.219.15.17      |
| Cina settentrionale 2        | 40.73.50.0         |
| Asia orientale            | 191.234.2.139, 52.175.33.150, 13.75.32.4 |
| East US              | 40.121.158.30, 40.79.153.12, 191.238.6.43, 40.78.225.32 |
| Stati Uniti orientali 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3 | 
| Francia centrale       | 40.79.137.0, 40.79.129.1 |
| Germania centrale      | 51.4.144.100       |
| Germania nord-orientale   | 51.5.144.179       |
| India centrale        | 104.211.96.159     |
| India meridionale          | 104.211.224.146    |
| India occidentale           | 104.211.160.80     |
| Giappone orientale           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 191.237.240.43, 40.79.192.5 | 
| Giappone occidentale           | 104.214.148.156, 40.74.100.192, 191.238.68.11, 40.74.97.10 | 
| Corea centrale        | 52.231.32.42       |
| Corea meridionale          | 52.231.200.86      |
| Stati Uniti centro-settentrionali     | 23.96.178.199, 23.98.55.75, 52.162.104.33 |
| Europa settentrionale         | 40.113.93.91, 191.235.193.75, 52.138.224.1 | 
| Sudafrica settentrionale   | 102.133.152.0      |
| Sudafrica occidentale    | 102.133.24.0       |
| Stati Uniti centro-meridionali     | 13.66.62.124, 23.98.162.75, 104.214.16.32   | 
| Asia sud-orientale      | 104.43.15.0, 23.100.117.95, 40.78.232.3   | 
| Emirati Arabi Uniti centrali          | 20.37.72.64        |
| Emirati Arabi Uniti settentrionali            | 65.52.248.0        |
| Regno Unito meridionale             | 51.140.184.11      |
| Regno Unito occidentale              | 51.141.8.11        |
| Stati Uniti centro-occidentali      | 13.78.145.25       |
| Europa occidentale          | 40.68.37.158, 191.237.232.75, 104.40.168.105  |
| Stati Uniti occidentali              | 104.42.238.205, 23.99.34.75, 13.86.216.196   |
| Stati Uniti occidentali 2            | 13.66.226.202      |
|                      |                    |

## <a name="change-azure-sql-database-connection-policy"></a>Modificare il criterio di connessione del database SQL di Azure

Per modificare il criterio di connessione del database SQL di Azure per un server di database SQL di Azure, usare il comando [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

- Se il criterio di connessione è impostato su `Proxy`, tutti i pacchetti di rete passano attraverso il gateway del database SQL di Azure. Per questa impostazione, è necessario consentire il traffico in uscita solo per l'indirizzo IP del gateway del database SQL di Azure. L'uso dell'impostazione `Proxy` ha una latenza maggiore rispetto all'impostazione `Redirect`.
- Se il criterio di connessione è impostato su `Redirect`, tutti i pacchetti di rete passano direttamente al cluster di database. Per questa impostazione, è necessario consentire il traffico in uscita verso più IP.

## <a name="script-to-change-connection-settings-via-powershell"></a>Script per modificare le impostazioni di connessione tramite PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato dal database SQL di Azure, ma tutte le attività di sviluppo future sono per il modulo AZ. SQL. Per questi cmdlet, vedere [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo AZ e nei moduli AzureRm sono sostanzialmente identici. Lo script seguente richiede il [modulo Azure PowerShell](/powershell/azure/install-az-ps).

Lo script di PowerShell seguente mostra come modificare il criterio di connessione.

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

## <a name="script-to-change-connection-settings-via-azure-cli"></a>Script per modificare le impostazioni di connessione tramite l'interfaccia della riga di comando di Azure

> [!IMPORTANT]
> Per questo script è necessaria l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>INTERFACCIA della riga di comando di Azure in una shell bash

> [!IMPORTANT]
> Per questo script è necessaria l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

Il seguente script dell'interfaccia della riga di comando Mostra come modificare i criteri di connessione in una shell bash.

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

> [!IMPORTANT]
> Per questo script è necessaria l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

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

- Per informazioni su come modificare il criterio di connessione del database SQL di Azure per un server di database SQL di Azure, vedere [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Per informazioni sul comportamento della connessione al database SQL di Azure per i client che usano ADO.NET 4.5 o versione successiva, vedere [Porte successive alla 1433 per ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Per una panoramica generale sullo sviluppo di applicazioni, vedere [Panoramica dello sviluppo di applicazioni del database SQL](sql-database-develop-overview.md).
