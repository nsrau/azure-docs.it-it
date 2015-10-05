<properties
   pageTitle="Prerequisiti per l'adozione di ExpressRoute | Microsoft Azure"
   description="Questa pagina fornisce un elenco di requisiti da soddisfare prima di potere ordinare un circuito Azure ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/21/2015"
   ms.author="cherylmc"/>


# Prerequisiti per ExpressRoute   

Per connettersi ai servizi cloud Microsoft usando ExpressRoute, sarà necessario verificare che siano stati soddisfatti i requisiti elencati nelle sezioni seguenti.

## Requisiti di account

- Un account Microsoft Azure valido e attivo. Questo è necessario per configurare il circuito ExpressRoute. I circuiti ExpressRoute sono risorse all'interno di sottoscrizioni di Azure. Una sottoscrizione di Azure è un requisito, anche se la connettività è limitata a servizi cloud non Microsoft Azure, ad esempio servizi di Office 365 e CRM Online.
- Una sottoscrizione di Office 365 attiva (se si usano servizi di Office 365). Per altre informazioni, vedere la sezione [Requisiti specifici di Office 365](#office-365-specific-requirements) in questo articolo.

## Relazione con un provider di connettività

- Una relazione con un provider di connettività dall'elenco di provider supportati tramite il quale è necessario fornire la connettività. È necessaria una relazione commerciale esistente con il provider di connettività. Assicurarsi che il servizio fornito dal provider di connettività sia compatibile con ExpressRoute.
- Se si desidera usare un provider di connettività non incluso nell'elenco dei provider supportati, è comunque possibile creare una connessione ai servizi cloud Microsoft tramite scambio.
	- Contattare il provider di connettività per verificare se è presente in una delle località di scambio nell'elenco dei provider supportati.
	- Richiedere al provider di connettività di estendere la rete alla località di scambio scelta.
	- Ordinare un circuito ExpressRoute con lo scambio come provider di connettività.

## Connettività fisica tra la rete e il provider di connettività

Per informazioni dettagliate sui modelli di connettività, vedere la sezione relativa ai modelli di connettività. I clienti devono verificare che la propria infrastruttura locale sia fisicamente connessa all'infrastruttura di provider di servizi tramite uno dei modelli descritti.

## Requisiti di ridondanza per la connettività

Non sono previsti requisiti di ridondanza per la connettività fisica tra l'infrastruttura del cliente e l'infrastruttura del provider di servizi. Microsoft richiede la ridondanza nel livello 3. Richiede invece la configurazione di un routing ridondante tra il server perimetrale di Microsoft e la rete del cliente tramite il provider di servizi per l'abilitazione di ogni peering. Se le sessioni di routing non vengono configurate in modo ridondante, il contratto di servizio sulla disponibilità dei servizi non sarà valido.

## Considerazioni sul routing e gli indirizzi IP

I clienti/provider di connettività sono responsabili della configurazione di sessioni BGP ridondanti con l'infrastruttura perimetrale di Microsoft. I clienti che scelgono di connettersi mediante provider VPN IP si affideranno ai provider di connettività per gestire le configurazioni di routing. I clienti con percorso condiviso con uno scambio o che si connettono a Microsoft tramite un provider Ethernet punto a punto dovranno configurare sessioni BGP ridondanti per ogni peering per soddisfare i requisiti del contratto di servizio per la disponibilità. I provider di connettività possono fornire questo servizio come valore aggiunto. Per altre informazioni sui limiti, fare riferimento alla tabella dei domini di routing nell'articolo [Circuiti e domini di routing ExpressRoute](expressroute-circuit-peerings.md).

## Sicurezza e configurazione di firewall

Per informazioni sulla sicurezza e la configurazione di firewall, vedere il documento relativo ai [servizi cloud Microsoft e alla sicurezza di rete](../best-practices-network-security.md).

## Configurazione di NAT per il peering Microsoft e il peering pubblico di Azure

Per istruzioni dettagliate sui requisiti e le configurazioni, vedere [Requisiti NAT di ExpressRoute](expressroute-nat.md) . Contattare il provider di connettività per verificare se viene configurata e gestita un'infrastruttura NAT. I provider di connettività di livello 3 in genere gestiscono un'infrastruttura NAT.

## Requisiti specifici di Office 365

Esaminare le risorse seguenti per altre informazioni sui requisiti di Office 365.

- [Pianificazione della rete e ottimizzazione delle prestazioni per Office 365](http://aka.ms/tune)
- [Gestione del traffico di rete di Office 365](https://msft.spoppe.com/teams/cpub/teams/IW_Admin/modsquad/_layouts/15/WopiFrame.aspx?sourcedoc=%7b23f09224-0668-4476-8627-aaff30931439%7d&action=edit&source=https%3A%2F%2Fmsft%2Espoppe%2Ecom%2Fteams%2Fcpub%2Fteams%2FIW%5FAdmin%2Fmodsquad%2FSitePages%2FHome%2Easpx)
- Vedere l'articolo relativo ai [requisiti di Qualità del servizio (QoS) di ExpressRoute](expressroute-qos.md) per informazioni dettagliate sui requisiti e le configurazioni di QoS. Rivolgersi al proprio provider di connettività per verificare se vengono offerte più classi di servizio per la VPN. 

## Passaggi successivi

- Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).
- Trovare un provider di servizi. Vedere [Partner e località di peering per Azure ExpressRoute](expressroute-locations.md).
- Fare riferimento ai requisiti per [routing](expressroute-routing.md), [NAT](expressroute-nat.md) e [QoS](expressroute-qos.md).
- Configurare la connessione ExpressRoute.
	- [Creare un circuito ExpressRoute](expressroute-howto-circuit-classic.md)
	- [Configurare il routing](expressroute-howto-routing-classic.md)
	- [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-classic.md)

<!---HONumber=Sept15_HO4-->