---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/28/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8803aada61ae58f1e221767aeb382f7d74c63eb4
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2018
ms.locfileid: "30326538"
---
### <a name="noconnection"></a>Per modificare i prefissi degli indirizzi IP del gateway di rete locale senza connessione gateway

Per aggiungere altri prefissi degli indirizzi:

```azurepowershell-interactive
$local = Get-AzureRmLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
-AddressPrefix @('10.101.0.0/24','10.101.1.0/24','10.101.2.0/24')
```

Per rimuovere prefissi degli indirizzi:<br>
Escludere i prefissi non più necessari. In questo esempio non è più necessario il prefisso 10.101.2.0/24 usato nell'esempio precedente. Il gateway di rete locale verrà quindi aggiornato escludendo tale prefisso.

```azurepowershell-interactive
$local = Get-AzureRmLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
-AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
```

### <a name="withconnection"></a>Per modificare i prefissi degli indirizzi IP del gateway di rete locale con connessione gateway esistente

Se è disponibile una connessione gateway e si vogliono aggiungere o rimuovere i prefissi di indirizzo IP inclusi nel gateway di rete locale, è necessario seguire questa procedura nell'ordine indicato. Questo comporterà periodi di inattività per la connessione VPN. Quando si modificano i prefissi degli indirizzi IP, non è necessario eliminare il gateway VPN. Occorre rimuovere solo la connessione.


1. Rimuovere la connessione.

  ```azurepowershell-interactive
  Remove-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1
  ```
2. Modificare i prefissi di indirizzo IP per il gateway di rete locale.
   
  Impostare la variabile per LocalNetworkGateway.

  ```azurepowershell-interactive
  $local = Get-AzureRmLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
  ```
   
  Modificare i prefissi.
   
  ```azurepowershell-interactive
  Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
  -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
  ```
3. Creare la connessione. In questo esempio viene configurato un tipo di connessione IPsec. Quando si ricrea la connessione, usare il tipo di connessione specificato per la configurazione in uso. Per altri tipi di connessione, vedere la pagina dei [cmdlet di PowerShell](https://msdn.microsoft.com/library/mt603611.aspx) .
   
  Impostare la variabile per VirtualNetworkGateway.

  ```azurepowershell-interactive
  $gateway1 = Get-AzureRmVirtualNetworkGateway -Name VNet1GW  -ResourceGroupName TestRG1
  ```
   
  Creare la connessione. Questo esempio usa la variabile $local impostata nel passaggio 2.

  ```azurepowershell-interactive
  New-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite1 `
  -ResourceGroupName TestRG1 -Location 'East US' `
  -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
  -ConnectionType IPsec `
  -RoutingWeight 10 -SharedKey 'abc123'
  ```