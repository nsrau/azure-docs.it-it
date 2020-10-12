---
title: Tipi di connessioni
titleSuffix: Azure SQL Managed Instance
description: Informazioni sui tipi di connessione Istanza gestita SQL di Azure
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f1c4fe8268d24026609f55d76a102a5c9a4e8295
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91356302"
---
# <a name="azure-sql-managed-instance-connection-types"></a>Tipi di connessione di Istanza gestita di SQL di Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Questo articolo illustra il modo in cui i client si connettono al Istanza gestita SQL di Azure a seconda del tipo di connessione. Sotto sono descritti alcuni esempi di script per modificare i tipi di connessione e si propongono alcune considerazioni sulla modifica delle impostazioni di connettività predefinite.

## <a name="connection-types"></a>Tipi di connessione

Azure SQL Istanza gestita supporta i due tipi di connessione seguenti:

- **Redirect (scelta consigliata):** i client stabiliscono connessioni dirette al nodo che ospita il database. Per abilitare la connettività tramite il reindirizzamento, è necessario aprire i firewall e i gruppi di sicurezza di rete (NSG) per consentire l'accesso alle porte 1433 e 11000-11999. I pacchetti passano direttamente al database e, di conseguenza, si riscontrano miglioramenti delle prestazioni di latenza e velocità effettiva con il reindirizzamento sul proxy.
- **Proxy (predefinito):** In questa modalità tutte le connessioni usano un componente gateway del proxy. Per abilitare la connettività, è necessario aprire solo la porta 1433 per le reti private e la porta 3342 per la connessione pubblica. Se si sceglie questa modalità, è possibile che si riscontri un aumento della latenza e una riduzione della velocità effettiva, a seconda della natura del carico di lavoro. Per la minor latenza e la maggiore velocità effettiva possibili, quindi, è vivamente consigliabile scegliere i criteri di connessione tramite reindirizzamento anziché tramite proxy.

## <a name="redirect-connection-type"></a>Tipo di connessione di reindirizzamento

Nel tipo di connessione reindirizzamento, dopo che la sessione TCP viene stabilita nel motore SQL, la sessione client ottiene l'indirizzo IP virtuale di destinazione del nodo del cluster virtuale dal servizio di bilanciamento del carico. I pacchetti successivi passano direttamente al nodo del cluster virtuale, ignorando il gateway. Il diagramma seguente illustra il flusso del traffico.

![Il diagramma mostra una rete locale con reindirizzamento-Find-DB connesso a un gateway in una rete virtuale di Azure e una query di reindirizzamento connessa a un nodo primario del database nella rete virtuale.](./media/connection-types-overview/redirect.png)

> [!IMPORTANT]
> Il tipo di connessione di reindirizzamento attualmente funziona solo per un endpoint privato. Indipendentemente dalle impostazioni del tipo di connessione, le connessioni che passano attraverso l'endpoint pubblico passano attraverso un proxy.

## <a name="proxy-connection-type"></a>Tipo di connessione proxy

Nel tipo di connessione proxy la sessione TCP viene stabilita usando il gateway e tutti i pacchetti successivi li passano. Il diagramma seguente illustra il flusso del traffico.

![Il diagramma mostra una rete locale con un proxy connesso a un gateway in una rete virtuale di Azure, connettersi accanto a un nodo primario del database nella rete virtuale.](./media/connection-types-overview/proxy.png)

## <a name="changing-connection-type"></a>Modifica del tipo di connessione

- **Tramite il portale:** Per modificare il tipo di connessione utilizzando la portale di Azure, aprire la pagina rete virtuale e utilizzare l'impostazione **tipo** di connessione per modificare il tipo di connessione e salvare le modifiche.

- **Script per modificare le impostazioni del tipo di connessione tramite PowerShell:**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Lo script di PowerShell seguente mostra come modificare il tipo di connessione per un'istanza gestita in `Redirect` .

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

- [Ripristinare un database a SQL Istanza gestita](restore-sample-database-quickstart.md)
- Informazioni su come [configurare un endpoint pubblico in SQL istanza gestita](public-endpoint-configure.md)
- Informazioni sull' [architettura di connettività di SQL istanza gestita](connectivity-architecture-overview.md)
