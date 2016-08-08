<properties 
   pageTitle="Collegamento di una rete virtuale a un circuito ExpressRoute usando PowerShell | Microsoft Azure"
   description="Questo documento offre una panoramica sulle procedure di collegamento delle reti virtuali ai circuiti ExpressRoute usando il modello di distribuzione di Resource Manager e PowerShell."
   services="expressroute"
   documentationCenter="na"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/09/2016"
   ms.author="ganesr" />

# Collegare una rete virtuale a un circuito ExpressRoute

> [AZURE.SELECTOR]
- [Portale di Azure - Gestione risorse](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell - Gestione risorse](expressroute-howto-linkvnet-arm.md)
- [PowerShell - Classico](expressroute-howto-linkvnet-classic.md)


Questo articolo spiega come collegare le reti virtuali ai circuiti di Azure ExpressRoute usando il modello di distribuzione di Resource Manager e PowerShell. Le reti virtuali possono essere nella stessa sottoscrizione o appartenere a un'altra sottoscrizione.

**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Prerequisiti di configurazione

- È necessaria la versione più recente dei moduli di Azure PowerShell (almeno la versione 1.0). Per altre informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).
- Prima di procedere con la configurazione, è necessario verificare i [prerequisiti](expressroute-prerequisites.md), i [requisiti di routing](expressroute-routing.md) e i [flussi di lavoro](expressroute-workflows.md).
- È necessario avere un circuito ExpressRoute attivo.
	- Seguire le istruzioni per [creare un circuito ExpressRoute](expressroute-howto-circuit-arm.md) e fare in modo che venga abilitato dal provider di connettività.
	- Assicurarsi di disporre del peering privato di Azure configurato per il circuito. Vedere l'articolo relativo alla [configurazione del routing](expressroute-howto-routing-arm.md) per istruzioni relative al routing.
	- Per abilitare la connettività end-to-end, assicurarsi che sia configurato il peering privato di Azure e sia attivo il peering BGP tra la rete e Microsoft.
	- Assicurarsi di avere una rete virtuale e un gateway di rete virtuale creati e con provisioning completo. Seguire le istruzioni relative alla creazione di un [gateway VPN](../articles/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) e assicurarsi di usare `-GatewayType ExpressRoute`.

È possibile collegare fino a 10 reti virtuali a un circuito ExpressRoute. Tutte le reti virtuali devono trovarsi nella stessa area geopolitica. È possibile collegare un numero maggiore di reti virtuali al circuito ExpressRoute o collegare reti virtuali che non sono presenti in altre aree geopolitiche se è stato abilitato il componente aggiuntivo ExpressRoute Premium. Per altre informazioni sul componente aggiuntivo Premium, vedere le [domande frequenti](expressroute-faqs.md).

## Collegare una rete virtuale della stessa sottoscrizione a un circuito

È possibile collegare un gateway di rete virtuale a un circuito ExpressRoute usando il cmdlet seguente. Prima di eseguire il cmdlet, assicurarsi che il gateway di rete virtuale sia stato creato e sia pronto per il collegamento.

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
	$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute

## Collegare una rete virtuale di un'altra sottoscrizione a un circuito

È possibile condividere un circuito ExpressRoute tra più sottoscrizioni. La figura seguente mostra un semplice schema del funzionamento della condivisione di circuiti ExpressRoute tra più sottoscrizioni.

Ciascuno dei cloud più piccoli nel cloud di grandi dimensioni viene usato per rappresentare le sottoscrizioni appartenenti a reparti diversi di un'organizzazione. Ciascun reparto dell'organizzazione può usare la propria sottoscrizione per distribuire i servizi, ma i reparti possono condividere un singolo circuito ExpressRoute per la connessione alla rete locale. Un singolo reparto (in questo esempio, IT) può possedere il circuito ExpressRoute. Altre sottoscrizioni all'interno dell'organizzazione possono usare il circuito ExpressRoute.

>[AZURE.NOTE] I costi relativi a connettività e larghezza di banda per il circuito dedicato saranno addebitati al proprietario del circuito ExpressRoute. Tutte le reti virtuali condividono la stessa larghezza di banda.

![Connettività tra sottoscrizioni](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### Amministrazione

Il *proprietario del circuito* è l'utente esperto autorizzato della risorsa circuito ExpressRoute. Il proprietario del circuito può creare le autorizzazioni che possono essere riscattate dagli *utenti del circuito*. *Gli utenti del circuito* sono i proprietari dei gateway di rete virtuale (che non sono nella stessa sottoscrizione del circuito ExpressRoute). *Gli utenti del circuito* possono riscattare le autorizzazioni (un'autorizzazione per ogni rete virtuale).

Il *proprietario del circuito* ha la facoltà di modificare e revocare le autorizzazioni in qualsiasi momento. La revoca dell'autorizzazione comporterà l'eliminazione di tutti i collegamenti dalla sottoscrizione di cui è stato revocato l'accesso.

### Operazioni del proprietario del circuito 

#### Creazione di un'autorizzazione
	
Il proprietario del circuito crea un'autorizzazione. Questo comporta la creazione di una chiave di autorizzazione che può essere utilizzata da un utente del circuito per connettere i gateway di rete virtuale al circuito ExpressRoute. Un'autorizzazione è valida per una sola connessione.

Il frammento di cmdlet seguente mostra come creare un'autorizzazione:

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

	$auth1 = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
		

La risposta conterrà la chiave di autorizzazione e lo stato:

	Name                   : MyAuthorization1
	Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
	Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
	AuthorizationKey       : ####################################
	AuthorizationUseStatus : Available
	ProvisioningState      : Succeeded

		

#### Verifica delle autorizzazioni

Il proprietario del circuito può esaminare tutte le autorizzazioni rilasciate in un particolare circuito eseguendo il cmdlet seguente:

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
	

#### Aggiunta di autorizzazioni

Il proprietario del circuito può aggiungere le autorizzazioni usando il cmdlet seguente:

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
	
	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit

	
#### Eliminazione delle autorizzazioni

Il proprietario del circuito può revocare o eliminare le autorizzazioni dell'utente eseguendo il cmdlet seguente:

	Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit	

### Operazioni dell'utente del circuito

L’utente del circuito deve richiedere l’ID peer e una chiave di autorizzazione al proprietario del circuito. La chiave di autorizzazione è un GUID.

#### Riscatto delle autorizzazioni di collegamento

L'utente del circuito può eseguire il cmdlet seguente per riscattare un'autorizzazione di collegamento:

	$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"	
	$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"

#### Rilascio delle autorizzazioni di collegamento

È possibile rilasciare un'autorizzazione eliminando il collegamento del circuito ExpressRoute alla rete virtuale.

## Passaggi successivi

Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).

<!---HONumber=AcomDC_0727_2016-->