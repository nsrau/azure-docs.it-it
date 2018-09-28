---
title: 'Come configurare OpenVPN sul Gateway VPN di Azure: PowerShell | Microsoft Docs'
description: Procedura per configurare OpenVPN per il Gateway VPN di Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: b915a56b14332bfa885eaccf9a151d08c58dc5b1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973695"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway-preview"></a>Configurare OpenVPN per il Gateway VPN da punto a sito Web di Azure (anteprima)

Questo articolo descrive come configurare OpenVPN sul Gateway VPN di Azure. L'articolo presuppone che si disponga già di un ambiente di lavoro da punto a sito. In caso contrario, usare le istruzioni nel passaggio 1 per creare una VPN da punto a sito.

> [!IMPORTANT]
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="vnet"></a>1. Creare una VPN Point-to-Site

Se non è già presente un ambiente da punto a sito in funzione, seguire le istruzioni per crearne uno. Visualizzare [Creare una VPN Point-to-Site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) per creare e configurare un gateway VPN da punto a sito con autenticazione del certificato di Azure nativa.

## <a name="cmdlets"></a>2. Installare i cmdlet di PowerShell

Installare la versione più recente dei cmdlet di PowerShell per Resource Manager. Per altre informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview). Questo elemento è importante perché le versioni precedenti dei cmdlet non contengono i valori correnti necessari per questo esercizio.

## <a name="enable"></a>3. Abilitare OpenVPN nel gateway

Abilitare OpenVPN nel gateway Assicurarsi che il gateway sia già configurato per connessioni da punto a sito (IKEv2 o SSTP) prima di eseguire i comandi seguenti:

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Passaggi successivi

Per configurare i client per OpenVPN, vedere [Configurare i client OpenVPN](vpn-gateway-howto-openvpn-clients.md).