<properties 
	pageTitle="Esercitazione: Creare una rete virtuale cloud-only" 
	description="Informazioni su come creare una rete virtuale di Azure di esempio solo cloud in questa esercitazione." 
	services="virtual-machines, virtual-network" 
	documentationCenter="" 
	authors="cherylmc" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/29/2014" 
	ms.author="cherylmc"/>

<h1 id="vnettut1">Esercitazione: Creare una rete virtuale solo cloud in Azure</h1>

Questa esercitazione illustra in modo dettagliato le procedure da eseguire nel portale di gestione di Azure per creare una rete virtuale solo cloud di Azure di esempio contenente due subnet. La rete virtuale ottenuta avrà un aspetto analogo al seguente:

![createvnet](./media/create-virtual-network/createVNet_06_VNetExample.png)

Ad esempio, è possibile usare FrontEndSubnet per server Web ed è possibile usare BackEndSubnet per server SQL o controller di dominio.

In questa esercitazione si presuppone che l'utente non abbia mai usato Azure. Permette di acquisire familiarità con i passaggi necessari per creare una rete virtuale personalizzata, esaminando una configurazione di esempio. Per creare una rete virtuale solo cloud adatta alla configurazione specifica, vedere [Configurare una rete virtuale solo cloud nel portale di gestione](http://msdn.microsoft.com/library/azure/dn631643.aspx). Per scenari di progettazione e informazioni avanzate su Rete virtuale, vedere [Informazioni generali su Rete virtuale di Azure](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx).


> [AZURE.NOTE] Questa esercitazione non esamina in modo dettagliato la creazione di una configurazione cross-premise, in cui la rete virtuale è connessa alla rete dell'organizzazione. Per un'esercitazione che illustra in modo dettagliato una rete virtuale con connettività cross-premise e una connessione VPN da sito a sito, ovvero la connessione ad Active Directory o SharePoint presenti nell'organizzazione, vedere [Esercitazione: Creare una rete virtuale cross-premise per la connettività da sito a sito](/manage/services/networking/cross-premises-connectivity/).


##  Obiettivi

In questa esercitazione si apprenderà come configurare una rete virtuale solo cloud di Azure di base con due subnet.

##  Prerequisiti

*  Account Microsoft con almeno una sottoscrizione di Azure valida attiva. Se non si dispone di una sottoscrizione Azure, è possibile iscriversi per ottenere una [versione di valutazione gratuita](http://www.windowsazure.com/pricing/free-trial/). Se si ha un abbonamento a MSDN, vedere [Offerte speciali di Microsoft Azure: vantaggi per i membri di MSDN, MPN e Bizspark](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

##  Creare la rete virtuale per questa esercitazione

Per creare la rete virtuale solo cloud di esempio, eseguire le operazioni seguenti.

1. Accedere al [portale di gestione di Azure](http://manage.windowsazure.com/).

2. Nell'angolo inferiore sinistro della schermata fare clic su **Nuovo** > **Servizi di rete** > **Rete virtuale**, quindi fare clic su **Creazione personalizzata** per avviare la configurazione guidata.

	![][Image1]

3. Nella pagina **Dettagli della rete virtuale** immettere le seguenti informazioni:

- **Nome:** digitare**YourVirtualNetwork**.

- **Area:** la rete virtuale verrà creata in un data center situato nell'area geografica specificata. Per ottenere prestazioni ottimali, selezionare l'area geografica di appartenenza dall'elenco a discesa.
 

	![][Image2]

4. Fare clic sulla freccia Avanti in basso a destra. Per altre informazioni sulle impostazioni disponibili in questa pagina, vedere la sezione Dettagli della rete virtuale della pagina in [Informazioni sulla configurazione di una rete virtuale nel portale di gestione](http://go.microsoft.com/fwlink/?linkid=248092&clcid=0x409).

5. Nella pagina **Server DNS e connettività VPN** fare clic sulla freccia Avanti in basso a destra. Azure assegnerà un server DNS di Azure basato su Internet alle nuove macchine virtuali aggiunte alla rete virtuale, in modo da permettere alle VM di accedere alle risorse Internet. Per altre informazioni sulle impostazioni disponibili in questa pagina, vedere la pagina Server DNS e connettività VPN in [Informazioni sulla configurazione di una rete virtuale nel portale di gestione](http://go.microsoft.com/fwlink/?linkid=248092&clcid=0x409).
	
6.	Analogamente a una rete reale, la rete virtuale necessita di un intervallo di indirizzi IP, noto come spazio di indirizzi, da assegnare alle macchine virtuali inserite nella rete virtuale. La rete virtuale supporta anche le subnet, che necessitano di spazi di indirizzi specifici, derivati dallo spazio di indirizzi della rete virtuale. Per questa esercitazione verranno create le reti virtuali BackEndSubnet e FrontEndSubnet. Nella pagina **Spazi di indirizzi della rete virtuale** configurare i seguenti elementi:

	- Per Spazio di indirizzi selezionare **/16 (65535)** in **CIDR (CONTEGGIO INDIRIZZI)**.

	- Per le subnet, digitare nella prima riga **BackEndSubnet** sostituendo il nome esistente e **10.0.1.0** per l'indirizzo IP di inizio, quindi selezionare **/24 (256)** in **CIDR (CONTEGGIO INDIRIZZI)**. Fare clic su **Aggiungi subnet**, quindi digitare **FrontEndSubnet** per il nome e **10.0.2.0** per l'indirizzo IP di inizio.
		
	![][Image4]

 Come si può vedere nel diagramma della rete virtuale, sono stati configurati gli spazi di indirizzi seguenti:
 
	![][Image7]

 Si noti che lo spazio di indirizzi per una rete virtuale deve derivare dagli spazi di indirizzi privati di 10.0.0.0/8, 172.16.0.0/12 o 192.168.0.0/16 e deve essere specificato nella notazione CIDR (Classless Inter-Domain Routing), definita anche notazione per i prefissi di rete. Per altre informazioni sulle impostazioni disponibili nella pagina, vedere Spazi di indirizzi della rete virtuale in [Informazioni sulla configurazione di una rete virtuale nel portale di gestione](http://go.microsoft.com/fwlink/?linkid=248092&clcid=0x409).


7. Fare clic sul segno di spunta in basso a destra nella pagina per iniziare a creare la rete virtuale. Al termine della creazione della rete virtuale, nell'area Stato della pagina **Reti** del portale di gestione di Azure verrà visualizzato **Creato**.  

	![][Image5]

Per altre informazioni sui servizi di infrastruttura di Azure, vedere le risorse seguenti:

- [Come creare una macchina virtuale personalizzata](http://www.windowsazure.com/manage/windows/how-to-guides/custom-create-a-vm/) Usare questo argomento per installare una macchina virtuale nella rete virtuale. Per altre informazioni sulle macchine virtuali e sulle opzioni di installazione, vedere [Macchine virtuali di Azure](http://www.windowsazure.com/manage/windows/).

- [Installazione di una nuova foresta Active Directory in una rete virtuale di Azure](http://www.windowsazure.com/manage/services/networking/active-directory-forest/) Usare questo argomento per installare una nuova foresta Active Directory (AD) di Windows Server senza connettività con altre reti. Nell'esercitazione viene illustrata la procedura specifica necessaria per la creazione di una macchina virtuale per l'installazione di una nuova foresta. Se si desidera svolgere questa esercitazione, non creare macchine virtuali usando il portale di gestione. Per altre informazioni, vedere [Linee guida per la distribuzione di Active Directory di Windows Server nelle macchine virtuali di Azure](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx).

Per rimuovere questa rete virtuale, selezionarla, fare clic su **Elimina** e quindi su **Sì**.

Quando si è pronti per creare una rete virtuale solo cloud adatta alla configurazione specifica, vedere [Configurare una rete virtuale solo cloud nel portale di gestione](http://msdn.microsoft.com/library/azure/dn631643.aspx). 

Per scenari di progettazione e informazioni avanzate su Rete virtuale, vedere [Informazioni generali su Rete virtuale di Azure](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx). 

Per altre procedure e impostazioni di configurazione di Rete virtuale, vedere [Attività di configurazione di Rete virtuale di Azure](http://go.microsoft.com/fwlink/?linkid=296652&clcid=0x409).


## Vedere anche

-  [Domande frequenti sulla rete virtuale di Azure](http://go.microsoft.com/fwlink/?LinkId=296650)

-  [Attività di configurazione di Rete virtuale di Azure](http://go.microsoft.com/fwlink/?LinkId=296652)

-  [Configurare una rete virtuale usando file di configurazione di rete](http://msdn.microsoft.com/library/windowsazure/jj156097.aspx)

-  [Risoluzione dei nomi (DNS)](http://go.microsoft.com/fwlink/?LinkId=248097)


[Image1]: ./media/create-virtual-network/createVNet_01_OpenVirtualNetworkWizard.png
[Image2]: ./media/create-virtual-network/createVNet_02_VirtualNetworkDetails.png
[Image3]: ./media/create-virtual-network/createVNet_03_DNSServersandVPNConnectivity.png
[Image4]: ./media/create-virtual-network/createVNet_04_VirtualNetworkAddressSpaces.png
[Image5]: ./media/create-virtual-network/createVNet_05_VirtualNetworkCreatedStatus.png
[Image7]: ./media/create-virtual-network/createVNet_07_VNetExampleSpaces.png
[Image8]: ./media/create-virtual-network/createVNet_07_VNetExampleSpaces.png


<!--HONumber=47-->
 