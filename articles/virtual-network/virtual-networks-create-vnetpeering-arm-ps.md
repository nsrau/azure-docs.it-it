---
title: Peering reti virtuali di Azure - PowerShell | Documentazione Microsoft
description: Informazioni su come creare un peering reti virtuali con Azure PowerShell.
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
ms.sourcegitcommit: 5240bfc66ce15f845a511b7f09a5cd6209c8d539
ms.openlocfilehash: 34cc0fbddadb3860320ae730c2bc9951c735c7f9


---
# <a name="create-a-virtual-network-peering-using-azure-powershell"></a>Creare un peering reti virtuali con Azure PowerShell
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Per creare un peering reti virtuali con PowerShell, seguire questa procedura:

1. Se è la prima volta che si utilizza Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs) e seguire le istruzioni fino al termine della procedura per accedere ad Azure e selezionare la sottoscrizione desiderata.

    > [!NOTE]
    > Il cmdlet di PowerShell per la gestione di un peering reti virtuali viene fornito con [Azure PowerShell 1.6.](http://www.powershellgallery.com/packages/Azure/1.6.0)
    >

2. Leggere gli oggetti di rete virtuale:

    ```powershell
    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
    $vnet2 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet2
    ```

3. Per stabilire un peering reti virtuali, è necessario creare due collegamenti, uno per ogni direzione. Nel passaggio seguente si creerà un collegamento per il peering reti virtuali prima da VNet1 a VNet2:

    ```powershell
    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $vnet2.Id
    ```

    Output restituito:
        
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

4. In questo passaggio si creerà un collegamento per il peering reti virtuali da VNet2 a VNet1:

    ```powershell
    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $vnet2 -RemoteVirtualNetworkId $vnet1.Id
    ```

    Output restituito:

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
5. Dopo la creazione del collegamento per il peering reti virtuali, immettere il comando seguente per visualizzare lo stato del collegamento:

    ```powershell
    Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name linktovnet2
    ```

    Output restituito:
   
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

    Ogni collegamento in un peering reti virtuali include il precedente set di proprietà. Ad esempio, è possibile impostare `AllowVirtualNetworkAccess` su *True* per il collegamento per il peering reti virtuali da VNet1 a VNet2 e su *False* per il collegamento per il peering reti virtuali nella direzione opposta.

    ```powershell
    $LinktoVNet2 = Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 `
    -ResourceGroupName vnet101 -Name LinkToVNet2

    $LinktoVNet2.AllowForwardedTraffic = $true
    Set-AzureRmVirtualNetworkPeering -VirtualNetworkPeering $LinktoVNet2
    ```

    È possibile eseguire `Get-AzureRmVirtualNetworkPeering` per controllare il valore della proprietà dopo la modifica. Dall'output è possibile vedere che `AllowForwardedTraffic` viene impostato su *True* dopo l'esecuzione dei cmdlet precedenti.

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

    Dopo aver stabilito il peering, le macchine virtuali devono poter comunicare tra loro in entrambe le reti virtuali. Per impostazione predefinita, `AllowVirtualNetworkAccess` è impostato su *True* e il peering reti virtuali effettuerà il provisioning degli ACL appropriati per consentire la comunicazione tra le reti virtuali. È comunque possibile applicare le regole del gruppo di sicurezza di rete per bloccare la connettività tra subnet o macchine virtuali specifiche per ottenere il controllo granulare dell'accesso tra due reti virtuali. Per altre informazioni sui gruppi di sicurezza di rete, vedere l'articolo ]network security group](virtual-networks-create-nsg-arm-ps.md).

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Per creare un peering reti virtuali tra sottoscrizioni con PowerShell, seguire questa procedura:

1. Accedere ad Azure con l'account utente A con privilegi per la sottoscrizione A ed eseguire il cmdlet seguente:

    ```powershell
    New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet5
    ```

    Questo non è un requisito. Il peering può essere stabilito anche se gli utenti generano singolarmente richieste di peering per le rispettive reti virtuali, purché le richieste corrispondano. L'aggiunta di utenti con privilegi dell'altra rete virtuale come utente nella rete virtuale locale facilita la configurazione.
2. Accedere ad Azure con l'account utente B con privilegi per la sottoscrizione B ed eseguire il cmdlet seguente:

    ```powershell
    New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet3
    ```

    > [!IMPORTANT]
    > Se si sta creando il peering tra due reti virtuali create con il modello di distribuzione Azure Resource Manager, continuare con i passaggi rimanenti di questa sezione. Se le due reti virtuali sono state create con modelli di distribuzione diversi, ignorare i passaggi rimanenti di questa sezione e completare la procedura descritta nella sezione [Peering di reti virtuali create con modelli di distribuzione diversi](#x-model) di questo articolo.

3. Nella sessione di accesso dell'utente A eseguire questo comando:

    ```powershell
    $vnet3 = Get-AzureRmVirtualNetwork -ResourceGroupName hr-vnets -Name vnet3

    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet5 -VirtualNetwork $vnet3 -RemoteVirtualNetworkId "/subscriptions/<Subscription-B-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet5" -BlockVirtualNetworkAccess
    ```

4. Nella sessione di accesso dell'utente B eseguire quindi il cmdlet seguente:

    ```powershell
    $vnet5 = Get-AzureRmVirtualNetwork -ResourceGroupName vendor-vnets -Name vnet5

    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet3 -VirtualNetwork $vnet5 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-A-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet3" -BlockVirtualNetworkAccess
    ```

5. Dopo avere stabilito il peering, tutte le macchine virtuali in VNet3 potranno comunicare con tutte le macchine virtuali in VNet5.

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. In questo scenario eseguire i cmdlet di PowerShell seguenti per stabilire il peering reti virtuali. È necessario impostare la proprietà `AllowForwardedTraffic` su *True* e collegare VNET1 a HubVnet, consentendo così il traffico in ingresso dall'esterno dello spazio indirizzi della rete virtuale con peering.

    ```powershell
    $hubVNet = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name HubVNet
    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

    Add-AzureRmVirtualNetworkPeering -Name LinkToHub -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $HubVNet.Id -AllowForwardedTraffic

    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $HubVNet -RemoteVirtualNetworkId $vnet1.Id
    ```

2. Dopo aver stabilito il peering, è possibile vedere questo [articolo](virtual-network-create-udr-arm-ps.md) e creare route definite dall'utente per reindirizzare il traffico di VNet1 attraverso un'appliance virtuale per usarne le funzionalità. Quando si specifica l'indirizzo dell'hop successivo nella route, è possibile impostarlo sull'indirizzo IP dell'appliance virtuale nella rete virtuale peer HubVNet. Di seguito è riportato un esempio:

    ```powershell
    $route = New-AzureRmRouteConfig -Name TestNVA -AddressPrefix 10.3.0.0/16 -NextHopType VirtualAppliance -NextHopIpAddress 192.0.1.5

    $routeTable = New-AzureRmRouteTable -ResourceGroupName VNet101 -Location brazilsouth -Name TestRT -Route $route

    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName VNet101 -Name VNet1

    Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet1 -Name subnet-1 -AddressPrefix 10.1.1.0/24 -RouteTable $routeTable

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet1
    ```

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

1. Se si sta creando un peering tra reti virtuali distribuite con modelli di distribuzione diversi nella *stessa* sottoscrizione, andare al passaggio 2. La possibilità di creare un peering tra reti virtuali distribuite con modelli di distribuzione diversi in sottoscrizioni *diverse* è disponibile in versione di **anteprima**. Le funzionalità in anteprima non offrono lo stesso livello di affidabilità e lo stesso contratto di servizio delle funzionalità in versione di disponibilità generale. Se si sta creando un peering tra reti virtuali distribuite con modelli di distribuzione diversi in sottoscrizioni diverse, è necessario prima completare le attività seguenti:
    - Registrare la funzionalità in anteprima nella sottoscrizione di Azure immettendo il comando seguente di PowerShell: `Register-AzureRmProviderFeature -FeatureName AllowClassicCrossSubscriptionPeering -ProviderNamespace Microsoft.Network`
    - Completare i passaggi 1 e 2 della sezione [Peering tra sottoscrizioni](#x-sub) di questo articolo.
2. Leggere l'oggetto di rete virtuale per **VNET1**, la rete virtuale di Azure Resource Manager, immettendo il comando seguente:

    ```powershell
    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
    ```

3. Per stabilire il peering reti virtuali in questo scenario, è necessario un solo collegamento, nello specifico uno da **VNET1** a **VNET2**. Per questo passaggio, è necessario conoscere l'ID risorsa della rete virtuale classica. Il formato dell'ID del gruppo di risorse è simile all'esempio seguente:

        subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.ClassicNetwork/virtualNetworks/{VirtualNetworkName}

    Assicurarsi di sostituire SubscriptionID, ResourceGroupName e VirtualNetworkName con i nomi appropriati.

    A tale scopo è possibile immettere il comando seguente:

    ```powershell
    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2
    ```

4. Dopo aver creato il collegamento per il peering reti virtuali, è possibile visualizzare lo stato del collegamento come illustrato nell'output seguente:
   
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

## <a name="remove-a-vnet-peering"></a>Rimuovere un peering reti virtuali
1. Per rimuovere un peering reti virtuali è necessario eseguire questo cmdlet:

    ```powershell
    Remove-AzureRmVirtualNetworkPeering
    ```

    Per rimuovere entrambi i collegamenti, immettere i comandi seguenti:

    ```powershell
    Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
    Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
    ```

2. Dopo la rimozione di un collegamento in un peering reti virtuali, lo stato del collegamento peer sarà *Disconnesso*. Con questo stato non si potrà ricreare il collegamento fino a quando lo stato del collegamento peer non diventerà *Avviato*. È consigliabile rimuovere entrambi i collegamenti prima di ricreare il peering reti virtuali.




<!--HONumber=Feb17_HO1-->


