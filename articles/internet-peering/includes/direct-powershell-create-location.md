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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774289"
---
Il cmdlet **Get-AzPeeringLocation** di PowerShell restituisce un elenco di percorsi di peering con il parametro obbligatorio `Kind`, che verrà usato nei passaggi successivi:

```powershell
Get-AzPeeringLocation -Kind Direct
```

Le località di peering dirette contengono i campi seguenti:
* PeeringLocation 
* Paese
* PeeringDBFacilityId
* PeeringDBFacilityLink
* BandwidthOffers

Verificare di essere presenti nella struttura di peering desiderata facendo riferimento a [PeeringDB](https://w www.peeringdb.com).

Di seguito è riportato un esempio che illustra come usare Seattle come posizione di peering per creare un peering diretto:

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