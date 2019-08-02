---
title: Avviso di migrazione del gateway per il database SQL di Azure da Gen2 a Gen3 | Microsoft Docs
description: In questo articolo viene fornita una notifica agli utenti sulla migrazione degli indirizzi IP dei gateway del database SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 85691464684ff327c01a85bf357514f447564dd7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568115"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Migrazione del traffico del database SQL di Azure ai gateway più recenti

Con l'ottimizzazione dell'infrastruttura di Azure, Microsoft aggiornerà periodicamente l'hardware per garantire la migliore esperienza possibile per i clienti. Nei prossimi mesi si prevede di aggiungere gateway basati su generazioni hardware più recenti e di rimuovere le autorizzazioni dei gateway creati su hardware meno recenti in alcune aree.  

I clienti riceveranno una notifica tramite posta elettronica e in portale di Azure ben prima di qualsiasi modifica ai gateway disponibili in ogni area. Le informazioni più aggiornate verranno mantenute nella tabella degli [indirizzi IP del gateway del database SQL di Azure](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) .

## <a name="impact-of-this-change"></a>Conseguenze di questa modifica

Il primo ciclo di rimozione delle autorizzazioni del gateway è pianificato per il 1 ° settembre 2019 nelle aree seguenti:

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

L'indirizzo IP rimosso smetterà di accettare il traffico e i nuovi tentativi di connessione verranno indirizzati a uno dei gateway nell'area.

Dove non verrà visualizzato l'effetto di questa modifica:

- I clienti che usano il reindirizzamento come criterio di connessione non vedranno alcun effetto.
- Le connessioni al database SQL da Azure e all'uso dei tag del servizio non saranno interessate.
- Le connessioni eseguite utilizzando le versioni supportate del driver JDBC per SQL Server non vedranno alcun effetto. Per le versioni JDBC supportate, vedere [scaricare Microsoft JDBC driver per SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Cosa fare se si è interessati

Si consiglia di consentire il traffico in uscita agli indirizzi IP per tutti gli [indirizzi IP del gateway del database SQL di Azure](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) nell'area sulla porta TCP 1433 e l'intervallo di porte 11000-11999 nel dispositivo firewall. Per ulteriori informazioni sugli intervalli di porte, vedere [criteri di connessione](sql-database-connectivity-architecture.md#connection-policy).

Le connessioni effettuate da applicazioni che utilizzano il driver Microsoft JDBC per la versione 4,0 potrebbero avere esito negativo. Le versioni precedenti di Microsoft JDBC si basano sul nome comune (CN) nel campo oggetto del certificato. La mitigazione consiste nel verificare che la proprietà hostNameInCertificate sia impostata su *. database.windows.net. Per ulteriori informazioni su come impostare la proprietà hostNameInCertificate, vedere [connessione con la crittografia SSL](/sql/connect/jdbc/connecting-with-ssl-encryption).

Se la mitigazione precedente non funziona, archiviare una richiesta di supporto per il database SQL usando l'URL seguente: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Passaggi successivi

- Scopri di più sull' [architettura di connettività SQL di Azure](sql-database-connectivity-architecture.md)