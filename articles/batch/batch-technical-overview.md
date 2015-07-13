<properties
	pageTitle="Panoramica tecnica di Azure Batch"
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
	ms.date="04/28/2015"
	ms.author="danlep"/>


#Panoramica tecnica di Azure Batch
Azure Batch semplifica l'esecuzione di applicazioni parallele e HPC (High Performance Computing) su larga scala nel cloud. Si tratta infatti di un servizio di piattaforma che offre la pianificazione dei processi e la scalabilità automatica di una raccolta gestita di macchine virtuali (VM) per eseguire i processi. Tramite il servizio Batch, è possibile configurare carichi di lavoro batch da eseguire in Azure su richiesta o in base a una pianificazione, senza doversi preoccupare delle difficoltà correlate alla pianificazione dei processi e alla gestione delle macchine virtuali nella piattaforma sottostante.

>[AZURE.NOTE]Batch è disponibile in anteprima. Per usarlo, è necessario disporre di un account Azure ed effettuare l'iscrizione per la versione di anteprima. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni, vedere [Creazione di un account Azure](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/).


## Casi di utilizzo

Batch sfrutta la flessibilità e la scalabilità del cloud per facilitare l'*elaborazione batch* o il *calcolo batch* eseguendo un volume elevato di attività simili per ottenere il risultato desiderato. Un programma della riga di comando o uno script usa un set di file di dati come input, li elabora in una serie di attività e produce un set di file di output. I file di output possono essere i risultati finali o costituire un passaggio intermedio in un flusso di lavoro di maggiori dimensioni.

Il calcolo batch è un modello comune per le organizzazioni che elaborano, trasformano e analizzano elevate quantità di dati, sia in base a una pianificazione che su richiesta. Include l'elaborazione di fine ciclo, quali report giornalieri sui rischi di una banca oppure l'esecuzione o la pianificazione della corresponsione delle retribuzioni. Include inoltre applicazioni aziendali, scientifiche e tecniche su vasta scala che in genere necessitano degli strumenti e delle risorse di un cluster o grid di calcolo. Sono incluse le applicazioni HPC tradizionali quali le simulazioni della dinamica dei fluidi, nonché carichi di lavoro specializzati in settori quali la progettazione automobilistica, la prospezione di gas e petrolio, la ricerca nel campo delle scienze biologiche e la creazione di contenuti digitali.

Batch funziona bene con applicazioni o carichi di lavoro intrinsecamente paralleli (a volte definiti "imbarazzantemente paralleli"), che si prestano a essere eseguiti come attività parallele su più computer, ad esempio le VM di calcolo gestite dal servizio Batch. Vedere la figura 1.

![Attività parallele][parallel]

**Figura 1. Attività parallele in esecuzione su più computer**

Tra gli esempi sono inclusi:

* Modellazione dei rischi finanziari
* Rendering ed elaborazione di immagini
* Codifica e transcodifica multimediale
* Analisi delle sequenze genetiche
* Test di software

È inoltre possibile usare Batch per eseguire calcoli paralleli con un passaggio di riduzione alla fine e altri carichi di lavoro paralleli non aciclici meno complicati.

>[AZURE.NOTE]L'anteprima di Batch attualmente non supporta le applicazioni MPI (Message Passing Interface).

## Scenari di sviluppatori

Le API di Batch basate su RES supportano due scenari di sviluppatori per semplificare la configurazione e l'esecuzione dei carichi di lavoro batch con il servizio Batch:

1. **Distribuire i calcoli come elementi di lavoro**: usare le API di *Batch* per creare e gestire un pool flessibile di VM di calcolo e specificare il lavoro che eseguiranno. Ciò offre il controllo completo sulle risorse e richiede la gestione della pipeline di esecuzione delle attività da parte del cliente, ad esempio con uno strumento di gestione dei flussi di lavoro o un'utilità di metapianificazione, che è possibile implementare usando le API REST di Batch oppure, facoltativamente, una funzionalità di gestione dei processi dell'elemento di lavoro. Invece di dover configurare un cluster di calcolo di scrivere codice per inserire in una coda e pianificare i processi, è possibile automatizzare la pianificazione dei processi di calcolo e scalare verticalmente un pool di VM di calcolo per eseguirli. Quando si specificano gli elementi di lavoro, si definiscono tutte le dipendenze e lo spostamento dei file di input e output.

2. **Pubblicare ed eseguire le applicazioni con il servizio Batch**: le API di *Batch Apps* operano a un livello più elevato, consentendo di effettuare il wrapping di un'applicazione esistente in modo che possa essere eseguita come servizio in un pool di VM gestite in background da Batch. L'applicazione può essere una di quelle eseguite in workstation client o in un cluster di calcolo. Il framework di Batch Apps gestisce gli spostamenti dei file di input e output, l'esecuzione dei processi, la gestione dei processi e la persistenza dei dati. Batch Apps consente inoltre di modellare le attività per la modalità di partizionamento delle attività e per le procedure in più passaggi in un processo. Sono inclusi un'API basata su REST e il portale di Batch Apps, al quale è possibile accedere dal portale di gestione di Azure e che consente di monitorare i processi inviati.


## Concetti relativi a Batch

Nelle sezioni seguenti sono riepilogati i concetti chiave per lavorare con il servizio e le API Batch. Per altre informazioni, vedere [Nozioni di base delle API per Azure Batch](batch-api-basics.md).

* [Account Batch](#BKMK_Account)
* [Macchine virtuali e pool delle attività](#BKMK_TVM)
* [Elementi di lavoro, processi e attività](#BKMK_Workitem)
* [File e directory](#BKMK_Files)

### <a id="BKMK_Account">Account Batch</a>
Per usare il servizio Batch è necessario un **account Batch** univoco. Tutte le richieste effettuate al servizio Batch devono essere autenticate usando il nome dell'account e la relativa chiave di accesso.

È possibile creare un account Batch e gestire le chiavi di accesso per l'account nel portale di Azure o con i [cmdlet di PowerShell per Batch](batch-powershell-cmdlets-get-started.md).

Per creare un account Batch nel portale:

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).
2. Fare clic su **Nuovo**, **Calcolo**, **Servizio Batch** e quindi su **Creazione rapida**. ![Creare un account Batch][account_portal]

3. Immettere le seguenti informazioni:

	* In **Nome account** immettere un nome univoco da usare nell'URL dell'account Batch.
	* In **Area** selezionare un'area in cui Batch è disponibile.
	* Se sono presenti più sottoscrizioni, in **Sottoscrizione** selezionare una sottoscrizione disponibile su cui verrà fatturato l'uso di Batch.


### <a id="BKMK_TVM">Macchine virtuali e pool delle attività</a>

Una **macchina virtuale delle attività** (TVM) è una VM di Azure che il servizio Batch dedica all'esecuzione di un'attività specifica per l'applicazione, ad esempio un file eseguibile (con estensione exe) o, nel caso di Batch Apps, uno o più programmi da un'immagine di applicazione. A differenza di quanto accade per una tipica VM di Azure, lo sviluppatore non effettua direttamente il provisioning o la gestione di una TVM, ma è il servizio Batch che crea e gestisce le TVM come **pool** di VM configurate in modo simile, come mostrato nella figura 2.

![Pool e TVM][TVM_pool]

**Figura 2. Un pool di TVM**

Se si usano le API di Batch, è possibile creare un pool direttamente oppure crearne uno automaticamente quando si specifica il lavoro da eseguire. Se si usano le API di Batch Apps, viene creato automaticamente un pool quando si esegue l'applicazione Batch abilitata per il cloud.


Gli attributi di un pool includono:

* Una [dimensione](http://msdn.microsoft.com/library/azure/dn197896.aspx) per le TVM
* Il sistema operativo eseguito sulle TVM
* Il numero massimo di TVM
* Un criterio di ridimensionamento per il pool, cioè una formula basata sul carico di lavoro e sull'utilizzo delle risorse correnti che regola in modo dinamico il numero di TVM che elaborano attività
* L'eventuale abilitazione di porte firewall sulle TVM per consentire la comunicazione tra pool
* Un certificato installato sulle TVM, ad esempio per autenticare l'accesso a un account di archiviazione
* Un'attività iniziale per le TVM, per le operazioni da eseguire una sola volta come l'installazione di applicazioni o download dei dati usati dalle attività

>[AZURE.NOTE]Al momento le TVM possono essere eseguite solo su sistema operativo Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2 SP1.

Un pool può essere usato solo dall'account Batch in cui è stato creato. Un account Batch può avere più pool.

A ogni TVM inclusa in un pool viene assegnato un nome univoco e un indirizzo IP associato. Quando una TVM viene rimossa da un pool, perde le modifiche apportate al sistema operativo, i file locali, il nome e l'indirizzo IP. Quando una TVM esce da un pool, la sua durata è terminata.


### <a id="BKMK_Workitem">Elementi di lavoro, processi e attività</a>

Un **elemento di lavoro** è un modello che specifica la modalità di esecuzione di un'applicazione nelle TVM di un pool. Un **processo** è un elemento di lavoro pianificato e può verificarsi una sola volta o ripetersi. Un processo è costituito da una raccolta di **attività**. La figura 3 illustra le relazioni di base.

![Elemento di lavoro, processo e attività][job_task]

**Figura 3. Elemento di lavoro, processo e attività**

A seconda delle API usate per eseguire le operazioni di sviluppo con Batch, è necessario gestire un numero variabile di dettagli sugli elementi di lavoro, sui processi e sulle attività.

* Se si sviluppa un'applicazione con le API di Batch, è necessario definire a livello di codice tutti gli elementi di lavoro, i processi e le attività e configurare i pool di TVM che eseguono le attività.

* Se si integra un'applicazione client usando le API e gli strumenti di Batch Apps, è possibile usare componenti che suddividono automaticamente un processo in attività, elaborano le attività e uniscono i risultati delle singole attività ai risultati del processo finale. Quando si invia il carico di lavoro al servizio Batch, il framework di Batch Apps gestisce i processi ed esegue le attività nelle TVM.



### <a id="BKMK_Files">File e directory</a>

Un file è costituito da dati usati come input in un'attività di processo. Un'attività può presentare uno o più file di input associati o essere priva di file di input associati. Lo stesso file può anche essere usato in più attività; ad esempio per un processo di rendering di filmati potrebbero essere comunemente usati modelli e trame. Per le attività di un processo di analisi dei dati, i file potrebbero essere costituiti da un insieme di osservazioni o misurazioni.

Per ogni attività è presente una directory di lavoro che contiene i dati di input elaborati e i dati di output creati. Tali directory e file sono disponibili per l'uso da parte di altre attività durante l'esecuzione di un processo. Tutte le attività e le directory nonché tutti i file di una TVM sono di proprietà di un singolo account utente.

Ancora una volta, a seconda delle API usate con Batch, sarà necessario gestire un numero variabile di dettagli sui file di input e output dei processi e delle attività. Se si eseguono le operazioni di sviluppo con le API di Batch, è necessario specificare queste dipendenze e gli spostamenti dei file in modo esplicito. Con Batch Apps il framework gestisce automaticamente la maggior parte di questi dettagli.

## Flusso di lavoro degli elementi di lavoro
La figura 4 illustra come inviare un'applicazione a un pool in cui viene distribuita per l'elaborazione. Viene usata l'API di Batch.

![Flusso di lavoro degli elementi di lavoro][work_item_workflow]

**Figura 4. Flusso di lavoro per distribuire elementi di lavoro in VM in pool**

1.	Caricare i file di input (ad esempio i dati o le immagini di origine) necessari per un processo in un account di archiviazione di Azure. I file devono trovarsi nell'account di archiviazione in modo che il servizio Batch possa accedervi. Il servizio Batch li carica in una TVM quando viene eseguita l'attività.
2.	Caricare i file binari dipendenti nell'account di archiviazione. I file binari includono il programma che viene eseguito dall'attività e gli assembly dipendenti. Questi file devono inoltre essere accessibili dalla risorsa di archiviazione ed essere caricati nella TVM.
3.	Creare un pool di TVM, specificando la dimensione delle TVM nel pool, il sistema operativo eseguito e altre proprietà. Quando viene eseguita un'attività, le viene assegnata una TVM da questo pool.
4.	Creare un elemento di lavoro. Quando si crea un elemento di lavoro, viene creato automaticamente un processo. Un elemento di lavoro consente di gestire un processo di attività.
5.	Aggiungere attività al processo. Ciascuna attività usa il programma caricato per elaborare le informazioni presenti in un file caricato.
6.	Eseguire l'applicazione e monitorare i risultati dell'output.

## Flusso di lavoro per la pubblicazione e l'esecuzione di applicazioni
La figura 5 illustra un carico di lavoro di base per pubblicare un'applicazione usando l'API di Batch Apps e quindi inviare i processi all'applicazione abilitata da Batch.

![Flusso di lavoro per la pubblicazione di applicazioni][app_pub_workflow]

**Figura 5. Flusso di lavoro per la pubblicazione e l'esecuzione di un'applicazione con Batch Apps**

1.	Preparare un'**immagine dell'applicazione**, ovvero un file ZIP degli eseguibili dell'applicazione esistenti e degli eventuali file di supporto necessari. Potrebbe trattarsi degli stessi eseguibili che vengono eseguiti in una server farm o in un cluster tradizionale.
2.	Creare un file ZIP dell'**assembly cloud** che richiamerà e invierà i carichi di lavoro al servizio Batch. Contiene due componenti che sono disponibili tramite l'SDK:

	a. **Componente di suddivisione dei processi**: suddivide un processo in attività che è possibile elaborare in modo indipendente. Ad esempio, in uno scenario di animazione un componente di suddivisione dei processi divide il processo di rendering dei filmati in singoli fotogrammi.

	b. **Componente di elaborazione delle attività**: richiama l'eseguibile dell'applicazione per una determinata attività. Ad esempio, in uno scenario di animazione, l'elaboratore delle attività invoca un programma di rendering per eseguire il rendering del singolo frame specificato dall'attività.

3.	Usare le API o gli strumenti per gli sviluppatori di Batch Apps per caricare i file zip preparati nei due passaggi precedenti in un account di archiviazione di Azure. I file devono trovarsi nell'account di archiviazione in modo che il servizio Batch possa accedervi. Questa operazione viene in genere eseguita una volta per ogni applicazione da un amministratore del servizio.
4.	Specificare una modalità di invio dei processi al servizio dell'applicazione abilitato in Azure. Potrebbe trattarsi di un plug-in nell'interfaccia utente dell'applicazione, di un portale Web o di un servizio automatico nell'ambito del sistema di back-end. Sono disponibili esempi con l'SDK che dimostrano le varie opzioni.

	Per eseguire un processo:

	a. Caricare i file di input, ad esempio dati o immagini di origine, specifici per il processo dell'utente. I file devono trovarsi nell'account di archiviazione in modo che il servizio Batch possa accedervi.

	b. Inviare un processo con i parametri richiesti e l'elenco dei file.

	c. Monitorare l'avanzamento dei processi usando le API o il portale di Batch Apps.

	d. Scaricare gli output.

## Risorse aggiuntive

* [Introduzione alla libreria di Azure Batch per .NET](batch-dotnet-get-started.md)
* [Strumenti e librerie di sviluppo di Azure Batch](batch-development-libraries-tools.md)
* [Informazioni di riferimento sulle API REST di Azure Batch](http://go.microsoft.com/fwlink/p/?LinkId=517803)
* [Informazioni di riferimento sulle API REST di Azure Batch Apps](http://go.microsoft.com/fwlink/p/?LinkId=517804)

[parallel]: ./media/batch-technical-overview/parallel.png
[TVM_pool]: ./media/batch-technical-overview/TVM_pool.png
[job_task]: ./media/batch-technical-overview/job_task.png
[account_portal]: ./media/batch-technical-overview/account_portal.png
[work_item_workflow]: ./media/batch-technical-overview/work_item_workflow.png
[app_pub_workflow]: ./media/batch-technical-overview/app_pub_workflow.png

<!---HONumber=62-->