<properties
   pageTitle="Prerequisiti per l'adozione di ExpressRoute"
   description="Questa pagina fornisce un elenco di requisiti da soddisfare prima di potere ordinare un circuito ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor="tysonn"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/28/2015"
   ms.author="cherylmc"/>


# Prerequisiti per ExpressRoute  

Per connettersi ai servizi cloud Microsoft usando ExpressRoute, sarà necessario verificare che siano stati soddisfatti i prerequisiti seguenti:

## Prerequisiti per la connettività

- Un account Microsoft Azure valido e attivo
- Una relazione con un provider di servizi di rete (NSP) o un provider di Exchange (EXP) dall'[elenco di provider supportati](expressroute-locations.md) tramite il quale è necessario facilitare la connettività. È necessaria una relazione commerciale esistente con il provider di servizi di rete o con il provider di Exchange. Sarà necessario assicurarsi che il servizio usato sia compatibile con ExpressRoute.
- Se si vuole usare un provider di servizi di rete e tale provider non è incluso nell'elenco precedente, sarà comunque possibile creare una connessione ad Azure.
	- Contattare il provider di servizi di rete per verificare se è presente in una delle località di Exchange elencate in precedenza.
	- Richiedere al provider di servizi di rete di estendere la rete alla località di Exchange scelta.
	- Ordinare un circuito ExpressRoute tramite il provider di Exchange per connettersi ad Azure.
- Connettività all'infrastruttura di provider di servizi. È necessario soddisfare i criteri per almeno uno degli elementi seguenti elencati:
	- È necessario essere un cliente VPN del provider di servizi di rete e avere almeno un sito locale connesso all'infrastruttura di rete VPN del provider di servizi di rete. Contattare il provider di servizi di rete per verificare se il servizio VPN in uso soddisfa i requisiti per ExpressRoute
	- L'infrastruttura condivide il percorso con il data center del provider di Exchange.
	- Si dispone di connettività Ethernet all'infrastruttura di scambio Ethernet del provider di Exchange.
- Indirizzi IP e numeri AS per la configurazione di routing.
	- È possibile usare numeri AS privati per connettersi al dominio di routing di peering privato di Azure. Se si sceglie questa opzione, dovrà essere > 65000. Per altre informazioni sui numeri AS, vedere la pagina relativa ai [numeri di sistemi autonomi (AS)](http://www.iana.org/assignments/as-numbers/as-numbers.xhtml).
	- Indirizzi IP per configurare le route. È necessaria una subnet /28. Non deve sovrapporsi con eventuali intervalli di indirizzi IP usati in locale o in Azure.
	- È necessario usare i propri numeri AS pubblici per configurare le sessioni BGP con i servizi pubblici di Azure.

## Passaggi successivi

- Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).
- Per informazioni su come configurare la connessione ExpressRoute, vedere:
	- [Configurare una connessione ExpressRoute tramite un provider di servizi di rete](expressroute-configuring-nsps.md)
	- [Configurare una connessione ExpressRoute mediante un provider di Exchange](expressroute-configuring-exps.md)
 

<!---HONumber=August15_HO6-->