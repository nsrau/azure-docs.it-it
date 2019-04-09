---
title: 'Procedura per configurare OpenVPN in Gateway VPN di Azure: PowerShell | Microsoft Docs'
description: Procedura per configurare OpenVPN per il Gateway VPN di Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: cherylmc
ms.openlocfilehash: 8cc2a6d4ad06bf4a55d4ef078970823df1e0d910
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281965"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway-preview"></a>Configurare OpenVPN per il Gateway VPN da punto a sito Web di Azure (anteprima)

Questo articolo consente di configurare **OpenVPN® protocollo** sul Gateway VPN di Azure. L'articolo presuppone che si disponga già di un ambiente di lavoro da punto a sito. In caso contrario, usare le istruzioni nel passaggio 1 per creare una VPN da punto a sito.

> [!IMPORTANT]
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="register"></a>Registrare questa funzionalità

Fare clic sul pulsante **Prova** in questi passaggi per registrare facilmente questa funzionalità tramite Azure Cloud Shell.

>[!NOTE]
>Se non si registra la funzionalità, non sarà possibile usarla.
>

Dopo aver fatto clic su **Prova** per aprire Azure Cloud Shell, copiare e incollare i comandi seguenti:

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```
 
```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

Una volta che la funzionalità viene contrassegnata come registrata, registrare nuovamente la sottoscrizione nello spazio dei nomi Microsoft.Network.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="vnet"></a>1. Creare una VPN Point-to-Site

Se non è già presente un ambiente da punto a sito in funzione, seguire le istruzioni per crearne uno. Visualizzare [Creare una VPN Point-to-Site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) per creare e configurare un gateway VPN da punto a sito con autenticazione del certificato di Azure nativa. 

> [!IMPORTANT]
> Lo SKU Basic non è supportato per OpenVPN.

## <a name="enable"></a>2. Abilitare OpenVPN nel gateway

Abilitare OpenVPN nel gateway Assicurarsi che il gateway sia già configurato per connessioni da punto a sito (IKEv2 o SSTP) prima di eseguire i comandi seguenti:

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Passaggi successivi

Per configurare i client per OpenVPN, vedere [Configurare i client OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" è un marchio registrato di OpenVPN Inc.**