<properties
   pageTitle="Flusso di lavoro per la configurazione di un circuito ExpressRoute | Microsoft Azure"
   description="Questa pagina illustra i flussi di lavoro per la configurazione di un circuito ExpressRoute e dei peering"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/06/2015"
   ms.author="cherylmc"/>

# Flussi di lavoro e stati di provisioning di un circuito ExpressRoute
Questa pagina illustra i flussi di lavoro di provisioning del servizio e configurazione del routing a livello generale.

![](./media/expressroute-workflows/expressroute-circuit-workflow.png)

La figura e i passaggi corrispondenti riportati di seguito descrivono le attività da seguire per il provisioning end-to-end di un circuito ExpressRoute.

1. Usare PowerShell per configurare un circuito ExpressRoute. Per altre informazioni, seguire le istruzioni contenute nell'articolo relativo alla [creazione di circuiti ExpressRoute](expressroute-howto-circuit-classic.md).

2. Ordinare la connettività dal provider di connettività. Questo processo varia. Per altre informazioni su come ordinare la connettività, contattare il provider di connettività.

3. Assicurarsi che il provisioning del circuito sia stato eseguito correttamente verificando lo stato di provisioning del circuito ExpressRoute tramite PowerShell.

4. Configurare i domini di routing. Se il provider di connettività gestisce il livello 3, configurerà il routing per il circuito. Se invece il provider di connettività offre solo servizi di livello 2, sarà necessario configurare il routing secondo le linee guida descritte nelle pagine relative ai [requisiti di routing](expressroute-routing.md) e alla [configurazione del routing](expressroute-howto-routing-classic.md).

	-  Abilitare il peering privato di Azure: è necessario abilitare questo peering per la connessione alle macchine virtuali e ai servizi cloud distribuiti nelle reti virtuali.
	-  Abilitare il peering pubblico di Azure: è necessario abilitare questo peering se si desidera connettersi ai servizi di Azure ospitati in indirizzi IP pubblici. Questo è un requisito per accedere alle risorse di Azure se si è scelto di abilitare il routing predefinito per il peering privato di Azure.
	-  Abilitare il peering Microsoft: è necessario abilitare questo peering per accedere ai servizi Office 365 e CRM Online. 
	
	>[AZURE.IMPORTANT]Se si abilita il peering Microsoft, assicurarsi che sia abilitato anche il peering pubblico di Azure per l'accesso ad Azure Active Directory. Per la connessione a Microsoft è necessario usare un server proxy/perimetrale separato rispetto a quello usato per Internet. Se si usa lo stesso server perimetrale per ExpressRoute e Internet, si verificherà un routing asimmetrico con interruzioni della connettività di rete.


	![](./media/expressroute-workflows/expressroute-routing-workflow.png)

5. Collegamento di reti virtuali a circuiti ExpressRoute: è possibile collegare reti virtuali al circuito ExpressRoute. Seguire le istruzioni [per collegare reti virtuali](expressroute-howto-linkvnets-classic.md) a un circuito. Queste reti virtuali possono trovarsi nella stessa sottoscrizione di Azure del circuito ExpressRoute oppure in una sottoscrizione diversa.


## Stati di provisioning del circuito ExpressRoute

Ogni circuito ExpressRoute prevede due stati:

- Stato di provisioning del provider di servizi
- Stato

Status rappresenta lo stato di provisioning di Microsoft. Questa proprietà può essere impostata su uno degli stati seguenti: *Enabled*, *Enabling* o *Disabling*. Il circuito ExpressRoute può essere usato solo se è impostato sullo stato Enabled.

Lo stato di provisioning del provider di connettività rappresenta lo stato sul lato del provider di connettività. Può essere impostato su *NotProvisioned*, *Provisioning* o *Provisioned*. Il circuito ExpressRoute può essere usato solo se è impostato sullo stato Provisioned.

### Stati possibili di un circuito ExpressRoute

Questa sezione elenca gli stati possibili di un circuito ExpressRoute.

#### Al momento della creazione

Lo stato del circuito ExpressRoute sarà il seguente nel momento in cui si esegue il cmdlet di PowerShell per creare il circuito ExpressRoute.

	ServiceProviderProvisioningState : NotProvisioned
	Status                           : Enabled


#### Quando il provider di connettività è in fase di provisioning del circuito

Lo stato del circuito ExpressRoute sarà il seguente quando si passa la chiave di servizio al provider di connettività ed è stato avviato il processo di provisioning.

	ServiceProviderProvisioningState : Provisioning
	Status                           : Enabled


#### Quando il provider di connettività ha completato il processo di provisioning

Lo stato del circuito ExpressRoute sarà il seguente quando il provider di connettività ha completato il processo di provisioning.

	ServiceProviderProvisioningState : Provisioned
	Status                           : Enabled

Provisioned ed Enabled sono gli unici stati che consentono di usare il circuito. Se si usa un provider di livello 2, è possibile configurare il routing per il circuito solo quando è attivo questo stato.

#### Se viene avviato il deprovisioning prima sul lato Microsoft

Lo stato del circuito ExpressRoute sarà il seguente nel momento in cui si esegue il cmdlet di PowerShell per eliminare il circuito ExpressRoute.

	ServiceProviderProvisioningState : Provisioning
	Status                           : Disabling

È necessario contattare il provider di connettività per eseguire il deprovisioning del circuito ExpressRoute. **Importante:** Microsoft continuerà ad applicare l'addebito per il circuito finché non si esegue il cmdlet di PowerShell per il deprovisioning.

#### Se viene avviato il deprovisioning sul lato del provider di servizi

Se si è richiesto prima al provider di servizi di eseguire il deprovisioning del circuito ExpressRoute, lo stato del circuito sarà il seguente al termine del processo di deprovisioning eseguito dal provider di servizi.


	ServiceProviderProvisioningState : NotProvisioned
	Status                           : Enabled

È possibile scegliere di abilitarlo nuovamente, se necessario, o eseguire i cmdlet di PowerShell per eliminare il circuito. **Importante:** Microsoft continuerà ad applicare l'addebito per il circuito finché non si esegue il cmdlet di PowerShell per il deprovisioning.


## Stato di configurazione della sessione di routing

Lo stato di provisioning BGP indica se la sessione BGP è stata abilitata sul lato Microsoft. Per usare il peering è necessario che lo stato sia abilitato.

È importante controllare lo stato della sessione BGP soprattutto per il peering Microsoft. Oltre allo stato di provisioning BGP, esiste un altro stato denominato *prefissi pubblici annunciati*. Per i prefissi pubblici annunciati deve essere impostato lo stato *configured* perché la sessione BGP sia attiva e perché il routing funzioni in modalità end-to-end.

Se invece è impostato lo stato *validation needed*, la sessione BGP non è abilitata, poiché i prefissi annunciati non corrispondono al numero AS nei registri di routing.

>[AZURE.IMPORTANT]Se i prefissi pubblici annunciati sono impostati sullo stato *manual validation*, è necessario aprire un ticket di supporto con il [supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e fornire la prova di essere proprietari degli indirizzi IP annunciati insieme con il numero di sistema autonomo (AS) associato.


## Passaggi successivi

- Configurare la connessione ExpressRoute.

	- [Creare un circuito ExpressRoute](expressroute-howto-circuit-classic.md)
	- [Configurare il routing](expressroute-howto-routing-classic.md)
	- [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-classic.md)

<!---HONumber=Oct15_HO2-->