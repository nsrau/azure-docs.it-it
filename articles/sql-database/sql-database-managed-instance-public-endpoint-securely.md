---
title: Proteggere gli endpoint pubblici di istanza gestita - Database SQL di Azure istanza gestita | Microsoft Docs
description: Usare in modo sicuro gli endpoint pubblici in Azure con un'istanza gestita
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 05/08/2019
ms.openlocfilehash: f06677b66c8f6586fec8cc5dfe97b1515b741e9c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65470292"
---
# <a name="use-an-azure-sql-database-managed-instance-securely-with-public-endpoints"></a>Usare un'istanza gestita di Database SQL di Azure in modo sicuro con gli endpoint pubblici

Le istanze gestite possono offrire connettività degli utenti sui Database SQL di Azure [gli endpoint pubblici](../virtual-network/virtual-network-service-endpoints-overview.md). Questo articolo illustra come effettuare questa configurazione più sicura.

## <a name="scenarios"></a>Scenari

Un'istanza gestita di Database SQL fornisce un endpoint privato per consentire la connettività tra la propria rete virtuale. L'opzione predefinita è per garantire l'isolamento massimo. Tuttavia, esistono scenari in cui è necessario fornire una connessione all'endpoint pubblico:

- L'istanza gestita deve essere integrato con multi-tenant solo offerte di platform-as-a-service (PaaS).
- Necessaria una velocità effettiva dello scambio di dati rispetto a quello ottenibile quando si usa una connessione VPN.
- Criteri aziendali impediscono alle PaaS all'interno delle reti aziendale.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>Distribuire un'istanza gestita per l'accesso agli endpoint pubblici

Benché non sia obbligatorio, il modello di distribuzione comuni per un'istanza gestita con accesso all'endpoint pubblico consiste nel creare l'istanza in una rete virtuale isolata dedicata. In questa configurazione, la rete virtuale viene usata solo per l'isolamento del cluster virtuale. Non è importante se si sovrappone a spazio di indirizzi IP dell'istanza gestita con spazio di indirizzi IP della rete aziendale.

## <a name="secure-data-in-motion"></a>Proteggere i dati in transito

Se il driver del client supporta la crittografia, il traffico dei dati di istanza gestita è sempre crittografato. I dati inviati tra l'istanza gestita e altre macchine virtuali di Azure o servizi di Azure non lasciano mai la backbone di Azure. Se c'è una connessione tra l'istanza gestita e una rete locale, è consigliabile che usare Azure ExpressRoute con peering Microsoft. ExpressRoute consente di evitare lo spostamento dei dati sulla rete internet pubblica. Per la connettività privata di istanza gestita, è possibile utilizzare solo il peering privato.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Bloccare la connettività in ingresso e in uscita

Il diagramma seguente mostra le configurazioni di sicurezza consigliate:

![Configurazioni di sicurezza per bloccare la connettività in ingresso e in uscita](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

Un'istanza gestita ha un [indirizzo endpoint pubblico dedicato](sql-database-managed-instance-find-management-endpoint-ip-address.md). Nel firewall in uscita dal lato client e nelle regole del gruppo sicurezza di rete, impostare questo indirizzo IP dell'endpoint pubblico per limitare la connettività in uscita.

Per garantire il traffico all'istanza gestita è provenienti da origini attendibili, è consigliabile connettersi da origini diverse con gli indirizzi IP conosciuti. Usare un gruppo di sicurezza di rete per limitare l'accesso all'endpoint pubblico di istanza gestita sulla porta 3342.

Quando i client devono avviare una connessione da una rete locale, assicurarsi che l'indirizzo di origine viene convertito in un set noto degli indirizzi IP. Se non è possibile eseguire in modo (ad esempio, una forza lavoro mobile in uno scenario tipico), è consigliabile usare [le connessioni VPN point-to-site e un endpoint privato](sql-database-managed-instance-configure-p2s.md).

Se vengono avviate le connessioni da Azure, è consigliabile che il traffico proviene da un noto assegnato [indirizzo IP virtuale](../virtual-network/virtual-networks-reserved-public-ip.md) (ad esempio, una macchina virtuale). Per semplificare la gestione indirizzi IP virtuali (VIP) più semplice, si potrebbe voler usare [prefissi di indirizzo IP pubblici](../virtual-network/public-ip-address-prefix.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come configurare un endpoint pubblico per le istanze di gestione: [Configurare endpoint pubblici](sql-database-managed-instance-public-endpoint-configure.md)
