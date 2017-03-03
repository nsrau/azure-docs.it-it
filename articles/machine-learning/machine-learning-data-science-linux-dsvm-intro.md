---
title: Effettuare il provisioning di una macchina virtuale Linux per l&quot;analisi scientifica dei dati | Microsoft Docs
description: Configurare e creare una macchina virtuale Linux per l&quot;analisi scientifica dei dati in Azure per eseguire analisi dei dati e per l&quot;apprendimento automatico.
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 3bab0ab9-3ea5-41a6-a62a-8c44fdbae43b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2016
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: 3136b8345d0c851c29a9498089da73c8564549d1
ms.openlocfilehash: f355ea88f4d14e48d9523a0ac7aff965171bed4b
ms.lasthandoff: 01/31/2017


---
# <a name="provision-the-linux-data-science-virtual-machine"></a>Effettuare il provisioning di una macchina virtuale Linux per l'analisi scientifica dei dati
La macchina virtuale Linux per l'analisi scientifica dei dati è una macchina virtuale di Azure fornita con un insieme di strumenti preinstallati. Questi strumenti vengono comunemente usati per eseguire l'analisi dei dati e il Machine Learning. Ecco i componenti software principali:

* Microsoft R Server Developer Edition
* Distribuzione di Anaconda Python, versioni 2.7 e 3.5, incluse le più comuni librerie di analisi dei dati
* JupyterHub: un server notebook Jupyter multiutente che supporta kernel R, Python e Julia
* Azure Storage Explorer
* Interfaccia della riga di comando di Azure per la gestione delle risorse di Azure
* Database PostgresSQL
* Strumenti di Machine Learning
  * [Computational Network Toolkit (CNTK)](https://github.com/Microsoft/CNTK): toolkit di software di formazione avanzato sviluppato da Microsoft Research.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): sistema di apprendimento automatico rapido che supporta tecniche come hash, allreduce, reduction, learning2search, nonché apprendimento online, attivo e interattivo.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): strumento che consente un'implementazione dell'albero con boosting rapida e accurata.
  * [Rattle](http://rattle.togaware.com/) (R Analytical Tool To Learn Easily): strumento che rende semplice la fase introduttiva all'analisi dei dati e al Machine Learning in R, grazie a funzionalità di esplorazione e modellazione dei dati basate su GUI con generazione automatica di codice R.
* Azure SDK in Java, Python, Node.js, Ruby, PHP
* Librerie in R e Python da usare in Azure Machine Learning e altri servizi di Azure
* Editor e strumenti di sviluppo, ad esempio Eclipse, Emacs, gedit, vi

L'esecuzione dell'analisi scientifica dei dati comporta l'iterazione di una sequenza di attività quali:

1. Ricerca, caricamento e pre-elaborazione dei dati
2. Compilazione e test di modelli
3. Distribuzione dei modelli per l'uso in applicazioni intelligenti

Gli esperti di dati usano vari strumenti per completare queste attività. Trovare le versioni del software appropriate e quindi scaricarle, compilarle e installarle può essere un'operazione molto dispersiva in termini di tempo.

La macchina virtuale Linux per l'analisi scientifica dei dati può rendere queste attività sostanzialmente più facili. Usarla per avviare rapidamente il progetto di analisi. Consente di svolgere attività in diversi linguaggi, ad esempio R, Python, SQL, Java e C++. Eclipse fornisce un IDE facile da usare per sviluppare e testare il codice. Azure SDK, incluso nella VM, consente di compilare le applicazioni usando vari servizi in Linux sulla piattaforma cloud di Microsoft. È anche possibile accedere ad altri linguaggi, come Ruby, Perl, PHP e Node. js, anch'essi pre-installati.

Per questa immagine di VM per l'analisi scientifica dei dati non sono previsti costi per il software. Si pagano solo le spese d'uso dell'hardware di Azure valutate in base alle dimensioni della macchina virtuale di cui si esegue il provisioning con l'immagine della VM. Altre informazioni sui costi di calcolo sono disponibili alla [pagina con l'elenco delle VM su Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

## <a name="prerequisites"></a>Prerequisiti
Prima di creare una macchina virtuale Linux per l'analisi scientifica dei dati, è necessario avere gli elementi seguenti:

* **Sottoscrizione di Azure**: per ottenerne una, vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/free/).
* **Account di archiviazione di Azure**: per crearne uno, vedere [Creare un account di archiviazione](../storage/storage-create-storage-account.md#create-a-storage-account). In alternativa, è possibile creare l'account di archiviazione come parte del processo di creazione della VM se non si vuole usare un account esistente.

## <a name="create-your-linux-data-science-virtual-machine"></a>Creare la macchina virtuale Linux per l'analisi scientifica dei dati
Ecco i passaggi necessari per creare un'istanza della macchina virtuale Linux per l'analisi scientifica dei dati:

1. Passare all'elenco di macchine virtuali nel [portale di Azure](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vmlinuxdsvm).
2. Fare clic su **Crea** (in basso) per aprire la procedura guidata.![configure-data-science-vm](./media/machine-learning-data-science-linux-dsvm-intro/configure-linux-data-science-virtual-machine.png)
3. Le sezioni seguenti forniscono gli input per ognuno dei passaggi (elencati nella parte destra della figura precedente) della procedura guidata usata per creare la macchina virtuale per l'analisi scientifica dei dati di Microsoft. Di seguito sono riportati gli input necessari per configurare ciascuno di questi passaggi:
   
   a. **Nozioni di base**:
   
   * **Name**: nome del server di analisi scientifica dei dati che si sta creando.
   * **Nome utente**: primo ID di accesso dell'account.
   * **Password**: la prima password dell'account. È possibile usare una chiave pubblica SSH invece di una password.
   * **Sottoscrizione**: se si ha più di una sottoscrizione, selezionare quella in cui viene creata e fatturata la macchina virtuale. È necessario disporre di privilegi di creazione delle risorse per questa sottoscrizione.
   * **Gruppo di risorse**: è possibile creare un nuovo gruppo di risorse o usarne uno esistente.
   * **Location**: selezionare la posizione del data center più appropriata. In genere è il data center che include la maggior parte dei dati o è più vicino alla posizione fisica per l'accesso più veloce alla rete.
   
   b. **Dimensione**:
   
   * Selezionare uno dei tipi di server che soddisfa i requisiti funzionali e vincoli di costo. Selezionare **Visualizza tutto** per visualizzare altre opzioni per le dimensioni delle VM.
   
   c. **Impostazioni**:
   
   * **Tipo di disco**: se si preferisce un'unità a stato solido, scegliere **Premium**. In caso contrario, scegliere **Standard**.
   * **Account di archiviazione**: è possibile creare un nuovo account di archiviazione di Azure nella sottoscrizione o usarne uno esistente nella stessa posizione scelta nel passaggio **Nozioni di base** della procedura guidata.
   * **Altri parametri**: nella maggior parte dei casi si usano semplicemente i valori predefiniti. Nel caso in cui si desideri usare valori non predefiniti, è possibile passare il puntatore sul collegamento informativo per visualizzare informazioni sui campi specifici.
   
   d. **Riepilogo**:
   
   * Verificare che tutte le informazioni immesse siano corrette.
   
   e. **Acquisto**:
   
   * Fare clic su **Acquista**per avviare il provisioning. Viene fornito un collegamento alle condizioni della transazione. La macchina virtuale non prevede costi aggiuntivi oltre a quelli per il calcolo delle dimensioni del server scelto nel passaggio **Size** .

Per il provisioning sono necessari circa 10-20 minuti. Lo stato del provisioning viene visualizzato nel portale di Azure.

## <a name="how-to-access-the-linux-data-science-virtual-machine"></a>Come accedere alla macchina virtuale Linux per l'analisi scientifica dei dati
Dopo aver creato la VM, è possibile accedervi tramite SSH. Usare le credenziali dell'account creato nella sezione **Nozioni di base** del passaggio 3 per l'interfaccia della shell di testo. In Windows è possibile scaricare uno strumento client SSH come [Putty](http://www.putty.org). Se si preferisce un desktop con interfaccia grafica (X Windows System), è possibile usare X11 Forwarding su Putty o installare il client X2Go.

> [!NOTE]
> Nei test il client X2Go ha fornito prestazioni significativamente migliori di X11 Forwarding. È quindi consigliabile usare il client X2Go per un'interfaccia desktop grafica.
> 
> 

## <a name="installing-and-configuring-x2go-client"></a>Installazione e configurazione del client X2Go
Nella VM Linux è già stato effettuato il provisioning del server X2Go ed è pronta per accettare connessioni client. Per connettersi al desktop con interfaccia grafica della VM Linux, è necessario eseguire le operazioni seguenti sul client:

1. Scaricare e installare il client X2Go per la piattaforma client da [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Eseguire il client X2Go e selezionare **New Session**(Nuova sessione). Viene visualizzata una finestra di configurazione con più schede. Immettere i parametri di configurazione seguenti:
   * **Scheda Session**(Sessione):
     * **Host**: nome host o indirizzo IP della VM Linux di analisi scientifica dei dati.
     * **Login**(Accesso): nome utente di accesso della VM Linux.
     * **SSH Port**(Porta SSH): lasciare il valore predefinito 22.
     * **Session Type**(Tipo sessione): modificare il valore in XFCE. La VM Linux attualmente supporta solo l'ambiente desktop XFCE.
   * **Scheda Media**(Supporti): è possibile disattivare il supporto audio e la stampa client se non è necessario usarli.
   * **Shared folders**(Cartelle condivise): se si intende montare directory dei computer client nella VM Linux, aggiungere in questa scheda le directory dei computer client da condividere con la VM.

Dopo aver eseguito l'accesso alla VM con il client SSH o il desktop con interfaccia grafica XFCE tramite il client X2Go, è possibile iniziare a usare gli strumenti installati e configurati nella VM. In XFCE è possibile visualizzare i collegamenti di menu delle applicazioni e le icone del desktop per molti di questi strumenti.

## <a name="tools-installed-on-the-linux-data-science-virtual-machine"></a>Strumenti installati nella macchina virtuale LInux per l'analisi scientifica dei dati
### <a name="microsoft-r-open"></a>Microsoft R Open
R è uno dei linguaggi più diffusi per l'analisi dei dati e il Machine Learning. Se si vuole usare R per l'esecuzione di analisi, è necessario che nella VM sia installato Microsoft R Open (MRO) con Math Kernel Library (MKL). La libreria MKL ottimizza le operazioni matematiche comuni negli algoritmi di analisi. MRO è totalmente compatibile con CRAN-R e tutte le librerie R pubblicate in CRAN possono essere installate in MRO. È possibile modificare i programmi R in uno degli editor predefiniti, ad esempio vi, Emacs o gedit. È anche possibile scaricare e usare altri IDE, ad esempio [RStudio](http://www.rstudio.com). Per praticità, nella directory **/dsvm/tools** viene fornito un semplice script (installRStudio.sh) che installa RStudio. Se si usa l'editor Emacs, si noti che il relativo pacchetto ESS (Emacs Speaks Statistics), che semplifica l'uso dei file R all'interno dell'editor Emacs, è preinstallato.

Per avviare R è sufficiente digitare **R** nella shell. Verrà visualizzato un ambiente interattivo. Per sviluppare il programma R, si usa in genere un editor come Emacs o vi oppure gedit e quindi si eseguono gli script all'interno di R. Se si installa RStudio, si ottiene un ambiente IDE con interfaccia grafica completo per sviluppare il programma R.

È anche disponibile uno script R per installare i [20 pacchetti R più popolari](http://www.kdnuggets.com/2015/06/top-20-r-packages.html) , se necessario. Questo script può essere eseguito una volta nell'interfaccia interattiva R, a cui è possibile accedere digitando **R** nella shell, come indicato sopra.  

### <a name="python"></a>Python
Per lo sviluppo tramite Python, è installata la distribuzione Anaconda Python 2.7 e 3.5. Questa distribuzione contiene il linguaggio Python di base con circa 300 dei più diffusi pacchetti di matematica, ingegneria e analisi dei dati. È possibile usare gli editor di testo predefiniti. Si può anche usare Spyder, un IDE Python incluso nelle distribuzioni di Anaconda Python. Spyder richiede un desktop con interfaccia grafica o X11 Forwarding. Nel desktop con interfaccia grafica è disponibile un collegamento a Spyder.

Poiché sono presenti sia Python 2.7 che 3.5, è necessario attivare in modo specifico la versione di Python che si vuole usare nella sessione corrente. Il processo di attivazione imposta la variabile PATH sulla versione di Python desiderata.

Per attivare Python 2.7, eseguire il comando seguente dalla shell:

    source /anaconda/bin/activate root

Python 2.7 viene installato in */anaconda/bin*.

Per attivare Python 3.5, eseguire il comando seguente dalla shell:

    source /anaconda/bin/activate py35


Python 3.5 viene installato in */anaconda/envs/py35/bin*.

Per richiamare la sessione interattiva di Python, è sufficiente digitare **python** nella shell. Se si usa un'interfaccia grafica o è installato X11 Forwarding, è possibile digitare il comando **spyder** per avviare l'IDE di Python.

### <a name="jupyter-notebook"></a>Notebook di Jupyter
La distribuzione Anaconda include anche Jupyter Notebook, un ambiente per condividere codice e analisi. Notebook di Jupyter è accessibile tramite JupyterHub. Per eseguire l'accesso, usare il nome utente e la password locali di Linux.

Il server Notebook di Jupyter è stato preconfigurato con Python 2, Python 3 e i kernel R. Sul desktop è disponibile un'icona denominata Notebook di Jupyter per avviare il browser e accedere al server Notebook di Jupyter. Se si usa la VM tramite SSH o il client X2Go, è anche possibile visitare [https://localhost:8000/](https://localhost:8000/) per accedere al server Notebook di Jupyter.

> [!NOTE]
> Se vengono visualizzati avvisi relativi al certificato, scegliere di continuare.
> 
> 

È possibile accedere al server Jupyter Notebook da qualsiasi host, digitando semplicemente *https://\<nome DNS o indirizzo IP della VM\>:8000/*

> [!NOTE]
> La porta 8000 è aperta nel firewall per impostazione predefinita quando viene effettuato il provisioning della VM.
> 
> 

Nel pacchetto sono inclusi alcuni notebook di esempio: uno in Python e uno in R. È possibile visualizzare il collegamento agli esempi nella home page del notebook dopo l'autenticazione a Notebook di Jupyter con il nome utente e la password locali di Linux. È possibile creare un nuovo notebook selezionando **Nuovo** e quindi il kernel del linguaggio adatto. Se il pulsante **Nuovo** non è visualizzato, fare clic sull'icona **Jupyter** in alto a sinistra per passare alla home page del server notebook.

### <a name="ides-and-editors"></a>IDE ed editor
È possibile scegliere tra diversi editor di codice. Ad esempio vi/VIM, Emacs, gEdit ed Eclipse. gEdit ed Eclipse sono editor grafici e per usarli è necessario essere connessi a un desktop con interfaccia grafica. Per avviare questi editor sono disponibili collegamenti di menu delle applicazioni e sul desktop.

**VIM** e **Emacs** sono editor basati su testo. In Emacs è installato un pacchetto di componenti aggiuntivi denominato Emacs Speaks Statistics (ESS) che facilita l'utilizzo di R nell'editor Emacs. Altre informazioni sono disponibili nella pagina relativa a [ESS](http://ess.r-project.org/).

**Eclipse** è un IDE open source estendibile che supporta più linguaggi. L'edizione per sviluppatori Java è l'istanza installata nella VM. Sono disponibili plug-in per diversi linguaggi comuni che possono essere installati per estendere l'ambiente Eclipse. È anche disponibile un plug-in installato in Eclipse, **Toolkit di Azure per Eclipse**. Il plug-in consente di creare, sviluppare, testare e distribuire applicazioni Azure tramite l'ambiente di sviluppo Eclipse che supporta linguaggi come Java. È disponibile anche **Azure SDK per Java** che consente l'accesso a diversi servizi di Azure da un ambiente Java. Altre informazioni su Azure Toolkit for Eclipse sono disponibili nella pagina [Azure Toolkit for Eclipse](../azure-toolkit-for-eclipse.md).

**LaTex** viene installato tramite il pacchetto texlive insieme a un pacchetto di componenti aggiuntivi di Emacs, [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) , che semplifica la creazione di documenti LaTex in Emacs.  

### <a name="databases"></a>Database
#### <a name="postgres"></a>Postgres
Il database open source **Postgres** è disponibile nella VM con i servizi in esecuzione e initdb già completati. È comunque necessario creare database e utenti. Per altre informazioni, vedere la [documentazione di Postgres](https://www.postgresql.org/docs/).  

#### <a name="graphical-sql-client"></a>Client SQL grafico
Il client SQL grafico **SQuirreL SQL** viene fornito per la connessione a diversi database, come Microsoft SQL Server, Postgres e MySQL, e per l'esecuzione di query SQL. È possibile eseguirlo da una sessione desktop con interfaccia grafica, usando ad esempio il client X2Go. Per richiamare SQuirreL SQL è possibile avviarlo dall'icona sul desktop o eseguire il comando seguente nella shell.

    /usr/local/squirrel-sql-3.7/squirrel-sql.sh

Prima di usarlo per la prima volta, è necessario configurare i driver e gli alias di database. I driver JDBC si trovano in:

*/usr/share/Java/jdbcdrivers*

Per altre informazioni, vedere [SQL SQuirrel](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

#### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Strumenti da riga di comando per l'accesso a Microsoft SQL Server
Anche nel pacchetto driver ODBC per SQL Server sono disponibili due strumenti da riga di comando:

**bcp**: questa utilità crea copie bulk di dati tra un'istanza di Microsoft SQL Server e un file di dati in un formato specificato dall'utente. L'utilità bcp può essere usata per importare un numero elevato di nuove righe nelle tabelle di SQL Server o per esportare dati dalle tabelle in file di dati. Per importare dati in una tabella, è necessario usare un file di formato creato per la tabella o conoscere la struttura della tabella e i tipi di dati validi per le relative colonne.

Per altre informazioni, vedere [Connessione a bcp](https://msdn.microsoft.com/library/hh568446.aspx).

**sqlcmd**: questa utilità consente di immettere istruzioni Transact-SQL, procedure di sistema e file script al prompt dei comandi. Questa utilità usa ODBC per eseguire batch Transact-SQL.

Per altre informazioni, vedere [Connessione con sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

> [!NOTE]
> Esistono alcune differenze in questa utilità tra le piattaforme Linux e Windows. Per informazioni dettagliate, vedere la documentazione di .
> 
> 

#### <a name="database-access-libraries"></a>Librerie di accesso al database
In R e Python sono disponibili librerie per accedere ai database.

* In R il pacchetto **RODBC** o **dplyr** consente di eseguire query o istruzioni SQL sul server di database.
* In Python la libreria **pyodbc** fornisce l'accesso al database con ODBC come livello sottostante.  

Per accedere a **Postgres**:

* Da R usare il pacchetto **RPostgreSQL**.
* Da Python usare la libreria **psycopg2** .

### <a name="azure-tools"></a>Strumenti di Azure
Nella VM sono installati gli strumenti di Azure seguenti:

* **Interfaccia della riga di comando di Azure**: consente di creare e gestire risorse di Azure tramite i comandi della shell. Per richiamare gli strumenti di Azure, digitare semplicemente **azure help**. Per altre informazioni, vedere la [pagina di documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Microsoft Azure Storage Explorer**(Esplora archivi di Microsoft Azure): è uno strumento grafico usato per esplorare gli oggetti archiviati nell'account di archiviazione di Azure e per caricare o scaricare dati nei BLOB e dai BLOB di Azure. È possibile accedere a Esplora archivi dall'icona del collegamento sul desktop. Questo strumento può essere richiamato da un prompt della shell digitando **StorageExplorer**. È necessario essere connessi da un client X2Go o avere installato X11 Forwarding.
* **Librerie di Azure**: di seguito vengono illustrate alcune delle librerie preinstallate.
  
  * **Python**: le librerie installate correlate ad Azure in Python sono **azure**, **azureml**, **pydocumentdb** e **pyodbc**. Le prime tre librerie consentono di accedere ai servizi di archiviazione di Azure, Azure Machine Learning e Azure DocumentDB, ovvero un database NoSQL in Azure. La quarta libreria, pyodbc (insieme ai driver Microsoft ODBC per SQL Server), consente l'accesso da Python a SQL Server, al database SQL di Azure e ad Azure SQL Data Warehouse tramite un'interfaccia ODBC. Immettere **pip list** per vedere elencate tutte le librerie. Assicurarsi di eseguire questo comando in Python sia nell'ambiente 2.7 che 3.5.
  * **R**: le librerie installate correlate ad Azure in R sono **AzureML** e **RODBC**.
  * **Java**: l'elenco delle librerie Java per Azure è disponibile nella directory **/dsvm/sdk/AzureSDKJava** della VM. Le librerie principali sono l'archiviazione di Azure e le API di gestione, DocumentDB e i driver JDBC per SQL Server.  

È possibile accedere al [portale di Azure](https://portal.azure.com) dal browser Firefox pre-installato. Nel portale di Azure si possono creare, gestire e monitorare le risorse di Azure.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning è un servizio cloud completamente gestito che consente di creare, distribuire e condividere soluzioni di analisi predittiva. Si possono creare esperimenti e modelli da Azure Machine Learning Studio, a cui è possibile accedere da un Web browser nella macchina virtuale di analisi scientifica dei dati, visitando il sito [Microsoft Azure Machine Learning](https://studio.azureml.net).

Dopo aver eseguito l'accesso ad Azure Machine Learning Studio, si può accedere a un'area di sperimentazione che consente di compilare un flusso logico per gli algoritmi di Machine Learning. È anche possibile accedere a un notebook di Jupyter ospitato in Azure Machine Learning e usare direttamente gli esperimenti in Machine Learning Studio. È possibile rendere operativi i modelli di Machine Learning creati eseguendone il wrapping in un'interfaccia del servizio Web. Questo consente ai client scritti in qualsiasi linguaggio di richiamare le stime dai modelli di Machine Learning. Per altre informazioni, vedere [Documentazione su Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

È anche possibile creare modelli personalizzati in R o Python nella VM e quindi distribuirli nell'ambiente di produzione in Azure Machine Learning. Per abilitare questa funzionalità sono state installate librerie in R (**AzureML**) e Python (**azureml**).

Per informazioni su come distribuire i modelli in R e Python in Azure Machine Learning, vedere la sezione "Compilare modelli usando R o Python e renderli operativi con Azure Machine Learning" nell'articolo [Dieci cose da fare con la macchina virtuale per l'analisi scientifica dei dati](machine-learning-data-science-vm-do-ten-things.md) .

> [!NOTE]
> Queste istruzioni sono state scritte per la versione Windows della macchina virtuale di analisi scientifica dei dati. Tuttavia, le informazioni sulla distribuzione di modelli in Azure Machine Learning è applicabile anche alle VM Linux.
> 
> 

### <a name="machine-learning-tools"></a>Strumenti di Machine Learning
La VM viene fornita con alcuni strumenti e algoritmi di Machine Learning precompilati e preinstallati localmente. Sono inclusi:

* **CNTK** (Computational Network Toolkit di Microsoft Research): toolkit di apprendimento avanzato.
* **Vowpal Wabbit**: algoritmo di apprendimento rapido online.
* **xgboost**: strumento che fornisce algoritmi di albero con boosting ottimizzati.
* **Python**: Anaconda Python integra algoritmi Machine Learning con librerie come Scikit-learn. È possibile installare altre librerie usando il comando `pip install` .
* **R**: una libreria completa di funzioni di Machine Learning disponibili per R. Alcune librerie preinstallate sono lm, glm, randomForest, rpart. Altre librerie possono essere installate eseguendo:
  
        install.packages(<lib name>)

Ecco alcune informazioni aggiuntive sui primi tre strumenti di Machine Learning nell'elenco.

#### <a name="cntk"></a>CNTK
È un toolkit open source di apprendimento avanzato e uno strumento da riga di comando (cntk) già incluso in PATH.

Per un esempio di base, eseguire i comandi seguenti nella shell:

    # Copy samples to your home directory and execute cntk
    cp -r /dsvm/tools/CNTK-2016-02-08-Linux-64bit-CPU-Only/Examples/Other/Simple2d cntkdemo
    cd cntkdemo/Data
    cntk configFile=../Config/Simple.cntk

L'output del modello si trova in *~/cntkdemo/Output/Models*.

Per altre informazioni, vedere la sezione CNTK di [GitHub](https://github.com/Microsoft/CNTK) e la [wiki di CNTK](https://github.com/Microsoft/CNTK/wiki).

#### <a name="vowpal-wabbit"></a>Vowpal Wabbit
Vowpal Wabbit è un sistema di apprendimento automatico che usa tecniche come hash, allreduce, reduction, learning2search, nonché apprendimento online, attivo e interattivo.

Per eseguire lo strumento su un esempio molto semplice, eseguire questi comandi:

    cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
    cd vwdemo
    vw house_dataset

Nella directory sono presenti altre demo più approfondite. Per altre informazioni su VW, vedere [questa sezione di GitHub](https://github.com/JohnLangford/vowpal_wabbit) e la [wiki di Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki).

#### <a name="xgboost"></a>XGBoost
Si tratta di una libreria progettata e ottimizzata per gli algoritmi di albero con boosting. L'obiettivo di questa libreria consiste nello spingere i limiti di calcolo dei computer fino ai massimi livelli necessari per fornire una funzionalità di boosting degli alberi su larga scala portabile, scalabile e accurata.

Viene fornita sia come riga di comando che come libreria R.

Per usare questa libreria in R, è possibile avviare una sessione R interattiva digitando semplicemente **R** nella shell e quindi caricando la libreria.

Ecco un semplice esempio eseguibile al prompt di R:

    library(xgboost)

    data(agaricus.train, package='xgboost')
    data(agaricus.test, package='xgboost')
    train <- agaricus.train
    test <- agaricus.test
    bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                    eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
    pred <- predict(bst, test$data)

Per eseguire la riga di comando di xgboost, ecco i comandi da eseguire nella shell:

    cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
    cd xgboostdemo
    xgboost mushroom.conf


Nella directory specificata viene scritto un file con estensione model. Altre informazioni su questa demo sono disponibili [su GitHub](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Per altre informazioni su xgboost, vedere la [pagina della documentazione di xgboost](https://xgboost.readthedocs.org/en/latest/) e il relativo [repository GitHub](https://github.com/dmlc/xgboost).

#### <a name="rattle"></a>Rattle
Rattle (**R** **A**nalytical **T**ool **T**o **L**earn **E**asily) usa la funzionalità di esplorazione e modellazione dei dati basate su GUI. Presenta riepiloghi statistici e visivi dei dati, trasforma i dati che possono essere modellati facilmente, compila modelli con e senza supervisione dai dati, presenta graficamente le prestazioni dei modelli e assegna un punteggio ai nuovi set di dati. Genera anche codice R replicando le operazioni nell'interfaccia utente che possono essere eseguite direttamente in R o usate come punto di partenza per altre analisi.

Per eseguire Rattle, è necessario aprire una sessione di accesso desktop con interfaccia grafica. Nel terminale digitare ```R``` per accedere all'ambiente R. Al prompt di R immettere i comandi seguenti:

    library(rattle)
    rattle()

Si apre un'interfaccia grafica con un set di schede. Qui è disponibile una procedura di avvio rapido in Rattle per l'uso di un set di dati meteo di esempio e la compilazione di un modello. In alcune delle procedure illustrate di seguito viene chiesto di installare automaticamente e caricare alcuni pacchetti R necessari non ancora disponibili nel sistema.

> [!NOTE]
> Se non si dispone di accesso per l'installazione del pacchetto nella directory predefinita, è possibile che venga visualizzata la richiesta sulla console R di Windows per l'installazione dei pacchetti nella libreria personale. Se vengono visualizzate queste richieste, rispondere *y* (Sì).
> 
> 

1. Fare clic su **Execute**.
2. Viene visualizzata una finestra di dialogo in cui viene chiesto se si vuole usare il set di dati meteo di esempio. Fare clic su **Yes** (Sì) per caricare l'esempio.
3. Fare clic sulla scheda **Model** (Modello).
4. Fare clic su **Execute** (Esegui) per compilare un albero delle decisioni.
5. Fare clic su **Draw** (Progetta) per visualizzare l'albero delle decisioni.
6. Fare clic sul pulsante di opzione **Foresta** e quindi su **Esegui** per creare una foresta casuale.
7. Fare clic sulla scheda **Evaluate** (Valuta).
8. Fare clic sul pulsante di opzione **Rischio** e quindi su **Esegui** per visualizzare due tracciati delle prestazioni per Rischio (Cumulativo).
9. Fare clic sulla scheda **Log** (Log) per visualizzare il codice R generato per le operazioni precedenti.
   (A causa di un bug nella versione corrente di Rattle, è necessario inserire un carattere *#* davanti a *Esporta il log...* nel testo del log).
10. Fare clic sul pulsante **Esporta** per salvare il file di script R denominato *weather_script.R* nella home directory.

È possibile uscire da Rattle e da R. A questo punto è possibile modificare lo script R generato o usarlo così com'è, in qualsiasi momento, per ripetere tutte le operazioni eseguite nell'interfaccia utente di Rattle. Questo è un modo semplice, specialmente per gli utenti meno esperti di R, di eseguire analisi e accedere all'apprendimento automatico rapidamente in un'interfaccia grafica semplice, generando automaticamente codice in R per apportare modifiche e/o apprendere.

## <a name="next-steps"></a>Passaggi successivi
Ecco come è possibile continuare l'apprendimento e l'esplorazione:

* La procedura dettagliata [Data science on the Linux Data Science Virtual Machine](machine-learning-data-science-linux-dsvm-walkthrough.md) (Analisi scientifica dei dati nella macchina virtuale Linux Data Science) illustra come eseguire diverse attività comuni di analisi scientifica dei dati con la macchina virtuale Data Science Linux qui descritta. 
* Esaminare e provare i vari strumenti di analisi scientifica dei dati descritti in questo articolo nella VM di analisi scientifica dei dati. È anche possibile eseguire *dsvm-more-info* nella shell della macchina virtuale per un'introduzione di base e per visualizzare collegamenti ad altre informazioni sugli strumenti installati nella VM.  
* Informazioni su come creare sistematicamente soluzioni analitiche end-to-end usando il [Processo di analisi scientifica dei dati per i team](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).
* Visitare la pagina relativa a [Cortana Analytics Gallery](http://gallery.cortanaanalytics.com) per esempi di Machine Learning e di analisi dei dati tramite Cortana Analytics Suite.


