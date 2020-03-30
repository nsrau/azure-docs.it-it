---
title: Sicurezza nel database di Azure per PostgreSQL - Server singoloSecurity in Azure Database for PostgreSQL - Single Server
description: Panoramica delle funzionalità di sicurezza in Database di Azure per PostgreSQL - Server singolo.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 02dc9e1ad9ee46b1a400e44b6ef737e70571a17a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972593"
---
# <a name="security-in-azure-database-for-postgresql---single-server"></a>Sicurezza nel database di Azure per PostgreSQL - Server singoloSecurity in Azure Database for PostgreSQL - Single Server

Sono disponibili più livelli di sicurezza per proteggere i dati nel database di Azure per il server PostgreSQL.There are multiple layers of security that are available to protect the data on your Azure Database for PostgreSQL server. In questo articolo vengono descritte tali opzioni di sicurezza.

## <a name="information-protection-and-encryption"></a>Crittografia e protezione delle informazioni

### <a name="in-transit"></a>In transito
Il database di Azure per PostgreSQL protegge i dati crittografando i dati in transito con Transport Layer Security. La crittografia (SSL/TLS) viene applicata per impostazione predefinita.

### <a name="at-rest"></a>A riposo
Il servizio Database di Azure per PostgreSQL usa il modulo crittografico convalidato FIPS 140-2 per la crittografia dei dati archiviati inattivi. I dati, inclusi i backup, vengono crittografati su disco, ad eccezione dei file temporanei creati durante l'esecuzione di query. Il servizio usa la crittografia AES a 256 bit inclusa nella crittografia di archiviazione di Azure e le chiavi vengono gestite dal sistema. La crittografia dell'archiviazione è sempre attiva e non può essere disabilitata.


## <a name="network-security"></a>Sicurezza di rete
Le connessioni a un database di Azure per il server PostgreSQL vengono prima instradate tramite un gateway regionale. Il gateway dispone di un indirizzo IP accessibile pubblicamente, mentre gli indirizzi IP del server sono protetti. Per ulteriori informazioni sul gateway, vedere [l'articolo sull'architettura di connettività.](concepts-connectivity-architecture.md)  

Un database di Azure appena creato per il server PostgreSQL dispone di un firewall che blocca tutte le connessioni esterne. Anche se raggiungono il gateway, non sono autorizzati a connettersi al server. 

### <a name="ip-firewall-rules"></a>Regole del firewall IP
Le regole del firewall IP concedono l'accesso ai server in base all'indirizzo IP di origine di ogni richiesta. Per ulteriori informazioni, vedere la [panoramica delle regole del firewall.](concepts-firewall-rules.md)

### <a name="virtual-network-firewall-rules"></a>Regole del firewall della rete virtuale
Gli endpoint del servizio di rete virtuale estendono la connettività di rete virtuale tramite il backbone di Azure.Virtual network service endpoints extend your virtual network connectivity over the Azure backbone. Usando le regole di rete virtuale è possibile abilitare il database di Azure per il server PostgreSQL per consentire le connessioni da subnet selezionate in una rete virtuale. Per altre informazioni, vedere [Panoramica dell'endpoint del servizio di rete virtuale.](concepts-data-access-and-security-vnet.md)

### <a name="private-ip"></a>IP privato
Private Link consente di connettersi al database di Azure per PostgreSQL Singolo server in Azure tramite un endpoint privato. Azure Private Link porta essenzialmente i servizi di Azure all'interno della rete virtuale privata (VNet). È possibile accedere alle risorse PaaS utilizzando l'indirizzo IP privato come qualsiasi altra risorsa nella rete virtuale. Per ulteriori informazioni, vedere la [panoramica](concepts-data-access-and-security-private-link.md) del collegamento privato


## <a name="access-management"></a>Gestione degli accessi

Durante la creazione del database di Azure per il server PostgreSQL, si forniscono le credenziali per un ruolo di amministratore. Questo ruolo di amministratore può essere utilizzato per creare [ruoli PostgreSQL](https://www.postgresql.org/docs/current/user-manag.html)aggiuntivi.

È anche possibile connettersi al server usando l'autenticazione di [Azure Active Directory (AAD).](concepts-aad-authentication.md)


## <a name="threat-protection"></a>Protezione dalle minacce

È possibile acconsentire esplicitamente a [Advanced Threat Protection](concepts-data-access-and-security-threat-protection.md) che rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i server.

[La registrazione di](concepts-audit.md) controllo è disponibile per tenere traccia dell'attività nei database. 


## <a name="next-steps"></a>Passaggi successivi
- Abilitare le regole del firewall per gli indirizzi IP o le [reti virtualiEnable](concepts-data-access-and-security-vnet.md) firewall rules for [IPs](concepts-firewall-rules.md) or virtual networks
- Informazioni [sull'autenticazione](concepts-aad-authentication.md) di Azure Active Directory nel database di Azure per PostgreSQLLearn about Azure Active Directory authentication in Azure Database for PostgreSQL
