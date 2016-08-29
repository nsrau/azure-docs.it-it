<properties 
   pageTitle="Come connettere le reti virtuali classiche alle reti virtuali di Gestione risorse tramite PowerShell | Microsoft Azure"
   description="Informazioni su come creare una connessione VPN tra le reti virtuali classiche e le reti virtuali di Gestione risorse usando il gateway VPN e PowerShell"
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
   ms.date="08/09/2016"
   ms.author="cherylmc" />  

# Connettere reti virtuali da diversi modelli di distribuzione usando PowerShell

> [AZURE.SELECTOR]
- [Portale](vpn-gateway-connect-different-deployment-models-portal.md)
- [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)

Azure dispone attualmente di due modelli di gestione: classico e Gestione risorse. Se si utilizza Azure da qualche tempo, si dispone probabilmente di macchine virtuali e ruoli di istanza di Azure in esecuzione su una rete virtuale classica. Le macchine virtuali e le istanze del ruolo più recenti potrebbero invece essere in esecuzione su una rete virtuale creata in Gestione risorse. Questo articolo descrive come connettere le reti virtuali classiche alle reti virtuali di Gestione risorse per consentire alle risorse presenti nei modelli di distribuzione separati di comunicare tra di loro tramite una connessione gateway.

È possibile creare una connessione tra reti virtuali in diverse sottoscrizioni, in aree diverse e in diversi modelli di distribuzione. È anche possibile connettere reti virtuali che già dispongono di connessioni alle reti locali, purché il gateway con cui è stato configurato sia dinamico o basato su route. Per ulteriori informazioni sulle connessioni da rete virtuale a rete virtuale, vedere la sezione [Domande frequenti relative alla connessione di reti virtuali](#faq) alla fine di questo articolo. 
[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## Prima di iniziare

I passaggi seguenti illustrano le impostazioni necessarie per configurare un gateway dinamico o basato su route per ogni rete virtuale e creare una connessione VPN tra i gateway. Questa configurazione non supporta i gateway con routing statico o basati su criteri.

Prima di iniziare, verificare quanto segue:

 - Entrambe le reti virtuali sono già state create.
 - Gli intervalli di indirizzi per le reti virtuali non si sovrappongono l'uno con l'altro o non si sovrappongano con gli intervalli delle eventuali connessioni a cui i gateway potrebbero essere collegati.
 - Sono stati installati i cmdlet di PowerShell più recenti (1.0.2 o successivi). Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md). Assicurarsi di installare sia la Gestione dei servizi (SM) sia i cmdlet di Gestione risorse (RM).

### <a name="exampleref"></a>Impostazioni di esempio

È possibile fare riferimento alle impostazioni di esempio quando si usano i cmdlet di PowerShell nei passaggi seguenti.

**Impostazioni classiche della rete virtuale**

Nome rete virtuale = ClassicVNet <br> Posizione = Stati Uniti occidentali <br> Spazi di indirizzi della rete virtuale = 10.0.0.0/24 <br> Subnet-1 = 10.0.0.0/27 <br> GatewaySubnet = 10.0.0.32/29 <br> Nome della rete locale = RMVNetLocal <br> GatewayType = DynamicRouting

**Impostazioni della rete virtuale Gestione risorse**

Nome della rete virtuale = RMVNet <br> Gruppo di risorse = RG1 <br> Spazi di indirizzi IP della rete virtuale = 192.168.1.0/16 <br> Subnet -1 = 192.168.1.0/24 <br> GatewaySubnet = 192.168.0.0/26 <br> Posizione = Stati Uniti orientali <br> Nome IP pubblico del gateway = gwpip <br> Gateway di rete locale = ClassicVNetLocal <br> Nome del gateway di rete virtuale = RMGateway <br> Configurazione di indirizzamento IP del gateway = gwipconfig


## <a name="createsmgw"></a>Sezione 1 - Configurazione di una rete virtuale classica

### Parte 1 - Scaricamento del file di configurazione di rete

1. Accedere all'account di Azure nella console PowerShell. Il cmdlet richiederà le credenziali di accesso per l'account Azure. Dopo l'accesso, vengono scaricate le impostazioni dell'account in modo che siano disponibili per Azure PowerShell. Per completare questa parte della configurazione verranno usati i cmdlet PowerShell di Gestione servizi.

		Add-AzureAccount

2. Esportare il file di configurazione di rete di Azure eseguendo il comando seguente. Se necessario è possibile modificare il percorso del file da esportare. Dopo aver modificato il file, importarlo in Azure.

		Get-AzureVNetConfig -ExportToFile c:\AzureNet\NetworkConfig.xml

3. Aprire il file XML scaricato per modificarlo. Per un esempio del file di configurazione di rete, vedere lo [Schema di configurazione di Rete](https://msdn.microsoft.com/library/jj157100.aspx).
 

### Parte 2 - Verifica della subnet del gateway

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
       
### Parte 3 - Aggiunta del sito di rete locale

Il sito di rete locale aggiunto rappresenta la rete virtuale di Gestione risorse a cui si desidera connettersi. Potrebbe essere necessario aggiungere un elemento **LocalNetworkSites** al file se non ne esiste già uno. A questo punto nella configurazione, il parametro VPNGatewayAddress può essere qualsiasi indirizzo IP pubblico valido poiché non è stato ancora creato il gateway per la rete virtuale di Gestione risorse. Dopo aver creato il gateway, l'indirizzo IP del segnaposto verrà sostituito con l'indirizzo IP pubblico corretto assegnato al gateway di Gestione risorse.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.1.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### Parte 4 - Associazione della rete locale con il sito della rete locale

Questa sezione specifica il sito di rete locale a cui si desidera connettere la rete virtuale. In questo caso, è la rete virtuale di Gestione risorse a cui si è fatto riferimento in precedenza. Assicurarsi che i nomi corrispondano. Questo passaggio non crea un gateway. Specifica la rete locale a cui il gateway si collegherà.

		<Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### Parte 5 - Salvataggio del file e caricamento

Salvare il file, quindi importarlo in Azure eseguendo il comando seguente. Assicurarsi di modificare il percorso del file in base al proprio ambiente.

		Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\ClassicVNet.xml

Dovrebbe essere visualizzato qualcosa di simile a questo risultato, che indica come l'importazione è stata eseguita correttamente.

		OperationDescription        OperationId                      OperationStatus                                                
		--------------------        -----------                      ---------------                                                
		Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### Parte 6 - Creazione del gateway di rete virtuale 

È possibile creare il gateway di rete virtuale usando il portale classico o PowerShell. Attualmente non è possibile creare un gateway classico nel portale di Azure.

#### Per creare il gateway nel portale classico

1. Nel [portale classico](https://manage.windowsazure.com) andare su **Reti** e fare clic sulla rete virtuale classica per la quale si desidera creare un gateway di rete virtuale. Verrà visualizzata la pagina principale della rete virtuale.
2. Fare clic su **Dashboard** nella parte superiore della pagina per passare alla pagina Dashboard.
3. Nella parte inferiore della pagina Dashboard fare clic su **Crea gateway**, quindi su **Routing dinamico**.
4. Fare clic su **Sì** per iniziare a creare il gateway.
5. Attendere che il gateway venga creato. In alcuni casi, l'operazione può richiedere 45 minuti o più.
6. Dopo aver creato il gateway, è possibile visualizzare l'indirizzo IP del gateway nella pagina **Dashboard**. Questo è l'indirizzo IP pubblico del gateway. Scrivere o copiare l'indirizzo IP pubblico. Usarlo nei passaggi successivi quando si crea la rete locale per la configurazione della rete virtuale di Gestione risorse.
7. Facoltativamente è possibile utilizzare questo cmdlet per recuperare le impostazioni del gateway e controllare lo stato del gateway: `Get-AzureVirtualNetworkGateway`

#### Per creare il gateway tramite PowerShell

Per creare il gateway tramite PowerShell, fare riferimento all'esempio seguente.

	New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting


## <a name="creatermgw"></a>Sezione 2: configurazione del gateway della rete virtuale di Gestione risorse

Per creare un gateway VPN per la rete virtuale di Gestione risorse, seguire le istruzioni seguenti. Non eseguire i passaggi fino a quando non si è recuperato l'indirizzo IP pubblico del gateway della rete virtuale classica.

1. **Accedere all'account di Azure** nella console PowerShell. Il cmdlet seguente richiede le credenziali di accesso per l'account di Azure. Dopo l'accesso, vengono scaricate le impostazioni dell'account in modo che siano disponibili per Azure PowerShell.

		Login-AzureRmAccount 

 	Ottenere un elenco delle sottoscrizioni di Azure se si dispone di più di una sottoscrizione.

		Get-AzureRmSubscription

	Specificare la sottoscrizione da usare.

		Select-AzureRmSubscription -SubscriptionName "Name of subscription"


2.  **Creare un gateway di rete locale**. In una rete virtuale il gateway di rete locale in genere fa riferimento al percorso locale. In questo caso, il gateway di rete locale fa riferimento alla rete virtuale classica. Assegnargli un nome che Azure possa usare come riferimento e specificare il prefisso dello spazio dell'indirizzo. Azure usa il prefisso di indirizzo IP che viene specificato per identificare il traffico da inviare al percorso locale. Se si desidera modificare le informazioni prima di creare il gateway, è possibile modificare i valori ed eseguire nuovamente l'esempio.<br><br>
	- `-Name` è il nome da assegnare per fare riferimento al gateway di rete locale.<br>
	- `-AddressPrefix` è lo Spazio di indirizzi per la rete virtuale classica.<br>
	- `-GatewayIpAddress` è l'indirizzo IP pubblico del gateway della rete virtuale classica. Assicurarsi di modificare l'esempio seguente in modo da riflettere l'indirizzo IP corretto.
	
			New-AzureRmLocalNetworkGateway -Name ClassicVNetLocal `
			-Location "West US" -AddressPrefix "10.0.0.0/24" `
			-GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1

3. **Richiesta di un indirizzo IP pubblico** da allocare per il gateway VPN della rete virtuale di Azure Resource Manager. Non è possibile specificare l'indirizzo IP che si desidera usare. Viene allocato in modo dinamico per il gateway. Ciò non significa però che l'indirizzo IP verrà modificato. L'indirizzo IP del gateway VPN di Azure viene modificato solamente quando il gateway viene eliminato e ricreato. Non verrà modificato in caso di ridimensionamento, reimpostazione o altre manutenzioni/aggiornamenti interni del gateway VPN di Azure.<br>In questo passaggio è anche possibile impostare una variabile che verrà usata in un passaggio successivo.


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
	
7. **Creare il gateway della rete virtuale di Gestione risorse** eseguendo il comando seguente. `-VpnType` deve essere *RouteBased*.

		New-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
		-Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
		-IpConfigurations $gwipconfig `
		-EnableBgp $false -VpnType RouteBased

8. **Copiare l'indirizzo IP pubblico** dopo aver creato il gateway VPN. Usarlo quando si configurano le impostazioni di rete locale per la rete virtuale classica. È possibile usare il cmdlet seguente per recuperare l'indirizzo IP pubblico. L'indirizzo IP pubblico viene elencato nel valore restituito come *IpAddress*.

		Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName RG1

## Sezione 3: modifica della rete locale nella rete virtuale classica

È possibile eseguire questa operazione esportando il file di configurazione di rete, modificandolo, salvandolo e importandolo nuovamente in Azure. In alternativa, è possibile modificare questa impostazione nel portale classico.

### Per la modifica nel portale

1. Aprire il [portale classico](https://manage.windowsazure.com).

2. Nel portale classico scorrere verso il basso a sinistra e fare clic su **Reti**. Nella pagina **Reti** fare clic su **Reti locali** nella parte superiore della pagina.

3. Nella pagina **Reti locali** selezionare la rete locale configurata nella parte 1, quindi passare alla parte inferiore della pagina e fare clic su **Modifica**.

4. Nella pagina **Specificare i dettagli della rete locale** sostituire l'indirizzo IP del segnaposto con l'indirizzo IP pubblico del gateway di Gestione risorse che è stato creato nella sezione precedente. Fare clic sulla freccia per passare alla sezione successiva. Verificare che lo **spazio di indirizzi** sia corretto, quindi fare clic sul segno di spunta per accettare le modifiche.

### Per la modifica tramite il file di configurazione della rete

1. Esportare il file di configurazione della rete.
2. In un editor di testo modificare l'indirizzo IP del gateway VPN.

	    <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>

3. Salvare le modifiche e importare il file modificato nuovamente in Azure.


## <a name="connect"></a>Sezione 4: creazione di una connessione tra i gateway

La creazione di una connessione tra i gateway richiede PowerShell. Potrebbe essere necessario aggiungere l'account di Azure per usare i cmdlet di PowerShell classici. A tale scopo, è possibile usare l'esempio di cmdlet seguente:
		
	Add-AzureAccount

1. **Impostare la chiave condivisa** eseguendo il comando seguente. In questo esempio, `-VNetName` è il nome della rete virtuale classica e `-LocalNetworkSiteName` è il nome specificato per la rete locale quando è stata configurata nel portale classico. `-SharedKey` è un valore che è possibile generare e specificare. Il valore specificato qui deve essere lo stesso valore specificato nel passaggio successivo quando si crea la connessione. Il valore restituito per questo esempio deve visualizzare lo stato: Operazione completata.

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

Fare riferimento alle domande frequenti per ulteriori informazioni sulle connessioni da rete virtuale a rete virtuale.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

<!---HONumber=AcomDC_0810_2016-->
