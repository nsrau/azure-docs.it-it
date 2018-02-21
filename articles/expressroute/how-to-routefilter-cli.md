---
title: 'Configurare i filtri di route per il peering Microsoft Azure ExpressRoute: Interfaccia della riga di comando | Microsoft Docs'
description: Questo articolo descrive come configurare i filtri di route per il peering Microsoft usando l'interfaccia della riga di comando di Azure
documentationcenter: na
services: expressroute
author: anzaman
manager: ganesr
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: anzaman
ms.openlocfilehash: a85a68393f3dc946db651791de9efff0694f9989
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>Configurare i filtri di route per il peering Microsoft: interfaccia della riga di comando di Azure

> [!div class="op_single_selector"]
> * [Portale di Azure](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Interfaccia della riga di comando di Azure](how-to-routefilter-cli.md)
> 

I filtri di route rappresentano un modo per usare un subset di servizi supportati tramite il peering Microsoft. I passaggi descritti in questo articolo consentono di configurare e gestire i filtri di route per i circuiti ExpressRoute.

I servizi Dynamics 365 e i servizi Office 365, come Exchange Online, SharePoint Online e Skype for Business sono accessibili tramite il peering Microsoft. Quando si configura il peering Microsoft in un circuito ExpressRoute, tutti i prefissi relativi a questi servizi vengono annunciati tramite le sessioni BGP stabilite. A ogni prefisso viene associato un valore di community BGP per identificare il servizio offerto tramite il prefisso. Per un elenco dei valori di community BGP e i servizi a cui sono associati, vedere [community BGP](expressroute-routing.md#bgp).

Se è necessaria la connettività a tutti i servizi, tramite BGP viene annunciato un numero elevato di prefissi. Ciò aumenta notevolmente le dimensioni delle tabelle di route gestite dai router all'interno della rete. Se si prevede di usare solo un subset dei servizi offerti tramite il peering Microsoft, è possibile ridurre le dimensioni delle tabelle di route in due modi. È possibile:

* Escludere i prefissi indesiderati applicando filtri di route alle community BGP. Si tratta di una procedura di rete standard usata comunemente in molte reti.

* Definire i filtri di route e applicarli al circuito ExpressRoute. Un filtro di route è una nuova risorsa che consente di selezionare l'elenco dei servizi che si prevede di usare tramite il peering Microsoft. I router ExpressRoute inviano solo l'elenco dei prefissi che appartengono ai servizi identificati nel filtro di route.

### <a name="about"></a>Informazioni sui filtri di route

Quando il peering Microsoft è configurato nel circuito ExpressRoute, i router perimetrali Microsoft stabiliscono una coppia di sessioni BGP con i router perimetrali (dell'utente o del provider di connettività). Non viene annunciata alcuna route per la rete. Per abilitare gli annunci delle ruote per la rete, è necessario associare un filtro di route.

Un filtro di route consente di identificare i servizi da usare tramite il peering Microsoft del circuito di ExpressRoute. Si tratta fondamentalmente di un elenco di tutti i valori di community BGP consentiti. Dopo aver definito una risorsa filtro di route e averla associata a un circuito ExpressRoute, tutti i prefissi corrispondenti ai valori di community BGP vengono annunciati per la rete.

Per poter associare i filtri di route ai servizi Office 365, è necessario essere autorizzati all'uso dei servizi Office 365 tramite ExpressRoute. Se non si è autorizzati a usare i servizi Office 365 tramite ExpressRoute, l'operazione di associazione dei filtri di route non riesce. Per altre informazioni sul processo di autorizzazione, vedere [Azure ExpressRoute per Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd). Per la connettività ai servizi Dynamics 365 NON sono richieste autorizzazioni preliminari.

> [!IMPORTANT]
> Per i circuiti ExpressRoute configurati prima del 1 agosto 2017, tutti i prefissi dei servizi verranno annunciati tramite il peering Microsoft, anche in mancanza di filtri di route definiti. Il peering Microsoft dei circuiti ExpressRoute che vengono configurati dopo il 1° agosto 2017 non avrà alcun prefisso annunciato fino a quando non viene associato un filtro di route per il circuito.
> 
> 

### <a name="workflow"></a>Flusso di lavoro

Per riuscire a connettersi correttamente ai servizi tramite il peering Microsoft, è necessario completare i passaggi di configurazione seguenti:

* È necessario avere un circuito ExpressRoute attivo, per cui è stato effettuato il provisioning del peering Microsoft. È possibile usare le istruzioni seguenti per eseguire queste attività:
  * [Creare un circuito ExpressRoute](howto-circuit-cli.md) e fare in modo che venga abilitato dal provider di connettività prima di procedere. È necessario che sia stato effettuato il provisioning del circuito ExpressRoute e che il circuito sia in stato abilitato.
  * [Creare il peering Microsoft](howto-routing-cli.md) se si gestisce direttamente la sessione BGP. In caso contrario, richiedere al provider della connettività di effettuare il provisioning del peering Microsoft per il circuito.

* È necessario creare e configurare un filtro di route.
  * Identificare i servizi da usare tramite il peering Microsoft
  * Identificare l'elenco dei valori di community BGP associati ai servizi
  * Creare una regola per consentire l'elenco di prefissi corrispondenti ai valori di community BGP

* È necessario associare il filtro di route al circuito ExpressRoute.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare, installare la versione più recente dei comandi dell'interfaccia della riga di comando (2.0 o successiva). Per informazioni sull'installazione dei comandi dell'interfaccia della riga di comando, vedere [Install Azure CLI 2.0](/cli/azure/install-azure-cli) (Installare l'interfaccia della riga di comando di Azure 2.0) e [Get Started with Azure CLI 2.0](/cli/azure/get-started-with-azure-cli) (Introduzione all'interfaccia della riga di comando di Azure 2.0).

* Prima di iniziare la configurazione, verificare i [prerequisiti](expressroute-prerequisites.md) e i [flussi di lavoro](expressroute-workflows.md).

* È necessario avere un circuito ExpressRoute attivo. Seguire le istruzioni per [creare un circuito ExpressRoute](howto-circuit-cli.md) e fare in modo che venga abilitato dal provider di connettività prima di procedere. È necessario che sia stato effettuato il provisioning del circuito ExpressRoute e che il circuito sia in stato abilitato.

* È necessario disporre di un peering Microsoft attivo. Seguire le istruzioni per [creare e modificare la configurazione del peering](howto-routing-cli.md).

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Accedere al proprio account Azure e selezionare la sottoscrizione

Per iniziare la configurazione, accedere al proprio account Azure. Per eseguire la connessione, usare gli esempi che seguono:

```azurecli
az login
```

Controllare le sottoscrizioni per l'account.

```azurecli
az account list
```

Selezionare la sottoscrizione per la quale si vuole creare un circuito ExpressRoute.

```azurecli
az account set --subscription "<subscription ID>"
```

## <a name="prefixes"></a>Passaggio 1: Ottenere un elenco di prefissi e di valori di community BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Ottenere un elenco dei valori di community BGP

Usare il cmdlet seguente per ottenere l'elenco dei valori di community BGP associati ai servizi accessibili tramite il peering Microsoft e l'elenco dei prefissi associati:

```azurecli
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Creare un elenco dei valori che si vuole usare

Creare un elenco dei valori di community BGP che si vuole usare nel filtro di route. Ad esempio, il valore di community BGP per i servizi Dynamics 365 è 12076:5040.

## <a name="filter"></a>Passaggio 2: Creare un filtro di route e una regola di filtro

Un filtro di route può includere una sola regola di tipo 'Consenti'. A questa regola può essere associato un elenco di valori di community BGP.

### <a name="1-create-a-route-filter"></a>1. Creare un filtro di route

Creare prima di tutto il filtro di route. Il comando 'az network route-filter create' crea solo una risorsa filtro di route. Dopo aver creato la risorsa, è necessario creare una regola e associarla all'oggetto filtro di route. Eseguire il comando seguente per creare una risorsa filtro di route:

```azurecli
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2. Creare una regola di filtro

Eseguire il comando seguente per creare una nuova regola:
 
```azurecli
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="attach"></a>Passaggio 3: Associare il filtro di route a un circuito ExpressRoute

Eseguire il comando seguente per associare il filtro di route al circuito ExpressRoute:

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="tasks"></a>Attività comuni

### <a name="getproperties"></a>Per ottenere le proprietà di un filtro di route

Per ottenere le proprietà di un filtro di route, usare il comando seguente:

```azurecli
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="updateproperties"></a>Per aggiornare le proprietà di un filtro di route

Se il filtro di route è già associato a un circuito, gli aggiornamenti all'elenco di community BGP includono la propagazione automatica delle modifiche per gli annunci dei prefissi tramite le sessioni BGP stabilite. È possibile aggiornare l'elenco di community BGP del filtro di route con il comando seguente:

```azurecli
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="detach"></a>Per rimuovere l'associazione di un filtro di route da un circuito ExpressRoute

Dopo aver rimosso l'associazione di un filtro di route dal circuito ExpressRoute, nessun prefisso viene annunciato tramite la sessione BGP. Usare il comando seguente per rimuovere l'associazione di un filtro di route da un circuito ExpressRoute:

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="delete"></a>Per eliminare un filtro di route

È possibile eliminare un filtro di route solo se non è associato a un circuito. Assicurarsi che il filtro di route non sia associato a un circuito prima di tentare di eliminarlo. È possibile eliminare un filtro di route con il comando seguente:

```azurecli
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).
