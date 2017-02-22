---
title: Collegare una rete virtuale a un circuito ExpressRoute usando il portale di Azure | Documentazione Microsoft
description: Questo documento fornisce una panoramica delle operazioni per il collegamento di reti virtuali (VNet) a circuiti ExpressRoute.
services: expressroute
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: f5cb5441-2fba-46d9-99a5-d1d586e7bda4
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 75b7cee2485d1d68fee8dcd65ade84995dcdb9be
ms.openlocfilehash: aa473abd00fbddb01e50577cea8984c6f3e9a7f3


---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Connettere una rete virtuale a un circuito ExpressRoute
> [!div class="op_single_selector"]
> * [Resource Manager - Portale di Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Classica: PowerShell](expressroute-howto-linkvnet-classic.md)
> * [Video - Portale di Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> 
>  

Questo articolo spiega come collegare le reti virtuali (Vnet) ai circuiti ExpressRoute di Azure usando il modello di distribuzione di Gestione risorse e il portale di Azure. Le reti virtuali possono trovarsi nella stessa sottoscrizione o appartenere a un'altra sottoscrizione.

**Informazioni sui modelli di distribuzione di Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Prerequisiti di configurazione
* Prima di procedere con la configurazione, assicurarsi di avere verificato i [prerequisiti](expressroute-prerequisites.md), i [requisiti di routing](expressroute-routing.md) e i [flussi di lavoro](expressroute-workflows.md).
* È necessario avere un circuito ExpressRoute attivo.
  
  * Seguire le istruzioni per [creare un circuito ExpressRoute](expressroute-howto-circuit-arm.md) e fare in modo che venga abilitato dal provider di connettività.
  * Assicurarsi di disporre del peering privato di Azure configurato per il circuito. Vedere l'articolo relativo alla [configurazione del routing](expressroute-howto-routing-portal-resource-manager.md) per istruzioni relative al routing.
  * Per abilitare la connettività end-to-end è necessario assicurarsi che sia configurato il peering privato di Azure e sia attivo il peering BGP tra la rete e Microsoft.
  * Assicurarsi di disporre di una rete virtuale e di un gateway di rete virtuale creati e con provisioning completo. Seguire le istruzioni relative alla creazione di un [gateway VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) (solo i passaggi da 1 a 5).

È possibile collegare fino a 10 reti virtuali a un circuito ExpressRoute standard. Tutte le reti virtuali devono essere nella stessa area geopolitica quando si usa un circuito ExpressRoute standard. È possibile collegare reti virtuali esterne all'area geopolitica del circuito ExpressRoute o connettere un numero maggiore di reti virtuali al circuito ExpressRoute se è stato abilitato il componente aggiuntivo ExpressRoute Premium. Per altre informazioni sul componente aggiuntivo Premium, vedere le [domande frequenti](expressroute-faqs.md) .

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Collegare una rete virtuale della stessa sottoscrizione a un circuito

### <a name="to-create-a-connection"></a>Per creare una connessione

1. Verificare che il circuito ExpressRoute e il peering privato di Azure siano configurati correttamente. Seguire le istruzioni in [Creare e modificare un circuito ExpressRoute](expressroute-howto-circuit-arm.md) e [Creare e modificare il routing per un circuito ExpressRoute](expressroute-howto-routing-arm.md). Il circuito ExpressRoute deve essere simile a quello della figura seguente.
    ![Schermata del circuito ExpressRoute](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)
   
   > [!NOTE]
   > Le informazioni sulla configurazione BGP non verranno visualizzate se i peering sono stati configurati dal provider di livello 3. Se è stato eseguito il provisioning del circuito, deve essere possibile creare le connessioni.
   > 
2. È ora possibile avviare il provisioning di una connessione per collegare il gateway della rete virtuale al circuito ExpressRoute. Fare clic su **Connessione** > **Aggiungi** per aprire il pannello **Aggiungi connessione** e configurare i valori. Vedere l'esempio di riferimento seguente.
    ![Aggiungere la schermata della connessione](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)  

3. Al termine della configurazione, l'oggetto connessione visualizzerà le informazioni per la connessione.
     ![Schermata dell'oggetto connessione](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)

### <a name="to-delete-a-connection"></a>Per eliminare una connessione
È possibile eliminare una connessione selezionando l'icona **Elimina** nel pannello della connessione.

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Collegare una rete virtuale di un'altra sottoscrizione a un circuito
È possibile condividere un circuito ExpressRoute tra più sottoscrizioni. La figura seguente mostra un semplice schema del funzionamento della condivisione di circuiti ExpressRoute tra più sottoscrizioni.

Ciascuno dei cloud più piccoli nel cloud di grandi dimensioni viene usato per rappresentare le sottoscrizioni appartenenti a reparti diversi di un'organizzazione. Ciascun reparto dell'organizzazione può usare la propria sottoscrizione per distribuire i servizi, ma i reparti possono condividere un singolo circuito ExpressRoute per la connessione alla rete locale. Un singolo reparto (in questo esempio, IT) può possedere il circuito ExpressRoute. Altre sottoscrizioni all'interno dell'organizzazione possono usare il circuito ExpressRoute.

> [!NOTE]
> I costi relativi a connettività e larghezza di banda per il circuito dedicato saranno addebitati al proprietario del circuito ExpressRoute. Tutte le reti virtuali condividono la stessa larghezza di banda.
> 
> 
![Connettività tra sottoscrizioni](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Amministrazione
Il *proprietario del circuito* è l'utente esperto autorizzato della risorsa circuito ExpressRoute. Il proprietario del circuito può creare le autorizzazioni che possono essere riscattate dagli *utenti del circuito*. *Gli utenti del circuito* sono i proprietari dei gateway di rete virtuale (che non sono nella stessa sottoscrizione del circuito ExpressRoute). *Gli utenti del circuito* possono riscattare le autorizzazioni (un'autorizzazione per ogni rete virtuale).

Il *proprietario del circuito* ha la facoltà di modificare e revocare le autorizzazioni in qualsiasi momento. La revoca dell'autorizzazione comporterà l'eliminazione di tutti i collegamenti dalla sottoscrizione di cui è stato revocato l'accesso.

### <a name="circuit-owner-operations"></a>Operazioni del proprietario del circuito

**Creazione di un'autorizzazione**

Il proprietario del circuito crea un'autorizzazione. Questo comporta la creazione di una chiave di autorizzazione che può essere utilizzata da un utente del circuito per connettere i gateway di rete virtuale al circuito ExpressRoute. Un'autorizzazione è valida per una sola connessione.

1. Nel pannello di ExpressRoute fare clic su **Authorizations** (Autorizzazioni), digitare un **nome** per l'autorizzazione e fare clic su **Save** (Salva).
![Connettività tra sottoscrizioni](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)

2. Dopo che la configurazione è stata salvata, copiare l'**ID risorsa** e la **chiave di autorizzazione**.
![Connettività tra sottoscrizioni](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**Eliminazione delle autorizzazioni**

Per eliminare una connessione, selezionare l'icona relativa nel pannello della connessione.

### <a name="circuit-user-operations"></a>Operazioni dell'utente del circuito

   > [!NOTE]
   > L'utente del circuito deve richiedere l'ID risorsa e una chiave di autorizzazione al proprietario del circuito. 

**Riscatto delle autorizzazioni di collegamento**

1.  Fare clic sul pulsante **+ New** (Nuovo).
    ![Connettività tra sottoscrizioni](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)

2.  Cercare **"Connection"** (Connessione) in Marketplace, selezionare l'opzione e fare clic **Create** (Crea).
    ![Connettività tra sottoscrizioni](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)

3.  Verificare che **Connection type** (Tipo di connessione) sia impostato su "ExpressRoute".

4.  Inserire i dettagli e fare clic su **OK** nella scheda Basics (Nozioni di base).
    ![Connettività tra sottoscrizioni](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)

5.  Nella scheda **Settings** (Impostazioni) selezionare **Virtual network gateway** (Gateway di rete virtuale) e selezionare la casella di controllo **Redeem authorization** (Riscatta autorizzazione).

6.  Immettere i valori nei campi **Authorization key** (Chiave di autorizzazione) e **Peer circuit URI** (URI circuito peer) e assegnare un nome di connessione. Fare clic su **OK**
    ![Connettività tra sottoscrizioni](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)

7.  Verificare le informazioni nella scheda **Summary** (Riepilogo) scheda e fare clic su **OK**

**Rilascio delle autorizzazioni di collegamento**

È possibile rilasciare un'autorizzazione eliminando il collegamento del circuito ExpressRoute alla rete virtuale.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).



<!--HONumber=Feb17_HO1-->


