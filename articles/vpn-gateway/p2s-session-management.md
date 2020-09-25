---
title: 'Gateway VPN di Azure: gestione delle sessioni VPN da punto a sito'
description: Questo articolo consente di visualizzare e disconnettere le sessioni VPN da punto a sito.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/23/2020
ms.author: cherylmc
ms.openlocfilehash: 2f2184507e17e3ecae40bb33be4202c183d32b77
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91274234"
---
# <a name="point-to-site-vpn-session-management"></a>Gestione delle sessioni VPN da punto a sito

I gateway di rete virtuale di Azure consentono di visualizzare e disconnettere in modo semplice le sessioni VPN da punto a sito correnti. Questo articolo consente di visualizzare e disconnettere le sessioni correnti.

>[!NOTE]
>Lo stato della sessione viene aggiornato ogni 5 minuti. Non viene aggiornato immediatamente.
>

## <a name="portal"></a>Portale

Per visualizzare e disconnettere una sessione nel portale:

1. Passare al gateway VPN.
1. Nella sezione **monitoraggio** selezionare **sessioni da punto a sito**.

   :::image type="content" source="./media/p2s-session-management/portal.png" alt-text="Esempio di portale":::
1. È possibile visualizzare tutte le sessioni correnti nella finestra di vetro.
1. Selezionare **"..."** per la sessione che si desidera disconnettere, quindi selezionare **Disconnetti**.

## <a name="powershell"></a>PowerShell

Per visualizzare e disconnettere una sessione usando PowerShell:

1. Eseguire il comando di PowerShell seguente per elencare le sessioni attive:

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGatewayVpnClientConnectionHealth -VirtualNetworkGatewayName <name of the gateway>  -ResourceGroupName <name of the resource group>
   ```
1. Copiare il **VpnConnectionId** della sessione che si desidera disconnettere.

   :::image type="content" source="./media/p2s-session-management/powershell.png" alt-text="Esempio di PowerShell":::
1. Per disconnettere la sessione, eseguire il comando seguente:

   ```azurepowershell-interactive
   Disconnect-AzVirtualNetworkGatewayVpnConnection -VirtualNetworkGatewayName <name of the gateway> -ResourceGroupName <name of the resource group> -VpnConnectionId <VpnConnectionId of the session>
   ```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle connessioni da punto a sito, vedere [informazioni sulla VPN da punto a sito](point-to-site-about.md).
