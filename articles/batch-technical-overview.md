<properties linkid="batch-technical-overview" urlDisplayName="" pageTitle="Panoramica tecnica di Azure Batch" metaKeywords="" description="Informazioni sui concetti, i flussi di lavoro e gli scenari del servizio Azure Batch." metaCanonical="" services="batch" documentationCenter="" title="Azure Batch technical overview" authors="danlep" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="batch" ms.workload="big-compute" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/08/2014" ms.author="danlep" />


#Panoramica tecnica di Azure Batch
Azure Batch semplifica l'esecuzione su larga scala di applicazioni di elaborazione ad alte prestazioni (HPC) e paralleli nel cloud fornendo una pianificazione dei processi con scalabilità automatica delle risorse di calcolo come servizio di piattaforma. Con il servizio Batch e Batch SDK è possibile configurare carichi di lavoro batch da eseguire su richiesta o in base a una pianificazione su una raccolta gestita di macchine virtuali e non doversi preoccupare della complessità della pianificazione dei processi e della gestione delle risorse nella piattaforma sottostante.
 
>[WACOM.NOTE]Batch è disponibile in anteprima. Per usare Batch è necessario un account Azure e occorre abilitare l'anteprima di Batch nella sottoscrizione. Se non si ha un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Creare un account Azure](http://www.windowsazure.com/it-it/develop/php/tutorials/create-a-windows-azure-account/). 


In questo articolo viene fornita una panoramica di:
 
* [Casi di utilizzo](#BKMK_Scenarios)
* [Scenari di sviluppatori](#BKMK_Approaches)
* [Concetti relativi a Batch](#BKMK_Entities)
* [Flusso di lavoro degli elementi di lavoro](#BKMK_Workflow_workitems)
* [Flusso di lavoro per la pubblicazione e l'esecuzione di applicazioni](#BKMK_Workflow_cloudappss)
* [Risorse aggiuntive](#BKMK_Resources)

<h2 id="BKMK_Scenarios">Casi di utilizzo</h2>

Batch usa l'elasticità e la scalabilità del cloud per semplificare l'*elaborazione batch* o il *calcolo batch* eseguendo a livello di codice volumi elevati di attività simili. Un programma della riga di comando o uno script usa un set di file di dati come input, li elabora in una serie di attività e produce un set di file di output. I file di output potrebbero essere i risultati finali o costituire un passaggio intermedio in un flusso di lavoro di maggiori dimensioni.       
 
Il calcolo batch è un modello comune per le organizzazioni che elaborano, trasformano e analizzano elevate quantità di dati, sia in base a una pianificazione che su richiesta. Include l'elaborazione di fine ciclo, quali report giornalieri sui rischi di una banca oppure l'esecuzione o la pianificazione della corresponsione delle retribuzioni. Include inoltre applicazioni aziendali, scientifiche e tecniche su vasta scala che in genere necessitano degli strumenti e delle risorse di un cluster o grid di calcolo. Sono incluse le applicazioni HPC tradizionali quali le simulazioni della dinamica dei fluidi, nonché carichi di lavoro specializzati in settori quali la progettazione automobilistica, la prospezione di gas e petrolio, la ricerca nel campo delle scienze biologiche e la creazione di contenuti digitali. 
 
Batch funziona bene con applicazioni o carichi di lavoro intrinsecamente paralleli (a volte definiti "imbarazzantemente paralleli"), che si prestano a essere eseguiti come attività parallele su più computer, ad esempio le VM di calcolo gestite dal servizio Batch. Vedere la Figura 1.  

![Parallel tasks][parallel]

**Figura 1. Attività parallele in esecuzione su più computer**

Tra gli esempi sono inclusi:

* Modellazione dei rischi finanziari 
* Rendering ed elaborazione di immagini
* Codifica e transcodifica multimediale
* Analisi delle sequenze genetiche
* Test di software

È inoltre possibile usare Batch per eseguire calcoli paralleli con un passaggio di riduzione alla fine e altri carichi di lavoro paralleli non aciclici meno complicati. 

>[WACOM.NOTE]L'anteprima di Batch attualmente non supporta le applicazioni MPI (Message Passing Interface).

<h2 id="BKMK_Approaches">Scenari di sviluppo</h2>

Le API di Batch basate su RES supportano due scenari di sviluppatori per semplificare la configurazione e l'esecuzione dei carichi di lavoro batch con il servizio Batch:
 
1. **Distribuire calcoli come elementi di lavoro**: usare le API di *Batch* per creare e gestire un pool flessibile di VM di calcolo e specificare gli elementi di lavoro che vengono eseguiti in esse. Ciò offre il controllo completo sulle risorse e richiede la gestione della pipeline di esecuzione delle attività da parte del cliente, ad esempio con la gestione dei flussi di lavoro o un'utilità di meta-pianificazione, che è possibile implementare usando le API REST di Batch oppure, facoltativamente, una funzionalità di gestione dei processi dell'elemento di lavoro. Invece di dover configurare un cluster di calcolo di scrivere codice per inserire in una coda e pianificare i processi, è possibile automatizzare la pianificazione dei processi di calcolo e scalare verticalmente un pool di VM di calcolo per eseguirli. Quando si specificano gli elementi di lavoro, si definiscono tutte le dipendenze e lo spostamento dei file di input e output. 

2. **Pubblicare ed eseguire le applicazioni con il servizio Batch**: le API di *Batch Apps* forniscono una pipeline con un livello più elevato di astrazione ed esecuzione dei processi ospitata dal servizio Batch. Con Batch Apps è possibile creare un carico di lavoro batch come servizio nel cloud da un'applicazione eseguita su workstation client o su un cluster di calcolo. Batch Apps consente di eseguire il wrapping dei file binari ed eseguibili esistenti e di pubblicarli per eseguirli in VM in pool che il servizio Batch crea e gestisce in background. Il framework di Batch Apps gestisce gli spostamenti dei file di input e output, l'esecuzione dei processi, la gestione dei processi e la persistenza dei dati. Batch Apps consente inoltre di modellare le attività per la modalità di partizionamento delle attività e per le procedure in più passaggi in un processo. Sono inclusi un'API basata su REST e il portale di Batch Apps, al quale è possibile accedere dal portale di gestione di Azure e che consente di monitorare i processi inviati.


<h2 id="BKMK_Entities">Concetti relativi a Batch</h2>

Nelle sezioni seguenti sono riepilogati i concetti chiave per lavorare con il servizio e le API Batch. Per altre informazioni, vedere la pagina relativa alle [nozioni di base delle API per Azure Batch](http://azure.microsoft.com/it-it/documentation/articles/batch-api-basics). 

* [Account Batch](#BKMK_Account)
* [Macchine virtuali e pool delle attività](#BKMK_TVM)
* [Elementi di lavoro, processi e attività](#BKMK_Workitem)
* [File e directory](#BKMK_Files)

<h3 id="BKMK_Account">Account Batch</h3>
Per usare il servizio Batch è necessario un **Account Batch** univoco. Quando si eseguono operazioni API con il servizio Batch, le richieste vengono autenticate con l'URL dell'account e con la chiave di accesso associata. È inoltre necessario un account Batch per accedere al portale di Batch Apps.
 
È possibile creare un account Batch e gestire le chiavi di accesso per l'account nel portale di gestione di Azure.

Per creare un account Batch:

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).
2. Fare clic su **Nuovo**, **Calcolo**, **Servizio Batch**, quindi su **Creazione rapida**.
![Create a Batch account][account_portal]

3. Immettere le seguenti informazioni:

	* In **Nome account** immettere un nome univoco da usare nell'URL dell'account Batch.
	* In **Area** selezionare un'area in cui Batch è disponibile.
	* Se sono presenti più sottoscrizioni, in **Sottoscrizione** selezionare una sottoscrizione disponibile che verrà fatturata per l'uso di Batch.

 
<h3 id="BKMK_TVM">Macchine virtuali e pool delle attività</h3>

Una **macchina virtuale delle attività** (TVM) è una VM di Azure che il servizio Batch dedica all'esecuzione di un carico di lavoro specifico (attività) per l'applicazione, ad esempio un file eseguibile (.exe) o, nel caso di Batch Apps, uno o più programmi da un'immagine di applicazione. A differenza di una VM di Azure tipica, non si effettua direttamente il provisioning o la gestione di una TVM, ma è il servizio Batch che crea e gestisce le TVM come **pool** di risorse di calcolo configurate in modo simile, come mostrato nella figura 2. 

![Pool and TVMs][TVM_pool]

**Figura 2. Pool di TMV**

Se si usano le API di Batch è possibile creare un pool direttamente o configurare il servizio Batch in modo da crearne uno automaticamente quando si specifica l'attività da eseguire. Se si usano le API di Batch Apps, viene creato automaticamente un pool quando si esegue l'applicazione Batch abilitata per il cloud.


Gli attributi di un pool includono:

* Una [dimensione](http://msdn.microsoft.com/library/azure/dn197896.aspx) per le TVM 
* Il sistema operativo eseguito sulle TVM
* Il numero massimo di TVM
* Un criterio di ridimensionamento per il pool, cioè una formula basata sul carico di lavoro e sull'utilizzo delle risorse correnti che regola in modo dinamico il numero di TVM che elaborano attività
* L'eventuale abilitazione di porte firewall sulle TVM per consentire la comunicazione tra pool
* Un certificato installato sulle TVM, ad esempio per autenticare l'accesso a un account di archiviazione
* Un'attività iniziale per le TVM, per le operazioni da eseguire una sola volta come l'installazione di applicazioni o download dei dati usati dalle attività

>[WACOM.NOTE]Al momento le TVM possono essere eseguite solo su sistema operativo Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2 SP1.

Un pool può essere usato solo dall'account Batch in cui è stato creato. Un account Batch può avere più pool.

A ogni TVM aggiunta a un pool viene assegnato un nome univoco e un indirizzo IP associato. Quando una TVM viene rimossa da un pool, perde le modifiche apportate al sistema operativo, i file locali, il nome e l'indirizzo IP. Quando una TVM esce da un pool, la sua durata è terminata.


<h3 id="BKMK_Workitem">Elementi di lavoro, processi e attività</h3>

Un **elemento di lavoro** è un modello che specifica la modalità di esecuzione di un'applicazione nelle TVM di un pool. Un **processo** è un'istanza pianificata di un elemento di lavoro e potrebbe verificarsi una sola volta o ripetersi. Un processo è costituito da una raccolta di **attività**. La figura 3 illustra le relazioni di base.    
 
![Work item, job, and tasks][job_task]

**Figura 3. Elemento di lavoro, processo e attività**

A seconda delle API usate per sviluppare con Batch, sarà necessario gestire un numero variabile di dettagli sugli elementi di lavoro, sui processi e sulle attività.

* Se si sviluppa un'applicazione con le API Batch di livello inferiore, è necessario definire a livello di codice tutti gli elementi di lavoro, i processi e le attività eseguite dal servizio Batch e configurare i pool delle TVM che eseguono le attività.

* Se si integra un'applicazione client usando le API e gli strumenti di Batch Apps, è possibile usare componenti che suddividono automaticamente un processo in attività, elaborano le attività e uniscono i risultati delle singole attività ai risultati del processo finale. Quando si invia il carico di lavoro al servizio Batch, il framework di Batch Apps gestisce i processi ed esegue le attività nelle risorse di calcolo sottostanti. 



<h3 id="BKMK_Files">File e directory</h3>

Un file è costituito da dati usati come input in un'attività di processo. Un'attività può presentare uno o più file di input associati o essere priva di file di input associati. Lo stesso file può anche essere usato in più attività; ad esempio per un processo di rendering di filmati potrebbero essere comunemente usati modelli e trame. Per le attività di un processo di analisi dei dati, i file potrebbero essere costituiti da un insieme di osservazioni o misurazioni.

Ogni attività presenta una directory di lavoro sotto la quale crea le directory e i file necessari per archiviare il programma eseguito da un'attività, i dati di input elaborati da un'attività e l'output dell'elaborazione eseguita da un'attività. Tali directory e file sono disponibili per l'uso da parte di altre attività durante l'esecuzione di un processo. Tutte le attività e le directory nonché tutti i file di una TVM sono di proprietà di un singolo account utente.

Ancora una volta, a seconda delle API usate con Batch, sarà necessario gestire un numero variabile di dettagli sulle posizioni e lo spostamento dei file di input e output dei processi e delle attività. Se si sviluppa con le API Batch di livello inferiore, è necessario specificare queste dipendenze e gli spostamenti dei file in modo esplicito. Con Batch Apps il framework gestisce automaticamente la maggior parte di questi dettagli. 

<h2 id="BKMK_Workflow_workitems">Flusso di lavoro degli elementi di lavoro</h2>
La figura 4 illustra un tipico flusso di lavoro per distribuire calcoli a un pool di TVM usando l'API di Batch.

![Work items workflow][work_item_workflow]

**Figura 4. Flusso di lavoro per distribuire elementi di lavoro in VM in pool**

1.	Caricare i file di input (ad esempio i dati o le immagini di origine) necessari per un processo in un account di archiviazione di Azure. I file devono trovarsi nell'account di archiviazione in modo che il servizio Batch possa accedervi. Il servizio Batch li carica in una TVM quando viene eseguita l'attività.
2.	Caricare i file binari dipendenti nell'account di archiviazione. I file binari includono il programma che viene eseguito dall'attività e gli assembly dipendenti. Questi file devono inoltre essere accessibili dalla risorsa di archiviazione ed essere caricati nella TVM.
3.	Creare un pool di TVM, specificando la dimensione delle TVM nel pool, il sistema operativo eseguito e altre proprietà. Quando viene eseguita un'attività, le viene assegnata una TVM da questo pool.
4.	Creare un elemento di lavoro. Un processo verrà automaticamente creato quando si crea un elemento di lavoro. Un elemento di lavoro consente di gestire un processo di attività.
5.	Aggiungere attività al processo. Ciascuna attività usa il programma caricato per elaborare le informazioni presenti in un file caricato.
6.	Eseguire l'applicazione e monitorare i risultati dell'output.

<h2 id="BKMK_Workflow_cloudapps">Flusso di lavoro per la pubblicazione e l'esecuzione di applicazioni </h2>
La Figura 5 illustra un carico di lavoro di base per pubblicare un'applicazione usando l'API di Batch Apps e quindi inviare i processi all'applicazione abilitata da Batch.

![Application publishing workflow][app_pub_workflow]

**Figura 5. Flusso di lavoro per la pubblicazione e l'esecuzione di un'applicazione con Batch Apps**

1.	Preparare un'**immagine dell'applicazione**: un file zip degli eseguibili dell'applicazione esistenti e degli eventuali file di supporto necessari. Potrebbe trattarsi degli stessi eseguibili che vengono eseguiti in una server farm o in un cluster tradizionale.
2.	Creare un file zip dell'**assembly cloud** che richiamerà e invierà i carichi di lavoro al servizio Batch. Contiene due componenti che sono disponibili tramite l'SDK:

	a. **Componente di suddivisione dei processi**: suddivide un processo in attività che è possibile elaborare in modo indipendente. Ad esempio, in uno scenario di animazione un componente di suddivisione dei processi divide il processo di rendering dei filmati in singoli fotogrammi.

	b. **Elaboratore delle attività**: invoca l'applicazione eseguibile per una determinata attività. Ad esempio, in uno scenario di animazione, l'elaboratore delle attività invoca un programma di rendering per eseguire il rendering del singolo frame specificato dall'attività. 

3.	Usare le API o gli strumenti per gli sviluppatori di Batch Apps per caricare i file zip preparati nei due passaggi precedenti in un account di archiviazione di Azure. I file devono trovarsi nell'account di archiviazione in modo che il servizio Batch possa accedervi. Questa operazione viene in genere eseguita una volta per ogni applicazione da un amministratore del servizio.
4.	Specificare una modalità di invio dei processi al servizio dell'applicazione abilitato in Azure. Potrebbe trattarsi di un plug-in nell'interfaccia utente dell'applicazione, di un portale Web o di un servizio automatico nell'ambito del sistema di back-end. Sono disponibili esempi con l'SDK che dimostrano le varie opzioni. 

	Per eseguire un processo:

	a. Caricare i file di input, ad esempio dati o immagini di origine, specifici per il processo dell'utente. I file devono trovarsi nell'account di archiviazione in modo che il servizio Batch possa accedervi.

	b. Inviare un processo con i parametri richiesti e l'elenco dei file.
	
	c. Monitorare l'avanzamento dei processi usando le API o il portale di Batch Apps.
	
	d. Scaricare gli output.
	
<h2 id="BKMK_Resources">Risorse aggiuntive</h2>

* [Introduzione alla libreria di Azure Batch per .NET](http://azure.microsoft.com/it-it/documentation/articles/batch-dotnet-get-started/)
* [Strumenti e librerie di sviluppo di Azure Batch](http://azure.microsoft.com/it-it/documentation/articles/batch-development-libraries-tools/)
* [Informazioni di riferimento sulle API REST di Azure Batch](http://go.microsoft.com/fwlink/p/?LinkId=517803)
* [Informazioni di riferimento sulle API REST di Azure Batch Apps](http://go.microsoft.com/fwlink/p/?LinkId=517804)

[parallel]: ./media/batch-technical-overview/parallel.png
[TVM_pool]: ./media/batch-technical-overview/TVM_pool.png
[job_task]: ./media/batch-technical-overview/job_task.png
[account_portal]: ./media/batch-technical-overview/account_portal.png
[work_item_workflow]: ./media/batch-technical-overview/work_item_workflow.png
[app_pub_workflow]: ./media/batch-technical-overview/app_pub_workflow.png

<!--HONumber=35.2-->
