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
ms.openlocfilehash: 86d768db7a31c634bdaca6c93f633c7bbaf10a65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774289"
---
Il cmdlet di PowerShell **Get-AzPeeringLocation** restituisce un `Kind`elenco di posizioni di peering con il parametro obbligatorio, che verrà usato nei passaggi successivi:

```powershell
Get-AzPeeringLocation -Kind Direct
```

Le posizioni di peering diretto contengono i seguenti campi:
* PeeringLocation 
* Country
* PeeringDBFacilityIdPeeringDBFacilityId
* PeeringDBFacilityLink
* Larghezza di bandaOfferte

Verificare che si è presenti nella struttura di peering desiderata facendo riferimento a [PeeringDB](https://wwww.peeringdb.com).

Di seguito è riportato un esempio che mostra come usare Seattle come posizione di peering per creare un peering diretto:Below is an example that shows how to use Seattle as the peering location to create a Direct peering:

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