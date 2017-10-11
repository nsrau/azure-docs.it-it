---
title: Ottimizzazione distribuita di iperparametri con Azure Machine Learning Workbench | Microsoft Docs
description: Questo scenario mostra come eseguire l'ottimizzazione distribuita di iperparametri usando Azure Machine Learning Workbench
services: machine-learning
author: pechyony
ms.service: machine-learning
ms.topic: article
ms.author: dmpechyo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.date: 09/20/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 643cea5cc134a2eb25a0dec4abefd9edca726332
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="distributed-tuning-of-hyperparameters-using-azure-machine-learning-workbench"></a>Ottimizzazione distribuita di iperparametri con Azure Machine Learning Workbench

Questo scenario mostra come usare Azure Machine Learning Workbench per scalare orizzontalmente l'ottimizzazione degli iperparametri di algoritmi di apprendimento automatico che implementano l'API scikit-learn. Viene illustrato come configurare e usare un contenitore Docker remoto e un cluster Spark come back-end di esecuzione per l'ottimizzazione degli iperparametri.

## <a name="link-of-the-gallery-github-repository"></a>Collegamento al repository GitHub delle raccolte
Di seguito è disponibile il collegamento al repository GitHub pubblico: 

[https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning)

## <a name="use-case-overview"></a>Panoramica del caso d'uso

Molti algoritmi di apprendimento automatico hanno uno o più controlli, detti iperparametri. Questi controlli consentono di regolare gli algoritmi per ottimizzarne le prestazioni sui dati futuri, in base a metriche specificate dall'utente, ad esempio, accuratezza, RMSE (radice errore quadratico medio) e AUC (area sottesa della curva). I data scientist devono fornire i valori degli iperparametri quando creano un modello in base ai dati di training e prima di visualizzare i dati di test futuri. In che modo, in base ai dati di training noti, è possibile configurare i valori degli iperparametri in modo che il modello abbia buone prestazioni sui dati di test non noti? 

Una tecnica comune per l'ottimizzazione degli iperparametri consiste in una *ricerca a griglia* combinata con la *convalida incrociata*. La convalida incrociata è una tecnica che valuta l'accuratezza con cui un modello, di cui è stato eseguito il training in base a un set di training, esegue le stime sul set di test. Usando questa tecnica, inizialmente il set di dati viene diviso in K parti e quindi viene eseguito il training dell'algoritmo K volte, in base a uno schema round robin su tutte le parti eccetto una, che viene esclusa. Si calcola il valore medio delle metriche di K modelli sulle K parti escluse. Questo valore medio, detto *stima delle prestazioni con convalida incrociata*, dipende dai valori degli iperparametri usati per la creazione di K modelli. Quando si ottimizzano gli iperparametri, si cerca nello spazio dei valori degli iperparametri candidati per trovare quelli che consentono di ottimizzare la stima delle prestazioni della convalida incrociata. La ricerca a griglia è una tecnica comune di ricerca, in cui lo spazio dei valori candidati di più iperparametri è un prodotto incrociato di un set di valori candidati dei singoli iperparametri. 

La ricerca a griglia con la convalida incrociata può richiedere molto tempo. Se un algoritmo ha 5 iperparametri, ognuno con 5 valori candidati e si usano K=5 parti, per completare una ricerca a griglia è necessario eseguire il training di 5<sup>6</sup>=15625 modelli. Fortunatamente, la ricerca a griglia con convalida incrociata è una procedura con un elevatissimo livello di parallelismo e il training di tutti questi modelli può essere eseguito in parallelo.

## <a name="prerequisites"></a>Prerequisiti

* Un [account di Azure](https://azure.microsoft.com/free/) (sono disponibili versioni di valutazione gratuite).
* Una copia di [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) installata seguendo la [guida introduttiva all'installazione e alla creazione](./quickstart-installation.md) per installare Workbench e creare gli account.
* Questo scenario presuppone che Azure ML Workbench sia in esecuzione in un computer Windows 10 o MacOS con il motore Docker installato in locale. 
* Per eseguire lo scenario con un contenitore Docker remoto, effettuare il provisioning di una macchina virtuale di data science (DSVM) Ubuntu seguendo le [istruzioni](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-data-science-provision-vm). È consigliabile usare una macchina virtuale con almeno 8 core e 28 GB di memoria. Le istanze D4 delle macchine virtuali offrono questa capacità. 
* Per eseguire questo scenario con un cluster Spark, effettuare il provisioning del cluster HDInsight seguendo le [istruzioni](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters). È consigliabile usare un cluster con almeno sei nodi del ruolo di lavoro e almeno 8 core e 28 GB di memoria sia nel modo head che nei nodi del ruolo di lavoro. Le istanze D4 delle macchine virtuali offrono questa capacità. Per ottimizzare le prestazioni del cluster, si consiglia di modificare i parametri spark.executor.instances, spark.executor.cores e spark.executor.memory seguendo le [istruzioni](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-resource-manager) e modificando le definizioni nella sezione relativa alle impostazioni predefinite di Spark personalizzate.

     **Risoluzione dei problemi**: la sottoscrizione di Azure potrebbe prevedere una quota per il numero di core che è possibile usare. Il portale di Azure non consente la creazione di cluster con un numero totale di core superiore alla quota. Per individuare la quota, andare alla sezione Sottoscrizioni nel portale di Azure, fare clic sulla sottoscrizione usata per distribuire un cluster e quindi fare clic su **Utilizzo e quote**. In genere, le quote sono definite per ogni area di Azure ed è possibile scegliere di distribuire il cluster Spark in un'area in cui c'è un numero sufficiente di core liberi. 

* Creare un account di archiviazione di Azure per l'archiviazione del set di dati. Seguire le [istruzioni](https://docs.microsoft.com/en-us/azure/storage/common/storage-create-storage-account) per creare un account di archiviazione.

## <a name="data-description"></a>Descrizione dei dati

Viene usato il [set di dati TalkingData](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data). Questo set di dati include eventi delle app nei telefoni cellulari. L'obiettivo è quello di stimare sesso ed età degli utenti di telefoni cellulari in base al tipo di telefono e agli eventi recenti generati dall'utente.  

## <a name="scenario-structure"></a>Struttura dello scenario
Questo scenario ha più cartelle nel repository GitHub. I file di codice e configurazione si trovano nella cartella **Code**, tutta la documentazione si trova nella cartella **Docs** e tutte le immagini si trovano nella cartella **Images**. La cartella radice include un file leggimi che contiene un breve riepilogo dello scenario.

### <a name="getting-started"></a>Introduzione
Fare clic sull'icona di Azure Machine Learning Workbench per eseguire la soluzione e creare un progetto dal modello "Distributed Tuning of Hyperparameters" (Ottimizzazione distribuita di iperparametri). È possibile trovare istruzioni dettagliate su come creare un nuovo progetto nella [guida introduttiva all'installazione e alla creazione](quickstart-installation.md).   

### <a name="configuration-of-execution-environments"></a>Configurazione degli ambienti di esecuzione
Viene illustrato come eseguire il codice in un contenitore Docker remoto e in un cluster Spark. Si inizia con la descrizione delle impostazioni comuni a entrambi gli ambienti. 

Vengono usati i pacchetti [scikit-learn](https://anaconda.org/conda-forge/scikit-learn), [xgboost](https://anaconda.org/conda-forge/xgboost) e [azure-storage](https://pypi.python.org/pypi/azure-storage) che non sono forniti nel contenitore Docker predefinito di Azure Machine Learning Workbench. L'installazione del pacchetto azure-storage richiede l'installazione dei pacchetti [cryptography](https://pypi.python.org/pypi/cryptography) e [azure](https://pypi.python.org/pypi/azure). Per installare questi pacchetti nel contenitore Docker e nei nodi del cluster Spark, si modifica il file conda_dependencies.yml:

    name: project_environment
    channels:
      - conda-forge
    dependencies:
      - python=3.5.2
      - scikit-learn
      - xgboost
      - pip:
        - cryptography
        - azure
        - azure-storage

Il file conda\_dependencies.yml modificato è archiviato nella directory aml_config dell'esercitazione. 

Nei passaggi successivi l'ambiente di esecuzione viene connesso all'account di Azure. Aprire la finestra di interfaccia della riga di comando facendo clic sul menu File nell'angolo superiore destro di Azure Machine Learning Workbench e scegliendo il comando per l'apertura del prompt dei comandi. Eseguire quindi nell'interfaccia della riga di comando:

    az login

Viene visualizzato un messaggio:

    To sign in, use a web browser to open the page https://aka.ms/devicelogin and enter the code <code> to authenticate.

Passare a questa pagina Web, immettere il codice e accedere all'account di Azure. Dopo questo passaggio, eseguire nell'interfaccia della riga di comando:

    az account list -o table

e trovare l'ID della sottoscrizione di Azure con l'account di Azure Machine Learning Workbench. Infine, eseguire nell'interfaccia della riga di comando:

    az account set -s <subscription ID>

per completare la connessione alla sottoscrizione di Azure.

Nelle due sezioni successive viene illustrato come completare la configurazione di un contenitore Docker remoto e di un cluster Spark.

#### <a name="configuration-of-remote-docker-container"></a>Configurazione del contenitore Docker remoto

 Per configurare un contenitore Docker remoto, eseguire nell'interfaccia della riga di comando:

    az ml computetarget attach --name dsvm --address <IP address> --username <username> --password <password> --type remotedocker

con indirizzo IP, nome utente e password della macchina virtuale di data science (DSVM). L'indirizzo IP della macchina virtuale di data science è disponibile nella sezione di panoramica della pagina relativa alla macchina stessa nel portale di Azure:

![IP della VM](media/scenario-distributed-tuning-of-hyperparameters/vm_ip.png)

#### <a name="configuration-of-spark-cluster"></a>Configurazione del cluster Spark

Per configurare l'ambiente Spark, eseguire nell'interfaccia della riga di comando:

    az ml computetarget attach --name spark --address <cluster name>-ssh.azurehdinsight.net  --username <username> --password <password> --type cluster

con il nome del cluster e la password e il nome utente SSH del cluster. Il valore predefinito del nome utente SSH è `sshuser`, a meno che non sia stato modificato durante il provisioning del cluster. Il nome del cluster è indicato nella sezione delle proprietà nella pagina del cluster nel portale di Azure:

![Nome del cluster](media/scenario-distributed-tuning-of-hyperparameters/cluster_name.png)

Si usa il pacchetto spark-sklearn per avere Spark come ambiente di esecuzione per l'ottimizzazione distribuita degli iperparametri. Il file spark_dependencies.yml è stato modificato per installare questo pacchetto quando viene usato l'ambiente di esecuzione Spark:

    configuration: {}
    repositories:
      - "https://mmlspark.azureedge.net/maven"
      - "https://spark-packages.org/packages"
    packages:
      - group: "com.microsoft.ml.spark"
        artifact: "mmlspark_2.11"
        version: "0.7"
      - group: "databricks"
        artifact: "spark-sklearn"
        version: "0.2.0"

Il file spark\_dependencies.yml modificato è archiviato nella directory aml_config dell'esercitazione. 

### <a name="data-ingestion"></a>Inserimento di dati
Il codice in questo scenario presuppone che i dati siano archiviati nel servizio Archiviazione BLOB di Azure. Prima di tutto viene illustrato come scaricare i dati dal sito Kaggle nel computer locale e caricarli nell'archiviazione BLOB. Quindi viene illustrato come leggere i dati dall'archiviazione BLOB. 

Per scaricare i dati da Kaggle, visitare la [pagina del set di dati](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data) e fare clic sul pulsante Download. Verrà chiesto di accedere a Kaggle. Dopo l'accesso, si verrà reindirizzati alla pagina del set di dati. Scaricare quindi i primi sette file nella colonna sinistra selezionandoli e facendo clic sul pulsante Download. Le dimensioni totali dei file scaricati sono di 289 MB. Per caricare i file nell'archiviazione BLOB, creare il contenitore di archiviazione BLOB denominato "dataset" nell'account di archiviazione. A tale scopo, passare alla pagina di Azure dell'account di archiviazione, fare clic su BLOB e quindi su +Contenitore. Immettere "dataset" come nome e fare clic su OK. Gli screenshot seguenti illustrano questi passaggi:

![Apertura del BLOB](media/scenario-distributed-tuning-of-hyperparameters/open_blob.png)
![Apertura del contenitore](media/scenario-distributed-tuning-of-hyperparameters/open_container.png)

Selezionare quindi il contenitore denominato dataset nell'elenco e fare clic sul pulsante Carica. Il portale di Azure consente di caricare più file contemporaneamente. Nella sezione "Carica BLOB" fare clic sul pulsante a forma di cartella, selezionare tutti i file del set di dati, fare clic su Apri e quindi fare clic su Carica. Lo screenshot seguente illustra questi passaggi:

![Caricamento del BLOB](media/scenario-distributed-tuning-of-hyperparameters/upload_blob.png) 

Il caricamento dei file richiede alcuni minuti, a seconda della connessione Internet. 

Nel codice viene usato [Azure Storage SDK](https://azure-storage.readthedocs.io/en/latest/) per scaricare il set di dati dall'archiviazione BLOB nell'ambiente di esecuzione corrente. Il download viene eseguito nella funzione load\_data() del file load_data.py. Per usare questo codice, è necessario sostituire <ACCOUNT_NAME> e <ACCOUNT_KEY> con il nome e la chiave primaria dell'account di archiviazione che ospita il set di dati. Il nome dell'account è indicato nell'angolo superiore sinistro della pagina di Azure relativa all'account di archiviazione. Per ottenere la chiave dell'account, selezionare Chiavi di accesso nella pagina di Azure dell'account di archiviazione (vedere il primo screenshot nella sezione relativa all'inserimento dei dati) e quindi copiare la lunga stringa nella prima riga di colonne chiave:
 
![Chiave di accesso](media/scenario-distributed-tuning-of-hyperparameters/access_key.png)

Il codice seguente della funzione load_data() scarica un singolo file:

    from azure.storage.blob import BlockBlobService

    # Define storage parameters 
    ACCOUNT_NAME = "<ACCOUNT_NAME>"
    ACCOUNT_KEY = "<ACCOUNT_KEY>"
    CONTAINER_NAME = "dataset"

    # Define blob service     
    my_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # Load blob
    my_service.get_blob_to_path(CONTAINER_NAME, 'app_events.csv.zip', 'app_events.csv.zip')

Si noti che non è necessario eseguire manualmente il file load_data.py. Più avanti, lo si chiamerà da altri file.

### <a name="feature-engineering"></a>Progettazione delle funzioni
Il codice per il calcolo di tutte le funzionalità è nel file feature\_engineering.py. Non è necessario eseguire manualmente il file feature_engineering.py. Più avanti, lo si chiamerà da altri file.

Vengono creati più set di funzionalità:
* Codifica one-hot di marca e modello del telefono cellulare (funzione one\_hot\_brand_model)
* Frazione degli eventi generati dall'utente in ogni giorno della settimana (funzione weekday\_hour_features)
* Frazione degli eventi generati dall'utente in ogni ora (funzione weekday\_hour_features)
* Frazione degli eventi generati dall'utente in ogni combinazione di giorno della settimana e ora (funzione weekday\_hour_features)
* Frazione degli eventi generati dall'utente in ogni app (funzione one\_hot\_app_labels)
* Frazione degli eventi generati dall'utente in ogni etichetta di app (funzione one\_hot\_app_labels)
* Frazione degli eventi generati dall'utente in ogni categoria di app (funzione text\_category_features)
* Funzionalità di indicatore per le categorie di app usate per generare gli eventi (funzione one\_hot_category)

Queste funzionalità sono state ispirate dal kernel Kaggle [A linear model on apps and labels](https://www.kaggle.com/dvasyukova/a-linear-model-on-apps-and-labels) (Modello lineare per app ed etichette).

Il calcolo di queste funzionalità richiede una notevole quantità di memoria. Inizialmente è stato eseguito un tentativo di calcolare le funzionalità nell'ambiente locale con 16 GB di RAM. È stato possibile calcolare i primi quattro set di funzionalità, ma durante il calcolo del quinto set di funzionalità è stato visualizzato un errore di memoria esaurita. Il calcolo dei primi quattro set di funzionalità avviene nel file singleVMsmall.py e può essere effettuato nell'ambiente locale eseguendo: 

     az ml experiment submit -c local .\singleVMsmall.py   

nella finestra di interfaccia della riga di comando.

Poiché l'ambiente locale è troppo piccolo per calcolare tutti i set di funzionalità, si passa alla macchina virtuale di data science remota, che ha una maggiore quantità di memoria. L'esecuzione nella macchina virtuale di data science avviene all'interno del contenitore Docker gestito da Azure Machine Learning Workbench. Usando questa macchina virtuale di data science è possibile calcolare tutte le funzionalità, oltre che eseguire il training dei modelli e ottimizzare gli iperparametri (vedere la sezione successiva). Il file singleVM.py contiene codice di modellazione e calcolo delle funzionalità completo. Nella sezione successiva verrà illustrato come eseguire singleVM.py nella macchina virtuale di data science remota. 

### <a name="tuning-hyperparameters-using-remote-dsvm"></a>Ottimizzazione degli iperparametri tramite la macchina virtuale di data science remota
Viene usata l'implementazione [xgboost](https://anaconda.org/conda-forge/xgboost) [1] del potenziamento del gradiente di alberi. Si usa il pacchetto [scikit-learn](http://scikit-learn.org/) per ottimizzare gli iperparametri di xgboost. Sebbene xgboost non faccia parte del pacchetto scikit-learn, implementa l'API scikit-learn e quindi può essere usato con le funzioni di ottimizzazione degli iperparametri di scikit-learn. 

Xgboost ha otto iperparametri:
* n_esitmators
* max_depth
* reg_alpha
* reg_lambda
* colsample\_by_tree
* learning_rate
* colsample\_by_level
* subsample
* objective. Una descrizione di questi iperparametri è disponibile [qui](http://xgboost.readthedocs.io/en/latest/python/python_api.html#module-xgboost.sklearn) e [qui](https://github.com/dmlc/xgboost/blob/master/doc/parameter.md). Inizialmente si usa una macchina virtuale di data science remota e si ottimizzano gli iperparametri da una piccola griglia di valori candidati:

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1],'learning_rate': [0.1], 'colsample_bylevel': [0.1,], 'subsample': [0.5]}]  

La griglia ha quattro combinazioni di valori degli iperparametri. Si usa la convalida incrociata in 5 parti, che comporta 4x5=20 esecuzioni di xgboost. Per misurare le prestazioni dei modelli, si usa una metrica di perdita logaritmica negativa. Il codice seguente trova i valori degli iperparametri nella griglia che ottimizzano la perdita logaritmica negativa con convalida incrociata. Il codice usa inoltre questi valori per il training del modello finale sul set di training completo:

    clf = XGBClassifier(seed=0)
    metric = 'neg_log_loss'
    
    clf_cv = GridSearchCV(clf, tuned_parameters, scoring=metric, cv=5, n_jobs=8)
    model = clf_cv.fit(X_train,y_train)

Dopo aver creato il modello, si salvano i risultati dell'ottimizzazione degli iperparametri. Si usa l'API di registrazione di Azure Machine Learning Workbench per salvare i valori migliori degli iperparametri e la corrispondente stima della perdita logaritmica negativa con convalida incrociata:

    from azureml.logging import get_azureml_logger

    # initialize logger
    run_logger = get_azureml_logger()

    ...

    run_logger.log(metric, float(clf_cv.best_score_))

    for key in clf_cv.best_params_.keys():
        run_logger.log(key, clf_cv.best_params_[key]) 

Si crea inoltre il file sweeping_results.txt con le perdite logaritmiche negative con convalida incrociata di tutte le combinazioni di valori degli iperparametri nella griglia:

    if not path.exists('./outputs'):
        makedirs('./outputs')
    outfile = open('./outputs/sweeping_results.txt','w')

    print("metric = ", metric, file=outfile)
    for i in range(len(model.grid_scores_)):
        print(model.grid_scores_[i], file=outfile)
    outfile.close()

Il file è archiviato in una speciale cartella ./outputs. Più avanti verrà illustrato come scaricarlo.  

 Prima di eseguire singleVM.py nella macchina virtuale di data science remota per la prima volta, si crea un contenitore Docker eseguendo: 

    az ml experiment prepare -c dsvm

nell'interfaccia della riga di comando di Windows. Per la creazione del contenitore sono necessari alcuni minuti. Al termine, si esegue singleVM.py nella macchina virtuale di data science:

    az ml experiment submit -c dsvm .\singleVM.py

Questo comando richiede 1 ora e 38 minuti per il completamento se la macchina virtuale di data science ha 8 core e 28 GB di memoria. I valori registrati possono essere visualizzati nella finestra della cronologia di esecuzione di Azure Machine Learning Workbench:

![Cronologia di esecuzione](media/scenario-distributed-tuning-of-hyperparameters/run_history.png)

Per impostazione predefinita, la finestra della cronologia di esecuzione mostra valori e grafici dei primi 1-2 valori registrati. Per visualizzare l'elenco completo dei valori scelti degli iperparametri, fare clic sull'icona delle impostazioni contrassegnata con un cerchio rosso nello screenshot precedente e selezionare gli iperparametri da visualizzare nella tabella. Inoltre, per selezionare i grafici visualizzati nella parte superiore della finestra della cronologia di esecuzione, fare clic sull'icona delle impostazioni contrassegnata con un cerchio blu e selezionare i grafici nell'elenco. 

I valori scelti degli iperparametri possono anche essere esaminati nella finestra delle proprietà di esecuzione: 

![Proprietà di esecuzione](media/scenario-distributed-tuning-of-hyperparameters/run_properties.png)

Nell'angolo superiore destro della finestra delle proprietà di esecuzione c'è una sezione relativa ai file di output con l'elenco di tutti i file creati nella cartella ".\output" nell'ambiente di esecuzione. È possibile scaricare sweep\_results.txt da questa posizione, selezionando il file e facendo clic sul pulsante Download. L'output di sweeping_results.txt dovrebbe essere il seguente:

    metric =  neg_log_loss
    mean: -2.29096, std: 0.03748, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28712, std: 0.03822, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28706, std: 0.03863, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}
    mean: -2.28530, std: 0.03927, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}

### <a name="tuning-hyperparameters-using-spark-cluster"></a>Ottimizzazione degli iperparametri tramite il cluster Spark
Viene usato il cluster Spark per scalare orizzontalmente l'ottimizzazione degli iperparametri e usare una griglia più grande. La nuova griglia è:

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1], 'learning_rate': [0.1], 'colsample_bylevel': [0.01, 0.1], 'subsample': [0.5, 0.7]}]

Questa griglia ha 16 combinazioni di valori degli iperparametri. Poiché si usa la convalida incrociata in 5 parti, si esegue xgboost 16x5=80 volte.

Il pacchetto scikit-learn non ha il supporto nativo per l'ottimizzazione degli iperparametri tramite il cluster Spark. Fortunatamente, a questo scopo è disponibile il pacchetto [spark-sklearn](https://spark-packages.org/package/databricks/spark-sklearn) di Databricks. Questo pacchetto fornisce la funzione GridSearchCV, che ha circa la stessa API della funzione GridSearchCV in scikit-learn. Per usare spark-sklearn e ottimizzare gli iperparametri con Spark è necessario connettersi per creare il contesto di Spark

    from pyspark import SparkContext
    sc = SparkContext.getOrCreate()

Sostituire quindi: 

    from sklearn.model_selection import GridSearchCV

con 

    from spark_sklearn import GridSearchCV

Sostituire inoltre la chiamata a GridSearchCV da scikit-learn in quella da spark-sklearn:

    clf_cv = GridSearchCV(sc = sc, param_grid = tuned_parameters, estimator = clf, scoring=metric, cv=5)

Il codice finale per l'ottimizzazione degli iperparametri con Spark si trova nel file distributed\_sweep.py. La differenza tra singleVM.py e distributed_sweep.py è nella definizione della griglia e nelle quattro righe di codice aggiuntive. Si noti anche che a causa dei servizi di Azure Machine Learning Workbench, il codice di registrazione non cambia quando si cambia l'ambiente di esecuzione dalla macchina virtuale di data science remota al cluster Spark.

Prima di eseguire distributed_sweep.py nel cluster Spark per la prima volta, è necessario installare i pacchetti Python in questa posizione. A tale scopo, eseguire: 

    az ml experiment prepare -c spark

nell'interfaccia della riga di comando di Windows. L'installazione richiede alcuni minuti. Eseguire quindi distributed_sweep.py nel cluster Spark:

    az ml experiment submit -c spark .\distributed_sweep.py

Questo comando richiede 1 ora e 6 minuti per il completamento se il cluster Spark ha 6 nodi del ruolo di lavoro e 28 GB di memoria. È possibile accedere ai risultati dell'ottimizzazione degli iperparametri nel cluster Spark, ovvero log, valori migliori degli iperparametri e file sweeping_results.txt, in Azure Machine Learning Workbench analogamente a quanto avviene con l'esecuzione nella macchina virtuale di data science remota. 

### <a name="architecture-diagram"></a>Diagramma dell'architettura

Il diagramma seguente illustra il flusso di lavoro end-to-end: ![architettura](media/scenario-distributed-tuning-of-hyperparameters/architecture.png) 

## <a name="conclusion"></a>Conclusioni 

In questo scenario è stato illustrato come usare Azure Machine Learning Workbench per eseguire l'ottimizzazione di iperparametri nella macchina virtuale remota e nel cluster Spark remoto. È stato illustrato come Azure Machine Learning Workbench fornisca strumenti che semplificano la configurazione degli ambienti di esecuzione e il passaggio tra di essi. 

## <a name="references"></a>Riferimenti

[1] T. Chen and C. Guestrin. [XGBoost: A Scalable Tree Boosting System](https://arxiv.org/abs/1603.02754) (XGBoost: Sistema scalabile di potenziamento degli alberi). KDD 2016.





