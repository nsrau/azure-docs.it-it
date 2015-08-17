1. Accedere al [portale di gestione di Azure](http://manage.windowsazure.com). Se non si dispone di una sottoscrizione, usare la [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/).


2. Fare clic su **Nuovo** nella barra dei comandi nella parte inferiore della finestra.


3. Fare clic su **Calcolo**, quindi su **Macchina virtuale** e infine su **Da raccolta**.

	![Passare a Da raccolta nella barra dei comandi](./media/virtual-machines-create-WindowsVM/fromgallery.png)


4. Nella prima schermata è disponibile l'opzione **Scegli un'immagine**, che consente di selezionare la macchina virtuale dall’elenco di immagini disponibili. Le immagini visualizzate possono variare in base alla sottoscrizione in uso.


5. Nella seconda schermata è possibile scegliere un nome computer, una dimensione e il nome e la password dell'amministratore. Scegliere il livello e la dimensione necessari per eseguire l'app o il carico di lavoro. Di seguito sono indicate alcune informazioni utili per agevolare la compilazione:

	- **Nuovo nome utente** indica l'account amministrativo da usare per la gestione del server. Creare una password univoca per questo account e annotarla per evitare di dimenticarla. **Il nome utente e la password serviranno per accedere alla macchina virtuale**.

	- La dimensione di una macchina virtuale influisce sul costo dell'uso e sulle opzioni di configurazione come il numero di dischi dati che è possibile collegare. Per informazioni dettagliate, vedere [Dimensioni per le macchine virtuali](../articles/virtual-machines-size-specs.md) e [Dimensioni per i servizi cloud](../articles/cloud-services-sizes-specs.md) in Azure.


6. Nella terza schermata è possibile configurare le risorse per le connessioni di rete, l'archiviazione e la disponibilità. Di seguito sono indicate alcune informazioni utili per agevolare la compilazione:


	- **Nome DNS del servizio cloud** indica il nome DNS globale che sarà incluso nell'URI usato per contattare la macchina virtuale. Occorre usare il nome del proprio servizio cloud perché deve essere univoco in Azure. I servizi cloud sono importanti per gli scenari in cui si usano [più macchine virtuali](../articles/cloud-services-connect-virtual-machine.md).

	- Per **Regione/Gruppo di affinità/Rete virtuale** usare l'area geografica più appropriata per il luogo in cui ci si trova. In alternativa, è anche possibile scegliere di specificare una rete virtuale.

	>[AZURE.NOTE]Se si desidera che una macchina virtuale usi una rete virtuale, è **necessario** specificare la rete quando si crea la macchina. Non è possibile aggiungere la macchina virtuale a una rete virtuale dopo averla creata. Per altre informazioni, vedere [Panoramica di Rete virtuale](http://go.microsoft.com/fwlink/p/?LinkID=294063).

	- Per informazioni dettagliate sulla configurazione degli endpoint, vedere [Come configurare gli endpoint a una macchina virtuale](../articles/virtual-machines-set-up-endpoints.md).


7. La quarta schermata di configurazione consente di installare l'agente di macchine virtuali e alcune delle estensioni disponibili.


	>[AZURE.NOTE]L'agente di macchine virtuali offre l'ambiente per installare le estensioni che consentono di interagire con la macchina virtuale o gestirla. Per informazioni dettagliate, vedere [Gestire le estensioni](http://go.microsoft.com/FWLink/p/?LinkID=390493).

8. Dopo la creazione della macchina virtuale, nel portale di gestione la nuova macchina virtuale viene elencata in **Macchine virtuali**. Vengono creati anche il servizio cloud e l'account di archiviazione corrispondenti ed elencati nelle relative sezioni. La macchina virtuale e il servizio cloud vengono avviati automaticamente e viene indicato lo stato **In esecuzione**.

	![Configurare l'agente di macchine virtuali e gli endpoint della macchina virtuale](./media/virtual-machines-create-WindowsVM/vmcreated.png)

<!---HONumber=August15_HO6-->