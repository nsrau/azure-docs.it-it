---
title: Usare applicazione Azure gateway con macchine virtuali VMware
description: Viene descritto come usare il gateway applicazione di Azure per gestire il traffico Web in ingresso per i server Web in esecuzione in macchine virtuali VMware. vincere l'ambiente cloud privato CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2cbfdd358fdfd5403c677c067376142169cdc6bf
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576784"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-cloudsimple-private-cloud-environment"></a>Usare applicazione Azure gateway con macchine virtuali VMware nell'ambiente cloud privato CloudSimple

È possibile usare il gateway applicazione Azure per gestire il traffico Web in ingresso per i server Web in esecuzione in macchine virtuali VMware all'interno dell'ambiente cloud privato CloudSimple.

Sfruttando applicazione Azure gateway in una distribuzione ibrida pubblica-privata, è possibile gestire il traffico Web per le applicazioni, fornire un front-end sicuro ed eseguire l'offload dell'elaborazione SSL per i servizi in esecuzione nell'ambiente VMware. Il gateway applicazione Azure instrada il traffico Web in ingresso alle istanze del pool back-end che si trovano in ambienti VMware in base alle regole configurate e ai Probe di integrità.

Questa soluzione applicazione Azure gateway richiede:

* Disporre di una sottoscrizione di Azure
* Creare e configurare una rete virtuale di Azure e una subnet all'interno della rete virtuale.
* Creare e configurare le regole di NSG e il peering di vNet usando ExpressRoute nel cloud privato CloudSimple.
* Creare & configurare il cloud privato.
* Creare & configurare il gateway di applicazione Azure.

## <a name="azure-application-gateway-deployment-scenario"></a>Scenario di distribuzione di applicazione Azure gateway

In questo scenario, il gateway applicazione Azure viene eseguito nella rete virtuale di Azure. La rete virtuale è connessa al cloud privato tramite un circuito ExpressRoute. Tutte le subnet nel cloud privato sono raggiungibili tramite IP dalle subnet della rete virtuale.

![Servizio di bilanciamento del carico di Azure in rete virtuale di Azure](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>Come distribuire la soluzione

Il processo di distribuzione è costituito dalle attività seguenti:

1. [Verificare che siano soddisfatti i prerequisiti](#1-verify-prerequisites)
2. [Connettere la connessione virtuale di Azure al cloud privato](#2-connect-your-azure-virtual-network-to-your-private-cloud)
3. [Distribuire un gateway applicazione di Azure](#3-deploy-an-azure-application-gateway)
4. [Creare e configurare un pool di macchine virtuali del server Web nel cloud privato](#4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud)

## <a name="1-verify-prerequisites"></a>1. Verificare i prerequisiti

Verificare che siano soddisfatti i prerequisiti seguenti:

* È già stata creata una Azure Resource Manager e una rete virtuale.
* Una subnet dedicata (per il gateway applicazione) all'interno della rete virtuale di Azure è già stata creata.
* Un cloud privato CloudSimple è già stato creato.
* Non esiste alcun conflitto IP tra le subnet IP nella rete virtuale e le subnet nel cloud privato.

## <a name="2-connect-your-azure-virtual-network-to-your-private-cloud"></a>2. Connettere la rete virtuale di Azure al cloud privato

Per connettere la rete virtuale di Azure al cloud privato, seguire questa procedura.

1. [Nel portale di CloudSimple copiare le informazioni sul peering ExpressRoute](virtual-network-connection.md).

2. [Configurare un gateway di rete virtuale per la rete virtuale di Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).

3. [Collegare la rete virtuale al circuito ExpressRoute di CloudSimple](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

4. [Usare le informazioni di peering copiate per collegare la rete virtuale al circuito ExpressRoute](virtual-network-connection.md).

## <a name="3-deploy-an-azure-application-gateway"></a>3. Distribuire un gateway applicazione di Azure

Le istruzioni dettagliate per questa operazione sono disponibili in [creare un gateway applicazione con regole di routing basate sul percorso usando il portale di Azure](../application-gateway/create-url-route-portal.md). Di seguito è riportato un riepilogo dei passaggi necessari:

1. Creare una rete virtuale nella sottoscrizione e nel gruppo di risorse.
2. Creare una subnet, da usare come subnet dedicata, all'interno della rete virtuale.
3. Creare un gateway applicazione standard (abilitando facoltativamente WAF):  Dalla Home page di portale di Azure fare clic su **Resource** > **Network** > **gateway applicazione** dal lato superiore sinistro della pagina. Selezionare lo SKU standard e le dimensioni e specificare le informazioni sulla sottoscrizione, il gruppo di risorse e la località di Azure. Se necessario, creare un nuovo indirizzo IP pubblico per il gateway applicazione e fornire i dettagli sulla rete virtuale e la subnet dedicata per il gateway applicazione.
4. Aggiungere un pool back-end con macchine virtuali e aggiungerlo al gateway applicazione.

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud"></a>4. Creare e configurare un pool di macchine virtuali del server Web nel cloud privato

In vCenter creare VM con il sistema operativo e il server Web di propria scelta, ad esempio Windows/IIS o Linux/Apache. Scegliere una subnet/VLAN designata per il livello Web nel cloud privato. Verificare che almeno un vNIC delle macchine virtuali del server Web si trovi nella subnet del livello Web.
