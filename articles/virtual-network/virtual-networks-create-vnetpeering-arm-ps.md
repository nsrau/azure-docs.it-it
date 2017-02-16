---
title: Creare un peering reti virtuali usando i cmdlet di PowerShell | Microsoft Docs
description: Informazioni su come creare una rete virtuale con il portale di Azure in Resource Manager.
services: virtual-network
documentationcenter: 
author: NarayanAnnamalai
manager: jefco
editor: 
tags: azure-resource-manager
ms.assetid: dac579bd-7545-461a-bdac-301c87434c84
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: narayan; annahar
translationtype: Human Translation
ms.sourcegitcommit: 3fe204c09eebf7d254a1bf2bb130e2d3498b6b45
ms.openlocfilehash: c2ccb107cbfec16440d5ff5ffe43a9009b5c4e48


---
# <a name="create-vnet-peering-using-powershell-cmdlets"></a>Creare un peering reti virtuali usando i cmdlet di PowerShell
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Per creare un peering reti virtuali con PowerShell, seguire questa procedura:

1. Se è la prima volta che si utilizza Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs) e seguire le istruzioni fino al termine della procedura per accedere ad Azure e selezionare la sottoscrizione desiderata.

    > [!NOTE]
    > Il cmdlet di PowerShell per la gestione di un peering reti virtuali viene fornito con [Azure PowerShell 1.6.](http://www.powershellgallery.com/packages/Azure/1.6.0)
    >

1. Leggere gli oggetti di rete virtuale:
   
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
        $vnet2 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet2
2. Per stabilire un peering reti virtuali, è necessario creare due collegamenti, uno per ogni direzione. Nel passaggio seguente si creerà un collegamento per il peering reti virtuali prima da VNet1 a VNet2:
   
        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $vnet2.Id
   
    Ecco l'output:
   
        Name            : LinkToVNet2
        Id: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Initiated
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic    : False
        AllowGatewayTransit    : False
        UseRemoteGateways    : False
        RemoteGateways        : null
        RemoteVirtualNetworkAddressSpace : null
3. In questo passaggio si creerà un collegamento per il peering reti virtuali da VNet2 a VNet1:
   
        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $vnet2 -RemoteVirtualNetworkId $vnet1.Id
   
    Ecco l'output:
   
        Name            : LinkToVNet1
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2/virtualNetworkPeerings/LinkToVNet1
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet2
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic    : False
        AllowGatewayTransit    : False
        UseRemoteGateways    : False
        RemoteGateways        : null
        RemoteVirtualNetworkAddressSpace : null
4. Dopo aver creato il collegamento per il peering reti virtuali, è possibile visualizzare lo stato del collegamento come segue:
   
        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name linktovnet2
   
    Ecco l'output:
   
        Name            : LinkToVNet2
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null
   
    Esistono alcune proprietà configurabili per il peering reti virtuali:
   
   | Opzione | Descrizione | Default |
   |:--- |:--- |:--- |
   | AllowVirtualNetworkAccess |Indica se lo spazio indirizzi della rete virtuale peer deve essere incluso come parte del tag Virtual_network. |Sì |
   | AllowForwardedTraffic |Consente di accettare o eliminare il traffico che non ha origine da una rete virtuale con peering. |No |
   | AllowGatewayTransit |Consente alla rete virtuale peer di usare il gateway di rete virtuale. |No |
   | UseRemoteGateways |Consente di usare il gateway di rete virtuale del peer. Per la rete virtuale peer deve essere configurato un gateway ed essere selezionata l'opzione AllowGatewayTransit. Non è possibile usare questa opzione se si ha un gateway configurato. |No |
   
    Ogni collegamento nel peering reti virtuali include un set delle proprietà precedenti. Ad esempio, è possibile impostare AllowVirtualNetworkAccess su True per il collegamento per il peering reti virtuali da VNet1 a VNet2 e su False per il collegamento per il peering reti virtuali nella direzione opposta.
   
        $LinktoVNet2 = Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name LinkToVNet2
        $LinktoVNet2.AllowForwardedTraffic = $true
        Set-AzureRmVirtualNetworkPeering -VirtualNetworkPeering $LinktoVNet2
   
    È possibile eseguire Get-AzureRmVirtualNetworkPeering per controllare il valore della proprietà dopo la modifica. Dall'output è possibile vedere che AllowForwardedTraffic viene impostato su True dopo l'esecuzione del cmdlet precedente.
   
        Name            : LinkToVNet2
        Id            : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic        : True
        AllowGatewayTransit        : False
        UseRemoteGateways        : False
        RemoteGateways        : null
        RemoteVirtualNetworkAddressSpace : null
   
    Dopo aver stabilito il peering in questo scenario, sarà possibile avviare le connessioni da qualsiasi macchina virtuale a qualsiasi macchina virtuale di entrambe le reti virtuali. Per impostazione predefinita, AllowVirtualNetworkAccess è impostato su True e il peering reti virtuali effettuerà il provisioning degli ACL appropriati per consentire la comunicazione tra le reti virtuali. È comunque possibile applicare le regole del gruppo di sicurezza di rete per bloccare la connettività tra subnet o macchine virtuali specifiche per ottenere il controllo granulare dell'accesso tra due reti virtuali.  Per altre informazioni sulla creazione di regole del gruppo di sicurezza di rete, vedere questo [articolo](virtual-networks-create-nsg-arm-ps.md).

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Per creare un peering reti virtuali tra sottoscrizioni con PowerShell, seguire questa procedura:

1. Accedere ad Azure con l'account utente A con privilegi per la sottoscrizione A ed eseguire il cmdlet seguente:
   
        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet5
   
    Questo non è un requisito, perché il peering può essere stabilito anche se gli utenti generano singolarmente richieste di peering per le rispettive reti virtuali, purché le richieste corrispondano. L'aggiunta di utenti con privilegi dell'altra rete virtuale come utente nella rete virtuale locale facilita la configurazione.
2. Accedere ad Azure con l'account utente B con privilegi per la sottoscrizione B ed eseguire il cmdlet seguente:
   
        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet3
3. Nella sessione di accesso dell'utente A eseguire quindi il cmdlet seguente:
   
        $vnet3 = Get-AzureRmVirtualNetwork -ResourceGroupName hr-vnets -Name vnet3
   
        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet5 -VirtualNetwork $vnet3 -RemoteVirtualNetworkId "/subscriptions/<Subscription-B-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet5" -BlockVirtualNetworkAccess
4. Nella sessione di accesso dell'utente B eseguire quindi il cmdlet seguente:
   
        $vnet5 = Get-AzureRmVirtualNetwork -ResourceGroupName vendor-vnets -Name vnet5
   
        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet3 -VirtualNetwork $vnet5 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-A-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet3" -BlockVirtualNetworkAccess
5. Dopo avere stabilito il peering, tutte le macchine virtuali in VNet3 potranno comunicare con tutte le macchine virtuali in VNet5.

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. In questo scenario è possibile eseguire i cmdlet di PowerShell seguenti per stabilire il peering reti virtuali.  È necessario impostare la proprietà AllowForwardedTraffic su True e collegare VNET1 a HubVnet, il che consente il traffico in ingresso dall'esterno dello spazio indirizzi della rete virtuale con peering.
   
        $hubVNet = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name HubVNet
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
   
        Add-AzureRmVirtualNetworkPeering -Name LinkToHub -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $HubVNet.Id -AllowForwardedTraffic
   
        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $HubVNet -RemoteVirtualNetworkId $vnet1.Id
2. Dopo aver stabilito il peering, è possibile vedere questo [articolo](virtual-network-create-udr-arm-ps.md) e creare route definite dall'utente per reindirizzare il traffico di VNet1 attraverso un'appliance virtuale per usarne le funzionalità. Quando si specifica l'indirizzo dell'hop successivo nella route, è possibile impostarlo sull'indirizzo IP dell'appliance virtuale nella rete virtuale peer HubVNet. Di seguito è riportato un esempio:
   
        $route = New-AzureRmRouteConfig -Name TestNVA -AddressPrefix 10.3.0.0/16 -NextHopType VirtualAppliance -NextHopIpAddress 192.0.1.5
   
        $routeTable = New-AzureRmRouteTable -ResourceGroupName VNet101 -Location brazilsouth -Name TestRT -Route $route
   
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName VNet101 -Name VNet1
   
        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet1 -Name subnet-1 -AddressPrefix 10.1.1.0/24 -RouteTable $routeTable
   
        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet1

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

Per creare un peering reti virtuali tra una rete virtuale classica e una rete virtuale di Azure Resource Manager in PowerShell, seguire questa procedura:

1. Leggere l'oggetto di rete virtuale per **VNET1**, la rete virtuale di Azure Resource Manager nel modo seguente:
   
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
2. Per stabilire il peering reti virtuali in questo scenario, è necessario un solo collegamento, nello specifico uno da **VNET1** a **VNET2**. Per questo passaggio, è necessario conoscere l'ID risorsa della rete virtuale classica. Il formato ID del gruppo di risorse è simile al seguente:
   
        /subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.ClassicNetwork/virtualNetworks/{VirtualNetworkName}
   
    Assicurarsi di sostituire SubscriptionID, ResourceGroupName e VirtualNetworkName con i nomi appropriati.
   
    Ad esempio:
   
        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2
3. Dopo aver creato il collegamento per il peering reti virtuali, è possibile visualizzare lo stato del collegamento come illustrato nell'output seguente:
   
        Name                             : LinkToVNet2
        Id                               : /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeerings/LinkToVNet2
        Etag                             : W/"acecbd0f-766c-46be-aa7e-d03e41c46b16"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                         "Id": "/subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                       }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

## <a name="remove-vnet-peering"></a>Rimuovere il peering reti virtuali
1. Per rimuovere il peering reti virtuali, è necessario eseguire il cmdlet seguente:
   
     Remove-AzureRmVirtualNetworkPeering  
   
     Rimuovere entrambi i collegamenti usando i comandi seguenti:
   
     Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2   Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
2. Dopo la rimozione di un collegamento in un peering reti virtuali, lo stato del collegamento peer sarà Disconnesso. Con questo stato non si potrà ricreare il collegamento fino a quando lo stato del collegamento peer non diventerà Avviato. È consigliabile rimuovere entrambi i collegamenti prima di ricreare il peering reti virtuali.




<!--HONumber=Dec16_HO1-->


