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
	ms.date="12/17/2015" 
	ms.author="bradsev" />


# Eseguire il provisioning di una macchina virtuale per l'analisi scientifica dei dati di Microsoft

## Introduzione

La macchina virtuale per l'analisi scientifica dei dati di Microsoft è un'immagine di macchina virtuale (VM) di Azure pre-installata e configurata con diversi strumenti diffusi comunemente usati per l'analisi dei dati e l'apprendimento automatico. Gli strumenti inclusi sono:

- Revolution R Open
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

- **Sottoscrizione di Azure**: per ottenerne una, vedere [Ottenere una versione di valutazione gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

*   **Account di archiviazione di Azure**: per crearne uno, vedere [Creare un account di archiviazione di Azure](storage-create-storage-account.md#create-a-storage-account). In alternativa, è possibile creare l'account di archiviazione nell'ambito del processo di creazione della macchina virtuale, se si preferisce non usare un account esistente.


## Creare la macchina virtuale per l'analisi scientifica dei dati di Microsoft

Di seguito sono riportati i passaggi necessari per creare un'istanza della macchina virtuale per l'analisi scientifica dei dati di Microsoft:

1.	Passare alla macchina virtuale nel [portale di Azure classico](https://portal.azure.com/#gallery/microsoft-ads.standard-data-science-vmstandard-data-science-vm).
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

   - Fare clic su **Acquista** per avviare il provisioning. Viene fornito un collegamento alle condizioni della transazione. La VM non prevede costi aggiuntivi oltre a quelli per il calcolo delle dimensioni del server scelto nel passaggio **Size**. 


Per il provisioning sono necessari circa 10-20 minuti. Lo stato del provisioning viene visualizzato nel portale di Azure classico.

## Come accedere a una macchina virtuale per l'analisi scientifica dei dati di Microsoft

Dopo aver creato la VM è possibile eseguire l'accesso tramite desktop remoto con le credenziali dell'account amministratore creato nella sezione Basics del passaggio 4.

Una volta creata la VM ed effettuato il provisioning, si è pronti per iniziare a usare gli strumenti installati e configurati nella VM. Sono disponibili riquadri con le icone del desktop e i menu di avvio per molti strumenti.

## Come creare una password complessa per la macchina virtuale di Data Science

Eseguire il seguente comando dal prompt dei comandi nella macchina virtuale di Data Science per creare la password complessa per la macchina.

	c:\anaconda\python.exe -c "import IPython;print IPython.lib.passwd()"

Immettere la password quando viene richiesto.

Verrà visualizzato l'hash della password nel formato "sha1:xxxxxx" nell'output. Copiare questo hash della password e sostituire l’hash esistente nel file di configurazione del notebook disponibile al percorso: **C:\\Aaqs.ipython\\profile\_nbserver\\ipython\_notebook\_config.py** con un nome di parametro ***c.NotebookApp.password***.

È necessario sostituire solo il valore dell’hash esistente all'interno delle virgolette. Le virgolette e il prefisso ***sha1:*** per il valore del parametro devono essere conservati.

Infine, è necessario arrestare e riavviare il server di Ipython che è in esecuzione nella macchina virtuale come un'attività pianificata di windows denominata "Start\_IPython\_Notebook". Se la nuova password non viene accettata dopo il riavvio di questa attività, provare a riavviare la macchina virtuale.

## Strumenti installati nella macchina virtuale per l'analisi scientifica dei dati di Microsoft

### R
Se si vuole usare R per l'analisi, nella VM è installato Revolution R Open (RRO). Si tratta di una distribuzione open source di R, completamente compatibile con CRAN-R. Contiene il motore R open source più recente e la libreria Intel Math Kernel. Nella VM è incluso anche un IDE denominato "RRO RGui". È anche possibile scaricare e usare altri IDE, ad esempio [RStudio](http://www.rstudio.com).

### Python
Per lo sviluppo tramite Python, è installata distribuzione Anaconda Python 2.7. Questa distribuzione contiene il linguaggio Python di base con circa 300 dei più diffusi pacchetti di matematica, ingegneria e analisi dei dati. È possibile usare Python Tools per Visual Studio (PTVS), installato con Visual Studio 2015 Community Edition, o uno degli ambienti IDE inclusi in Anaconda, ad esempio IDLE o Spyder. È possibile avviare uno di questi IDE eseguendo una ricerca sulla barra di ricerca (**Win**+**S**).

### IPython Notebook
La distribuzione Anaconda include anche IPython Notebook, un ambiente per condividere il codice e le analisi. È stato preconfigurato un server IPython Notebook. Sul desktop è disponibile un'icona per avviare il browser per accedere al server Notebook. Se si usa la VM da desktop remoto, è anche possibile visitare il sito [https://localhost:9999/](https://localhost:9999/) per accedere al server notebook IPython. Nota: scegliere semplicemente di continuare se viene visualizzato un avviso relativo al certificato.

### Visual Studio 2015 Community Edition
Visual Studio Community Edition è installato nella VM. È una versione gratuita di IDE più diffuso di Microsoft che è possibile usare per scopi di valutazione e per team molto piccoli. È possibile consultare le condizioni di licenza [qui](https://www.visualstudio.com/support/legal/mt171547). Aprire Visual Studio facendo doppio clic sull'icona del desktop o sul menu **Start**. È anche possibile cercare i programmi premendo i tasti **Win** + **S** e immettendo "Visual Studio".

Nota: è possibile che venga visualizzato un messaggio indicante che il periodo di valutazione è scaduto. È possibile immettere le credenziali di un account Microsoft o crearne uno e immetterlo per accedere a Visual Studio Community Edition. È quindi possibile creare progetti in linguaggi come C# e Python.

### SQL Server Express
Con Visual Studio Community Edition viene fornita anche una versione limitata di SQL Server. È possibile accedere al server SQL avviando **SQL Server Management Studio**. Il nome della VM verrà popolato come Nome server. Usare l'autenticazione di Windows durante l'accesso come amministratore a Windows. Una volta eseguito l'accesso a SQL Server Management Studio è possibile creare altri utenti, creare database, importare i dati ed eseguire query SQL.

### Azure 
Nella macchina virtuale sono installati diversi strumenti di Azure: un collegamento sul desktop per accedere alla documentazione di Azure SDK; **AzCopy**, usato per spostare i dati fuori e dentro l'account di archiviazione di Microsoft Azure; **Esplora archivi di Azure**, usato per scorrere gli oggetti archiviati nell'account di archiviazione di Azure; **Microsoft Azure Powershell**, installato nella VM e usato per amministrare le risorse di Azure in un linguaggio di script.

###Power BI

È stato installato **Power BI Desktop** per facilitare la creazione di dashboard e interessanti visualizzazioni. Usare questo strumento per estrarre dati da origini diverse, per creare dashboard e report e pubblicarli nel cloud. Per informazioni, visitare il sito di [Power BI](http://powerbi.microsoft.com).

Nota: è necessario un account Office 365 per accedere a Power BI.

## Altri strumenti di sviluppo Microsoft
È possibile usare [**Microsoft Web Platform Installare**](https://www.microsoft.com/web/downloads/platform.aspx) per trovare e scaricare altri strumenti di sviluppo Microsoft. È disponibile anche un collegamento allo strumento sul desktop della macchina virtuale per l'analisi scientifica dei dati di Microsoft.

<!---HONumber=AcomDC_1223_2015-->