---
title: Creare una rete virtuale usando il portale di Azure | Microsoft Docs
description: Informazioni su come creare una rete virtuale con il portale di Azure | Resource Manager
services: virtual-network
documentationcenter: 
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/8/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 6e96471c4f61e1ebe15c23f87ac646001d8e30ee
ms.openlocfilehash: d68f6a7e935f530630ee33f48cfad1b9e01e66a8


---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>Creare una rete virtuale usando il portale di Azure

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure offre due modelli di distribuzione, ovvero Azure Resource Manager e la distribuzione classica. Microsoft consiglia di creare le risorse tramite il modello di distribuzione Resource Manager. Per altre informazioni sulle differenze tra i due modelli, leggere l'articolo [Understand Azure deployment models](../resource-manager-deployment-model.md) (Informazioni sui modelli di distribuzione di Azure).
 
Questo articolo illustra come creare una rete virtuale tramite il modello di distribuzione Resource Manager usando il portale di Azure. È anche possibile creare una rete virtuale tramite Resource Manager usando altri strumenti oppure tramite il modello di distribuzione classica selezionando un'opzione diversa dall'elenco seguente:

> [!div class="op_single_selector"]
- [Portale](virtual-networks-create-vnet-arm-pportal.md)
- [PowerShell](virtual-networks-create-vnet-arm-ps.md)
- [CLI](virtual-networks-create-vnet-arm-cli.md)
- [Modello](virtual-networks-create-vnet-arm-template-click.md)
- [Portale (versione classica)](virtual-networks-create-vnet-classic-pportal.md)
- [PowerShell (versione classica)](virtual-networks-create-vnet-classic-netcfg-ps.md)
- [Interfaccia della riga di comando (versione classica)](virtual-networks-create-vnet-classic-cli.md)


[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>Crea rete virtuale

Per creare una rete virtuale usando il portale di Azure, completare i passaggi seguenti:

1. In un browser passare a http://portal.azure.com e, se necessario, accedere con l'account Azure.
2. Fare clic su **Nuovo** > **Rete** > **Rete virtuale**, come illustrato nell'immagine seguente:

    ![Nuova rete virtuale](./media/virtual-network-create-vnet-arm-pportal/1.png)

3. Nel pannello **Rete virtuale** visualizzato verificare che *Resource Manager* sia selezionato e fare clic su **Crea**, come illustrato nell'immagine seguente:

    ![Rete virtuale](./media/virtual-network-create-vnet-arm-pportal/2.png)
    
4. Nel pannello **Crea rete virtuale** visualizzato immettere *TestVNet* per **Nome**, *192.168.0.0/16* per **Spazio indirizzi**, *FrontEnd* per **Nome subnet** *192.168.1.0/24* per **Intervallo di indirizzi subnet**, *TestRG* per **Gruppo di risorse**, quindi selezionare **Sottoscrizione** e **Località** e infine fare clic sul pulsante **Crea**, come illustrato nell'immagine seguente:

    ![Creare una rete virtuale](./media/virtual-network-create-vnet-arm-pportal/3.png)

    In alternativa è possibile selezionare un gruppo di risorse esistente. Per altre informazioni sui gruppi di risorse, leggere l'articolo [Panoramica di Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups). È inoltre possibile selezionare una località diversa. Per altre informazioni sulle località e sulle aree geografiche di Azure, leggere l'articolo [Aree di Azure](https://azure.microsoft.com/regions).

5. Quando si crea una rete virtuale il portale consente di creare una sola subnet. Per questo scenario, dopo aver creato la rete virtuale è necessario creare una seconda subnet. A tale scopo, fare clic su **Tutte le risorse** e quindi su **TestVNet** nel pannello **Tutte le risorse**, come illustrato nell'immagine seguente:

    ![Rete virtuale creata](./media/virtual-network-create-vnet-arm-pportal/4.png)

6. Nel pannello **TestVNet** visualizzato fare clic su **Subnet** e quindi su **+Subnet**, immettere *BackEnd* per **Nome** e *192.168.2.0/24* per **Intervallo di indirizzi** nel pannello **Aggiungi subnet** e infine fare clic su **OK**, come illustrato nell'immagine seguente:

    ![Aggiungi subnet](./media/virtual-network-create-vnet-arm-pportal/5.png)

7. Sono elencate due subnet, come illustrato nell'immagine seguente:
    
    ![Elenco delle subnet nella rete virtuale](./media/virtual-network-create-vnet-arm-pportal/6.png)

Questo articolo ha illustrato come creare una rete virtuale con due subnet a scopo di test. Prima di creare una rete virtuale per l'uso in un ambiente di produzione, è consigliabile leggere gli articoli [Panoramica della rete virtuale](virtual-networks-overview.md) e [Pianificare e progettare reti virtuali](virtual-network-vnet-plan-design-arm.md) per informazioni approfondite sulle reti virtuali e su tutte le impostazioni. 

## <a name="next-steps"></a>Passaggi successivi

Leggere le informazioni su come connettere:

- Una macchina virtuale (VM) a una rete virtuale negli articoli [Creare una macchina virtuale Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) o [Creare una VM Linux](../virtual-machines/virtual-machines-linux-quick-create-portal.md). Anziché creare una rete virtuale e una subnet, come illustrato nelle procedure degli articoli, è possibile selezionare una rete virtuale e una subnet esistenti a cui connettere una VM.
- La rete virtuale ad altre reti virtuali nell'articolo [Connettere reti virtuali](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
- La rete virtuale a una rete locale tramite una rete privata virtuale (VPN) da sito a sito o il circuito ExpressRoute negli articoli [Connect a VNet to an on-premises network using a site-to-site VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) (Connettere una rete virtuale a una rete locale tramite una VPN da sito a sito) e [Collegare una rete virtuale a un circuito ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).


<!--HONumber=Nov16_HO3-->


