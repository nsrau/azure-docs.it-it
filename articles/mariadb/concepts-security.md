---
title: Security in Azure Database for MariaDB - Single Server
description: An overview of the security features in Azure Database for MariaDB - Single Server.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: 1a2d7ac449c131519473afaa80eb3a57c287eb28
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485185"
---
# <a name="security-in-azure-database-for-mariadb---single-server"></a>Security in Azure Database for MariaDB - Single Server

There are multiple layers of security that are available to protect the data on your Azure Database for MariaDB server. This article outlines those security options.

## <a name="information-protection-and-encryption"></a>Crittografia e protezione delle informazioni

### <a name="in-transit"></a>In-transit
Azure Database for MariaDB secures your data by encrypting data in-transit with Transport Layer Security. Encryption (SSL/TLS) is enforced by default.

### <a name="at-rest"></a>At-rest
Il servizio Database di Azure per MariaDB usa il modulo crittografico convalidato FIPS 140-2 per la crittografia dei dati archiviati inattivi. Data, including backups, are encrypted on disk, with the exception of temporary files created while running queries. Il servizio usa la crittografia AES a 256 bit inclusa nella crittografia di archiviazione di Azure e le chiavi vengono gestite dal sistema. La crittografia dell'archiviazione è sempre attiva e non può essere disabilitata.


## <a name="network-security"></a>Sicurezza di rete
Connections to an Azure Database for MariaDB server are first routed through a regional gateway. The gateway has a publicly accessible IP, while the server IP addresses are protected. For more information about the gateway, visit the [connectivity architecture article](concepts-connectivity-architecture.md).  

A newly created Azure Database for MariaDB server has a firewall that blocks all external connections. Though they reach the gateway, they are not allowed to connect to the server. 

### <a name="ip-firewall-rules"></a>Regole del firewall IP
IP firewall rules grant access to servers based on the originating IP address of each request. See the [firewall rules overview](concepts-firewall-rules.md) for more information.

### <a name="virtual-network-firewall-rules"></a>Regole del firewall della rete virtuale
Virtual network service endpoints extend your virtual network connectivity over the Azure backbone. Using virtual network rules you can enable your Azure Database for MariaDB server to allow connections from selected subnets in a virtual network. For more information, see the [virtual network service endpoint overview](concepts-data-access-security-vnet.md).


## <a name="access-management"></a>gestione degli accessi

While creating the Azure Database for MariaDB server, you provide credentials for an administrator user. This administrator can be used to create additional MariaDB users.


## <a name="threat-protection"></a>Protezione dalle minacce

You can opt in to [Advanced Threat Protection](concepts-data-access-and-security-threat-protection.md) which detects anomalous activities indicating unusual and potentially harmful attempts to access or exploit servers.

[Audit logging](concepts-audit-logs.md) is available to track activity in your databases. 


## <a name="next-steps"></a>Passaggi successivi
- Enable firewall rules for [IPs](concepts-firewall-rules.md) or [virtual networks](concepts-data-access-security-vnet.md)