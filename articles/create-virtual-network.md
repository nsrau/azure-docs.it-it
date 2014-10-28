<properties linkid="manage-services-create-a-virtual-network" urlDisplayName="Tutorial: Create a cloud-only virtual network" pageTitle="Tutorial: Create a cloud-only virtual network" metaKeywords="" description="Learn how to create an example cloud-only Azure Virtual Network in this tutorial." metaCanonical="" services="virtual-machines,virtual-network" documentationCenter="" title="Tutorial: Create a Clound-only Virtual Network in Azure" authors="cherylmc" solutions="" manager="adinah" editor="" />

<tags ms.service="virtual-network" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/29/2014" ms.author="cherylmc"></tags>

# Esercitazione: Creare una rete virtuale solo cloud in Azure

Questa esercitazione illustra in modo dettagliato le procedure da eseguire nel portale di gestione di Azure per creare una rete virtuale solo cloud di Azure di esempio contenente due subnet. La rete virtuale ottenuta avrà un aspetto analogo al seguente:

![createvnet][createvnet]

Ad esempio, è possibile usare FrontEndSubnet per server Web ed è possibile usare BackEndSubnet per server SQL o controller di dominio.

In questa esercitazione si presuppone che l'utente non abbia mai utilizzato Azure. Permette di acquisire familiarità con i passaggi necessari per creare una rete virtuale personalizzata, esaminando una configurazione di esempio. Per creare una rete virtuale solo cloud adatta alla configurazione specifica, vedere [Configurare una rete virtuale solo cloud nel portale di gestione][Configurare una rete virtuale solo cloud nel portale di gestione]. Per scenari di progettazione e informazioni avanzate su Rete virtuale, vedere [Informazioni generali su Rete virtuale di Azure][Informazioni generali su Rete virtuale di Azure].

<div class="dev-callout"> 
<b>Nota</b> 
<p>Questa esercitazione non esamina in modo dettagliato la creazione di una configurazione cross-premise, in cui la rete virtuale &egrave; connessa alla rete dell'organizzazione. Per un'esercitazione che illustra in modo dettagliato una rete virtuale con connettivit&agrave; cross-premise e una connessione VPN da sito a sito, ovvero la connessione ad Active Directory o SharePoint presenti nell'organizzazione, vedere <a href="/it-it/manage/services/networking/cross-premises-connectivity/">Esercitazione: Creare una rete virtuale cross-premise per connettivit&agrave; da sito a sito</a>.</p> 
</div>

## Obiettivi

In questa esercitazione si apprenderà come configurare una rete virtuale solo cloud di Azure di base con due subnet.

## Prerequisiti

-   Account Microsoft con almeno una sottoscrizione di Azure valida attiva. Se non si ha già una sottoscrizione di Azure, è possibile iscriversi per ottenere una versione di valutazione gratuita in [Prova Azure][Prova Azure]. Se si ha un abbonamento a MSDN, vedere [Offerte speciali di Microsoft Azure: vantaggi per i membri di MSDN, MPN e Bizspark][Offerte speciali di Microsoft Azure: vantaggi per i membri di MSDN, MPN e Bizspark].

## Creare la rete virtuale per questa esercitazione

Per creare la rete virtuale solo cloud di esempio, eseguire le operazioni seguenti.

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].

2.  Nell'angolo inferiore sinistro della schermata fare clic su **Nuovo** \> **Servizi di rete** \> **Rete virtuale** e quindi fare clic su **Creazione personalizzata** per avviare la configurazione guidata.

    ![][]

3.  Nella pagina **Dettagli della rete virtuale** immettere le informazioni seguenti:

-   **Nome:** digitare **YourVirtualNetwork**.

-   **Area:** la rete virtuale verrà creata in un data center situato nell'area geografica specificata. Per ottenere prestazioni ottimali, selezionare l'area geografica di appartenenza dall'elenco a discesa.

    ![][1]

1.  Fare clic sulla freccia Avanti in basso a destra. Per altre informazioni sulle impostazioni disponibili in questa pagina, vedere la sezione Dettagli della rete virtuale della pagina in [Informazioni sulla configurazione di una rete virtuale nel portale di gestione][Informazioni sulla configurazione di una rete virtuale nel portale di gestione].

2.  Nella pagina **Server DNS e connettività VPN** fare clic sulla freccia Avanti in basso a destra. Azure assegnerà un server DNS di Azure basato su Internet alle nuove macchine virtuali aggiunte alla rete virtuale, in modo da permettere alle VM di accedere alle risorse Internet. Per altre informazioni sulle impostazioni disponibili in questa pagina, vedere la pagina Server DNS e connettività VPN in [Informazioni sulla configurazione di una rete virtuale nel portale di gestione][Informazioni sulla configurazione di una rete virtuale nel portale di gestione].

3.  Analogamente a una rete reale, la rete virtuale necessita di un intervallo di indirizzi IP, noto come spazio di indirizzi, da assegnare alle macchine virtuali inserite nella rete virtuale. La rete virtuale supporta anche le subnet, che necessitano di spazi di indirizzi specifici, derivati dallo spazio di indirizzi della rete virtuale. Per questa esercitazione verranno create le reti virtuali BackEndSubnet e FrontEndSubnet. Nella pagina **Spazi di indirizzi della rete virtuale** configurare gli elementi seguenti:

    -   Per Spazio di indirizzi selezionare **/16 (65535)** in **CIDR (CONTEGGIO INDIRIZZI)**.

    -   Per le subnet, digitare nella prima riga **BackEndSubnet** sostituendo il nome esistente e **10.0.1.0** per l'indirizzo IP di inizio, quindi selezionare **/24 (256)** in **CIDR (CONTEGGIO INDIRIZZI)**. Fare clic su **Aggiungi subnet**, quindi digitare **FrontEndSubnet** per il nome e **10.0.2.0** per l'indirizzo IP di inizio.

    ![][2]

Come si può vedere nel diagramma della rete virtuale, sono stati configurati gli spazi di indirizzi seguenti:

<p><img src="./media/create-virtual-network/createVNet_07_VNetExampleSpaces.png" /></p>

Si noti che lo spazio di indirizzi per una rete virtuale deve derivare dagli spazi di indirizzi privati di 10.0.0.0/8, 172.16.0.0/12 o 192.168.0.0/16 e deve essere specificato nella notazione CIDR (Classless Inter-Domain Routing), definita anche notazione per i prefissi di rete. Per altre informazioni sulle impostazioni disponibili nella pagina, vedere Spazi di indirizzi della rete virtuale in [Informazioni sulla configurazione di una rete virtuale nel portale di gestione][Informazioni sulla configurazione di una rete virtuale nel portale di gestione].

1.  Fare clic sul segno di spunta in basso a destra nella pagina per iniziare a creare la rete virtuale. Al termine della creazione della rete virtuale, in Stato nella pagina **Rete** del portale di gestione di Azure verrà visualizzato **Creato**.

    ![][3]

Per altre informazioni sui servizi di infrastruttura di Azure, vedere le risorse seguenti:

-   [Come creare una macchina virtuale personalizzata][Come creare una macchina virtuale personalizzata] Usare questo argomento per installare una macchina virtuale nella rete virtuale. Per altre informazioni sulle macchine virtuali e sulle opzioni di installazione, vedere [Macchine virtuali di Azure][Macchine virtuali di Azure].

-   [Installazione di una nuova foresta Active Directory in una rete virtuale di Azure][Installazione di una nuova foresta Active Directory in una rete virtuale di Azure] Usare questo argomento per installare una nuova foresta Active Directory (AD) di Windows Server senza connettività con altre reti. Nell'esercitazione viene illustrata la procedura specifica necessaria per la creazione di una macchina virtuale per l'installazione di una nuova foresta. Se si desidera svolgere questa esercitazione, non creare macchine virtuali usando il portale di gestione. Per altre informazioni, vedere [Linee guida per la distribuzione di Active Directory di Windows Server nelle macchine virtuali di Azure][Linee guida per la distribuzione di Active Directory di Windows Server nelle macchine virtuali di Azure].

Per rimuovere questa rete virtuale, selezionarla, fare clic su **Elimina** e quindi su **Sì**.

Quando si è pronti per creare una rete virtuale solo cloud adatta alla configurazione specifica, vedere [Configurare una rete virtuale solo cloud nel portale di gestione][Configurare una rete virtuale solo cloud nel portale di gestione].

Per scenari di progettazione e informazioni avanzate su Rete virtuale, vedere [Informazioni generali su Rete virtuale di Azure][Informazioni generali su Rete virtuale di Azure].

Per altre procedure e impostazioni di configurazione di Rete virtuale, vedere [Attività di configurazione di Rete virtuale di Azure][Attività di configurazione di Rete virtuale di Azure].

## Vedere anche

-   [Domande frequenti sulla rete virtuale di Azure][Domande frequenti sulla rete virtuale di Azure]

-   [Attività di configurazione di Rete virtuale di Azure][4]

-   [Configurare una rete virtuale usando file di configurazione di rete][Configurare una rete virtuale usando file di configurazione di rete]

-   [Risoluzione dei nomi][Risoluzione dei nomi]

  [createvnet]: ./media/create-virtual-network/createVNet_06_VNetExample.png
  [Configurare una rete virtuale solo cloud nel portale di gestione]: http://msdn.microsoft.com/library/azure/dn631643.aspx
  [Informazioni generali su Rete virtuale di Azure]: http://msdn.microsoft.com/library/windowsazure/jj156007.aspx
  [Esercitazione: Creare una rete virtuale cross-premise per connettività da sito a sito]: /it-it/manage/services/networking/cross-premises-connectivity/
  [Prova Azure]: http://www.windowsazure.com/pricing/free-trial/
  [Offerte speciali di Microsoft Azure: vantaggi per i membri di MSDN, MPN e Bizspark]: http://azure.microsoft.com/it-it/pricing/member-offers/msdn-benefits-details/
  [portale di gestione di Azure]: http://manage.windowsazure.com/
  []: ./media/create-virtual-network/createVNet_01_OpenVirtualNetworkWizard.png
  [1]: ./media/create-virtual-network/createVNet_02_VirtualNetworkDetails.png
  [Informazioni sulla configurazione di una rete virtuale nel portale di gestione]: http://go.microsoft.com/fwlink/?linkid=248092&clcid=0x409
  [2]: ./media/create-virtual-network/createVNet_04_VirtualNetworkAddressSpaces.png
  [3]: ./media/create-virtual-network/createVNet_05_VirtualNetworkCreatedStatus.png
  [Come creare una macchina virtuale personalizzata]: http://www.windowsazure.com/it-it/manage/windows/how-to-guides/custom-create-a-vm/
  [Macchine virtuali di Azure]: http://www.windowsazure.com/it-it/manage/windows/
  [Installazione di una nuova foresta Active Directory in una rete virtuale di Azure]: http://www.windowsazure.com/it-it/manage/services/networking/active-directory-forest/
  [Linee guida per la distribuzione di Active Directory di Windows Server nelle macchine virtuali di Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/jj156090.aspx
  [Attività di configurazione di Rete virtuale di Azure]: http://go.microsoft.com/fwlink/?linkid=296652&clcid=0x409
  [Domande frequenti sulla rete virtuale di Azure]: http://go.microsoft.com/fwlink/?LinkId=296650
  [4]: http://go.microsoft.com/fwlink/?LinkId=296652
  [Configurare una rete virtuale usando file di configurazione di rete]: http://msdn.microsoft.com/it-it/library/windowsazure/jj156097.aspx
  [Risoluzione dei nomi]: http://go.microsoft.com/fwlink/?LinkId=248097
  [Image7]: ./media/create-virtual-network/createVNet_07_VNetExampleSpaces.png
