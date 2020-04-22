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
ms.openlocfilehash: dbaa0b5fc87cb5393b323b8a9b7a38b72efe9518
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680792"
---
Il cmdlet di PowerShell **Get-AzPeeringLocation** restituisce un `Kind`elenco di posizioni di peering con il parametro obbligatorio, che verrà usato nei passaggi successivi.

```powershell
Get-AzPeeringLocation -Kind Direct
```

Le posizioni di peering diretto contengono i seguenti campi:
* PeeringLocation 
* Country
* PeeringDBFacilityIdPeeringDBFacilityId
* PeeringDBFacilityLink
* Larghezza di bandaOfferte

Verificare che la presenza sia nella struttura di peering desiderata facendo riferimento a [PeeringDB](https://wwww.peeringdb.com).

Questo esempio mostra come usare Seattle come posizione di peering per creare un peering diretto.

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