---
title: includere file
titleSuffix: Azure
description: includere file
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 12d169697a35af446392843eb57e6ec3a5508e45
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81678612"
---
Per il peering di Exchange sono supportate le operazioni di modifica seguenti:
* Aggiungere connessioni peering di Exchange.
* Rimuovere le connessioni peering di Exchange.
* Aggiungere una sessione IPv4 o IPv6 in connessioni attive.
* Rimuovere una sessione IPv4 o IPv6 in connessioni attive.


### <a name="add-exchange-peering-connections"></a>Aggiungere connessioni peering di Exchange

Questo esempio descrive come aggiungere connessioni a un peering di Exchange esistente.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringExchangeConnectionObject `
    -PeeringDBFacilityId $exchangeLocation[1].PeeringDBFacilityId `
    -PeerSessionIPv4Address 198.32.134.22 `
    -PeerSessionIPv6Address  2001:504:12::22 `
    -MaxPrefixesAdvertisedIPv4 2000 `
    -MaxPrefixesAdvertisedIPv6 2000 `

$exchangePeering.Connections.Add($connection)

$exchangePeering | Update-AzPeering

```

### <a name="remove-exchange-peering-connections"></a>Rimuovere le connessioni peering di Exchange

In questo esempio viene descritto come rimuovere connessioni a un peering di Exchange esistente.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

```

Visualizzare tutte le connessioni e selezionare la connessione che si desidera rimuovere. 

```powershell

$exchangePeering

Name              : SeattleExchangePeering
Sku.Name          : Basic_Exchange_Free
Kind              : Exchange
Connections       : {11}
PeerAsn.Id        : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{peerAsnName}
PeeringLocation   : Seattle
ProvisioningState : Succeeded
Location          : West US 2
Id                : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleExchangePeering
Type              : Microsoft.Peering/peerings
Tags              : {}

```

Nel comando seguente, anziché 0, immettere il numero di indice per la connessione che si desidera rimuovere.

```powershell

$connection = $exchangePeering.Connections[0]

$exchangePeering.Connections.Remove($connection)

$exchangePeering | Update-AzPeering

```

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>Aggiungere una sessione IPv4 o IPv6 in connessioni attive

In questo esempio viene illustrato come aggiungere una sessione IPv6 a una connessione Exchange esistente.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$exchangePeering.Connections[0].BgpSession.PeerSessionIPv6Address = "2001:504:12::34"

$exchangePeering | Update-AzPeering

```

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>Rimuovere una sessione IPv4 o IPv6 in connessioni attive

La rimozione di una sessione IPv4 o IPv6 da una connessione esistente non è attualmente supportata in PowerShell. Per ulteriori informazioni, contattare il [peering Microsoft](mailto:peeringexperience@microsoft.com).