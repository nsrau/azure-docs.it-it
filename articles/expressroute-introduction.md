<properties 
   pageTitle="Introduzione a ExpressRoute"
   description="Questa pagina offre una panoramica del servizio ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/> <tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="04/29/2015"
   ms.author="cherylmc"/>

# Panoramica tecnica relativa a ExpressRoute

Microsoft Azure ExpressRoute permette di creare connessioni private tra i data center Microsoft e l'infrastruttura disponibile localmente o in un ambiente con percorso condiviso. Con ExpressRoute è possibile stabilire connessioni a servizi cloud Microsoft, ad esempio Microsoft Azure e Office 365 in una struttura di partner ExpressRoute con percorso condiviso, oppure connettersi direttamente dalla rete WAN esistente, ad esempio una VPN MPLS fornita da un provider di servizi di rete.
 
Le connessioni ExpressRoute offrono un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali. In alcuni casi, l'uso di connessioni ExpressRoute per trasferire i dati tra la rete locale e Azure può offrire vantaggi significativi in termini di costi. Se già stata creata una connessione cross-premise dalla rete locale ad Azure, è possibile eseguire la migrazione a una connessione ExpressRoute, mantenendo intatta la rete virtuale.

Per altre informazioni, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).

## Funzionamento di una connessione ExpressRoute

Per connettere la rete WAN ai servizi cloud Microsoft, è necessario ordinare un circuito dedicato e abilitarlo tramite un provider di connettività. È possibile scegliere tra due provider di connettività: Direct Layer 3 tramite un provider di Exchange oppure Layer 3 tramite un provider di servizi di rete. È possibile scegliere se abilitare uno o entrambi i tipi di connettività tra la rete WAN e il cloud Microsoft.

## Provider di Exchange e provider di servizi di rete
I provider per ExpressRoute vengono classificati come provider di servizi di rete \(NSP, Network Service Provider\) e provider di Exchange \(EXP, Exchange Provider\).

![](./media/expressroute-introduction/expressroute-nsp-exp.png)


| |**Provider di Exchange**|**Provider di servizi di rete**|
|---|---|---|
|**Modello di connettività tipico**| Collegamenti Ethernet da punto a punto o connettività all'infrastruttura Cloud Exchange | Connettività da qualsiasi rete a qualsiasi rete tramite una VPN di telecomunicazioni |
|**Larghezze di banda supportate**|200 Mbps, 500 Mbps, 1 Gbps and 10 Gbps|10 Mbps, 50 Mbps, 100 Mbps, 500 Mbps, 1 Gbps|
|**Provider di connettività**|[Pagina relativa ai provider di Exchange](https://msdn.microsoft.com/library/azure/4da69a0f-8f52-49ea-a990-dacd4202150a#BKMK_EXP)|[Pagina relativa ai provider di servizi di rete](https://msdn.microsoft.com/library/azure/4da69a0f-8f52-49ea-a990-dacd4202150a#BKMK_NSP)|
|**Routing**|Sessioni BGP dirette con i router perimetrali dei clienti| Sessioni BGP con telecomunicazioni|
|**Prezzi**|[Prezzi per i provider di Exchange](http://azure.microsoft.com/pricing/details/expressroute/)|[Prezzi per i provider di servizi di rete](http://azure.microsoft.com/pricing/details/expressroute/)|

### Provider di Exchange \(EXP\)
Collaboriamo con provider di servizi Cloud Exchange, come il gruppo Equinix e TeleCity, nonché con provider di servizi di connettività da punto a punto come Cole e Level 3, per fornire la connettività tra Azure e la sede del cliente. Offriamo larghezze di banda circuito da 200 Mbps a 10 Gbps \(200 Mbps, 500 Mbps, 1 Gbps e 10 Gbps\).

Se l'obiettivo è una connessione Direct Layer 3 tramite un provider di Exchange, è possibile ottenerla in uno di tre modi seguenti:

- L'utente può condividere la località in cui sono offerti i servizi con Equinix Cloud Exchange o TeleCity Cloud IX. In questi casi la connettività ridondante verrà ordinata nell'infrastruttura Cloud Exchange. 
- È possibile usare provider Ethernet, ad esempio Level 3, per configurare circuiti Ethernet tra i data center e Microsoft. 
- È possibile usare il provider di connettività locale per acquisire connettività ridondante per la struttura di provider di Exchange più vicina e connettersi a Cloud Exchange.

Per soddisfare i requisiti del Contratto di servizio, è necessario che sia disponibile connettività ridondante. La connettività diretta al perimetro Microsoft non è supportata. I circuiti dedicati verranno sempre abilitati tramite un provider Ethernet o l'infrastruttura Cloud Exchange locale. Anche se ciò consente di configurare la connettività Layer 2 tra Microsoft e la rete dell'utente, l'estensione del dominio Layer 2 non sarà supportata. È necessario configurare sessioni di routing ridondanti tra i router perimetrali dell'utente e i router perimetrali Microsoft in modo da ottenere connettività Layer 3.

Per altre informazioni sulla configurazione e per esempi concreti, è possibile seguire le indicazioni della guida dettagliata [Configurare una connessione ExpressRoute tramite un EXP](expressroute-configuring-exps.md).


### Provider di servizi di rete \(NSP\)

Microsoft collabora con società di telecomunicazioni come AT&T e British Telecom al fine di offrire la connettività tra Azure e la rete WAN dell'utente. Microsoft offre larghezze di banda circuito da 10 Mbps a 1 Gbps \(10 Mbps, 50 Mbps, 100 Mbps, 500 Mbps, 1 Gbps\).

Se si usano i servizi VPN da qualsiasi provider di servizi di rete che collabora con Microsoft, sarà possibile estendere le reti in Azure senza dover distribuire nuovo hardware o apportare importanti modifiche di configurazione alle reti esistenti.

Per altre informazioni sulla configurazione e per esempi concreti, è possibile seguire le indicazioni della guida dettagliata [Configurare circuiti ExpressRoute tramite NSP](expressroute-configuring-nsps.md).

## Peering di ExpressRoute
La figura riportata di seguito fornisce una rappresentazione logica della connettività fra la rete WAN e Microsoft. È necessario ordinare un "circuito dedicato" per connettere la rete WAN a Microsoft tramite un provider di connettività \(NSP / EXP\). Un "circuito dedicato" rappresenta una connessione logica tra la rete WAN e Microsoft tramite il provider di connettività. È possibile ordinare più circuiti dedicati, ognuno dei quali può trovarsi nella stessa area geografica o in un'area diversa e può essere connesso alla rete WAN tramite provider di servizi diversi.

![](./media/expressroute-introduction/expressroute-basic.png)

A un circuito dedicato saranno associati più domini di routing, ovvero pubblico, privato e Microsoft. Ogni dominio di routing è configurato in modo identico in una coppia di router \(in una configurazione di tipo attivo-attivo o di condivisione del carico\) per offrire una disponibilità elevata.

![](./media/expressroute-introduction/expressroute-peerings.png)


### Peering privato
I servizi di calcolo di Azure, ovvero le macchine virtuali \(IaaS\) e i servizi cloud \(PaaS\) distribuiti all'interno di una rete virtuale, possono essere connessi tramite il dominio di peering privato. Il dominio di peering privato viene considerato un'estensione attendibile della rete di base in Microsoft Azure. È possibile configurare la connettività bidirezionale tra la rete di base e le reti virtuali \(VNet\) di Azure. Ciò permetterà la connessione diretta a macchine virtuali e servizi cloud nei rispettivi indirizzi IP privati.

È possibile connettere più di una rete virtuale al dominio di peering privato. Per informazioni su limiti e limitazioni, vedere la [pagina relativa alle domande frequenti](expressroute-faqs.md).
  

### Peering pubblico
I servizi quali Archiviazione, database SQL e Siti Web di Azure vengono offerti su indirizzi IP pubblici. È possibile connettersi privatamente ai servizi ospitati su indirizzi IP pubblici, inclusi gli indirizzi VIP dei servizi cloud, tramite il dominio routing di peering pubblico. È possibile connettere il dominio di peering pubblico alla rete Extranet e connettersi a tutti i servizi di Azure sui rispettivi indirizzi IP pubblici dalla rete WAN senza doversi connettere a Internet. La connettività viene sempre attivata dalla rete WAN verso i servizi di Microsoft Azure. I servizi di Microsoft Azure non potranno attivare connessioni alla rete dell'utente tramite questo dominio di routing. Dopo l'abilitazione del peering pubblico, sarà possibile connettersi a tutti i servizi di Azure. Non è consentita la selezione solo di alcuni servizi per cui vengono pubblicate route. Per verificare l'elenco dei prefissi pubblicati tramite questo peering, vedere la pagina relativa agli [intervalli IP per i data center di Microsoft Azure](http://www.microsoft.com/it-it/download/details.aspx?id=41653). È possibile definire filtri di route personalizzati nella propria rete per usare solo le route necessarie.

Per altre informazioni sui servizi supportati tramite il dominio di peering pubblico, vedere la [pagina relativa alle domande frequenti](expressroute-faqs.md).
 
### Peering Microsoft
La connettività verso tutti gli altri servizi online Microsoft \(ad esempio i servizi di Office 365\) verrà attivata tramite peering Microsoft. La connettività bidirezionale tra la rete WAN e i servizi cloud Microsoft verrà abilitata tramite il dominio di routing di peering Microsoft. È necessario connettersi ai servizi cloud Microsoft solo tramite indirizzi IP pubblici di cui si è proprietari o di proprietà del proprio provider di connettività ed è necessario rispettare tutte le regole definite. Per altre informazioni, vedere la pagina relativa ai [prerequisiti per ExpressRoute](expressroute-prerequisites.md).

Per altre informazioni sui servizi supportati, sui costi e sui dettagli per la configurazione, vedere la [pagina relativa alle domande frequenti](expressroute-faqs.md). Per informazioni sull'elenco di provider di connettività che offrono supporto per il peering Microsoft, vedere la pagina relativa alle [località per ExpressRoute](expressroute-locations.md).


La tabella seguente confronta i tre domini di routing.

\|\|\*\*Peering privato\*\*\|\*\*Peering pubblico\*\*\|\*\*Peering Microsoft\*\*\| \|---\|---\|---\|---\| \|\*\*N. massimo di prefissi supportati per peering\*\*\|4000 per impostazione predefinita, 10.000 con ExpressRoute Premium\|4000 per impostazione predefinita, 10.000 con ExpressRoute Premium\|200\| \|\*\*Intervalli di indirizzi IP supportati\*\*\|Qualsiasi indirizzo IPv4 valido entro la rete WAN\|Indirizzi IPv4 pubblici di proprietà del provider di connettività\|Indirizzi IPv4 pubblici di proprietà del provider di connettività\| \|\*\*Requisiti per i numeri AS\*\*\|Numeri AS privati e pubblici. I clienti devono essere proprietari di un numero AS pubblico. \| Numeri AS privati e pubblici. I clienti devono essere proprietari di un numero AS pubblico.\| Solo numeri AS pubblici. Il numero AS deve essere convalidato rispetto ai registri di routing per convalidare la proprietà.\| \|\*\*Indirizzi IP per l'interfaccia di routing\*\*\|RFC1918 e indirizzi IP pubblici\|Indirizzi IP pubblici registrati per i clienti / NSP in registri di routing.\| Indirizzi IP pubblici registrati per i clienti / NSP in registri di routing.\| \|\*\*Supporto per hash MD5\*\*\| Sì\|Sì\|Sì\|

È possibile scegliere di abilitare uno o più domini di routing come parte del rispettivo circuito dedicato. È possibile scegliere di posizionare tutti i domini di routing nella stessa rete VPN \(nel caso NSP\) se si vuole inserirli in un singolo dominio di routing. È anche possibile posizionarli in domini di routing diversi, in modo analogo a quanto illustrato nel diagramma precedente. La configurazione consigliata consiste nel peering privato connesso direttamente alla rete di base e nei collegamenti del peering pubblico e Microsoft connessi alla Extranet.
 
Se si sceglie di usare tutte e tre le sessioni di peering, saranno necessarie tre coppie di sessioni BGP \(una coppia per ogni tipo di peering\). Le coppie di sessioni BGP forniscono un collegamento a disponibilità elevata. Se ci si connette tramite provider di Exchange, l'utente sarà responsabile della configurazione e della gestione del routing \(a meno che il provider di Exchange offra la gestione automatica del routing\). Se si sceglie di connettersi tramite i provider di servizi di rete, sarà possibile affidare la gestione automatica del routing a tali provider. È possibile ottenere più informazioni esaminando i flussi di lavoro per la configurazione di ExpressRoute

- [Configurare una connessione ExpressRoute tramite un provider di servizi di rete](expressroute-configuring-nsps.md)
- [Configurare una connessione ExpressRoute mediante un provider di Exchange](expressroute-configuring-exps.md)

## Passaggi successivi

- [Provider di servizi ExpressRoute e sedi](expressroute-locations.md) 
- [Configurare una connessione ExpressRoute tramite un provider di servizi di rete](expressroute-configuring-nsps.md)
- [Configurare una connessione ExpressRoute mediante un provider di Exchange](expressroute-configuring-exps.md)

<!--HONumber=54-->