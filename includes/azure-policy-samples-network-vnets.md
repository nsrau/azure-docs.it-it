---
title: File di inclusione
description: File di inclusione
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: f93e22012a4855257f5372c1fc1dbc05ad29a6cd
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155495"
---
### <a name="virtual-networks"></a>Reti virtuali

|  |  |
|---------|---------|
| [Allowed Application Gateway SKUs](../articles/governance/policy/samples/allowed-app-gateway-sku.md) (SKU del gateway applicazione consentiti) | Richiede che i gateway applicazione usino uno SKU approvato. Si specifica una matrice di SKU approvati. |
| [SKU dei gateway di rete virtuale consentiti](../articles/governance/policy/samples/allowed-vnet-gateway-sku.md) | Richiede che i gateway di rete virtuale usino uno SKU approvato. Si specifica una matrice di SKU approvati. |
| [SKU di bilanciamento del carico consentiti](../articles/governance/policy/samples/allowed-load-balancer-skus.md) | Richiede che gli strumenti di bilanciamento del carico di rete virtuale usino uno SKU approvato. Si specifica una matrice di SKU approvati. |
| [Nessun peering di rete per la rete di ExpressRoute](../articles/governance/policy/samples/no-peering-express-route-network.md) | Impedisce a un peering reti di essere associato a una rete in un gruppo di risorse specificato. Usarlo per impedire la connessione con l'infrastruttura di rete gestita centrale. Si specifica il nome del gruppo di risorse per impedire l'associazione. |
| [Nessuna tabella di route definita dall'utente](../articles/governance/policy/samples/no-user-defined-route-table.md)  | Impedisce che le reti virtuali vengano distribuite con una tabella di route definita dall'utente. |
| [NSG X in ogni subnet](../articles/governance/policy/samples/nsg-on-subnet.md) | Richiede che con ogni subnet virtuale venga usato uno specifico gruppo di sicurezza di rete. Si specifica l'ID del gruppo di sicurezza di rete da usare. |
| [Usare una subnet approvata per le interfacce di rete delle macchine virtuali](../articles/governance/policy/samples/use-approved-subnet-vm-nics.md) | Richiede che le interfacce di rete usino una subnet approvata. Si specifica l'ID della subnet approvata. |
| [Usare una rete virtuale approvata per le interfacce di rete delle macchine virtuali](../articles/governance/policy/samples/use-approved-vnet-vm-nics.md) | Richiede che le interfacce di rete usino una rete virtuale approvata. Si specifica l'ID della rete virtuale approvata. |