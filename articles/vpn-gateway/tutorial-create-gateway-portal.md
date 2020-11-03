---
title: 'Esercitazione - Creare e gestire un gateway VPN: Portale di Azure'
description: Seguire questa esercitazione per apprendere come creare, distribuire e gestire un gateway VPN di Azure tramite il portale.
author: cherylmc
ms.author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 10/23/2020
ms.openlocfilehash: 74000322372652b90e0d0db07d687f8e57c4f88e
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518246"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-azure-portal"></a>Esercitazione: Creare e gestire un gateway VPN usando il portale di Azure

I gateway VPN di Azure offrono connettività cross-premise tra l'infrastruttura locale del cliente e Azure. Questa esercitazione illustra gli elementi di base della distribuzione di un gateway VPN di Azure, ad esempio la creazione e la gestione di un gateway VPN.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Crea rete virtuale
> * Creare un gateway VPN
> * Visualizzare l'indirizzo IP pubblico del gateway
> * Ridimensionare un gateway VPN (ridimensionare lo SKU)
> * Reimpostare un gateway VPN

Il diagramma seguente mostra la rete virtuale e il gateway VPN creati nell'ambito di questa esercitazione.

:::image type="content" source="./media/tutorial-create-gateway-portal/gateway-diagram.png" alt-text="Diagramma della rete virtuale e del gateway VPN":::

## <a name="prerequisites"></a>Prerequisiti

Un account Azure con una sottoscrizione attiva. Se non è disponibile, [crearne uno gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Creare una rete virtuale

Creare una rete virtuale con i valori seguenti:

* **Gruppo di risorse:** TestRG1
* **Nome:** VNet1
* **Area:** (Stati Uniti) Stati Uniti orientali
* **Spazio indirizzi IPv4:** 10.1.0.0/16
* **Nome della subnet:** FrontEnd
* **Spazio indirizzi della subnet:** 10.1.0.0/24

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-a-vpn-gateway"></a><a name="VNetGateway"></a>Creare un gateway VPN

Questo passaggio illustra come creare il gateway di rete virtuale per la rete virtuale. La creazione di un gateway spesso richiede anche più di 45 minuti di tempo a seconda dello SKU gateway selezionato.

Creare un gateway di rete virtuale con i valori seguenti:

* **Nome:** VNet1GW
* **Area:** Stati Uniti orientali
* **Tipo di gateway:** VPN
* **Tipo di VPN:** basato su route
* **SKU:** VpnGw1
* **Generazione:** Generation1
* **Rete virtuale:** VNet1
* **Intervallo di indirizzi subnet del gateway:** 10.1.255.0/27
* **Indirizzo IP pubblico:** Creare un nuovo gruppo di risorse
* **Nome indirizzo IP pubblico:** VNet1GWpip
* **Abilita modalità attiva-attiva:** Disabled
* **Configura BGP:** Disabled

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="view-the-public-ip-address"></a><a name="view"></a>Visualizzare l'indirizzo IP pubblico

È possibile visualizzare l'indirizzo IP pubblico del gateway nella pagina **Panoramica** del gateway.

:::image type="content" source="./media/tutorial-create-gateway-portal/address.png" alt-text="Pagina di panoramica":::

Per visualizzare altre informazioni sull'oggetto indirizzo IP pubblico, fare clic sul collegamento del nome/indirizzo IP accanto a **Indirizzo IP pubblico**.

## <a name="resize-a-gateway-sku"></a><a name="resize"></a>Ridimensionare uno SKU del gateway

Il ridimensionamento e la modifica di uno SKU del gateway sono soggetti a regole specifiche. In questa sezione si ridimensionerà lo SKU. Per altre informazioni, vedere [Impostazioni del gateway - Ridimensionare o modificare uno SKU](vpn-gateway-about-vpn-gateway-settings.md#resizechange).

[!INCLUDE [resize a gateway](../../includes/vpn-gateway-resize-gw-portal-include.md)]

## <a name="reset-a-gateway"></a><a name="reset"></a>Reimpostare un gateway

[!INCLUDE [reset a gateway](../../includes/vpn-gateway-reset-gw-portal-include.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si prevede di continuare a usare questa applicazione o di passare all'esercitazione successiva, eliminare le risorse seguendo questa procedura:

1. Immettere il nome del gruppo di risorse nella casella **Cerca** nella parte superiore del portale e selezionarlo nei risultati della ricerca.

1. Selezionare **Elimina gruppo di risorse**.

1. Immettere il nome del gruppo di risorse in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Ora che si dispone di un gateway VPN, è possibile configurare le connessioni. Gli articoli seguenti illustrano come creare alcune delle configurazioni più comuni:

> [!div class="nextstepaction"]
> [Connessioni VPN da sito a sito](vpn-gateway-howto-site-to-site-resource-manager-portal.md)

> [!div class="nextstepaction"]
> [Connessioni VPN da punto a sito](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
