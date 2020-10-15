---
title: 'Esercitazione: Azure ExpressRoute - Aggiungere un gateway a una rete virtuale - Azure PowerShell'
description: Questa esercitazione illustra come aggiungere un gateway di rete virtuale a una rete virtuale di Resource Manager per ExpressRoute preesistente tramite Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/05/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 9f01961ec7c7f8e0a4e2d72e28e6def50e93ad5d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91854308"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Esercitazione: Configurare un gateway di rete virtuale per ExpressRoute usando PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager - Portale di Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Classica: PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video - Portale di Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

Questa esercitazione illustra come aggiungere, ridimensionare e rimuovere un gateway di rete virtuale per una rete virtuale preesistente. I passaggi di questa configurazione si applicano alle reti virtuali create usando il modello di distribuzione di Resource Manager per una configurazione di ExpressRoute. Per altre informazioni, vedere [Informazioni sui gateway di rete virtuale per ExpressRoute](expressroute-about-virtual-network-gateways.md).

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> - Creare una subnet del gateway.
> - Creare il gateway di rete virtuale.

## <a name="prerequisites"></a>Prerequisiti

### <a name="configuration-reference-list"></a>Elenco di riferimento per la configurazione

Nei passaggi di questa attività viene usata una rete virtuale basata sui valori indicati nell'elenco di riferimento per la configurazione riportato di seguito. In questo elenco sono indicati anche i nomi e le impostazioni aggiuntive. Questo elenco non verrà utilizzato direttamente nella procedura, ma si aggiungeranno variabili basate sui valori nell'elenco. È possibile copiare l'elenco per usarlo come riferimento, sostituendo i valori con quelli personalizzati.

| Impostazione                   | Valore                                              |
| ---                       | ---                                                |
| Nome della rete virtuale | *TestVNet* |    
| Spazio degli indirizzi della rete virtuale | *192.168.0.0/16* |
| Gruppo di risorse | *TestRG* |
| Nome della subnet1 | *FrontEnd* |   
| Spazio indirizzi della subnet1 | *192.168.1.0/24* |
| Nome della subnet1 | *FrontEnd* |
| Nome della subnet del gateway | *GatewaySubnet* |    
| Spazio degli indirizzi della subnet del gateway | *192.168.200.0/26* |
| Area | *Stati Uniti orientali* |
| Nome gateway | *GW* |   
| Nome IP del gateway | *GWIP* |
| Nome della configurazione IP del gateway | *gwipconf* |
| Type | *ExpressRoute* |
| Nome IP pubblico del gateway  | *gwpip* |

## <a name="add-a-gateway"></a>Aggiungere un gateway

1. Per connettersi ad Azure, eseguire `Connect-AzAccount`.

1. Dichiarare le variabili per questo esercizio. Assicurarsi di modificare l'esempio in base alle impostazioni da usare.

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
1. Archiviare l'oggetto rete virtuale come variabile.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
1. Aggiungere una subnet gateway alla rete virtuale. La subnet gateway deve esistere già e deve essere denominata "GatewaySubnet". Il valore per la subnet del gateway deve essere /27 o superiore (/26, /25 e così via).

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
1. Impostare la configurazione.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
1. Archiviare la subnet gateway come variabile.

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
1. Richiedere un indirizzo IP pubblico. L'indirizzo IP viene richiesto prima della creazione del gateway. Non è possibile specificare l'indirizzo IP da usare, perché viene assegnato dinamicamente. Questo indirizzo IP sarà utilizzato nella prossima sezione di configurazione. AllocationMethod deve essere Dynamic.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
1. Creare la configurazione per il gateway. La configurazione del gateway definisce la subnet e l'indirizzo IP pubblico da utilizzare. In questo passaggio si specifica la configurazione che verrà usata quando si crea il gateway. Per creare la configurazione del gateway, usare l'esempio seguente.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
1. Creare il gateway. In questo passaggio **-GatewayType** è particolarmente importante. È necessario usare il valore **ExpressRoute**. Dopo aver eseguito questi cmdlet, la creazione del gateway può richiedere anche oltre 45 minuti.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```

## <a name="verify-the-gateway-was-created"></a>Verificare che il gateway sia stato creato
Per verificare che il gateway sia stato creato, usare i comandi seguenti:

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Ridimensionare un gateway
Esistono diversi [SKU del gateway](expressroute-about-virtual-network-gateways.md). È possibile usare il comando seguente per modificare la SKU del gateway in qualsiasi momento.

> [!IMPORTANT]
> Questo comando non funziona per il gateway UltraPerformance. Per modificare il gateway in un gateway UltraPerformance, innanzitutto rimuovere il gateway ExpressRoute esistente, quindi creare un nuovo gateway UltraPerformance. Per effettuare il downgrade del gateway da un gateway UltraPerformance, innanzitutto rimuovere il gateway UltraPerformance, quindi creare un nuovo gateway.
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="clean-up-resources"></a>Pulire le risorse
Usare il comando seguente per rimuovere il gateway:

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```

## <a name="next-steps"></a>Passaggi successivi
Dopo avere creato il gateway della rete virtuale, è possibile collegare la rete virtuale a un circuito ExpressRoute. 

> [!div class="nextstepaction"]
> [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)
