---
title: 'Esercitazione: Usare la rete WAN virtuale di Azure per creare una connessione da punto a sito ad Azure'
description: In questa esercitazione si vedrà come usare la rete WAN virtuale di Azure per creare una connessione VPN da punto a sito ad Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/06/2020
ms.author: cherylmc
ms.openlocfilehash: 3d03d0267ff4fb16042d5cc2016e87139b88281a
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92056583"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Esercitazione: Creare una connessione VNP utente usando la rete WAN virtuale di Azure

Questa esercitazione illustra come usare la rete WAN virtuale per connettersi alle risorse in Azure tramite una connessione VPN IPSec/IKE (IKEv2) o OpenVPN. Questo tipo di connessione richiede la configurazione di un client nel computer client. Per altre informazioni sulla rete WAN virtuale, vedere la [panoramica sulla rete WAN virtuale](virtual-wan-about.md).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare una rete WAN
> * Creare una configurazione da punto a sito
> * Creare un hub
> * Specificare i server DNS
> * Scaricare un profilo client VPN
> * Visualizzare la rete WAN virtuale

![Diagramma della rete WAN virtuale](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Before beginning](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Creare una rete WAN virtuale

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Creare una configurazione da punto a sito

Una configurazione da punto a sito definisce i parametri per la connessione di client remoti.

[!INCLUDE [Create client profiles](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-hub-with-point-to-site-gateway"></a><a name="hub"></a>Creare un hub con un gateway da punto a sito

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="specify-dns-server"></a><a name="dns"></a>Specificare il server DNS

I gateway VPN utente della rete WAN virtuale consentono di specificare fino a 5 server DNS. È possibile eseguire questa configurazione durante il processo di creazione dell'hub oppure modificarla in un secondo momento. A questo scopo, individuare l'hub virtuale. In **VPN utente (da punto a sito)** selezionare **Configura** e immettere gli indirizzi IP dei server DNS nelle caselle di testo **Server DNS personalizzati**.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="DNS personalizzato" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="download-vpn-profile"></a><a name="download"></a>Scaricare il profilo VPN

Usare il profilo VPN per configurare i client.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

### <a name="configure-user-vpn-clients"></a>Configurare i client VPN utente

Usare il profilo scaricato per configurare i client di accesso remoto. La procedura per ogni sistema operativo è diversa; seguire le istruzioni applicabili al sistema in uso.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Visualizzare la rete WAN virtuale

1. Passare alla rete WAN virtuale.
1. Nella pagina **Panoramica** ogni punto sulla mappa rappresenta un hub.
1. Nella sezione **Hub e connessioni** è possibile visualizzare lo stato dell'hub, il sito, l'area, lo stato della connessione VPN e i byte in entrata e in uscita.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Pulire le risorse

Quando queste risorse non sono più necessarie, è possibile usare [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuovere il gruppo di risorse e tutte le risorse in esso contenute. Sostituire "myResourceGroup" con il nome del gruppo di risorse specifico ed eseguire il comando di PowerShell seguente:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere:

> [!div class="nextstepaction"]
> * [Domande frequenti sulla rete WAN virtuale](virtual-wan-faq.md)
