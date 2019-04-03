---
title: Configurazione della connettività dalla rete virtuale al SAP HANA di Azure (istanze Large) | Microsoft Docs
description: Configurazione della connettività dalla rete virtuale per usare SAP HANA di Azure (istanze Large).
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
ms.openlocfilehash: cb14d0784ecb87c85b02952880e9eb5744d205a2
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58850658"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Collegare una rete virtuale alle istanze Large di HANA

Dopo aver creato una rete virtuale di Azure, è possibile connettere la rete a SAP HANA in istanze Large di Azure. Creare un gateway ExpressRoute di Azure nella rete virtuale. Il gateway consente di collegare la rete virtuale al circuito ExpressRoute che si connette al tenant del cliente sul modulo per istanze Large.

> [!NOTE] 
> Questo passaggio può richiedere fino a 30 minuti. Il nuovo gateway viene creato nella sottoscrizione di Azure specificata e viene quindi connesso alla rete virtuale di Azure indicata.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Se esiste già un gateway, controllare che sia un gateway ExpressRoute. In caso contrario, eliminare il gateway e crearlo nuovamente come un gateway ExpressRoute. Se è già stato definito un gateway ExpressRoute, vedere la sezione seguente di questo articolo: "Collegare reti virtuali". 

- Usare il [portale di Azure](https://portal.azure.com/) o PowerShell per creare un gateway VPN ExpressRoute connesso alla rete virtuale.
  - Se si usa il portale di Azure, aggiungere un nuovo **Gateway di rete virtuale** e selezionare **ExpressRoute** come tipo di gateway.
  - Se si usa PowerShell, è necessario prima scaricare e usare la versione più recente di [Azure PowerShell SDK](https://azure.microsoft.com/downloads/). I seguenti comandi creano un gateway ExpressRoute. Il testo preceduto dal simbolo _$_ è costituito da variabili definite dall'utente, da aggiornare in base alle informazioni personali specifiche.

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA large instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

In questo esempio è stato usato lo SKU del gateway HighPerformance. Le opzioni disponibili sono HighPerformance o UltraPerformance e sono le uniche SKU del gateway supportate per SAP HANA in Azure (istanze Large).

> [!IMPORTANT]
> Per le SKU Classe di tipo II delle istanze Large di HANA, è necessario usare UltraPerformance Gateway SKU.

## <a name="link-virtual-networks"></a>Collegare reti virtuali

La rete virtuale di Azure dispone ora di un gateway ExpressRoute. Usare le informazioni di autorizzazione fornite da Microsoft per connettere il gateway ExpressRoute al circuito ExpressRoute di SAP HANA in Azure (istanze Large). Questa operazione può essere eseguita nel portale di Azure o con PowerShell. Sebbene il portale sia la scelta consigliata, di seguito sono riportate le istruzioni per usare PowerShell. 

Eseguire questi comandi per ogni gateway di rete virtuale usando un AuthGUID diverso per ogni connessione. Le prime due voci illustrate nello script seguente provengono dalle informazioni ottenute da Microsoft. Il codice AuthGUID, inoltre, è specifico per ogni rete virtuale e il relativo gateway. Per aggiungere un'altra rete virtuale di Azure, quindi, è necessario ottenere un altro AuthID per il circuito ExpressRoute che connette istanze Large di HANA ad Azure. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Se si vuole collegare il gateway a più circuiti ExpressRoute associati alla sottoscrizione, è possibile che sia necessario eseguire più volte questo passaggio. Ad esempio, sarà probabilmente necessario connettere lo stesso gateway di rete virtuale al circuito ExpressRoute che si connette alla rete virtuale locale.

## <a name="next-steps"></a>Passaggi successivi

- [Requisiti aggiuntivi della rete per HLI](hana-additional-network-requirements.md)
