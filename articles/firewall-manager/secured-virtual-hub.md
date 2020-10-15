---
title: Che cos'è un hub virtuale protetto?
description: Informazioni sugli hub virtuali protetti
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: victorh
ms.openlocfilehash: c840bf9e82b8dcdb1fbf9b380ea847b3d1b08dd9
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91948069"
---
# <a name="what-is-a-secured-virtual-hub"></a>Che cos'è un hub virtuale protetto?

Un hub virtuale è una rete virtuale gestita da Microsoft che consente la connettività da altre risorse. Quando un hub virtuale viene creato da una rete WAN virtuale nel portale di Azure, vengono creati come componenti un hub virtuale VNet e gateway (facoltativo).

Un hub virtuale *protetto* è un [Hub WAN virtuale di Azure](../virtual-wan/virtual-wan-about.md#resources) con criteri di sicurezza e routing associati configurati da gestione firewall di Azure. USA Hub virtuali protetti per creare con facilità architetture con hub e spoke e transitive con servizi di sicurezza nativi per la governance e la protezione del traffico. 

È possibile usare un hub virtuale protetto per filtrare il traffico tra le reti virtuali (V2V), le reti virtuali e le succursali (B2V) e il traffico verso Internet (B2I/V2I). Un hub virtuale protetto fornisce il routing automatico. Non è necessario configurare UdR (route definite dall'utente) per instradare il traffico attraverso il firewall.

È possibile scegliere i provider di sicurezza necessari per proteggere e gestire il traffico di rete, tra cui firewall di Azure, provider di sicurezza come servizio (SECar) di terze parti o entrambi. Attualmente, un hub protetto non supporta il filtraggio e il filtraggio di branch to Branch (B2B) tra più hub. Per altre informazioni, vedere informazioni su [gestione firewall di Azure](overview.md#known-issues). 

## <a name="create-a-secured-virtual-hub"></a>Creare un hub virtuale protetto

Usando gestione firewall nella portale di Azure è possibile creare un nuovo hub virtuale protetto o convertire un hub virtuale esistente creato in precedenza tramite la rete WAN virtuale di Azure.

## <a name="next-steps"></a>Passaggi successivi

Per creare un hub virtuale protetto e usarlo per proteggere e governare una rete hub-spoke, vedere [esercitazione: proteggere la rete cloud con Azure Firewall Manager usando il portale di Azure](secure-cloud-network.md).