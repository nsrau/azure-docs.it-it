<properties 
   pageTitle="Risolvere i problemi relativi alle route - Portale | Microsoft Azure"
   description="Informazioni su come risolvere i problemi relativi alle route nel modello di distribuzione Azure Resource Manager usando il portale di Azure."
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

# Risolvere i problemi relativi alle route con il portale di Azure

> [AZURE.SELECTOR]
- [Portale di Azure](virtual-network-routes-troubleshoot-portal.md)
- [PowerShell](virtual-network-routes-troubleshoot-powershell.md)

Se si riscontrano problemi di connettività di rete da o verso una macchina virtuale (VM) di Azure, le route potrebbero influire sui flussi di traffico della VM. Questo articolo offre una panoramica delle funzionalità di diagnostica per le route che consentono di risolvere il problema.

Le tabelle di route sono associate alle subnet e sono valide per tutte le interfacce di rete della subnet. A ogni interfaccia di rete sono applicabili i tipi di route seguenti.

- **Route di sistema:** per impostazione predefinita, ogni subnet creata in una rete virtuale di Azure ha tabelle di route di sistema che consentono il traffico di rete virtuale locale, il traffico locale tramite gateway VPN e il traffico Internet. Le route di sistema esistono anche per le reti virtuali con peering.
- **Route BGP:** sono propagate alle interfacce di rete tramite connessioni VPN da sito a sito o ExpressRoute. Per altre informazioni sul routing BGP, vedere gli articoli [BGP con i gateway VPN](../vpn-gateway/vpn-gateway-bgp-overview.md) e [Panoramica relativa a ExpressRoute](../expressroute/expressroute-introduction.md).
- **Route definite dall'utente:** se si usano appliance virtuali di rete o il tunneling forzato del traffico verso una rete locale tramite una VPN da sito a sito, alla tabella di route della subnet potrebbero essere associate route definite dall'utente. Se non si ha familiarità con questo tipo di route, vedere l'articolo sulle [route definite dall'utente](virtual-networks-udr-overview.md#user-defined-routes).

Con le varie route che possono essere applicate a un'interfaccia di rete, può essere difficile determinare le route aggregate valide. Per risolvere i problemi di connettività di rete delle VM, è possibile visualizzare tutte le route valide per un'interfaccia di rete nel modello di distribuzione Azure Resource Manager.

## Uso delle route valide per risolvere i problemi di flusso del traffico delle VM

Per illustrare come risolvere i problemi relativi alle route valide per un'interfaccia di rete, questo articolo usa come esempio lo scenario seguente:

Una VM (*VM1*) connessa alla rete virtuale (*VNet1*, prefisso 10.9.0.0/16) non riesce a connettersi a un VM (VM3) in una rete virtuale di cui è appena stato eseguito il peering (*VNet3*, prefisso 10.10.0.0/16). All'interfaccia di rete VM1-NIC1 connessa alla VM non sono applicate route definite dall'utente o route BGP, ma solo route di sistema.

Questo articolo illustra come determinare la causa dell'errore di connessione usando le funzionalità delle route valide nel modello di distribuzione Azure Resource Manager. Nonostante l'esempio usi solo route di sistema, la stessa procedura può essere seguita per determinare gli errori di connessione in ingresso e in uscita su qualsiasi tipo di route.

>[AZURE.NOTE] Se alla VM sono collegate più interfacce di rete, controllare le route valide per ognuna per diagnosticare i problemi di connettività di rete da e verso una VM.

### Visualizzare le route valide per una macchina virtuale

Per visualizzare le route aggregate applicate a una VM, seguire questa procedura:

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
2. Fare clic su **Altri servizi** e quindi su **Macchine virtuali** nell'elenco visualizzato.
3. Selezionare una VM in cui risolvere i problemi nell'elenco visualizzato. Verrà visualizzato un pannello della VM contenente opzioni.
4. Fare clic su **Diagnostica e risoluzione dei problemi** e quindi selezionare un problema comune. Per questo esempio si seleziona **Non è possibile connettersi alla macchina virtuale Windows**.

	![](./media/virtual-network-routes-troubleshoot-portal/image1.png)  

5. Sotto il problema vengono visualizzati i passaggi, come illustrato nell'immagine seguente:

	![](./media/virtual-network-routes-troubleshoot-portal/image2.png)  

	Fare clic su *effective routes* (route valide) nell'elenco dei passaggi consigliati.

6. Verrà visualizzato il pannello **Route valide**, come illustrato nell'immagine seguente:

	![](./media/virtual-network-routes-troubleshoot-portal/image3.png)  

	Se la VM ha una sola interfaccia di rete, sarà selezionata per impostazione predefinita. Se sono presenti più interfacce di rete, selezionare quella per cui si vogliono visualizzare le route valide.

	>[AZURE.NOTE] Se la VM associata all'interfaccia di rete non si trova in stato di esecuzione, le route valide non verranno visualizzate. Nel portale vengono visualizzate solo le prime 200 route valide. Per l'elenco completo, fare clic su **Scarica**. È possibile filtrare ulteriormente i risultati nel file con estensione csv scaricato.

	Nell'output prestare attenzione agli aspetti seguenti.
	- **Source**: indica il tipo di route. Le route di sistema vengono visualizzate come *Default*, le route definite dall'utente come *User* e le route del gateway (statiche o BGP) come *VPNGateway*.
	- **State**: indica lo stato della route valida. I valori possibili sono *Active* e *Invalid*.
	- **AddressPrefixes**: specifica il prefisso dell'indirizzo della route valida nella notazione CIDR.
	- **nextHopType**: indica l'hop successivo per la route specificata. I valori possibili sono *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering* e *Null*. Un valore *Null* per **nextHopType** in una route definita dall'utente potrebbe indicare una route non valida, ad esempio se il valore di **nextHopType** è *VirtualAppliance* e la VM appliance virtuale di rete non si trova in stato di provisioning/esecuzione. Se il valore di **nextHopType** è *VPNGateway* e non sono presenti gateway in stato di provisioning/esecuzione nella rete virtuale specificata, la route potrebbe diventare non valida.
    
7. Nell'immagine del passaggio precedente non sono elencate route alla rete virtuale *WestUS-VNET3* (prefisso 10.10.0.0/16) da *WestUS-VNet1* (prefisso 10.9.0.0/16). Nell'immagine seguente, lo stato del collegamento per il peering è *Disconnesso*:
	
	![](./media/virtual-network-routes-troubleshoot-portal/image4.png)  

	Il collegamento bidirezionale per il peering è interrotto e questo spiega perché la macchina virtuale VM1 non è riuscita a connettersi a VM3 nella rete virtuale *WestUS-VNet3*.

8. L'immagine seguente illustra le route dopo che è stato stabilito il collegamento bidirezionale per il peering:

	![](./media/virtual-network-routes-troubleshoot-portal/image5.png)  

Per altri scenari di risoluzione dei problemi correlati al tunneling forzato e alla valutazione delle route, vedere la sezione [Considerazioni](virtual-network-routes-troubleshoot-portal.md#Considerations) di questo articolo.

### Visualizzare le route valide per un'interfaccia di rete

In caso di impatto sul flusso del traffico di rete per una determinata interfaccia di rete, è possibile visualizzare direttamente un elenco completo delle route valide nell'interfaccia di rete. Per visualizzare le route aggregate applicate a un'interfaccia di rete, seguire questa procedura:

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
2. Fare clic su **Altri servizi** e quindi su **Interfacce di rete**
3. Cercare nell'elenco il nome di un'interfaccia di rete oppure selezionarlo nell'elenco visualizzato. In questo esempio si seleziona **VM1-NIC1**.
4. Selezionare **Route valide** nel pannello **Interfaccia di rete**, come illustrato nell'immagine seguente:
   
   	![](./media/virtual-network-routes-troubleshoot-portal/image6.png)  

    L'impostazione predefinita di **Ambito** corrisponde all'interfaccia di rete selezionata.

  	![](./media/virtual-network-routes-troubleshoot-portal/image7.png)  


### Visualizzare le route valide per una tabella di route

Quando si modificano route definite dall'utente in una tabella di route, può essere opportuno esaminare l'impatto delle route aggiunte in una determinata VM. Una tabella di route può essere associata a qualsiasi numero di subnet. È ora possibile visualizzare tutte le route valide per tutte le interfacce di rete a cui è applicata una determinata tabella di route restando nel contesto del pannello della specifica tabella di route.

Per questo esempio, la route definita dall'utente *UDRoute* è specificata nella tabella di route *UDRouteTable* e invia tutto il traffico Internet da *Subnet1* della rete virtuale *WestUS-VNet1*, attraverso un'appliance virtuale di rete, a *Subnet2* della stessa rete virtuale. La route è illustrata nella figura seguente:

![](./media/virtual-network-routes-troubleshoot-portal/image8.png)  

Per visualizzare le route aggregate per una tabella di rete, seguire questa procedura:

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
2. Fare clic su **Altri servizi** e quindi su **Tabelle route**.
3. Cercare nell'elenco la tabella di route per cui si vogliono visualizzare le route aggregate e selezionarla. In questo esempio si seleziona **UDRouteTable**. Viene visualizzato un pannello per la tabella di route selezionata, come illustrato nell'immagine seguente:

	![](./media/virtual-network-routes-troubleshoot-portal/image9.png)  

4. Selezionare **Route valide** nel pannello **Tabella route**. L'impostazione di **Ambito** corrisponde alla tabella di route selezionata.
5. Una tabella di route può essere applicata a più subnet. In **Subnet** selezionare quella che si vuole esaminare nell'elenco. In questo esempio si seleziona **Subnet1**.
6. Selezionare un'opzione in **Interfaccia di rete**. Nell'elenco sono incluse tutte le interfacce di rete connesse alla subnet selezionata. In questo esempio si seleziona **VM1-NIC1**.

	![](./media/virtual-network-routes-troubleshoot-portal/image10.png)  

	>[AZURE.NOTE] Se l'interfaccia di rete non è associata a una VM in esecuzione, non vengono visualizzate route valide.

## Considerazioni

Alcuni aspetti da tenere presenti quando si esamina l'elenco delle route restituito:

- Il routing è basato sulla corrispondenza di prefisso più lungo (LPM, Longest Prefix Match) tra route definite dall'utente, BGP e di sistema. Se sono presenti più route con la stessa corrispondenza LPM, la route viene selezionata in base alla relativa origine nell'ordine seguente:
	- Route definita dall'utente
	- Route BGP
	- Route di sistema (predefinita)

	Le route valide vengono visualizzate solo se presentano una corrispondenza LPM in base a tutte le route disponibili. Visualizzando come vengono effettivamente valutate le route per una determinata interfaccia di rete, è molto più semplice risolvere i problemi relativi a route specifiche che potrebbero influire sulle connettività da e verso la VM.

- Se sono presenti route definite dall'utente e si invia il traffico a un'appliance virtuale di rete, con *VirtualAppliance* come **nextHopType**, assicurarsi che nell'appliance virtuale di rete che riceve il traffico sia abilitato l'inoltro IP o i pacchetti verranno eliminati.
- Se è abilitato il tunneling forzato, tutto il traffico Internet in uscita verrà instradato in locale. A seconda di come viene gestito il traffico in locale, una connessione RDP/SSH da Internet alla VM potrebbe non funzionare con questa impostazione. Il tunneling forzato può essere abilitato nei casi seguenti:
	- Se si usa una VPN da sito a sito, impostando una route definita dall'utente con Gateway VPN come nextHopType
	- Se una route predefinita è annunciata su BGP
- Per il corretto funzionamento del traffico del peering reti virtuali, deve essere presente una route di sistema con **nextHopType** *VNetPeering* per l'intervallo di prefissi della rete virtuale con peering. Se non esiste una route di questo tipo e il collegamento per il peering reti virtuali appare corretto:
	- Se il collegamento per il peering è appena stato stabilito, attendere alcuni secondi e riprovare. In alcuni casi è necessario più tempo per propagare le route a tutte le interfacce di rete in una subnet.
	- Le regole dei gruppi di sicurezza di rete (NSG) potrebbero influire sui flussi di traffico. Per altre informazioni, vedere l'articolo relativo alla [risoluzione dei problemi dei gruppi di sicurezza di rete](virtual-network-nsg-troubleshoot-portal.md).

<!---HONumber=AcomDC_0928_2016-->