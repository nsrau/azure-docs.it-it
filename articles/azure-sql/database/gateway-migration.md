---
title: Avviso migrazione traffico gateway
description: In questo articolo viene fornita una notifica agli utenti sulla migrazione degli indirizzi IP del gateway del database SQL di Azure
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1 
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: ee55e0a66a0487291233713498f6eb541980dbba
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "90004273"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Migrazione del traffico del database SQL di Azure ai gateway più recenti
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Con l'ottimizzazione dell'infrastruttura di Azure, Microsoft aggiornerà periodicamente l'hardware per garantire la migliore esperienza possibile per i clienti. Nei prossimi mesi si prevede di aggiungere gateway basati su generazioni hardware più recenti, migrare il traffico verso di essi e infine rimuovere le autorizzazioni per i gateway creati su hardware meno recenti in alcune aree.  

I clienti riceveranno una notifica tramite posta elettronica e in portale di Azure ben prima di qualsiasi modifica ai gateway disponibili in ogni area. Le informazioni più aggiornate verranno mantenute nella tabella degli [indirizzi IP del gateway del database SQL di Azure](connectivity-architecture.md#gateway-ip-addresses) .

## <a name="status-updates"></a>Aggiornamenti dello stato

# <a name="in-progress"></a>[In corso](#tab/in-progress-ip)
### <a name="october-2020"></a>2020 ottobre

Nuovi gateway SQL vengono aggiunti alle aree seguenti:

- Germania centro-occidentale: 51.116.240.0, 51.116.248.0

Questi gateway SQL inizieranno ad accettare il traffico dei clienti il 12 ottobre 2020. 

### <a name="september-2020"></a>Settembre 2020
Nuovi gateway SQL vengono aggiunti alle aree seguenti. Questi gateway SQL dovranno iniziare ad accettare il traffico dei clienti il **15 settembre 2020**:

- Australia sudorientale: 13.77.48.10
- Canada orientale: 40.86.226.166, 52.242.30.154
- Regno Unito meridionale: 51.140.184.11, 51.105.64.0

I gateway SQL esistenti inizieranno ad accettare il traffico nelle aree seguenti. Questi gateway SQL dovranno iniziare ad accettare il traffico dei clienti il **15 settembre 2020** :

- Australia sudorientale: 191.239.192.109 e 13.73.109.251
- Stati Uniti centrali: 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96 e 104.208.21.1
- Asia orientale: 191.234.2.139, 52.175.33.150 e 13.75.32.4
- Stati Uniti orientali: 40.121.158.30, 40.79.153.12, 191.238.6.43 e 40.78.225.32
- Stati Uniti orientali 2:40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107 e 104.208.150.3
- Francia centrale: 40.79.137.0 e 40.79.129.1
- Giappone occidentale: 104.214.148.156, 40.74.100.192, 191.238.68.11 e 40.74.97.10
- Stati Uniti centro-settentrionali: 23.96.178.199, 23.98.55.75 e 52.162.104.33
- Asia sudorientale: 104.43.15.0, 23.100.117.95 e 40.78.232.3
- Stati Uniti occidentali: 104.42.238.205, 23.99.34.75 e 13.86.216.196

Nuovi gateway SQL vengono aggiunti alle aree seguenti. Questi gateway SQL dovranno iniziare ad accettare il traffico dei clienti il **10 settembre 2020**:

- Stati Uniti centro-occidentali: 13.78.248.43 
- Sudafrica settentrionale: 102.133.120.2  

Nuovi gateway SQL vengono aggiunti alle aree seguenti. Questi gateway SQL inizieranno ad accettare il traffico dei clienti il **1 ° settembre 2020**:

- Europa settentrionale: 13.74.104.113 
- Uniti Ovest: 40.78.248.10 
- Europa occidentale: 52.236.184.163 
- Stati Uniti centro-meridionali: 20.45.121.1, 20.49.88.1 

I gateway SQL esistenti inizieranno ad accettare il traffico nelle aree seguenti. Questi gateway SQL inizieranno ad accettare il traffico dei clienti il **1 ° settembre 2020** :
- Giappone orientale: 40.79.184.8, 40.79.192.5

# <a name="completed"></a>[Operazione completata](#tab/completed-ip)

Sono state completate le migrazioni del gateway seguenti: 

### <a name="august-2020"></a>Agosto 2020

Nuovi gateway SQL vengono aggiunti alle aree seguenti:

- Australia orientale: 13.70.112.9
- Canada centrale: 52.246.152.0, 20.38.144.1 
- Stati Uniti occidentali 2:40.78.240.8

Questi gateway SQL dovranno iniziare ad accettare il traffico dei clienti il 10 agosto 2020. 

### <a name="october-2019"></a>Ottobre 2019
- Brasile meridionale
- Stati Uniti occidentali
- Europa occidentale
- Stati Uniti orientali
- Stati Uniti centrali
- Asia sud-orientale
- Stati Uniti centro-meridionali
- Europa settentrionale
- Stati Uniti centro-settentrionali
- Giappone occidentale
- Giappone orientale
- Stati Uniti orientali 2
- Asia orientale

---

## <a name="impact-of-this-change"></a>Conseguenze di questa modifica

La migrazione del traffico può modificare l'indirizzo IP pubblico che DNS risolve per il database nel database SQL di Azure.
È possibile che si verifichino i seguenti effetti:

- Hardcoded l'indirizzo IP per un determinato gateway nel firewall locale
- Disporre di subnet che usano Microsoft. SQL come endpoint del servizio ma che non possono comunicare con gli indirizzi IP del gateway
- Usare la [configurazione con ridondanza della zona](high-availability-sla.md#zone-redundant-configuration) per il database

L'utente non avrà alcun effetto sui seguenti effetti:
 
- Reindirizzamento come criterio di connessione
- Connessioni al database SQL dall'interno di Azure e uso dei tag del servizio
- Le connessioni eseguite utilizzando le versioni supportate del driver JDBC per SQL Server non vedranno alcun effetto. Per le versioni JDBC supportate, vedere [scaricare Microsoft JDBC driver per SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Cosa fare se si è interessati

Si consiglia di consentire il traffico in uscita agli indirizzi IP per tutti gli [indirizzi IP del gateway](connectivity-architecture.md#gateway-ip-addresses) nell'area sulla porta TCP 1433 e l'intervallo di porte 11000-11999. Questa raccomandazione è applicabile ai client che si connettono da locale e anche a quelli che si connettono tramite gli endpoint del servizio. Per ulteriori informazioni sugli intervalli di porte, vedere [criteri di connessione](connectivity-architecture.md#connection-policy).

Le connessioni effettuate da applicazioni che utilizzano il driver Microsoft JDBC per la versione 4,0 potrebbero avere esito negativo. Le versioni precedenti di Microsoft JDBC si basano sul nome comune (CN) nel campo oggetto del certificato. La mitigazione consiste nel verificare che la proprietà hostNameInCertificate sia impostata su *. database.windows.net. Per ulteriori informazioni su come impostare la proprietà hostNameInCertificate, vedere [connessione con crittografia](/sql/connect/jdbc/connecting-with-ssl-encryption).

Se la mitigazione precedente non funziona, archiviare una richiesta di supporto per il database SQL o SQL Istanza gestita usando l'URL seguente: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Passaggi successivi

- Scopri di più sull' [architettura di connettività SQL di Azure](connectivity-architecture.md)
