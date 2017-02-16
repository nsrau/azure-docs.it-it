---
title: Controllare il routing in una rete virtuale di Azure - Interfaccia della riga di comando - Modello di distribuzione classica | Documentazione Microsoft
description: Informazioni su come controllare il routing in reti virtuali mediante l&quot;interfaccia della riga di comando di Azure nel modello di distribuzione classica
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: ca2b4638-8777-4d30-b972-eb790a7c804f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 71153e46d1695611c8286d2e57b109db9e7ad9df
ms.openlocfilehash: 4a610a65b33f91f46d011e07c6cb11523d3dae0d


---
# <a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Controllare il routing e usare dispositivi virtuali di rete (distribuzione classica) mediante l'interfaccia della riga di comando di Azure

> [!div class="op_single_selector"]
- [PowerShell](virtual-network-create-udr-arm-ps.md)
- [Interfaccia della riga di comando di Azure](virtual-network-create-udr-arm-cli.md)
- [Modello](virtual-network-create-udr-arm-template.md)
- [PowerShell (versione classica)](virtual-network-create-udr-classic-ps.md)
- [Interfaccia della riga di comando (versione classica)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

In questo articolo viene illustrato il modello di distribuzione classica. È possibile anche [controllare il routing e usare dispositivi virtuali di rete nel modello di distribuzione di Gestione risorse](virtual-network-create-udr-arm-cli.md).

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

I comandi di esempio dell'interfaccia della riga di comando di Azure riportati di seguito prevedono un ambiente semplice già creato in base allo scenario precedente. Se si desidera eseguire i comandi illustrati in questo documento, creare l'ambiente descritto nella pagina relativa alla [creazione di una rete virtuale (classica) mediante l'interfaccia della riga di comando di Azure](virtual-networks-create-vnet-classic-cli.md).

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Creare la route definita dall'utente per la subnet front-end
Per creare la tabella di route e la route necessarie per la subnet front-end in base allo scenario precedente, attenersi alla procedura seguente.

1. Usare il comando seguente per passare alla modalità classica:

    ```azurecli
    azure config mode asm
    ```

    Output:

        info:    New mode is asm

2. Eseguire il comando seguente per creare una tabella di route per la subnet front-end:

    ```azurecli
    azure network route-table create -n UDR-FrontEnd -l uswest
    ```
   
    Output:
   
        info:    Executing command network route-table create
        info:    Creating route table "UDR-FrontEnd"
        info:    Getting route table "UDR-FrontEnd"
        data:    Name                            : UDR-FrontEnd
        data:    Location                        : West US
        info:    network route-table create command OK
   
    Parametri
   
   * **-l (o --location)**. Area di Azure in cui verrà creato il nuovo gruppo di sicurezza di rete. Per questo scenario, *westus*.
   * **-n (o --name)**. Nome per il nuovo gruppo di sicurezza di rete. Per questo scenario, *NSG-FrontEnd*.
3. Eseguire il comando seguente per creare una route nella tabella della route creata in precedenza per inviare tutto il traffico destinato alla subnet back-end (192.168.2.0/24) alla VM **FW1** (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

    Output:
   
        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK
   
    Parametri
   
   * **-r (o --route-table-name)**. Nome della tabella di route in cui verrà aggiunta la route. Per questo scenario, *UDR-FrontEnd*.
   * **-a (o --address-prefix)**. Prefisso di indirizzo della subnet alla quale sono destinati i pacchetti. Per questo scenario, *192.168.2.0/24*.
   * **-t (o --next-hop-type)**. Tipo di oggetto al quale verrà inviato il traffico. I valori possibili sono *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet* o *None*.
   * **-p (o --next-hop-ip-address**). Indirizzo IP per l'hop successivo. Per questo scenario, *192.168.0.4*.
4. Eseguire il comando seguente per associare la tabella di route creata con la subnet **FrontEnd**:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd
    ```
   
    Output:
   
        info:    Executing command network vnet subnet route-table add
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        data:    Route table name                : UDR-FrontEnd
        data:      Location                      : West US
        data:      Routes:
        info:    network vnet subnet route-table add command OK    
   
    Parametri
   
   * **-t (o --vnet-name)**. Nome della rete virtuale in cui si trova la subnet. Per questo scenario, *TestVNet*.
   * **-n (o --subnet-name**. Nome della subnet in cui verrà aggiunta la tabella di route. Per questo scenario, *FrontEnd*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>Creare la route definita dall'utente per la subnet back-end
Per creare la tabella di route e la route necessarie per la subnet back-end in base allo scenario precedente, attenersi alla procedura seguente:

1. Eseguire il comando seguente per creare una tabella di route per la subnet back-end:

    ```azurecli
    azure network route-table create -n UDR-BackEnd -l uswest
    ```

2. Eseguire il comando seguente per creare una route nella tabella della route creata in precedenza per inviare tutto il traffico destinato alla subnet front-end (192.168.1.0/24) alla VM **FW1** (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

3. Eseguire il comando seguente per associare la tabella di route creata in precedenza alla subnet **BackEnd**:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd
    ```




<!--HONumber=Feb17_HO1-->


