<properties  linkid="manage-services-cross-premises-connectivity" urlDisplayName="Cross-premises Connectivity" pageTitle="Create a cross-premises virtual network - Azure" metaKeywords="" description="Learn how to create an Azure Virtual Network with cross-premises connectivity." metaCanonical="" services="virtual-network" documentationCenter="" title="Create a Virtual Network for Site-to-Site Cross-Premises Connectivity" authors="" solutions="" manager="" editor="" />

<h1>Creazione di una rete virtuale per la connettività cross-premise da sito a sito</h1>   

In questa esercitazione verranno descritte le procedure per creare una rete virtuale cross-premise. Verrà inoltre creata una connessione di tipo da sito a sito. Se si desidera creare una rete VPN da punto a sito utilizzando certificati e un client VPN, vedere [Configurare una VPN da punto a sito utilizzando la procedura guidata del portale di gestione][1].

In questa esercitazione si presuppone che l'utente non abbia mai utilizzato Azure. L'utente avrà quindi la possibilità di acquisire familiarità con le procedure necessarie per creare una rete virtuale da sito a sito. Per scenari di progettazione e informazioni avanzate su Rete virtuale, vedere le [informazioni generali su Rete virtuale di Azure][2].

Al termine di questa esercitazione, sarà disponibile una rete virtuale in cui distribuire servizi e macchine virtuali di Azure in grado di comunicare direttamente con la rete della propria società.

Per informazioni su come aggiungere una macchina virtuale ed estendere Active Directory locale alla rete virtuale di Azure, vedere gli argomenti seguenti:

* [Come creare una macchina virtuale personalizzata][3]

* [Installazione di un controller di dominio Active Directory di replica in una rete virtuale di Azure.][4]

Per le linee guida sulla distribuzione di Servizi di dominio Active Directory nelle macchine virtuali di Azure, vedere [Linee guida per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure][5].

Per ulteriori procedure e impostazioni di configurazione di Rete virtuale, vedere [Attività di configurazione di Rete virtuale di Azure][6].

## Obiettivi

In questa esercitazione si apprenderà come:

* Configurare una rete virtuale di Azure di base a cui è possibile aggiungere servizi di Azure.

* Configurare la rete virtuale per la comunicazione con la rete aziendale.

## Prerequisiti

* Account Windows Live con almeno una sottoscrizione valida attiva.

* Spazio di indirizzi (in notazione CIDR) da utilizzare le la rete virtuale e le subnet.

* Nome e indirizzo IP del server DNS in uso (se si desidera utilizzare il server DNS locale per la risoluzione dei nomi).

* Dispositivo VPN con un indirizzo IPv4 pubblico. L'indirizzo IP è necessario per completare la procedura guidata. Il dispositivo VPN non può essere protetto tramite NAT e deve soddisfare gli standard del dispositivo minimi. Per ulteriori informazioni, vedere [Informazioni sui dispositivi VPN per Rete virtuale][7].
  
	Nota: è possibile utilizzare il servizio Routing e Accesso remoto come parte della soluzione VPN. Tuttavia, in questa esercitazione non vengono descritte le procedure di configurazione del servizio Routing e Accesso remoto.
  
	Per informazioni sulla configurazione del servizio Routing e Accesso remoto, vedere [Modelli del servizio Routing e Accesso remoto][8].

* Esperienza nella configurazione di un router o assistenza da parte di una persona esperta.

* Lo spazio di indirizzi per la rete locale.

## Procedure generali

1.  [Creare una rete virtuale](#CreateVN)

2.  [Avviare il gateway e raccogliere informazioni per l'amministratore di rete](#StartGateway)

3.  [Configurare il dispositivo VPN](#ConfigVPN)

## <a name="CreateVN">Creare una rete virtuale</a>

**Per creare una rete virtuale per la connessione alla rete aziendale:**

1.  Accedere al [portale di gestione di Azure][9].

2.  Nell'angolo inferiore sinistro della schermata fare clic su **New**. Nel pannello di navigazione fare clic su **Networks** e quindi su **Virtual Network**. Fare clic su **Custom Create** per avviare la configurazione guidata.
    
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_01_OpenVirtualNetworkWizard.png)

3.  Nella pagina **Virtual Network Details** immettere le informazioni seguenti e quindi fare clic sulla freccia Next in basso a destra. Per ulteriori informazioni sulle impostazioni per la pagina relativa ai dettagli, vedere la sezione **Dettagli rete virtuale** in [Informazioni sulla configurazione di una rete virtuale nel portale di gestione][10].

* **NAME:** assegnare un nome alla rete virtuale. Digitare *YourVirtualNetwork*.

* **AFFINITY GROUP:** nell'elenco a discesa selezionare **Create a new affinity group**. I gruppi di affinità consentono di raggruppare fisicamente servizi di Azure nello stesso data center per aumentare le prestazioni. Un gruppo di affinità può essere assegnato a una sola rete virtuale.

* **REGION:** selezionare l'area desiderata nell'elenco a discesa. La rete virtuale verrà creata in un data center situato nell'area specificata.

* **AFFINITY GROUP NAME:** assegnare un nome al nuovo gruppo di affinità. Digitare *YourAffinityGroup*.
  
	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_02_VirtualNetworkDetails.png)

1.  Nella pagina **DNS Servers and VPN Connectivity** immettere le informazioni seguenti e quindi fare clic sulla freccia avanti in basso a destra.
    
    <div  class="dev-callout" markdown="1">
	<b>Nota</b>
	<p>È possibile selezionare contemporaneamente in questa pagina entrambe
	le configurazioni **Point-To-Site** e **Site-To-Site**. Ai fini di questa esercitazione, si selezionerà solo la configurazione 	**Site-To-Site**. Per ulteriori informazioni sulle impostazioni disponibili in questa pagina, vedere la sezione **Server DNS e connettività VPN** in <a href="http://go.microsoft.com/fwlink/?LinkID=248092">Informazioni sulla configurazione di unarete virtuale nel portale di gestione</a>.</p>
    </div>


* **DNS SERVERS:** immettere il nome del server DNS e l'indirizzo IP da utilizzare per la risoluzione dei nomi. In genere, si tratta di un server DNS utilizzato per la risoluzione dei nomi locale. Questa impostazione non comporta la creazione di un server DNS. Digitare *YourDNS* per il nome e *10.1.0.4* per l'indirizzo IP.
* **Configure Point-To-Site VPN:** lasciare questo campo vuoto.
* **Configure Site-To-Site VPN:** selezionare la casella di controllo.
* **LOCAL NETWORK:** Selezionare **Specify a New Local Network** nell'elenco a discesa.
  
	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_03_DNSServersandVPNConnectivity.png)

5.  Nella pagina **Site-To-Site Connectivity** immettere le informazioni seguenti e quindi fare clic sul segno di spunta in basso a destra. Per ulteriori informazioni sulle impostazioni disponibili in questa pagina, vedere la sezione **Connettività Site-to-Site** in [Informazioni sulla configurazione di una rete virtuale nel portale di gestione][10].

* **NAME:** digitare *YourCorpHQ*.

* **VPN DEVICE IP ADDRESS:** immettere l'indirizzo IP pubblico del dispositivo VPN. Se non si dispone di questa informazione, sarà necessario ottenerla prima di passare alle fasi successive della procedura guidata. Si noti che il dispositivo VPN non può essere protetto tramite NAT. Per ulteriori informazioni sui dispositivi VPN, vedere [Informazioni sui dispositivi VPN per Rete virtuale][11].

* **ADDRESS SPACE:** digitare *10.1.0.0/16*.
* **Add address space:** per questa esercitazione non è richiesto uno spazio di indirizzi aggiuntivo.
  
	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_04_SitetoSite.png)

1.  Nella pagina **Virtual Network Address Spaces** immettere le informazioni seguenti e quindi fare clic sul segno di spunta in basso a destra per configurare la rete.
    
    Lo spazio di indirizzi deve essere un intervallo di indirizzi privato, specificato nella notazione CIDR 10.0.0.0/8, 172.16.0.0/12 o 192.168.0.0/16 (come indicato dalla specifica RFC 1918). Per ulteriori informazioni sulle impostazioni disponibili in questa pagina, vedere la sezione **Spazi di indirizzi della rete virtuale** in [Informazioni sulla configurazione di una rete virtuale nel portale di gestione][10].

* **Address Space:** fare clic su **CIDR** nell'angolo superiore destro e immettere le informazioni seguenti:
  * **Starting IP:** 10.4.0.0
  * **CIDR:** /16
* **Add subnet:** immettere le informazioni seguenti:
  * **Rinominare Subnet-1** in *FrontEndSubnet* con IP iniziale *10.4.2.0/24* e quindi fare clic su **add subnet**.
  * **aggiungere una subnet** denominata *BackEndSubnet* con IP iniziale *10.4.3.0/24*.
  * **aggiungere una subnet** denominata *ADDNSSubnet* con IP iniziale *10.4.4.0/24*.
  * **Aggiungere la subnet del gateway** con IP iniziale *10.4.1.0/24*.
  * **Verificare** che siano disponibili tre subnet e che sia stato creato il gateway della subnet, quindi fare clic sul segno di spunta in basso a destra per creare la rete virtuale.

  ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_05_VirtualNetworkAddressSpaces.png)

7.  Dopo avere fatto clic sul segno di spunta, verrà avviato il processo di creazione della rete virtuale. Al termine della creazione della rete virtuale, in Status verrà visualizzata la dicitura Created nella pagina relativa alle reti del portale di gestione.
    
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_06_VirtualNetworkCreatedStatus.png)

## <a name="StartGateway">Avviare il gateway</a>

Dopo aver creato la rete virtuale di Azure, attenersi alla procedura seguente per configurare il gateway di rete virtuale e creare la VPN da sito a sito. Questa procedura richiede un dispositivo VPN che soddisfi i requisiti minimi. Per ulteriori informazioni sui dispositivi VPN e sulla configurazione dei dispositivi, vedere [Informazioni sui dispositivi VPN per Rete virtuale][7].

**Per avviare il gateway:**

1.  Una volta creata la rete virtuale, nella pagina **networks** verrà visualizzato lo stato **Created** per la rete virtuale.
    
    Nella colonna **NAME** fare clic su **YourVirtualNetwork** per aprire il dashboard.
    
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_07_ClickYourVirtualNetwork.png)

2.  Fare clic su **DASHBOARD** nella parte superiore della pagina. In Dashboard, nella parte inferiore della pagina fare clic su **CREATE GATEWAY**. Selezionare **Dynamic Routing** o **Static Routing** per il tipo di gateway da creare.
    
    Si noti che se si desidera utilizzare la rete virtuale per connessioni punto a sito oltre a quelle sito a sito, è necessario selezionare Dynamic Routing come tipo di gateway. Prima di creare il
    gateway, verificare che il dispositivo VPN supporti il tipo di gateway che si intende creare. Per ulteriori informazioni, vedere [Informazioni sui dispositivi VPN per Rete virtuale][7]. Quando viene richiesto di confermare che si desidera procedere con la creazione del gateway, fare clic su **YES**.
    
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_08_CreateGateway.png)

3.  All'avvio della creazione del gateway, verrà visualizzato un messaggio che indica che la procedura è stata avviata.
    
    La creazione del gateway potrebbe richiedere fino a 15 minuti.

4.  Al termine della creazione del gateway, sarà necessario raccogliere le informazioni seguenti per configurare il dispositivo VPN.

* Indirizzo IP del gateway
* Chiave condivisa
* Modello di script di configurazione del dispositivo VPN
  
	Attenersi alla procedura seguente:

5.  Per individuare l'indirizzo IP del gateway: l'indirizzo IP del gateway è disponibile nella pagina **DASHBOARD** della rete virtuale.
    
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_09_GatewayIP.png)

6.  Per ottenere la chiave condivisa: la chiave condivisa è disponibile nella pagina **DASHBOARD** della rete virtuale. Fare clic su Manage Key nella parte inferiore della schermata e quindi copiare la chiave visualizzata nella finestra di dialogo.
    
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_10_ManageSharedKey.png)

7.  Scaricare il modello di script di configurazione del dispositivo VPN. Nel dashboard fare clic su **Download VPN Device Script**.

8.  Nella finestra di dialogo **Download a VPN Device Configuration Script** selezionare il fornitore, la piattaforma e il sistema operativo per il dispositivo VPN aziendale. Fare clic sul pulsante con il segno di spunta e salvare il file.
    
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_11_DownloadVPNDeviceScript.png)

Se il dispositivo VPN non è presente nell'elenco a discesa, vedere [Informazioni sui dispositivi VPN per Rete virtuale][7] in MSDN Library per ulteriori modelli di script.

## <a name="ConfigVPN">Configurare il dispositivo VPN (amministratore di rete)</a>

Poiché ogni dispositivo VPN è diverso, quella fornita di seguito è solo una procedura di carattere generale. Questa procedura deve essere eseguita dall'amministratore di rete.

Lo script di configurazione del dispositivo VPN è disponibile nel portale di gestione o nell'articolo [Informazioni sui dispositivi VPN per Rete virtuale][12] in cui vengono inoltre illustrati i tipi di routing e i dispositivi compatibili con la configurazione di routing che si sceglie di utilizzare.

Per ulteriori informazioni sulla configurazione di un gateway di rete virtuale, vedere [Configurare un gateway di rete virtuale nel portale di gestione][13] e consultare la documentazione fornita con il dispositivo VPN.

Per questa procedura, si presuppone quanto segue:

* La persona che configura il dispositivo VPN sia esperta nella configurazione del dispositivo selezionato. A causa dell'elevato numero di dispositivi compatibili con la rete virtuale e delle configurazioni specifiche per ogni famiglia di dispositivi, questa procedura non illustra la configurazione del dispositivo a livello dettagliato. È pertanto importante che la persona che configura il dispositivo abbia familiarità con il spositivo stesso e le relative impostazioni di configurazione.

* Il dispositivo selezionato per l'utilizzo sia compatibile con la rete virtuale. Fare clic [qui][7] per verificare la compatibilità del dispositivo.

**Per configurare il dispositivo VPN:**

1.  Modificare lo script di configurazione del dispositivo VPN. Verranno configurate le impostazioni seguenti:
    
    a. Criteri di sicurezza
    
    b. Tunnel in ingresso
    
    c. Tunnel in uscita

2.  Eseguire lo script di configurazione VPN modificato per configurare il dispositivo VPN.

3.  Per verificare la connessione, eseguire uno dei comandi seguenti:
    
    <table  border="1">
    <tr>
    <th>-</th>
    <th>Cisco ASA</th>
    <th>Cisco ISR/ASR</th>
    <th>Juniper SSG/ISG</th>
    <th>Juniper SRX/J</th>
    </tr>
    
    <tr>
    <td><b>Check main mode SAs</b></td>
    <td><font  face="courier" size="-1">show crypto isakmp sa</font></td>
    <td><font  face="courier" size="-1">show crypto isakmp sa</font></td>
    <td><font  face="courier" size="-1">get ike cookie</font></td>
    <td><font  face="courier" size="-1">show security ike security-association</font></td>
    </tr>
    
    	
    <tr>
    <td><b>Check quick mode SAs</b></td>
    <td><font  face="courier" size="-1">show crypto ipsec sa</font></td>
    <td><font  face="courier" size="-1">show crypto ipsec sa</font></td>
    <td><font  face="courier" size="-1">get sa</font></td>
    <td><font  face="courier" size="-1">show security ipsec security-association</font></td>
    </tr>
    </table>

## Passaggi successivi

Per estendere Active Directory locale alla rete virtuale appena creata, procedere con le esercitazioni riportate di seguito:

* [Come creare una macchina virtuale personalizzata][3]

* [Installazione di un controller di dominio Active Directory di replica in una rete virtuale di Azure.][4]

Per esportare le impostazioni della rete virtuale in un file di configurazione di rete per eseguire il backup della configurazione o per utilizzarlo come modello, vedere [Esportare le impostazioni della rete virtuale in un file di configurazione di rete][14].

## Vedere anche

* [Rete virtuale di Azure][2]

* [Domande frequenti su Rete virtuale][15]

* [Configurare una rete virtuale utilizzando file di configurazione di rete][16]

* [Aggiunta di una macchina virtuale a Rete virtuale][17]

* [Informazioni sui dispositivi VPN per Rete virtuale][11]

* [Risoluzione dei nomi][18]



[1]: http://go.microsoft.com/fwlink/?LinkId=296653
[2]: http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx
[3]: http://go.microsoft.com/fwlink/?LinkID=294356
[4]: http://go.microsoft.com/fwlink/?LinkId=299877
[5]: http://msdn.microsoft.com/en-us/library/windowsazure/jj156090.aspx
[6]: http://go.microsoft.com/fwlink/?LinkId=296652
[7]: http://go.microsoft.com/fwlink/?LinkID=248098
[8]: http://msdn.microsoft.com/library/windowsazure/dn133801.aspx
[9]: http://manage.windowsazure.com/
[10]: http://go.microsoft.com/fwlink/?LinkID=248092
[11]: http://msdn.microsoft.com/en-us/library/windowsazure/jj156075.aspx
[12]: http://go.microsoft.com/fwlink/?LinkId=248098
[13]: http://go.microsoft.com/fwlink/?LinkId=299878
[14]: http://go.microsoft.com/fwlink/?LinkID=299880
[15]: http://msdn.microsoft.com/library/windowsazure/dn133803.aspx
[16]: http://msdn.microsoft.com/en-us/library/windowsazure/jj156097.aspx
[17]: http://www.windowsazure.com/en-us/manage/services/networking/add-a-vm-to-a-virtual-network/
[18]: http://go.microsoft.com/fwlink/?LinkId=248097