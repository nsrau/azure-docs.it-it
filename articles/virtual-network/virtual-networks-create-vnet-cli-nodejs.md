---
title: Creare una rete virtuale usando l'interfaccia della riga di comando 1.0 di Azure | Documentazione Microsoft
description: Informazioni su come creare una rete virtuale usando l'interfaccia della riga di comando 1.0 di Azure | Resource Manager.
services: virtual-network
documentationcenter: 
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: jdial
ms.openlocfilehash: f0649c5c8c04dda72d2f147601efb37217f9bade
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Creare una rete virtuale usando l'interfaccia della riga di comando di Azure

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure offre due modelli di distribuzione, ovvero Azure Resource Manager e la distribuzione classica. Microsoft consiglia di creare le risorse tramite il modello di distribuzione Resource Manager. Per altre informazioni sulle differenze tra i due modelli, leggere l'articolo [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md) (Informazioni sui modelli di distribuzione di Azure).

## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività
È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

- [Interfaccia della riga di comando di Azure 2.0](virtual-networks-create-vnet-arm-cli.md): interfaccia avanzata per il modello di distribuzione di gestione delle risorse
- [Interfaccia della riga di comando di Azure 1.0](#create-a-virtual-network): l'interfaccia della riga di comando per i modelli di distribuzione classica e di gestione delle risorse (questo articolo)

 
[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>Crea rete virtuale

Per creare una rete virtuale usando l'interfaccia della riga di comando di Azure, seguire questa procedura:

1. Per installare e configurare l'interfaccia della riga di comando di Azure, seguire la procedura riportata nell'articolo [Installare e configurare l'interfaccia della riga di comando di Azure](../cli-install-nodejs.md).

2. Creare una rete virtuale e subnet:

    ```azurecli
    azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
    ```

    Output previsto:
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK

    Parametri utilizzati:

   * **--vnet**. Nome della rete virtuale da creare. Per questo scenario, *TestVNet*
   * **-e (o --address-space)**. Spazio degli indirizzi della rete virtuale. Per questo scenario, *192.168.0.0*
   * **-i (o -cidr)**. Maschera di rete nel formato CIDR. Per questo scenario, *16*
   * **- n (o --subnet-name**). Nome della prima subnet. Per questo scenario, *FrontEnd*.
   * **-p (or --subnet-start-ip)**. Indirizzo IP iniziale per la subnet o spazio di indirizzi della subnet. Per questo scenario, *192.168.1.0*
   * **-r (o --subnet-cidr)**. Maschera di rete nel formato CIDR per la subnet. Per questo scenario, *24*
   * **-l (o --location)**. Area di Azure in cui verrà creata la rete virtuale. Per questo scenario, *Central US*.

3. Creare una subnet:

    ```azurecli
    azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
    ```
   
    Output previsto:

            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK

    Parametri utilizzati:

   * **-t (o --vnet-name**. Nome della rete virtuale in cui verrà creata la subnet. Per questo scenario, *TestVNet*.
   * **-n (o --name)**. Nome della nuova subnet. Per questo scenario, *BackEnd*.
   * **-a (o --address-prefix)**. Blocco CIDR della subnet. Per questo scenario, *192.168.2.0/24*.
   
4. Per visualizzare le proprietà della nuova rete virtuale:

    ```azurecli
    azure network vnet show
    ```
   
    Output previsto:
   
            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

## <a name="next-steps"></a>Passaggi successivi

Leggere le informazioni su come connettere:

- Una macchina virtuale (VM) a una rete virtuale nell'articolo [Creare una VM Linux](../virtual-machines/linux/quick-create-cli.md). Anziché creare una rete virtuale e una subnet, come illustrato nelle procedure degli articoli, è possibile selezionare una rete virtuale e una subnet esistenti a cui connettere una VM.
- La rete virtuale ad altre reti virtuali nell'articolo [Connettere reti virtuali](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
- La rete virtuale a una rete locale tramite una rete privata virtuale (VPN) da sito a sito o il circuito ExpressRoute Per le informazioni su come procedere, leggere [Connect a VNet to an on-premises network using a site-to-site VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) (Connettere una rete virtuale a una rete locale tramite una VPN da sito a sito) e [Collegare una rete virtuale a un circuito ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).