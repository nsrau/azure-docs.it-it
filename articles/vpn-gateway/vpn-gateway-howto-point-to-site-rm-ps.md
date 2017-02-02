---
title: 'Connettersi a una rete virtuale di Azure da punto a sito: PowerShell | Documentazione Microsoft'
description: Connettersi in modo sicuro alla rete virtuale di Azure attraverso la creazione di una connessione gateway VPN da punto a sito.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3eddadf6-2e96-48c4-87c6-52a146faeec6
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/19/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: a1f4cf97371d1ce17d68a58c9f62b6a92a0f8210
ms.openlocfilehash: 4b190ad3e4f08a3a935bd46c05e6f7daf506aad8


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-powershell"></a>Configurare una connessione da punto a sito a una rete virtuale usando PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager - Portale di Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Classica - Portale di Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Una configurazione da punto a sito (P2S) permette di creare una connessione protetta da un singolo computer client a una rete virtuale. Una soluzione P2S è utile quando ci si vuole connettere alla rete virtuale da una posizione remota, ad esempio da casa o durante una riunione, oppure quando solo pochi client devono connettersi a una rete virtuale. 

Le connessioni da punto a sito non richiedono un dispositivo VPN o un indirizzo IP pubblico per funzionare. Una connessione VPN viene stabilita avviando la connessione dal computer client. Per altre informazioni sulle connessioni da punto a sito, vedere [Domande frequenti sul gateway VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) e [Pianificazione e progettazione](vpn-gateway-plan-design.md). 

Questo articolo illustra la creazione di una rete virtuale con una connessione da punto a sito nel modello di distribuzione Resource Manager usando PowerShell.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Metodi e modelli di distribuzione per connessioni P2S
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

La tabella seguente descrive i due modelli di distribuzione e i metodi di distribuzione disponibili per le configurazioni P2S. Quando è disponibile un articolo contenente la procedura di configurazione, nella tabella è presente un collegamento diretto.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Flusso di lavoro di base
![Diagramma da punto a sito](./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "Da punto a sito")

In questo scenario si creerà una rete virtuale con una connessione da punto a sito. Le istruzioni illustrano anche come generare i certificati necessari per questa configurazione. Una connessione da punto a sito è costituita dagli elementi seguenti: una rete virtuale con un gateway VPN, un file CER del certificato radice, ovvero la chiave pubblica, un certificato client e la configurazione VPN nel client. 

Per questa configurazione si useranno i valori seguenti. Le variabili sono state impostate nella sezione [1](#declare) di questo articolo. È possibile seguire la procedura dettagliata e usare i valori senza modificarle oppure modificarli in base all'ambiente. 

### <a name="a-nameexampleaexample-values"></a><a name="example"></a>Valori di esempio
* **Nome: VNet1**
* **Spazio degli indirizzi: 192.168.0.0/16** e **10.254.0.0/16**<br>Per questo esempio, si usa più di uno spazio indirizzi per mostrare che la configurazione funziona con più spazi indirizzi, ma per questa configurazione non sono necessari più spazi indirizzi.
* **Nome subnet: FrontEnd**
  * **Intervallo di indirizzi subnet: 192.168.1.0/24**
* **Nome subnet: BackEnd**
  * **Intervallo di indirizzi subnet: 10.254.1.0/24**
* **Nome subnet: GatewaySubnet**<br>Il nome subnet *GatewaySubnet* è obbligatorio per il funzionamento del gateway VPN.
  * **Intervallo di indirizzi subnet: 192.168.200.0/24** 
* **Pool di indirizzi client VPN: 172.16.201.0/24**<br>I client VPN che si connettono alla rete virtuale con questa connessione da punto a sito ricevono un indirizzo IP dal pool di indirizzi client VPN.
* **Sottoscrizione:** se si hanno più sottoscrizioni, verificare di usare quella corretta.
* **Gruppo di risorse: TestRG**
* **Località: Stati Uniti orientali**
* **Server DNS: indirizzo IP** del server DNS che si vuole usare per la risoluzione dei nomi.
* **Nome del gateway: Vnet1GW**
* **Nome dell'IP pubblico: VNet1GWPIP**
* **VpnType: RouteBased**

## <a name="before-beginning"></a>Prima di iniziare
* Verificare di possedere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial).
* Installare la versione più recente dei cmdlet di PowerShell per Azure Resource Manager. Per altre informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs) . Quando si usa PowerShell per questa configurazione, assicurarsi di scegliere l'opzione Esegui come amministratore. 

## <a name="a-namedeclareapart-1---log-in-and-set-variables"></a><a name="declare"></a>Parte 1: Accedere e impostare le variabili
In questa sezione si accede e si dichiarano i valori usati per la configurazione. I valori dichiarati saranno usati negli script di esempio. È possibile modificare i valori in base all'ambiente personalizzato. In alternativa, è possibile usare i valori dichiarati e seguire la procedura come un esercizio.

1. Nella console di PowerShell accedere all'account Azure. Il cmdlet richiederà le credenziali di accesso per l'account Azure. Dopo l'accesso, vengono scaricate le impostazioni dell'account in modo che siano disponibili per Azure PowerShell.
   
        Login-AzureRmAccount 
2. Ottenere un elenco delle sottoscrizioni di Azure.
   
        Get-AzureRmSubscription
3. Specificare la sottoscrizione da usare. 
   
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"
4. Dichiarare le variabili da usare. Usare l'esempio seguente, sostituendo i valori con quelli personalizzati, se necessario.
   
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
        $GWName = "VNet1GW"
        $GWIPName = "VNet1GWPIP"
        $GWIPconfName = "gwipconf"

## <a name="a-nameconfigurevnetapart-2---configure-a-vnet"></a><a name="ConfigureVNet"></a>Parte 2: Configurare una rete virtuale
1. Creare un gruppo di risorse.
   
        New-AzureRmResourceGroup -Name $RG -Location $Location
2. Creare le configurazioni delle subnet per la rete virtuale, denominandole *FrontEnd*, *BackEnd* e *GatewaySubnet*. Questi prefissi devono fare parte dello spazio indirizzi della rete virtuale dichiarato.
   
        $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
        $besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
        $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
3. Creare la rete virtuale. Il server DNS specificato deve essere un server DNS in grado di risolvere i nomi per le risorse a cui ci si connette. Per questo esempio è stato usato un indirizzo IP pubblico. Assicurarsi di usare valori personalizzati.
   
        New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS
4. Specificare le variabili per la rete virtuale creata.
   
        $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
5. Richiedere un indirizzo IP pubblico assegnato dinamicamente. Questo indirizzo IP è necessario per il corretto funzionamento del gateway. In un secondo momento si connette il gateway alla configurazione IP del gateway.
   
        $pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

## <a name="a-namecertificatesapart-3---certificates"></a><a name="Certificates"></a>Parte 3: Certificati
I certificati vengono usati da Azure per autenticare i client VPN per VPN da punto a sito. I dati del certificato pubblico (non la chiave privata) vengono esportati come file CER X.509 con codifica in base&64; da un certificato radice generato da una soluzione aziendale per la creazione di certificati o da un certificato radice autofirmato. Si importano quindi i dati del certificato pubblico dal certificato radice ad Azure. È anche necessario generare un certificato client da un certificato radice per i client. In ogni client che si vuole connettere alla rete virtuale con una connessione P2S deve essere installato un certificato client generato dal certificato radice.

### <a name="a-namecerastep-1---obtain-the-cer-file-for-the-root-certificate"></a><a name="cer"></a>Passaggio 1: Ottenere il file CER per il certificato radice
Sarà necessario ottenere i dati del certificato pubblico per il certificato radice che si vuole usare.

* Se si usa un sistema aziendale per la creazione di certificati, ottenere il file CER per il certificato radice che si vuole usare. 
* Se non si usa una soluzione aziendale per la creazione di certificati, è necessario generare un certificato radice autofirmato. Il metodo consigliato per creare un certificato autofirmato per l'uso di P2S è makecert. Anche se è possibile usare PowerShell per creare certificati autofirmati, il certificato generato con PowerShell non contiene i campi necessari per le connessioni P2S. Per i passaggi relativi a Windows 10, è possibile vedere [Usare i certificati radice autofirmati per le configurazioni da punto a sito](vpn-gateway-certificates-point-to-site.md).

1. Per ottenere un file CER da un certificato, aprire **certmgr.msc** e individuare il certificato radice. Fare clic con il pulsante destro del mouse sul certificato radice autofirmato, scegliere **Tutte le attività** e quindi fare clic su **Esporta**. Si avvia la procedura di **Esportazione guidata certificati**.
2. Nella procedura guidata fare clic su **Avanti**, selezionare **No, non esportare la chiave privata** e quindi fare clic su **Avanti**.
3. Nella pagina **Formato file di esportazione** selezionare **Codificato Base&64; X.509 (.CER)**. Quindi fare clic su **Avanti**. 
4. In **File da esportare** fare clic su **Sfoglia** e passare alla posizione in cui si vuole esportare il certificato. Per **Nome file**, assegnare un nome al file del certificato. Quindi fare clic su **Avanti**.
5. Fare clic su **Fine** per esportare il certificato.

### <a name="a-namegenerateastep-2---generate-the-client-certificate"></a><a name="generate"></a>Passaggio 2: Generare il certificato client
Procedere quindi alla generazione dei certificati client. È possibile generare un certificato univoco per ogni client che stabilirà la connessione oppure usare lo stesso certificato in più client. Il vantaggio della generazione di certificati client univoci è dato dalla possibilità di revocare un certificato, se necessario. Diversamente, se tutti gli utenti usano lo stesso certificato client ed è necessario revocarlo per un client, si dovranno generare e installare nuovi certificati per tutti i client che usano il certificato per l'autenticazione. I certificati client verranno installati in ogni computer client più avanti in questa esercitazione.

* Se si usa una soluzione aziendale per la creazione di certificati, generare un certificato client con il valore di nome comune nel formato 'name@yourdomain.com', anziché nel formato NetBIOS 'DOMINIO\nomeutente'. 
* Se si usa un certificato autofirmato, per generare un certificato client vedere [Usare i certificati radice autofirmati per le configurazioni da punto a sito](vpn-gateway-certificates-point-to-site.md) .

### <a name="a-nameexportclientcertastep-3---export-the-client-certificate"></a><a name="exportclientcert"></a>Passaggio 3: Esportare il certificato client
Il certificato client è necessario per l'autenticazione. Dopo avere generato il certificato client, esportarlo. Il certificato client esportato verrà installato in seguito in ogni computer client.

1. Per esportare un certificato client, è possibile usare *certmgr.msc*. Fare clic con il pulsante destro del mouse sul certificato client da esportare, scegliere **Tutte le attività** e quindi fare clic su **Esporta**.
2. Esportare il certificato client con la chiave privata. Si tratta di un file con estensione *prx* . Assicurarsi di eseguire la registrazione o ricordare la password (chiave) impostata per il certificato.

### <a name="a-nameuploadastep-4---upload-the-root-certificate-cer-file"></a><a name="upload"></a>Passaggio 4: Caricare il file CER del certificato radice
Dichiarare la variabile per il nome del certificato, sostituendo il valore con il proprio:

        $P2SRootCertName = "Mycertificatename.cer"

Aggiungere i dati del certificato pubblico per il certificato radice ad Azure. È possibile caricare file per un massimo di 20 certificati radice. Non si può caricare la chiave privata per il certificato radice in Azure. Dopo che è stato caricato, il file CER viene usato da Azure per autenticare i client che si connettono alla rete virtuale. 

Sostituire il percorso file con il proprio e quindi eseguire i cmdlet.

        $filePathForCert = "C:\cert\Mycertificatename.cer"
        $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
        $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
        $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64

## <a name="a-namecreategatewayapart-4---create-the-vpn-gateway"></a><a name="creategateway"></a>Parte 4: Creare il gateway VPN
Configurare e creare il gateway di rete virtuale per la rete virtuale. *-GatewayType* deve essere **Vpn** e *-VpnType* deve essere **RouteBased**. Per completare questa operazione possono essere necessari fino a 45 minuti.

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
        -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
        -VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
        -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## <a name="a-nameclientconfigapart-5---download-the-vpn-client-configuration-package"></a><a name="clientconfig"></a>Parte 5: Scaricare il pacchetto di configurazione del client VPN
Nei client che si connettono ad Azure con una connessione da punto a sito devono essere installati un certificato client e un pacchetto di configurazione del client VPN. I pacchetti di configurazione del client VPN sono disponibili per i client Windows. Il pacchetto del client VPN contiene informazioni per la configurazione del software client VPN incorporato in Windows ed è specifico per la VPN a cui ci si vuole connettere. Tramite il pacchetto non viene installato del software aggiuntivo. Per altre informazioni, vedere [Domande frequenti sul gateway VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) .

1. Dopo aver creato il gateway, è possibile scaricare il pacchetto di configurazione client. Questo esempio scarica il pacchetto per i client a 64 bit. Per scaricare il client a 32 bit, sostituire "Amd64" con "x86". È anche possibile scaricare il client VPN usando il portale di Azure.
   
        Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
        -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64
2. Il cmdlet di PowerShell restituisce un collegamento URL. Ecco un esempio dell'aspetto dell'URL restituito:
   
        "https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"
3. Copiare e incollare il collegamento restituito in un Web browser per scaricare il pacchetto. Quindi installare il pacchetto nel computer client. Se viene visualizzato un popup SmartScreen, fare clic su **Altre informazioni**, quindi su **Esegui comunque** per installare il pacchetto.
4. Nel computer client passare a **Impostazioni di rete** e fare clic su **VPN**. La connessione verrà visualizzata in un elenco, insieme al nome della rete virtuale a cui si connette ed è simile a questo esempio: 
   
    ![Client VPN](./media/vpn-gateway-howto-point-to-site-rm-ps/vpn.png "Client VPN")

## <a name="a-nameclientcertificateapart-6---install-the-client-certificate"></a><a name="clientcertificate"></a>Parte 6: Installare il certificato client
Ogni computer client deve avere un certificato client per l'autenticazione. Quando si installa il certificato client, sarà necessaria la password creata quando il certificato client è stato esportato.

1. Copiare il file PFX nel computer client.
2. Fare doppio clic sul file PFX per installarlo. Non modificare il percorso di installazione.

## <a name="a-nameconnectapart-7---connect-to-azure"></a><a name="connect"></a>Parte 7: Connettersi ad Azure
1. Per connettersi alla rete virtuale, nel computer client passare alle connessioni VPN e individuare quella creata, che ha lo stesso nome della rete virtuale locale. Fare clic su **Connetti**. È possibile che venga visualizzato un messaggio popup che fa riferimento all'uso del certificato. In questo caso, fare clic su **Continua** per usare privilegi elevati. 
2. Nella pagina **Stato connessione** fare clic su **Connetti** per avviare la connessione. Se viene visualizzato un **Seleziona certificato** dello schermo, verificare che il certificato client visualizzato sia quello che si desidera utilizzare per la connessione. In caso contrario, usare la freccia a discesa per selezionare il certificato corretto e quindi fare clic su **OK**.
   
    ![Connessione del client VPN](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "Connessione del client VPN")
3. Verrà stabilita la connessione.
   
    ![Connessione stabilita](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "Connessione stabilita")

## <a name="a-nameverifyapart-8---verify-your-connection"></a><a name="verify"></a>Parte 8: Verificare la connessione
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

## <a name="a-nameaddremovecertato-add-or-remove-a-trusted-root-certificate"></a><a name="addremovecert"></a>Per aggiungere o rimuovere un certificato radice attendibile
I certificati vengono usati per autenticare client VPN per VPN da punto a sito. I passaggi seguenti illustrano in dettaglio come aggiungere e rimuovere certificati radice. L'aggiunta di un file con codifica Base64 X.509 (estensione CER) consente ad Azure di considerare attendibile il certificato radice che il file rappresenta. 

È possibile aggiungere o rimuovere i certificati radice attendibili usando PowerShell o il portale di Azure. Per eseguire questa operazione usando il portale di Azure, passare a **Gateway di rete virtuale > Impostazioni > Configurazione da punto a sito > Certificati radice**. I passaggi seguenti illustrano l'esecuzione di queste attività con PowerShell. 

### <a name="add-a-trusted-root-certificate"></a>Aggiunta di un certificato radice attendibile
In Azure è possibile aggiungere fino a 20 file CER di certificato radice trusted. Attenersi alla procedura seguente per aggiungere un certificato radice.

1. Creare e preparare il nuovo certificato che sarà aggiunto in Azure. Esportare la chiave pubblica come file CER con codifica Base&64; X.509 e aprirla con un editor di testo. Copiare solo la sezione seguente. 
   
    Copiare i valori, come illustrato nell'esempio seguente:
   
    ![Certificato](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png "Certificato")
2. Specificare il nome del certificato e le informazioni sulla chiave come variabile. Sostituire le informazioni con i propri dati, come illustrato nell'esempio seguente:
   
        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
3. Aggiungere il nuovo certificato radice. È possibile aggiungere solo un certificato alla volta.
   
        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2
4. È possibile verificare che il nuovo certificato sia stato aggiunto correttamente mediante il cmdlet seguente.
   
        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

### <a name="to-remove-a-trusted-root-certificate"></a>Per rimuovere un certificato radice attendibile
È possibile rimuovere un certificato radice attendibile da Azure. Quando si rimuove un certificato attendibile, i certificati client generati dal certificato radice non potranno più connettersi ad Azure tramite una connessione da punto a sito. Per consentire ai client di connettersi, sarà necessario installare un nuovo certificato client generato da un certificato considerato attendibile in Azure.

1. Per rimuovere un certificato radice attendibile, modificare l'esempio seguente:
   
    Dichiarare le variabili.
   
        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
2. Rimuovere il certificato.
   
        Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
3. Usare il cmdlet seguente per verificare che il certificato sia stato rimosso correttamente. 
   
        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

## <a name="a-namerevokeato-manage-the-list-of-revoked-client-certificates"></a><a name="revoke"></a>Per gestire l'elenco dei certificati client revocati
È possibile revocare i certificati client. L'elenco di revoche di certificati consente di negare in modo selettivo la connettività da punto a sito basata sui singoli certificati client. Se si rimuove un file CER del certificato radice da Azure, viene revocato l'accesso per tutti i certificati client generati o firmati dal certificato radice revocato. Se necessario, è possibile revocare un certificato client specifico, non radice. In questo modo, gli altri certificati generati dal certificato radice rimarranno validi.

La regola generale è quella di usare il certificato radice per gestire l'accesso a livello di team o organizzazione, usando i certificati client revocati per il controllo di accesso con granularità fine su singoli utenti.

### <a name="revoke-a-client-certificate"></a>Revocare un certificato client
1. Ottenere l'identificazione personale del certificato client da revocare.
   
        $RevokedClientCert1 = "ClientCert1"
        $RevokedThumbprint1 = "?ef2af033d0686820f5a3c74804d167b88b69982f"
2. Aggiungere l'identificazione personale all'elenco delle identificazioni personali revocate.
   
        Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
3. Verificare che l'identificazione personale sia stata aggiunta all'elenco di revoche di certificati. È necessario aggiungere un'identificazione personale alla volta.
   
        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### <a name="reinstate-a-client-certificate"></a>Reintegrare un certificato client
È possibile reintegrare un certificato client rimuovendo l'identificazione personale dall'elenco dei certificati client revocati.

1. Rimuovere l'identificazione personale dall'elenco delle identificazioni personali dei certificati client revocate.
   
       Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
       -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
2. Verificare che l'identificazione personale sia stata rimossa dall'elenco di quelle revocate.
   
        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## <a name="next-steps"></a>Passaggi successivi
Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per altre informazioni, vedere [Macchine virtuali](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).




<!--HONumber=Jan17_HO4-->


