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
ms.openlocfilehash: 03c67ccf88a8c73fe04f062c6af9520115c185a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774263"
---
In questa sezione viene descritto come eseguire le operazioni di modifica seguenti per il peering diretto:This section describes how to perform the following modification operations for Direct peering:

* Aggiungere connessioni di peering direttoAdd Direct peering connections
* Rimuovere le connessioni di peering direttoRemove Direct peering connections
* Aggiornare o eseguire il downgrade della larghezza di banda nelle connessioni attive.
* Aggiungere una sessione IPv4/IPv6 su connessioni attive.
* Rimuovere la sessione IPv4/IPv6 nelle connessioni attive.

### <a name="add-direct-peering-connections"></a>Aggiungere connessioni di peering direttoAdd Direct peering connections

Di seguito viene descritto come aggiungere connessioni al peering diretto esistente

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringDirectConnection `
    -PeeringDBFacilityId $peeringLocation.PeeringDBFacilityId `
    -SessionPrefixV4 "10.22.31.0/31" `
    -SessionPrefixV6 "fe02::3e:0/127" `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000

$directPeering.Connections.Add($connection)

$directPeering | Update-AzPeering
```

### <a name="remove-direct-peering-connections"></a>Rimuovere le connessioni di peering direttoRemove Direct peering connections

La rimozione di una connessione non è attualmente supportata in PowerShell.Removing a connection is not currently supported on PowerShell. Contattare [Microsoft peering](mailto:peeringexperience@microsoft.com).

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Aggiornare o ridurre la larghezza di banda nelle connessioni attiveUpgrade or downgrade bandwidth on Active connections

Di seguito viene descritto come aggiungere 10 Gbps alla connessione diretta esistente.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Aggiungere una sessione IPv4/IPv6 su connessioni attive.

Di seguito viene descritto come aggiungere una sessione IPv6 a una connessione diretta esistente con solo una sessione IPv4. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Rimuovere la sessione IPv4/IPv6 nelle connessioni attive.

La rimozione di una sessione IPv4/IPv6 da una connessione esistente non è attualmente supportata in PowerShell.Removing an IPv4/IPv6 session from an existing connection is not currently supported on PowerShell. Contattare [Microsoft peering](mailto:peeringexperience@microsoft.com).