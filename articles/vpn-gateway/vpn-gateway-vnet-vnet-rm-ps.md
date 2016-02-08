<properties
   pageTitle="Configurare una connessione gateway VPN da VNet a VNet tramite Gestione risorse di Azure e PowerShell per reti virtuali che risiedono nella stessa sottoscrizione| Microsoft Azure"
   description="In questo articolo viene illustrata la connessione tra reti virtuali utilizzando Gestione risorse di Azure e PowerShell."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/18/2015"
   ms.author="cherylmc"/>

# Configurare una connessione da VNet a VNet per reti virtuali nella stessa sottoscrizione usando Gestione risorse di Azure e PowerShell

> [AZURE.SELECTOR]
- [Azure Classic Portal](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell - Azure Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)

In questo articolo verrà illustrata la procedura usando il modello di distribuzione di Gestione risorse. Se si sta cercando un modello di distribuzione differente per questa configurazione, usare le schede riportate sopra per selezionare l'articolo più pertinente.

A questo punto, non c’è una soluzione per le connessioni VENT-VNET per reti virtuali create tramite il metodo di distribuzione di Gestione risorse che risiedono in diverse sottoscrizioni. Il team sta attualmente lavorando a una soluzione e si prevede di anticipare la procedura entro la fine dell'anno o all’inizio del prossimo anno. Quando sarà disponibile, questo articolo illustrerà quella procedura. La procedura seguente è valida per le reti virtuali che sono nella stessa sottoscrizione.

**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]
	
- Se le reti virtuali sono state create usando il modello di distribuzione classica, vedere [Creare una connessione da rete virtuale a rete virtuale](virtual-networks-configure-vnet-to-vnet-connection.md). Il modello di distribuzione classica supporta la connessione di reti virtuali che risiedono in diverse sottoscrizioni.
	
- Se si desidera connettere una rete virtuale creata con il modello di distribuzione classica a una rete virtuale creata tramite il modello di Gestione risorse di Azure, vedere [Connessione di reti virtuali classiche a nuove reti virtuali](../virtual-network/virtual-networks-arm-asm-s2s.md).

## Informazioni sulla connessione da rete virtuale a rete virtuale

La connessione di una rete virtuale a un'altra rete virtuale (da VNet a Vnet) è molto simile alla connessione di una rete virtuale a un percorso di sito locale. Entrambi i tipi di connettività utilizzano un gateway VPN per fornire un tunnel sicuro tramite IPsec/IKE. Le reti virtuali che si connettono possono trovarsi in aree geografiche diverse. È anche possibile combinare una comunicazione tra reti virtuali con configurazioni multisito. In questo modo è possibile definire topologie di rete che consentono di combinare la connettività fra più sedi locali con connettività fra più reti virtuali, come illustrato nel diagramma seguente.

![Diagramma di connettività tra reti virtuali](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727360.png)
 
### Perché connettere reti virtuali?

È possibile connettere reti virtuali per i seguenti motivi:

- **Presenza e ridondanza in più aree geografiche**
	- È possibile impostare le proprie sincronizzazione o replica geografica con connettività sicura senza passare da endpoint con connessione internet.
	- Con il servizio di bilanciamento del carico di Azure e Microsoft o tecnologia di clustering di terze parti, è possibile configurare il carico di lavoro a disponibilità elevata con ridondanza geografica in più aree di Azure. Un esempio importante è quella di configurare SQL Always On con gruppi di disponibilità distribuendo tra più aree di Azure.

- **Applicazioni multi livello in singole aree geografiche con alto grado di isolamento**
	- Nella stessa area, è possibile configurare applicazioni multilivello con più reti virtuali collegate tra loro forte isolamento e la comunicazione tra livelli sicura.


### Domande frequenti relative alla connessione di reti virtuali

- Le reti virtuali possono essere nelle sottoscrizioni uguale o diverse.

- Un servizio cloud o un endpoint di bilanciamento del carico non può estendersi tra reti virtuali, anche se sono connesse tra loro.

- Il collegamento di più reti virtuali di Azure non richiede alcun gateway VPN locale, a meno che la connettività cross-premise sia obbligatoria.

- La connettività da VNet a VNet supporta la connessione di reti virtuali. Non supporta le macchine virtuali o servizi non in una rete virtuale cloud.

- La connettività da VNet a VNet richiede la presenza di gateway VPN con tipi VPN RouteBased (in precedenza denominati dinamici).

- La connettività di rete virtuale può essere utilizzata contemporaneamente con VPN multisito, con un massimo di 10 tunnel VPN per gateway VPN di rete virtuale con connessione ad altre reti virtuali o a siti locali.

- Gli spazi degli indirizzi delle reti virtuali e dei siti di rete locali non devono sovrapporsi. La sovrapposizione degli spazi di indirizzi causerà errori nella creazione di reti virtuali.

- Non sono supportati tunnel ridondanti tra una coppia di reti virtuali.

- Tutti i tunnel VPN della rete virtuale condividono la larghezza di banda disponibile sul gateway VPN di Azure e i tempi di servizio del gateway VPN dello stesso contratto di servizio in Azure.

- Il traffico da rete virtuale a rete virtuale scorre attraverso il backbone di Azure.

## Configurare una connessione da rete virtuale a rete virtuale

In questo articolo verrà illustrata la connessione di due reti virtuali, VNet1 e VNet2. È necessario avere una certa familiarità con la rete per sostituire gli intervalli di indirizzi IP sono compatibili con i requisiti di progettazione della rete.

![Connessione tra reti virtuali](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727361.png)

### Prima di iniziare


- Verificare di possedere una sottoscrizione di Azure. Se non si dispone già di una sottoscrizione di Azure, è possibile attivare i [benefici della sottoscrizione MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Installare i moduli di PowerShell. Per configurare la connessione è necessario usare la versione più recente dei cmdlet di PowerShell per Gestione risorse di Azure.[AZURE.INCLUDE [vpn-gateway-ps-rm-howto](../../includes/vpn-gateway-ps-rm-howto-include.md)]


## Passaggio 1 - Pianificare gli intervalli di indirizzi IP


È importante definire gli intervalli che verranno utilizzati per impostare la configurazione di rete. Dalla prospettiva di VNet1, VNet2 è semplicemente un'altra connessione VPN definita nella piattaforma Azure. E dalla prospettiva di VNet2, VNet1 è solo un'altra connessione VPN. Tenere presente che è necessario assicurarsi che nessuno di intervalli di rete virtuale o intervalli di rete locale si sovrappongano in alcun modo.


Nei passaggi seguenti verranno create due reti virtuali con le rispettive subnet del gateway e le configurazioni. Verrà quindi creata una connessione di gateway VPN fra le due reti virtuali.

Per questo esercizio, utilizzare i valori seguenti per le reti virtuali:

Valori per VNet1:

- Nome di rete virtuale = VNet1
- Gruppo di risorse = testrg1
- Spazio degli indirizzi = 10.1.0.0/16 
- Area geografica = Stati Uniti occidentali
- GatewaySubnet = 10.1.0.0/28
- Subnet1 = 10.1.1.0/28

Valori per VNet2:

- Nome di rete virtuale = VNet2
- Gruppo di risorse = testrg2
- Spazio degli indirizzi = 10.2.0.0/16
- Area geografica = Giappone orientale
- GatewaySubnet = 10.2.0.0/28
- Subnet1 = 10.2.1.0/28

## Passaggio 2 - Eseguire la connessione alla sottoscrizione 

Verificare di passare alla modalità PowerShell per usare i cmdlet di Gestione risorse. Per altre informazioni, vedere [Uso di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md).

Aprire la console di PowerShell e connettersi al proprio account. Per connettersi, usare l'esempio seguente:

	Login-AzureRmAccount

Controllare le sottoscrizioni per l'account.

	Get-AzureRmSubscription 

Specificare la sottoscrizione da usare.

	Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


## Passaggio 3 - Creare una rete virtuale


Per creare una rete virtuale e una subnet del gateway, usare l'esempio seguente. Sostituire i valori per il proprio. In questo esempio verrà creata VNet1. Si ripeteranno gli stessi passaggi per creare VNet2 in un secondo momento.

Creare prima un gruppo di risorse.

	New-AzureRmResourceGroup -Name testrg1 -Location 'West US'

Successivamente, creare la rete virtuale. Nell'esempio seguente viene creata una rete virtuale denominata *VNet1* e due subnet, di cui una denominata *GatewaySubnet* e l'altra denominata *Subnet1*. È importante creare una subnet denominata specificamente *GatewaySubnet*. Se si assegnano altri nomi, la configurazione della connessione avrà esito negativo. Nell'esempio seguente, la subnet del gateway sta utilizzando un intervallo di /28. È possibile scegliere di utilizzare una subnet del gateway piccola quanto un intervallo di /29. Notare che alcune funzionalità (ad esempio una connessione ExpressRoute/da sito a sito coesistente) richiedono una subnet del gateway più ampia di /27, pertanto è opportuno creare subnet del gateway adeguate per supportare le funzionalità aggiuntive che potrebbero essere utilizzate in futuro.

 	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.0.0/28
	$subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.1.1.0/28'
	New-AzureRmVirtualNetwork -Name VNet1 -ResourceGroupName testrg1 -Location 'West US' -AddressPrefix 10.1.0.0/16
	-Subnet $subnet, $subnet1

## Passaggio 4 - Richiedere un indirizzo IP pubblico


Successivamente, si richiederà un indirizzo IP pubblico da allocare per il gateway che verrà creato per la rete virtuale. Non è possibile specificare l'indirizzo IP che si desidera utilizzare. Viene allocato in modo dinamico per il gateway. Questo indirizzo IP sarà utilizzato nella prossima sezione di configurazione.

Utilizzare l'esempio seguente. Il metodo di allocazione per questo indirizzo deve essere dinamico.


	$gwpip= New-AzureRmPublicIpAddress -Name gwpip1 -ResourceGroupName testrg1 -Location 'West US' -AllocationMethod Dynamic

## Passaggio 5 - Definire la configurazione del gateway


La configurazione del gateway definisce la subnet e l'indirizzo IP pubblico da utilizzare. Per creare la configurazione del gateway, usare l'esempio seguente.


	$vnet = Get-AzureRmVirtualNetwork -Name VNet1 -ResourceGroupName testrg1
	$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
	$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## Passaggio 6 - Creare il gateway


In questo passaggio verrà creato il gateway di rete virtuale per la VNet. Le configurazioni da rete virtuale a rete virtuale richiedono un tipo di VpnType RouteBased. Creare un gateway può richiedere tempo, pertanto è necessario essere pazienti.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1 -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## Passaggio 7 - Creare VNet2


Dopo aver configurato VNet1, ripetere i passaggi precedenti per configurare VNet2 inclusa la configurazione del relativo gateway. Dopo aver completato la configurazione per entrambe le reti virtuali e i rispettivi gateway, procedere a **Passaggio 8. Connettere i gateway**.

## Passaggio 8 - Connettere i gateway

In questo passaggio si creano le connessioni di gateway VPN tra i due gateway delle reti virtuali. Verrà visualizzata una chiave condivisa a cui si fa riferimento negli esempi. È possibile utilizzare i propri valori specifici per la chiave condivisa. La cosa importante è che la chiave condivisa deve corrispondere per entrambe le configurazioni.

Quando si creano connessioni, notare che richiedono tempo per il completamento.

**Per la connessione da VNet1 a VNet2**
    
    $vnetgw1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1
    $vnetgw2 = Get-AzureRmVirtualNetworkGateway -Name vnetgw2 -ResourceGroupName testrg2
    
    New-AzureRmVirtualNetworkGatewayConnection -Name conn1 -ResourceGroupName testrg1 -VirtualNetworkGateway1 $vnetgw1 -VirtualNetworkGateway2 $vnetgw2 -Location 'West US' -ConnectionType Vnet2Vnet -SharedKey 'abc123'


**Per la connessione da VNet2 a VNet1**
    
    $vnetgw1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw2 -ResourceGroupName testrg2
    $vnetgw2 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1
    
    New-AzureRmVirtualNetworkGatewayConnection -Name conn2 -ResourceGroupName testrg2 -VirtualNetworkGateway1 $vnetgw1 -VirtualNetworkGateway2 $vnetgw2 -Location 'Japan East' -ConnectionType Vnet2Vnet -SharedKey 'abc123'
    

Dopo alcuni minuti, la connessione dovrebbe stabilirsi. Notare che a questo punto i gateway e le connessioni creati con Gestione risorse di Azure non sono visibili nel portale di anteprima.

## Verificare le connessioni

A questo punto, le connessioni VPN create con Gestione risorse non sono visibili nel portale di anteprima. Tuttavia, è possibile verificare che la connessione abbia avuto esito positivo usando *Get-AzureRmVirtualNetworkGatewayConnection –Debug*. In futuro, per eseguire questa operazione sarà disponibile un cmdlet, nonché la possibilità di visualizzare la connessione nel portale di anteprima.

È possibile usare l'esempio di cmdlet seguente. Assicurarsi di modificare i valori in modo che corrispondano a ogni connessione che si desidera verificare. Quando richiesto, selezionare *A* per eseguirli tutti.

	Get-AzureRmVirtualNetworkGatewayConnection -Name vnet2connection -ResourceGroupName vnet2vnetrg -Debug 

 Al termine, scorrere i cmdlet per visualizzare i valori. Nell'esempio seguente, lo stato di connessione risulta *Connesso* ed è possibile visualizzare i byte in ingresso e in uscita.

	Body:
	{
	  "name": "Vnet2Connection",
	  "id": "/subscriptions/a932c0e6-b5cb-4e68-b23d-5064372c8a3cdef/resourceGroups/Vnet2VnetRG/providers/Microsoft.Network/connections/Vnet2Connection",
	  "properties": {
	    "provisioningState": "Succeeded",
	    "resourceGuid": "3c0bc049-860d-46ea-9909-e08098680a8bdef",
	    "virtualNetworkGateway1": {
	      "id": "/subscriptions/a932c0e6-b5cb-4e68-b23d-5064372c8a3cdef/resourceGroups/Vnet2VnetRG/providers/Microsoft.Network/virtualNetworkGateways/Vnet2Gw"
	    },
	    "virtualNetworkGateway2": {
	      "id": "/subscriptions/a932c0e6-b5cb-4e68-b23d-5064372c8a3cdef/resourceGroups/Vnet2VnetRG/providers/Microsoft.Network/virtualNetworkGateways/Vnet1Gw"
	    },
	    "connectionType": "Vnet2Vnet",
	    "routingWeight": 3,
	    "sharedKey": "abc123",
	    "connectionStatus": "Connected",
	    "ingressBytesTransferred": 3359044,
	    "egressBytesTransferred": 4142451
	  }
	} 

[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

## Connessione di reti virtuali esistenti

Se le reti virtuali sono già state create in modalità di Gestione risorse di Azure e si desidera connetterle, verificare quanto segue:

- Disponibilità di una subnet del gateway di almeno /29 o superiore per ogni rete virtuale.
- Gli intervalli di indirizzi per le reti virtuali non si sovrappongono.
- Le reti virtuali sono nella stessa sottoscrizione.

Se è necessario aggiungere subnet di gateway alle reti virtuali, utilizzare l'esempio seguente sostituendo i valori indicati con i propri valori. Assicurarsi di assegnare alla subnet del gateway il nome 'GatewaySubnet'. Se si assegnano altri nomi, la configurazione VPN non funzionerà come previsto.

	
	$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet
	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet
	Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

Dopo aver verificato che le subnet del gateway sono configurate correttamente, continuare con **Passaggio 4. Richiedere un indirizzo IP pubblico** e seguire i passaggi.


## Passaggi successivi

Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per i passaggi, vedere [Creare una macchina virtuale](../virtual-machines/virtual-machines-windows-tutorial.md).

<!---HONumber=AcomDC_0128_2016-->