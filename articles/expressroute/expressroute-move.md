<properties
   pageTitle="Spostamento dei circuiti ExpressRoute dal modello di distribuzione classica a quello di Resource Manager | Microsoft Azure"
   description="Questa pagina offre una panoramica delle informazioni utili sul bridging dei modelli di distribuzione classica e di Resource Manager."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/01/2016"
   ms.author="ganesr"/>

# Spostamento dei circuiti ExpressRoute dal modello di distribuzione classica a quello di Resource Manager

Questo articolo offre una panoramica di che cosa si intenda per spostamento di un circuito ExpressRoute dal modello di distribuzione classica a quello di Resource Manager.

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

Per connettersi alle reti virtuali distribuite con i modelli di distribuzione sia classica che di Resource Manager, si può usare un solo circuito ExpressRoute. Un circuito ExpressRoute, indipendentemente da come viene creato, ora può essere collegato alle reti virtuali in entrambi i modelli di distribuzione.

![](./media/expressroute-move/expressroute-move-1.png)

## Circuiti ExpressRoute creati nel modello di distribuzione classica

I circuiti ExpressRoute creati nel modello di distribuzione classica dovranno essere spostati nel modello di distribuzione di Resource Manager prima di abilitare la connettività ai modelli di distribuzione sia classica che di Resource Manager Non si verificheranno perdite o interruzioni della connettività durante lo spostamento di una connessione. Tutti i collegamenti circuito-rete virtuale nel modello di distribuzione classica (nella stessa sottoscrizione e tra sottoscrizioni) verranno mantenuti. Al termine dello spostamento, il circuito ExpressRoute avrà esattamente lo stesso aspetto e le stesse prestazioni di un circuito ExpressRoute creato nel modello di distribuzione di Resource Manager. Ora sarà possibile creare le connessioni alle reti virtuali nel modello di distribuzione di Resource Manager.

Una volta spostato un circuito ExpressRoute nel modello di distribuzione di Resource Manager, sarà possibile gestire il ciclo di vita del circuito ExpressRoute solo usando il modello di distribuzione Resource Manager. Quindi operazioni come l'aggiunta/aggiornamento/eliminazione di peering, l'aggiornamento di proprietà dei circuiti, ad esempio la larghezza di banda, la SKU e il tipo di fatturazione, e l'eliminazione dei circuiti possono essere eseguite solo nel modello di distribuzione di Resource Manager. Vedere la sezione seguente sui circuiti creati nel modello di distribuzione di Resource Manager per altri dettagli su come sia possibile gestire l'accesso a entrambi i modelli di distribuzione.

Per eseguire lo spostamento, non è necessario coinvolgere il provider di connettività.

## Circuiti ExpressRoute creati nel modello di distribuzione di Resource Manager

È possibile fare in modo che i circuiti ExpressRoute creati nel modello di distribuzione di Resource Manager siano accessibili da entrambi i modelli di distribuzione. È possibile fare in modo che un circuito ExpressRoute nella sottoscrizione sia accessibile da entrambi i modelli di distribuzione.

- I circuiti ExpressRoute creati nel modello di distribuzione di Resource Manager non avranno accesso al modello di distribuzione classica per impostazione predefinita.
- I circuiti ExpressRoute che sono stati spostati dal modello di distribuzione classica al modello di distribuzione di Resource Manager saranno accessibili da entrambi i modelli di distribuzione per impostazione predefinita.
- Un circuito ExpressRoute avrà sempre accesso al modello di distribuzione di Resource Manager indipendentemente dal fatto che sia stato creato nel modello di distribuzione di Resource Manager o nel modello di distribuzione classica. Ciò significa che è possibile creare connessioni alle reti virtuali create nel modello di distribuzione di Resource Manager seguendo le istruzioni su [come collegare le reti virtuali](expressroute-howto-linkvnet-arm.md). 
- L'accesso al modello di distribuzione classica è controllato dal parametro "allowClassicOperations" nel circuito ExpressRoute. 

>[AZURE.IMPORTANT] Verranno applicate tutte le quote documentate nella pagina relativa ai [limiti dei servizi](../azure-subscription-service-limits.md). Ad esempio, un circuito standard può avere al massimo 10 collegamenti/connessioni alle reti virtuali sia nel modello di distribuzione classica che in quello di Resource Manager.


### Controllo dell'accesso al modello di distribuzione classica

È possibile abilitare un circuito ExpressRoute per il collegamento alle reti virtuali in entrambi i modelli di distribuzione impostando il parametro "allowClassicOperations" del circuito ExpressRoute.

Impostando "allowClassicOperations" su TRUE, sarà possibile collegare le reti virtuali da entrambi i modelli di distribuzione al circuito ExpressRoute. Per creare i collegamenti alle reti virtuali nel modello di distribuzione classica, seguire le istruzioni su [come collegare le reti virtuali nel modello di distribuzione classica](expressroute-howto-linkvnet-classic.md). Per creare i collegamenti alle reti virtuali nel modello di distribuzione di Resource Manager, seguire le istruzioni su [come collegare le reti virtuali nel modello di distribuzione di Resource Manager](expressroute-howto-linkvnet-arm.md).

Impostando "allowClassicOperations" su FALSE, verrà bloccato l'accesso al circuito dal modello di distribuzione classica. Tutti i collegamenti alle reti virtuali nel modello di distribuzione classica verranno tuttavia conservati. In questo caso, il circuito ExpressRoute non sarà visibile nel modello di distribuzione classica.

### Operazioni supportate nel modello di distribuzione classica

Le operazioni classiche seguenti sono supportate in un circuito ExpressRoute quando "allowClassicOperations" è impostato su TRUE.

 - Ottenere informazioni sul circuito ExpressRoute
 - Creare/Aggiornare/Ottenere/Eliminare i collegamenti delle reti virtuali alle reti virtuali classiche
 - Creare/Aggiornare/Ottenere/Eliminare le autorizzazioni per i collegamenti delle reti virtuali per la connettività tra sottoscrizioni

Non sarà possibile eseguire le operazioni classiche seguenti quando "allowClassicOperations" è impostato su TRUE.

 - Creare/Aggiornare/Ottenere/Eliminare i peering BGP per i peering privati di Azure, pubblici di Azure e Microsoft
 - Eliminare un circuito ExpressRoute

## Comunicazione dalla distribuzione classica alla distribuzione di Resource Manager

Il circuito ExpressRoute fungerà da ponte tra i modelli di distribuzione classica e quello di Resource Manager. Il traffico tra le macchine virtuali distribuite nelle reti virtuali nel modello di distribuzione classica e quelle distribuite nelle reti virtuali nel modello di distribuzione di Resource Manager scorrerà attraverso ExpressRoute se entrambe le reti virtuali sono collegate allo stesso circuito ExpressRoute. La velocità effettiva aggregata verrà limitata dalla capacità di velocità effettiva del gateway di rete virtuale. In questi casi, il traffico non avrà accesso alle reti dell'utente o del provider di connettività. Il flusso del traffico tra le reti virtuali sarà interamente contenuto nella rete Microsoft.

## Accesso alle risorse di peering Microsoft e peering pubblico di Azure

È possibile continuare ad accedere alle risorse normalmente accessibili con il peering pubblico di Azure e il peering Microsoft senza interruzioni.

## Attività supportate

Questa sezione descrive che cosa è supportato con questa funzionalità

 - Per accedere alle reti virtuali distribuite con i modelli di distribuzione sia classica che di Resource Manager, si può usare un solo circuito ExpressRoute.
 - È possibile spostare un circuito ExpressRoute dal modello di distribuzione classica a quello di Resource Manager. Una volta spostato, il circuito ExpressRoute avrà lo stesso aspetto e le stesse prestazioni di qualsiasi altro circuito ExpressRoute creato nel modello di distribuzione di Resource Manager.
 - Solo il circuito ExpressRoute può essere spostato. I gateway VPN, le reti virtuali e i collegamenti del circuito non verranno spostati con questa operazione.
 - Una volta spostato un circuito ExpressRoute nel modello di distribuzione di Resource Manager, sarà possibile gestire il ciclo di vita del circuito ExpressRoute solo usando il modello di distribuzione Resource Manager. Quindi operazioni come l'aggiunta/aggiornamento/eliminazione di peering, l'aggiornamento di proprietà dei circuiti, ad esempio la larghezza di banda, la SKU e il tipo di fatturazione, e l'eliminazione dei circuiti possono essere eseguite solo nel modello di distribuzione di Resource Manager.
 - Il circuito ExpressRoute fungerà da ponte tra i modelli di distribuzione classica e quello di Resource Manager. Il traffico tra le macchine virtuali distribuite nelle reti virtuali nel modello di distribuzione classica e quelle distribuite nelle reti virtuali nel modello di distribuzione di Resource Manager scorrerà attraverso ExpressRoute se entrambe le reti virtuali sono collegate allo stesso circuito ExpressRoute. 
 - Connettività tra sottoscrizioni sia nel modello di distribuzione classica che in quello di Resource Manager.

## Attività NON supportate

Questa sezione descrive che cosa non è supportato con questa funzionalità

 - Spostamento delle rete virtuali, dei gateway e dei collegamenti del circuito dal modello di distribuzione classica a quello di Resource Manager.
 - Gestione del ciclo di vita del circuito ExpressRoute dal modello di distribuzione classica.
 - Supporto del controllo degli accessi in base al ruolo per il modello di distribuzione classica. Non sarà possibile eseguire i controlli degli accessi in base al ruolo per il circuito nel modello di distribuzione classica. Tutti gli amministratori/coamministratori della sottoscrizione potranno collegare/scollegare le reti virtuali nel circuito.

## Configurazione

Seguire le istruzioni descritte in [Spostamento di un circuito ExpressRoute dal modello di distribuzione classica a quello di Resource Manager](expressroute-howto-move-arm.md)

## Passaggi successivi

- Per informazioni sul flusso di lavoro, vedere [Flussi di lavoro e stati di provisioning di un circuito ExpressRoute](expressroute-workflows.md).
- Configurare la connessione ExpressRoute.

	- [Creare un circuito ExpressRoute](expressroute-howto-circuit-arm.md)
	- [Configurare il routing](expressroute-howto-routing-arm.md)
	- [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0406_2016-->