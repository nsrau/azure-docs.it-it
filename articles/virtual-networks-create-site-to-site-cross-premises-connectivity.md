<properties 
	pageTitle="Esercitazione: Creare una rete virtuale cross-premise per la connettività da sito a sito" 
	description="In questa esercitazione vengono fornite informazioni su come creare una rete virtuale di Azure con connettività cross-premise." 
	services="virtual-network" 
	documentationCenter="" 
	authors="cherylmc" 
	manager="adinah" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="cherylmc"/>





<h1 id="vnettut1">Esercitazione: Creare una rete virtuale cross-premise per la connettività da sito a sito</h1>

Questa esercitazione descrive le procedure da seguire per creare una rete virtuale cross-premise di esempio con una connessione da sito a sito. 

Se si desidera creare una rete virtuale solo cloud, vedere [Esercitazione: Creare una rete virtuale solo cloud in Azure](http://azure.microsoft.com/documentation/articles/create-virtual-network/). Se si desidera creare una rete VPN da punto a sito utilizzando certificati e un client VPN, vedere [Configurare una VPN da punto a sito nel portale di gestione](http://go.microsoft.com/fwlink/?LinkId=296653).

In questa esercitazione si presuppone che l'utente non abbia mai usato Azure. L'utente avrà quindi la possibilità di acquisire familiarità con le procedure necessarie per creare una rete virtuale cross-premise di esempio. Per scenari di progettazione e informazioni avanzate su Rete virtuale, vedere le [informazioni generali su Rete virtuale di Azure](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx).

Al termine di questa esercitazione, si disporrà di una rete virtuale cross-premise di esempio. La figura seguente mostra i dettagli, in base alle impostazioni di esempio in questa esercitazione.

![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_12_TutorialCrossPremVNet.png)

Per una copia di questa figura e una figura da usare per rappresentare la propria rete virtuale cross-premise, vedere la [figura di una rete virtuale cross-premise di esempio dall'argomento dell'esercitazione](http://gallery.technet.microsoft.com/Example-cross-premises-e5ecb8bb).

Si noti che le impostazioni di configurazione di esempio usate in questa esercitazione non sono personalizzate per la rete di un'organizzazione specifica. Se configurate con le impostazioni di esempio descritte in questo argomento, la rete virtuale e la connessione da sito a sito non funzioneranno. Per configurare una rete virtuale cross-premise effettivamente funzionante, è necessario collaborare con il reparto IT e l'amministratore di rete della propria organizzazione per ottenere le impostazioni corrette. Per altre informazioni, vedere la sezione **Prerequisiti** di questo argomento.

Per informazioni su come aggiungere una macchina virtuale ed estendere Active Directory locale alla rete virtuale di Azure, vedere gli argomenti seguenti:

-  [Come creare una macchina virtuale personalizzata](http://go.microsoft.com/fwlink/?LinkID=294356)

-  [Installazione di un controller di dominio Active Directory di replica in una rete virtuale di Azure.](http://go.microsoft.com/fwlink/?LinkId=299877)

Per le linee guida sulla distribuzione di Servizi di dominio Active Directory nelle macchine virtuali di Azure, vedere [Linee guida per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx).

Per altre procedure e impostazioni di configurazione di Rete virtuale, vedere [Attività di configurazione di Rete virtuale di Azure](http://go.microsoft.com/fwlink/?LinkId=296652).

##  Obiettivi

In questa esercitazione si apprenderà come:

-  Configurare una rete virtuale di Azure cross-premise di esempio a cui è possibile aggiungere servizi di Azure.

-  Configurare la rete virtuale per la comunicazione con la rete aziendale.

##  Prerequisiti

-  Account Microsoft con almeno una sottoscrizione di Azure valida attiva.  Se non si dispone di una sottoscrizione Azure, è possibile iscriversi per ottenere una [versione di valutazione gratuita](http://www.windowsazure.com/pricing/free-trial/). Se si ha un abbonamento a MSDN, vedere [Offerte speciali di Microsoft Azure: vantaggi per i membri di MSDN, MPN e Bizspark](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

Se si intende usare questa esercitazione per configurare una rete virtuale cross-premise funzionante e personalizzata per la propria organizzazione, sono previsti i seguenti prerequisiti:

-  Spazi di indirizzi IPv4 privati (in notazione CIDR) per la rete virtuale e le relative subnet.

-  Nome e indirizzo IP di un server DNS locale.

-  Dispositivo VPN con un indirizzo IPv4 pubblico. L'indirizzo IP è necessario per completare la procedura guidata. Il dispositivo VPN non può essere protetto tramite NAT (Network Address Translator) e deve soddisfare gli standard del dispositivo minimi. Per altre informazioni, vedere [Informazioni sui dispositivi VPN per Rete virtuale](http://go.microsoft.com/fwlink/?LinkID=248098). 

	Nota: è possibile usare il servizio Routing e Accesso remoto in Windows Server come parte della soluzione VPN. Tuttavia, in questa esercitazione non vengono descritte le procedure di configurazione del servizio Routing e Accesso remoto. 
	Per informazioni sulla configurazione del servizio Routing e Accesso remoto, vedere [Modelli del servizio Routing e Accesso remoto (RRAS)](http://msdn.microsoft.com/library/windowsazure/dn133801.aspx). 

-  Esperienza nella configurazione di un router per una connessione in modalità tunnel IPsec o assistenza da parte di una persona esperta.

-  Set di spazi di indirizzi (in notazione CIDR) che forniscono la sintesi delle posizioni raggiungibili nella rete locale.


## Procedure generali

1.	[Creare una rete virtuale](#CreateVN)

2.	[Avviare il gateway e raccogliere informazioni per l'amministratore di rete](#StartGateway)

3.  [Configurare il dispositivo VPN](#ConfigVPN)

##  <a name="CreateVN">Creare una rete virtuale</a>

Per creare una rete virtuale di esempio per la connessione a una rete aziendale:

1.	Accedere al [portale di gestione di Azure](http://manage.windowsazure.com/).

2.	Nell'angolo inferiore sinistro della schermata fare clic su **Nuovo**. Nel pannello di navigazione fare clic su **Reti** e quindi su **Rete virtuale**. Fare clic su **Creazione personalizzata** per avviare la configurazione guidata. 

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_01_OpenVirtualNetworkWizard.png)

3.	Nella pagina **Dettagli della rete virtuale** immettere le seguenti informazioni e quindi fare clic sulla freccia Avanti in basso a destra. Per altre informazioni sulle impostazioni per la pagina relativa ai dettagli, vedere la sezione **Pagina Dettagli rete virtuale** in [Informazioni sulle impostazioni della rete virtuale nel portale di gestione](http://go.microsoft.com/fwlink/?LinkID=248092).

	-  **Nome:** assegnare un nome alla rete virtuale. Per l'esempio in questa esercitazione digitare **YourVirtualNetwork**.

	-  **Posizione:** selezionare l'area desiderata nell'elenco a discesa. La rete virtuale verrà creata in un data center di Azure situato nell'area specificata.

	
4.	Nella pagina **Server DNS e connettività VPN** immettere le seguenti informazioni e quindi fare clic sulla freccia avanti in basso a destra. 

> [AZURE.NOTE] In questa pagina è possibile selezionare contemporaneamente entrambe le configurazioni **Point-To-Site** e **Site-To-Site**. Ai fini di questa esercitazione, si selezionerà solo la configurazione **Site-To-Site**. Per altre informazioni sulle impostazioni disponibili in questa pagina, vedere la pagina **Server DNS e connettività VPN** in [Informazioni sulla configurazione di una rete virtuale nel portale di gestione](http://go.microsoft.com/fwlink/?LinkID=248092).

	-  **SERVER DNS:** immettere il nome del server DNS e l'indirizzo IP da usare per la risoluzione dei nomi. In genere, si tratta di un server DNS usato per la risoluzione dei nomi locale. Questa impostazione non comporta la creazione di un server DNS. Per l'esempio in questa esercitazione, digitare **YourDNS** per il nome e **10.1.0.4** per l'indirizzo IP.
	-  **Configura una VPN Point-to-Site:** lasciare questo campo vuoto. 
	-  **Configura una VPN Site-to-Site:** selezionare la casella di controllo.
	-  **RETE LOCALE:** selezionare **Specificare una nuova rete locale** dall'elenco a discesa.
 
![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_03_DNSServersandVPNConnectivity.png)

5.	Nella pagina **Connettività Site-to-Site** immettere le seguenti informazioni e quindi fare clic sul segno di spunta in basso a destra. Per altre informazioni sulle impostazioni disponibili in questa pagina, vedere la sezione **Connettività Site-to-Site** in [Informazioni sulle impostazioni della rete virtuale nel portale di gestione](http://go.microsoft.com/fwlink/?LinkID=248092). 

	-  **NOME:** per l'esempio in questa esercitazione digitare **YourCorpHQ**.

	-  **INDIRIZZO IP DISPOSITIVO VPN:** per l'esempio in questa esercitazione, digitare **3.2.1.1**. Altrimenti, immettere l'indirizzo IP pubblico del dispositivo VPN. Se non si dispone di questa informazione, sarà necessario ottenerla prima di passare alle fasi successive della procedura guidata. Si noti che il dispositivo VPN non può essere protetto tramite NAT. Per altre informazioni sui dispositivi VPN, vedere [Informazioni sui dispositivi VPN per Rete virtuale](http://msdn.microsoft.com/library/windowsazure/jj156075.aspx).

	-  **SPAZIO DEGLI INDIRIZZI:** per l'esempio in questa esercitazione digitare **10.1.0.0/16**.
	-  **Aggiungi spazio di indirizzi:** per questa esercitazione non è richiesto uno spazio di indirizzi aggiuntivo.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_04_SitetoSite.png)

6.  Nella pagina **Spazi di indirizzi della rete virtuale** immettere le seguenti informazioni e quindi fare clic sul segno di spunta in basso a destra per configurare la rete. 

	Lo spazio di indirizzi deve essere un intervallo di indirizzi privato, specificato nella notazione CIDR dagli spazi di indirizzi 10.0.0.0/8, 172.16.0.0/12 o 192.168.0.0/16 (come indicato dalla specifica RFC 1918). Per altre informazioni sulle impostazioni disponibili in questa pagina, vedere la sezione **Pagina Spazi di indirizzi della rete virtuale** in [Informazioni sulle impostazioni della rete virtuale nel portale di gestione](http://go.microsoft.com/fwlink/?LinkID=248092).

	-  **Spazio degli indirizzi:** per l'esempio in questa esercitazione fare clic su **CIDR** nell'angolo in alto a destra e immettere le seguenti informazioni:
		-  **IP iniziale:** 10.4.0.0
		-  **CIDR:** /16
	-  **Aggiungi subnet:** per l'esempio in questa esercitazione immettere le seguenti informazioni:
		-  Rinominare **Subnet-1** in **FrontEndSubnet** con IP iniziale **10.4.2.0/24**.
		-  Aggiungere una subnet denominata **BackEndSubnet** con IP iniziale **10.4.3.0/24**.
		-  Aggiungere una subnet denominata **ADDNSSubnet** con IP iniziale **10.4.4.0/24**.
		-  Aggiungere una subnet di gateway con IP iniziale **10.4.1.0/24**.
	-  Per l'esempio in questa esercitazione verificare che siano disponibili tre subnet e che sia stato creato il gateway della subnet, quindi fare clic sul segno di spunta in basso a destra per creare la rete virtuale.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_05_VirtualNetworkAddressSpaces.png)

7.	Dopo avere fatto clic sul segno di spunta, verrà avviato il processo di creazione della rete virtuale. Al termine della creazione della rete virtuale, verrà visualizzato **Creata** nell'elenco **Stato** nella pagina relativa alle reti del portale di gestione 

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_06_VirtualNetworkCreatedStatus.png)

##  <a name="StartGateway">Avviare il gateway</a>

Dopo aver creato la rete virtuale di Azure, attenersi alla procedura seguente per configurare il gateway di rete virtuale e creare la VPN da sito a sito. Questa procedura richiede un dispositivo VPN che soddisfi i requisiti minimi. Per altre informazioni sui dispositivi VPN e sulla configurazione dei dispositivi, vedere [Informazioni sui dispositivi VPN per Rete virtuale](http://go.microsoft.com/fwlink/?LinkID=248098).

**Per avviare il gateway:**

1.	Una volta creata la rete virtuale, nella pagina **Reti** verrà visualizzato lo stato **Creata** per la rete virtuale.

	Nella colonna **NOME** fare clic su **YourVirtualNetwork** (per l'esempio creato in questa esercitazione) per aprire il dashboard.
 
	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_07_ClickYourVirtualNetwork.png)

2.	Fare clic su **DASHBOARD** nella parte superiore della pagina. Nella parte inferiore della pagina Dashboard fare clic su **CREA GATEWAY**. Selezionare **Routing dinamico** o **Routing statico** per il tipo di gateway da creare. 

	Si noti che se si desidera usare la rete virtuale per connessioni punto a sito oltre a quelle sito a sito, è necessario selezionare **Routing dinamico** come tipo di gateway. Prima di creare il gateway, verificare che il dispositivo VPN supporti il tipo di gateway che si intende creare. Per altre informazioni, vedere [Informazioni sui dispositivi VPN per Rete virtuale](http://go.microsoft.com/fwlink/?LinkID=248098). Quando viene richiesto di confermare che si desidera procedere con la creazione del gateway, fare clic su **Sì**.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_08_CreateGateway.png)

3.	All'avvio della creazione del gateway, verrà visualizzato un messaggio che indica che la procedura è stata avviata.

	La creazione del gateway potrebbe richiedere fino a 15 minuti.

4.	Al termine della creazione del gateway, sarà necessario raccogliere le informazioni seguenti per configurare il dispositivo VPN. 

	-  Indirizzo IP del gateway
	-  Chiave condivisa
	-  Modello di script di configurazione del dispositivo VPN

	Attenersi alla procedura seguente:

5.	Per individuare l'indirizzo IP del gateway: l'indirizzo IP del gateway è disponibile nella pagina **DASHBOARD** della rete virtuale. Di seguito è fornito un esempio:

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_09_GatewayIP.png)

6.	Per acquisire la chiave condivisa: la chiave condivisa è disponibile nella pagina **DASHBOARD** della rete virtuale. Fare clic su **Gestisci chiave** nella parte inferiore della schermata e quindi copiare la chiave visualizzata nella finestra di dialogo. Questa chiave sarà necessaria per configurare il tunnel IPsec nel dispositivo VPN aziendale.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_10_ManageSharedKey.png)

7.	Per scaricare il modello di script di configurazione del dispositivo VPN: Nel dashboard fare clic su **Scarica script dispositivo VPN**.

8.	Nella finestra di dialogo **Scaricare uno script di configurazione del dispositivo VPN** selezionare il fornitore, la piattaforma e il sistema operativo per il dispositivo VPN aziendale. Fare clic sul pulsante con il segno di spunta e salvare il file. 

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_11_DownloadVPNDeviceScript.png)

Se il dispositivo VPN non è presente nell'elenco a discesa, vedere [Informazioni sui dispositivi VPN per Rete virtuale](http://go.microsoft.com/fwlink/?LinkID=248098) in MSDN Library per ulteriori modelli di script.


##  <a name="ConfigVPN">Configurare il dispositivo VPN (amministratore di rete)</a>

Poiché ogni dispositivo VPN è diverso, quella fornita di seguito è solo una procedura di carattere generale. Questa procedura deve essere eseguita dall'amministratore di rete.

Lo script di configurazione del dispositivo VPN è disponibile nel portale di gestione o nell'articolo [Informazioni sui dispositivi VPN per Rete virtuale](http://go.microsoft.com/fwlink/?LinkId=248098), in cui vengono inoltre illustrati i tipi di routing e i dispositivi compatibili con la configurazione di routing che si sceglie di usare.

Per ulteriori informazioni sulla configurazione di un gateway di rete virtuale, vedere [Configurare un gateway di rete virtuale nel portale di gestione](http://go.microsoft.com/fwlink/?LinkId=299878) e consultare la documentazione fornita con il dispositivo VPN.

Per questa procedura, si presuppone quanto segue:

-  La persona che configura il dispositivo VPN sia esperta nella configurazione del dispositivo selezionato. A causa dell'elevato numero di dispositivi compatibili con la rete virtuale e delle configurazioni specifiche per ogni famiglia di dispositivi, questa procedura non illustra la configurazione del dispositivo a livello dettagliato. È pertanto importante che la persona che configura il dispositivo abbia familiarità con il dispositivo stesso e le relative impostazioni di configurazione. 

-  Il dispositivo selezionato per l'utilizzo sia compatibile con la rete virtuale. Fare clic [qui](http://go.microsoft.com/fwlink/?LinkID=248098) per verificare la compatibilità del dispositivo.


**Per configurare il dispositivo VPN:**

1.	Modificare lo script di configurazione del dispositivo VPN. Verranno configurate le impostazioni seguenti:

	a.	Criteri di sicurezza

	b.	Tunnel in ingresso

	c.	Tunnel in uscita

2.	Eseguire lo script di configurazione VPN modificato per configurare il dispositivo VPN.

3.	Per verificare la connessione, eseguire uno dei comandi seguenti:

	<table border="1">
	<tr>
	<th>-</th>
	<th>Cisco ASA</th>
	<th>Cisco ISR/ASR</th>
	<th>Juniper SSG/ISG</th>
	<th>Juniper SRX/J</th>
	</tr>
	
	<tr>
	<td><b>Check main mode SAs</b></td>
	<td><FONT FACE="courier" SIZE="-1">show crypto isakmp sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">show crypto isakmp sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">get ike cookie</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">show security ike security-association</FONT></td>
	</tr>
	
	<tr>
	<td><b>Check quick mode SAs</b></td>
	<td><FONT FACE="courier" SIZE="-1">show crypto ipsec sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">show crypto ipsec sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">get sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">show security ipsec security-association</FONT></td>
	</tr>
	</table>


##  Passaggi successivi
Per estendere Active Directory locale alla rete virtuale appena creata, procedere con le esercitazioni riportate di seguito:

-  [Come creare una macchina virtuale personalizzata](http://go.microsoft.com/fwlink/?LinkID=294356)

-  [Installazione di un controller di dominio Active Directory di replica in una rete virtuale di Azure.](http://go.microsoft.com/fwlink/?LinkId=299877)

Per esportare le impostazioni della rete virtuale in un file di configurazione di rete per eseguire il backup della configurazione o per usarlo come modello, vedere [Esportare le impostazioni della rete virtuale in un file di configurazione di rete](http://go.microsoft.com/fwlink/?LinkID=299880).

## Vedere anche

-  [Panoramica di Rete virtuale](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)

-  [Domande frequenti sulla rete virtuale](http://msdn.microsoft.com/library/windowsazure/dn133803.aspx)

-  [Configurare una rete virtuale usando file di configurazione di rete](http://msdn.microsoft.com/library/windowsazure/jj156097.aspx)

-  [Aggiunta di una macchina virtuale a una Rete virtuale](http://www.windowsazure.com/manage/services/networking/add-a-vm-to-a-virtual-network/)

-  [Informazioni sui dispositivi VPN per Rete virtuale](http://msdn.microsoft.com/library/windowsazure/jj15] 75.aspx)

-  [Risoluzione dei nomi (DNS)](http://go.microsoft.com/fwlink/?LinkId=248097)
-  [Configurazione di un ambiente cloud ibrido per l'esecuzione di test](http://azure.microsoft.com/documentation/articles/virtual-networks-setup-hybrid-cloud-environment-testing/)






<!--HONumber=47-->
