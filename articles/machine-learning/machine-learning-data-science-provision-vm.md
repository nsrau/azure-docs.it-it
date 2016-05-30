<properties 
	pageTitle="Eseguire il provisioning di una macchina virtuale per l'analisi scientifica dei dati di Microsoft | Microsoft Azure" 
	description="Configurare e creare una macchina virtuale per l'analisi scientifica dei dati in Azure per l'analisi dei dati e l'apprendimento automatico." 
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
	ms.date="05/10/2016" 
	ms.author="bradsev" />


# Eseguire il provisioning di una macchina virtuale per l'analisi scientifica dei dati di Microsoft

La macchina virtuale per l'analisi scientifica dei dati di Microsoft è un'immagine di macchina virtuale (VM) di Azure pre-installata e configurata con diversi strumenti diffusi comunemente usati per l'analisi dei dati e l'apprendimento automatico. Gli strumenti inclusi sono:

- Microsoft R Server Developer Edition
- Distribuzione Anaconda Python
- Visual Studio Community Edition
- Power BI Desktop
- SQL Server Express Edition
- Azure SDK


L'esecuzione di analisi scientifica dei dati implica l'iterazione di una sequenza di attività: ricerca, caricamento e pre-elaborazione dei dati, generazione e test di modelli e distribuzione dei modelli per l'utilizzo in applicazioni intelligenti. Non è raro che i data scientist usino un'ampia gamma di strumenti per completare queste attività. Trovare le versioni software appropriate e quindi scaricarle e installarle può essere un'operazione molto dispersiva in termini di tempo. La macchina virtuale per l'analisi scientifica dei dati di Microsoft può rendere queste attività molto più facili.

La macchina virtuale per l'analisi scientifica dei dati di Microsoft avvia il progetto di analisi. Consente di svolgere attività in vari tipi di linguaggio, tra cui R, Python, SQL e C#. Visual Studio fornisce un IDE facile da usare per sviluppare e testare il codice. Azure SDK incluso nella VM consente di compilare le applicazioni usando vari servizi sulla piattaforma cloud di Microsoft.

Per questa immagine di macchina virtuale per l'analisi scientifica dei dati non sono previsti addebiti software. Si pagano solo le spese di utilizzo di Azure in base alla dimensione della macchina virtuale di cui verrà eseguito il provisioning con questa immagine di macchina virtuale. Altre informazioni sui costi di calcolo sono disponibili [qui](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/).


## Prerequisiti

Prima di creare una macchina virtuale per l'analisi scientifica dei dati di Microsoft, è necessario quanto segue:

- **Sottoscrizione di Azure**: per ottenerne una, vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

*   **Account di archiviazione di Azure**: per crearne uno, vedere [Creare un account di archiviazione di Azure](storage-create-storage-account.md#create-a-storage-account). In alternativa, è possibile creare l'account di archiviazione nell'ambito del processo di creazione della macchina virtuale, se si preferisce non usare un account esistente.


## Creare la macchina virtuale per l'analisi scientifica dei dati di Microsoft

Di seguito sono riportati i passaggi necessari per creare un'istanza della macchina virtuale per l'analisi scientifica dei dati di Microsoft:

1.	Passare all'elenco delle macchine virtuali nel [portale di Azure](https://ms.portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm).
2.	 Fare clic sul pulsante **Crea** in basso per visualizzare una procedura guidata. ![configure-data-science-vm](./media/machine-learning-data-science-provision-vm/configure-data-science-virtual-machine.png)
3.	 Le sezioni seguenti forniscono gli **input** per ognuno dei **5 passaggi** (elencati in ordine numerico nella parte destra della figura precedente) della procedura guidata usata per creare la macchina virtuale per l'analisi scientifica dei dati di Microsoft. Di seguito sono riportati gli input necessari per configurare ciascuno di questi passaggi:

  **a. Informazioni di base**:

   - **Name**: nome del server di analisi scientifica dei dati che si sta creando.
   - **User name**: ID di accesso dell'account amministratore.
   - **Password**: password dell'account amministratore.
   - **Subscription**: se si ha più di una sottoscrizione, selezionare quella in cui la macchina virtuale verrà creata e fatturata.
   - **Resource Group**: è possibile creare un nuovo gruppo di risorse o usarne uno esistente.
   - **Location**: selezionare la posizione del data center più appropriata. In genere è il data center che include la maggior parte dei dati o è più vicino alla località fisica per l'accesso più veloce alla rete.

  **b. Dimensione**:

   - Selezionare uno dei tipi di server che soddisfa i requisiti funzionali e vincoli di costo. È possibile ottenere altre opzioni per le dimensioni delle VM selezionando "View All"

  **c. Impostazioni**

   - **Tipo di disco**: scegliere Premium se si preferisce un'unità a stato solido (SSD); in caso contrario scegliere "Standard".
   - **Account di archiviazione**: è possibile creare un nuovo account di archiviazione di Azure nella sottoscrizione o usarne uno esistente nella stessa *posizione* scelta nel passaggio Basic della procedura guidata.
   - **Altri parametri**: nella maggior parte dei casi si userà semplicemente i valori predefiniti. Nel caso in cui si desideri usare valori non predefiniti, è possibile passare il puntatore sul collegamento informativo per visualizzare informazioni su campi specifici.

  **d. Riepilogo**:

   - Verificare che tutte le informazioni immesse siano corrette.

  **e. Acquisto**:

   - Fare clic su **Acquista** per avviare il provisioning. Viene fornito un collegamento alle condizioni della transazione. La macchina virtuale non prevede costi aggiuntivi oltre a quelli per il calcolo delle dimensioni del server scelto nel passaggio **Size**. 


Per il provisioning sono necessari circa 10-20 minuti. Lo stato del provisioning viene visualizzato nel portale di Azure.

## Come accedere a una macchina virtuale per l'analisi scientifica dei dati di Microsoft

Dopo aver creato la VM è possibile eseguire l'accesso tramite desktop remoto con le credenziali dell'account amministratore creato nella sezione Basics del passaggio 4.

Una volta creata la VM ed effettuato il provisioning, si è pronti per iniziare a usare gli strumenti installati e configurati nella VM. Sono disponibili riquadri del menu di avvio e icone del desktop per molti strumenti.

## Come creare una password complessa nel server notebook di Jupyter 

Eseguire questo comando dal prompt dei comandi nella macchina virtuale per l'analisi scientifica dei dati per creare la password complessa per il server notebook di Jupyter installato nel computer.

	c:\anaconda\python.exe -c "import IPython;print IPython.lib.passwd()"

Scegliere una password complessa quando viene richiesto.

Verrà visualizzato l'hash della password nel formato "sha1:xxxxxx" nell'output. Copiare questo hash della password e sostituire l'hash esistente nel file di configurazione del notebook disponibile nel percorso **C:\\ProgramData\\jupyter\\jupyter\_notebook\_config.py** con un nome di parametro ***c.NotebookApp.password***.

È necessario sostituire solo il valore dell’hash esistente all'interno delle virgolette. Le virgolette e il prefisso ***sha1:*** per il valore del parametro devono essere conservati.

Infine, è necessario arrestare e riavviare il server di Ipython che è in esecuzione nella macchina virtuale come un'attività pianificata di windows denominata "Start\_IPython\_Notebook". Se la nuova password non viene accettata dopo il riavvio di questa attività, provare a riavviare la macchina virtuale.

## Strumenti installati nella macchina virtuale per l'analisi scientifica dei dati di Microsoft

### Microsoft R Server Developer Edition
Per usare R per l'analisi, è necessario che nella macchina virtuale sia installato Microsoft R Server Developer Edition. Microsoft R Server è una piattaforma di analisi di livello aziendale su vasta scala supportata, scalabile, sicura e basata su R. Grazie al supporto per un'ampia gamma di statistiche di Big Data, la modellazione predittiva e le funzionalità di apprendimento automatico, R Server supporta la gamma completa di funzionalità di analisi: esplorazione, analisi, visualizzazione e modellazione. Usando ed estendendo il linguaggio R open source, Microsoft R Server è completamente compatibile con gli script e le funzioni R, nonché i pacchetti CRAN, per analizzare i dati a livello aziendale. Risolve inoltre le limitazioni in memoria di R open source mediante l'aggiunta dell'elaborazione parallela e a blocchi dei dati in Microsoft R Server, consentendo agli utenti di eseguire l'analisi su dati di dimensioni molto più elevate rispetto alla memoria principale. Nella macchina virtuale è inoltre incluso un IDE per R a cui è possibile accedere facendo clic sull'icona "Revolution R Enterprise 8.0" nel menu di avvio o sul desktop. È anche possibile scaricare e usare altri IDE, ad esempio [RStudio](http://www.rstudio.com).

### Python
Per lo sviluppo tramite Python, è installata la distribuzione Anaconda Python 2.7 e 3.5. Questa distribuzione contiene il linguaggio Python di base con circa 300 dei più diffusi pacchetti di matematica, ingegneria e analisi dei dati. È possibile usare Python Tools per Visual Studio (PTVS), installato con Visual Studio 2015 Community Edition, o uno degli ambienti IDE inclusi in Anaconda, ad esempio IDLE o Spyder. È possibile avviare uno di questi IDE eseguendo una ricerca sulla barra di ricerca (**Win**+**S**). **Nota**: per puntare Strumenti Python per Visual Studio su Anaconda Python 2.7 e 3.5, è necessario creare ambienti personalizzati per ogni versione passando a Strumenti -> Strumenti Python ->Ambienti Python, fare clic su "+ Personalizza" in Visual Studio 2015 Community Edition e impostare i percorsi di ambiente. Anaconda Python 2.7 viene installato in C:\\Anaconda e Anaconda Python 3.5 viene installato in C:\\Anaconda\\envs\\py35. Per la procedura dettagliata, vedere la [documentazione di PTVS](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it).

### Notebook Jupyter
La distribuzione Anaconda include anche il notebook di Jupyter, un ambiente per condividere il codice e le analisi. Un server notebook di Jupyter è stato preconfigurato con Python 2, Python 3 e i kernel R. Sul desktop è disponibile un'icona denominata Notebook di Jupyter per avviare il browser per accedere al server notebook. Se si usa la VM da desktop remoto, è anche possibile visitare il sito [https://localhost:9999/](https://localhost:9999/) per accedere al server notebook di Jupyter. Nota: se vengono visualizzati avvisi relativi al certificato, continuare. Nel pacchetto sono inclusi alcuni notebook di esempio: uno in Python e l'altro in R. È possibile visualizzare il collegamento agli esempi nella home page del notebook dopo aver eseguito l'autenticazione al notebook di Jupyter mediante la password creata nel passaggio precedente.

### Visual Studio 2015 Community Edition
Visual Studio Community Edition è installato nella VM. È una versione gratuita di IDE più diffuso di Microsoft che è possibile usare per scopi di valutazione e per team molto piccoli. È possibile consultare le condizioni di licenza [qui](https://www.visualstudio.com/support/legal/mt171547). Aprire Visual Studio facendo doppio clic sull'icona del desktop o sul menu **Start**. È anche possibile cercare i programmi premendo i tasti **Win** + **S** e immettendo "Visual Studio". È quindi possibile creare progetti in linguaggi come C# e Python. Sono inoltre disponibili plug-in installati che semplificano l'utilizzo dei servizi Azure come Catalogo dati di Azure, Azure HDInsight (Hadoop, Spark) e Azure Data Lake.

Nota: è possibile che venga visualizzato un messaggio indicante che il periodo di valutazione è scaduto. È possibile immettere le credenziali di un account Microsoft o crearne uno e immetterlo per accedere a Visual Studio Community Edition.

### SQL Server Express
Con Visual Studio Community Edition viene fornita anche una versione limitata di SQL Server. È possibile accedere a SQL Server avviando **SQL Server Management Studio**. Il nome della VM verrà popolato come Nome server. Usare l'autenticazione di Windows durante l'accesso come amministratore a Windows. Una volta eseguito l'accesso a SQL Server Management Studio è possibile creare altri utenti, creare database, importare i dati ed eseguire query SQL.

### Azure 
Diversi strumenti di Azure vengono installati nella macchina virtuale:
- Sul desktop c'è un collegamento per accedere alla documentazione di Azure SDK. 
- **AzCopy** sposta i dati da e verso l'account di archiviazione di Microsoft Azure. 
- **Esplora archivi di Azure** esplora gli oggetti archiviati nell'account di archiviazione di Azure. 
- Anche **Microsoft Azure Powershell**, uno strumento che consente di amministrare le risorse di Azure nel linguaggio di scripting Powershell, viene installato nella VM. 

###Power BI

È stato installato **Power BI Desktop** per facilitare la creazione di dashboard e interessanti visualizzazioni. Usare questo strumento per estrarre dati da origini diverse, per creare dashboard e report e pubblicarli nel cloud. Per informazioni, visitare il sito di [Power BI](http://powerbi.microsoft.com).

Nota: è necessario un account Office 365 per accedere a Power BI.

## Altri strumenti di sviluppo Microsoft
È possibile usare [**Microsoft Web Platform Installare**](https://www.microsoft.com/web/downloads/platform.aspx) per trovare e scaricare altri strumenti di sviluppo Microsoft. È disponibile anche un collegamento allo strumento sul desktop della macchina virtuale per l'analisi scientifica dei dati di Microsoft.

## Passaggi successivi
Ecco alcuni passaggi successivi per continuare l'apprendimento e l'esplorazione.

* Esaminare i vari strumenti di analisi scientifica dei dati nella macchina virtuale per l'analisi scientifica dei dati facendo clic sul pulsante di avvio ed estraendo gli strumenti elencati nel menu
* Passare a **C:\\Programmi\\Microsoft\\MRO-for-RRE\\8.0\\R-3.2.2\\library\\RevoScaleR\\demoScripts** per esempi d'uso della libreria RevoScaleR in R che supporta l'analisi dei dati a livello aziendale.  
* Leggere l'articolo [Dieci cose da fare con la macchina virtuale per l'analisi scientifica dei dati](http://aka.ms/dsvmtenthings)
* Informazioni su come creare sistematicamente soluzioni analitiche end-to-end usando l'[analisi scientifica dei dati](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).
* Visitare [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com) per esempi di Machine Learning e di analisi dei dati usando Cortana Intelligence Suite. Viene inoltre fornita un'icona sul desktop e nel menu di avvio della macchina virtuale per semplificare l'accesso 

<!---HONumber=AcomDC_0518_2016-->