---
title: Come configurare il routing dell'hub virtuale
titleSuffix: Azure Virtual WAN
description: Questo articolo descrive come configurare il routing dell'hub virtuale
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 07/07/2020
ms.author: cherylmc
ms.openlocfilehash: 634967fbe50c74770bdc3b526d68896728e4ee07
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513061"
---
# <a name="how-to-configure-virtual-hub-routing"></a>Come configurare il routing dell'hub virtuale

Un hub virtuale può contenere più gateway, ad esempio un gateway VPN da sito a sito, un gateway ExpressRoute, un gateway da punto a sito e un firewall di Azure. Le funzionalità di routing nell'hub virtuale sono fornite da un router che gestisce tutto il routing, incluso il routing di transito, tra i gateway usando Border Gateway Protocol (BGP). Questo router fornisce anche la connettività di transito tra le reti virtuali che si connettono a un hub virtuale e può supportare fino a una velocità effettiva aggregata di 50 Gbps. Queste funzionalità di routing si applicano ai clienti WAN virtuali standard.

Per ulteriori informazioni, vedere [About Virtual Hub routing](about-virtual-hub-routing.md).

> [!NOTE]
> Alcune di queste funzionalità potrebbero ancora essere implementate. Questa operazione dovrebbe essere completata entro il 17 agosto.
>

## <a name="create-a-route-table"></a><a name="create-table"></a>Creare una tabella di route

1. Nella portale di Azure passare all'hub virtuale.
2. In **connettività**selezionare **routing**. Nella pagina routing vengono visualizzate le tabelle di route **default** e **None** .

   :::image type="content" source="./media/how-to-virtual-hub-routing/routing.png" alt-text="Pagina di routing":::
3. Selezionare **+ Crea tabella di route** per aprire la pagina **Crea tabella di route** .
4. Nella scheda Crea **nozioni di base** della pagina della tabella di route completare i campi seguenti.

   :::image type="content" source="./media/how-to-virtual-hub-routing/basics.png" alt-text="Scheda Informazioni di base":::

   * **Nome**
   * **Route**
   * **Nome route**
   * **Tipo di destinazione**
   * **Prefisso di destinazione**: è possibile aggregare i prefissi. Ad esempio: VNet 1:10.1.0.0/24 e VNet 2:10.1.1.0/24 possono essere aggregati come 10.1.0.0/16. Le route di **ramo** si applicano a tutti i siti VPN connessi, circuiti ExpressRoute e connessioni VPN utente.
   * **Hop successivo**: un elenco di connessioni di rete virtuale o di Azure firewall.

     Se si seleziona una connessione di rete virtuale, si vedrà **configure static routes**. Si tratta di un'impostazione di configurazione facoltativa. Per altre informazioni, vedere [Configuring static routes](about-virtual-hub-routing.md#static).

      :::image type="content" source="./media/how-to-virtual-hub-routing/next-hop.png" alt-text="Hop successivo":::

5. Selezionare la scheda **etichette** per configurare i nomi delle etichette. Le etichette forniscono un meccanismo per raggruppare logicamente le tabelle di route.

    :::image type="content" source="./media/how-to-virtual-hub-routing/labels.png" alt-text="Configurare i nomi delle etichette":::

6. Selezionare la scheda **associazioni** per associare le connessioni alla tabella di route.
Vengono visualizzati i **rami**, le **reti virtuali**e le **impostazioni correnti** delle connessioni.

    :::image type="content" source="./media/how-to-virtual-hub-routing/associations.png" alt-text="Connessioni di associazione alla tabella di route":::

7. Selezionare la scheda **propagazioni** per propagare le route dalle connessioni alla tabella di route.

    :::image type="content" source="./media/how-to-virtual-hub-routing/propagations.png" alt-text="Propagazione Route":::

8. Selezionare **Crea** per creare la tabella di route.

## <a name="to-edit-a-route-table"></a><a name="edit-table"></a>Per modificare una tabella di route

Nella portale di Azure individuare la tabella di route dell'hub virtuale. Selezionare la tabella di route per modificare le informazioni.

## <a name="to-delete-a-route-table"></a><a name="delete-table"></a>Per eliminare una tabella route

Nella portale di Azure individuare la tabella di route dell'hub virtuale. Non è possibile eliminare una tabella di route predefinita o None. Tuttavia, è possibile eliminare tutte le tabelle di route personalizzate. Fare clic su **"..."**, quindi selezionare **Elimina**.

## <a name="to-view-effective-routes"></a><a name="view-routes"></a>Per visualizzare le route valide

Nella portale di Azure individuare la tabella di route dell'hub virtuale. Fare clic su **"..."** e selezionare **route valide** per visualizzare le route apprese dalla tabella di route selezionata. Le route propagate dalla connessione alla tabella di route vengono popolate automaticamente in **route valide** della tabella di route. Per ulteriori informazioni, vedere [informazioni sulle route valide](effective-routes-virtual-hub.md).

:::image type="content" source="./media/how-to-virtual-hub-routing/effective.png" alt-text="Visualizza route valide" lightbox="./media/how-to-virtual-hub-routing/effective-expand.png":::

## <a name="to-set-up-routing-configuration-for-a-virtual-network-connection"></a><a name="routing-configuration"></a>Per impostare la configurazione del routing per una connessione di rete virtuale

1. Nella portale di Azure passare alla rete WAN virtuale e in **connettività**selezionare **connessioni di rete virtuale**.
1. Selezionare **+ Aggiungi connessione**.
1. Selezionare la rete virtuale nell'elenco a discesa.
1. Configurare la configurazione di routing da associare a una tabella di route. Per **associa tabella di route**selezionare la tabella di route nell'elenco a discesa.
1. Configurare la configurazione del routing per la propagazione a una o più tabelle di route. Per **propaga a tabella di route**, selezionare dall'elenco a discesa.
1. Per le **Route statiche**, configurare le route statiche per l'appliance virtuale di rete (se applicabile). La rete WAN virtuale supporta un singolo IP hop successivo per la route statica in una connessione di rete virtuale. Se, ad esempio, si dispone di un'appliance virtuale separata per i flussi di traffico in ingresso e in uscita, è consigliabile fare in modo che le appliance virtuali in reti virtuali separate e alleghino il reti virtuali all'hub virtuale.


:::image type="content" source="./media/how-to-virtual-hub-routing/routing-configuration.png" alt-text="Configurare la configurazione del routing" lightbox="./media/how-to-virtual-hub-routing/routing-configuration-expand.png":::

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).
Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti](virtual-wan-faq.md).
