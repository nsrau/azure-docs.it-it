---
title: Si noti che la migrazione di gateway per il Database SQL di Azure da Gen2 per Gen3 | Microsoft Docs
description: Articolo fornisce un avviso per gli utenti relative alla migrazione di indirizzi IP del gateway del Database SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
manager: craigg
ms.date: 07/01/2019
ms.openlocfilehash: 5894579c62c524394c7fea044b96885f7c8e8204
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538380"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Migrazione ad Azure SQL Database il traffico al gateway più recente

Come migliora l'infrastruttura di Azure, Microsoft aggiornerà periodicamente hardware per assicurarsi che Microsoft fornisce la migliore esperienza. Nei prossimi mesi, si prevede di aggiungere che i gateway basato su generazioni di hardware più recente e rimuovere le autorizzazioni di gateway basato su hardware meno recenti in alcune aree.  

I clienti verranno informati tramite posta elettronica e nel portale di Azure anche prima che qualsiasi modifica apportata ai gateway disponibili in ogni area. Le informazioni più aggiornate verranno mantenute nel [indirizzi IP del Database SQL di Azure gateway](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) tabella.

## <a name="impact-of-this-change"></a>Impatto della modifica

Il primo ciclo di rimozione delle autorizzazioni di Gateway è pianificato per 1 settembre 2019 nelle aree seguenti:

- Stati Uniti occidentali
- Europa occidentale
- East US
- Stati Uniti centrali
- Asia sudorientale
- Stati Uniti centro-meridionali
- Europa settentrionale
- Stati Uniti centro-settentrionali
- Giappone occidentale
- Giappone orientale
- Stati Uniti orientali 2
- Asia orientale

L'indirizzo IP messo fuori servizio smetterà di accettare il traffico ed eventuali nuovi tentativi di connessione verranno indirizzati a uno dei gateway nell'area.

In cui non verrà visualizzato l'impatto della modifica:

- Clienti che usano il reindirizzamento quando i criteri di connessione non visualizzano alcun impatto.
- Le connessioni al Database SQL all'interno di Azure e usando i tag di servizio non saranno interessati.
- Le connessioni effettuate con le versioni supportate del Driver JDBC per SQL Server non verranno visualizzato alcun impatto. Per le versioni supportate di JDBC, vedere [scaricare Microsoft JDBC Driver per SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Cosa fare è eseguire se sei interessato

È consigliabile consentire il traffico in uscita agli indirizzi IP per tutti i [indirizzi IP del Database SQL di Azure gateway](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) nell'area a TCP porta 1433 e la porta 11000-11999 intervallo nel proprio dispositivo firewall. Per altre informazioni sugli intervalli di porte, vedere [criterio di connessione](sql-database-connectivity-architecture.md#connection-policy).

Le connessioni effettuate dalle applicazioni usando Microsoft JDBC Driver versione 4.0 precedente potrebbero non riuscire convalida del certificato. Le versioni precedenti di Microsoft JDBC si basano sul nome comune (CN) nel campo soggetto del certificato. La soluzione consiste nell'assicurarsi che la proprietà hostNameInCertificate è impostata su *. database.windows.net. Per altre informazioni su come impostare la proprietà hostNameInCertificate, vedere [ci si connette con la crittografia SSL](/sql/connect/jdbc/connecting-with-ssl-encryption).

Se la soluzione precedente non funziona, inviare una richiesta di supporto per il Database SQL usando l'URL seguente: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [architettura della connettività SQL Azure](sql-database-connectivity-architecture.md)