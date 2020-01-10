---
title: Convertire un peering di Exchange legacy in una risorsa di Azure usando PowerShell
titleSuffix: Azure
description: Convertire un peering di Exchange legacy in una risorsa di Azure usando PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: aa11f329cf0a0cb27d58b940b42731a2ec41c272
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775394"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-powershell"></a>Convertire un peering di Exchange legacy in una risorsa di Azure usando PowerShell

Questo articolo descrive come convertire un peering di Exchange legacy esistente in una risorsa di Azure usando i cmdlet di PowerShell.

Se si preferisce, è possibile completare questa guida usando il [portale](howto-legacy-exchange-portal.md).

## <a name="before-you-begin"></a>Prima di iniziare
* Esaminare i [prerequisiti](prerequisites.md) e la [procedura dettagliata per il peering di Exchange](walkthrough-exchange-all.md) prima di iniziare la configurazione.

### <a name="working-with-azure-powershell"></a>Uso di Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Convertire un peering di Exchange legacy in una risorsa di Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Accedere al proprio account Azure e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name= get></a>Ottenere peering Exchange legacy per la conversione
Di seguito è riportato l'esempio per ottenere il peering di Exchange legacy nella località di peering di Seattle:

```powershell
$legacyPeering = Get-AzLegacyPeering -Kind Exchange -PeeringLocation "Seattle"
$legacyPeering
```

La risposta sarà simile all'esempio di seguito:
```powershell
    Kind                     : Exchange
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```

### <a name="convert-legacy-peering"></a>Converti peering legacy
Il comando seguente può essere usato per convertire il peering di Exchange legacy in una risorsa di Azure:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> Si noti che quando si converte il peering legacy in una risorsa di Azure, le modifiche non sono supportate &nbsp;

Di seguito è riportato un esempio di risposta quando il provisioning end-to-end è stato completato correttamente:

```powershell
    Name                     : SeattleExchangePeering
    Kind                     : Exchange
    Sku                      : Basic_Exchange_Free
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```
## <a name="additional-resources"></a>Risorse aggiuntive
È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo il comando seguente:

```powershell
Get-Help Get-AzPeering -detailed
```
Per altre informazioni, vedere [domande frequenti sul peering Internet](faqs.md)

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare un peering di Exchange con PowerShell](howto-exchange-powershell.md)
