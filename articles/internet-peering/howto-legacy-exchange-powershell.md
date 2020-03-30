---
title: Convertire un peering di Exchange legacy in una risorsa di Azure usando PowerShellConvert a legacy Exchange peering to Azure resource using PowerShell
titleSuffix: Azure
description: Convertire un peering di Exchange legacy in una risorsa di Azure usando PowerShellConvert a legacy Exchange peering to Azure resource using PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: aa11f329cf0a0cb27d58b940b42731a2ec41c272
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775394"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-powershell"></a>Convertire un peering di Exchange legacy in una risorsa di Azure usando PowerShellConvert a legacy Exchange peering to Azure resource using PowerShell

In questo articolo viene descritto come convertire un peering di Exchange legacy esistente in risorse di Azure usando i cmdlet di PowerShell.This article describes how to convert an existing legacy Exchange peering to Azure resource using PowerShell cmdlets.

Se si preferisce, è possibile completare questa guida utilizzando il [portale](howto-legacy-exchange-portal.md).

## <a name="before-you-begin"></a>Prima di iniziare
* Esaminare [i prerequisiti](prerequisites.md) e la procedura dettagliata di [peering di Exchange](walkthrough-exchange-all.md) prima di iniziare la configurazione.

### <a name="working-with-azure-powershell"></a>Uso di Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Convertire un peering di Exchange legacy in una risorsa di Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Accedere al proprio account Azure e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-legacy-exchange-peering-for-conversion"></a><a name= get></a>Ottieni il peering legacy di Exchange per la conversione
Di seguito è riportato l'esempio per ottenere il peering legacy di Exchange nel percorso di peering di Seattle:

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

### <a name="convert-legacy-peering"></a>Convertire il peering legacyConvert legacy peering
Il comando seguente può essere usato per convertire il peering di Exchange legacy in una risorsa di Azure:Below command can be used to convert legacy Exchange peering to Azure resource:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> Si noti che quando si converte il peering legacy in risorsa azure, le modifiche non sono supportateNote that when converting legacy peering to azure resource, modifications are not supported&nbsp;

Di seguito è riportata una risposta di esempio quando il provisioning end-to-end è stato completato correttamente:Below is an example response when the end-to-end provisioning was successfully completed:

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
Per ulteriori informazioni, visita Le domande frequenti sul [peering Internet](faqs.md)

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare un peering di Exchange tramite PowerShellCreate or modify an Exchange peering using PowerShell](howto-exchange-powershell.md)
