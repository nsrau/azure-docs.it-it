---
title: 'Connettere un computer a una rete virtuale di Azure da punto a sito: portale | Documentazione Microsoft'
description: Connettersi in modo sicuro a una rete virtuale di Azure creando una connessione gateway VPN da punto a sito con Resource Manager e il portale di Azure.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a15ad327-e236-461f-a18e-6dbedbf74943
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/27/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 4045dd501ba70b04cb8ea06901c76072fc009018
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017


---
<a id="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal" class="xliff"></a>

# Configurare una connessione da punto a sito a una rete virtuale usando il portale di Azure

Questo articolo illustra come creare una rete virtuale con una connessione da punto a sito nel modello di distribuzione Resource Manager usando il portale di Azure. È anche possibile creare questa configurazione usando strumenti o modelli di distribuzione diversi selezionando un'opzione differente nell'elenco seguente:

> [!div class="op_single_selector"]
> * [Portale di Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Portale di Azure (classico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>
>

Una configurazione da punto a sito (P2S) permette di creare una connessione protetta da un singolo computer client a una rete virtuale. Una soluzione Da punto a sito è utile quando ci si vuole connettere alla rete virtuale da una posizione remota, ad esempio da casa o durante una riunione, oppure quando solo pochi client devono connettersi a una rete virtuale. La connessione VPN da punto a sito viene avviata dal computer client usando il client VPN di Windows nativo. I client che si connettono usano i certificati per eseguire l'autenticazione. 


![Diagramma da punto a sito](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-connection-diagram.png)

Le connessioni da punto a sito non richiedono un dispositivo VPN o un indirizzo IP pubblico. La modalità Da punto a sito crea la connessione VPN tramite SSTP (Secure Sockets Tunneling Protocol). Sul lato server sono supportate le versioni 1.0, 1.1 e 1.2 di SSTP. Il client decide quale versione usare. Per Windows 8.1 e versioni successive, SSTP usa per impostazione predefinita la versione 1.2. Per altre informazioni sulla connettività da punto a sito, consultare le [Domande frequenti sulla connettività da punto a sito](#faq) alla fine di questo articolo.

Le connessioni da punto a sito richiedono gli elementi seguenti:

* Un gateway VPN RouteBased.
* La chiave pubblica (file CER) per un certificato radice, caricato in Azure. Questo viene considerato un certificato attendibile e viene usato per l'autenticazione.
* Un certificato client generato dal certificato radice e installato in ogni computer client che effettuerà la connessione. Questo certificato viene usato per l'autenticazione client.
* Un pacchetto di configurazione client VPN deve essere generato e installato in ogni computer client che effettua la connessione. Il pacchetto di configurazione client configura il client VPN nativo già disponibile nel sistema operativo con le informazioni necessarie per la connessione alla rete virtuale.

### <a name="example"></a>Valori di esempio

È possibile usare i valori seguenti per creare un ambiente di test o fare riferimento a questi valori per comprendere meglio gli esempi di questo articolo:

* **Nome: VNet1**
* **Spazio indirizzi: 192.168.0.0/16**<br>Per questo esempio, viene usato un solo spazio indirizzi. È possibile avere più di uno spazio indirizzi per la rete virtuale.
* **Nome subnet: FrontEnd**
* **Intervallo di indirizzi subnet: 192.168.1.0/24**
* **Sottoscrizione:** se si hanno più sottoscrizioni, verificare di usare quella corretta.
* **Gruppo di risorse: TestRG**
* **Località: Stati Uniti orientali**
* **GatewaySubnet: 192.168.200.0/24**
* **Nome gateway di rete virtuale: VNet1GW**
* **Tipo di gateway: VPN**
* **Tipo VPN: Basato su route**
* **Indirizzo IP pubblico: VNet1GWpip**
* **Tipo di connessione: Da punto a sito**
* **Pool di indirizzi client: 172.16.201.0/24**<br>I client VPN che si connettono alla rete virtuale con questa connessione da punto a sito ricevono un indirizzo IP dal pool di indirizzi client.

## <a name="createvnet"></a>1 - Creare una rete virtuale

Prima di iniziare, verificare di possedere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial). Se si crea questa configurazione come esercizio, è possibile fare riferimento ai [valori di esempio](#example).

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="address"></a>2 - Specificare lo spazio di indirizzi e le subnet

È possibile aggiungere altri spazi degli indirizzi e subnet alla rete virtuale dopo che è stata creata.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>3 - Aggiungere una subnet del gateway

Prima di connettere la rete virtuale a un gateway, è necessario creare la subnet del gateway per la rete virtuale con cui si vuole stabilire la connessione. Il servizio del gateway usa gli indirizzi IP specificati nella subnet del gateway. Se possibile, creare una subnet del gateway con un blocco CIDR di /28 o /27 per fornire indirizzi IP sufficienti a soddisfare altri requisiti di configurazione futuri.

Gli screenshot di questa sezione servono come esempio di riferimento. Assicurarsi di usare l'intervallo di indirizzi GatewaySubnet corrispondente ai valori obbligatori per la configurazione.

<a id="to-create-a-gateway-subnet" class="xliff"></a>

### Per creare una subnet del gateway

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="dns"></a>4 - Specificare un server DNS (facoltativo)

Dopo aver creato la rete virtuale, è possibile aggiungere l'indirizzo IP di un server DNS per gestire la risoluzione dei nomi. Il server DNS specificato deve essere un server DNS in grado di risolvere i nomi per le risorse a cui ci si connette. Il pacchetto di configurazione del client VPN generato in un passaggio successivo conterrà l'indirizzo IP dei server DNS specificati in questa impostazione. Se è necessario aggiornare l'elenco di server DNS in futuro, è possibile generare e installare nuovi pacchetti di configurazione del client VPN che riflettono il nuovo elenco.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="creategw"></a>5 - Creare un gateway di rete virtuale

Le connessioni da punto a sito richiedono le impostazioni seguenti.

* Tipo di gateway: VPN
* Tipo VPN: Basato su route

<a id="to-create-a-virtual-network-gateway" class="xliff"></a>

### Per creare un gateway di rete virtuale

[!INCLUDE [create a vnet gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="generatecert"></a>6 - Generare i certificati

I certificati vengono usati da Azure per autenticare i client VPN per VPN da punto a sito. È necessario caricare le informazioni della chiave pubblica del certificato radice in Azure. La chiave pubblica viene quindi considerata "attendibile". I certificati client devono essere generati dal certificato radice attendibile e quindi installati in ogni computer client nell'archivio certificati Certificati - Utente corrente/Personale. Il certificato viene usato per l'autenticazione del client all'avvio di una connessione alla rete virtuale. 

Se usati, i certificati autofirmati devono essere creati con parametri specifici. È possibile creare un certificato autofirmato seguendo le istruzioni per [PowerShell e Windows 10](vpn-gateway-certificates-point-to-site.md) o [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). È importante seguire i passaggi di queste istruzioni quando si usano i certificati radice autofirmati e si generano certificati client dal certificato radice autofirmato. In caso contrario, i certificati creati non saranno compatibili con le connessioni P2S e si riceverà un errore di connessione.

### <a name="getcer"></a>Passaggio 1: Ottenere il file CER per il certificato radice

[!INCLUDE [obtain root certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generateclientcert"></a>Passaggio 2: Generare un certificato client

[!INCLUDE [generate client certificate](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="addresspool"></a>7 - Aggiungere il pool di indirizzi client

Il pool di indirizzi client è un intervallo di indirizzi IP privati specificati dall'utente. I client che si connettono tramite connessione da punto a sito ricevono un indirizzo IP da questo intervallo. Usare un intervallo di indirizzi IP privati che non si sovrapponga con la posizione locale da cui verrà effettuata la connessione o con la rete virtuale a cui ci si vuole connettere.

1. Dopo avere creato il gateway di rete virtuale, andare alla sezione **Impostazioni** del pannello della rete virtuale. Nella sezione **Impostazioni** fare clic su **Configurazione da punto a sito** per aprire il pannello **Configurazione**.

  ![Pannello Da punto a sito](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configuration.png)
2. È possibile eliminare l'intervallo compilato automaticamente e quindi aggiungere l'intervallo di indirizzi IP privati da usare. Fare clic su **Salva** per convalidare e salvare le impostazioni.

  ![Pool di indirizzi client](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/ipaddresspool.png)

## <a name="uploadfile"></a>8 - Caricare il file CER del certificato radice

Dopo la creazione del gateway, è possibile caricare il file con estensione cer, che contiene le informazioni sulla chiave pubblica, per un certificato radice attendibile in Azure. Al termine del caricamento di un file CER, Azure lo può usare per autenticare i client che hanno installato un certificato client generato dal certificato radice attendibile. È possibile caricare fino a 20 file di certificato radice attendibile aggiuntivi in un secondo momento, se necessario. 

1. I certificati vengono aggiunti nel pannello **Point-to-site configuration** (Configurazione da punt a sito) nella sezione **Certificato radice**.  
2. Verificare di avere esportato il certificato radice come file X.509 con codifica in base 64 (CER). È necessario esportare il certificato in questo formato per poterlo aprire con un editor di testo.
3. Aprire il certificato con un editor di testo, ad esempio il Blocco note. Durante la copia dei dati del certificato, assicurarsi di copiare il testo come unica riga continua senza ritorni a capo o avanzamenti riga. Potrebbe essere necessario modificare la visualizzazione nell'editor di testo in "Show Symbol/Show all characters" (Mostra simbolo/Mostra tutti i caratteri) per visualizzare i ritorni a capo e gli avanzamenti riga. Copiare solo la sezione seguente come un'unica riga continua:

  ![Dati del certificato](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/copycert.png)
4. Incollare i dati del certificato nel campo **Public Certificate Data** (Dati del certificato pubblico). **Dare un nome** al certificato e fare quindi clic su **Salva**. È possibile aggiungere fino a 20 certificati radice attendibili.

  ![Caricamento del certificato](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/rootcertupload.png)

## <a name="clientconfig"></a>9 - Installare il pacchetto di configurazione del client VPN

Per connettersi a una rete virtuale tramite VPN da punto a sito, ogni client deve installare un pacchetto per configurare il client VPN di Windows nativo. Il pacchetto di configurazione configura il client VPN Windows nativo con le impostazioni necessarie per la connessione alla rete virtuale e, se è stato specificato un server DNS per la rete virtuale, contiene l'indirizzo IP del server DNS che verrà usato dal client per la risoluzione dei nomi. Se si cambia in seguito il server DNS specificato, dopo la generazione del pacchetto di configurazione del client, assicurarsi di generare un nuovo pacchetto di configurazione del client da installare nei computer client.

È possibile usare lo stesso pacchetto di configurazione del client VPN in ogni computer client, a condizione che la versione corrisponda all'architettura del client. Per l'elenco dei sistemi operativi client supportati, vedere [Domande frequenti sulla connettività da punto a sito](#faq) alla fine di questo articolo.

<a id="step-1---download-the-client-configuration-package" class="xliff"></a>

### Passaggio 1: Scaricare il pacchetto di configurazione del client

1. Nel pannello **Configurazione da punto a sito** fare clic su **Download VPN client** (Scarica client VPN) per aprire il pannello **Download VPN client** (Scarica client VPN). La generazione del pacchetto richiede un paio di minuti.

  ![Download del client VPN 1](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/downloadvpnclient1.png)
2. Selezionare il pacchetto corretto per il client e quindi fare clic su **Scarica**. Salvare il file del pacchetto di configurazione. Il pacchetto di configurazione del client VPN viene installato in ogni computer client che si connette alla rete virtuale.

  ![Download del client VPN 2](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpnclient.png)

<a id="step-2---install-the-client-configuration-package" class="xliff"></a>

### Passaggio 2: Installare il pacchetto di configurazione del client

1. Copiare il file di configurazione in locale nel computer che si vuole connettere alla rete virtuale. 
2. Fare doppio clic sul file con estensione exe per installare il pacchetto nel computer client. Dato che è stato creato dall'utente, il pacchetto di configurazione non è firmato e potrebbe essere visualizzato un avviso. Se viene visualizzato un popup Windows SmartScreen, fare clic su **Altre informazioni** (a sinistra) e quindi su **Esegui comunque** per installare il pacchetto.
3. Installare il pacchetto nel computer client. Se viene visualizzato un popup Windows SmartScreen, fare clic su **Altre informazioni** (a sinistra) e quindi su **Esegui comunque** per installare il pacchetto.
4. Nel computer client passare a **Impostazioni di rete** e fare clic su **VPN**. La connessione VPN viene visualizzata con il nome della rete virtuale a cui si connette.

## <a name="installclientcert"></a>10 - Installare il certificato client

Se si vuole creare una connessione da punto a sito da un computer client diverso da quello usato per generare i certificati client, è necessario installare un certificato client. Quando si installa un certificato client, è necessaria la password che è stata creata durante l'esportazione del certificato client. In genere è sufficiente fare doppio clic sul certificato e installarlo. Per altre informazioni, vedere [Installare un certificato client esportato](vpn-gateway-certificates-point-to-site.md#install).

## <a name="connect"></a>11 - Connettersi ad Azure

1. Per connettersi alla rete virtuale, nel computer client passare alle connessioni VPN e individuare quella creata, che ha lo stesso nome della rete virtuale locale. Fare clic su **Connetti**. È possibile che venga visualizzato un messaggio popup che fa riferimento all'uso del certificato. Fare clic su **Continua** per usare privilegi elevati.

2. Nella pagina **Stato connessione** fare clic su **Connetti** per avviare la connessione. Se viene visualizzato un **Seleziona certificato** dello schermo, verificare che il certificato client visualizzato sia quello che si desidera utilizzare per la connessione. In caso contrario, usare la freccia a discesa per selezionare il certificato corretto e quindi fare clic su **OK**.

  ![Connessione del client VPN ad Azure](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/clientconnect.png)
3. Verrà stabilita la connessione.

  ![Connessione stabilita](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/connected.png)

In caso di problemi di connessione, effettuare i controlli seguenti:

- Aprire **Gestire i certificati utente** e passare ad **Autorità di certificazione radice attendibili\Certificati**. Verificare che il certificato radice sia incluso nell'elenco. Per il corretto funzionamento dell'autenticazione è necessario che il certificato radice sia presente. Quando si esporta un certificato client con estensione pfx con il valore predefinito "Se possibile, includi tutti i certificati nel percorso certificazione", vengono esportate anche le informazioni del certificato radice. Durante l'installazione del certificato client, nel computer client viene quindi installato anche il certificato radice. 

- Se si usa un certificato che è stato rilasciato tramite una soluzione CA globale e si riscontrano problemi durante l'autenticazione, controllare l'ordine di autenticazione del certificato client. È possibile controllare l'ordine dell'elenco di autenticazione facendo doppio clic sul certificato client e andando in **Dettagli > Utilizzo chiavi avanzato**. Assicurarsi che l'elenco mostri "Autenticazione client" come primo elemento. In caso contrario, è necessario emettere un certificato client in base al modello di utente con l'autenticazione client come primo elemento nell'elenco.


## <a name="verify"></a>12 - Verificare la connessione

1. Per verificare che la connessione VPN è attiva, aprire un prompt dei comandi con privilegi elevati ed eseguire *ipconfig/all*.
2. Visualizzare i risultati. Si noti che l'indirizzo IP ricevuto è uno degli indirizzi compresi nel pool di indirizzi del client VPN da punto a sito specificato al momento della configurazione. I risultati sono simili a questo esempio:
   
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


In caso di problemi durante la connessione a una macchina virtuale tramite P2S, usare "ipconfig" per controllare l'indirizzo IPv4 assegnato alla scheda Ethernet nel computer da cui viene effettuata la connessione. Se l'indirizzo IP è compreso nell'intervallo di indirizzi della rete virtuale a cui ci si connette o nell'intervallo di indirizzi del pool di indirizzi del client VPN, si verifica la cosiddetta sovrapposizione dello spazio indirizzi. Con questo tipo di sovrapposizione, il traffico di rete non raggiunge Azure e rimane nella rete locale. Se gli spazi indirizzi di rete non si sovrappongono ma non è comunque possibile connettersi alla VM, vedere l'articolo [Risolvere i problemi di connessione con Desktop remoto di una macchina virtuale di Azure](../virtual-machines/windows/troubleshoot-rdp-connection.md).

## <a name="connectVM"></a>Connettersi a una macchina virtuale

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="add"></a>Aggiungere o rimuovere certificati radice attendibili

È possibile aggiungere e rimuovere certificati radice attendibili da Azure. Quando si rimuove un certificato radice, i client con un certificato generato da tale radice non potranno eseguire l'autenticazione e quindi non potranno connettersi. Per consentire ai client di eseguire l'autenticazione e connettersi, è necessario installare un nuovo certificato client generato da un certificato radice considerato attendibile (caricato) in Azure.

<a id="to-add-a-trusted-root-certificate" class="xliff"></a>

### Per aggiungere un certificato radice attendibile

In Azure è possibile aggiungere fino a 20 file CER di certificato radice trusted. Per istruzioni, vedere la sezione relativa al [caricamento di un certificato radice attendibile](#uploadfile) in questo articolo.

<a id="to-remove-a-trusted-root-certificate" class="xliff"></a>

### Per rimuovere un certificato radice attendibile

1. Per rimuovere un certificato radice attendibile, passare al pannello **Configurazione da punto a sito** per il gateway di rete virtuale.
2. Nella sezione **Certificato radice** del pannello individuare il certificato che si vuole rimuovere.
3. Fare clic sui puntini di sospensione accanto al certificato e quindi fare clic su "Rimuovi".

## <a name="revokeclient"></a>Revocare un certificato client

È possibile revocare i certificati client. L'elenco di revoche di certificati consente di negare in modo selettivo la connettività da punto a sito basata sui singoli certificati client. Questa operazione è diversa rispetto alla rimozione di un certificato radice attendibile. Se si rimuove un file con estensione cer del certificato radice attendibile da Azure, viene revocato l'accesso per tutti i certificati client generati o firmati dal certificato radice revocato. La revoca di un certificato client, anziché del certificato radice, consente di continuare a usare gli altri certificati generati dal certificato radice per l'autenticazione.

La regola generale è quella di usare il certificato radice per gestire l'accesso a livello di team o organizzazione, usando i certificati client revocati per il controllo di accesso con granularità fine su singoli utenti.

<a id="to-revoke-a-client-certificate" class="xliff"></a>

### Per revocare un certificato client

È possibile revocare un certificato client aggiungendo l'identificazione personale all'elenco di revoche di certificati.

1. Ottenere l'identificazione personale del certificato client. Per altre informazioni, vedere [Procedura: recuperare l'identificazione personale di un certificato](https://msdn.microsoft.com/library/ms734695.aspx).
2. Copiare le informazioni in un editor di testo e rimuovere tutti gli spazi in modo che sia una stringa continua.
3. Passare al gateway di rete virtuale nel pannello **Configurazione da punto a sito** che è stato usato anche per [caricare un certificato radice attendibile](#uploadfile).
4. Nella sezione **Certificati revocati** immettere un nome descrittivo per il certificato (non deve corrispondere necessariamente alle credenziali del certificato).
5. Copiare e incollare la stringa di identificazione personale nel campo **Identificazione personale**.
6. L'identificazione personale verrà convalidata e aggiunta automaticamente all'elenco di revoche. Verrà visualizzato un messaggio che segnala che è in corso l'aggiornamento dell'elenco. 
7. Dopo aver completato l'aggiornamento, il certificato non può più essere usato per la connessione. Ai client che provano a connettersi con questo certificato verrà visualizzato un messaggio che informa che il certificato non è più valido.

## <a name="faq"></a>Domande frequenti sulla connettività da punto a sito

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

<a id="next-steps" class="xliff"></a>

## Passaggi successivi
Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per altre informazioni, vedere [Macchine virtuali](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Per altre informazioni sulla rete e sulle macchine virtuali, vedere [Panoramica di rete delle macchine virtuali Linux e Azure](../virtual-machines/linux/azure-vm-network-overview.md).

