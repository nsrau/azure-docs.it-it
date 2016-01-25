<properties 
   pageTitle="Collegamento di reti virtuali a circuiti ExpressRoute | Microsoft Azure"
   description="Questo documento fornisce una panoramica delle operazioni per il collegamento di reti virtuali (VNet) a circuiti ExpressRoute."
   services="expressroute"
   documentationCenter="na"
   authors="ganesr"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/07/2015"
   ms.author="ganesr" />

# Collegamento di reti virtuali a circuiti ExpressRoute

> [AZURE.SELECTOR]
- [PowerShell - Classic](expressroute-howto-linkvnet-classic.md)
- [PowerShell - Resource Manager] (expressroute-howto-linkvnet-arm.md)
- [Template - Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection)

Questo articolo fornisce una panoramica delle operazioni per il collegamento di reti virtuali (VNet) a circuiti ExpressRoute. Le reti virtuali possono trovarsi nella stessa sottoscrizione o appartenere a un'altra sottoscrizione. Questo articolo si applica alle reti virtuali distribuite mediante il modello di distribuzione Gestione risorse. Se si desidera collegare una rete virtuale che è stata distribuita con il modello di distribuzione classico, vedere [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-classic.md).

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## Prerequisiti di configurazione

- Sarà necessario scaricare la versione più recente dei moduli di Azure PowerShell, la versione 1.0 o quelle successive. 
- Prima di procedere con la configurazione, leggere la pagina [prerequisiti](expressroute-prerequisites.md), la pagina [requisiti di routing](expressroute-routing.md) e la pagina [flussi di lavoro](expressroute-workflows.md).
- È necessario avere un circuito ExpressRoute attivo. Seguire le istruzioni per [creare un circuito ExpressRoute](expressroute-howto-circuit-classic.md) e fare in modo che venga abilitato dal provider di connettività prima di procedere. Per poter eseguire i cmdlet descritti di seguito deve essere stato effettuato il provisioning del circuito ExpressRoute e lo stato del circuito deve essere abilitato.
- È necessario avere un circuito ExpressRoute attivo. 
	- Seguire le istruzioni per [creare un circuito ExpressRoute](expressroute-howto-circuit-arm.md) e fare in modo che venga abilitato dal provider di connettività. 
	- Assicurarsi di disporre del peering privato di Azure configurato per il circuito. Vedere l'articolo relativo alla [configurazione del routing](expressroute-howto-routing-arm.md) per istruzioni relative al routing. 
	- Per abilitare la connettività end-to-end deve essere configurato il peering privato di Azure e deve essere attivo il peering BGP tra la rete e Microsoft.
	- È necessario disporre di una rete virtuale e di un gateway di rete virtuale creati e con provisioning completo. Seguire le istruzioni per creare un [gateway vpn](../articles/vpn-gateway-create-site-to-site-rm-powershell.md)

È possibile collegare fino a 10 reti virtuali a un circuito ExpressRoute. Tutti i circuiti ExpressRoute devono trovarsi nella stessa area geopolitica. È possibile collegare numerose reti virtuali al circuito ExpressRoute se è stato abilitato il componente aggiuntivo ExpressRoute Premium. Per altre informazioni sul componente aggiuntivo Premium, vedere le [domande frequenti](expressroute-faqs.md).

## Collegamento di una rete virtuale della stessa sottoscrizione di Azure a un circuito ExpressRoute

È possibile collegare un gateway di rete virtuale a un circuito ExpressRoute usando il cmdlet seguente. Prima di eseguire il cmdlet, assicurarsi che il gateway di rete virtuale sia stato creato e sia pronto per il collegamento.

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
	$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute

## Collegare una rete virtuale di una sottoscrizione di Azure diversa a un circuito ExpressRoute

Un circuito ExpressRoute può essere condiviso tra più sottoscrizioni. La figura seguente mostra un semplice schema del funzionamento della condivisione di circuiti ExpressRoute tra più sottoscrizioni. Ognuno dei cloud più piccoli nel cloud di grandi dimensioni viene usato per rappresentare le sottoscrizioni appartenenti a reparti diversi di un'organizzazione. Ogni reparto dell'organizzazione può usare la propria sottoscrizione per distribuire i servizi, ma può condividere un singolo circuito ExpressRoute per la connessione alla rete locale. Un singolo reparto (in questo esempio, IT) può possedere il circuito ExpressRoute. Altre sottoscrizioni all'interno dell'organizzazione possono usare il circuito ExpressRoute.

>[AZURE.NOTE]I costi relativi a connettività e larghezza di banda per il circuito dedicato saranno addebitati al proprietario del circuito ExpressRoute. Tutte le reti virtuali condividono la stessa larghezza di banda.

![Connettività tra sottoscrizioni](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### Amministrazione

Il *proprietario del circuito* è il power user autorizzato della risorsa circuito ExpressRoute. Il proprietario del circuito può creare le autorizzazioni che possono essere riscattate dagli *utenti del circuito*. *Gli utenti del circuito* sono i proprietari dei gateway di rete virtuale (che non sono nella stessa sottoscrizione del circuito ExpressRoute). *Gli utenti del circuito* possono riscattare le autorizzazioni (un'autorizzazione per ogni rete virtuale.

Il *proprietario del circuito* ha la facoltà di modificare e revocare le autorizzazioni in qualsiasi momento. La revoca dell'autorizzazione comporterà l'eliminazione di tutti i collegamenti dalla sottoscrizione di cui è stato revocato l'accesso.

### Operazioni del proprietario del circuito 

#### Creazione di un'autorizzazione
	
Il proprietario del circuito crea un'autorizzazione. Questo comporta la creazione di una chiave di autorizzazione che può essere utilizzata da un utente del circuito per connettere i gateway di rete virtuale al circuito ExpressRoute. Un'autorizzazione è valida per una sola connessione.

Il frammento di cmdlet riportato di seguito mostra come creare un'autorizzazione.

		Add-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit -Name "MyAuthorization1"
		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
		$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"

		$auth1 = Get-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit -Name "MyAuthorization1"
		

La risposta conterrà la chiave di autorizzazione e lo stato

		Name                   : MyAuthorization1
		Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
		Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
		AuthorizationKey       : ####################################
		AuthorizationUseStatus : Available
		ProvisioningState      : Succeeded

		

#### Verifica delle autorizzazioni

Il proprietario del circuito può esaminare tutte le autorizzazioni rilasciate in un particolare circuito eseguendo il cmdlet seguente.

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit
	

#### Aggiunta di autorizzazioni

Il proprietario del circuito può aggiungere le autorizzazioni usando il cmdlet seguente.

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	Add-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit -Name "MyAuthorization2"
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
	
	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit

	
#### Eliminazione delle autorizzazioni

Il proprietario del circuito può revocare o eliminare le autorizzazioni usando il cmdlet seguente.

	Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -Circuit $circuit
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit	

### Operazioni dell'utente del circuito

L’utente del circuito deve richiedere l’ID peer e una chiave di autorizzazione al proprietario del circuito. La chiave del circuito è simile a quella illustrata di seguito:


La chiave di autorizzazione è un GUID.

#### Riscatto delle autorizzazioni di collegamento

L'utente del circuito può eseguire il cmdlet seguente per riscattare un'autorizzazione di collegamento.

	$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"	
	$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"

#### Rilascio delle autorizzazioni di collegamento

È possibile rilasciare un'autorizzazione eliminando il collegamento del circuito ExpressRoute alla rete virtuale.

## Passaggi successivi

Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).

<!---HONumber=AcomDC_0114_2016-->