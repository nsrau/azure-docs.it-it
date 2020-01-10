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
ms.openlocfilehash: 86e1a9cce1864ce259fe07b6949be2e32be242a8
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774419"
---
Le operazioni di modifica seguenti sono supportate per il peering di Exchange
1. Aggiungere connessioni peering di Exchange
1. Rimuovere le connessioni peering di Exchange
1. Aggiungere la sessione IPv4/IPv6 alle connessioni attive.
1. Rimuovere la sessione IPv4/IPv6 in connessioni attive.


### <a name="add-exchange-peering-connections"></a>Aggiungere connessioni peering di Exchange

Nell'esempio seguente viene descritto come aggiungere connessioni al peering di Exchange esistente

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

Nell'esempio seguente viene descritto come rimuovere le connessioni al peering di Exchange esistente

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

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Aggiungi sessione IPv4/IPv6 in connessioni attive

Nell'esempio seguente viene illustrato come aggiungere una sessione IPv6 alla connessione Exchange esistente.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$exchangePeering.Connections[0].BgpSession.PeerSessionIPv6Address = "2001:504:12::34"

$exchangePeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Rimuovere la sessione IPv4/IPv6 in connessioni attive

La rimozione di una sessione IPv4/IPv6 da una connessione esistente non è attualmente supportata in PowerShell. Contattare il [peering Microsoft](mailto:peeringexperience@microsoft.com).