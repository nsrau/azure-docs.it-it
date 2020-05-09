---
title: Problemi noti e risoluzione dei problemi
titleSuffix: Azure Machine Learning
description: Ottenere un elenco dei problemi noti, le soluzioni alternative e la risoluzione dei problemi per Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 93015da810f163a48529704e69e1747ac1aec401
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82889387"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Problemi noti e risoluzione dei problemi Azure Machine Learning

Questo articolo consente di individuare e correggere gli errori o gli errori che possono verificarsi quando si usa Azure Machine Learning.

## <a name="diagnostic-logs"></a>Log di diagnostica

In alcuni casi può essere utile fornire le informazioni di diagnostica quando si richiede supporto. Per visualizzare alcuni log: 
1. Visitare [Azure Machine Learning Studio](https://ml.azure.com). 
1. Sul lato sinistro selezionare **Experiment (esperimento** ) 
1. Selezionare un esperimento.
1. Selezionare un'esecuzione.
1. Nella parte superiore selezionare **output + log**.

> [!NOTE]
> Azure Machine Learning registra le informazioni da diverse origini durante il training, ad esempio AutoML o il contenitore Docker che esegue il processo di training. Molti di questi log non sono documentati. Se si verificano problemi e si contatta il supporto tecnico Microsoft, potrebbero essere in grado di utilizzare questi log durante la risoluzione dei problemi.


## <a name="resource-quotas"></a>Quote di risorse

Informazioni sulle [quote di risorse](how-to-manage-quotas.md) che si potrebbero incontrare quando si lavora con Azure Machine Learning.

## <a name="installation-and-import"></a>Installazione e importazione
                           
* **Installazione PIP: le dipendenze non sono necessariamente coerenti con l'installazione di una sola riga**: 

   Si tratta di un limite noto di PIP, in quanto non dispone di un resolver di dipendenza funzionante quando si installa come una singola riga. La prima dipendenza univoca è l'unica che esamina. 

   Nel codice `azure-ml-datadrift` seguente e `azureml-train-automl` sono entrambi installati usando un'installazione PIP a riga singola. 
     ```
       pip install azure-ml-datadrift, azureml-train-automl
     ```
   Per questo esempio, supponiamo che `azure-ml-datadrift` sia richiesta la versione > `azureml-train-automl` 1,0 e che sia richiesta la versione < 1,2. Se la versione più recente `azure-ml-datadrift` di è 1,3, entrambi i pacchetti vengono aggiornati a 1,3, indipendentemente dal `azureml-train-automl` requisito del pacchetto per una versione precedente. 

   Per assicurarsi che le versioni appropriate siano installate per i pacchetti, installare usando più righe, come nel codice seguente. L'ordine non è un problema, poiché PIP esegue il downgrade esplicito come parte della chiamata alla riga successiva. Quindi, vengono applicate le dipendenze della versione appropriate.
    
     ```
        pip install azure-ml-datadrift
        pip install azureml-train-automl 
     ```
     
* **Il pacchetto di spiegazione non guarateed da installare durante l'installazione di azureml-Train-automl-client:** 
   
   Quando si esegue un'esecuzione remota di automl con la spiegazione del modello abilitata, viene visualizzato un messaggio di errore che informa che "" installare il pacchetto azureml-explain-Model per le spiegazioni del modello ". Si tratta di un problema noto e, come soluzione alternativa, attenersi a uno dei passaggi seguenti:
  
  1. Installare azureml-explain-Model localmente.
   ```
      pip install azureml-explain-model
   ```
  2. Disabilitare completamente la funzionalità di spiegazione passando model_explainability = false nella configurazione automl.
   ```
      automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             model_explainability=False,
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)
    ``` 
    
* **Errori Panda: in genere visualizzati durante l'esperimento AutoML:**
   
   Quando si configura manualmente la Environmnet con pip, si noterà l'errore degli attributi, soprattutto da Pandas, a causa dell'installazione di versioni del pacchetto non supportate. Per evitare tali errori, [installare AUTOML SDK usando il automl_setup. cmd](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md):
   
    1. Aprire un prompt di Anaconda e clonare il repository GitHub per un set di notebook di esempio.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
    
    2. CD per la cartella How-to-use-azureml/Automated-Machine-Learning in cui sono stati estratti i notebook di esempio e quindi eseguire:
    
    ```bash
    automl_setup
    ```
  
* **Messaggio di errore: Impossibile installare "PyYAML"**

    Azure Machine Learning SDK per Python: PyYAML è un `distutils` progetto installato. Non è pertanto possibile stabilire in modo accurato quali file appartengono a esso in caso di disinstallazione parziale. Per continuare a installare l'SDK, ignorando l'errore, usare:
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **Errore di databricks durante l'installazione dei pacchetti**

    L'installazione di Azure Machine Learning SDK non riesce in Azure Databricks quando sono installati altri pacchetti. Alcuni pacchetti, come `psutil`, possono causare conflitti. Per evitare errori di installazione, installare i pacchetti bloccando la versione della libreria. Questo problema è correlato a databricks e non al Azure Machine Learning SDK. Questo problema può verificarsi anche con altre librerie. Esempio:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    In alternativa, è possibile usare gli script init se si continuano a risolvere i problemi di installazione con le librerie di Python. Questo approccio non è ufficialmente supportato. Per altre informazioni, vedere [script init con ambito cluster](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

* **Errore di importazione di databricks: Impossibile importare il nome "timedelta" da "Pandas. _libs. tslibs"**: se viene visualizzato questo errore quando si usa Machine Learning automatico, eseguire le due righe seguenti nel notebook:
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Errore di importazione di databricks: nessun modulo denominato ' Pandas. Core. Indexes '**: se viene visualizzato questo errore quando si usa Machine Learning automatico:

    1. Eseguire questo comando per installare due pacchetti nel cluster Azure Databricks:
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. Scollegare e quindi riconnettere il cluster al notebook.
    
    Se questi passaggi non risolvono il problema, provare a riavviare il cluster.

* **Databricks FailToSendFeather**: se viene visualizzato un `FailToSendFeather` errore durante la lettura dei dati in Azure Databricks cluster, fare riferimento alle soluzioni seguenti:
    
    * Aggiornare `azureml-sdk[automl]` il pacchetto alla versione più recente.
    * Aggiungere `azureml-dataprep` la versione 1.1.8 o successiva.
    * Aggiungere `pyarrow` la versione 0,11 o successiva.
    
## <a name="create-and-manage-workspaces"></a>Creare e gestire aree di lavoro

> [!WARNING]
> Non è supportato lo stato di un'area di lavoro di Azure Machine Learning in una sottoscrizione diversa o di trasferimento della sottoscrizione proprietaria a un nuovo tenant. Questa operazione può causare errori.

* **Portale di Azure**: se si passa direttamente a visualizzare l'area di lavoro da un collegamento di condivisione dall'SDK o dal portale, non sarà possibile visualizzare la pagina **Panoramica** normale con le informazioni sulla sottoscrizione nell'estensione. Inoltre non sarà possibile passare in un'altra area di lavoro. Se è necessario visualizzare un'altra area di lavoro, passare direttamente a [Azure Machine Learning Studio](https://ml.azure.com) e cercare il nome dell'area di lavoro.

## <a name="set-up-your-environment"></a>Configurare l'ambiente

* **Problemi di creazione di AmlCompute**: esiste una rara possibilità che alcuni utenti che hanno creato l'area di lavoro Azure Machine Learning dal portale di Azure prima della versione GA potrebbero non essere in grado di creare AmlCompute in tale area di lavoro. È possibile generare una richiesta di supporto per il servizio o creare una nuova area di lavoro tramite il portale o l'SDK per sbloccarsi immediatamente.

## <a name="work-with-data"></a>Usare i dati

### <a name="overloaded-azurefile-storage"></a>Archiviazione AzureFile di overload

Se viene visualizzato un errore `Unable to upload project files to working directory in AzureFile because the storage is overloaded`, applicare le soluzioni alternative seguenti.

Se si usa una condivisione file per altri carichi di lavoro, ad esempio il trasferimento dei dati, si consiglia di usare i BLOB in modo che la condivisione file sia disponibile per l'invio di esecuzioni. È inoltre possibile suddividere il carico di lavoro tra due aree di lavoro diverse.

### <a name="passing-data-as-input"></a>Passaggio di dati come input

*  **TypeError: FileNotFound: nessun file o directory**di questo tipo: questo errore si verifica se il percorso del file fornito non è quello in cui si trova il file. È necessario assicurarsi che il modo in cui si fa riferimento al file sia coerente con la posizione in cui è stato montato il set di dati nella destinazione di calcolo. Per garantire uno stato deterministico, è consigliabile usare il percorso astratto quando si monta un set di dati in una destinazione di calcolo. Nel codice seguente, ad esempio, il set di dati viene montato sotto la radice del file System della destinazione di calcolo `/tmp`. 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Se non si include la barra iniziale '/', sarà necessario anteporre la directory di lavoro ad esempio `/mnt/batch/.../tmp/dataset` alla destinazione di calcolo per indicare dove si desidera montare il set di dati.

### <a name="data-labeling-projects"></a>Progetti di assegnazione di etichette ai dati

|Problema  |Soluzione  |
|---------|---------|
|È possibile usare solo i set di dati creati negli archivi dati BLOB     |  si tratta di un limite noto della versione corrente.       |
|Dopo la creazione, il progetto Mostra "inizializzazione" per molto tempo     | Aggiornare manualmente la pagina. L'inizializzazione deve continuare a circa 20 punti di database al secondo. La mancanza di AutoRefresh è un problema noto.         |
|Quando si esaminano le immagini, le immagini appena etichettate non vengono visualizzate     |   Per caricare tutte le immagini con etichetta, scegliere il **primo** pulsante. Il **primo** pulsante consente di tornare all'inizio dell'elenco, ma carica tutti i dati con etichetta.      |
|Quando si preme il tasto ESC durante l'assegnazione di etichette per il rilevamento di oggetti, viene creata un'etichetta con dimensioni pari a zero nell'angolo superiore sinistro. L'invio di etichette in questo stato non riesce.     |   Eliminare l'etichetta facendo clic sul segno incrociato accanto.  |

## <a name="azure-machine-learning-designer"></a>Finestra di progettazione di Azure Machine Learning

Problemi noti:

* **Tempo di preparazione per il calcolo lungo**: possono essere pochi minuti o anche più a lungo quando si esegue la prima connessione o si crea una destinazione di calcolo. 

## <a name="train-models"></a>Eseguire il training dei modelli

* **ModuleErrors (nessun modulo denominato)**: se si esegue ModuleErrors durante l'invio di esperimenti in Azure ml, significa che lo script di training prevede l'installazione di un pacchetto, ma non viene aggiunto. Una volta fornito il nome del pacchetto, Azure ML installerà il pacchetto nell'ambiente usato per l'esecuzione del training. 

    Se si usano gli [estimatori](concept-azure-machine-learning-architecture.md#estimators) per inviare esperimenti, è possibile specificare un nome di pacchetto `pip_packages` tramite `conda_packages` il parametro o nello strumento di stima basato su da quale origine si vuole installare il pacchetto. È anche possibile specificare un file yml con tutte le dipendenze `conda_dependencies_file`usando o elencare tutti i requisiti PIP in un file `pip_requirements_file` txt usando il parametro. Se si dispone di un oggetto ambiente di Azure ML personalizzato per cui si vuole eseguire l'override dell'immagine predefinita usata dallo strumento di stima, è possibile `environment` specificare tale ambiente tramite il parametro del costruttore Estimator.

    Azure ML fornisce anche estimatori specifici del Framework per Tensorflow, PyTorch, Chainer e SKLearn. Con questi estimatori si assicurerà che le dipendenze del Framework di base siano installate per conto dell'utente nell'ambiente utilizzato per il training. È possibile specificare dipendenze aggiuntive, come descritto in precedenza. 
 
    Le immagini Docker gestite da Azure ML e il relativo contenuto possono essere visualizzate nei [contenitori AzureML](https://github.com/Azure/AzureML-Containers).
    Le dipendenze specifiche del Framework sono elencate nella rispettiva documentazione di Framework- [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks), [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks).

    > [!Note]
    > Se si ritiene che un particolare pacchetto sia abbastanza comune da essere aggiunto in ambienti e immagini gestite da Azure ML, è necessario generare un problema di GitHub nei [contenitori AzureML](https://github.com/Azure/AzureML-Containers). 
 
* **NameError (nome non definito), AttributeError (oggetto senza attributo)**: questa eccezione deve provenire dagli script di training. È possibile esaminare i file di log da portale di Azure per ottenere altre informazioni sul nome specifico non definito o sull'errore dell'attributo. Dall'SDK è possibile usare `run.get_details()` per esaminare il messaggio di errore. Vengono inoltre elencati tutti i file di log generati per l'esecuzione. Assicurarsi di esaminare lo script di training e correggere l'errore prima di inviare nuovamente l'esecuzione. 

* **Horovod è stato arrestato**: nella maggior parte dei casi se si verifica l'arresto di "AbortedError: Horovod" questa eccezione significa che è presente un'eccezione sottostante in uno dei processi che hanno causato l'arresto di Horovod. Ogni rango nel processo MPI ottiene il proprio file di log dedicato in Azure ML. Questi log sono denominati `70_driver_logs`. In caso di training distribuito, i nomi dei log sono con `_rank` suffisso per semplificare la differenziazione dei log. Per individuare l'errore esatto che ha causato l'arresto di Horovod, esaminare tutti i file di log e cercare `Traceback` alla fine dei file di driver_log. Uno di questi file fornirà l'effettiva eccezione sottostante. 

* **Esecuzione o eliminazione dell'esperimento**: gli esperimenti possono essere archiviati tramite il metodo [Experiment. Archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) o dalla visualizzazione della scheda dell'esperimento nel client di Azure Machine Learning Studio tramite il pulsante "Archivia esperimento". Questa azione consente di nascondere l'esperimento dall'elenco di query e viste, ma non di eliminarlo.

    L'eliminazione permanente di singoli esperimenti o esecuzioni non è attualmente supportata. Per ulteriori informazioni sull'eliminazione delle risorse dell'area di lavoro, vedere [esportare o eliminare i dati dell'area di lavoro del servizio Machine Learning](how-to-export-delete-data.md).

* **Documento metrico troppo grande**: Azure Machine Learning presenta limiti interni sulle dimensioni degli oggetti metrica che possono essere registrati contemporaneamente da un'esecuzione di training. Se si verifica un errore di "documento metrico troppo grande" durante la registrazione di una metrica con valori di elenco, provare a suddividere l'elenco in blocchi più piccoli, ad esempio:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Internamente, Azure ML concatena i blocchi con lo stesso nome di metrica in un elenco contiguo.

## <a name="automated-machine-learning"></a>Funzionalità automatizzate di Machine Learning

* **Flusso tensore**: Machine Learning automatizzato attualmente non supporta il flusso tensore versione 1,13. Se si installa questa versione, le dipendenze del pacchetto smetteranno di funzionare. Ci stiamo impegnando per risolvere questo problema in una versione futura.

* **Grafici degli esperimenti**: i grafici di classificazione binaria (precisione-richiamo, Roc, curva di guadagno e così via) mostrati nelle iterazioni dell'esperimento di ml automatizzato non vengono visualizzati correttamente nell'interfaccia utente a partire da 4/12. I tracciati del grafico mostrano attualmente risultati inversi, in cui i modelli con prestazioni migliori vengono visualizzati con risultati inferiori. Una soluzione è in fase di analisi.

* **Databricks Annulla un'esecuzione automatica di Machine Learning**: quando si usano le funzionalità automatiche di machine learning in Azure Databricks, per annullare un'esecuzione e avviare una nuova esecuzione dell'esperimento, riavviare il cluster di Azure Databricks.

* **Databricks >10 iterazioni per Machine Learning automatico**: nelle impostazioni automatiche di Machine Learning, se sono presenti più di 10 iterazioni `show_output` , `False` impostare su quando si invia l'esecuzione.

* **Widget databricks per Azure Machine Learning SDK e Machine Learning automatico**: il widget SDK Azure Machine Learning non è supportato in un notebook di databricks perché i notebook non possono analizzare i widget HTML. È possibile visualizzare il widget nel portale usando questo codice Python nella cella Azure Databricks notebook:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```

## <a name="deploy--serve-models"></a>Distribuire e gestire modelli

Eseguire queste azioni per gli errori seguenti:

|Errore  | Soluzione  |
|---------|---------|
|Errore di compilazione dell'immagine durante la distribuzione del servizio Web     |  Aggiungere "pynacl = = 1.2.1" come dipendenza pip al file conda per la configurazione dell'immagine       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   Modificare lo SKU per le macchine virtuali usate nella distribuzione in uno con una maggiore quantità di memoria. |
|Errore di FPGA     |  Non sarà possibile distribuire i modelli in FPGA fino a quando non viene richiesta e approvata la quota FPGA. Per richiedere l'accesso, compilare il modulo di richiesta della quota: https://aka.ms/aml-real-time-ai       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Aggiornamento dei componenti di Azure Machine Learning nel cluster AKS

È necessario applicare manualmente gli aggiornamenti ai componenti Azure Machine Learning installati in un cluster del servizio Azure Kubernetes. 

È possibile applicare questi aggiornamenti scollegando il cluster dall'area di lavoro Azure Machine Learning, quindi riconnettendo il cluster all'area di lavoro. Se TLS è abilitato nel cluster, sarà necessario fornire il certificato TLS/SSL e la chiave privata quando si riconnette il cluster. 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

Se non si dispone più del certificato TLS/SSL e della chiave privata oppure si usa un certificato generato da Azure Machine Learning, è possibile recuperare i file prima di scollegare il cluster connettendosi al cluster usando `kubectl` e recuperando il segreto. `azuremlfessl`

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes archivia i segreti nel formato con codifica base 64. Prima di fornire tali componenti, è necessario decodificare in base 64 i `cert.pem` componenti e `key.pem` dei segreti `attach_config.enable_ssl`. 

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Problemi relativi ai servizi WebService in Azure Kubernetes

Molti errori WebService nel servizio Azure Kubernetes possono essere sottoposti a debug connettendosi al cluster usando `kubectl`. È possibile ottenere per `kubeconfig.json` un cluster del servizio Kubernetes di Azure eseguendo

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="authentication-errors"></a>Errori di autenticazione

Se si esegue un'operazione di gestione in una destinazione di calcolo da un processo remoto, si riceverà uno degli errori seguenti:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Ad esempio, si riceverà un errore se si prova a creare o collegare una destinazione di calcolo da una pipeline di Machine Learning che viene inviata per l'esecuzione remota.
