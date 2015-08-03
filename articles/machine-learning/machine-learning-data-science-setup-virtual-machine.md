<properties
	pageTitle="Configurare una macchina virtuale come server IPython Notebook | Microsoft Azure"
	description="Configurare una macchina virtuale di Azure per usarla in un ambiente di analisi scientifica dei dati con IPython Server per le analisi avanzate."
	metaKeywords=""
	services="machine-learning"
	solutions="" 
	documentationCenter=""
	authors="msolhab"
	manager="paulettm"
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/29/2015"
	ms.author="mohabib;xibingao;bradsev" />

# Configurare una macchina virtuale di Azure come server IPython Notebook per l'analisi avanzata

Questo argomento illustra come preparare e configurare una macchina virtuale di Azure per l'analisi avanzata, che può essere usata come parte di un ambiente di analisi scientifica dei dati. La macchina virtuale Windows è configurata con strumenti di supporto quali IPython Notebook, Azure Storage Explorer e AzCopy, nonché altre utilità per progetti di analisi scientifica dei dati. Ad esempio, Esplora archivi Azure e AzCopy forniscono modi efficaci per caricare dati nell'archiviazione BLOB di Azure dal computer locale o per scaricarli dall'archiviazione BLOB nel computer locale.

## <a name="create-vm"></a>Passaggio 1: Creare una macchina virtuale di Azure per utilizzo generico

Se si ha già una macchina virtuale di Azure e si vuole configurare un server di IPython Notebook nella VM, è possibile procedere direttamente con il [Passaggio 2: Aggiungere un endpoint per IPython Notebook a una macchina virtuale esistente](#add-endpoint).

Prima di iniziare il processo di creazione di una macchina virtuale su Azure, occorre determinare la dimensione della macchina necessaria per l'elaborazione dei dati del relativo progetto. Le macchine di dimensioni inferiori sono dotate di meno memoria e core CPU rispetto alle macchine di dimensioni più elevate, ma sono anche meno costose. Per un elenco dei prezzi e dei tipi di macchine disponibili, vedere la pagina [Macchine virtuali - Prezzi](http://azure.microsoft.com/pricing/details/virtual-machines/)

1. Accedere a https://manage.windowsazure.com e fare clic su **Nuovo** nell'angolo inferiore sinistro. Viene visualizzata una finestra. Selezionare **CALCOLA** -> **MACCHINA VIRTUALE** -> **DA RACCOLTA**.

	![Creare un'area di lavoro][24]

2. Scegliere una delle immagini seguenti:

	* Windows Server 2012 R2 Datacenter
	* Esperienza Windows Server Essentials (Windows Server 2012 R2)

	Fare quindi clic sulla freccia rivolta verso destra in basso a destra per passare alla pagina di configurazione successiva.

	![Creare un'area di lavoro][25]

3. Immettere un nome per la macchina virtuale che si desidera creare, selezionare le dimensioni della macchina in base a quelle dei dati che verranno elaborati dalla stessa macchina virtuale e selezionare la potenza desiderata per la macchina virtuale (dimensioni memoria e numero di core di calcolo), immettere nome utente e password per la macchina. Fare quindi clic sulla freccia rivolta verso destra per passare alla pagina di configurazione successiva.

	![Creare un'area di lavoro][26]

4. Selezionare **AREA/GRUPPO DI AFFINITÀ/RETE VIRTUALE** che contiene l'**ACCOUNT DI ARCHIVIAZIONE** da usare per la macchina virtuale, quindi selezionare l'account di archiviazione. Aggiungere un endpoint in basso nel campo **ENDPOINT** immettendo il nome dell'endpoint (qui "IPython"). È possibile scegliere qualsiasi stringa come **NOME** dell'endpoint e qualsiasi intero compreso tra 0 e 65536 **disponibile** come **PORTA PUBBLICA**. La **PORTA PRIVATA** deve essere **9999**. È necessario che l'utente **eviti** di usare le porte pubbliche che già assegnate ai servizi Internet. In [Porte per servizi Internet](http://www.chebucto.ns.ca/~rakerman/port-table.html) viene fornito un elenco di porte già assegnate e che devono essere evitate.

	![Creare un'area di lavoro][27]

	>[AZURE.NOTE]Se in questo passaggio viene aggiunto l'endpoint, è possibile ignorare il [Passaggio 2: Aggiungere un endpoint per IPython Notebook a una macchina virtuale esistente](#add-endpoint) può essere ignorato.

5. Fare clic sul segno di spunta per avviare il processo di provisioning della macchina virtuale.

	![Creare un'area di lavoro][28]


Il completamento di tale processo può richiedere dai 15 ai 25 minuti. Dopo aver creato la macchina virtuale, il relativo stato dovrebbe essere visualizzato come **In esecuzione**.

![Creare un'area di lavoro][29]

## <a name="add-endpoint"></a>Passaggio 2: Aggiungere un endpoint per IPython Notebooks a una macchina virtuale esistente

Se è stata creata la macchina virtuale attenendosi alla procedura descritta nel passaggio 1, l'endpoint per IPython Notebook è già stato aggiunto ed è possibile ignorare questo passaggio.

Se la macchina virtuale già esiste ed è necessario aggiungere un endpoint per IPython Notebook che verrà installato più avanti nel passaggio 3, effettuare prima di tutto l'accesso al portale di gestione di Azure, selezionare la macchina virtuale e aggiungere l'endpoint per il server di IPython Notebook. Nella figura seguente viene riportata una schermata del portale dopo l'aggiunta dell'endpoint per IPython Notebook a una macchina virtuale di Windows.

![Creare un'area di lavoro][17]

## <a name="run-commands"></a>Passaggio 3: Installare IPython Notebook e altri strumenti di supporto

Dopo aver creato la macchina virtuale, usare Remote Desktop Protocol (RDP) per accedere alla macchina virtuale di Windows. Per informazioni, vedere [Come accedere a una macchina virtuale che esegue Windows Server](../virtual-machines-log-on-windows-server.md). Aprire il **prompt dei comandi** (**non la finestra di comando di Powershell) come amministratore ed eseguire il comando seguente.

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'

	@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Al termine dell'installazione, il server IPython Notebook viene avviato automaticamente nella directory *C:\Users&#60;nome utente>\Documents\IPython Notebooks*.

Quando richiesto, immettere una password per IPython Notebook e la password dell'amministratore del computer. In questo modo IPython Notebook viene abilitato in modo da essere eseguito come servizio sul computer.

## <a name="access"></a>Passaggio 4: Accedere a IPython Notebook da un Web browser
Per accedere al server IPython Notebook, aprire un Web browser e immettere *https://&#60;virtual nome DNS macchina virtuale>:&#60;numero porta pubblica>* nella casella di testo dell'URL. In questo caso, il *&#60;numero porta pubblica>* dovrebbe essere quello specificato quando è stato aggiunto l'endpoint di IPython Notebook. Se si sceglie *443* come numero di porta pubblica, è possibile accedere a IPython Notebook senza specificare il numero di porta nella casella di testo dell'URL. In caso contrario, the **&#60;numero porta pubblica>* è obbligatorio.

Il*&#60;nome DNS macchina virtuale>* si trova nel portale di gestione di Azure. Dopo aver effettuato l'accesso al portale di gestione, fare clic su **MACCHINE VIRTUALI**, selezionare la macchina creata, quindi selezionare **DASHBOARD**. Verrà visualizzato il nome DNS come mostrato di seguito:

![Creare un'area di lavoro][19]

Viene visualizzato un avviso che indica che _è stato riscontrato un problema con il certificato di sicurezza del sito Web_ (Internet Explorer) oppure che _la connessione non è privata_ (Chrome), come riportato nelle figure seguenti. Fare clic su **Continuare con il sito Web (scelta non consigliata**) (Internet Explorer) oppure **Avanzate** e quindi **passare a &#60;Nome DNS> (non sicuro)** (Chrome) per continuare. Inserire quindi la password specificata in precedenza per accedere a IPython Notebook.

Internet Explorer: ![Creare un'area di lavoro][20]

Chrome: ![Creare un'area di lavoro][21]

Dopo aver eseguito l'accesso a IPython Notebook, nel browser viene visualizzata la directory *DataScienceSamples*. Tale directory contiene esempi di IPython Notebook condivisi da Microsoft per offrire un supporto agli utenti che svolgono attività legate all'analisi scientifica dei dati. Questi esempi di IPython Notebook sono estratti dal [**repository Github**](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) nelle macchine virtuali durante la procedura di configurazione del server IPython Notebook. Microsoft gestisce e aggiorna frequentemente questi repository. Gli utenti possono visitare il repository Github per ottenere gli esempi di IPython Notebook più recenti. ![Creare un'area di lavoro][18]

## <a name="upload"></a>Step 5: Caricare un IPython Notebook esistente da un computer locale nel server IPython Notebook

Con IPython Notebook si consente agli utenti di caricare con facilità un IPython Notebook locale sul server di IPython Notebook nelle macchine virtuali. Dopo che gli utenti hanno effettuato l'accesso a IPython Notebook in un Web browser, fare clic sulla **directory** in cui verrà caricato IPython Notebook. Selezionare quindi un file con estensione IPYNB di IPython Notebook da caricare dal computer locale in **Esplora file** e trascinarlo nella directory di IPython Notebook nel Web browser. Fare clic sul pulsante **Carica** per caricare il file IPYNB sul server IPython Notebook. Gli altri utenti potranno quindi iniziare a usarlo dai Web browser di cui dispongono.

![Creare un'area di lavoro][22]

![Creare un'area di lavoro][23]


##<a name="shutdown"></a>Arrestare e deallocare la macchina virtuale quando non è in uso

Macchine virtuali di Azure è disponibile con **pagamento a consumo**. Per assicurarsi di non ricevere addebiti quando non si usa la macchina virtuale, lo stato deve essere impostato su **Arrestato (deallocato)**.

> [AZURE.NOTE]Se si arresta la macchina virtuale dall'interno (usando le opzioni di risparmio energia di Windows), la macchina virtuale viene arrestata ma rimane allocata. Per assicurarsi di non continuare a ricevere addebiti, arrestare sempre le macchine virtuali dal [portale di gestione di Azure](http://manage.windowsazure.com/). È inoltre possibile arrestare la VM tramite Powershell chiamando **ShutdownRoleOperation** con "PostShutdownAction" uguale a "StoppedDeallocated".

Per arrestare e deallocare la macchina virtuale:

1. Accedere al [portale di gestione di Azure](http://manage.windowsazure.com/) con il proprio account.  

2. Selezionare **MACCHINE VIRTUALI** dalla barra di spostamento a sinistra.

3. Nell'elenco delle macchine virtuali fare clic sul nome della macchina virtuale, quindi andare alla pagina **DASHBOARD**.

4. Nella parte inferiore della pagina fare clic su **ARRESTO**.

![Arresto della macchina virtuale][15]

La macchina virtuale verrà deallocata ma non eliminata. È possibile riavviare la macchina virtuale in qualsiasi momento dal portale di gestione di Azure.

## La macchina virtuale di Azure è pronta all'utilizzo: passaggi successivi

La macchina virtuale è pronta per essere utilizzata negli esercizi di analisi scientifica dei dati. La macchina virtuale può inoltre essere utilizzata come server di IPython Notebook per la navigazione e l'elaborazione dei dati e per altre attività legate ad Azure Machine Learning e Advanced Analytics Process and Technology (ADAPT).

I passaggi successivi del processo ADAPT (Advanced Analytics Process and Technology) sono illustrati in [Guida all'apprendimento: Elaborazione dati avanzata in Azure](machine-learning-data-science-advanced-data-processing.md) e possono includere le procedure per lo spostamento, l'elaborazione e il campionamento dei dati in HDInsight in preparazione dell'apprendimento dei dati con Azure Machine Learning.


[15]: ./media/machine-learning-data-science-setup-virtual-machine/vmshutdown.png
[17]: ./media/machine-learning-data-science-setup-virtual-machine/add-endpoints-after-creation.png
[18]: ./media/machine-learning-data-science-setup-virtual-machine/sample-ipnbs.png
[19]: ./media/machine-learning-data-science-setup-virtual-machine/dns-name-and-host-name.png
[20]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning-ie.png
[21]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning.png
[22]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-1.png
[23]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-2.png
[24]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-1.png
[25]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-2.png
[26]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-3.png
[27]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-4.png
[28]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-5.png
[29]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-6.png
 

<!---HONumber=July15_HO4-->