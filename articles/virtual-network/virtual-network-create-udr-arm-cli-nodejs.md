---
title: Controllare il routing e i dispositivi virtuali usando l&quot;interfaccia della riga di comando di Azure 1.0 | Documentazione Microsoft
description: Informazioni su come controllare il routing e i dispositivi virtuali mediante l&quot;interfaccia della riga di comando di Azure 1.0.
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2017
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 5f21bc7a4fcd9507ea9d6b2b752a2328a7b834f0
ms.lasthandoff: 03/22/2017


---
# <a name="create-user-defined-routes-udr-using-the-azure-cli-10"></a>Creare route definite dall'utente con l'interfaccia della riga di comando di Azure 1.0

> [!div class="op_single_selector"]
> * [PowerShell](virtual-network-create-udr-arm-ps.md)
> * [Interfaccia della riga di comando di Azure](virtual-network-create-udr-arm-cli.md)
> * [Modello](virtual-network-create-udr-arm-template.md)
> * [PowerShell (versione classica)](virtual-network-create-udr-classic-ps.md)
> * [Interfaccia della riga di comando (versione classica)](virtual-network-create-udr-classic-cli.md)

Creare routing personalizzati e dispositivi virtuali mediante l'interfaccia della riga di comando di Azure.

## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività 

È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando: 

- [Interfaccia della riga di comando di Azure 1.0](#Create-the-UDR-for-the-front-end-subnet): l'interfaccia della riga di comando per i modelli di distribuzione classica e di gestione delle risorse (questo articolo)
- [Interfaccia della riga di comando di Azure 2.0](virtual-network-create-udr-arm-cli.md): interfaccia della riga di comando di prossima generazione per il modello di distribuzione di Gestione risorsa 


[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

I comandi di esempio dell'interfaccia della riga di comando di Azure riportati di seguito prevedono un ambiente semplice già creato in base allo scenario precedente. Se si desidera eseguire i comandi così come sono visualizzati in questo documento, creare innanzitutto l'ambiente di test distribuendo [questo modello](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before), fare clic su **Distribuisci in Azure**, sostituire i valori di parametro predefiniti, se necessario e seguire le istruzioni nel portale.


## <a name="create-the-udr-for-the-front-end-subnet"></a>Creare la route definita dall'utente per la subnet front-end
Per creare la tabella di route e la route necessarie per la subnet front-end in base allo scenario precedente, attenersi alla procedura seguente.

1. Eseguire il comando seguente per creare una tabella di route per la subnet front-end:

    ```azurecli
    azure network route-table create -g TestRG -n UDR-FrontEnd -l uswest
    ```
   
    Output:
   
        info:    Executing command network route-table create
        info:    Looking up route table "UDR-FrontEnd"
        info:    Creating route table "UDR-FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    Name                            : UDR-FrontEnd
        data:    Type                            : Microsoft.Network/routeTables
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        info:    network route-table create command OK
   
    Parametri
   
   * **-g (o --resource-group)**. Nome del gruppo di risorse in cui verrà creata la route definita dall'utente. Per questo scenario, *TestRG*.
   * **-l (o --location)**. Area di Azure in cui verrà creata la route definita dall'utente. Per questo scenario, *westus*.
   * **-n (o --nome)**. Nome della nuova route definita dall'utente. Per questo scenario, *UDR-FrontEnd*.
2. Eseguire il comando seguente per creare una route nella tabella della route creata in precedenza per inviare tutto il traffico destinato alla subnet back-end (192.168.2.0/24) alla VM **FW1** (192.168.0.4):

    ```azurecli
    azure network route-table route create -g TestRG -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -y VirtualAppliance -p 192.168.0.4
    ```
   
    Output:
   
        info:    Executing command network route-table route create
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        info:    Creating route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/[Subscription Id]/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd/routes/RouteToBackEnd
        data:    Name                            : RouteToBackEnd
        data:    Provisioning state              : Succeeded
        data:    Next hop type                   : VirtualAppliance
        data:    Next hop IP address             : 192.168.0.4
        data:    Address prefix                  : 192.168.2.0/24
        info:    network route-table route create command OK
   
    Parametri
   
   * **-r (o --route-table-name)**. Nome della tabella di route in cui verrà aggiunta la route. Per questo scenario, *UDR-FrontEnd*.
   * **-a (o --address-prefix)**. Prefisso di indirizzo della subnet alla quale sono destinati i pacchetti. Per questo scenario, *192.168.2.0/24*.
   * **-y (o --next-hop-type)**. Tipo di oggetto al quale verrà inviato il traffico. I valori possibili sono *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet* o *None*.
   * **-p (o --next-hop-ip-address**). Indirizzo IP per l'hop successivo. Per questo scenario, *192.168.0.4*.
3. Eseguire il comando seguente per associare la tabella di route creata in precedenza alla subnet **FrontEnd**:

    ```azurecli
    azure network vnet subnet set -g TestRG -e TestVNet -n FrontEnd -r UDR-FrontEnd
    ```
   
    Output:
   
        info:    Executing command network vnet subnet set
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        info:    Setting subnet "FrontEnd"
        info:    Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:    Network security group          : [object Object]
        data:    Route Table                     : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    IP configurations:
        data:      /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConf
        igurations/ipconfig1
        data:      /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConf
        igurations/ipconfig1
        data:    
        info:    network vnet subnet set command OK
   
    Parametri
   
   * **-e (o --vnet-name)**. Nome della rete virtuale in cui si trova la subnet. Per questo scenario, *TestVNet*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>Creare la route definita dall'utente per la subnet back-end
Per creare la tabella di route e la route necessarie per la subnet back-end in base allo scenario precedente, seguire questa procedura:

1. Per creare una tabella di route per la subnet back-end, eseguire il comando seguente:

    ```azurecli
    azure network route-table create -g TestRG -n UDR-BackEnd -l westus
    ```

2. Eseguire il comando seguente per creare una route nella tabella della route creata in precedenza per inviare tutto il traffico destinato alla subnet front-end (192.168.1.0/24) alla VM **FW1** (192.168.0.4):

    ```azurecli
    azure network route-table route create -g TestRG -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -y VirtualAppliance -p 192.168.0.4
    ```

3. Eseguire il comando seguente per associare la tabella di route creata in precedenza alla subnet **BackEnd**:

    ```azurecli
    azure network vnet subnet set -g TestRG -e TestVNet -n BackEnd -r UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-fw1"></a>Abilitare l'inoltro dell'indirizzo IP su FW1
Per abilitare l'inoltro dell'indirizzo IP nella scheda di interfaccia di rete usata da **FW1**, seguire questa procedura:

1. Eseguire il comando seguente e notare il valore per **Abilita inoltro dell'indirizzo IP**. Il valore deve essere impostato su *false*.

    ```azurecli
    azure network nic show -g TestRG -n NICFW1
    ```

    Output:
   
        info:    Executing command network nic show
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : false
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic show command OK
2. Eseguire il comando seguente per abilitare l'inoltro dell'indirizzo IP:

    ```azurecli
    azure network nic set -g TestRG -n NICFW1 -f true
    ```
   
    Output:
   
        info:    Executing command network nic set
        info:    Looking up the network interface "NICFW1"
        info:    Updating network interface "NICFW1"
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : true
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic set command OK
   
    Parametri
   
   * **-f (o --enable-ip-forwarding)**. *true* o *false*.


