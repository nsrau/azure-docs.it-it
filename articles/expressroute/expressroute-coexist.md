<properties
   pageTitle="Configurare connessioni di ExpressRoute e VPN da sito a sito coesistenti | Microsoft Azure"
   description="Questo articolo illustra come configurare connessioni ExpressRoute e VPN da sito a sito ai fini della coesistenza."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/17/2015"
   ms.author="cherylmc"/>

# Configurare connessioni di Azure ExpressRoute e VPN da sito a sito coesistenti

Ora si possono connettere ExpressRoute e VPN da sito a sito alla stessa rete virtuale. Esistono due diversi scenari in cui è possibile utilizzare connessioni coesistenti. Lo Scenario 1 utilizza ExpressRoute come collegamento attivo e la connessione da sito a sito come connessione di backup. Nello Scenario 2, entrambe le connessioni sono attive.

I passaggi descritti in questo articolo si applicano alle reti virtuali che utilizzando il modello di distribuzione classica. Questo tipo di connessione coesistente non è ancora disponibile per le reti virtuali che utilizzano il modello di distribuzione di gestione delle risorse.



### Scenario 1

In questo scenario, si dispone di una rete locale. ExpressRoute è il collegamento attivo e la VPN da sito a sito è il backup. Se la connessione ExpressRoute non riesce, la connessione VPN da sito a sito diventa attiva. Questo scenario è il più appropriato per la disponibilità elevata.

![Coesistenza](media/expressroute-coexist/scenario1.jpg)



### Scenario 2

In questo scenario, si dispone di due reti locali. È possibile connettere una rete ad Azure tramite ExpressRoute e un’altra rete tramite VPN da sito a sito. In questo caso, entrambe le connessioni sono attive contemporaneamente.

![Coesistenza](media/expressroute-coexist/scenario2.jpg)


## Scelta di una procedura di configurazione

Esistono due diverse serie di procedure disponibili per configurare le connessioni per coesistere. La procedura di configurazione scelta dipende dalla disponibilità o meno di una rete virtuale esistente a cui stabilire la connessione oppure dall'esigenza di creare una nuova rete virtuale.

- **Creare una nuova rete virtuale con connessioni coesistenti:**

	Se non si ha già una rete virtuale, questa procedura consentirà la creazione di una nuova rete virtuale e di nuove connessioni ExpressRoute e VPN da sito a sito. Per configurare, eseguire i passaggi per[Creare una nuova rete virtuale con connessioni coesistenti](#create-a-new-vnet-with-coexisting-connections).

- **Configurare la rete virtuale esistente per le connessioni coesistenti:**

	E’ possibile che esista già una rete virtuale con una connessione VPN da sito a sito o una connessione ExpressRoute. La procedura [Configurare connessioni coesistenti per la rete virtuale già esistente](#configure-coexisting-connections-for-an-existing-vnet) illustra come eliminare il gateway e quindi come creare nuove connessioni ExpressRoute e VPN da sito a sito. Durante la creazione di nuove connessioni, è necessario completare i passaggi in un ordine molto specifico. Non usare le istruzioni in altri articoli per creare gateway e connessioni.

	In questa procedura, la creazione di connessioni che possono coesistere richiederà l’eliminazione del gateway e la configurazione di nuovi gateway. Mentre si elimina e si ricrea il gateway e le connessioni, si avrà un tempo di inattività per le connessioni cross-premise, ma non sarà necessario eseguire la migrazione delle macchine virtuali o dei servizi a una nuova rete virtuale. Le macchine virtuali e i servizi saranno comunque in grado di comunicare tramite il servizio di bilanciamento del carico mentre si configura il gateway, se sono configurati in questo senso.


### Note e limitazioni

- Tramite Azure non sarà possibile attivare il routing tra la rete locale connessa tramite la VPN da sito a sito e la rete locale connessa tramite ExpressRoute.
- Non è possibile abilitare connessioni VPN da punto a sito dalla stessa rete virtuale connessa a ExpressRoute. Le connessioni VPN da punto a sito ed ExpressRoute non possono coesistere per la stessa rete virtuale.
- Sia il gateway ExpressRoute che il gateway VPN da sito a sito devono essere SKU del gateway di tipo Standard o HighPerformance. Per informazioni sugli SKU del gateway, vedere [SKU del gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- Se la rete locale è connessa sia a ExpressRoute che a una VPN da sito a sito (scenario 1), per il routing della connessione VPN da sito a sito alla rete Internet pubblica è necessario che nella rete locale sia configurata una route statica.
- È necessario creare il gateway ExpressRoute prima di aggiungere il gateway VPN da sito a sito.
- Entrambe le procedure presuppongono la disponibilità di un circuito ExpressRoute già configurato. In caso contrario, vedere gli articoli seguenti:

	- [Configurare una connessione ExpressRoute tramite un provider di servizi di rete](expressroute-configuring-nsps.md)
	- [Configurare una connessione ExpressRoute mediante un provider di Exchange](expressroute-configuring-exps.md)


## Creare una nuova rete virtuale con connessioni coesistenti

Questa procedura illustra come creare una rete virtuale e creare connessioni Site-to-Site ed ExpressRoute coesistenti.

1. Verificare che sia disponibile l'ultima versione dei cmdlet PowerShell. È possibile installare i cmdlet PowerShell più recenti dalla sezione relativa a PowerShell della [pagina Download](http://azure.microsoft.com/downloads/).
2. Creare uno schema per la rete virtuale. Per maggiori informazioni sull'utilizzo del file di configurazione di rete, vedere [Configurare una rete virtuale usando un file di configurazione di rete](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal). Per altre informazioni sullo schema di configurazione, vedere [Schema di configurazione della rete virtuale di Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

	Quando si crea lo schema, assicurarsi di usare i valori seguenti:

	- La lunghezza del prefisso di rete della subnet del gateway per la rete virtuale deve essere 27 o inferiore (/ 26, /25, ecc.).
	- Il tipo di connessione del gateway è "Dedicato".

		      <VirtualNetworkSite name="MyAzureVNET" Location="Central US">
		        <AddressSpace>
		          <AddressPrefix>10.17.159.192/26</AddressPrefix>
		        </AddressSpace>
		        <Subnets>
		          <Subnet name="Subnet-1">
		            <AddressPrefix>10.17.159.192/27</AddressPrefix>
		          </Subnet>
		          <Subnet name="GatewaySubnet">
		            <AddressPrefix>10.17.159.224/27</AddressPrefix>
		          </Subnet>
		        </Subnets>
		        <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="MyLocalNetwork">
		              <Connection type="Dedicated" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>
		      </VirtualNetworkSite>



3. Dopo la creazione e configurazione del file di schema xml, caricare il file. Verrà creata la rete virtuale.

	Usare il cmdlet seguente per caricare il file, sostituendo il valore con uno personalizzato.

	`Set-AzureVNetConfig -ConfigurationPath 'C:\NetworkConfig.xml'`
4. Creare un gateway ExpressRoute. Specificare *Standard* o *HighPerformance* per GatewaySKU e *DynamicRouting* per GatewayType.

	Usare l'esempio di seguito, sostituendo i valori personalizzati.

	`New-AzureVNetGateway -VNetName MyAzureVNET -GatewayType DynamicRouting -GatewaySKU HighPerformance`

5. Collegare il gateway ExpressRoute al circuito ExpressRoute. Dopo aver completato questo passaggio, viene stabilita la connessione tra la rete locale e Azure tramite ExpressRoute.

	`New-AzureDedicatedCircuitLink -ServiceKey <service-key> -VNetName MyAzureVNET`

6. Creare quindi il gateway VPN da sito a sito. Il GatewaySKU deve essere *Standard* o *HighPerformance* e il GatewayType deve essere *DynamicRouting*.

	`New-AzureVirtualNetworkGateway -VNetName MyAzureVNET -GatewayName S2SVPN -GatewayType DynamicRouting -GatewaySKU  HighPerformance`

	Per recuperare le impostazioni del gateway di rete virtuale, inclusi l'ID del gateway e l'indirizzo IP pubblico, usare il cmdlet `Get-AzureVirtualNetworkGateway`.

		Get-AzureVirtualNetworkGateway

		GatewayId            : 348ae011-ffa9-4add-b530-7cb30010565e
		GatewayName          : S2SVPN
		LastEventData        :
		GatewayType          : DynamicRouting
		LastEventTimeStamp   : 5/29/2015 4:41:41 PM
		LastEventMessage     : Successfully created a gateway for the following virtual network: GNSDesMoines
		LastEventID          : 23002
		State                : Provisioned
		VIPAddress           : 104.43.x.y
		DefaultSite          :
		GatewaySKU           : HighPerformance
		Location             :
		VnetId               : 979aabcf-e47f-4136-ab9b-b4780c1e1bd5
		SubnetId             :
		EnableBgp            : False
		OperationDescription : Get-AzureVirtualNetworkGateway
		OperationId          : 42773656-85e1-a6b6-8705-35473f1e6f6a
		OperationStatus      : Succeeded

7. Creare un'entità gateway VPN del sito locale. Questo comando non configura il gateway VPN locale. Consente invece di fornire le impostazioni del gateway locale, ad esempio l'indirizzo IP pubblico e lo spazio indirizzi locale, in modo che il gateway VPN di Azure possa connettersi.

	> [AZURE.IMPORTANT]Il sito locale per la connessione VPN da sito a sito non è definito nel file netcfg. È invece necessario usare questo cmdlet per specificare i parametri del sito locale. Non è possibile definirlo tramite il portale di gestione o il file netcfg.

	Usare l'esempio seguente, sostituendo i valori con quelli personalizzati.

	`New-AzureLocalNetworkGateway -Gatewayname MyLocalNetwork -IpAddress <MyLocalGatewayIp> -AddressSpace <MyLocalNetworkAddress>`

	> [AZURE.IMPORTANT]Se la rete locale include più route, è possibile passarle tutte come una matrice. $MyLocalNetworkAddress = @("10.1.2.0/24","10.1.3.0/24","10.2.1.0/24")


	Per recuperare le impostazioni del gateway di rete virtuale, inclusi l'ID del gateway e l'indirizzo IP pubblico, usare il cmdlet `Get-AzureVirtualNetworkGateway`. Vedere l'esempio seguente.

		Get-AzureLocalNetworkGateway

		GatewayId            : 532cb428-8c8c-4596-9a4f-7ae3a9fcd01b
		GatewayName          : MyLocalNetwork
		IpAddress            : 23.39.x.y
		AddressSpace         : {10.1.2.0/24}
		OperationDescription : Get-AzureLocalNetworkGateway
		OperationId          : ddc4bfae-502c-adc7-bd7d-1efbc00b3fe5
		OperationStatus      : Succeeded


8. Configurare il dispositivo VPN locale per la connessione al nuovo gateway. Quando si configura il dispositivo VPN, usare le informazioni recuperate nel passaggio 6. Per altre informazioni sulla configurazione del dispositivo VPN, vedere la pagina relativa alla [configurazione del dispositivo VPN](http://go.microsoft.com/fwlink/p/?linkid=615099).


9. Collegare il gateway VPN da sito a sito in Azure al gateway locale.

	In questo esempio connectedEntityId è l'ID del gateway locale, che è possibile trovare eseguendo `Get-AzureLocalNetworkGateway`. È possibile trovare virtualNetworkGatewayId usando il cmdlet `Get-AzureVirtualNetworkGateway`. Dopo questo passaggio, viene stabilita la connessione tra la rete locale e Azure tramite la connessione VPN da sito a sito.


	`New-AzureVirtualNetworkGatewayConnection -connectedEntityId <local-network-gateway-id> -gatewayConnectionName Azure2Local -gatewayConnectionType IPsec -sharedKey abc123 -virtualNetworkGatewayId <azure-s2s-vpn-gateway-id>`


## Configurare connessioni coesistenti per una rete virtuale esistente

Se si ha una rete virtuale esistente, connessa tramite una connessione ExpressRoute o VPN da sito a sito, per abilitare entrambe le connessioni in modo da connettersi alla rete virtuale esistente, è necessario eliminare prima di tutto il gateway esistente. Ciò significa che le sedi locali perderanno la connessione alla rete virtuale tramite il gateway mentre si lavora a questa configurazione.

**Prima di iniziare la configurazione:** verificare che nella rete virtuale siano rimasti indirizzi IP sufficienti per aumentare le dimensioni della subnet del gateway.


1. Scaricare la versione più recente dei cmdlet di PowerShell. È possibile installare i cmdlet PowerShell più recenti dalla sezione relativa a PowerShell della [pagina Download](http://azure.microsoft.com/downloads/).

2. Eliminare il gateway VPN da sito a sito esistente. Usare il cmdlet seguente, sostituendo i valori con quelli personalizzati.

	`Remove-AzureVNetGateway –VnetName MyAzureVNET`

2. Esportare lo schema della rete virtuale. Usare il cmdlet PowerShell seguente, sostituendo i valori con quelli personalizzati.

	`Get-AzureVNetConfig –ExportToFile “C:\NetworkConfig.xml”`
3. Modificare lo schema del file di configurazione rete in modo che la subnet del gateway sia /27 o più breve (/26. /25, ecc.). Vedere l'esempio seguente. Per maggiori informazioni sull'utilizzo del file di configurazione di rete, vedere [Configurare una rete virtuale usando un file di configurazione di rete](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal). Per altre informazioni sullo schema di configurazione, vedere [Schema di configurazione della rete virtuale di Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).


          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.17.159.224/27</AddressPrefix>
          </Subnet>
4. Se il gateway precedente era una VPN da sito a sito, è necessario modificare anche il tipo di connessione su **Dedicato**.

		         <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="MyLocalNetwork">
		              <Connection type="Dedicated" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>
5. A questo punto, si avrà una rete virtuale senza gateway. È possibile procedere con il **Passaggio 3** nell'articolo, [Creare una nuova rete virtuale con connessioni coesistenti](#create-a-new-vnet-with-coexisting-connections) per creare nuovi gateway e completare le connessioni.



## Passaggi successivi

Altre informazioni su ExpressRoute. Vedere l'articolo [Panoramica tecnica relativa a ExpressRoute](expressroute-introduction.md).

Altre informazioni sui gateway VPN. Vedere [Informazioni sui gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

<!---HONumber=Sept15_HO4-->