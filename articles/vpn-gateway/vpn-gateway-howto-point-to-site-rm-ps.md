<properties 
   pageTitle="Configurare una connessione VPN da punto a sito a una rete virtuale usando il modello di distribuzione di Gestione risorse | Microsoft Azure"
   description="Connettersi in modo sicuro alla rete virtuale di Azure attraverso la creazione di una connessione VPN da punto a sito."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="cherylmc" />

# Configurare una connessione da punto a sito a una rete virtuale con PowerShell

> [AZURE.SELECTOR]
- [PowerShell - Gestione risorse](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Portale - Classica](vpn-gateway-point-to-site-create.md)

Una configurazione da punto a sito (P2S) consente di creare una connessione sicura da un singolo computer client a una rete virtuale. Una soluzione P2S è utile quando ci si vuole connettere alla rete virtuale da una posizione remota, ad esempio da casa o durante una riunione, oppure quando solo pochi client devono connettersi a una rete virtuale.

Le connessioni da punto a sito non richiedono un dispositivo VPN o un indirizzo IP pubblico per funzionare. Una connessione VPN viene stabilita avviando la connessione dal computer client. Per altre informazioni sulle connessioni da punto a sito, vedere [Domande frequenti sul gateway VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) e [Pianificazione e progettazione per il gateway VPN](vpn-gateway-plan-design.md).

Questo articolo si applica alle connessioni da punto a sito a una rete virtuale creata con il modello di distribuzione Resource Manager. La procedura descritta in questo articolo usa PowerShell.

**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

**Strumenti e modelli di distribuzione per le connessioni da punto a sito**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

![Diagramma da punto a sito](./media/vpn-gateway-point-to-site-create/point2site.png "da punto a sito")


## Informazioni sulla configurazione

In questo scenario si creerà una rete virtuale con una connessione da punto a sito. Una connessione P2S è costituita dagli elementi seguenti: rete virtuale con un gateway, un file con estensione cer del certificato radice, un certificato client e la configurazione VPN sul lato client.

Per questa configurazione si useranno i valori seguenti:

- Nome: **VNet1** con spazi indirizzi **192.168.0.0/16** e **10.254.0.0/16**. Si noti che è possibile usare più di uno spazio degli indirizzi per una rete virtuale.
- Nome subnet: **FrontEnd** con **192.168.1.0/24**
- Nome subnet: **BackEnd** con **10.254.1.0/24**
- Nome subnet: **GatewaySubnet** con **192.168.200.0/24**. Il nome subnet *GatewaySubnet* è obbligatorio per il funzionamento del gateway.
- Pool di indirizzi client VPN: **172.16.201.0/24**. I client VPN che si connettono alla rete virtuale tramite questa connessione da punto a sito ricevono un indirizzo IP da questo pool.
- Sottoscrizione: verificare di avere la sottoscrizione corretta se si dispone di più di una sottoscrizione.
- Gruppo di risorse: **TestRG**
- Località: **Stati Uniti orientali**
- Server DNS: **indirizzo IP** del server DNS che si vuole usare per la risoluzione dei nomi.
- Nome gateway: **GW**
- Nome IP pubblico: **GWIP**
- VpnType: **RouteBased**


## Prima di iniziare

- Verificare di possedere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi dell'abbonamento a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).
	
- Installare i cmdlet di PowerShell per Azure Resource Manager (versione 1.0.2 o successiva). Per altre informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

## Configurare una connessione da punto a sito per Azure

1. Nella console di PowerShell accedere all'account Azure. Il cmdlet richiederà le credenziali di accesso per l'account Azure. Dopo l'accesso, vengono scaricate le impostazioni dell'account in modo che siano disponibili per Azure PowerShell.

		Login-AzureRmAccount 

2. Ottenere un elenco delle sottoscrizioni di Azure.

		Get-AzureRmSubscription

3. Specificare la sottoscrizione da usare.

		Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. In questa configurazione vengono dichiarate le variabili di PowerShell seguenti con i valori da usare. I valori dichiarati saranno usati negli script di esempio. Dichiarare i valori che da usare. Usare l'esempio seguente, sostituendo i valori con quelli personalizzati, se necessario.

		$VNetName  = "VNet1"
		$FESubName = "FrontEnd"
		$BESubName = "Backend"
		$GWSubName = "GatewaySubnet"
		$VNetPrefix1 = "192.168.0.0/16"
		$VNetPrefix2 = "10.254.0.0/16"
		$FESubPrefix = "192.168.1.0/24"
		$BESubPrefix = "10.254.1.0/24"
		$GWSubPrefix = "192.168.200.0/26"
		$VPNClientAddressPool = "172.16.201.0/24"
		$RG = "TestRG"
		$Location = "East US"
		$DNS = "8.8.8.8"
		$GWName = "GW"
		$GWIPName = "GWIP"
		$GWIPconfName = "gwipconf"
    	$P2SRootCertName = "ARMP2SRootCert.cer"

5. Creare un nuovo gruppo di risorse.

		New-AzureRmResourceGroup -Name $RG -Location $Location

6. Creare le configurazioni delle subnet per la rete virtuale, denominandole *FrontEnd*, *BackEnd* e *GatewaySubnet*. Questi prefissi devono fare parte dello spazio indirizzi della rete virtuale dichiarato in precedenza.

		$fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
		$besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
		$gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix

7. Creare la rete virtuale. Il server DNS specificato deve essere un server DNS in grado di risolvere i nomi per le risorse a cui ci si connette. Per questo esempio è stato usato un indirizzo IP pubblico. Assicurarsi di usare valori personalizzati.
	
		New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS

8. Specificare le variabili per la rete virtuale creata.

		$vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

9. Richiedere un indirizzo IP pubblico assegnato dinamicamente. Questo indirizzo IP è necessario per il corretto funzionamento del gateway. In un secondo momento si connetterà il gateway alla configurazione IP del gateway.
		
		$pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
		$ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
		
10. Aggiungere un certificato attendibile in Azure. È possibile aggiungere fino a 20 certificati. Per istruzioni sulla creazione di un certificato radice autofirmato con *makecert*, vedere [Usare i certificati radice autofirmati per le configurazioni da punto a sito](vpn-gateway-certificates-point-to-site.md). L'aggiunta di un file con codifica Base64 X.509 (estensione CER) consente ad Azure di considerare attendibile il certificato radice che il file rappresenta.

	Per ottenere la chiave pubblica, esportare il certificato come file con codifica Base64 X.509 (estensione CER). Prendere nota del percorso in cui si è esportato il file con estensione CER. Ecco un esempio di come ottenere la rappresentazione di stringa Base64 del certificato. Per questo passaggio è necessario usare il proprio percorso del file con estensione CER.
    
		$filePathForCert = "pasteYourCerFilePathHere"
		$cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
		$CertBase64 = [system.convert]::ToBase64String($cert.RawData)
		$p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64


11. Creare il gateway di rete virtuale per la rete virtuale. *-GatewayType* deve essere **Vpn** e *-VpnType* deve essere **RouteBased**.

		New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
		-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
		-VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
		-VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## Configurazione del client

Ogni client che si connette ad Azure tramite una connessione da punto a sito deve rispettare due requisiti: il client VPN deve essere configurato per la connessione e deve avere un certificato client installato. I pacchetti di configurazione del client VPN sono disponibili per i client Windows. Per altre informazioni, vedere [Domande frequenti sul gateway VPN](vpn-gateway-vpn-faq.md#point-to-site-connections).

1. Scaricare il pacchetto di configurazione del client VPN. In questo passaggio usare l'esempio seguente per scaricare il pacchetto di configurazione del client.

		Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
		-VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64

	Il cmdlet di PowerShell restituisce un collegamento URL. Copiare e incollare il collegamento restituito in un Web browser per scaricare il pacchetto nel computer. Ecco un esempio dell'aspetto dell'URL restituito.

    	"https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"
	
2. Generare e installare i certificati client (*.pfx) creati dal certificato radice nei computer client. È possibile usare qualsiasi metodo di installazione con cui si ha maggiore familiarità. Se si usa un certificato radice autofirmato e si ha scarsa familiarità con le procedure per eseguire questa operazione, è possibile fare riferimento all'articolo [Usare i certificati radice autofirmati per le configurazioni da punto a sito](vpn-gateway-certificates-point-to-site.md).

3. Per connettersi alla rete virtuale, nel computer client passare alle connessioni VPN e individuare quella creata, che ha lo stesso nome della rete virtuale locale. Fare clic su **Connect**. È possibile che venga visualizzato un messaggio popup che fa riferimento all'uso del certificato. In questo caso, fare clic su **Continua** per usare privilegi elevati.

4. Nella pagina **Stato connessione** fare clic su **Connetti** per avviare la connessione. Se viene visualizzato un **Seleziona certificato** dello schermo, verificare che il certificato client visualizzato sia quello che si desidera utilizzare per la connessione. In caso contrario, usare la freccia a discesa per selezionare il certificato corretto e quindi fare clic su **OK**.

5. Verrà stabilita la connessione. È possibile verificarla con la procedura seguente.

## Verificare la connessione

1. Per verificare che la connessione VPN è attiva, aprire un prompt dei comandi con privilegi elevati ed eseguire *ipconfig/all*.

2. Visualizzare i risultati. Si noti che l'indirizzo IP ricevuto è uno degli indirizzi compresi nel pool di indirizzi del client VPN da punto a sito specificato al momento della configurazione. I risultati dovrebbero essere simili a quanto segue:
    
		PPP adapter VNet1:
			Connection-specific DNS Suffix .:
			Description.....................: VNet1
			Physical Address................:
			DHCP Enabled....................: No
			Autoconfiguration Enabled.......: Yes
			IPv4 Address....................: 172.16.201.3(Preferred)
			Subnet Mask.....................: 255.255.255.255
			Default Gateway.................:
			NetBIOS over Tcpip..............: Enabled

## Per aggiungere o rimuovere un certificato radice attendibile

I certificati vengono usati per autenticare client VPN per VPN da punto a sito. I passaggi seguenti illustrano in dettaglio come aggiungere e rimuovere certificati radice. L'aggiunta di un file con codifica Base64 X.509 (estensione CER) consente ad Azure di considerare attendibile il certificato radice che il file rappresenta.

### Aggiunta di un certificato radice attendibile

In Azure è possibile aggiungere fino a 20 file CER di certificato radice trusted. Attenersi alla procedura seguente per aggiungere un certificato radice.

1. Creare e preparare il nuovo certificato che sarà aggiunto in Azure.

		$P2SRootCertName2 = "ARMP2SRootCert2.cer"
		$MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

2. Aggiungere il nuovo certificato radice. È possibile aggiungere solo un certificato alla volta.

		Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2

3. È possibile verificare che il nuovo certificato sia stato aggiunto correttamente mediante il cmdlet seguente.

		Get-AzureRmVpnClientRootCertificate -ResourceGroupName $RG `
		-VirtualNetworkGatewayName $GWName

### Rimozione di un certificato radice attendibile

È possibile rimuovere un certificato radice attendibile da Azure. Quando si rimuove un certificato attendibile, i certificati client generati dal certificato radice non potranno più connettersi ad Azure tramite una connessione da punto a sito. Per consentire ai client di connettersi, sarà necessario installare un nuovo certificato client generato da un certificato considerato attendibile in Azure.

1. Per rimuovere un certificato radice trusted, modificare l'esempio seguente.

		Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

 
2. Usare il cmdlet seguente per verificare che il certificato sia stato rimosso correttamente.

		Get-AzureRmVpnClientRootCertificate -ResourceGroupName $RG -VirtualNetworkGatewayName $GWName

## Gestire l'elenco dei certificati client revocati

È possibile revocare i certificati client. L'elenco di revoche di certificati consente di negare in modo selettivo la connettività da punto a sito basata sui singoli certificati client. Se si rimuove un file CER del certificato radice da Azure, viene revocato l'accesso per tutti i certificati client generati o firmati dal certificato radice revocato. Se necessario, è possibile revocare un certificato client specifico, non radice. In questo modo, gli altri certificati generati dal certificato radice rimarranno validi. La regola generale è quella di usare il certificato radice per gestire l'accesso a livello di team o organizzazione, usando i certificati client revocati per il controllo di accesso con granularità fine su singoli utenti.

### Revocare un certificato client

1. Ottenere l'identificazione personale del certificato client da revocare.

		$RevokedClientCert1 = "ClientCert1"
		$RevokedThumbprint1 = "‎ef2af033d0686820f5a3c74804d167b88b69982f"

2. Aggiungere l'identificazione personale all'elenco delle identificazioni personali revocate.

		Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
		-VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

3. Verificare che l'identificazione personale sia stata aggiunta all'elenco di revoche di certificati. È necessario aggiungere un'identificazione personale alla volta.

		Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### Reintegrare un certificato client

È possibile reintegrare un certificato client rimuovendo l'identificazione personale dall'elenco dei certificati client revocati.

1.  Rimuovere l'identificazione personale dall'elenco delle identificazioni personali dei certificati client revocate.

		Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
		-VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

2. Verificare che l'identificazione personale sia stata rimossa dall'elenco di quelle revocate.

		Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## Passaggi successivi

È possibile aggiungere una macchina virtuale alla rete virtuale. Per i passaggi, vedere [Creare una macchina virtuale](../virtual-machines/virtual-machines-windows-hero-tutorial.md).

<!---HONumber=AcomDC_0817_2016-->