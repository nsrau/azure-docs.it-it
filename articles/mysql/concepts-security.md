---
title: Sicurezza-database di Azure per MySQL
description: Panoramica delle funzionalità di sicurezza del database di Azure per MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5caed64beb43cbb1e884a3dde6bc0052c617f83e
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378781"
---
# <a name="security-in-azure-database-for-mysql"></a>Sicurezza in Database di Azure per MySQL

Sono disponibili più livelli di sicurezza per proteggere i dati nel database di Azure per il server MySQL. Questo articolo descrive le opzioni di sicurezza.

## <a name="information-protection-and-encryption"></a>Crittografia e protezione delle informazioni

### <a name="in-transit"></a>In transito
Database di Azure per MySQL protegge i dati mediante la crittografia dei dati in transito con Transport Layer Security. La crittografia (SSL/TLS) viene applicata per impostazione predefinita.

### <a name="at-rest"></a>In-Rest
Il servizio Database di Azure per MySQL usa il modulo crittografico convalidato FIPS 140-2 per la crittografia dei dati archiviati inattivi. I dati, inclusi i backup, vengono crittografati su disco, inclusi i file temporanei creati durante l'esecuzione delle query. Il servizio usa la crittografia AES a 256 bit inclusa nella crittografia di archiviazione di Azure e le chiavi vengono gestite dal sistema. La crittografia dell'archiviazione è sempre attiva e non può essere disabilitata.


## <a name="network-security"></a>Sicurezza di rete
Le connessioni a un database di Azure per il server MySQL vengono innanzitutto indirizzate tramite un gateway a livello di area. Il gateway dispone di un indirizzo IP accessibile pubblicamente, mentre gli indirizzi IP del server sono protetti. Per altre informazioni sul gateway, vedere l'articolo relativo all' [architettura della connettività](concepts-connectivity-architecture.md).  

Un database di Azure per il server MySQL appena creato ha un firewall che blocca tutte le connessioni esterne. Sebbene raggiungano il gateway, non sono autorizzati a connettersi al server. 

### <a name="ip-firewall-rules"></a>Regole del firewall IP
Le regole del firewall IP concedono l'accesso ai server in base all'indirizzo IP di origine di ogni richiesta. Per ulteriori informazioni, vedere [Cenni preliminari sulle regole del firewall](concepts-firewall-rules.md) .

### <a name="virtual-network-firewall-rules"></a>Regole del firewall della rete virtuale
Gli endpoint di servizio della rete virtuale estendono la connettività della rete virtuale tramite la backbone di Azure. Uso delle regole della rete virtuale è possibile abilitare il database di Azure per il server MySQL per consentire le connessioni da subnet selezionate in una rete virtuale. Per ulteriori informazioni, vedere [Panoramica dell'endpoint del servizio rete virtuale](concepts-data-access-and-security-vnet.md).

### <a name="private-ip"></a>IP privato
Collegamento privato consente di connettersi al database di Azure per MySQL in Azure tramite un endpoint privato. Collegamento privato di Azure in pratica porta i servizi di Azure all'interno della rete virtuale privata. È possibile accedere alle risorse PaaS usando l'indirizzo IP privato come qualsiasi altra risorsa in VNet. Per ulteriori informazioni, vedere la [Panoramica del collegamento privato](concepts-data-access-security-private-link.md) .

## <a name="access-management"></a>Gestione degli accessi

Quando si crea il database di Azure per il server MySQL, si forniscono le credenziali per un utente amministratore. Questo amministratore può essere usato per creare altri utenti di MySQL.


## <a name="threat-protection"></a>Protezione dalle minacce

È possibile optare per [Advanced Threat Protection](concepts-data-access-and-security-threat-protection.md) che rileva le attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei server.

La [registrazione di controllo](concepts-audit-logs.md) è disponibile per tenere traccia delle attività nei database. 


## <a name="next-steps"></a>Passaggi successivi
- Abilitare le regole del firewall per gli [indirizzi IP](concepts-firewall-rules.md) o le [reti virtuali](concepts-data-access-and-security-vnet.md)
