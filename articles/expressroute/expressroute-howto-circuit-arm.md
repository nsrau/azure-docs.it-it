<properties
   pageTitle="Creare e modificare un circuito ExpressRoute tramite Resource Manager e PowerShell | Microsoft Azure"
   description="Questo articolo descrive come creare un circuito ExpressRoute ed eseguirne il provisioning. Descrive anche come controllare lo stato del circuito ed eseguirne l'aggiornamento, l'eliminazione e il deprovisioning."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/04/2015"
   ms.author="cherylmc"/>

# Creare e modificare un circuito ExpressRoute tramite Resource Manager e PowerShell

   > [AZURE.SELECTOR]
   [PowerShell - Classic](expressroute-howto-circuit-classic.md)
   [PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)

Questo articolo illustra i passaggi per creare un circuito ExpressRoute di Azure tramite i cmdlet di Windows PowerShell e il modello di distribuzione di Azure Resource Manager. I passaggi seguenti descrivono anche come controllare lo stato del circuito ed eseguirne l'aggiornamento, l'eliminazione e il deprovisioning.

   [AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## Prerequisiti di configurazione

Per creare un circuito ExpressRoute è necessario:

- Ottenere la versione più recente dei moduli di Azure PowerShell, versione 1.0 o successive. Per istruzioni dettagliate su come configurare il computer per l'uso dei moduli PowerShell, seguire le istruzioni contenute nella pagina [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).
- Prima di iniziare la configurazione, leggere le pagine relative ai [prerequisiti](expressroute-prerequisites.md) e ai [flussi di lavoro](expressroute-workflows.md).

## Creare un circuito ExpressRoute ed eseguirne il provisioning

**Passaggio 1. Importare il modulo PowerShell per ExpressRoute.**

Per iniziare a usare i cmdlet ExpressRoute, è necessario installare da [PowerShell Gallery](http://www.powershellgallery.com/) il programma di installazione di PowerShell più recente e importare i moduli di Resource Manager nella sessione di PowerShell. Eseguire PowerShell come amministratore.

```
Install-Module AzureRM

Install-AzureRM
```

Importare tutti i moduli Azure Resource Manager nell'intervallo noto delle versioni semantiche:

```
Import-AzureRM
```

È anche possibile importare un modulo selezionato nell'intervallo noto delle versioni semantiche:

```
Import-Module AzureRM.Network
```

Accedere al proprio account:

```
Login-AzureRmAccount
```

Selezionare la sottoscrizione desiderata per creare un circuito ExpressRoute:

```
Select-AzureRmSubscription -SubscriptionId "<subscription ID>"   			
```

**Passaggio 2. Ottenere l'elenco dei provider, delle località e delle larghezze di banda supportate.**

Prima di creare un circuito ExpressRoute, è necessario l'elenco dei provider di connettività, delle località supportate e delle opzioni di larghezza di banda. Il cmdlet *Get-AzureRmExpressRouteServiceProvider* di PowerShell restituisce queste informazioni, che verranno usate successivamente.

```
PS C:\> Get-AzureRmExpressRouteServiceProvider
```

Verificare se sia presente anche il proprio provider di connettività. Prendere nota dei dati seguenti, perché saranno necessari al momento della creazione di un circuito:

- Nome
- PeeringLocations
- BandwidthsOffered

È ora possibile creare un circuito ExpressRoute.

**Passaggio 3. Creare un circuito ExpressRoute.**

Se non si ha già un gruppo di risorse, è necessario crearne uno prima di creare il circuito ExpressRoute. A questo scopo, eseguire il comando seguente:

```
New-AzureRmResourceGroup -Name “ExpressRouteResourceGroup” -Location "West US"
```

L'esempio seguente illustra come creare un circuito ExpressRoute a 200 Mbps tramite Equinix nella Silicon Valley. Se si usa un altro provider e impostazioni diverse, sostituire tali informazioni al momento della richiesta. Di seguito è riportato un esempio di richiesta di una nuova chiave di servizio:

```
New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Verificare di aver specificato il livello e la famiglia SKU corretti:

- Il livello SKU determina se deve essere abilitato il componente aggiuntivo ExpressRoute Standard o Premium. È possibile specificare *standard* per ottenere lo SKU Standard o *premium* per abilitare il componente aggiuntivo Premium.
- La famiglia SKU determina il tipo di fatturazione. È possibile selezionare *metereddata* per un piano dati a consumo e *unlimiteddata* per un piano dati illimitato. **Nota:** il tipo di fatturazione non potrà essere modificato dopo la creazione di un circuito.

La risposta contiene la chiave di servizio. È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo questo comando:

```
get-help New-AzureRmExpressRouteCircuit -detailed
```

**Passaggio 4. Elencare tutti i circuiti ExpressRoute.**

Per ottenere un elenco di tutti i circuiti ExpressRoute creati, eseguire il comando *Get-AzureRmExpressRouteCircuit*.

```
#Getting service key
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

La risposta ha un aspetto simile all'esempio seguente:

```
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

È possibile recuperare queste informazioni in qualsiasi momento usando il cmdlet *Get-AzureRmExpressRouteCircuit*. Se si esegue la chiamata senza parametri, verranno elencati tutti i circuiti. La chiave di servizio viene elencata nel campo *ServiceKey*.

```
Get-AzureRmExpressRouteCircuit
```

La risposta ha un aspetto simile all'esempio seguente:

```
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

È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo questo comando:

```
get-help Get-AzureRmExpressRouteCircuit -detailed
```

**Passaggio 5. Inviare la chiave di servizio al provider di connettività per il provisioning.**

Quando si crea un nuovo circuito ExpressRoute, il circuito ha lo stato seguente:

```
ServiceProviderProvisioningState : NotProvisioned

CircuitProvisioningState         : Enabled
```

*ServiceProviderProvisioningState* offre informazioni sullo stato di provisioning corrente sul lato provider del servizio, mentre Status indica lo stato sul lato Microsoft. Per poterlo usare, un circuito ExpressRoute deve avere lo stato seguente:

```
ServiceProviderProvisioningState : Provisioned

CircuitProvisioningState         : Enabled
```

Il circuito passa allo stato seguente quando è in corso l'abilitazione da parte del provider di connettività:

```
ServiceProviderProvisioningState : Provisioned

Status                           : Enabled
```

**Passaggio 6. Controllare periodicamente lo stato e la condizione della chiave del circuito.**

La verifica dello stato e della condizione della chiave del circuito comunicano all'utente quando il provider ha abilitato il circuito. Dopo la configurazione del circuito, il parametro *ServiceProviderProvisioningState* viene visualizzato come *Provisioned*, come illustrato nell'esempio seguente:

```
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

La risposta ha un aspetto simile all'esempio seguente:

```
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

**Step 7.  Create your routing configuration.**

For step-by-step instructions, refer to the [ExpressRoute circuit routing configuration (create and modify circuit peerings)](expressroute-howto-routing-arm.md).

**Step 8.  Link a virtual network to an ExpressRoute circuit.**

Next, link a virtual network to your ExpressRoute circuit. You can use [this template](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection) when you work with the Resource Manager deployment mode. We're currently working on steps to accomplish this by using PowerShell.

## Get the status of an ExpressRoute circuit

You can retrieve this information at any time by using the *Get-AzureRmExpressRouteCircuit* cmdlet. Making the call with no parameters will list all circuits.

```
Get-AzureRmExpressRouteCircuit
```

The response will be similar to the following example:

```
Name : ExpressRouteARMCircuit ResourceGroupName : ExpressRouteResourceGroup Location : westus Id : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit Etag : W/"################################" ProvisioningState : Succeeded Sku : { "Name": "Standard\_MeteredData", "Tier": "Standard", "Family": "MeteredData" } CircuitProvisioningState : Enabled ServiceProviderProvisioningState : Provisioned ServiceProviderNotes : ServiceProviderProperties : { "ServiceProviderName": "Equinix", "PeeringLocation": "Silicon Valley", "BandwidthInMbps": 200 } ServiceKey : ************************************** Peerings :
```

You can get information on a specific ExpressRoute circuit by passing the resource group name and circuit name as a parameter to the call:

```
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

The response will look similar to the following example:

```
Name : ExpressRouteARMCircuit ResourceGroupName : ExpressRouteResourceGroup Location : westus Id : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit Etag : W/"################################" ProvisioningState : Succeeded Sku : { "Name": "Standard\_MeteredData", "Tier": "Standard", "Family": "MeteredData" } CircuitProvisioningState : Enabled ServiceProviderProvisioningState : Provisioned ServiceProviderNotes : ServiceProviderProperties : { "ServiceProviderName": "Equinix", "PeeringLocation": "Silicon Valley", "BandwidthInMbps": 200 } ServiceKey : ************************************** Peerings :
```

You can get detailed descriptions of all parameters by running the following:

```
get-help get-azurededicatedcircuit -detailed
```

## Modify an ExpressRoute circuit

You can modify certain properties of an ExpressRoute circuit without impacting connectivity.

You can do the following, with no downtime:

- Enable or disable an ExpressRoute premium add-on for your ExpressRoute circuit.
- Increase the bandwidth of your ExpressRoute circuit.

For more information on limits and limitations, refer to the [ExpressRoute FAQ](expressroute-faqs.md) page.

### Enable the ExpressRoute premium add-on

You can enable the ExpressRoute premium add-on for your existing circuit by using the following PowerShell snippet:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Name = "Premium" $ckt.sku.Name = "Premium\_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

The circuit will now have the ExpressRoute premium add-on features enabled. Note that Microsoft will begin billing you for the premium add-on capability as soon as the command has successfully run.

### Disable the ExpressRoute premium add-on

You can disable the ExpressRoute premium add-on for the existing circuit by using the following PowerShell cmdlet:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard" $ckt.sku.Name = "Standard\_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

The premium add-on is now disabled for the circuit.

Note that this operation can fail if you are using resources greater than what is permitted for the standard circuit.

- Before you downgrade from premium to standard, you must ensure that the number of virtual networks linked to the circuit is less than 10. If you don't do so, your update request fails and Microsoft will bill you at premium rates.
- You must unlink all virtual networks in other geopolitical regions. If you don't do so, your update request will fail and Microsoft will bill you at premium rates.
- Your route table must be less than 4,000 routes for private peering. If your route table size is greater than 4,000 routes, the BGP session drops and won't be reenabled until the number of advertised prefixes goes below 4,000.

### Update the ExpressRoute circuit bandwidth

For supported bandwidth options for your provider, check the [ExpressRoute FAQ](expressroute-faqs.md) page. You can pick any size greater than the size of your existing circuit. After you decide what size you need, use the following command to resize your circuit:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Your circuit will be sized up on the Microsoft side. Then you must contact your connectivity provider to update configurations on their side to match this change. After you make this notification, Microsoft will begin billing you for the updated bandwidth option.

**Important**: You cannot reduce the bandwidth of an ExpressRoute circuit without disruption. Downgrading bandwidth requires you to deprovision the ExpressRoute circuit and then reprovision a new ExpressRoute circuit.

## Delete and deprovision an ExpressRoute circuit

You can delete your ExpressRoute circuit by running the following command:

```
Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit" ```

Si noti che è necessario scollegare tutte le reti virtuali dal circuito ExpressRoute perché l'operazione abbia esito positivo. Se l'operazione non riesce, controllare se sono presenti reti virtuali collegate al circuito.

Se lo stato di provisioning del provider di servizi del circuito ExpressRoute è abilitato, lo stato verrà modificato in *Disabling*. Collaborare con il provider di servizi per eseguire il deprovisioning del circuito su tale lato. Le risorse continueranno a essere riservate e la fatturazione continuerà a essere applicata finché il provider di servizi non avrà completato il deprovisioning del circuito e inviato una notifica a Microsoft.

Se il provider di servizi ha eseguito il deprovisioning del circuito (stato di provisioning del provider di servizi impostato su *not provisioned*) prima dell'esecuzione del cmdlet precedente, verrà eseguito il deprovisioning del circuito e non verrà più applicata la fatturazione corrispondente.

## Passaggi successivi

Dopo aver creato il circuito, verificare di eseguire le operazioni seguenti:

- [Creare e modificare il routing per un circuito ExpressRoute](expressroute-howto-routing-arm.md)
- [Collegare la rete virtuale al circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0302_2016-->