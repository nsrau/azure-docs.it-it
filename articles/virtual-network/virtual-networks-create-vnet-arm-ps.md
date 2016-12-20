---
title: Creare una rete virtuale usando PowerShell | Documentazione Microsoft
description: Informazioni su come creare una rete virtuale usando PowerShell | Resource Manager.
services: virtual-network
documentationcenter: 
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: a31f4f12-54ee-4339-b968-1a8097ca77d3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 3d854b5073909f30a1bf59e0986c7f0f926ee21c
ms.openlocfilehash: 2ce6f0d280609dc62063f87d2ab17256b3ff67c4


---
# <a name="create-a-virtual-network-using-powershell"></a>Creare una rete virtuale usando PowerShell

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure offre due modelli di distribuzione, ovvero Azure Resource Manager e la distribuzione classica. Microsoft consiglia di creare le risorse tramite il modello di distribuzione Resource Manager. Per altre informazioni sulle differenze tra i due modelli, leggere l'articolo [Understand Azure deployment models](../resource-manager-deployment-model.md) (Informazioni sui modelli di distribuzione di Azure).
 
Questo articolo illustra come creare una rete virtuale tramite il modello di distribuzione Resource Manager usando PowerShell. È anche possibile creare una rete virtuale tramite Resource Manager usando altri strumenti oppure tramite il modello di distribuzione classica selezionando un'opzione diversa dall'elenco seguente:

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

Per creare una rete virtuale usando PowerShell, completare i passaggi seguenti:

1. Installare e configurare Azure PowerShell seguendo i passaggi descritti nell'articolo [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

2. Se necessario, creare un nuovo gruppo di risorse, come mostrato di seguito. Per questo scenario, creare un gruppo di risorse denominato *TestRG*. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

    ```powershell   
    New-AzureRmResourceGroup -Name TestRG -Location centralus
    ```

    Output previsto:

        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/[Subscription Id]/resourceGroups/TestRG    
3. Creare una nuova rete virtuale denominata *TestVNet*:

    ```powershell
    New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
    -AddressPrefix 192.168.0.0/16 -Location centralus
    ```

    Output previsto:

        Name                  : TestVNet
        ResourceGroupName     : TestRG
        Location              : centralus
        Id                    : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                   : W/"[Id]"
        ProvisioningState          : Succeeded
        Tags                       : 
        AddressSpace               : {
                                   "AddressPrefixes": [
                                     "192.168.0.0/16"
                                   ]
                                  }
        DhcpOptions                : {}
        Subnets                    : []
        VirtualNetworkPeerings     : []
4. Archiviare l'oggetto di rete virtuale in una variabile:

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    ```

   > [!TIP]
   > È possibile combinare i passaggi 3 e 4 eseguendo `$vnet = New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet -AddressPrefix 192.168.0.0/16 -Location centralus`.
   > 

5. Aggiungere una subnet alla nuova variabile di rete virtuale:

    ```powershell
    Add-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd `
    -VirtualNetwork $vnet -AddressPrefix 192.168.1.0/24
    ```

    Output previsto:
   
        Name                  : TestVNet
        ResourceGroupName     : TestRG
        Location              : centralus
        Id                    : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                  : W/"[Id]"
        ProvisioningState     : Succeeded
        Tags                  :
        AddressSpace          : {
                                  "AddressPrefixes": [
                                    "192.168.0.0/16"
                                  ]
                                }
        DhcpOptions           : {}
        Subnets             : [
                                  {
                                    "Name": "FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24"
                                  }
                                ]
        VirtualNetworkPeerings     : []

6. Ripetere il passaggio 5 riportato in precedenza per ogni subnet che si desidera creare. Il comando seguente crea la subnet *BackEnd* per lo scenario:

    ```powershell
    Add-AzureRmVirtualNetworkSubnetConfig -Name BackEnd `
    -VirtualNetwork $vnet -AddressPrefix 192.168.2.0/24
    ```

7. Quando si crea una subnet, questa rimane solo nella variabile locale usata per recuperare la rete virtuale creata nel passaggio 4. Per salvare le modifiche in Azure, eseguire il comando seguente:

    ```powershell
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```
   
    Output previsto:
   
        Name                  : TestVNet
        ResourceGroupName     : TestRG
        Location              : centralus
        Id                    : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                  : W/"[Id]"
        ProvisioningState     : Succeeded
        Tags                  :
        AddressSpace          : {
                                  "AddressPrefixes": [
                                    "192.168.0.0/16"
                                  ]
                                }
        DhcpOptions           : {
                                  "DnsServers": []
                                }
        Subnets               : [
                                  {
                                    "Name": "FrontEnd",
                                    "Etag": "W/\"[Id]\"",
                                    "Id": "/subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24",
                                    "IpConfigurations": [],
                                    "ProvisioningState": "Succeeded"
                                  },
                                  {
                                    "Name": "BackEnd",
                                    "Etag": "W/\"[Id]\"",
                                    "Id": "/subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                    "AddressPrefix": "192.168.2.0/24",
                                    "IpConfigurations": [],
                                    "ProvisioningState": "Succeeded"
                                  }
                                ]
        VirtualNetworkPeerings : []

## <a name="next-steps"></a>Passaggi successivi

Leggere le informazioni su come connettere:

- Una macchina virtuale (VM) a una rete virtuale nell'articolo [Creare una VM Windows](../virtual-machines/virtual-machines-windows-ps-create.md). Anziché creare una rete virtuale e una subnet, come illustrato nelle procedure degli articoli, è possibile selezionare una rete virtuale e una subnet esistenti a cui connettere una VM.
- La rete virtuale ad altre reti virtuali nell'articolo [Connettere reti virtuali](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).
- La rete virtuale a una rete locale tramite una rete privata virtuale (VPN) da sito a sito o il circuito ExpressRoute negli articoli [Connect a VNet to an on-premises network using a site-to-site VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) (Connettere una rete virtuale a una rete locale tramite una VPN da sito a sito) e [Collegare una rete virtuale a un circuito ExpressRoute](../expressroute/expressroute-howto-linkvnet-arm.md).


<!--HONumber=Nov16_HO3-->


