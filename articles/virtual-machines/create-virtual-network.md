<properties 
	pageTitle="Esercitazione: Creare una rete virtuale solo cloud" 
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
	ms.date="05/18/2015" 
	ms.author="cherylmc"/>

# Esercitazione: Creare una rete virtuale solo cloud in Azure

Questa esercitazione illustra in modo dettagliato le procedure da eseguire nel portale di gestione di Azure per creare una rete virtuale solo cloud di Azure di esempio contenente due subnet. La rete virtuale ottenuta avrà un aspetto analogo al seguente:

![createvnet](./media/create-virtual-network/createVNet_06_VNetExample.png)

Ad esempio, è possibile usare FrontEndSubnet per server Web ed è possibile usare BackEndSubnet per server SQL o controller di dominio.

In questa esercitazione si presuppone che l'utente non abbia mai usato Azure. Permette di acquisire familiarità con i passaggi necessari per creare una rete virtuale personalizzata, esaminando una configurazione di esempio. Per creare una rete virtuale solo cloud adatta alla configurazione specifica, vedere [Configurare una rete virtuale solo cloud nel portale di gestione](http://msdn.microsoft.com/library/azure/dn631643.aspx). Per scenari di progettazione e informazioni avanzate su Rete virtuale, vedere [informazioni generali su Rete virtuale di Azure](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx).


> [AZURE.NOTE]Questa esercitazione non esamina in modo dettagliato la creazione di una configurazione cross-premise, in cui la rete virtuale è connessa alla rete dell'organizzazione. Per un'esercitazione relativa alla creazione di una rete virtuale con connettività cross-premise e connessione VPN da sito a sito (ad esempio, con connessione ad Active Directory o SharePoint presenti nell'azienda), vedere [Esercitazione: Creare una rete virtuale cross-premise per la connettività da sito a sito](../virtual-network/virtual-networks-create-site-to-site-cross-premises-connectivity.md).


##  Obiettivi

In questa esercitazione si apprenderà come configurare una rete virtuale solo cloud di Azure di base con due subnet.

##  Prerequisiti

*  Account Windows con almeno una sottoscrizione Azure valida attiva. Se non si dispone di una sottoscrizione Azure, è possibile effettuare l'iscrizione per ottenere una [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/). Se si ha un abbonamento MSDN, vedere [Offerte speciali di Microsoft Azure: vantaggi per i membri di MSDN, MPN e Bizspark](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

##  Creare la rete virtuale per questa esercitazione

Per creare la rete virtuale solo cloud di esempio, eseguire le operazioni seguenti.

1. Accedere al portale di gestione.

2. Nell'angolo inferiore sinistro della schermata fare clic su **Nuovo** > **Servizi di rete** > **Rete virtuale** e quindi fare clic su **Creazione personalizzata** per avviare la configurazione guidata.

	![][Image1]

3. Nella pagina **Dettagli della rete virtuale** immettere le informazioni seguenti:

- **Nome:** digitare **YourVirtualNetwork**.

- **Area:** la rete virtuale verrà creata in un data center situato nell'area geografica specificata. Per ottenere prestazioni ottimali, selezionare l'area geografica di appartenenza dall'elenco a discesa.
 

	![][Image2]

4. Fare clic sulla freccia Avanti in basso a destra. Per altre informazioni sulle impostazioni disponibili in questa pagina, vedere la sezione Dettagli della rete virtuale della pagina in [Informazioni sulla configurazione di una rete virtuale nel portale di gestione](http://go.microsoft.com/fwlink/p/?linkid=248092&clcid=0x409).

5. Nella pagina **Server DNS e connettività VPN** fare clic sulla freccia Avanti in basso a destra. Azure assegnerà un server DNS di Azure basato su Internet alle nuove macchine virtuali aggiunte alla rete virtuale, in modo da permettere alle VM di accedere alle risorse Internet. Per altre informazioni sulle impostazioni disponibili in questa pagina, vedere la pagina Server DNS e connettività VPN in [Informazioni sulla configurazione di una rete virtuale nel portale di gestione](http://go.microsoft.com/fwlink/p/?linkid=248092&clcid=0x409).
	
6.	Analogamente a una rete reale, la rete virtuale necessita di un intervallo di indirizzi IP, noto come spazio di indirizzi, da assegnare alle macchine virtuali inserite nella rete virtuale. La rete virtuale supporta anche le subnet, che necessitano di spazi di indirizzi specifici, derivati dallo spazio di indirizzi della rete virtuale. Per questa esercitazione verranno create le reti virtuali BackEndSubnet e FrontEndSubnet. Nella pagina **Spazi di indirizzi della rete virtuale** configurare gli elementi seguenti:

	- Per Spazio di indirizzi selezionare **/16 (65535)** in **CIDR (CONTEGGIO INDIRIZZI)**.

	- Per le subnet, digitare nella prima riga **BackEndSubnet** sostituendo il nome esistente e **10.0.1.0** per l'indirizzo IP di inizio, quindi selezionare **/24 (256)** in **CIDR (CONTEGGIO INDIRIZZI)**. Fare clic su **Aggiungi subnet**, quindi digitare **FrontEndSubnet** per il nome e **10.0.2.0** per l'indirizzo IP di inizio.
		
	![][Image4]

 Come si può vedere nel diagramma della rete virtuale, sono stati configurati gli spazi di indirizzi seguenti:
 
	
- FrontEndSubnet: 10.0.2.0/24
- BackEndSubnet: 10.0.1.0/24

 Per altre informazioni sulle impostazioni disponibili nella pagina, vedere Spazi di indirizzi della rete virtuale in [Informazioni sulla configurazione di una rete virtuale nel portale di gestione](http://go.microsoft.com/fwlink/p/?linkid=248092&clcid=0x409).


7. Fare clic sul segno di spunta in basso a destra nella pagina per iniziare a creare la rete virtuale. Al termine della creazione della rete virtuale, in Stato nella pagina **Rete** del portale di gestione di Azure verrà visualizzato **Creato**.  

	![][Image5]

Per altre informazioni sui servizi di infrastruttura di Azure, vedere le risorse seguenti:

- [Come creare una macchina virtuale personalizzata](virtual-machines-create-custom.md) Usare questo argomento per installare una macchina virtuale nella rete virtuale. Per altre informazioni sulle macchine virtuali e sulle opzioni di installazione, vedere [Macchine virtuali di Azure](http://azure.microsoft.com/documentation/services/virtual-machines/).

- [Installazione di una nuova foresta Active Directory in una rete virtuale di Azure](../active-directory-new-forest-virtual-machine.md) Usare questo argomento per installare una nuova foresta Active Directory (AD) di Windows Server senza connettività con altre reti. Nell'esercitazione viene illustrata la procedura specifica necessaria per la creazione di una macchina virtuale per l'installazione di una nuova foresta. Se si desidera svolgere questa esercitazione, non creare macchine virtuali usando il portale di gestione. Per altre informazioni, vedere [Linee guida per la distribuzione di Active Directory di Windows Server nelle macchine virtuali di Azure](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx).

Per rimuovere questa rete virtuale, selezionarla, fare clic su **Elimina** e quindi su **Sì**.

Quando si è pronti per creare una rete virtuale solo cloud adatta alla configurazione specifica, vedere [Configurare una rete virtuale solo cloud nel portale di gestione](http://msdn.microsoft.com/library/azure/dn631643.aspx).

Per scenari di progettazione e informazioni avanzate su Rete virtuale, vedere [Informazioni generali su Rete virtuale di Azure](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx).

Per altre procedure e impostazioni di configurazione di Rete virtuale, vedere [Attività di configurazione di Rete virtuale di Azure](http://go.microsoft.com/fwlink/p/?linkid=296652&clcid=0x409).


## Vedere anche

-  [Domande frequenti sulla rete virtuale di Azure](http://go.microsoft.com/fwlink/p/?LinkId=296650)

-  [Attività di configurazione di Rete virtuale di Azure](http://go.microsoft.com/fwlink/p/?LinkId=296652)

-  [Configurare una rete virtuale usando file di configurazione di rete](../virtual-network/virtual-networks-using-network-configuration-file.md)

-  [Risoluzione dei nomi per le macchine virtuali e le istanze del ruolo](http://go.microsoft.com/fwlink/?LinkId=248097)


[Image1]: ./media/create-virtual-network/createVNet_01_OpenVirtualNetworkWizard.png
[Image2]: ./media/create-virtual-network/createVNet_02_VirtualNetworkDetails.png
[Image3]: ./media/create-virtual-network/createVNet_03_DNSServersandVPNConnectivity.png
[Image4]: ./media/create-virtual-network/createVNet_04_VirtualNetworkAddressSpaces.png
[Image5]: ./media/create-virtual-network/createVNet_05_VirtualNetworkCreatedStatus.png
[Image7]: ./media/create-virtual-network/createVNet_07_VNetExampleSpaces.png
[Image8]: ./media/create-virtual-network/createVNet_07_VNetExampleSpaces.png

 

<!---HONumber=July15_HO1-->