---
title: File di inclusione
description: File di inclusione
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 02/21/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 922ac7eb6cb9676af65700a6a2fe7fbae35a0dc5
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158738"
---
Nei passaggi di questa attività viene usata una rete virtuale basata sui valori indicati nell'elenco di riferimento per la configurazione riportato di seguito. In questo elenco sono indicati anche i nomi e le impostazioni aggiuntive. Questo elenco non verrà utilizzato direttamente nella procedura, ma si aggiungeranno variabili basate sui valori nell'elenco. È possibile copiare l'elenco per usarlo come riferimento, sostituendo i valori con quelli personalizzati.

* Nome rete virtuale = "TestVNet"
* Spazio degli indirizzi della rete virtuale = 192.168.0.0/16
* Gruppo di risorse = "TestRG"
* Nome subnet1 = "FrontEnd" 
* Spazio indirizzi subnet1 = "192.168.1.0/24"
* Nome subnet del gateway: "GatewaySubnet". Il nome della subnet del gateway deve sempre essere *GatewaySubnet*.
* Spazio degli indirizzi della subnet gateway = "192.168.200.0/26"
* Area = "East US"
* Nome del gateway = "GW"
* Nome IP del gateway = "GWIP"
* Nome della configurazione IP del gateway = "gwipconf"
* Tipo = "ExpressRoute" Questo tipo è necessario per una configurazione ExpressRoute.
* Nome IP pubblico del gateway = "gwpip"

## <a name="add-a-gateway"></a>Aggiungere un gateway
1. Connettersi alla sottoscrizione di Azure.

   [!INCLUDE [Sign in](expressroute-cloud-shell-connect.md)]
2. Dichiarare le variabili per questo esercizio. Assicurarsi di modificare l'esempio in base alle impostazioni da usare.

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
3. Archiviare l'oggetto rete virtuale come variabile.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
4. Aggiungere una subnet gateway alla rete virtuale. La subnet gateway deve esistere già e deve essere denominata "GatewaySubnet". È consigliabile creare una subnet gateway che sia /27 o più grande (/26, /25 e così via).

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
5. Impostare la configurazione.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Archiviare la subnet gateway come variabile.

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
7. Richiedere un indirizzo IP pubblico. L'indirizzo IP viene richiesto prima della creazione del gateway. Non è possibile specificare l'indirizzo IP che si desidera utilizzare. Viene allocato in modo dinamico. Questo indirizzo IP sarà utilizzato nella prossima sezione di configurazione. AllocationMethod deve essere Dynamic.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
8. Creare la configurazione per il gateway. La configurazione del gateway definisce la subnet e l'indirizzo IP pubblico da utilizzare. In questo passaggio si specifica la configurazione che si userà per creare il gateway. Questo passaggio non crea effettivamente l'oggetto gateway. Per creare la configurazione del gateway, usare l'esempio seguente.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
9. Creare il gateway. In questo passaggio **-GatewayType** è particolarmente importante. È necessario usare il valore **ExpressRoute**. Dopo aver eseguito questi cmdlet, la creazione del gateway può richiedere anche oltre 45 minuti.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```

## <a name="verify-the-gateway-was-created"></a>Verificare che il gateway sia stato creato
Per verificare che il gateway sia stato creato, usare i comandi seguenti:

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Ridimensionare un gateway
Esistono diversi [SKU del gateway](../articles/expressroute/expressroute-about-virtual-network-gateways.md). È possibile usare il comando seguente per modificare la SKU del gateway in qualsiasi momento.

> [!IMPORTANT]
> Questo comando non funziona per il gateway UltraPerformance. Per modificare il gateway in un gateway UltraPerformance, innanzitutto rimuovere il gateway ExpressRoute esistente, quindi creare un nuovo gateway UltraPerformance. Per effettuare il downgrade del gateway da un gateway UltraPerformance, innanzitutto rimuovere il gateway UltraPerformance, quindi creare un nuovo gateway.
> 
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="remove-a-gateway"></a>Rimuovere un gateway
Per rimuovere un gateway, usare il comando seguente:

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```
