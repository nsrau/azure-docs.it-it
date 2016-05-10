<properties
   pageTitle="Creare una connessione gateway VPN tra reti virtuali tramite Azure Resource Manager e PowerShell per reti virtuali | Microsoft Azure"
   description="In questo articolo viene illustrata la connessione tra reti virtuali utilizzando Gestione risorse di Azure e PowerShell."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/02/2016"
   ms.author="cherylmc"/>

# Configurare una connessione da VNet a VNet tramite Azure Resource Manager e PowerShell

> [AZURE.SELECTOR]
- [Portale di Azure classico](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell - Azure Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)

Questo articolo illustra i passaggi per creare una connessione tra reti virtuali usando il modello di distribuzione di Azure Resource Manager e PowerShell. Le reti virtuali possono trovarsi in aree geografiche uguali o diverse e in sottoscrizioni uguali o diverse.


**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

**Strumenti e modelli di distribuzione per la connessione tra reti virtuali**

È possibile configurare una connessione tra reti virtuali in entrambi i modelli di distribuzione e tramite numerosi strumenti. Per altre informazioni, vedere la tabella seguente. La tabella verrà aggiornata man mano che per questa configurazione risultano disponibili nuovi articoli, modelli di distribuzione e strumenti. Quando un articolo risulterà disponibile, nella tabella sarà presente un collegamento diretto.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]


## Informazioni sulla connessione da rete virtuale a rete virtuale

La connessione di una rete virtuale a un'altra rete virtuale (da VNet a Vnet) è molto simile alla connessione di una rete virtuale a un percorso di sito locale. Entrambi i tipi di connettività usano un gateway VPN di Azure per fornire un tunnel sicuro tramite IPsec/IKE. Le reti virtuali che si connettono possono trovarsi in aree geografiche diverse. Possono anche trovarsi in sottoscrizioni diverse. È anche possibile combinare una comunicazione tra reti virtuali con configurazioni multisito. In questo modo è possibile definire topologie di rete che consentono di combinare la connettività fra più sedi locali con connettività fra più reti virtuali, come illustrato nel diagramma seguente.


![Informazioni sulle connessioni](./media/vpn-gateway-vnet-vnet-rm-ps/aboutconnections.png)
 
### Perché connettere reti virtuali?

È possibile connettere reti virtuali per i seguenti motivi:

- **Presenza e ridondanza in più aree geografiche**
	- È possibile configurare la sincronizzazione o la replica geografica con connettività sicura senza passare da endpoint con connessione Internet.
	- Con Gestione traffico e il servizio di bilanciamento del carico di Azure è possibile configurare il carico di lavoro a disponibilità elevata con ridondanza geografica in più aree di Azure. Un esempio importante è rappresentato dalla configurazione SQL AlwaysOn con gruppi di disponibilità distribuiti in più aree di Azure.

- **Applicazioni multilivello in singole aree geografiche con isolamento o limite amministrativo**
	- All'interno di una stessa area è possibile configurare applicazioni multilivello con più reti virtuali connesse tra loro a causa dell'isolamento o di requisiti amministrativi.


### Domande frequenti relative alla connessione di reti virtuali

- Le reti virtuali possono essere nelle sottoscrizioni uguale o diverse.

- Un servizio cloud o un endpoint di bilanciamento del carico non può estendersi tra reti virtuali, anche se sono connesse tra loro.

- Il collegamento di più reti virtuali di Azure non richiede alcun gateway VPN locale, a meno che la connettività cross-premise non sia obbligatoria.

- La connettività da VNet a VNet supporta la connessione di reti virtuali. Non supporta le macchine virtuali o servizi non in una rete virtuale cloud.

- La connettività tra reti virtuali richiede la presenza di gateway VPN con tipi VPN RouteBased (in precedenza denominato routing dinamico).

- La connettività di rete virtuale può essere usata contemporaneamente alle VPN multisito, con un massimo di 10 (gateway predefiniti/standard) o 30 (gateway ad alte prestazioni) tunnel VPN per un gateway VPN di rete virtuale che si connette ad altre reti virtuali o a siti locali.

- Gli spazi degli indirizzi delle reti virtuali e dei siti di rete locali non devono sovrapporsi. La sovrapposizione degli spazi di indirizzi causerà errori nella creazione di connessioni tra reti virtuali.

- Non sono supportati tunnel ridondanti tra una coppia di reti virtuali.

- Tutti i tunnel VPN della rete virtuale condividono la larghezza di banda disponibile sul gateway VPN di Azure e i tempi di servizio del gateway VPN dello stesso contratto di servizio in Azure.

- Il traffico tra reti virtuali passa per la rete Microsoft, non Internet.

- Il traffico tra reti virtuali all'interno della stessa area è gratuito in entrambe le direzioni, mentre per il traffico tra reti virtuali in aree diverse vengono applicate le tariffe di trasferimento dati in uscita tra reti virtuali in base alle aree di origine. Per informazioni dettagliate, vedere la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/vpn-gateway/).


## Quale procedura è consigliabile seguire?

In questo articolo sono presentate diverse procedure applicabili alle reti virtuali create con il modello di distribuzione di Gestione risorse. La procedura per la configurazione tra reti virtuali varia a seconda del fatto che le reti virtuali si trovino nella stessa sottoscrizione o in sottoscrizioni diverse.

![Entrambe le connessioni](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)


Per quando riguarda la procedura di configurazione, la differenza principale per i due casi risiede nella possibilità o nell'impossibilità di creare e configurare tutte le risorse di rete virtuale e gateway all'interno della stessa sessione di PowerShell. Questo documento illustra la procedura per ognuno dei due casi. L'immagine riportata sopra mostra una connessione tra reti virtuali nella stessa sottoscrizione e una connessione tra reti virtuali in sottoscrizioni diverse.


- [Reti virtuali incluse nella stessa sottoscrizione](#samesub)
- [Reti virtuali incluse in diverse sottoscrizioni](#difsub)


## <a name="samesub"></a>Come connettere reti virtuali che si trovano nella stessa sottoscrizione

Questa configurazione si applica alle reti virtuali che si trovano nella stessa sottoscrizione, come mostrato nella figura seguente:

![Da rete virtuale a rete virtuale nella stessa sottoscrizione](./media/vpn-gateway-vnet-vnet-rm-ps/samesubscription.png)

### Prima di iniziare

- Verificare di possedere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi dell'abbonamento a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).
	
- È necessario installare i cmdlet di PowerShell per Gestione risorse di Azure. Per altre informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

### <a name="Step1"></a>Passaggio 1: Pianificare gli intervalli di indirizzi IP


È importante definire gli intervalli che verranno utilizzati per impostare la configurazione di rete. Tenere presente che è necessario assicurarsi che nessuno di intervalli di rete virtuale o intervalli di rete locale si sovrappongano in alcun modo.

Nei passaggi seguenti verranno create due reti virtuali con le rispettive subnet del gateway e le configurazioni. Verrà quindi creata una connessione di gateway VPN fra le due reti virtuali.

Per questo esercizio, utilizzare i valori seguenti per le reti virtuali:

**Valori per TestVNet1:**

- Nome della rete virtuale: TestVNet1
- Gruppo di risorse: TestRG1
- Location: Stati Uniti orientali
- TestVNet1: 10.11.0.0/16 & 10.12.0.0/16
- FrontEnd: 10.11.0.0/24
- BackEnd: 10.12.0.0/24
- GatewaySubnet: 10.12.255.0/27
- Server DNS: 8.8.8.8
- GatewayName: VNet1GW
- IP pubblico: VNet1GWIP
- VPNType: RouteBased
- Connection(1to4): VNet1toVNet4
- Connection(1to5): VNet1toVNet5
- ConnectionType: VNet2VNet


**Valori per TestVNet4:**

- Nome della rete virtuale: TestVNet4
- TestVNet2: 10.41.0.0/16 & 10.42.0.0/16
- FrontEnd: 10.41.0.0/24
- BackEnd: 10.42.0.0/24
- GatewaySubnet: 10.42.255.0.0/27
- Gruppo di risorse: TestRG4
- Località: Stati Uniti occidentali
- Server DNS: 8.8.8.8
- GatewayName: VNet4GW
- IP pubblico: VNet4GWIP
- VPNType: RouteBased
- Connessione: VNet4toVNet1
- ConnectionType: VNet2VNet



### <a name="Step2"></a>Passaggio 2: Creare e configurare TestVNet1

1. Dichiarare le variabili

	Per questo esercizio, si inizierà dichiarando le variabili. Nell'esempio seguente vengono dichiarate le variabili usando i valori per questo esercizio. Assicurarsi di sostituire i valori con quelli reali durante la configurazione per la produzione. Queste variabili possono essere usate se si esegue la procedura per acquisire familiarità con questo tipo di configurazione. Modificare le variabili, quindi copiarle e incollarle nella console di PowerShell.

		$Sub1          = "Replace_With_Your_Subcription_Name"
		$RG1           = "TestRG1"
		$Location1     = "East US"
		$VNetName1     = "TestVNet1"
		$FESubName1    = "FrontEnd"
		$BESubName1    = "Backend"
		$GWSubName1    = "GatewaySubnet"
		$VNetPrefix11  = "10.11.0.0/16"
		$VNetPrefix12  = "10.12.0.0/16"
		$FESubPrefix1  = "10.11.0.0/24"
		$BESubPrefix1  = "10.12.0.0/24"
		$GWSubPrefix1  = "10.12.255.0/27"
		$DNS1          = "8.8.8.8"
		$GWName1       = "VNet1GW"
		$GWIPName1     = "VNet1GWIP"
		$GWIPconfName1 = "gwipconf1"
		$Connection14  = "VNet1toVNet4"
		$Connection15  = "VNet1toVNet5"

2. Connettersi alla sottoscrizione 1

	Verificare di passare alla modalità PowerShell per usare i cmdlet di Gestione risorse. Per altre informazioni, vedere [Uso di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md).

	Aprire la console di PowerShell e connettersi al proprio account. Per connettersi, usare l'esempio seguente:

		Login-AzureRmAccount

	Controllare le sottoscrizioni per l'account.

		Get-AzureRmSubscription 

	Specificare la sottoscrizione da usare.

		Select-AzureRmSubscription -SubscriptionName $Sub1

3. Creare un nuovo gruppo di risorse

		New-AzureRmResourceGroup -Name $RG1 -Location $Location1

4. Creare le configurazioni di subnet per TestVNet1

	Nell'esempio seguente vengono create una rete virtuale denominata TestVNet1 e tre subnet, denominate GatewaySubnet, FrontEnd e Backend. Quando si sostituiscono i valori, è importante che la subnet gateway venga denominata sempre esattamente GatewaySubnet. Se si assegnano altri nomi, la creazione del gateway avrà esito negativo.

	Nell'esempio seguente la subnet gateway usa una dimensione /27. Anche se tecnicamente è possibile creare una subnet gateway usando una subnet di dimensioni /29, non è consigliabile procedere in questo modo. È preferibile usare una dimensione maggiore, ad esempio /27 o /26, per poter eventualmente sfruttare configurazioni esistenti o future che richiedano una subnet gateway più ampia.


		$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
		$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
		$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1


5. Creare TestVNet1

		New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

6. Richiedere un indirizzo IP pubblico

	Successivamente, si richiederà un indirizzo IP pubblico da allocare per il gateway che verrà creato per la rete virtuale. Non è possibile specificare l'indirizzo IP che si desidera utilizzare. Viene allocato in modo dinamico per il gateway. Questo indirizzo IP sarà utilizzato nella prossima sezione di configurazione. Usare l'esempio seguente. Il metodo di allocazione per questo indirizzo deve essere dinamico.

		$gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

7. Definire la configurazione del gateway

	La configurazione del gateway definisce la subnet e l'indirizzo IP pubblico da utilizzare. Per creare la configurazione del gateway, usare l'esempio seguente.

		$vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
		$subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
		$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

8. Creare il gateway per TestVNet1

	In questo passaggio si creerà il gateway di rete virtuale per TestVNet1. Le configurazioni da rete virtuale a rete virtuale richiedono un tipo di VpnType RouteBased. La creazione di un gateway può richiedere un po' di tempo (30 minuti o più).

		New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### Passaggio 3 - Creare e configurare TestVNet4

Dopo aver configurato TestVNet1, ripetere la procedura per creare TestVNet4. Eseguire la procedura seguente, sostituendo i valori con quelli personalizzati quando necessario. Questo passaggio può essere eseguito nella stessa sessione di PowerShell perché si trova nella stessa sottoscrizione.

1. Dichiarare le variabili

	Sostituire i valori con quelli desiderati per la propria configurazione.

		$RG4           = "TestRG4"
		$Location4     = "West US"
		$VnetName4     = "TestVNet4"
		$FESubName4    = "FrontEnd"
		$BESubName4    = "Backend"
		$GWSubName4    = "GatewaySubnet"
		$VnetPrefix41  = "10.41.0.0/16"
		$VnetPrefix42  = "10.42.0.0/16"
		$FESubPrefix4  = "10.41.0.0/24"
		$BESubPrefix4  = "10.42.0.0/24"
		$GWSubPrefix4  = "10.42.255.0/27"
		$DNS4          = "8.8.8.8"
		$GWName4       = "VNet4GW"
		$GWIPName4     = "VNet4GWIP"
		$GWIPconfName4 = "gwipconf4"
		$Connection41  = "VNet4toVNet1"

	Prima di proseguire, verificare di essere ancora connessi alla sottoscrizione 1.

2. Creare un nuovo gruppo di risorse

		New-AzureRmResourceGroup -Name $RG4 -Location $Location4

3. Creare le configurazioni di subnet per TestVNet4

		$fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
		$besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
		$gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4

4. Creare TestVNet4

		New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4

5. Richiedere un indirizzo IP pubblico

		$gwpip4    = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 -Location $Location4 -AllocationMethod Dynamic

6. Definire la configurazione del gateway

		$vnet4     = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
		$subnet4   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
		$gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4

7. Creare il gateway di TestVNet4

		New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### Passaggio 4 - Connessione dei gateway

1. Ottenere entrambi i gateway di rete virtuale

	In questo esempio, dato che entrambi i gateway si trovano nella stessa sottoscrizione, il passaggio può essere completato nella stessa sessione di PowerShell.

		$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
		$vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4


2. Creare la connessione da TestVNet1 a TestVNet4

	In questo passaggio si creerà la connessione da TestVNet1 a TestVNet4. Verrà visualizzata una chiave condivisa a cui si fa riferimento negli esempi. È possibile utilizzare i propri valori specifici per la chiave condivisa. L'importante è che la chiave condivisa corrisponda per entrambe le configurazioni. Il completamento della creazione di una connessione può richiedere un po' di tempo.

		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

3. Creare la connessione da TestVNet4 a TestVNet1

	Questo passaggio è simile a quello precedente, ma riguarda la creazione della connessione da TestVNet4 a TestVNet1. Assicurarsi che le chiavi condivise corrispondano.

		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

	Dopo alcuni minuti, la connessione dovrebbe stabilirsi.

## <a name="Verify"></a>Come verificare una connessione tra reti virtuali

L'esempio seguente illustra come verificare la connessione. Assicurarsi di modificare i valori in base all'ambiente.

### Per verificare la connessione tramite il portale di Azure

È possibile verificare una connessione VPN nel portale di Azure passando a **Gateway di rete virtuale**, **facendo clic sul nome del gateway** -> **Impostazioni** -> **Connessioni**. Selezionando il nome della connessione è possibile visualizzare informazioni aggiuntive nel pannello **Connessione**.


### Per verificare la connessione tramite PowerShell

È anche possibile verificare che la connessione sia riuscita usando *Get-AzureRmVirtualNetworkGatewayConnection –Debug*. Si può usare l'esempio seguente, modificando i valori in modo che corrispondano ai propri. Quando richiesto, selezionare A per eseguirli tutti.

	Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 -Debug

Al termine, scorrere i cmdlet per visualizzare i valori. Nell'esempio di output di PowerShell seguente lo stato della connessione risulta *Connesso* ed è possibile vedere i byte in ingresso e in uscita.



	AuthorizationKey           :
	VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
	VirtualNetworkGateway2     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
	LocalNetworkGateway2       :
	Peer                       :
	ConnectionType             : Vnet2Vnet
	RoutingWeight              : 0
	SharedKey                  : AzureA1b2C3
	ConnectionStatus           : Connected
	EgressBytesTransferred     : 1376
	IngressBytesTransferred    : 1232
	ProvisioningState          : Succeeded
	VirtualNetworkGateway1Text : "/subscriptions/<SubscriptionID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
	VirtualNetworkGateway2Text : "/subscriptions/<SubscriptionID>/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW"
	LocalNetworkGateway2Text   :
	PeerText                   :
	ResourceGroupName          : TestRG1
	Location                   : eastus
	ResourceGuid               : 173489d1-37e2-482c-b8b8-6ca69fc3e069
	Tag                        : {}
	TagsTable                  :
	Name                       : VNet1toVNet4
	Id                         : /subscriptions/<SubscriptionID>/resourceGroups/TestRG1/providers/Micr osoft.Network/connections/VNet1toVNet4

## <a name="difsub"></a>Come connettere reti virtuali che si trovano in sottoscrizioni diverse

La procedura di configurazione seguente aggiunge un'altra connessione tra reti virtuali per connettere TestVNet1 a TestVNet5, che si trova in una sottoscrizione diversa. La differenza consiste nel fatto che una parte dei passaggi di configurazione deve essere eseguita in una sessione di PowerShell separata nel contesto della seconda sottoscrizione, soprattutto quando le due sottoscrizioni appartengono a organizzazioni diverse. Nella figura seguente è mostrata la configurazione risultante al termine della procedura che segue:

![Da rete virtuale a rete virtuale in sottoscrizioni diverse](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)

Le istruzioni seguenti sono la prosecuzione dei passaggi precedenti. È necessario completare il [Passaggio 1](#Step1) e il [Passaggio 2](#Step2) per creare e configurare TestVNet1 e il gateway VPN per TestVNet1. Se si prevede di connettere solo reti virtuali in sottoscrizioni diverse, non è necessario eseguire i passaggi 3 e 4 dall'esercizio precedente e si può procedere al passaggio 5 seguente.

### Passaggio 5 - Verificare gli intervalli di indirizzi IP aggiuntivi

È importante assicurarsi che lo spazio di indirizzi IP della nuova rete virtuale, TestVNet5, non si sovrapponga ad altri di intervalli di rete virtuale o intervalli di gateway di rete locale.

In questo esempio le reti virtuali possono appartenere a organizzazioni diverse. Per questo esercizio è possibile usare i valori seguenti per TestVNet5:

**Valori per TestVNet5:**

- Nome della rete virtuale: TestVNet5
- Gruppo di risorse: TestRG5
- Ubicazione: Giappone orientale
- TestVNet5: 10.51.0.0/16 e 10.52.0.0/16
- FrontEnd: 10.51.0.0/24
- BackEnd: 10.52.0.0/24
- GatewaySubnet: 10.52.255.0.0/27
- Server DNS: 8.8.8.8
- GatewayName: VNet5GW
- IP pubblico: VNet5GWIP
- VPNType: RouteBased
- Connessione: VNet5toVNet1
- ConnectionType: VNet2VNet

**Altri valori per TestVNet1:**

- Connessione: VNet1toVNet5


### Passaggio 6 - Creare e configurare TestVNet5

Questo passaggio deve essere eseguito nel contesto della nuova sottoscrizione. Questa parte può essere eseguita dall'amministratore in un'altra organizzazione che possiede la sottoscrizione.

1. Dichiarare le variabili

	Sostituire i valori con quelli desiderati per la propria configurazione.

		$Sub5          = "Replace_With_the_New_Subcription_Name"
		$RG5           = "TestRG5"
		$Location5     = "Japan East"
		$VnetName5     = "TestVNet5"
		$FESubName5    = "FrontEnd"
		$BESubName5    = "Backend"
		$GWSubName5    = "GatewaySubnet"
		$VnetPrefix51  = "10.51.0.0/16"
		$VnetPrefix52  = "10.52.0.0/16"
		$FESubPrefix5  = "10.51.0.0/24"
		$BESubPrefix5  = "10.52.0.0/24"
		$GWSubPrefix5  = "10.52.255.0/27"
		$DNS5          = "8.8.8.8"
		$GWName5       = "VNet5GW"
		$GWIPName5     = "VNet5GWIP"
		$GWIPconfName5 = "gwipconf5"
		$Connection51  = "VNet5toVNet1"

2. Connettersi alla sottoscrizione 5

	Aprire la console di PowerShell e connettersi al proprio account. Per connettersi, usare l'esempio seguente:

		Login-AzureRmAccount

	Controllare le sottoscrizioni per l'account.

		Get-AzureRmSubscription 

	Specificare la sottoscrizione da usare.

		Select-AzureRmSubscription -SubscriptionName $Sub5

3. Creare un nuovo gruppo di risorse

		New-AzureRmResourceGroup -Name $RG5 -Location $Location5

4. Creare le configurazioni di subnet per TestVNet4
	
		$fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
		$besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
		$gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5

5. Creare TestVNet5

		New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5

6. Richiedere un indirizzo IP pubblico

		$gwpip5    = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 -Location $Location5 -AllocationMethod Dynamic


7. Definire la configurazione del gateway

		$vnet5     = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
		$subnet5   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
		$gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5

8. Creare il gateway di TestVNet5

		New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### Passaggio 7 - Connessione dei gateway

In questo esempio, dato che i gateway si trovano in sottoscrizioni diverse, il passaggio è stato suddiviso in due sessioni di PowerShell contrassegnate come [Sottoscrizione 1] e [Sottoscrizione 5].

1. **[Sottoscrizione 1]** Ottenere il gateway di rete virtuale per Sottoscrizione 1

	Eseguire l'accesso e connettersi a Sottoscrizione 1.

		$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1

	Copiare l'output degli elementi seguenti e inviarli all'amministratore della Sottoscrizione 5 tramite posta elettronica o con un altro metodo.

		$vnet1gw.Name
		$vnet1gw.Id

	Questi due elementi avranno valori simili all'esempio di output seguente:

		PS D:> $vnet1gw.Name
		VNet1GW
		PS D:> $vnet1gw.Id
		/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW

2. **[Sottoscrizione 5]** Ottenere il gateway di rete virtuale per Sottoscrizione 5

	Eseguire l'accesso e connettersi a Sottoscrizione 5.

		$vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5

	Copiare l'output degli elementi seguenti e inviarli all'amministratore della Sottoscrizione 1 tramite posta elettronica o con un altro metodo.

		$vnet5gw.Name
		$vnet5gw.Id

	Questi due elementi avranno valori simili all'esempio di output seguente:

		PS C:\> $vnet5gw.Name
		VNet5GW
		PS C:\> $vnet5gw.Id
		/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW

3. **[Sottoscrizione 1]** Creare la connessione da TestVNet1 a TestVNet5

	In questo passaggio si creerà la connessione da TestVNet1 a TestVNet5. La differenza in questo caso consiste nel fatto che non è possibile ottenere direttamente $vnet5gw perché si trova in una sottoscrizione diversa. Sarà necessario creare un nuovo oggetto di PowerShell con i valori comunicati dalla Sottoscrizione 1 nei passaggi precedenti. Sostituire il nome, l'ID e la chiave condivisa con i valori personalizzati. L'importante è che la chiave condivisa corrisponda per entrambe le configurazioni. Il completamento della creazione di una connessione può richiedere un po' di tempo.

	Connettersi a Sottoscrizione 1.
	
		$vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
		$vnet5gw.Name = "VNet5GW"
		$vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
		$Connection15 = "VNet1toVNet5"
		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

4. **[Sottoscrizione 5]** Creare la connessione da TestVNet5 a TestVNet1

	Questo passaggio è simile a quello precedente, ma riguarda la creazione della connessione da TestVNet5 a TestVNet1. Anche in questo vale lo stesso processo per la creazione di un oggetto di PowerShell in base ai valori ottenuti da Sottoscrizione 1. In questo passaggio assicurarsi che le chiavi condivise corrispondano.

	Connettersi a Sottoscrizione 5.

		$vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
		$vnet1gw.Name = "VNet1GW"
		$vnet1gw.Id   = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

5. Verifica della connessione

	Al termine della procedura è possibile verificare la connessione usando i metodi indicati in [Come verificare una connessione tra reti virtuali](#Verify).

## Passaggi successivi

Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per i passaggi, vedere [Creare una macchina virtuale](../virtual-machines/virtual-machines-windows-hero-tutorial.md).

<!---HONumber=AcomDC_0504_2016-->