---
title: Architettura di connettività - Database di Azure per MariaDB
description: Descrive l'architettura di connettività per il database di Azure per il server MariaDB.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 4fd6cc2133c6910bace6c36d153085956419b22a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532367"
---
# <a name="connectivity-architecture-in-azure-database-for-mariadb"></a>Architettura di connettività nel database di Azure per MariaDB
Questo articolo illustra l'architettura di connettività del database di Azure per MariaDB e come il traffico viene indirizzato al database di Azure per l'istanza MariaDB dai client sia all'interno che all'esterno di Azure.This article explains the Azure Database for MariaDB connectivity architecture as well as how the traffic is directed to your Azure Database for MariaDB instance from clients both within and outside Azure.

## <a name="connectivity-architecture"></a>Architettura della connettività

La connessione al database di Azure per MariaDB viene stabilita tramite un gateway responsabile del routing delle connessioni in ingresso alla posizione fisica del server nei cluster. Il diagramma seguente illustra il flusso del traffico.

![Panoramica dell'architettura di connettività](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

Quando il client si connette al database, ottiene una stringa di connessione che si connette al gateway. Questo gateway ha un indirizzo IP pubblico in ascolto sulla porta 3306.This gateway has a public IP address that listens to port 3306. All'interno del cluster di database, il traffico viene inoltrato al database di Azure appropriato per MariaDB. Pertanto, per connettersi al server, ad esempio da reti aziendali, è necessario aprire il firewall lato client per consentire al traffico in uscita di raggiungere i gateway. Di seguito è riportato un elenco completo degli indirizzi IP utilizzati dai nostri gateway per regione.

## <a name="azure-database-for-mariadb-gateway-ip-addresses"></a>Database di Azure per gli indirizzi IP del gateway MariaDBAzure Database for MariaDB gateway IP addresses

Nella tabella seguente sono elencati gli indirizzi IP primari e secondari del gateway Database di Azure per MariaDB per tutte le aree dati. L'indirizzo IP primario è l'indirizzo IP corrente del gateway e il secondo indirizzo IP è un indirizzo IP di failover in caso di errore del server primario. Come accennato in precedenza, i clienti devono consentire l'uscita a entrambi gli indirizzi IP. Il secondo indirizzo IP non è in ascolto su alcun servizio finché non viene attivato dal database di Azure affinché MariaDB accetti le connessioni.

| **Nome area** | **Indirizzi IP gateway** |
|:----------------|:-------------|
| Australia centrale| 20.36.105.0     |
| Australia Centrale2     | 20.36.113.0     |
| Australia orientale | 13.75.149.87, 40.79.161.1     |
| Australia sud-orientale |191.239.192.109, 13.73.109.251     |
| Brasile meridionale | 104.41.11.5, 191.233.201.8, 191.233.200.16     |
| Canada centrale |40.85.224.249     |
| Canada orientale | 40.86.226.166     |
| Stati Uniti centrali | 23.99.160.139, 13.67.215.62     |
| Cina orientale 2 | 40.73.82.1     |
| Cina settentrionale 2 | 40.73.50.0     |
| Asia orientale | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     |
| Stati Uniti orientali | 40.121.158.30, 191.238.6.43     |
| Stati Uniti orientali 2 |40.79.84.180, 191.239.224.107, 52.177.185.181     |
| Francia centrale | 40.79.137.0, 40.79.129.1     |
| Germania centrale | 51.4.144.100     |
| Germania nord-orientale | 51.5.144.179     |
| India centrale | 104.211.96.159     |
| India meridionale | 104.211.224.146     |
| India occidentale | 104.211.160.80     |
| Giappone orientale | 13.78.61.196, 191.237.240.43     |
| Giappone occidentale | 104.214.148.156, 191.238.68.11, 40.74.96.6, 40.74.96.7     |
| Corea centrale | 52.231.32.42     |
| Corea meridionale | 52.231.200.86     |
| Stati Uniti centro-settentrionali | 23.96.178.199, 23.98.55.75, 52.162.104.35, 52.162.104.36     |
| Europa settentrionale | 40.113.93.91, 191.235.193.75     |
| Sudafrica settentrionale  | 102.133.152.0     |
| Sudafrica Ovest    | 102.133.24.0     |
| Stati Uniti centro-meridionali |13.66.62.124, 23.98.162.75, 20.45.120.0, 104.214.16.39     |
| Asia sud-orientale | 104.43.15.0, 23.100.117.95, 40.78.233.2, 23.98.80.12     |
| Emirati Arabi Uniti centrali | 20.37.72.64     |
| Emirati Arabi Uniti settentrionali | 65.52.248.0     |
| Regno Unito meridionale | 51.140.184.11     |
| Regno Unito occidentale | 51.141.8.11     |
| Stati Uniti centro-occidentali | 13.78.145.25     |
| Europa occidentale | 40.68.37.158, 191.237.232.75     |
| Stati Uniti occidentali | 104.42.238.205, 23.99.34.75     |
| Stati Uniti occidentali 2 | 13.66.226.202     |
||||

## <a name="next-steps"></a>Passaggi successivi

* [Creare e gestire le regole del firewall di Database di Azure per MariaDB con il portale di Azure](./howto-manage-firewall-portal.md)
* [Creare e gestire regole del firewall di Database di Azure per MariaDB tramite l'interfaccia della riga di comando di Azure](./howto-manage-firewall-cli.md)
