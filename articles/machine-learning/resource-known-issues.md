---
title: Problemi noti e risoluzione dei problemi
titleSuffix: Azure Machine Learning
description: Ottenere un elenco dei problemi noti, delle soluzioni alternative e della risoluzione dei problemi per Azure Machine Learning.Get a list of the known issues, workarounds, and troubleshooting for Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 22a24d01f606cd3f76a0de950351feb3d964da54
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478923"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Problemi noti e risoluzione dei problemi di Azure Machine Learning

Questo articolo consente di individuare e correggere errori o errori che possono verificarsi quando si usa Azure Machine Learning.This article helps you find and correct errors or failures you may encounter when using Azure Machine Learning.

## <a name="diagnostic-logs"></a>Log di diagnostica

In alcuni casi può essere utile fornire le informazioni di diagnostica quando si richiede supporto. Per visualizzare alcuni registri: 
1. Visita [lo studio di Azure Machine Learning](https://ml.azure.com). 
1. Sul lato sinistro, seleziona **Esperimento** 
1. Selezionare un esperimento.
1. Selezionare una concluna.
1. In alto, selezionare **Output e registri**.

> [!NOTE]
> Azure Machine Learning registra le informazioni da diverse origini durante il training, ad esempio AutoML o il contenitore Docker che esegue il processo di training. Molti di questi registri non sono documentati. Se si verificano problemi e contattare il supporto tecnico Microsoft, potrebbero essere in grado di utilizzare questi registri durante la risoluzione dei problemi.


## <a name="resource-quotas"></a>Quote di risorse

Informazioni sulle [quote di risorse](how-to-manage-quotas.md) che si potrebbero incontrare quando si lavora con Azure Machine Learning.

## <a name="installation-and-import"></a>Installazione e importazione

* **Messaggio di errore: Impossibile installare "PyYAML"**

    Azure Machine Learning SDK per Python: `distutils` PyYAML è un progetto installato. Non è pertanto possibile stabilire in modo accurato quali file appartengono a esso in caso di disinstallazione parziale. Per continuare a installare l'SDK, ignorando l'errore, usare:
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **Messaggio di errore: Nessuna distribuzione corrispondente trovata per azureml-dataprep-native**

    La distribuzione Python 3.7.4 di Anaconda presenta un bug che interrompe l'installazione di azureml-sdk. Questo problema è discusso in questo [problema Di GitHub](https://github.com/ContinuumIO/anaconda-issues/issues/11195)
    
    Questo problema può essere risolto creando un nuovo ambiente Conda utilizzando questo comando:This can be amp around by creating a new Conda Environment using this command:
    ```bash
    conda create -n <env-name> python=3.7.3
    ```
    Che crea un ambiente Conda utilizzando Python 3.7.3, che non ha il problema di installazione presente in 3.7.4.

* **Errore di Databrickdurante l'installazione dei pacchetti**

    L'installazione di Azure Machine Learning SDK non riesce in Azure Databricks quando vengono installati più pacchetti. Alcuni pacchetti, come `psutil`, possono causare conflitti. Per evitare errori di installazione, installare i pacchetti bloccando la versione della libreria. Questo problema è correlato a Databricks e non ad Azure Machine Learning SDK. È possibile che si verifichi questo problema anche con altre librerie. Esempio:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    In alternativa, è possibile utilizzare gli script init se si continua ad affrontare problemi di installazione con le librerie Python. Questo approccio non è ufficialmente supportato. Per ulteriori informazioni, consultate [Script init con ambito cluster.](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)

* Errore di **importazione Databricks: Impossibile importare il nome 'Timedelta' da 'pandas._libs.tslibs':** se viene visualizzato questo errore quando si utilizza l'apprendimento automatico, eseguire le due righe seguenti nel blocco appunti:
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* Errore di **importazione Databricks: nessun modulo denominato 'pandas.core.indexes':** se viene visualizzato questo errore quando si utilizza l'apprendimento automatico automatico:

    1. Eseguire questo comando per installare due pacchetti nel cluster Azure Databricks:Run this command to install two packages in your Azure Databricks cluster:
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. Scollegare e quindi ricollegare il cluster al blocco appunti.
    
    Se questi passaggi non risolvono il problema, provare a riavviare il cluster.

* **Databricks FailToSendFeather**: Se `FailToSendFeather` viene visualizzato un errore durante la lettura dei dati nel cluster Azure Databricks, fare riferimento alle soluzioni seguenti:Databricks FailToSendFeather : If you see a error when reading data on Azure Databricks cluster, refer to the following solutions:
    
        * Upgrade `azureml-sdk[automl]` package to the latest version.
        * Add `azureml-dataprep` version 1.1.8 or above.
        * Add `pyarrow` version 0.11 or above.
        `

## <a name="create-and-manage-workspaces"></a>Creare e gestire aree di lavoro

> [!WARNING]
> Lo spostamento dell'area di lavoro di Azure Machine Learning in una sottoscrizione diversa o lo spostamento della sottoscrizione proprietaria in un nuovo tenant non sono supportati. Questa operazione può causare errori.

* **Portale di Azure:** se si passa direttamente a visualizzare l'area di lavoro da un collegamento di condivisione dall'SDK o dal portale, non sarà possibile visualizzare la normale pagina **Panoramica** con le informazioni sulla sottoscrizione nell'estensione. Inoltre non sarà possibile passare in un'altra area di lavoro. Se è necessario visualizzare un'altra area di lavoro, passare direttamente a [Azure Machine Learning Studio](https://ml.azure.com) e cercare il nome dell'area di lavoro.

## <a name="set-up-your-environment"></a>Configurare l'ambiente

* **Problemi durante la creazione di AmlCompute:** è raro che alcuni utenti che hanno creato l'area di lavoro di Azure Machine Learning dal portale di Azure prima della versione GA non siano in grado di creare AmlCompute in tale area di lavoro. È possibile generare una richiesta di supporto sul servizio o creare una nuova area di lavoro tramite il portale o l'SDK per sbloccare immediatamente se stessi.

## <a name="work-with-data"></a>Usare i dati

### <a name="overloaded-azurefile-storage"></a>Archiviazione di AzureFile sovraccaricataOverloaded AzureFile storage

Se viene visualizzato `Unable to upload project files to working directory in AzureFile because the storage is overloaded`un errore, applicare le seguenti soluzioni alternative.

Se si usa la condivisione file per altri carichi di lavoro, ad esempio il trasferimento dei dati, è consigliabile usare i BLOB in modo che la condivisione file sia libera di essere usata per l'invio di esecuzioni. È inoltre possibile suddividere il carico di lavoro tra due diverse aree di lavoro.

### <a name="datasets-and-data-preparation"></a>Set di dati e preparazione dei dati

Questi sono problemi noti per i set di dati di Azure Machine Learning.These are known issues for Azure Machine Learning Datasets.

*  **TypeError: FileNotFound: file o directory non di questo**tipo: questo errore si verifica se il percorso del file fornito non è la posizione del file. È necessario assicurarsi che il modo in cui si fa riferimento al file sia coerente con la posizione in cui è stato montato il set di dati nella destinazione di calcolo. Per garantire uno stato deterministico, è consigliabile usare il percorso astratto durante il montaggio di un set di dati in una destinazione di calcolo. Nel codice seguente, ad esempio, viene montato il set di `/tmp`dati nella radice del file system della destinazione di calcolo , . 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Se non si include la barra iniziale, '/', sarà necessario anteporre `/mnt/batch/.../tmp/dataset` il prefisso alla directory di lavoro, ad esempio nella destinazione di calcolo per indicare dove si desidera montare il set di dati.

### <a name="data-labeling-projects-issues"></a>Problemi relativi ai progetti di etichettatura dei dati


|Problema  |Risoluzione  |
|---------|---------|
|È possibile usare solo i set di dati creati negli archivi dati BLOBOnly datasets created on blob datastores can be used only datasets created on blob datastores can be used     |  si tratta di una limitazione nota della versione corrente.       |
|Dopo la creazione, il progetto mostra "Inizializzazione" per molto tempo     | Aggiornare manualmente la pagina. L'inizializzazione dovrebbe procedere a circa 20 punti dati al secondo. La mancanza di aggiornamento automatico è un problema noto.         |
|Quando si esaminano le immagini, le immagini appena etichettate non vengono visualizzate     |   Per caricare tutte le immagini con etichetta, scegliere il pulsante **Primo.** Il pulsante **Primo** consente di tornare all'inizio dell'elenco, ma carica tutti i dati etichettati.      |
|Premendo il tasto Esc durante l'etichettatura per il rilevamento degli oggetti, viene creata un'etichetta di dimensione zero nell'angolo superiore sinistro. L'invio di etichette in questo stato ha esito negativo.     |   Eliminare l'etichetta facendo clic sul segno di croce accanto ad essa.  |

## <a name="azure-machine-learning-designer-issues"></a>Problemi relativi alla finestra di progettazione di Azure Machine LearningAzure Machine Learning designer issues

Problemi noti con la finestra di progettazione.

* Tempo di preparazione del **calcolo lungo:** può essere necessario un paio di minuti o anche più lungo quando ci si connette a una destinazione di calcolo o crea. 

## <a name="train-models"></a>Eseguire il training dei modelli

* **ModuleErrors (Nessun modulo denominato):** se si esegue ModuleErrors durante l'invio di esperimenti in Azure ML, significa che lo script di training prevede l'installazione di un pacchetto ma non viene aggiunto. Dopo aver fornito il nome del pacchetto, Azure ML installerà il pacchetto nell'ambiente usato per l'esecuzione del training. 

    Se si utilizza [Estimators](concept-azure-machine-learning-architecture.md#estimators) per inviare esperimenti, `pip_packages` è `conda_packages` possibile specificare un nome di pacchetto tramite o parametro nello stimatore in base a quale origine si desidera installare il pacchetto. È inoltre possibile specificare un file yml con tutte le dipendenze `conda_dependencies_file` `pip_requirements_file` utilizzando o elencare tutti i requisiti pip in un file txt utilizzando il parametro. Se si dispone di un oggetto Ambiente Azure ML di cui si vuole eseguire l'override `environment` dell'immagine predefinita usata dallo stimatore, è possibile specificare tale ambiente tramite il parametro del costruttore dello stimatore.

    Azure ML also provides framework-specific estimators for Tensorflow, PyTorch, Chainer and SKLearn. L'uso di questi stimatori garantisce che le dipendenze del framework di base siano installate per conto dell'ambiente utilizzato per il training. È possibile specificare dipendenze aggiuntive come descritto in precedenza. 
 
    Le immagini docker gestite da Azure ML e il relativo contenuto possono essere visualizzati nei [contenitori di AzureML.Azure](https://github.com/Azure/AzureML-Containers)ML maintained docker images and their contents can be seen in AzureML Containers .
    Le dipendenze specifiche del framework sono elencate nella documentazione del rispettivo framework: [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks), [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks).

    > [!Note]
    > Se si ritiene che un determinato pacchetto sia abbastanza comune da essere aggiunto in ambienti e immagini mantenute di Azure ML, generare un problema di GitHub nei [contenitori di AzureML.](https://github.com/Azure/AzureML-Containers) 
 
* **NameError (nome non definito), AttributeError (oggetto senza attributo):** questa eccezione deve provenire dagli script di training. È possibile esaminare i file di log dal portale di Azure per ottenere altre informazioni sul nome specifico non definito o sull'errore di attributo. Dall'SDK, è `run.get_details()` possibile utilizzare per esaminare il messaggio di errore. Verranno inoltre elencati tutti i file di registro generati per l'esecuzione. Assicurati di dare un'occhiata allo script di allenamento e correggere l'errore prima di inviare nuovamente la corsa. 

* **Horovod è stato arrestato**: Nella maggior parte dei casi se si verifica "AbortedError: Horovod has been shut down" questa eccezione significa che c'è stata un'eccezione sottostante in uno dei processi che ha causato l'arresto di Horovod. Ogni classificazione nel processo MPI ottiene il proprio file di log dedicato in Azure ML. Questi registri `70_driver_logs`sono denominati . In caso di formazione distribuita, i `_rank` nomi dei log sono suffissi con per semplificare la differenziazione dei log. Per trovare l'errore esatto che ha causato l'arresto di Horovod, esaminare tutti i file di registro e cercare `Traceback` alla fine dei file di driver_log. Uno di questi file fornirà l'eccezione sottostante effettiva. 

* Eliminazione di run **o experiment:** gli esperimenti possono essere archiviati usando il metodo [Experiment.archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) o dalla visualizzazione della scheda Esperimento nel client studio di Azure Machine Learning tramite il pulsante "Esperimento di archiviazione". Questa azione nasconde l'esperimento alle query e alle visualizzazioni di elenco, ma non lo elimina.

    L'eliminazione permanente di singoli esperimenti o esecuzioni non è attualmente supportata. Per altre informazioni sull'eliminazione delle risorse dell'area di lavoro, vedere [Esportare o eliminare i dati dell'area](how-to-export-delete-data.md)di lavoro del servizio Machine Learning.

* **Il documento metrico è troppo grande:** Azure Machine Learning ha limiti interni sulle dimensioni degli oggetti metrico che possono essere registrati contemporaneamente da un'esecuzione di training. Se si verifica un errore "Documento metrico è troppo grande" durante la registrazione di una metrica con valori di elenco, provare a dividere l'elenco in blocchi più piccoli, ad esempio:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Internamente, Azure ML concatena i blocchi con lo stesso nome di metrica in un elenco contiguo.

## <a name="automated-machine-learning"></a>Funzionalità automatizzate di Machine Learning

* **Flusso di tensore:** l'apprendimento automatico automatico non supporta attualmente la versione 1.13 del flusso di tensore. L'installazione di questa versione causerà l'interruzione del funzionamento delle dipendenze del pacchetto. Stiamo lavorando per risolvere questo problema in una versione futura.

* **Grafici degli esperimenti**: I grafici di classificazione binari (precision-recall, ROC, gain curve ecc.) mostrati nelle iterazioni automatizzate dell'esperimento ML non vengono visualizzati correttamente nell'interfaccia utente dal 4/12. I grafici mostrano attualmente risultati inversi, in cui i modelli con prestazioni migliori vengono visualizzati con risultati inferiori. Una risoluzione è in fase di indagine.

* **Databricks cancel an automated machine learning run**: Quando si usano funzionalità di apprendimento automatico in Azure Databricks, per annullare un'esecuzione e avviare una nuova esecuzione dell'esperimento, riavviare il cluster Azure Databricks.Databricks cancel an automated machine learning run : When you use automated machine learning capabilities on Azure Databricks, to cancel a run and start a new experiment run, restart your Azure Databricks cluster.

* **Databricks >10 iterazioni per l'apprendimento automatico:** nelle impostazioni di apprendimento automatico automatizzato, se si dispone di più di 10 iterazioni, impostato su `show_output` `False` quando si invia l'esecuzione.

* **Widget Databrick per Azure Machine Learning SDK e apprendimento automatico automatico:** il widget Azure Machine Learning SDK non è supportato in un blocco appunti di Databricks perché i blocchi appunti non possono analizzare i widget HTML. È possibile visualizzare il widget nel portale usando questo codice Python nella cella del blocco appunti di Azure Databricks:You can view the widget in the portal by using this Python code in your Azure Databricks notebook cell:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```

## <a name="deploy--serve-models"></a>Distribuire e gestire modelli

Eseguire queste azioni per i seguenti errori:

|Errore  | Risoluzione  |
|---------|---------|
|Errore di creazione dell'immagine durante la distribuzione del servizio WebImage building failure when deploying web service     |  Aggiungere "pynacl 1.2.1" come dipendenza pip al file Conda per la configurazione dell'immagine       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   Modificare lo SKU per le macchine virtuali usato nella distribuzione in uno con più memoria. |
|Errore FPGA     |  Non sarà possibile distribuire i modelli in FPGA fino a quando non viene richiesta e approvata la quota FPGA. Per richiedere l'accesso, compilare il modulo di richiesta della quota: https://aka.ms/aml-real-time-ai       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Aggiornamento dei componenti di Azure Machine Learning nel cluster AKSUpdating Azure Machine Learning components in AKS cluster

Gli aggiornamenti ai componenti di Azure Machine Learning installati in un cluster di servizi Azure Kubernetes devono essere applicati manualmente. 

È possibile applicare questi aggiornamenti scollegando il cluster dall'area di lavoro di Azure Machine Learning e quindi ricollegando il cluster all'area di lavoro. Se TLS è abilitato nel cluster, sarà necessario fornire il certificato TLS/SSL e la chiave privata quando si ricollega il cluster. 

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

Se non si dispone più del certificato TLS/SSL e della chiave privata oppure si usa un certificato generato da Azure `kubectl` Machine Learning, `azuremlfessl`è possibile recuperare i file prima di scollegare il cluster connettendosi al cluster usando e recuperando il segreto.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes memorizza i segreti in formato codificato in base 64. Sarà necessario decodificare in base `cert.pem` 64 i segreti e `key.pem` i `attach_config.enable_ssl`componenti prima di fornirli a . 

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Webservices in Azure Kubernetes Errori del servizio

È possibile eseguire il debug di molti errori del servizio Web nel `kubectl`servizio Kubernetes di Azure connettendosi al cluster tramite . È possibile `kubeconfig.json` ottenere il per un cluster di servizi di Azure Kubernetes eseguendo

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
