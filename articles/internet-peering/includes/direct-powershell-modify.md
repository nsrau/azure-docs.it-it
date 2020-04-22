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
ms.openlocfilehash: b2609a069872ec55ac9068fadcbb3f312d68a630
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680824"
---
In questa sezione viene descritto come eseguire le operazioni di modifica seguenti per il peering diretto:This section describes how to perform the following modification operations for Direct peering:

* Aggiungere connessioni di peering diretto.
* Rimuovere le connessioni di peering diretto.
* Aggiornare o eseguire il downgrade della larghezza di banda nelle connessioni attive.
* Aggiungere sessioni IPv4 o IPv6 su connessioni attive.
* Rimuovere le sessioni IPv4 o IPv6 sulle connessioni attive.

### <a name="add-direct-peering-connections"></a>Aggiungere connessioni di peering direttoAdd Direct peering connections

In questo esempio viene descritto come aggiungere connessioni al peering diretto esistente.

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

La rimozione di una connessione non è attualmente supportata in PowerShell.Removing a connection isn't currently supported on PowerShell. Per ulteriori informazioni, [contattare Microsoft peering](mailto:peeringexperience@microsoft.com).

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Aggiornare o ridurre la larghezza di banda nelle connessioni attiveUpgrade or downgrade bandwidth on Active connections

In questo esempio viene descritto come aggiungere 10 Gbps a una connessione Direct esistente.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4-or-ipv6-sessions-on-active-connections"></a>Aggiungere sessioni IPv4 o IPv6 su connessioni attive

In questo esempio viene descritto come aggiungere una sessione IPv6 a una connessione diretta esistente con solo una sessione IPv4. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4-or-ipv6-sessions-on-active-connections"></a>Rimuovere sessioni IPv4 o IPv6 su connessioni attive

La rimozione di una sessione IPv4 o IPv6 da una connessione esistente non è attualmente supportata in PowerShell.Removing an IPv4 or IPv6 session from an existing connection isn't currently supported on PowerShell. Per ulteriori informazioni, [contattare Microsoft peering](mailto:peeringexperience@microsoft.com).