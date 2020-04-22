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
ms.openlocfilehash: 2ebf5d574b4d185953ab0f7984648c440d6f107e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678683"
---
Il cmdlet di PowerShell **Get-AzPeeringLocation** restituisce un `Kind`elenco di posizioni di peering con il parametro obbligatorio, che verrà usato nei passaggi successivi.

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

In questo esempio viene illustrato come utilizzare Seattle come percorso di peering per creare un peering.

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