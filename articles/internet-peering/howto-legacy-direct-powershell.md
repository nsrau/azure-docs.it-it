---
title: Convertire un peering diretto legacy in una risorsa di Azure tramite PowerShell
titleSuffix: Azure
description: Convertire un peering diretto legacy in una risorsa di Azure tramite PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 5d2a8c910c9e384e137785bc1cd491bc85c7e7a8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678476"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-powershell"></a>Convertire un peering diretto legacy in una risorsa di Azure tramite PowerShell

Questo articolo descrive come convertire un peering diretto legacy esistente in una risorsa di Azure usando i cmdlet di PowerShell.

Se si preferisce, è possibile completare questa guida usando il [portale](howto-legacy-direct-portal.md)di Azure.

## <a name="before-you-begin"></a>Prima di iniziare
* Prima di iniziare la configurazione, esaminare i [prerequisiti](prerequisites.md) e la [procedura dettagliata relativa al peering diretto](walkthrough-direct-all.md) .

### <a name="work-with-azure-powershell"></a>Usare Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Convertire un peering diretto legacy in una risorsa di Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Accedere al proprio account Azure e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-a-legacy-direct-peering-for-conversion"></a><a name= get></a>Ottenere un peering diretto legacy per la conversione
Questo esempio illustra come ottenere un peering diretto Legacy nella località di peering di Seattle.

```powershell
$legacyPeering = Get-AzLegacyPeering `
    -Kind Direct -PeeringLocation "Seattle"
$legacyPeering
```

Di seguito è riportata una risposta di esempio:
```powershell
Name                       :
Sku                        : Basic_Direct_Free
Kind                       : Direct
PeeringLocation            : Seattle
UseForPeeringService       : False
PeerAsn.Id                 :
Connection                 : ------------------------
PeeringDBFacilityId        : 71
SessionPrefixIPv4          : 4.71.156.72/30
PeerSessionIPv4Address     : 4.71.156.73
MicrosoftIPv4Address       : 4.71.156.74
SessionStateV4             : Established
MaxPrefixesAdvertisedV4    : 20000
SessionPrefixIPv6          : 2001:1900:2100::1e10/126
MaxPrefixesAdvertisedV6    : 2000
ConnectionState            : Active
BandwidthInMbps            : 0
ProvisionedBandwidthInMbps : 20000
Connection                 : ------------------------
PeeringDBFacilityId        : 71
SessionPrefixIPv4          : 4.68.70.140/30
PeerSessionIPv4Address     : 4.68.70.141
MicrosoftIPv4Address       : 4.68.70.142
SessionStateV4             : Established
MaxPrefixesAdvertisedV4    : 20000
SessionPrefixIPv6          : 2001:1900:4:3::cc/126
PeerSessionIPv6Address     : 2001:1900:4:3::cd
MicrosoftIPv6Address       : 2001:1900:4:3::ce
SessionStateV6             : Established
MaxPrefixesAdvertisedV6    : 2000
ConnectionState            : Active
BandwidthInMbps            : 0
ProvisionedBandwidthInMbps : 20000
ProvisioningState          : Succeeded
```

### <a name="convert-a-legacy-direct-peering"></a>Convertire un peering diretto legacy

&nbsp;
> [!IMPORTANT]
> Quando si converte un peering legacy in una risorsa di Azure, le modifiche non sono supportate. &nbsp;

Usare questo comando per convertire un peering diretto legacy in una risorsa di Azure:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleDirectPeering" `
    -ResourceGroupName "PeeringResourceGroup" `

```

Di seguito è riportata una risposta di esempio:

```powershell
Name                 : SeattleDirectPeering
Sku.Name             : Basic_Direct_Free
Kind                 : Direct
Connections          : {11, 11}
PeerAsn.Id           : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{asnNumber}
UseForPeeringService : False
PeeringLocation      : Seattle
ProvisioningState    : Succeeded
Location             : centralus
Id                   : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleDirectPeering
Type                 : Microsoft.Peering/peerings
Tags                 : {}
```

## <a name="additional-resources"></a>Risorse aggiuntive
È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo questo comando:

```powershell
Get-Help Get-AzPeering -detailed
```

Per altre informazioni, vedere [domande frequenti sul peering Internet](faqs.md).

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare un peering diretto usando PowerShell](howto-direct-powershell.md)
