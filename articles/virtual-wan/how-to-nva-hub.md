---
title: "Rete WAN virtuale di Azure: creare un'appliance virtuale di rete nell'hub"
description: Informazioni su come distribuire un'appliance virtuale di rete nell'hub WAN virtuale.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a Network Virtual Appliance (NVA) in my Virtual WAN hub.
ms.openlocfilehash: 0e174f9b78606e0bea5cded3a176f4207745bb5c
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92077304"
---
# <a name="how-to-create-a-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>Come creare un'appliance virtuale di rete in un hub WAN virtuale di Azure (anteprima)

Questo articolo illustra come usare la rete WAN virtuale per connettersi alle risorse in Azure tramite un' **appliance virtuale di rete** in Azure. Questo tipo di connessione richiede un dispositivo VPN che si trova in locale con un indirizzo IP pubblico esterno assegnato. Per ulteriori informazioni sulla rete WAN virtuale, vedere la pagina relativa alla [rete WAN virtuale](virtual-wan-about.md).

I passaggi descritti in questo articolo consentono di creare un'appliance virtuale di rete **WAN Barracuda CloudGen** nell'hub WAN virtuale. Per completare questo esercizio, è necessario disporre di un dispositivo di cloud locale Barracuda e di una licenza per l'appliance Barracuda CloudGen WAN distribuita nell'hub prima di iniziare.

Per la documentazione sulla distribuzione di **Cisco SD-WAN** all'interno della rete WAN virtuale di Azure, inviare un messaggio di posta elettronica a Cisco all'indirizzo di posta elettronica seguente: vwan_public_preview@external.cisco.com


## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare la configurazione, verificare di soddisfare i criteri seguenti:

* Ottenere una licenza per il gateway WAN Barracuda CloudGen. Per ulteriori informazioni su come eseguire questa operazione, vedere la [documentazione di Barracuda CLOUDGEN WAN](https://www.barracuda.com/products/cloudgenwan)

* Si ha una rete virtuale a cui ci si vuole connettere. Verificare che nessuna delle subnet della rete locale possa sovrapporsi alle reti virtuali a cui ci si vuole connettere. Per creare una rete virtuale nel portale di Azure, vedere l'[Avvio rapido](../virtual-network/quick-create-portal.md).

* La rete virtuale non presenta alcun gateway di rete virtuale. Se la rete virtuale presenta un gateway (VPN o ExpressRoute) è necessario rimuovere tutti i gateway. Questa configurazione richiede che le reti virtuali siano invece connesse al gateway dell'hub della rete WAN virtuale.

* Ottenere un intervallo di indirizzi IP per l'area dell'hub. L'hub è una rete virtuale che viene creata e usata dalla rete WAN virtuale. L'intervallo di indirizzi specificati per l'hub non può sovrapporsi ad alcuna delle reti virtuali esistenti a cui ci si connette. Inoltre non può sovrapporsi agli intervalli di indirizzi a cui ci si connette in locale. Se non si ha familiarità con gli intervalli degli indirizzi IP disponibili nella configurazione della rete locale, coordinarsi con qualcuno che possa fornire tali dettagli.

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Creare una rete WAN virtuale

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>Creare un hub

Un hub è una rete virtuale che può contenere gateway per la funzionalità ExpressRoute da sito a sito, da punto a sito o da dispositivo virtuale di rete. Dopo aver creato l'hub, verranno addebitati i relativi costi, anche se si non collega alcun sito. Se si sceglie di creare un gateway VPN da sito a sito, la creazione del gateway VPN da sito a sito nell'hub virtuale richiede 30 minuti. A differenza da sito a sito, ExpressRoute o da punto a sito, l'hub deve essere creato prima di poter distribuire un'appliance virtuale di rete nella VNet Hub.

1. Individuare la rete WAN virtuale creata. Nella sezione **connettività** della pagina **WAN virtuale** selezionare **Hub**.
1. Nella pagina **Hub** selezionare + nuovo hub per aprire la pagina **Crea hub virtuale** .

   :::image type="content" source="./media/how-to-nva-hub/vwan-hub.png" alt-text="Nozioni di base":::
1. Nella scheda **Generale** della pagina **Crea hub virtuale** completare i campi seguenti:

   **Dettagli del progetto**

   * Regione (precedentemente denominata Posizione)
   * Nome
   * Spazio di indirizzi privato dell'hub. Lo spazio di indirizzi minimo per la creazione di un hub è /24; qualsiasi intervallo compreso tra /25 e /32 genererà pertanto un errore durante la creazione. La rete WAN virtuale di Azure, che è un servizio gestito da Microsoft, crea le subnet appropriate nell'hub virtuale per i diversi gateway/Servizi. Ad esempio: appliance virtuali di rete, gateway VPN, gateway ExpressRoute, gateway VPN utente/gateway da punto a sito, firewall, routing e così via. Non è necessario che l'utente pianifichi in modo esplicito lo spazio degli indirizzi della subnet per i servizi nell'hub virtuale perché Microsoft esegue questa operazione come parte del servizio.
1. Selezionare **Rivedi e crea** per convalidare la selezione.
1. Fare clic su **Crea** per creare l'hub.

## <a name="create-the-network-virtual-appliance-in-the-hub"></a>Creare l'appliance di rete virtuale nell'hub

In questo passaggio verrà creata un'appliance virtuale di rete nell'hub. La procedura per ogni appliance virtuale di virtualizzazione sarà diversa per ogni prodotto del partner appliance virtuale di virtualizzazione. Per questo esempio viene creato un gateway WAN Barracuda CloudGen.

1. Individuare l'hub WAN virtuale creato nel passaggio precedente e aprirlo.

   :::image type="content" source="./media/how-to-nva-hub/nva-hub.png" alt-text="Nozioni di base":::
1. Individuare il riquadro appliance virtuali di rete e selezionare il collegamento **Crea** .
1. Nel pannello **dispositivo virtuale di rete** selezionare **Barracuda CloudGen WAN**, quindi selezionare il pulsante **Crea** .

   :::image type="content" source="./media/how-to-nva-hub/select-nva.png" alt-text="Nozioni di base":::
1. Verrà visualizzata l'offerta di Azure Marketplace per il gateway WAN Barracuda CloudGen. Leggere i termini, quindi selezionare il pulsante **Crea** quando si è pronti.

   :::image type="content" source="./media/how-to-nva-hub/barracuda-create-basics.png" alt-text="Nozioni di base":::
1. Nella pagina informazioni di **base** è necessario fornire le seguenti informazioni:

   * **Sottoscrizione** : scegliere la sottoscrizione usata per distribuire la rete WAN virtuale e l'hub.
   * **Gruppo di risorse** : scegliere lo stesso gruppo di risorse usato per distribuire la rete WAN virtuale e l'hub.
   * **Region** : scegliere la stessa area in cui si trova la risorsa dell'hub virtuale.
   * **Nome dell'applicazione** : Barracuda NextGen WAN è un'applicazione gestita. Scegliere un nome che facilita l'identificazione di questa risorsa, in quanto si tratta dell'elemento che verrà chiamato quando verrà visualizzato nella sottoscrizione.
   * **Gruppo di risorse gestite** : questo è il nome del gruppo di risorse gestite in cui Barracuda distribuirà le risorse gestite da tali gruppi. Il nome deve essere pre-popolato per questo.
1. Selezionare il pulsante **Next: CLOUDGEN WAN gateway** .

   :::image type="content" source="./media/how-to-nva-hub/barracuda-cloudgen-wan.png" alt-text="Nozioni di base":::
1. Fornire le seguenti informazioni:

   * **Hub WAN virtuale** : Hub WAN virtuale in cui si vuole distribuire questa appliance virtuale di rete.
   * **Unità di infrastruttura** di appliance virtuale di dispositivi: indica il numero di unità di infrastruttura dell'appliance virtuale con cui si vuole distribuire l'appliance virtuale di Scegliere la quantità di larghezza di banda aggregata che si vuole fornire in tutti i siti di succursale che si connetteranno a questo hub tramite l'appliance virtuale di dispositivo.
   * **Token** -Barracuda richiede l'uso di un token di autenticazione per identificarsi come utente registrato di questo prodotto. È necessario ottenere questo da Barracuda.
1. Selezionare il pulsante **Verifica e crea** per continuare.
1. In questa pagina verrà richiesto di accettare le condizioni del contratto di accesso Co-Admin. Questa operazione è standard con le applicazioni gestite in cui il server di pubblicazione avrà accesso ad alcune risorse in questa distribuzione. Selezionare la casella **Accetto i termini e le condizioni precedenti** , quindi selezionare **Crea**.

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>Connettere la rete virtuale all'hub

[!INCLUDE [Connect](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sulla rete WAN virtuale, vedere la pagina [che cos'è la rete WAN virtuale?](virtual-wan-about.md) .
* Per altre informazioni su appliance virtuali in un hub WAN virtuale, vedere [informazioni sull'appliance virtuale di rete nell'hub WAN virtuale (anteprima)](about-nva-hub.md).
