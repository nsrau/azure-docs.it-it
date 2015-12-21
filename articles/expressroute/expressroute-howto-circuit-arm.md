<properties
   pageTitle="Configurare un circuito ExpressRoute tramite Gestione risorse di Azure e PowerShell | Microsoft Azure"
   description="Questo articolo illustra i passaggi per la creazione e il provisioning di un circuito ExpressRoute. Questo articolo descrive anche come controllare lo stato, eseguire l'aggiornamento o effettuare l'eliminazione e il deprovisioning di un circuito."
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

# Creare e modificare un circuito ExpressRoute tramite Gestione risorse di Azure e PowerShell

> [AZURE.SELECTOR]
[PowerShell - Classic](expressroute-howto-circuit-classic.md)
[PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)

Questo articolo illustra i passaggi per creare un circuito ExpressRoute tramite i cmdlet di PowerShell e il modello di distribuzione di Gestione risorse di Azure. I passaggi che seguono descrivono anche come controllare lo stato, eseguire l'aggiornamento o effettuare l'eliminazione e il deprovisioning di un circuito ExpressRoute.

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## Prerequisiti di configurazione

- Sarà necessario scaricare la versione più recente dei moduli di Azure PowerShell, la versione 1.0 o quelle successive. Seguire le istruzioni fornite nella pagina [Come installare e configurare Azure PowerShell](../powershell-install-configure.md) per indicazioni dettagliate su come configurare il computer per l'uso dei moduli di Azure PowerShell. 
- Prima di iniziare la configurazione, assicurarsi di aver letto le pagine relative ai [Prerequisiti](expressroute-prerequisites.md) e ai [Flussi di lavoro](expressroute-workflows.md).

## Per creare un circuito ExpressRoute ed eseguirne il provisioning

1. **Importare il modulo PowerShell per ExpressRoute.**

 	È necessario installare da [PowerShell Gallery](http://www.powershellgallery.com/) il programma di installazione di PowerShell più recente e importare i moduli di Gestione risorse di Azure nella sessione di PowerShell per iniziare a usare i cmdlet di ExpressRoute. È necessario eseguire PowerShell come amministratore.

	    Install-Module AzureRM

		Install-AzureRM

	Importare tutti i moduli AzureRM.* nell'intervallo noto delle versioni semantiche

		Import-AzureRM

	È possibile anche importare un solo modulo nell'intervallo noto delle versioni semantiche
		
		Import-Module AzureRM.Network 

	Accedere al proprio account.

		Login-AzureRmAccount

	Selezionare la sottoscrizione desiderata per creare il circuito ExpressRoute
		
		Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
			


2. **Ottenere l'elenco dei provider, delle sedi e delle larghezze di banda supportate.**

	Prima di creare un circuito ExpressRoute, è necessario disporre di un elenco dei provider di connettività, delle località supportate e delle opzioni di larghezza di banda. Il cmdlet *Get-AzureRmExpressRouteServiceProvider* di PowerShell restituisce queste informazioni, che verranno usate successivamente.

		PS C:\> Get-AzureRmExpressRouteServiceProvider

	Verificare se sia presente anche il proprio provider di connettività. Prendere nota dei valori seguenti, poiché saranno necessari per la creazione dei circuiti.
	
	- Nome
	- PeeringLocations
	- BandwidthsOffered

	È ora possibile creare un circuito ExpressRoute.

		
3. **Creare un circuito ExpressRoute.**

	Prima di creare il circuito ExpressRoute, è necessario generare un gruppo di risorse, se non è già presente. A questo scopo, è possibile eseguire questo comando.

		New-AzureRmResourceGroup -Name “ExpressRouteResourceGroup” -Location "West US"

	L'esempio seguente illustra come creare un circuito ExpressRoute a 200 Mbps tramite Equinix in Silicon Valley. Se si usa un altro provider e impostazioni diverse, sostituire tali informazioni al momento della richiesta.

	Di seguito è riportato un esempio di richiesta di una nuova chiave di servizio:

		New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200

	Assicurarsi di aver specificato il livello e la famiglia SKU corretti.
 
	 - Con il livello SKU si determina se deve essere abilitato il componente aggiuntivo ExpressRoute Standard o Premium. È possibile specificare *standard* per ottenere lo SKU Standard o *premium* per abilitare il componente aggiuntivo Premium
	 - La famiglia SKU determina il tipo di fatturazione. È possibile selezionare *metereddata* per un piano dati a consumo e *unlimiteddata" per un piano dati illimitato. **Nota:** il tipo di fatturazione non potrà essere modificato successivamente alla creazione di un circuito.

	La risposta conterrà la chiave di servizio. È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo questo comando:

		get-help New-AzureRmExpressRouteCircuit -detailed 

4. **Elencare tutti i circuiti ExpressRoute.**

	Per ottenere un elenco di tutti i circuiti ExpressRoute creati, è possibile eseguire il comando *Get-AzureRmExpressRouteCircuit*.

		#Getting service key
		Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	La risposta sarà simile a quella riportata nel seguente esempio:

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


	È possibile recuperare queste informazioni in qualsiasi momento usando il cmdlet *Get-AzureRmExpressRouteCircuit*. Se si effettua la chiamata senza parametri, verranno elencati tutti i circuiti. La chiave di servizio verrà elencata nel campo *ServiceKey*.

		Get-AzureRmExpressRouteCircuit

	La risposta sarà simile a quella riportata nel seguente esempio:

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



	È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo questo comando:

		get-help Get-AzureRmExpressRouteCircuit -detailed 

5. **Inviare la chiave di servizio al provider di connettività per il provisioning.**

	Quando si crea un nuovo circuito ExpressRoute, il circuito avrà lo stato seguente:
	
		ServiceProviderProvisioningState : NotProvisioned
		
		CircuitProvisioningState         : Enabled

	*ServiceProviderProvisioningState* offre informazioni sullo stato di provisioning corrente sul lato provider del servizio, mentre Status indica lo stato sul lato Microsoft. Un circuito ExpressRoute può essere usato solo se è associato allo stato seguente.

		ServiceProviderProvisioningState : Provisioned
		
		CircuitProvisioningState         : Enabled

	Il circuito passa allo stato seguente quando è in corso l'abilitazione da parte del provider di connettività.

		ServiceProviderProvisioningState : Provisioned
		
		Status                           : Enabled



5. **Controllare periodicamente lo stato e la condizione della chiave del circuito.**

	In questo modo è possibile sapere quando il provider ha abilitato il circuito. Dopo la configurazione del circuito, il parametro *ServiceProviderProvisioningState* verrà visualizzato come *Provisioned*, come illustrato nell'esempio riportato di seguito.

		Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	La risposta sarà simile a quella riportata nel seguente esempio:

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

6. **Creare la configurazione di routing.**
	
	Per istruzioni dettagliate, vedere la pagina relativa alla [configurazione di routing del circuito ExpressRoute (creazione e modifica dei peering del circuito)](expressroute-howto-routing-arm.md).

7. **Collegare una rete virtuale a un circuito ExpressRoute.**

	Collegare ora una rete virtuale a un circuito ExpressRoute. È possibile usare [questo modello](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection) quando si usa la modalità di distribuzione di Gestione risorse di Azure. Si sta attualmente lavorando ai passaggi di PowerShell.

##  Ottenere lo stato di un circuito ExpressRoute

È possibile recuperare queste informazioni in qualsiasi momento usando il cmdlet *Get-AzureRmExpressRouteCircuit*. Se si effettua la chiamata senza parametri, verranno elencati tutti i circuiti.

		Get-AzureRmExpressRouteCircuit

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

È possibile ottenere informazioni su un circuito ExpressRoute specifico passando alla chiamata il nome del gruppo di risorse e il nome del circuito come parametri.

		Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	The response will be something similar to the example below:

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

È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo questo comando:

		get-help get-azurededicatedcircuit -detailed 

## Per modificare un circuito ExpressRoute

È possibile modificare determinate proprietà di un circuito ExpressRoute senza conseguenze per la connettività.

A questo punto è possibile eseguire le attività seguenti:

- Abilitare o disabilitare il componente aggiuntivo ExpressRoute Premium per un circuito ExpressRoute senza tempo di inattività.
- Aumentare la larghezza di banda del circuito ExpressRoute senza alcun tempo di inattività.

Per altre informazioni su limiti e limitazioni, fare riferimento alla pagina delle [Domande frequenti su ExpressRoute](expressroute-faqs.md).

### Come abilitare il componente aggiuntivo ExpressRoute Premium

È possibile abilitare il componente aggiuntivo ExpressRoute Premium per il circuito esistente usando il seguente snippet di PowerShell:

		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

		$ckt.Sku.Name = "Premium"
		$ckt.sku.Name = "Premium_MeteredData"

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
	
		
Il circuito avrà le funzionalità del componente aggiuntivo ExpressRoute Premium abilitate. La fatturazione della funzionalità del componente aggiuntivo Premium inizierà non appena il comando verrà eseguito correttamente.

### Come disabilitare il componente aggiuntivo ExpressRoute Premium

È possibile disabilitare il componente aggiuntivo ExpressRoute Premium per il circuito esistente usando il cmdlet di PowerShell riportato di seguito:
	
		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
		
		$ckt.Sku.Tier = "Standard"
		$ckt.sku.Name = "Standard_MeteredData"
		
		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


Il componente aggiuntivo Premium è ora disabilitato per il circuito.

Notare che questa operazione può avere esito negativo se si usano più risorse di quelle consentite per il circuito standard.

- È necessario assicurarsi che il numero di reti virtuali collegate al circuito sia minore di 10 prima di eseguire il downgrade da premium a standard. In caso contrario, la richiesta di aggiornamento avrà esito negativo e verranno fatturate le tariffe premium.
- È necessario scollegare tutte le reti virtuali in altre aree geopolitiche. In caso contrario, la richiesta di aggiornamento avrà esito negativo e verranno fatturate le tariffe premium.
- La tabella di route deve includere meno di 4000 route per il peering privato. Se la tabella di route include più di 4000 route, la sessione BGP verrà eliminata e non verrà abilitata finché il numero di prefissi pubblicati non scenderà al di sotto di 4000.


### Come aggiornare la larghezza di banda del circuito ExpressRoute

Consultare la pagina delle [Domande frequenti su ExpressRoute](expressroute-faqs.md) per larghezze di banda supportate per il provider. È possibile scegliere qualsiasi dimensione maggiore della dimensione del circuito esistente. Dopo aver stabilito le dimensioni necessarie, è possibile usare il comando seguente per ridimensionare il circuito.

		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

		$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

Il circuito verrà ridimensionato sul lato di Microsoft. È necessario contattare il provider di connettività per aggiornare le configurazioni corrispondenti in base a questa modifica. Si noti che inizierà la fatturazione per la larghezza di banda aggiornata da questo punto in poi.

>[AZURE.IMPORTANT]Non è possibile ridurre la larghezza di banda di un circuito ExpressRoute senza interruzioni. Il downgrade della larghezza di banda comporterà il deprovisioning del circuito ExpressRoute e quindi il provisioning di un nuovo circuito ExpressRoute.

## Eliminare un circuito ExpressRoute ed eseguire il deprovisioning

È possibile eliminare un circuito ExpressRoute eseguendo questo comando:

		Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"

Si noti che è necessario scollegare tutte le reti virtuali da ExpressRoute perché l'operazione abbia esito positivo. Se l'operazione non riesce, controllare se sono presenti reti virtuali collegate al circuito.

Se lo stato di provisioning del provider di servizi del circuito ExpressRoute è abilitato, lo stato verrà modificato in *disabilitato*. Collaborare con il provider di servizi per eseguire il deprovisioning del circuito su tale lato. Le risorse continueranno a essere riservate e la fatturazione continuerà a essere applicata finché il provider di servizi non avrà completato il deprovisioning e inviato una notifica.

Se il provider di servizi ha eseguito il deprovisioning del circuito (stato di provisioning del provider di servizi impostato su *senza provisioning*) prima dell'esecuzione del cmdlet precedente, verrà eseguito il deprovisioning del circuito e non verrà più applicata la fatturazione corrispondente.

## Passaggi successivi

- [Configurare il routing](expressroute-howto-routing-arm.md)

<!---HONumber=AcomDC_1210_2015-->