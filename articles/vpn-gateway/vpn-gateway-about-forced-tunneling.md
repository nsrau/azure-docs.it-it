<properties pageTitle="Configurare il tunneling forzato per i gateway VPN tramite PowerShell | Microsoft Azure" description="Se si dispone di una rete virtuale con un gateway VPN cross-premise, è possibile reindirizzare o "forzare" tutto il traffico associato a Internet al sistema locale. Questo articolo si applica ai gateway VPN creati utilizzando il modello di distribuzione classica " services="vpn-gateway" documentationCenter="na" authors="cherylmc" manager="carolz" editor="" tags="azure-resource-manager"/>
<tags  
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/21/2015"
   ms.author="cherylmc" />

# È possibile configurare il tunneling forzato?

Questo articolo è applicabile alle reti virtuali e ai gateway VPN creati usando il modello di distribuzione classica. Se si desidera configurare il tunneling forzato per le reti virtuali e i gateway VPN creati utilizzando il modello di distribuzione di gestione risorse, vedere [configurare il tunneling forzato tramite PowerShell e Gestione risorse di Azure](vpn-gateway-forced-tunneling-rm.md).

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## Informazioni sul tunneling forzato

Il tunneling forzato consente di reindirizzare o "forzare" tutto il traffico associato a Internet verso la posizione locale tramite un tunnel VPN da sito a sito per l'ispezione e il controllo. Si tratta di un requisito di sicurezza critico per la maggior parte dei criteri IT aziendali. Senza il tunneling forzato, il traffico associato a Internet dalle macchine virtuali in Azure raggiungerà direttamente Internet attraversando sempre l'infrastruttura di rete di Azure, senza l'opzione che consente di ispezionare o controllare il traffico. L'accesso a Internet non autorizzato potrebbe provocare la divulgazione di informazioni o altri tipi di violazioni della sicurezza.

Nel diagramma seguente viene illustrato il funzionamento del tunneling forzato.

![Tunneling forzato](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

Nell'esempio riportato in precedenza, il tunneling della subnet front-end non viene forzato. I carichi di lavoro nella subnet front-end possono continuare ad accettare e a rispondere alle richieste dei clienti direttamente da Internet. Il tunneling delle subnet di livello intermedio e back-end viene forzato. Tutte le connessioni in uscita da queste due subnet a Internet verranno forzate o reindirizzate verso un sito locale tramite uno dei tunnel VPN S2S. Ciò consente di limitare e ispezionare l'accesso a Internet dalle macchine virtuali o dai servizi cloud in Azure, pur continuando ad abilitare l'architettura dei servizi multilivello richiesta. È inoltre possibile applicare il tunneling forzato a tutte le reti virtuali se non sono presenti carichi di lavoro con connessione Internet nelle reti virtuali.

## Problemi e considerazioni

Il tunneling forzato in Azure viene configurato tramite route definite dall’utente delle reti virtuali. Il reindirizzamento del traffico a un sito locale viene espresso come route predefinita al gateway VPN di Azure. Nella sezione seguente viene elencata la limitazione attuale della tabella di routing e delle route per una rete virtuale di Azure:



-  Ciascuna subnet della rete virtuale dispone di una tabella di routing di sistema integrata. La tabella di routing di sistema include i 3 gruppi di route seguenti:

	- **Route della rete virtuale locale:** direttamente alla destinazione di macchine virtuali nella stessa rete virtuale
	
	- **Route locale:** al gateway VPN di Azure
	
	- **Route predefinita:** direttamente a Internet. I pacchetti destinati agli indirizzi IP privati non rientranti nelle due route precedenti verranno eliminati.



-  Con il rilascio di route definite dall'utente, è possibile creare una tabella di routing per aggiungere una route predefinita, quindi associare la tabella di routing alle subnet della rete virtuale per abilitare il tunneling forzato su tali subnet.

- Il tunneling forzato deve essere associato a una rete virtuale che disponga di un gateway VPN (non un gateway statico). È necessario impostare un "sito predefinito" tra i siti locali cross-premise connessi alla rete virtuale.

- Il tunneling forzato ExpressRoute non viene configurato mediante questo meccanismo, ma è abilitato dai clienti annunciando una route predefinita tramite le sessioni di peering BGP ExpressRoute. Per altre informazioni, vedere la [documentazione di ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## Panoramica della configurazione

La procedura riportata di seguito consentirà di specificare il tunneling forzato in una rete virtuale. I passaggi di configurazione corrispondono all’esempio di file netcgf di rete virtuale riportato di seguito.

Nell'esempio, la rete virtuale multi-livello "MultiTier-VNet", dispone di 3 subnet (subnet *Frontend*, *Midtier* e *Backend*), con 4 connessioni cross-premise (*DefaultSiteHQ*) e 3 *Rami*. I passaggi seguenti consentiranno di impostare *DefaultSiteHQ* come connessione predefinita del sito per il tunneling forzato e di configurare le subnet *Midtier* e *Backend* per l’utilizzo del tunneling forzato.

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

### Prerequisiti

- Sottoscrizione di Azure

- Una rete virtuale configurata.

- La versione più recente dei cmdlet PowerShell di Azure che utilizzano il programma di installazione di Web Platform. È possibile scaricare e installare la versione più recente dalla sezione **Windows PowerShell** della [pagina Download](http://azure.microsoft.com/downloads/).

## È possibile configurare il tunneling forzato?

Utilizzare la procedura seguente per configurare il tunneling forzato.

1. Creare una tabella di routing. Utilizzare il cmdlet seguente per creare la tabella route.

		New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"

1. Aggiungere una route predefinita alla tabella di routing.

	Nell'esempio di cmdlet riportato di seguito viene aggiunta una route predefinita alla tabella di routing creata nel passaggio 1. L'unica route supportata è il prefisso di destinazione di "0.0.0.0/0" sul nexthop "VPNGateway".
 
		Set-AzureRoute –RouteTableName "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway

1. Associare la tabella di routing alle subnet.

	Dopo aver creato una tabella di routing e aggiunto una route, utilizzare il cmdlet riportato di seguito per aggiungere o associare la tabella route a una subnet della rete virtuale. Negli esempi riportati di seguito viene aggiunta una tabella route "MyRouteTable" alle subnet di livello intermedio e back-end della rete virtuale multilivello.

		Set-AzureSubnetRouteTable -VNetName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"

		Set-AzureSubnetRouteTable -VNetName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"

1. Assegnare un sito predefinito per il tunneling forzato.

	Nel passaggio precedente, gli script di cmdlet di esempio hanno consentito di creare la tabella route e di associarla a due delle subnet della rete virtuale. L'ultimo passaggio consiste nel selezionare un sito locale tra le connessioni multisito della rete virtuale come sito predefinito o tunnel.

		$DefaultSite = @("DefaultSiteHQ")
		Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"

## Ulteriori cmdlet di PowerShell

Di seguito sono riportasti alcuni altri cmdlet PowerShell che possono risultare utili quando si utilizzano le configurazioni di tunneling forzate.

**Per eliminare una tabella route:**

	Remove-AzureRouteTable -RouteTableName <routeTableName>

**Per elencare una tabella route:**

	Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]

**Per eliminare una route da una tabella route:**

	Remove-AzureRouteTable –Name <routeTableName>

**Per rimuovere una route da una subnet:**

	Remove-AzureSubnetRouteTable –VNetName <virtualNetworkName> -SubnetName <subnetName>

**Per elencare la tabella route associata a una subnet:**
	
	Get-AzureSubnetRouteTable -VNetName <virtualNetworkName> -SubnetName <subnetName>

**Per rimuovere un sito predefinito da un gateway VPN della rete virtuale:**

	Remove-AzureVnetGatewayDefaultSites -VNetName <virtualNetworkName>

<!---HONumber=AcomDC_1125_2015-->