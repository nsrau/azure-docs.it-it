---
title: 'Aggiungere più connessioni da sito a sito del gateway VPN a una VNet: portale di Azure'
description: Come aggiungere più connessioni da sito a sito (S2S) a un gateway VPN con una connessione esistente
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/27/2020
ms.author: cherylmc
ms.openlocfilehash: 92d39b0d39511571701fd092f641cb8ca3ae42c7
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "92890149"
---
# <a name="add-additional-s2s-connections-to-a-vnet-azure-portal"></a>Aggiungere altre connessioni S2S a una VNet: portale di Azure

> [!div class="op_single_selector"]
> * [Portale di Azure](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (classic)](vpn-gateway-multi-site.md) (PowerShell (classico))
>

Questo articolo consente di aggiungere altre connessioni da sito a sito (S2S) a un gateway VPN con una connessione esistente. Questa architettura è spesso definita configurazione "multisito". È possibile aggiungere una connessione da sito a sito a una rete virtuale che dispone già di una connessione da sito a sito, una connessione da punto a sito o una connessione da rete virtuale a rete virtuale. Quando si aggiungono delle connessioni, esistono alcune limitazioni di cui è necessario tenere conto. Controllare la sezione [prerequisiti](#before) in questo articolo per verificare prima di iniziare la configurazione.

Questo articolo si applica alle reti virtuali di Resource Manager con un gateway VPN RouteBased. Questi passaggi non si applicano alle nuove configurazioni di connessione ExpressRoute/da sito a sito coesistente. Tuttavia, se si aggiunge semplicemente una nuova connessione VPN a una configurazione coesistente già esistente, è possibile usare questi passaggi. Per informazioni sulle connessioni coesistenti vedere [Creare connessioni coesistenti da sito a sito ed ExpressRoute](../expressroute/expressroute-howto-coexist-resource-manager.md).

## <a name="prerequisites"></a><a name="before"></a>Prerequisiti

Verificare quanto segue:

* Non si sta configurando una nuova configurazione coesistente del gateway ExpressRoute e VPN.
* Si dispone di una rete virtuale creata usando il modello di distribuzione Resource Manager con una connessione esistente.
* Il gateway di rete virtuale per la rete virtuale è di tipo RouteBased. Se si dispone di un gateway VPN basato su criteri, è necessario eliminare il gateway di rete virtuale e creare un nuovo gateway VPN di tipo RouteBased.
* Nessuno degli intervalli di indirizzi si sovrappone a una qualsiasi delle reti virtuali a cui si connette questa rete virtuale.
* Si dispone di un dispositivo VPN compatibile ed è presente un utente in grado di configurarlo. Vedere [Informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md). Se non si ha familiarità con la configurazione del dispositivo VPN o con gli intervalli di indirizzi IP disponibili nella configurazione di rete locale, è necessario coordinarsi con qualcuno che possa fornire tali dettagli.
* Si dispone di un indirizzo IP pubblico esterno per il dispositivo VPN.

## <a name="configure-a-connection"></a><a name="configure"></a>Configurare una connessione

1. Da un browser, passare al [portale di Azure](https://portal.azure.com) e, se necessario, accedere con l'account Azure.
1. Selezionare **tutte le risorse** e individuare il **gateway di rete virtuale** dall'elenco di risorse e selezionarlo.
1. Nella pagina **gateway di rete virtuale** selezionare **connessioni**.

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connections.png" alt-text="Connessioni gateway VPN":::
1. Nella pagina **connessioni** selezionare **+ Aggiungi**.
1. Verrà visualizzata la pagina **Aggiungi connessione** .

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/add-connection.png" alt-text="Pagina Aggiungi connessione":::
1. Nella pagina **Aggiungi connessione** compilare i campi seguenti:

   * **Nome** : nome del sito a cui si sta creando la connessione.
   * **Tipo di connessione:** Selezionare **da sito a sito (IPSec)**.

## <a name="add-a-local-network-gateway"></a><a name="local"></a>Aggiungere un gateway di rete locale

1. Per il campo **gateway di rete locale** selezionare **_Scegli un gateway di rete locale_*_. Verrà visualizzata la pagina _ scegliere il gateway di* rete locale** .
1. Selezionare **+ Crea nuovo** per aprire la pagina **Crea gateway di rete locale** .

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/create-local-network-gateway.png" alt-text="Pagina Crea gateway di rete locale":::
1. Nella pagina **Crea un gateway di rete locale** compilare i campi seguenti:

   * **Nome** : nome da assegnare alla risorsa gateway di rete locale.
   * **Endpoint:** Indirizzo IP pubblico del dispositivo VPN nel sito a cui si desidera connettersi oppure il nome di dominio completo dell'endpoint.
   * **Spazio indirizzi** : spazio indirizzi che si vuole venga indirizzato al nuovo sito di rete locale.
1. Fare clic su **OK** nella pagina **Crea gateway di rete locale** per salvare le modifiche.

## <a name="add-the-shared-key"></a><a name="part3"></a>Aggiungere la chiave condivisa

1. Dopo aver creato il gateway di rete locale, tornare alla pagina **Aggiungi connessione** .
1. Completare i campi rimanenti. Per la **chiave condivisa (PSK)** , è possibile ottenere la chiave condivisa dal dispositivo VPN o crearne una qui e quindi configurare il dispositivo VPN per l'uso della stessa chiave condivisa. È fondamentale che le chiavi siano assolutamente identiche.

## <a name="create-the-connection"></a><a name="create"></a>Creare la connessione

1. Nella parte inferiore della pagina fare clic su **OK** per creare la connessione. La connessione inizia immediatamente a creare.
1. Una volta completata la connessione, è possibile visualizzarla e verificarla.

## <a name="view-and-verify-the-vpn-connection"></a><a name="verify"></a>Visualizzare e verificare la connessione VPN

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per altre informazioni, vedere [percorsi di apprendimento delle macchine virtuali](/learn/paths/deploy-a-website-with-azure-virtual-machines/).
