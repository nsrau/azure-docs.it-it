<properties
   pageTitle="Come configurare il routing per un circuito ExpressRoute per il modello di distribuzione classica mediante PowerShell | Microsoft Azure"
   description="Questo articolo descrive come creare ed eseguire il provisioning di un circuito ExpressRoute per il peering privato, il peering pubblico e il peering Microsoft. Questo articolo mostra anche come controllare lo stato e aggiornare o eliminare i peering per un circuito."
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

# Creare e modificare il routing per un circuito ExpressRoute usando PowerShell

> [AZURE.SELECTOR]
[PowerShell - Classic](expressroute-howto-routing-classic.md)
[PowerShell - Resource Manager](expressroute-howto-routing-arm.md)

Questo articolo descrive le procedure per creare e gestire la configurazione di routing per un circuito ExpressRoute usando i cmdlet di PowerShell e il modello di distribuzione classico. La procedura seguente mostra anche come controllare lo stato e aggiornare, eliminare o effettuare il deprovisioning dei peering per un circuito ExpressRoute.

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## Prerequisiti di configurazione

- Sarà necessario scaricare la versione più recente dei moduli di Azure PowerShell. È possibile scaricare il modulo PowerShell più recente dalla sezione relativa a PowerShell della [pagina Download di Azure](http://azure.microsoft.com/downloads). Seguire le istruzioni fornite nella pagina [Come installare e configurare Azure PowerShell](../powershell-install-configure.md) per indicazioni dettagliate su come configurare il computer per l'uso dei moduli di Azure PowerShell. 
- Prima di procedere con la configurazione, leggere la pagina [prerequisiti](expressroute-prerequisites.md), la pagina [requisiti di routing](expressroute-routing.md) e la pagina [flussi di lavoro](expressroute-workflows.md).
- È necessario avere un circuito ExpressRoute attivo. Seguire le istruzioni per [creare un circuito ExpressRoute](expressroute-howto-circuit-classic.md) e fare in modo che venga abilitato dal provider di connettività prima di procedere. Per poter eseguire i cmdlet descritti di seguito deve essere stato effettuato il provisioning del circuito ExpressRoute e lo stato del circuito deve essere abilitato.

>[AZURE.IMPORTANT]Queste istruzioni si applicano solo ai circuiti creati con provider di servizi che offrono servizi di connettività di livello 2. Se si usa un provider di servizi che offre servizi gestiti di livello 3 (di solito un IPVPN, come MPLS), il provider di connettività configurerà e gestirà il routing per conto dell'utente. In questo caso, non sarà possibile creare o gestire i peering.

Per un circuito ExpressRoute è possibile configurare uno, due o tutti e tre i peering (peering privato di Azure, peering pubblico di Azure e peering Microsoft). È possibile configurare i peering nell'ordine desiderato. assicurandosi, tuttavia, di completare la configurazione di un peering per volta.

## Peering privato di Azure

Questa sezione fornisce le istruzioni per creare, ottenere, aggiornare ed eliminare la configurazione del peering privato di Azure per un circuito ExpressRoute.

### Per creare un peering privato di Azure

1. **Importare il modulo PowerShell per ExpressRoute.**
	
	Per iniziare a usare i cmdlet per ExpressRoute, è necessario importare i moduli di Azure ed ExpressRoute nella sessione di PowerShell. Per importare i moduli di Azure ed ExpressRoute nella sessione di PowerShell, eseguire i comandi seguenti.

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Creare un circuito ExpressRoute.**
	
	Seguire le istruzioni per creare un [circuito ExpressRoute](expressroute-howto-circuit-classic.md) e chiedere al provider di connettività di effettuarne il provisioning. Se il provider di connettività offre servizi gestiti di livello 3, è possibile chiedere al provider di connettività di abilitare il peering privato di Azure. In questo caso, non sarà necessario seguire le istruzioni riportate nelle sezioni seguenti. Se invece il provider di connettività non gestisce il routing per conto dell'utente, dopo aver creato il circuito, seguire questa procedura.

3. **Verificare che sia stato effettuato il provisioning del circuito ExpressRoute.**

	In primo luogo, è necessario verificare se è stato effettuato il provisioning del circuito ExpressRoute e se il circuito è abilitato. Vedere l'esempio seguente.

		PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

	Assicurarsi che lo stato visualizzato per il circuito sia Provisioning eseguito e Abilitato. In caso contrario, rivolgersi al provider di connettività affinché imposti gli stati necessari per il circuito.

		ServiceProviderProvisioningState : Provisioned
		Status                           : Enabled


4. **Configurare il peering privato di Azure per il circuito.**

	Prima di procedere con i passaggi successivi, verificare che siano presenti gli elementi seguenti:

	- Una subnet /30 per il collegamento primario. Non deve far parte di alcuno spazio indirizzi riservato per le reti virtuali.
	- Una subnet /30 per il collegamento secondario. Non deve far parte di alcuno spazio indirizzi riservato per le reti virtuali.
	- Un ID VLAN valido su cui stabilire questo peering. Assicurarsi che nessun altro peering nel circuito usi lo stesso ID VLAN.
	- Numero AS per il peering. È possibile usare numeri AS a 2 e a 4 byte. È possibile usare il numero AS privato per questo peering. Assicurarsi di non usare il numero 65515.
	- Hash MD5, se si sceglie di usarne uno. **Facoltativo**.
	
	Per configurare il peering privato di Azure per il circuito, eseguire il cmdlet seguente.

		New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100

	Se si sceglie di usare un hash MD5, eseguire il cmdlet seguente.

		New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"

	>[AZURE.IMPORTANT]Assicurarsi di specificare il numero AS come ASN di peering e non come ASN cliente.

### Per ottenere i dettagli relativi al peering privato di Azure

Per ottenere i dettagli di configurazione, usare il cmdlet seguente

	Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
	
	AdvertisedPublicPrefixes       : 
	AdvertisedPublicPrefixesState  : Configured
	AzureAsn                       : 12076
	CustomerAutonomousSystemNumber : 
	PeerAsn                        : 1234
	PrimaryAzurePort               : 
	PrimaryPeerSubnet              : 10.0.0.0/30
	RoutingRegistryName            : 
	SecondaryAzurePort             : 
	SecondaryPeerSubnet            : 10.0.0.4/30
	State                          : Enabled
	VlanId                         : 100


### Per aggiornare la configurazione del peering privato di Azure

Per aggiornare qualsiasi parte della configurazione, usare il cmdlet seguente. Nell'esempio seguente il valore dell'ID VLAN del circuito viene aggiornato da 100 a 500.

	Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"

### Per eliminare un peering privato di Azure

Per rimuovere la configurazione di peering, eseguire il cmdlet seguente.

>[AZURE.WARNING]Assicurarsi che tutte le reti virtuali siano scollegate dal circuito ExpressRoute prima di eseguire questo cmdlet.

	Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"


## Peering pubblico di Azure

Questa sezione fornisce le istruzioni per creare, ottenere, aggiornare ed eliminare la configurazione del peering pubblico di Azure per un circuito ExpressRoute.

### Per creare un peering pubblico di Azure

1. **Importare il modulo PowerShell per ExpressRoute.**
	
	Per iniziare a usare i cmdlet per ExpressRoute, è necessario importare i moduli di Azure ed ExpressRoute nella sessione di PowerShell. Per importare i moduli di Azure ed ExpressRoute nella sessione di PowerShell, eseguire i comandi seguenti.

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Creare un circuito ExpressRoute**
	
	Seguire le istruzioni per creare un [circuito ExpressRoute](expressroute-howto-circuit-classic.md) e chiedere al provider di connettività di effettuarne il provisioning. Se il provider di connettività offre servizi gestiti di livello 3, è possibile chiedere al provider di connettività di abilitare il peering privato di Azure. In questo caso, non sarà necessario seguire le istruzioni riportate nelle sezioni seguenti. Se invece il provider di connettività non gestisce il routing per conto dell'utente, dopo aver creato il circuito, seguire questa procedura.

3. **Verificare che sia stato eseguito il provisioning del circuito ExpressRoute**

	In primo luogo, è necessario verificare se è stato effettuato il provisioning del circuito ExpressRoute e se il circuito è abilitato. Vedere l'esempio seguente.

		PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

	Assicurarsi che lo stato visualizzato per il circuito sia Provisioning eseguito e Abilitato. In caso contrario, rivolgersi al provider di connettività affinché imposti gli stati necessari per il circuito.

		ServiceProviderProvisioningState : Provisioned
		Status                           : Enabled

	

4. **Configurare il peering pubblico di Azure per il circuito**

	Prima di procedere, verificare che siano presenti gli elementi seguenti.

	- Una subnet /30 per il collegamento primario. Deve essere un prefisso IPv4 pubblico valido.
	- Una subnet /30 per il collegamento secondario. Deve essere un prefisso IPv4 pubblico valido.
	- Un ID VLAN valido su cui stabilire questo peering. Assicurarsi che nessun altro peering nel circuito usi lo stesso ID VLAN.
	- Numero AS per il peering. È possibile usare numeri AS a 2 e a 4 byte. È necessario usare un numero AS pubblico per questo peering.
	- Hash MD5, se si sceglie di usarne uno. **Facoltativo**.
	
	Per configurare il peering pubblico di Azure per il circuito, eseguire il cmdlet seguente

		New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200

	Se si sceglie di usare un hash MD5, eseguire il cmdlet seguente

		New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"

	>[AZURE.IMPORTANT]Assicurarsi di specificare il numero AS come ASN di peering e non come ASN cliente.

### Per ottenere i dettagli relativi al peering pubblico di Azure

Per ottenere i dettagli di configurazione, usare il cmdlet seguente

	Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
	
	AdvertisedPublicPrefixes       : 
	AdvertisedPublicPrefixesState  : Configured
	AzureAsn                       : 12076
	CustomerAutonomousSystemNumber : 
	PeerAsn                        : 1234
	PrimaryAzurePort               : 
	PrimaryPeerSubnet              : 131.107.0.0/30
	RoutingRegistryName            : 
	SecondaryAzurePort             : 
	SecondaryPeerSubnet            : 131.107.0.4/30
	State                          : Enabled
	VlanId                         : 200


### Per aggiornare la configurazione del peering pubblico di Azure

Per aggiornare qualsiasi parte della configurazione, usare il cmdlet seguente

	Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"

Nell'esempio precedente il valore dell'ID VLAN è stato aggiornato da 200 a 600.

### Per eliminare un peering pubblico di Azure

Per rimuovere la configurazione di peering, eseguire il cmdlet seguente

	Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

## Peering Microsoft

Questa sezione fornisce le istruzioni per creare, ottenere, aggiornare ed eliminare la configurazione del peering Microsoft per un circuito ExpressRoute.

### Per creare il peering Microsoft

1. **Importare il modulo PowerShell per ExpressRoute.**
	
	Per iniziare a usare i cmdlet per ExpressRoute, è necessario importare i moduli di Azure ed ExpressRoute nella sessione di PowerShell. Per importare i moduli di Azure ed ExpressRoute nella sessione di PowerShell, eseguire i comandi seguenti.

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Creare un circuito ExpressRoute**
	
	Seguire le istruzioni per creare un [circuito ExpressRoute](expressroute-howto-circuit-classic.md) e chiedere al provider di connettività di effettuarne il provisioning. Se il provider di connettività offre servizi gestiti di livello 3, è possibile chiedere al provider di connettività di abilitare il peering privato di Azure. In questo caso, non sarà necessario seguire le istruzioni riportate nelle sezioni seguenti. Se invece il provider di connettività non gestisce il routing per conto dell'utente, dopo aver creato il circuito, seguire questa procedura.

3. **Verificare che sia stato eseguito il provisioning del circuito ExpressRoute**

	In primo luogo, è necessario verificare se è stato eseguito il provisioning del circuito ExpressRoute e se il circuito è abilitato.

		PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

	Assicurarsi che lo stato visualizzato per il circuito sia Provisioning eseguito e Abilitato. In caso contrario, rivolgersi al provider di connettività affinché imposti gli stati necessari per il circuito.

		ServiceProviderProvisioningState : Provisioned
		Status                           : Enabled


4. **Configurare il peering Microsoft per il circuito**

	Prima di procedere, verificare quanto segue:

	- Una subnet /30 per il collegamento primario. Deve essere un prefisso IPv4 pubblico valido di proprietà dell'utente e registrato presso un registro RIR o IRR.
	- Una subnet /30 per il collegamento secondario. Deve essere un prefisso IPv4 pubblico valido di proprietà dell'utente e registrato presso un registro RIR o IRR.
	- Un ID VLAN valido su cui stabilire questo peering. Assicurarsi che nessun altro peering nel circuito usi lo stesso ID VLAN.
	- Numero AS per il peering. È possibile usare numeri AS a 2 e a 4 byte. È necessario usare solo numeri AS pubblici ed essere proprietari del numero AS.
	- Advertised prefixes: è necessario fornire un elenco di tutti i prefissi che si intende pubblicizzare nella sessione BGP. Sono accettati solo prefissi di indirizzi IP pubblici. Per inviare un set di prefissi, è possibile creare un elenco con valori delimitati da virgole. Questi prefissi devono essere intestati all'utente in un registro RIR o IRR.
	- Customer ASN: se si annunciano prefissi registrati al numero AS di peering, è possibile specificare il numero AS a cui sono registrati. **Facoltativo**.
	- Routing Registry Name: è possibile specificare il registro RIR/IRR in cui sono registrati il numero AS e i prefissi.
	- Un hash MD5, se si sceglie di usarne uno. **Facoltativo.**
	
	Per configurare il peering Microsoft per il circuito, eseguire il cmdlet seguente

		New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"


### Ottenere i dettagli del peering Microsoft

Per ottenere i dettagli di configurazione, usare il cmdlet seguente.

	Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
	
	AdvertisedPublicPrefixes       : 123.0.0.0/30
	AdvertisedPublicPrefixesState  : Configured
	AzureAsn                       : 12076
	CustomerAutonomousSystemNumber : 2245
	PeerAsn                        : 1234
	PrimaryAzurePort               : 
	PrimaryPeerSubnet              : 10.0.0.0/30
	RoutingRegistryName            : ARIN
	SecondaryAzurePort             : 
	SecondaryPeerSubnet            : 10.0.0.4/30
	State                          : Enabled
	VlanId                         : 300


### Per aggiornare la configurazione del peering Microsoft

Per aggiornare qualsiasi parte della configurazione, usare il cmdlet seguente.

		Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"

### Per eliminare il peering Microsoft

Per rimuovere la configurazione di peering, eseguire il cmdlet seguente.

	Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

## Passaggi successivi

Successivamente, [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-classic.md)


-  Per ulteriori informazioni sui flussi di lavoro, vedere [Flussi di lavoro ExpressRoute](expressroute-workflows.md).
-  Per altre informazioni sul peering del circuito, vedere l'articolo relativo ai [circuiti ExpressRoute e domini di routing](expressroute-circuit-peerings.md)

<!---HONumber=AcomDC_0121_2016-->