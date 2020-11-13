---
title: "Connettersi a una VNet usando l'autenticazione del certificato & VPN P2S: portale"
titleSuffix: Azure VPN Gateway
description: Connettere i client Windows, macOS e Linux in modo sicuro a una rete virtuale di Azure usando P2S e certificati autofirmati o emessi da un'autorità di certificazione. Questo articolo usa il portale di Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/09/2020
ms.author: cherylmc
ms.openlocfilehash: c9a77fc8d6e832165a78b9d83cc0a22b31b65362
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94556001"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-azure-portal"></a>Configurare una connessione VPN da punto a sito a una VNet usando l'autenticazione del certificato di Azure nativo: portale di Azure

Questo articolo consente di connettere in modo sicuro i singoli client che eseguono Windows, Linux o macOS a una VNet di Azure. Le connessioni VPN da punto a sito sono utili per connettersi alla rete virtuale da una posizione remota, ad esempio nel caso di telecomunicazioni da casa o durante una riunione. È anche possibile usare una VPN da punto a sito al posto di una VPN da sito a sito quando solo pochi client devono connettersi a una rete virtuale. Le connessioni da punto a sito non richiedono un dispositivo VPN o un indirizzo IP pubblico. La modalità da punto a sito crea la connessione VPN tramite SSTP (Secure Sockets Tunneling Protocol) o IKEv2. Per altre informazioni sulle connessioni VPN da punto a sito, vedere [Informazioni sulla VPN da punto a sito](point-to-site-about.md).

:::image type="content" source="./media\vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-diagram.png" alt-text="Connettersi da un computer a un diagramma di connessione da punto a sito di Azure VNet":::

C:\Users\cherylmc\Documents\GitHub\azure-docs-pr\articles\vpn-gateway\media\vpn-gateway-howto-point-to-site-resource-manager-portal

Per ulteriori informazioni sulla VPN da punto a sito, vedere [informazioni sulla VPN da punto a sito](point-to-site-about.md). Per creare questa configurazione usando il Azure PowerShell, vedere [configurare una VPN da punto a sito con Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md).

[!INCLUDE [P2S basic architecture](../../includes/vpn-gateway-p2s-architecture.md)]

## <a name="prerequisites"></a>Prerequisiti

Verificare di possedere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial).

### <a name="example-values"></a><a name="example"></a>Valori di esempio

È possibile usare i valori seguenti per creare un ambiente di test o fare riferimento a questi valori per comprendere meglio gli esempi di questo articolo:

* **Nome VNet:** VNet1
* **Spazio di indirizzi:** 10.1.0.0/16<br>Per questo esempio, viene usato un solo spazio indirizzi. È possibile avere più di uno spazio indirizzi per la rete virtuale.
* **Nome della subnet:** FrontEnd
* **Intervallo di indirizzi subnet:** 10.1.0.0/24
* **Sottoscrizione:** se si hanno più sottoscrizioni, verificare di usare quella corretta.
* **Gruppo di risorse:** TestRG1
* **Percorso:** Stati Uniti orientali
* **GatewaySubnet:** 10.1.255.0/27<br>
* **Nome gateway di rete virtuale:** VNet1GW
* **Tipo di gateway:** VPN
* **Tipo di VPN:** basato su route
* **Nome indirizzo IP pubblico:** VNet1GWpip
* **Tipo di connessione:** Da punto a sito
* **Pool di indirizzi client:** 172.16.201.0/24<br>I client VPN che si connettono alla rete virtuale con questa connessione da punto a sito ricevono un indirizzo IP dal pool di indirizzi client.

## <a name="1-create-a-virtual-network"></a><a name="createvnet"></a>1. creare una rete virtuale

Prima di iniziare, verificare di possedere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="2-create-a-virtual-network-gateway"></a><a name="creategw"></a>2. creare un gateway di rete virtuale

Questo passaggio illustra come creare il gateway di rete virtuale per la rete virtuale. La creazione di un gateway spesso richiede anche più di 45 minuti di tempo a seconda dello SKU gateway selezionato.

>[!NOTE]
>Lo SKU del gateway Basic non supporta l'autenticazione IKEv2 o RADIUS. Se si prevede che i client Mac si connettano alla rete virtuale, non usare lo SKU Basic.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="3-generate-certificates"></a><a name="generatecert"></a>3. generare certificati

I certificati vengono usati da Azure per autenticare i client che si connettono a una rete virtuale tramite una connessione VPN da punto a sito. Dopo avere ottenuto un certificato radice, è necessario [caricare](#uploadfile) le informazioni della chiave pubblica in Azure. Il certificato radice viene quindi considerato "attendibile" da Azure per la connessione da punto a sito alla rete virtuale. È anche possibile generare i certificati client dal certificato radice considerato attendibile e quindi installarli in ogni computer client. Il certificato client viene usato per l'autenticazione del client all'avvio di una connessione alla rete virtuale. 

### <a name="generate-a-root-certificate"></a><a name="getcer"></a>Generare un certificato radice

[!INCLUDE [root-certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-client-certificates"></a><a name="generateclientcert"></a>Genera certificati client

[!INCLUDE [generate-client-cert](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="4-add-the-client-address-pool"></a><a name="addresspool"></a>4. aggiungere il pool di indirizzi client

Il pool di indirizzi client è un intervallo di indirizzi IP privati specificati dall'utente. I client che si connettono dinamicamente tramite VPN da punto a sito ricevono un indirizzo IP da questo intervallo. Usare un intervallo di indirizzi IP privati che non si sovrapponga con la posizione locale da cui viene effettuata la connessione o con la rete virtuale a cui ci si vuole connettere. Se si configurano più protocolli e SSTP è uno dei protocolli, il pool di indirizzi configurato viene suddiviso equamente tra i protocolli configurati.

1. Dopo avere creato il gateway di rete virtuale, andare alla sezione **Impostazioni** della pagina della rete virtuale. In **Impostazioni** selezionare **configurazione da punto a sito**. Selezionare **Configura ora** per aprire la pagina di configurazione.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png" alt-text="Pagina configurazione da punto a sito" lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png":::
1. Nella pagina **configurazione da punto a sito** è possibile configurare diverse impostazioni. Se non viene visualizzato il tipo di tunnel o il tipo di autenticazione in questa pagina, il gateway usa lo SKU Basic. Lo SKU Basic non supporta l'autenticazione IKEv2 o RADIUS. Se si vogliono usare queste impostazioni, è necessario eliminare e ricreare il gateway usando uno SKU del gateway diverso.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/address-pool.png" alt-text="Specificare il pool di indirizzi" lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/address-pool.png":::
1. Nella casella **pool di indirizzi** aggiungere l'intervallo di indirizzi IP privati che si vuole usare. I client VPN ricevono dinamicamente un indirizzo IP dall'intervallo specificato. Il subnet mask minimo è 29 bit per attivo/passivo e 28 bit per la configurazione attiva/attiva.
1. Passare alla sezione successiva per configurare il tipo di tunnel.

## <a name="5-configure-tunnel-type"></a><a name="tunneltype"></a>5. configurare il tipo di tunnel

Selezionare il tipo di tunnel. Le opzioni del tunnel sono OpenVPN, SSTP e IKEv2.

* Il client strongSwan in Android e Linux e il client VPN IKEv2 nativo in iOS e OSX useranno solo il tunnel IKEv2 per la connessione.
* I client Windows tentano prima di IKEv2 e se non si connettono, eseguono il fallback al SSTP.
* È possibile usare il client OpenVPN per connettersi al tipo di tunnel OpenVPN.

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/tunnel-ike.png" alt-text="Tipo di tunnel":::

## <a name="6-configure-authentication-type"></a><a name="authenticationtype"></a>6. configurare il tipo di autenticazione

In **tipo di autenticazione** selezionare **certificato di Azure**.

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/azure-certificate.png" alt-text="Tipo di autenticazione":::

## <a name="7-upload-the-root-certificate-public-certificate-data"></a><a name="uploadfile"></a>7. caricare i dati del certificato pubblico del certificato radice

È possibile caricare fino a 20 certificati radice attendibili aggiuntivi. Al termine del caricamento dei dati del certificato pubblico, Azure li può usare per autenticare i client che hanno installato un certificato client generato dal certificato radice attendibile. Caricare le informazioni della chiave pubblica del certificato radice in Azure.

1. I certificati vengono aggiunti nella pagina **Configurazione da punto a sito** nella sezione **Certificato radice**.
1. Verificare di avere esportato il certificato radice come file X.509 con codifica in base 64 (CER). È necessario esportare il certificato in questo formato per poterlo aprire con un editor di testo.
1. Aprire il certificato con un editor di testo, ad esempio il Blocco note. Durante la copia dei dati del certificato, assicurarsi di copiare il testo come unica riga continua senza ritorni a capo o avanzamenti riga. Potrebbe essere necessario modificare la visualizzazione nell'editor di testo in "Show Symbol/Show all characters" (Mostra simbolo/Mostra tutti i caratteri) per visualizzare i ritorni a capo e gli avanzamenti riga. Copiare solo la sezione seguente come un'unica riga continua:

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/notepadroot.png" alt-text="Dati del certificato" border="false":::
1. Incollare i dati del certificato nel campo **Public Certificate Data** (Dati del certificato pubblico). **Assegnare un nome** al certificato e quindi selezionare **Save (Salva** ). È possibile aggiungere fino a 20 certificati radice attendibili.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploaded.png" alt-text="Incollare i dati del certificato" border="false":::
1. Selezionare **Save (Salva** ) nella parte superiore della pagina per salvare tutte le impostazioni di configurazione.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/save.png" alt-text="Salvare la configurazione" border="false":::

## <a name="8-install-an-exported-client-certificate"></a><a name="installclientcert"></a>8. installare un certificato client esportato

Se si vuole creare una connessione da punto a sito da un computer client diverso da quello usato per generare i certificati client, è necessario installare un certificato client. Quando si installa un certificato client, è necessaria la password che è stata creata durante l'esportazione del certificato client.

Verificare che il certificato client sia stato esportato come PFX con l'intera catena di certificati (impostazione predefinita). In caso contrario, le informazioni del certificato radice non sono presenti nel computer client e il client non potrà essere autenticato correttamente.

Per la procedura di installazione, vedere [Install a client certificate](point-to-site-how-to-vpn-client-install-azure-cert.md) (Installare un certificato client).

## <a name="9-generate-and-install-the-vpn-client-configuration-package"></a><a name="clientconfig"></a>9. generare e installare il pacchetto di configurazione del client VPN

I file di configurazione del client VPN contengono le impostazioni per configurare i dispositivi per la connessione a una rete virtuale tramite una connessione P2S. Per le istruzioni per generare e installare i file di configurazione del client VPN, vedere [Create and install VPN client configuration files for native Azure certificate authentication P2S configurations](point-to-site-vpn-client-configuration-azure-cert.md) (Creare e installare i file di configurazione del client VPN per le configurazioni P2S per l'autenticazione del certificato di Azure nativo).

## <a name="10-connect-to-azure"></a><a name="connect"></a>10. connettersi ad Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Per connettersi da un client VPN Windows

[!INCLUDE [Connect from a Windows client](../../includes/vpn-gateway-p2s-connect-windows-client.md)]

[!INCLUDE [verifies client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Per connettersi da un client VPN Mac

Nella finestra di dialogo rete individuare il profilo client che si desidera utilizzare, specificare le impostazioni dall' [VpnSettings.xml](point-to-site-vpn-client-configuration-azure-cert.md#installmac), quindi selezionare **Connetti**.

Per istruzioni dettagliate, vedere [Installazione in Mac (OS X)](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-vpn-client-configuration-azure-cert#installmac). Se si riscontrano problemi di connessione, verificare che il gateway di rete virtuale non usi uno SKU Basic. Lo SKU Basic non è supportato per i client Mac.

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png" alt-text="Connessione client VPN Mac" border="false":::

## <a name="to-verify-your-connection"></a><a name="verify"></a>Per verificare la connessione

Queste istruzioni si applicano ai client Windows.

1. Per verificare che la connessione VPN è attiva, aprire un prompt dei comandi con privilegi elevati ed eseguire *ipconfig/all*.
2. Visualizzare i risultati. Si noti che l'indirizzo IP ricevuto è uno degli indirizzi compresi nel pool di indirizzi del client VPN da punto a sito specificato al momento della configurazione. I risultati sono simili a questo esempio:

   ```
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
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Per connettersi a una macchina virtuale

Queste istruzioni si applicano ai client Windows.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* Verificare che il pacchetto di configurazione del client VPN sia stato generato dopo che sono stati specificati gli indirizzi IP del server DNS per la rete virtuale. Se gli indirizzi IP del server DNS sono stati aggiornati, generare e installare un nuovo pacchetto di configurazione del client VPN.

* Usare "ipconfig" per controllare l'indirizzo IPv4 assegnato alla scheda Ethernet nel computer da cui viene effettuata la connessione. Se l'indirizzo IP è compreso nell'intervallo di indirizzi della rete virtuale a cui ci si connette o nell'intervallo di indirizzi del pool di indirizzi del client VPN, si verifica la cosiddetta sovrapposizione dello spazio indirizzi. Con questo tipo di sovrapposizione, il traffico di rete non raggiunge Azure e rimane nella rete locale.

## <a name="to-add-or-remove-trusted-root-certificates"></a><a name="add"></a>Per aggiungere o rimuovere certificato radice attendibili

È possibile aggiungere e rimuovere certificati radice attendibili da Azure. Quando si rimuove un certificato radice, i client con un certificato generato da tale radice non potranno eseguire l'autenticazione e quindi non potranno connettersi. Per consentire ai client di eseguire l'autenticazione e connettersi, è necessario installare un nuovo certificato client generato da un certificato radice considerato attendibile (caricato) in Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Per aggiungere un certificato radice attendibile

In Azure è possibile aggiungere fino a 20 file CER di certificato radice trusted. Per istruzioni, vedere la sezione relativa al [caricamento di un certificato radice attendibile](#uploadfile) in questo articolo.

### <a name="to-remove-a-trusted-root-certificate"></a>Per rimuovere un certificato radice attendibile

1. Per rimuovere un certificato radice attendibile, passare alla pagina **Configurazione da punto a sito** per il gateway di rete virtuale.
1. Nella sezione **Certificato radice** della pagina individuare il certificato che si vuole rimuovere.
1. Selezionare i puntini di sospensione accanto al certificato e quindi fare clic su "Rimuovi".

## <a name="to-revoke-a-client-certificate"></a><a name="revokeclient"></a>Per revocare un certificato client

È possibile revocare i certificati client. L'elenco di revoche di certificati consente di negare in modo selettivo la connettività da punto a sito basata sui singoli certificati client. Questa operazione è diversa rispetto alla rimozione di un certificato radice attendibile. Se si rimuove un file con estensione cer del certificato radice attendibile da Azure, viene revocato l'accesso per tutti i certificati client generati o firmati dal certificato radice revocato. La revoca di un certificato client, anziché del certificato radice, consente di continuare a usare gli altri certificati generati dal certificato radice per l'autenticazione.

La regola generale è quella di usare il certificato radice per gestire l'accesso a livello di team o organizzazione, usando i certificati client revocati per il controllo di accesso con granularità fine su singoli utenti.

### <a name="revoke-a-client-certificate"></a>Revocare un certificato client

È possibile revocare un certificato client aggiungendo l'identificazione personale all'elenco di revoche di certificati.

1. Ottenere l'identificazione personale del certificato client. Per ulteriori informazioni, vedere [come recuperare l'identificazione personale di un certificato](https://msdn.microsoft.com/library/ms734695.aspx).
1. Copiare le informazioni in un editor di testo e rimuovere tutti gli spazi in modo che sia una stringa continua.
1. Passare al gateway di rete virtuale nella pagina **Configurazione da punto a sito** che è stata usata anche per [caricare un certificato radice attendibile](#uploadfile).
1. Nella sezione **Certificati revocati** immettere un nome descrittivo per il certificato (non deve corrispondere necessariamente alle credenziali del certificato).
1. Copiare e incollare la stringa di identificazione personale nel campo **Identificazione personale**.
1. L'identificazione personale viene convalidata e aggiunta automaticamente all'elenco di revoche. Verrà visualizzato un messaggio che segnala che è in corso l'aggiornamento dell'elenco. 
1. Dopo aver completato l'aggiornamento, il certificato non può più essere usato per la connessione. Ai client che provano a connettersi con questo certificato verrà visualizzato un messaggio che informa che il certificato non è più valido.

## <a name="point-to-site-faq"></a><a name="faq"></a>Domande frequenti sulla connettività da punto a sito

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per altre informazioni, vedere [Macchine virtuali](https://docs.microsoft.com/azure/). Per altre informazioni sulla rete e sulle macchine virtuali, vedere [Panoramica di rete delle macchine virtuali Linux e Azure](../virtual-machines/linux/azure-vm-network-overview.md).

Per informazioni sulla risoluzione dei problemi della connessione da punto a sito, vedere [Risoluzione dei problemi di connessione da punto a sito di Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
