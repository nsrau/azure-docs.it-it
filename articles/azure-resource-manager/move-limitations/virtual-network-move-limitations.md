---
title: Spostare le risorse delle reti virtuali di Azure alla nuova sottoscrizione o gruppo di risorse | Microsoft Docs
description: Usare Azure Resource Manager per spostare le reti virtuali in un nuovo gruppo di risorse o sottoscrizione.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 809d403a463048910a284e7f8fcdc18cf7c65b69
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723493"
---
# <a name="move-guidance-for-virtual-networks"></a>Spostare linee guida per le reti virtuali

Questo articolo descrive come spostare le reti virtuali per scenari specifici.

## <a name="dependent-resources"></a>Risorse dipendenti

Quando si esegue lo spostamento di una rete virtuale, è necessario spostare anche le relative risorse dipendenti. Per i gateway VPN, è necessario spostare gli indirizzi IP, i gateway di rete virtuale e tutte le risorse di connessione associata. I gateway di rete locali possono trovarsi in un gruppo di risorse diverso.

Per spostare una macchina virtuale con una scheda di interfaccia di rete, è necessario spostare tutte le risorse dipendenti. Spostare la rete virtuale per la scheda di interfaccia di rete, tutte le altre schede di interfaccia di rete per la rete virtuale e i gateway VPN.

## <a name="peered-virtual-network"></a>Rete virtuale con peering

Per spostare una rete virtuale con peering, è prima necessario disabilitare il peering. Dopo la disabilitazione del peering è possibile spostare la rete virtuale. Riabilitare il peering della rete virtuale dopo lo spostamento.

## <a name="subnet-links"></a>Collegamenti subnet

Non è possibile spostare una rete virtuale in un'altra sottoscrizione se la rete virtuale contiene una subnet con collegamenti di navigazione delle risorse. Se un'istanza di Cache Redis di Azure viene distribuita in una subnet, in tale subnet è presente un collegamento di navigazione per la risorsa.

## <a name="next-steps"></a>Passaggi successivi

Per i comandi sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../resource-group-move-resources.md).
