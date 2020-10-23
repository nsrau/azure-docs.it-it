---
title: 'Esercitazione: Collegare una rete virtuale a un circuito ExpressRoute - Portale di Azure'
description: Questa esercitazione illustra come creare una connessione per collegare una rete virtuale a un circuito Azure ExpressRoute tramite il portale di Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 0ffc9c2ee17862497d3fd986da8e003f7a497056
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107284"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Esercitazione: Connettere una rete virtuale a un circuito ExpressRoute usando il portale

> [!div class="op_single_selector"]
> * [Portale di Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Interfaccia della riga di comando di Azure](howto-linkvnet-cli.md)
> * [Video - Portale di Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (classic)](expressroute-howto-linkvnet-classic.md) (PowerShell (classico))
> 

Questa esercitazione spiega come creare una connessione per collegare una rete virtuale a un circuito Azure ExpressRoute tramite il portale di Azure. Le reti virtuali da connettere al circuito Azure ExpressRoute possono trovarsi nella stessa sottoscrizione o appartenere a un'altra sottoscrizione.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> - Collegare una rete virtuale a un circuito nella stessa sottoscrizione.
> - Collegare una rete virtuale a un circuito in un'altra sottoscrizione.
> - Eliminare il collegamento tra la rete virtuale e il circuito ExpressRoute.

## <a name="prerequisites"></a>Prerequisiti

* Verificare i [prerequisiti](expressroute-prerequisites.md), i [requisiti di routing](expressroute-routing.md) e i [flussi di lavoro](expressroute-workflows.md).

* È necessario avere un circuito ExpressRoute attivo.
  * Seguire le istruzioni per [creare un circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e fare in modo che venga abilitato dal provider di connettività.
  * Assicurarsi di disporre del peering privato di Azure configurato per il circuito. Per istruzioni relative al peering e al routing, vedere [Creare e modificare i peering per un circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md).
  * Assicurarsi che il peering privato di Azure sia configurato e stabilisca il peering BGP tra la rete e Microsoft per la connettività end-to-end.
  * Assicurarsi di disporre di una rete virtuale e di un gateway di rete virtuale creati e con provisioning completo. Seguire le istruzioni per [creare un gateway di rete virtuale per ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Un gateway di rete virtuale per ExpressRoute usa 'ExpressRoute' come GatewayType, non VPN.

* È possibile collegare fino a 10 reti virtuali a un circuito ExpressRoute standard. Tutte le reti virtuali devono essere nella stessa area geopolitica quando si usa un circuito ExpressRoute standard.

* Una singola rete virtuale può essere collegata a un massimo di quattro circuiti ExpressRoute. Usare la procedura seguente per creare un nuovo oggetto di connessione per ogni circuito ExpressRoute a cui ci si connette. I circuiti ExpressRoute possono essere nella stessa sottoscrizione, diverse sottoscrizioni o una combinazione di entrambe le situazioni.

* Abilitando il componente aggiuntivo ExpressRoute Premium, è possibile collegare reti virtuali esterne all'area geopolitica del circuito ExpressRoute. Il componente aggiuntivo Premium consente anche di connettere più di 10 reti virtuali al circuito ExpressRoute, in base alla larghezza di banda scelta. Per altre informazioni sul componente aggiuntivo Premium, vedere le [domande frequenti](expressroute-faqs.md) .

* È possibile [visualizzare un video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) prima di iniziare, per comprendere meglio la procedura.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Connettere una rete virtuale a un circuito: stessa sottoscrizione

> [!NOTE]
> Le informazioni sulla configurazione BGP non vengono visualizzate se i peering sono stati configurati dal provider di livello 3. Se è stato eseguito il provisioning del circuito, deve essere possibile creare le connessioni.
>

### <a name="to-create-a-connection"></a>Per creare una connessione

1. Verificare che il circuito ExpressRoute e il peering privato di Azure siano configurati correttamente. Seguire le istruzioni in [Creare un circuito ExpressRoute](expressroute-howto-circuit-arm.md) e [Creare e modificare i peering per un circuito ExpressRoute](expressroute-howto-routing-arm.md). Il circuito ExpressRoute deve essere simile a quello della figura seguente:

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/express-route-circuit.png" alt-text="Schermata del circuito ExpressRoute":::

1. È ora possibile avviare il provisioning di una connessione per collegare il gateway della rete virtuale al circuito ExpressRoute. Selezionare **Connessione** > **Aggiungi** per aprire la pagina **Aggiungi connessione**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/add-connection.png" alt-text="Schermata del circuito ExpressRoute":::

1. Immettere un nome per la connessione, quindi selezionare **Avanti: Impostazioni >** .

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-connection-basic.png" alt-text="Schermata del circuito ExpressRoute":::

1. Selezionare il gateway che appartiene alla rete virtuale da collegare al circuito, quindi selezionare **Rivedi e crea**. Al termine della convalida selezionare **Crea**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-connection-settings.png" alt-text="Schermata del circuito ExpressRoute":::

1. Al termine della configurazione, l'oggetto connessione visualizzerà le informazioni per la connessione.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-object.png" alt-text="Schermata del circuito ExpressRoute":::

## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Connettere una rete virtuale a un circuito: diversa sottoscrizione

È possibile condividere un circuito ExpressRoute tra più sottoscrizioni. La figura seguente mostra un semplice schema del funzionamento della condivisione di circuiti ExpressRoute tra più sottoscrizioni.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png" alt-text="Schermata del circuito ExpressRoute":::

Ciascuno dei cloud più piccoli nel cloud di grandi dimensioni viene usato per rappresentare le sottoscrizioni appartenenti a reparti diversi di un'organizzazione. Ciascun reparto dell'organizzazione usa la propria sottoscrizione per distribuire i servizi, ma può condividere un solo circuito ExpressRoute per la connessione alla rete locale. Un singolo reparto (in questo esempio, IT) può possedere il circuito ExpressRoute. Altre sottoscrizioni all'interno dell'organizzazione possono usare il circuito ExpressRoute.

  > [!NOTE]
  > I costi relativi a connettività e larghezza di banda per il circuito dedicato saranno addebitati al proprietario del circuito ExpressRoute. Tutte le reti virtuali condividono la stessa larghezza di banda.
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Amministrazione: proprietari e utenti del circuito

Il proprietario del circuito è l'utente esperto autorizzato della risorsa circuito ExpressRoute. Il proprietario del circuito può creare le autorizzazioni che possono essere riscattate dagli "utenti del circuito". Gli utenti del circuito sono i proprietari dei gateway di rete virtuale che non sono nella stessa sottoscrizione del circuito ExpressRoute. Gli utenti del circuito possono riscattare le autorizzazioni (un'autorizzazione per ogni rete virtuale).

Il proprietario del circuito ha la facoltà di modificare e revocare le autorizzazioni in qualsiasi momento. La revoca dell'autorizzazione comporterà l'eliminazione di tutti i collegamenti dalla sottoscrizione di cui è stato revocato l'accesso.

### <a name="circuit-owner-operations"></a>Operazioni del proprietario del circuito

**Per creare un'autorizzazione di connessione**

Il proprietario del circuito crea un'autorizzazione, che crea a sua volta una chiave di autorizzazione che può essere usata da un utente del circuito per connettere i propri gateway di rete virtuale al circuito ExpressRoute. Un'autorizzazione è valida per una sola connessione.

> [!NOTE]
> Ogni connessione richiede un'autorizzazione separata.
>

1. Nella pagina di ExpressRoute selezionare **Autorizzazioni**, digitare un **nome** per l'autorizzazione e selezionare **Salva**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png" alt-text="Schermata del circuito ExpressRoute":::

2. Dopo che la configurazione è stata salvata, copiare l'**ID risorsa** e la **chiave di autorizzazione**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization-key.png" alt-text="Schermata del circuito ExpressRoute":::

**Per eliminare un'autorizzazione di connessione**

Per eliminare una connessione, selezionare l'icona **Elimina** corrispondente alla chiave di autorizzazione per la connessione.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-authorization-key.png" alt-text="Schermata del circuito ExpressRoute":::

Per eliminare la connessione ma mantenere la chiave di autorizzazione, è possibile eliminare la connessione nella pagina corrispondente del circuito.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-connection-owning-circuit.png" alt-text="Schermata del circuito ExpressRoute":::

### <a name="circuit-user-operations"></a>Operazioni dell'utente del circuito

L'utente del circuito deve richiedere l'ID risorsa e una chiave di autorizzazione al proprietario del circuito.

**Per riscattare un'autorizzazione di connessione**

1. Selezionare il pulsante **+ Crea una risorsa**. Cercare **Connessione** e selezionare **Crea**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-new-resources.png" alt-text="Schermata del circuito ExpressRoute":::

1. Assicurarsi che l'opzione *Tipo di connessione* sia impostata su **ExpressRoute**. Selezionare i valori per *Gruppo di risorse* e *Località*, quindi selezionare **OK** nella pagina Informazioni di base.

    > [!NOTE]
    > La località *deve* corrispondere a quella del gateway di rete virtuale per cui creare la connessione.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-basics.png" alt-text="Schermata del circuito ExpressRoute":::

1. Nella pagina **Impostazioni** selezionare *Gateway di rete virtuale* e selezionare la casella di controllo **Riscatta autorizzazione**. Immettere i valori nei campi *Authorization key* (Chiave di autorizzazione) e *Peer circuit URI* (URI circuito peer) e assegnare un nome di connessione. Selezionare **OK**. 
 
    > [!NOTE]
    > Il valore di *URI del circuito peer* è l'ID risorsa del circuito ExpressRoute (disponibile nel riquadro di impostazione delle proprietà del circuito ExpressRoute).

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-settings.png" alt-text="Schermata del circuito ExpressRoute":::

1. Verificare le informazioni nella pagina **Riepilogo** e scegliere **OK**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-summary.png" alt-text="Schermata del circuito ExpressRoute":::

## <a name="clean-up-resources"></a>Pulizia delle risorse

È possibile eliminare una connessione e scollegare la rete virtuale da un circuito ExpressRoute selezionando l'icona **Elimina** nella pagina della connessione.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-connection.png" alt-text="Schermata del circuito ExpressRoute":::

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come connettere una rete virtuale a un circuito nella stessa sottoscrizione e in una sottoscrizione diversa. Per altre informazioni sul gateway ExpressRoute, vedere: 

> [!div class="nextstepaction"]
> [Informazioni sui gateway di rete virtuale per ExpressRoute](expressroute-about-virtual-network-gateways.md)
