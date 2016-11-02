<properties
   pageTitle="Flussi di lavoro per la configurazione di un circuito ExpressRoute | Microsoft Azure"
   description="Questa pagina illustra i flussi di lavoro per la configurazione di un circuito ExpressRoute e dei peering"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>


# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Flussi di lavoro ExpressRoute per provisioning di un circuito e stati di circuito

Questa pagina illustra i flussi di lavoro di provisioning del servizio e configurazione del routing a livello generale.

![](./media/expressroute-workflows/expressroute-circuit-workflow.png)

La figura e i passaggi corrispondenti riportati di seguito descrivono le attività per il provisioning end-to-end di un circuito ExpressRoute. 

1. Usare PowerShell per configurare un circuito ExpressRoute. Per altre informazioni, seguire le istruzioni contenute nell'articolo relativo alla [creazione di circuiti ExpressRoute](expressroute-howto-circuit-classic.md) .

2. Ordinare la connettività dal provider di connettività. Questo processo varia. Per altre informazioni su come ordinare la connettività, contattare il provider di connettività.

3. Assicurarsi che il provisioning del circuito sia stato eseguito correttamente verificando lo stato di provisioning del circuito ExpressRoute tramite PowerShell. 

4. Configurare i domini di routing. Se il provider di connettività gestisce il livello 3, configurerà il routing per il circuito. Se invece il provider di connettività offre solo servizi di livello 2, sarà necessario configurare il routing secondo le linee guida descritte nelle pagine relative ai [requisiti di routing](expressroute-routing.md) e alla [configurazione del routing](expressroute-howto-routing-classic.md).

    -  Abilitare il peering privato di Azure: è necessario abilitare questo peering per la connessione alle macchine virtuali e ai servizi cloud distribuiti nelle reti virtuali.
    -  Abilitare il peering pubblico di Azure: è necessario abilitare questo peering se si desidera connettersi ai servizi di Azure ospitati in indirizzi IP pubblici. Questo è un requisito per accedere alle risorse di Azure se si è scelto di abilitare il routing predefinito per il peering privato di Azure.
    -  Abilitare il peering Microsoft: è necessario abilitare questo peering per accedere ai servizi Office 365 e CRM Online. 
    
    >[AZURE.IMPORTANT] Per la connessione a Microsoft è necessario usare un server proxy/perimetrale separato rispetto a quello usato per Internet. Se si usa lo stesso server perimetrale per ExpressRoute e Internet, si verificherà un routing asimmetrico con interruzioni della connettività di rete.

    ![](./media/expressroute-workflows/routing-workflow.png)


5. Collegamento di reti virtuali a circuiti ExpressRoute: è possibile collegare reti virtuali al circuito ExpressRoute. Seguire le istruzioni [per collegare reti virtuali](expressroute-howto-linkvnet-arm.md) a un circuito. Queste reti virtuali possono trovarsi nella stessa sottoscrizione di Azure del circuito ExpressRoute oppure in una sottoscrizione diversa.


## <a name="expressroute-circuit-provisioning-states"></a>Stati di provisioning del circuito ExpressRoute

Ogni circuito ExpressRoute prevede due stati:

- Stato di provisioning del provider di servizi
- Stato

Status rappresenta lo stato di provisioning di Microsoft. Questa proprietà è impostata su Enabled quando si crea un circuito Expressroute

Lo stato di provisioning del provider di connettività rappresenta lo stato sul lato del provider di connettività. Può essere impostato su *NotProvisioned*, *Provisioning* o *Provisioned*. Il circuito ExpressRoute può essere usato solo se è impostato sullo stato Provisioned.

### <a name="possible-states-of-an-expressroute-circuit"></a>Stati possibili di un circuito ExpressRoute

Questa sezione elenca gli stati possibili di un circuito ExpressRoute.

#### <a name="at-creation-time"></a>Al momento della creazione

Lo stato del circuito ExpressRoute sarà il seguente nel momento in cui si esegue il cmdlet di PowerShell per creare il circuito ExpressRoute.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


#### <a name="when-connectivity-provider-is-in-the-process-of-provisioning-the-circuit"></a>Quando il provider di connettività è in fase di provisioning del circuito

Lo stato del circuito ExpressRoute sarà il seguente quando si passa la chiave di servizio al provider di connettività ed è stato avviato il processo di provisioning.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


#### <a name="when-connectivity-provider-has-completed-the-provisioning-process"></a>Quando il provider di connettività ha completato il processo di provisioning

Lo stato del circuito ExpressRoute sarà il seguente quando il provider di connettività ha completato il processo di provisioning.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled

Provisioned ed Enabled sono gli unici stati che consentono di usare il circuito. Se si usa un provider di livello 2, è possibile configurare il routing per il circuito solo quando è attivo questo stato.

#### <a name="when-connectivity-provider-is-deprovisioning-the-circuit"></a>Quando il provider di connettività sta eseguendo il deprovisioning del circuito

Se si è richiesto al provider di servizi di eseguire il deprovisioning del circuito ExpressRoute, al termine del processo di deprovisioning eseguito dal provider di servizi lo stato del circuito sarà il seguente.


    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


È possibile scegliere di abilitarlo nuovamente, se necessario, o eseguire i cmdlet di PowerShell per eliminare il circuito.  

>[AZURE.IMPORTANT] Se si esegue il cmdlet PowerShell per eliminare il circuito quando lo stato di ServiceProviderProvisioningState è Provisioning in corso o Provisioning eseguito, l'operazione avrà esito negativo. Collaborare con il provider di connettività per eseguire il deprovisioning del circuito ExpressRoute e quindi eliminare il circuito. Microsoft continuerà ad applicare l'addebito per il circuito finché non si esegue il cmdlet di PowerShell per eliminare il circuito.


## <a name="routing-session-configuration-state"></a>Stato di configurazione della sessione di routing

Lo stato di provisioning BGP indica se la sessione BGP è stata abilitata sul lato Microsoft. Per usare il peering è necessario che lo stato sia abilitato.

È importante controllare lo stato della sessione BGP soprattutto per il peering Microsoft. Oltre allo stato di provisioning BGP, esiste un altro stato denominato *prefissi pubblici annunciati*. Per i prefissi pubblici annunciati deve essere impostato lo stato *configured* perché la sessione BGP sia attiva e perché il routing funzioni in modalità end-to-end. 

Se invece è impostato lo stato *validation needed* , la sessione BGP non è abilitata, poiché i prefissi annunciati non corrispondono al numero AS nei registri di routing. 

>[AZURE.IMPORTANT] Se i prefissi pubblici annunciati sono impostati sullo stato *manual validation* , è necessario aprire un ticket di supporto con il [supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e fornire la prova di essere proprietari degli indirizzi IP annunciati insieme con il numero di sistema autonomo (AS) associato.


## <a name="next-steps"></a>Passaggi successivi

- Configurare la connessione ExpressRoute.

    - [Creare un circuito ExpressRoute](expressroute-howto-circuit-arm.md)
    - [Configurare il routing](expressroute-howto-routing-arm.md)
    - [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)



<!--HONumber=Oct16_HO2-->


