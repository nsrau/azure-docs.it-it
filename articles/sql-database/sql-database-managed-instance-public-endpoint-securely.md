---
title: Proteggere gli endpoint pubblici di istanza gestita - Database SQL di Azure istanza gestita | Microsoft Docs
description: Usare in modo sicuro gli endpoint pubblici in Azure con istanza gestita
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: vanto, carlrab
manager: digimobile
origin.date: 04/16/2019
ms.date: 04/29/2019
ms.openlocfilehash: 9d5a3d18e8a7d3c5a6cb08e16e74dd4fbda9ca31
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61315045"
---
# <a name="using-azure-sql-database-managed-instance-securely-with-public-endpoint"></a>Istanza in modo sicuro con endpoint pubblici gestita di Database SQL di Azure

Istanza gestita è stato possibile abilitare per fornire connettività degli utenti nel Database SQL di Azure [endpoint pubblico](../virtual-network/virtual-network-service-endpoints-overview.md). Questo articolo fornisce indicazioni come effettuare questa configurazione più sicura.

## <a name="scenarios"></a>Scenari

Istanza gestita offre endpoint privato per abilitare la connettività tra la propria rete virtuale. L'opzione predefinita è per garantire l'isolamento massimo. Tuttavia, esistono scenari in cui è necessaria una connessione all'endpoint pubblico:

- Integrazione con offerte PaaS sole multi-tenant.
- Velocità effettiva superiore dello scambio di dati rispetto all'uso di VPN.
- Criteri aziendali impediscono alle PaaS all'interno delle reti aziendale.

## <a name="deploying-managed-instance-for-public-endpoint-access"></a>Distribuzione di istanza gestita per l'accesso agli Endpoint pubblico

Benché non sia obbligatorio, il modello di distribuzione comuni per un'istanza gestita con accesso all'endpoint pubblico consiste nel creare l'istanza in una rete virtuale isolata dedicata. In questa configurazione, la rete virtuale viene usata solo per l'isolamento del cluster virtuale. Non è rilevante se lo spazio di indirizzi IP gestito istanza si sovrappone a uno spazio di indirizzi IP di rete aziendale.

## <a name="securing-data-in-motion"></a>Protezione dei dati in movimento

Se il driver del client supporta la crittografia, il traffico dei dati di istanza gestita è sempre crittografato. I dati tra l'istanza gestita e altre macchine virtuali di Azure o servizi di Azure non lascia mai la backbone di Azure. Se è presente un'istanza gestita a una connessione di rete locale, è consigliabile usare Expressroute con peering Microsoft. Circuito Expressroute consentirà di evitare lo spostamento dei dati sulla rete Internet pubblica (per la connettività privata di istanza gestita, solo il peering privato può essere usato).

## <a name="locking-down-inbound-and-outbound-connectivity"></a>Bloccare la connettività in ingresso e in uscita

Il diagramma seguente mostra le configurazioni di sicurezza consigliata.

![managed-instance-vnet.png](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

Istanza gestita ha un [indirizzo endpoint pubblico dedicato](sql-database-managed-instance-find-management-endpoint-ip-address.md). Questo indirizzo IP deve essere impostato nel firewall in uscita sul lato client delle regole del gruppo di sicurezza di rete per limitare la connettività in uscita.

Per garantire il traffico all'istanza gestita è provenienti da origini attendibili, è consigliabile per la connessione da origini diverse con gli indirizzi IP conosciuti. Limitare l'accesso all'endpoint pubblico di istanza gestita sulla porta 3342 usando un gruppo di sicurezza di rete.

Quando i client devono avviare una connessione da una rete locale, assicurarsi che l'indirizzo di origine viene convertito in un set noto di indirizzi IP. Se non si riscontra che (ad esempio, in uno scenario tipico della forza lavoro mobile), è consigliabile per usare [Point-to-site VPN connessioni e un endpoint privato](sql-database-managed-instance-configure-p2s.md).

Se vengono avviate le connessioni da Azure, è consigliabile che il traffico proviene da noto assegnato [VIP](../virtual-network/virtual-networks-reserved-public-ip.md) (ad esempio, le macchine virtuali). Per semplificare la gestione degli indirizzi VIP, i clienti potrebbero considerare l'uso [prefisso dell'indirizzo IP pubblico](../virtual-network/public-ip-address-prefix.md).