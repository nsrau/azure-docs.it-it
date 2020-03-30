---
title: 'Azure ExpressRoute: flussi di lavoro di configurazione dei circuitiAzure ExpressRoute: Circuit configuration workflows'
description: Questa pagina illustra i flussi di lavoro per la configurazione di un circuito ExpressRoute e dei peering
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.openlocfilehash: e833e20085d7cfd8f727acb394851e96e7e19368
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864367"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Flussi di lavoro ExpressRoute per provisioning di un circuito e stati di circuito
Questa pagina illustra i flussi di lavoro di provisioning del servizio e configurazione del routing a livello generale.

![flusso di lavoro del circuito](./media/expressroute-workflows/expressroute-circuit-workflow.png)

La figura seguente e i passaggi corrispondenti illustrano le attività per il provisioning di un circuito ExpressRoute end-to-end. 

1. Usare PowerShell per configurare un circuito ExpressRoute. Per altre informazioni, seguire le istruzioni contenute nell'articolo relativo alla [creazione di circuiti ExpressRoute](expressroute-howto-circuit-classic.md) .
2. Ordinare la connettività dal provider di connettività. Questo processo varia. Per altre informazioni su come ordinare la connettività, contattare il provider di connettività.
3. Assicurarsi che il provisioning del circuito sia stato eseguito correttamente verificando lo stato di provisioning del circuito ExpressRoute tramite PowerShell. 
4. Configurare i domini di routing. Se il provider di connettività gestisce la configurazione di livello 3, configurerà il routing per il circuito. Se il provider di connettività offre solo servizi di livello 2, è necessario configurare il routing in base alle linee guida descritte nelle pagine di configurazione dei requisiti di [routing](expressroute-routing.md) e del [routing.](expressroute-howto-routing-classic.md)
   
   * Abilitare il peering privato di Azure: abilitare questo peering per la connessione alle macchine virtuali e ai servizi cloud distribuiti nelle reti virtuali.

   * Abilita peering Microsoft: abilitare questa opzione per accedere ai servizi online Microsoft, ad esempio Office 365.Enable Microsoft peering - Enable this to access Microsoft online services, such as Office 365. Tutti i servizi PaaS di Azure sono accessibili tramite il peering Microsoft.
     
     > [!IMPORTANT]
     > Per la connessione a Microsoft è necessario usare un server proxy/perimetrale separato rispetto a quello usato per Internet. Se si usa lo stesso server perimetrale per ExpressRoute e Internet, si verificherà un routing asimmetrico con interruzioni della connettività di rete.
     > 
     > 
     
     ![routing del flusso di lavoro](./media/expressroute-workflows/routing-workflow.png)
5. Collegamento di reti virtuali a circuiti ExpressRoute: è possibile collegare reti virtuali al circuito ExpressRoute. Seguire le istruzioni [per collegare reti virtuali](expressroute-howto-linkvnet-arm.md) a un circuito. Queste reti virtuali possono trovarsi nella stessa sottoscrizione di Azure del circuito ExpressRoute oppure in una sottoscrizione diversa.

## <a name="expressroute-circuit-provisioning-states"></a>Stati di provisioning del circuito ExpressRoute
Ogni circuito ExpressRoute prevede due stati:

* Stato di provisioning del provider di servizi
* Stato

Status rappresenta lo stato di provisioning di Microsoft. Questa proprietà è impostata su Enabled quando si crea un circuito Expressroute

Lo stato di provisioning del provider di connettività rappresenta lo stato sul lato del provider di connettività. Può essere impostato su *NotProvisioned*, *Provisioning* o *Provisioned*. Il circuito ExpressRoute deve essere in uno stato di provisioning per configurare il peering.

### <a name="possible-states-of-an-expressroute-circuit"></a>Stati possibili di un circuito ExpressRoute
In questa sezione vengono elencati i possibili stati di un circuito ExpressRoute.This section lists the possible states of an ExpressRoute circuit.

**Al momento della creazione**

Il circuito ExpressRoute segnalerà gli stati seguenti al momento della creazione delle risorse.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Quando il provider di connettività è in fase di provisioning del circuito**

Il circuito ExpressRoute segnalerà gli stati seguenti mentre il provider di connettività sta lavorando per eseguire il provisioning del circuito.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Quando il provider di connettività ha completato il processo di provisioning**

Il circuito ExpressRoute segnalerà gli stati seguenti dopo che il provider di connettività ha eseguito correttamente il provisioning del circuito.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


**Quando il provider di connettività sta eseguendo il deprovisioning del circuito**

Se è necessario eseguire il deprovisioning del circuito ExpressRoute, segnalando gli stati seguenti dopo che il provider di servizi ha completato il processo di deprovisioning.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


È possibile scegliere di abilitarlo nuovamente, se necessario, o eseguire i cmdlet di PowerShell per eliminare il circuito.  

> [!IMPORTANT]
> Un circuito non può essere eliminato quando ServiceProviderProvisioningState è Provisioning o Provisioning. Il provider di connettività deve eseguire il deprovisioning del circuito prima di poterlo eliminare. Microsoft will continue to bill the circuit until the ExpressRoute circuit resource is deleted in Azure.
> 

## <a name="routing-session-configuration-state"></a>Stato di configurazione della sessione di routing
Lo stato di provisioning BGP segnala se la sessione BGP è stata abilitata sul bordo Microsoft. Lo stato deve essere abilitato per l'utilizzo del peering privato o Microsoft.The state must be enabled to use private or Microsoft peering.

È importante controllare lo stato della sessione BGP soprattutto per il peering Microsoft. Oltre allo stato di provisioning BGP, esiste un altro stato denominato *prefissi pubblici annunciati*. Lo stato dei prefissi pubblici annunciati deve essere nello stato *configurato,* sia per la sessione BGP sia per il routing end-to-end. 

Se invece è impostato lo stato *validation needed* , la sessione BGP non è abilitata, poiché i prefissi annunciati non corrispondono al numero AS nei registri di routing. 

> [!IMPORTANT]
> Se lo stato dei prefissi pubblici annunciati è in stato di *convalida manuale,* è necessario aprire un ticket di supporto con [supporto Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e fornire la prova che si è proprietari degli indirizzi IP annunciati insieme al numero di sistema autonomo associato.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
* Configurare la connessione ExpressRoute.
  
  * [Creare un circuito ExpressRouteCreate an ExpressRoute circuit](expressroute-howto-circuit-arm.md)
  * [Configurare il routing](expressroute-howto-routing-arm.md)
  * [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)

