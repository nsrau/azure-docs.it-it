---
title: Spostare le risorse di rete di Azure in una nuova sottoscrizione o in un gruppo di risorse | Microsoft Docs
description: Usare Azure Resource Manager per spostare le reti virtuali e altre risorse di rete in un nuovo gruppo di risorse o una nuova sottoscrizione.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: c41ab1c3309243fa3a96d907f620ffeff10376a1
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69626262"
---
# <a name="move-guidance-for-networking-resources"></a>Spostare le linee guida per le risorse di rete

Questo articolo descrive come spostare le reti virtuali e altre risorse di rete per scenari specifici.

## <a name="dependent-resources"></a>Risorse dipendenti

Quando si esegue lo spostamento di una rete virtuale, è necessario spostare anche le relative risorse dipendenti. Per i gateway VPN, è necessario spostare gli indirizzi IP, i gateway di rete virtuale e tutte le risorse di connessione associata. I gateway di rete locali possono trovarsi in un gruppo di risorse diverso.

Per spostare una macchina virtuale con una scheda di interfaccia di rete, è necessario spostare tutte le risorse dipendenti. Spostare la rete virtuale per la scheda di interfaccia di rete, tutte le altre schede di interfaccia di rete per la rete virtuale e i gateway VPN.

## <a name="state-of-dependent-resources"></a>Stato delle risorse dipendenti

Se il gruppo di risorse di origine o di destinazione contiene una rete virtuale, durante lo spostamento vengono controllati gli Stati di tutte le risorse dipendenti per la rete virtuale. Se una di queste risorse si trova in uno stato di errore, lo spostamento è bloccato. Se ad esempio una macchina virtuale che usa la rete virtuale non è riuscita, lo spostamento è bloccato. Lo spostamento viene bloccato anche quando la macchina virtuale non è una delle risorse spostate e non si trova in uno dei gruppi di risorse per lo spostamento. Per evitare questo problema, spostare le risorse in un gruppo di risorse che non dispone di una rete virtuale.

## <a name="peered-virtual-network"></a>Rete virtuale con peering

Per spostare una rete virtuale con peering, è prima necessario disabilitare il peering. Dopo la disabilitazione del peering è possibile spostare la rete virtuale. Riabilitare il peering della rete virtuale dopo lo spostamento.

## <a name="subnet-links"></a>Collegamenti subnet

Non è possibile spostare una rete virtuale in un'altra sottoscrizione se la rete virtuale contiene una subnet con collegamenti di navigazione delle risorse. Se un'istanza di Cache Redis di Azure viene distribuita in una subnet, in tale subnet è presente un collegamento di navigazione per la risorsa.

## <a name="next-steps"></a>Passaggi successivi

Per i comandi sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../resource-group-move-resources.md).
