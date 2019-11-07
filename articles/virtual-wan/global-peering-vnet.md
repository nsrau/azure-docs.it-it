---
title: Configurare il peering VNet globale per la rete WAN virtuale di Azure | Microsoft Docs
description: Connettere una VNet in un'area diversa all'hub WAN virtuale.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 340472f84d2dd2c4f46d180992745a57e8ad1884
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588226"
---
# <a name="configure-global-vnet-peering-cross-region-vnet-for-virtual-wan"></a>Configurare il peering VNet globale (VNet tra più aree) per la rete WAN virtuale

È possibile connettere una VNet in un'area diversa all'hub WAN virtuale.

## <a name="before-you-begin"></a>Prima di iniziare

Verificare di aver soddisfatto i criteri seguenti:

* VNet tra aree diverse (spoke) non è connesso a un altro hub WAN virtuale. Un spoke può essere connesso a un solo hub virtuale.
* VNet (spoke) non contiene un gateway di rete virtuale, ad esempio un gateway VPN di Azure o un gateway di rete virtuale ExpressRoute. Se il VNet contiene un gateway di rete virtuale, è necessario rimuovere il gateway prima di connettere il VNet spoke all'hub.

## <a name="register"></a>Registrare questa funzionalità

È possibile eseguire la registrazione per questa funzionalità usando PowerShell. Se si seleziona "prova" nell'esempio seguente, Azure cloud-Shell si apre e non è necessario installare i cmdlet di PowerShell in locale nel computer. Se necessario, è possibile modificare le sottoscrizioni usando il cmdlet ' Select-AzSubscription-SubscriptionId <subid>'.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Network'
```

## <a name="verify"></a>Verifica registrazione

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
```

## <a name="hub"></a>Connettere un VNet all'hub

In questo passaggio viene creata la connessione peering tra l'hub e la VNet tra aree. Ripetere questi passaggi per ogni rete virtuale a cui ci si vuole connettere.

1. Nella pagina della rete WAN virtuale fare clic su **Connessioni rete virtuale**.
2. Nella pagina di connessione alla rete virtuale fare clic su **+ Aggiungi connessione**.
3. Nella pagina **Aggiungi connessione** compilare i campi seguenti:

    * **Nome connessione** - Specificare un nome per la connessione.
    * **Hub** - Selezionare l'hub che si vuole associare a questa connessione.
    * **Sottoscrizione** - Verificare la sottoscrizione.
    * **Rete virtuale** - Selezionare la rete virtuale che si vuole connettere all'hub. La rete virtuale non può avere un gateway di rete virtuale già esistente.
4. Fare clic su **OK** per creare la connessione peering.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere [Panoramica di WAN virtuale](virtual-wan-about.md).