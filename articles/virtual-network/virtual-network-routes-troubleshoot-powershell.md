<properties 
   pageTitle="Risoluzione dei problemi delle route - PowerShell | Microsoft Azure"
   description="Informazioni su come risolvere i problemi delle route nel modello di distribuzione Azure Resource Manager con Azure PowerShell."
   services="virtual-network"
   documentationCenter="na"
   authors="AnithaAdusumilli"
   manager="narayan"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="anithaa" />

# Risoluzione dei problemi di route con Azure PowerShell

> [AZURE.SELECTOR]
- [Portale di Azure](virtual-network-routes-troubleshoot-portal.md)
- [PowerShell](virtual-network-routes-troubleshoot-powershell.md)

Se si verificano problemi di connettività di rete da o verso una macchina virtuale (VM) di Azure, le route possono influire sui flussi di traffico della VM. In questo articolo viene fornita una panoramica delle funzionalità di diagnostica per le route che consentono di risolvere il problema.

Le tabelle di route sono associate alle subnet e valgono per tutte le interfacce di rete della subnet. I seguenti tipi di route sono applicabili a ogni interfaccia di rete:

- **Route di sistema:** per impostazione predefinita, ogni subnet creata in una rete virtuale di Azure dispone di tabelle di route di sistema che consentono il traffico di rete virtuale locale, il traffico locale tramite gateway VPN e il traffico Internet. Le route di sistema esistono anche per le reti virtuali con peering.
- **Route BGP:** sono propagate alle interfacce di rete tramite connessioni VPN site-to-site o ExpressRoute. Per ulteriori informazioni sul routing BGP, leggere gli articoli [BGP con i gateway VPN](../vpn-gateway/vpn-gateway-bgp-overview.md) e [Panoramica relativa a ExpressRoute](../expressroute/expressroute-introduction.md).
- **Route definite dall'utente:** se si usano dispositivi virtuali di rete o si forza il tunneling del traffico verso una rete locale tramite una VPN site-to-site, potrebbero esserci route definite dall'utente associate alla tabella di route della subnet. Se non si ha dimestichezza con le route definite dall'utente, leggere l'articolo sulle [route definite dall'utente](virtual-networks-udr-overview.md#user-defined-routes).

Con le varie route che possono essere applicate a un'interfaccia di rete, può essere difficile determinare quali route di aggregazione sono efficaci. Per risolvere i problemi di connettività di rete delle VM, è possibile visualizzare tutte le route valide per un'interfaccia di rete nel modello di distribuzione Azure Resource Manager.

## Uso di regole efficaci per risolvere i problemi di flusso del traffico delle VM

In questo articolo viene usato lo scenario seguente come esempio per illustrare come risolvere i problemi di route valide per un'interfaccia di rete:

Una VM (*VM1*) connessa alla rete virtuale (*VNet1*, prefisso: 10.9.0.0/16) non riesce a connettersi a un VM (VM3) in una rete virtuale appena associata tramite peering (*VNet3*, prefisso 10.10.0.0/16). Non ci sono route definite dall'utente o route BGP applicate all'interfaccia di rete VM1-NIC1 connessa alla VM; sono applicate solo le route di sistema.

Questo articolo spiega come determinare la causa dell'errore di connessione usando la capacità delle route valide nel modello di distribuzione Azure Resource Manager. Sebbene in questo esempio si usino solo le route di sistema, è possibile attenersi alla stessa procedura per determinare gli errori di connessione in ingresso e in uscita su qualsiasi tipo di route.

>[AZURE.NOTE] Se alla VM è collegata più di un'interfaccia di rete, verificare le route valide per ciascuna delle interfacce di rete per diagnosticare i problemi di connettività di rete da e verso una VM.

### Visualizzare le route valide per una macchina virtuale

Per visualizzare le route di aggregazione applicate a una VM, completare i passaggi seguenti:

### Visualizzare le route valide per un'interfaccia di rete

Per visualizzare le route di aggregazione applicate a un'interfaccia di rete, completare i passaggi seguenti:

1.  Avviare una sessione di Azure PowerShell e accedere ad Azure. Se non si ha dimestichezza con Azure PowerShell, leggere l'articolo [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

2.  Il comando seguente restituisce tutte le route applicate a un'interfaccia di rete denominata *VM1 NIC1* nel gruppo di risorse *RG1*.

		Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1

	>[AZURE.TIP] Se non si conosce il nome di un'interfaccia di rete, digitare il comando seguente per recuperare i nomi di tutte le interfacce di rete in un gruppo di risorse.*

	    Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name

	L'output seguente ha un aspetto simile all'output per ciascuna route applicata alla subnet a cui è connessa l'interfaccia di rete:

		Name :
		State : Active
		AddressPrefix : {10.9.0.0/16}
		NextHopType : VNetLocal
		NextHopIpAddress : {}

		Name :
		State : Active
		AddressPrefix : {0.0.0.0/16}
		NextHopType : Internet
		NextHopIpAddress : {}

	Prestare attenzione agli aspetti seguenti nell'output:
	- **Name**: il nome della route valida può essere vuoto, a meno che non venga specificato esplicitamente, per le route definite dall'utente.
	- **State**: indica lo stato della route valida. Alcuni valori possibili sono "Active" o "Invalid"
	- **AddressPrefixes**: specifica il prefisso dell'indirizzo della route valida in notazione CIDR.
	- **nextHopType**: indica l'hop successivo per la route specificata. Alcuni valori possibili sono *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering* e *Null*. Un valore *Null* per **nextHopType** in una route definita dall'utente potrebbe indicare una route non valida. Ad esempio, se **nextHopType** è *VirtualAppliance* e la VM del dispositivo virtuale di rete non si trova nello stato di provisioning/esecuzione. Se **nextHopType** è *VPNGateway* e non sono presenti gateway in provisioning/in esecuzione nella rete virtuale specificata, la route potrebbe diventare non valida.
	- **NextHopIpAddress**: specifica l'indirizzo IP dell'hop successivo della route valida.
    
	Il comando seguente restituisce le route in una tabella più semplice da visualizzare:

		Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1 | Format-Table

	L'output seguente è parte dell'output ricevuto per lo scenario descritto in precedenza:

		Name State AddressPrefix NextHopType NextHopIpAddress
		---- ----- ------------- ----------- ----------------
		Active {10.9.0.0/16} VnetLocal {}
		Active {0.0.0.0/0} Internet {}
	

3. Non ci sono route elencate per la rete virtuale *WestUS VNet3* (prefisso 10.10.0.0/16)** da *WestUS VNet1* (prefisso 10.9.0.0/16) nell'output del passaggio precedente. Come illustrato nell'immagine seguente, la rete virtuale che collega in peering il collegamento con la rete virtuale *WestUS VNet3* ha lo stato *Disconnesso*.
	
	![](./media/virtual-network-routes-troubleshoot-portal/image4.png)

	Il collegamento bidirezionale per il peering è interrotto, il che spiega il motivo per cui non è riuscita la connessione tra VM1 e VM3 nella rete virtuale *WestUS VNet3*. Configurare di nuovo un collegamento di peering di rete virtuale bidirezionale per le reti virtuali *WestUS VNet1* e *WestUS VNet3*. Di seguito è mostrato l'output restituito dopo che il collegamento di peering della rete virtuale è stato stabilito correttamente:

		Name State AddressPrefix NextHopType NextHopIpAddress
		---- ----- ------------- ----------- ----------------
		Active {10.9.0.0/16} VnetLocal {}
		Active {10.10.0.0/16} VNetPeering {}
		Active {0.0.0.0/0} Internet {}
		
	Dopo aver determinato il problema, è possibile aggiungere, rimuovere, o modificare le route e le relative tabelle. Digitare il comando seguente per visualizzare un elenco dei comandi usati per eseguire questa operazione:

		Get-Help *-AzureRmRouteConfig

## Considerazioni

Alcuni aspetti da tenere presenti quando si esamina l'elenco delle route restituito:

- Il routing è basato sulla corrispondenza del prefisso più lunga (LPM) tra le route definite dall'utente, BGP e di sistema. Se è presente più di una route con la stessa corrispondenza LPM, allora la route viene selezionata in base alla sua origine nell'ordine seguente:
	- Route definita dall'utente
	- Route BGP
	- Route di sistema (predefinita)

	Con route valide, è possibile vedere solo route valide con corrispondenza LPM basata su tutte le route disponibili. Mostrando come le route vengono effettivamente valutate per un'interfaccia di rete specificata, è molto più semplice risolvere i problemi delle route specifiche che potrebbero influire sulle connettività da e verso la VM.

- Se si dispone di route definite dall'utente e si invia traffico a un dispositivo virtuale di rete, con *VirtualAppliance* come **nextHopType**, assicurarsi che l'inoltro IP sia abilitato nel dispositivo virtuale di rete che riceve il traffico o che i pacchetti vengano eliminati.
- Se è abilitato il tunneling forzato, tutto il traffico Internet in uscita verrà instradato in locale. Una connessione RDP o SSH da Internet alla VM potrebbe non funzionare con questa impostazione, a seconda di come l'hardware di rete locale gestisce il traffico. Il tunneling forzato può essere abilitato:
	- Se si usa una VPN site-to-site, impostando una route definita dall'utente con nextHopType come Gateway VPN
	- Se una route predefinita è pubblicizzata su BGP
- Per il corretto funzionamento del traffico peering di rete virtuale, deve esistere una route di sistema con **nextHopType** *VNetPeering* per l'intervallo dei prefissi della rete virtuale con peering. Se non esiste una route di questo tipo e il collegamento di peering della rete virtuale ha un aspetto corretto:
	- Attendere alcuni secondi e riprovare, se si tratta di un collegamento di peering appena stabilito. In alcuni casi è necessario più tempo per propagare le route a tutte le interfacce di rete in una subnet.
	- Le regole del gruppo di sicurezza di rete possono influire sui flussi di traffico. Per altre informazioni, vedere l'articolo [Troubleshoot Network Security Groups](virtual-network-nsg-troubleshoot-powershell.md) (Risolvere i problemi dei gruppi di sicurezza di rete).

<!---HONumber=AcomDC_0928_2016-->