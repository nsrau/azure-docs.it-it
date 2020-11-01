---
title: 'Connettersi a una VNet da un computer-P2S VPN e autenticazione del certificato di Azure nativo: PowerShell'
description: Connettere i client Windows e macOS in modo sicuro alla rete virtuale di Azure usando P2S e i certificati autofirmati o emessi dall'autorità di certificazione. Questo articolo usa PowerShell.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/29/2020
ms.author: cherylmc
ms.openlocfilehash: b6df7aa919721576aad10d6a476be976ef81df7d
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145872"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-powershell"></a>Configurare una connessione VPN da punto a sito a una VNet usando l'autenticazione del certificato di Azure nativo: PowerShell

Questo articolo consente di connettere in modo sicuro i singoli client che eseguono Windows, Linux o macOS a una VNet di Azure. Le connessioni VPN da punto a sito sono utili quando si vuole connettersi alla VNet da una posizione remota, ad esempio quando si esegue la telecommutazione da casa o da una conferenza. È anche possibile usare una VPN da punto a sito al posto di una VPN da sito a sito quando solo pochi client devono connettersi a una rete virtuale. Le connessioni da punto a sito non richiedono un dispositivo VPN o un indirizzo IP pubblico. La modalità da punto a sito crea la connessione VPN tramite SSTP (Secure Sockets Tunneling Protocol) o IKEv2.

:::image type="content" source="./media/vpn-gateway-how-to-point-to-site-rm-ps/point-to-site-diagram.png" alt-text="Connettersi da un computer a un diagramma di connessione da punto a sito di Azure VNet":::

Per ulteriori informazioni sulla VPN da punto a sito, vedere [informazioni sulla VPN da punto a sito](point-to-site-about.md). Per creare questa configurazione usando il portale di Azure, vedere [configurare una VPN da punto a sito usando il portale di Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

[!INCLUDE [P2S basic architecture](../../includes/vpn-gateway-p2s-architecture.md)]

## <a name="prerequisites"></a>Prerequisiti

Verificare di possedere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial).

### <a name="azure-powershell"></a>Azure PowerShell

>[!IMPORTANT]
> Molti dei passaggi descritti in questo articolo possono usare la Azure Cloud Shell. Tuttavia, non è possibile usare Cloud Shell per generare i certificati. Inoltre, per caricare la chiave pubblica del certificato radice, è necessario usare Azure PowerShell localmente o l'portale di Azure.
>

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="1-sign-in"></a><a name="signin"></a>1. Accesso

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="2-declare-variables"></a><a name="declare"></a>2. dichiarare le variabili

Le variabili vengono usate per questo articolo, in modo che sia possibile modificare facilmente i valori da applicare al proprio ambiente senza dover modificare gli esempi. Dichiarare le variabili da usare. È possibile usare l'esempio seguente, sostituendo i valori personalizzati, se necessario. Se si chiude la sessione di PowerShell o Cloud Shell in qualsiasi momento durante l'esercizio, copiare e incollare nuovamente i valori per dichiarare nuovamente le variabili.

```azurepowershell-interactive
$VNetName  = "VNet1"
$FESubName = "FrontEnd"
$GWSubName = "GatewaySubnet"
$VNetPrefix = "10.1.0.0/16"
$FESubPrefix = "10.1.0.0/24"
$GWSubPrefix = "10.1.255.0/27"
$VPNClientAddressPool = "172.16.201.0/24"
$RG = "TestRG1"
$Location = "EastUS"
$GWName = "VNet1GW"
$GWIPName = "VNet1GWpip"
$GWIPconfName = "gwipconf"
$DNS = "10.2.1.4"
```

## <a name="3-configure-a-vnet"></a><a name="ConfigureVNet"></a>3. configurare un VNet

1. Creare un gruppo di risorse.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG -Location $Location
   ```

1. Creare le configurazioni di subnet per la rete virtuale, denominarle front- *end* e *GatewaySubnet* . Questi prefissi devono fare parte dello spazio indirizzi della rete virtuale dichiarato.

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
   ```

1. Creare la rete virtuale.

   In questo esempio il parametro del server -DnsServer è facoltativo. Se si specifica un valore, non verrà creato un nuovo server DNS. L'indirizzo IP del server DNS specificato deve essere un server DNS che riesce a risolvere i nomi per le risorse a cui ci si connette dalla rete virtuale. Questo esempio usa un indirizzo IP privato, ma è probabile che non si tratti dell'indirizzo IP del server DNS in uso. Assicurarsi di usare valori personalizzati. Il valore specificato viene usato dalle risorse distribuite nella rete virtuale, non dalla connessione P2S o dal client VPN.

   ```azurepowershell-interactive
       New-AzVirtualNetwork `
      -ResourceGroupName $RG `
      -Location $Location `
      -Name $VNetName `
      -AddressPrefix $VNetPrefix `
      -Subnet $fesub, $gwsub `
      -DnsServer $DNS
   ```

1. Specificare le variabili per la rete virtuale creata.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   ```

1. Un gateway VPN deve avere un indirizzo IP pubblico. È necessario richiedere prima di tutto la risorsa dell'indirizzo IP e quindi farvi riferimento durante la creazione del gateway di rete virtuale. L'indirizzo IP viene assegnato dinamicamente alla risorsa durante la creazione del gateway VPN. Il gateway VPN supporta attualmente solo l'allocazione degli indirizzi IP pubblici *dinamici* . Non è possibile richiedere un'assegnazione degli indirizzi IP pubblici statici. Ciò non significa tuttavia che l'indirizzo IP venga modificato dopo l'assegnazione al gateway VPN. L'indirizzo IP pubblico viene modificato solo quando il gateway viene eliminato e ricreato. Non viene modificato in caso di ridimensionamento, reimpostazione o altre manutenzioni/aggiornamenti del gateway VPN.

   Richiedere un indirizzo IP pubblico assegnato dinamicamente.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="4-create-the-vpn-gateway"></a><a name="creategateway"></a>4. creare il gateway VPN

In questo passaggio si configura e si crea il gateway di rete virtuale per la VNet.

* -GatewayType deve essere **Vpn** e -VpnType deve essere **RouteBased** .
* -VpnClientProtocol consente di specificare i tipi di tunnel da abilitare. Le opzioni del tunnel sono **OpenVPN, SSTP** e **IKEv2** . È possibile scegliere di abilitarne una o qualsiasi combinazione supportata. Se si vuole abilitare più tipi, specificare i nomi separati da una virgola. Non è possibile abilitare OpenVPN e SSTP insieme. Il client strongSwan in Android e Linux e il client VPN IKEv2 nativo in iOS e OSX useranno solo il tunnel IKEv2 per la connessione. I client Windows provano prima IKEv2 e, se la connessione non viene stabilita, tornano a SSTP. È possibile usare il client OpenVPN per connettersi al tipo di tunnel OpenVPN.
* Lo SKU del gateway di rete virtuale ' Basic ' non supporta l'autenticazione IKEv2, OpenVPN o RADIUS. Se si prevede che client Mac si connetteranno alla rete virtuale, non usare lo SKU Basic.
* Per il completamento di un gateway VPN possono essere necessari fino a 45 minuti, in base allo [SKU del gateway](vpn-gateway-about-vpn-gateway-settings.md) selezionato. In questo esempio viene usato IKEv2.

1. Configurare e creare il gateway di rete virtuale per la rete virtuale. Sono necessari circa 45 minuti per la creazione del gateway.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
   -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
   -VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
   ```

1. Una volta creato il gateway, è possibile visualizzarlo usando l'esempio seguente. Se è stato chiuso PowerShell o si è verificato un timeout durante la creazione del gateway, è possibile dichiarare di nuovo [le variabili](#declare) .

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroup $RG
   ```

## <a name="5-add-the-vpn-client-address-pool"></a><a name="addresspool"></a>5. aggiungere il pool di indirizzi del client VPN

Al termine della creazione del gateway VPN, è possibile aggiungere il pool di indirizzi del client VPN. Il pool di indirizzi client VPN è l'intervallo da cui i client VPN ricevono un indirizzo IP al momento della connessione. Usare un intervallo di indirizzi IP privati che non si sovrapponga con la posizione locale da cui viene effettuata la connessione o con la rete virtuale a cui ci si vuole connettere.

In questo esempio il pool di indirizzi del client VPN viene dichiarato come [variabile](#declare) in un passaggio precedente.

```azurepowershell-interactive
$Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientAddressPool $VPNClientAddressPool
```

## <a name="6-generate-certificates"></a><a name="Certificates"></a>6. generare certificati

>[!IMPORTANT]
> Non è possibile generare certificati utilizzando Azure Cloud Shell. È necessario usare uno dei metodi descritti in questa sezione. Se si vuole usare PowerShell, è necessario installarlo localmente.
>

I certificati vengono usati da Azure per autenticare i client VPN per le VPN da punto a sito. È necessario caricare le informazioni della chiave pubblica del certificato radice in Azure. La chiave pubblica viene quindi considerata "attendibile". I certificati client devono essere generati dal certificato radice attendibile e quindi installati in ogni computer client nell'archivio certificati Certificati - Utente corrente/Personale. Il certificato viene usato per l'autenticazione del client all'avvio di una connessione alla rete virtuale. 

Se usati, i certificati autofirmati devono essere creati con parametri specifici. È possibile creare un certificato autofirmato seguendo le istruzioni per [PowerShell e Windows 10](vpn-gateway-certificates-point-to-site.md) oppure è possibile usare [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) se Windows 10 non è disponibile. È importante seguire i passaggi delle istruzioni quando si generano certificati radice autofirmati e certificati client. In caso contrario, i certificati generati non saranno compatibili con le connessioni P2S e si riceverà un errore di connessione.

### <a name="root-certificate"></a><a name="cer"></a>Certificato radice

1. [!INCLUDE [Root certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

1. Dopo aver creato il certificato radice, [esportare](vpn-gateway-certificates-point-to-site.md#cer) i dati del certificato pubblico (non la chiave privata) come file con estensione cer X. 509 con codifica Base64.

### <a name="client-certificate"></a><a name="generate"></a>Certificato client

1. [!INCLUDE [Generate a client certificate](../../includes/vpn-gateway-p2s-clientcert-include.md)]

1. Dopo aver creato il certificato client, [esportarlo](vpn-gateway-certificates-point-to-site.md#clientexport) . Il certificato client verrà distribuito ai computer client che si connetteranno.

## <a name="7-upload-the-root-certificate-public-key-information"></a><a name="upload"></a>7. caricare le informazioni sulla chiave pubblica del certificato radice

Verificare che la creazione del gateway VPN sia stata completata. In tal caso sarà possibile caricare il file CER, contenente le informazioni sulla chiave pubblica, per un certificato radice attendibile in Azure. Al termine del caricamento di un file CER, Azure lo può usare per autenticare i client che hanno installato un certificato client generato dal certificato radice attendibile. È possibile caricare fino a 20 file di certificato radice attendibile aggiuntivi in un secondo momento, se necessario.

>[!NOTE]
> Non è possibile caricare il file con estensione cer usando Azure Cloud Shell. È possibile usare PowerShell localmente nel computer oppure è possibile usare la [procedura portale di Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile).
>

1. Dichiarare la variabile per il nome del certificato, sostituendo il valore con il proprio.

   ```azurepowershell
   $P2SRootCertName = "P2SRootCert.cer"
   ```

1. Sostituire il percorso file con il proprio e quindi eseguire i cmdlet.

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
   ```

1. Caricare le informazioni sulla chiave pubblica in Azure. Dopo aver caricato le informazioni sul certificato, Azure lo considera un certificato radice attendibile. Quando si carica, assicurarsi di eseguire PowerShell localmente nel computer, oppure è possibile usare la [procedura portale di Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). Non è possibile caricare usando Azure Cloud Shell.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG1" -PublicCertData $CertBase64
   ```

## <a name="8-install-an-exported-client-certificate"></a><a name="clientcertificate"></a>8. installare un certificato client esportato

I passaggi seguenti consentono di installare in un client Windows. Per ulteriori client e altre informazioni, vedere [Install a client certificate](point-to-site-how-to-vpn-client-install-azure-cert.md).

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

Verificare che il certificato client sia stato esportato come PFX con l'intera catena di certificati (impostazione predefinita). In caso contrario, le informazioni del certificato radice non sono presenti nel computer client e il client non potrà essere autenticato correttamente.

## <a name="9-configure-the-vpn-client"></a><a name="clientconfig"></a>9. configurare il client VPN

In questa sezione viene configurato Native Client per il computer per la connessione al gateway di rete virtuale. Ad esempio, quando si passa a impostazioni VPN nel computer Windows, è possibile aggiungere connessioni VPN. Una connessione da punto a sito richiede impostazioni di configurazione specifiche. Questi passaggi consentono di creare un pacchetto con le impostazioni specifiche che il client VPN nativo deve essere in grado di connettersi alla rete virtuale tramite una connessione da punto a sito.

È possibile utilizzare i seguenti esempi rapidi per generare e installare il pacchetto di configurazione client. Per ulteriori informazioni sui contenuti del pacchetto e istruzioni aggiuntive su come generare e installare i file di configurazione del client VPN, vedere [creare e installare i file di configurazione del client VPN](point-to-site-vpn-client-configuration-azure-cert.md).

Se è necessario dichiarare di nuovo le variabili, è possibile trovarle [qui](#declare).

### <a name="to-generate-configuration-files"></a>Per generare i file di configurazione

```azurepowershell-interactive
$profile=New-AzVpnClientConfiguration -ResourceGroupName $RG -Name $GWName -AuthenticationMethod "EapTls"

$profile.VPNProfileSASUrl
```

### <a name="to-install-the-client-configuration-package"></a>Per installare il pacchetto di configurazione client

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="10-connect-to-azure"></a><a name="connect"></a>10. connettersi ad Azure

### <a name="windows-vpn-client"></a>Client VPN Windows

[!INCLUDE [Connect from Windows client](../../includes/vpn-gateway-p2s-connect-windows-client.md)]

[!INCLUDE [Client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="mac-vpn-client"></a>Client VPN Mac

Dalla finestra di dialogo Rete individuare il profilo client che si vuole usare, quindi fare clic su **Connessione** .
Per istruzioni dettagliate, vedere [Installazione in Mac (OS X)](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-vpn-client-configuration-azure-cert#installmac). Se si riscontrano problemi di connessione, verificare che il gateway di rete virtuale non usi uno SKU Basic. Lo SKU Basic non è supportato per i client Mac.

  ![Connessione Mac](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-a-connection"></a><a name="verify"></a>Per verificare una connessione

Queste istruzioni si applicano ai client Windows.

1. Per verificare che la connessione VPN è attiva, aprire un prompt dei comandi con privilegi elevati ed eseguire *ipconfig/all* .
2. Visualizzare i risultati. Si noti che l'indirizzo IP ricevuto è uno degli indirizzi all'interno del pool di indirizzi client VPN da punto a sito specificato nella configurazione. I risultati sono simili a questo esempio:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.13(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Per connettersi a una macchina virtuale

Queste istruzioni si applicano ai client Windows.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* Verificare che il pacchetto di configurazione del client VPN sia stato generato dopo che sono stati specificati gli indirizzi IP del server DNS per la rete virtuale. Se gli indirizzi IP del server DNS sono stati aggiornati, generare e installare un nuovo pacchetto di configurazione del client VPN.

* Usare "ipconfig" per controllare l'indirizzo IPv4 assegnato alla scheda Ethernet nel computer da cui viene effettuata la connessione. Se l'indirizzo IP è compreso nell'intervallo di indirizzi della rete virtuale a cui ci si connette o nell'intervallo di indirizzi del pool di indirizzi del client VPN, si verifica la cosiddetta sovrapposizione dello spazio indirizzi. Con questo tipo di sovrapposizione, il traffico di rete non raggiunge Azure e rimane nella rete locale.

## <a name="to-add-or-remove-a-root-certificate"></a><a name="addremovecert"></a>Per aggiungere o rimuovere un certificato radice

È possibile aggiungere e rimuovere certificati radice attendibili da Azure. Quando si rimuove un certificato radice, i client con un certificato generato da tale certificato radice non possono eseguire l'autenticazione e non potranno connettersi. Per consentire ai client di eseguire l'autenticazione e connettersi, è necessario installare un nuovo certificato client generato da un certificato radice considerato attendibile (caricato) in Azure. Per eseguire questa procedura, è necessario che i cmdlet di Azure PowerShell siano installati localmente nel computer (non Azure Cloud Shell). È anche possibile usare la portale di Azure per aggiungere certificati radice.

**Per aggiungere:**

In Azure è possibile aggiungere fino a 20 file CER di certificato radice. La procedura seguente consente di aggiungere un certificato radice. 

1. Preparare il file CER da caricare:

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert3.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64_3 = [system.convert]::ToBase64String($cert.RawData)
   ```

1. Caricare il file. È possibile caricare un solo file alla volta.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG1" -PublicCertData $CertBase64_3
   ```

1. Per verificare che il file del certificato sia stato caricato:

   ```azurepowershell
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG1" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

**Per rimuovere:**

1. Dichiarare le variabili. Modificare le variabili nell'esempio in modo che corrispondano al certificato che si desidera rimuovere.

   ```azurepowershell-interactive
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   $P2SRootCertName2 = "ARMP2SRootCert2.cer"
   $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
   ```

1. Rimuovere il certificato.

   ```azurepowershell-interactive
   Remove-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
   ```

1. Usare l'esempio seguente per verificare che il certificato sia stato rimosso correttamente.

   ```azurepowershell-interactive
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG1" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

## <a name="to-revoke-or-reinstate-a-client-certificate"></a><a name="revoke"></a>Per revocare o ripristinare un certificato client

È possibile revocare i certificati client. L'elenco di revoche di certificati consente di negare selettivamente la connettività da punto a sito in base ai singoli certificati client. Questa operazione è diversa rispetto alla rimozione di un certificato radice attendibile. Se si rimuove un file con estensione cer del certificato radice attendibile da Azure, viene revocato l'accesso per tutti i certificati client generati o firmati dal certificato radice revocato. La revoca di un certificato client, anziché del certificato radice, consente di continuare a usare gli altri certificati generati dal certificato radice per l'autenticazione.

La regola generale è quella di usare il certificato radice per gestire l'accesso a livello di team o organizzazione, usando i certificati client revocati per il controllo di accesso con granularità fine su singoli utenti.

**Per revocare:**

1. Ottenere l'identificazione personale del certificato client. Per ulteriori informazioni, vedere [come recuperare l'identificazione personale di un certificato](https://msdn.microsoft.com/library/ms734695.aspx).

1. Copiare le informazioni in un editor di testo e rimuovere tutti gli spazi in modo che sia una stringa continua. Questa stringa viene dichiarata come variabile nel passaggio successivo.

1. Dichiarare le variabili. Assicurarsi di dichiarare l'identificazione personale ottenuta nel passaggio precedente.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```

1. Aggiungere l'identificazione personale all'elenco dei certificati revocati. Viene visualizzato il messaggio "Operazione completata" quando l'identificazione personale è stata aggiunta.

   ```azurepowershell-interactive
   Add-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
   -Thumbprint $RevokedThumbprint1
   ```

1. Verificare che l'identificazione personale sia stata aggiunta all'elenco di revoche di certificati.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

1. Dopo aver aggiunto l'identificazione personale, il certificato non può più essere usato per la connessione. Ai client che provano a connettersi con questo certificato verrà visualizzato un messaggio che informa che il certificato non è più valido.

**Per ripristinare:**

È possibile reintegrare un certificato client rimuovendo l'identificazione personale dall'elenco dei certificati client revocati.

1. Dichiarare le variabili. Assicurarsi di dichiarare l'identificazione personale corretta per il certificato che si vuole reintegrare.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```

1. Rimuovere l'identificazione personale del certificato dall'elenco di revoche di certificati.

   ```azurepowershell-interactive
   Remove-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
   ```

1. Verificare che l'identificazione personale sia stata rimossa dall'elenco di quelle revocate.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

## <a name="point-to-site-faq"></a><a name="faq"></a>Domande frequenti sulla connettività da punto a sito

Per informazioni aggiuntive da punto a sito, vedere [domande frequenti sul gateway VPN](vpn-gateway-vpn-faq.md#P2S)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per altre informazioni, vedere [Macchine virtuali](https://docs.microsoft.com/azure/). Per altre informazioni sulla rete e sulle macchine virtuali, vedere [Panoramica di rete delle macchine virtuali Linux e Azure](../virtual-machines/linux/azure-vm-network-overview.md).

Per informazioni sulla risoluzione dei problemi della connessione da punto a sito, vedere [Risoluzione dei problemi: problemi di connessione da punto a sito di Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
