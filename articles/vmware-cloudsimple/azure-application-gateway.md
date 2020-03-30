---
title: Usare il gateway applicazione di Azure con le macchine virtuali VMwareUse Azure Application Gateway with VMware virtual machines
description: Descrive come usare il gateway applicazione di Azure per gestire il traffico Web in ingresso per i server Web in esecuzione nelle macchine virtuali VMware che eseguono l'ambiente CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2cbfdd358fdfd5403c677c067376142169cdc6bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77015457"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-cloudsimple-private-cloud-environment"></a>Usare il Gateway applicazione di Azure con macchine virtuali VMware nell'ambiente CloudSimple Private CloudUse Azure Application Gateway with VMware virtual machines in the CloudSimple Private Cloud environment

È possibile usare il gateway applicazione di Azure per gestire il traffico Web in ingresso per i server Web in esecuzione nelle macchine virtuali VMware all'interno dell'ambiente CloudSimple Private Cloud.You can use the Azure Application Gateway to manage incoming web traffic for your web servers running in VMware virtual machines within your CloudSimple Private Cloud environment.

Sfruttando il gateway applicazione di Azure in una distribuzione ibrida pubblico-privata, è possibile gestire il traffico Web verso le applicazioni, fornire un front-end sicuro e eseguire l'offload dell'elaborazione SSL per i servizi in esecuzione nell'ambiente VMware.By leveraging Azure Application Gateway in a public-private hybrid deployment, you can manage web traffic to your applications, provide a secure front-end, and offload SSL processing for their services running in VMware environment. Il gateway applicazione di Azure indirizza il traffico Web in ingresso alle istanze del pool back-end che risiedano in ambienti VMware in base alle regole configurate e ai probe di integrità.

Questa soluzione Gateway applicazione di Azure richiede di:This Azure Application Gateway solution requires you to:

* Disporre di una sottoscrizione di Azure
* Creare e configurare una rete virtuale di Azure e una subnet all'interno della rete virtuale.
* Creare e configurare le regole del gruppo di sicurezza di rete ed eseguire il peering della rete virtuale usando ExpressRoute nel cloud privato CloudSimple.Create and configure NSG rules and peer your vNet using ExpressRoute to your CloudSimple Private Cloud.
* Creare & configurare il cloud privato.
* Creare & configurare il gateway applicazione di Azure.Create with Configure your Azure Application Gateway.

## <a name="azure-application-gateway-deployment-scenario"></a>Scenario di distribuzione di Gateway applicazione di AzureAzure Application Gateway scenario

In questo scenario, il gateway applicazione di Azure viene eseguito nella rete virtuale di Azure.In this scenario, the Azure Application Gateway runs in your Azure virtual network. La rete virtuale è connessa al cloud privato tramite un circuito ExpressRoute.The virtual network is connected to your Private Cloud over an ExpressRoute circuit. Tutte le subnet nel cloud privato sono IP raggiungibili dalle subnet della rete virtuale.

![Servizio di bilanciamento del carico di Azure nella rete virtuale di AzureAzure load balancer in Azure virtual network](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>Come distribuire la soluzione

Il processo di distribuzione è costituito dalle attività seguenti:The deployment process consists of the following tasks:

1. [Verificare che i prerequisiti siano soddisfatti](#1-verify-prerequisites)
2. [Connettere la connessione virtuale di Azure al cloud privatoConnect your Azure virtual connection to the Private Cloud](#2-connect-your-azure-virtual-network-to-your-private-cloud)
3. [Distribuire un gateway applicazione di AzureDeploy an Azure application gateway](#3-deploy-an-azure-application-gateway)
4. [Creare e configurare il pool di macchine virtuali del server Web nel cloud privato](#4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud)

## <a name="1-verify-prerequisites"></a>1. Verificare i prerequisiti

Verificare che siano soddisfatti i prerequisiti seguenti:Verify that these prerequisites are met:

* È già stato creato un Azure Resource Manager e una rete virtuale.
* Una subnet dedicata (per il gateway applicazione) all'interno della rete virtuale di Azure è già stata creata.
* È già stato creato un cloud privato CloudSimple.
* Non vi è alcun conflitto IP tra le subnet IP nella rete virtuale e le subnet nel cloud privato.

## <a name="2-connect-your-azure-virtual-network-to-your-private-cloud"></a>2. Connettere la rete virtuale di Azure al cloud privato

Per connettere la rete virtuale di Azure al cloud privato, seguire questa procedura.

1. [Nel portale CloudSimple copiare le informazioni di peering ExpressRoute.](virtual-network-connection.md)

2. [Configurare un gateway di rete virtuale per la rete virtuale di Azure.Configure a virtual network gateway for your Azure virtual network](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).

3. [Collegare la rete virtuale al circuito CloudSimple ExpressRoute.](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)

4. Usare le informazioni di [peering copiate per collegare la rete virtuale al circuito ExpressRoute](virtual-network-connection.md).

## <a name="3-deploy-an-azure-application-gateway"></a>3. Distribuire un gateway applicazione di Azure

Le istruzioni dettagliate sono disponibili in Creare un gateway applicazione con regole di routing basate su percorso usando il portale di Azure.The detailed instructions for this are available in [Create an application gateway with path-based routing rules using the Azure portal](../application-gateway/create-url-route-portal.md). Di seguito è riportato un riepilogo dei passaggi necessari:

1. Creare una rete virtuale nella sottoscrizione e nel gruppo di risorse.
2. Creare una subnet (da utilizzare come subnet dedicata) all'interno della rete virtuale.
3. Creare un gateway applicazione standard (abilitare facoltativamente WAF): dalla home page del portale di Azure fare clic su**Gateway applicazione** **di rete** >  **risorse** > nella parte superiore sinistra della pagina. Selezionare lo SKU e le dimensioni standard e fornire informazioni sulla sottoscrizione, sul gruppo di risorse e sulla posizione di Azure.Select the standard SKU and size and provide Azure subscription, resource group and location information. Se necessario, creare un nuovo indirizzo IP pubblico per questo gateway applicazione e fornire informazioni dettagliate sulla rete virtuale e sulla subnet dedicata per il gateway applicazione.
4. Aggiungere un pool back-end con macchine virtuali e aggiungerlo al gateway applicazione.

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud"></a>4. Creare e configurare un pool di macchine virtuali del server Web nel cloud privato

In vCenter creare macchine virtuali con il sistema operativo e il server Web di propria scelta (ad esempio Windows/IIS o Linux/Apache). Scegliere una subnet/VLAN designata per il livello Web nel cloud privato. Verificare che almeno un vNIC delle macchine virtuali del server Web si trova nella subnet del livello Web.
