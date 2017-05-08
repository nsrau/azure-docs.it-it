---
title: Effettuare il provisioning di una macchina virtuale per l&quot;analisi scientifica dei dati per Linux (Ubuntu) in Azure | Microsoft Docs
description: "Configurare e creare una macchina virtuale per l&quot;analisi scientifica dei dati per Linux (Ubuntu) in Azure per attività di analisi e Machine Learning."
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
ms.date: 03/24/2017
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: c29e31f59acac56d6ec522e1e7946238a6e22aa5
ms.lasthandoff: 04/20/2017


---
# <a name="provision-the-data-science-virtual-machine-for-linux-ubuntu"></a>Effettuare il provisioning di una macchina virtuale per l'analisi scientifica dei dati per Linux (Ubuntu)
La macchina virtuale per l'analisi scientifica dei dati per Linux è un'immagine di macchina virtuale basata su Ubuntu che consente di iniziare a usare in modo semplice l'apprendimento avanzato in Azure. Gli strumenti di apprendimento avanzato includono:

  * [Caffe](http://caffe.berkeleyvision.org/): infrastruttura di apprendimento avanzato creata per la velocità, l'espressività e la modularità
  * [Caffe2](https://github.com/caffe2/caffe2): versione multipiattaforma di Caffe
  * [Computational Network Toolkit (CNTK)](https://github.com/Microsoft/CNTK): toolkit software di apprendimento avanzato sviluppato da Microsoft Research
  * [H2O](https://www.h2o.ai/): piattaforma Big Data e interfaccia utente grafica open source
  * [Keras](https://keras.io/): API per reti neurali di alto livello in Python per Theano e TensorFlow
  * [MXNet](http://mxnet.io/): libreria di apprendimento avanzato flessibile ed efficiente con numerosi binding di linguaggio
  * [NVIDIA DIGITS](https://developer.nvidia.com/digits): sistema grafico che semplifica le attività di apprendimento avanzato comuni
  * [TensorFlow](https://www.tensorflow.org/): libreria open source di Google per l'intelligenza artificiale
  * [Theano](http://deeplearning.net/software/theano/): libreria Python per la definizione, ottimizzazione e la valutazione efficiente di espressioni matematiche che prevedono matrici multidimensionali
  * [Torch](http://torch.ch/): framework di calcolo scientifico con ampio supporto per algoritmi di Machine Learning
  * CUDA, cuDNN e driver NVIDIA
  * Molti notebook di Jupyter di esempio

Tutte le librerie sono le versioni GPU, anche se possono funzionare anche sulla CPU.

La macchina virtuale per l'analisi scientifica dei dati per Linux contiene anche strumenti di ampia diffusione per attività di sviluppo e di analisi scientifica dei dati, tra cui:

* Microsoft R Server Developer Edition con Microsoft R Open
* Distribuzione di Anaconda Python, versioni 2.7 e 3.5, incluse le più comuni librerie di analisi dei dati
* JuliaPro: un'accurata distribuzione di linguaggio Julia con librerie scientifiche e dati di analitica diffuse
* Istanza Spark univoca e un solo nodo Hadoop (HDFS, Yarn)
* JupyterHub: un server notebook Jupyter multiutente che supporta kernel R, Python, PySpark e Julia
* Azure Storage Explorer
* Interfaccia della riga di comando di Azure per la gestione delle risorse di Azure
* Strumenti di Machine Learning
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): sistema di Machine Learning rapido che supporta tecniche come hash, allreduce, reduction, learning2search, nonché apprendimento online, attivo e interattivo
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): strumento che consente un'implementazione di albero con boosting rapida e accurata
  * [Rattle](http://rattle.togaware.com/): strumento grafico che consente di iniziare a usare in modo semplice analisi dei dati e Machine Learning in R
  * [LightGBM](https://github.com/Microsoft/LightGBM): framework rapido, distribuito e a prestazioni elevate di boosting a gradienti
* Azure SDK in Java, Python, Node.js, Ruby, PHP
* Librerie in R e Python da usare in Azure Machine Learning e altri servizi di Azure
* Editor e strumenti di sviluppo (RStudio, PyCharm, IntelliJ, Emacs, vim)


L'esecuzione dell'analisi scientifica dei dati comporta l'iterazione di una sequenza di attività quali:

1. Ricerca, caricamento e pre-elaborazione dei dati
2. Compilazione e test di modelli
3. Distribuzione dei modelli per l'uso in applicazioni intelligenti

Gli esperti di dati usano vari strumenti per completare queste attività. Trovare le versioni del software appropriate e quindi scaricarle, compilarle e installarle può essere un'operazione molto dispersiva in termini di tempo.

La macchina virtuale per l'analisi scientifica dei dati per Linux può rendere queste attività sostanzialmente più facili. Usarla per avviare rapidamente il progetto di analisi. Consente di svolgere attività in diversi linguaggi, ad esempio R, Python, SQL, Java e C++. Azure SDK, incluso nella VM, consente di compilare le applicazioni usando vari servizi in Linux sulla piattaforma cloud di Microsoft. È anche possibile accedere ad altri linguaggi, come Ruby, Perl, PHP e Node. js, anch'essi pre-installati.

Per questa immagine di VM per l'analisi scientifica dei dati non sono previsti costi per il software. Si pagano solo le spese d'uso dell'hardware di Azure valutate in base alle dimensioni della macchina virtuale di cui si esegue il provisioning. Altre informazioni sui costi di calcolo sono disponibili alla [pagina con l'elenco delle VM in Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Altre versioni della macchina virtuale per l'analisi scientifica dei dati
È disponibile anche un'immagine [CentOS](machine-learning-data-science-linux-dsvm-intro.md), con molti degli stessi strumenti dell'immagine Ubuntu. È disponibile anche un'immagine [Windows](machine-learning-data-science-provision-vm.md).

## <a name="prerequisites"></a>Prerequisiti
Prima di creare una macchina virtuale per l'analisi scientifica dei dati per Linux è necessario avere una sottoscrizione di Azure. Per ottenerne una, vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/free/).

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Creare la macchina virtuale per l'analisi scientifica dei dati per Linux
Di seguito sono elencati i passaggi per la creazione di un'istanza della macchina virtuale per l'analisi scientifica dei dati per Linux:

1. Passare all'elenco di macchine virtuali nel [portale di Azure](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vm-ubuntulinuxdsvmubuntu).
2. Fare clic su **Crea** (in basso) per aprire la procedura guidata.![configure-data-science-vm](./media/machine-learning-data-science-dsvm-ubuntu-intro/configure-data-science-virtual-machine.png)
3. Le sezioni seguenti forniscono gli input per ognuno dei passaggi (elencati nella parte destra della figura precedente) della procedura guidata usata per creare la macchina virtuale per l'analisi scientifica dei dati di Microsoft. Di seguito sono riportati gli input necessari per configurare ciascuno di questi passaggi:
   
   a. **Nozioni di base**:
   
   * **Name**: nome del server di analisi scientifica dei dati che si sta creando.
   * **Nome utente**: primo ID di accesso dell'account.
   * **Password**: la prima password dell'account. È possibile usare una chiave pubblica SSH invece di una password.
   * **Sottoscrizione**: se si ha più di una sottoscrizione, selezionare quella in cui viene creata e fatturata la macchina virtuale. È necessario disporre di privilegi di creazione delle risorse per questa sottoscrizione.
   * **Gruppo di risorse**: è possibile creare un nuovo gruppo di risorse o usarne uno esistente.
   * **Location**: selezionare la posizione del data center più appropriata. In genere è il data center che include la maggior parte dei dati o è più vicino alla posizione fisica per l'accesso più veloce alla rete.
   
   b. **Dimensione**:
   
   * Selezionare uno dei tipi di server che soddisfa i requisiti funzionali e vincoli di costo. Selezionare **Visualizza tutto** per visualizzare altre opzioni per le dimensioni delle VM. Selezionare una macchina virtuale di classe NC per il training GPU.
   
   c. **Impostazioni**:
   
   * **Tipo di disco**: se si preferisce un'unità a stato solido, scegliere **Premium**. In caso contrario, scegliere **Standard**. Le macchine virtuali GPU richiedono un disco Standard.
   * **Account di archiviazione**: è possibile creare un nuovo account di archiviazione di Azure nella sottoscrizione o usarne uno esistente nella stessa posizione scelta nel passaggio **Nozioni di base** della procedura guidata.
   * **Altri parametri**: nella maggior parte dei casi si usano semplicemente i valori predefiniti. Nel caso in cui si desideri usare valori non predefiniti, è possibile passare il puntatore sul collegamento informativo per visualizzare informazioni sui campi specifici.
   
   d. **Riepilogo**:
   
   * Verificare che tutte le informazioni immesse siano corrette.
   
   e. **Acquisto**:
   
   * Fare clic su **Acquista**per avviare il provisioning. Viene fornito un collegamento alle condizioni della transazione. La macchina virtuale non prevede costi aggiuntivi oltre a quelli per il calcolo delle dimensioni del server scelto nel passaggio **Size** .

Per il provisioning sono necessari circa 5-10 minuti. Lo stato del provisioning viene visualizzato nel portale di Azure.

## <a name="how-to-access-the-data-science-virtual-machine-for-linux"></a>Come accedere alla macchina virtuale per l'analisi scientifica dei dati per Linux
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

## <a name="tools-installed-on-the-data-science-virtual-machine-for-linux"></a>Strumenti installati nella macchina virtuale per l'analisi scientifica dei dati per Linux
### <a name="deep-learning-libraries"></a>Librerie di apprendimento avanzato

#### <a name="cntk"></a>CNTK
Microsoft Cognitive Toolkit, noto anche come CNTK, è un toolkit open source di apprendimento avanzato. Sono disponibili binding Python negli ambienti root e py35 Conda, oltre a uno strumento da riga di comando (cntk) già incluso in PATH.

I notebook di Python di esempio sono disponibili in JupyterHub. Per un esempio di base nella riga di comando, eseguire i comandi seguenti nella shell:

    cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
    cntk configFile=lr_bs.cntk makeMode=false command=Train

Per altre informazioni, vedere la sezione CNTK di [GitHub](https://github.com/Microsoft/CNTK) e la [wiki di CNTK](https://github.com/Microsoft/CNTK/wiki).

#### <a name="caffe"></a>Caffe
Caffe è un framework di apprendimento avanzato del Berkeley Vision and Learning Center. È disponibile in /opt/caffe. Esempi sono disponibili in /opt/caffe/examples.

#### <a name="h2o"></a>H2O
H2O è una piattaforma rapida, in memoria e distribuita di analisi predittiva e Machine Learning. Un pacchetto Python è installato negli ambienti root e py35 Anaconda. Viene installato anche un pacchetto R. L'interfaccia utente Web di Flow può essere avviata con "java -jar /dsvm/tools/h2o/current/h2o.jar". Passare a http://localhost:54321 per iniziare. Sono anche disponibili notebook di esempio in JupyterHub.

#### <a name="keras"></a>Keras
Keras è un'API per reti neurali di alto livello in Python che può essere eseguita su Tensorflow o Theano. È disponibile negli ambienti root e py35 Python. 

#### <a name="mxnet"></a>MXNet
MXNet è un framework di apprendimento avanzato progettato per l'efficienza e la flessibilità. Offre binding R e Python inclusi nella macchina virtuale per l'analisi scientifica dei dati. Sono disponibili notebook di esempio in JupyterHub, mentre il codice di esempio è disponibile in /dsvm/samples/mxnet.

#### <a name="nvidia-digits"></a>NVIDIA DIGITS
NVIDIA Deep Learning GPU Training System, noto come DIGITS, è un sistema che semplifica attività di apprendimento avanzato comuni come la gestione di dati, la progettazione e il training di reti neurali in sistemi GPU e il monitoraggio delle prestazioni in tempo reale con visualizzazione avanzata. 

DIGITS è disponibile come servizio denominato digits. Avviare il servizio e passare a http://localhost:5000 per iniziare.

DIGITS viene anche installato come modulo Python nell'ambiente root Conda.

#### <a name="tensorflow"></a>TensorFlow
TensorFlow è la libreria di apprendimento avanzato di Google. Si tratta di una libreria software open source per calcoli numerici con grafici del flusso di dati. TensorFlow è disponibile nell'ambiente py35 Python e alcuni notebook di esempio sono inclusi in JupyterHub.

#### <a name="theano"></a>Theano
Theano è una libreria Python per calcoli numerici efficienti. È disponibile negli ambienti root e py35 Python. 

#### <a name="torch"></a>Torch
Torch è un framework di calcolo scientifico con ampio supporto per algoritmi di Machine Learning. È disponibile in /dsvm/tools/torch, mentre la sessione interattiva e la gestione pacchetti LuaRocks sono disponibili nella riga di comando. Esempi sono disponibili in /dsvm/samples/torch.

PyTorch è disponibile nell'ambiente root Anaconda. Esempi sono disponibili in /dsvm/samples/pytorch.

### <a name="microsoft-r-server"></a>Microsoft R Server
R è uno dei linguaggi più diffusi per l'analisi dei dati e il Machine Learning. Se si vuole usare R per l'esecuzione di analisi, è necessario che nella macchina virtuale sia installato Microsoft R Server (MRS) con Microsoft R Open (MRO) con Math Kernel Library (MKL). La libreria MKL ottimizza le operazioni matematiche comuni negli algoritmi di analisi. MRO è totalmente compatibile con CRAN-R e tutte le librerie R pubblicate in CRAN possono essere installate in MRO. MRS garantisce scalabilità e messa in funzione dei modelli R nei servizi Web. È possibile modificare i programmi R in uno degli editor predefiniti, ad esempio RStudio, VI o Emacs. Se si usa l'editor Emacs, si noti che il relativo pacchetto ESS (Emacs Speaks Statistics), che semplifica l'uso dei file R all'interno dell'editor Emacs, è preinstallato.

Per avviare la console R è sufficiente digitare **R** nella shell. Verrà visualizzato un ambiente interattivo. Per sviluppare il programma R, si usa in genere un editor come Emacs o VI e quindi si eseguono gli script all'interno di R. Con RStudio si ottiene un ambiente IDE con interfaccia grafica completo per sviluppare il programma R.

È anche disponibile uno script R per installare i [20 pacchetti R più popolari](http://www.kdnuggets.com/2015/06/top-20-r-packages.html) , se necessario. Questo script può essere eseguito una volta nell'interfaccia interattiva R, a cui è possibile accedere digitando **R** nella shell, come indicato sopra.  

### <a name="python"></a>Python
Per lo sviluppo tramite Python, è installata la distribuzione Anaconda Python 2.7 e 3.5. Questa distribuzione contiene il linguaggio Python di base con circa 300 dei più diffusi pacchetti di matematica, ingegneria e analisi dei dati. È possibile usare gli editor di testo predefiniti. Si può anche usare Spyder, un IDE Python incluso nelle distribuzioni di Anaconda Python. Spyder richiede un desktop con interfaccia grafica o X11 Forwarding. Nel desktop con interfaccia grafica è disponibile un collegamento a Spyder.

Poiché sono presenti sia Python 2.7 che 3.5, è necessario attivare in modo specifico la versione di Python (ambiente conda) che si vuole usare nella sessione corrente. Il processo di attivazione imposta la variabile PATH sulla versione di Python desiderata.

Per attivare l'ambiente conda di Python 2.7, eseguire il comando seguente dalla shell:

    source /anaconda/bin/activate root

Python 2.7 viene installato in */anaconda/bin*.

Per attivare l'ambiente conda di Python 3.5, eseguire il comando seguente dalla shell:

    source /anaconda/bin/activate py35


Python 3.5 viene installato in */anaconda/envs/py35/bin*.

Per richiamare la sessione interattiva di Python, è sufficiente digitare **python** nella shell. Se si usa un'interfaccia grafica o è installato l'inoltro X11, è possibile digitare il comando **pycharm** per avviare l'IDE di PyCharm Python.

Per installare le librerie di Python aggiuntive, è necessario eseguire il comando ```conda``` o ````pip```` in sudo e specificare il percorso completo della gestione del pacchetto Python (conda o pip) per eseguire l'installazione nell'ambiente Python corretto. ad esempio:

    sudo /anaconda/bin/pip install <package> #for Python 2.7 environment
    sudo /anaconda/envs/py35/bin/pip install <package> # for Python 3.5 environment


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

### <a name="apache-spark-standalone"></a>Apache Spark autonomo 
Un'istanza autonoma di Apache Spark è preinstallata in DSVM di Linux per contribuire allo sviluppo delle applicazioni Spark inizialmente in locale, prima di eseguire i test e la distribuzione in cluster di grandi dimensioni. È possibile eseguire programmi PySpark attraverso il kernel Jupyter. Quando si apre Jupyter e si fa clic sul pulsante "Nuovo" verrà visualizzato un elenco di kernel disponibili. "Spark – Python" è il kernel PySpark che consentirà di creare applicazioni Spark usando il linguaggio Python. È anche possibile usare un IDE Python come PyCharm o Spyder per creare un programma Spark. Poiché si tratta di un'istanza autonoma, lo stack di Spark viene eseguito all'interno del programma client che esegue la chiamata. Questo rende più veloce e semplice la risoluzione dei problemi rispetto allo sviluppo in un cluster Spark. 

Su Jupyter viene assicurato un blocco di appunti di esempio PySpark che è possibile trovare nella directory "SparkML" nella home directory di Jupyter ($HOME/notebooks/SparkML/pySpark). 

Se si programma in R per Spark, è possibile usare Microsoft R Server, SparkR o sparklyr. 

Prima di eseguire nel contesto Spark in Microsoft R Server, è necessario eseguire un unico passaggio di configurazione per abilitare un solo nodo locale Hadoop HDFS e un'istanza Yarn. Per impostazione predefinita, i servizi Hadoop sono installati ma disabilitati su DSVM. Per attivarli, è necessario eseguire i comandi seguenti come radice la prima volta:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

È possibile arrestare i servizi relativi a Hadoop quando non servono eseguendo ````systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn````. un esempio che illustra come sviluppare e verificare che MRS nel contesto di Spark remoto, ovvero l'istanza di Spark autonoma nel DSVM, sia implementato e disponibile nella directory `/dsvm/samples/MRS`. 

### <a name="ides-and-editors"></a>IDE ed editor
È possibile scegliere tra diversi editor di codice, tra cui VI/VIM, Emacs, PyCharm, RStudio e IntelliJ. IntelliJ, RStudio e PyCharm sono editor grafici e per usarli è necessario essere connessi a un desktop con interfaccia grafica. Per avviare questi editor sono disponibili collegamenti di menu delle applicazioni e sul desktop.

**VIM** e **Emacs** sono editor basati su testo. In Emacs è installato un pacchetto di componenti aggiuntivi denominato Emacs Speaks Statistics (ESS) che facilita l'utilizzo di R nell'editor Emacs. Altre informazioni sono disponibili nella pagina relativa a [ESS](http://ess.r-project.org/).

**LaTex** viene installato tramite il pacchetto texlive insieme a un pacchetto di componenti aggiuntivi di Emacs, [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) , che semplifica la creazione di documenti LaTex in Emacs.  

### <a name="databases"></a>Database

#### <a name="graphical-sql-client"></a>Client SQL grafico
Il client SQL grafico **SQuirreL SQL** viene fornito per la connessione a diversi database, come Microsoft SQL Server e MySQL, e per l'esecuzione di query SQL. È possibile eseguirlo da una sessione desktop con interfaccia grafica, usando ad esempio il client X2Go. Per richiamare SQuirreL SQL è possibile avviarlo dall'icona sul desktop o eseguire il comando seguente nella shell.

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
La VM viene fornita con alcuni strumenti e algoritmi di Machine Learning precompilati e preinstallati localmente. inclusi i seguenti:

* **Vowpal Wabbit**: algoritmo di apprendimento rapido online.
* **xgboost**: strumento che fornisce algoritmi di albero con boosting ottimizzati.
* **Rattle**: strumento grafico basato su R per una semplice modellazione ed esplorazione dei dati.
* **Python**: Anaconda Python integra algoritmi Machine Learning con librerie come Scikit-learn. È possibile installare altre librerie usando il comando `pip install` .
* **LightGBM**: framework rapido, distribuito e a prestazioni elevate di boosting a gradienti basato su algoritmi dell'albero delle decisioni.
* **R**: una libreria completa di funzioni di Machine Learning disponibili per R. Alcune librerie preinstallate sono lm, glm, randomForest, rpart. Altre librerie possono essere installate eseguendo:
  
        install.packages(<lib name>)

Ecco alcune informazioni aggiuntive sui primi tre strumenti di Machine Learning nell'elenco.

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

Per altre informazioni su xgboost, vedere la [pagina della documentazione di xgboost](https://xgboost.readthedocs.org/en/latest/) e il relativo [archivio GitHub](https://github.com/dmlc/xgboost).

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

* La procedura dettagliata [Analisi scientifica dei dati in una macchina virtuale Linux per l'analisi scientifica dei dati](machine-learning-data-science-linux-dsvm-walkthrough.md) illustra come eseguire diverse attività comuni di analisi scientifica dei dati con la macchina virtuale per l'analisi scientifica dei dati per Linux di cui si è effettuato il provisioning in questo articolo. 
* Esaminare e provare i vari strumenti di analisi scientifica dei dati descritti in questo articolo nella VM di analisi scientifica dei dati. È anche possibile eseguire *dsvm-more-info* nella shell della macchina virtuale per un'introduzione di base e per visualizzare collegamenti ad altre informazioni sugli strumenti installati nella VM.  
* Informazioni su come creare sistematicamente soluzioni analitiche end-to-end usando il [Processo di analisi scientifica dei dati per i team](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).
* Visitare la pagina relativa a [Cortana Analytics Gallery](http://gallery.cortanaanalytics.com) per esempi di Machine Learning e di analisi dei dati tramite Cortana Analytics Suite.


