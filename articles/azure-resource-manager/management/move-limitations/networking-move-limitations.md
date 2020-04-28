---
title: Spostare le risorse di rete di Azure in una nuova sottoscrizione o in un gruppo di risorse
description: Usare Azure Resource Manager per spostare le reti virtuali e altre risorse di rete in un nuovo gruppo di risorse o una nuova sottoscrizione.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 0cd6887d3489f2ffede0f5e3d63533a33a6ccc04
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75485234"
---
# <a name="move-guidance-for-networking-resources"></a>Spostare le linee guida per le risorse di rete

Questo articolo descrive come spostare le reti virtuali e altre risorse di rete per scenari specifici.

## <a name="dependent-resources"></a>Risorse dipendenti

Quando si esegue lo spostamento di una rete virtuale, è necessario spostare anche le relative risorse dipendenti. Per i gateway VPN, è necessario spostare gli indirizzi IP, i gateway di rete virtuale e tutte le risorse di connessione associata. I gateway di rete locali possono trovarsi in un gruppo di risorse diverso.

Per spostare una macchina virtuale con una scheda di interfaccia di rete in una nuova sottoscrizione, è necessario spostare tutte le risorse dipendenti. Spostare la rete virtuale per la scheda di interfaccia di rete, tutte le altre schede di interfaccia di rete per la rete virtuale e i gateway VPN.

Per ulteriori informazioni, vedere [scenario per lo spostamento tra sottoscrizioni](../move-resource-group-and-subscription.md#scenario-for-move-across-subscriptions).

## <a name="peered-virtual-network"></a>Rete virtuale con peering

Per spostare una rete virtuale con peering, è prima necessario disabilitare il peering. Dopo la disabilitazione del peering è possibile spostare la rete virtuale. Riabilitare il peering della rete virtuale dopo lo spostamento.

## <a name="subnet-links"></a>Collegamenti subnet

Non è possibile spostare una rete virtuale in un'altra sottoscrizione se la rete virtuale contiene una subnet con collegamenti di navigazione delle risorse. Se un'istanza di Cache Redis di Azure viene distribuita in una subnet, in tale subnet è presente un collegamento di navigazione per la risorsa.

## <a name="next-steps"></a>Passaggi successivi

Per i comandi sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../move-resource-group-and-subscription.md).
