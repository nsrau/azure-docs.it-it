---
title: 'Creare e impostare criteri IPsec personalizzati per la configurazione da punto a sito: PowerShell'
titleSuffix: Azure VPN Gateway
description: Questo articolo consente di creare e impostare criteri IPSec personalizzati per le configurazioni P2S del gateway VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/09/2020
ms.author: cherylmc
ms.openlocfilehash: 852ff5e6ad847ff33f8e32b4c3d9f8b325b80716
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89664519"
---
# <a name="create-and-set-custom-ipsec-policies-for-point-to-site"></a>Creare e impostare criteri IPsec personalizzati per la configurazione da punto a sito

Se l'ambiente richiede un criterio IPsec personalizzato per la crittografia, è possibile configurare facilmente un oggetto criteri con le impostazioni necessarie. Questo articolo consente di creare un oggetto criteri personalizzato e quindi di impostarlo usando PowerShell.

## <a name="before-you-begin"></a>Prima di iniziare

### <a name="prerequisites"></a>Prerequisiti

Verificare che l'ambiente soddisfi i prerequisiti seguenti:

* È già stata configurata una VPN da punto a sito funzionante. In caso contrario, configurarne uno seguendo la procedura illustrata **in creare un articolo VPN da punto a sito**  usando [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)o il [portale di Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

### <a name="working-with-azure-powershell"></a>Uso di Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="1-set-variables"></a><a name="signin"></a>1. impostare le variabili

Dichiarare le variabili da usare. Usare l'esempio seguente, sostituendo i valori personalizzati, se necessario. Se si chiude la sessione di PowerShell/Cloud Shell in qualsiasi momento durante l'esercizio, è sufficiente copiare e incollare nuovamente i valori per dichiarare di nuovo le variabili.

  ```azurepowershell-interactive
  $RG = "TestRG"
  $GWName = "VNet1GW"
  ```

## <a name="2-create-policy-object"></a><a name="create"></a>2. creare un oggetto Criteri

Creare un oggetto criterio IPsec personalizzato. È possibile modificare i valori per soddisfare i criteri richiesti.

```azurepowershell-interactive
$vpnclientipsecpolicy = New-AzVpnClientIpsecPolicy -IpsecEncryption AES256 -IpsecIntegrity SHA256 -SALifeTime 86471 -SADataSize 429496 -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup2 -PfsGroup PFS2
```

## <a name="3-update-gateway-and-set-policy"></a><a name="update"></a>3. aggiornare il gateway e impostare i criteri

In questo passaggio aggiornare il gateway VPN P2S esistente e impostare i criteri IPsec.

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway -VpnClientIpsecPolicy $vpnclientipsecpolicy
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle configurazioni P2S, vedere [informazioni sulla VPN da punto a sito](point-to-site-about.md).