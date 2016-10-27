<properties
   pageTitle="Spostamento dei circuiti ExpressRoute dal modello di distribuzione classica al modello di distribuzione Resource Manager | Microsoft Azure"
   description="Questo articolo offre una panoramica delle informazioni utili sul bridging dei modelli di distribuzione Resource Manager e classica."
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
   ms.date="10/10/2016"
   ms.author="ganesr"/>


# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>Spostamento dei circuiti ExpressRoute dal modello di distribuzione classica al modello di distribuzione Resource Manager

Questo articolo offre una panoramica di cosa si intende per spostamento di un circuito Azure ExpressRoute dal modello di distribuzione classica al modello di distribuzione Azure Resource Manager.

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Per connettersi alle reti virtuali distribuite nei modelli di distribuzione Resource Manager o classica è possibile usare un solo circuito ExpressRoute. Indipendentemente da come viene creato, un circuito ExpressRoute ora può essere collegato alle reti virtuali in entrambi i modelli di distribuzione.

![Circuito ExpressRoute collegato a reti virtuali in entrambi i modelli di distribuzione](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>Circuiti ExpressRoute creati nel modello di distribuzione classica

I circuiti ExpressRoute creati nel modello di distribuzione classica devono prima essere spostati nel modello di distribuzione Resource Manager per abilitare la connettività a entrambi i modelli di distribuzione. Non si verificano perdite o interruzioni della connettività durante lo spostamento di una connessione. Vengono mantenuti tutti i collegamenti circuito-rete virtuale nel modello di distribuzione classica, nella stessa sottoscrizione e tra sottoscrizioni.

Al termine dello spostamento, il circuito ExpressRoute ha esattamente lo stesso aspetto e le stesse prestazioni di un circuito ExpressRoute creato nel modello di distribuzione Resource Manager. Ora è possibile creare le connessioni alle reti virtuali nel modello di distribuzione Resource Manager.

Dopo aver spostato un circuito ExpressRoute nel modello di distribuzione Resource Manager, il ciclo di vita del circuito ExpressRoute può essere gestito unicamente con il modello di distribuzione Resource Manager. Ciò significa che operazioni come l'aggiunta, l'aggiornamento o l'eliminazione di peering, l'aggiornamento di proprietà dei circuiti, ad esempio la larghezza di banda, lo SKU e il tipo di fatturazione, e l'eliminazione dei circuiti possono essere eseguite solo nel modello di distribuzione Resource Manager. Vedere la sezione seguente sui circuiti creati nel modello di distribuzione Resource Manager per altri dettagli su come gestire l'accesso a entrambi i modelli di distribuzione.

Per eseguire lo spostamento, non è necessario coinvolgere il provider di connettività.

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>Circuiti ExpressRoute creati nel modello di distribuzione Resource Manager

È possibile fare in modo che i circuiti ExpressRoute creati nel modello di distribuzione Resource Manager siano accessibili da entrambi i modelli di distribuzione. È possibile fare in modo che un circuito ExpressRoute nella sottoscrizione sia accessibile da entrambi i modelli di distribuzione.

- I circuiti ExpressRoute creati nel modello di distribuzione Resource Manager non hanno accesso al modello di distribuzione classica per impostazione predefinita.
- I circuiti ExpressRoute che sono stati spostati dal modello di distribuzione classica al modello di distribuzione Resource Manager sono accessibili da entrambi i modelli di distribuzione per impostazione predefinita.
- Un circuito ExpressRoute ha sempre accesso al modello di distribuzione Resource Manager, indipendentemente dal modello di distribuzione, classica o Resource Manager, in cui è stato creato. Ciò significa che è possibile creare connessioni alle reti virtuali create nel modello di distribuzione Resource Manager seguendo le istruzioni su [come collegare le reti virtuali](expressroute-howto-linkvnet-arm.md).
- L'accesso al modello di distribuzione classica è controllato dal parametro **allowClassicOperations** nel circuito ExpressRoute.

>[AZURE.IMPORTANT] Vengono applicate tutte le quote documentate nella pagina relativa ai [limiti dei servizi](../azure-subscription-service-limits.md) . Ad esempio, un circuito standard può avere al massimo 10 collegamenti/connessioni di rete virtuale sia nel modello di distribuzione classica che nel modello di distribuzione Resource Manager.


## <a name="controlling-access-to-the-classic-deployment-model"></a>Controllo dell'accesso al modello di distribuzione classica

È possibile abilitare un unico circuito ExpressRoute per il collegamento alle reti virtuali in entrambi i modelli di distribuzione impostando il parametro **allowClassicOperations** del circuito ExpressRoute.

Impostando **allowClassicOperations** su TRUE è possibile collegare le reti virtuali da entrambi i modelli di distribuzione al circuito ExpressRoute. Per creare i collegamenti alle reti virtuali nel modello di distribuzione classica, seguire le istruzioni su [come collegare le reti virtuali nel modello di distribuzione classica](expressroute-howto-linkvnet-classic.md). Per creare i collegamenti alle reti virtuali nel modello di distribuzione Resource Manager, seguire le istruzioni su [come collegare le reti virtuali nel modello di distribuzione Resource Manager](expressroute-howto-linkvnet-arm.md).

Impostando **allowClassicOperations** su FALSE viene bloccato l'accesso al circuito dal modello di distribuzione classica. Tuttavia, tutti i collegamenti alle reti virtuali nel modello di distribuzione classica vengono conservati. In tal caso il circuito ExpressRoute non è visibile nel modello di distribuzione classica.

## <a name="supported-operations-in-the-classic-deployment-model"></a>Operazioni supportate nel modello di distribuzione classica

Le operazioni classiche seguenti sono supportate in un circuito ExpressRoute quando **allowClassicOperations** è impostato su TRUE:

 - Ottenere informazioni sul circuito ExpressRoute.
 - Creare, aggiornare, ottenere o eliminare collegamenti alle reti virtuali classiche.
 - Creare, aggiornare, ottenere o eliminare autorizzazioni dei collegamenti alle reti virtuali per la connettività tra sottoscrizioni.

Non è possibile eseguire le operazioni classiche seguenti quando **allowClassicOperations** è impostato su TRUE:

 - Creare, aggiornare, ottenere o eliminare peering BGP (Border Gateway Protocol) per peering pubblici e privati di Azure e peering Microsoft.
 - Eliminare circuiti ExpressRoute.

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>Comunicazione tra i modelli di distribuzione Resource Manager e classica

Il circuito ExpressRoute fa da bridge tra il modello di distribuzione classica e il modello di distribuzione Resource Manager. Il traffico tra le macchine virtuali nelle reti virtuali del modello di distribuzione classica e quelle nelle reti virtuali del modello di distribuzione Resource Manager passa attraverso ExpressRoute se entrambe le reti virtuali sono collegate allo stesso circuito ExpressRoute.

La velocità effettiva aggregata è limitata dalla capacità di velocità effettiva del gateway di rete virtuale. In questi casi il traffico non ha accesso alle reti dell'utente o del provider di connettività. Il flusso del traffico tra le reti virtuali è interamente contenuto nella rete Microsoft.

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>Accesso alle risorse di peering Microsoft e peering pubblico di Azure

È possibile continuare ad accedere alle risorse normalmente accessibili con il peering pubblico di Azure e il peering Microsoft senza interruzioni.  

## <a name="what's-supported"></a>Attività supportate

Questa sezione descrive le attività supportate per i circuiti ExpressRoute:

 - Per accedere alle reti virtuali distribuite nei modelli di distribuzione Resource Manager o classica, è possibile usare un solo circuito ExpressRoute.
 - È possibile spostare un circuito ExpressRoute dal modello di distribuzione classica al modello di distribuzione Resource Manager. Dopo lo spostamento, il circuito ExpressRoute ha lo stesso aspetto e le stesse prestazioni di qualsiasi altro circuito ExpressRoute creato nel modello di distribuzione Resource Manager.
 - Solo il circuito ExpressRoute può essere spostato. I gateway VPN, le reti virtuali e i collegamenti del circuito non possono essere spostati con questa operazione.
 - Dopo aver spostato un circuito ExpressRoute nel modello di distribuzione Resource Manager, il ciclo di vita del circuito ExpressRoute può essere gestito unicamente con il modello di distribuzione Resource Manager. Ciò significa che operazioni come l'aggiunta, l'aggiornamento o l'eliminazione di peering, l'aggiornamento di proprietà dei circuiti, ad esempio la larghezza di banda, lo SKU e il tipo di fatturazione, e l'eliminazione dei circuiti possono essere eseguite solo nel modello di distribuzione Resource Manager.
 - Il circuito ExpressRoute fa da bridge tra il modello di distribuzione classica e il modello di distribuzione Resource Manager. Il traffico tra le macchine virtuali nelle reti virtuali del modello di distribuzione classica e quelle nelle reti virtuali del modello di distribuzione Resource Manager passa attraverso ExpressRoute se entrambe le reti virtuali sono collegate allo stesso circuito ExpressRoute.
 - La connettività tra sottoscrizioni è supportata sia nel modello di distribuzione classica che nel modello di distribuzione Resource Manager.

## <a name="what's-not-supported"></a>Attività non supportate

Questa sezione descrive le attività non supportate per i circuiti ExpressRoute:

 - Spostamento delle reti virtuali, dei gateway e dei collegamenti del circuito dal modello di distribuzione classica al modello di distribuzione Resource Manager.
 - Gestione del ciclo di vita di un circuito ExpressRoute dal modello di distribuzione classica.
 - Supporto del Controllo degli accessi in base al ruolo per il modello di distribuzione classica. Non è possibile eseguire il Controllo degli accessi in base al ruolo per un circuito nel modello di distribuzione classica. Qualsiasi amministratore o coamministratore della sottoscrizione può collegare o scollegare le reti virtuali dal circuito.

## <a name="configuration"></a>Configurazione

Vedere le istruzioni riportate nell'articolo [Spostamento dei circuiti ExpressRoute dal modello di distribuzione classica al modello di distribuzione Resource Manager](expressroute-howto-move-arm.md).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sul flusso di lavoro, vedere [Flussi di lavoro e stati di provisioning di un circuito ExpressRoute](expressroute-workflows.md).
- Per configurare la connessione ExpressRoute:

    - [Creare un circuito ExpressRoute](expressroute-howto-circuit-arm.md)
    - [Configurare il routing](expressroute-howto-routing-arm.md)
    - [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)



<!--HONumber=Oct16_HO2-->


