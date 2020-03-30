---
title: Sicurezza - Database di Azure per MariaDBSecurity - Azure Database for MariaDB
description: Panoramica delle funzionalità di sicurezza nel database di Azure per MariaDB.An overview of the security features in Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8f41fe1005e96b428337bc73b9d468962a079596
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527827"
---
# <a name="security-in-azure-database-for-mariadb"></a>Protezione in Database di Azure per MariaDB

Sono disponibili più livelli di sicurezza per proteggere i dati nel database di Azure per il server MariaDB.There are multiple layers of security that are available to protect the data on your Azure Database for MariaDB server. In questo articolo vengono descritte tali opzioni di sicurezza.

## <a name="information-protection-and-encryption"></a>Crittografia e protezione delle informazioni

### <a name="in-transit"></a>In transito
Il database di Azure per MariaDB protegge i dati crittografando i dati in transito con Transport Layer Security. La crittografia (SSL/TLS) viene applicata per impostazione predefinita.

### <a name="at-rest"></a>A riposo
Il servizio Database di Azure per MariaDB usa il modulo crittografico convalidato FIPS 140-2 per la crittografia dei dati archiviati inattivi. I dati, inclusi i backup, vengono crittografati su disco, ad eccezione dei file temporanei creati durante l'esecuzione di query. Il servizio usa la crittografia AES a 256 bit inclusa nella crittografia di archiviazione di Azure e le chiavi vengono gestite dal sistema. La crittografia dell'archiviazione è sempre attiva e non può essere disabilitata.


## <a name="network-security"></a>Sicurezza di rete
Le connessioni a un server di Database di Azure per MariaDB vengono prima instradate tramite un gateway regionale. Il gateway dispone di un indirizzo IP accessibile pubblicamente, mentre gli indirizzi IP del server sono protetti. Per ulteriori informazioni sul gateway, vedere [l'articolo sull'architettura di connettività.](concepts-connectivity-architecture.md)  

Un database di Azure appena creato per il server MariaDB dispone di un firewall che blocca tutte le connessioni esterne. Anche se raggiungono il gateway, non sono autorizzati a connettersi al server. 

### <a name="ip-firewall-rules"></a>Regole del firewall IP
Le regole del firewall IP concedono l'accesso ai server in base all'indirizzo IP di origine di ogni richiesta. Per ulteriori informazioni, vedere la [panoramica delle regole del firewall.](concepts-firewall-rules.md)

### <a name="virtual-network-firewall-rules"></a>Regole del firewall della rete virtuale
Gli endpoint del servizio di rete virtuale estendono la connettività di rete virtuale tramite il backbone di Azure.Virtual network service endpoints extend your virtual network connectivity over the Azure backbone. Usando le regole di rete virtuale è possibile abilitare il database di Azure per il server MariaDB per consentire le connessioni da subnet selezionate in una rete virtuale. Per altre informazioni, vedere [Panoramica dell'endpoint del servizio di rete virtuale.](concepts-data-access-security-vnet.md)


## <a name="access-management"></a>Gestione degli accessi

Durante la creazione del database di Azure per il server MariaDB, si forniscono le credenziali per un utente amministratore. Questo amministratore può essere utilizzato per creare altri utenti MariaDB.


## <a name="threat-protection"></a>Protezione dalle minacce

È possibile acconsentire esplicitamente a [Advanced Threat Protection](concepts-data-access-and-security-threat-protection.md) che rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i server.

[La registrazione di](concepts-audit-logs.md) controllo è disponibile per tenere traccia dell'attività nei database. 


## <a name="next-steps"></a>Passaggi successivi
- Abilitare le regole del firewall per gli indirizzi IP o le [reti virtualiEnable](concepts-data-access-security-vnet.md) firewall rules for [IPs](concepts-firewall-rules.md) or virtual networks