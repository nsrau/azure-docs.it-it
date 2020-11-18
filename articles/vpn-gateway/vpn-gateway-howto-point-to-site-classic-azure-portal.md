---
title: 'Connettere un computer a una rete virtuale usando P2S: autenticazione del certificato: portale di Azure classica'
titleSuffix: Azure VPN Gateway
description: Creare una connessione gateway VPN da punto a sito classica usando il portale di Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 42b0945de55775f55f20cefdeb547cb5d6492c06
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657075"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>Configurare una connessione da punto a sito usando l'autenticazione del certificato (versione classica)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Questo articolo illustra come creare una rete virtuale con una connessione da punto a sito. Per creare questo VNet con il modello di distribuzione classica, usare il portale di Azure. Questa configurazione usa i certificati autofirmati o rilasciati da un'autorità di certificazione per autenticare il client di connessione. È anche possibile creare questa configurazione con un diverso modello o strumento di distribuzione usando le opzioni descritte negli articoli seguenti:

> [!div class="op_single_selector"]
> * [Portale di Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Portale di Azure (classico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

Si usa un gateway VPN da punto a sito (P2S) per creare una connessione sicura alla rete virtuale da un singolo computer client. Le connessioni VPN da punto a sito sono utili per connettersi alla rete virtuale da una posizione remota. Una VPN P2S è una soluzione utile da usare al posto di una VPN da sito a sito quando solo pochi client devono connettersi a una rete virtuale. Una connessione VPN P2S viene stabilita avviandola dal computer client.

> [!IMPORTANT]
> Il modello di distribuzione classica supporta solo i client VPN Windows e usa SSTP (Secure Socket Tunneling Protocol), un protocollo VPN basato su SSL. Per supportare i client VPN non Windows, è necessario creare la rete virtuale con il modello di distribuzione Resource Manager. Il modello di distribuzione Resource Manager supporta VPN IKEv2, oltre a SSTP. Per altre informazioni, vedere [Informazioni sulle connessioni da punto a sito](point-to-site-about.md).
>
>

![Diagramma da punto a sito](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

## <a name="settings-and-requirements"></a>Impostazioni e requisiti

### <a name="requirements"></a>Requisiti

Per le connessioni di autenticazione con certificati da punto a sito sono necessari gli elementi seguenti. Questo articolo illustra i passaggi che consentono di crearli.

* Un gateway VPN dinamico.
* La chiave pubblica (file CER) per un certificato radice, caricato in Azure. Questa chiave viene considerata un certificato attendibile e viene usata per l'autenticazione.
* Un certificato client generato dal certificato radice e installato in ogni computer client che effettuerà la connessione. Questo certificato viene usato per l'autenticazione client.
* Un pacchetto di configurazione client VPN deve essere generato e installato in ogni computer client che effettua la connessione. Il pacchetto di configurazione client configura il client VPN nativo già disponibile nel sistema operativo con le informazioni necessarie per la connessione alla rete virtuale.

Le connessioni da punto a sito non richiedono un dispositivo VPN o un indirizzo IP pubblico locale. La connessione VPN viene creata tramite SSTP (Secure Sockets Tunneling Protocol). Sul lato server sono supportate le versioni 1.0, 1.1 e 1.2 di SSTP. Il client decide quale versione usare. Per Windows 8.1 e versioni successive, SSTP usa per impostazione predefinita la versione 1.2.

Per ulteriori informazioni, vedere [informazioni sulle connessioni da punto a sito](point-to-site-about.md) e le [domande frequenti](#faq).

### <a name="example-settings"></a>Impostazioni di esempio

Usare i valori seguenti per creare un ambiente di test o fare riferimento a questi valori per comprendere meglio gli esempi di questo articolo:

* **Gruppo di risorse:** TestRG
* **Nome VNet:** VNet1
* **Spazio di indirizzi:** 192.168.0.0/16 <br>Per questo esempio, viene usato un solo spazio indirizzi. È possibile avere più di uno spazio indirizzi per la rete virtuale.
* **Nome della subnet:** FrontEnd
* **Intervallo di indirizzi subnet:** 192.168.1.0/24
* **GatewaySubnet:** 10.11.255.0/27
* **Area:** (Stati Uniti) Stati Uniti orientali
* **Spazio indirizzi client:** 172.16.201.0/24 <br> I client VPN che si connettono alla rete virtuale con questa connessione da punto a sito ricevono un indirizzo IP dal pool specificato.
* **Tipo di connessione**: selezionare **da punto a sito**.
* **Intervallo di indirizzi GatewaySubnet (blocco CIDR):** 192.168.200.0/24

Prima di iniziare, verificare di possedere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial).

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Creare una rete virtuale

Se si ha già una rete virtuale, verificare che le impostazioni siano compatibili con la progettazione del gateway VPN. Prestare particolare attenzione alle subnet che potrebbero sovrapporsi ad altre reti.

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="create-a-vpn-gateway"></a><a name="gateway"></a>Creare un gateway VPN

1. Passare al VNet creato.
1. Nella pagina VNet, in impostazioni, selezionare **gateway**. Nella pagina **gateway** è possibile visualizzare il gateway per la rete virtuale. Questa rete virtuale non ha ancora un gateway. Fare clic sulla nota che dice **fare clic qui per aggiungere una connessione e un gateway**.
1. Nella pagina **Configura una connessione VPN e un gateway** selezionare le impostazioni seguenti:

   * Tipo di connessione: Da punto a sito
   * Spazio indirizzi client: aggiungere l'intervallo di indirizzi IP da cui i client VPN ricevono un indirizzo IP durante la connessione. Usare un intervallo di indirizzi IP privati che non si sovrapponga con la posizione locale da cui viene effettuata la connessione o con la rete virtuale a cui ci si connette.
1. Lasciare selezionata la casella di controllo non **configurare un gateway in questo momento** . Si creerà un gateway.
1. Nella parte inferiore della pagina fare clic su **Next: Gateway >**.
1. Nella scheda **gateway** selezionare i valori seguenti:

   * **Dimensioni:** La dimensione è lo SKU del gateway per il gateway di rete virtuale. Nel portale di Azure, lo SKU predefinito è **Predefinito**. Per altre informazioni sugli SKU del gateway, vedere [informazioni sulle impostazioni del gateway VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
   * **Tipo di routing:** È necessario selezionare **dinamico** per una configurazione da punto a sito. Il routing statico non funzionerà.
   * **Subnet del gateway:** Questo campo è già riempito automaticamente. Non è possibile modificare il nome. Se si tenta di modificare il nome usando PowerShell o qualsiasi altro mezzo, il gateway non funzionerà correttamente.
   * **Intervallo di indirizzi (blocco CIDR):** Sebbene sia possibile creare una subnet del gateway con dimensioni pari a/29, è consigliabile creare una subnet più grande che includa più indirizzi selezionando almeno/28 o/27. In questo modo saranno disponibili indirizzi sufficienti per supportare in futuro le possibili configurazioni aggiuntive desiderate. Quando si usano le subnet del gateway, evitare di associare un gruppo di sicurezza di rete (NSG) alla subnet del gateway. Se si associa un gruppo di sicurezza di rete a tale subnet, il gateway VPN potrebbe non funzionare come previsto.
1. Selezionare **Rivedi e crea** per convalidare le impostazioni.
1. Al termine della convalida, selezionare **Crea**. Per il completamento di un gateway VPN possono essere necessari fino a 45 minuti, in base allo SKU selezionato per il gateway.

## <a name="create-certificates"></a><a name="generatecerts"></a>Creare i certificati

Azure usa i certificati per autenticare client VPN per VPN da punto a sito. È necessario caricare le informazioni della chiave pubblica del certificato radice in Azure. La chiave pubblica viene quindi considerata *attendibile*. I certificati client devono essere generati dal certificato radice attendibile e quindi installati in ogni computer client nell'archivio certificati Certificati - Utente corrente\Personale\Certificati. Il certificato viene usato per l'autenticazione del client alla connessione alla rete virtuale. 

Se usati, i certificati autofirmati devono essere creati con parametri specifici. È possibile creare un certificato autofirmato seguendo le istruzioni per [PowerShell e Windows 10](vpn-gateway-certificates-point-to-site.md) o [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). È importante seguire i passaggi di queste istruzioni quando si usano i certificati radice autofirmati e si generano certificati client dal certificato radice autofirmato. In caso contrario, i certificati creati non saranno compatibili con le connessioni P2S e si riceverà un errore di connessione.

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>Ottenere il file di chiave pubblica (con estensione cer) per il certificato radice

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>Generare un certificato client

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>Caricare il file CER del certificato radice

Dopo la creazione del gateway, caricare il file con estensione cer, che contiene le informazioni sulla chiave pubblica, per un certificato radice attendibile nel server di Azure. Non caricare la chiave privata per il certificato radice. Una volta caricato un certificato, Azure lo usa per autenticare i client che hanno installato un certificato client generato dal certificato radice attendibile. È possibile caricare in seguito fino a 20 file di certificato radice attendibile aggiuntivi, se necessario.

1. Passare alla rete virtuale creata.
1. In **Impostazioni** selezionare **connessioni da punto a sito**.
1. Selezionare **Gestisci certificato**.
1. Selezionare **Carica**.
1. Nel riquadro **carica un certificato** selezionare l'icona della cartella e passare al certificato che si vuole caricare.
1. Selezionare **Carica**.
1. Una volta completato il caricamento del certificato, è possibile visualizzarlo nella pagina Gestisci certificato. Potrebbe essere necessario selezionare **Aggiorna** per visualizzare il certificato appena caricato.

## <a name="configure-the-client"></a>Configurare il client

Per connettersi a una rete virtuale tramite VPN da punto a sito, ogni client deve installare un pacchetto per configurare il client VPN di Windows nativo. Il pacchetto di configurazione configura il client VPN nativo di Windows con le impostazioni necessarie per la connessione alla rete virtuale.

È possibile usare lo stesso pacchetto di configurazione del client VPN in ogni computer client, a condizione che la versione corrisponda all'architettura del client. Per l'elenco dei sistemi operativi client supportati, vedere [informazioni sulle connessioni da punto a sito](point-to-site-about.md) e le [domande frequenti](#faq).

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>Generare e installare un pacchetto di configurazione del client VPN

1. Passare alle impostazioni di **connessione da punto a sito** per la VNet.
1. Nella parte superiore della pagina selezionare il pacchetto di download corrispondente al sistema operativo client in cui verrà installato:

   * Per i client a 64 bit, selezionare **client VPN (64-bit)**.
   * Per i client a 32 bit, selezionare **client VPN (32-bit)**.

1. Azure genera un pacchetto con le impostazioni specifiche richieste dal client. Ogni volta che si apportano modifiche a VNet o gateway, è necessario scaricare un nuovo pacchetto di configurazione client e installarlo nei computer client.
1. Dopo la generazione del pacchetto, selezionare **Scarica**.
1. Installare il pacchetto di configurazione client nel computer client. Quando si installa, se viene visualizzato un popup SmartScreen che indica che Windows ha protetto il PC, selezionare **altre informazioni**, quindi selezionare **Esegui comunque**. È anche possibile salvare il pacchetto per l'installazione su altri computer client.

### <a name="install-a-client-certificate"></a>Installare un certificato client

Per questo esercizio, quando è stato generato il certificato client, è stato installato automaticamente nel computer. Per creare una connessione P2S da un computer client diverso da quello utilizzato per generare i certificati client, è necessario installare il certificato client generato nel computer.

Quando si installa un certificato client, è necessaria la password che è stata creata durante l'esportazione del certificato client. In genere, è possibile installare il certificato semplicemente facendo doppio clic su di esso. Per altre informazioni, vedere [installare un certificato client esportato](vpn-gateway-certificates-point-to-site.md#install).

## <a name="connect-to-your-vnet"></a>Connettersi alla rete virtuale

>[!NOTE]
>È necessario avere diritti di amministratore per il computer client da cui ci si connette.
>

1. Nel computer client passare a impostazioni VPN.
1. Selezionare la VPN creata. Se sono state usate le impostazioni di esempio, la connessione verrà etichettata come **gruppo TestRG VNet1**.
1. Selezionare **Connetti**.
1. Nella casella rete virtuale di Microsoft Azure selezionare **Connetti**. Se viene visualizzato un messaggio popup relativo al certificato, selezionare **continua** per usare privilegi elevati e **Sì** per accettare le modifiche di configurazione.
1. Quando la connessione ha esito positivo, viene visualizzata una notifica **connessa** .

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>Verificare la connessione VPN

1. Verificare che la connessione VPN sia attiva. Aprire un prompt dei comandi con privilegi elevati nel computer client ed eseguire **ipconfig/all**.
1. Visualizzare i risultati. Si noti che l'indirizzo IP ricevuto è uno degli indirizzi compresi nell'intervallo di indirizzi di connettività da punto a sito specificato al momento della creazione della rete virtuale. I risultati dovrebbero essere simili all'esempio seguente:

   ```
    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a>Per connettersi a una macchina virtuale

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="to-add-or-remove-trusted-root-certificates"></a>Per aggiungere o rimuovere certificato radice attendibili

È possibile aggiungere e rimuovere certificati radice attendibili da Azure. Quando si rimuove un certificato radice, i client con un certificato generato da tale radice non potranno più eseguire l'autenticazione e connettersi. Per consentire ai client di eseguire l'autenticazione e connettersi nuovamente, è necessario installare un nuovo certificato client generato da un certificato radice considerato attendibile in Azure.

### <a name="add-a-trusted-root-certificate"></a>Aggiunta di un certificato radice attendibile

È possibile aggiungere fino a 20 file CER del certificato radice attendibile in Azure usando lo stesso processo usato per aggiungere il primo certificato radice attendibile.

### <a name="remove-a-trusted-root-certificate"></a>Rimuovere un certificato radice attendibile

1. Nella sezione **connessioni da punto a sito** della pagina relativa a VNet selezionare **Gestisci certificato**.
1. Selezionare i puntini di sospensione accanto al certificato che si vuole rimuovere, quindi selezionare **Elimina**.

## <a name="to-revoke-a-client-certificate"></a>Per revocare un certificato client

Se necessario, è possibile revocare un certificato client. L'elenco di revoche di certificati consente di negare in modo selettivo la connettività da punto a sito basata sui singoli certificati client. Questo metodo è diverso rispetto alla rimozione di un certificato radice attendibile. Se si rimuove un file con estensione cer del certificato radice attendibile da Azure, viene revocato l'accesso per tutti i certificati client generati o firmati dal certificato radice revocato. La revoca di un certificato client, anziché del certificato radice, consente di continuare a usare gli altri certificati generati dal certificato radice per l'autenticazione per la connessione da punto a sito.

La regola generale è quella di usare il certificato radice per gestire l'accesso a livello di team o organizzazione, usando i certificati client revocati per il controllo di accesso con granularità fine su singoli utenti.

È possibile revocare un certificato client aggiungendo l'identificazione personale all'elenco di revoche di certificati.

1. Ottenere l'identificazione personale del certificato client. Per altre informazioni, vedere [Procedura: recuperare l'identificazione personale di un certificato](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate).
1. Copiare le informazioni in un editor di testo e rimuovere tutti gli spazi in modo da avere una stringa continua.
1. Passare a **connessione VPN da punto a sito**, quindi selezionare **Gestisci certificato**.
1. Selezionare **Elenco di revoche** per aprire la pagina **Elenco di revoche**.
1. Nella pagina **Identificazione personale** incollare l'identificazione personale del certificato come una riga di testo continua, senza spazi.
1. Selezionare **+ Aggiungi a elenco** per aggiungere l'identificazione personale all'elenco di revoche di certificati (CRL).

Dopo aver completato l'aggiornamento, il certificato non può più essere usato per la connessione. Ai client che provano a connettersi con questo certificato verrà visualizzato un messaggio che informa che il certificato non è più valido.

## <a name="faq"></a><a name="faq"></a>Domande frequenti

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>Passaggi successivi

* Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per altre informazioni, vedere [Macchine virtuali](../index.yml).

* Per altre informazioni sulla rete e sulle macchine virtuali Linux, vedere [Panoramica di rete delle macchine virtuali Linux e Azure](../virtual-machines/network-overview.md).

* Per informazioni sulla risoluzione dei problemi della connessione da punto a sito, vedere [Risolvere i problemi di connessione da punto a sito di Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).