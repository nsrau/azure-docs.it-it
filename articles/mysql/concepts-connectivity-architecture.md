---
title: Architettura della connettività nel Database di Azure per MySQL
description: Descrive l'architettura della connettività per il Database di Azure per il server MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 7a7ac843960e253b3172d1ed22fe5b59633897dc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67062474"
---
# <a name="connectivity-architecture-in-azure-database-for-mysql"></a>Architettura della connettività nel Database di Azure per MySQL
Questo articolo illustra il Database di Azure per MySQL e architettura della connettività del modo in cui il traffico viene indirizzato al Database di Azure per l'istanza di MySQL dal client all'interno e all'esterno di Azure.

## <a name="connectivity-architecture"></a>Architettura della connettività
Connessione al Database di Azure per MySQL viene stabilito tramite un gateway che è responsabile per le connessioni in ingresso di routing alla posizione fisica del server nel nostro cluster. Il diagramma seguente illustra il flusso del traffico.

![Panoramica dell'architettura di connettività](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

Come client di connettersi al database, si ottiene una stringa di connessione che si connette al gateway. Questo gateway ha un indirizzo IP pubblico che è in ascolto sulla porta 3306. All'interno del cluster di database, il traffico viene inoltrato al Database appropriato in Azure per MySQL. Pertanto, per connettersi al server, ad esempio, da reti aziendali, è necessario aprire il firewall sul lato client per consentire il traffico in uscita sia in grado di raggiungere il gateway. Di seguito è possibile trovare un elenco completo degli indirizzi IP usati dai nostri gateway per ogni area.

## <a name="azure-database-for-mysql-gateway-ip-addresses"></a>Database di Azure per MySQL indirizzi IP del gateway
La tabella seguente elenca gli indirizzi IP primari e secondari del Database di Azure per MySQL gateway per tutte le aree dati. L'indirizzo IP primario è l'indirizzo IP corrente del gateway e il secondo indirizzo IP è un indirizzo IP di failover in caso di errore del database primario. Come accennato, i clienti devono consentire in uscita verso entrambi gli indirizzi IP. Il secondo indirizzo IP è in ascolto su alcun servizio fino a quando non viene attivato dal Database di Azure per MySQL accettare connessioni.

| **Nome dell'area** | **Indirizzo IP primario** | **Indirizzo IP secondario** |
|:----------------|:-------------|:------------------------|
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
| Europa occidentale | 191.237.232.75 | 40.68.37.158 |
| Stati Uniti occidentali 1 | 23.99.34.75 | 104.42.238.205 |
| Stati Uniti occidentali 2 | 13.66.226.202 | |
||||

> [!NOTE]
> *Stati Uniti orientali 2* ha anche l'indirizzo IP terziario `52.167.104.0`.

## <a name="next-steps"></a>Passaggi successivi

* [Creare e gestire regole del firewall di Database di Azure per MySQL con il portale di Azure](./howto-manage-firewall-using-portal.md)
* [Creare e gestire Database di Azure per le regole del firewall MySQL tramite la CLI di Azure](./howto-manage-firewall-using-cli.md)

