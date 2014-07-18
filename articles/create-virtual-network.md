<properties  linkid="manage-services-create-a-virtual-network" urlDisplayName="Create a virtual network" pageTitle="Create a virtual network - Azure service management" metaKeywords="" description="Learn how to create an Azure Virtual Network." metaCanonical="" services="virtual-machines,virtual-network" documentationCenter="" title="Create a Virtual Network in Azure" authors="" solutions="" manager="" editor="" />

<h1>Creazione di una rete virtuale in Azure</h1>   


In questa esercitazione vengono descritte le procedure per creare una rete virtuale di Azure di base, utilizzando il portale di gestione di Azure. Per ulteriori informazioni sulla rete virtuale di Azure, vedere le [informazioni generali su Rete virtuale di Azure][1].

In questa esercitazione si presuppone che l'utente non abbia mai utilizzato Azure. L'utente avrà quindi la possibilità di acquisire familiarità con le procedure necessarie per creare una rete virtuale. Per scenari di progettazione e informazioni avanzate su Rete virtuale, vedere [informazioni generali su Rete virtuale di Azure][1].

Al termine di questa esercitazione, sarà disponibile una rete virtuale in cui distribuire servizi e macchine virtuali di Azure.

<div  class="dev-callout" markdown="1">
**Nota**
In questa esercitazione non viene descritta la procedura per la creazione di una configurazione cross-premise. Per un'esercitazione relativa alla creazione di una rete virtuale con connettività cross-premise da sito a sito (ad esempio, con connessione ad Active Directory o SharePoint presenti nell'azienda), vedere [Creazione di una rete virtuale per la connettività cross-premise](/en-us/manage/services/networking/cross-premises-connectivity/).


</div>

Per ulteriori procedure e impostazioni di configurazione di Rete virtuale, vedere [Attività di configurazione di Rete virtuale di Azure][2].

Per le linee guida sulla distribuzione di Servizi di dominio Active Directory nelle macchine virtuali di Azure, vedere [Linee guida per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure][3].

## Obiettivi

In questa esercitazione si apprenderà come:

* Configurare una rete virtuale di Azure di base a cui è possibile
  aggiungere servizi cloud e macchine virtuali di Azure.

## Prerequisiti

* Account Windows Live con almeno una sottoscrizione valida attiva.

## Creare una rete virtuale

**Per creare una rete virtuale cloud-only:**

1.  Accedere al [portale di gestione di Azure][4].

2.  Nell'angolo inferiore sinistro della schermata fare clic su **New**.
    Nel pannello di navigazione fare clic su **Networks** e quindi su
    **Virtual Network**. Fare clic su **Custom Create** per avviare la
    configurazione guidata.
    
    ![](./media/create-virtual-network/createVNet_01_OpenVirtualNetworkWizard.png)

3.  Nella pagina **Virtual Network Details** immettere le informazioni
    seguenti e quindi fare clic sulla freccia Next in basso a destra.
    Per ulteriori informazioni sulle impostazioni per la pagina relativa
    ai dettagli, vedere la sezione **Dettagli rete virtuale** in
    [Informazioni sulla configurazione di una rete virtuale nel portale
    di gestione][5].

* **Name -** assegnare un nome alla rete virtuale. Digitare
  *YourVirtualNetwork*.

* **Affinity Group -** nell'elenco a discesa selezionare **Create a new
  affinity group**. I gruppi di affinità consentono di raggruppare
  fisicamente servizi di Azure nello stesso data center per aumentare le
  prestazioni. Un gruppo di affinità può essere assegnato a una sola
  rete virtuale.

* **Region -** selezionare l'area desiderata nell'elenco a discesa. La
  rete virtuale verrà creata in un data center situato nell'area
  specificata.

* **Affinity Group Name -** assegnare un nome al nuovo gruppo di
  affinità. Digitare *YourAffinityGroup*.
  
	![](./media/create-virtual-network/createVNet_02_VirtualNetworkDetails.png)

1.  Nella pagina **DNS Servers and VPN Connectivity** immettere le
    informazioni seguenti e quindi fare clic sulla freccia Next in basso
    a destra. Per ulteriori informazioni sulle impostazioni disponibili
    in questa pagina, vedere **Server DNS e connettività VPN** in
    [Informazioni sulla configurazione di una rete virtuale nel portale
    di gestione][5].
    
    * **DNS Servers-Optional -** immettere il nome del server DNS e
      l'indirizzo IP da utilizzare. Questa impostazione non crea un
      server DNS, ma si riferisce a un server DNS esistente.
      
		<div  class="dev-callout" markdown="1">

		<b>Nota</b>
		<p>Se si desidera utilizzare un servizio DNS pubblico, è possibile
		immettere l'informazione su questa schermata. In caso contrario,
		per impostazione predefinita la risoluzione dei nomi verrà gestita
		dal servizio di Azure. Per ulteriori informazioni, vedere
		<a href="http://go.microsoft.com/fwlink/?linkid=248097">Risoluzione dei nomi</a>
		</p>

		</div>

    
    * **Non selezionare la casella di controllo per la connettività
      point-to-site o site-to-site.** La rete virtuale creata in questa
      esercitazione non è progettata per la connettività cross-premise.
    
	![](./media/create-virtual-network/createVNet_03_DNSServersandVPNConnectivity.png)

2.  Nella pagina **Virtual Network Address Spaces** immettere le
    informazioni seguenti e quindi fare clic sul segno di spunta in
    basso a destra per configurare la rete. Lo spazio di indirizzi deve
    essere un intervallo di indirizzi privato, specificato nella
    notazione CIDR 10.0.0.0/8, 172.16.0.0/12 o 192.168.0.0/16 (come
    indicato dalla specifica RFC 1918). Per ulteriori informazioni sulle
    impostazioni disponibili in questa pagina, vedere la sezione **Spazi
    di indirizzi della rete virtuale** in [Informazioni sulla
    configurazione di una rete virtuale nel portale di gestione][5].
    
    * **Address Space:** fare clic su CIDR nell'angolo superiore destro
      e immettere le informazioni seguenti:
      
      * **Starting IP:** 10.4.0.0
      
      * **CIDR:** /16
    
    * **Add subnet:** immettere le informazioni seguenti:
      
      * **Rinominare Subnet-1** in *FrontEndSubnet* con IP iniziale
        *10.4.2.0/24* e quindi fare clic su **add subnet**.
      
      * **Creare una subnet** denominata *BackEndSubnet* con IP iniziale
        *10.4.3.0/24*.
      
      * Verificare che siano state create due subnet e quindi fare clic
        sul segno di spunta in basso a destra per creare la rete
        virtuale.
    
    ![](./media/create-virtual-network/createVNet_04_VirtualNetworkAddressSpaces.png)

3.  Dopo avere fatto clic sul segno di spunta, verrà avviato il processo
    di creazione della rete virtuale. Al termine della creazione della
    rete virtuale, in **Status** verrà visualizzata la dicitura
    **Created** nella pagina relativa alle reti del portale di gestione.
    
    ![](./media/create-virtual-network/createVNet_05_VirtualNetworkCreatedStatus.png)

4.  Dopo la creazione della rete virtuale, è possibile procedere con le
    esercitazioni riportate di seguito:
    
    * [Aggiunta di una macchina virtuale a una Rete
      virtuale](/en-us/manage/services/networking/add-a-vm-to-a-virtual-network/) - Svolgere questa esercitazione di base per
      installare una macchina virtuale nella rete virtuale.
    
    * Per ulteriori informazioni sulle macchine virtuali e le opzioni di
      installazione, vedere [Come creare una macchina virtuale
      personalizzata](/en-us/manage/windows/how-to-guides/custom-create-a-vm/) e [Macchine virtuali di
      Azure](/en-us/manage/windows/).
    
    * [Installazione di una nuova foresta Active Directory in una rete
      virtuale di
      Azure](/en-us/manage/services/networking/active-directory-forest/) - Svolgere questa esercitazione per
      installare una nuova foresta Active Directory senza connettività
      con altre reti. Nell'esercitazione viene illustrata la procedura
      specifica necessaria per la creazione di una macchina virtuale per
      l'installazione di una nuova foresta. Se si desidera svolgere
      questa esercitazione, non creare macchine virtuali utilizzando il
      portale di gestione.

## Vedere anche

* [Rete virtuale di Azure][1]

* [Domande frequenti sulla rete virtuale di Azure][7]

* [Attività di configurazione di Rete virtuale di Azure][2]

* [Configurare una rete virtuale utilizzando file di configurazione di
  rete][8]

* [Risoluzione dei nomi][9]



[1]: http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx
[2]: http://go.microsoft.com/fwlink/?LinkId=296652
[3]: http://msdn.microsoft.com/en-us/library/windowsazure/jj156090.aspx
[4]: http://manage.windowsazure.com/
[5]: http://go.microsoft.com/fwlink/?LinkID=248092
[6]: http://go.microsoft.com/fwlink/?linkid=248097
[7]: http://go.microsoft.com/fwlink/?LinkId=296650
[8]: http://msdn.microsoft.com/en-us/library/windowsazure/jj156097.aspx
[9]: http://go.microsoft.com/fwlink/?LinkId=248097