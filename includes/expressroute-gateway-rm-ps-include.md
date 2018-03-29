---
title: File di inclusione
description: File di inclusione
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/22/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d9eaab359d76afce61cfcc72e3fa125b08856e32
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
Nei passaggi di questa attività viene usata una rete virtuale basata sui valori indicati nell'elenco di riferimento per la configurazione riportato di seguito. In questo elenco sono indicati anche i nomi e le impostazioni aggiuntive. Questo elenco non verrà utilizzato direttamente nella procedura, ma si aggiungeranno variabili basate sui valori nell'elenco. È possibile copiare l'elenco per usarlo come riferimento, sostituendo i valori con quelli personalizzati.

**Elenco di riferimento per la configurazione**

* Nome rete virtuale = "TestVNet"
* Spazio degli indirizzi della rete virtuale = 192.168.0.0/16
* Gruppo di risorse = "TestRG"
* Nome subnet1 = "FrontEnd" 
* Spazio indirizzi subnet1 = "192.168.1.0/24"
* Nome subnet del gateway: "GatewaySubnet" Il nome della subnet del gateway deve sempre essere *GatewaySubnet*.
* Spazio degli indirizzi della subnet gateway = "192.168.200.0/26"
* Area = "East US"
* Nome del gateway = "GW"
* Nome IP del gateway = "GWIP"
* Nome della configurazione IP del gateway = "gwipconf"
* Tipo = "ExpressRoute" Questo tipo è necessario per una configurazione ExpressRoute.
* Nome IP pubblico del gateway = "gwpip"

## <a name="add-a-gateway"></a>Aggiungere un gateway
1. Connettersi alla sottoscrizione di Azure.

  ```powershell 
  Login-AzureRmAccount
  Get-AzureRmSubscription 
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
2. Dichiarare le variabili per questo esercizio. Assicurarsi di modificare l'esempio in base alle impostazioni da usare.

  ```powershell 
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "GW"
  $GWIPName = "GWIP"
  $GWIPconfName = "gwipconf"
  $VNetName = "TestVNet"
  ```
3. Archiviare l'oggetto rete virtuale come variabile.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
  ```
4. Aggiungere una subnet gateway alla rete virtuale. La subnet gateway deve esistere già e deve essere denominata "GatewaySubnet". È consigliabile creare una subnet gateway che sia /27 o più grande (/26, /25 e così via).

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
  ```
5. Impostare la configurazione.

  ```powershell
  $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
6. Archiviare la subnet gateway come variabile.

  ```powershell
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
  ```
7. Richiedere un indirizzo IP pubblico. L'indirizzo IP viene richiesto prima della creazione del gateway. Non è possibile specificare l'indirizzo IP che si desidera utilizzare. Viene allocato in modo dinamico. Questo indirizzo IP sarà utilizzato nella prossima sezione di configurazione. AllocationMethod deve essere Dynamic.

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
  ```
8. Creare la configurazione per il gateway. La configurazione del gateway definisce la subnet e l'indirizzo IP pubblico da utilizzare. In questo passaggio si specifica la configurazione che si userà per creare il gateway. Questo passaggio non crea effettivamente l'oggetto gateway. Per creare la configurazione del gateway, usare l'esempio seguente.

  ```powershell
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
  ```
9. Creare il gateway. In questo passaggio **-GatewayType** è particolarmente importante. È necessario usare il valore **ExpressRoute**. Dopo aver eseguito questi cmdlet, la creazione del gateway può richiedere anche oltre 45 minuti.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
  ```

## <a name="verify-the-gateway-was-created"></a>Verificare che il gateway sia stato creato
Per verificare che il gateway sia stato creato, usare i comandi seguenti:

```powershell
Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Ridimensionare un gateway
Esistono diversi [SKU del gateway](../articles/expressroute/expressroute-about-virtual-network-gateways.md). È possibile usare il comando seguente per modificare la SKU del gateway in qualsiasi momento.

> [!IMPORTANT]
> Questo comando non funziona per il gateway UltraPerformance. Per modificare il gateway in un gateway UltraPerformance, innanzitutto rimuovere il gateway ExpressRoute esistente, quindi creare un nuovo gateway UltraPerformance. Per effettuare il downgrade del gateway da un gateway UltraPerformance, innanzitutto rimuovere il gateway UltraPerformance, quindi creare un nuovo gateway.
> 
> 

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="remove-a-gateway"></a>Rimuovere un gateway
Per rimuovere un gateway, usare il comando seguente:

```powershell
Remove-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```
