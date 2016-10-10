<properties 
   pageTitle="Come connettere le reti virtuali classiche alle reti virtuali di Resource Manager tramite PowerShell | Microsoft Azure"
   description="Informazioni su come creare una connessione VPN tra le reti virtuali classiche e le reti virtuali di Resource Manager usando il gateway VPN e PowerShell"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="cherylmc" />

# Connettere reti virtuali da diversi modelli di distribuzione usando PowerShell

> [AZURE.SELECTOR]
- [Portale](vpn-gateway-connect-different-deployment-models-portal.md)
- [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)

Azure offre attualmente due modelli di gestione: classico e Resource Manager. Se si usa Azure da qualche tempo, si dispone probabilmente di VM e istanze del ruolo di Azure in esecuzione su una rete virtuale classica. Le macchine virtuali e le istanze del ruolo più recenti potrebbero invece essere in esecuzione su una rete virtuale creata in Resource Manager. Questo articolo descrive come connettere le reti virtuali classiche alle reti virtuali di Resource Manager per consentire alle risorse presenti nei modelli di distribuzione separati di comunicare tra di loro tramite una connessione gateway.

È possibile creare una connessione tra reti virtuali in sottoscrizioni diverse, in aree diverse e in modelli di distribuzione diversi. È anche possibile connettere reti virtuali che già dispongono di connessioni alle reti locali, purché il gateway con cui sono state configurate sia dinamico o basato su route. Per altre informazioni sulle connessioni da rete virtuale a rete virtuale, vedere la sezione [Domande frequenti relative alla connessione da rete virtuale a rete virtuale](#faq) alla fine di questo articolo. [AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## Prima di iniziare

I passaggi seguenti illustrano le impostazioni necessarie per configurare un gateway dinamico o basato su route per ogni rete virtuale e creare una connessione VPN tra i gateway. Questa configurazione non supporta i gateway con routing statico o basati su criteri.

Prima di iniziare, verificare quanto segue:

 - Entrambe le reti virtuali sono già state create.
 - Gli intervalli di indirizzi per le reti virtuali non si sovrappongono l'uno con l'altro o non si sovrappongano con gli intervalli delle eventuali altre connessioni a cui i gateway potrebbero essere collegati.
 - Sono stati installati i cmdlet di PowerShell più recenti (versione 1.0.2 o successive). Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md). Assicurarsi di installare sia i cmdlet di Gestione dei servizi (SM) che i cmdlet di Resource Manager (RM).

### <a name="exampleref"></a>Impostazioni di esempio

È possibile fare riferimento alle impostazioni di esempio quando si usano i cmdlet di PowerShell.

**Impostazioni della rete virtuale classica**

Nome rete virtuale = ClassicVNet <br> Località = Stati Uniti occidentali <br> Spazi indirizzi della rete virtuale = 10.0.0.0/24 <br> Subnet-1 = 10.0.0.0/27 <br> GatewaySubnet = 10.0.0.32/29 <br> Nome della rete locale = RMVNetLocal <br> GatewayType = DynamicRouting

**Impostazioni della rete virtuale di Resource Manager**

Nome della rete virtuale = RMVNet <br> Gruppo di risorse = RG1 <br> Spazi indirizzi IP della rete virtuale = 192.168.0.0/16 <br> Subnet-1 = 192.168.1.0/24 <br> GatewaySubnet = 192.168.0.0/26 <br> Località = Stati Uniti orientali <br> Nome IP pubblico del gateway = gwpip <br> Nome del gateway di rete locale = ClassicVNetLocal <br> Nome del gateway di rete virtuale = RMGateway <br> Configurazione di indirizzamento IP del gateway = gwipconfig


## <a name="createsmgw"></a>Sezione 1: Configurare una rete virtuale classica

### Parte 1: Eseguire il download del file di configurazione di rete

1. Accedere all'account Azure nella console di PowerShell con diritti elevati. Il cmdlet seguente richiede le credenziali di accesso per l'account Azure. Dopo l'accesso, vengono scaricate le impostazioni dell'account in modo che siano disponibili per Azure PowerShell. Per completare questa parte della configurazione si useranno i cmdlet PowerShell di Gestione servizi.

		Add-AzureAccount

2. Esportare il file di configurazione di rete di Azure eseguendo il comando seguente. Se necessario è possibile modificare il percorso del file da esportare. Dopo aver modificato il file, importarlo in Azure.

		Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml

3. Aprire il file XML scaricato per modificarlo. Per un esempio del file di configurazione di rete, vedere lo [Schema di configurazione di rete](https://msdn.microsoft.com/library/jj157100.aspx).
 

### Parte 2: Verificare la subnet del gateway

Nell'elemento **VirtualNetworkSites** aggiungere una subnet del gateway alla rete virtuale se non ne è ancora stato creato uno. Quando si lavora con il file di configurazione di rete, la subnet del gateway DEVE essere denominata "GatewaySubnet", altrimenti Azure non potrà riconoscerla e utilizzarla come una subnet del gateway.
	
    <VirtualNetworkSites>
      <VirtualNetworkSite name="ClassicVNet" Location="West US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/24</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Subnet-1">
            <AddressPrefix>10.0.0.0/27</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.0.32/29</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
       
### Parte 3: Aggiungere il sito di rete locale

Il sito di rete locale aggiunto rappresenta la rete virtuale di Resource Manager a cui si desidera connettersi. Potrebbe essere necessario aggiungere un elemento **LocalNetworkSites** al file se non ne esiste già uno. A questo punto nella configurazione, il parametro VPNGatewayAddress può essere qualsiasi indirizzo IP pubblico valido poiché non è stato ancora creato il gateway per la rete virtuale di Resource Manager. Dopo aver creato il gateway, l'indirizzo IP segnaposto verrà sostituito con l'indirizzo IP pubblico corretto assegnato al gateway di Resource Manager.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### Parte 4: Associare la rete locale al sito della rete locale

Questa sezione descrive come specificare il sito di rete locale a cui si desidera connettere la rete virtuale. In questo caso, è la rete virtuale di Resource Manager a cui si è fatto riferimento in precedenza. Assicurarsi che i nomi corrispondano. Questo passaggio non crea un gateway. Specifica la rete locale a cui il gateway si collegherà.

		<Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### Parte 5: Salvare il file e caricarlo

Salvare il file, quindi importarlo in Azure eseguendo il comando seguente. Assicurarsi di modificare il percorso del file in base al proprio ambiente.

		Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml

Verrà visualizzato un risultato simile al seguente, che indica che l'importazione è stata completata.

		OperationDescription        OperationId                      OperationStatus                                                
		--------------------        -----------                      ---------------                                                
		Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### Parte 6: creare il gateway 

È possibile creare il gateway di rete virtuale usando il portale classico o PowerShell.

Usare l'esempio seguente per creare un gateway con routing dinamico:

	New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting

È possibile verificare lo stato del gateway usando il cmdlet `Get-AzureVNetGateway`.


## <a name="creatermgw"></a>Sezione 2: configurazione del gateway della rete virtuale di Gestione risorse

Per creare un gateway VPN per la rete virtuale di Resource Manager, seguire le istruzioni seguenti. Non eseguire i passaggi fino a quando non si è recuperato l'indirizzo IP pubblico del gateway della rete virtuale classica.

1. **Accedere all'account di Azure** nella console PowerShell. Il cmdlet seguente richiede le credenziali di accesso per l'account di Azure. Dopo l'accesso, vengono scaricate le impostazioni dell'account in modo che siano disponibili per Azure PowerShell.

		Login-AzureRmAccount 

 	Ottenere un elenco delle sottoscrizioni di Azure se si dispone di più di una sottoscrizione.

		Get-AzureRmSubscription

	Specificare la sottoscrizione da usare.

		Select-AzureRmSubscription -SubscriptionName "Name of subscription"


2.  **Creare un gateway di rete locale**. In una rete virtuale il gateway di rete locale in genere fa riferimento al percorso locale. In questo caso, il gateway di rete locale fa riferimento alla rete virtuale classica. Assegnargli un nome che Azure possa usare come riferimento e specificare il prefisso dello spazio indirizzi. Azure usa il prefisso di indirizzo IP che viene specificato per identificare il traffico da inviare al percorso locale. Se si desidera modificare le informazioni prima di creare il gateway, è possibile modificare i valori ed eseguire nuovamente l'esempio.<br><br>
	- `-Name` è il nome da assegnare per fare riferimento al gateway di rete locale.<br>
	- `-AddressPrefix` è lo Spazio di indirizzi per la rete virtuale classica.<br>
	- `-GatewayIpAddress` è l'indirizzo IP pubblico del gateway della rete virtuale classica. Assicurarsi di modificare l'esempio seguente in modo da riflettere l'indirizzo IP corretto.
	
			New-AzureRmLocalNetworkGateway -Name ClassicVNetLocal `
			-Location "West US" -AddressPrefix "10.0.0.0/24" `
			-GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1

3. **Richiedere un indirizzo IP pubblico** da allocare al gateway di rete virtuale di Resource Manager. Non è possibile specificare l'indirizzo IP che si vuole usare. L'indirizzo IP viene allocato in modo dinamico al gateway di rete virtuale. Ciò non significa però che l'indirizzo IP verrà modificato. L'indirizzo IP del gateway di rete virtuale viene modificato solamente quando il gateway viene eliminato e ricreato. Non verrà modificato in caso di ridimensionamento, reimpostazione o altre manutenzioni/aggiornamenti del gateway.<br>In questo passaggio viene anche impostata una variabile che verrà usata in un passaggio successivo.


		$ipaddress = New-AzureRmPublicIpAddress -Name gwpip `
		-ResourceGroupName RG1 -Location 'EastUS' `
		-AllocationMethod Dynamic

4. **Verificare che la rete virtuale abbia una subnet gateway**. Se non esiste alcuna subnet del gateway, aggiungerne una. Assicurarsi che la subnet del gateway sia denominata *GatewaySubnet*.

5. **Recuperare la subnet** usata per il gateway eseguendo il comando seguente. In questo passaggio, è anche possibile impostare una variabile da usare nel passaggio successivo.
	- `-Name` è il nome della rete virtuale di Gestione risorse.
	- `-ResourceGroupName` è il gruppo di risorse a cui è associata la rete virtuale. Per poter funzionare correttamente, la subnet del gateway deve esistere già per questa rete virtuale e deve essere denominata *GatewaySubnet*.


			$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet `
			-VirtualNetwork (Get-AzureRmVirtualNetwork -Name RMVNet -ResourceGroupName RG1) 

6. **Creare la configurazione di indirizzamento IP gateway**. La configurazione del gateway definisce la subnet e l'indirizzo IP pubblico da utilizzare. Usare l'esempio seguente per creare la configurazione del gateway.<br>In questo passaggio i parametri `-SubnetId` e `-PublicIpAddressId` devono essere passati rispettivamente alla proprietà id dalla subnet e agli oggetti di indirizzo IP. Non è possibile utilizzare una stringa semplice. Queste variabili vengono impostate nel passaggio per richiedere un indirizzo IP pubblico e nel passaggio per recuperare la subnet.

		$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig `
		-Name gwipconfig -SubnetId $subnet.id `
		-PublicIpAddressId $ipaddress.id
	
7. **Creare il gateway di rete virtuale di Resource Manager** eseguendo il comando seguente. `-VpnType` deve essere *RouteBased*. Il completamento può richiedere 45 minuti o più.

		New-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
		-Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
		-IpConfigurations $gwipconfig `
		-EnableBgp $false -VpnType RouteBased

8. **Copiare l'indirizzo IP pubblico** dopo aver creato il gateway VPN. Verrà usato al momento della configurazione delle impostazioni di rete locale per la rete virtuale classica. È possibile usare il cmdlet seguente per recuperare l'indirizzo IP pubblico. L'indirizzo IP pubblico viene elencato nel valore restituito come *IpAddress*.

		Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName RG1

## Sezione 3: Modificare la rete locale nella rete virtuale classica

È possibile eseguire questa operazione esportando il file di configurazione di rete, modificandolo, salvandolo e importandolo nuovamente in Azure. In alternativa, è possibile modificare questa impostazione nel portale classico.

### Per eseguire la modifica nel portale

1. Aprire il [portale classico](https://manage.windowsazure.com).

2. Nel portale classico scorrere verso il basso a sinistra e fare clic su **Reti**. Nella pagina **Reti** fare clic su **Reti locali** nella parte superiore della pagina.

3. Nella pagina **Reti locali** selezionare la rete locale configurata nella parte 1, quindi passare alla parte inferiore della pagina e fare clic su **Modifica**.

4. Nella pagina **Specificare i dettagli della rete locale** sostituire l'indirizzo IP del segnaposto con l'indirizzo IP pubblico del gateway di Gestione risorse che è stato creato nella sezione precedente. Fare clic sulla freccia per passare alla sezione successiva. Verificare che lo **spazio di indirizzi** sia corretto, quindi fare clic sul segno di spunta per accettare le modifiche.

### Per eseguire la modifica tramite il file di configurazione della rete

1. Esportare il file di configurazione della rete.
2. In un editor di testo modificare l'indirizzo IP del gateway VPN.

	    <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>

3. Salvare le modifiche e importare il file modificato in Azure.


## <a name="connect"></a>Sezione 4: creazione di una connessione tra i gateway

La creazione di una connessione tra i gateway richiede PowerShell. Potrebbe essere necessario aggiungere l'account Azure per usare i cmdlet di PowerShell classico. A tale scopo, è possibile usare il cmdlet seguente:
		
	Add-AzureAccount

1. **Impostare la chiave condivisa** eseguendo il comando seguente. In questo esempio, `-VNetName` è il nome della rete virtuale classica e `-LocalNetworkSiteName` è il nome specificato per la rete locale quando è stata configurata nel portale classico. `-SharedKey` è un valore che è possibile generare e specificare. Il valore specificato qui deve essere lo stesso valore specificato nel passaggio successivo quando si crea la connessione. Il valore restituito per questo esempio deve visualizzare lo **stato: Operazione completata**.

		Set-AzureVNetGatewayKey -VNetName ClassicVNet `
		-LocalNetworkSiteName RMVNetLocal -SharedKey abc123

2. Creare la connessione VPN eseguendo i comandi seguenti.

	**Impostare le variabili**

		$vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
		$vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1

	**Creare la connessione**<br> Si noti che `-ConnectionType` è IPsec, non Vnet2Vnet.
		
		New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
		-Location "East US" -VirtualNetworkGateway1 `
		$vnet02gateway -LocalNetworkGateway2 `
		$vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

3. È possibile visualizzare la connessione in entrambi i portali o usando il cmdlet `Get-AzureRmVirtualNetworkGatewayConnection`.

		Get-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1

## <a name="faq"></a>Domande frequenti relative alla connessione di reti virtuali

Visualizzare i dettagli delle frequenti per altre informazioni sulle connessioni da rete virtuale a rete virtuale.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

<!---HONumber=AcomDC_0928_2016-->