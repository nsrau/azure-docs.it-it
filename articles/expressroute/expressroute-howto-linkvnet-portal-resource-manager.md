---
title: 'ExpressRoute: collegare un VNet a un circuito: portale di Azure'
description: Connettere una VNet a un circuito ExpressRoute di Azure. Procedure.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 4c7a24ad692086398059d1afd48c8927e9d18582
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022201"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Connettere una rete virtuale a un circuito ExpressRoute usando il portale
> [!div class="op_single_selector"]
> * [Portale di Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Interfaccia della riga di comando di Azure](howto-linkvnet-cli.md)
> * [Video - Portale di Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (classic)](expressroute-howto-linkvnet-classic.md) (PowerShell (classico))
> 

Questo articolo consente di creare una connessione per collegare una rete virtuale a un circuito ExpressRoute di Azure tramite il portale di Azure. Le reti virtuali da connettere al circuito ExpressRoute di Azure possono trovarsi nella stessa sottoscrizione o appartenere a un'altra sottoscrizione.

## <a name="before-you-begin"></a>Prima di iniziare

* Verificare i [prerequisiti](expressroute-prerequisites.md), i [requisiti di routing](expressroute-routing.md) e i [flussi di lavoro](expressroute-workflows.md).

* È necessario avere un circuito ExpressRoute attivo.
  * Seguire le istruzioni per [creare un circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e fare in modo che venga abilitato dal provider di connettività.
  * Assicurarsi di disporre del peering privato di Azure configurato per il circuito. Vedere l'articolo [creare e modificare il peering per un circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) per istruzioni per il peering e il routing.
  * Per abilitare la connettività end-to-end è necessario assicurarsi che sia configurato il peering privato di Azure e sia attivo il peering BGP tra la rete e Microsoft.
  * Assicurarsi di avere una rete virtuale e un gateway di rete virtuale creati e con provisioning completo. Seguire le istruzioni per [creare un gateway di rete virtuale per ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Un gateway di rete virtuale per ExpressRoute usa 'ExpressRoute' come GatewayType, non VPN.

* È possibile collegare fino a 10 reti virtuali a un circuito ExpressRoute standard. Tutte le reti virtuali devono essere nella stessa area geopolitica quando si usa un circuito ExpressRoute standard.

* Una singola rete virtuale può essere collegata a un massimo di quattro circuiti ExpressRoute. Usare la procedura seguente per creare un nuovo oggetto di connessione per ogni circuito ExpressRoute a cui si esegue la connessione. I circuiti ExpressRoute possono essere nella stessa sottoscrizione, diverse sottoscrizioni o una combinazione di entrambe le situazioni.

* È possibile collegare una rete virtuale esterna all'area geopolitica del circuito ExpressRoute o connettere un numero maggiore di reti virtuali al circuito ExpressRoute se è stato abilitato il componente aggiuntivo ExpressRoute Premium. Per altre informazioni sul componente aggiuntivo Premium, vedere le [domande frequenti](expressroute-faqs.md) .

* È possibile [visualizzare un video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) prima di iniziare, per comprendere meglio la procedura.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Connettere una rete virtuale a un circuito: stessa sottoscrizione

> [!NOTE]
> Le informazioni sulla configurazione BGP non verranno visualizzate se i peering sono stati configurati dal provider di livello 3. Se è stato eseguito il provisioning del circuito, deve essere possibile creare le connessioni.
>

### <a name="to-create-a-connection"></a>Per creare una connessione

1. Verificare che il circuito ExpressRoute e il peering privato di Azure siano configurati correttamente. Seguire le istruzioni riportate in [creare un circuito ExpressRoute](expressroute-howto-circuit-arm.md) e [creare e modificare il peering per un circuito ExpressRoute](expressroute-howto-routing-arm.md). Il circuito ExpressRoute deve essere simile a quello della figura seguente:

   [![Schermata del circuito ExpressRoute](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png "Visualizza circuito")](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1-exp.png#lightbox)
2. È ora possibile avviare il provisioning di una connessione per collegare il gateway della rete virtuale al circuito ExpressRoute. Fare clic su **Connessione** > **Aggiungi** per aprire la pagina **Aggiungi connessione** e configurare i valori.

   [![Schermata Aggiungi connessione](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png "Schermata Aggiungi connessione")](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1-exp.png#lightbox)
3. Al termine della configurazione, l'oggetto connessione visualizzerà le informazioni per la connessione.

   ![Schermata dell'oggetto connessione](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Connettere una rete virtuale a un circuito: diversa sottoscrizione

È possibile condividere un circuito ExpressRoute tra più sottoscrizioni. La figura seguente mostra un semplice schema del funzionamento della condivisione di circuiti ExpressRoute tra più sottoscrizioni.

![Connettività tra sottoscrizioni](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- Ciascuno dei cloud più piccoli nel cloud di grandi dimensioni viene usato per rappresentare le sottoscrizioni appartenenti a reparti diversi di un'organizzazione.
- Ciascun reparto dell'organizzazione può usare la propria sottoscrizione per distribuire i servizi, ma i reparti possono condividere un singolo circuito ExpressRoute per la connessione alla rete locale.
- Un singolo reparto (in questo esempio, IT) può possedere il circuito ExpressRoute. Altre sottoscrizioni all'interno dell'organizzazione possono usare il circuito ExpressRoute e le autorizzazioni associate al circuito, incluse le sottoscrizioni collegate ad altri tenant di Azure Active Directory e le registrazione di contratti Enterprise.

  > [!NOTE]
  > I costi relativi a connettività e larghezza di banda per il circuito dedicato saranno addebitati al proprietario del circuito ExpressRoute. Tutte le reti virtuali condividono la stessa larghezza di banda.
  >
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Amministrazione: proprietari e utenti del circuito

Il proprietario del circuito è l'utente esperto autorizzato della risorsa circuito ExpressRoute. Il proprietario del circuito può creare le autorizzazioni che possono essere riscattate dagli "utenti del circuito". Gli utenti del circuito sono i proprietari dei gateway di rete virtuale, che non sono nella stessa sottoscrizione del circuito ExpressRoute. Gli utenti del circuito possono riscattare le autorizzazioni (un'autorizzazione per ogni rete virtuale).

Il proprietario del circuito ha la facoltà di modificare e revocare le autorizzazioni in qualsiasi momento. La revoca dell'autorizzazione comporterà l'eliminazione di tutti i collegamenti dalla sottoscrizione di cui è stato revocato l'accesso.

### <a name="circuit-owner-operations"></a>Operazioni del proprietario del circuito

**Per creare un'autorizzazione di connessione**

Il proprietario del circuito crea un'autorizzazione. Questo comporta la creazione di una chiave di autorizzazione che può essere utilizzata da un utente del circuito per connettere i gateway di rete virtuale al circuito ExpressRoute. Un'autorizzazione è valida per una sola connessione.

> [!NOTE]
> Ogni connessione richiede un'autorizzazione separata.
>

1. Nella pagina di ExpressRoute fare clic su **Autorizzazioni**, digitare un **nome** per l'autorizzazione e fare clic su **Salva**.

   ![Authorizations](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)
2. Dopo che la configurazione è stata salvata, copiare l'**ID risorsa** e la **chiave di autorizzazione**.

   ![Chiave di autorizzazione](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**Per eliminare un'autorizzazione di connessione**

È possibile eliminare una connessione selezionando l'icona **Elimina** nella pagina della connessione.

### <a name="circuit-user-operations"></a>Operazioni dell'utente del circuito

L'utente del circuito deve richiedere l'ID risorsa e una chiave di autorizzazione al proprietario del circuito.

**Per riscattare un'autorizzazione di connessione**

1. Fare clic sul pulsante **+ New** (Nuovo).

   ![Click New](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)
2. Cercare **"Connection"** (Connessione) in Marketplace, selezionare l'opzione e fare clic **Create** (Crea).

   ![Cercare Connection (Connessione)](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)
3. Verificare che **Connection type** (Tipo di connessione) sia impostato su "ExpressRoute".
4. Inserire i dettagli e fare clic su **OK** nella pagina Informazioni di base.

   ![Pagina Informazioni di base](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)
5. Nella pagina **Impostazioni** selezionare **Gateway di rete virtuale** e selezionare la casella di controllo **Riscatta autorizzazione**.
6. Immettere i valori nei campi **Authorization key** (Chiave di autorizzazione) e **Peer circuit URI** (URI circuito peer) e assegnare un nome di connessione. Fare clic su **OK**. L' **URI del circuito peer** è l'ID di risorsa del circuito ExpressRoute, che è possibile trovare nel riquadro delle impostazioni delle proprietà del circuito ExpressRoute.

   ![Pagina Impostazioni](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)
7. Verificare le informazioni nella pagina **Riepilogo** e fare clic su **OK**.

**Per rilasciare un'autorizzazione di connessione**

È possibile rilasciare un'autorizzazione eliminando il collegamento del circuito ExpressRoute alla rete virtuale.

## <a name="delete-a-connection-to-unlink-a-vnet"></a>Eliminare una connessione per scollegare una rete virtuale

È possibile eliminare una connessione e scollegare la rete virtuale da un circuito ExpressRoute selezionando l'icona **Elimina** nella pagina della connessione.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).
