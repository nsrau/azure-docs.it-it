<properties 
   pageTitle="Connettere più siti locali a una rete virtuale mediante un gateway VPN"
   description="Questo articolo descrive la procedura per connettere più siti locali a una rete virtuale mediante un gateway VPN per il modello di distribuzione classica."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/17/2015"
   ms.author="yushwang" />

# Connettere più siti locali a una rete virtuale

Questo articolo riguarda la connessione di più siti locali a una rete virtuale creata mediante il modello di distribuzione classica (noto anche come Gestione dei servizi).

**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]Quando sarà disponibile un articolo con la procedura da seguire per le reti virtuali create mediante il modello di Gestione risorse, in questa pagina reso disponibile un collegamento a esso.

## Informazioni sulla connessione

È possibile connettere più siti locali a una singola rete virtuale. Ciò è particolarmente interessante per la creazione di soluzioni per cloud ibride. La creazione di una connessione multisito al gateway della rete virtuale di Azure è molto simile alla creazione di altre connessioni da sito a sito. È infatti possibile usare un gateway VPN di Azure esistente, purché il gateway sia dinamico (basato su route).

Se è già presente un gateway statico connesso alla rete virtuale, è possibile modificare il tipo di gateway in dinamico senza dover ricreare la rete virtuale per adattarla a più siti. Prima di modificare il tipo di routing, assicurarsi che il gateway VPN locale supporti le configurazioni VPN basate su route.

![VPN multisito](./media/vpn-gateway-multi-site/IC727363.png)

## Elementi da considerare:

**Non sarà possibile usare il portale di Azure classico per apportare modifiche a questa rete virtuale.** Per questa versione è necessario apportare modifiche al file di configurazione di rete invece di usare il portale di Azure classico. Se si apportano modifiche nel portale di Azure classico, queste sovrascriveranno le impostazioni di riferimento multisito per questa rete virtuale. Dovrebbe risultare piuttosto semplice usando il file di configurazione di rete nel momento in cui è stata completata la procedura multisito. Tuttavia, se si ci sono più persone che lavorano alla configurazione di rete, è necessario verificare che tutti siano a conoscenza di questa limitazione. Ciò non significa che non sia possibile usare il portale di Azure classico. È possibile usarlo per qualsiasi operazione, ad eccezione delle modifiche di configurazione a questa particolare rete virtuale.

## Prima di iniziare

Prima di iniziare la configurazione, verificare ci siano le condizioni seguenti:

- Una sottoscrizione di Azure. Se non si dispone già di una sottoscrizione di Azure, è possibile attivare i [benefici della sottoscrizione MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Hardware VPN compatibile per ogni sede locale. Controllare le [Informazioni sui dispositivi VPN per le connessioni di rete virtuale da sito a sito](http://go.microsoft.com/fwlink/p/?linkid=615099) per verificare se il dispositivo che si intende usare è ritenuto compatibile.

- Un indirizzo IP IPv4 pubblico esterno per ogni dispositivo VPN. L’indirizzo IP non può trovarsi dietro un NAT. Questo è un requisito.

- La versione più recente dei cmdlets di Azure PowerShell. È possibile scaricare e installare la versione più recente dalla sezione Windows PowerShell della [Pagina di download](https://azure.microsoft.com/downloads/).

- Una persona esperta nella configurazione di hardware VPN. Non sarà possibile usare gli script VPN generati automaticamente dal portale di Azure classico per configurare i propri dispositivi VPN. Ciò significa che è necessario avere una conoscenza approfondita di come configurare il proprio dispositivo VPN oppure lavorare insieme una persona esperta in questo campo.

- Gli intervalli di indirizzi IP da usare per la propria rete virtuale (se non ne hai già creata una).

- Gli intervalli di indirizzi IP per ogni sito della rete locale a cui si effettuerà la connessione. È necessario verificare che gli intervalli di indirizzi IP per ogni sito della rete locale a cui ci si vuole connettere non si sovrappongano. In caso contrario, il portale di Azure classico o l'API REST rifiuteranno il caricamento della configurazione. Se, ad esempio, ci sono due siti della rete locale che entrambi contengono l'intervallo di indirizzo IP 10.2.3.0/24 e si ha a disposizione un pacchetto con un indirizzo di destinazione 10.2.3.3, Azure non saprà a quale sito si vuole inviare il pacchetto perché gli intervalli degli indirizzi si sovrappongono. Per evitare problemi di routing, Azure non consente di caricare un file di configurazione con intervalli che si sovrappongono.

## Creare la rete virtuale e il gateway

1. **Creare una VPN da sito a sito con un gateway di routing dinamico (basato su route).** Se già ne avete una, È possibile procedere e iniziare a [Esportare le impostazioni di configurazione della rete virtuale](#export). Se non ne avete una, eseguire le operazioni seguenti:

	**Se si dispone di una rete virtuale da sito a sito, ma con un gateway con routing statico (basato su criteri):** Passaggio 1: modificare il tipo di gateway in routing dinamico. Una VPN multisito richiede un gateway di routing dinamico. Per modificare il tipo di gateway, è necessario prima eliminare quello esistente e poi crearne uno nuovo. Per istruzioni, vedere la pagina relativa alla [modifica di un tipo di routing del gateway VPN](vpn-gateway-configure-vpn-gateway-mp.md/#how-to-change-your-vpn-gateway-type). Passaggio 2: configurare il nuovo gateway e creare il proprio tunnel VPN. Per istruzioni, vedere [Configurare un gateway VPN nel portale di Azure classico](vpn-gateway-configure-vpn-gateway-mp.md).Innanzitutto, modificare il tipo di gateway in routing dinamico.

	**Se si dispone di una rete virtuale da sito a sito:** Passaggio 1: creare una rete virtuale da sito a sito usando le istruzioni seguenti: [Creare una rete virtuale con una connessione VPN da sito a sito con il portale di Azure classico](vpn-gateway-site-to-site-create.md). Passaggio 2: configurare un gateway di routing dinamico usando le istruzioni seguenti: [Configurare un gateway VPN nel portale di Azure classico](vpn-gateway-configure-vpn-gateway-mp.md). Assicurarsi di selezionare **routing dinamico** per il tipo di gateway.

2. **<a name="export"></a>Esportare le impostazioni di configurazione della rete virtuale.** Per esportare il file di configurazione di rete, vedere le istruzioni [per esportare le impostazioni di rete](../virtual-network/virtual-networks-using-network-configuration-file.md). Il file esportato verrà usato per configurare le nuove impostazioni multisito.

3. **Aprire il file di configurazione di rete.** Aprire il file di configurazione di rete scaricato nell'ultimo passaggio. Usare qualsiasi editor xml desiderato. Il codice dovrebbe essere simile a quello riportato di seguito:

		<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
		  <VirtualNetworkConfiguration>
		    <LocalNetworkSites>
		      <LocalNetworkSite name="Site1">
		        <AddressSpace>
		          <AddressPrefix>10.0.0.0/16</AddressPrefix>
		          <AddressPrefix>10.1.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
		      </LocalNetworkSite>
		      <LocalNetworkSite name="Site2">
		        <AddressSpace>
		          <AddressPrefix>10.2.0.0/16</AddressPrefix>
		          <AddressPrefix>10.3.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
		      </LocalNetworkSite>
		    </LocalNetworkSites>
		    <VirtualNetworkSites>
		      <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
		        <AddressSpace>
		          <AddressPrefix>10.20.0.0/16</AddressPrefix>
		          <AddressPrefix>10.21.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <Subnets>
		          <Subnet name="FE">
		            <AddressPrefix>10.20.0.0/24</AddressPrefix>
		          </Subnet>
		          <Subnet name="BE">
		            <AddressPrefix>10.20.1.0/24</AddressPrefix>
		          </Subnet>
		          <Subnet name="GatewaySubnet">
		            <AddressPrefix>10.20.2.0/29</AddressPrefix>
		          </Subnet>
		        </Subnets>
		        <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="Site1">
		              <Connection type="IPsec" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>
		      </VirtualNetworkSite>
		    </VirtualNetworkSites>
		  </VirtualNetworkConfiguration>
		</NetworkConfiguration>

4. **Aggiungere riferimenti a più siti al file di configurazione di rete**. Quando si aggiungono o rimuovono le informazioni di riferimento ai siti, si apporteranno modifiche alla configurazione in ConnectionsToLocalNetwork/LocalNetworkSiteRef. L'aggiunta di un nuovo riferimento a siti locali induce Azure a creare un nuovo tunnel. Nell'esempio seguente, la configurazione di rete è per una connessione a un singolo sito.

		<Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

	Per aggiungere riferimenti ad altri siti (creare una configurazione multisito), aggiungere semplicemente le righe "LocalNetworkSiteRef", come illustrato nell'esempio seguente:

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

5. **Salvare il file di configurazione di rete e importarlo.** Per importare il file di configurazione di rete, vedere le istruzioni [per importare le impostazioni di rete](../virtual-network/../virtual-network/virtual-networks-using-network-configuration-file.md#export-and-import-virtual-network-settings-using-the-management-portal). Quando si importa questo file con le modifiche, verranno aggiunti i nuovi tunnel. I tunnel useranno il gateway dinamico creato in precedenza.

6. **Scaricare le chiavi condivise per i tunnel VPN.** Una volta che sono stati aggiunti i nuovi tunnel, usare i cmdlet di PowerShell Get-AzureVNetGatewayKey per ottenere le chiavi precondivise IPsec/IKE per ogni tunnel.

	Ad esempio:

		Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"

		Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"

	Se si preferisce, è possibile usare anche l'API REST *Ottenere la chiave condivisa del gateway di rete virtuale* per ottenere le chiavi precondivise.

## Verificare le connessioni

**Verificare lo stato del tunnel multisito.** Dopo aver scaricato le chiavi per ogni tunnel, è opportuno verificare le connessioni. Usare *Get-AzureVnetConnection* per ottenere un elenco di tunnel di reti virtuali, come illustrato nell'esempio seguente. VNet1 è il nome della rete virtuale.

		Get-AzureVnetConnection -VNetName VNET1
		
		ConnectivityState         : Connected
		EgressBytesTransferred    : 661530
		IngressBytesTransferred   : 519207
		LastConnectionEstablished : 5/2/2014 2:51:40 PM
		LastEventID               : 23401
		LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
		LastEventTimeStamp        : 5/2/2014 2:51:40 PM
		LocalNetworkSiteName      : Site1
		OperationDescription      : Get-AzureVNetConnection
		OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
		OperationStatus           : Succeeded
		
		ConnectivityState         : Connected
		EgressBytesTransferred    : 789398
		IngressBytesTransferred   : 143908
		LastConnectionEstablished : 5/2/2014 3:20:40 PM
		LastEventID               : 23401
		LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
		LastEventTimeStamp        : 5/2/2014 2:51:40 PM
		LocalNetworkSiteName      : Site2
		OperationDescription      : Get-AzureVNetConnection
		OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
		OperationStatus           : Succeeded

## Passaggi successivi

Per altre informazioni sui gateway VPN, vedere [Informazioni sui gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

<!---HONumber=AcomDC_0427_2016-->