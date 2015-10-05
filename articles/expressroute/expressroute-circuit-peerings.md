<properties 
   pageTitle="Circuiti e domini di routing ExpressRoute | Microsoft Azure"
   description="Questa pagina fornisce una panoramica dei circuiti e domini di routing ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/22/2015"
   ms.author="cherylmc"/>

# Circuiti e domini di routing ExpressRoute

 È necessario ordinare un *circuito ExpressRoute* per connettere un'infrastruttura locale a Microsoft tramite un provider di connettività. La figura riportata di seguito fornisce una rappresentazione logica della connettività fra la rete WAN e Microsoft.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

## Circuiti ExpressRoute

Un *circuito ExpressRoute* rappresenta una connessione logica tra un'infrastruttura locale e i servizi cloud Microsoft tramite un provider di connettività. È possibile ordinare più circuiti ExpressRoute. Ogni circuito può trovarsi nella stessa area o in aree diverse e può essere connesso alle proprie risorse mediante diversi provider di connettività.

I circuiti ExpressRoute non sono mappati ad alcuna entità fisica. Un circuito è identificato in modo univoco da un GUID standard denominato chiave di servizio. La chiave di servizio è l'unica informazione scambiata tra Microsoft, il provider di connettività e l'utente. Non è un segreto usato ai fini della sicurezza. Esiste un mapping 1:1 tra un circuito ExpressRoute e la chiave di servizio.

Per un circuito ExpressRoute sono previsti fino a tre peering indipendenti: pubblico di Azure, privato di Azure e Microsoft. Ogni peering è una coppia di sessioni BGP indipendenti, ognuna configurata in modo ridondante per garantire la disponibilità elevata. Esiste un mapping 1:N (1 <= N <= 3) tra un circuito ExpressRoute e i domini di routing. Per un circuito ExpressRoute può essere abilitato uno, due o tutti e tre i peering.
 
Ogni circuito prevede una larghezza di banda fissa (50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 10 Gbps) ed è mappato a un provider di connettività e a una località di peering. La larghezza di banda selezionata verrà condivisa tra tutti i peering del circuito.

### Quote, limiti e limitazioni

Per ogni circuito ExpressRoute si applicano quote e limiti predefiniti. Per informazioni aggiornate sulle quote, vedere la pagina [Sottoscrizione di Azure e limiti dei servizi, quote e vincoli](../articles/azure-subscription-service-limits.md).

## Domini di routing ExpressRoute

A un circuito ExpressRoute sono associati più domini di routing, ovvero pubblico di Azure, privato di Azure e Microsoft. Ogni dominio di routing è configurato in modo identico in una coppia di router (in una configurazione di tipo attivo-attivo o di condivisione del carico) per offrire una disponibilità elevata. I servizi di Azure sono classificati come *pubblico di Azure* e *privato di Azure* per rappresentare gli schemi di indirizzamento IP.


![](./media/expressroute-circuit-peerings/expressroute-peerings.png)


### Peering privato

I servizi di calcolo di Azure, ovvero le macchine virtuali (IaaS) e i servizi cloud (PaaS), che sono distribuiti all'interno di una rete virtuale possono essere connessi tramite il dominio di peering privato. Il dominio di peering privato viene considerato un'estensione attendibile della rete di base in Microsoft Azure. È possibile configurare la connettività bidirezionale tra la rete di base e le reti virtuali (VNet) di Azure. Ciò permetterà la connessione diretta a macchine virtuali e servizi cloud nei rispettivi indirizzi IP privati.

È possibile connettere più di una rete virtuale al dominio di peering privato. Per informazioni su limiti e limitazioni, vedere la [pagina relativa alle domande frequenti](expressroute-faqs.md). Per informazioni aggiornate sui limiti, visitare la pagina [Sottoscrizione di Azure e limiti dei servizi, quote e vincoli](../articles/azure-subscription-service-limits.md). Per informazioni sulla configurazione del routing, vedere la pagina relativa al [routing](expressroute-routing.md).

### Peering pubblico

I servizi quali Archiviazione, database SQL e Siti Web di Azure vengono offerti su indirizzi IP pubblici. È possibile connettersi privatamente ai servizi ospitati su indirizzi IP pubblici, inclusi gli indirizzi VIP dei servizi cloud, tramite il dominio di routing di peering pubblico. È possibile connettere il dominio di peering pubblico al DMZ e connettersi a tutti i servizi di Azure sui rispettivi indirizzi IP pubblici dalla rete WAN senza doversi connettere a Internet.

La connettività viene sempre attivata dalla rete WAN verso i servizi di Microsoft Azure. I servizi di Microsoft Azure non potranno attivare connessioni alla rete dell'utente tramite questo dominio di routing. Dopo l'abilitazione del peering pubblico, sarà possibile connettersi a tutti i servizi di Azure. Non è consentito scegliere in modo selettivo i servizi per cui vengono annunciate route. Per verificare l'elenco dei prefissi pubblicati tramite questo peering, vedere la pagina relativa agli [intervalli IP per i data center di Microsoft Azure](http://www.microsoft.com/download/details.aspx?id=41653). La pagina viene aggiornata ogni settimana.

È possibile definire filtri di route personalizzati nella propria rete per usare solo le route necessarie. Per informazioni sulla configurazione del routing, vedere la pagina relativa al [routing](expressroute-routing.md). È possibile definire filtri di route personalizzati nella propria rete per usare solo le route necessarie.

Per altre informazioni sui servizi supportati tramite il dominio di routing di peering pubblico, vedere la [pagina relativa alle domande frequenti](expressroute-faqs.md).
 
### Peering Microsoft

La connettività verso tutti gli altri servizi online Microsoft (ad esempio i servizi di Office 365) verrà attivata tramite peering Microsoft. La connettività bidirezionale tra la rete WAN e i servizi cloud Microsoft verrà abilitata tramite il dominio di routing di peering Microsoft. È necessario connettersi ai servizi cloud Microsoft solo tramite indirizzi IP pubblici di cui si è proprietari o di proprietà del proprio provider di connettività ed è necessario rispettare tutte le regole definite. Per altre informazioni, vedere la pagina relativa ai [prerequisiti per ExpressRoute](expressroute-prerequisites.md).

Per altre informazioni sui servizi supportati, sui costi e sui dettagli per la configurazione, vedere la [pagina relativa alle domande frequenti](expressroute-faqs.md). Per informazioni sull'elenco di provider di connettività che offrono supporto per il peering Microsoft, vedere la pagina relativa alle [località per ExpressRoute](expressroute-locations.md).

## Confronto tra i domini di routing

La tabella seguente confronta i tre domini di routing.

||**Peering privato**|**Peering pubblico**|**Peering Microsoft**| |---|---|---|---| |**N. massimo di prefissi supportati per peering**|4000 per impostazione predefinita, 10.000 con ExpressRoute Premium|200|200| |**Intervalli di indirizzi IP supportati**|Qualsiasi indirizzo IPv4 valido entro la rete WAN.|Indirizzi IPv4 pubblici di proprietà dell'utente o del provider di connettività.|Indirizzi IPv4 pubblici di proprietà dell'utente o del provider di connettività.| |**Requisiti per i numeri AS**|Numeri AS privati e pubblici. I clienti devono essere proprietari di un numero AS pubblico. | Numeri AS privati e pubblici. I clienti devono essere proprietari di un numero AS pubblico.| Solo numeri AS pubblici. Il numero AS deve essere convalidato rispetto ai registri di routing per convalidare la proprietà.| |**Indirizzi IP per l'interfaccia di routing**|RFC1918 e indirizzi IP pubblici|Indirizzi IP pubblici registrati per i clienti in registri di routing.| Indirizzi IP pubblici registrati per i clienti in registri di routing.| |**Supporto per hash MD5**| Sì|Sì|Sì|

È possibile scegliere di abilitare uno o più domini di routing come parte del rispettivo circuito ExpressRoute. È possibile scegliere di posizionare tutti i domini di routing nella stessa rete VPN se si vuole combinarli in un singolo dominio di routing. È anche possibile posizionarli in domini di routing diversi, in modo analogo a quanto illustrato nel diagramma. La configurazione consigliata consiste nel connettere il peering privato direttamente alla rete di base e i collegamenti del peering pubblico e Microsoft al DMZ.
 
Se si sceglie di usare tutte e tre le sessioni di peering, saranno necessarie tre coppie di sessioni BGP (una coppia per ogni tipo di peering). Le coppie di sessioni BGP forniscono un collegamento a disponibilità elevata. Se si stabilisce la connessione tramite provider di connettività di livello 2, l'utente sarà responsabile della configurazione e della gestione del routing. È possibile ottenere più informazioni esaminando i [flussi di lavoro](expressroute-workflows.md) per la configurazione di ExpressRoute.

## Passaggi successivi

- Trovare un provider di servizi. Vedere l'articolo relativo ai [provider di servizi e alle località ExpressRoute](expressroute-locations.md).
- Verificare che vengano soddisfatti tutti i prerequisiti. Vedere [Prerequisiti per ExpressRoute](expressroute-prerequisites.md).
- Configurare la connessione ExpressRoute.
	- [Creare un circuito ExpressRoute](expressroute-howto-circuit-classic.md)
	- [Configurare il routing (peering del circuito)](expressroute-howto-routing-classic.md)
	- [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-classic.md)

<!---HONumber=Sept15_HO4-->