---
title: Avviso di migrazione del traffico gateway
description: Articolo fornisce una notifica agli utenti sulla migrazione degli indirizzi IP di Azure SQL Database Gateways
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 9e3c33bb7493f07d9fbf19710f21d0114e7abec8
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757075"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database traffic migration to newer Gateways

Man mano che l'infrastruttura di Azure migliora, Microsoft aggiorna periodicamente l'hardware per garantire che forniamo la migliore esperienza possibile per i clienti. Nei prossimi mesi, si prevede di aggiungere gateway basati su generazioni di hardware più recenti, eseguire la migrazione del traffico a loro e, infine, rimuovere le autorizzazioni Gateway basati su hardware meno recente in alcune aree.  

I clienti riceveranno una notifica tramite posta elettronica e nel portale di Azure con largo anticipo rispetto a qualsiasi modifica ai gateway disponibili in ogni area. Le informazioni più aggiornate verranno mantenute nella tabella Indirizzi IP del gateway del database SQL di [Azure.The](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) most up-to-date information will be maintained in the Azure SQL Database gateway IP addresses table.

## <a name="impact-of-this-change"></a>Impatto di questo cambiamento

Il primo ciclo di migrazione del traffico verso gateway più recenti è previsto per il **14 ottobre 2019** nelle seguenti aree:
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

La migrazione del traffico modificherà l'indirizzo IP pubblico risolto da DNS per il database SQL.
Sarete interessati se si dispone di:
- Hardcoded l'indirizzo IP per un particolare gateway nel firewall locale
- Tutte le subnet che utilizzano Microsoft.SQL come endpoint del servizio, ma non possono comunicare con gli indirizzi IP del gateway

Non sarete interessati se si dispone di:
- Reindirizzamento come criterio di connessione
- Connessioni al database SQL dall'interno di Azure e tramite i tag di servizioConnections to SQL Database from inside Azure and using Service Tags
- Le connessioni effettuate utilizzando le versioni supportate del driver JDBC per SQL Server non vedranno alcun impatto. Per le versioni JDBC supportate, vedere [Scaricare il driver Microsoft JDBC per SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Cosa fare se si è interessati

È consigliabile consentire il traffico in uscita agli indirizzi IP per tutti gli indirizzi IP del [gateway del database SQL](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) di Azure nell'area sulla porta TCP 1433 e nell'intervallo di porte 11000-11999. Questa raccomandazione è applicabile ai client che si connettono da locali e anche a quelli che si connettono tramite endpoint del servizio. Per ulteriori informazioni sugli intervalli di porte, vedere [Criteri di connessione](sql-database-connectivity-architecture.md#connection-policy).

Le connessioni effettuate da applicazioni che utilizzano il driver Microsoft JDBC sotto la versione 4.0 potrebbero non riuscire la convalida del certificato. Le versioni inferiori di Microsoft JDBC si basano sul nome comune (CN) nel campo Oggetto del certificato. L'attenuazione consiste nel garantire che la proprietà hostNameInCertificate sia impostata su .database.windows.net. Per ulteriori informazioni su come impostare la proprietà hostNameInCertificate , vedere [Connessione con crittografia](/sql/connect/jdbc/connecting-with-ssl-encryption).

Se l'attenuazione precedente non funziona, presentare una richiesta di supporto per il database SQL utilizzando l'URL seguente:https://aka.ms/getazuresupport

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sull'architettura di connettività SQL di [AzureLearn more](sql-database-connectivity-architecture.md) out about Azure SQL Connectivity Architecture
