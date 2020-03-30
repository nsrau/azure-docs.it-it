---
title: Endpoint pubblici dell'istanza gestita protettiSecure managed instance public endpoints
description: Usare in modo sicuro endpoint pubblici in Azure con un'istanza gestitaSecurely use public endpoints in Azure with a managed instance
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/08/2019
ms.openlocfilehash: 6dfeab3530445f8f9a102f47039d15b04fdf134a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821746"
---
# <a name="use-an-azure-sql-database-managed-instance-securely-with-public-endpoints"></a>Usare un'istanza gestita del database SQL di Azure in modo sicuro con endpoint pubbliciUse an Azure SQL Database managed instance securely with public endpoints

Le istanze gestite del database SQL di Azure possono fornire connettività utente tramite [endpoint pubblici.](../virtual-network/virtual-network-service-endpoints-overview.md) In questo articolo viene illustrato come rendere questa configurazione più sicura.

## <a name="scenarios"></a>Scenari

Un'istanza gestita del database SQL fornisce un endpoint privato per consentire la connettività dall'interno della rete virtuale. L'opzione predefinita consiste nel fornire il massimo isolamento. Tuttavia, esistono scenari in cui è necessario fornire una connessione endpoint pubblico:However, there are scenarios where you need to provide a public endpoint connection:

- L'istanza gestita deve integrarsi con le offerte PaaS (Platform-As-A-Service) solo multi-tenant.
- È necessaria una maggiore velocità effettiva dello scambio di dati rispetto a quando si utilizza una VPN.
- Le politiche aziendali proibiscono PaaS all'interno delle reti aziendali.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>Distribuire un'istanza gestita per l'accesso agli endpoint pubbliciDeploy a managed instance for public endpoint access

Sebbene non sia obbligatorio, il modello di distribuzione comune per un'istanza gestita con accesso agli endpoint pubblici consiste nel creare l'istanza in una rete virtuale isolata dedicata. In questa configurazione, la rete virtuale viene utilizzata solo per l'isolamento del cluster virtuale. Non importa se lo spazio di indirizzi IP dell'istanza gestita si sovrappone allo spazio di indirizzi IP di una rete aziendale.

## <a name="secure-data-in-motion"></a>Proteggere i dati in movimento

Il traffico dati dell'istanza gestita viene sempre crittografato se il driver client supporta la crittografia. I dati inviati tra l'istanza gestita e altre macchine virtuali di Azure o servizi di Azure non lasciano mai la backbone di Azure.Data sent between the managed instance and other Azure virtual machines or Azure services never leaves Azure's backbone. Se esiste una connessione tra l'istanza gestita e una rete locale, è consigliabile usare Azure ExpressRoute con il peering Microsoft.If there's a connection between the managed instance and an on-premises network, we recommend you use Azure ExpressRoute with Microsoft peering. ExpressRoute consente di evitare di spostare i dati tramite Internet pubblico. Per la connettività privata dell'istanza gestita, è possibile usare solo il peering privato.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Bloccare la connettività in ingresso e in uscita

Nel diagramma seguente vengono illustrate le configurazioni di sicurezza consigliate:The following diagram shows the recommended security configurations:

![Configurazioni di sicurezza per il blocco della connettività in ingresso e in uscitaSecurity configurations for locking down inbound and outbound connectivity](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

Un'istanza gestita dispone di un [indirizzo endpoint pubblico dedicato.](sql-database-managed-instance-find-management-endpoint-ip-address.md) Nel firewall in uscita sul lato client e nelle regole del gruppo di sicurezza di rete impostare l'indirizzo IP dell'endpoint pubblico per limitare la connettività in uscita.

Per garantire che il traffico verso l'istanza gestita provenga da origini attendibili, è consigliabile connettersi da origini con indirizzi IP noti. Usare un gruppo di sicurezza di rete per limitare l'accesso all'endpoint pubblico dell'istanza gestita sulla porta 3342.Use a network security group to limit access to the managed instance public endpoint on port 3342.

Quando i client devono avviare una connessione da una rete locale, assicurarsi che l'indirizzo di origine venga convertito in un insieme noto di indirizzi IP. Se non è possibile eseguire questa operazione (ad esempio, una forza lavoro mobile è uno scenario tipico), è consigliabile utilizzare [connessioni VPN da punto a sito e un endpoint privato.](sql-database-managed-instance-configure-p2s.md)

Se le connessioni vengono avviate da Azure, è consigliabile che il traffico provenga da un [indirizzo IP virtuale](../virtual-network/virtual-networks-reserved-public-ip.md) assegnato noto, ad esempio una macchina virtuale. Per semplificare la gestione degli indirizzi IP virtuali (VIP), è possibile utilizzare prefissi di [indirizzi IP pubblici.](../virtual-network/public-ip-address-prefix.md)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come configurare l'endpoint pubblico per gestire le istanze: [Configurare l'endpoint pubblicoLearn](sql-database-managed-instance-public-endpoint-configure.md) how to configure public endpoint for manage instances: Configure public endpoint
