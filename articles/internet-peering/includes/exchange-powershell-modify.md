---
title: File di inclusione
titleSuffix: Azure
description: File di inclusione
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 12d169697a35af446392843eb57e6ec3a5508e45
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678612"
---
Per il peering di Exchange sono supportate le seguenti operazioni di modifica:
* Aggiungere connessioni di peering di Exchange.
* Rimuovere le connessioni di peering di Exchange.
* Aggiungere una sessione IPv4 o IPv6 su connessioni attive.
* Rimuovere una sessione IPv4 o IPv6 su connessioni attive.


### <a name="add-exchange-peering-connections"></a>Aggiungere connessioni di peering di Exchange

In questo esempio viene descritto come aggiungere connessioni a un peering di Exchange esistente.

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

### <a name="remove-exchange-peering-connections"></a>Rimuovere le connessioni di peering di Exchange

In questo esempio viene descritto come rimuovere le connessioni a un peering di Exchange esistente.

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

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>Aggiungere una sessione IPv4 o IPv6 su connessioni attive

In questo esempio viene descritto come aggiungere una sessione IPv6 a una connessione di Exchange esistente.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$exchangePeering.Connections[0].BgpSession.PeerSessionIPv6Address = "2001:504:12::34"

$exchangePeering | Update-AzPeering

```

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>Rimuovere una sessione IPv4 o IPv6 su connessioni attive

La rimozione di una sessione IPv4 o IPv6 da una connessione esistente non è attualmente supportata in PowerShell.Removing an IPv4 or IPv6 session from an existing connection isn't currently supported on PowerShell. Per ulteriori informazioni, [contattare Microsoft peering](mailto:peeringexperience@microsoft.com).