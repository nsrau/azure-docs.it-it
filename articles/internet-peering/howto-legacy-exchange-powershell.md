---
title: Convertire un peering di Exchange legacy in una risorsa di Azure tramite PowerShellConvert a legacy Exchange peering to an Azure resource by using PowerShell
titleSuffix: Azure
description: Convertire un peering di Exchange legacy in una risorsa di Azure tramite PowerShellConvert a legacy Exchange peering to an Azure resource by using PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: eedf87548d62e05d4940911ed3dcd821077acb27
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686783"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-powershell"></a>Convertire un peering di Exchange legacy in una risorsa di Azure tramite PowerShellConvert a legacy Exchange peering to an Azure resource by using PowerShell

In questo articolo viene descritto come convertire un peering di Exchange legacy esistente in una risorsa di Azure usando i cmdlet di PowerShell.This article describes how to convert an existing legacy Exchange peering to an Azure resource by using PowerShell cmdlets.

Se si preferisce, è possibile completare questa guida usando il [portale](howto-legacy-exchange-portal.md)di Azure .

## <a name="before-you-begin"></a>Prima di iniziare
* Esaminare i [prerequisiti](prerequisites.md) e la procedura dettagliata di [peering di Exchange](walkthrough-exchange-all.md) prima di iniziare la configurazione.

### <a name="work-with-azure-powershell"></a>Usare Azure PowerShellWork with Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Convertire un peering di Exchange legacy in una risorsa di AzureConvert a legacy Exchange peering to an Azure resource

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Accedere al proprio account Azure e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-legacy-exchange-peering-for-conversion"></a><a name= get></a>Ottieni il peering legacy di Exchange per la conversione
In questo esempio viene illustrato come ottenere il peering legacy di Exchange nel percorso di peering di Seattle:

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
Questo comando può essere usato per convertire il peering di Exchange legacy in una risorsa di Azure:This command can be used to convert legacy Exchange peering to an Azure resource:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> Quando si converte il peering legacy in una risorsa di Azure, le modifiche non sono supportate.
&nbsp;

Questa risposta di esempio mostra quando il provisioning end-to-end è stato completato correttamente:This example response shows when the end-to-end provisioning was successfully completed:

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
Per ulteriori informazioni, vedere [Domande frequenti sul peering Internet](faqs.md).

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare un peering di Exchange tramite PowerShellCreate or modify an Exchange peering by using PowerShell](howto-exchange-powershell.md)
