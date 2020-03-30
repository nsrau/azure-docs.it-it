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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774419"
---
Sono supportate le seguenti operazioni di modifica per il peering di Exchange
1. Aggiungere connessioni di peering di Exchange
1. Rimuovere le connessioni di peering di Exchange
1. Aggiungere una sessione IPv4/IPv6 su connessioni attive.
1. Rimuovere la sessione IPv4/IPv6 nelle connessioni attive.


### <a name="add-exchange-peering-connections"></a>Aggiungere connessioni di peering di Exchange

Di seguito viene descritto come aggiungere connessioni al peering di Exchange esistente

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

Di seguito viene descritto come rimuovere le connessioni al peering di Exchange esistente

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

Nel comando seguente, invece di 0, immettere il numero di indice per la connessione che si desidera rimuovere.

```powershell

$connection = $exchangePeering.Connections[0]

$exchangePeering.Connections.Remove($connection)

$exchangePeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Aggiungi sessione IPv4/IPv6 su connessioni attive

Di seguito viene descritto come aggiungere una sessione IPv6 alla connessione di scambio esistente.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$exchangePeering.Connections[0].BgpSession.PeerSessionIPv6Address = "2001:504:12::34"

$exchangePeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Rimuovere la sessione IPv4/IPv6 sulle connessioni attive

La rimozione di una sessione IPv4/IPv6 da una connessione esistente non è attualmente supportata in PowerShell.Removing an IPv4/IPv6 session from an existing connection is not currently supported on PowerShell. Contattare [Microsoft peering](mailto:peeringexperience@microsoft.com).