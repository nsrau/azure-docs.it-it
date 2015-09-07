<properties
	pageTitle="Panoramica tecnica di Azure Batch | Microsoft Azure"
	description="Informazioni sui concetti, i flussi di lavoro e gli scenari del servizio Azure Batch"
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/13/2015"
	ms.author="danlep"/>


# Panoramica tecnica di Azure Batch
Azure Batch semplifica l'esecuzione di applicazioni parallele e HPC (High Performance Computing) su larga scala e in modo efficiente nel cloud. Si tratta infatti di un servizio di piattaforma che offre la pianificazione dei processi e la scalabilità automatica di una raccolta gestita di macchine virtuali (VM) per eseguire i processi. Tramite il servizio Batch, è possibile configurare carichi di lavoro batch da eseguire in Azure su richiesta o in base a una pianificazione, senza doversi preoccupare delle difficoltà correlate alla configurazione e alla gestione di un cluster HPC, di macchine virtuali o di uno strumento di pianificazione dei processi.

>[AZURE.NOTE]Per usare Batch, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni, vedere [Creazione di un account Azure](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/).


## Casi d'uso

Batch sfrutta la flessibilità e la scalabilità del cloud per facilitare l'*elaborazione batch* o il *calcolo batch* eseguendo un volume elevato di attività simili per ottenere il risultato desiderato. Un programma della riga di comando o uno script usa un set di file di dati come input, li elabora in una serie di attività e produce un set di file di output. I file di output possono essere i risultati finali o costituire un passaggio intermedio in un flusso di lavoro di maggiori dimensioni.

Il calcolo batch è un modello comune per le organizzazioni che elaborano, trasformano e analizzano elevate quantità di dati, sia in base a una pianificazione che su richiesta. Include l'elaborazione di fine ciclo, quali report giornalieri sui rischi di una banca oppure l'esecuzione o la pianificazione della corresponsione delle retribuzioni. Include inoltre applicazioni aziendali, scientifiche e tecniche su vasta scala che in genere necessitano degli strumenti e delle risorse di un cluster o grid di calcolo. Sono incluse le applicazioni HPC tradizionali quali le simulazioni della dinamica dei fluidi, nonché carichi di lavoro specializzati in settori quali la creazione di contenuti digitali, i servizi finanziari e la ricerca nel campo delle scienze biologiche.

Batch funziona bene con applicazioni o carichi di lavoro intrinsecamente paralleli (a volte definiti "imbarazzantemente paralleli"), che si prestano a essere eseguiti come attività parallele su più computer, ad esempio le VM di calcolo gestite dal servizio Batch.

![Attività parallele][parallel]

**Figura 1. Attività parallele in esecuzione su più computer**

Tra gli esempi sono inclusi:

* Modellazione dei rischi finanziari
* Rendering ed elaborazione di immagini
* Codifica e transcodifica multimediale
* Analisi delle sequenze genetiche
* Test di software

È inoltre possibile usare Batch per eseguire calcoli paralleli con un passaggio di riduzione alla fine e altri carichi di lavoro paralleli più complicati.

>[AZURE.NOTE]Attualmente è possibile eseguire solo i carichi di lavoro di Windows Server in Batch. Inoltre, Batch attualmente non supporta le applicazioni MPI (Message Passing Interface).

## Scenari di sviluppatori

Batch supporta scenari di sviluppo diversi che consentono di configurare ed eseguire i carichi di lavoro paralleli su larga scala con il servizio Batch. Questi scenari usano le API per creare e gestire pool di macchine virtuali (nodi di calcolo) e pianificare i processi e le attività eseguite su di essi. Per altre informazioni su Batch, vedere [Nozioni di base delle API per Azure Batch](batch-api-basics.md).

Scenari di sviluppo tipici di Batch sono riportati nelle sezioni seguenti.

### Scalare orizzontalmente un carico di lavoro parallelo

Usare l'API di Batch per scalare orizzontalmente un lavoro intrinsecamente parallelo, ad esempio il rendering delle immagini in un pool di migliaia di core di calcolo. Invece di configurare un cluster di elaborazione o di scrivere codice per l'inserimento in una coda, la pianificazione dei processi e lo spostamento dei dati di input e output necessari, è possibile automatizzare la pianificazione di processi di calcolo intensi e scalare verticalmente un pool di macchine virtuali di calcolo per eseguirli. È possibile scrivere applicazioni client o front-end per l'esecuzione di processi e attività su richiesta, in base a una pianificazione o come parte di un flusso di lavoro più esteso gestito tramite strumenti quali [Data factory di Azure](https://azure.microsoft.com/documentation/services/data-factory/).

Nella Figura 2 viene mostrato un flusso di lavoro semplificato per l'invio di un'applicazione a un pool di Batch in cui viene distribuita per l'elaborazione.

![Flusso di lavoro degli elementi di lavoro][work_item_workflow]

**Figura 2. Scalare orizzontalmente un carico di lavoro parallelo in Batch**

1.	Caricare i file di input (ad esempio i dati o le immagini di origine) necessari per un processo in un account di archiviazione di Azure. I file devono trovarsi nell'account di archiviazione in modo che il servizio Batch possa accedervi. Il servizio di Batch carica i file nei nodi di elaborazione durante l'esecuzione delle attività.
2.	Caricare i file binari dipendenti nell'account di archiviazione. I file binari includono il programma che viene eseguito dall'attività e gli assembly dipendenti. Questi file devono inoltre essere accessibili dalla risorsa di archiviazione ed essere caricati nei nodi di elaborazione.
3.	Creare un pool di nodi di elaborazione, specificare le proprietà, ad esempio le dimensioni della macchina virtuale e il sistema operativo in esecuzione. È inoltre possibile definire come il numero di nodi nel pool di scalabilità verso l'alto o verso il basso in risposta al carico di lavoro. Quando viene eseguita un'attività, le viene assegnato da questo pool.
4.	Definire un processo da eseguire nel pool.
5.	Aggiungere attività al processo. Ciascuna attività usa il programma caricato per elaborare le informazioni presenti in un file caricato.
6.	Eseguire l'applicazione e monitorare i risultati dell'output.


### Abilitare un'applicazione a elevato utilizzo di calcolo al cloud

È possibile usare l'API di app di anteprima di Batch per eseguire il wrapping di un'applicazione esistente affinché venga eseguita come servizio in un pool di nodi di elaborazione gestiti da Batch in background. L'applicazione può essere una di quelle eseguite in workstation client o in un cluster di calcolo. È possibile sviluppare il servizio per consentire agli utenti eseguire l'offload del lavoro nelle ore di punta verso il cloud o eseguire il lavoro interamente nel cloud. Il framework di App Batch gestisce lo spostamento dei file di input e output, la suddivisione dei processi in attività, l'elaborazione dei processi e delle attività nonché la persistenza dei dati.

>[AZURE.IMPORTANT]Azure offre solamente l'API di App Batch nel modulo di anteprima. Sono necessarie solo attività di sviluppo per i progetti di test o di prova. Le funzionalità delle app di Batch fondamentali verranno integrate nell'API di Batch in versioni future del servizio.

La figura 3 illustra un flusso di lavoro di base per pubblicare un'applicazione mediante l'API di App Batch e quindi consentire a un utente di inviare i processi all'applicazione.

![Flusso di lavoro per la pubblicazione di applicazioni][app_pub_workflow]

**Figura 3. Flusso di lavoro per la pubblicazione e l'esecuzione di un'applicazione con App Batch**

1.	Preparare un'**immagine dell'applicazione**, ovvero un file ZIP degli eseguibili dell'applicazione esistenti e degli eventuali file di supporto necessari. Potrebbe trattarsi degli stessi eseguibili che vengono eseguiti in una server farm o in un cluster tradizionale.
2.	Creare un file con estensione zip dell'**assembly cloud** che richiamerà e invierà i carichi di lavoro al servizio Batch. Questo file contiene due componenti:

	a. **Componente di suddivisione dei processi**: suddivide un processo in attività che è possibile elaborare in modo indipendente. Ad esempio, in uno scenario di animazione un componente di suddivisione dei processi divide il processo di rendering dei filmati in singoli fotogrammi.

	b. **Componente di elaborazione delle attività**: richiama l'eseguibile dell'applicazione per una determinata attività. Ad esempio, in uno scenario di animazione, l'elaboratore delle attività invoca un programma di rendering per eseguire il rendering del singolo frame specificato dall'attività.

3.	Usare l'API o gli strumenti per gli sviluppatori di App Batch per caricare i file con estensione zip preparati nei due passaggi precedenti in un account di archiviazione di Azure. I file devono trovarsi nell'account di archiviazione in modo che il servizio Batch possa accedervi. Questa operazione viene in genere eseguita una volta per ogni applicazione da un amministratore del servizio.
4.	Specificare una modalità di invio dei processi al servizio dell'applicazione abilitato in Azure. Potrebbe trattarsi di un plug-in nell'interfaccia utente dell'applicazione, di un portale Web o di un servizio automatico nell'ambito del sistema di back-end.

	Per eseguire un processo:

	a. Caricare i file di input, ad esempio dati o immagini di origine, specifici per il processo dell'utente. I file devono trovarsi nell'account di archiviazione in modo che il servizio Batch possa accedervi.

	b. Inviare un processo con i parametri richiesti e l'elenco dei file.

	c. Monitorare l'avanzamento dei processi usando le API o il portale di App Batch.



## <a id="BKMK_Account">Account Batch</a>
È necessario creare uno o più **account Batch** univoci da usare e sviluppare con il servizio Batch. Tutte le richieste effettuate al servizio Batch devono essere autenticate usando il nome dell'account e la relativa chiave di accesso.

È possibile creare un account Batch e gestire le chiavi di accesso per l'account nel portale di anteprima di Azure o con i [cmdlet di PowerShell per Batch](batch-powershell-cmdlets-get-started.md).

Per creare un account Batch nel portale:

1. Accedere al [portale di anteprima di Azure](https://portal.azure.com).

2. Fare clic su **Nuovo** > **Calcolo** > **Marketplace** > **Tutto**, quindi immettere*Batch*nella casella di ricerca.

	![Batch in Marketplace][marketplace_portal]

3. Fare clic su **Servizio Batch** nei risultati di ricerca e fare clic su **Crea**.

4. Nel pannello **Nuovo Account Batch**, immettere le informazioni seguenti:

	a. In **Nome account** immettere un nome univoco da usare nell'URL dell'account Batch.

	>[AZURE.NOTE]Il nome dell'account Batch deve essere univoco in Azure e contenere tra 3 e 24 caratteri (sono ammessi solo numeri e lettere minuscole).

	b. Fare clic su **Gruppo di risorse** per selezionare un gruppo di risorse esistenti per l'account o crearne uno nuovo.

	c. Se si dispone di più sottoscrizioni, fare clic su **Sottoscrizione** per selezionare una sottoscrizione disponibile in cui verrà creato l'account.

	d. In **Percorso** selezionare un'area Azure in cui Batch è disponibile.

	![Creare un account Batch][account_portal]

5. Fare clic su **Crea** per completare la creazione dell'account.


Dopo aver creato l'account, sarà possibile trovare nel portale di gestione le chiavi di accesso e altre impostazioni. Ad esempio, fare clic sull'icona a forma di chiave per la gestione delle chiavi di accesso.

![Chiavi di account Batch][account_keys]

## Risorse aggiuntive

* [Introduzione alla libreria di Azure Batch per .NET](batch-dotnet-get-started.md)
* [Strumenti e librerie di sviluppo di Azure Batch](batch-development-libraries-tools.md)
* [Informazioni di riferimento sulle API REST di Azure Batch](http://go.microsoft.com/fwlink/p/?LinkId=517803)
* [Informazioni di riferimento sulle API REST di Azure App Batch](http://go.microsoft.com/fwlink/p/?LinkId=517804)

[parallel]: ./media/batch-technical-overview/parallel.png
[marketplace_portal]: ./media/batch-technical-overview/marketplace_batch.PNG
[account_portal]: ./media/batch-technical-overview/batch_acct_portal.png
[account_keys]: ./media/batch-technical-overview/account_keys.PNG
[work_item_workflow]: ./media/batch-technical-overview/work_item_workflow.png
[app_pub_workflow]: ./media/batch-technical-overview/app_pub_workflow.png

<!---HONumber=August15_HO9-->