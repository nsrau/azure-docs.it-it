<properties linkid="manage-services-cross-premises-connectivity" urlDisplayName="Tutorial: Create a Cross-Premises Virtual Network for Site-to-Site Connectivity" pageTitle="Tutorial: Create a Cross-Premises Virtual Network for Site-to-Site Connectivity" metaKeywords="" description="Learn how to create an Azure Virtual Network with cross-premises connectivity in this tutorial." metaCanonical="" services="virtual-network" documentationCenter="" title="Create a Virtual Network for Site-to-Site Cross-Premises Connectivity" authors="cherylmc" solutions="" manager="adinah" editor="" />

<tags ms.service="virtual-network" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/23/2014" ms.author="cherylmc"></tags>

# Esercitazione: Creazione di una rete virtuale cross-premise per la connettività da sito a sito

Questa esercitazione descrive le procedure da seguire per creare una rete virtuale cross-premise di esempio con una connessione da sito a sito. Se si desidera creare una rete VPN da punto a sito utilizzando certificati e un client VPN, vedere [Configurare una VPN da punto a sito utilizzando la procedura guidata del portale di gestione][Configurare una VPN da punto a sito utilizzando la procedura guidata del portale di gestione].

In questa esercitazione si presuppone che l'utente non abbia mai utilizzato Azure. L'utente avrà quindi la possibilità di acquisire familiarità con le procedure necessarie per creare una rete virtuale cross-premise. Per scenari di progettazione e informazioni avanzate su Rete virtuale, vedere le [informazioni generali su Rete virtuale di Azure][informazioni generali su Rete virtuale di Azure].

Dopo aver completato questa esercitazione, si avrà una rete virtuale cross-premise di esempio in cui possono essere distribuiti i servizi e le macchine virtuali di Azure.

Si noti che le impostazioni di configurazione di esempio usate in questa esercitazione non sono personalizzate per la rete di un'organizzazione specifica. Se configurate con le impostazioni di esempio descritte in questo argomento, la rete virtuale e la connessione da sito a sito non funzioneranno. Per configurare una rete virtuale cross-premise effettivamente funzionante, è necessario collaborare con il reparto IT e l'amministratore di rete della propria organizzazione per ottenere le impostazioni corrette. Per altre informazioni, vedere la sezione **Prerequisiti** di questo argomento.

Per informazioni su come aggiungere una macchina virtuale ed estendere Active Directory locale alla rete virtuale di Azure, vedere gli argomenti seguenti:

-   [Come creare una macchina virtuale personalizzata][Come creare una macchina virtuale personalizzata]

-   [Installazione di un controller di dominio Active Directory di replica in una rete virtuale di Azure.][Installazione di un controller di dominio Active Directory di replica in una rete virtuale di Azure.]

Per le linee guida sulla distribuzione di Servizi di dominio Active Directory nelle macchine virtuali di Azure, vedere [Linee guida per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure][Linee guida per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure].

Per ulteriori procedure e impostazioni di configurazione di Rete virtuale, vedere [Attività di configurazione di Rete virtuale di Azure][Attività di configurazione di Rete virtuale di Azure].

## Obiettivi

In questa esercitazione si apprenderà come:

-   Configurare una rete virtuale di Azure cross-premise di esempio a cui è possibile aggiungere servizi di Azure.

-   Configurare la rete virtuale per la comunicazione con la rete aziendale.

## Prerequisiti

-   Account Windows con almeno una sottoscrizione Azure valida attiva. Se non si dispone di una sottoscrizione Azure, è possibile effettuare l'iscrizione per ottenere una [versione di valutazione gratuita][versione di valutazione gratuita]. Se si dispone di una sottoscrizione MSDN, vedere la pagina [Offerte speciali di Microsoft Azure: vantaggi per i membri di MSDN, MPN e Bizspark][Offerte speciali di Microsoft Azure: vantaggi per i membri di MSDN, MPN e Bizspark].

Se si intende usare questa esercitazione per configurare una rete virtuale cross-premise funzionante e personalizzata per la propria organizzazione, sono previsti i seguenti prerequisiti:

-   Spazi di indirizzi IPv4 privati (in notazione CIDR) per la rete virtuale e le relative subnet.

-   Nome e indirizzo IP di un server DNS locale.

-   Dispositivo VPN con un indirizzo IPv4 pubblico. L'indirizzo IP è necessario per completare la procedura guidata. Il dispositivo VPN non può essere protetto tramite NAT (Network Address Translator) e deve soddisfare gli standard del dispositivo minimi. Per ulteriori informazioni, vedere [Informazioni sui dispositivi VPN per Rete virtuale][Informazioni sui dispositivi VPN per Rete virtuale].

    Nota: è possibile usare il servizio Routing e Accesso remoto in Windows Server come parte della soluzione VPN. Tuttavia questa esercitazione non include la procedura per configurare questo servizio.
    Per informazioni sulla configurazione del servizio Routing e Accesso remoto, vedere [Modelli del servizio Routing e Accesso remoto][Modelli del servizio Routing e Accesso remoto].

-   Esperienza nella configurazione di un router per una connessione in modalità tunnel IPsec o assistenza da parte di una persona esperta.

-   Set di spazi di indirizzi (in notazione CIDR) che forniscono la sintesi delle posizioni raggiungibili nella rete locale.

## Procedure generali

1.  [Creare una rete virtuale][Creare una rete virtuale]

2.  [Avviare il gateway e raccogliere informazioni per l'amministratore di rete][Avviare il gateway e raccogliere informazioni per l'amministratore di rete]

3.  [Configurare il dispositivo VPN][Configurare il dispositivo VPN]

## <a name="CreateVN">Creare una rete virtuale</a>

Per creare una rete virtuale di esempio per la connessione a una rete aziendale:

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].

2.  Nell'angolo inferiore sinistro della schermata fare clic su **New**. Nel pannello di navigazione fare clic su **Networks** e quindi su **Virtual Network**. Fare clic su **Custom Create** per avviare la configurazione guidata.

    ![][]

3.  Nella pagina **Virtual Network Details** immettere le informazioni seguenti e quindi fare clic sulla freccia Next in basso a destra. Per ulteriori informazioni sulle impostazioni per la pagina relativa ai dettagli, vedere la sezione **Dettagli rete virtuale** in [Informazioni sulla configurazione di una rete virtuale nel portale di gestione][Informazioni sulla configurazione di una rete virtuale nel portale di gestione].

    -   **NAME:** assegnare un nome alla rete virtuale. Per l'esempio in questa esercitazione digitare **YourVirtualNetwork**.

    -   **REGION:** selezionare l'area desiderata nell'elenco a discesa. La rete virtuale verrà creata in un data center di Azure situato nell'area specificata.

4.  Nella pagina **DNS Servers and VPN Connectivity** immettere le informazioni seguenti e quindi fare clic sulla freccia avanti in basso a destra.

    <div class="dev-callout">

    **Nota**
    È possibile selezionare contemporaneamente in questa pagina entrambe le configurazioni **Point-To-Site** e **Site-To-Site**. Ai fini di questa esercitazione, si selezionerà solo la configurazione **Site-To-Site**. Per altre informazioni sulle impostazioni disponibili in questa pagina, vedere **Server DNS e connettività VPN** in [Informazioni sulla configurazione di una rete virtuale nel portale di gestione][Informazioni sulla configurazione di una rete virtuale nel portale di gestione].

    </div>

    -   **DNS SERVERS:** immettere il nome del server DNS e l'indirizzo IP da utilizzare per la risoluzione dei nomi. In genere, si tratta di un server DNS utilizzato per la risoluzione dei nomi locale. Questa impostazione non comporta la creazione di un server DNS. Per l'esempio in questa esercitazione digitare **YourDNS** come nome **10.1.0.4** come indirizzo IP.
    -   **Configure Point-To-Site VPN:** lasciare questo campo vuoto.
    -   **Configure Site-To-Site VPN:** selezionare la casella di controllo.
    -   **LOCAL NETWORK:** selezionare **Specify a New Local Network** dall'elenco a discesa.

    ![][1]

5.  Nella pagina **Site-To-Site Connectivity** immettere le informazioni seguenti e quindi fare clic sul segno di spunta in basso a destra. Per ulteriori informazioni sulle impostazioni disponibili in questa pagina, vedere la sezione **Connettività Site-to-Site** in [Informazioni sulla configurazione di una rete virtuale nel portale di gestione][Informazioni sulla configurazione di una rete virtuale nel portale di gestione].

    -   **NAME:** per l'esempio in questa esercitazione digitare **YourCorpHQ**.

    -   **VPN DEVICE IP ADDRESS:** immettere l'indirizzo IP pubblico del dispositivo VPN. Se non si dispone di questa informazione, sarà necessario ottenerla prima di passare alle fasi successive della procedura guidata. Si noti che il dispositivo VPN non può essere protetto tramite NAT. Per ulteriori informazioni sui dispositivi VPN, vedere [Informazioni sui dispositivi VPN per Rete virtuale][2].

    -   **ADDRESS SPACE:** per l'esempio in questa esercitazione digitare **10.1.0.0/16**.
    -   **Add address space:** per questa esercitazione non è richiesto uno spazio di indirizzi aggiuntivo.

    ![][3]

6.  Nella pagina **Virtual Network Address Spaces** immettere le informazioni seguenti e quindi fare clic sul segno di spunta in basso a destra per configurare la rete.

    Lo spazio di indirizzi deve essere un intervallo di indirizzi privato, specificato nella notazione CIDR dagli spazi di indirizzi 10.0.0.0/8, 172.16.0.0/12 o 192.168.0.0/16 (come indicato dalla specifica RFC 1918). Per ulteriori informazioni sulle impostazioni disponibili in questa pagina, vedere la sezione **Spazi di indirizzi della rete virtuale** in [Informazioni sulla configurazione di una rete virtuale nel portale di gestione][Informazioni sulla configurazione di una rete virtuale nel portale di gestione].

    -   **Address Space:** per l'esempio in questa esercitazione fare clic su **CIDR** nell'angolo in alto a destra e immettere le informazioni seguenti:

        -   **Starting IP:** 10.4.0.0
        -   **CIDR:** /16
    -   **Add subnet:** per l'esempio in questa esercitazione immettere le informazioni seguenti:

        -   Rinominare **Subnet-1** in **FrontEndSubnet** con IP iniziale **10.4.2.0/24**.
        -   Aggiungere una subnet denominata **BackEndSubnet** con IP iniziale **10.4.3.0/24**.
        -   Aggiungere una subnet denominata **ADDNSSubnet** con IP iniziale **10.4.4.0/24**.
        -   Aggiungere una subnet di gateway con IP iniziale **10.4.1.0/24**.
    -   Per l'esempio in questa esercitazione verificare che siano disponibili tre subnet e che sia stato creato il gateway della subnet, quindi fare clic sul segno di spunta in basso a destra per creare la rete virtuale.

    ![][4]

7.  Dopo avere fatto clic sul segno di spunta, verrà avviato il processo di creazione della rete virtuale. Al termine della creazione della rete virtuale, in **Status** verrà visualizzata la dicitura **Created** nella pagina relativa alle reti del portale di gestione.

    ![][5]

## <a name="StartGateway">Avviare il gateway</a>

Dopo aver creato la rete virtuale di Azure, attenersi alla procedura seguente per configurare il gateway di rete virtuale e creare la VPN da sito a sito. Questa procedura richiede un dispositivo VPN che soddisfi i requisiti minimi. Per ulteriori informazioni sui dispositivi VPN e sulla configurazione dei dispositivi, vedere [Informazioni sui dispositivi VPN per Rete virtuale][Informazioni sui dispositivi VPN per Rete virtuale].

**Per avviare il gateway:**

1.  Una volta creata la rete virtuale, nella pagina **networks** verrà visualizzato lo stato **Created** per la rete virtuale.

    Nella colonna **NAME** fare clic su **YourVirtualNetwork** (per l'esempio creato in questa esercitazione) per aprire il dashboard.

    ![][6]

2.  Fare clic su **DASHBOARD** nella parte superiore della pagina. In Dashboard, nella parte inferiore della pagina fare clic su **CREATE GATEWAY**. Selezionare **Dynamic Routing** o **Static Routing** per il tipo di gateway da creare.

    Se si desidera usare questa rete virtuale per le connessioni da punto a sito oltre che per quelle da sito a sito, è necessario selezionare **Dynamic Routing** come tipo di gateway. Prima di creare il gateway, verificare che il dispositivo VPN supporti il tipo di gateway che si intende creare. Per ulteriori informazioni, vedere [Informazioni sui dispositivi VPN per Rete virtuale][Informazioni sui dispositivi VPN per Rete virtuale]. Quando viene richiesto di confermare che si desidera procedere con la creazione del gateway, fare clic su **YES**.

    ![][7]

3.  All'avvio della creazione del gateway, verrà visualizzato un messaggio che indica che la procedura è stata avviata.

    La creazione del gateway potrebbe richiedere fino a 15 minuti.

4.  Al termine della creazione del gateway, sarà necessario raccogliere le informazioni seguenti per configurare il dispositivo VPN.

    -   Indirizzo IP del gateway
    -   Chiave condivisa
    -   Modello di script di configurazione del dispositivo VPN

    Attenersi alla procedura seguente:

5.  Per individuare l'indirizzo IP del gateway: l'indirizzo IP del gateway è disponibile nella pagina **DASHBOARD** della rete virtuale. Di seguito è fornito un esempio:

    ![][8]

6.  Per acquisire la chiave condivisa: la chiave condivisa è disponibile nella pagina **DASHBOARD** della rete virtuale. Fare clic su **Manage Key** nella parte inferiore della schermata e quindi copiare la chiave visualizzata nella finestra di dialogo. Questa chiave sarà necessaria per configurare il tunnel IPsec nel dispositivo VPN aziendale.

    ![][9]

7.  Per scaricare il modello di script di configurazione del dispositivo VPN: Nel dashboard fare clic su **Download VPN Device Script**.

8.  Nella finestra di dialogo **Download a VPN Device Configuration Script** selezionare il fornitore, la piattaforma e il sistema operativo per il dispositivo VPN aziendale. Fare clic sul pulsante con il segno di spunta e salvare il file.

    ![][10]

Se il dispositivo VPN non è presente nell'elenco a discesa, vedere [Informazioni sui dispositivi VPN per Rete virtuale][Informazioni sui dispositivi VPN per Rete virtuale] in MSDN Library per ulteriori modelli di script.

## <a name="ConfigVPN">Configurare il dispositivo VPN (amministratore di rete)</a>

Poiché ogni dispositivo VPN è diverso, quella fornita di seguito è solo una procedura di carattere generale. Questa procedura deve essere eseguita dall'amministratore di rete.

Lo script di configurazione del dispositivo VPN è disponibile nel portale di gestione o nell'articolo [Informazioni sui dispositivi VPN per Rete virtuale][11] in cui vengono inoltre illustrati i tipi di routing e i dispositivi compatibili con la configurazione di routing che si sceglie di utilizzare.

Per ulteriori informazioni sulla configurazione di un gateway di rete virtuale, vedere [Configurare un gateway di rete virtuale nel portale di gestione][Configurare un gateway di rete virtuale nel portale di gestione] e consultare la documentazione fornita con il dispositivo VPN.

Per questa procedura, si presuppone quanto segue:

-   La persona che configura il dispositivo VPN sia esperta nella configurazione del dispositivo selezionato. A causa dell'elevato numero di dispositivi compatibili con la rete virtuale e delle configurazioni specifiche per ogni famiglia di dispositivi, questa procedura non illustra la configurazione del dispositivo a livello dettagliato. È pertanto importante che la persona che configura il dispositivo abbia familiarità con il dispositivo stesso e le relative impostazioni di configurazione.

-   Il dispositivo selezionato per l'utilizzo sia compatibile con la rete virtuale. Fare clic [qui][Informazioni sui dispositivi VPN per Rete virtuale] per verificare la compatibilità del dispositivo.

**Per configurare il dispositivo VPN:**

1.  Modificare lo script di configurazione del dispositivo VPN. Verranno configurate le impostazioni seguenti:

    a. Criteri di sicurezza

    b. Tunnel in ingresso

    c. Tunnel in uscita

2.  Eseguire lo script di configurazione VPN modificato per configurare il dispositivo VPN.

3.  Per verificare la connessione, eseguire uno dei comandi seguenti:

    | -                        | Cisco ASA                                                   | Cisco ISR/ASR                                               | Juniper SSG/ISG                                      | Juniper SRX/J                                                                  |
    |--------------------------|-------------------------------------------------------------|-------------------------------------------------------------|------------------------------------------------------|--------------------------------------------------------------------------------|
    | **Check main mode SAs**  | <font face="courier" size="-1">show crypto isakmp sa</font> | <font face="courier" size="-1">show crypto isakmp sa</font> | <font face="courier" size="-1">get ike cookie</font> | <font face="courier" size="-1">show security ike security-association</font>   |
    | **Check quick mode SAs** | <font face="courier" size="-1">show crypto ipsec sa</font>  | <font face="courier" size="-1">show crypto ipsec sa</font>  | <font face="courier" size="-1">get sa</font>         | <font face="courier" size="-1">show security ipsec security-association</font> |

## Passaggi successivi

Per estendere Active Directory locale alla rete virtuale appena creata, procedere con le esercitazioni riportate di seguito:

-   [Come creare una macchina virtuale personalizzata][Come creare una macchina virtuale personalizzata]

-   [Installazione di un controller di dominio Active Directory di replica in una rete virtuale di Azure.][Installazione di un controller di dominio Active Directory di replica in una rete virtuale di Azure.]

Per esportare le impostazioni della rete virtuale in un file di configurazione di rete per eseguire il backup della configurazione o per utilizzarlo come modello, vedere [Esportare le impostazioni della rete virtuale in un file di configurazione di rete][Esportare le impostazioni della rete virtuale in un file di configurazione di rete].

## Vedere anche

-   [Rete virtuale di Azure][informazioni generali su Rete virtuale di Azure]

-   [Domande frequenti su Rete virtuale][Domande frequenti su Rete virtuale]

-   [Configurare una rete virtuale utilizzando file di configurazione di rete][Configurare una rete virtuale utilizzando file di configurazione di rete]

-   [Aggiunta di una macchina virtuale a Rete virtuale][Aggiunta di una macchina virtuale a Rete virtuale]

-   [Informazioni sui dispositivi VPN per Rete virtuale][2]

-   [Risoluzione dei nomi][Risoluzione dei nomi]

  [Configurare una VPN da punto a sito utilizzando la procedura guidata del portale di gestione]: http://go.microsoft.com/fwlink/?LinkId=296653
  [informazioni generali su Rete virtuale di Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/jj156007.aspx
  [Come creare una macchina virtuale personalizzata]: http://go.microsoft.com/fwlink/?LinkID=294356
  [Installazione di un controller di dominio Active Directory di replica in una rete virtuale di Azure.]: http://go.microsoft.com/fwlink/?LinkId=299877
  [Linee guida per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/jj156090.aspx
  [Attività di configurazione di Rete virtuale di Azure]: http://go.microsoft.com/fwlink/?LinkId=296652
  [versione di valutazione gratuita]: http://www.windowsazure.com/pricing/free-trial/
  [Offerte speciali di Microsoft Azure: vantaggi per i membri di MSDN, MPN e Bizspark]: http://azure.microsoft.com/it-it/pricing/member-offers/msdn-benefits-details/
  [Informazioni sui dispositivi VPN per Rete virtuale]: http://go.microsoft.com/fwlink/?LinkID=248098
  [Modelli del servizio Routing e Accesso remoto]: http://msdn.microsoft.com/library/windowsazure/dn133801.aspx
  [Creare una rete virtuale]: #CreateVN
  [Avviare il gateway e raccogliere informazioni per l'amministratore di rete]: #StartGateway
  [Configurare il dispositivo VPN]: #ConfigVPN
  [portale di gestione di Azure]: http://manage.windowsazure.com/
  []: ./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_01_OpenVirtualNetworkWizard.png
  [Informazioni sulla configurazione di una rete virtuale nel portale di gestione]: http://go.microsoft.com/fwlink/?LinkID=248092
  [1]: ./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_03_DNSServersandVPNConnectivity.png
  [2]: http://msdn.microsoft.com/it-it/library/windowsazure/jj156075.aspx
  [3]: ./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_04_SitetoSite.png
  [4]: ./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_05_VirtualNetworkAddressSpaces.png
  [5]: ./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_06_VirtualNetworkCreatedStatus.png
  [6]: ./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_07_ClickYourVirtualNetwork.png
  [7]: ./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_08_CreateGateway.png
  [8]: ./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_09_GatewayIP.png
  [9]: ./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_10_ManageSharedKey.png
  [10]: ./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_11_DownloadVPNDeviceScript.png
  [11]: http://go.microsoft.com/fwlink/?LinkId=248098
  [Configurare un gateway di rete virtuale nel portale di gestione]: http://go.microsoft.com/fwlink/?LinkId=299878
  [Esportare le impostazioni della rete virtuale in un file di configurazione di rete]: http://go.microsoft.com/fwlink/?LinkID=299880
  [Domande frequenti su Rete virtuale]: http://msdn.microsoft.com/library/windowsazure/dn133803.aspx
  [Configurare una rete virtuale utilizzando file di configurazione di rete]: http://msdn.microsoft.com/it-it/library/windowsazure/jj156097.aspx
  [Aggiunta di una macchina virtuale a Rete virtuale]: http://www.windowsazure.com/it-it/manage/services/networking/add-a-vm-to-a-virtual-network/
  [Risoluzione dei nomi]: http://go.microsoft.com/fwlink/?LinkId=248097
