---
title: Tipi di connessione di un'istanza gestita
description: Informazioni sui tipi di connessione di un'istanza gestita
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.openlocfilehash: a7df26590ec1514edcab24a1af9d85048b332d92
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773651"
---
# <a name="azure-sql-database-managed-instance-connection-types"></a>Tipi di connessione di un'istanza gestita per database SQL di Azure

Questo articolo illustra il modo in cui i client si connettono all'istanza gestita di database SQL di Azure a seconda del tipo di connessione. Sotto sono descritti alcuni esempi di script per modificare i tipi di connessione e si propongono alcune considerazioni sulla modifica delle impostazioni di connettività predefinite.

## <a name="connection-types"></a>Tipi di connessione

Istanza gestita di database SQL di Azure supporta i due tipi di connessione seguenti:

- **Redirect (scelta consigliata):** i client stabiliscono connessioni dirette al nodo che ospita il database. Per abilitare la connettività tramite il reindirizzamento, è necessario aprire i firewall e i gruppi di sicurezza di rete (NSG) per consentire l'accesso alle porte 1433 e 11000-11999. I pacchetti passano direttamente al database e, di conseguenza, si riscontrano miglioramenti delle prestazioni di latenza e velocità effettiva con il reindirizzamento sul proxy.
- **Proxy (predefinito):** In questa modalità tutte le connessioni usano un componente gateway del proxy. Per abilitare la connettività, è necessario aprire solo la porta 1433 per le reti private e la porta 3342 per la connessione pubblica. Se si sceglie questa modalità, è possibile che si riscontri un aumento della latenza e una riduzione della velocità effettiva, a seconda della natura del carico di lavoro. Se si preferisce la minor latenza e la maggiore velocità effettiva possibili, quindi, si consiglia di scegliere i criteri di connessione tramite reindirizzamento anziché tramite proxy.

## <a name="redirect-connection-type"></a>Tipo di connessione di reindirizzamento

Il tipo di connessione di reindirizzamento implica che dopo aver stabilito la sessione TCP nel motore SQL, la sessione client ottiene l'indirizzo IP virtuale di destinazione del nodo del cluster virtuale dal bilanciamento del carico. I pacchetti successivi passano direttamente al nodo del cluster virtuale, ignorando il gateway. Il diagramma seguente illustra il flusso del traffico.

![redirect.png](media/sql-database-managed-instance-connection-types/redirect.png)

> [!IMPORTANT]
> Il tipo di connessione di reindirizzamento attualmente funziona solo per l'endpoint privato. Indipendentemente dalle impostazioni del tipo di connessione, le connessioni che passano attraverso l'endpoint pubblico passano attraverso un proxy.

## <a name="proxy-connection-type"></a>Tipo di connessione proxy

Il tipo di connessione proxy implica che la sessione TCP viene stabilita tramite il gateway TCP e che tutti i pacchetti successivi passano attraverso di esso. Il diagramma seguente illustra il flusso del traffico.

![proxy.png](media/sql-database-managed-instance-connection-types/proxy.png)

## <a name="script-to-change-connection-type-settings-using-powershell"></a>Script per modificare le impostazioni del tipo di connessione tramite PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Lo script di PowerShell seguente mostra come modificare il tipo di connessione per un'istanza gestita in tipo di connessione di reindirizzamento.

```powershell
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
# Get your SubscriptionId from the Get-AzSubscription command
Get-AzSubscription
# Use your SubscriptionId in place of {subscription-id} below
Select-AzSubscription -SubscriptionId {subscription-id}
# Replace {rg-name} with the resource group for your managed instance, and replace {mi-name} with the name of your managed instance
$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}
$mi = $mi | Set-AzSqlInstance -ProxyOverride "Redirect" -force
```

## <a name="next-steps"></a>Passaggi successivi

- [Ripristinare un database a un'istanza gestita](sql-database-managed-instance-get-started-restore.md)
- Informazioni su come [configurare un endpoint pubblico in un'istanza gestita](sql-database-managed-instance-public-endpoint-configure.md)
- Informazioni sull'[architettura di connettività delle istanze gestite](sql-database-managed-instance-connectivity-architecture.md)