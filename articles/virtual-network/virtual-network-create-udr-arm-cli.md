---
title: Controllare il routing e i dispositivi virtuali usando l&quot;interfaccia della riga di comando di Azure 2.0 | Microsoft Docs
description: Informazioni su come controllare il routing e i dispositivi virtuali mediante l&quot;interfaccia della riga di comando di Azure 2.0.
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 5452a0b8-21a6-4699-8d6a-e2d8faf32c25
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2017
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 3a85fa624dc55f31822f00910b6d124c1d37323f
ms.lasthandoff: 03/22/2017


---
# <a name="create-user-defined-routes-udr-using-the-azure-cli-20"></a>Creare route definite dall'utente con l'interfaccia della riga di comando di Azure 2.0

> [!div class="op_single_selector"]
> * [PowerShell](virtual-network-create-udr-arm-ps.md)
> * [Interfaccia della riga di comando di Azure](virtual-network-create-udr-arm-cli.md)
> * [Modello](virtual-network-create-udr-arm-template.md)
> * [PowerShell (distribuzione classica)](virtual-network-create-udr-classic-ps.md)
> * [Interfaccia della riga di comando (distribuzione classica)](virtual-network-create-udr-classic-cli.md)

## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività 

È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando: 

- [Interfaccia della riga di comando di Azure 1.0](virtual-network-create-udr-arm-cli-nodejs.md): l'interfaccia della riga di comando per i modelli di distribuzione classici e di gestione delle risorse 
- [Interfaccia della riga di comando di Azure 2.0](#Create-the-UDR-for-the-front-end-subnet): interfaccia avanzata per il modello di distribuzione di gestione delle risorse (questo articolo)

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

I comandi di esempio dell'interfaccia della riga di comando di Azure riportati di seguito prevedono un ambiente semplice già creato in base allo scenario precedente. Se si desidera eseguire i comandi così come sono visualizzati in questo documento, creare innanzitutto l'ambiente di test distribuendo [questo modello](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before), fare clic su **Distribuisci in Azure**, sostituire i valori di parametro predefiniti, se necessario e seguire le istruzioni nel portale.


## <a name="create-the-udr-for-the-front-end-subnet"></a>Creare la route definita dall'utente per la subnet front-end
Per creare la tabella di route e la route necessarie per la subnet front-end in base allo scenario precedente, attenersi alla procedura seguente.

1. Creare una tabella di route per la subnet front-end con il comando [az network route-table create](/cli/azure/network/route-table#create):

    ```azurecli
    az network route-table create \
    --resource-group testrg \
    --location centralus \
    --name UDR-FrontEnd
    ```

    Output:

    ```json
    {
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd",
    "location": "centralus",
    "name": "UDR-FrontEnd",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "routes": [],
    "subnets": null,
    "tags": null,
    "type": "Microsoft.Network/routeTables"
    }
    ```

2. Creare una route che invia tutto il traffico destinato alla subnet back-end (192.168.2.0/24) alla VM **FW1** (192.168.0.4) usando il comando [az network route-table route create](/cli/azure/network/route-table/route#create):

    ```azurecli 
    az network route-table route create \
    --resource-group testrg \
    --name RouteToBackEnd \
    --route-table-name UDR-FrontEnd \
    --address-prefix 192.168.2.0/24 \
    --next-hop-type VirtualAppliance \
    --next-hop-ip-address 192.168.0.4
    ```

    Output:

    ```json
    {
    "addressPrefix": "192.168.2.0/24",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd/routes/RouteToBackEnd",
    "name": "RouteToBackEnd",
    "nextHopIpAddress": "192.168.0.4",
    "nextHopType": "VirtualAppliance",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg"
    }
    ```
    Parametri

    * **--route-table-name**. Nome della tabella di route in cui verrà aggiunta la route. Per questo scenario, *UDR-FrontEnd*.
    * **--address-prefix**. Prefisso di indirizzo della subnet alla quale sono destinati i pacchetti. Per questo scenario, *192.168.2.0/24*.
    * **--next-hop-type**. Tipo di oggetto al quale verrà inviato il traffico. I valori possibili sono *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet* o *None*.
    * **--next-hop-ip-address**. Indirizzo IP per l'hop successivo. Per questo scenario, *192.168.0.4*.

3. Eseguire il comando [az network vnet subnet update](/cli/azure/network/vnet/subnet#update) per associare la tabella di route creata in precedenza alla subnet **FrontEnd**:

    ```azurecli
    az network vnet subnet update \
    --resource-group testrg \
    --vnet-name testvnet \
    --name FrontEnd \
    --route-table UDR-FrontEnd
    ```

    Output:

    ```json
    {
    "addressPrefix": "192.168.1.0/24",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/FrontEnd",
    "ipConfigurations": null,
    "name": "FrontEnd",
    "networkSecurityGroup": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "resourceNavigationLinks": null,
    "routeTable": {
        "etag": null,
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd",
        "location": null,
        "name": null,
        "provisioningState": null,
        "resourceGroup": "testrg",
        "routes": null,
        "subnets": null,
        "tags": null,
        "type": null
        }
    }
    ```

    Parametri
    
    * **--vnet-name**. Nome della rete virtuale in cui si trova la subnet. Per questo scenario, *TestVNet*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>Creare la route definita dall'utente per la subnet back-end

Per creare la tabella di route e la route necessarie per la subnet back-end in base allo scenario precedente, seguire questa procedura:

1. Per creare una tabella di route per la subnet back-end, eseguire il comando seguente:

    ```azurecli
    az network route-table create \
    --resource-group testrg \
    --name UDR-BackEnd \
    --location centralus
    ```

2. Eseguire il comando seguente per creare una route nella tabella della route creata in precedenza per inviare tutto il traffico destinato alla subnet front-end (192.168.1.0/24) alla VM **FW1** (192.168.0.4):

    ```azurecli
    az network route-table route create \
    --resource-group testrg \
    --name RouteToFrontEnd \
    --route-table-name UDR-BackEnd \
    --address-prefix 192.168.1.0/24 \
    --next-hop-type VirtualAppliance \
    --next-hop-ip-address 192.168.0.4
    ```

3. Eseguire il comando seguente per associare la tabella di route creata in precedenza alla subnet **BackEnd**:

    ```azurecli
    az network vnet subnet update \
    --resource-group testrg \
    --vnet-name testvnet \
    --name BackEnd \
    --route-table UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-fw1"></a>Abilitare l'inoltro dell'indirizzo IP su FW1

Per abilitare l'inoltro dell'indirizzo IP nella scheda di interfaccia di rete usata da **FW1**, seguire questa procedura:

1. Eseguire il comando [az network nic show](/cli/az/network/nic#show) con un filtro JMESPATH per visualizzare il valore **enable-ip-forwarding** corrente per **Enable IP forwarding** (Abilita inoltro IP). Il valore deve essere impostato su *false*.

    ```azurecli
    az network nic show \
    --resource-group testrg \
    --nname nicfw1 \
    --query 'enableIpForwarding' -o tsv
    ```

    Output:

        false

2. Eseguire il comando seguente per abilitare l'inoltro dell'indirizzo IP:

    ```azurecli
    az network nic update \
    --resource-group testrg \
    --name nicfw1 \
    --ip-forwarding true
    ```

    È possibile esaminare l'output trasferito alla console o semplicemente ripetere il test per lo specifico valore **enableIpForwarding**:

    ```azurecli
    az network nic show -g testrg -n nicfw1 --query 'enableIpForwarding' -o tsv
    ```

    Output:

        true

    Parametri

    **--ip-forwarding**: *true* o *false*.


