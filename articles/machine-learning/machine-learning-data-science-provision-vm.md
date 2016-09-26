<properties 
	pageTitle="Eseguire il provisioning di una macchina virtuale per l'analisi scientifica dei dati di Microsoft | Microsoft Azure" 
	description="Configurare e creare una macchina virtuale per l'analisi scientifica dei dati in Azure per l'analisi dei dati e l'apprendimento automatico." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="jhubbard" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2016" 
	ms.author="bradsev" />


# Eseguire il provisioning di una macchina virtuale per l'analisi scientifica dei dati di Microsoft

La macchina virtuale per l'analisi scientifica dei dati di Microsoft è un'immagine di macchina virtuale (VM) di Azure pre-installata e configurata con diversi strumenti diffusi comunemente usati per l'analisi dei dati e l'apprendimento automatico. Gli strumenti inclusi sono:

- Microsoft R Server Developer Edition
- Distribuzione Anaconda Python
- Notebook Jupyter (con kernel R o Python)
- Visual Studio Community Edition
- Power BI Desktop
- SQL Server 2016 Developer Edition
- Strumenti di Machine Learning
    - [Computational Network Toolkit (CNTK)](https://github.com/Microsoft/CNTK): toolkit di software di formazione avanzato sviluppato da Microsoft Research.
    - [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): sistema di apprendimento automatico rapido che supporta tecniche come hash, allreduce, reduction, learning2search, nonché apprendimento online, attivo e interattivo.
    - [XGBoost](https://xgboost.readthedocs.org/en/latest/): strumento che consente un'implementazione dell'albero con boosting rapida e accurata.
    - [Rattle](http://rattle.togaware.com/) (R Analytical Tool To Learn Easily): strumento che rende semplice la fase introduttiva all'analisi dei dati e al Machine Learning in R, grazie a funzionalità di esplorazione e modellazione dei dati basate su GUI con generazione automatica di codice R.
    - [mxnet](https://github.com/dmlc/mxnet): un framework di apprendimento avanzato progettato per l'efficienza e la flessibilità
- Librerie in R e Python da usare in Azure Machine Learning e altri servizi di Azure
- Git, che include Git Bash, per lavorare con i repository di codice sorgente fra cui GitHub e Visual Studio Team Services
- Porte Windows di diverse comuni utilità della riga di comando di Linux (tra cui awk, sed, perl, grep, find, wget, curl e così via) accessibili dal prompt dei comandi.


L'esecuzione di analisi scientifica dei dati implica l'iterazione di una sequenza di attività: ricerca, caricamento e pre-elaborazione dei dati, generazione e test di modelli e distribuzione dei modelli per l'utilizzo in applicazioni intelligenti. I data scientist usano un'ampia gamma di strumenti per completare queste attività. Trovare le versioni software appropriate e quindi scaricarle e installarle può essere un'operazione molto dispersiva in termini di tempo. La macchina virtuale per l'analisi scientifica dei dati di Microsoft facilita questo compito fornendo un'immagine pronta da usare di cui si può eseguire il provisioning in Azure con tutti i più comuni strumenti pre-installati e configurati.

La macchina virtuale per l'analisi scientifica dei dati di Microsoft avvia rapidamente il progetto di analisi e consente di svolgere attività in vari linguaggi, ad esempio R, Python, SQL e C#. Visual Studio fornisce un IDE facile da usare per sviluppare e testare il codice. Azure SDK incluso nella VM consente di compilare le applicazioni usando vari servizi sulla piattaforma cloud di Microsoft.

Per questa immagine di VM per l'analisi scientifica dei dati non sono previsti costi per il software. Si pagano solo le spese di utilizzo di Azure in base alle dimensioni della macchina virtuale di cui viene eseguito il provisioning. Per informazioni dettagliate sui costi di calcolo, vedere la sezione Dettagli prezzi nella pagina relativa alla [macchina virtuale per l'analisi scientifica dei dati](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/).


## Prerequisiti

Prima di creare una macchina virtuale per l'analisi scientifica dei dati di Microsoft, è necessario quanto segue:

- **Sottoscrizione di Azure**: per ottenerne una, vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

*   **Account di archiviazione di Azure**: per crearne uno, vedere [Creare un account di archiviazione](storage-create-storage-account.md#create-a-storage-account). In alternativa, se non si vuole usare un account esistente è possibile creare l'account di archiviazione come parte del processo di creazione della VM.


## Creare la macchina virtuale per l'analisi scientifica dei dati di Microsoft

Di seguito sono riportati i passaggi necessari per creare un'istanza della macchina virtuale per l'analisi scientifica dei dati di Microsoft:

1.	Passare alla macchina virtuale nel [portale di Azure](https://portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm).
2.	 Selezionare il pulsante **Crea** in basso per visualizzare una procedura guidata.![configure-data-science-vm](./media/machine-learning-data-science-provision-vm/configure-data-science-virtual-machine.png)
3.	 La procedura guidata usata per creare la macchina virtuale per l'analisi scientifica dei dati di Microsoft richiede **input** per ognuno dei **cinque passaggi** elencati in ordine numerico sul lato destro della figura. Di seguito sono riportati gli input necessari per configurare ciascuno di questi passaggi:
	
	1.	 **Nozioni di base**
		1.	 **Name**: nome del server di analisi scientifica dei dati che si sta creando.
		2.	 **Nome utente**: ID di accesso dell'account amministratore.
		3.	 **Password**: password dell'account amministratore.
		4.	 **Sottoscrizione**: se si ha più di una sottoscrizione, selezionare quella in cui viene creata e fatturata la macchina virtuale.
		5.	 **Gruppo di risorse**: è possibile creare un nuovo gruppo di risorse o usarne uno esistente.
		6.	 **Location**: selezionare la posizione del data center più appropriata. In genere è il data center contenente la maggior parte dei dati o più vicino alla posizione fisica per garantire la massima velocità di accesso alla rete.
		 
	2.	 **Dimensione**: selezionare un tipo di server che soddisfa i requisiti funzionali e i vincoli di costo. Per accedere ad altre opzioni di dimensioni delle VM, selezionare "Visualizza tutto".
	
	3.	 **Impostazioni**:
		1.	 **Tipo di disco**: scegliere Premium se si preferisce un'unità SSD, in caso contrario scegliere "Standard".
		2.	 **Account di archiviazione**: è possibile creare un nuovo account di archiviazione di Azure nella sottoscrizione oppure usarne uno esistente nella stessa *località* scelta nel passaggio **Nozioni di base** della procedura guidata.
		3.	 **Altri parametri**: in genere si usano semplicemente i valori predefiniti. Nel caso in cui si desideri usare valori non predefiniti, è possibile passare il puntatore sul collegamento informativo per visualizzare informazioni su campi specifici.

	4.	 **Riepilogo**: verificare che tutte le informazioni immesse siano corrette.
	5.	 **Acquista**: fare clic su **Acquista** per avviare il provisioning. Viene fornito un collegamento alle condizioni della transazione. La macchina virtuale non prevede costi aggiuntivi oltre a quelli per il calcolo delle dimensioni del server scelto nel passaggio **Size**.


>[AZURE.NOTE] Per il provisioning sono necessari circa 10-20 minuti. Lo stato del provisioning viene visualizzato nel portale di Azure.

## Come accedere a una macchina virtuale per l'analisi scientifica dei dati di Microsoft

Dopo aver creato la VM, è possibile connettersi tramite desktop remoto con le credenziali dell'account amministratore configurato nella sezione **Nozioni di base** precedente.

Una volta creata la VM ed effettuato il provisioning, si è pronti per iniziare a usare gli strumenti installati e configurati nella VM. Sono disponibili riquadri del menu di avvio e icone del desktop per molti strumenti.

## Come creare una password complessa nel server notebook di Jupyter 

Per creare la password complessa per il server Jupyter Notebook installato nel computer, eseguire il comando seguente dal prompt dei comandi nella macchina virtuale per l'analisi scientifica dei dati.

	c:\anaconda\python.exe -c "import IPython;print IPython.lib.passwd()"

Scegliere una password complessa quando viene richiesto.

Nell'output verrà visualizzato l'hash della password nel formato "sha1:xxxxxx". Copiare questo hash di password e sostituire l'hash esistente nel file di configurazione del notebook disponibile nel percorso: **C:\\ProgramData\\jupyter\\jupyter\_notebook\_config.py** con un nome di parametro ***c.NotebookApp.password***.

Sostituire solo la parte (xxxxxx) del valore dell'hash esistente all'interno delle virgolette. Sia le virgolette che il prefisso ***sha1:*** per il valore del parametro devono essere mantenuti.

Infine, è necessario arrestare e riavviare il server Jupyter in esecuzione nella VM come attività pianificata di Windows denominata **Start\_IPython\_Notebook**. Se la nuova password non viene accettata dopo il riavvio di questa attività, provare a terminare tutti i processi Python in esecuzione da Gestione attività e quindi riavviare l'attività pianificata. In alternativa, provare a riavviare la macchina virtuale.

## Strumenti installati nella macchina virtuale per l'analisi scientifica dei dati di Microsoft

### Microsoft R Server Developer Edition
Per usare R per l'analisi, è necessario che nella macchina virtuale sia installato Microsoft R Server Developer Edition. Microsoft R Server è una piattaforma di analisi di livello aziendale su vasta scala basata su R supportata, scalabile e sicura. Grazie al supporto di diverse funzionalità per statistiche di Big Data, modellazione predittiva e apprendimento automatico, R Server supporta l'intera gamma dell'analisi: esplorazione, analisi, visualizzazione e modellazione. Usando ed estendendo il linguaggio R open source, Microsoft R Server è completamente compatibile con gli script e le funzioni R, nonché i pacchetti CRAN, per analizzare i dati a livello aziendale. Risolve anche le limitazioni in memoria di R open source aggiungendo l'elaborazione parallela e in blocchi dei dati, consentendo così di eseguire analisi su dati con volumi molto più elevati rispetto a quelli che possono essere contenuti nella memoria principale. Visual Studio Community Edition incluso nella VM contiene l'estensione Strumenti R per Visual Studio che fornisce un IDE completo per l'uso di R. È anche possibile scaricare e usare altri IDE, come [RStudio](http://www.rstudio.com).

### Python
Per lo sviluppo tramite Python, è installata la distribuzione Anaconda Python 2.7 e 3.5. Questa distribuzione contiene il linguaggio Python di base con circa 300 dei più diffusi pacchetti di matematica, ingegneria e analisi dei dati. È possibile usare Python Tools per Visual Studio (PTVS), installato con Visual Studio 2015 Community Edition, o uno degli ambienti IDE inclusi in Anaconda, ad esempio IDLE o Spyder. È possibile avviare uno di questi IDE eseguendo una ricerca sulla barra di ricerca (**Win**+**S**).

>[AZURE.NOTE] Per usare Python Tools for Visual Studio in Anaconda Python 2.7 e 3.5, è necessario creare ambienti personalizzati per ogni versione. Per impostare questi percorsi per gli ambienti in Visual Studio 2015 Community Edition, passare a **Strumenti** -> **Python Tools** -> **Python Environments** e quindi fare clic su "**+ Custom**".

Anaconda Python 2.7 viene installato in C:\\Anaconda e Anaconda Python 3.5 viene installato in C:\\Anaconda\\envs\\py35. Per la procedura dettagliata, vedere la [documentazione di PTVS](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it).

### Notebook Jupyter
La distribuzione Anaconda include anche Jupyter Notebook, un ambiente per la condivisione di codice e analisi. Un server Jupyter Notebook è stato preconfigurato con Python 2, Python 3 e i kernel R. Sul desktop è disponibile un'icona denominata Notebook di Jupyter per avviare il browser per accedere al server notebook. Se si usa la VM da desktop remoto, è anche possibile visitare il sito [https://localhost:9999/](https://localhost:9999/) per accedere al server Jupyter Notebook quando si è connessi alla VM.
 
>[AZURE.NOTE] Se vengono visualizzati avvisi relativi al certificato, scegliere di continuare.

Nel pacchetto sono inclusi notebook di esempio in Python e in R. I Jupyter Notebook illustrano come usare Microsoft R Server, i servizi R di SQL Server 2016 (analisi nel database), Python e altre tecnologie di Azure dopo aver effettuato l'accesso a Jupyter. È possibile visualizzare il collegamento agli esempi nella home page del notebook dopo aver eseguito l'autenticazione a Jupyter Notebook con la password creata nel passaggio precedente.

### Visual Studio 2015 Community Edition
Visual Studio Community Edition è installato nella VM. È una versione gratuita del diffuso IDE di Microsoft che è possibile usare a scopo di valutazione e per team di dimensioni ridotte. È possibile consultare le condizioni di licenza [qui](https://www.visualstudio.com/support/legal/mt171547). Aprire Visual Studio facendo doppio clic sull'icona del desktop o sul menu **Start**. È anche possibile cercare i programmi premendo i tasti **Win** + **S** e immettendo "Visual Studio". È quindi possibile creare progetti in linguaggi come C#, Python, R e node.js. Vengono anche installati plug-in che semplificano l'uso di servizi Azure come Azure Data Catalog, Azure HDInsight (Hadoop, Spark) e Azure Data Lake.

>[AZURE.NOTE] Potrebbe essere visualizzato un messaggio indicante che il periodo di valutazione è scaduto. Per accedere a Visual Studio Community Edition è necessario immettere le credenziali di un account Microsoft o creare un nuovo account gratuito.

### SQL Server 2016 Developer Edition
Una versione per sviluppatori di SQL Server 2016 con i R Services per eseguire analisi nel database è fornita nella VM. I servizi R offrono una piattaforma per lo sviluppo e la distribuzione di applicazioni intelligenti. È possibile usare il linguaggio R, avanzato e potente, e i numerosi pacchetti della community per creare modelli e generare stime per i dati SQL Server. È possibile mantenere le analisi insieme ai dati perché i servizi R (nel database) integrano il linguaggio R con SQL Server. Vengono così eliminati i costi e i rischi per la sicurezza associati allo spostamento dei dati.

>[AZURE.NOTE] È possibile usare SQL Server 2016 Developer Edition solo a scopo di sviluppo e test. Per l'esecuzione nell'ambiente di produzione è necessaria una licenza.

È possibile accedere a SQL Server avviando **SQL Server Management Studio**. Il nome della VM viene inserito come Nome server. Usare l'autenticazione di Windows durante l'accesso come amministratore a Windows. Una volta eseguito l'accesso a SQL Server Management Studio è possibile creare altri utenti, creare database, importare i dati ed eseguire query SQL.

Per abilitare l'analisi nel database con Microsoft R, eseguire il comando seguente come azione una tantum in SQL Server Management Studio dopo aver eseguito l'accesso come amministratore del server.

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 
        
        (Please replace the %COMPUTERNAME% with your VM name)


### Azure 
Diversi strumenti di Azure vengono installati nella macchina virtuale:

- Sul desktop c'è un collegamento per accedere alla documentazione di Azure SDK.
- **AzCopy**: consente di spostare i dati da e verso l'account di archiviazione di Microsoft Azure. Per visualizzare l'utilizzo, digitare **Azcopy** al prompt dei comandi.
- **Microsoft Azure Storage Explorer**: consente di esplorare gli oggetti archiviati nell'account di archiviazione di Azure e di trasferire dati da e verso Archiviazione di Azure. È possibile digitare **Esplora archivi** nella casella di ricerca oppure individuarlo e selezionarlo dal menu Start di Windows per accedere allo strumento.
- **Adlcopy**: consente di spostare i dati in Azure Data Lake. Per visualizzare l'utilizzo, digitare **adlcopy** nel prompt dei comandi.
- **dtui**: consente di spostare dati da e verso Azure DocumentDB, un database NoSQL nel cloud. Digitare **dtui** al prompt dei comandi.
- **Gateway di gestione dati di Microsoft**: consente lo spostamento di dati tra origini dati locali e il cloud. Viene usato all'interno di strumenti come Data Factory di Azure.
- **Microsoft Azure Powershell**: nella VM viene installato anche uno strumento che consente di amministrare le risorse di Azure nel linguaggio di scripting di PowerShell.

###Power BI

È stato installato **Power BI Desktop** per facilitare la creazione di dashboard e interessanti visualizzazioni. Usare questo strumento per estrarre dati da origini diverse, per creare dashboard e report e pubblicarli nel cloud. Per informazioni, visitare il sito di [Power BI](http://powerbi.microsoft.com). Power BI Desktop è disponibile nel menu Start.

>[AZURE.NOTE] Per accedere a Power BI è necessario un account Office 365.

## Altri strumenti di sviluppo Microsoft
È possibile usare [**Microsoft Web Platform Installare**](https://www.microsoft.com/web/downloads/platform.aspx) per trovare e scaricare altri strumenti di sviluppo Microsoft. È disponibile anche un collegamento allo strumento sul desktop della macchina virtuale per l'analisi scientifica dei dati di Microsoft.

## Directory importanti nella VM

| Item | Directory |
| ------------------------------| ---------------- |
|Configurazioni del server Jupyter Notebook | C:\\ProgramData\\jupyter |
|Home directory degli esempi di Jupyter Notebook| c:\\dsvm\\notebooks|
|Altri esempi | c:\\dsvm\\samples|
| Anaconda (impostazione predefinita: Python 2.7) | c:\\Anaconda |
| Ambiente Anaconda Python 3.5 | c:\\Anaconda\\envs\\py35|
|Directory dell'istanza autonoma di R Server (istanza predefinita di R) | C:\\Programmi\\Microsoft SQL Server\\130\\R\_SERVER |
| Directory dell'istanza nel database di Server R | C:\\Programmi\\Microsoft SQL Server\\MSSQL13. MSSQLSERVER\\R\_SERVICES |
| Strumenti vari | c:\\dsvm\\tools|

>[AZURE.NOTE] Le istanze della macchina virtuale per l'analisi scientifica dei dati di Microsoft create prima della versione 1.5.0 (ovvero prima del 3 settembre 2016) usano una struttura di directory leggermente diversa rispetto a quella specificata nella tabella precedente.

## Passaggi successivi
Ecco alcuni passaggi successivi per continuare l'apprendimento e l'esplorazione.

* Esaminare i vari strumenti di analisi scientifica dei dati nella VM per l'analisi scientifica dei dati facendo clic sul menu Start e verificando gli strumenti elencati nel menu.
* Per esempi d'uso della libreria RevoScaleR in R che supporta l'analisi dei dati a livello aziendale, passare a **C:\\Programmi\\Microsoft SQL Server\\130\\R\_SERVER\\library\\RevoScaleR\\demoScripts**.
* Leggere l'articolo [Dieci cose da fare con la macchina virtuale per l'analisi scientifica dei dati](http://aka.ms/dsvmtenthings).
* Informazioni su come creare sistematicamente soluzioni analitiche end-to-end usando il [processo di analisi scientifica dei dati per i team](https://azure.microsoft.com/documentation/learning-paths/data-science-process/).
* Visitare [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com) per esempi di Machine Learning e di analisi dei dati con Cortana Intelligence Suite. Per questa raccolta è disponibile anche un'icona nel menu **Start** e sul desktop della macchina virtuale.

<!---HONumber=AcomDC_0914_2016-->