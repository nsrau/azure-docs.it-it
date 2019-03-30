---
title: Indirizzamento del traffico di Azure al database SQL di Azure e ad Azure SQL Data Warehouse | Microsoft Docs
description: Questo documento illustra l'architettura della connettività del database SQL di Azure e di SQL Data Warehouse dall'interno o dall'esterno di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: c5fadf5c445310534ab3001371e1b73b1f502f15
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661787"
---
# <a name="azure-sql-connectivity-architecture"></a>Architettura della connettività di SQL di Azure

Questo articolo illustra non solo l'architettura della connettività del database SQL di Azure e di SQL Data Warehouse, ma anche il funzionamento dei diversi componenti per indirizzare il traffico a un'istanza di SQL di Azure. La funzione dei componenti di connettività è indirizzare il traffico di rete verso il database SQL di Azure o SQL Data Warehouse con client che si connettono dall'interno di Azure e client che si connettono dall'esterno di Azure. Questo articolo include anche alcuni esempi di script per modificare la modalità di connessione e propone alcune considerazioni sulla modifica delle impostazioni di connettività predefinite.

> [!IMPORTANT]
> **[Modifica imminente] Per le connessioni dell'endpoint di servizio ai server SQL di Azure, un comportamento di connettività `Default` passa a `Redirect`.**
> I clienti sono invitati a creare nuovi server e a impostare quelli esistenti con un tipo di connessione impostata esplicitamente su Redirect (scelta consigliata) oppure su Proxy a seconda della relativa architettura di connettività.
>
> Per impedire che la connettività tramite un endpoint di servizio venga interrotta negli ambienti esistenti in seguito a questa modifica, vengono usati i dati di telemetria per effettuare le operazioni seguenti:
>
> - Per i server a cui è stato effettuato l'accesso tramite gli endpoint di servizio prima della modifica, il tipo di connessione viene impostato su `Proxy`.
> - Per tutti gli altri server, il tipo di connessione verrà impostato su `Redirect`.
>
> Gli utenti degli endpoint di servizio potrebbero tuttavia essere interessati dagli scenari seguenti:
>
> - L'applicazione si connette raramente a un server esistente, quindi i dati di telemetria non hanno acquisito le informazioni su tali applicazioni
> - La logica di distribuzione automatizzata crea un database SQL presupponendo che il comportamento predefinito per le connessioni degli endpoint di servizio sia `Proxy`
>
> Se non è stato possibile stabilire le connessioni degli endpoint di servizio al server di Azure SQL e si sospetta di essere interessati da questa modifica, verificare che il tipo di connessione sia esplicitamente impostato su `Redirect`. In tal caso, è necessario aprire le regole del firewall e i gruppi di sicurezza di rete della macchina virtuale a tutti gli indirizzi IP di Azure dell'area appartenenti al [tag di servizio](../virtual-network/security-overview.md#service-tags) Sql per le porte 11000-12000. Se non è possibile, impostare in modo esplicito il server su `Proxy`.
> [!NOTE]
> In questo argomento si applica ai server di Database SQL di Azure che ospita i database singoli e pool elastici, database SQL Data Warehouse, Database di Azure per MySQL, Database di Azure per MariaDB e Database di Azure per PostgreSQL. Per semplicità, Database SQL viene usato quando si fa riferimento al Database SQL, SQL Data Warehouse, Database di Azure per MySQL, Database di Azure per MariaDB e Database di Azure per PostgreSQL.

## <a name="connectivity-architecture"></a>Architettura della connettività

Il diagramma seguente offre una panoramica generale dell'architettura della connettività del database SQL di Azure.

![panoramica dell'architettura](./media/sql-database-connectivity-architecture/connectivity-overview.png)

I passaggi seguenti descrivono come viene stabilita una connessione a un database SQL di Azure:

- I client si connettono al gateway, che ha un indirizzo IP pubblico ed è in ascolto sulla porta 1433.
- A seconda dei criteri di connessione effettivi, il gateway reindirizza o trasmette tramite proxy il traffico al cluster di database corretto.
- All'interno del cluster di database il traffico viene inoltrato al database SQL di Azure appropriato.

## <a name="connection-policy"></a>Criteri di connessione

Il database SQL di Azure supporta le tre opzioni seguenti per l'impostazione dei criteri di connessione di un server di database SQL.

- **Redirect (scelta consigliata):** i client stabiliscono connessioni dirette al nodo che ospita il database. Per abilitare la connettività, i client devono consentire regole del firewall in uscita a tutti gli indirizzi IP di Azure nell'area usando i gruppi di sicurezza rete con [tag di servizio](../virtual-network/security-overview.md#service-tags) per le porte 11000-12000 e non solo agli indirizzi IP del gateway del database SQL di Azure sulla porta 1433. I pacchetti vengono inviati direttamente al database e si verifica quindi un miglioramento di prestazioni in termini latenza e velocità effettiva.
- **Proxy:** in questa modalità, tutte le connessioni vengono trasmesse tramite proxy ai gateway del database SQL di Azure. Per abilitare la connettività, il client deve avere regole del firewall in uscita che consentano solo gli indirizzi IP dei gateway del database SQL di Azure (in genere due indirizzi IP per ogni area). Se si sceglie questa modalità, è possibile che si riscontri un aumento della latenza e una riduzione della velocità effettiva, a seconda della natura del carico di lavoro. Se si preferisce la minor latenza e la maggiore velocità effettiva possibili, quindi, si consiglia di scegliere i criteri di connessione `Redirect` anziché `Proxy`.
- **Default:** i criteri di connessione applicati in tutti i server dopo la creazione, se non esplicitamente impostati su `Proxy` o `Redirect`. I criteri applicati dipendono dall'origine delle connessioni, ossia se provengono dall'interno di Azure (`Redirect`) o dall'esterno di Azure (`Proxy`).

## <a name="connectivity-from-within-azure"></a>Connettività dall'interno di Azure

Se ci si connette dall'interno di Azure, il criterio di connessione predefinito per le connessioni è `Redirect`. Un criterio `Redirect` significa che, dopo aver stabilito la sessione TCP al database SQL di Azure, la sessione client viene reindirizzata al cluster di database corretto sostituendo l'indirizzo IP virtuale di destinazione del gateway del database SQL di Azure con quello del cluster. Tutti i pacchetti successivi passano poi direttamente al cluster, ignorando il gateway del database SQL di Azure. Il diagramma seguente illustra il flusso del traffico.

![panoramica dell'architettura](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Connettività dall'esterno di Azure

Se ci si connette dall'esterno di Azure, le connessioni usano un criterio di connessione `Proxy` per impostazione predefinita. Il criterio `Proxy` significa che la sessione TCP viene stabilita tramite il gateway del database SQL di Azure e che tutti i pacchetti successivi passano attraverso il gateway. Il diagramma seguente illustra il flusso del traffico.

![panoramica dell'architettura](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Indirizzi IP del gateway del database SQL di Azure

Per connettersi a un database SQL di Azure da risorse locali, è necessario consentire il traffico di rete in uscita verso il gateway del database SQL di Azure per la propria area di Azure. Le connessioni passano solo attraverso il gateway quando ci si connette in modalità `Proxy`, ovvero l'impostazione predefinita per la connessione da risorse locali.

La tabella seguente elenca gli indirizzi IP primario e secondario del gateway del database SQL di Azure per tutte le aree dati. Per alcune aree sono disponibili due indirizzi IP. In queste aree, l'indirizzo IP primario è l'indirizzo IP corrente del gateway e il secondo indirizzo IP è un indirizzo IP di failover. L'indirizzo di failover è l'indirizzo verso cui potrebbe essere spostato il server per mantenere l'alta disponibilità del servizio. Per queste aree, è consigliabile consentire il traffico in uscita verso entrambi gli indirizzi IP. Il secondo indirizzo IP è di proprietà di Microsoft e non è in ascolto su alcun servizio fino a quando non viene attivato dal database SQL di Azure per accettare connessioni.

| Nome area | Indirizzo IP primario | Indirizzo IP secondario |
| --- | --- |--- |
| Australia orientale | 13.75.149.87 | 40.79.161.1 |
| Australia sud-orientale | 191.239.192.109 | 13.73.109.251 |
| Brasile meridionale | 104.41.11.5 | |
| Canada centrale | 40.85.224.249 | |
| Canada orientale | 40.86.226.166 | |
| Stati Uniti centrali | 23.99.160.139 | 13.67.215.62 |
| Cina orientale 1 | 139.219.130.35 | |
| Cina orientale 2 | 40.73.82.1 | |
| Cina settentrionale 1 | 139.219.15.17 | |
| Cina settentrionale 2 | 40.73.50.0 | |
| Asia orientale | 191.234.2.139 | 52.175.33.150 |
| Stati Uniti orientali 1 | 191.238.6.43 | 40.121.158.30 |
| Stati Uniti orientali 2 | 191.239.224.107 | 40.79.84.180 * |
| Francia centrale | 40.79.137.0 | 40.79.129.1 |
| Germania centrale | 51.4.144.100 | |
| Germania nord-orientale | 51.5.144.179 | |
| India centrale | 104.211.96.159 | |
| India meridionale | 104.211.224.146 | |
| India occidentale | 104.211.160.80 | |
| Giappone orientale | 191.237.240.43 | 13.78.61.196 |
| Giappone occidentale | 191.238.68.11 | 104.214.148.156 |
| Corea del Sud centrale | 52.231.32.42 | |
| Corea del Sud meridionale | 52.231.200.86 |  |
| Stati Uniti centro-settentrionali | 23.98.55.75 | 23.96.178.199 |
| Europa settentrionale | 191.235.193.75 | 40.113.93.91 |
| Stati Uniti centro-meridionali | 23.98.162.75 | 13.66.62.124 |
| Asia sudorientale | 23.100.117.95 | 104.43.15.0 |
| Regno Unito meridionale | 51.140.184.11 | |
| Regno Unito occidentale | 51.141.8.11| |
| Stati Uniti centro-occidentali | 13.78.145.25 | |
| Europa occidentale | 191.237.232.75 | 40.68.37.158 |
| Stati Uniti occidentali 1 | 23.99.34.75 | 104.42.238.205 |
| Stati Uniti occidentali 2 | 13.66.226.202 | |
||||

\* **NOTA:** *Stati Uniti orientali 2* ha anche l'indirizzo IP terziario `52.167.104.0`.

## <a name="change-azure-sql-database-connection-policy"></a>Modificare il criterio di connessione del database SQL di Azure

Per modificare il criterio di connessione del database SQL di Azure per un server di database SQL di Azure, usare il comando [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

- Se il criterio di connessione è impostato su `Proxy`, tutti i pacchetti di rete passano attraverso il gateway del database SQL di Azure. Per questa impostazione, è necessario consentire il traffico in uscita solo per l'indirizzo IP del gateway del database SQL di Azure. L'uso dell'impostazione `Proxy` ha una latenza maggiore rispetto all'impostazione `Redirect`.
- Se il criterio di connessione è impostato su `Redirect`, tutti i pacchetti di rete passano direttamente al cluster di database. Per questa impostazione, è necessario consentire il traffico in uscita verso più IP.

## <a name="script-to-change-connection-settings-via-powershell"></a>Script per modificare le impostazioni di connessione tramite PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure PowerShell per Resource Manager è ancora supportato dal Database SQL di Azure, ma i progetti di sviluppo future è per il modulo Az.Sql. Per questi cmdlet, vedere [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo Az e nei moduli AzureRm sono sostanzialmente identici.

> [!IMPORTANT]
> Per questo script è necessario il [modulo Azure PowerShell](/powershell/azure/install-az-ps).

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

Lo script dell'interfaccia della riga di comando seguente mostra come modificare il criterio di connessione.

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $id

# Update connection policy
az resource update --ids $id --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come modificare il criterio di connessione del database SQL di Azure per un server di database SQL di Azure, vedere [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Per informazioni sul comportamento della connessione al database SQL di Azure per i client che usano ADO.NET 4.5 o versione successiva, vedere [Porte successive alla 1433 per ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Per una panoramica generale sullo sviluppo di applicazioni, vedere [Panoramica dello sviluppo di applicazioni del database SQL](sql-database-develop-overview.md).
