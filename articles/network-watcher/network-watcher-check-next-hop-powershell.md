---
title: Individuare l&quot;hop successivo con Network Watcher di Azure - PowerShell | Documentazione Microsoft
description: "Questo articolo descrive come individuare il tipo di hop successivo e l&quot;indirizzo IP tramite la funzionalità Hop successivo usando PowerShell."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 6a656c55-17bd-40f1-905d-90659087639c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 00161e7c6fb4becdb7d8eab266fa27128e50f8ca
ms.contentlocale: it-it
ms.lasthandoff: 05/26/2017


---

# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-powershell"></a>Individuare il tipo di hop successivo tramite la funzionalità Hop successivo di Network Watcher di Azure usando PowerShell

> [!div class="op_single_selector"]
> - [Portale di Azure](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [Interfaccia della riga di comando 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [Interfaccia della riga di comando 2.0](network-watcher-check-next-hop-cli.md)
> - [API REST di Azure](network-watcher-check-next-hop-rest.md)

Hop successivo è una funzionalità di Network Watcher che consente di recuperare il tipo di hop successivo e l'indirizzo IP in base a una macchina virtuale specificata. La funzionalità è utile per determinare se il traffico in uscita da una macchina virtuale attraversa gateway, Internet o reti virtuali per arrivare alla propria destinazione.

## <a name="before-you-begin"></a>Prima di iniziare

In questo scenario viene usato il portale di Azure per trovare il tipo di hop successivo e l'indirizzo IP.

Questo scenario presuppone il completamento dei passaggi descritti in [Creare un servizio Network Watcher](network-watcher-create.md) per creare un servizio Network Watcher. Lo scenario presuppone inoltre che esista e possa essere usato un gruppo di risorse con una macchina virtuale valida.

## <a name="scenario"></a>Scenario

Lo scenario illustrato in questo articolo usa la funzionalità Hop successivo di Network Watcher che rileva il tipo di hop successivo e l'indirizzo IP di una risorsa. Per altre informazioni sulla funzionalità di individuazione dell'hop successivo, consultare la [panoramica sulla funzionalità Hop successivo](network-watcher-next-hop-overview.md).

## <a name="retrieve-network-watcher"></a>Recuperare Network Watcher

Il primo passaggio consente di recuperare l'istanza di Network Watcher. La variabile `$networkWatcher` viene passata al cmdlet di verifica dell'hop successivo.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-virtual-machine"></a>Ottenere una macchina virtuale

La funzionalità Hop successivo restituisce l'hop successivo e l'indirizzo IP dell'hop successivo da una macchina virtuale. Il cmdlet richiede un ID di macchina virtuale. Se l'ID della macchina virtuale da usare è già noto, è possibile ignorare questo passaggio.

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

> [!NOTE]
> La funzionalità Hop successivo richiede che la risorsa VM sia allocata.

## <a name="get-the-network-interfaces"></a>Ottenere le interfacce di rete

È necessario l'indirizzo IP di una scheda di rete nella macchina virtuale. In questo esempio vengono recuperate le schede NIC in una macchina virtuale. Se l'indirizzo IP da testare nella macchina virtuale è già noto, è possibile ignorare questo passaggio.

```powershell
$Nics = Get-AzureRmNetworkInterface | Where {$_.Id -eq $vm.NetworkProfile.NetworkInterfaces.Id.ForEach({$_})}
```

## <a name="get-next-hop"></a>Ottenere l'hop successivo

Ora chiamiamo il cmdlet `Get-AzureRmNetworkWatcherNextHop`. Al cmdlet vengono passati il Network Watcher, l'ID della macchina virtuale, l'indirizzo IP di origine e l'indirizzo IP di destinazione. Nell'esempio seguente l'indirizzo IP di destinazione corrisponde a una macchina virtuale in un'altra rete virtuale. Tra le due reti virtuali esiste un gateway di rete virtuale.

```powershell
Get-AzureRmNetworkWatcherNextHop -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id -SourceIPAddress $nics[0].IpConfigurations[0].PrivateIpAddress  -DestinationIPAddress 10.0.2.4 
```

## <a name="review-results"></a>Esaminare i risultati

Al termine vengono forniti i risultati. Viene inoltre restituito l'indirizzo IP dell'hop successivo e il tipo di risorsa corrispondente. In questo scenario è l'indirizzo IP pubblico del gateway di rete virtuale.

```
NextHopIpAddress NextHopType           RouteTableId 
---------------- -----------           ------------ 
13.78.238.92     VirtualNetworkGateway Gateway Route
```

L'elenco seguente mostra i valori NextHopType attualmente disponibili:

**Tipo di hop successivo**

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* None

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come verificare le impostazioni del gruppo di sicurezza di rete a livello di codice, consultare l'articolo su come [verificare i gruppi di sicurezza di rete con Network Watcher](network-watcher-nsg-auditing-powershell.md)


















