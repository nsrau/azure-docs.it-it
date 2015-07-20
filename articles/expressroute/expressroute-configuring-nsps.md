<properties 
   pageTitle="Configurazione di Expressroute con i provider di servizi di rete"
   description="Questa esercitazione illustra in modo dettagliato la configurazione di ExpressRoute tramite i provider di servizi di rete"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="06/29/2015"
   ms.author="cherylmc"/>

#  Configurare una connessione ExpressRoute tramite un provider di servizi di rete

Per configurare la connessione ExpressRoute tramite un provider di servizi di rete, sarà necessario eseguire più passaggi nell'ordine corretto. Queste istruzioni consentono di eseguire le operazioni seguenti:

- Creare e gestire circuiti ExpressRoute
- Collegare una rete virtuale al circuito ExpressRoute

##  Prerequisiti di configurazione


Prima di iniziare la configurazione, verificare che siano soddisfatti i prerequisiti seguenti:

- Sottoscrizione di Azure
- Versione più recente di Windows PowerShell
- I seguenti requisiti della rete virtuale:
	- Un set di prefissi di indirizzo IP da usare in reti virtuali in Azure.
	- Un set di prefissi IP locali, che può contenere indirizzi IP pubblici.
	- Gateway di rete virtuale creato con una subnet /28.
	- Un set aggiuntivo di prefissi IP (/28) all'esterno della rete virtuale, che verrà utilizzato per la configurazione delle route. Sarà necessario fornire questo set al provider di servizi di rete.
	- Numero AS della rete. Sarà necessario fornire questo numero al provider di servizi di rete. È possibile usare numeri AS privati. Se si sceglie questa opzione, dovrà essere > 65000. Per altre informazioni sui numeri AS, vedere la pagina relativa ai numeri di sistemi autonomi (AS). 

- Dal provider di servizi di rete:
	- È necessario disporre di un servizio VPN supportato con ExpressRoute. Contattare il provider di servizi di rete per verificare se il servizio VPN esistente soddisfa le condizioni.

##  Flusso di lavoro per la distribuzione

![Flusso di lavoro del provider di servizi di rete](./media/expressroute-configuring-nsps/expressroute-nsp-connectivity-workflow.png)

##  Configurazione delle impostazioni mediante PowerShell
Windows PowerShell è un ambiente di scripting potente che può essere usato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. Per altre informazioni, vedere la documentazione relativa a PowerShell in [MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx)



1. **Importare il modulo PowerShell per ExpressRoute.**

		Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
		Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1' 

2. **Ottenere l'elenco dei provider, delle sedi e delle larghezze di banda supportate.**

	Prima di creare un circuito, è necessario disporre di un elenco dei provider di servizi, delle sedi e delle opzioni di larghezza di banda supportati per ciascuna sede. Il cmdlet di PowerShell riportato di seguito restituirà informazioni necessarie nei passaggi successivi.

		PS C:> Get-AzureDedicatedCircuitServiceProvider

	Le informazioni restituite saranno simili al seguente esempio:

		PS C:> Get-AzureDedicatedCircuitServiceProvider
	
		Name                 DedicatedCircuitLocations      DedicatedCircuitBandwidths                                                                                                                                                                                   
		----                 -------------------------      --------------------------                                                                                                                                                                                   
		AT&T                 Silicon Valley,Washington DC   10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		British Telecom      London,Amsterdam               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Equinix              Amsterdam,Atlanta,Chicago,Dall 200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		                     as,New York,Seattle,Silicon                                                                                                                                                                                                                 
		                     Valley,Washington                                                                                                                                                                                                                           
		                     DC,London,Hong                                                                                                                                                                                                                              
		                     Kong,Singapore,Sydney,Tokyo                                                                                                                                                                                                                 
		IIJ                  Tokyo                          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Level 3              London,Silicon                 200Mbps:200, 500Mbps:500, 1Gbps:1000                                                                                                                                                                         
		Communications -     Valley,Washington DC                                                                                                                                                                                                                        
		Exchange                                                                                                                                                                                                                                                         
		Level 3              London,Silicon                 10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Communications -     Valley,Washington DC                                                                                                                                                                                                                        
		IPVPN                                                                                                                                                                                                                                                            
		Orange               Amsterdam,London               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		SingTel Domestic     Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		SingTel              Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		International                                                                                                                                                                                                                                                    
		TeleCity Group       Amsterdam,London               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Telstra Corporation  Sydney                         10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Verizon              Silicon Valley,Washington DC   10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		

3. **Effettuare una richiesta per una chiave del servizio e passarla al provider di servizi di rete.**

	Per creare la richiesta verrà usato un cmdlet di PowerShell. Per questo esempio verrà usato AT & T Netbond come provider di servizi e sarà specificato un circuito ExpressRoute a 50 Mbps in Silicon Valley. Se si usa un altro provider e impostazioni diverse, sostituire tali informazioni al momento della richiesta.

	Di seguito è riportato un esempio di richiesta di una nuova chiave di servizio:

		##Creating a new circuit
		$Bandwidth = 50
		$CircuitName = "NetBondSVTest"
		$ServiceProvider = "at&t"
		$Location = "Silicon Valley"
		
		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location
		
		#Getting service key
		Get-AzureDedicatedCircuit

	La risposta sarà simile a quella riportata nel seguente esempio:

		Bandwidth                        : 50
		CircuitName                      : NetBondSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : at&t
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled

	Si possono recuperare queste informazioni in qualsiasi momento usando il cmdlet Get-AzureCircuit. Se si effettua la chiamata senza parametri, verranno elencati tutti i circuiti. La chiave di servizio verrà elencata nel campo ServiceKey.

		PS C:> Get-AzureDedicatedCircuit
		
		Bandwidth                        : 500
		CircuitName                      : NetBondSVTest
		Location                         : Silicon Valley
		ServiceKey                       : 00-0000-0000-0000-0000000000
		ServiceProviderName              : at&t
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled

	Una volta completato questo passaggio, si disporrà della connettività per l'archiviazione di Azure e altri servizi.



4. **Configurare la rete virtuale e il gateway.**

	Vedere [Configurare una rete virtuale e un gateway per ExpressRoute](https://msdn.microsoft.com/library/azure/dn643737.aspx). Si noti che la subnet del gateway deve essere /28 per poter usare una connessione ExpressRoute.

5. **Collegare la rete al circuito.**

	Procedere con la procedura seguente solo dopo avere confermato che
 
	- ServiceProviderState: Provisioned
	- Status: Enabled

	Verificare che sia disponibile almeno una rete virtuale di Azure con un gateway creato. Il gateway deve essere in esecuzione.

		PS C:> $Vnet = "MyTestVNet"
		New-AzureDedicatedCircuitLink -ServiceKey $ServiceKey -VNetName $Vnet
		
		Provisioned 

<!---HONumber=July15_HO2-->