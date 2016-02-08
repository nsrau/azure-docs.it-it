<properties
	pageTitle="Configurare una macchina virtuale SQL Server come server IPython Notebook | Microsoft Azure"
	description="Configurazione di una macchina virtuale per l'analisi scientifica dei dati con SQL Server e IPython Server."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev" 
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/13/2015"
	ms.author="mohabib;xibingao;bradsev" />

# Configurare una macchina virtuale SQL Server di Azure come server IPython Notebook per l'analisi avanzata

In questo argomento viene illustrato come preparare e configurare una macchina virtuale di SQL Server da utilizzare come parte di un ambiente di analisi scientifica dei dati basati su cloud. La macchina virtuale di Windows è configurata con strumenti di supporto quali IPython Notebook, Esplora archivi Azure e AzCopy, nonché altre utilità per progetti di analisi scientifica dei dati. Ad esempio, Esplora archivi Azure e AzCopy forniscono modi efficaci per caricare dati nell'archiviazione BLOB di Azure dal computer locale o per scaricarli dall'archiviazione BLOB nel computer locale.

Nella raccolta di macchine virtuali di Azure sono disponibili numerose immagini che contengono Microsoft SQL Server. Selezionare l'immagine di una macchina virtuale di SQL Server adatta alle esigenze dei dati di cui si dispone. Di seguito sono riportate le immagini consigliate:

- SQL Server 2012 SP2 Enterprise per dati di piccole e medie dimensioni
- SQL Server 2012 SP2 Enterprise ottimizzato per carichi di lavoro di data warehouse per dati di dimensioni molto elevate

 >[AZURE.NOTE] L'immagine di SQL Server 2012 SP2 Enterprise **non include un disco dati**. Sarà necessario aggiungere e/o collegare uno o più dischi rigidi virtuali per archiviare i dati. Quando si crea una macchina virtuale di Azure, questa contiene un disco per il sistema operativo mappato all'unità C e un disco temporaneo mappato all'unità D. Non usare l'unità D per archiviare i dati. Come si può dedurre dal nome, fornisce solo archiviazione temporanea. Non offre funzionalità di ridondanza o backup perché non risiede nel servizio di archiviazione di Azure.


##<a name="Provision"></a>Connettersi al portale di Azure classico ed effettuare il provisioning di una macchina virtuale di SQL Server

1.  Accedere al [portale di Azure classico](http://manage.windowsazure.com/) con il proprio account. Se non si dispone di un account Azure, provare la [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

2.  Nel portale di Azure classico, nella parte sinistra della pagina Web fare clic su **+NUOVO**, su **CALCOLO**, su **MACCHINA VIRTUALE** e infine su **DA RACCOLTA**.

3.  Nella pagina **Crea macchina virtuale** selezionare un'immagine di una macchina virtuale contenente SQL Server in base alle esigenze dei dati di cui si dispone, quindi fare clic sulla freccia Avanti nella parte inferiore destra della pagina. Per le informazioni più aggiornate sulle immagini di SQL Server supportate in Azure, vedere l'argomento [Introduzione a SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294720) nel set di documentazione [SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294719).

	![Selezionare la macchina virtuale SQL Server][1]

4.  Nella prima pagina di **Configurazione macchina virtuale** immettere le informazioni seguenti:

    -   Specificare un nome in **NOME MACCHINA VIRTUALE**.
    -   Nella casella **NEW USER NAME** digitare un nome utente univoco per l'account di amministratore locale della macchina virtuale.
    -   Nella casella **NEW PASSWORD** immettere una password complessa. Per ulteriori informazioni, vedere [Password complesse](http://msdn.microsoft.com/library/ms161962.aspx).
    -   Nella casella **CONFIRM PASSWORD** ridigitare la password.
    -   Selezionare un valore appropriato per **SIZE** nell'elenco a discesa.

     >[AZURE.NOTE] Le dimensioni della macchina virtuale vengono specificate durante il provisioning: A2 corrisponde alle dimensioni minime consigliate per i carichi di lavoro di produzione. Le dimensioni minime consigliate per una macchina virtuale corrispondono ad A3 quando si usa SQL Server Enterprise Edition. Selezionare A3 o un valore superiore per l'uso di SQL Server Enterprise Edition. Selezionare A4 per l'uso di immagini di SQL Server 2012 o 2014 Enterprise ottimizzato per carichi di lavoro transazionali. Selezionare A7 per l'uso di immagini di SQL Server 2012 o 2014 Enterprise ottimizzato per carichi di lavoro di data warehouse. Le dimensioni selezionate limitano il numero di dischi dati che è possibile configurare. Per informazioni aggiornate sulle dimensioni di macchine virtuali disponibili e sul numero di dischi dati che è possibile collegare a una macchina virtuale, vedere [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Per informazioni sui prezzi, vedere [Macchine virtuali - Prezzi](https://azure.microsoft.com/pricing/details/virtual-machines/).

    Fare clic sulla freccia Avanti nella parte inferiore destra per continuare.

    ![Configurazione macchina virtuale][2]

5.  Nella seconda pagina di **Configurazione macchina virtuale** configurare le risorse per la rete, l'archiviazione e la disponibilità:

    -   Nella casella **Servizio cloud** scegliere **Crea un nuovo servizio cloud**.
    -   Nella casella **Nome DNS del servizio cloud** specificare la prima parte del nome DNS desiderato, in modo che completi un nome nel formato **TESTNAME.cloudapp.net**.
    -   Nella casella **REGIONE/GRUPPO DI AFFINITÀ/RETE VIRTUALE** selezionare l'area in cui verrà ospitata l'immagine virtuale.
    -   In **Account di archiviazione** selezionare un account di archiviazione esistente o sceglierne uno generato automaticamente.
    -   Nella casella **AVAILABILITY SET** selezionare **(none)**.
    -   Leggere e accettare le informazioni sui prezzi.

6.	Nella sezione **ENDPOINTS** fare clic sull'elenco a discesa vuoto in **NOME** e selezionare **MSSQL**, quindi digitare il numero di porta dell'istanza del motore di database (**1433** per l'istanza predefinita).

7.  La macchina virtuale di SQL Server può inoltre essere utilizzata come server di IPython Notebook, che verrà configurato in un momento successivo. Aggiungere un nuovo endpoint per specificare la porta da utilizzare per il server di IPython Notebook. Immettere un nome nella colonna **NOME**, selezionare un numero di porta scelto dall'utente per la porta pubblica e 9999 per quella privata.

	Fare clic sulla freccia Avanti nella parte inferiore destra per continuare.

	![Selezionare le porte MSSQL e IPython][3]

8.  Accettare l'opzione **Installa agente di macchine virtuali** predefinita selezionata e fare clic sul segno di spunta nell'angolo inferiore destro della procedura guidata per completare il processo di provisioning della macchina virtuale.

	`![Opzioni finali della macchina virtuale][4]

9.  Attendere durante la preparazione della macchina virtuale in Azure. Lo stato della macchina virtuale attraverserà le fasi seguenti:

    -   Starting (Provisioning)
    -   Stopped
    -   Starting (Provisioning)
    -   Running (Provisioning)
    -   Running

##<a name="RemoteDesktop"></a>Aprire la macchina virtuale tramite Desktop remoto e completare la configurazione

1.  Al termine del provisioning, fare clic sul nome della macchina virtuale per passare alla pagina DASHBOARD. Nella parte inferiore della pagina fare clic su **Connect**.

2.  Scegliere di aprire il file rpd usando il programma Desktop remoto Windows (`%windir%\system32\mstsc.exe`).

3.  Nella finestra di dialogo **Protezione di Windows** immettere la password per l'account Administrator locale specificata in uno dei passaggi precedenti. (Potrebbe essere richiesta la verifica delle credenziali della macchina virtuale).

4.  In occasione del primo accesso alla macchina virtuale, potrebbe essere necessario completare diversi processi, come la configurazione del desktop, l'aggiornamento di Windows e il completamento delle attività di configurazione iniziali di Windows (sysprep). Al termine delle attività di configurazione iniziali di Windows, verranno completate le attività di configurazione di SQL Server. Queste attività potrebbero causare un ritardo di pochi minuti mentre vengono completate. `SELECT @@SERVERNAME` potrebbe non restituire il nome corretto fino al completamento dell'installazione di SQL Server e SQL Server Management Studio potrebbe non essere visibile nella pagina iniziale.

Dopo avere eseguito la connessione alla macchina virtuale con Desktop remoto Windows, la macchina virtuale funziona come qualsiasi altro computer. Eseguire normalmente la connessione all'istanza predefinita di SQL Server con SQL Server Management Studio (in esecuzione nella macchina virtuale).

##<a name="InstallIPython"></a>Installare IPython Notebook e altri strumenti di supporto

Per configurare la nuova macchina virtuale di SQL Server in modo che funzioni da server di IPython Notebook e per installare ulteriori strumenti di supporto, come AzCopy, Esplora archivi Azure, i pacchetti Python per l'analisi scientifica dei dati e altro, all'utente viene fornita una personalizzazione particolare. Per effettuare l'installazione:

- Fare clic con il pulsante destro del mouse sull'icona di avvio di Windows e selezionare **Prompt dei comandi (amministratore)**
- Copiare i seguenti comandi e incollarli nel prompt dei comandi.

    	set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'
    	@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

- Quando richiesto, immettere una password per il server di IPython Notebook.
- Lo script di personalizzazione consente di automatizzare varie procedure post-installazione, tra cui:
	+ Installazione e configurazione del server di IPython Notebook
	+ Apertura di porte TCP in Windows Firewall per gli endpoint creati in precedenza:
	+ Per la connessione remota di SQL Server
	+ Per la connessione remota del server di IPython Notebook
	+ Recupero di blocchi appunti IPython e script SQL di esempio
	+ Download e installazione di pacchetti Python per l'analisi scientifica dei dati
	+ Download e installazione di strumenti Azure come AzCopy ed Esplora archivi Azure <br>
- È possibile accedere a IPython Notebook ed eseguirlo da un browser remoto o locale usando un URL nel formato `https://<virtual_machine_DNS_name>:<port>`, dove la porta indica la porta pubblica di IPython selezionata durante il provisioning della macchina virtuale.
- Il server di IPython Notebook è in esecuzione come servizio in background e verrà riavviato automaticamente quando si riavvierà la macchina virtuale.

##<a name="Optional"></a>Collegare un disco dati secondo necessità

Se l'immagine di macchina virtuale non contiene dischi dati, vale a dire dischi diversi dall'unità C (disco del SO) e dall'unità D (disco temporaneo), è necessario aggiungere uno o più dischi dati per archiviare i dati. L'immagine di macchina virtuale per SQL Server 2012 SP2 Enterprise ottimizzato per carichi di lavoro di data warehouse viene preconfigurata con dischi aggiuntivi per file di dati e log di SQL Server.

 >[AZURE.NOTE] Non usare l'unità D per archiviare i dati. Come si può dedurre dal nome, fornisce solo archiviazione temporanea. Non offre funzionalità di ridondanza o backup perché non risiede nel servizio di archiviazione di Azure.

Per collegare ulteriori dischi dati, attenersi alla procedura illustrata in [Come collegare un disco dati a una macchina virtuale Windows](storage-windows-attach-disk.md), in cui vengono fornite indicazione per effettuare le seguenti operazioni:

1. Collegamento di dischi vuoti alla macchina virtuale di cui è stato effettuato il provisioning nei passaggi precedenti
2. Inizializzazione di nuovi dischi nella macchina virtuale


##<a name="SSMS"></a>Connessione a SQL Server Management Studio e attivazione dell'autenticazione in modalità mista

Il motore di database di SQL Server non può usare l'Autenticazione di Windows senza ambiente di dominio. Per connettersi al motore di database da un altro computer, configurare SQL Server per l'autenticazione in modalità mista. L'autenticazione in modalità mista consente sia l'autenticazione di SQL Server sia l'autenticazione di Windows. La modalità di autenticazione di SQL è necessaria per inserire dati direttamente dai database della macchina virtuale di SQL Server in [Azure Machine Learning Studio](https://studio.azureml.net) tramite il modulo Lettore.

1.  Durante la connessione alla macchina virtuale tramite Desktop remoto, usare il riquadro **Ricerca** di Windows e digitare **SQL Server Management Studio** (SMSS). Fare clic per avviare SQL Server Management Studio (SSMS). È possibile aggiungere un collegamento a SSMS sul desktop per utilizzi futuri.

    ![Avvio di SQL Server Management Studio][5]

    Alla prima apertura di Management Studio è necessario creare gli utenti dell'ambiente Management Studio. L'operazione potrebbe richiedere alcuni istanti.

2.  All'apertura verrà visualizzata la finestra di dialogo **Connetti al server** di Management Studio. Nella casella **Nome server** digitare il nome della macchina virtuale da connettere al motore di database con Esplora oggetti. Anziché il nome della macchina virtuale come **Nome server** è inoltre possibile utilizzare **(locale)** o un singolo punto. Selezionare **Autenticazione di Windows** e lasciare ***nome\_macchina\_virtuale*\\amministratore\_locale** nella casella **Nome utente**. Fare clic su **Connect**.

    ![Connetti al server][6]

	<br>

	 >[AZURE.TIP] È possibile cambiare la modalità di autenticazione di SQL Server tramite una modifica della chiave di registro di Windows oppure tramite SQL Server Management Studio. Per cambiare la modalità di autenticazione tramite una modifica della chiave di registro, avviare una **Nuova query** ed eseguire lo script seguente:

		USE master
    	go

    	EXEC xp_instance_regwrite N'HKEY_LOCAL_MACHINE', N'Software\Microsoft\MSSQLServer\MSSQLServer', N'LoginMode', REG_DWORD, 2
    	go


	Per cambiare la modalità di autenticazione tramite SQL Server management Studio:

3.  In Esplora oggetti di SQL Server Management Studio fare clic con il pulsante destro del mouse sul nome dell'istanza di SQL Server (nome della macchina virtuale) e quindi scegliere **Proprietà**.

    ![Proprietà del server][7]

4.  Nella pagina **Sicurezza**, in **Autenticazione server** selezionare **Autenticazione di SQL Server e di Windows** e quindi fare clic su **OK**.

    ![Selezione della modalità di autenticazione][8]

5.  Nella finestra di dialogo di SQL Server Management Studio fare clic su **OK** per confermare il requisito del riavvio di SQL Server.

6.  In Esplora oggetti fare clic con il pulsante destro del mouse sul server e quindi scegliere **Riavvia**. (Se SQL Server Agent è in esecuzione, anch'esso dovrà essere riavviato).

    ![Riavvio][9]

7.  Nella finestra di dialogo di SQL Server Management Studio fare clic su **Sì** per accettare il riavvio di SQL Server.

##<a name="Logins"></a>Creare gli account di accesso di SQL Server

Per connettersi al motore di database da un altro computer, configurare almeno un account di accesso con autenticazione di SQL Server.

> [AZURE.TIP] È possibile creare nuovi account di accesso di SQL Server a livello di programmazione oppure tramite SQL Server Management Studio. Per creare un nuovo utente sysadmin con l'autenticazione di SQL a livello di programmazione, avviare una **Nuova query** ed eseguire lo script seguente. Sostituire <new user name> con il nome utente e la password selezionati. Regolare i criteri relativi alla password secondo necessità (il codice di esempio consente di disattivare il controllo dei criteri e la scadenza della password). Per ulteriori informazioni sugli account di accesso di SQL Server, vedere [Creazione di un account di accesso](http://msdn.microsoft.com/library/aa337562.aspx).

    USE master
    go

    CREATE LOGIN <new user name> WITH PASSWORD = N'<new password>',
    	CHECK_POLICY = OFF,
    	CHECK_EXPIRATION = OFF;

    EXEC sp_addsrvrolemember @loginame = N'<new user name>', @rolename = N'sysadmin';

Per creare nuovi account di accesso di SQL Server tramite SQL Server Management Studio:

1.  In Esplora oggetti di SQL Server Management Studio espandere la cartella dell'istanza del server in cui si desidera creare il nuovo account di accesso.

2.  Fare clic con il pulsante destro del mouse sulla cartella **Sicurezza** scegliere **Nuovo** e quindi **Account di accesso...**.

    ![Nuovo account di accesso][10]

3.  Nella finestra di dialogo **Account di accesso - Nuovo**, nella pagina **Generale** immettere il nome del nuovo utente nella casella **Nome account di accesso**.

4.  Selezionare **Autenticazione di SQL Server**.

5.  Nella casella **Password** digitare una password per il nuovo utente. Digitare di nuovo la password nella casella **Conferma password**.

6.  Per applicare le opzioni dei criteri password per la complessità e l'imposizione, selezionare **Applica criteri password** (scelta consigliata). Si tratta di un'opzione predefinita quando si seleziona l'autenticazione di SQL Server.

7.  Per applicare le opzioni dei criteri password per la scadenza, selezionare **Imponi scadenza password** (scelta consigliata). Per abilitare questa casella di controllo, è necessario che sia selezionata l'opzione Applica criteri password. Si tratta di un'opzione predefinita quando si seleziona l'autenticazione di SQL Server.

8.  Per forzare un utente a creare una nuova password dopo il primo utilizzo dell'account di accesso, selezionare **Richiedi modifica della password all'accesso successivo** (scelta consigliata se il nome di accesso verrà utilizzato da un altro utente. Se si tratta del proprio nome di accesso, non selezionare questa opzione). Per abilitare questa casella di controllo, è necessario che sia selezionata l'opzione Imponi scadenza password. Si tratta di un'opzione predefinita quando si seleziona l'autenticazione di SQL Server.

9.  Nell'elenco **Database predefinito** selezionare un database predefinito per il nome di accesso. **master** è il valore predefinito per questa opzione. Se il database utente non è ancora stato creato, lasciare questa opzione impostata su **master**.

10. Nell'elenco **Lingua predefinita** lasciare il valore predefinito **default**.

    ![Proprietà account di accesso][11]

11. Se si tratta del primo account di accesso che si crea, è possibile assegnarlo all'amministratore di SQL Server. A questo scopo, nella pagina **Ruoli del server** selezionare **sysadmin**.

    **Nota sulla sicurezza:** i membri del ruolo predefinito del server dispongono del controllo completo sul motore di database. È consigliabile limitare attentamente le appartenenze a questo ruolo.

    ![sysadmin][12]

12. Fare clic su OK.

##<a name="DNS"></a>Determinare il nome DNS della macchina virtuale

Per connettersi al motore di database di SQL Server da un altro computer, è necessario conoscere il nome DNS (Domain Name System) della macchina virtuale. (Si tratta del nome utilizzato da Internet per identificare la macchina virtuale. È possibile utilizzare l'indirizzo IP, ma questo indirizzo può cambiare se Azure sposta le risorse per la ridondanza o la manutenzione. Il nome DNS rimane stabile in quanto può essere reindirizzato a un nuovo indirizzo IP).

1.  Nel portale di Azure classico (o dal passaggio precedente) selezionare **MACCHINE VIRTUALI**.

2.  Nella pagina **ISTANZE MACCHINA VIRTUALE**, nella colonna **NOME DNS**, trovare e copiare il nome DNS della macchina virtuale preceduto da ****http://**. (Nell'interfaccia utente potrebbe non essere visualizzato l'intero nome, ma è possibile fare clic su di esso con il pulsante destro del mouse e scegliere Copia).

##<a name="cde"></a>Eseguire la connessione al motore di database da un altro computer

1.  In un computer connesso a Internet aprire SQL Server Management Studio.

2.  Nella casella **Connetti al server** o **Connetti al motore di database**, nella casella **Nome server** immettere il nome DNS della macchina virtuale (determinato nell'attività precedente) e un numero di porta di endpoint pubblica nel formato *NomeDNS,Numero porta*, ad esempio **tutorialtestVM.cloudapp.net,57500**.

3.  Nella casella **Autenticazione** selezionare **Autenticazione di SQL Server**.

4.  Nella casella **Account di accesso** digitare il nome di un account di accesso creato in una delle attività precedenti.

5.  Nella casella **Password** digitare la password dell'account di accesso creato in una delle attività precedenti.

6.  Fare clic su **Connect**.

##<a name="amlconnect"></a>Connettersi al motore di database da Azure Machine Learning

Nelle fasi successive di Advanced Analytics Process and Technology verrà utilizzato [Azure Machine Learning Studio](https://studio.azureml.net) per creare e distribuire modelli di Machine Learning. Per inserire dati dai database delle macchine virtuali di SQL Server direttamente in Azure Machine Learning per il training o l'assegnazione di punteggi, usare il modulo Lettore in un nuovo esperimento di [Azure Machine Learning Studio](https://studio.azureml.net). In questo argomento vengono descritti dettagliatamente i collegamenti della guida di Advanced Analytics Process and Technology. Per un'introduzione, vedere [Informazioni su Azure Machine Learning Studio](machine-learning-what-is-ml-studio.md).

2.	Nel riquadro **Proprietà** del [modulo Lettore](https://msdn.microsoft.com/library/azure/dn905997.aspx) selezionare **Database SQL di Azure** dall'elenco a discesa **Origine dati**.

3.	Nella casella di testo **Nome server database** immettere `tcp:<DNS name of your virtual machine>,1433`

4.	Immettere il nome utente di SQL nella casella di testo **Nome account utente server**.

5.	Immettere la password dell'utente di SQL nella casella di testo **Password account utente server**.

	![Lettore Azure ML][13]

##<a name="shutdown"></a>Arresto e deallocazione della macchina virtuale quando non in uso

Macchine virtuali di Azure è disponibile con **pagamento a consumo**. Per assicurarsi di non subire addebiti quando non si utilizzano le macchine virtuali, lo stato deve essere impostato su **Arrestato (deallocato)**.

> [AZURE.NOTE] Arrestando la macchina virtuale dall'interno (usando le opzioni di risparmio energia di Windows), la macchina virtuale viene arrestata ma rimane allocata. Per assicurarsi di non subire addebiti, arrestare sempre le macchine virtuali dal [portale di Azure classico](http://manage.windowsazure.com/). È inoltre possibile arrestare la macchina virtuale con Powershell chiamando ShutdownRoleOperation con "PostShutdownAction" uguale a "StoppedDeallocated".

Per arrestare e deallocare la macchina virtuale:

1. Accedere al [portale di Azure classico](http://manage.windowsazure.com/) con il proprio account.  

2. Selezionare **MACCHINE VIRTUALI** dalla barra di spostamento a sinistra.

3. Nell'elenco delle macchine virtuali fare clic sul nome della macchina virtuale, quindi andare alla pagina **DASHBOARD**.

4. Nella parte inferiore della pagina fare clic su **ARRESTO**.

![Arresto della macchina virtuale][15]

La macchina virtuale verrà deallocata ma non eliminata. È possibile riavviare la macchina virtuale in qualsiasi momento dal portale di Azure classico.

## La macchina virtuale di Azure SQL Server è pronta all'utilizzo: passaggi successivi

La macchina virtuale è pronta per essere utilizzata negli esercizi di analisi scientifica dei dati. La macchina virtuale può inoltre essere utilizzata come server di IPython Notebook per la navigazione e l'elaborazione dei dati e per altre attività legate ad Azure Machine Learning e il Cortana Analytics Process (CAP).

I passaggi successivi del processo di analisi scientifica dei dati sono illustrati in [Guida alla formazione: Elaborazione dati avanzata in Azure](machine-learning-data-science-advanced-data-processing.md) e possono includere le procedure per lo spostamento, l'elaborazione e il campionamento dei dati in HDInsight in preparazione dell'apprendimento dei dati con Azure Machine Learning.


[1]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/selectsqlvmimg.png
[2]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/4vm-config.png
[3]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/sqlvmports.png
[4]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmpostopts.png
[5]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/searchssms.png
[6]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/19connect-to-server.png
[7]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/20server-properties.png
[8]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/21mixed-mode.png
[9]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/22restart2.png
[10]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/23new-login.png
[11]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/24test-login.png
[12]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/25sysadmin.png
[13]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/amlreader.png
[15]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmshutdown.png
 

<!---HONumber=AcomDC_0128_2016-->