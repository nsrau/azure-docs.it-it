---
title: 'Connettere un computer a una rete virtuale di Azure da punto a sito: portale di Azure: distribuzione classica | Documentazione Microsoft'
description: Connettersi in modo sicuro a una rete virtuale di Azure classica creando una connessione gateway VPN da punto a sito con il portale di Azure.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 65e14579-86cf-4d29-a6ac-547ccbd743bd
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/02/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: 0e55e0a3d7be1ceadf4f58517b3e2f996661791d
ms.lasthandoff: 03/03/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal"></a>Configurare una connessione da punto a sito a una rete virtuale usando il portale di Azure
> [!div class="op_single_selector"]
> * [Resource Manager - Portale di Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Classica - Portale di Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>
>

Una configurazione da punto a sito (P2S) permette di creare una connessione protetta da un singolo computer client a una rete virtuale. Una soluzione P2S è utile quando ci si vuole connettere alla rete virtuale da una posizione remota, ad esempio da casa o durante una riunione, oppure quando solo pochi client devono connettersi a una rete virtuale.

Le connessioni da punto a sito non richiedono un dispositivo VPN o un indirizzo IP pubblico per funzionare. Una connessione VPN viene stabilita avviando la connessione dal computer client. Per altre informazioni sulla connettività da punto a sito, consultare le [Domande frequenti sulla connettività da punto a sito](#faq) alla fine di questo articolo.

Questo articolo illustra la creazione di una rete virtuale con una connessione da punto a sito nel modello di distribuzione classica usando il portale di Azure.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Metodi e modelli di distribuzione per connessioni P2S
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

La tabella seguente descrive i due modelli di distribuzione e i metodi di distribuzione disponibili per le configurazioni P2S. Quando è disponibile un articolo contenente la procedura di configurazione, nella tabella è presente un collegamento diretto.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Flusso di lavoro di base
![Diagramma da punto a sito](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

Le sezioni seguenti illustrano i passaggi per creare una connessione da punto a sito sicura a una rete virtuale.

1. Creare una rete virtuale e un gateway VPN
2. Generare i certificati
3. Caricare il file CER
4. Generare il pacchetto di configurazione del client VPN
5. Configurare il computer client
6. Connettersi ad Azure

### <a name="example-settings"></a>Impostazioni di esempio
È possibile usare le impostazioni di esempio seguenti.

* **Nome: VNet1**
* **Spazio indirizzi: 192.168.0.0/16**<br>Per questo esempio, viene usato un solo spazio indirizzi. È possibile avere più di uno spazio indirizzi per la rete virtuale.
* **Nome subnet: FrontEnd**
* **Intervallo di indirizzi subnet: 192.168.1.0/24**
* **Sottoscrizione:** se si hanno più sottoscrizioni, verificare di usare quella corretta.
* **Gruppo di risorse: TestRG**
* **Località: Stati Uniti orientali**
* **Tipo di connessione: Da punto a sito**
* **: 172.16.201.0/24**. I client VPN che si connettono alla rete virtuale con questa connessione da punto a sito ricevono un indirizzo IP dal pool specificato.
* **GatewaySubnet: 192.168.200.0/24**. La subnet del gateway deve usare il nome "GatewaySubnet".
* **Dimensioni:** selezionare lo SKU di gateway da usare.
* **Tipo di routing: Dinamico**

## <a name="vnetvpn"></a>Sezione 1: Creare una rete virtuale e un gateway VPN
### <a name="createvnet"></a>Parte 1: Creare una rete virtuale
Se non si ha una rete virtuale, crearne una. Gli screenshot sono forniti come esempio. Assicurarsi di sostituire i valori con i propri. Per creare una rete virtuale usando il portale di Azure, seguire questa procedura:

1. In un browser passare al [portale di Azure](http://portal.azure.com) e, se necessario, accedere con l'account Azure.
2. Fare clic su **New**. Nel campo **Cerca nel Marketplace** digitare "Rete virtuale". Trovare **Rete virtuale** nell'elenco restituito e fare clic per aprire il pannello **Rete virtuale**.

    ![Cercare il pannello Rete virtuale](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvnetportal700.png)
3. Nella parte inferiore del pannello Rete virtuale selezionare **Classico** nell'elenco **Selezionare un modello di distribuzione** e quindi fare clic su **Crea**.

    ![Selezionare il modello di distribuzione](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png)
4. Nel pannello **Crea rete virtuale** configurare le impostazioni della rete virtuale. Nel pannello si aggiungerà il primo spazio di indirizzi e un intervallo di indirizzi di una singola subnet. Dopo aver creato la rete virtuale, è possibile tornare indietro e aggiungere altre subnet e altri spazi di indirizzi.

    ![Pannello Creare la rete virtuale](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)
5. Verificare che la **sottoscrizione** sia quella corretta. È possibile cambiare sottoscrizione tramite l'elenco a discesa.
6. Fare clic su **Gruppo di risorse** e selezionare un gruppo di risorse esistente o crearne uno nuovo digitandone il nome. Se si crea un nuovo gruppo, denominare il gruppo di risorse in base ai valori di configurazione pianificati. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Selezionare quindi le impostazioni relative alla **località** per la rete virtuale. La località determinerà la posizione in cui risiederanno le risorse distribuite in questa rete virtuale.
8. Selezionare **Aggiungi al dashboard** se si vuole trovare facilmente la rete virtuale nel dashboard e quindi fare clic su **Crea**.

    ![Aggiungi al dashboard](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png)
9. Dopo aver fatto clic su Crea, verrà visualizzato un riquadro nel dashboard che riflette lo stato di avanzamento della rete virtuale. Il riquadro cambia durante la creazione della rete virtuale.

    ![Pannello Creazione della rete virtuale](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png)
10. Dopo aver creato la rete virtuale, è possibile aggiungere l'indirizzo IP di un server DNS per gestire la risoluzione dei nomi. Aprire le impostazioni della rete virtuale, fare clic su Server DNS e aggiungere l'indirizzo IP del server DNS da usare. Questa impostazione non determina la creazione di un nuovo server DNS. Assicurarsi di aggiungere un server DNS con cui le risorse possono comunicare.

Al termine della creazione della rete virtuale, nella pagina Reti del portale di Azure classico verrà visualizzato **Creato** in **Stato**.

### <a name="gateway"></a>Parte 2: Creare una subnet del gateway e un gateway di routing dinamico
In questo passaggio si creeranno una subnet del gateway e un gateway di routing dinamico. Nel portale di Azure per il modello di distribuzione classica, la subnet del gateway e il gateway possono essere creati tramite gli stessi pannelli di configurazione.

1. Nel portale passare alla rete virtuale per cui si vuole creare un gateway.
2. Nel pannello **Panoramica** del pannello della rete virtuale fare clic su **Gateway** nella sezione Connessioni VPN.

    ![Fare clic per creare un gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. Nel pannello **Nuova connessione VPN** selezionare **Da punto a sito**.

    ![Tipo di connessione da punto a sito](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. In **Spazio indirizzi client** aggiungere l'intervallo di indirizzi IP, da cui i client VPN riceveranno un indirizzo IP al momento della connessione. Eliminare l'intervallo compilato automaticamente e aggiungere il proprio.

    ![Spazio indirizzi client](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Selezionare la casella di controllo **Crea gateway immediatamente**.

    ![Crea gateway immediatamente](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/creategwimm.png)
6. Fare clic su **Configurazione gateway facoltativa** per aprire il pannello **Configurazione gateway**.

    ![Fare clic su Configurazione gateway facoltativa](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)
7. Fare clic su **Subnet Configure required settings** (Configura impostazioni necessarie subnet) per aggiungere la **subnet del gateway**. Nonostante sia possibile creare una subnet del gateway con dimensioni di /29 soltanto, è consigliabile crearne una più grande che includa più indirizzi selezionando almeno /28 o /27. In questo modo saranno disponibili indirizzi sufficienti per supportare in futuro le possibili configurazioni aggiuntive desiderate.

   > [!IMPORTANT]
   > Quando si usano le subnet del gateway, evitare di associare un gruppo di sicurezza di rete (NSG) alla subnet del gateway. Se si associa un gruppo di sicurezza di rete a tale subnet, il gateway VPN potrebbe smettere di funzionare come previsto. Per altre informazioni sui gruppi di sicurezza di rete, vedere [Che cos'è un gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md).
   >
   >

    ![Aggiungere GatewaySubnet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
8. Selezionare le **dimensioni** del gateway, che rappresentano lo SKU di gateway che verrà usato per creare il gateway della rete virtuale. Nel portale, lo SKU predefinito è **Basic**. Per altre informazioni sugli SKU di gateway, vedere [Informazioni sulle impostazioni del gateway VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

    ![Dimensioni del gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
9. Selezionare il **tipo di routing** per il gateway. Le configurazioni P2S richiedono il tipo di routing **Dinamico**. Al termine della configurazione del pannello, fare clic su **OK**.

    ![Configurare il tipo di routing](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)
10. Nel pannello **Nuova connessione VPN** fare clic su **OK** nella parte inferiore per iniziare a creare il gateway della rete virtuale. Per completare questa operazione possono essere necessari fino a 45 minuti.

## <a name="generatecerts"></a>Sezione 2: Generare i certificati
I certificati vengono usati da Azure per autenticare i client VPN per VPN da punto a sito. I dati del certificato pubblico (non la chiave privata) vengono esportati come file CER X.509 con codifica in base&64; da un certificato radice generato da una soluzione aziendale per la creazione di certificati o da un certificato radice autofirmato. Si importano quindi i dati del certificato pubblico dal certificato radice ad Azure. È anche necessario generare un certificato client da un certificato radice per i client. In ogni client che si vuole connettere alla rete virtuale con una connessione P2S deve essere installato un certificato client generato dal certificato radice.

### <a name="cer"></a>Parte 1: Ottenere il file CER per il certificato radice
 
Se si tratta di una soluzione aziendale, è possibile usare la catena di certificati esistente. Ottenere il file con estensione cer per il certificato radice che si vuole usare.

Se non si usa una soluzione aziendale per la creazione di certificati, è necessario creare un certificato radice autofirmato. Per creare un certificato autofirmato contenente i campi necessari per l'autenticazione da punto a sito, usare makecert. L'articolo [Usare i certificati autofirmati per le connessioni da punto a sito](vpn-gateway-certificates-point-to-site.md) illustra i passaggi per creare un certificato radice autofirmato. Benché deprecata, makecert in questo momento è la soluzione supportata.

>[!NOTE]
>Anche se è possibile usare PowerShell per creare certificati autofirmati, il certificato generato con PowerShell non contiene i campi necessari per le connessioni da punto a sito.
>
>

#### <a name="to-obtain-the-cer-file-from-a-self-signed-root-certificate"></a>Per ottenere il file con estensione cer da un certificato radice autofirmato

1. Per ottenere un file con estensione cer da un certificato radice autofirmato, aprire **certmgr.msc** e individuare il certificato radice creato. Il certificato si trova in genere in "Certificati-Utente corrente/ Personale/Certificati" e ha il nome che gli è stato assegnato al momento della sua creazione. Fare clic con il pulsante destro del mouse sul certificato radice autofirmato, scegliere **Tutte le attività** e quindi fare clic su **Esporta**. Si avvia la procedura di **Esportazione guidata certificati**.
2. Nella procedura guidata fare clic su **Avanti**, selezionare **No, non esportare la chiave privata** e quindi fare clic su **Avanti**.
3. Nella pagina **Formato file di esportazione** selezionare **Codificato Base&64; X.509 (.CER)**. Quindi fare clic su **Avanti**.
4. In **File da esportare** fare clic su **Sfoglia** e passare alla posizione in cui si vuole esportare il certificato. Per **Nome file**, assegnare un nome al file del certificato. Quindi fare clic su **Avanti**.
5. Fare clic su **Fine** per esportare il certificato.

### <a name="genclientcert"></a>Parte 2: Generare un certificato client
È possibile generare un certificato univoco per ogni client che stabilirà la connessione oppure usare lo stesso certificato in più client. Il vantaggio della generazione di certificati client univoci è dato dalla possibilità di revocare un certificato, se necessario. In caso contrario, se tutti gli utenti usano lo stesso certificato client ed è necessario revocarlo per un client, si dovranno generare e installare nuovi certificati per tutti i client che usano tale certificato per l'autenticazione.

####<a name="enterprise-certificate"></a>Certificato aziendale
- Se si usa una soluzione aziendale per la creazione di certificati, generare un certificato client con il valore di nome comune nel formato "name@yourdomain.com", invece che nel formato "nome dominio\nome utente".
- Assicurarsi che il certificato client che si emette sia basato sul modello di certificato "Utente" con "Autenticazione client" come primo elemento nell'elenco d'uso, invece di Accesso smart card e così via. È possibile controllare il certificato facendo doppio clic sul certificato client e aprendo **Dettagli > Utilizzo chiavi avanzato**.

####<a name="self-signed-certificate"></a>Certificato autofirmato 
Se si usa un certificato autofirmato, per generare un certificato client vedere [Usare i certificati radice autofirmati per le configurazioni da punto a sito](vpn-gateway-certificates-point-to-site.md) .

### <a name="exportclientcert"></a>Parte 3: Esportare il certificato client
Installare un certificato client in ogni computer che si vuole connettere alla rete virtuale. Il certificato client è necessario per l'autenticazione. È possibile automatizzare l'installazione del certificato client oppure installarlo manualmente. La procedura seguente illustra come esportare e installare manualmente il certificato client.

1. Per esportare un certificato client, è possibile usare *certmgr.msc*. Fare clic con il pulsante destro del mouse sul certificato client da esportare, scegliere **Tutte le attività** e quindi fare clic su **Esporta**.
2. Esportare il certificato client con la chiave privata. Si tratta di un file con estensione *prx* . Assicurarsi di eseguire la registrazione o ricordare la password (chiave) impostata per il certificato.

## <a name="upload"></a>Sezione 3: Caricare il file CER del certificato radice
Dopo che il gateway è stato creato, è possibile caricare il file CER di un certificato radice trusted in Azure. È possibile caricare file per un massimo di 20 certificati radice. Non si può caricare la chiave privata per il certificato radice in Azure. Dopo che è stato caricato, il file CER viene usato da Azure per autenticare i client che si connettono alla rete virtuale.

1. Nella sezione **Connessioni VPN** del pannello della rete virtuale fare clic sull'icona **client** per aprire il pannello **Connessione VPN da punto a sito**.

    ![Client](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. Nel pannello **Connessione VPN da punto a sito** fare clic su **Gestisci certificato** per aprire il pannello **Certificati**.<br>

    ![Pannello Certificati](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. Nel pannello **Certificati** fare clic su **Carica** per aprire il pannello **Carica certificato**.<br>

    ![Pannello Carica certificato](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)<br>
4. Fare clic sull'icona della cartella per cercare il file CER. Selezionare il file e quindi fare clic su **OK**. Aggiornare la pagina per visualizzare il certificato caricato nel pannello **Certificati**.

    ![Caricamento del certificato](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)<br>

## <a name="vpnclientconfig"></a>Sezione 4: Generare il pacchetto di configurazione del client VPN
Per connettersi alla rete virtuale, è necessario configurare anche un client VPN. Per la connessione del computer client sono necessari sia un certificato client sia il pacchetto di configurazione del client VPN corretto.

Nel pacchetto client VPN sono contenute le informazioni per configurare il software client VPN incorporato in Windows. Tramite il pacchetto non viene installato del software aggiuntivo. Le impostazioni sono specifiche per la rete virtuale a cui ci si vuole connettere. Per l'elenco dei sistemi operativi client supportati, vedere [Domande frequenti sulla connettività da punto a sito](#faq) alla fine di questo articolo.

### <a name="to-generate-the-vpn-client-configuration-package"></a>Per generare il pacchetto di configurazione del client VPN
1. Nel pannello **Panoramica** della rete virtuale nel portale di Azure fare clic sull'icona relativa ai client in **Connessioni VPN** per aprire il pannello **Connessione VPN da punto a sito**.
2. Nella parte superiore del pannello **Connessione VPN da punto a sito** fare clic sul pacchetto di download corrispondente al sistema operativo client in cui verrà installato.

   * Per client a 64 bit, selezionare **Client VPN (64 bit)**.
   * Per client a 32 bit, selezionare **Client VPN (32 bit)**.

     ![Scaricare il pacchetto di configurazione del client VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)<br>
3. Verrà visualizzato un messaggio che segnala che è in corso la generazione del pacchetto di configurazione del client VPN per la rete virtuale. Dopo alcuni minuti, la generazione del pacchetto verrà completata e nel computer locale verrà visualizzato un messaggio che segnala che il pacchetto è stato scaricato. Salvare il file del pacchetto di configurazione. Questo file verrà installato in ogni computer client che si connetterà alla rete virtuale con P2S.

## <a name="clientconfiguration"></a>Sezione 5: Configurare il computer client
### <a name="part-1-install-the-client-certificate"></a>Parte 1: Installare il certificato client
Ogni computer client deve avere un certificato client per l'autenticazione. Quando si installa il certificato client, sarà necessaria la password creata quando il certificato client è stato esportato.

1. Copiare il file PFX nel computer client.
2. Fare doppio clic sul file PFX per installarlo. Non modificare il percorso di installazione.

### <a name="part-2-install-the-vpn-client-configuration-package"></a>Parte 2: Installare il pacchetto di configurazione del client VPN
È possibile usare lo stesso pacchetto di configurazione del client VPN in ogni computer client, purché la versione corrisponda all'architettura del client.

1. Copiare il file di configurazione in locale nel computer che si vuole connettere alla rete virtuale. 
2. Fare doppio clic sul file con estensione exe per installare il pacchetto nel computer client. Il pacchetto di configurazione non è firmato perché è stato creato dall'utente. Questo vuol dire che potrebbe essere visualizzato un avviso. Se viene visualizzato un popup Windows SmartScreen, fare clic su **Altre informazioni** (a sinistra) e quindi su **Esegui comunque** per installare il pacchetto.
3. Nel computer client passare a **Impostazioni di rete** e fare clic su **VPN**. La connessione verrà visualizzata in un elenco, con il nome della rete virtuale a cui si connette, e avrà un aspetto simile al seguente:

    ![Client VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vpn.png)

## <a name="connect"></a>Parte 6: Connettersi ad Azure
### <a name="connect-to-your-vnet"></a>Connettersi alla rete virtuale
1. Per connettersi alla rete virtuale, nel computer client passare alle connessioni VPN e individuare quella creata, che ha lo stesso nome della rete virtuale locale. Fare clic su **Connetti**. È possibile che venga visualizzato un messaggio popup che fa riferimento all'uso del certificato. In questo caso, fare clic su **Continua** per usare privilegi elevati.
2. Nella pagina **Stato connessione** fare clic su **Connetti** per avviare la connessione. Se viene visualizzato un **Seleziona certificato** dello schermo, verificare che il certificato client visualizzato sia quello che si desidera utilizzare per la connessione. In caso contrario, usare la freccia a discesa per selezionare il certificato corretto e quindi fare clic su **OK**.

    ![Connessione del client VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png)
3. Verrà stabilita la connessione.

    ![Connessione stabilita](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png)

> [!NOTE]
> Se si usa un certificato che è stato rilasciato tramite una soluzione CA globale e si riscontrano problemi durante l'autenticazione, controllare l'ordine di autenticazione del certificato client. È possibile controllare l'ordine dell'elenco di autenticazione facendo doppio clic sul certificato client e andando in **Dettagli > Utilizzo chiavi avanzato**. Assicurarsi che l'elenco mostri "Autenticazione client" come primo elemento. In caso contrario, è necessario emettere un certificato client in base al modello di utente con l'autenticazione client come primo elemento nell'elenco. 
>
>

### <a name="verify-the-vpn-connection"></a>Verificare la connessione VPN
1. Per verificare che la connessione VPN è attiva, aprire un prompt dei comandi con privilegi elevati ed eseguire *ipconfig/all*.
2. Visualizzare i risultati. Si noti che l'indirizzo IP ricevuto è uno degli indirizzi compresi nell'intervallo di indirizzi di connettività da punto a sito specificato al momento della creazione della rete virtuale. I risultati dovrebbero essere simili a quanto segue:

Esempio:

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

## <a name="add"></a>Aggiungere o rimuovere certificati radice attendibili

È possibile aggiungere e rimuovere certificati radice attendibili da Azure. Quando si rimuove un certificato attendibile, i certificati client generati dal certificato radice non potranno più connettersi ad Azure tramite una connessione da punto a sito. Per consentire ai client di connettersi, sarà necessario installare un nuovo certificato client generato da un certificato considerato attendibile in Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Per aggiungere un certificato radice attendibile

In Azure è possibile aggiungere fino a 20 file CER di certificato radice trusted. Per istruzioni, vedere [Sezione 3: Caricare il file CER del certificato radice](#upload).

### <a name="to-remove-a-trusted-root-certificate"></a>Per rimuovere un certificato radice attendibile


1. Nella sezione **Connessioni VPN** del pannello della rete virtuale fare clic sull'icona **client** per aprire il pannello **Connessione VPN da punto a sito**.

    ![Client](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. Nel pannello **Connessione VPN da punto a sito** fare clic su **Gestisci certificato** per aprire il pannello **Certificati**.<br>

    ![Pannello Certificati](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. Nel pannello **Certificati** fare clic sui puntini di sospensione accanto al certificato che si vuole rimuovere e quindi fare clic su **Elimina**.

     ![Eliminare un certificato radice](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)<br>


## <a name="revokeclient"></a>Revocare un certificato client
È possibile revocare i certificati client. L'elenco di revoche di certificati consente di negare in modo selettivo la connettività da punto a sito basata sui singoli certificati client. Questa operazione è diversa rispetto alla rimozione di un certificato radice attendibile. Se si rimuove un file con estensione cer del certificato radice attendibile da Azure, viene revocato l'accesso per tutti i certificati client generati o firmati dal certificato radice revocato. La revoca di un certificato client, anziché del certificato radice, consente di continuare a usare gli altri certificati generati dal certificato radice per l'autenticazione per la connessione da punto a sito.

La regola generale è quella di usare il certificato radice per gestire l'accesso a livello di team o organizzazione, usando i certificati client revocati per il controllo di accesso con granularità fine su singoli utenti.

### <a name="to-revoke-a-client-certificate"></a>Per revocare un certificato client

È possibile revocare un certificato client aggiungendo l'identificazione personale all'elenco di revoche di certificati.

1. Ottenere l'identificazione personale del certificato client. Per altre informazioni, vedere [Procedura: recuperare l'identificazione personale di un certificato](https://msdn.microsoft.com/library/ms734695.aspx).
2. Copiare le informazioni in un editor di testo e rimuovere tutti gli spazi in modo che sia una stringa continua.
3. Passare al pannello **"nome rete virtuale classica" > Connessione VPN da punto a sito > Certificati** e quindi fare clic su **Elenco di revoche** per aprire il pannello Elenco di revoche. 
4. Nel pannello **Elenco di revoche** fare clic su **+Aggiungi certificato** per aprire il pannello **Aggiungi certificato all'elenco di revoche**.
5. Nel pannello **Aggiungi certificato all'elenco di revoche** incollare l'identificazione personale del certificato come una riga di testo continua, senza spazi. Fare clic su **OK** nella parte inferiore del pannello.
6. Dopo aver completato l'aggiornamento, il certificato non può più essere usato per la connessione. Per i client che tentano di connettersi con questo certificato verrà visualizzato un messaggio che informa che il certificato non è più valido.


## <a name="faq"></a>Domande frequenti sulla connettività da punto a sito

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per altre informazioni, vedere [Macchine virtuali](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).

