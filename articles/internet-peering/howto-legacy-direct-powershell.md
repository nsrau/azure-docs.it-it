---
title: Convertire un peering diretto legacy in risorse di Azure usando PowerShellConvert a legacy Direct peering to Azure resource using PowerShell
titleSuffix: Azure
description: Convertire un peering diretto legacy in risorse di Azure usando PowerShellConvert a legacy Direct peering to Azure resource using PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ba41f4ad8014ba3e85174b7c32e11394f0068643
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775017"
---
# <a name="convert-a-legacy-direct-peering-to-azure-resource-using-powershell"></a>Convertire un peering diretto legacy in risorse di Azure usando PowerShellConvert a legacy Direct peering to Azure resource using PowerShell

Questo articolo descrive come convertire un peering diretto legacy esistente in risorse di Azure usando i cmdlet di PowerShell.This article describes how to convert an existing legacy Direct peering to Azure resource using PowerShell cmdlets.

Se si preferisce, è possibile completare questa guida utilizzando il [portale](howto-legacy-direct-portal.md).

## <a name="before-you-begin"></a>Prima di iniziare
* Esaminare [i prerequisiti](prerequisites.md) e la procedura dettagliata di [peering diretto](walkthrough-direct-all.md) prima di iniziare la configurazione.

### <a name="working-with-azure-powershell"></a>Uso di Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-legacy-direct-peering-to-azure-resource"></a>Convertire il peering diretto legacy in una risorsa di AzureConvert legacy Direct peering to Azure resource

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Accedere al proprio account Azure e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-legacy-direct-peering-for-conversion"></a><a name= get></a>Ottieni il peering diretto legacy per la conversione
Di seguito è riportato un esempio per ottenere il peering diretto legacy nel percorso di peering di Seattle

```powershell
$legacyPeering = Get-AzLegacyPeering `
    -Kind Direct -PeeringLocation "Seattle"
$legacyPeering
```

Di seguito è riportata una risposta di esempio:Below is an example response:
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

### <a name="convert-legacy-direct-peering"></a>Convertire il peering diretto legacyConvert legacy Direct peering

&nbsp;
> [!IMPORTANT]
> Si noti che quando si converte il peering legacy in risorsa azure, le modifiche non sono supportate. &nbsp;

Usare il comando seguente per convertire il peering diretto legacy in una risorsa di Azure:Use below command to convert legacy Direct peering to Azure resource:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleDirectPeering" `
    -ResourceGroupName "PeeringResourceGroup" `

```

Di seguito è riportata una risposta di esempio:Below is an example response:

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
È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo il comando seguente:

```powershell
Get-Help Get-AzPeering -detailed
```

Per ulteriori informazioni, visita Le domande frequenti sul [peering Internet](faqs.md)

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare un peering diretto tramite PowerShell](howto-direct-powershell.md).
