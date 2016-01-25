<properties 
   pageTitle="Configurare una connessione gateway VPN da punto a sito a una rete virtuale di Azure | Microsoft Azure"
   description="Connettersi in modo sicuro alla rete virtuale di Azure attraverso la creazione di una connessione VPN da punto a sito. Questa configurazione risulta utile quando è necessaria una connessione cross-premise da una posizione remota senza usare un dispositivo VPN e può essere usata con le configurazioni di rete ibride. Questo articolo contiene istruzioni di PowerShell per le reti virtuali create con il modello di distribuzione di Gestione risorse."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/11/2016"
   ms.author="cherylmc" />

# Configurare una connessione da punto a sito a una rete virtuale con PowerShell

> [AZURE.SELECTOR]
- [PowerShell - Resource Manager](vpn-gateway-howto-point-to-site-rm-ps.md)
- [PowerShell - Classic](vpn-gateway-point-to-site-create.md)

Una configurazione da punto a sito consente di creare una singola connessione sicura alla rete virtuale da un computer client. Una connessione VPN viene stabilita avviando la connessione dal computer client. La configurazione da punto a sito è la soluzione ideale quando ci si vuole connettere alla rete virtuale da una posizione remota, ad esempio da casa o durante una riunione, oppure quando solo pochi client devono connettersi a una rete virtuale. Le connessioni da punto a sito non richiedono un dispositivo VPN o un indirizzo IP pubblico per funzionare. Per altre informazioni sulle connessioni da punto a sito, vedere [Domande frequenti sul gateway VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) e [Informazioni sulle connessioni cross-premise](vpn-gateway-cross-premises-options.md).

Questo articolo si applica alle reti virtuali e ai gateway VPN creati con il modello di distribuzione di **Gestione risorse di Azure**. Per configurare una connessione da punto a sito per una rete virtuale creata con Gestione dei servizi, noto anche come modello di distribuzione classica, vedere [questo articolo](vpn-gateway-point-to-site-create.md).

[AZURE.INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Informazioni sulla configurazione

In questo scenario si creerà una rete virtuale con una connessione da punto a sito. Una connessione da punto a sito è costituita dagli elementi seguenti: rete virtuale con un gateway, un file con estensione cer del certificato radice caricato, un certificato client e la configurazione VPN sul lato client.

Per questa configurazione si useranno i valori seguenti:

- Nome = **TestVNet** con spazi degli indirizzi 192.168.0.0/16 e 10.254.0.0/16. Si noti che è possibile usare più di uno spazio degli indirizzi per una rete virtuale.
- Nome subnet = **FrontEnd** con 192.168.1.0/24
- Nome subnet = **BackEnd** con 10.254.1.0/24
- Nome subnet = **GatewaySubnet** con 192.168.200.0/24 Il nome della subnet *GatewaySubnet* è obbligatorio per il funzionamento del gateway. 
- Pool di indirizzi client VPN: 172.16.201.0/24. I client VPN che si connettono alla rete virtuale tramite questa connessione da punto a sito riceveranno un indirizzo IP da questo pool.
- Sottoscrizione = verificare di avere la sottoscrizione corretta se ne è disponibile più d'una.
- Gruppo di risorse = **TestRG**
- Località: **East US**
- Server DNS= indirizzo IP del server da usare per la risoluzione dei nomi.
- Nome GW = **GW**
- Nome IP pubblico = **GWIP**
- VpnType = **RouteBased**


## Prima di iniziare

Verificare di avere una sottoscrizione di Azure e avere installato il cmdlet di Azure PowerShell richiesto per questa configurazione \(versione 1.0.2 o successiva\). Se non si dispone già di una sottoscrizione di Azure, è possibile attivare i [benefici della sottoscrizione MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere una [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/).
	
**Informazioni sull'installazione dei moduli dei cmdlet PowerShell**

	[AZURE.INCLUDE [vpn-gateway-ps-rm-howto](../../includes/vpn-gateway-ps-rm-howto-include.md)]

## Configurare una connessione da punto a sito per Azure

1. Nella console di PowerShell accedere all'account Azure. Il cmdlet richiederà le credenziali di accesso per l'account Azure. Dopo l'accesso, vengono scaricate le impostazioni dell'account in modo che siano disponibili per Azure PowerShell.

		Login-AzureRmAccount 

2. Ottenere un elenco delle sottoscrizioni di Azure.

		Get-AzureRmSubscription

3. Specificare la sottoscrizione da usare.

		Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. In questa configurazione vengono dichiarate le variabili di PowerShell seguenti con i valori da usare. I valori dichiarati saranno usati negli script di esempio. Dichiarare i valori che da usare. Usare l'esempio seguente, sostituendo i valori con quelli personalizzati, se necessario.

		$VNetName  = "TestVNet"
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

6. Creare le configurazioni delle subnet per la rete virtuale, denominandole *FrontEnd*, *BackEnd* e *GatewaySubnet*. Si noti che questi prefissi devono fare parte dello spazio degli indirizzi della rete virtuale dichiarato in precedenza.

		$fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
		$besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
		$gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix

7. Creare la rete virtuale. Il server DNS specificato deve essere un server DNS in grado di risolvere i nomi per le risorse a cui ci si connette. Per questo esempio è stato usato un indirizzo IP pubblico, ma è possibile inserire valori personalizzati.

		New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS

8. Specificare le variabili per la rete virtuale appena creata.

		$vnet   = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

9. Richiedere un indirizzo IP pubblico assegnato dinamicamente. Questo indirizzo IP è necessario per il corretto funzionamento del gateway. In un secondo momento si connetterà il gateway alla configurazione IP del gateway.
		
		$pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
		$ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
		
10. Caricare il file CER del certificato radice in Azure. È possibile usare un certificato radice dall'ambiente aziendale per la creazione di certificati oppure è possibile usare un certificato autofirmato. È possibile caricare fino a 20 certificati radice. Per istruzioni su come creare un certificato radice autofirmato con *makecert*, vedere la sezione alla fine di questo articolo. Si noti che il file con estensione CER non contiene la chiave privata del certificato radice.
	
	Di seguito è riportato un esempio dell'aspetto che avrà il file. La parte difficile nel caricare i dati del certificato pubblico riguarda la necessità di copiare e incollare l'intera stringa senza spazi. In caso contrario, il caricamento non funzionerà. Per questo passaggio è necessario usare il proprio file del certificato con estensione cer. Non provare a copiare e incollare il codice di esempio seguente.

		$MyP2SRootCertPubKeyBase64 = "MIIDUzCCAj+gAwIBAgIQRggGmrpGj4pCblTanQRNUjAJBgUrDgMCHQUAMDQxEjAQBgNVBAoTCU1pY3Jvc29mdDEeMBwGA1UEAxMVQnJrIExpdGUgVGVzdCBSb290IENBMB4XDTEzMDExOTAwMjQxOFoXDTIxMDExOTAwMjQxN1owNDESMBAGA1UEChMJTWljcm9zb2Z0MR4wHAYDVQQDExVCcmsgTGl0ZSBUZXN0IFJvb3QgQ0EwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQC7SmE+iPULK0Rs7mQBO/6a6B6/G9BaMxHgDGzAmSG0Qsyt5e08aqgFnPdkMl3zRJw3lPKGha/JCvHRNrO8UpeAfc4IXWaqxx2iBipHjwmHPHh7+VB8lU0EJcUe7WBAI2n/sgfCwc+xKtuyRVlOhT6qw/nAi8e5don/iHPU6q7GCcnqoqtceQ/pJ8m66cvAnxwJlBFOTninhb2VjtvOfMQ07zPP+ZuYDPxvX5v3nd6yDa98yW4dZPuiGO2s6zJAfOPT2BrtyvLekItnSgAw3U5C0bOb+8XVKaDZQXbGEtOw6NZvD4L2yLd47nGkN2QXloiPLGyetrj3Z2pZYcrZBo8hAgMBAAGjaTBnMGUGA1UdAQReMFyAEOncRAPNcvJDoe4WP/gH2U+hNjA0MRIwEAYDVQQKEwlNaWNyb3NvZnQxHjAcBgNVBAMTFUJyayBMaXRlIFRlc3QgUm9vdCBDQYIQRggGmrpGj4pCblTanQRNUjAJBgUrDgMCHQUAA4IBAQCGyHhMdygS0g2tEUtRT4KFM+qqUY5HBpbIXNAav1a1dmXpHQCziuuxxzu3iq4XwnWUF1OabdDE2cpxNDOWxSsIxfEBf9ifaoz/O1ToJ0K757q2Rm2NWqQ7bNN8ArhvkNWa95S9gk9ZHZLUcjqanf0F8taJCYgzcbUSp+VBe9DcN89sJpYvfiBiAsMVqGPc/fHJgTScK+8QYrTRMubtFmXHbzBSO/KTAP5rBTxse88EGjK5F8wcedvge2Ksk6XjL3sZ19+Oj8KTQ72wihN900p1WQldHrrnbixSpmHBXbHr9U0NQigrJp5NphfuU5j81C8ixvfUdwyLmTv7rNA7GTAD"
		$p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $MyP2SRootCertPubKeyBase64

11. Creare il gateway di rete virtuale per la rete virtuale. GatewayType deve essere Vpn e VpnType deve essere RouteBased.

		New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Vpn -VpnType RouteBased -EnableBgp $false -GatewaySku Standard -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## Configurazione del client

Ogni client che si connette ad Azure tramite una connessione da punto a sito deve rispettare due requisiti: il client VPN deve essere configurato per la connessione e deve avere un certificato client installato. I pacchetti di configurazione del client VPN sono disponibili per i client Windows. Per altre informazioni, vedere [Domande frequenti sul gateway VPN](vpn-gateway-vpn-faq.md#point-to-site-connections).

1. Scaricare il pacchetto di configurazione del client VPN. In questo passaggio usare l'esempio seguente per scaricare il pacchetto di configurazione del client.

		Get-AzureRmVpnClientPackage -ResourceGroupName $RG -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64

	Il cmdlet PowerShell restituirà un collegamento URL. Copiare e incollare il collegamento restituito in un Web browser per scaricare il pacchetto nel computer. Di seguito è riportato un esempio dell'aspetto dell'URL restituito.

    	"https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"
	
2. Generare e installare i certificati client \(\*.pfx\) creati dal certificato radice nei computer client. È possibile usare qualsiasi metodo di installazione con cui si ha maggiore familiarità. Se si usa un certificato radice autofirmato e si ha familiarità con le procedure per eseguire questa operazione, è possibile fare riferimento alle istruzioni che si trovano alla fine di questo articolo.

3. Per connettersi alla rete virtuale, nel computer client passare alle connessioni VPN e individuare quella appena creata. Avrà lo stesso nome della rete virtuale. Fare clic su **Connect**. È possibile che venga visualizzato un messaggio popup che fa riferimento all'uso del certificato. In questo caso, fare clic su **Continua** per usare privilegi elevati.

4. Nel **connessione** pagina di stato, fare clic su **Connect** per avviare la connessione. Se viene visualizzato un **Seleziona certificato** dello schermo, verificare che il certificato client visualizzato sia quello che si desidera utilizzare per la connessione. In caso contrario, usare la freccia a discesa per selezionare il certificato corretto e quindi fare clic su **OK**.

5. Verrà stabilita la connessione. È possibile verificarla con la procedura riportata di seguito.

## Verificare la connessione

1. Per verificare che la connessione VPN è attiva, aprire un prompt dei comandi con privilegi elevati ed eseguire *ipconfig/all*.

2. Visualizzare i risultati. Si noti che l'indirizzo IP ricevuto è uno degli indirizzi compresi nel pool di indirizzi del client VPN da punto a sito specificato al momento della configurazione. I risultati dovrebbero essere simili a quanto segue:
    
		PPP adapter VNetEast:
			Connection-specific DNS Suffix .:
			Description.....................: VNetEast
			Physical Address................:
			DHCP Enabled....................: No
			Autoconfiguration Enabled.......: Yes
			IPv4 Address....................: 172.16.201.3(Preferred)
			Subnet Mask.....................: 255.255.255.255
			Default Gateway.................:
			NetBIOS over Tcpip..............: Enabled

## Per creare un certificato radice autofirmato con makecert

Makecert è un modo per creare un certificato radice autofirmato. La procedura seguente illustra in dettaglio come procedere.

1. Da un computer che esegue Windows 10 scaricare e installare Windows 10 SDK da [questo collegamento](https://dev.windows.com/en-us/downloads/windows-10-sdk).

2. Dopo l'installazione, è possibile trovare l'utilità makecert.exe in questo percorso: C:\\Programmi \(x86\) \\Windows Kits\\10\\bin \< arch \>.
		
	Esempio:
	
		C:\Program Files (x86)\Windows Kits\10\bin\x64\makecert.exe

3. Creare e installare quindi un certificato radice nell'archivio Certificati personali nel computer in uso. L'esempio seguente crea un file *cer* corrispondente che si caricherà in un secondo momento. Eseguire il comando seguente come amministratore, dove *RootCertificateName* è il nome che si vuole usare per il certificato.

	Nota: se si esegue l'esempio riportato di seguito senza modifiche, il risultato sarà un certificato radice e il file corrispondente sarà *RootCertificateName.cer*. È possibile trovare il file con estensione cer nella directory da cui è stato eseguito il comando. Il certificato si troverà in Certificati -Utente corrente\\Personale\\Certificati.

    	makecert -sky exchange -r -n "CN=RootCertificateName" -pe -a sha1 -len 2048 -ss My "RootCertificateName.cer"

	>[AZURE.NOTE]Poiché è stato creato un certificato radice da cui verranno generati i certificati client, è possibile esportarlo, insieme alla relativa chiave privata e salvarlo in una posizione sicura dove può essere recuperato.

## Per generare un certificato client da un certificato radice autofirmato

La procedura seguente consente di generare un certificato client da un certificato radice autofirmato e quindi di esportare e installare il certificato client.

### Generazione di un certificato client

I passaggi seguenti illustrano come generare un certificato client da un certificato radice autofirmato. È possibile generare più certificati client dallo stesso certificato radice. Ogni certificato client può essere esportato e installato nel computer client.

1. Nello stesso computer usato per creare il certificato radice autofirmato aprire un prompt dei comandi come amministratore.
2. Passare alla directory in cui si vuole salvare il file del certificato client. *RootCertificateName* fa riferimento al certificato radice autofirmato generato. Se si esegue l'esempio riportato di seguito \(sostituendo RootCertificateName con il nome del certificato radice\), il risultato sarà un certificato client denominato "ClientCertificateName" nell'archivio certificati personale.
3. Digitare il seguente comando:

    	makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "RootCertificateName" -is my -a sha1

4. Tutti i certificati vengono archiviati nell'archivio Certificati - Utente corrente\\Personale\\Certificati nel computer in uso. È possibile generare tutti i certificati client necessari in base a questa procedura.

### Esportare e installare il certificato client

È obbligatorio installare un certificato client in ogni computer che si vuole connettere alla rete virtuale. La procedura seguente illustra l'installazione manuale del certificato client.

1. Per esportare un certificato client, usare *certmgr.msc*. Fare clic con il pulsante destro del mouse sul certificato client da esportare, scegliere **Tutte le attività** e quindi fare clic su **Esporta**. Verrà aperta l'Esportazione guidata certificati.
2. Nella procedura guidata fare clic su **Avanti**, selezionare **Sì, esporta la chiave privata** e quindi fare clic su **Avanti**.
3. Nella pagina **Formato file di esportazione** è possibile lasciare selezionate le impostazioni predefinite. Quindi fare clic su **Avanti**.  
4. Nella pagina **Sicurezza** è necessario proteggere la chiave privata. Se si sceglie di usare una password, assicurarsi di registrare o ricordare quella impostata per questo certificato, quindi fare clic su **Avanti**.
5. In **File da esportare** fare clic su**Sfoglia** e passare al percorso in cui si vuole esportare il certificato. Per **Nome File** assegnare un nome al file del certificato. Quindi fare clic su **Avanti**.
6. Fare clic su **Fine** per esportare il certificato.	
3. Trovare e copiare il file con estensione *pfx* nel computer client. Nel computer client fare doppio clic sul file con estensione *pfx* per installarlo. Lasciare il **Percorso archivio** impostato come **Utente corrente**, quindi fare clic su **Avanti**.
4. Nella pagina **File da importare** non apportare alcuna modifica. Fare clic su **Avanti**.
5. Nella pagina **Protezione della chiave privata** immettere la password per il certificato, se ne è stata usata una, o verificare che l'entità di sicurezza che installa il certificato sia corretta, quindi fare clic su **Avanti**.
6. Nella pagina **Archivio certificati** lasciare il percorso predefinito e quindi fare clic su **Avanti**.
7. Fare clic su **Finish**. In **Avviso di sicurezza** per l'installazione del certificato fare clic su **Sì**. Il certificato è stato importato correttamente.

## Passaggi successivi

È possibile aggiungere una macchina virtuale alla rete virtuale. Per i passaggi, vedere [Creare una macchina virtuale](../virtual-machines/virtual-machines-windows-tutorial.md).

<!---HONumber=AcomDC_0114_2016-->