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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774263"
---
Questa sezione descrive come eseguire le operazioni di modifica seguenti per il peering diretto:

* Aggiungi connessioni peering diretto
* Rimuovere connessioni peering dirette
* Aggiornare o effettuare il downgrade della larghezza di banda nelle connessioni attive.
* Aggiungere la sessione IPv4/IPv6 alle connessioni attive.
* Rimuovere la sessione IPv4/IPv6 in connessioni attive.

### <a name="add-direct-peering-connections"></a>Aggiungi connessioni peering diretto

Nell'esempio seguente viene descritto come aggiungere connessioni al peering diretto esistente

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

### <a name="remove-direct-peering-connections"></a>Rimuovere connessioni peering dirette

La rimozione di una connessione non è attualmente supportata in PowerShell. Contattare il [peering Microsoft](mailto:peeringexperience@microsoft.com).

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Aggiornare o effettuare il downgrade della larghezza di banda nelle connessioni attive

Nell'esempio seguente viene descritto come aggiungere 10Gbps alla connessione diretta esistente.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Aggiungere la sessione IPv4/IPv6 alle connessioni attive.

Nell'esempio seguente viene illustrato come aggiungere una sessione IPv6 in una connessione diretta esistente solo con una sessione IPv4. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Rimuovere la sessione IPv4/IPv6 in connessioni attive.

La rimozione di una sessione IPv4/IPv6 da una connessione esistente non è attualmente supportata in PowerShell. Contattare il [peering Microsoft](mailto:peeringexperience@microsoft.com).