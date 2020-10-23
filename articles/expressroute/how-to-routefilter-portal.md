---
title: 'Esercitazione: Configurare i filtri di route per il peering Microsoft - Portale di Azure'
description: Questa esercitazione descrive come configurare i filtri di route per il peering Microsoft con il portale di Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: d142028d197f9e279b5f1e05757946dc40d2c153
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92109137"
---
# <a name="tutorial-configure-route-filters-for-microsoft-peering-using-the-azure-portal"></a>Esercitazione: Configurare i filtri di route per il peering Microsoft usando il Portale di Azure

> [!div class="op_single_selector"]
> * [Portale di Azure](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Interfaccia della riga di comando di Azure](how-to-routefilter-cli.md)
> 

I filtri di route rappresentano un modo per usare un subset di servizi supportati tramite il peering Microsoft. I passaggi descritti in questo articolo consentono di configurare e gestire i filtri di route per i circuiti ExpressRoute.

I servizi di Microsoft 365 come Exchange Online, SharePoint Online e Skype for Business sono accessibili tramite il peering Microsoft. Quando il peering Microsoft viene configurato in un circuito ExpressRoute, tutti i prefissi relativi a questi servizi vengono annunciati tramite le sessioni BGP stabilite. A ogni prefisso viene associato un valore di community BGP per identificare il servizio offerto tramite il prefisso. Per un elenco dei valori di community BGP e i servizi a cui sono associati, vedere [community BGP](expressroute-routing.md#bgp).

Con la connettività a tutti i servizi di Azure e Microsoft 365 viene annunciato un numero elevato di prefissi tramite BGP. Questo numero elevato aumenta notevolmente le dimensioni delle tabelle di route gestite dai router all'interno della rete. Se si prevede di usare solo un subset dei servizi offerti tramite il peering Microsoft, è possibile ridurre le dimensioni delle tabelle di route in due modi. è possibile:

* Escludere i prefissi indesiderati applicando filtri di route alle community BGP. Il filtro di route è una procedura di rete standard usata comunemente in molte reti.

* Definire i filtri di route e applicarli al circuito ExpressRoute. Un filtro di route è una nuova risorsa che consente di selezionare l'elenco dei servizi che si prevede di usare tramite il peering Microsoft. I router ExpressRoute inviano solo l'elenco dei prefissi che appartengono ai servizi identificati nel filtro di route.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> - Ottenere i valori di community BGP.
> - Creare un filtro di route e una regola di filtro.
> - Associare il filtro di route a un circuito ExpressRoute.

### <a name="about-route-filters"></a><a name="about"></a>Informazioni sui filtri di route

Quando il peering Microsoft viene configurato nel circuito ExpressRoute, i router perimetrali Microsoft stabiliscono una coppia di sessioni BGP con i router perimetrali dell'utente attraverso il provider di connettività. Non viene annunciata alcuna route per la rete. Per abilitare gli annunci delle ruote per la rete, è necessario associare un filtro di route.

Un filtro di route consente di identificare i servizi da usare tramite il peering Microsoft del circuito di ExpressRoute. Si tratta fondamentalmente di un elenco elementi consentiti di tutti i valori di community BGP. Dopo aver definito una risorsa filtro di route e averla associata a un circuito ExpressRoute, tutti i prefissi corrispondenti ai valori di community BGP vengono annunciati alla rete.

Per associare i filtri di route ai servizi Microsoft 365, è necessario avere l'autorizzazione per l'utilizzo di tali servizi tramite ExpressRoute. Se non si è autorizzati a utilizzare i servizi Microsoft 365 tramite ExpressRoute, l'operazione di associazione dei filtri di route non riesce. Per altre informazioni sul processo di autorizzazione, vedere [Azure ExpressRoute per Microsoft 365](/microsoft-365/enterprise/azure-expressroute).

> [!IMPORTANT]
> Il peering Microsoft dei circuiti ExpressRoute che sono stati configurati prima del 1° agosto 2017, avranno tutti i prefissi di servizio pubblicati tramite il peering Microsoft, anche se non sono definiti i filtri di route. Il peering Microsoft dei circuiti ExpressRoute che vengono configurati dopo il 1° agosto 2017 non avrà alcun prefisso annunciato fino a quando non viene associato un filtro di route per il circuito.
> 

## <a name="prerequisites"></a>Prerequisiti

- Prima di iniziare la configurazione, verificare i [prerequisiti](expressroute-prerequisites.md) e i [flussi di lavoro](expressroute-workflows.md).

- È necessario avere un circuito ExpressRoute attivo, per cui è stato effettuato il provisioning del peering Microsoft. È possibile usare le istruzioni seguenti per eseguire queste attività:
  - [Creare un circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e chiedere al provider di connettività di abilitarlo prima di continuare. È necessario che sia stato effettuato il provisioning del circuito ExpressRoute e che il circuito sia in stato abilitato.
  - [Creare il peering Microsoft](expressroute-howto-routing-portal-resource-manager.md) se si gestisce direttamente la sessione BGP. In caso contrario, richiedere al provider della connettività di effettuare il provisioning del peering Microsoft per il circuito.

## <a name="get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Ottenere un elenco di prefissi e di valori di community BGP

### <a name="get-a-list-of-bgp-community-values"></a>Ottenere un elenco dei valori di community BGP

I valori di community BGP associati ai servizi accessibili tramite il peering Microsoft sono disponibili nella pagina [Requisiti per il routing di ExpressRoute](expressroute-routing.md).

### <a name="make-a-list-of-the-values-that-you-want-to-use"></a>Creare un elenco dei valori che si vuole usare

Creare un elenco dei [valori di community BGP](expressroute-routing.md#bgp) da usare nel filtro di route. 

## <a name="create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Creare un filtro di route e una regola di filtro

Un filtro di route può includere una sola regola di tipo 'Consenti'. A questa regola può essere associato un elenco di valori di community BGP.

1. Selezionare **Crea una risorsa**, quindi cercare *Filtro della route*, come illustrato nell'immagine seguente:

    :::image type="content" source="./media/how-to-routefilter-portal/create-route-filter.png" alt-text="Screenshot che mostra la pagina Filtro della route":::

1. Inserire il filtro della route in un gruppo di risorse. Assicurarsi che la località sia la stessa del circuito ExpressRoute. Selezionare **Rivedi e crea** e quindi **Crea**.

    :::image type="content" source="./media/how-to-routefilter-portal/create-route-filter-basic.png" alt-text="Screenshot che mostra la pagina Filtro della route":::

### <a name="create-a-filter-rule"></a>Creare una regola di filtro

1. Per aggiungere e aggiornare regole, selezionare la scheda Gestisci regola per il filtro di route.

    :::image type="content" source="./media/how-to-routefilter-portal/manage-route-filter.png" alt-text="Screenshot che mostra la pagina Filtro della route":::

1. Selezionare i servizi a cui connettersi nell'elenco a discesa e al termine salvare la regola.

    :::image type="content" source="./media/how-to-routefilter-portal/add-route-filter-rule.png" alt-text="Screenshot che mostra la pagina Filtro della route":::

## <a name="attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Associare il filtro di route a un circuito ExpressRoute

Associare il filtro di route a un circuito facendo clic sul pulsante **+ Aggiungi circuito** e selezionando il circuito ExpressRoute nell'elenco a discesa.

:::image type="content" source="./media/how-to-routefilter-portal/add-circuit-to-route-filter.png" alt-text="Screenshot che mostra la pagina Filtro della route":::

Se il provider di connettività configura il peering per il circuito ExpressRoute, aggiornare il circuito ExpressRoute nella pagina corrispondente prima di selezionare il pulsante **+ Aggiungi circuito**.

:::image type="content" source="./media/how-to-routefilter-portal/refresh-express-route-circuit.png" alt-text="Screenshot che mostra la pagina Filtro della route":::

## <a name="common-tasks"></a><a name="tasks"></a>Attività comuni

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Per ottenere le proprietà di un filtro di route

È possibile visualizzare le proprietà di un filtro di route quando si apre la risorsa nel portale.

:::image type="content" source="./media/how-to-routefilter-portal/view-route-filter.png" alt-text="Screenshot che mostra la pagina Filtro della route":::

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Per aggiornare le proprietà di un filtro di route

1. È possibile aggiornare l'elenco dei valori di community BGP associato a un circuito facendo clic sul pulsante **Gestisci regola**.

    :::image type="content" source="./media/how-to-routefilter-portal/update-route-filter.png" alt-text="Screenshot che mostra la pagina Filtro della route":::

1. Selezionare le community di servizi e quindi **Salva**.

    :::image type="content" source="./media/how-to-routefilter-portal/add-route-filter-rule.png" alt-text="Screenshot che mostra la pagina Filtro della route":::

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Per rimuovere l'associazione di un filtro di route da un circuito ExpressRoute

Per annullare l'associazione di circuito dal filtro di route, fare clic con il pulsante destro del mouse sul circuito e scegliere **Annulla associazione**.

:::image type="content" source="./media/how-to-routefilter-portal/detach-route-filter.png" alt-text="Screenshot che mostra la pagina Filtro della route":::


## <a name="clean-up-resources"></a>Pulizia delle risorse

È possibile eliminare un filtro di route selezionando il pulsante **Elimina**. Prima di procedere, verificare che il filtro di route non sia associato ad alcun circuito.

:::image type="content" source="./media/how-to-routefilter-portal/delete-route-filter.png" alt-text="Screenshot che mostra la pagina Filtro della route":::

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su esempi di configurazione del router, vedere:

> [!div class="nextstepaction"]
> [Esempi di configurazione del router per l'impostazione e la gestione del routing](expressroute-config-samples-routing.md)
