---
title: Sicurezza nel database di Azure per PostgreSQL-server singolo
description: Panoramica delle funzionalità di sicurezza del database di Azure per PostgreSQL-server singolo.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: a1bd9b8cbcbc785425c2d1870dc555ff91f695f7
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485081"
---
# <a name="security-in-azure-database-for-postgresql---single-server"></a>Sicurezza nel database di Azure per PostgreSQL-server singolo

Sono disponibili più livelli di sicurezza per proteggere i dati nel database di Azure per il server PostgreSQL. Questo articolo descrive le opzioni di sicurezza.

## <a name="information-protection-and-encryption"></a>Crittografia e protezione delle informazioni

### <a name="in-transit"></a>In transito
Database di Azure per PostgreSQL protegge i dati mediante la crittografia dei dati in transito con Transport Layer Security. La crittografia (SSL/TLS) viene applicata per impostazione predefinita.

### <a name="at-rest"></a>In-Rest
Il servizio Database di Azure per PostgreSQL usa il modulo crittografico convalidato FIPS 140-2 per la crittografia dei dati archiviati inattivi. I dati, inclusi i backup, vengono crittografati su disco, ad eccezione dei file temporanei creati durante l'esecuzione delle query. Il servizio usa la crittografia AES a 256 bit inclusa nella crittografia di archiviazione di Azure e le chiavi vengono gestite dal sistema. La crittografia dell'archiviazione è sempre attiva e non può essere disabilitata.


## <a name="network-security"></a>Sicurezza di rete
Le connessioni a un database di Azure per il server PostgreSQL vengono innanzitutto indirizzate tramite un gateway a livello di area. Il gateway dispone di un indirizzo IP accessibile pubblicamente, mentre gli indirizzi IP del server sono protetti. Per altre informazioni sul gateway, vedere l'articolo relativo all' [architettura della connettività](concepts-connectivity-architecture.md).  

Un database di Azure per il server PostgreSQL appena creato ha un firewall che blocca tutte le connessioni esterne. Sebbene raggiungano il gateway, non sono autorizzati a connettersi al server. 

### <a name="ip-firewall-rules"></a>Regole del firewall IP
Le regole del firewall IP concedono l'accesso ai server in base all'indirizzo IP di origine di ogni richiesta. Per ulteriori informazioni, vedere [Cenni preliminari sulle regole del firewall](concepts-firewall-rules.md) .

### <a name="virtual-network-firewall-rules"></a>Regole del firewall della rete virtuale
Gli endpoint di servizio della rete virtuale estendono la connettività della rete virtuale tramite la backbone di Azure. Uso delle regole della rete virtuale è possibile abilitare il database di Azure per il server PostgreSQL per consentire le connessioni da subnet selezionate in una rete virtuale. Per ulteriori informazioni, vedere [Panoramica dell'endpoint del servizio rete virtuale](concepts-data-access-and-security-vnet.md).


## <a name="access-management"></a>gestione degli accessi

Durante la creazione del database di Azure per il server PostgreSQL è possibile fornire le credenziali per un ruolo di amministratore. Questo ruolo di amministratore può essere usato per creare altri [ruoli PostgreSQL](https://www.postgresql.org/docs/current/user-manag.html).

È anche possibile connettersi al server usando [l'autenticazione di Azure Active Directory (AAD)](concepts-aad-authentication.md).


## <a name="threat-protection"></a>Protezione dalle minacce

È possibile optare per [Advanced Threat Protection](concepts-data-access-and-security-threat-protection.md) che rileva le attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei server.

La [registrazione di controllo](concepts-audit.md) è disponibile per tenere traccia delle attività nei database. 


## <a name="next-steps"></a>Passaggi successivi
- Abilitare le regole del firewall per gli [indirizzi IP](concepts-firewall-rules.md) o le [reti virtuali](concepts-data-access-and-security-vnet.md)
- Informazioni sull' [autenticazione Azure Active Directory](concepts-aad-authentication.md) in database di Azure per PostgreSQL
