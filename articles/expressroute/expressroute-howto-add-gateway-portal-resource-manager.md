---
title: 'Aggiungere un gateway di rete virtuale a una rete virtuale per ExpressRoute: Portale: Azure | Documentazione Microsoft'
description: "Questo articolo illustra come aggiungere un gateway di rete virtuale a una rete virtuale di Resource Manager già creata per ExpressRoute."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 2bd0cf8be87937044ad515a2c6f253b1711bb2bf
ms.contentlocale: it-it
ms.lasthandoff: 04/18/2017

---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Configurare un gateway di rete virtuale per ExpressRoute usando il portale di Azure
> [!div class="op_single_selector"]
> * [Resource Manager - Portale di Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Classica: PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video - Portale di Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Questo articolo illustra le procedure per l'aggiunta di un gateway di rete virtuale per una rete virtuale già esistente. Questo articolo illustra le procedure per l'aggiunta, il ridimensionamento e la rimozione di un gateway di rete virtuale per una rete virtuale già esistente. I passaggi di questa configurazione sono specifici per le reti virtuali create usando il modello di distribuzione di Resource Manager che viene usato in una configurazione ExpressRoute. Per altre informazioni sui gateway di rete virtuale e sulle impostazioni di configurazione dei gateway per ExpressRoute, vedere [Informazioni sui gateway di rete virtuale per ExpressRoute](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Prima di iniziare

Nei passaggi di questa attività viene usata una rete virtuale basata sui valori indicati nell'elenco di riferimento per la configurazione riportato di seguito. Nelle procedure degli esempi viene usato questo elenco. È possibile copiare l'elenco per usarlo come riferimento, sostituendo i valori con quelli personalizzati.

**Elenco di riferimento per la configurazione**

* Nome rete virtuale = "TestVNet"
* Spazio degli indirizzi della rete virtuale = 192.168.0.0/16
* Nome subnet: "FrontEnd" 
    * Spazio degli indirizzi della subnet = "192.168.1.0/24"
* Gruppo di risorse = "TestRG"
* Località = "Stati Uniti orientali"
* Nome subnet del gateway: "GatewaySubnet" Il nome della subnet del gateway deve sempre essere *GatewaySubnet*.
    * Spazio degli indirizzi della subnet gateway = "192.168.200.0/26"
* Nome del gateway = "ERGW"
* Nome del gateway IP = "MyERGWVIP"
* Tipo di gateway = "ExpressRoute" Questo tipo è necessario per una configurazione ExpressRoute.
* Nome IP pubblico del gateway = "MyERGWVIP"

È possibile visualizzare un [video](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) relativo a questi passaggi prima di avviare la configurazione.

## <a name="create-the-gateway-subnet"></a>Creare la subnet del gateway

1. Nel [portale](http://portal.azure.com) passare alla rete virtuale di Resource Manager per cui si vuole creare un gateway di rete virtuale.
2. Nella sezione **Impostazioni** del pannello della rete virtuale fare clic su **Subnet** per espandere il pannello Subnet.
3. Nel pannello **Subnet** fare clic su **Subnet del gateway** per aprire il pannello **Aggiungi subnet**. 
   
    ![Aggiungere la subnet del gateway](./media/expressroute-howto-add-gateway-portal-resource-manager/addgwsubnet.png "Aggiungere la subnet del gateway")


4. Il **nome** della subnet verrà compilato automaticamente con il valore 'GatewaySubnet'. Questo valore è obbligatorio per consentire ad Azure di riconoscere la subnet come subnet del gateway. Modificare i valori di **Intervallo di indirizzi** compilati automaticamente in modo che corrispondano ai requisiti di configurazione. È consigliabile creare una subnet del gateway con un valore /27 o superiore (/26, /25 e così via). Fare clic su **OK** per salvare i valori e creare la subnet del gateway.

    ![Aggiunta della subnet](./media/expressroute-howto-add-gateway-portal-resource-manager/addsubnetgw.png "Aggiunta della subnet")

## <a name="create-the-virtual-network-gateway"></a>Creare il gateway di rete virtuale

1. Sul lato sinistro del portale fare clic su **+** e digitare "Gateway di rete virtuale" nella casella di ricerca. Individuare **Gateway di rete virtuale** nei risultati della ricerca e fare clic sulla voce. Nella parte inferiore del pannello **Gateway di rete virtuale** fare clic su **Crea**. Verrà aperto il pannello **Gateway di rete virtuale**.
2. Nel pannello **Crea gateway di rete virtuale** inserire i valori per il gateway di rete virtuale.

    ![Creare i campi nel pannello Gateway di rete virtuale](./media/expressroute-howto-add-gateway-portal-resource-manager/gw.png "Creare i campi nel pannello Gateway di rete virtuale")
3. **Nome**: assegnare un nome al gateway. Questa operazione non è come quella utilizzata per assegnare un nome alla subnet del gateway. Si tratta del nome dell'oggetto gateway che verrà creato.
4. **Tipo di gateway**: selezionare **ExpressRoute**.
5. **SKU**: selezionare lo SKU del gateway dall'elenco a discesa.
6. **Località**: modificare il campo **Località** in modo che faccia riferimento alla località in cui si trova la rete virtuale. Se la località non fa riferimento all'area in cui si trova la rete virtuale, quest'ultima non verrà visualizzata nell'elenco a discesa "Scegliere una rete virtuale".
7. Scegliere la rete virtuale a cui si vuole aggiungere il gateway. Fare clic su **Rete virtuale** per aprire il pannello **Scegliere una rete virtuale**. Selezionare la rete virtuale. Se la rete virtuale non viene visualizzata, verificare che il campo **Località** faccia riferimento all'area in cui si trova la rete virtuale.
9. Definire un indirizzo IP pubblico. Fare clic su **Indirizzo IP pubblico** per aprire il pannello **Scegli indirizzo IP pubblico**. Fare clic su **+Crea nuovo** per aprire il pannello **Crea indirizzo IP pubblico**. Immettere un nome per l'indirizzo IP pubblico. Il pannello crea un oggetto indirizzo IP pubblico a cui verrà assegnato dinamicamente un indirizzo IP pubblico. Fare clic su **OK** per salvare le modifiche al pannello.
10. **Sottoscrizione**: verificare che sia selezionata la sottoscrizione corretta.
11. **Gruppo di risorse**: questa impostazione è determinata dalla rete virtuale selezionata.
12. Non modificare il valore di **Località** dopo aver specificato le impostazioni precedenti.
13. Verificare le impostazioni. È possibile selezionare **Aggiungi al dashboard** nella parte inferiore del pannello se si vuole che il gateway venga visualizzato nel dashboard.
14. Fare clic su **Crea** per iniziare a creare il gateway. Le impostazioni vengono convalidate e il gateway viene distribuito. La creazione di un gateway di rete virtuale può richiedere fino a 45 minuti.

## <a name="next-steps"></a>Passaggi successivi
Dopo avere creato il gateway VNet, è possibile collegare la rete virtuale a un circuito ExpressRoute. Vedere [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md).
