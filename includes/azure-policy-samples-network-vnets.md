---
title: File di inclusione
description: File di inclusione
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: b7bbf1fb8ffc1f57a53682d3baf288077b144572
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664582"
---
### <a name="virtual-networks"></a>Reti virtuali

|  |  |
|---------|---------|
| [Allowed Application Gateway SKUs](../articles/azure-policy/scripts/allowed-app-gate-sku.md) (SKU del gateway applicazione consentiti) | Richiede che i gateway applicazione usino uno SKU approvato. Si specifica una matrice di SKU approvati. |
| [Nessun peering reti per la rete ER](../articles/azure-policy/scripts/no-peering-er-net.md) | Impedisce a un peering reti di essere associato a una rete in un gruppo di risorse specificato. Usarlo per impedire la connessione con l'infrastruttura di rete gestita centrale. Si specifica il nome del gruppo di risorse per impedire l'associazione. |
| [Nessuna tabella di route definita dall'utente](../articles/azure-policy/scripts/no-user-def-route-table.md)  |Impedisce che le reti virtuali vengano distribuite con una tabella di route definita dall'utente. |
| [NSG X in ogni subnet](../articles/azure-policy/scripts/nsg-on-subnet.md) | Richiede che con ogni subnet virtuale venga usato uno specifico gruppo di sicurezza di rete. Si specifica l'ID del gruppo di sicurezza di rete da usare. |
| [Usare una subnet approvata per le interfacce di rete delle macchine virtuali](../articles/azure-policy/scripts/use-approved-subnet-vm-nics.md) | Richiede che le interfacce di rete usino una subnet approvata. Si specifica l'ID della subnet approvata. |
| [Usare una rete virtuale approvata per le interfacce di rete delle macchine virtuali](../articles/azure-policy/scripts/use-approved-vnet-vm-nics.md) | Richiede che le interfacce di rete usino una rete virtuale approvata. Si specifica l'ID della rete virtuale approvata. |