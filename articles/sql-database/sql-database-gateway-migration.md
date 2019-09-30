---
title: Avviso di migrazione del traffico del gateway per il database SQL di Azure | Microsoft Docs
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
ms.openlocfilehash: a2064e0012abea237c1b4c0c6f7e35119c1aeff5
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686812"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Migrazione del traffico del database SQL di Azure ai gateway più recenti

Con l'ottimizzazione dell'infrastruttura di Azure, Microsoft aggiornerà periodicamente l'hardware per garantire la migliore esperienza possibile per i clienti. Nei prossimi mesi si prevede di aggiungere gateway basati su generazioni hardware più recenti, migrare il traffico verso di essi e infine rimuovere le autorizzazioni per i gateway creati su hardware meno recenti in alcune aree.  

I clienti riceveranno una notifica tramite posta elettronica e in portale di Azure ben prima di qualsiasi modifica ai gateway disponibili in ogni area. Le informazioni più aggiornate verranno mantenute nella tabella degli [indirizzi IP del gateway del database SQL di Azure](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) .

## <a name="impact-of-this-change"></a>Conseguenze di questa modifica

Il primo ciclo di migrazione del traffico ai gateway più recenti è pianificato per il **14 ottobre 2019** nelle aree seguenti:
- Brasile meridionale
- Stati Uniti occidentali
- Europa occidentale
- East US
- Stati Uniti centrali
- Asia sud-orientale
- Stati Uniti centro-meridionali
- Europa settentrionale
- Stati Uniti centro-settentrionali
- Giappone occidentale
- Giappone orientale
- Stati Uniti orientali 2
- Asia orientale

La migrazione del traffico cambierà l'indirizzo IP pubblico risolto da DNS per il database SQL.
L'utente sarà interessato da quanto segue:
- Hardcoded l'indirizzo IP per un determinato gateway nel firewall locale
- Tutte le subnet che usano Microsoft. SQL come endpoint del servizio ma non possono comunicare con gli indirizzi IP del gateway

L'utente non avrà alcun effetto sui seguenti effetti:
- Reindirizzamento come criterio di connessione
- Connessioni al database SQL dall'interno di Azure e uso dei tag del servizio
- Le connessioni eseguite utilizzando le versioni supportate del driver JDBC per SQL Server non vedranno alcun effetto. Per le versioni JDBC supportate, vedere [scaricare Microsoft JDBC driver per SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Cosa fare se si è interessati

Si consiglia di consentire il traffico in uscita agli indirizzi IP per tutti gli [indirizzi IP del gateway del database SQL di Azure](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) nell'area sulla porta TCP 1433 e l'intervallo di porte 11000-11999. Questa raccomandazione è applicabile ai client che si connettono da locale e anche a quelli che si connettono tramite gli endpoint del servizio. Per ulteriori informazioni sugli intervalli di porte, vedere [criteri di connessione](sql-database-connectivity-architecture.md#connection-policy).

Le connessioni effettuate da applicazioni che utilizzano il driver Microsoft JDBC per la versione 4,0 potrebbero avere esito negativo. Le versioni precedenti di Microsoft JDBC si basano sul nome comune (CN) nel campo oggetto del certificato. La mitigazione consiste nel verificare che la proprietà hostNameInCertificate sia impostata su *. database.windows.net. Per ulteriori informazioni su come impostare la proprietà hostNameInCertificate, vedere [connessione con la crittografia SSL](/sql/connect/jdbc/connecting-with-ssl-encryption).

Se la mitigazione precedente non funziona, archiviare una richiesta di supporto per il database SQL usando l'URL seguente: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Passaggi successivi

- Scopri di più sull' [architettura di connettività SQL di Azure](sql-database-connectivity-architecture.md)
