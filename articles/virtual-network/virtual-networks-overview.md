<properties
   pageTitle="Panoramica della rete virtuale (VNet) di Azure"
   description="Informazioni sulle reti virtuali in Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/05/2015"
   ms.author="telmos" />

# Panoramica di Rete virtuale

Una rete virtuale di Azure (VNet) è una rappresentazione della propria rete personalizzata nel cloud. È possibile controllare le impostazioni della rete di Azure network e definire blocchi di indirizzi DHCP, impostazioni DNS, criteri di sicurezza e routing. È anche possibile segmentare ulteriormente la propria rete virtuale in subnet e distribuire istanze del ruolo PaaS e delle macchine virtuali IaaS di Azure, nello stesso modo è possibile distribuire macchine fisiche e virtuali al data center locale. In pratica è possibile espandere la rete ad Azure, portando i propri blocchi di indirizzi IP.

Per meglio comprendere le reti virtuali, vedere la figura seguente che mostra una rete locale semplificata.

![Rete locale](./media/virtual-networks-overview/figure01.png)

La figura precedente mostra una rete locale connessa a Internet pubblico attraverso un router. È anche possibile visualizzare un firewall tra il router e una rete perimetrale che ospita un server DNS e un server farm Web. Il server farm Web è con carico bilanciato usando un servizio di bilanciamento del carico hardware che viene esposto a Internet e consuma risorse dalla subnet interna. La subnet interna è separata dalla rete perimetrale da un altro firewall e ospita i server, i server di database e i server delle applicazioni del controller di dominio di Active Directory.

La stessa rete può essere ospitata in Azure come illustrato nella figura seguente.

![Rete virtuale di Azure](./media/virtual-networks-overview/figure02.png)

Si noti come l'infrastruttura di Azure assume il ruolo di router, consentendo l'accesso dalla propria VNet a Internet pubblico senza la necessità di alcuna configurazione. I firewall possono essere sostituiti da gruppi di sicurezza di rete (NSG) applicati a ogni singola subnet. I servizi di bilanciamento del carico fisici vengono sostituiti da servizi di bilanciamento del carico Internet e interni in Azure.

## Reti virtuali

Le reti virtuali offrono i servizi seguenti alle istanze del ruolo PaaS e delle macchine virtuali IaaS distribuite:

- **Isolamento**. Le reti virtuali sono completamente isolate una dall'altra. In questo modo è possibile creare reti virtuali separate per la distribuzione, il test e la produzione che usano gli stessi blocchi di indirizzi CIDR.

- **Contenimento**. Le reti virtuali non possono estendersi a più aree di Azure.

    >[AZURE.NOTE]Esistono due diverse modalità di distribuzione in Azure: classica (nota anche come Gestione dei servizi) e Gestione risorse di Azure. Le reti virtuali classiche possono essere aggiunte a un gruppo di affinità o create come reti virtuali regionali. Se si ha disposizione una rete virtuale in un gruppo di affinità, si consiglia di [migrarlo a una rete virtuale regionale](./virtual-networks-migrate-to-regional-vnet.md).

- **Accesso a Internet pubblico**. Tutte le istanze del ruolo PaaS e delle macchine virtuali IaaS in una rete virtuale possono accedere a Internet pubblico per impostazione predefinita. È possibile controllare l'accesso usando gruppi di sicurezza di rete (NSG).

- **Accesso alle macchine virtuali all'interno della rete virtuale**. Le istanze del ruolo PaaS e delle macchine virtuali IaaS possono connettersi alla stessa rete virtuale, anche se sono in subnet diverse senza che sia necessario configurare un gateway o usare indirizzi IP pubblici, riunendo gli ambienti PaaS e IaaS.

- **Risoluzione dei nomi**. Azure offre una risoluzione dei nomi interna per le istanze del ruolo PaaS e delle macchine virtuali IaaS distribuiti nella propria rete virtuale. È possibile anche distribuire i propri server DNS e configurare la rete virtuale per usarli.

- **Connettività**. Le reti virtuali possono essere connesse tra loro e anche al data center locale usando una connessione VPN da sito a sito o una connessione ExpressRoute. Per altre informazioni sui gateway VPN, visitare [Informazioni sui gateway VPN](./vpn-gateway-about-vpngateways.md). Per altre informazioni su ExpressRoute, visitare [Panoramica tecnica relativa a ExpressRoute](./expressroute-introduction.md).

    >[AZURE.NOTE]Verificare di aver creato una nuova rete virtuale prima di distribuire eventuali istanze del ruolo PaaS o delle macchine virtuali IaaS nell'ambiente Azure. Le macchine virtuali basate su ARM richiedono una rete virtuale e, se non si specifica una rete virtuale esistente, Azure crea una rete virtuale predefinita che potrebbe presentare un conflitto dei blocchi di indirizzi CIDR con la propria rete locale, rendendo impossibile la connessione tra la propria rete virtuale e la propria rete locale.

## Subnet

È possibile dividere la rete virtuale in più subnet per l'organizzazione e la sicurezza. Le subnet all'interno di una rete virtuale possono comunicare tra loro senza alcuna configurazione aggiuntiva. È possibile modificare anche le impostazioni di routing al livello di subnet e applicare i gruppi di sicurezza di rete (NSG) alle subnet.

## Indirizzi IP

Ci sono due tipi di indirizzi IP assegnati ai componenti in Azure: pubblici e privati. Le istanze del ruolo PaaS e delle macchine virtuali IaaS distribuite in una subnet di Azure vengono automaticamente assegnate a un indirizzo IP privato a ognuna delle schede di interfaccia di rete basate sui blocchi di indirizzi CIDR assegnati alle proprie subnet. È anche possibile assegnare un indirizzo IP pubblico alle istanze del ruolo PaaS e delle macchine virtuali IaaS.

Questi indirizzi IP sono dinamici, vale a dire che possono cambiare in qualsiasi momento. È possibile si voglia verificare che l'indirizzo IP per alcuni servizi resti uguale in qualsiasi momento. A tale scopo, è possibile riservare un indirizzo IP, rendendolo statico.

## Servizi di bilanciamento del carico di Azure

È possibile usare due tipi dei servizi di bilanciamento del carico in Azure:

- **Servizio di bilanciamento del carico esterno**. È possibile usare un servizio di bilanciamento del carico esterno per garantire un'elevata disponibilità per istanze del ruolo PaaS e delle macchine virtuali IaaS a cui si è eseguito l'accesso da Internet pubblico.

- **Servizio di bilanciamento del carico interno**. È possibile usare un servizio di bilanciamento del carico interno per garantire un'elevata disponibilità per le istanze del ruolo PaaS e delle macchine virtuali IaaS a cui si è eseguito l'accesso da altri servizi nella propria rete virtuale.

Per altre informazioni sul servizio di bilanciamento del carico in Azure, visitare [Panoramica del bilanciamento del carico](../load-balancer-overview.md).

## Gruppi di sicurezza di rete (NGS)

È possibile creare gruppi di sicurezza di rete per controllare l'accesso in ingresso e in uscita per le interfacce di rete (NIC), le macchine virtuali e le subnet. Ogni gruppo di sicurezza di rete contiene una o più regole che specificano se il traffico è approvato o respinto in base all'indirizzo IP di origine, alla porta di origine, all'indirizzo IP di destinazione e alla porta di destinazione. Per altre informazioni sui gruppi di accesso di rete, visitare [Che cos'è un gruppo di sicurezza di rete](../virtual-networks-nsg.md).

## Dispositivo virtuale

Un dispositivo virtuale è semplicemente un'altra macchina virtuale nella propria rete virtuale che esegue una funzione di dispositivo basata sul software, come ad esempio un firewall, l'ottimizzazione WAN o il rilevamento intrusione. È possibile creare una route in Azure per indirizzare il traffico della rete virtuale attraverso un dispositivo virtuale per usarne le funzionalità.

Ad esempio, i gruppi di sicurezza di rete possono essere usati per garantire la sicurezza nella propria rete virtuale. I gruppi di sicurezza di rete tuttavia offrono l'elenco di controllo di accesso di rete (ACL) di livello 4 a pacchetti in entrata e in uscita. Se si vuole usare un modello di sicurezza di livello 7, è necessario usare un dispositivo firewall.

I dispositivi virtuali dipendono da [route e inoltro IP definiti dall'utente](../virtual-networks-udr-overview.md).

## Passaggi successivi

- [Creare una rete virtuale](../virtual-networks-create-a-vnet.md) e subnet.
- [Creare una macchina virtuale in una rete virtuale](../virtual-machines-windows-tutorial.md).
- Informazioni sui [gruppi di sicurezza di rete](../virtual-networks-nsg.md).
- Informazioni sui [servizi di bilanciamento del carico](../load-balancer-overview.md).
- [Riservare un indirizzo IP interno](../virtual-networks-reserved-private-ip.md)
- [Riservare un indirizzo IP pubblico](../virtual-networks-reserved-public-ip.md).
- Informazioni su [route e inoltro IP definiti dall'utente](virtual-networks-udr-overview.md).

<!---HONumber=August15_HO6-->