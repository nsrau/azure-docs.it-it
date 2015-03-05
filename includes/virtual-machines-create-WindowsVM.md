1. Accedere al [portale di gestione](http://manage.windowsazure.com) di Azure. Se non si dispone ancora di una sottoscrizione, usare la [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/).

2. Fare clic su **Nuovo** nella barra dei comandi nella parte inferiore della finestra.

3. Fare clic su **Calcolo**, quindi su **Macchina virtuale** e infine su **Da raccolta**.

	![Navigate to From Gallery in the Command Bar](./media/virtual-machines-create-WindowsVM/fromgallery.png)
	
4. Nella prima schermata è disponibile l'opzione **Scegli un'immagine**, che consente di selezionare un'immagine per la macchina virtuale da uno degli elenchi in Raccolta immagini. Le immagini visualizzate possono variare in base alla sottoscrizione in uso. Fare clic sulla freccia per continuare.

	![Choose an image](./media/virtual-machines-create-WindowsVM/chooseimage.png)

5. Nella seconda schermata è possibile scegliere un nome computer, una dimensione e il nome e la password dell'amministratore. Se si vuole semplicemente provare Macchine virtuali di Azure, compilare i campi come illustrato nell'immagine sottostante. In caso contrario, scegliere il livello e la dimensione necessari per eseguire l'app o il carico di lavoro. Di seguito sono indicate alcune informazioni utili per agevolare la compilazione: 
	
	- **Nuovo nome utente** indica l'account amministrativo da usare per la gestione del server. Creare una password univoca per questo account e annotarla per evitare di dimenticarla. **Il nome utente e la password serviranno per accedere alla macchina virtuale**. 

	- La dimensione di una macchina virtuale influisce sul costo dell'uso e sulle opzioni di configurazione come il numero di dischi dati che è possibile collegare. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](http://go.microsoft.com/fwlink/p/?LinkId=466520).

	![Configure the properties of the virtual machine](./media/virtual-machines-create-WindowsVM/vmconfiguration.png)



6. Nella terza schermata è possibile configurare le risorse per le connessioni di rete, l'archiviazione e la disponibilità. Di seguito sono indicate alcune informazioni utili per agevolare la compilazione: 
	

	- **Nome DNS del servizio cloud** indica il nome DNS globale che sarà incluso nell'URI usato per contattare la macchina virtuale. Occorre usare il nome del proprio servizio cloud perché deve essere univoco in Azure. I servizi cloud sono importanti per gli scenari in cui si usano [più macchine virtuali](http://azure.microsoft.com/documentation/articles/cloud-services-connect-virtual-machine/).
 
	- Per **Area/Gruppo di affinità/Rete virtuale** usare l'area geografica più appropriata per il luogo in cui ci si trova. In alternativa, è anche possibile scegliere di specificare una rete virtuale.
 
	>[AZURE.NOTE] Se si vuole che una macchina virtuale usi una rete virtuale, è **necessario** specificare la rete quando si crea la macchina. Non è possibile aggiungere la macchina virtuale a una rete virtuale dopo averla creata. Per altre informazioni, vedere [Panoramica di Rete virtuale](http://go.microsoft.com/fwlink/p/?LinkID=294063).

	- Per informazioni dettagliate sulla configurazione degli endpoint, vedere [Come configurare gli endpoint a una macchina virtuale](http://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/).

	![Configure the connected resources of the virtual machine](./media/virtual-machines-create-WindowsVM/resourceconfiguration.png)

7. La quarta schermata di configurazione consente di configurare l'agente di macchine virtuali e alcune delle estensioni disponibili. Fare clic sul segno di spunta per creare la macchina virtuale.


	![Configure VM Agent and extensions for the virtual machine](./media/virtual-machines-create-WindowsVM/agent-and-extensions.png)

	>[AZURE.NOTE] L'agente di macchine virtuali offre l'ambiente per installare le estensioni che consentono di interagire con la macchina virtuale o gestirla. Per informazioni dettagliate, vedere [Gestire le estensioni](http://go.microsoft.com/FWLink/p/?LinkID=390493).  
    
8. Dopo la creazione della macchina virtuale, nel portale di gestione la nuova macchina virtuale viene elencata in **Macchine virtuali**. Vengono creati anche il servizio cloud e l'account di archiviazione corrispondenti ed elencati nelle relative sezioni. La macchina virtuale e il servizio cloud vengono avviati automaticamente e nel portale di gestione viene indicato lo stato **In esecuzione**. 

	![Configure VM Agent and the endpoints of the virtual machine](./media/virtual-machines-create-WindowsVM/vmcreated.png)
<!--HONumber=45--> 
