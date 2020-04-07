---
title: 'Gateway VPN di Azure: annunciare route personalizzate per i client VPN P2SAzure VPN Gateway: Advertise custom routes for P2S VPN clients'
description: Passaggi per pubblicizzare percorsi personalizzati per i client da punto a sito
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/11/2019
ms.author: cherylmc
ms.openlocfilehash: 3588755e2aab1c84d443e917eca8c7fca280b49a
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756888"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>Pubblicizzare percorsi personalizzati per i client VPN P2S

È possibile pubblicizzare percorsi personalizzati a tutti i client VPN da punto a sito. Ad esempio, dopo aver abilitato gli endpoint di archiviazione nella rete virtuale e si desidera che gli utenti remoti siano in grado di accedere a questi account di archiviazione tramite la connessione VPN. È possibile annunciare l'indirizzo IP dell'area di archiviazione a tutti gli utenti remoti in modo che il traffico verso l'account di archiviazione passi oltre il tunnel VPN e non la rete Internet pubblica.

![Esempio di connessione gateway VPN di Azure multisito](./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png)

## <a name="to-advertise-custom-routes"></a>Per annunciare percorsi personalizzati

Per annunciare percorsi `Set-AzVirtualNetworkGateway cmdlet`personalizzati, utilizzare il file . Nell'esempio seguente viene illustrato come annunciare l'indirizzo IP per le [tabelle dell'account di archiviazione Contoso.](https://contoso.table.core.windows.net)

1. Eseguire *il contoso.table.core.windows.net* e prendere nota dell'indirizzo IP. Ad esempio:

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. Eseguire i comandi di PowerShell seguenti:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. Per aggiungere più percorsi personalizzati, utilizzare una virgola e spazi per separare gli indirizzi. Ad esempio:

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>Per visualizzare percorsi personalizzati

Utilizzare l'esempio seguente per visualizzare le route personalizzate:Use the following example to view custom routes:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```
## <a name="to-delete-custom-routes"></a>Per eliminare percorsi personalizzati

Utilizzare l'esempio seguente per eliminare route personalizzate:Use the following example to delete custom routes:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute @0
  ```
## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul routing P2S, vedere [Informazioni sul routing da punto a sito](vpn-gateway-about-point-to-site-routing.md).
