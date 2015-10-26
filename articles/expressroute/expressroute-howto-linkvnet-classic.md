<properties 
   pageTitle="Collegamento di reti virtuali a circuiti ExpressRoute | Microsoft Azure"
   description="Questo documento fornisce una panoramica delle operazioni per il collegamento di reti virtuali (VNet) a circuiti ExpressRoute."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/21/2015"
   ms.author="cherylmc" />

# Collegamento di reti virtuali a circuiti ExpressRoute

Questo articolo fornisce una panoramica delle operazioni per il collegamento di reti virtuali (VNet) a circuiti ExpressRoute. Le reti virtuali possono trovarsi nella stessa sottoscrizione o appartenere a un'altra sottoscrizione. Questo articolo si applica alle reti virtuali distribuite mediante il modello di distribuzione classico.

>[AZURE.IMPORTANT]Prima di creare una rete virtuale, è importante comprendere che Azure attualmente funziona con due modelli di distribuzione: Gestione risorse e Classico. Prima di iniziare la configurazione, assicurarsi di comprendere i modelli di distribuzione e gli strumenti. Per informazioni sui modelli di distribuzione, vedere [Modelli di distribuzione di Azure](../azure-classic-rm.md)

## Prerequisiti di configurazione

- Sarà necessario scaricare la versione più recente dei moduli di Azure PowerShell. È possibile scaricare il modulo PowerShell più recente dalla sezione relativa a PowerShell della [pagina dei download di Azure](http://azure.microsoft.com/downloads). Per istruzioni dettagliate su come configurare il computer per l'uso dei moduli Azure PowerShell, seguire le istruzioni contenute nella pagina [Come installare e configurare Azure PowerShell](../powershell-install-configure.md). 
- Prima di iniziare la configurazione, assicurarsi di aver letto le pagine relative ai [prerequisiti](expressroute-prerequisites.md), ai [requisiti per il routing](expressroute-routing.md) e ai [flussi di lavoro](expressroute-workflows.md)
- È necessario disporre di un circuito ExpressRoute attivo. 
	- Seguire le istruzioni per [creare un circuito ExpressRoute](expressroute-howto-circuit-classic.md) e fare in modo che venga abilitato dal provider di connettività. 
	- Assicurarsi di disporre del peering privato di Azure configurato per il circuito. Vedere l'articolo relativo alla [configurazione del routing](expressroute-howto-routing-classic.md) per istruzioni relative al routing. 
	- Per abilitare la connettività end-to-end deve essere configurato il peering privato di Azure e deve essere attivo il peering BGP tra la rete e Microsoft.

È possibile collegare fino a 10 reti virtuali a un circuito ExpressRoute. Tutti i circuiti ExpressRoute devono trovarsi nella stessa area geopolitica. È possibile collegare numerose reti virtuali al circuito ExpressRoute se è stato abilitato il componente aggiuntivo ExpressRoute Premium. Per altre informazioni sul componente aggiuntivo Premium, vedere le [domande frequenti](expressroute-faqs.md).

## Collegamento di una rete virtuale della stessa sottoscrizione di Azure a un circuito ExpressRoute

Yon possibile collegare una rete virtuale a un circuito ExpressRoute usando il cmdlet seguente. Prima di eseguire il cmdlet, assicurarsi che il gateway di rete virtuale sia stato creato e sia pronto per il collegamento.

	C:\> New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
	Provisioned

## Collegamento di una rete virtuale di una sottoscrizione di Azure diversa a un circuito ExpressRoute

Un circuito ExpressRoute può essere condiviso tra più sottoscrizioni. La figura seguente mostra un semplice schema del funzionamento della condivisione di circuiti ExpressRoute tra più sottoscrizioni. Ognuno dei cloud più piccoli nel cloud di grandi dimensioni viene usato per rappresentare le sottoscrizioni appartenenti a reparti diversi di un'organizzazione. Ogni reparto dell'organizzazione può usare la propria sottoscrizione per distribuire i servizi, ma può condividere un singolo circuito ExpressRoute per la connessione alla rete locale. Un singolo reparto (in questo esempio, IT) può possedere il circuito ExpressRoute. Altre sottoscrizioni all'interno dell'organizzazione possono usare il circuito ExpressRoute.

>[AZURE.NOTE]I costi relativi a connettività e larghezza di banda per il circuito dedicato saranno addebitati al proprietario del circuito ExpressRoute. Tutte le reti virtuali condividono la stessa larghezza di banda.

![Connettività tra sottoscrizioni](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### Amministrazione

Il *proprietario del circuito* è l'amministratore o il coamministratore della sottoscrizione in cui viene creato il circuito ExpressRoute. Il proprietario del circuito può autorizzare gli amministratori o i coamministratori di altre sottoscrizioni (indicati come *utenti del circuito* nel diagramma del flusso di lavoro) a usare il proprio circuito dedicato. Gli utenti del circuito autorizzati a usare il circuito ExpressRoute dell'organizzazione possono collegare la rete virtuale della propria sottoscrizione al circuito ExpressRoute dopo aver ricevuto l'autorizzazione.

Il proprietario del circuito ha la facoltà di modificare e revocare le autorizzazioni in qualsiasi momento. La revoca dell'autorizzazione comporterà l'eliminazione di tutti i collegamenti dalla sottoscrizione di cui è stato revocato l'accesso.

### Operazioni del proprietario del circuito 

#### Creazione di un'autorizzazione
	
Il proprietario del circuito autorizza gli amministratori di altre sottoscrizioni a usare il circuito specificato. Nell'esempio seguente l'amministratore del circuito (Contoso IT) abilita l'amministratore di un'altra sottoscrizione (Dev-Test), specificando il relativo ID Microsoft, per collegare fino a due reti virtuali al circuito. Il cmdlet non invia messaggi di posta elettronica all'ID di Microsoft specificato. È necessario che il proprietario del circuito informi in modo esplicito il proprietario dell'altra sottoscrizione che l'autorizzazione è stata completata.

		PS C:\> New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
		
		Description         : Dev-Test Links 
		Limit               : 2 
		LinkAuthorizationId : ********************************** 
		MicrosoftIds        : devtest@contoso.com 
		Used                : 0

#### Verifica delle autorizzazioni

Il proprietario del circuito può esaminare tutte le autorizzazioni rilasciate in un particolare circuito eseguendo il cmdlet seguente.

	PS C:\> Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
	
	Description         : EngineeringTeam 
	Limit               : 3 
	LinkAuthorizationId : #################################### 
	MicrosoftIds        : engadmin@contoso.com 
	Used                : 1 
	
	Description         : MarketingTeam 
	Limit               : 1 
	LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ 
	MicrosoftIds        : marketingadmin@contoso.com 
	Used                : 0 
	
	Description         : Dev-Test Links 
	Limit               : 2 
	LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
	MicrosoftIds        : salesadmin@contoso.com 
	Used                : 2 
	

#### Aggiornamento delle autorizzazioni

Il proprietario del circuito può modificare le autorizzazioni usando il cmdlet seguente.

	PS C:\> set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
		
	Description         : Dev-Test Links 
	Limit               : 5 
	LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
	MicrosoftIds        : devtest@contoso.com 
	Used                : 0


#### Eliminazione delle autorizzazioni

Il proprietario del circuito può revocare o eliminare le autorizzazioni usando il cmdlet seguente.

	PS C:\> Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### Operazioni dell'utente del circuito

#### Verifica delle autorizzazioni

L'utente del circuito può esaminare le autorizzazioni usando il cmdlet seguente.

	PS C:\> Get-AzureAuthorizedDedicatedCircuit
		
	Bandwidth                        : 200
	CircuitName                      : ContosoIT
	Location                         : Washington DC
	MaximumAllowedLinks              : 2
	ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
	ServiceProviderName              : equinix
	ServiceProviderProvisioningState : Provisioned
	Status                           : Enabled
	UsedLinks                        : 0

#### Riscatto delle autorizzazioni dei collegamenti

L'utente del circuito può eseguire il cmdlet seguente per riscattare un'autorizzazione di collegamento.

	PS C:\> New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1' 
		
	State VnetName 
	----- -------- 
	Provisioned SalesVNET1

## Passaggi successivi

Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).

<!---HONumber=Oct15_HO3-->