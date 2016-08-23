<properties 
   pageTitle="Configurare il tunneling forzato per connessioni da sito a sito con il modello di distribuzione classico | Microsoft Azure"
   description="Come reindirizzare o forzare tutto il traffico associato a Internet verso il percorso locale."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/10/2016"
   ms.author="cherylmc" />  

# Configurare il tunneling forzato con il modello di distribuzione classico

> [AZURE.SELECTOR]
- [PowerShell - Classico](vpn-gateway-about-forced-tunneling.md)
- [PowerShell - Gestione risorse](vpn-gateway-forced-tunneling-rm.md)

Il tunneling forzato consente di reindirizzare o "forzare" tutto il traffico associato a Internet verso la posizione locale tramite un tunnel VPN da sito a sito per l'ispezione e il controllo. Si tratta di un requisito di sicurezza critico per la maggior parte dei criteri IT aziendali.

Senza il tunneling forzato, il traffico associato a Internet dalle macchine virtuali in Azure raggiungerà direttamente Internet attraversando sempre l'infrastruttura di rete di Azure, senza l'opzione che consente di ispezionare o controllare il traffico. L'accesso a Internet non autorizzato potrebbe provocare la divulgazione di informazioni o altri tipi di violazioni della sicurezza.

Questo articolo illustra la configurazione del tunneling forzato per le reti virtuali create usando il modello di distribuzione classico.

**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

**Strumenti e modelli di distribuzione per il tunneling forzato**

Una connessione di tunneling forzato può essere configurata sia per il modello di distribuzione classico che per il modello di distribuzione di Resource Manager. Per altre informazioni, vedere la tabella seguente. La tabella verrà aggiornata man mano che per questa configurazione risultano disponibili nuovi articoli, modelli di distribuzione e strumenti. Quando un articolo risulterà disponibile, nella tabella sarà presente un collegamento diretto.

[AZURE.INCLUDE [vpn-gateway-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)]


## Problemi e considerazioni

Il tunneling forzato in Azure viene configurato tramite route di rete virtuale definite dall'utente. Il reindirizzamento del traffico a un sito locale viene espresso come route predefinita al gateway VPN di Azure. Nella sezione seguente viene elencata la limitazione attuale della tabella di routing e delle route per una rete virtuale di Azure:


-  Ciascuna subnet della rete virtuale dispone di una tabella di routing di sistema integrata. La tabella di routing di sistema include i tre gruppi di route seguenti:

	- **Route della rete virtuale locale:** direttamente alla destinazione di macchine virtuali nella stessa rete virtuale
	
	- **Route locale:** al gateway VPN di Azure
	
	- **Route predefinita:** direttamente a Internet. I pacchetti destinati agli indirizzi IP privati non rientranti nelle due route precedenti verranno eliminati.


-  Con il rilascio di route definite dall'utente, è possibile creare una tabella di routing per aggiungere una route predefinita, quindi associare la tabella di routing alle subnet della rete virtuale per abilitare il tunneling forzato su tali subnet.

- È necessario impostare un "sito predefinito" tra i siti locali cross-premise connessi alla rete virtuale.

- Il tunneling forzato deve essere associato a una rete virtuale che disponga di un gateway VPN (non un gateway statico).
 
- Il tunneling forzato ExpressRoute non viene configurato mediante questo meccanismo, ma è abilitato annunciando una route predefinita tramite le sessioni di peering BGP ExpressRoute. Per altre informazioni, vedere la [documentazione di ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).



## Panoramica della configurazione

Nell'esempio seguente il tunneling della subnet front-end non viene forzato. I carichi di lavoro nella subnet front-end possono continuare ad accettare e a rispondere alle richieste dei clienti direttamente da Internet. Il tunneling delle subnet di livello intermedio e back-end viene forzato. Tutte le connessioni in uscita da queste due subnet a Internet verranno forzate o reindirizzate verso un sito locale tramite uno dei tunnel VPN S2S.

Ciò consente di limitare e ispezionare l'accesso a Internet dalle macchine virtuali o dai servizi cloud in Azure, pur continuando ad abilitare l'architettura dei servizi multilivello richiesta. È anche possibile applicare il tunneling forzato a tutte le reti virtuali se non sono presenti carichi di lavoro con connessione Internet nelle reti virtuali.


![Tunneling forzato](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)  



## Prima di iniziare

Prima di iniziare la configurazione, verificare che ci siano le condizioni seguenti:

- Una sottoscrizione di Azure. Se non è disponibile una sottoscrizione di Azure, è possibile attivare i [vantaggi dell'abbonamento a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).

- Una rete virtuale configurata.

- La versione più recente dei cmdlets di Azure PowerShell. Per altre informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).


## È possibile configurare il tunneling forzato?

La procedura seguente consentirà di specificare il tunneling forzato per una rete virtuale. I passaggi di configurazione corrispondono al file di configurazione della rete virtuale.



	<VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
	</VirtualNetworkSite>

In questo esempio la rete virtuale "MultiTier-VNet" include tre subnet (*Frontend*, *Midtier* e *Backend*) con quattro connessioni di più sedi locali (*DefaultSiteHQ*) e tre *Branch*.

La procedura consente di impostare *DefaultSiteHQ* come connessione predefinita del sito per il tunneling forzato e di configurare le subnet Midtier e Backend per l'uso del tunneling forzato.


1. Creare una tabella di routing. Utilizzare il cmdlet seguente per creare la tabella route.

		New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"

2. Aggiungere una route predefinita alla tabella di routing.

	L'esempio seguente aggiunge una route predefinita alla tabella di routing creata nel passaggio 1. Notare che l'unica route supportata è il prefisso di destinazione di "0.0.0.0/0" su NextHop "VPNGateway".
 
		Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway

3. Associare la tabella di routing alle subnet.

	Dopo aver creato una tabella di routing e aggiunto una route, usare l'esempio seguente per aggiungere o associare la tabella di route a una subnet della rete virtuale. L'esempio aggiunge la tabella di route "MyRouteTable" alle subnet Midtier e Backend della rete virtuale MultiTier-VNet.

		Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"

		Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"

4. Assegnare un sito predefinito per il tunneling forzato.

	Nel passaggio precedente, gli script di cmdlet di esempio hanno consentito di creare la tabella route e di associarla a due delle subnet della rete virtuale. L'ultimo passaggio consiste nel selezionare un sito locale tra le connessioni multisito della rete virtuale come sito predefinito o tunnel.

		$DefaultSite = @("DefaultSiteHQ")
		Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"

## Ulteriori cmdlet di PowerShell

### Per eliminare una tabella route

	Remove-AzureRouteTable -Name <routeTableName>

### Per elencare una tabella route

	Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]

### Per eliminare una route da una tabella route

	Remove-AzureRouteTable –Name <routeTableName>

### Per rimuovere una route da una subnet

	Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>

### Per elencare la tabella route associata a una subnet
	
	Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>

### Per rimuovere un sito predefinito da un gateway VPN della rete virtuale

	Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>

<!---HONumber=AcomDC_0810_2016-->