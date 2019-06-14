---
title: Creare una tabella di route dell'hub virtuale della rete WAN virtuale di Azure per l'indirizzamento a un'appliance virtuale di rete | Microsoft Docs
description: Tabella di route dell'hub virtuale della rete WAN virtuale per indirizzare il traffico a un'appliance virtuale di rete.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to work with routing tables for NVA.
ms.openlocfilehash: fc8dd6770efa1c057a56374ddc0094c2d88d2eb5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60457600"
---
# <a name="create-a-virtual-hub-route-table-to-steer-traffic-to-a-network-virtual-appliance"></a>Creare una tabella di route dell'hub virtuale per indirizzare il traffico a un'appliance virtuale di rete

Questo articolo illustra come indirizzare il traffico proveniente da un Hub virtuale a un'appliance virtuale di rete. 

![Diagramma della rete WAN virtuale](./media/virtual-wan-route-table/vwanroute.png)

In questo articolo viene spiegato come:

* Creare una rete WAN
* Creare un hub
* Creare connessioni di rete virtuale dell'hub
* Creare una route dell'hub
* Creare una tabella di route
* Applicare la tabella di route

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Verificare di aver soddisfatto i criteri seguenti:

1. Si dispone di un'Appliance virtuale di rete (NVA). Si tratta di un software di terze parti di propria scelta che viene in genere il provisioning da Azure Marketplace in una rete virtuale.
2. Un indirizzo IP privato assegnato all'interfaccia di rete dell'appliance virtuale di rete. 
3. Appliance virtuale di rete non può essere distribuito nell'hub virtuale. Deve essere distribuita in una rete virtuale separata. Per questo articolo, la rete virtuale dell'appliance virtuale di rete viene definita "rete virtuale della rete perimetrale".
4. La "rete virtuale della rete perimetrale" può avere una o più reti virtuali connesse ad essa. In questo articolo viene fatto riferimento alla rete virtuale come "rete virtuale spoke indiretta". Queste reti virtuali possono essere connesse alla rete virtuale della rete perimetrale usando il peering reti virtuali.
5. Verificare di avere 2 reti virtuali già create. Queste verranno usate come reti virtuali spoke. Per questo articolo, gli spazi di indirizzi della rete virtuale spoke sono 10.0.2.0/24 e 10.0.3.0/24. Se sono necessarie informazioni su come creare una rete virtuale, vedere [Creare una rete virtuale usando PowerShell](../virtual-network/quick-create-powershell.md).
6. Assicurarsi che non ci siano gateway di rete virtuale in nessuna rete virtuale.

## <a name="signin"></a>1. Accesso

Assicurarsi di installare la versione più recente dei cmdlet di PowerShell per Resource Manager. Per altre informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Questo elemento è importante perché le versioni precedenti dei cmdlet non contengono i valori correnti necessari per questo esercizio.

1. Aprire la console di PowerShell con privilegi elevati e accedere al proprio account Azure. Il cmdlet richiederà le credenziali di accesso. Dopo l'accesso vengono scaricate le impostazioni dell'account in modo che siano disponibili per Azure PowerShell.

   ```powershell
   Connect-AzAccount
   ```
2. Ottenere un elenco delle sottoscrizioni di Azure.

   ```powershell
   Get-AzSubscription
   ```
3. Specificare la sottoscrizione da usare.

   ```powershell
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```

## <a name="rg"></a>2. Creare le risorse

1. Creare un gruppo di risorse.

   ```powershell
   New-AzResourceGroup -Location "West US" -Name "testRG"
   ```
2. Creare una rete WAN virtuale.

   ```powershell
   $virtualWan = New-AzVirtualWan -ResourceGroupName "testRG" -Name "myVirtualWAN" -Location "West US"
   ```
3. Creare un hub virtuale.

   ```powershell
   New-AzVirtualHub -VirtualWan $virtualWan -ResourceGroupName "testRG" -Name "westushub" -AddressPrefix "10.0.1.0/24"
   ```

## <a name="connections"></a>3. Creare le connessioni

Creare le connessioni dell'hub di rete virtuale dalla rete virtuale spoke indiretta e dalla rete virtuale di rete perimetrale all'hub virtuale.

  ```powershell
  $remoteVirtualNetwork1= Get-AzVirtualNetwork -Name "indirectspoke1" -ResourceGroupName "testRG"
  $remoteVirtualNetwork2= Get-AzVirtualNetwork -Name "indirectspoke2" -ResourceGroupName "testRG"
  $remoteVirtualNetwork3= Get-AzVirtualNetwork -Name "dmzvnet" -ResourceGroupName "testRG"

  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection1" -RemoteVirtualNetwork $remoteVirtualNetwork1
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection2" -RemoteVirtualNetwork $remoteVirtualNetwork2
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection3" -RemoteVirtualNetwork $remoteVirtualNetwork3
  ```

## <a name="route"></a>4. Creare una route dell'hub virtuale

Per questo articolo, gli spazi di indirizzi della rete virtuale spoke indiretta sono 10.0.2.0/24 e 10.0.3.0/24 e l'indirizzo IP privato dell'interfaccia di rete della rete virtuale della rete perimetrale è 10.0.4.5.

```powershell
$route1 = New-AzVirtualHubRoute -AddressPrefix @("10.0.2.0/24", "10.0.3.0/24") -NextHopIpAddress "10.0.4.5"
```

## <a name="applyroute"></a>5. Creare una tabella di route dell'hub virtuale

Creare una tabella di route dell'hub virtuale, quindi applicarvi la route creata.
 
```powershell
$routeTable = New-AzVirtualHubRouteTable -Route @($route1)
```

## <a name="commit"></a>6. Eseguire il commit delle modifiche

Eseguire il commit delle modifiche nell'hub virtuale.

```powershell
Update-AzVirtualHub -VirtualWanId $virtualWan.Id -ResourceGroupName "testRG" -Name "westushub" -RouteTable $routeTable
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere la [panoramica sulla rete WAN virtuale](virtual-wan-about.md).
