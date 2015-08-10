<properties
   pageTitle="Configurazione di ExpressRoute tramite un provider di Exchange"
   description="Questa esercitazione illustra in modo dettagliato la configurazione di ExpressRoute tramite i provider di Exchange (EXP)."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor="tysonn" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/28/2015"
   ms.author="cherylmc"/>

#  Configurare una connessione ExpressRoute mediante un provider di Exchange

Per configurare la connessione ExpressRoute tramite un provider di Exchange, sarà necessario eseguire più passaggi nell'ordine corretto. Queste istruzioni consentono di eseguire le operazioni seguenti:

- Creare e gestire circuiti ExpressRoute
- Configurare il peering BGP per circuiti ExpressRoute
- Collegare una rete virtuale al circuito ExpressRoute

##  Prerequisiti di configurazione


Prima di iniziare la configurazione, verificare che siano soddisfatti i prerequisiti seguenti:

- Sottoscrizione di Azure
- Versione più recente di Azure PowerShell
- I seguenti requisiti della rete virtuale:
	- Un set di prefissi di indirizzo IP da usare in reti virtuali in Azure.
	- Un set di prefissi IP locali, che può contenere indirizzi IP pubblici.
	- Gateway di rete virtuale creato con una subnet /28.
	- Un set aggiuntivo di prefissi IP (/28) all'esterno della rete virtuale, che verrà usato per la configurazione del peering BGP.
	- Numero AS della rete. Per altre informazioni sui numeri AS, vedere la pagina relativa ai [numeri di sistemi autonomi (AS)](http://www.iana.org/assignments/as-numbers/as-numbers.xhtml).
	- L'hash MD5 se è necessaria una sessione BGP autenticata.
	- ID VLAN in cui verrà inviato il traffico. Saranno necessari due ID VLAN per ogni circuito: uno per le reti virtuali e l'altro per i servizi ospitati in indirizzi IP pubblici.
	- [Numeri di sistemi autonomi (AS)](http://www.iana.org/assignments/as-numbers/as-numbers.xhtml) per la rete.
	- Due connessioni incrociate 1 Gbps/10 Gbps all'Ethernet Exchange del provider di Exchange.
	- Una coppia di router con supporto BGP per il routing.

##  Flusso di lavoro per la distribuzione


![](./media/expressroute-configuring-exps/expressroute-exp-connectivity-workflow.png)

##  Configurazione delle impostazioni mediante PowerShell

Windows PowerShell è un ambiente di scripting potente che può essere usato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. Per altre informazioni, vedere la documentazione relativa a PowerShell in [MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx).

1. **Importare il modulo PowerShell per ExpressRoute.**

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Ottenere l'elenco dei provider, delle sedi e delle larghezze di banda supportate.**

	Prima di creare un circuito, è necessario disporre di un elenco dei provider di servizi, delle sedi e delle opzioni di larghezza di banda supportati per ciascuna sede. Il cmdlet di PowerShell riportato di seguito restituirà informazioni necessarie nei passaggi successivi.

    	PS C:\> Get-AzureDedicatedCircuitServiceProvider
		**The information returned will look similar to the example below:**


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

3. **Creare un circuito dedicato**

	L'esempio seguente illustra come creare un circuito ExpressRoute a 200 Mbps tramite Equinix Silicon Valley. Se si usa un altro provider e impostazioni diverse, sostituire tali informazioni al momento della richiesta.

	Di seguito è riportato un esempio di richiesta di una nuova chiave di servizio:

		#Creating a new circuit
		$Bandwidth = 200
		$CircuitName = "EquinixSVTest"
		$ServiceProvider = "Equinix"
		$Location = "Silicon Valley"

		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location

		#Getting service key
		Get-AzureDedicatedCircuit

	La risposta sarà simile a quella riportata nel seguente esempio:

		Bandwidth                        : 200
		CircuitName                      : EquinixSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled

	Si possono recuperare queste informazioni in qualsiasi momento usando il cmdlet Get-AzureCircuit. Se si effettua la chiamata senza parametri, verranno elencati tutti i circuiti. La chiave di servizio verrà elencata nel campo ServiceKey.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : EquinixSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled


4. **Inviare la chiave di servizio al provider di Exchange.**

	Il provider di servizi userà la chiave di servizio per abilitare la connessione. Per abilitare la connettività, sarà necessario fornire informazioni aggiuntive al provider di connettività.

5. **Controllare periodicamente lo stato e la condizione della chiave del circuito.**

	In questo modo, sarà possibile sapere quando il circuito viene abilitato dal provider. Dopo l'abilitazione del circuito, il parametro *ServiceProviderProvisioningState* verrà visualizzato come *Provisioned*, come illustrato nell'esempio riportato di seguito.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : EquinixSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Status                           : Enabled

6. **Configurare il routing per la rete virtuale.**

	Per lo scambio delle route e per garantire un'elevata disponibilità, vengono usate sessioni BGP. Per creare una sessione BGP per il circuito, usare l'esempio seguente. Durante la creazione della sessione, sostituire i seguenti valori con valori personalizzati.

		#Setting up a bgp session
		$ServiceKey = "<your key>"
		$PriSN = "<subnet/30 you use IP #1 and Azure uses IP #2>"
		$SecSN = "<subnet/30 use IP #1 and Azure uses IP #2>"
		$ASN = <your ASN>
		$VLAN = <your vlan ID>

		#Create a new bgp peering session
		New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN VlanId $VLAN –AccessType Private
		#Get BGP parameters and Azure ASN
		Get-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Private
		#Update BGP peering config
		Set-AzureBGPPeering  -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Private
		#Removing BGP peering config
		Remove-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Private

	È possibile ottenere informazioni sul routing per un circuito che usa Get-AzureBGPPeering fornendo la chiave di servizio. È anche possibile aggiornare le impostazioni BGP tramite Set-AzureBGPPeering. Quando questo comando è in esecuzione, la sessione BGP non verrà visualizzata. Per attivare la sessione BGP, è necessario che il circuito sia collegato ad almeno una rete VNet.

	La risposta riportata di seguito fornirà informazioni necessarie nei passaggi successivi. Utilizzare l'ASN del peer per configurare BGP sui VRF del router.

		PS C:\> New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Private

		AzureAsn            : 12076
		PeerAsn             : 65001
		PrimaryAzurePort    : EQIX-SJC-06GMR-CIS-1-PRI-A
		PrimaryPeerSubnet   : 10.0.1.0/30
		SecondaryAzurePort  : EQIX-SJC-06GMR-CIS-2-SEC-A
		SecondaryPeerSubnet : 10.0.2.0/30
		State               : Enabled
		VlanId              : 100

7. **Configurare il routing per i servizi ospitati in indirizzi IP pubblici.**

	Per lo scambio delle route e per garantire un'elevata disponibilità, vengono usate sessioni BGP. Per creare una sessione BGP per il circuito, usare l'esempio seguente. Durante la creazione della sessione, sostituire i seguenti valori con valori personalizzati.

		#Setting up a bgp session
		$ServiceKey = "<your key>"
		$PriSN = "<subnet/30 subnet you use IP #1 and Azure uses IP #2>"
		$SecSN = "< subnet/30 subnet you use IP #1 and Azure uses IP #2>"
		$ASN = <your ASN>
		$VLAN = <your vlan ID>

		#Create a new bgp peering session
		New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Public
		#Get BGP parameters and Azure ASN
		Get-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Public
		#Update BGP peering config
		Set-AzureBGPPeering  -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Public
		#Removing BGP peering config
		Remove-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Public

	È possibile ottenere informazioni sul routing per un circuito che usa Get-AzureBGPPeering fornendo la chiave di servizio. È anche possibile aggiornare le impostazioni BGP tramite Set-AzureBGPPeering. Quando questo comando è in esecuzione, la sessione BGP non verrà visualizzata. Per attivare la sessione BGP, è necessario che il circuito sia collegato ad almeno una rete VNet.

	La risposta riportata di seguito fornirà informazioni necessarie nei passaggi successivi. Utilizzare l'ASN del peer per configurare BGP sui VRF del router.

		PS C:\> New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Private

		AzureAsn            : 12076
		PeerAsn             : 65001
		PrimaryAzurePort    : EQIX-SJC-06GMR-CIS-1-PRI-A
		PrimaryPeerSubnet   : 10.0.1.8/30
		SecondaryAzurePort  : EQIX-SJC-06GMR-CIS-2-SEC-A
		SecondaryPeerSubnet : 10.0.2.8/30
		State               : Enabled
		VlanId              : 101

8. **Configurare la rete virtuale e il gateway.**

	Vedere [Configurare una rete virtuale e un gateway per ExpressRoute](expressroute-configuring-vnet-gateway.md). Si noti che la subnet del gateway deve essere /28 per poter usare una connessione ExpressRoute.

9. **Collegare la rete al circuito.** Procedere con le istruzioni riportate di seguito solo dopo aver confermato che lo stato del circuito è ora il seguente:
	- ServiceProviderProvisioningState: Provisioned
	- Status: Enabled

			PS C:\> $Vnet = "MyTestVNet"
			New-AzureDedicatedCircuitLink -ServiceKey $ServiceKey -VNetName $Vnet
 
## Passaggi successivi

- Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).

<!---HONumber=July15_HO5-->