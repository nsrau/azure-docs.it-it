---
title: 'Azure ExpressRoute: modificare un circuito: PowerShell'
description: Creazione, provisioning, verifica, aggiornamento, eliminazione e deprovisioning di un circuito ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 11/13/2019
ms.author: cherylmc
ms.openlocfilehash: 3ce1096129d7fb45901d80b0173035dcca47c3ee
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74080371"
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell"></a>Creare e modificare un circuito ExpressRoute mediante PowerShell
> [!div class="op_single_selector"]
> * [Portale di Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Interfaccia della riga di comando di Azure](howto-circuit-cli.md)
> * [Modello di Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Video - Portale di Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (classic)](expressroute-howto-circuit-classic.md) (PowerShell (classico))
>

Questo articolo include informazioni utili per creare un circuito ExpressRoute di Azure usando cmdlet di PowerShell e il modello di distribuzione di Azure Resource Manager. È anche possibile controllare lo stato, aggiornare, eliminare o effettuare il deprovisioning di un circuito.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la configurazione, verificare i [prerequisiti](expressroute-prerequisites.md) e i [flussi di lavoro](expressroute-workflows.md).

### <a name="working-with-azure-powershell"></a>Uso di Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="create"></a>Creare un circuito ExpressRoute ed eseguirne il provisioning
### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. accedere al proprio account Azure e selezionare la sottoscrizione

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. ottenere l'elenco dei provider, delle posizioni e delle larghezze di banda supportate
Prima di creare un circuito ExpressRoute, è necessario avere l'elenco delle località, delle opzioni di larghezza di banda e dei provider di connettività supportati.

Il cmdlet **Get-AzExpressRouteServiceProvider** di PowerShell restituisce queste informazioni, che verranno usate nei passaggi successivi:

```azurepowershell-interactive
Get-AzExpressRouteServiceProvider
```

Verificare se sia presente anche il proprio provider di connettività. Prendere nota delle informazioni seguenti, perché saranno necessarie al momento della creazione di un circuito:

* Nome
* PeeringLocations
* BandwidthsOffered

È ora possibile creare un circuito ExpressRoute.

### <a name="3-create-an-expressroute-circuit"></a>3. creare un circuito ExpressRoute
Se non si ha già un gruppo di risorse, è necessario crearne uno prima di creare il circuito ExpressRoute. A questo scopo, eseguire il comando seguente:

```azurepowershell-interactive
New-AzResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

L'esempio seguente illustra come creare un circuito ExpressRoute a 200 Mbps tramite Equinix nella Silicon Valley. Se si usa un altro provider e impostazioni diverse, sostituire tali informazioni al momento della richiesta. Usare l'esempio seguente per richiedere una nuova chiave di servizio:

```azurepowershell-interactive
New-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Verificare di aver specificato il livello e la famiglia SKU corretti:

* Il livello SKU determina se un circuito ExpressRoute è [locale](expressroute-faqs.md#expressroute-local), standard o [Premium](expressroute-faqs.md#expressroute-premium). È possibile specificare *local*, *standard* o *Premium*.
* La famiglia SKU determina il tipo di fatturazione. Specificare *Metereddata* per un piano dati a consumo e *Unlimiteddata* per un piano dati senza limiti. È possibile modificare il tipo di fatturazione da *Metereddata* a *Unlimiteddata*, ma *non* è possibile eseguire il passaggio *inverso*. Un circuito *locale* è sempre *Unlimiteddata*.

> [!IMPORTANT]
> Il circuito ExpressRoute viene addebitato dal momento in cui viene emessa una chiave di servizio. Verificare che l'operazione venga eseguita quando il provider di connettività è pronto a effettuare il provisioning del circuito.
>
>

La risposta contiene la chiave di servizio. È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo il comando seguente:

```azurepowershell-interactive
get-help New-AzExpressRouteCircuit -detailed
```


### <a name="4-list-all-expressroute-circuits"></a>4. elencare tutti i circuiti ExpressRoute
Per ottenere un elenco di tutti i circuiti ExpressRoute creati, eseguire il comando **Get-AzExpressRouteCircuit** :

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

La risposta sarà simile all'esempio di seguito:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                          }
    CircuitProvisioningState          : Enabled
    ServiceProviderProvisioningState  : NotProvisioned
    ServiceProviderNotes              :
    ServiceProviderProperties         : {
                                          "ServiceProviderName": "Equinix",
                                          "PeeringLocation": "Silicon Valley",
                                          "BandwidthInMbps": 200
                                        }
    ServiceKey                        : **************************************
    Peerings                          : []

È possibile recuperare queste informazioni in qualsiasi momento usando il cmdlet `Get-AzExpressRouteCircuit` . Se si effettua la chiamata senza parametri, verranno elencati tutti i circuiti. La chiave di servizio è elencata nel campo *ServiceKey* :

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```


La risposta sarà simile all'esempio di seguito:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                          }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : NotProvisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. inviare la chiave di servizio al provider di connettività per il provisioning
*ServiceProviderProvisioningState* offre informazioni sullo stato di provisioning corrente sul lato provider del servizio. Status indica lo stato sul lato Microsoft. Per altre informazioni sullo stato di provisioning dei circuiti, vedere l'articolo [Flussi di lavoro](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Quando si crea un nuovo circuito ExpressRoute, il circuito ha lo stato seguente:

    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



Il circuito passa allo stato seguente quando è in corso l'abilitazione da parte del provider di connettività:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Per poterlo usare, un circuito ExpressRoute deve avere lo stato seguente:

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. controllare periodicamente lo stato e lo stato della chiave del circuito
La verifica dello stato e della condizione della chiave del circuito comunicano all'utente quando il provider ha abilitato il circuito. Dopo la configurazione del circuito, lo stato di *ServiceProviderProvisioningState* visualizzato è *Provisioned*, come illustrato nell'esempio seguente:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


La risposta sarà simile all'esempio di seguito:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                       }
    ServiceKey                       : **************************************
    Peerings                         : []

### <a name="7-create-your-routing-configuration"></a>7. creare la configurazione di routing
Per istruzioni dettagliate, vedere l'articolo relativo alla [configurazione del routing per un circuito ExpressRoute](expressroute-howto-routing-arm.md) per creare e modificare i peering del circuito.

> [!IMPORTANT]
> Queste istruzioni si applicano solo ai circuiti creati con provider di servizi che offrono servizi di connettività di livello 2. Se si usa un provider di servizi che offre servizi gestiti di livello 3 (di solito una VPN IP, come MPLS), il provider di connettività configura e gestisce il routing per conto dell'utente.
>
>

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Collegare una rete virtuale a un circuito ExpressRoute
Collegare quindi una rete virtuale al circuito ExpressRoute. Fare riferimento all'articolo [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md) quando si usa il modello di distribuzione di Resource Manager.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Ottenere lo stato di un circuito ExpressRoute
È possibile recuperare queste informazioni in qualsiasi momento usando il cmdlet **Get-AzExpressRouteCircuit** . Se si effettua la chiamata senza parametri, verranno elencati tutti i circuiti.

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```


La risposta restituita è simile all'esempio seguente:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                            "ServiceProviderName": "Equinix",
                                            "PeeringLocation": "Silicon Valley",
                                            "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


È possibile ottenere informazioni su un circuito ExpressRoute specifico passando il nome del gruppo di risorse e il nome del circuito come parametri alla chiamata:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


La risposta sarà simile all'esempio di seguito:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                            "Tier": "Standard",
                                            "Family": "MeteredData"
                                          }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo il comando seguente:

```azurepowershell-interactive
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify"></a>Modifica di un circuito ExpressRoute
È possibile modificare determinate proprietà di un circuito ExpressRoute senza conseguenze per la connettività.

È possibile eseguire le attività seguenti senza tempi di inattività:

* Abilitare o disabilitare un componente aggiuntivo ExpressRoute Premium per il circuito ExpressRoute.
* Aumentare la larghezza di banda del circuito ExpressRoute, a condizione che sulla porta sia disponibile capacità. Il downgrade della larghezza di banda di un circuito non è supportato.
* Modificare il piano di misurazione da Dati a consumo a Dati senza limiti. La modifica del piano di misurazione da Dati senza limiti a Dati a consumo non è supportata.
* È possibile abilitare e disabilitare l'opzione *Allow Classic Operations*(Consenti operazioni classiche).

Per altre informazioni su limiti e limitazioni, vedere [Domande frequenti su ExpressRoute](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Per abilitare il componente aggiuntivo ExpressRoute Premium
È possibile abilitare il componente aggiuntivo ExpressRoute Premium per il circuito esistente usando il frammento di PowerShell seguente:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Le funzionalità del componente aggiuntivo ExpressRoute Premium sono così abilitate nel circuito. La fatturazione della funzionalità del componente aggiuntivo Premium inizia non appena il comando viene eseguito correttamente.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Per disabilitare il componente aggiuntivo ExpressRoute Premium
> [!IMPORTANT]
> Questa operazione può avere esito negativo se si usano più risorse di quelle consentite per il circuito standard.
>
>

Tenere presente quanto segue:

* Prima di effettuare il downgrade da Premium a Standard, è necessario assicurarsi che il numero di reti virtuali collegate al circuito sia minore di 10. In caso contrario, la richiesta di aggiornamento avrà esito negativo e verranno fatturate le tariffe Premium.
* È necessario scollegare tutte le reti virtuali in altre aree geopolitiche. Altrimenti, la richiesta di aggiornamento avrà esito negativo e verranno fatturate le tariffe Premium.
* La tabella di route deve includere meno di 4.000 route per il peering privato. Se la tabella di route include più di 4.000 route, la sessione BGP viene eliminata e non sarà riabilitata finché il numero di prefissi pubblicati non scenderà al di sotto di 4.000.

È possibile disabilitare il componente aggiuntivo ExpressRoute Premium per il circuito esistente usando il cmdlet di PowerShell seguente:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Per aggiornare la larghezza di banda del circuito ExpressRoute
Per le opzioni relative alla larghezza di banda supportate per il provider, vedere [Domande frequenti su ExpressRoute](expressroute-faqs.md). È possibile scegliere qualsiasi dimensione maggiore della dimensione del circuito esistente.

> [!IMPORTANT]
> Se la capacità sulla porta esistente non è sufficiente, potrebbe essere necessario ricreare il circuito ExpressRoute. Il circuito non può essere aggiornato se in tale posizione non è disponibile capacità aggiuntiva.
>
> Non è possibile ridurre la larghezza di banda di un circuito ExpressRoute senza interruzioni. Il downgrade della larghezza di banda richiede il deprovisioning del circuito ExpressRoute e quindi il provisioning di un nuovo circuito ExpressRoute.
>

Una volta stabilite le dimensioni necessarie, usare il comando seguente per ridimensionare il circuito:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```


Il circuito verrà ridimensionato su lato di competenza di Microsoft. Sarà quindi necessario contattare il provider di connettività perché aggiorni le configurazioni corrispondenti in base a questa modifica. In seguito alla notifica, Microsoft inizierà la fatturazione in base all'opzione relativa alla larghezza di banda aggiornata.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Per passare lo SKU dal piano a consumo al piano senza limiti
È possibile modificare lo SKU di un circuito ExpressRoute usando il frammento di PowerShell seguente:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Per controllare l'accesso all'ambiente classico e all'ambiente Resource Manager
Vedere le istruzioni contenute in [Spostare i circuiti ExpressRoute dal modello di distribuzione classica a quello Resource Manager](expressroute-howto-move-arm.md).

## <a name="delete"></a>Deprovisioning ed eliminazione di un circuito ExpressRoute
Tenere presente quanto segue:

* È necessario scollegare tutte le reti virtuali dal circuito ExpressRoute. Se l'operazione non riesce, controllare se sono presenti reti virtuali collegate al circuito.
* Se lo stato di provisioning del provider del servizio del circuito ExpressRoute è **Provisioning in corso** o **Provisioning eseguito**, è necessario collaborare con il provider di servizi per eseguire il deprovisioning del circuito sul lato del provider. Le risorse continuano a essere riservate e la fatturazione continuerà a essere applicata finché il provider di servizi non avrà completato il deprovisioning del circuito e inviato una notifica a Microsoft.
* Se il provider di servizi ha eseguito il deprovisioning del circuito, ovvero lo stato di provisioning del provider di servizi è impostato su **Senza provisioning**, è possibile eliminare il circuito. Viene così interrotta la fatturazione per il circuito.

È possibile eliminare un circuito ExpressRoute eseguendo questo comando:

```azurepowershell-interactive
Remove-AzExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato il circuito, verificare di eseguire la procedura seguente:

* [Creare e modificare il routing per un circuito ExpressRoute](expressroute-howto-routing-arm.md)
* [Collegare la rete virtuale al circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)
