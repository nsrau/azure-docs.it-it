---
title: Architettura di connettività nel database di Azure per PostgreSQL
description: Descrive l'architettura di connettività per il database di Azure per il server PostgreSQL.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 5288c3025e523e767082f4ab7cfbb08805a74eaf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500099"
---
# <a name="connectivity-architecture-in-azure-database-for-postgresql"></a>Architettura di connettività nel database di Azure per PostgreSQL
Questo articolo illustra l'architettura di connettività del database di Azure per PostgreSQL e il modo in cui il traffico viene indirizzato all'istanza del database di Azure per PostgreSQL dai client all'interno e all'esterno di Azure.

## <a name="connectivity-architecture"></a>Architettura della connettività
La connessione al database di Azure per PostgreSQL viene stabilita tramite un gateway responsabile del routing delle connessioni in ingresso alla posizione fisica del server nei cluster. Il diagramma seguente illustra il flusso del traffico.

![Panoramica dell'architettura di connettività](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

Quando il client si connette al database, ottiene una stringa di connessione che si connette al gateway. Questo gateway ha un indirizzo IP pubblico che è in ascolto sulla porta 5432. All'interno del database il traffico del cluster viene inviato al database di Azure appropriato per PostgreSQL. Pertanto, per connettersi al server, ad esempio dalle reti aziendali, è necessario aprire il firewall sul lato client per consentire al traffico in uscita di raggiungere i nostri Gateway. Di seguito è possibile trovare un elenco completo degli indirizzi IP usati dai gateway per area.

## <a name="azure-database-for-postgresql-gateway-ip-addresses"></a>Indirizzi IP del gateway di database di Azure per PostgreSQL
La tabella seguente elenca gli indirizzi IP primari e secondari del database di Azure per il gateway PostgreSQL per tutte le aree dati. L'indirizzo IP primario è l'indirizzo IP corrente del gateway e il secondo indirizzo IP è un indirizzo IP di failover in caso di errore del database primario. Come indicato in precedenza, i clienti devono consentire il traffico in uscita verso gli indirizzi IP. Il secondo indirizzo IP non è in ascolto in alcun servizio fino a quando non viene attivato da database di Azure per PostgreSQL per accettare le connessioni.

| **Nome area** | **Indirizzo IP primario** | **Indirizzo IP secondario** |
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
| Sudafrica settentrionale | 102.133.152.0 | |
| Sudafrica occidentale | 102.133.24.0 | |
| Emirati Arabi Uniti settentrionali | 65.52.248.0 | |
| Regno Unito meridionale | 51.140.184.11 | |
| Regno Unito occidentale | 51.141.8.11| |
| Europa occidentale | 191.237.232.75 | 40.68.37.158 |
| Stati Uniti occidentali 1 | 23.99.34.75 | 104.42.238.205 |
| Stati Uniti occidentali 2 | 13.66.226.202 | |
||||

> [!NOTE]
> *Stati Uniti orientali 2* ha anche l'indirizzo IP terziario `52.167.104.0`.

## <a name="next-steps"></a>Passaggi successivi

* [Create and manage Azure Database for PostgreSQL firewall rules using the Azure portal](./howto-manage-firewall-using-portal.md) (Creare e gestire regole firewall per il Database di Azure per PostgreSQL usando il portale di Azure)
* [Create and manage Azure Database for PostgreSQL firewall rules using Azure CLI](./howto-manage-firewall-using-cli.md) (Creare e gestire regole firewall del Database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure)
