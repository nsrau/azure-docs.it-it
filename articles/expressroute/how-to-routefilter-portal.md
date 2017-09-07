---
title: 'Configurare i filtri di route per il peering Microsoft Azure ExpressRoute: Portale | Microsoft Docs'
description: Questo articolo descrive come configurare i filtri di route per il peering Microsoft con il portale di Azure.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/25/2017
ms.author: ganesr;cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: f17bf3e475a33cfc617e8a026e9606b3792101f3
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---
# <a name="configure-route-filters-for-microsoft-peering"></a>Configurare i filtri di route per il peering Microsoft

I filtri di route rappresentano un modo per usare un subset di servizi supportati tramite il peering Microsoft. I passaggi descritti in questo articolo consentono di configurare e gestire i filtri di route per i circuiti ExpressRoute.

I servizi Dynamics 365 e i servizi Office 365, come Exchange Online, SharePoint Online e Skype for Business sono accessibili tramite il peering Microsoft. Quando si configura il peering Microsoft in un circuito ExpressRoute, tutti i prefissi relativi a questi servizi vengono annunciati tramite le sessioni BGP stabilite. A ogni prefisso viene associato un valore di community BGP per identificare il servizio offerto tramite il prefisso. Per un elenco dei valori di community BGP e i servizi a cui sono associati, vedere [community BGP](expressroute-routing.md#bgp).

Se è necessaria la connettività a tutti i servizi, tramite BGP viene annunciato un numero elevato di prefissi. Ciò aumenta notevolmente le dimensioni delle tabelle di route gestite dai router all'interno della rete. Se si prevede di usare solo un subset dei servizi offerti tramite il peering Microsoft, è possibile ridurre le dimensioni delle tabelle di route in due modi. È possibile:

- Escludere i prefissi indesiderati applicando filtri di route alle community BGP. Si tratta di una procedura di rete standard usata comunemente in molte reti.

- Definire i filtri di route e applicarli al circuito ExpressRoute. Un filtro di route è una nuova risorsa che consente di selezionare l'elenco dei servizi che si prevede di usare tramite il peering Microsoft. I router ExpressRoute inviano solo l'elenco dei prefissi che appartengono ai servizi identificati nel filtro di route.

### <a name="about"></a>Informazioni sui filtri di route

Quando il peering Microsoft è configurato nel circuito ExpressRoute, i router perimetrali Microsoft stabiliscono una coppia di sessioni BGP con i router perimetrali (dell'utente o del provider di connettività). Non viene annunciata alcuna route per la rete. Per abilitare gli annunci delle ruote per la rete, è necessario associare un filtro di route.

Un filtro di route consente di identificare i servizi da usare tramite il peering Microsoft del circuito di ExpressRoute. Si tratta fondamentalmente di un elenco di tutti i valori di community BGP consentiti. Dopo aver definito una risorsa filtro di route e averla associata a un circuito ExpressRoute, tutti i prefissi corrispondenti ai valori di community BGP vengono annunciati per la rete.

Per poter associare i filtri di route ai servizi Office 365, è necessario essere autorizzati all'uso dei servizi Office 365 tramite ExpressRoute. Se non si è autorizzati a usare i servizi Office 365 tramite ExpressRoute, l'operazione di associazione dei filtri di route non riesce. Per altre informazioni sul processo di autorizzazione, vedere [Azure ExpressRoute per Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd). Per la connettività ai servizi Dynamics 365 NON sono richieste autorizzazioni preliminari.

> [!IMPORTANT]
> Per i circuiti ExpressRoute configurati prima del 1 agosto 2017, tutti i prefissi dei servizi verranno annunciati tramite il peering Microsoft, anche in mancanza di filtri di route definiti. Per il peering Microsoft dei circuiti ExpressRoute configurati dopo il 1 agosto 2017 non verrà annunciato alcun prefisso fino a quando non viene associato un filtro di route al circuito.
> 
> 

### <a name="workflow"></a>Flusso di lavoro

Per riuscire a connettersi correttamente ai servizi tramite il peering Microsoft, è necessario completare i passaggi di configurazione seguenti:

- È necessario avere un circuito ExpressRoute attivo, per cui è stato effettuato il provisioning del peering Microsoft. È possibile usare le istruzioni seguenti per eseguire queste attività:
  - [Creare un circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e fare in modo che venga abilitato dal provider di connettività prima di procedere. È necessario che sia stato effettuato il provisioning del circuito ExpressRoute e che il circuito sia in stato abilitato.
  - [Creare il peering Microsoft](expressroute-howto-routing-portal-resource-manager.md) se si gestisce direttamente la sessione BGP. In caso contrario, richiedere al provider della connettività di effettuare il provisioning del peering Microsoft per il circuito.

-  È necessario creare e configurare un filtro di route.
    - Identificare i servizi da usare tramite il peering Microsoft
    - Identificare l'elenco dei valori di community BGP associati ai servizi
    - Creare una regola per consentire l'elenco di prefissi corrispondenti ai valori di community BGP

-  È necessario associare il filtro di route al circuito ExpressRoute.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la configurazione, assicurarsi che siano soddisfatti i criteri seguenti:

 - Prima di iniziare la configurazione, verificare i [prerequisiti](expressroute-prerequisites.md) e i [flussi di lavoro](expressroute-workflows.md).

 - È necessario avere un circuito ExpressRoute attivo. Seguire le istruzioni per [creare un circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e fare in modo che venga abilitato dal provider di connettività prima di procedere. È necessario che sia stato effettuato il provisioning del circuito ExpressRoute e che il circuito sia in stato abilitato.

 - È necessario disporre di un peering Microsoft attivo. Seguire le istruzioni per [creare e modificare la configurazione del peering](expressroute-howto-routing-portal-resource-manager.md).


## <a name="prefixes"></a>Passaggio 1. Ottenere un elenco di prefissi e di valori di community BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Ottenere un elenco dei valori di community BGP

I valori di community BGP associati ai servizi accessibili tramite il peering Microsoft sono disponibili nella pagina [Requisiti per il routing di ExpressRoute](expressroute-routing.md).

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Creare un elenco dei valori che si vuole usare

Creare un elenco dei valori di community BGP che si vuole usare nel filtro di route. Ad esempio, il valore di community BGP per i servizi Dynamics 365 è 12076:5040.

## <a name="filter"></a>Passaggio 2. Creare un filtro di route e una regola di filtro

Un filtro di route può includere una sola regola di tipo 'Consenti'. A questa regola può essere associato un elenco di valori di community BGP.

### <a name="1-create-a-route-filter"></a>1. Creare un filtro di route
È possibile creare un filtro di route selezionando l'opzione che consente di creare una nuova risorsa. Fare clic su **Nuovo** > **Rete** > **Filtro della route**, come illustrato nell'immagine seguente:

![Creare un filtro di route](.\media\how-to-routefilter-portal\CreateRouteFilter1.png)

Il filtro di route deve essere inserito in un gruppo di risorse. 

![Creare un filtro di route](.\media\how-to-routefilter-portal\CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. Creare una regola di filtro

È possibile aggiungere e aggiornare regole selezionando la scheda di gestione delle regole per il filtro di route.

![Creare un filtro di route](.\media\how-to-routefilter-portal\ManageRouteFilter.png)


Nell'elenco a discesa è possibile selezionare i servizi a cui si intende connettersi e, al termine, salvare la regola.

![Creare un filtro di route](.\media\how-to-routefilter-portal\AddRouteFilterRule.png)


## <a name="attach"></a>Passaggio 3. Associare il filtro di route a un circuito ExpressRoute

È possibile associare il filtro di route a un circuito facendo clic sul pulsante "Aggiungi circuito" e selezionando il circuito ExpressRoute nell'elenco a discesa.

![Creare un filtro di route](.\media\how-to-routefilter-portal\AddCktToRouteFilter.png)

## <a name="getproperties"></a>Per ottenere le proprietà di un filtro di route

È possibile visualizzare le proprietà di un filtro di route quando si apre la risorsa nel portale.

![Creare un filtro di route](.\media\how-to-routefilter-portal\ViewRouteFilter.png)


## <a name="updateproperties"></a>Per aggiornare le proprietà di un filtro di route

È possibile aggiornare l'elenco dei valori di community BGP associato a un circuito facendo clic sul pulsante "Gestisci regola".


![Creare un filtro di route](.\media\how-to-routefilter-portal\ManageRouteFilter.png)

![Creare un filtro di route](.\media\how-to-routefilter-portal\AddRouteFilterRule.png) 


## <a name="detach"></a>Per rimuovere l'associazione di un filtro di route da un circuito ExpressRoute

Per disconnettere un circuito dal filtro di route, fare clic con il pulsante destro del mouse sul circuito e fare clic su "annulla associazione".

![Creare un filtro di route](.\media\how-to-routefilter-portal\DetachRouteFilter.png) 


## <a name="delete"></a>Per eliminare un filtro di route

È possibile eliminare un filtro di route selezionando il pulsante di eliminazione. 

![Creare un filtro di route](.\media\how-to-routefilter-portal\DeleteRouteFilter.png) 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).
