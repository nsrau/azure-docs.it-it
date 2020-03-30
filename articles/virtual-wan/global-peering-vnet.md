---
title: Configurare il peering della rete virtuale globale per la rete WAN virtuale di Azure Documenti Microsoft
description: Connettere una rete virtuale in un'area diversa all'hub WAN virtuale.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 340472f84d2dd2c4f46d180992745a57e8ad1884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73588226"
---
# <a name="configure-global-vnet-peering-cross-region-vnet-for-virtual-wan"></a>Configurare il peering della rete virtuale globale (vNet tra aree) per la rete WAN virtuale

È possibile connettere una rete virtuale in un'area diversa all'hub WAN virtuale.

## <a name="before-you-begin"></a>Prima di iniziare

Verificare di aver soddisfatto i criteri seguenti:

* La rete virtuale tra aree (spoke) non è connessa a un altro hub WAN virtuale. Un raggio può essere connesso a un solo hub virtuale.
* La rete virtuale (spoke) non contiene un gateway di rete virtuale (ad esempio, un gateway VPN di Azure o un gateway di rete virtuale ExpressRoute). Se la rete virtuale contiene un gateway di rete virtuale, è necessario rimuovere il gateway prima di connettere la rete virtuale a raggio all'hub.

## <a name="register-this-feature"></a><a name="register"></a>Registrare questa funzionalità

È possibile eseguire la registrazione per questa funzionalità usando PowerShell.You can register for this feature using PowerShell. Se si seleziona "Prova" nell'esempio seguente, azure Cloud-Shell si apre e non sarà necessario installare i cmdlet di PowerShell in locale nel computer. Se necessario, è possibile modificare le sottoscrizioni utilizzando <subid>il cmdlet 'Select-AzSubscription -SubscriptionId'.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Network'
```

## <a name="verify-registration"></a><a name="verify"></a>Verificare la registrazione

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
```

## <a name="connect-a-vnet-to-the-hub"></a><a name="hub"></a>Connettere una rete virtuale all'hub

In questo passaggio viene creata la connessione peering tra l'hub e la rete virtuale tra aree. Ripetere questi passaggi per ogni rete virtuale a cui ci si vuole connettere.

1. Nella pagina della rete WAN virtuale fare clic su **Connessioni rete virtuale**.
2. Nella pagina di connessione alla rete virtuale fare clic su **+ Aggiungi connessione**.
3. Nella pagina **Aggiungi connessione** compilare i campi seguenti:

    * **Nome connessione** - Specificare un nome per la connessione.
    * **Hub** - Selezionare l'hub che si vuole associare a questa connessione.
    * **Sottoscrizione** - Verificare la sottoscrizione.
    * **Rete virtuale** - Selezionare la rete virtuale che si vuole connettere all'hub. La rete virtuale non può avere un gateway di rete virtuale già esistente.
4. Fare clic su **OK** per creare la connessione peering.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla rete WAN virtuale, vedere [Panoramica della rete WAN virtuale](virtual-wan-about.md).