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
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="danlep"/>


#Panoramica tecnica di Azure Batch
Batch di Azure consente di eseguire su larga scala parallele e alta applicazioni performance computing (HPC) nel cloud. È un servizio di piattaforma che fornisce la pianificazione dei processi e la scalabilità automatica di una raccolta di macchine virtuali (VM) per eseguire i processi gestita. Tramite il servizio di Batch, è possibile configurare i carichi di lavoro batch per l'esecuzione in Azure su richiesta o in una pianificazione e senza preoccuparsi della complessità di pianificazione dei processi e la gestione delle macchine virtuali nella piattaforma sottostante.

>[AZURE.NOTE]Batch è disponibile in anteprima. Per utilizzare Batch, è necessario un account Azure ed è necessario effettuare l'iscrizione per l'anteprima del Batch. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni, vedere [Creazione di un account Azure](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/).


## Casi di utilizzo

Batch utilizza la flessibilità e la scala del cloud per aiutarvi a *l'elaborazione batch* o *computing batch* -esecuzione di un volume elevato di operazioni simili per ottenere alcuni risultato desiderato. Un programma della riga di comando o uno script usa un set di file di dati come input, li elabora in una serie di attività e produce un set di file di output. I file di output potrebbero essere il risultato finale o un passaggio intermedio in un flusso di lavoro più grande.

Il calcolo batch è un modello comune per le organizzazioni che elaborano, trasformano e analizzano elevate quantità di dati, sia in base a una pianificazione che su richiesta. Include l'elaborazione di fine ciclo, quali report giornalieri sui rischi di una banca oppure l'esecuzione o la pianificazione della corresponsione delle retribuzioni. Include inoltre applicazioni aziendali, scientifiche e tecniche su vasta scala che in genere necessitano degli strumenti e delle risorse di un cluster o grid di calcolo. Sono incluse le applicazioni HPC tradizionali quali le simulazioni della dinamica dei fluidi, nonché carichi di lavoro specializzati in settori quali la progettazione automobilistica, la prospezione di gas e petrolio, la ricerca nel campo delle scienze biologiche e la creazione di contenuti digitali.

Batch funziona bene con applicazioni o carichi di lavoro intrinsecamente paralleli (a volte definiti "imbarazzantemente paralleli"), che si prestano a essere eseguiti come attività parallele su più computer, ad esempio le VM di calcolo gestite dal servizio Batch. Vedere la Figura 1.

![Attività in parallelo][parallel]

**Figura 1. Attività in parallelo in esecuzione su più computer**

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

1. **Distribuiscono i calcoli come elementi di lavoro** -utilizzare il *Batch* API per creare e gestire un pool di macchine virtuali di calcolo flessibile e specificare il lavoro verrà effettuato. Questo consente un controllo completo sulle risorse e il client deve gestire la pipeline di esecuzione attività - ad esempio, con un gestore del flusso di lavoro o meta-utilità di pianificazione, che può essere implementato utilizzando le API REST di Batch o, facoltativamente, una funzionalità di gestione del processo dell'elemento di lavoro. Invece di dover configurare un cluster di calcolo di scrivere codice per inserire in una coda e pianificare i processi, è possibile automatizzare la pianificazione dei processi di calcolo e scalare verticalmente un pool di VM di calcolo per eseguirli. Come parte della specifica gli elementi di lavoro, è necessario definire tutte le dipendenze e definire lo spostamento dei file di input e outpui.

2. **Pubblica ed eseguire applicazioni con il servizio di Batch** - *Batch app* API funzionano a un livello superiore, consentendo di eseguire il wrapping di un'applicazione esistente in modo che venga eseguito come un servizio in un pool di macchine virtuali gestiti in background in Batch. L'applicazione potrebbe essere che viene eseguita oggi nelle workstation client o un cluster di calcolo. Il framework di Batch Apps gestisce gli spostamenti dei file di input e output, l'esecuzione dei processi, la gestione dei processi e la persistenza dei dati. Batch Apps consente inoltre di modellare le attività per la modalità di partizionamento delle attività e per le procedure in più passaggi in un processo. Sono inclusi un'API basata su REST e il portale di Batch Apps, al quale è possibile accedere dal portale di gestione di Azure e che consente di monitorare i processi inviati.


## Concetti relativi a Batch

Nelle sezioni seguenti sono riepilogati i concetti chiave per lavorare con il servizio e le API Batch. Per ulteriori informazioni, vedere [Nozioni fondamentali di API per Azure Batch](batch-api-basics.md).

* [Account di batch](#BKMK_Account)
* [Attività le macchine virtuali e i pool](#BKMK_TVM)
* [Gli elementi di lavoro, processi e attività](#BKMK_Workitem)
* [File e directory](#BKMK_Files)

### <a id="BKMK_Account">Account di batch</a>
È necessario un univoco **Batch account** per lavorare con il servizio di Batch. Tutte le richieste effettuate al servizio Batch devono essere autenticate utilizzando il nome dell'account e la relativa chiave di accesso.

È possibile creare un account di Batch e gestire chiavi di accesso per l'account nel portale di Azure o con il [i cmdlet PowerShell di Batch](batch-powershell-cmdlets-get-started.md).

Per creare un account di Batch nel portale:

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).
2. Fare clic su **nuovo**, fare clic su **calcolo**, fare clic su **servizio Batch**, quindi fare clic su **Creazione rapida**. ![Creare un account Batch][account_portal]

3. Immettere le seguenti informazioni:

	* In **nome Account**, immettere un nome univoco da utilizzare nell'URL dell'account di Batch.
	* In **area**, selezionare un'area in cui il Batch è disponibile.
	* Se si dispone di più di una sottoscrizione, in **sottoscrizione**, selezionare una sottoscrizione disponibile verrà fatturata per l'uso del Batch.


### <a id="BKMK_TVM">Attività le macchine virtuali e i pool</a>

Oggetto **macchina virtuale** (TVM) è una macchina virtuale di Azure che il servizio di Batch è dedicato all'esecuzione di una determinata attività per l'applicazione - ad esempio un file eseguibile (.exe) o nel caso di applicazioni di Batch, uno o più programmi da un'immagine di applicazione. A differenza di una macchina virtuale Azure tipica, non eseguire il provisioning o gestire una TVM direttamente. al contrario, il servizio di Batch crea e gestisce TVMs come un **pool** di macchine virtuali configurate in modo analogo, come illustrato nella figura 2.

![Pool e TVMs][TVM_pool]

**Figura 2. Un pool di TVMs**

Se si utilizzano le API di Batch, è possibile creare direttamente un pool o crearne uno automaticamente quando si specificano le operazioni da eseguire. Se si usano le API di Batch Apps, viene creato automaticamente un pool quando si esegue l'applicazione Batch abilitata per il cloud.


Gli attributi di un pool includono:

* Oggetto [dimensioni](http://msdn.microsoft.com/library/azure/dn197896.aspx) per il TVMs
* Il sistema operativo eseguito sulle TVM
* Il numero massimo di TVM
* Un criterio di ridimensionamento per il pool, cioè una formula basata sul carico di lavoro e sull'utilizzo delle risorse correnti che regola in modo dinamico il numero di TVM che elaborano attività
* L'eventuale abilitazione di porte firewall sulle TVM per consentire la comunicazione tra pool
* Un certificato installato sulle TVM, ad esempio per autenticare l'accesso a un account di archiviazione
* Un'attività iniziale per le TVM, per le operazioni da eseguire una sola volta come l'installazione di applicazioni o download dei dati usati dalle attività

>[AZURE.NOTE]Al momento le TVM possono essere eseguite solo su sistema operativo Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2 SP1.

Un pool può essere usato solo dall'account Batch in cui è stato creato. Un account Batch può avere più pool.

Ogni TVM in un pool assegnato un nome univoco e un indirizzo IP associato. Quando una TVM viene rimossa da un pool, perde le modifiche apportate al sistema operativo, i file locali, il nome e l'indirizzo IP. Quando una TVM esce da un pool, la sua durata è terminata.


### <a id="BKMK_Workitem">Gli elementi di lavoro, processi e attività</a>

Oggetto **workitem** è un modello che specifica la modalità di esecuzione di un'applicazione in TVMs in un pool. Oggetto **processo** è un elemento di lavoro pianificato e potrebbe verificarsi una volta o si ripresenta. Un processo costituito da una raccolta di **attività**. La figura 3 illustra le relazioni di base.

![Elemento di lavoro, processi e attività][job_task]

**Figura 3. Un elemento di lavoro, processi e attività**

A seconda di API utilizzate per sviluppare con Batch, sarà necessario gestire più o meno dettagli sulle attività, processi e gli elementi di lavoro.

* Se si sviluppa un'applicazione con le API di Batch, è necessario definire gli elementi di lavoro, processi e attività e configurare i pool di TVM eseguire le attività a livello di codice.

* Se si integra un'applicazione client usando le API e gli strumenti di Batch Apps, è possibile usare componenti che suddividono automaticamente un processo in attività, elaborano le attività e uniscono i risultati delle singole attività ai risultati del processo finale. Quando si invia il carico di lavoro per il servizio di Batch, il framework di applicazioni Batch gestisce i processi ed esegue le attività sul TVMs.



### <a id="BKMK_Files">File e directory</a>

Un file è costituito da dati usati come input in un'attività di processo. Un'attività può presentare uno o più file di input associati o essere priva di file di input associati. Lo stesso file può anche essere usato in più attività; ad esempio per un processo di rendering di filmati potrebbero essere comunemente usati modelli e trame. Per le attività di un processo di analisi dei dati, i file potrebbero essere costituiti da un insieme di osservazioni o misurazioni.

Esiste una directory di lavoro per ogni attività che contiene i dati di input che viene elaborato e i dati di output che viene creato. Tali directory e file sono disponibili per l'uso da parte di altre attività durante l'esecuzione di un processo. Tutte le attività e le directory nonché tutti i file di una TVM sono di proprietà di un singolo account utente.

Nuovamente, a seconda di API utilizzate con Batch, è necessario gestire più o meno dettagli sui file di input e outpui per i processi e le attività. Se si sviluppa con le API di Batch, specificare queste dipendenze e i movimenti di file in modo esplicito. Con Batch Apps il framework gestisce automaticamente la maggior parte di questi dettagli.

## Flusso di lavoro degli elementi di lavoro
Figura 4 viene illustrato un come inviare una richiesta a un pool in cui viene distribuito per l'elaborazione. Utilizza l'API di Batch.

![Flusso di lavoro degli elementi di lavoro][work_item_workflow]

**Figura 4. Flusso di lavoro per distribuire gli elementi di lavoro di macchine virtuali in pool**

1.	Caricare i file di input (ad esempio i dati o le immagini di origine) necessari per un processo in un account di archiviazione di Azure. I file devono trovarsi nell'account di archiviazione in modo che il servizio Batch possa accedervi. Il servizio Batch li carica in una TVM quando viene eseguita l'attività.
2.	Caricare i file binari dipendenti nell'account di archiviazione. I file binari includono il programma che viene eseguito dall'attività e gli assembly dipendenti. Questi file devono inoltre essere accessibili dalla risorsa di archiviazione ed essere caricati nella TVM.
3.	Creare un pool di TVM, specificando la dimensione delle TVM nel pool, il sistema operativo eseguito e altre proprietà. Quando viene eseguita un'attività, le viene assegnata una TVM da questo pool.
4.	Creare un elemento di lavoro. Verrà creato automaticamente un processo quando si crea un elemento di lavoro. Un elemento di lavoro consente di gestire un processo di attività.
5.	Aggiungere attività al processo. Ciascuna attività usa il programma caricato per elaborare le informazioni presenti in un file caricato.
6.	Eseguire l'applicazione e monitorare i risultati dell'output.

## Flusso di lavoro per la pubblicazione e l'esecuzione di applicazioni
La Figura 5 illustra un carico di lavoro di base per pubblicare un'applicazione usando l'API di Batch Apps e quindi inviare i processi all'applicazione abilitata da Batch.

![Flusso di lavoro pubblicazione di applicazioni][app_pub_workflow]

**Figura 5. Flusso di lavoro da pubblicare ed eseguire un'applicazione con le applicazioni di Batch**

1.	Preparare un **immagine dell'applicazione** -un file zip contenente i file eseguibili dell'applicazione esistente e i file di supporto necessari. Potrebbe trattarsi degli stessi eseguibili che vengono eseguiti in una server farm o in un cluster tradizionale.
2.	Creare un file zip contenente il **cloud assembly** che richiamare e inviare i carichi di lavoro per il servizio di Batch. Contiene due componenti che sono disponibili tramite l'SDK:

	a. **Splitter processo** – suddivide un processo in attività che possono essere elaborate in modo indipendente. Ad esempio, in uno scenario di animazione un componente di suddivisione dei processi divide il processo di rendering dei filmati in singoli fotogrammi.

	b. **Processore task** – richiama l'applicazione eseguibile per una determinata attività. Ad esempio, in uno scenario di animazione, l'elaboratore delle attività invoca un programma di rendering per eseguire il rendering del singolo frame specificato dall'attività.

3.	Usare le API o gli strumenti per gli sviluppatori di Batch Apps per caricare i file zip preparati nei due passaggi precedenti in un account di archiviazione di Azure. I file devono trovarsi nell'account di archiviazione in modo che il servizio Batch possa accedervi. Questa operazione viene in genere eseguita una volta per ogni applicazione da un amministratore del servizio.
4.	Specificare una modalità di invio dei processi al servizio dell'applicazione abilitato in Azure. Potrebbe trattarsi di un plug-in nell'interfaccia utente dell'applicazione, di un portale Web o di un servizio automatico nell'ambito del sistema di back-end. Sono disponibili esempi con l'SDK che dimostrano le varie opzioni.

	Per eseguire un processo:

	a. Caricare i file di input, ad esempio dati o immagini di origine, specifici per il processo dell'utente. I file devono trovarsi nell'account di archiviazione in modo che il servizio Batch possa accedervi.

	b. Inviare un processo con i parametri richiesti e l'elenco dei file.

	c. Monitorare l'avanzamento dei processi usando le API o il portale di Batch Apps.

	d. Scaricare gli output.

## Risorse aggiuntive

* [Iniziare con la libreria di Batch di Azure per .NET](batch-dotnet-get-started.md)
* [Strumenti e librerie di sviluppo di azure Batch](batch-development-libraries-tools.md)
* [Riferimento all'API REST di Azure Batch](http://go.microsoft.com/fwlink/p/?LinkId=517803)
* [Riferimento all'API REST di applicazioni Azure Batch](http://go.microsoft.com/fwlink/p/?LinkId=517804)

[parallel]: ./media/batch-technical-overview/parallel.png
[TVM_pool]: ./media/batch-technical-overview/TVM_pool.png
[job_task]: ./media/batch-technical-overview/job_task.png
[account_portal]: ./media/batch-technical-overview/account_portal.png
[work_item_workflow]: ./media/batch-technical-overview/work_item_workflow.png
[app_pub_workflow]: ./media/batch-technical-overview/app_pub_workflow.png

<!---HONumber=GIT-SubDir-->