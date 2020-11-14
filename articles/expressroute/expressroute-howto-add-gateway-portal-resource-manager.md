---
title: 'Esercitazione: Azure ExpressRoute - Aggiungere un gateway a una rete virtuale (portale di Azure)'
description: Questa esercitazione illustra come aggiungere un gateway di rete virtuale a una rete virtuale per ExpressRoute tramite il portale di Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/05/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 843d0b8cfd75e8cbdf45ac535cc9486aa42442d6
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "91761802"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Esercitazione: Configurare un gateway di rete virtuale per ExpressRoute usando il portale di Azure
> [!div class="op_single_selector"]
> * [Resource Manager - Portale di Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Classica: PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video - Portale di Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

Questa esercitazione illustra le procedure per aggiungere un gateway di rete virtuale a una rete virtuale preesistente. Questo articolo illustra le procedure per l'aggiunta, il ridimensionamento e la rimozione di un gateway di rete virtuale per una rete virtuale già esistente. I passaggi di questa configurazione sono specifici per le reti virtuali create usando il modello di distribuzione di Resource Manager che viene usato in una configurazione ExpressRoute. Per altre informazioni sui gateway di rete virtuale e sulle impostazioni di configurazione dei gateway per ExpressRoute, vedere [Informazioni sui gateway di rete virtuale per ExpressRoute](expressroute-about-virtual-network-gateways.md). 

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> - Creare una subnet del gateway.
> - Creare il gateway di rete virtuale.

## <a name="prerequisites"></a>Prerequisiti

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
* Nome IP pubblico del gateway = "MyERGWVIP"
* Tipo di gateway = "ExpressRoute" Questo tipo è necessario per una configurazione ExpressRoute.

È possibile visualizzare un [video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) relativo a questi passaggi prima di avviare la configurazione.

## <a name="create-the-gateway-subnet"></a>Creare la subnet del gateway

1. Nel [portale](https://portal.azure.com) passare alla rete virtuale di Resource Manager per cui si vuole creare un gateway di rete virtuale.
1. Nella sezione **Impostazioni** della rete virtuale selezionare **Subnet** per espandere le impostazioni della subnet.
1. Nelle impostazioni di **Subnet** selezionare **+ Subnet gateway** per aggiungere una subnet del gateway. 
   
    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-gateway-subnet.png" alt-text="Aggiungere la subnet del gateway":::

1. Il **nome** della subnet verrà compilato automaticamente con il valore 'GatewaySubnet'. Questo valore è obbligatorio per consentire ad Azure di riconoscere la subnet come subnet del gateway. Modificare i valori di **Intervallo di indirizzi** in modo che corrispondano ai requisiti di configurazione. È consigliabile creare una subnet del gateway con un valore /27 o superiore (/26, /25 e così via). Quindi selezionare **OK** per salvare i valori e creare la subnet del gateway.

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-subnet-gateway.png" alt-text="Aggiunta della subnet":::

## <a name="create-the-virtual-network-gateway"></a>Creare il gateway di rete virtuale

1. Sul lato sinistro del portale fare clic su **Crea una risorsa** e digitare 'gateway di rete virtuale' nella casella di ricerca. Individuare **Gateway di rete virtuale** nei risultati della ricerca e selezionare la voce. Nella pagina **Gateway di rete virtuale** selezionare **Crea**.
1. Nella pagina **Crea gateway di rete virtuale** immettere o selezionare queste impostazioni:

    | Impostazione | Valore |
    | --------| ----- |
    | Subscription | verificare che sia selezionata la sottoscrizione corretta. |
    | Gruppo di risorse | Il gruppo di risorse verrà scelto automaticamente dopo aver selezionato la rete virtuale. | 
    | Nome | Assegnare un nome al gateway. Non è il nome della subnet del gateway, ma il nome dell'oggetto gateway da creare.|
    | Region | Cambiare il campo **Area** in modo che faccia riferimento alla località in cui si trova la rete virtuale. Se questa impostazione non punta all'area in cui si trova la rete virtuale, quest'ultima non comparirà nell'elenco a discesa 'Scegliere una rete virtuale'. |
    | Tipo gateway | Selezionare **ExpressRoute**|
    | SKU | selezionare lo SKU del gateway dall'elenco a discesa. |
    | Rete virtuale | Selezionare *TestVNet*. |
    | Indirizzo IP pubblico | Selezionare **Crea nuovo**.|
    | Nome dell'indirizzo IP pubblico | Specificare un nome per l'indirizzo IP pubblico. |

1. Selezionare **Rivedi e crea** e quindi **Crea** per avviare la creazione del gateway. Le impostazioni vengono convalidate e il gateway viene distribuito. La creazione di un gateway di rete virtuale può richiedere fino a 45 minuti.

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/gateway.png" alt-text="Campi della pagina Crea gateway di rete virtuale":::

## <a name="clean-up-resources"></a>Pulire le risorse

Se il gateway ExpressRoute non è più necessario, individuarlo nel gruppo di risorse della rete virtuale e selezionare **Elimina**. Verificare che il gateway non abbia connessioni con un circuito.

:::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/delete-gateway.png" alt-text="Eliminare il gateway della rete virtuale":::

## <a name="next-steps"></a>Passaggi successivi
Dopo avere creato il gateway della rete virtuale, è possibile collegare la rete virtuale a un circuito ExpressRoute. 

> [!div class="nextstepaction"]
> [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
