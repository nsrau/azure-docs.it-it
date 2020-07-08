---
title: includere il file
titleSuffix: Azure
description: includere file
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: b2609a069872ec55ac9068fadcbb3f312d68a630
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81680824"
---
Questa sezione descrive come eseguire le operazioni di modifica seguenti per il peering diretto:

* Aggiungere connessioni di peering diretto.
* Rimuovere le connessioni peering diretto.
* Aggiornare o effettuare il downgrade della larghezza di banda nelle connessioni attive.
* Aggiungere sessioni IPv4 o IPv6 in connessioni attive.
* Rimuovere le sessioni IPv4 o IPv6 in connessioni attive.

### <a name="add-direct-peering-connections"></a>Aggiungi connessioni peering diretto

Questo esempio descrive come aggiungere connessioni al peering diretto esistente.

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

La rimozione di una connessione non è attualmente supportata in PowerShell. Per ulteriori informazioni, contattare il [peering Microsoft](mailto:peeringexperience@microsoft.com).

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Aggiornare o effettuare il downgrade della larghezza di banda nelle connessioni attive

Questo esempio descrive come aggiungere 10 Gbps a una connessione diretta esistente.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4-or-ipv6-sessions-on-active-connections"></a>Aggiungere sessioni IPv4 o IPv6 in connessioni attive

Questo esempio illustra come aggiungere una sessione IPv6 in una connessione diretta esistente solo con una sessione IPv4. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4-or-ipv6-sessions-on-active-connections"></a>Rimuovere sessioni IPv4 o IPv6 in connessioni attive

La rimozione di una sessione IPv4 o IPv6 da una connessione esistente non è attualmente supportata in PowerShell. Per ulteriori informazioni, contattare il [peering Microsoft](mailto:peeringexperience@microsoft.com).