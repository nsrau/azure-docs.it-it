---
title: Controllare il routing in una rete virtuale di Azure - PowerShell - Modello di distribuzione classica | Documentazione Microsoft
description: Informazioni su come controllare il routing in reti virtuali usando PowerShell | Classico
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: d8d07c16-cbe5-4536-acd6-870269346fe3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: e9564d223cb85529f1fa97bc398d35c6debcedae
ms.lasthandoff: 03/22/2017


---
# <a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>Controllare il routing e usare dispositivi virtuali di rete (distribuzione classica) mediante PowerShell

> [!div class="op_single_selector"]
> * [PowerShell](virtual-network-create-udr-arm-ps.md)
> * [Interfaccia della riga di comando di Azure](virtual-network-create-udr-arm-cli.md)
> * [Modello](virtual-network-create-udr-arm-template.md)
> * [PowerShell (versione classica)](virtual-network-create-udr-classic-ps.md)
> * [Interfaccia della riga di comando (versione classica)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

> [!IMPORTANT]
> Prima di iniziare a usare le risorse di Azure, è importante comprendere che Azure al momento offre due modelli di distribuzione, la distribuzione classica e Azure Resource Manager. È importante comprendere i [modelli e strumenti di distribuzione](../azure-resource-manager/resource-manager-deployment-model.md) prima di lavorare con le risorse di Azure. È possibile visualizzare la documentazione relativa ai diversi strumenti selezionando un'opzione nella parte superiore di questo articolo. In questo articolo viene illustrato il modello di distribuzione classica.
> 

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

I comandi di esempio di Azure PowerShell riportati di seguito prevedono un ambiente semplice già creato in base allo scenario precedente. Se si desidera eseguire i comandi illustrati in questo documento, creare l'ambiente descritto nella pagina relativa alla [creazione di una rete virtuale (classica) mediante PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Creare la route definita dall'utente per la subnet front-end
Per creare la tabella di route e la route necessarie per la subnet front-end in base allo scenario precedente, attenersi alla procedura seguente.

1. Eseguire il comando seguente per creare una tabella di route per la subnet front-end:

    ```powershell
    New-AzureRouteTable -Name UDR-FrontEnd -Location uswest `
    -Label "Route table for front end subnet"
    ```

2. Eseguire il comando seguente per creare una route nella tabella della route creata in precedenza per inviare tutto il traffico destinato alla subnet back-end (192.168.2.0/24) alla VM **FW1** (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-FrontEnd `
    |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Eseguire il comando seguente per associare la tabella di route creata in precedenza alla subnet **FrontEnd**:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName FrontEnd `
    -RouteTableName UDR-FrontEnd
    ```

## <a name="create-the-udr-for-the-back-end-subnet"></a>Creare la route definita dall'utente per la subnet back-end
Per creare la tabella di route e la route necessarie per la subnet back-end in base allo scenario precedente, seguire questa procedura:

1. Per creare una tabella di route per la subnet back-end, eseguire il comando seguente:

    ```powershell
    New-AzureRouteTable -Name UDR-BackEnd `
    -Location uswest `
    -Label "Route table for back end subnet"
    ```

2. Eseguire il comando seguente per creare una route nella tabella della route creata in precedenza per inviare tutto il traffico destinato alla subnet front-end (192.168.1.0/24) alla VM **FW1** (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-BackEnd
    | Set-AzureRoute `
    -RouteName RouteToFrontEnd `
    -AddressPrefix 192.168.1.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Eseguire il comando seguente per associare la tabella di route creata in precedenza alla subnet **BackEnd**:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName BackEnd `
    -RouteTableName UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>Abilitare l'inoltro dell'indirizzo IP sulla VM FW1

Per abilitare l'inoltro dell'indirizzo IP nella macchina virtuale FW1, seguire questa procedura:

1. Per verificare lo stato dell'inoltro dell'indirizzo IP, eseguire il comando seguente:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Get-AzureIPForwarding
    ```

2. Per abilitare l'inoltro dell'indirizzo IP per la macchina virtuale *FW1*, eseguire il comando seguente:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Set-AzureIPForwarding -Enable
    ```

