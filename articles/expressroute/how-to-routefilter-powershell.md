---
title: 'Esercitazione: Configurare i filtri di route per il peering Microsoft - Azure PowerShell'
description: Questa esercitazione descrive come configurare i filtri di route per il peering Microsoft con PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 90d4def5a1c08e305b9315f299e83e2187b6be2c
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91969899"
---
# <a name="tutorial-configure-route-filters-for-microsoft-peering-using-powershell"></a>Esercitazione: Configurare i filtri di route per il peering Microsoft con PowerShell

> [!div class="op_single_selector"]
> * [Portale di Azure](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Interfaccia della riga di comando di Azure](how-to-routefilter-cli.md)
> 

I filtri di route rappresentano un modo per usare un subset di servizi supportati tramite il peering Microsoft. I passaggi descritti in questo articolo consentono di configurare e gestire i filtri di route per i circuiti ExpressRoute.

I servizi di Microsoft 365, come Exchange Online, SharePoint Online e Skype for Business, e i servizi pubblici di Azure, come l'archiviazione e i database SQL, sono accessibili tramite il peering Microsoft. È possibile scegliere i servizi pubblici di Azure in base all'area geografica, ma non è possibile definirli in base al servizio pubblico.

Quando il peering Microsoft viene configurato in un circuito ExpressRoute, tutti i prefissi relativi a questi servizi vengono annunciati tramite le sessioni BGP stabilite. A ogni prefisso viene associato un valore di community BGP per identificare il servizio offerto tramite il prefisso. Per un elenco dei valori di community BGP e i servizi a cui sono associati, vedere [community BGP](expressroute-routing.md#bgp).

Con la connettività a tutti i servizi di Azure e Microsoft 365 viene annunciato un numero elevato di prefissi tramite BGP. Questo numero elevato aumenta notevolmente le dimensioni delle tabelle di route gestite dai router all'interno della rete. Se si prevede di usare solo un subset dei servizi offerti tramite il peering Microsoft, è possibile ridurre le dimensioni delle tabelle di route in due modi. è possibile:

* Escludere i prefissi indesiderati applicando filtri di route alle community BGP. Il filtro delle route è una procedura di rete standard usata comunemente in molte reti.

* Definire i filtri di route e applicarli al circuito ExpressRoute. Un filtro di route è una nuova risorsa che consente di selezionare l'elenco dei servizi che si prevede di usare tramite il peering Microsoft. I router ExpressRoute inviano solo l'elenco dei prefissi che appartengono ai servizi identificati nel filtro di route.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> - Ottenere i valori della community BGP.
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
  - [Creare un circuito ExpressRoute](expressroute-howto-circuit-arm.md) e chiedere al provider di connettività di abilitarlo prima di continuare. È necessario che sia stato effettuato il provisioning del circuito ExpressRoute e che il circuito sia in stato abilitato.
  - [Creare il peering Microsoft](expressroute-circuit-peerings.md) se si gestisce direttamente la sessione BGP. In caso contrario, richiedere al provider della connettività di effettuare il provisioning del peering Microsoft per il circuito.
  
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Accedere al proprio account Azure e selezionare la sottoscrizione

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a> Ottenere un elenco di prefissi e di valori di community BGP

1. Usare il cmdlet seguente per ottenere l'elenco dei valori di community BGP e dei prefissi associati ai servizi accessibili tramite il peering Microsoft:

    ```azurepowershell-interactive
    Get-AzBgpServiceCommunity
    ```

1. Creare un elenco dei valori di community BGP che si vuole usare nel filtro di route.

## <a name="create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Creare un filtro di route e una regola di filtro

Un filtro di route può includere una sola regola di tipo 'Consenti'. A questa regola può essere associato un elenco di valori di community BGP. Il comando `az network route-filter create` crea solo una risorsa filtro di route. Dopo aver creato la risorsa, è necessario creare una regola e associarla all'oggetto filtro di route.

1. Per creare una risorsa filtro di route, eseguire il comando seguente:

    ```azurepowershell-interactive
    New-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
    ```

1. Per creare una regola di filtro di route, eseguire il comando seguente:
 
    ```azurepowershell-interactive
    $rule = New-AzRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList 12076:5010,12076:5040
    ```

1. Eseguire il comando seguente per aggiungere la regola di filtro al filtro di route:
 
    ```azurepowershell-interactive
    $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
    $routefilter.Rules.Add($rule)
    Set-AzRouteFilter -RouteFilter $routefilter
    ```

## <a name="attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Associare il filtro di route a un circuito ExpressRoute

Se si usano solo peer Microsoft, eseguire questo comando per associare il filtro route al circuito ExpressRoute:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="common-tasks"></a><a name="tasks"></a>Attività comuni

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Per ottenere le proprietà di un filtro di route

Per ottenere le proprietà di un filtro di route, seguire questa procedura:

1. Eseguire il comando seguente per ottenere una risorsa filtro di route:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   ```
2. Eseguire il comando seguente per ottenere le regole di filtro di route per la risorsa filtro di route:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   $rule = $routefilter.Rules[0]
   ```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Per aggiornare le proprietà di un filtro di route

Se il filtro di route è già associato a un circuito, gli aggiornamenti all'elenco di community BGP propagano automaticamente le modifiche agli annunci dei prefissi tramite la sessione BGP stabilita. È possibile aggiornare l'elenco di community BGP del filtro di route con il comando seguente:

```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzRouteFilter -RouteFilter $routefilter
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Per rimuovere l'associazione di un filtro di route da un circuito ExpressRoute

Dopo aver rimosso l'associazione di un filtro di route dal circuito ExpressRoute, nessun prefisso viene annunciato tramite la sessione BGP. Usare il comando seguente per rimuovere l'associazione di un filtro di route da un circuito ExpressRoute:
  
```azurepowershell-interactive
$ckt.Peerings[0].RouteFilter = $null
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="clean-up-resources"></a>Pulire le risorse

Si può eliminare un filtro di route solo se non è associato a un circuito. Assicurarsi che il filtro di route non sia associato a un circuito prima di provare a eliminarlo. È possibile eliminare un filtro di route con il comando seguente:

```azurepowershell-interactive
Remove-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su esempi di configurazione del router, vedere:

> [!div class="nextstepaction"]
> [Esempi di configurazione del router per l'impostazione e la gestione del routing](expressroute-config-samples-routing.md)
