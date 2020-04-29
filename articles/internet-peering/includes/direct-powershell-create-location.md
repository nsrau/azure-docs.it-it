---
title: includere il file
titleSuffix: Azure
description: File di inclusione
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: dbaa0b5fc87cb5393b323b8a9b7a38b72efe9518
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680792"
---
Il cmdlet di PowerShell **Get-AzPeeringLocation** restituisce un elenco di percorsi di peering con il `Kind`parametro obbligatorio, che verrà usato nei passaggi successivi.

```powershell
Get-AzPeeringLocation -Kind Direct
```

Le località di peering dirette contengono i campi seguenti:
* PeeringLocation 
* Country
* PeeringDBFacilityId
* PeeringDBFacilityLink
* BandwidthOffers

Verificare di essere presenti nella struttura di peering desiderata facendo riferimento a [PeeringDB](https://wwww.peeringdb.com).

Questo esempio illustra come usare Seattle come posizione di peering per creare un peering diretto.

```powershell
$peeringLocations = Get-AzPeeringLocation -Kind Direct
$peeringLocation = $peeringLocations | where {$_.PeeringLocation -contains "Seattle"}
$peeringLocation

PeeringLocation       : Seattle
Address               : 2001 Sixth Avenue
Country               : US
PeeringDBFacilityId   : 71
PeeringDBFacilityLink : https://www.peeringdb.com/fac/71
BandwidthOffers       : {10Gbps, 100Gbps}
```