<properties
   pageTitle="Passaggi per la configurazione di un circuito ExpressRoute mediante PowerShell | Microsoft Azure"
   description="Questo articolo illustra i passaggi per la creazione e il provisioning di un circuito ExpressRoute. Questo articolo descrive anche come controllare lo stato, eseguire l'aggiornamento o effettuare l'eliminazione e il deprovisioning di un circuito."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/16/2016"
   ms.author="cherylmc"/>

# Creare e modificare un circuito ExpressRoute mediante PowerShell

> [AZURE.SELECTOR]
[PowerShell - Classic](expressroute-howto-circuit-classic.md)
[PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)

Questo articolo illustra i passaggi per creare un circuito ExpressRoute tramite i cmdlet di PowerShell e il modello di distribuzione classica. I passaggi che seguono descrivono anche come controllare lo stato, eseguire l'aggiornamento o effettuare l'eliminazione e il deprovisioning di un circuito ExpressRoute.

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]


## Prerequisiti di configurazione

- Sarà necessario scaricare la versione più recente dei moduli di Azure PowerShell. È possibile scaricare il modulo PowerShell più recente dalla sezione relativa a PowerShell della [pagina Download di Azure](https://azure.microsoft.com/downloads/). Per istruzioni dettagliate su come configurare il computer per l'uso dei moduli Azure PowerShell, seguire le istruzioni contenute nella pagina [Come installare e configurare Azure PowerShell](../powershell-install-configure.md). 
- Prima di iniziare la configurazione, assicurarsi di aver letto le pagine relative ai [prerequisiti](expressroute-prerequisites.md) e ai [flussi di lavoro](expressroute-workflows.md).

## Per creare un circuito ExpressRoute ed eseguirne il provisioning

1. **Importare il modulo PowerShell per ExpressRoute.**

 	Per iniziare a usare i cmdlet per ExpressRoute, è necessario importare i moduli di Azure ed ExpressRoute nella sessione di PowerShell. Eseguire questi comandi per importare i moduli Azure ed ExpressRoute nella sessione di PowerShell.

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Ottenere l'elenco dei provider, delle sedi e delle larghezze di banda supportate.**

	Prima di creare un circuito ExpressRoute, è necessario disporre di un elenco dei provider di connettività, delle località supportate e delle opzioni di larghezza di banda. Il cmdlet *Get-AzureDedicatedCircuitServiceProvider* di PowerShell restituisce queste informazioni, che verranno usate successivamente. Quando si esegue il cmdlet, il risultato sarà simile all'esempio seguente.

		PS C:\> Get-AzureDedicatedCircuitServiceProvider

		Name                 DedicatedCircuitLocations      DedicatedCircuitBandwidths                                                                                                                                                                                   
		----                 -------------------------      --------------------------                                                                                                                                                                                   
		Aryaka Networks      Silicon Valley,Washington      200Mbps:200, 500Mbps:500, 1Gbps:1000                                                                                                                                                                         
		                     DC,Singapore                                                                                                                                                                                                                                
		AT&T                 Silicon Valley,Washington DC   10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		AT&T Netbond         Washington DC,Silicon          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     Valley,Dallas                                                                                                                                                                                                                               
		British Telecom      London,Amsterdam,Washington    10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     DC,Tokyo,Silicon Valley                                                                                                                                                                                                                     
		Colt Ethernet        Amsterdam,London               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Colt IPVPN           Amsterdam,London               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Comcast              Washington DC,Silicon Valley   200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Equinix              Amsterdam,Atlanta,Chicago,Dall 200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		                     as,New York,Seattle,Silicon                                                                                                                                                                                                                 
		                     Valley,Washington                                                                                                                                                                                                                           
		                     DC,London,Hong Kong,Singapore,                                                                                                                                                                                                              
		                     Sydney,Tokyo,Sao Paulo,Los                                                                                                                                                                                                                  
		                     Angeles,Melbourne                                                                                                                                                                                                                           
		IIJ                  Tokyo                          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		InterCloud           Washington                     200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		                     DC,London,Singapore,Amsterdam                                                                                                                                                                                                               
		Internet Solutions   London,Amsterdam               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		– Cloud Connect                                                                                                                                                                                                                                                  
		Interxion            Amsterdam                      200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Level 3              London,Chicago,Dallas,Seattle, 200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Communications -     Silicon Valley,Washington DC                                                                                                                                                                                                                
		Exchange                                                                                                                                                                                                                                                         
		Level 3              London,Chicago,Dallas,Seattle, 10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Communications -     Silicon Valley,Washington DC                                                                                                                                                                                                                
		IPVPN                                                                                                                                                                                                                                                            
		Megaport             Melbourne,Sydney               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		NEXTDC               Melbourne                      200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		NTT Communications   Tokyo                          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Orange               Amsterdam,London,Silicon       10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     Valley,Washington DC,Hong Kong                                                                                                                                                                                                              
		PCCW Global Limited  Hong Kong                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		SingTel Domestic     Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		SingTel              Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		International                                                                                                                                                                                                                                                    
		Tata Communications  Hong Kong,Singapore,London,Ams 10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     terdam,Chennai,Mumbai                                                                                                                                                                                                                       
		TeleCity Group       Amsterdam,London               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Telstra Corporation  Sydney,Melbourne               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Verizon              London,Hong Kong,Silicon       10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     Valley,Washington DC                                                                                                                                                                                                                        
		Vodafone             London                         10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Zayo Group           Washington DC                  200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
    	

3. **Creare un circuito ExpressRoute.**

	L'esempio seguente illustra come creare un circuito ExpressRoute a 200 Mbps tramite Equinix in Silicon Valley. Se si usa un altro provider e impostazioni diverse, sostituire tali informazioni al momento della richiesta.

	Di seguito è riportato un esempio di richiesta di una nuova chiave di servizio:

		#Creating a new circuit
		$Bandwidth = 200
		$CircuitName = "MyTestCircuit"
		$ServiceProvider = "Equinix"
		$Location = "Silicon Valley"

		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData 

	Se invece si desidera creare un circuito ExpressRoute con il componente aggiuntivo Premium, usare il comando seguente. Per altri dettagli sul componente aggiuntivo Premium, vedere la pagina [Domande frequenti su ExpressRoute](expressroute-faqs.md).

		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData
	
	
	La risposta conterrà la chiave di servizio. È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo questo comando:

		get-help new-azurededicatedcircuit -detailed 

4. **Elencare tutti i circuiti ExpressRoute.**

	Per ottenere un elenco di tutti i circuiti ExpressRoute creati, è possibile eseguire il comando *Get-AzureDedicatedCircuit*.

		#Getting service key
		Get-AzureDedicatedCircuit

	La risposta sarà simile a quella riportata nel seguente esempio:

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Sku                              : Standard
		Status                           : Enabled

	Queste informazioni possono essere recuperate in qualsiasi momento usando il cmdlet *Get-AzureDedicatedCircuit*. Se si effettua la chiamata senza parametri, verranno elencati tutti i circuiti. La chiave di servizio verrà elencata nel campo *ServiceKey*.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Sku                              : Standard
		Status                           : Enabled

	È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo questo comando:

		get-help get-azurededicatedcircuit -detailed 

5. **Inviare la chiave di servizio al provider di connettività per il provisioning.**

	Quando si crea un nuovo circuito ExpressRoute, il circuito avrà lo stato seguente:
	
		ServiceProviderProvisioningState : NotProvisioned
		
		Status                           : Enabled

	*ServiceProviderProvisioningState* offre informazioni sullo stato di provisioning corrente sul lato provider del servizio, mentre Status indica lo stato sul lato Microsoft. Un circuito ExpressRoute può essere usato solo se è associato allo stato seguente.

		ServiceProviderProvisioningState : Provisioned
		
		Status                           : Enabled

	Il circuito passa allo stato seguente quando è in corso l'abilitazione da parte del provider di connettività.

		ServiceProviderProvisioningState : Provisioned
		
		Status                           : Enabled



5. **Controllare periodicamente lo stato e la condizione della chiave del circuito.**

	In questo modo è possibile sapere quando il provider ha abilitato il circuito. Dopo la configurazione del circuito, il parametro *ServiceProviderProvisioningState* verrà visualizzato come *Provisioned*, come illustrato nell'esempio riportato di seguito.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

6. **Creare la configurazione di routing.**
	
	Per istruzioni dettagliate, vedere la pagina relativa alla [configurazione di routing del circuito ExpressRoute (creazione e modifica dei peering del circuito)](expressroute-howto-routing-classic.md).

7. **Collegare una rete virtuale a un circuito ExpressRoute.**

	Collegare ora una rete virtuale a un circuito ExpressRoute. Per istruzioni dettagliate, vedere la pagina relativa al [collegamento di circuiti ExpressRoute a reti virtuali](expressroute-howto-linkvnet-classic.md). Se è necessario creare una rete virtuale usando il modello di distribuzione classico per ExpressRoute, per istruzioni vedere [Configurare una rete virtuale per ExpressRoute](expressroute-howto-vnet-portal-classic.md).

##  Ottenere lo stato di un circuito ExpressRoute

È possibile recuperare queste informazioni in qualsiasi momento usando il cmdlet *Get-AzureCircuit*. Se si effettua la chiamata senza parametri, verranno elencati tutti i circuiti.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

		Bandwidth                        : 1000
		CircuitName                      : MyAsiaCircuit
		Location                         : Singapore
		ServiceKey                       : #################################
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

È possibile ottenere informazioni su un circuito ExpressRoute specifico passando la chiave di servizio come parametro alla chiamata.

		PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled


È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo questo comando:

		get-help get-azurededicatedcircuit -detailed 

##  Per modificare un circuito ExpressRoute

È possibile modificare determinate proprietà di un circuito ExpressRoute senza conseguenze per la connettività.

A questo punto è possibile eseguire le attività seguenti:

- Abilitare o disabilitare il componente aggiuntivo ExpressRoute Premium per un circuito ExpressRoute senza tempo di inattività.
- Aumentare la larghezza di banda del circuito ExpressRoute senza alcun tempo di inattività.

Per altre informazioni su limiti e limitazioni, fare riferimento alla pagina delle [Domande frequenti su ExpressRoute](expressroute-faqs.md).

### Come abilitare il componente aggiuntivo ExpressRoute Premium

È possibile abilitare il componente aggiuntivo ExpressRoute Premium per il circuito esistente usando il cmdlet di PowerShell riportato di seguito:
	
		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Enabled

Il circuito avrà le funzionalità del componente aggiuntivo ExpressRoute Premium abilitate. La fatturazione della funzionalità del componente aggiuntivo Premium inizierà non appena il comando verrà eseguito correttamente.

### Come disabilitare il componente aggiuntivo ExpressRoute Premium

È possibile disabilitare il componente aggiuntivo ExpressRoute Premium per il circuito esistente usando il cmdlet di PowerShell riportato di seguito:
	
		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

Il componente aggiuntivo Premium è ora disabilitato per il circuito.

>[AZURE.IMPORTANT] Questa operazione può avere esito negativo se si usano più risorse di quelle consentite per il circuito standard.
>
>- È necessario assicurarsi che il numero di reti virtuali collegate al circuito sia minore di 10 prima di eseguire il downgrade da premium a standard. In caso contrario, la richiesta di aggiornamento avrà esito negativo e verranno fatturate le tariffe premium.
- È necessario scollegare tutte le reti virtuali in altre aree geopolitiche. In caso contrario, la richiesta di aggiornamento avrà esito negativo e verranno fatturate le tariffe premium.
- La tabella di route deve includere meno di 4000 route per il peering privato. Se la tabella di route include più di 4000 route, la sessione BGP verrà eliminata e non verrà abilitata finché il numero di prefissi pubblicati non scenderà al di sotto di 4000.


### Come aggiornare la larghezza di banda del circuito ExpressRoute

Consultare la pagina delle [Domande frequenti su ExpressRoute](expressroute-faqs.md) per larghezze di banda supportate per il provider. È possibile scegliere qualsiasi dimensione maggiore della dimensione del circuito esistente. Dopo aver stabilito le dimensioni necessarie, è possibile usare il comando seguente per ridimensionare il circuito.

		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

Il circuito verrà ridimensionato sul lato di Microsoft. È necessario contattare il provider di connettività per aggiornare le configurazioni corrispondenti in base a questa modifica. Si noti che inizierà la fatturazione per la larghezza di banda aggiornata da questo punto in poi.

>[AZURE.IMPORTANT] Non è possibile ridurre la larghezza di banda di un circuito ExpressRoute senza interruzioni. Il downgrade della larghezza di banda comporterà il deprovisioning del circuito ExpressRoute e quindi il provisioning di un nuovo circuito ExpressRoute.

##  Eliminare un circuito ExpressRoute ed eseguire il deprovisioning

È possibile eliminare un circuito ExpressRoute eseguendo questo comando:

		PS C:\> Remove-AzureDedicatedCircuit -ServiceKey "*********************************"	

Si noti che è necessario scollegare tutte le reti virtuali da ExpressRoute perché l'operazione abbia esito positivo. Se l'operazione non riesce, controllare se sono presenti reti virtuali collegate al circuito.

Se lo stato di provisioning del provider di servizi del circuito ExpressRoute è abilitato, lo stato verrà modificato in *disabilitato*. Collaborare con il provider di servizi per eseguire il deprovisioning del circuito su tale lato. Le risorse continueranno a essere riservate e la fatturazione continuerà a essere applicata finché il provider di servizi non avrà completato il deprovisioning e inviato una notifica.

Se il provider di servizi ha eseguito il deprovisioning del circuito (stato di provisioning del provider di servizi impostato su *not provisioned*) prima dell'esecuzione del cmdlet precedente, verrà eseguito il deprovisioning del circuito e non verrà più applicata la fatturazione corrispondente.

## Passaggi successivi

- [Configurare il routing](expressroute-howto-routing-classic.md)

<!---HONumber=AcomDC_0204_2016-->