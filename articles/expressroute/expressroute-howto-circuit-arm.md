---
title: 'Avvio rapido: Creare e modificare un circuito con ExpressRoute - Azure PowerShell'
description: Creazione, provisioning, verifica, aggiornamento, eliminazione e deprovisioning di un circuito ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: duau
ms.openlocfilehash: b5ac53c44429e23e2d22a934a9dc71bd485ec4cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761907"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit-using-azure-powershell"></a>Avvio rapido: Creare e modificare un circuito ExpressRoute con Azure PowerShell

Questa guida di avvio rapido illustra come creare un circuito ExpressRoute usando i cmdlet di PowerShell e il modello di distribuzione di Azure Resource Manager. È anche possibile controllare lo stato, aggiornare, eliminare o effettuare il deprovisioning di un circuito.

## <a name="prerequisites"></a>Prerequisiti

* Prima di iniziare la configurazione, verificare i [prerequisiti](expressroute-prerequisites.md) e i [flussi di lavoro](expressroute-workflows.md).
* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Installazione di Azure PowerShell in locale o Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Creare un circuito ExpressRoute ed eseguirne il provisioning
### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Accedere al proprio account Azure e selezionare la sottoscrizione

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="get-the-list-of-supported-providers-locations-and-bandwidths"></a>Ottenere l'elenco dei provider, delle località e delle larghezze di banda supportate
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

### <a name="create-an-expressroute-circuit"></a>Creare un circuito ExpressRoute
Se non si ha già un gruppo di risorse, è necessario crearne uno prima di creare il circuito ExpressRoute. A questo scopo, eseguire il comando seguente:

```azurepowershell-interactive
New-AzResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

L'esempio seguente illustra come creare un circuito ExpressRoute a 200 Mbps tramite Equinix nella Silicon Valley. Se si usano provider e impostazioni differenti, sostituire tali informazioni al momento della richiesta. Usare l'esempio seguente per richiedere una nuova chiave di servizio:

```azurepowershell-interactive
New-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Verificare di aver specificato il livello e la famiglia SKU corretti:

* Il livello SKU determina se un circuito ExpressRoute è [Local](expressroute-faqs.md#expressroute-local), Standard o [Premium](expressroute-faqs.md#expressroute-premium). È possibile specificare *Local*, *Standard o *Premium*. Non è possibile cambiare lo SKU da *Standard/Premium* a *Local*.
* La famiglia SKU determina il tipo di fatturazione. Specificare *MeteredData* per un piano dati a consumo e *UnlimitedData* per un piano dati senza limiti. È possibile cambiare il tipo di fatturazione da *MeteredData* a *UnlimitedData*, ma non da *UnlimitedData* a *MeteredData*. Un circuito *Local* è sempre di tipo *UnlimitedData*.

> [!IMPORTANT]
> Il circuito ExpressRoute viene addebitato dal momento in cui viene emessa una chiave di servizio. Verificare che l'operazione venga eseguita quando il provider di connettività è pronto a effettuare il provisioning del circuito.
>

La risposta contiene la chiave di servizio. È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo il comando seguente:

```azurepowershell-interactive
get-help New-AzExpressRouteCircuit -detailed
```


### <a name="list-all-expressroute-circuits"></a>Elencare tutti i circuiti ExpressRoute
Per ottenere un elenco di tutti i circuiti ExpressRoute creati, eseguire il comando **Get-AzExpressRouteCircuit**:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

La risposta sarà simile all'esempio di seguito:

```azurepowershell
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
```

È possibile recuperare queste informazioni in qualsiasi momento usando il cmdlet `Get-AzExpressRouteCircuit` . Se si effettua la chiamata senza parametri, verranno elencati tutti i circuiti. La chiave di servizio è elencata nel campo *ServiceKey* :

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```

La risposta sarà simile all'esempio di seguito:

```azurepowershell
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
```

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Inviare la chiave di servizio al provider di connettività per il provisioning
*ServiceProviderProvisioningState* fornisce informazioni sullo stato di provisioning corrente sul lato provider del servizio. Lo stato indica lo stato sul lato Microsoft. Per altre informazioni sullo stato di provisioning dei circuiti, vedere l'articolo [Flussi di lavoro](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Quando si crea un nuovo circuito ExpressRoute, il circuito ha lo stato seguente:

```azurepowershell
ServiceProviderProvisioningState : NotProvisioned
CircuitProvisioningState         : Enabled
```

Il circuito passa allo stato seguente quando è in corso l'abilitazione da parte del provider di connettività:

```azurepowershell
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

Per poterlo usare, il circuito ExpressRoute deve avere lo stato seguente:

```azurepowershell
ServiceProviderProvisioningState : Provisioned
CircuitProvisioningState         : Enabled
```

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Controllare periodicamente lo stato e la condizione della chiave del circuito
La verifica dello stato e della condizione della chiave del servizio comunicano quando il provider ha effettuato il provisioning del circuito. Dopo la configurazione del circuito, lo stato di *ServiceProviderProvisioningState* visualizzato è *Provisioned*, come illustrato nell'esempio seguente:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

La risposta sarà simile all'esempio di seguito:

```azurepowershell
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
```

### <a name="create-your-routing-configuration"></a>Creare la configurazione di routing
Per istruzioni dettagliate, vedere l'articolo relativo alla [configurazione del routing per un circuito ExpressRoute](expressroute-howto-routing-arm.md) per creare e modificare i peering del circuito.

> [!IMPORTANT]
> Queste istruzioni si applicano solo ai circuiti creati con provider di servizi che offrono servizi di connettività di livello 2. Se si usa un provider di servizi che offre servizi gestiti di livello 3 (di solito una VPN IP, come MPLS), il provider di connettività configura e gestisce il routing per conto dell'utente.
>

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Collegare una rete virtuale a un circuito ExpressRoute
Collegare quindi una rete virtuale al circuito ExpressRoute. Fare riferimento all'articolo [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md) quando si usa il modello di distribuzione di Resource Manager.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Ottenere lo stato di un circuito ExpressRoute
È possibile recuperare queste informazioni in qualsiasi momento usando il cmdlet **Get-AzExpressRouteCircuit**. Se si effettua la chiamata senza parametri, verranno elencati tutti i circuiti.

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```

La risposta restituita è simile all'esempio seguente:

```azurepowershell
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
```

È possibile ottenere informazioni su un circuito ExpressRoute specifico passando il nome del gruppo di risorse e il nome del circuito come parametri alla chiamata:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

La risposta sarà simile all'esempio di seguito:

```azurepowershell
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
```

È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo il comando seguente:

```azurepowershell-interactive
get-help Get-AzExpressRouteCircuit -detailed
```

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Modifica di un circuito ExpressRoute
È possibile modificare determinate proprietà di un circuito ExpressRoute senza conseguenze per la connettività.

È possibile eseguire le attività seguenti senza tempi di inattività:

* Abilitare o disabilitare un componente aggiuntivo ExpressRoute Premium per il circuito ExpressRoute. Non è possibile cambiare lo SKU da *Standard/Premium* a *Local*.
* Aumentare la larghezza di banda del circuito ExpressRoute, a condizione che nella porta sia disponibile capacità. Il downgrade della larghezza di banda di un circuito non è supportato.
* Modificare il piano di misurazione da Dati a consumo a Dati senza limiti. Non è possibile cambiare il piano di misurazione da Dati senza limiti a Dati a consumo.
* È possibile abilitare e disabilitare l'opzione *Consenti operazioni classiche*.

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

Tenere presente quanto segue:

* Prima di effettuare il downgrade da Premium a Standard, è necessario assicurarsi che il numero di reti virtuali collegate al circuito sia minore di 10. In caso contrario, la richiesta di aggiornamento avrà esito negativo e verranno fatturate le tariffe Premium.
* È necessario prima scollegare tutte le reti virtuali in altre aree geopolitiche. In caso contrario, la richiesta di aggiornamento avrà esito negativo e continueranno a essere fatturate le tariffe Premium.
* La tabella di route deve includere meno di 4.000 route per il peering privato. Se le dimensioni della tabella di route sono maggiori di 4.000 route, la sessione BGP verrà interrotta. La sessione non potrà essere riabilitata fino a quando il numero di prefissi annunciati non diventa inferiore a 4.000.

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

Il circuito verrà aggiornato su lato Microsoft. Sarà quindi necessario contattare il provider di connettività perché aggiorni le configurazioni corrispondenti in base a questa modifica. In seguito alla notifica, verrà avviata la fatturazione in base all'opzione relativa alla larghezza di banda aggiornata.

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

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>Deprovisioning di un circuito ExpressRoute
Tenere presente quanto segue:

* È necessario scollegare tutte le reti virtuali dal circuito ExpressRoute. Se l'operazione non riesce, controllare se sono presenti reti virtuali collegate al circuito.
* Se lo stato di provisioning del provider del servizio del circuito ExpressRoute è **Provisioning in corso** o **Provisioning eseguito**, è necessario collaborare con il provider di servizi per eseguire il deprovisioning del circuito sul lato del provider. Le risorse continuano a essere riservate e la fatturazione continuerà a essere applicata finché il provider di servizi non avrà completato il deprovisioning del circuito e inviato una notifica a Microsoft.
* Se il provider di servizi ha effettuato il deprovisioning del circuito, ovvero lo stato di provisioning del provider di servizi è impostato su **Senza provisioning**, è possibile eliminare il circuito. La fatturazione per il circuito verrà quindi interrotta.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Pulire le risorse

È possibile eliminare un circuito ExpressRoute eseguendo questo comando:

```azurepowershell-interactive
Remove-AzExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato il circuito e averne effettuato il provisioning con il provider, continuare con il passaggio successivo per configurare il peering:

> [!div class="nextstepaction"]
> [Creare e modificare il routing per un circuito ExpressRoute](expressroute-howto-routing-arm.md)
