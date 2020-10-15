---
title: Architettura della connettività del database SQL di Azure
description: Questo documento illustra l'architettura di connettività del database SQL di Azure per le connessioni di database da Azure o all'esterno di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit, sqldbrb=1
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 06/26/2020
ms.openlocfilehash: 71bd250cbfb2642a291d495273c4cd66ebb2c350
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325386"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-connectivity-architecture"></a>Architettura di connettività del database SQL di Azure e di Azure sinapsi Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Questo articolo illustra l'architettura dei vari componenti che indirizzano il traffico di rete a un server nel database SQL di Azure o in Azure sinapsi Analytics. Vengono inoltre illustrati i diversi criteri di connessione e il modo in cui i client si connettono da Azure e i client che si connettono dall'esterno di Azure.

> [!IMPORTANT]
> Questo articolo *non* si applica a **Istanza gestita di SQL di Azure**. [Per un'istanza gestita](../managed-instance/connectivity-architecture-overview.md), vedere Architettura di connettività.

## <a name="connectivity-architecture"></a>Architettura della connettività

Il diagramma seguente fornisce una panoramica generale dell'architettura di connettività.

![panoramica dell'architettura](./media/connectivity-architecture/connectivity-overview.png)

I passaggi seguenti descrivono come viene stabilita una connessione al database SQL di Azure:

- I client si connettono al gateway, che ha un indirizzo IP pubblico ed è in ascolto sulla porta 1433.
- A seconda dei criteri di connessione effettivi, il gateway reindirizza o trasmette tramite proxy il traffico al cluster di database corretto.
- All'interno del database il traffico del cluster viene inviato al database appropriato.

## <a name="connection-policy"></a>Criteri di connessione

I server del database SQL e di Azure sinapsi supportano le tre opzioni seguenti per l'impostazione dei criteri di connessione del server:

- **Reindirizzamento (scelta consigliata):** I client stabiliscono connessioni direttamente al nodo che ospita il database, con conseguente riduzione della latenza e della velocità effettiva migliorata. Per le connessioni per l'utilizzo di questa modalità, i client devono:
  - Consentire le comunicazioni in uscita dal client a tutti gli indirizzi IP SQL di Azure nell'area sulle porte nell'intervallo 11000 11999. Usare i tag di servizio per SQL per semplificare la gestione.  
  - Consentire le comunicazioni in uscita dal client agli indirizzi IP del gateway del database SQL di Azure sulla porta 1433.

- **Proxy:** In questa modalità, tutte le connessioni vengono inviate tramite proxy tramite i gateway del database SQL di Azure, con conseguente aumento della latenza e della velocità effettiva ridotta. Per le connessioni a usare questa modalità, i client devono consentire le comunicazioni in uscita dal client agli indirizzi IP del gateway del database SQL di Azure sulla porta 1433.

- **Impostazione predefinita:** Questo è il criterio di connessione attivo in tutti i server dopo la creazione, a meno che non si modifichi in modo esplicito i criteri di connessione a `Proxy` o `Redirect` . Il criterio predefinito è `Redirect` per tutte le connessioni client che provengono da Azure, ad esempio da una macchina virtuale di Azure, e `Proxy` per tutte le connessioni client che hanno origine all'esterno, ad esempio le connessioni dalla workstation locale.

Se si preferisce la minor latenza e la maggiore velocità effettiva possibili, quindi, si consiglia di scegliere i criteri di connessione `Redirect` anziché `Proxy`. Tuttavia, sarà necessario soddisfare i requisiti aggiuntivi per consentire il traffico di rete come descritto in precedenza. Se il client è una macchina virtuale di Azure, è possibile eseguire questa operazione usando i gruppi di sicurezza di rete (NSG) con i [tag del servizio](../../virtual-network/security-overview.md#service-tags). Se il client si connette da una workstation locale, potrebbe essere necessario collaborare con l'amministratore di rete per consentire il traffico di rete attraverso il firewall aziendale.

## <a name="connectivity-from-within-azure"></a>Connettività dall'interno di Azure

Se ci si connette dall'interno di Azure, il criterio di connessione predefinito per le connessioni è `Redirect`. Un criterio di `Redirect` significa che dopo che la sessione TCP è stata stabilita nel database SQL di Azure, la sessione client viene quindi reindirizzata al cluster di database appropriato con una modifica all'indirizzo IP virtuale di destinazione da quello del gateway del database SQL di Azure a quello del cluster. Tutti i pacchetti successivi passano poi direttamente al cluster, ignorando il gateway del database SQL di Azure. Il diagramma seguente illustra il flusso del traffico.

![panoramica dell'architettura](./media/connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Connettività dall'esterno di Azure

Se ci si connette dall'esterno di Azure, le connessioni usano un criterio di connessione `Proxy` per impostazione predefinita. Il criterio `Proxy` significa che la sessione TCP viene stabilita tramite il gateway del database SQL di Azure e che tutti i pacchetti successivi passano attraverso il gateway. Il diagramma seguente illustra il flusso del traffico.

![panoramica dell'architettura](./media/connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Aprire inoltre le porte TCP 1434 e 14000-14999 per abilitare [la connessione con DAC](https://docs.microsoft.com/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators?view=sql-server-2017#connecting-with-dac)

## <a name="gateway-ip-addresses"></a>Indirizzi IP del gateway

La tabella seguente elenca gli indirizzi IP dei gateway per area. Per connettersi a un database SQL o a una sinapsi di Azure, è necessario consentire il traffico di rete da e verso **tutti** i gateway per l'area.

Per informazioni dettagliate sul modo in cui verrà eseguita la migrazione del traffico ai nuovi gateway in aree specifiche, fare quanto segue: [migrazione del traffico del database SQL di Azure ai gateway più recenti](gateway-migration.md)

| Nome area          | Indirizzi IP del gateway |
| --- | --- |
| Australia centrale    | 20.36.105.0 |
| Central2 Australia   | 20.36.113.0 |
| Australia orientale       | 13.75.149.87, 40.79.161.1, 13.70.112.9 |
| Australia sud-orientale | 191.239.192.109, 13.73.109.251, 13.77.48.10 |
| Brasile meridionale         | 104.41.11.5, 191.233.200.14 |
| Canada centrale       | 40.85.224.249, 52.246.152.0, 20.38.144.1 |
| Canada orientale          | 40.86.226.166, 52.242.30.154 |
| Stati Uniti centrali           | 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96, 104.208.21.1 |
| Cina orientale           | 139.219.130.35     |
| Cina orientale 2         | 40.73.82.1         |
| Cina settentrionale          | 139.219.15.17      |
| Cina settentrionale 2        | 40.73.50.0         |
| Asia orientale            | 191.234.2.139, 52.175.33.150, 13.75.32.4 |
| Stati Uniti orientali              | 40.121.158.30, 40.79.153.12, 191.238.6.43, 40.78.225.32 |
| Stati Uniti orientali 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3 |
| Francia centrale       | 40.79.137.0, 40.79.129.1 |
| Germania centrale      | 51.4.144.100       |
| Germania nord-orientale   | 51.5.144.179       |
| Germania centro-occidentale | 51.116.240.0, 51.116.248.0, 51.116.152.0 |
| India centrale        | 104.211.96.159     |
| India meridionale          | 104.211.224.146    |
| India occidentale           | 104.211.160.80     |
| Giappone orientale           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 191.237.240.43, 40.79.192.5 |
| Giappone occidentale           | 104.214.148.156, 40.74.100.192, 191.238.68.11, 40.74.97.10 |
| Corea centrale        | 52.231.32.42       |
| Corea meridionale          | 52.231.200.86      |
| Stati Uniti centro-settentrionali     | 23.96.178.199, 23.98.55.75, 52.162.104.33 |
| Europa settentrionale         | 40.113.93.91, 191.235.193.75, 52.138.224.1, 13.74.104.113 |
| Norvegia orientale          | 51.120.96.0        |
| Norvegia occidentale          | 51.120.216.0       |
| Sudafrica settentrionale   | 102.133.152.0, 102.133.120.2       |
| Sudafrica occidentale    | 102.133.24.0       |
| Stati Uniti centro-meridionali     | 13.66.62.124, 23.98.162.75, 104.214.16.32, 20.45.121.1, 20.49.88.1   |
| Asia sud-orientale      | 104.43.15.0, 23.100.117.95, 40.78.232.3   |
| Svizzera settentrionale    | 51.107.56.0, 51.107.57.0 |
| Svizzera occidentale     | 51.107.152.0, 51.107.153.0 |
| Emirati Arabi Uniti centrali          | 20.37.72.64        |
| Emirati Arabi Uniti settentrionali            | 65.52.248.0        |
| Regno Unito meridionale             | 51.140.184.11, 51.105.64.0 |
| Regno Unito occidentale              | 51.141.8.11        |
| Stati Uniti centro-occidentali      | 13.78.145.25, 13.78.248.43        |
| Europa occidentale          | 40.68.37.158, 191.237.232.75, 104.40.168.105, 52.236.184.163  |
| Stati Uniti Occidentali              | 104.42.238.205, 23.99.34.75, 13.86.216.196   |
| West US 2            | 13.66.226.202, 40.78.240.8, 40.78.248.10  |
|                      |                    |

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come modificare i criteri di connessione del database SQL di Azure per un server, vedere [conn-Policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Per informazioni sul comportamento della connessione al database SQL di Azure per i client che usano ADO.NET 4.5 o versione successiva, vedere [Porte successive alla 1433 per ADO.NET 4.5](adonet-v12-develop-direct-route-ports.md).
- Per una panoramica generale sullo sviluppo di applicazioni, vedere [Panoramica dello sviluppo di applicazioni del database SQL](develop-overview.md).
