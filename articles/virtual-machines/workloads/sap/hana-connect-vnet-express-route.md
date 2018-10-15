---
title: Configurazione della connettività dalla rete virtuale al SAP HANA di Azure (Istanze large) | Microsoft Docs
description: Configurazione della connettività dalla rete virtuale per usare SAP HANA di Azure (Istanze large).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5efdda485e4e1f5013948c6636b267f0d388f4d5
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44167601"
---
# <a name="connecting-a-vnet-to-hana-large-instance-expressroute"></a>Connessione di una rete virtuale a ExpressRoute per istanze Large di HANA

Dopo aver definito tutti gli intervalli di indirizzi IP e aver ottenuto i dati da Microsoft, è possibile avviare la connessione della rete virtuale creata in precedenza alle istanze Large di HANA. Una volta creata la rete virtuale di Azure, è necessario creare un gateway ExpressRoute sulla rete per collegarla al circuito ExpressRoute che collega il tenant del cliente allo stamp di istanze di grandi dimensioni.

> [!NOTE] 
> Questo passaggio può richiedere fino a 30 minuti; il nuovo gateway viene creato nella sottoscrizione Azure specificata e viene quindi connesso alla rete virtuale di Azure indicata.

Se esiste già un gateway, controllare se sia o meno un gateway ExpressRoute. Se non si tratta di un gateway ExpressRoute, deve essere eliminato e ricreato come tale. Se è già presente un gateway ExpressRoute, poiché la rete virtuale di Azure è già connessa al circuito ExpressRoute per la connettività locale, passare alla sezione Collegamento di reti virtuali di seguito.

- Usare il (nuovo) [portale di Azure](https://portal.azure.com/) o PowerShell per creare un gateway VPN ExpressRoute connesso alla rete virtuale.
  - Se si usa il portale di Azure, aggiungere un nuovo **Gateway di rete virtuale** e selezionare **ExpressRoute** come tipo di gateway.
  - Se, invece, si usa PowerShell, scaricare e usare innanzitutto la versione più recente di [Azure PowerShell SDK](https://azure.microsoft.com/downloads/) per garantire un'esperienza ottimale. I seguenti comandi creano un gateway ExpressRoute. Il testo preceduto dal simbolo _$_ è costituito da variabili definite dall'utente, da aggiornare in base alle informazioni personali specifiche.

```PowerShell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

In questo esempio è stato usato lo SKU del gateway HighPerformance. Le opzioni disponibili sono HighPerformance o UltraPerformance e sono gli unici SKU del gateway supportati per SAP HANA in Azure (istanze Large).

> [!IMPORTANT]
> Per le SKU di classi di tipo II di istanze di HANA Large, è obbligatorio l'utilizzo di SKU gateway UltraPerformance.

**Collegamento delle reti virtuali**

Ora che la rete virtuale di Azure dispone di un gateway ExpressRoute, usare le informazioni di autorizzazione ottenute da Microsoft per connettere il gateway ExpressRoute al circuito ExpressRoute di SAP HANA in Azure (istanze grandi) creato per questa connettività. Questa operazione può essere eseguita usando il portale di Azure o PowerShell. Sebbene il portale sia la scelta consigliata, di seguito sono riportate le istruzioni per usare PowerShell. 

- Eseguire questi comandi per ogni gateway di rete virtuale usando un AuthGUID diverso per ogni connessione. Le prime due voci illustrate nello script seguente provengono dalle informazioni ottenute da Microsoft. Inoltre, AuthGUID è specifico per ogni rete virtuale e il relativo gateway. Per aggiungere un'altra rete virtuale di Azure, quindi, è necessario ottenere un altro AuthID per il circuito ExpressRoute che connette istanze Large di HANA ad Azure. 

```PowerShell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Se si vuole collegare il gateway a più circuiti ExpressRoute associati alla sottoscrizione, è possibile che sia necessario eseguire più volte questo passaggio. Ad esempio, sarà probabilmente necessario connettere lo stesso gateway di rete virtuale al circuito ExpressRoute che si connette alla rete virtuale locale.

**Passaggi successivi**

- Consultare [Requisiti aggiuntivi della rete per HLI](hana-additional-network-requirements.md).