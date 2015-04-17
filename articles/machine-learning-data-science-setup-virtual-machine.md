<properties 
	pageTitle="Configurazione di una macchina virtuale di Azure per l'analisi scientifica dei dati" 
	description="Configurare una macchina virtuale di Azure per utilizzarla in un ambiente di analisi scientifica dei dati cloud con IPython Server." 
	services="machine-learning" 
	documentationCenter="" 
	authors="msolhab" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/17/2015" 
	ms.author="mohabib;xibingao;bradsev" />

# Configurazione di una macchina virtuale di Azure per l'analisi scientifica dei dati

In questo argomento viene illustrato come preparare e configurare una macchina virtuale di Azure da utilizzare come parte di un ambiente di analisi scientifica dei dati basati su cloud. La macchina virtuale di Windows è configurata con strumenti di supporto quali IPython Notebook, Esplora archivi Azure e AzCopy, nonché altre utilità per progetti di analisi scientifica dei dati. Ad esempio, Esplora archivi Azure e AzCopy forniscono modi efficaci per caricare dati nell'archiviazione BLOB di Azure dal computer locale o per scaricarli dall'archiviazione BLOB nel computer locale.

## <a name="create-vm"></a>Passaggio 1: Creare una macchina virtuale di Azure per utilizzo generale

Se già si dispone di una macchina virtuale di Azure e si desidera configurare un server di IPython Notebook su di essa, è possibile procedere direttamente con il [Passaggio 2: Aggiungere un endpoint per IPython Notebook a una macchina virtuale esistente](#add-endpoint). 
 
Prima di iniziare il processo di creazione di una macchina virtuale su Azure, occorre determinare la dimensione della macchina necessaria per l'elaborazione dei dati del relativo progetto. Le macchine di dimensioni inferiori sono dotate di meno memoria e core CPU rispetto alle macchine di dimensioni più elevate, ma sono anche meno costose. Per un elenco dei prezzi e dei tipi di macchine disponibili, vedere la pagina [Macchine virtuali - Prezzi](http://azure.microsoft.com/pricing/details/virtual-machines/) 

1. Accedere a https://manage.windowsazure.com e fare clic su **Nuovo** nell'angolo inferiore sinistro. Viene visualizzata una finestra. Selezionare **CALCOLA** -> **MACCHINA VIRTUALE** -> **DALLA RACCOLTA**.

	![Create workspace][24]

2. Scegliere una delle immagini seguenti: 

	* Windows Server 2012 R2 Datacenter 
	* Esperienza Windows Server Essentials (Windows Server 2012 R2) 

	Fare quindi clic sulla freccia rivolta verso destra in basso a destra per passare alla pagina di configurazione successiva.
	
	![Create workspace][25]

3. Immettere un nome per la macchina virtuale che si desidera creare, selezionare le dimensioni della macchina in base a quelle dei dati che verranno elaborati dalla stessa macchina virtuale e selezionare la potenza desiderata per la macchina virtuale (dimensioni memoria e numero di core di calcolo), immettere nome utente e password per la macchina. Fare quindi clic sulla freccia rivolta verso destra per passare alla pagina di configurazione successiva.

	![Create workspace][26]

4. Selezionare **REGIONE/GRUPPO DI AFFINITÀ/RETE VIRTUALE** che contiene l'**ACCOUNT DI ARCHIVIAZIONE** che si intende utilizzare per la macchina virtuale, quindi selezionare tale account di archiviazione. Aggiungere un endpoint in basso nel campo **ENDPOINT** immettendo il nome dell'endpoint (qui "IPython"). È possibile scegliere qualsiasi stringa come **NOME** dell'endpoint e qualsiasi intero compreso tra 0 e 65536 **disponibile** come **PORTA PUBBLICA**. La **PORTA PRIVATA** deve essere **9999**. È necessario che l'utente **eviti** di usare le porte pubbliche che sono già state assegnate ai servizi Internet. [In Porte per servizi Internet](http://www.chebucto.ns.ca/~rakerman/port-table.html) viene fornito un elenco di porte già assegnate e che devono essere evitate. 

	![Create workspace][27]

	>[AZURE.NOTE] Se l'endpoint viene aggiunto in questa fase, il [Passaggio 2: Aggiungere un endpoint per IPython Notebook a una macchina virtuale esistente](#add-endpoint) può essere ignorato.

5. Fare clic sul segno di spunta per avviare il processo di provisioning della macchina virtuale. 

	![Create workspace][28]


Il completamento di tale processo può richiedere dai 15 ai 25 minuti. Dopo aver creato la macchina virtuale, lo stato di questa macchina dovrebbe essere visualizzato come **In esecuzione**.

![Create workspace][29]
	
## <a name="add-endpoint"></a>Passaggio 2: Aggiungere un endpoint per IPython Notebook a una macchina virtuale esistente

Se è stata creata la macchina virtuale attenendosi alla procedura descritta nel passaggio 1, l'endpoint per IPython Notebook è già stato aggiunto ed è possibile ignorare questo passaggio. 

Se la macchina virtuale già esiste ed è necessario aggiungere un endpoint per IPython Notebook che verrà installato più avanti nel passaggio 3, effettuare prima di tutto l'accesso al portale di gestione di Azure, selezionare la macchina virtuale e aggiungere l'endpoint per il server di IPython Notebook. Nella figura seguente viene riportata una schermata del portale dopo l'aggiunta dell'endpoint per IPython Notebook a una macchina virtuale di Windows. 

![Create workspace][17]

## <a name="run-commands"></a>Passaggio 3: Installare IPython Notebook e altri strumenti di supporto

Dopo aver creato la macchina virtuale, usare Remote Desktop Protocol (RDP) per accedere alla macchina virtuale di Windows. Per informazioni, vedere [Come accedere a una macchina virtuale che esegue Windows Server](virtual-machines-log-on-windows-server.md). Aprire il **prompt dei comandi** (**non la finestra di comando di Powershell**) come amministratore ed eseguire il comando seguente.
 
    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'

	@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Al termine dell'installazione il server di IPython Notebook viene avviato automaticamente nella directory  *C:\Users\&#60;user name>\Documents\IPython Notebooks*.

Quando richiesto, immettere una password per IPython Notebook e la password dell'amministratore del computer. In questo modo IPython Notebook viene abilitato in modo da essere eseguito come servizio sul computer. 

## <a name="access"></a>Passaggio 4: Accedere a IPython Notebook da un Web browser
Per accedere al server di IPython Notebook, aprire un Web browser e inserire  *https://&#60;virtual machine DNS name>:&#60;public port number>* nella casella di testo dell'URL. In questo caso, il *&#60;numero di porta pubblica>* dovrebbe essere quello specificato quando è stato aggiunto l'endpoint di IPython Notebook. Se si sceglie *443* come numero di porta pubblica, è possibile accedere a IPython Notebook senza specificare il numero di porta nella casella di testo dell'URL. In caso contrario, è necessario il **&#60;numero di porta pubblica>*. 

Il *&#60;nome DNS della macchina virtuale>* è disponibile nel portale di gestione di Azure. Dopo aver effettuato l'accesso al portale di gestione, fare clic su **MACCHINE VIRTUALI**, selezionare la macchina creata, quindi selezionare **DASHBOARD**; verrà visualizzato il nome DNS come mostrato di seguito:

![Create workspace][19]

Viene visualizzato l'avviso che indica che è stato riscontrato un problema con il certificato di sicurezza del sito Web (Internet Explorer) oppure che si dispone di una connessione non privata (Chrome), come riportato nelle figure seguenti. Fare clic su **Continuare con il sito Web (scelta non consigliata)** (Internet Explorer) oppure **Avanzate** e **passare a &#60;*Nome DNS*> (non sicuro)** (Chrome) per continuare. Inserire quindi la password specificata in precedenza per accedere a IPython Notebook.

Internet Explorer:
![Create workspace][20]

Chrome:
![Create workspace][21]

Dopo aver effettuato l'accesso a IPython Notebook, viene visualizzata una directory *DataScienceSamples* nel browser. Tale directory contiene esempi di IPython Notebook condivisi da Microsoft per offrire un supporto agli utenti che svolgono attività legate all'analisi scientifica dei dati. Questi esempi di IPython Notebook sono estratti dal [**repository Github **](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) nelle macchine virtuali durante la procedura di configurazione del server di IPython Notebook. Microsoft gestisce e aggiorna frequentemente questi repository. Gli utenti possono consultare il repository Github per ottenere gli esempi di IPython Notebook più recenti. 
![Create workspace][18]

## <a name="upload"></a>Passaggio 5: Caricare un IPython Notebook esistente da un computer locale al server di IPython Notebook

Con IPython Notebook si consente agli utenti di caricare con facilità un IPython Notebook locale sul server di IPython Notebook nelle macchine virtuali. Dopo che gli utenti hanno effettuato l'accesso a IPython Notebook in un Web browser, fare clic sulla **directory** in cui verrà caricato IPython Notebook. Selezionare quindi un file .ipynb di IPython Notebook da caricare dal computer locale in **Esplora file** e trascinarlo nella directory di IPython Notebook nel Web browser. Fare clic sul pulsante **Carica** per caricare il file .ipynb sul server di IPython Notebook. Gli altri utenti potranno quindi iniziare a usarlo dai Web browser di cui dispongono.

![Create workspace][22]

![Create workspace][23]


##<a name="shutdown"></a>Arresto e de-allocazione della macchina virtuale quando non è in uso

Macchine virtuali di Azure è disponibile con **pagamento a consumo**. Per assicurarsi di non subire addebiti quando non si utilizza la macchina virtuale, lo stato deve essere impostato su **Arrestato (deallocato)**.

> [AZURE.NOTE] Se si arresta la macchina virtuale dall'interno (usando le opzioni di risparmio energia di Windows), la macchina virtuale viene arrestata ma rimane allocata. Per assicurarsi di non continuare a subire addebiti, arrestare sempre la macchina virtuale dal [portale di gestione di Azure](http://manage.windowsazure.com/). È inoltre possibile arrestare la macchina virtuale con Powershell chiamando **ShutdownRoleOperation** con "PostShutdownAction" uguale a "StoppedDeallocated".

Per arrestare e deallocare la macchina virtuale:

1. Accedere al [portale di gestione di Azure](http://manage.windowsazure.com/) con il proprio account.  

2. Selezionare **MACCHINE VIRTUALI** dalla barra di spostamento a sinistra.

3. Nell'elenco delle macchine virtuali fare clic sul nome della macchina virtuale, quindi andare alla pagina **DASHBOARD**.

4. Nella parte inferiore della pagina fare clic su **ARRESTO**. 

![VM Shutdown][15]

La macchina virtuale verrà deallocata ma non eliminata. È possibile riavviare la macchina virtuale in qualsiasi momento dal portale di gestione di Azure.

## La macchina virtuale di Azure è pronta all'utilizzo: passaggi successivi

La macchina virtuale è pronta per essere utilizzata negli esercizi di analisi scientifica dei dati. La macchina virtuale può inoltre essere utilizzata come server di IPython Notebook per la navigazione e l'elaborazione dei dati e per altre attività legate ad Azure Machine Learning e al processo di analisi scientifica dei dati. 

I passaggi successivi nel processo di analisi scientifica dei dati sono illustrati nella guida [Learning Guide: Advanced data processing in Azure](machine-learning-data-science-advanced-data-processing.md), che può includere le procedure per lo spostamento, l'elaborazione e il campionamento dei dati in HDInsight in preparazione dell'apprendimento dei dati con Azure Machine Learning.

* Per informazioni su come spostare dati in HDInsight dall'archiviazione BLOB di Azure, vedere [Creare e caricare dati in tabelle Hive dall'archiviazione BLOB di Azure](machine-learning-data-science-hive-tables.md).
* Per informazioni sull'elaborazione dei dati in HDInsight con le query Hive, vedere [Inviare query Hive ai cluster Hadoop di HDInsight nel processo di analisi scientifica dei dati cloud](machine-learning-data-science-process-hive-tables.md).
* Per informazioni sul campionamento dei dati in HDInsight, vedere [Campionare i dati nelle tabelle Hive di Azure HDInsight](machine-learning-data-science-sample-data-hive.md).



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




<!--HONumber=49-->