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
ms.openlocfilehash: 6f5d2dc30ac0f6316587fa0836b87cbd4efc0a8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774393"
---
Il cmdlet di PowerShell **Get-AzPeeringLocation** restituisce un `Kind`elenco di posizioni di peering con il parametro obbligatorio, che verrà usato nei passaggi successivi:

```powershell
Get-AzPeeringLocation -Kind "Exchange"
```

I percorsi di peering di Exchange contengono i seguenti campi:
* Nome di Exchange
* PeeringLocation
* Country
* PeeringDBFacilityIdPeeringDBFacilityId
* PeeringDBFacilityLink
* MicrosoftIPv4Indirizzo
* MicrosoftIPv6Indirizzo

Verificare che si è presenti nella struttura di peering desiderata facendo riferimento a [PeeringDB](https://wwww.peeringdb.com).

Di seguito è riportato un esempio che mostra come usare Seattle come posizione di peering per creare un peering:Below is an example that shows how to use Seattle as the peering location to create a peering:

```powershell
$exchangeLocations = Get-AzPeeringLocation -Kind Exchange
$exchangeLocation = $exchangeLocations | where {$_.PeeringLocation -eq "Seattle"}

#check the location metadata
$exchangeLocation

ExchangeName          : Columbia IX
PeeringLocation       : Seattle
Country               : US
PeeringDBFacilityId   : 99999
PeeringDBFacilityLink : https://www.peeringdb.com/ix/99999
MicrosoftIPv4Address  : 10.12.97.129
MicrosoftIPv6Address  :

ExchangeName          : Equinix Seattle
PeeringLocation       : Seattle
Country               : US
PeeringDBFacilityId   : 11
PeeringDBFacilityLink : https://www.peeringdb.com/ix/11
MicrosoftIPv4Address  : 198.32.134.152
MicrosoftIPv6Address  : 2001:504:12::15

...

```