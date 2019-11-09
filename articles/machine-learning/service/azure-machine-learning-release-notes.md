---
title: Novità della versione
titleSuffix: Azure Machine Learning
description: Informazioni sugli aggiornamenti più recenti per Azure Machine Learning e sugli SDK Python di machine learning e di preparazione dei dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: e0709143f1beb9726fc79eb837d59d7db7cf00d7
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888587"
---
# <a name="azure-machine-learning-release-notes"></a>Note sulla versione di Azure Machine Learning

In questo articolo vengono fornite informazioni sulle versioni Azure Machine Learning.  Per il contenuto completo dell'SDK di riferimento, visitare la pagina di riferimento dell'SDK principale di Azure Machine Learning [**per Python**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) .

Per informazioni sui bug noti e le soluzioni alternative, vedere l'[elenco dei problemi noti](resource-known-issues.md).


## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Esperienza Web 

La pagina di destinazione dell'area di lavoro collaborativa in [https://ml.azure.com](https://ml.azure.com) è stata migliorata e rimarcata come Azure Machine Learning Studio (anteprima).

Da studio è possibile eseguire il training, il test, la distribuzione e la gestione di asset Azure Machine Learning come DataSet, pipeline, modelli, endpoint e altro ancora.  

Accedere ai seguenti strumenti di creazione basati sul Web da studio:

| Strumento basato sul Web | Descrizione | Edizione |
|-|-|-|
| VM notebook (anteprima) | Workstation basata su cloud completamente gestita | Basic & Enterprise |
| [Machine Learning automatizzato](tutorial-first-experiment-automated-ml.md) (anteprima) | Nessuna esperienza di codice per l'automazione dello sviluppo di modelli di Machine Learning | Enterprise |
| [Finestra di progettazione](ui-concept-visual-interface.md) (anteprima) | Strumento di modellazione di Machine Learning con trascinamento della selezione precedentemente noto come finestra di progettazione | Enterprise |


### <a name="azure-machine-learning-designer-enhancements"></a>Miglioramenti di Azure Machine Learning Designer 

+ Noto in precedenza come interfaccia visiva 
+   11 nuovi [moduli](../algorithm-module-reference/module-reference.md) , inclusi i consigli, i classificatori e le utilità di formazione, tra cui progettazione delle funzionalità, convalida incrociata e trasformazione dei dati.

### <a name="r-sdk"></a>R SDK 
 
I data scientist e gli sviluppatori di intelligenza artificiale usano il [Azure Machine Learning SDK per R](tutorial-1st-r-experiment.md) per creare ed eseguire flussi di lavoro di Machine Learning con Azure Machine Learning.

Il Azure Machine Learning SDK per R usa il pacchetto `reticulate` per l'associazione a Python SDK. Associando direttamente a Python, l'SDK per R consente di accedere agli oggetti e ai metodi principali implementati in Python SDK da qualsiasi ambiente R scelto.

Le funzionalità principali dell'SDK includono:

+   Gestione delle risorse cloud per il monitoraggio, la registrazione e l'organizzazione degli esperimenti di machine learning.
+   Esegui il training di modelli usando risorse cloud, incluso il training del modello con accelerazione GPU.
+   Distribuire i modelli come servizi WebService in istanze di contenitore di Azure (ACI) e Azure Kubernetes Service (AKS).

Per la documentazione completa, vedere il [sito Web del pacchetto](https://azure.github.io/azureml-sdk-for-r) .

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>SDK di Azure Machine Learning per Python v 1.0.72

+ **Nuove funzionalità**
  + Aggiunta dei monitoraggi del set di dati tramite il pacchetto [**azureml-datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift) , che consente di monitorare i set di dati timeseries per la deriva dei dati o altre modifiche statistiche nel tempo. Gli avvisi e gli eventi possono essere generati se viene rilevata una deriva o se vengono soddisfatte altre condizioni sui dati. Per informazioni dettagliate, vedere [la documentazione](https://aka.ms/datadrift) . 
  + Annunciare due nuove edizioni (anche dette SKU in modo interscambiabile) in Azure Machine Learning. Con questa versione è ora possibile creare un'area di lavoro di Azure ML di base o aziendale. Tutte le aree di lavoro esistenti verranno automaticamente inserite nell'edizione Basic ed è possibile passare al portale di Azure o a Studio per aggiornare l'area di lavoro in qualsiasi momento. È possibile creare un'area di lavoro di base o aziendale dal portale di Azure. Per ulteriori informazioni, leggere [la documentazione](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-workspace) . Dall'SDK è possibile determinare l'edizione dell'area di lavoro usando la proprietà "SKU" dell'oggetto dell'area di lavoro.
  + Sono stati apportati miglioramenti anche a Azure Machine Learning calcolo, ora è possibile visualizzare le metriche per i cluster (ad esempio, i nodi totali, i nodi in esecuzione, la quota Core totale) in monitoraggio di Azure, oltre a visualizzare i log di diagnostica per il debug. Inoltre, è possibile visualizzare le esecuzioni attualmente in esecuzione o in coda nel cluster e i dettagli, ad esempio gli IP dei vari nodi del cluster. È possibile visualizzarli nel portale o usando le funzioni corrispondenti nell'SDK o nell'interfaccia della riga di comando. 
  
  + **Funzionalità di anteprima**
    + Si sta rilasciando il supporto in anteprima per la crittografia del disco dell'unità SSD locale in Azure Machine Learning calcolo. Per usare questa funzionalità, è necessario inviare un ticket di supporto tecnico per ottenere la sottoscrizione.
    + Anteprima pubblica di Azure Machine Learning inferenza batch. Azure Machine Learning inferenza batch è destinata a processi di inferenza di grandi dimensioni che non sono dipendenti dal tempo. L'inferenza batch offre una scalabilità di calcolo inferenza conveniente, con velocità effettiva senza precedenti per le applicazioni asincrone. È ottimizzato per la velocità effettiva elevata, l'inferenza di incendi e dimenticamenti su raccolte di dati di grandi dimensioni.  
    + [**azureml-contrib-DataSet**](https://docs.microsoft.com/python/api/azureml-contrib-dataset)
        + Funzionalità abilitate per il set di dati con etichetta
        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore, Dataset
        import azureml.contrib.dataset
        from azureml.contrib.dataset import FileHandlingOption, LabeledDatasetTask
        
        # create a labeled dataset by passing in your JSON lines file
        dataset = Dataset._Labeled.from_json_lines(datastore.path('path/to/file.jsonl'), LabeledDatasetTask.IMAGE_CLASSIFICATION)
        
        # download or mount the files in the `image_url` column
        dataset.download()
        dataset.mount()
        
        # get a pandas dataframe
        from azureml.data.dataset_type_definitions import FileHandlingOption
        dataset.to_pandas_dataframe(FileHandlingOption.DOWNLOAD) 
        dataset.to_pandas_dataframe(FileHandlingOption.MOUNT)
        
        # get a Torchvision dataset
        dataset.to_torchvision()
        ```

+ **Correzioni di bug e miglioramenti**
  + **azure-cli-ml**
    + L'interfaccia della riga di comando ora supporta il packaging dei modelli
    + Aggiunta CLI del set di dati. Per ulteriori informazioni: `az ml dataset --help`
    + Aggiunta del supporto per la distribuzione e la creazione di pacchetti di modelli supportati (ONNX, Scikit-learn e TensorFlow) senza un'istanza di InferenceConfig.
    + Aggiunto il flag di sovrascrittura per la distribuzione del servizio (ACI e AKS) in SDK e CLI. Se specificato, sovrascriverà il servizio esistente se il servizio con nome esiste già. Se il servizio non esiste, creerà un nuovo servizio.
    + I modelli possono essere registrati con due nuovi Framework, Onnx e Tensorflow. -La registrazione del modello accetta dati di input di esempio, dati di output di esempio e la configurazione delle risorse per il modello.
  + **azureml-automl-Core**
    + Il training di un'iterazione verrebbe eseguito in un processo figlio solo quando vengono impostati vincoli di Runtime.
    + Aggiunto un barriera per le attività di previsione, per verificare se un max_horizon specificato provocherà un problema di memoria nel computer specificato. In tal caso, verrà visualizzato un messaggio di barriera.
    + Aggiunta del supporto per frequenze complesse, ad esempio 2 anni e 1 mese. -È stato aggiunto un messaggio di errore comprensibile se non è possibile determinare la frequenza.
    + Aggiungere azureml-defaults a conda ENV generata automaticamente per risolvere l'errore di distribuzione del modello
    + Consente di convertire in un set di dati tabulare i dati intermedi della pipeline Azure Machine Learning e di usarli nel `AutoMLStep`.
    + Aggiornamento a scopo di colonna implementato per lo streaming.
    + Aggiornamento del parametro transformer implementato per Imputator e HashOneHotEncoder per il flusso.
    + Aggiunta delle dimensioni correnti dei dati e delle dimensioni minime dei dati richieste ai messaggi di errore di convalida.
    + Aggiornamento della dimensione minima dei dati necessari per la convalida incrociata per garantire un minimo di due campioni in ogni riduzioni di convalida.
  + **azureml-CLI-comune**
    + L'interfaccia della riga di comando ora supporta il packaging dei modelli
    + I modelli possono essere registrati con due nuovi Framework, Onnx e Tensorflow.
    + La registrazione del modello accetta dati di input di esempio, dati di output di esempio e la configurazione delle risorse per il modello.
  + **azureml-contrib-Gradient**
    + correzione del canale di versione per il notebook
    + Aggiunto un avviso per la destinazione di calcolo non AmlCompute che non è supportata
    + Aggiunto LightGMB Estimator al pacchetto azureml-contrib-Gradient
  + [**azureml-Core**](https://docs.microsoft.com/python/api/azureml-core)
    + L'interfaccia della riga di comando ora supporta il packaging dei modelli
    + Aggiungere un avviso di deprecazione per le API del set di dati deprecate. Vedere Avviso di modifica dell'API del set di dati in https://aka.ms/tabular-dataset.
    + Modificare [`Dataset.get_by_id`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) per restituire il nome e la versione della registrazione se il set di dati è registrato.
    + Correggere un bug che ScriptRunConfig con DataSet come argomento non può essere usato ripetutamente per inviare l'esecuzione dell'esperimento.
    + I set di dati recuperati durante un'esecuzione verranno rilevati e possono essere visualizzati nella pagina Dettagli esecuzione oppure chiamando [`run.get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--) al termine dell'esecuzione.
    + Consente di convertire in un set di dati tabulare i dati intermedi della pipeline Azure Machine Learning e di usarli nel [`AutoMLStep`](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlstep).
    + Aggiunta del supporto per la distribuzione e la creazione di pacchetti di modelli supportati (ONNX, Scikit-learn e TensorFlow) senza un'istanza di InferenceConfig.
    + Aggiunto il flag di sovrascrittura per la distribuzione del servizio (ACI e AKS) in SDK e CLI. Se specificato, sovrascriverà il servizio esistente se il servizio con nome esiste già. Se il servizio non esiste, creerà un nuovo servizio.
    +  I modelli possono essere registrati con due nuovi Framework, Onnx e Tensorflow. La registrazione del modello accetta dati di input di esempio, dati di output di esempio e la configurazione delle risorse per il modello.
    + Aggiunta del nuovo archivio dati per il database di Azure per MySQL. Aggiunto esempio per l'uso di database di Azure per MySQL in DataTransferStep in pipeline Azure Machine Learning.
    + Aggiunta della funzionalità per aggiungere e rimuovere tag dagli esperimenti aggiunta della funzionalità per rimuovere i tag dalle esecuzioni
    + Aggiunto il flag di sovrascrittura per la distribuzione del servizio (ACI e AKS) in SDK e CLI. Se specificato, sovrascriverà il servizio esistente se il servizio con nome esiste già. Se il servizio non esiste, creerà un nuovo servizio.
  + [**azureml-datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift)
    + Spostato da `azureml-contrib-datadrift` in `azureml-datadrift`
    + Aggiunta del supporto per il monitoraggio dei set di impostazioni di timeseries per la deviazione e altre misure statistiche 
    + I nuovi metodi `create_from_model()` e `create_from_dataset()` alla classe [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) . Il metodo `create()` verrà deprecato. 
    + Regolazioni delle visualizzazioni in Python e nell'interfaccia utente in Azure Machine Learning Studio.
    + Supportare la pianificazione dei monitoraggi settimanali e mensili, oltre a giornaliera per i monitoraggi del set di dati.
    + Supportare il recupero delle metriche di monitoraggio dati per analizzare i dati cronologici per i monitoraggi del set di dati. 
    + Varie correzioni di bug 
  + [**azureml-pipeline-Core**](https://docs.microsoft.com/python/api/azureml-pipeline-core)
    + azureml-dataprep non è più necessario per inviare una pipeline di Azure Machine Learning eseguita dal file di `yaml` della pipeline.
  + [**azureml-Train-automl**](https://docs.microsoft.com/python/api/azureml-train-automl)
    + Aggiungere azureml-defaults a conda ENV generata automaticamente per risolvere l'errore di distribuzione del modello
    + Il training remoto di AutoML ora include azureml-defaults per consentire il riutilizzo della formazione ENV per l'inferenza.
  + **azureml-train-core**
    + Aggiunto il supporto di PyTorch 1,3 in [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch) Estimator
  
## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>Interfaccia visuale (anteprima)

+ L'interfaccia visiva Azure Machine Learning (anteprima) è stata revisionata per l'esecuzione su [Azure Machine Learning pipeline](concept-ml-pipelines.md). Le pipeline (note in precedenza come esperimenti) create nell'interfaccia visiva sono ora completamente integrate con l'esperienza di base Azure Machine Learning.
  + Esperienza di gestione unificata con asset SDK
  + Controllo delle versioni e rilevamento per modelli di interfaccia visivi, pipeline ed endpoint 
  + Interfaccia utente riprogettata
  + Aggiunta DISTRIBUZIONE inferenza batch
  + Aggiunto il supporto di Azure Kubernetes Service (AKS) per le destinazioni di calcolo inferenza
  + Nuovo flusso di lavoro di creazione della pipeline Python-Step
  + Nuova [pagina di destinazione](https://ml.azure.com) per gli strumenti di creazione visiva

+ **Nuovi moduli**
  + Applica operazione matematica
  + Applica trasformazione SQL
  + Ritaglia valori
  + Riepiloga dati
  + Importa da database SQL  

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>SDK di Azure Machine Learning per Python v 1.0.69

+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-Core**
    + Limitazione delle spiegazioni del modello per una migliore esecuzione piuttosto che per l'elaborazione di spiegazioni per ogni esecuzione. Questo comportamento cambia per local, remote e ADB.
    + Aggiunta del supporto per le spiegazioni del modello su richiesta per l'interfaccia utente
    + Aggiunto psutil come dipendenza di `automl` e incluso psutil come dipendenza conda in amlcompute.
    + Correzione del problema relativo ai ritardi euristici e alle dimensioni della finestra in sequenza nei dati di previsione set alcune serie di che possono causare errori di algebra lineare
      + Aggiunta della stampa per i parametri determinati in base all'euristica nelle esecuzioni delle previsioni.
  + **[azureml-contrib-datadrift](https://docs.microsoft.com/python/api/azureml-contrib-datadrift)**
    + Aggiunta della protezione durante la creazione della metrica di output se la tendenza al livello del set di dati non è presente nella prima sezione.
  + **azureml-contrib-interpreta**
    + azureml-contrib-explain-Package Model è stato rinominato in azureml-contrib-Interpret
  + **azureml-core**
    + Aggiunta dell'API per annullare la registrazione di DataSet. `dataset.unregister_all_versions()`
    + azureml-contrib-explain-Package Model è stato rinominato in azureml-contrib-interpreta.
  + **[azureml-Core](https://docs.microsoft.com/python/api/azureml-core)**
    + Aggiunta dell'API per annullare la registrazione di DataSet. DataSet. [unregister_all_versions ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + Aggiunta dell'API del set di dati per controllare l'ora di modifica dei dati. `dataset.data_changed_time`.
    + La possibilità di utilizzare `FileDataset` e `TabularDataset` come input per `PythonScriptStep`, `EstimatorStep`e `HyperDriveStep` nella pipeline Azure Machine Learning
    + Le prestazioni di `FileDataset.mount` sono state migliorate per le cartelle con un numero elevato di file
    + La possibilità di utilizzare [filedataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset) e [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset) come input per [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep), [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)e [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) nella pipeline Azure Machine Learning.
    + Prestazioni del set di dati. [Mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none-) è stato migliorato per le cartelle con un numero elevato di file
    + Aggiunta dell'URL alle raccomandazioni degli errori noti nei dettagli dell'esecuzione.
    + Correzione di un bug nell'esecuzione. get_metrics in cui le richieste avrebbero esito negativo se un'esecuzione avesse troppi elementi figlio
    + Correzione di un bug nell' [esecuzione. get_metrics](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) in cui le richieste avrebbero esito negativo se un'esecuzione avesse troppi elementi figlio
    + Aggiunta del supporto per l'autenticazione nel cluster Arcadia.
    + La creazione di un oggetto esperimento Ottiene o crea l'esperimento nell'area di lavoro Azure Machine Learning per il rilevamento della cronologia di esecuzione. L'ID dell'esperimento e l'ora di archiviazione vengono popolati nell'oggetto esperimento durante la creazione. Esempio: esperimento = Experiment (area di lavoro, "nuovo esperimento") experiment_id = experiment.id Archive () e reactivate () sono funzioni che possono essere chiamate su un esperimento per nascondere e ripristinare l'esperimento dall'esperienza utente o restituita per impostazione predefinita in una chiamata per elencare gli esperimenti. Se viene creato un nuovo esperimento con lo stesso nome di un esperimento archiviato, è possibile rinominare l'esperimento archiviato durante la riattivazione passando un nuovo nome. Può essere presente un solo esperimento attivo con un determinato nome. Esempio: experiment1 = Experiment (area di lavoro, "esperimento attivo") experiment1. Archive () # Crea nuovo esperimento attivo con lo stesso nome del file archiviato. experiment2. = Experiment (area di lavoro, "esperimento attivo") experiment1. reactivate (new_name = "esperimento attivo precedente") l'elenco di metodi statici () nell'esperimento può assumere un filtro nome e un filtro ViewType. I valori di ViewType sono "ACTIVE_ONLY", "ARCHIVED_ONLY" e "tutti" esempio: archived_experiments = Experiment. List (Workspace, view_type = "ARCHIVED_ONLY") all_first_experiments = Experiment. List (Workspace, Name = "primo esperimento", view_type = "ALL")
    + Supporto per l'uso dell'ambiente per la distribuzione dei modelli e l'aggiornamento del servizio
  + **azureml-datadrift**
    + L'attributo show della classe DataDriftDector non supporterà più l'argomento facoltativo ' with_details '. L'attributo show presenterà solo il coefficiente di drifting dei dati e il contributo alla deviazione dei dati delle colonne di funzionalità.
    + Il comportamento dell'attributo DataDriftDetector ' get_output ' cambia:
      + Parametro di input start_time, end_time sono facoltativi anziché obbligatoria;
      + L'input specifico start_time e/o end_time con un run_id specifico nella stessa chiamata provocherà un'eccezione di errore del valore perché si escludono a vicenda 
      + Per start_time e/o end_time specifici dell'input, verranno restituiti solo i risultati delle esecuzioni pianificate. 
      + Il parametro ' daily_latest_only ' è deprecato.
    + Supporto del recupero degli output di spostamento dei dati basati su DataSet.
  + **azureml-explain-model**
    + Rinomina il pacchetto AzureML-explain-Model in AzureML-interpreta, mantenendo il pacchetto precedente per la compatibilità con le versioni precedenti per ora
    + correzione `automl` bug con spiegazioni non elaborate impostate sull'attività di classificazione anziché sulla regressione per impostazione predefinita durante il download da ExplanationClient
    + Aggiungere il supporto per `ScoringExplainer` da creare direttamente con `MimicWrapper`
  + **azureml-pipeline-core**
    + Miglioramento delle prestazioni per la creazione di pipeline di grandi dimensioni
  + **azureml-train-core**
    + Aggiunto il supporto di TensorFlow 2,0 in TensorFlow Estimator
  + **azureml-train-automl**
    + La creazione di un oggetto [esperimento](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment) Ottiene o crea l'esperimento nell'area di lavoro Azure Machine Learning per il rilevamento della cronologia di esecuzione. L'ID dell'esperimento e l'ora di archiviazione vengono popolati nell'oggetto esperimento durante la creazione. Esempio:

        ```py
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [Archive ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#archive--) e [reactivate ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) sono funzioni che possono essere chiamate su un esperimento per nascondere e ripristinare l'esperimento dall'esperienza utente o restituita per impostazione predefinita in una chiamata a un elenco di esperimenti. Se viene creato un nuovo esperimento con lo stesso nome di un esperimento archiviato, è possibile rinominare l'esperimento archiviato durante la riattivazione passando un nuovo nome. Può essere presente un solo esperimento attivo con un determinato nome. Esempio: 
        
        ```py
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        L'elenco di metodi statici [()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly--) nell'esperimento può assumere un filtro di nome e un filtro ViewType. I valori di ViewType sono "ACTIVE_ONLY", "ARCHIVED_ONLY" e "tutti". Esempio: 
        
        ```py
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + Supporto per l'uso dell'ambiente per la distribuzione dei modelli e per l'aggiornamento del servizio.
  + **[azureml-datadrift](https://docs.microsoft.com/python/api/azureml-contrib-datadrift)**
    + L'attributo show della classe [DataDriftDetector](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector.datadriftdetector) non supporterà più l'argomento facoltativo ' with_details '. L'attributo show presenterà solo il coefficiente di drifting dei dati e il contributo alla deviazione dei dati delle colonne di funzionalità.
    + DataDriftDetector Function [get_Output](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector.datadriftdetector#get-output-start-time--end-time--run-id-none--daily-latest-only-true-) modifiche del comportamento:
      + Parametro di input start_time, end_time sono facoltativi anziché obbligatoria;
      + Gli input specifici start_time e/o end_time con un run_id specifico nella stessa chiamata genereranno un'eccezione di errore del valore perché si escludono a vicenda. 
      + Per start_time e/o end_time specifici dell'input, verranno restituiti solo i risultati delle esecuzioni pianificate. 
      + Il parametro ' daily_latest_only ' è deprecato.
    + Supporto del recupero degli output di spostamento dei dati basati su DataSet.
  + **[azureml-explain-Model](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + Rinomina il pacchetto AzureML-explain-Model in AzureML-interpreta, mantenendo per il momento la compatibilità con le versioni precedenti del pacchetto.
    + correzione del bug automl con spiegazioni non elaborate impostate sull'attività di classificazione anziché sulla regressione per impostazione predefinita durante il download da ExplanationClient.
    + Aggiungere il supporto per [ScoringExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.scoring.scoring_explainer.scoringexplainer) da creare direttamente usando [MimicWrapper](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic_wrapper.mimicwrapper)
  + **[azureml-pipeline-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Miglioramento delle prestazioni per la creazione di pipeline di grandi dimensioni.
  + **[azureml-Train-Core](https://docs.microsoft.com/python/api/azureml-train-core)**
    + Aggiunto il supporto di TensorFlow 2,0 in [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow) Estimator.
  + **[azureml-Train-automl](https://docs.microsoft.com/python/api/azureml-train-automl)**
    + Non sarà più possibile eseguire l'operazione padre quando l'iterazione del programma di installazione non è riuscita perché l'orchestrazione si occupa già di tale operazione.
    + Aggiunta del supporto locale-Docker e locale-conda per gli esperimenti AutoML
    + Aggiunta del supporto locale-Docker e locale-conda per gli esperimenti AutoML.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nuova esperienza Web (anteprima) per aree di lavoro Azure Machine Learning

La scheda Experiment (esperimento) del [nuovo portale dell'area di lavoro](https://ml.azure.com) è stata aggiornata in modo che i data scientist possano monitorare gli esperimenti in modo più efficiente. È possibile esplorare le funzionalità seguenti:
+ Eseguire l'esperimento dei metadati per filtrare e ordinare facilmente l'elenco degli esperimenti
+ Pagine di dettagli dell'esperimento semplificate e performanti che consentono di visualizzare e confrontare le esecuzioni
+ Nuova progettazione per eseguire le pagine dei dettagli per comprendere e monitorare le esecuzioni di training

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>SDK di Azure Machine Learning per Python v 1.0.65

  + **Nuove funzionalità**
    + Sono stati aggiunti ambienti curati. Questi ambienti sono stati preconfigurati con le librerie per le attività di Machine Learning comuni e sono stati pre-compilazione e memorizzati nella cache come immagini Docker per un'esecuzione più rapida. Vengono visualizzati per impostazione predefinita nell'elenco dell'ambiente dell'area di lavoro, con prefisso "AzureML".
    + Sono stati aggiunti ambienti curati. Questi ambienti sono stati preconfigurati con le librerie per le attività di Machine Learning comuni e sono stati pre-compilazione e memorizzati nella cache come immagini Docker per un'esecuzione più rapida. Vengono visualizzati per impostazione predefinita nell'elenco dell'ambiente dell' [area di lavoro](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29), con prefisso "AzureML".
  
  + **azureml-train-automl**
  + **[azureml-Train-automl](https://docs.microsoft.com/python/api/azureml-train-automl)**
    + Aggiunta del supporto per la conversione di ONNX per ADB e HDI

+ **Funzionalità di anteprima**  
  + **azureml-train-automl**
  + **[azureml-Train-automl](https://docs.microsoft.com/python/api/azureml-train-automl)**
    + BERT e BiLSTM supportati come testo featurizer (solo anteprima)
    + Personalizzazione di conteggi supportata per i parametri di funzione e di trasformazione colonna (solo anteprima)
    + Spiegazioni non elaborate supportate quando l'utente abilita la spiegazione del modello durante il training (solo anteprima)
    + Aggiunto il profeta per la previsione `timeseries` come pipeline trainable (solo anteprima)
  
  + **azureml-contrib-datadrift**
    + I pacchetti sono stati spostati da azureml-contrib-datadrift a azureml-datadrift; il pacchetto di `contrib` verrà rimosso in una versione futura 

+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-Core**
    + Introduzione di FeaturizationConfig a AutoMLConfig e AutoMLBaseSettings
    + Introduzione di FeaturizationConfig a [AutoMLConfig](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig) e AutoMLBaseSettings
      + Eseguire l'override dello scopo della colonna per conteggi con la colonna e il tipo di funzionalità specificati
      + Sostituisci parametri Transformer
    + Aggiunta del messaggio di deprecazione per explain_model () e retrieve_model_explanations ()
    + Aggiunto profeta come pipeline trainable (solo anteprima)
    + Aggiunta del messaggio di deprecazione per explain_model () e retrieve_model_explanations ().
    + Aggiunto profeta come pipeline trainable (solo anteprima).
    + Aggiunta del supporto per il rilevamento automatico dei ritardi di destinazione, le dimensioni della finestra in sequenza e l'orizzonte massimo. Se uno dei target_lags, target_rolling_window_size o max_horizon è impostato su' auto ', verrà applicata l'euristica per stimare il valore del parametro corrispondente in base ai dati di training.
    + Correzione della previsione nel caso in cui il set di dati contenga una colonna con granularità, questo tipo di granularità è di tipo numerico e si verifica un divario tra training e set di test
    + Correzione del messaggio di errore relativo all'indice duplicato nell'esecuzione remota nelle attività di previsione
    + Correzione della previsione nel caso in cui il set di dati contenga una colonna con granularità, questo tipo di granularità è di tipo numerico ed è presente un gap tra il training e il set di test.
    + Correzione del messaggio di errore relativo all'indice duplicato nell'esecuzione remota nelle attività di previsione.
    + È stata aggiunta una barriera per verificare se un set di dati è sbilanciato o meno. In caso contrario, viene scritto un messaggio di barriera nella console.
  + **azureml-core**
    + Aggiunta della possibilità di recuperare l'URL di firma di accesso condiviso nel modello nell'archiviazione tramite l'oggetto modello. Esempio: Model. get_sas_url ()
    + Introdurre `run.get_details()['datasets']` per ottenere i set di impostazioni associati all'esecuzione inviata
    + Aggiungere l'API `Dataset.Tabular.from_json_lines_files` per creare un TabularDataset da file di righe JSON. Per informazioni su questi dati tabulari nei file di righe JSON in TabularDataset, vedere https://aka.ms/azureml-data per la documentazione.
    + Aggiunta di campi di dimensioni di VM aggiuntivi (disco del sistema operativo, numero di GPU) alla funzione supported_vmsizes ()
    + Aggiunta di campi aggiuntivi alla funzione list_nodes () per visualizzare l'esecuzione, l'indirizzo IP privato e pubblico, la porta e così via.
    + Possibilità di specificare un nuovo campo durante il provisioning del cluster: remotelogin_port_public_access che può essere impostato su abilitato o disabilitato a seconda che si desideri lasciare aperta o chiusa la porta SSH al momento della creazione del cluster. Se non viene specificato, il servizio aprirà o chiuderà in modo intelligente la porta a seconda che si stia distribuendo il cluster all'interno di una VNet.
  + **azureml-explain-model**
  + **[azureml-Core](https://docs.microsoft.com/python/api/azureml-core/azureml.core)**
    + Aggiunta della possibilità di recuperare l'URL di firma di accesso condiviso nel modello nell'archiviazione tramite l'oggetto modello. Esempio: modello. [get_sas_url ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + Introdurre l'esecuzione. [get_Details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--)[' DataSets '] per ottenere i set di impostazioni associati all'esecuzione inviata
    + Aggiungere `Dataset.Tabular`API. [from_json_lines_files ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) per creare un TabularDataset dai file di righe JSON. Per informazioni su questi dati tabulari nei file di righe JSON in TabularDataset, vedere https://aka.ms/azureml-data per la documentazione.
    + Aggiunta di campi di dimensioni di VM aggiuntivi (disco del sistema operativo, numero di GPU) alla funzione [supported_vmsizes ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
    + Aggiunta di campi aggiuntivi alla funzione [list_nodes ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) per visualizzare l'esecuzione, l'indirizzo IP privato e pubblico, la porta e così via.
    + Possibilità di specificare un nuovo campo durante il [provisioning](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--) del cluster `--remotelogin_port_public_access` che può essere impostato su abilitato o disabilitato a seconda che si desideri lasciare aperta o chiusa la porta SSH al momento della creazione del cluster. Se non viene specificato, il servizio aprirà o chiuderà in modo intelligente la porta a seconda che si stia distribuendo il cluster all'interno di una VNet.
  + **[azureml-explain-Model](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + Documentazione migliorata per gli output di spiegazione nello scenario di classificazione.
    + Aggiunta della possibilità di caricare i valori y stimati nella spiegazione per gli esempi di valutazione. Sblocca visualizzazioni più utili.
    + Aggiunta della proprietà Explainer a MimicWrapper per consentire il recupero del MimicExplainer sottostante.
  + **azureml-pipeline-core**
    + Aggiunta del notebook per descrivere il modulo, ModuleVersion e ModuleStep
  + **azureml-pipeline-steps**
    + Aggiunta di RScriptStep per supportare l'esecuzione di script R tramite la pipeline AML
    + Correzione dell'analisi dei parametri di metadati in AzureBatchStep che causava il messaggio di errore "l'assegnazione per il parametro SubscriptionId non è stata specificata"
  + **azureml-train-automl**
    + Training_data supportati, validation_data, label_column_name, weight_column_name come formato di input dei dati
    + Aggiunta del messaggio di deprecazione per explain_model () e retrieve_model_explanations ()
  + **[azureml-pipeline-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + È stato aggiunto un [notebook](https://aka.ms/pl-modulestep) per descrivere il [modulo](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.module(class)), [ModuleVersion](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.moduleversion) e [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep).
  + **[azureml-pipeline-passaggi](https://docs.microsoft.com/python/api/azureml-pipeline-steps)**
    + Aggiunta di [RScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) per supportare l'esecuzione di script R tramite la pipeline AML.
    + Correzione dell'analisi dei parametri di metadati in [AzureBatchStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) che causava il messaggio di errore "l'assegnazione per il parametro SubscriptionId non è stata specificata".
  + **[azureml-Train-automl](https://docs.microsoft.com/python/api/azureml-train-automl)**
    + Training_data supportati, validation_data, label_column_name weight_column_name come formato di input dei dati.
    + Aggiunta del messaggio di deprecazione per [explain_model ()](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) e [retrieve_model_explanations ()](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlexplainer#retrieve-model-explanation-child-run-).

  
## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>SDK di Azure Machine Learning per Python v 1.0.62

+ **Nuove funzionalità**
  + In TabularDataset è stato introdotto il tratto `timeseries`. Questo tratto consente di filtrare facilmente i timestamp sui dati TabularDataset, ad esempio l'acquisizione di tutti i dati tra un intervallo di tempo o i dati più recenti. Per informazioni su questo tratto `timeseries` in TabularDataset, vedere https://aka.ms/azureml-data per la documentazione o https://aka.ms/azureml-tsd-notebook per un notebook di esempio. 
  + Training abilitato con TabularDataset e filedataset. Per un notebook di esempio, vedere https://aka.ms/dataset-tutorial. 
  
  + **azureml-train-core**
    + Aggiunta del supporto per `Nccl` e `Gloo` in PyTorch Estimator
  
+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-Core**
    + L'impostazione di AutoML ' lag_length ' e LaggingTransformer è stata deprecata.
    + Correzione della corretta convalida dei dati di input se specificati in un formato di flusso di dati
    + Il fit_pipeline. py è stato modificato per generare il grafo JSON e caricarlo negli artefatti. 
    + Rendering del grafico sotto `userrun` tramite `Cytoscape`.
  + **azureml-core**
    + È stata rivisitata la gestione delle eccezioni nel codice ADB e sono state apportate modifiche a in base alla nuova gestione degli errori
    + Aggiunta dell'autenticazione MSI automatica per le macchine virtuali del notebook.
    + Corregge il bug in cui è possibile caricare modelli danneggiati o vuoti a causa di tentativi non riusciti.
    + Correzione del bug in cui il nome `DataReference` cambia quando viene modificata la modalità `DataReference` (ad esempio, quando si chiama `as_upload`, `as_download` o `as_mount`).
    + Rendere `mount_point` e `target_path` facoltativo per `FileDataset.mount` e `FileDataset.download`.
    + Eccezione che non è possibile trovare la colonna timestamp se l'API relativa alle serie temporali è chiamata senza una colonna di tipo timestamp assegnata o se le colonne timestamp assegnate vengono eliminate.
    + Le colonne delle serie temporali devono essere assegnate con la colonna il cui tipo è date. in caso contrario, è prevista l'eccezione
    + Le colonne delle serie temporali che assegnano l'API ' with_timestamp_columns ' possono non assumere il nome della colonna timestamp fine/grossolana, che cancellerà le colonne timestamp assegnate in precedenza.
    + L'eccezione viene generata quando viene eliminata una colonna con granularità grossolana o un timestamp con granularità fine indicante che l'eliminazione può essere eseguita dopo l'esclusione della colonna timestamp nell'elenco di eliminazione o della chiamata with_time_stamp con valore None per rilasciare il timestamp colonne
    + L'eccezione verrà generata quando una colonna di tipo timestamp grossolano o con granularità fine non è inclusa nell'elenco Mantieni colonne con indicazione per l'utente che è possibile mantenere la conservazione dopo aver incluso la colonna timestamp in Mantieni elenco colonne o chiama with_time_stamp con None valore per rilasciare le colonne timestamp.
    + Aggiunta della registrazione per le dimensioni di un modello registrato.
  + **azureml-explain-model**
    + Correzione dell'avviso stampato sulla console quando non è installato il pacchetto python "packaging": "uso della versione precedente a quella supportata di lightgbm. eseguire l'aggiornamento alla versione successiva alla 2.2.1"
    + Correzione del modello di download con il partizionamento orizzontale per le spiegazioni globali con molte funzionalità
    + Sono stati corretti alcuni esempi di inizializzazione nella spiegazione dell'output.
    + Correzione di un errore immutabile nelle proprietà del set durante il caricamento con il client di spiegazione con due tipi diversi di modelli
    + È stato aggiunto un get_raw param a DELINEATORE Explainer. explain () in modo che un Explainer di assegnazione dei punteggi possa restituire i valori sia tecnici che non elaborati.
  + **azureml-train-automl**
    + Sono state introdotte API pubbliche da AutoML per il supporto di spiegazioni di `automl` explain SDK, il modo più recente per supportare le spiegazioni AutoML, disaccoppiando AutoML conteggi e spiegando il supporto per la spiegazione non elaborata integrata nell'SDK da azureml explain SDK per AutoML modelli.
    + Rimozione di azureml-impostazioni predefinite dagli ambienti di training remoto.
    + Il percorso predefinito dell'archivio cache è stato modificato da FileCacheStore basato su AzureFileCacheStore uno per AutoML in Azure Databricks percorso del codice.
    + Correzione della corretta convalida dei dati di input se specificati in un formato di flusso di dati
  + **azureml-train-core**
    + Ripristino source_directory_data_store deprecato.
    + È stata aggiunta la possibilità di eseguire l'override delle versioni del pacchetto installato azureml. 
    + Aggiunto il supporto di dockerfile nel parametro `environment_definition` negli estimatori.
    + Parametri di training distribuiti semplificati negli estimatori.

         ```py 
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer 
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nuova esperienza Web (anteprima) per aree di lavoro Azure Machine Learning 
La nuova esperienza Web consente a data scientist e data Engineers di completare il ciclo di vita di Machine Learning end-to-end dalla preparazione e visualizzazione dei dati al training e alla distribuzione di modelli in un'unica posizione. 

![Interfaccia utente dell'area di lavoro Azure Machine Learning (anteprima)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Funzionalità principali:**

Utilizzando questa nuova interfaccia di Azure Machine Learning, è ora possibile:
+ Gestione dei notebook o collegamento a Jupyter
+ [Eseguire esperimenti di Machine Learning automatici](tutorial-first-experiment-automated-ml.md)
+ [Creare set di dati da file locali, archivi dati, & file Web](how-to-create-register-datasets.md)
+ Esplorare & preparare i set di DataSet per la creazione di modelli
+ Monitorare la tendenza dei dati per i modelli 
+ Visualizzare le risorse recenti da un dashboard

Al momento di questa versione sono supportati i browser seguenti: Chrome, Firefox, Safari e Microsoft Edge Preview.

**Problemi noti:**

1. Aggiornare il browser se viene visualizzato il codice "si è verificato un errore. Errore durante il caricamento dei file di blocco "quando è in corso la distribuzione.  

1. Non è possibile eliminare o rinominare il file in notebook e file. Durante l'anteprima pubblica, è possibile usare l'interfaccia utente di Jupyter o il terminale nella macchina virtuale notebook per eseguire operazioni di aggiornamento del file. Poiché si tratta di una rete montata file system tutte le modifiche apportate alla VM notebook vengono immediatamente riflesse nell'area di lavoro del notebook. 

1. Per SSH nella macchina virtuale notebook:
   1. Trovare le chiavi SSH create durante l'installazione della macchina virtuale. In alternativa, trovare le chiavi nella portale di Azure Azure ML > aprire la scheda calcolo > individuare la macchina virtuale notebook nell'elenco > aprirne le proprietà: copiare le chiavi dalla finestra di dialogo.
   1. Importare le chiavi SSH pubbliche e private nel computer locale.
   1. Usarli per SSH nella macchina virtuale notebook. 

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>SDK di Azure Machine Learning per Python v 1.0.60

+ **Nuove funzionalità**
  + È stato introdotto filedataset, che fa riferimento a uno o più file negli archivi dati o negli URL pubblici. I file possono essere di qualsiasi formato. Filedataset offre la possibilità di scaricare o montare i file nel calcolo. Per informazioni su filedataset, visitare https://aka.ms/file-dataset.
  + Aggiunta del supporto YAML della pipeline per il passaggio PythonScript, Anna Step, databricks Step, DataTransferStep e AzureBatch Step

+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-Core**
    + Autoarima è ora una pipeline consigliata solo per l'anteprima.
    + Segnalazione errori migliorata per la previsione.
    + Miglioramento della registrazione usando eccezioni personalizzate anziché generiche nelle attività di previsione.
    + Rimuovere il controllo max_concurrent_iterations in modo che sia inferiore al numero totale di iterazioni.
    + I modelli AutoML ora restituiscono AutoMLExceptions
    + Questa versione migliora le prestazioni di esecuzione delle esecuzioni locali automatiche di machine learning.
  + **azureml-core**
    + Introduce DataSet. get_all (area di lavoro), che restituisce un dizionario di `TabularDataset` e `FileDataset` oggetti con chiave in base al nome di registrazione. 
    
    ```py 
    workspace = Workspace.from_config() 
    all_datasets = Dataset.get_all(workspace) 
    mydata = all_datasets['my-data'] 
    ```
    
    + Introdurre `parition_format` come argomento per `Dataset.Tabular.from_delimited_files` e `Dataset.Tabular.from_parquet.files`. Le informazioni sulla partizione di ogni percorso dati verranno estratte in colonne in base al formato specificato. ' {column_name}' crea una colonna stringa è {column_name: AAAA/MM/GG/HH/mm/SS}' crea una colonna DateTime, dove ' aaaa ',' MM ',' dd ',' HH ',' mm ' è SS ' vengono utilizzati per estrarre anno, mese, giorno, ora, minuto e secondo per il tipo DateTime. Il partition_format deve iniziare dalla posizione della prima chiave di partizione fino alla fine del percorso del file. Ad esempio, dato il percorso '.. /USA/2019/01/01/data.csv ' where the partition is by Country and time, partition_format ='/{Country}/{PartitionDate: AAAA/MM/gg}/data. csv ' crea la colonna stringa ' Country ' con il valore ' USA ' e la colonna DateTime ' PartitionDate ' con il valore ' 2019-01-01'.
        ```py 
        workspace = Workspace.from_config() 
        all_datasets = Dataset.get_all(workspace) 
        mydata = all_datasets['my-data'] 
        ```
        
    + Introdurre `partition_format` come argomento per `Dataset.Tabular.from_delimited_files` e `Dataset.Tabular.from_parquet.files`. Le informazioni sulla partizione di ogni percorso dati verranno estratte in colonne in base al formato specificato. ' {column_name}' crea una colonna stringa è {column_name: AAAA/MM/GG/HH/mm/SS}' crea una colonna DateTime, dove ' aaaa ',' MM ',' dd ',' HH ',' mm ' è SS ' vengono utilizzati per estrarre anno, mese, giorno, ora, minuto e secondo per il tipo DateTime. Il partition_format deve iniziare dalla posizione della prima chiave di partizione fino alla fine del percorso del file. Ad esempio, dato il percorso '.. /USA/2019/01/01/data.csv ' where the partition is by Country and time, partition_format ='/{Country}/{PartitionDate: AAAA/MM/gg}/data. csv ' crea la colonna stringa ' Country ' con il valore ' USA ' e la colonna DateTime ' PartitionDate ' con il valore ' 2019-01-01'.
    + `TabularDataset` sono stati aggiunti i metodi `to_csv_files` e `to_parquet_files`. Questi metodi consentono la conversione tra un `TabularDataset` e un `FileDataset` convertendo i dati in file del formato specificato.
    + Accedere automaticamente al registro immagini di base quando si salva un Dockerfile generato da Model. Package ().
    + ' gpu_support ' non è più necessario. AzureML ora rileva e usa automaticamente l'estensione Docker NVIDIA quando è disponibile. Verrà rimosso in una versione futura.
    + Aggiunta del supporto per la creazione, l'aggiornamento e l'uso di PipelineDrafts.
    + Questa versione migliora le prestazioni di esecuzione delle esecuzioni locali automatiche di machine learning.
    + Gli utenti possono eseguire query sulle metriche dalla cronologia di esecuzione in base al nome.
    + Miglioramento della registrazione usando eccezioni personalizzate anziché generiche nelle attività di previsione.
  + **azureml-explain-model**
    + Aggiunto feature_maps parametro al nuovo MimicWrapper, consentendo agli utenti di ottenere spiegazioni sulle funzionalità non elaborate.
    + Per impostazione predefinita, i caricamenti del set di dati sono disattivati per la spiegazione e possono essere riabilitati con upload_datasets = true
    + Aggiunta dei parametri di filtro "is_law" all'elenco di spiegazione e alle funzioni di download.
    + Aggiunge il metodo `get_raw_explanation(feature_maps)` agli oggetti spiegazione globale e locale.
    + Aggiunta del controllo della versione a lightgbm con avviso stampato se la versione supportata è la seguente
    + Utilizzo di memoria ottimizzato durante l'invio in batch di spiegazioni
    + I modelli AutoML ora restituiscono AutoMLExceptions
  + **azureml-pipeline-core**
    + Aggiunta del supporto per la creazione, l'aggiornamento e l'utilizzo di PipelineDrafts: può essere utilizzato per mantenere le definizioni di pipeline modificabili e utilizzarle in modo interattivo per l'esecuzione
  + **azureml-train-automl**
    + Creazione della funzionalità per l'installazione di versioni specifiche di pytorch v 1.1.0, :::no-loc text="cuda"::: Toolkit 9,0, pytorch-Transformator, che è necessario per abilitare BERT/XLNet nell'ambiente di runtime Python remoto.
  + **azureml-train-core**
    + Errore iniziale di alcuni errori di definizione di spazio degli iperparametri direttamente nell'SDK anziché sul lato server.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning data Prep SDK v 1.1.14
+ **Correzioni di bug e miglioramenti**
  + È stata abilitata la scrittura in ADLS/ADLSGen2 usando il percorso non elaborato e le credenziali.
  + Correzione di un bug che causava il mancato funzionamento di `include_path=True` per `read_parquet`.
  + Correzione dell'errore `to_pandas_dataframe()` causata dall'eccezione "valore di proprietà non valido: hostSecret".
  + Correzione di un bug per cui non è stato possibile leggere i file in DBFS in modalità Spark.
  
## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>SDK di Azure Machine Learning per Python v 1.0.57
+ **Nuove funzionalità**
  + Abilitato `TabularDataset` per essere utilizzato da AutomatedML. Per ulteriori informazioni su `TabularDataset`, visitare https://aka.ms/azureml/howto/createdatasets.
  
+ **Correzioni di bug e miglioramenti**
  + **automl-client-Core-nativeClient**
    + Correzione dell'errore, generato quando le etichette di training e/o di convalida (y e y_valid) vengono fornite sotto forma di dataframe Pandas ma non come matrice numpy.
    + Interfaccia aggiornata per creare un `RawDataContext` per richiedere solo i dati e l'oggetto `AutoMLBaseSettings`.
    +  Consentire agli utenti di AutoML di eliminare le serie di training che non sono sufficientemente lunghe durante la previsione. -Consentire agli utenti di AutoML di eliminare i granularità dal set di test che non esiste nel set di training durante la previsione.
  + **azure-cli-ml**
    + È ora possibile aggiornare il certificato SSL per l'endpoint di assegnazione dei punteggi distribuito nel cluster AKS sia per il certificato generato da Microsoft che per il cliente.
  + **azureml-automl-Core**
    + Correzione di un problema in AutoML, in cui le righe con etichette mancanti non sono state rimosse correttamente.
    + Registrazione degli errori migliorata in AutoML; i messaggi di errore completi ora verranno sempre scritti nel file di log.
    + AutoML ha aggiornato il blocco del pacchetto per includere `azureml-defaults`, `azureml-explain-model` e `azureml-dataprep`. AutoML non avviserà più in caso di mancata corrispondenza del pacchetto (ad eccezione del pacchetto `azureml-train-automl`).
    + È stato risolto un problema in `timeseries` in cui le divisioni del CV hanno una dimensione diversa, causando un errore di calcolo del contenitore.
    + Quando si esegue l'iterazione di ensemble per il tipo di training di convalida incrociata, se si verificano problemi durante il download dei modelli sottoposti a training sull'intero set di dati, si è verificata un'incoerenza tra i pesi del modello e i modelli che sono stati inseriti nel voto Ensemble.
    + Correzione dell'errore, generato quando le etichette di training e/o di convalida (y e y_valid) vengono fornite sotto forma di dataframe Pandas ma non come matrice numpy.
    + Correzione del problema relativo alle attività di previsione quando non è stata rilevata alcuna nelle colonne booleane delle tabelle di input.
    + Consentire agli utenti di AutoML di eliminare le serie di training che non sono sufficientemente lunghe durante la previsione. -Consentire agli utenti di AutoML di eliminare i granularità dal set di test che non esiste nel set di training durante la previsione.
  + **azureml-core**
    + Correzione del problema relativo all'ordinamento dei parametri blob_cache_timeout.
    + Aggiunta di tipi di eccezione Fit e Transform esterni agli errori di sistema.
    + Aggiunta del supporto per i segreti Key Vault per le esecuzioni remote. Aggiungere una classe azureml. Core. Vault. chiave Vault per aggiungere, ottenere ed elencare i segreti dall'insieme di credenziali delle chiavi associato all'area di lavoro. Sono supportate le operazioni seguenti:
      + azureml. Core. Workspace. Workspace. get_default_keyvault ()
      + azureml. Core. chiave Vault. insieme di credenziali. set_secret (nome, valore)
      + azureml. Core. chiave Vault. set_secrets (secrets_dict)
      + azureml. Core. chiave Vault. get_secret (nome)
      + azureml. Core. chiave Vault. get_secrets (secrets_list)
      + azureml. Core. chiave Vault. list_secrets ()
    + Metodi aggiuntivi per ottenere l'insieme di credenziali delle chiavi predefinito e ottenere i segreti durante l'esecuzione remota:
      + azureml. Core. Workspace. Workspace. get_default_keyvault ()
      + azureml. Core. Run. Run. get_secret (nome)
      + azureml. Core. Run. Run. get_secrets (secrets_list)
    + Aggiunta di parametri di override aggiuntivi al comando Invia-iperguida CLI.
    + Per migliorare l'affidabilità delle chiamate API, è necessario espandere i nuovi tentativi per le eccezioni comuni della libreria.
    + Aggiunta del supporto per l'invio di esecuzioni da un'esecuzione inviata.
    + Correzione del problema del token di firma di accesso condiviso in FileWatcher, che ha causato l'interruzione del caricamento dei file dopo la scadenza del token iniziale.
    + È supportata l'importazione di file CSV/TSV HTTP nel set di dati Python SDK.
    + Il Metodo Workspace. Setup () è stato deprecato. Un messaggio di avviso visualizzato dagli utenti suggerisce di usare Create () o Get ()/from_config ().
    + È stato aggiunto Environment. add_private_pip_wheel (), che consente il caricamento di pacchetti Python personalizzati privati `whl`all'area di lavoro e il loro utilizzo sicuro per la compilazione o la materializzazione dell'ambiente.
    + È ora possibile aggiornare il certificato SSL per l'endpoint di assegnazione dei punteggi distribuito nel cluster AKS sia per il certificato generato da Microsoft che per il cliente.
  + **azureml-explain-model**
    + Aggiunta del parametro per aggiungere un ID modello alle spiegazioni al caricamento.
    + Aggiunta dell'assegnazione di tag `is_raw` alle spiegazioni in memoria e caricamento.
    + Aggiunta del supporto di pytorch e test per il pacchetto azureml-explain-Model.
  + **azureml-opendatasets**
    + Supporto per il rilevamento e la registrazione dell'ambiente di test automatico.
    + Sono state aggiunte le classi per ottenere la popolazione degli Stati Uniti in base a County e zip
  + **azureml-pipeline-core**
    + Aggiunta della proprietà Label alle definizioni delle porte di input e di output.
  + **azureml-telemetria**
    + Correzione di una configurazione di telemetria non corretta.
  + **azureml-train-automl**
    + Correzione del bug in cui si è verificato un errore durante l'installazione. l'errore non è stato registrato nel campo "errori" per l'esecuzione del programma di installazione e pertanto non è stato archiviato nell'esecuzione padre "errori".
    + Correzione di un problema in AutoML, in cui le righe con etichette mancanti non sono state rimosse correttamente.
    + Consentire agli utenti di AutoML di eliminare le serie di training che non sono sufficientemente lunghe durante la previsione.
    + Consente agli utenti di AutoML di eliminare i granularità dal set di test che non esistono nel set di training durante la previsione.
    + Ora AutoMLStep passa `automl` configurazione a back-end per evitare eventuali problemi di modifiche o aggiunte di nuovi parametri di configurazione.
    + AutoML data barriera è ora disponibile in anteprima pubblica. L'utente visualizzerà un report di barriera di dati (per attività di classificazione/regressione) dopo il training e sarà in grado di accedervi tramite l'API SDK.
  + **azureml-train-core**
    + Aggiunto il supporto di torcia 1,2 in PyTorch Estimator.
  + **azureml-widget**
    + Grafici della matrice di confusione migliorati per il training della classificazione.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning data Prep SDK v 1.1.12
+ **Nuove funzionalità**
  + È ora possibile passare gli elenchi di stringhe come input ai metodi `read_*`.

+ **Correzioni di bug e miglioramenti**
  + Le prestazioni di `read_parquet` sono state migliorate significativamente durante l'esecuzione in Spark.
  + È stato risolto un problema per cui `column_type_builder` ha avuto esito negativo in caso di una singola colonna con formati di data ambigui.

### <a name="azure-portal"></a>Portale di Azure
+ **Funzionalità di anteprima**
  + Il flusso dei file di log e di output è ora disponibile per le pagine di dettagli dell'esecuzione. I file effettueranno lo streaming degli aggiornamenti in tempo reale quando l'interruttore di anteprima è attivato.
  + La possibilità di impostare la quota a livello di area di lavoro viene rilasciata in anteprima. Le quote AmlCompute vengono allocate a livello di sottoscrizione, ma ora è possibile distribuire tale quota tra le aree di lavoro e allocarla per la condivisione e la governance corrette. È sufficiente fare clic sul pannello **utilizzi e quote** nella barra di spostamento a sinistra dell'area di lavoro e selezionare la scheda **Configura quote** . si noti che è necessario essere un amministratore della sottoscrizione per poter impostare le quote a livello di area di lavoro poiché si tratta di un'area di lavoro incrociata operazione.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>SDK di Azure Machine Learning per Python v 1.0.55

+ **Nuove funzionalità**
  + L'autenticazione basata su token è ora supportata per le chiamate effettuate all'endpoint di assegnazione dei punteggi distribuito in AKS. Si continuerà a supportare l'autenticazione basata su chiave corrente e gli utenti potranno usare uno di questi meccanismi di autenticazione alla volta.
  + Possibilità di registrare un'archiviazione BLOB dietro la rete virtuale (VNet) come archivio dati.
  
+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-Core**
    + Corregge un bug in cui le dimensioni della convalida per le divisioni del CV sono ridotte e generano grafici non corretti rispetto a quelli reali per la regressione e la previsione.
    + La registrazione delle attività di previsione sulle esecuzioni remote è stata migliorata. l'utente viene ora fornito con un messaggio di errore completo se l'esecuzione non è riuscita.
    + Correzione degli errori di `Timeseries` se il flag di pre-elaborazione è true.
    + Sono stati resi più interoperabili alcuni messaggi di errore di convalida dei dati di previsione.
    + Riduzione del consumo di memoria delle esecuzioni di AutoML tramite l'eliminazione e/o il caricamento lazy dei set di impostazioni, in particolare tra le generazioni del processo
  + **azureml-contrib-explain-Model**
    + Aggiunta del flag model_task ai Explainer per consentire all'utente di eseguire l'override della logica di inferenza automatica predefinita per il tipo di modello
    + Modifiche dei widget: viene installato automaticamente con `contrib`, non altre `nbextension` spiegazione di installazione/abilitazione con la sola importanza della funzionalità globale (ad esempio Permutative)
    + Modifiche al Dashboard: i tracciati di box e i tracciati di violino oltre al tracciato `beeswarm` nella pagina di riepilogo. il rendering più rapido del tracciato `beeswarm` sul dispositivo di scorrimento "Top-k", che indica il modo in cui top-k è calcolato, i messaggi personalizzabili al posto dei grafici quando dati non specificati
  + **azureml-core**
    + Aggiunto il metodo Model. Package () per creare immagini Docker e Dockerfile che incapsulano i modelli e le relative dipendenze.
    + Aggiornamento di servizi WebService locali per accettare InferenceConfigs contenenti oggetti ambiente.
    + Correzione del modello. Register () che produce modelli non validi quando ' .' (per la directory corrente) viene passato come parametro model_path.
    + Aggiungere Run. submit_child, la funzionalità Mirrors Experiment. Submit, specificando l'esecuzione come elemento padre dell'esecuzione figlio inviata.
    + Supporta le opzioni di configurazione da Model. Register in Run. register_model.
    + Possibilità di eseguire processi JAR nel cluster esistente.
    + Sono ora supportati instance_pool_id e cluster_log_dbfs_path parametri.
    + Aggiunta del supporto per l'utilizzo di un oggetto Environment quando si distribuisce un modello in un WebService. È ora possibile fornire l'oggetto Environment come parte dell'oggetto InferenceConfig.
    + Aggiungere il mapping di appinsifht per le nuove aree-centralus-westus-northcentralus
    + Aggiunta della documentazione per tutti gli attributi di tutte le classi di archivio dati.
    + Aggiunta blob_cache_timeout parametro al `Datastore.register_azure_blob_container`.
    + Aggiunta di save_to_directory e load_from_directory metodi a azureml. Core. Environment. Environment.
    + Sono stati aggiunti i comandi "AZ ml Environment download" e "AZ ml Environment Register" all'interfaccia della riga di comando.
    + Aggiunta del metodo Environment. add_private_pip_wheel.
  + **azureml-explain-model**
    + Aggiunta del rilevamento del set di dati alle spiegazioni tramite il servizio DataSet (anteprima).
    + Riduzione delle dimensioni batch predefinite durante il flusso di spiegazioni globali da 10.000 a 100.
    + Aggiunta del flag model_task ai Explainer per consentire all'utente di eseguire l'override della logica di inferenza automatica predefinita per il tipo di modello.
  + **azureml-mlflow**
    + Correzione del bug in mlflow. azureml. build_image in cui le directory nidificate vengono ignorate.
  + **azureml-pipeline-steps**
    + Aggiunta della possibilità di eseguire processi JAR in un cluster di Azure Databricks esistente.
    + Aggiunta del instance_pool_id di supporto e dei parametri cluster_log_dbfs_path per il passaggio DatabricksStep.
    + Aggiunta del supporto per i parametri della pipeline nel passaggio DatabricksStep.
  + **azureml-train-automl**
    + Aggiunta `docstrings` per i file correlati all'insieme.
    + Aggiornamento di docs a un linguaggio più appropriato per `max_cores_per_iteration` e `max_concurrent_iterations`
    + La registrazione delle attività di previsione sulle esecuzioni remote è stata migliorata. l'utente viene ora fornito con un messaggio di errore completo se l'esecuzione non è riuscita.
    + Rimosso get_data dalla pipeline `automlstep` notebook.
    + Avvio del supporto `dataprep` in `automlstep`.

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning data Prep SDK v 1.1.10

+ **Nuove funzionalità**
  + È ora possibile richiedere l'esecuzione di controlli specifici, ad esempio istogramma, grafico a dispersione e così via, su colonne specifiche.
  + Aggiunto un argomento parallelizzare a `append_columns`. Se true, i dati verranno caricati in memoria, ma l'esecuzione verrà eseguita in parallelo; Se false, l'esecuzione sarà di flusso ma a thread singolo.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>SDK di Azure Machine Learning per Python v 1.0.53

+ **Nuove funzionalità**
  + Machine Learning automatizzato supporta ora i modelli ONNX di training nella destinazione di calcolo remota
  + Azure Machine Learning ora offre la possibilità di riprendere il training da un'esecuzione precedente, un checkpoint o un file di modello.
    + Informazioni su come [usare gli estimatori per riprendere il training da un'esecuzione precedente](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Correzioni di bug e miglioramenti**
  + **automl-client-Core-nativeClient**
    + Correzione del bug relativo alla perdita di tipi di colonne dopo la trasformazione (bug collegato); 
    + Consenti y_query essere un tipo di oggetto contenente nessuno all'inizio (#459519).
  + **azure-cli-ml**
    + I comandi dell'interfaccia della riga di comando "distribuzione modello" e "aggiornamento servizio" accettano ora parametri, file di configurazione o una combinazione dei due. I parametri hanno la precedenza sugli attributi nei file.
    + È ora possibile aggiornare la descrizione del modello dopo la registrazione
  + **azureml-automl-Core**
    + Aggiornare la dipendenza NimbusML alla versione 1.2.0 (Current Latest).
    + Aggiunta del supporto per gli estimatori Nimbus ML & le pipeline da usare all'interno degli estimatori AutoML.
    + Correzione di un bug nella procedura di selezione dell'insieme che inutilmente cresceva l'insieme risultante anche se i punteggi rimanevano costanti.
    + Consentire il riutilizzo di alcuni featurizations tra le divisioni del CV per le attività di previsione. Questo consente di velocizzare la fase di esecuzione dell'installazione fino a un fattore di n_cross_validations per featurizations costosi come i ritardi e le finestre in sequenza.
    + Risoluzione di un problema se il tempo è esterno all'intervallo di tempo supportato da Pandas. Viene ora generata un'eccezione DataException se l'ora è inferiore a PD. Timestamp. min o maggiore di PD. Timestamp. max
    + La previsione consente ora diverse frequenze nei set di training e di test se possono essere allineate. Ad esempio, "Quarterly a partire da gennaio" e a "Quarterly a partire da ottobre" possono essere allineati.
    + La proprietà "Parameters" è stata aggiunta a TimeSeriesTransformer.
    + Rimuovere le classi di eccezioni obsolete.
    + Nelle attività di previsione, il parametro `target_lags` accetta ora un singolo valore integer o un elenco di numeri interi. Se è stato specificato il valore integer, verrà creato solo un ritardo. Se viene specificato un elenco, verranno rilevati i valori univoci dei ritardi. target_lags = [1, 2, 2, 4] creerà i ritardi di uno, 2 e 4 punti.
    + Correzione del bug relativo alla perdita di tipi di colonne dopo la trasformazione (bug collegato);
    + In `model.forecast(X, y_query)`, consentire y_query essere un tipo di oggetto contenente nessuno all'inizio (#459519).
    + aggiungere i valori previsti a `automl` output
  + **azureml-contrib-datadrift**
    +  Miglioramenti al notebook di esempio, inclusa l'opzione passa a azureml-opendatasets anziché azureml-contrib-opendatasets e miglioramenti delle prestazioni quando si arricchiscono i dati
  + **azureml-contrib-explain-Model**
    + Correzione dell'argomento relativo alle trasformazioni per l'importanza della funzionalità RAW in azureml-contrib-explain-Model Package
    + Aggiunta di segmentazioni alle spiegazioni delle immagini in image Explainer per il pacchetto AzureML-contrib-explain-Model
    + Aggiunta del supporto di SciPy sparse per LimeExplainer
    + Aggiunta di `batch_size` a Mimit Explainer quando `include_local=False`, per la trasmissione di spiegazioni globali in batch per migliorare i tempi di esecuzione di DecisionTreeExplainableModel
  + **azureml-contrib-featureengineering**
    + Correzione per la chiamata di set_featurizer_timeseries_params (): modifica del tipo di valore dict e controllo null-Aggiungi notebook per `timeseries` featurizer
    + Aggiornare la dipendenza NimbusML alla versione 1.2.0 (Current Latest).
  + **azureml-core**
    + Aggiunta della possibilità di alleghire archivi dati DBFS nell'interfaccia della riga di comando di AzureML 
    + Correzione del bug relativo al caricamento dell'archivio dati in cui viene creata una cartella vuota se `target_path` è iniziata con `/`
    + Correzione `deepcopy` problema in ServicePrincipalAuthentication.
    + Sono stati aggiunti i comandi "AZ ml Environment Show" e "AZ ml Environment list" all'interfaccia della riga di comando.
    + Gli ambienti supportano ora la specifica di un base_dockerfile come alternativa a una base_image già compilata.
    + L'impostazione di RunConfiguration inutilizzata auto_prepare_environment è stata contrassegnata come deprecata.
    + È ora possibile aggiornare la descrizione del modello dopo la registrazione
    + Bugfix: Model and Image Delete offre ora ulteriori informazioni sul recupero di oggetti upstream che dipendono da essi se l'eliminazione non riesce a causa di una dipendenza upstream.
    + Correzione del bug che stampava una durata vuota per le distribuzioni che si verificano quando si crea un'area di lavoro per alcuni ambienti.
    + Eccezioni di creazione dell'area di lavoro migliorate. In modo che gli utenti non visualizzino "Impossibile creare l'area di lavoro. Impossibile trovare... " come messaggio e visualizzano invece l'effettivo errore di creazione.
    + Aggiungere il supporto per l'autenticazione del token in AKS WebServices. 
    + Aggiungere il metodo `get_token()` a oggetti `Webservice`.
    + Aggiunta del supporto dell'interfaccia della riga di comando per la gestione di set di impostazioni
    + `Datastore.register_azure_blob_container` ora accetta facoltativamente un valore `blob_cache_timeout` (in secondi) che configura i parametri di montaggio di blobfuse per abilitare la scadenza della cache per l'archivio dati. Il valore predefinito non prevede alcun timeout, ad esempio quando un BLOB viene letto, rimane nella cache locale fino al termine del processo. La maggior parte dei processi preferisce questa impostazione, ma alcuni processi devono leggere più dati da un set di dati di grandi dimensioni rispetto ai relativi nodi. Per questi processi, l'ottimizzazione di questo parametro ne consentirà l'esito positivo. Prestare attenzione quando si esegue l'ottimizzazione di questo parametro: l'impostazione di un valore troppo basso può comportare una riduzione delle prestazioni, in quanto i dati utilizzati in un'epoca possono scadere prima di essere riutilizzati. Ciò significa che tutte le letture verranno eseguite dall'archiviazione BLOB, ovvero dalla rete, anziché dalla cache locale, che influisce negativamente sui tempi di training.
    + La descrizione del modello può ora essere aggiornata correttamente dopo la registrazione
    + L'eliminazione di modelli e immagini offre ora ulteriori informazioni sugli oggetti upstream che dipendono da tali oggetti, causando l'esito negativo dell'eliminazione
    + Migliorare l'utilizzo delle risorse delle esecuzioni remote con azureml. mlflow.
  + **azureml-explain-model**
    + Correzione dell'argomento relativo alle trasformazioni per l'importanza della funzionalità RAW in azureml-contrib-explain-Model Package
    + Aggiunta del supporto di SciPy sparse per LimeExplainer
    + Aggiunta del wrapper per la spiegazione lineare di Shape, oltre a un altro livello di spiegazione tabulare per la spiegazione dei modelli lineari
    + per explain Explainer in explain Model Library, errore fisso quando include_local = false per l'input di dati di tipo sparse
    + aggiungere i valori previsti a `automl` output
    + priorità della funzionalità di permutazione fissa durante l'argomento delle trasformazioni fornito per ottenere l'importanza della funzionalità RAW
    + Aggiunta di `batch_size` a Mimit Explainer quando `include_local=False`, per la trasmissione di spiegazioni globali in batch per migliorare i tempi di esecuzione di DecisionTreeExplainableModel
    + per la libreria di spiegazione del modello, i Explainer di Blackbox fissi dove è richiesto l'input dei frame di dati Pandas per la stima
    + Correzione di un bug in cui `explanation.expected_values` restituiva a volte un valore float anziché un elenco con un valore float.
  + **azureml-mlflow**
    + Migliorare le prestazioni di mlflow. set_experiment (experiment_name)
    + Correzione di un bug nell'uso di InteractiveLoginAuthentication per mlflow tracking_uri
    + Migliorare l'utilizzo delle risorse delle esecuzioni remote con azureml. mlflow.
    + Migliorare la documentazione del pacchetto azureml-mlflow
    + Patch bug where mlflow. log_artifacts ("my_dir") Salva gli artefatti in "my_dir/< artefatto-percorsi >" anziché "< artefatto-percorsi >"
  + **azureml-opendatasets**
    + Aggiungere `pyarrow` di `opendatasets` alle versioni precedenti (< 0.14.0) a causa di un problema di memoria appena introdotto.
    +  Spostare azureml-contrib-opendatasets in azureml-opendatasets. -Consentire la registrazione delle classi del set di dati aperte nell'area di lavoro di AML e sfruttare facilmente le funzionalità del set di dati AML. -Migliorare significativamente le prestazioni di NoaaIsdWeather in una versione non SPARK.
  + **azureml-pipeline-steps**
    + DBFS datastore è ora supportato per gli input e gli output in DatabricksStep.
    + Documentazione aggiornata per Azure Batch step per quanto riguarda gli input/output.
    + In AzureBatchStep è stato modificato *delete_batch_job_after_finish* valore predefinito in *true*.
  + **azureml-telemetria**
    +  Spostare azureml-contrib-opendatasets in azureml-opendatasets. -Consentire la registrazione delle classi del set di dati aperte nell'area di lavoro di AML e sfruttare facilmente le funzionalità del set di dati AML. -Migliorare significativamente le prestazioni di NoaaIsdWeather in una versione non SPARK.
  + **azureml-train-automl**
    + Documentazione aggiornata su get_output per riflettere il tipo restituito effettivo e fornire note aggiuntive sul recupero delle proprietà chiave.
    + Aggiornare la dipendenza NimbusML alla versione 1.2.0 (Current Latest).
    + aggiungere i valori previsti a `automl` output
  + **azureml-train-core**
    + Le stringhe sono ora accettate come destinazione di calcolo per l'ottimizzazione automatica degli iperparametri
    + L'impostazione di RunConfiguration inutilizzata auto_prepare_environment è stata contrassegnata come deprecata.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning data Prep SDK v 1.1.9

+ **Nuove funzionalità**
  + Aggiunta del supporto per la lettura di un file direttamente da un URL http o HTTPS.

+ **Correzioni di bug e miglioramenti**
  + Messaggio di errore migliorato durante il tentativo di lettura di un set di dati parquet da un'origine remota, che non è attualmente supportata.
  + Correzione di un bug durante la scrittura in un formato di file parquet in ADLS gen 2 e l'aggiornamento del nome del contenitore ADLS gen 2 nel percorso.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Interfaccia visiva
+ **Funzionalità di anteprima**
  + È stato aggiunto il modulo "Execute R script" nell'interfaccia visiva.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>SDK di Azure Machine Learning per Python v 1.0.48

+ **Nuove funzionalità**
  + **azureml-opendatasets**
    + **azureml-contrib-opendatasets** è ora disponibile come **azureml-opendatasets**. Il pacchetto precedente può ancora funzionare, ma è consigliabile usare **azureml-opendatasets** in futuro per migliorare le funzionalità e i miglioramenti.
    + Questo nuovo pacchetto consente di registrare i set di dati aperti come set di dati nell'area di lavoro AML e di sfruttare le funzionalità offerte dal set di dati.
    + Sono incluse anche le funzionalità esistenti, ad esempio l'utilizzo di set di dati aperti come Panda/frame di dati di SPARK e i join della posizione per alcuni set di dati come Weather.

+ **Funzionalità di anteprima**
    + HyperDriveConfig è ora in grado di accettare un oggetto pipeline come parametro per supportare l'ottimizzazione degli iperparametri tramite una pipeline.

+ **Correzioni di bug e miglioramenti**
  + **azureml-train-automl**
    + Correzione del bug relativo alla perdita di tipi di colonne dopo la trasformazione.
    + Correzione del bug per consentire y_query essere un tipo di oggetto contenente nessuno all'inizio. 
    + Correzione del problema nella procedura di selezione dell'insieme che inutilmente cresceva l'insieme risultante anche se i punteggi rimanevano costanti.
    + Correzione del problema relativo alle impostazioni whitelist_models e blacklist_models in AutoMLStep.
    + Correzione del problema che impediva l'uso della pre-elaborazione quando AutoML sarebbe stato usato nel contesto delle pipeline di Azure ML.
  + **azureml-opendatasets**
    + Spostamento di azureml-contrib-opendatasets in azureml-opendatasets.
    + È possibile registrare le classi del set di dati aperte nell'area di lavoro AML e sfruttare le funzionalità del set di dati AML.
    + Miglioramento delle prestazioni di NoaaIsdWeather arricchito in modo significativo nella versione non SPARK.
  + **azureml-explain-model**
    + Documentazione online aggiornata per gli oggetti di interpretazione.
    + Aggiunta di `batch_size` a Mimit Explainer quando `include_local=False`, per la trasmissione di spiegazioni globali in batch per migliorare il tempo di esecuzione di DecisionTreeExplainableModel per la libreria di spiegazione del modello.
    + È stato risolto il problema per cui `explanation.expected_values` restituiva a volte un valore float anziché un elenco con un valore float.
    + Sono stati aggiunti i valori previsti per `automl` output per Mimic Explainer nella libreria di modelli explain.
    + Priorità della funzionalità di permutazione fissa quando l'argomento delle trasformazioni viene fornito per ottenere la funzionalità RAW.
  + **azureml-core**
    + Aggiunta della possibilità di alleghire gli archivi dati DBFS nell'interfaccia della riga di comando di AzureML.
    + Correzione del problema relativo al caricamento dell'archivio dati in cui viene creata una cartella vuota se `target_path` è iniziata con `/`.
    + È stato abilitato il confronto di due set di impostazioni.
    + Model and Image Delete offre ora ulteriori informazioni sul recupero di oggetti upstream che dipendono da essi se l'eliminazione non riesce a causa di una dipendenza upstream.
    + L'impostazione RunConfiguration non usata è stata deprecata in auto_prepare_environment.
  + **azureml-mlflow**
    + Miglioramento dell'utilizzo delle risorse delle esecuzioni remote che usano azureml. mlflow.
    + È stata migliorata la documentazione del pacchetto azureml-mlflow.
    + È stato risolto il problema per cui mlflow. log_artifacts ("my_dir") avrebbe salvato gli artefatti in "my_dir/Artifact-paths" invece di "artefatti-percorsi".
  + **azureml-pipeline-core**
    + Il parametro hash_paths per tutti i passaggi della pipeline è deprecato e verrà rimosso in futuro. Per impostazione predefinita, viene eseguito l'hashing del contenuto della source_directory (eccetto i file elencati in. amlignore o. gitignore)
    + Continuando a migliorare il modulo e ModuleStep per supportare i moduli specifici del tipo di calcolo, preparare l'integrazione con RunConfiguration e altre modifiche per sbloccare l'utilizzo del modulo specifico del tipo di calcolo nelle pipeline.
  + **azureml-pipeline-steps**
    + AzureBatchStep: documentazione migliorata per quanto riguarda gli input/output.
    + AzureBatchStep: modificato delete_batch_job_after_finish valore predefinito in true.
  + **azureml-train-core**
    + Le stringhe sono ora accettate come destinazione di calcolo per l'ottimizzazione automatica degli iperparametri.
    + L'impostazione RunConfiguration non usata è stata deprecata in auto_prepare_environment.
    + Parametri deprecati `conda_dependencies_file_path` e `pip_requirements_file_path` a favore di `conda_dependencies_file` e `pip_requirements_file` rispettivamente.
  + **azureml-opendatasets**
    + Migliorare significativamente le prestazioni di NoaaIsdWeather in una versione non SPARK.

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Azure Machine Learning data Prep SDK v 1.1.8

+ **Nuove funzionalità**
 + È ora possibile eseguire l'iterazione degli oggetti del flusso di dati, producendo una sequenza di record. Vedere la documentazione per `Dataflow.to_record_iterator`.
  + È ora possibile eseguire l'iterazione degli oggetti del flusso di dati, producendo una sequenza di record. Vedere la documentazione per `Dataflow.to_record_iterator`.

+ **Correzioni di bug e miglioramenti**
 + Miglioramento dell'affidabilità di dataprep SDK.
 + Gestione migliorata dei frame di frame Pandas con indici di colonna non stringa.
 + Miglioramento delle prestazioni di `to_pandas_dataframe` nei set di impostazioni.
 + Correzione di un bug per cui l'esecuzione Spark dei set di impostazioni non è riuscita quando viene eseguita in un ambiente a più nodi.
  + Miglioramento dell'affidabilità di dataprep SDK.
  + Gestione migliorata dei frame di frame Pandas con indici di colonna non stringa.
  + Miglioramento delle prestazioni di `to_pandas_dataframe` nei set di impostazioni.
  + Correzione di un bug per cui l'esecuzione Spark dei set di impostazioni non è riuscita quando viene eseguita in un ambiente a più nodi.

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Azure Machine Learning data Prep SDK v 1.1.7

È stata ripristinata una modifica che ha migliorato le prestazioni, in quanto causava problemi per alcuni clienti che utilizzavano Azure Databricks. Se si è verificato un problema in Azure Databricks, è possibile eseguire l'aggiornamento alla versione 1.1.7 usando uno dei metodi seguenti:
1. Eseguire questo script per eseguire l'aggiornamento: `%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. Ricreare il cluster, che installerà la versione più recente di data Prep SDK.

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>SDK di Azure Machine Learning per Python v 1.0.45

+ **Nuove funzionalità**
  + Aggiungere un modello di surrogato dell'albero delle decisioni a Mimit Explainer in azureml-explain-Model Package
  + Possibilità di specificare una versione di CUDA da installare per le immagini di inferenza. Supporto per CUDA 9,0, 9,1 e 10,0.
  + Informazioni sulle immagini di base di training di Azure ML sono ora disponibili nell' [Archivio GitHub di contenitori di Azure ml](https://github.com/Azure/AzureML-Containers) e [DockerHub](https://hub.docker.com/_/microsoft-azureml)
  + Aggiunta del supporto CLI per la pianificazione della pipeline. Per ulteriori informazioni, eseguire "AZ ml pipeline-h"
  + Aggiunta del parametro dello spazio dei nomi Kubernetes personalizzato alla configurazione della distribuzione del servizio Web AKS e CLI.
  + Parametro hash_paths deprecato per tutti i passaggi della pipeline
  + Model. Register supporta ora la registrazione di più singoli file come un unico modello con l'uso del parametro `child_paths`.
  
+ **Funzionalità di anteprima**
    + Gli Explainer con punteggio possono ora salvare le informazioni conda e PIP per la serializzazione e la deserializzazione più affidabili.
    + Correzione di bug per il selettore automatico delle funzionalità.
    + Aggiornamento di mlflow. azureml. build_image alla nuova API, bug con patch esposti dalla nuova implementazione.

+ **Correzioni di bug e miglioramenti**
  + Rimuovere `paramiko` dipendenza da azureml-core. Aggiunta di avvisi di deprecazione per i metodi di associazione di destinazione di calcolo legacy.
  + Migliorare le prestazioni dell'esecuzione. create_children
  + In MIME Explainer with binary Classificator, correggere l'ordine delle probabilità quando viene usata la probabilità del docente per ridimensionare i valori della forma.
  + Miglioramento della gestione degli errori e del messaggio per Machine Learning automatizzato. 
  + Correzione del problema di timeout dell'iterazione per Machine Learning automatizzato.
  + Miglioramento delle prestazioni di trasformazione delle serie temporali per Machine Learning automatizzato.

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Azure Machine Learning data Prep SDK v 1.1.6

+ **Nuove funzionalità**
  + Sono state aggiunte funzioni di riepilogo per i valori principali (`SummaryFunction.TOPVALUES`) e i valori inferiori (`SummaryFunction.BOTTOMVALUES`).

+ **Correzioni di bug e miglioramenti**
  + Miglioramento significativo delle prestazioni di `read_pandas_dataframe`.
  + Correzione di un bug che provocava l'errore `get_profile()` in un flusso di dati che puntava a file binari.
  + Esposto `set_diagnostics_collection()` per consentire l'abilitazione o la disabilitazione della raccolta di dati di telemetria a livello di codice.
  + Modifica del comportamento di `get_profile()`. I valori NaN vengono ora ignorati per min, Mean, STD e Sum, che è allineato al comportamento di Pandas.


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>SDK di Azure Machine Learning per Python v 1.0.43

+ **Nuove funzionalità**
  + Azure Machine Learning offre ora il supporto di prima classe per l'apprendimento automatico e il Framework di analisi dei dati più diffusi Scikit-learn. Con [`SKLearn` Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py), gli utenti possono eseguire facilmente il training e la distribuzione di modelli Scikit-learn.
    + Informazioni su come [eseguire l'ottimizzazione di iperparametri con Scikit-learn usando](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb)iperguida.
  + Aggiunto il supporto per la creazione di ModuleStep nelle pipeline insieme alle classi Module e ModuleVersion per gestire le unità di calcolo riutilizzabili.
  + I servizi WebService di ACI supportano ora scoring_uri permanenti tramite aggiornamenti. Il scoring_uri cambierà da IP a FQDN. L'etichetta del nome DNS per FQDN può essere configurata impostando il dns_name_label su deploy_configuration. 
  + Nuove funzionalità automatiche di Machine Learning:
    + Featurizer STL per la previsione
    + Il clustering KMeans è abilitato per lo sweep delle funzionalità
  + Le approvazioni della quota AmlCompute sono diventate più veloci. Il processo è stato automatizzato per approvare le richieste di quota entro una soglia. Per ulteriori informazioni sul funzionamento delle quote, vedere la pagina relativa [alla gestione delle quote](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).

+ **Funzionalità di anteprima**
    + Integrazione con [MLflow](https://mlflow.org) 1.0.0 tracking tramite il pacchetto azureml-MLflow ([notebook di esempio](https://aka.ms/azureml-mlflow-examples)).
    + Inviare Jupyter notebook come esecuzione. [Documentazione di riferimento per le API](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + Anteprima pubblica del [rilevamento](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift?view=azure-ml-py) della derivazione dei dati tramite il pacchetto azureml-contrib-datadrift ([notebook di esempio](https://aka.ms/azureml-datadrift-example)). La deviazione dei dati è uno dei motivi principali per cui l'accuratezza del modello diminuisce nel tempo. Si verifica quando i dati serviti al modello in produzione sono diversi da quelli sui quali è stato eseguito il training del modello. AML data Drift Detector consente ai clienti di monitorare la tendenza dei dati e invia un avviso ogni volta che viene rilevata una tendenza. 

+ **Modifiche di rilievo**

+ **Correzioni di bug e miglioramenti**
  + RunConfiguration Load e Save supporta la specifica di un percorso di file completo con compatibilità completa per il comportamento precedente.
  + Aggiunta della memorizzazione nella cache in ServicePrincipalAuthentication, disattivata per impostazione predefinita.
  + Abilitare la registrazione di più tracciati con lo stesso nome di metrica.
  + Classe modello ora importabile correttamente da azureml. Core (`from azureml.core import Model`).
  + Nei passaggi della pipeline, il parametro `hash_path` è ora deprecato. Un nuovo comportamento consiste nell'eseguire l'hashing completo source_directory, ad eccezione dei file elencati in. amlignore o. gitignore.
  + Nei pacchetti della pipeline, i vari metodi `get_all` e `get_all_*` sono stati deprecati a favore di `list` e `list_*` rispettivamente.
  + azureml. Core. get_run non richiede più le classi da importare prima di restituire il tipo di esecuzione originale.
  + È stato risolto un problema per cui alcune chiamate all'aggiornamento WebService non hanno attivato un aggiornamento.
  + Il timeout di assegnazione dei punteggi per i servizi WebService AKS deve essere compreso tra 5 ms e, che indica. Il numero massimo consentito di scoring_timeout_ms per le richieste di assegnazione dei punteggi è stato raggiunto da 1 min a 5 minuti.
  + Gli oggetti LocalWebservice hanno ora proprietà `scoring_uri` e `swagger_uri`.
  + La creazione della directory degli output è stata spostata e il caricamento della directory è stato eliminato dal processo utente. Abilitazione dell'SDK della cronologia di esecuzione per l'esecuzione in ogni processo utente. Questa operazione dovrebbe risolvere alcuni problemi di sincronizzazione riscontrati dalle esecuzioni di training distribuite.
  + Il nome del log azureml scritto dal nome del processo utente includerà ora il nome del processo (solo per il training distribuito) e il PID.

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Azure Machine Learning data Prep SDK v 1.1.5

+ **Correzioni di bug e miglioramenti**
  + Per i valori DateTime interpretati con formato di anno a 2 cifre, l'intervallo di anni validi è stato aggiornato in base alla versione di Windows. L'intervallo è stato modificato da 1930-2029 a 1950-2049.
  + Quando si esegue la lettura in un file e si imposta `handleQuotedLineBreaks=True`, `\r` verrà considerato come una nuova riga.
  + Correzione di un bug che causava l'esito negativo di `read_pandas_dataframe` in alcuni casi.
  + Miglioramento delle prestazioni di `get_profile`.
  + Messaggi di errore migliorati.

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Azure Machine Learning data Prep SDK v 1.1.4

+ **Nuove funzionalità**
  + È ora possibile usare le funzioni del linguaggio delle espressioni seguenti per estrarre e analizzare i valori DateTime in nuove colonne.
    + `RegEx.extract_record()` estrae gli elementi DateTime in una nuova colonna.
    + `create_datetime()` crea oggetti DateTime da elementi DateTime distinti.
  + Quando si chiama `get_profile()`, è ora possibile notare che le colonne quantile sono etichettate come (est.) per indicare chiaramente che i valori sono approssimazioni.
  + È ora possibile usare * * glob durante la lettura dall'archiviazione BLOB di Azure.
    + Ad esempio: `dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **Correzioni di bug**
  + Correzione di un bug relativo alla lettura di un file parquet da un'origine remota (BLOB di Azure).

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>SDK di Azure Machine Learning per Python v 1.0.39
+ **Modifiche**
  + L'opzione Esegui configurazione auto_prepare_environment verrà deprecata e la preparazione automatica diventerà il valore predefinito.

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Azure Machine Learning data Prep SDK v 1.1.3

+ **Nuove funzionalità**
  + È stato aggiunto il supporto per la lettura da un database PostgreSQL, chiamando read_postgresql o usando un archivio dati.
    + Vedere gli esempi nelle guide alle procedure:
      + [Notebook di inserimento dati](https://aka.ms/aml-data-prep-ingestion-nb)
      + [Notebook archivio dati](https://aka.ms/aml-data-prep-datastore-nb)

+ **Correzioni di bug e miglioramenti**
  + Correzione dei problemi relativi alla conversione del tipo di colonna:
  + Ora converte correttamente una colonna booleana o numerica in una colonna booleana.
  + Ora non ha esito negativo quando si tenta di impostare una colonna della data come tipo di data.
  + Tipi di JoinType migliorati e documentazione di riferimento. Quando si uniscono due flussi di data, è ora possibile specificare uno di questi tipi di join:
    + NONE, MATCH, INNER, UNMATCHLEFT, LEFTANTI, LEFTOUTER, UNMATCHRIGHT, RIGHTANTI, RIGHTOUTER, FULLANTI, FULL.
  + Miglioramento dell'inferenza del tipo di dati per riconoscere più formati di data.

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Portale di Azure

In portale di Azure ora è possibile:
+ Creazione ed esecuzione di esperimenti di Machine Learning automatici 
+ Creare una macchina virtuale notebook per provare ad esempio notebook Jupyter o personalizzati.
+ Sezione relativa alla creazione di una nuova versione (anteprima) nell'area di lavoro del servizio Machine Learning, che include Machine Learning automatizzata, interfaccia visiva e macchine virtuali del notebook ospitate
    + Creare automaticamente un modello usando Machine Learning automatico 
    + Usare un'interfaccia visiva di trascinamento per eseguire esperimenti
    + Creare una macchina virtuale notebook per esplorare i dati, creare modelli e distribuire servizi.
+ Aggiornamento della metrica e del grafico in tempo reale in eseguire report ed eseguire pagine dei dettagli
+ Aggiornamento del Visualizzatore file per log, output e snapshot nelle pagine dei dettagli di esecuzione.
+ Esperienza di creazione di report nuova e migliorata nella scheda esperimenti. 
+ È stata aggiunta la possibilità di scaricare il file config. JSON dalla pagina Panoramica dell'area di lavoro Azure Machine Learning.
+ Supporto Machine Learning creazione dell'area di lavoro del servizio dall'area di lavoro Azure Databricks 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>SDK di Azure Machine Learning per Python v 1.0.33
+ **Nuove funzionalità**
  + Il metodo _Workspace. Create_ accetta ora configurazioni cluster predefinite per i cluster CPU e GPU.
  + Se la creazione dell'area di lavoro non riesce, le risorse dipendenti vengono pulite.
  + Lo SKU di Azure Container Registry predefinito è stato impostato su Basic.
  + Azure Container Registry viene creato in modo differito, quando necessario per la creazione di un'esecuzione o di un'immagine.
  + Supporto per gli ambienti per le esecuzioni di training.

### <a name="notebook-virtual-machine"></a>Macchina virtuale notebook 

Usare una macchina virtuale notebook come ambiente host sicuro e pronto per l'azienda per notebook di Jupyter in cui è possibile programmare esperimenti di Machine Learning, distribuire modelli come endpoint Web ed eseguire tutte le altre operazioni supportate da Azure Machine Learning SDK usando Python. Offre diverse funzionalità:
+ [Creare rapidamente una macchina virtuale notebook preconfigurata](tutorial-1st-experiment-sdk-setup.md) che disponga della versione più recente di Azure Machine Learning SDK e dei pacchetti correlati.
+ L'accesso è protetto tramite tecnologie collaudate, ad esempio HTTPS, Azure Active Directory l'autenticazione e l'autorizzazione.
+ Archiviazione cloud affidabile di notebook e codice nell'account di archiviazione BLOB area di lavoro di Azure Machine Learning. È possibile eliminare in modo sicuro la VM del notebook senza perdere il lavoro.
+ Notebook di esempio preinstallati per esplorare e sperimentare le funzionalità di Azure Machine Learning.
+ Funzionalità di personalizzazione complete delle macchine virtuali di Azure, qualsiasi tipo di macchina virtuale, tutti i pacchetti e tutti i driver. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Rilasciata Azure Machine Learning SDK per Python v 1.0.33.

+ Azure ML Modelli con accelerazione hardware su [FPGA](concept-accelerate-with-fpgas.md) è disponibile a livello generale.
  + È ora possibile [usare il pacchetto azureml-Accel-Models](how-to-deploy-fpga-web-service.md) per:
    + Eseguire il training dei pesi di una rete neurale profonda supportata (ResNet 50, ResNet 152, DenseNet-121, VGG-16 e SSD-VGG)
    + Usare il trasferimento di formazione con il DNN supportato
    + Registrare il modello con Gestione modelli Service e distribuire il modello
    + Distribuire il modello in una macchina virtuale di Azure con un FPGA in un cluster Azure Kubernetes Service (AKS)
  + Distribuire il contenitore in un dispositivo di [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) server
  + Assegnare un punteggio ai dati con l'endpoint gRPC con questo [esempio](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Funzionalità automatizzate di Machine Learning

+ Sweep delle funzionalità per consentire l'aggiunta dinamica di :::no-loc text="featurizers"::: per l'ottimizzazione delle prestazioni. Nuovi :::no-loc text="featurizers":::: incorporamenti di lavoro, peso dell'evidenza, codifiche di destinazione, codifica della destinazione del testo, distanza del cluster
+ Smart CV per gestire divisioni di training/valide all'interno di Machine Learning automatiche
+ Poche modifiche all'ottimizzazione per la memoria e miglioramento delle prestazioni di runtime
+ Miglioramento delle prestazioni nella spiegazione del modello
+ Conversione del modello ONNX per l'esecuzione locale
+ Aggiunta del supporto per sottocampionamento
+ Arresto intelligente quando non sono definiti criteri di uscita
+ Insiemi in pila

+ Previsione delle serie temporali
  + Nuova funzione di previsione stima   
  + È ora possibile usare la convalida incrociata di origine in sequenza sui dati di serie temporali
  + Nuove funzionalità aggiunte per configurare i GAL delle serie temporali 
  + Nuove funzionalità aggiunte per supportare le funzionalità di aggregazione della finestra in sequenza
  + Nuovo rilevamento festivo e featurizer quando il codice paese viene definito nelle impostazioni dell'esperimento

+ Azure Databricks
  + Abilitazione della previsione della serie temporale e della funzionalità di interpretazione/explainabilty del modello
  + È ora possibile annullare e riprendere (continuare) gli esperimenti di Machine Learning automatici
  + Aggiunta del supporto per l'elaborazione multicore

### <a name="mlops"></a>MLOps
+ **Distribuzione locale & debug per i contenitori di Punteggio**<br/> È ora possibile distribuire un modello ML in locale ed eseguire un'iterazione rapida sul file di assegnazione dei punteggi e sulle dipendenze per assicurarsi che si comportino come previsto.

+ **Introduzione di InferenceConfig & Model. Deploy ()**<br/> La distribuzione del modello supporta ora la specifica di una cartella di origine con uno script di immissione, uguale a un RunConfig.  Inoltre, la distribuzione del modello è stata semplificata in un unico comando.

+ **Rilevamento dei riferimenti git**<br/> I clienti hanno richiesto le funzionalità di integrazione git di base per un certo periodo di tempo, in quanto consente di mantenere una audit trail end-to-end. È stato implementato il rilevamento tra le entità principali in Azure ML per i metadati correlati a git (repository, commit, stato pulito). Queste informazioni verranno raccolte automaticamente dall'SDK e dall'interfaccia della riga di comando.

+ **Profilatura del modello & servizio di convalida**<br/> I clienti si lamentano spesso della difficoltà di dimensionare correttamente le risorse di calcolo associate al servizio di inferenza. Con il servizio di profilatura del modello, il cliente può fornire input di esempio e profilare in 16 diverse configurazioni di CPU/memoria per determinare il dimensionamento ottimale per la distribuzione.

+ **Importare un'immagine di base personalizzata per l'inferenza**<br/> Un altro problema comune è stato la difficoltà nel passaggio dalla sperimentazione alle dipendenze di ricondivisione. Con la nuova funzionalità di condivisione delle immagini di base, è ora possibile riutilizzare le immagini di base di sperimentazione, le dipendenze e tutti, per l'inferenza. Questa operazione dovrebbe velocizzare le distribuzioni e ridurre il gap dal ciclo interno a quello esterno.

+ **Esperienza di generazione dello schema spavalderia migliorata**<br/> Il metodo di generazione spavalderia precedente è stato soggetto a errori e non può essere automatizzato. È disponibile un nuovo metodo inline per la generazione di schemi di spavalderia da qualsiasi funzione Python tramite elementi Decorator. Questo codice è open source e il protocollo di generazione dello schema non è associato alla piattaforma Azure ML.

+ **Interfaccia della riga di comando di Azure ML è disponibile a livello generale (GA)**<br/> È ora possibile distribuire i modelli con un unico comando CLI. Abbiamo ricevuto commenti e suggerimenti dei clienti comuni che nessuno distribuisce un modello ML da un notebook Jupyter. La [**documentazione di riferimento dell'interfaccia**](https://aka.ms/azmlcli) della riga di comando è stata aggiornata.


## <a name="2019-04-22"></a>2019-04-22

Rilasciata Azure Machine Learning SDK per Python v 1.0.30.

Il [`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) è stato introdotto per aggiungere una nuova versione di una pipeline pubblicata mantenendo lo stesso endpoint.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure Machine Learning data Prep SDK v 1.1.2

Nota: la preparazione dei dati Python SDK non installerà più `numpy` e `pandas` i pacchetti. Vedere [le istruzioni di installazione aggiornate](https://aka.ms/aml-data-prep-installation).

+ **Nuove funzionalità**
  + È ora possibile usare la trasformazione pivot.
    + Guida dettagliata: [blocco appunti di pivot](https://aka.ms/aml-data-prep-pivot-nb)
  + È ora possibile usare le espressioni regolari in funzioni native.
    + Esempi:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + È ora possibile usare le funzioni `to_upper` e `to_lower` nel linguaggio delle espressioni.
  + È ora possibile visualizzare il numero di valori univoci di ogni colonna in un profilo dati.
  + Per alcuni dei passaggi di Reader usati di frequente, è ora possibile passare l'argomento `infer_column_types`. Se è impostato su `True`, la preparazione dei dati tenterà di rilevare e convertire automaticamente i tipi di colonna.
    + `inference_arguments` è ora deprecata.
  + È ora possibile chiamare `Dataflow.shape`.

+ **Correzioni di bug e miglioramenti**
  + `keep_columns` ora accetta un argomento facoltativo aggiuntivo `validate_column_exists`, che controlla se il risultato di `keep_columns` conterrà le colonne.
  + Tutti i passaggi del lettore, che leggono da un file, accettano ora un argomento facoltativo aggiuntivo `verify_exists`.
  + Miglioramento delle prestazioni di lettura dal dataframe Pandas e recupero dei profili dati.
  + Correzione di un bug per cui il sezionamento di un singolo passaggio da un flusso di un flusso di un flusso di un oggetto non è riuscito

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Portale di Azure
  + È ora possibile inviare nuovamente uno script esistente eseguito in un cluster di elaborazione remota esistente. 
  + È ora possibile eseguire una pipeline pubblicata con nuovi parametri nella scheda Pipeline. 
  + I dettagli dell'esecuzione supportano ora un nuovo Visualizzatore file di snapshot. È possibile visualizzare uno snapshot della directory quando è stata inviata un'esecuzione specifica. È anche possibile scaricare il notebook che è stato inviato per avviare l'esecuzione.
  + È ora possibile annullare le esecuzioni padre dal portale di Azure.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>SDK di Azure Machine Learning per Python v 1.0.23

+ **Nuove funzionalità**
  + Il Azure Machine Learning SDK supporta ora Python 3,7.
  + Azure Machine Learning DNN Estimator ora fornisce supporto integrato per più versioni. Ad esempio, `TensorFlow` Estimator ora accetta un parametro `framework_version` e gli utenti possono specificare la versione "1,10" o "1,12". Per un elenco delle versioni supportate dalla versione corrente dell'SDK, chiamare `get_supported_versions()` sulla classe Framework desiderata, ad esempio `TensorFlow.get_supported_versions()`.
  Per un elenco delle versioni supportate dalla versione più recente dell'SDK, vedere la [documentazione di DNN Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py).

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure Machine Learning data Prep SDK v 1.1.1

+ **Nuove funzionalità**
  + È possibile leggere più origini archivio dati, DataPath/DataReference usando le trasformazioni read_ *.
  + È possibile eseguire le operazioni seguenti sulle colonne per creare una nuova colonna, ovvero divisione, piano, modulo, potenza, lunghezza.
  + La preparazione dei dati fa ora parte della suite di diagnostica di Azure ML e registrerà le informazioni di diagnostica per impostazione predefinita.
    + Per disattivare questa impostazione, impostare questa variabile di ambiente su true: DISABLE_DPREP_LOGGER

+ **Correzioni di bug e miglioramenti**
  + Documentazione di codice migliorata per le classi e le funzioni di uso comune.
  + Correzione di un bug in auto_read_file che non è riuscito a leggere i file di Excel.
  + Aggiunta dell'opzione per sovrascrivere la cartella in read_pandas_dataframe.
  + Miglioramento delle prestazioni dell'installazione delle dipendenze di dotnetcore2 e aggiunta del supporto per Fedora 27/28 e Ubuntu 1804.
  + Miglioramento delle prestazioni di lettura dai BLOB di Azure.
  + Il rilevamento del tipo di colonna supporta ora le colonne di tipo Long.
  + Correzione di un bug in cui alcuni valori di data venivano visualizzati come timestamp anziché come oggetti DateTime di Python.
  + Correzione di un bug in cui i conteggi dei tipi venivano visualizzati come doppi anziché come valori integer.

  
## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>SDK di Azure Machine Learning per Python v 1.0.21

+ **Nuove funzionalità**
  + Il metodo *azureml. Core. Run. create_children* consente la creazione a bassa latenza di più esecuzioni figlio con una singola chiamata.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure Machine Learning data Prep SDK v 1.1.0

+ **Modifiche di rilievo**
  + Il concetto di pacchetto di preparazione dei dati è stato deprecato e non è più supportato. Anziché salvare in maniera permanente più flussi di data in un pacchetto, è possibile salvare in maniera permanente i flussi di data.
    + Guida alle procedure: [apertura e salvataggio del notebook di flussi di data](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **Nuove funzionalità**
  + La preparazione dei dati può ora riconoscere le colonne che corrispondono a un particolare tipo semantico e dividere di conseguenza. Gli STypes attualmente supportati includono: indirizzo di posta elettronica, Coordinate geografiche (Latitudine & Longitudine), indirizzi IPv4 e IPv6, numero di telefono degli Stati Uniti e codice postale degli Stati Uniti.
    + Guida alle procedure: [notebook di tipi semantici](https://aka.ms/aml-data-prep-semantic-types-nb)
  + La preparazione dei dati supporta ora le operazioni seguenti per generare una colonna risultante da due colonne numeriche: sottrazione, moltiplicazione, divisione e modulo.
  + È possibile chiamare `verify_has_data()` su un flusso di dati per verificare se il flusso di dati produrrebbe record se eseguiti.

+ **Correzioni di bug e miglioramenti**
  + È ora possibile specificare il numero di contenitori da usare in un istogramma per i profili di colonna numerici.
  + Per la trasformazione `read_pandas_dataframe` è ora necessario che il dataframe includa nomi di colonna di tipo stringa o di tipo byte.
  + Correzione di un bug nella trasformazione `fill_nulls`, in cui i valori non venivano compilati correttamente se la colonna non è presente.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>SDK di Azure Machine Learning per Python v 1.0.18

 + **Modifiche**
   + Il pacchetto azureml-tensorboard sostituisce azureml-contrib-tensorboard.
   + Con questa versione è possibile configurare un account utente nel cluster di calcolo gestito (amlcompute), durante la creazione. Questa operazione può essere eseguita passando queste proprietà nella configurazione del provisioning. Per ulteriori informazioni, vedere la [documentazione di riferimento dell'SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--).

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning data Prep SDK v 1.0.17

+ **Nuove funzionalità**
  + Supporta ora l'aggiunta di due colonne numeriche per generare una colonna risultante utilizzando il linguaggio delle espressioni.

+ **Correzioni di bug e miglioramenti**
  + Miglioramento della documentazione e del controllo dei parametri per random_split.
  
## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning data Prep SDK v 1.0.16

+ **Correzione di bug**
  + Correzione di un problema di autenticazione dell'entità servizio causato da una modifica dell'API.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>SDK di Azure Machine Learning per Python v 1.0.17

+ **Nuove funzionalità**

  + Azure Machine Learning offre ora il supporto di primo livello per il famoso Chainer DNN Framework. L'uso di [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) classe gli utenti possono eseguire facilmente il training e la distribuzione di modelli di Chainer.
    + Informazioni su come [eseguire il training distribuito con ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + Informazioni su come [eseguire l'ottimizzazione di iperparametri con il Chainer usando](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) l'iperguida
  + Azure Machine Learning le pipeline hanno aggiunto la possibilità di attivare un'esecuzione della pipeline in base alle modifiche dell'archivio dati. Il [notebook della pianificazione](https://aka.ms/pl-schedule) della pipeline è stato aggiornato per illustrare questa funzionalità.

+ **Correzioni di bug e miglioramenti**
  + Sono state aggiunte le pipeline di supporto Azure Machine Learning per impostare la proprietà source_directory_data_store su un archivio dati desiderato, ad esempio un archivio BLOB, in [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) che vengono fornite al [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py). Per impostazione predefinita, l'archivio file di Azure viene usato come archivio dati di backup, che può comportare problemi di limitazione quando viene eseguito contemporaneamente un numero elevato di passaggi.

### <a name="azure-portal"></a>Portale di Azure

+ **Nuove funzionalità**
  + Nuova esperienza di Editor tabelle di trascinamento per i report. Gli utenti possono trascinare una colonna dal pozzetto all'area della tabella in cui verrà visualizzata un'anteprima della tabella. È possibile ridisporre le colonne.
  + Nuovo Visualizzatore file di log
  + Collegamenti a esecuzioni di esperimenti, calcolo, modelli, immagini e distribuzioni dalla scheda attività

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure Machine Learning data Prep SDK v 1.0.15

+ **Nuove funzionalità**
  + La preparazione dati supporta ora la scrittura di flussi di file da un flusso di dati. Consente inoltre di modificare i nomi dei flussi di file per creare nuovi nomi di file.
    + Guida alle procedure: [uso del notebook di flussi di file](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Correzioni di bug e miglioramenti**
  + Miglioramento delle prestazioni del digest t su set di dati di grandi dimensioni.
  + La preparazione dati supporta ora la lettura di dati da un percorso dati.
  + Una codifica a caldo ora funziona con colonne booleane e numeriche.
  + Altre correzioni di bug varie.

## <a name="2019-02-11"></a>11 febbraio 2019

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Azure Machine Learning SDK per Python v1.0.15

+ **Nuove funzionalità**
  + Azure Machine Learning pipeline hanno aggiunto AzureBatchStep ([notebook](https://aka.ms/pl-azbatch)), HyperDriveStep (notebook) e la funzionalità di pianificazione basata sul tempo ([notebook](https://aka.ms/pl-schedule)).
  +  È stato effettuato l'aggiornamento di DataTranferStep per garantire il funzionamento con il server SQL Azure e il database di Azure per PostgreSQL ([notebook](https://aka.ms/pl-data-trans)).

+ **Modifiche**
  + `PublishedPipeline.get_published_pipeline` deprecato a favore di `PublishedPipeline.get`.
  + `Schedule.get_schedule` deprecato a favore di `Schedule.get`.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning Data Prep SDK v1.0.12

+ **Nuove funzionalità**
  + Data Prep ora supporta la lettura da un database SQL di Azure con l'archivio dati.
 
+ **Modifiche**
  + Miglioramento delle prestazioni di memoria di determinate operazioni su dati di grandi dimensioni.
  + Per la specifica di `read_pandas_dataframe()` è ora necessario `temp_folder`.
  + La proprietà `name` in `ColumnProfile` è deprecata. Usare `column_name`.

## <a name="2019-01-28"></a>28 gennaio 2019

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning SDK per Python v1.0.10

+ **Modifiche**: 
  + Azure ML SDK non dispone più di pacchetti azure-cli come dipendenza. In particolare, le dipendenze di azure-cli-core e azure-cli-profilo sono state rimosse da azureml-core. Le modifiche con un impatto per l'utente sono:
    + Se si sta eseguendo "AZ login" e quindi si usa azureml-SDK, l'SDK eseguirà il log del codice del browser o del dispositivo in un altro momento. Non utilizza nessuno stato di credenziali creato da "az login".
    + Per l'autenticazione di Azure CLI, ad esempio usando "az login", usare la classe _azureml.core.authentication.AzureCliAuthentication_. Per l'autenticazione di Azure CLI, eseguire _i parametri di inizializzazione del programma per installare azure-cli_ nell'ambiente di Python in cui è stato installato azureml-sdk.
    + Se si esegue "az login" usando un'entità di servizio per l'automazione, è consigliabile usare la classe _azureml.core.authentication.ServicePrincipalAuthentication_, in quanto azure ml-sdk non userà credenziali di stato create dal comando Azure CLI. 

+ **Correzioni di bug**: questa versione contiene principalmente correzioni di bug secondarie

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning Data Prep SDK v1.0.8

+ **Correzioni di bug**
  + Miglioramento delle prestazioni del recupero dei profili dati.
  + Bug secondari correlati alla segnalazione degli errori risolti.
  
### <a name="azure-portal-new-features"></a>Portale di Azure: nuove funzionalità
+ Nuova funzionalità di trascinamento della traccia per i report. Gli utenti possono trascinare una colonna o un attributo dal pozzo all'area del grafico in cui il sistema selezionerà automaticamente il tipo di grafico appropriato per l'utente in base al tipo di informazioni. Gli utenti possono modificare il tipo di grafico in altri tipi applicabili o aggiungere altri attributi.

    Tipi di grafico supportati:
    - Grafico a linee
    - Istogramma
    - Grafico a barre in pila
    - Box plot
    - Grafico a dispersione
    - Grafico a bolle
+ Il portale a questo punto genera in modo dinamico i report per gli esperimenti. Quando un utente invia un'esecuzione a un esperimento, verrà generato automaticamente un report contenente i grafici e le metriche registrate per consentire il confronto tra diverse esecuzioni. 

## <a name="2019-01-14"></a>14-01-2019

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Azure Machine Learning SDK per Python v1.0.8

+ **Correzioni di bug**: questa versione contiene principalmente correzioni di bug secondarie

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Azure Machine Learning Data Prep SDK v1.0.7

+ **Nuove funzionalità**
  + Miglioramenti dell'archivio dati (documentati in [Manuale delle procedure dell'archivio dati](https://aka.ms/aml-data-prep-datastore-nb))
    + Maggiore capacità di lettura e scrittura di Azure File Share e archivi dati ADLS nel ridimensionamento verticale.
    + Quando si usano archivi dati, la preparazione dei dati ora supporta l'utilizzo dell'autenticazione principale del servizio invece dell'autenticazione interattiva.
    + Aggiunto supporto per gli URL wasb e wasbs.

## <a name="2019-01-09"></a>09-01-2019

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Azure Machine Learning Data Prep SDK v1.0.6

+ **Correzioni di bug**
  + Correzione di un bug relativo alla lettura da contenitori BLOB di Azure leggibili pubblicamente in Spark

## <a name="2018-12-20"></a>20-12-2018 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning SDK per Python v1.0.6
+ **Correzioni di bug**: questa versione contiene principalmente correzioni di bug secondarie

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning Data Prep SDK v1.0.4

+ **Nuove funzionalità**
  + La funzione `to_bool` consente ora la conversione di valori non corrispondenti in valori di errore. Questo è il nuovo comportamento predefinito per `to_bool` e `set_column_types` in caso di mancata corrispondenza. Il comportamento predefinito precedente consisteva nel convertire i valori non corrispondenti in valori False.
  + Quando si chiama `to_pandas_dataframe`, è disponibile una nuova opzione per interpretare i valori Null o mancanti nelle colonne numeriche come NaN.
  + È stata aggiunta la possibilità controllare il tipo restituito di alcune espressioni per garantire la coerenza dei tipi e la risposta anticipata agli errori.
  + È ora possibile chiamare `parse_json` per analizzare i valori in una colonna come oggetti JSON ed espanderli in più colonne.

+ **Correzioni di bug**
  + È stato corretto un bug che causava l'arresto anomalo di `set_column_types` in Python 3.5.2.
  + È stato corretto un bug che causava l'arresto anomalo del sistema durante la connessione all'archivio dati con un'immagine di Azure Machine Learning.

+ **Aggiornamenti**
  * [Notebook di esempio](https://aka.ms/aml-data-prep-notebooks) per esercitazioni introduttive, case study e guide pratiche.

## <a name="2018-12-04-general-availability"></a>2018-12-04: disponibilità generale

Azure Machine Learning è ora disponibile a livello generale.

### <a name="azure-machine-learning-compute"></a>Ambiente di calcolo di Azure Machine Learning
Con questa versione, Microsoft presenta una nuova esperienza di calcolo gestita tramite l'[ambiente di calcolo di Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute). Questa destinazione di calcolo sostituisce l'ambiente di calcolo di Azure Batch per intelligenza artificiale per Azure Machine Learning. 

La nuova destinazione di calcolo presenta queste caratteristiche:
+ Viene usato per il training del modello e l'inferenza/Punteggio batch
+ È un ambiente di calcolo a uno o più nodi
+ Esegue la gestione del cluster e la pianificazione dei processi per l'utente
+ Viene ridimensionata automaticamente per impostazione predefinita
+ Include il supporto per le risorse di CPU e GPU 
+ Consente l'uso di macchine virtuali con priorità bassa per un risparmio sui costi

L'ambiente di calcolo di Azure Machine Learning può essere creato in Python, tramite il portale di Azure o l'interfaccia della riga di comando. Deve essere creato nella zona dell'area di lavoro e non può essere collegato ad altre aree di lavoro. Questa destinazione di calcolo usa un contenitore Docker per l'esecuzione e crea pacchetti delle dipendenze per replicare lo stesso ambiente in tutti i nodi.

> [!Warning]
> È consigliabile creare una nuova area di lavoro per usare l'ambiente di calcolo di Azure Machine Learning. C'è una possibilità remota che gli utenti che tentano di creare l'ambiente di calcolo di Azure Machine Learning da un'area di lavoro esistente visualizzino un errore. L'ambiente di calcolo esistente nell'area di lavoro deve continuare a lavorare senza problemi.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Azure Machine Learning SDK per Python v1.0.2
+ **Modifiche di rilievo**
  + Con questa versione, si rimuoverà il supporto per la creazione di una macchina virtuale da Azure Machine Learning. È comunque possibile associare una macchina virtuale del cloud esistente o un server remoto in locale. 
  + Verrà rimosso anche il supporto per BatchAI, che ora dovrà essere supportato tramite l'ambiente di calcolo di Azure Machine Learning.

+ **Nuovo**
  + Per le pipeline di apprendimento automatico:
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **Aggiornato**
  + Per le pipeline di apprendimento automatico:
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) ora accetta runconfig
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) ora copia da e verso un'origine dati SQL
    + Pianificare la funzionalità in SDK per creare e aggiornare le pianificazioni per l'esecuzione di pipeline pubblicate

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Azure Machine Learning Data Prep SDK v0.5.2
+ **Modifiche di rilievo** 
  * `SummaryFunction.N` è stato rinominato `SummaryFunction.Count`.
  
+ **Correzioni di bug**
  * Usare il più recente token di esecuzione di Azure Machine Learning durante la lettura e la scrittura negli archivi dati in esecuzioni remote. Precedentemente, se il Token di esecuzione di Azure Machine Learning viene aggiornato in Python, il runtime di preparazione dei dati non viene aggiornato con il token di esecuzione di Azure Machine Learning aggiornato.
  * Altri messaggi di errore più chiari
  * Non si verifica più l'arresto anomalo di to_spark_dataframe() quando Spark usa la serializzazione `Kryo`
  * L'ispettore del conteggio del valore può ora mostrare più di 1000 valori univoci
  * La Divisione casuale non commette più errori se il flusso di dati originale non ha un nome  

+ **Altre informazioni**
  * [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Documentazione e notebook
+ Pipeline di Machine Learning
  + Notebook nuovi e aggiornati per iniziare con le pipeline, la definizione dell'ambito di batch e gli esempi di trasferimenti di stile: https://aka.ms/aml-pipeline-notebooks
  + Informazioni su come [creare la prima pipeline](how-to-create-your-first-pipeline.md)
  + Informazioni su come [eseguire previsioni in batch utilizzando le pipeline](how-to-run-batch-predictions.md)
+ Destinazione di calcolo di Azure Machine Learning
  + I [notebook di esempio](https://aka.ms/aml-notebooks) sono ora aggiornati per l'uso del nuovo ambiente di calcolo gestito.
  + [Informazioni su questo ambiente di calcolo](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Portale di Azure: nuove funzionalità
+ Creare e gestire i tipi di [Ambiente di calcolo di Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) nel portale.
+ Monitorare l'utilizzo della quota e [richiedere una quota](how-to-manage-quotas.md) per l'ambiente di calcolo di Azure Machine Learning.
+ Visualizzare lo stato del cluster dell'ambiente di calcolo di Azure Machine Learning in tempo reale.
+ È stato aggiunto il supporto di rete virtuale per la creazione dell'ambiente di calcolo di Azure Machine Learning e del servizio Azure Kubernetes.
+ Eseguire di nuovo le pipeline pubblicate con i parametri esistenti.
+ Nuovi [grafici automatizzati di apprendimento automatico](how-to-understand-automated-ml.md) per i modelli di classificazione (accuratezza, miglioramenti, calibrazione, grafico dell'importanza delle funzionalità con la spiegabilità del modello) e i modelli di regressione (valori residui e grafico di importanza della funzionalità con la spiegabilità del modello). 
+ Le pipeline possono essere visualizzate nel portale di Azure




## <a name="2018-11-20"></a>20-11-2018

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Azure Machine Learning SDK per Python v0.1.80

+ **Modifiche di rilievo** 
  * Lo spazio dei nomi *azureml.train.widgets* è stato spostato in *azureml.widgets*.
  * *azureml.core.compute.AmlCompute* imposta come deprecate le classi *azureml.core.compute.BatchAICompute* e *azureml.core.compute.DSVMCompute*. La seconda verrà rimossa in versioni successive. La classe AmlCompute ha ora una definizione più semplice, necessita solo dei parametri vm_size e max_nodes ed è in grado di ridimensionare automaticamente il cluster da 0 al valore di max_nodes quando viene inviato un processo. I [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) sono stati aggiornati con queste informazioni e dovrebbero offrire esempi di utilizzo. Ci auguriamo che gli utenti apprezzeranno questa semplificazione e le interessanti funzionalità che verranno aggiunte nelle prossime versioni.

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Azure Machine Learning Data Prep SDK v0.5.1 

Per altre informazioni su Data Prep SDK vedere la [documentazione di riferimento](https://aka.ms/data-prep-sdk).
+ **Nuove funzionalità**
   * È stata creata una nuova interfaccia della riga di comando DataPrep per eseguire i pacchetti DataPrep e visualizzare il profilo dati relativo a un set di dati o un flusso di dati
   * L'API SetColumnType è stata riprogettata per migliorarne l'usabilità
   * smart_read_file è stato rinominato in auto_read_file
   * Il profilo dati include ora i valori di asimmetria e curtosi
   * È supportato il campionamento stratificato
   * È possibile leggere da file ZIP che contengono file CSV
   * È possibile suddividere i set di dati riga per riga con la suddivisione casuale, ad esempio in set di test-training
   * È possibile ottenere tutti i tipi di dati delle colonne da un flusso di dati o un profilo dati chiamando `.dtypes`
   * È possibile ottenere il conteggio delle righe da un flusso di dati o un profilo dati chiamando `.row_count`

+ **Correzioni di bug**
   * È stata corretta la conversione da tipo di dati long a double 
   * È stata corretta un'asserzione dopo qualsiasi aggiunta di colonna 
   * È stato corretto un problema di Raggruppamento fuzzy che impediva il rilevamento dei gruppi in alcuni casi
   * È stata corretta la funzione di ordinamento per rispettare l'ordinamento a più colonne
   * Le espressioni and/or sono state corrette in modo che siano simili a come vengono gestite da `pandas`
   * È stata corretta la lettura dal percorso dbfs
   * I messaggi di errore sono stati resi più comprensibili 
   * Ora non si verificano più errori di lettura su una destinazione di calcolo remota tramite il token AML
   * Non si verificano più errori in Data Science Virtual Machine Linux
   * Non si verifica più un arresto anomalo quando predicati di stringa contengono valori non di tipo stringa
   * Gli errori di asserzione in caso di errore del flusso di dati vengono ora gestiti correttamente
   * Le posizioni di archiviazione montate in dbutils sono ora supportate in Azure Databricks

## <a name="2018-11-05"></a>05-11-2018

### <a name="azure-portal"></a>Portale di Azure 
Il portale di Azure per Azure Machine Learning presenta gli aggiornamenti seguenti:
  * Una nuova scheda **Pipeline** per le pipeline pubblicate.
  * Aggiunta del supporto per il collegamento di un cluster HDInsight esistente come destinazione di calcolo.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Azure Machine Learning SDK per Python v0.1.74

+ **Modifiche di rilievo** 
  * *Workspace.compute_targets, datastores, experiments, images, models e *webservices* sono diventate proprietà anziché metodi. Ad esempio, *Workspace.compute_targets()* dovrà essere sostituito con *Workspace.compute_targets*.
  * *Run.get_context* depreca *Run.get_submitted_run*. Il secondo metodo verrà rimosso nelle versioni successive.
  * La classe *PipelineData* prevede ora un oggetto datastore come parametro anziché datastore_name. Analogamente, *Pipeline* accetta default_datastore anziché default_datastore_name.

+ **Nuove funzionalità**
  * Il [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) di Azure Machine Learning Pipelines usa ora i passaggi MPI.
  * Il widget RunDetails per notebook Jupyter è stato aggiornato in modo da mostrare una visualizzazione della pipeline.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Azure Machine Learning Data Prep SDK v0.4.0 
 
+ **Nuove funzionalità**
  * Numero di tipi aggiunti al profilo dati 
  * Sono ora disponibili istogramma e conteggio dei valori
  * Altri percentili nel profilo dati
  * Il valore Median è disponibile in Summarize
  * Python 3.7 è ora supportato
  * Quando si salva un flusso di dati che contiene archivi dati in un pacchetto DataPrep, le informazioni degli archivi dati verranno rese persistenti come parte del pacchetto DataPrep
  * La scrittura in un archivio dati è ora supportata 
        
+ **Bug risolto**
  * Gli overflow di interi senza segno a 64 bit vengono ora gestiti correttamente in Linux
  * L'etichetta di testo errata per i file di testo normale è stata corretta in smart_read
  * Il tipo di colonna a stringa viene mostrata ora nella visualizzazione metrica
  * Il numero di tipi è stato corretto in modo da mostrare ValueKinds mappato a un unico FieldType invece che ai singoli
  * Write_to_csv non genera più errore quando il percorso viene specificato come stringa
  * Quando si usa Sostituisci, lasciare il campo "trova" vuoto non genera più un errore 

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Azure Machine Learning SDK per Python v0.1.68

+ **Nuove funzionalità**
  * Supporto di più tenant per la creazione di una nuova area di lavoro.

+ **Bug risolti**
  * Non è più necessario bloccare la versione della libreria pynacl durante la distribuzione di un servizio Web.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Azure Machine Learning Data Prep SDK v0.3.0

+ **Nuove funzionalità**
  * È stato aggiunto il metodo transform_partition_with_file(script_path), che consente agli utenti di passare il percorso di un file Python da eseguire

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Azure Machine Learning SDK per Python v0.1.65
La [versione 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) include nuove funzionalità, ulteriore documentazione, correzioni di bug e nuovi [blocchi appunti di esempio](https://aka.ms/aml-notebooks).

Per informazioni sui bug noti e le soluzioni alternative, vedere l'[elenco dei problemi noti](resource-known-issues.md).

+ **Modifiche di rilievo**
  * Workspace.experiments, Workspace.models, Workspace.compute_targets, Workspace.images e Workspace.web_services restituiscono un dizionario, mentre in precedenza restituivano un elenco. Vedere la documentazione dell'API [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py).

  * Le funzionalità automatizzate di Machine Learning hanno rimosso l'errore quadratico medio normalizzato dalle metriche principali.

+ **HyperDrive**
  * Diverse correzioni di bug di HyperDrive per Bayes, miglioramenti alle prestazioni delle chiamate di recupero delle metriche. 
  * Aggiornamento di Tensorflow da 1.9 a 1.10 
  * Ottimizzazione dell'immagine Docker per l'avvio a freddo. 
  * I processi ora segnalano lo stato corretto anche se terminano con un codice di errore diverso da 0. 
  * Convalida dell'attributo RunConfig in SDK. 
  * L'oggetto esecuzione HyperDrive supporta l'annullamento in modo analogo a una normale esecuzione, senza bisogno di passare alcun parametro. 
  * Miglioramenti ai widget per gestire lo stato dei valori degli elenchi a discesa per le esecuzioni distribuite e le esecuzioni di HyperDrive. 
  * Supporto di TensorBoard e di altri file di log risolto per il server dei parametri. 
  * Supporto di Intel(R) MPI sul lato servizi. 
  * Correzione di bug dell'ottimizzazione dei parametri per l'esecuzione distribuita durante la convalida in Batch per intelligenza artificiale. 
  * Gestione contesto ora identifica l'istanza primaria. 

+ **Esperienza del portale di Azure**
  * log_table() e log_row() sono supportati nei dettagli dell'esecuzione. 
  * Creazione automatica di grafici per tabelle e righe con 1, 2 o 3 colonne numeriche e una colonna di categoria facoltativa.

+ **Funzionalità automatizzate di Machine Learning**
  * Miglioramenti alla gestione degli errori e alla relativa documentazione 
  * Correzione di problemi di prestazioni relativi al recupero delle proprietà di esecuzione. 
  * Correzione di un problema di esecuzione continua. 
  * Correzione dei problemi di iterazione :::no-loc text="ensembling":::.
  * Correzione di un bug di blocco del training in MAC OS.
  * Sottocampionamento della curva di precisione-recupero/ROC media della macro in uno scenario di convalida personalizzata.
  * Rimozione della logica di indicizzazione eccedente.
  * Rimozione del filtro dall'API get_output.

+ **Pipeline**
  * È stato aggiunto un metodo Pipeline.publish() per pubblicare una pipeline direttamente, senza un'esecuzione preventiva.   
  * È stato aggiunto un metodo PipelineRun.get_pipeline_runs() per recuperare le esecuzioni di pipeline generate da una pipeline pubblicata.

+ **Project Brainwave**
  * Supporto aggiornato per i nuovi modelli di intelligenza artificiale disponibili in FPGA.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Azure Machine Learning Data Prep SDK v0.2.0
La [versione 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) include le funzionalità e le correzioni di bug seguenti:

+ **Nuove funzionalità**
  * Supporto della codifica one-hot
  * Supporto della trasformazione dei quantili
   
+ **Bug risolto:**
  * Funziona con qualsiasi versione di Tornado, senza bisogno di effettuare il downgrade della versione di Tornado in uso
  * Vengono conteggiati tutti i valori, non solo i primi tre

## <a name="2018-09-public-preview-refresh"></a>2018-09 (aggiornamento dell'anteprima pubblica)

Una nuova versione aggiornata di Azure Machine Learning: ulteriori informazioni su questa versione: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>Passaggi successivi

Leggere la panoramica di [Azure Machine Learning](../service/overview-what-is-azure-ml.md).
