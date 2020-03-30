---
title: Novità della versione
titleSuffix: Azure Machine Learning
description: Informazioni sugli aggiornamenti più recenti di Azure Machine Learning e sull'apprendimento automatico e sui dati prep Python SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 03/10/2020
ms.openlocfilehash: 70e8bf95022f88dab54fa13769df4b051cf41c92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247147"
---
# <a name="azure-machine-learning-release-notes"></a>Note sulla versione di Azure Machine LearningAzure Machine Learning release notes

In questo articolo sono informazioni sulle versioni di Azure Machine Learning.In this article, learn about Azure Machine Learning releases.  Per il contenuto di riferimento completo dell'SDK, visita la pagina di riferimento principale dell'SDK per Python di Azure Machine [**Learning.**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)

Per informazioni sui bug noti e le soluzioni alternative, vedere l'[elenco dei problemi noti](resource-known-issues.md).

## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>Azure Machine Learning SDK per Python v1.2.0

+ **Modifiche che causano un'interruzione**
  + Supporto per python 2.7

+ **Correzioni di bug e miglioramenti**
  + **azure-cli-ml**
    + Aggiunge "--subscription-id" `az ml model/computetarget/service` ai comandi nell'interfaccia della riga di comando
    + Aggiunta del supporto per il passaggio di vault_url, key_name e key_version di chiavi gestite dal cliente per la distribuzione di ACI
  + **azureml-automl-core** 
    + Abilitata l'imputazione personalizzata con valore costante per le attività di previsione dei dati X e y.
    + Risolto il problema durante la visualizzazione dei messaggi di errore all'utente.    
  + **azureml-automl-runtime**
    + Risolto il problema con le previsioni sui set di dati, contenenti grani con una sola riga
    + Diminuita la quantità di memoria richiesta dalle attività di previsione.
    + Aggiunti messaggi di errore migliori se la colonna dell'ora ha un formato non corretto.
    + Abilitata l'imputazione personalizzata con valore costante per le attività di previsione dei dati X e y.
  + **azureml-core**
    + Aggiungere il supporto per il caricamento di ServicePrincipal dalle variabili di ambiente: AZUREML_SERVICE_PRINCIPAL_ID, AZUREML_SERVICE_PRINCIPAL_TENANT_ID e AZUREML_SERVICE_PRINCIPAL_PASSWORD
    + Introdotto un `support_multi_line` `Dataset.Tabular.from_delimited_files`nuovo parametro`support_multi_line=False`in : Per impostazione predefinita ( ), tutte le interruzioni di riga, incluse quelle nei valori dei campi tra virgolette, verranno interpretate come un'interruzione di record. La lettura dei dati in questo modo è più veloce e ottimizzata per l'esecuzione parallela su più core CPU. Tuttavia, può comportare la produzione invisibile all'utente di più record con valori di campo non allineati. Deve essere impostato `True` su quando i file delimitati contengono interruzioni di riga tra virgolette.
    + Aggiunta la possibilità di registrare ADLS Gen2 nell'interfaccia della riga di comando di Azure Machine LearningAdded the ability to register ADLS Gen2 in the Azure Machine Learning CLI
    + Il parametro 'fine_grain_timestamp' è stato rinominato in 'timestamp' e il parametro 'coarse_grain_timestamp' in 'partition_timestamp' per il metodo with_timestamp_columns() in TabularDataset per riflettere meglio l'utilizzo dei parametri.
    + Aumento della lunghezza massima del nome dell'esperimento a 255.
  + **azureml-interpret**
    + Aggiornato azureml-interpret per interpretare-comunità 0.7.
  + **azureml-sdk**
    + Passaggio alle dipendenze con la versione compatibile Tilde per il supporto delle patch nelle versioni non definitive e stabili.


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>Azure Machine Learning SDK per Python v1.1.5

+ **Funzionalità deprecata**
  + **Python 2.7**
    + Ultima versione per supportare python 2.7

+ **Modifiche che causano un'interruzione**
  + **Semantic Versioning 2.0.0**
    + A partire dalla versione 1.1 Azure ML Python SDK adotta semantic Versioning 2.0.0. [Altre informazioni](https://semver.org/). Tutte le versioni successive seguiranno il nuovo schema di numerazione e il contratto di controllo delle versioni semantico. 

+ **Correzioni di bug e miglioramenti**
  + **azure-cli-ml**
    + Modificare il nome del comando cli dell'endpoint da 'az ml endpoint aks' a 'az ml endpoint realtime' per coerenza.
    + istruzioni di installazione dell'interfaccia della riga di comando di aggiornamento per la CLI stabile e sperimentale
    + La profilatura a istanza singola è stata fissata per produrre un suggerimento ed è stata resa disponibile nell'sdk principale.
  + **azureml-automl-core**
    + Attivazione dell'inferenza della modalità Batch (che prevede più righe una sola volta) per i modelli ONNX automl
    + Migliorato il rilevamento della frequenza sui set di dati, senza dati o contenente punti dati irregolari
    + Aggiunta la possibilità di rimuovere i punti dati non conformi alla frequenza dominante.
    + Modificato l'input del costruttore in modo da accettare un elenco di opzioni per applicare le opzioni di imputazione per le colonne corrispondenti.
    + La registrazione degli errori è stata migliorata.
  + **azureml-automl-runtime**
    + Risolto il problema con l'errore generato se il grano che non era presente nel set di training veniva visualizzato nel set di test
    + Rimosso il requisito di y_query durante il punteggio sul servizio di previsione
    + Risolto il problema con la previsione quando il set di dati contiene granulari brevi con intervalli di tempo lunghi.
    + Risolto il problema quando l'orizzonte massimo automatico è attivato e la colonna data contiene date sotto forma di stringhe. Sono stati aggiunti messaggi di conversione e di errore corretti per quando la conversione fino ad oggi non è possibile
    + Utilizzo di NumPy e SciPy nativi per serializzare e deserializzare i dati intermedi per FileCacheStore (utilizzato per le esecuzioni AutoML locali)Using native NumPy and SciPy for serializing and deserializing intermediate data for FileCacheStore (used for local AutoML runs)
    + Corretto un bug in cui le esecuzioni figlio non riuscite potevano rimanere bloccate nello stato In esecuzione.
    + Aumento della velocità di featurizzazione.
    + Corretto il controllo di frequenza durante il punteggio, ora le attività di previsione non richiedono una rigorosa equivalenza di frequenza tra treno e set di test.
    + Modificato l'input del costruttore in modo da accettare un elenco di opzioni per applicare le opzioni di imputazione per le colonne corrispondenti.
    + Corretti gli errori relativi alla selezione del tipo di ritardo.
    + Correzione dell'errore non classificato generato sui set di dati, con grani con la singola riga
    + Risolto il problema con la lentezza del rilevamento della frequenza.
    + Consente di correggere un bug nella gestione delle eccezioni AutoML che ha causato la sostituzione del motivo reale dell'errore di training da parte di un AttributeError.Fixes a bug in AutoML exception handling that caused the real reason for training failure to be replaced by an AttributeError.
  + **azureml-cli-common**
    + La profilatura a istanza singola è stata fissata per produrre un suggerimento ed è stata resa disponibile nell'sdk principale.
  + **azureml-contrib-mir**
    + Aggiunge funzionalità nella classe MirWebservice per recuperare il token di accesso
    + Usa l'autenticazione token per MirWebservice per impostazione predefinita durante la chiamata MirWebservice.run() - Aggiorna solo se la chiamata non riesce
    + La distribuzione del servizio Web Mir ora richiede Skus [Standard_DS2_v2, Standard_F16 Standard_A2_v2] invece di [Ds2v2, A2v2 e F16] rispettivamente.
  + **azureml-contrib-pipeline-steps**
    + Parametro facoltativo side_inputs aggiunto a ParallelRunStep.Optional parameter side_inputs added to ParallelRunStep. Questo parametro può essere utilizzato per montare la cartella nel contenitore. I tipi attualmente supportati sono DataReference e PipelineData.Currently supported types are DataReference and PipelineData.
    + I parametri passati in ParallelRunConfig possono essere sovrascritti passando ora i parametri della pipeline. I nuovi parametri della pipeline supportati aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count e aml_process_count_per_node fanno già parte della versione precedente).
  + **azureml-core**
    + I servizi Web di AzureML distribuiti verranno ora gestiti per impostazione predefinita per `INFO` la registrazione. Questo può essere controllato `AZUREML_LOG_LEVEL` impostando la variabile di ambiente nel servizio distribuito.
    + Python sdk usa il servizio di individuazione per usare l'endpoint 'api' anziché 'pipelines'.
    + Passare alle nuove route in tutte le chiamate SDK
    + Modifica il routing delle chiamate a ModelManagementService in una nuova struttura unificataChanges routing of calls to the ModelManagementService to a new unified structure
      + Reso disponibile pubblicamente il metodo di aggiornamento dell'area di lavoro.
      + Aggiunto image_build_compute parametro nel metodo di aggiornamento dell'area di lavoro per consentire all'utente di aggiornare il calcolo per la compilazione dell'immagine
    +  Aggiunti messaggi di deprecazione al flusso di lavoro di profilatura precedente. Correzione dei limiti di cpu e memoria di profilaturaFixed profiling cpu and memory limits
    + Aggiunta di RSection come parte dell'ambiente per eseguire i processi RAdded RSection as part of Environment to run R jobs
    +  Aggiunta la `Dataset.mount` convalida a per generare un errore quando l'origine del set di dati non è accessibile o non contiene dati.
    + Aggiunto `--grant-workspace-msi-access` come parametro aggiuntivo per l'interfaccia della riga di comando dell'archivio dati per la registrazione del contenitore BLOB di Azure che consentirà di registrare il contenitore BLOB che si trova dietro una rete virtualeAdded as an additional parameter for the Datastore CLI for registering Azure Blob Container which will allow you to register Blob Container that is behind a VNet
    + La profilatura a istanza singola è stata fissata per produrre un suggerimento ed è stata resa disponibile nell'sdk principale.
    + Risolto il problema in aks.py _deploy
    + Convalida l'integrità dei modelli caricati per evitare errori di archiviazione invisibile all'utente.
    + L'utente può ora specificare un valore per la chiave di autenticazione durante la rigenerazione delle chiavi per i servizi Web.
    + Correzione di un bug in cui le lettere maiuscole non possono essere utilizzate come nome di input del set di dati
  + **impostazioni predefinite di azuremlazureml-defaults**
    + `azureml-dataprep`verrà ora installato come `azureml-defaults`parte di . Non è più necessario installare manualmente dataprep[fuse] sulle destinazioni di calcolo per montare i set di dati.
  + **azureml-interpret**
    + Aggiornato azureml-interpret per interpretare-comunità 0.6.
    + Aggiornamento dell'interprete di azureml per dipendere dall'interpretazione della comunità 0.5.0Updated azureml-interpret to depend on interpret-community 0.5.0
    + Aggiunte eccezioni di tipo azureml all'interprete azureml
    + Correzione della serializzazione DeepScoringExplainer per i modelli di kerasFixed DeepScoringExplainer serialization for keras models
  + **azureml-mlflow**
    + Aggiungere il supporto per le nuvole sovrane a azureml.mlflowAdd support for sovereign clouds to azureml.mlflow
  + **azureml-pipeline-core**
    + Pipeline batch scoring notebook now uses ParallelRunStep
    + Correzione di un bug in cui i risultati di PythonScriptStep potevano essere riutilizzati in modo non corretto nonostante la modifica dell'elenco di argomenti
    + Aggiunta la possibilità di impostare il tipo di colonne quando si chiamano i metodi parse_`PipelineOutputFileDataset`
  + **azureml-pipeline-steps**
    + Spostato `AutoMLStep` il `azureml-pipeline-steps` al pacchetto. Deprecato `AutoMLStep` il `azureml-train-automl-runtime`all'interno di .
    + Aggiunta di un esempio di documentazione per il set di dati come input PythonScriptStepAdded documentation example for dataset as PythonScriptStep input
  + **azureml-tensorboard**
    + azureml-tensorboard aggiornato per supportare il tensorflow 2.0
    + Mostra il numero di porta corretto quando si utilizza una porta Tensorboard personalizzata in un'istanza di calcoloShow correct port number when using a custom Tensorboard port on a Compute Instance
  + **azureml-train-automl-client**
    + Risolto un problema a seguito del quale determinati pacchetti possono essere installati in versioni non corrette nelle esecuzioni remote.
    + risolto il problema di override di FeaturizationConfig che filtra la configurazione di fattibilizzazione personalizzata.
  + **azureml-train-automl-runtime**
    + Risolto il problema con il rilevamento della frequenza nelle esecuzioni remote
    + Spostato `AutoMLStep` nel `azureml-pipeline-steps` pacchetto. Deprecato `AutoMLStep` il `azureml-train-automl-runtime`all'interno di .
  + **azureml-train-core**
    + Supporto di PyTorch versione 1.4 nello stimatore PyTorchSupporting PyTorch version 1.4 in the PyTorch Estimator
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>Azure Machine Learning SDK per Python v1.1.2rc0 (versione non definitiva)Azure Machine Learning SDK for Python v1.1.2rc0 (Pre-release)

+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-core**
    + Attivazione dell'inferenza della modalità Batch (che prevede più righe una sola volta) per i modelli ONNX automl
    + Migliorato il rilevamento della frequenza sui set di dati, senza dati o contenente punti dati irregolari
    + Aggiunta la possibilità di rimuovere i punti dati non conformi alla frequrncy dominante.
  + **azureml-automl-runtime**
    + Risolto il problema con l'errore generato se il grano che non era presente nel set di training veniva visualizzato nel set di test
    + Rimosso il requisito di y_query durante il punteggio sul servizio di previsione
  + **azureml-contrib-mir**
    + Aggiunge funzionalità nella classe MirWebservice per recuperare il token di accesso
  + **azureml-core**
    + I servizi Web di AzureML distribuiti verranno ora gestiti per impostazione predefinita per `INFO` la registrazione. Questo può essere controllato `AZUREML_LOG_LEVEL` impostando la variabile di ambiente nel servizio distribuito.
    + Correggere l'iterazione per `Dataset.get_all` restituire tutti i set di dati registrati nell'area di lavoro.
    + Migliorare il messaggio di errore `path` quando viene passato un tipo non valido all'argomento delle API di creazione del set di dati.
    + Python sdk usa il servizio di individuazione per usare l'endpoint 'api' anziché 'pipelines'.
    + Passare alle nuove route in tutte le chiamate SDK
    + Modifica il routing delle chiamate a ModelManagementService in una nuova struttura unificataChanges routing of calls to the ModelManagementService to a new unified structure
      + Reso disponibile pubblicamente il metodo di aggiornamento dell'area di lavoro.
      + Aggiunto image_build_compute parametro nel metodo di aggiornamento dell'area di lavoro per consentire all'utente di aggiornare il calcolo per la compilazione dell'immagine
    +  Aggiunti messaggi di deprecazione al flusso di lavoro di profilatura precedente. Correzione dei limiti di cpu e memoria di profilaturaFixed profiling cpu and memory limits
  + **azureml-interpret**
    + aggiornare azureml-interpret per interpretare-comunità 0.6.
  + **azureml-mlflow**
    + Aggiungere il supporto per le nuvole sovrane a azureml.mlflowAdd support for sovereign clouds to azureml.mlflow
  + **azureml-pipeline-steps**
    + Spostato `AutoMLStep` il `azureml-pipeline-steps package`file all'oggetto . Deprecato `AutoMLStep` il `azureml-train-automl-runtime`all'interno di .
  + **azureml-train-automl-client**
    + Risolto un problema a seguito del quale determinati pacchetti possono essere installati in versioni non corrette nelle esecuzioni remote.
  + **azureml-train-automl-runtime**
    + Risolto il problema con il rilevamento della frequenza nelle esecuzioni remote
    + Spostato `AutoMLStep` il `azureml-pipeline-steps package`file all'oggetto . Deprecato `AutoMLStep` il `azureml-train-automl-runtime`all'interno di .
  + **azureml-train-core**
    + Spostato `AutoMLStep` il `azureml-pipeline-steps package`file all'oggetto . Deprecato `AutoMLStep` il `azureml-train-automl-runtime`all'interno di .

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>Azure Machine Learning SDK per Python v1.1.1rc0 (versione non definitiva)Azure Machine Learning SDK for Python v1.1.1rc0 (Pre-release)

+ **Correzioni di bug e miglioramenti**
  + **azure-cli-ml**
    + La profilatura a istanza singola è stata fissata per produrre un suggerimento ed è stata resa disponibile nell'sdk principale.
  + **azureml-automl-core**
    + La registrazione degli errori è stata migliorata.
  + **azureml-automl-runtime**
    + Risolto il problema con la previsione quando il set di dati contiene granulari brevi con intervalli di tempo lunghi.
    + Risolto il problema quando l'orizzonte massimo automatico è attivato e la colonna data contiene date sotto forma di stringhe. Abbiamo aggiunto la corretta conversione e un errore ragionevole se la conversione fino ad oggi non è possibile
    + Utilizzo di NumPy e SciPy nativi per serializzare e deserializzare i dati intermedi per FileCacheStore (utilizzato per le esecuzioni AutoML locali)Using native NumPy and SciPy for serializing and deserializing intermediate data for FileCacheStore (used for local AutoML runs)
    + Corretto un bug in cui le esecuzioni figlio non riuscite potevano rimanere bloccate nello stato In esecuzione.
  + **azureml-cli-common**
    + La profilatura a istanza singola è stata fissata per produrre un suggerimento ed è stata resa disponibile nell'sdk principale.
  + **azureml-core**
    + Aggiunto `--grant-workspace-msi-access` come parametro aggiuntivo per l'interfaccia della riga di comando dell'archivio dati per la registrazione del contenitore BLOB di Azure che consentirà di registrare il contenitore BLOB che si trova dietro una rete virtualeAdded as an additional parameter for the Datastore CLI for registering Azure Blob Container which will allow you to register Blob Container that is behind a VNet
    + La profilatura a istanza singola è stata fissata per produrre un suggerimento ed è stata resa disponibile nell'sdk principale.
    + Risolto il problema in aks.py _deploy
    + Convalida l'integrità dei modelli caricati per evitare errori di archiviazione invisibile all'utente.
  + **azureml-interpret**
    + aggiunto eccezioni di tipo azureml per azureml-interpretata
    + serializzazione Fixed DeepScoringExplainer per i modelli keras
  + **azureml-pipeline-core**
    + Pipeline batch scoring notebook now uses ParallelRunStep
  + **azureml-pipeline-steps**
    + Spostato `AutoMLStep` nel `azureml-pipeline-steps` pacchetto. Deprecato `AutoMLStep` il `azureml-train-automl-runtime`all'interno di .
  + **azureml-contrib-pipeline-steps**
    + Parametro facoltativo side_inputs aggiunto a ParallelRunStep.Optional parameter side_inputs added to ParallelRunStep. Questo parametro può essere utilizzato per montare la cartella nel contenitore. I tipi attualmente supportati sono DataReference e PipelineData.Currently supported types are DataReference and PipelineData.
  + **azureml-tensorboard**
    + azureml-tensorboard aggiornato per supportare il tensorflow 2.0
  + **azureml-train-automl-client**
    + risolto il problema di override di FeaturizationConfig che filtra la configurazione di fattibilizzazione personalizzata.
  + **azureml-train-automl-runtime**
    + Spostato `AutoMLStep` nel `azureml-pipeline-steps` pacchetto. Deprecato `AutoMLStep` il `azureml-train-automl-runtime`all'interno di .
  + **azureml-train-core**
    + Supporto di PyTorch versione 1.4 nello stimatore PyTorchSupporting PyTorch version 1.4 in the PyTorch Estimator
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>Azure Machine Learning SDK per Python v1.1.0rc0 (versione non definitiva)Azure Machine Learning SDK for Python v1.1.0rc0 (Pre-release)

+ **Modifiche che causano un'interruzione**
  + **Semantic Versioning 2.0.0**
    + A partire dalla versione 1.1 Azure ML Python SDK adotta semantic Versioning 2.0.0. [Altre informazioni](https://semver.org/). Tutte le versioni successive seguiranno il nuovo schema di numerazione e il contratto di controllo delle versioni semantico. 
  
+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-runtime**
    + Aumento della velocità di featurizzazione.
    + Corretto il controllo di frequenza durante il punteggio, ora nelle attività di previsione non è necessaria una rigorosa equivalenza di frequenza tra treno e set di test.
  + **azureml-core**
    + L'utente può ora specificare un valore per la chiave di autenticazione durante la rigenerazione delle chiavi per i servizi Web.
  + **azureml-interpret**
    + Aggiornamento dell'interprete di azureml per dipendere dall'interpretazione della comunità 0.5.0Updated azureml-interpret to depend on interpret-community 0.5.0
  + **azureml-pipeline-core**
    + Correzione di un bug in cui i risultati di PythonScriptStep potevano essere riutilizzati in modo non corretto nonostante la modifica dell'elenco di argomenti
  + **azureml-pipeline-steps**
    + Aggiunta di un esempio di documentazione per il set di dati come input PythonScriptStepAdded documentation example for dataset as PythonScriptStep input
  + **azureml-contrib-pipeline-steps**
    + I parametri passati in ParallelRunConfig possono essere sovrascritti passando ora i parametri della pipeline. I nuovi parametri della pipeline supportati aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count e aml_process_count_per_node fanno già parte della versione precedente).
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Azure Machine Learning SDK per Python v1.0.85

+ **Nuove funzionalità**
  + **azureml-core**
    + Ottenere l'utilizzo principale corrente e la limitazione della quota per le risorse AmlCompute in una determinata area di lavoro e sottoscrizioneGet the current core usage and quota limitation for AmlCompute resources in a given workspace and subscription
  
  + **azureml-contrib-pipeline-steps**
    + Consentire all'utente di passare il set di dati tabulare come risultato intermedio dal passaggio precedente a parallelrunstepEnable user to pass tabular dataset as intermediate result from previous step to parallelrunstep

+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-runtime**
    + È stato rimosso il requisito di y_query colonna nella richiesta al servizio di previsione distribuito. 
    + La "y_query" è stata rimossa dalla sezione di richiesta del servizio notebook Orange Juice di Dominick.
    + Corretto il bug che impediva la previsione sui modelli distribuiti, operando su set di dati con colonne data-ora.
    + È stato aggiunto Matthews Correlation Coefficient come metrica di classificazione, sia per la classificazione binaria che per la classificazione multiclasse.
  + **azureml-contrib-interpret**
    + Gli esplicatori di testo rimossi dall'interprete azureml-contrib come spiegazione testuale sono stati spostati nel repository interpret-text che verrà rilasciato a breve.
  + **azureml-core**
    + Set di dati: gli utilizzi per il set di dati dei file non dipendono più dal numero e dai panda da installare nel python env.
    + Modificato LocalWebservice.wait_for_deployment() per controllare lo stato del contenitore Docker locale prima di tentare di eseguire il ping dell'endpoint di integrità, riducendo notevolmente il tempo necessario per segnalare una distribuzione non riuscita.
    + Correzione dell'inizializzazione di una proprietà interna utilizzata in LocalWebservice.reload() quando l'oggetto servizio viene creato da una distribuzione esistente utilizzando la funzione di costruzione LocalWebservice().
    + Messaggio di errore modificato per chiarimenti.
    + Aggiunto un nuovo metodo denominato get_access_token() ad AksWebservice che restituirà l'oggetto AksServiceAccessToken, che contiene il token di accesso, l'aggiornamento dopo il timestamp, la scadenza nel timestamp e il tipo di token. 
    + Deprecato metodo get_token() esistente in AksWebservice poiché il nuovo metodo restituisce tutte le informazioni restituite da questo metodo.
    + Output modificato del comando get-access-token del servizio az ml. Token rinominato per accessToken e refreshBy per refreshAfter. Aggiunte le proprietà expiryOn e tokenType.
    + get_active_runs fisso
  + **azureml-explain-model**
    + aggiornato shap a 0.33.0 e interpret-community a 0.4.
  + **azureml-interpret**
    + aggiornato shap a 0.33.0 e interpret-community a 0.4.
  + **azureml-train-automl-runtime**
    + È stato aggiunto Matthews Correlation Coefficient come metrica di classificazione, sia per la classificazione binaria che per la classificazione multiclasse.
    + Deprecare il flag di pre-elaborazione dal codice e sostituito con la fattibilità -featurization è attivata per impostazione predefinita

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Azure Machine Learning SDK per Python v1.0.83

+ **Nuove funzionalità**
  + Set di dati: `out_of_range_datetime` `to_pandas_dataframe` aggiungere due opzioni `on_error` e affinché esito negativo quando i dati contengono valori di errore anziché riempirli con `None`.
  + Workspace: aggiunto `hbi_workspace` il flag per le aree di lavoro con dati sensibili che abilita un'ulteriore crittografia e disabilita la diagnostica avanzata nelle aree di lavoro. È stato inoltre aggiunto il supporto per l'aggiunta di chiavi `cmk_keyvault` `resource_cmk_uri` personalizzate per l'istanza Cosmos DB associata, specificando i parametri e durante la creazione di un workspace, che crea un'istanza Cosmos DB nella sottoscrizione durante il provisioning dell'area di lavoro. [Per saperne di più clicca qui.](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-cosmos-db)

+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-runtime**
    + Correzione di una regressione che causava la generazione di un TypeError durante l'esecuzione di AutoML in versioni Python inferiori alla 3.5.4.Fixed a regression that caused a TypeError to be raised when running AutoML on Python versions below 3.5.4.
  + **azureml-core**
    + Corretto bug `datastore.upload_files` nel punto in cui `./` il percorso relativo che non ha iniziato con non era possibile utilizzare.
    + Aggiunti messaggi di deprecazione per tutti i percorsi di codice delle classi Image
    + Correzione della costruzione dell'URL di gestione dei modelli per la regione Mooncake.
    + Risolto il problema per cui i modelli che usano source_dir non potevano essere inclusi nel pacchetto per Funzioni di Azure.Fixed issue where models using source_dir couldn't be packaged for Azure Functions.    
    + Aggiunta di un'opzione a Environment.build_local() per eseguire il push di un'immagine nel registro del contenitore dell'area di lavoro di AzureMLAdded an option to [Environment.build_local()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) to push an image into AzureML workspace container registry
    + L'SDK è stato aggiornato per usare la nuova libreria di token in azure synapse in modo compatibile con la schiena.
  + **azureml-interpret**
    + Corretto un bug in cui None veniva restituito quando non erano disponibili spiegazioni per il download. Ora genera un'eccezione, che abbina il comportamento altrove.
  + **azureml-pipeline-steps**
    + Non è `DatasetConsumptionConfig`stato `Estimator`consentito `inputs` il `Estimator` passaggio di `EstimatorStep`s al parametro 's quando verrà utilizzato in un oggetto .
  + **azureml-sdk**
    + Aggiunto il client AutoML al pacchetto azureml-sdk, consentendo l'invio di esecuzioni AutoML remote senza installare il pacchetto AutoML completo.
  + **azureml-train-automl-client**
    + Corretto l'allineamento sull'output della console per le esecuzioni automl
    + Risolto un bug in cui era possibile installare una versione non corretta dei panda in caso di assillo remoto.

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Azure Machine Learning SDK per Python v1.0.81

+ **Correzioni di bug e miglioramenti**
  + **azureml-contrib-interpret**
    + rinviare la dipendenza dello shap a interpretare-comunità da azureml-interpretata
  + **azureml-core**
    + La destinazione di calcolo può ora essere specificata come parametro per gli oggetti di configurazione della distribuzione corrispondente. Si tratta in particolare del nome della destinazione di calcolo in cui eseguire la distribuzione, non dell'oggetto SDK.
    + Aggiunte informazioni CreatedBy agli oggetti Model e Service.Added CreatedBy information to Model and Service objects. È possibile <var>accedere tramite .created_by
    + Fixed ContainerImage.run(), che non stava configurando correttamente la porta HTTP del contenitore Docker.
    + Rendi `azureml-dataprep` facoltativo il `az ml dataset register` comando cli
    + Risolto un `TabularDataset.to_pandas_dataframe` bug in cui sarebbe stato erroneamente necessario eseguire il rollback a un lettore alternativo e stampare un avviso.
  + **azureml-explain-model**
    + rinviare la dipendenza dello shap a interpretare-comunità da azureml-interpretata
  + **azureml-pipeline-core**
    + Aggiunto un `NotebookRunnerStep`nuovo passaggio della pipeline per eseguire un blocco appunti locale come passaggio nella pipeline.
    + Rimosse le funzioni di get_all deprecate per PublishedPipelines, Schedules e PipelineEndpoints
  + **azureml-train-automl-client**
    + Avviata la deprecazione di data_script come input per AutoML.


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Azure Machine Learning SDK per Python v1.0.79

+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-core**
    + Rimosso featurizationConfig da registrare
      + Registrazione aggiornata per registrare solo "auto"/"off"/"customized".
  + **azureml-automl-runtime**
    + Aggiunto il supporto per i panda. Serie e panda. Categoricaper il rilevamento del tipo di dati della colonna. Precedentemente supportato solo numpy.ndarray
      + Aggiunte modifiche al codice correlate per gestire correttamente dtype categorico.
    + L'interfaccia della funzione di previsione è stata migliorata: il parametro y_pred è stato reso facoltativo. - Le docstrings sono state migliorate.
  + **azureml-contrib-dataset**
    + È stato corretto un bug in cui non era possibile montare i set di dati con etichetta.
  + **azureml-core**
    + Correzione di `Environment.from_existing_conda_environment(name, conda_environment_name)`bug per . L'utente può creare un'istanza dell'ambiente che è la replica esatta dell'ambiente localeUser can create an instance of Environment that is exact replica of the local environment
    + Modificati i metodi Dataset correlati `include_boundary=True` alle serie temporali per impostazione predefinita.
  + **azureml-train-automl-client**
    + Risolto il problema per cui i risultati della convalida non vengono stampati quando mostra l'output è impostato su false.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Azure Machine Learning SDK per Python v1.0.76

+ **Modifiche che causano un'interruzione**
  + Problemi di aggiornamento di Azureml-Train-AutoMLAzureml-Train-AutoML upgrade issues
    + L'aggiornamento a azureml-train-automl>1.0.76 da azureml-train-automl<1.0.76 può causare installazioni parziali, causando l'esito negativo di alcune importazioni automatiche. Per risolvere questo problema, è possibile https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmdeseguire lo script di installazione disponibile in . Oppure, se si utilizza pip direttamente è possibile:
      + "pip install --upgrade azureml-train-automl"
      + "pip install--ignore-installed azureml-train-automl-client"
    + oppure è possibile disinstallare la versione precedente prima dell'aggiornamento
      + "pip disinstallare azureml-train-automl"
      + "pip installare azureml-train-automl"

+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-runtime**
    + AutoML prenderà ora in considerazione sia le classi true che false durante il calcolo delle metriche scalari medie per le attività di classificazione binaria.
    + Spostato il codice di apprendimento automatico e formazione in AzureML-AutoML-Core in un nuovo pacchetto AzureML-AutoML-Runtime.
  + **azureml-contrib-dataset**
    + Quando `to_pandas_dataframe` si chiama su un set di dati con etichetta con l'opzione di download, è ora possibile specificare se sovrascrivere o meno i file esistenti.
    + Quando `keep_columns` si `drop_columns` chiama o che comporta l'eliminazione di una colonna timeseries, label o image, le funzionalità corrispondenti verranno eliminate anche per il set di dati.
    + Risolto un problema con il caricatore a pirosa per l'attività di rilevamento degli oggetti.
  + **azureml-contrib-interpret**
    + Rimosso widget dashboard di spiegazione da azureml-contrib-interpret, pacchetto modificato per fare riferimento a quello nuovo in interpret_community
    + Versione aggiornata di interpret-community a 0.2.0
  + **azureml-core**
    + Migliorare le `workspace.datasets`prestazioni di .
    + Aggiunta la possibilità di registrare l'archivio dati del database SQL di Azure usando l'autenticazione di nome utente e passwordAdded the ability to register Azure SQL Database Datastore using username and password authentication
    + Correzione del caricamento di RunConfigurations da percorsi relativi.
    + Quando `keep_columns` si `drop_columns` chiama o che comporta l'eliminazione di una colonna di serie temporali, le funzionalità corrispondenti verranno eliminate anche per il set di dati.
  + **azureml-interpret**
    + versione aggiornata di interpret-community a 0.2.0
  + **azureml-pipeline-steps**
    + Valori supportati documentati per i passaggi della pipeline di Azure Machine Learning.Documented supported values for `runconfig_pipeline_params` for azure machine learning pipeline steps.
  + **azureml-pipeline-core**
    + Aggiunta l'opzione CLI per scaricare l'output in formato json per i comandi Pipeline.Added CLI option to download output in json format for Pipeline commands.
  + **azureml-train-automl**
    + Dividere AzureML-Train-AutoML in 2 pacchetti, un pacchetto client AzureML-Train-AutoML-Client e un pacchetto di formazione ML AzureML-Train-AutoML-Runtime
  + **azureml-train-automl-client**
    + È stato aggiunto un thin client per l'invio di esperimenti AutoML senza dover installare le dipendenze di Machine Learning in locale.
    + Correzione della registrazione dei ritardi rilevati automaticamente, delle dimensioni delle finestre mobili e degli orizzonti massimi nelle condutture remote.
  + **azureml-train-automl-runtime**
    + Aggiunto un nuovo pacchetto AutoML per isolare i componenti di apprendimento automatico e runtime dal client.
  + **azureml-contrib-treno-rl**
    + Aggiunto il supporto per l'apprendimento di rinforzo in SDK.
    + Aggiunto il supporto Di AmlWindowsCompute in RL SDK.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Azure Machine Learning SDK per Python v1.0.74

  + **Funzionalità di anteprima**
    + **azureml-contrib-dataset**
      + Dopo aver importato azureml-contrib-dataset, è possibile chiamare `Dataset.Labeled.from_json_lines` anziché `._Labeled` creare un set di dati con etichetta.
      + Quando `to_pandas_dataframe` si chiama su un set di dati con etichetta con l'opzione di download, è ora possibile specificare se sovrascrivere o meno i file esistenti.
      + Quando `keep_columns` si `drop_columns` chiama o che comporta l'eliminazione di una serie temporale, di un'etichetta o di un'immagine, le funzionalità corrispondenti verranno eliminate anche per il set di dati.
      + Risolti i problemi relativi `dataset.to_torchvision()`al caricatore PyTorch durante la chiamata a .

+ **Correzioni di bug e miglioramenti**
  + **azure-cli-ml**
    + Aggiunta la profilatura del modello all'interfaccia della riga di comando dell'anteprima.
    + Risolve le modifiche di rilievo in Archiviazione di Azure causando l'esito negativo dell'interfaccia della riga di comando di AzureML.Fixes breaking change in Azure Storage causing AzureML CLI to fail.
    + Aggiunto il tipo di bilanciamento del carico a MLC per i tipi AKSAdded Load Balancer Type to MLC for AKS types
  + **azureml-automl-core**
    + Risolto il problema con il rilevamento dell'orizzonte massimo sulla serie temporale, con valori mancanti e grani multipli.
    + Corretto il problema con gli errori durante la generazione di divisioni di convalida incrociata.
    + Sostituire questa sezione con un messaggio in formato markdown da visualizzare nelle note sulla versione: -Migliorata la gestione dei grani brevi nei set di dati di previsione.
    + Risolto il problema con il mascheramento di alcune informazioni utente durante la registrazione. -Migliorata la registrazione degli errori durante le esecuzioni di previsione.
    + Aggiunta di psutil come dipendenza conda al file di distribuzione yml generato automaticamente.
  + **azureml-contrib-mir**
    + Risolve le modifiche di rilievo in Archiviazione di Azure causando l'esito negativo dell'interfaccia della riga di comando di AzureML.Fixes breaking change in Azure Storage causing AzureML CLI to fail.
  + **azureml-core**
    + Correggere un bug che ha causato la produzione di 500 modelli distribuiti in Funzioni di Azure.Fixes a bug which caused models deployed on Azure Functions to produce 500s.
    + Risolto un problema a seguito del quale il file amlignore non era applicato alle istantanee.
    + È stato aggiunto un nuovo api amlcompute.get_active_runs che restituisce un generatore per l'esecuzione e l'esecuzione in coda in un ambiente specificato.
    + Aggiunto il tipo di bilanciamento del carico a MLC per i tipi AKS.
    + È stato aggiunto append_prefix parametro bool per download_files in run.py e download_artifacts_from_prefix in artifacts_client. Questo flag viene utilizzato per appiattire in modo selettivo il percorso del file di origine in modo che solo il nome del file o della cartella venga aggiunto all'output_directory
    + Risolvere il problema `run_config.yml` di deserializzazione per l'utilizzo del set di dati.
    + Quando `keep_columns` si `drop_columns` chiama o che comporta l'eliminazione di una colonna di serie temporali, le funzionalità corrispondenti verranno eliminate anche per il set di dati.
  + **azureml-interpret**
    + Aggiornata la versione interprete-community a 0.1.0.3
  + **azureml-train-automl**
    + Risolto un problema a deposizione automl_step potrebbe non stampare i problemi di convalida.
    + Sono stati risolti register_model per avere esito positivo anche se nell'ambiente del modello mancano le dipendenze in locale.
    + Risolto un problema a seguito del quale alcune esecuzioni remote non erano abilitate per la docker.
    + Aggiungere la registrazione dell'eccezione che causa l'esito negativo di un'esecuzione locale.
  + **azureml-train-core**
    + Considerare resume_from viene eseguita nel calcolo delle esecuzioni figlio migliore per l'ottimizzazione automatica degli iperparametri.
  + **azureml-pipeline-core**
    + Correzione della gestione dei parametri nella costruzione dell'argomento della pipeline.
    + Aggiunto descrizione della pipeline e parametro yaml del tipo di passaggio.
    + Nuovo formato yaml per il passaggio Pipeline e avviso di deprecazione aggiunto per il formato precedente.



## <a name="2019-11-04"></a>4 novembre 2019

### <a name="web-experience"></a>Esperienza Web

La pagina di destinazione [https://ml.azure.com](https://ml.azure.com) dell'area di lavoro collaborativa in cui è stata migliorata e rinominata come Azure Machine Learning Studio (anteprima).

Dallo studio è possibile eseguire il training, testare, distribuire e gestire gli asset di Azure Machine Learning, ad esempio set di dati, pipeline, modelli, endpoint e altro ancora.

Accedi ai seguenti strumenti di authoring basati sul Web dallo studio:

| Strumento basato sul Web | Descrizione | Edizione |
|-|-|-|
| Notebook VM(preview) | Workstation basata su cloud completamente gestita | Basic & Enterprise |
| [Apprendimento automatico automatizzato (anteprima)Automated machine learning](tutorial-first-experiment-automated-ml.md) (preview) | Nessuna esperienza di codice per automatizzare lo sviluppo di modelli di apprendimento automaticoNo code experience for automating machine learning model development | Enterprise |
| [Designer](concept-designer.md) (anteprima)Designer (preview) | Strumento di modellazione di apprendimento automatico trascinato in base al trascinamento della selezione precedentemente noto come finestra di progettazione | Enterprise |


### <a name="azure-machine-learning-designer-enhancements"></a>Miglioramenti della finestra di progettazione di Azure Machine LearningAzure Machine Learning designer enhancements

+ Precedentemente noto come interfaccia visiva 
+    11 nuovi [moduli,](algorithm-module-reference/module-reference.md) tra cui consiglieri, classificatori e utilità di formazione, tra cui la progettazione delle funzionalità, la convalida incrociata e la trasformazione dei dati.

### <a name="r-sdk"></a>R SDK 
 
Gli scienziati dei dati e gli sviluppatori di informazioni sull'iaformazione a i dati usano Azure Machine Learning SDK for R per creare ed eseguire flussi di lavoro di Machine Learning con Azure Machine Learning.Data scientists and AI developers use the [Azure Machine Learning SDK for R](tutorial-1st-r-experiment.md) to build and run machine learning workflows with Azure Machine Learning.

Azure Machine Learning SDK per `reticulate` R usa il pacchetto da associare a Python SDK. Mediante l'associazione diretta a Python, l'SDK per R consente di accedere agli oggetti e ai metodi di base implementati in Python SDK da qualsiasi ambiente R.

Le funzionalità principali dell'SDK includono:

+    Gestione delle risorse cloud per il monitoraggio, la registrazione e l'organizzazione degli esperimenti di machine learning.
+    Eseguire il training dei modelli usando le risorse cloud, incluso il training dei modelli con accelerazione GPU.
+    Distribuire i modelli come webservices in istanze del contenitore di Azure (ACI) e nel servizio Azure Kubernetes (AKS).

Per la documentazione completa, vedere il sito Web del [pacchetto.](https://azure.github.io/azureml-sdk-for-r)

### <a name="azure-machine-learning-integration-with-event-grid"></a>Azure Machine Learning integration with Event Grid 

Azure Machine Learning is now a resource provider for Event Grid, you can configure machine learning events through the Azure portal or Azure CLI. Gli utenti possono creare eventi per il completamento dell'esecuzione, la registrazione del modello, la distribuzione del modello e la deriva dei dati rilevati. Questi eventi possono essere indirizzati ai gestori eventi supportati da Griglia di eventi per l'utilizzo. Per altre informazioni, vedere [lo schema](https://docs.microsoft.com/azure/event-grid/event-schema-machine-learning)degli eventi di apprendimento automatico, [i concetti](https://docs.microsoft.com/azure/machine-learning/concept-event-grid-integration) e gli articoli [dell'esercitazione.](https://docs.microsoft.com/azure/machine-learning/how-to-use-event-grid)

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Azure Machine Learning SDK per Python v1.0.72

+ **Nuove funzionalità**
  + Sono stati aggiunti monitoraggi dei set di dati tramite il pacchetto [**azureml-datadrift,**](https://docs.microsoft.com/python/api/azureml-datadrift) consentendo il monitoraggio dei set di dati delle serie temporali per la deriva dei dati o altre modifiche statistiche nel tempo. Gli avvisi e gli eventi possono essere attivati se viene rilevata una deriva o se vengono soddisfatte altre condizioni sui dati. Consulta [la nostra documentazione](https://aka.ms/datadrift) per i dettagli.
  + Annuncio di due nuove edizioni (definite anche SKU intercambiabili) in Azure Machine Learning.Announcing two new editions (also referred to as a SKU intercambiaably) in Azure Machine Learning. Con questa versione è ora possibile creare un'area di lavoro di base o Enterprise Azure Machine Learning.With this release you can now create either a Basic or Enterprise Azure Machine Learning workspace. Tutte le aree di lavoro esistenti verranno predefinite per l'edizione Basic ed è possibile passare al portale di Azure o allo studio per aggiornare l'area di lavoro in qualsiasi momento. È possibile creare un'area di lavoro di base o aziendale dal portale di Azure.You can create either a Basic or Enterprise workspace from the Azure portal. Si prega di leggere [la nostra documentazione](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) per saperne di più. Dall'SDK, l'edizione dell'area di lavoro può essere determinata utilizzando la proprietà "sku" dell'oggetto dell'area di lavoro.
  + Sono stati apportati anche miglioramenti a Azure Machine Learning Compute: è ora possibile visualizzare le metriche per i cluster (ad esempio nodi totali, nodi in esecuzione, quota core totale) in Monitoraggio di Azure, oltre a visualizzare i log di diagnostica per il debug. Inoltre, è anche possibile visualizzare le esecuzioni attualmente in esecuzione o in coda nel cluster e dettagli quali gli indirizzi IP dei vari nodi nel cluster. È possibile visualizzarli nel portale o utilizzando le funzioni corrispondenti nell'SDK o nell'interfaccia della riga di comando.

  + **Funzionalità di anteprima**
    + Stiamo rilasciando il supporto di anteprima per la crittografia del disco dell'sSD locale in Azure Machine Learning Compute. Si prega di sollevare un ticket di supporto tecnico per ottenere il vostro abbonamento nella whitelist per utilizzare questa funzione.
    + Anteprima pubblica dell'inferenza batch di Azure Machine Learning.Public Preview of Azure Machine Learning Batch Inference. L'inferenza batch di Azure Machine Learning è destinata a processi di inferenza di grandi dimensioni che non sono sensibili al tempo. L'inferenza batch offre un calcolo di calcolo conveniente, con velocità effettiva senza precedenti per le applicazioni asincrone. È ottimizzato per l'inferenza ad alta velocità effettiva e fire-and-forget su raccolte di dati di grandi dimensioni.
    + [**azureml-contrib-dataset**](https://docs.microsoft.com/python/api/azureml-contrib-dataset)
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
    + CLI supporta ora l'imballaggio del modello.
    + Aggiunta dell'interfaccia della riga di comando del set di dati. Per ulteriori informazioni:`az ml dataset --help`
    + Aggiunto il supporto per la distribuzione e la creazione di pacchetti di modelli supportati (ONNX, scikit-learn e TensorFlow) senza un'istanza di InferenceConfig.Added support for deploying and packaging supported models (ONNX, scikit-learn, and TensorFlow) without an InferenceConfig instance.
    + Aggiunto il flag di sovrascrittura per la distribuzione del servizio (ACI e AKS) in SDK e CLI. Se specificato, sovrascriverà il servizio esistente se il servizio con nome esiste già. Se il servizio non esiste, verrà creato un nuovo servizio.
    + I modelli possono essere registrati con due nuovi framework, Onnx e Tensorflow. - La registrazione del modello accetta dati di input di esempio, dati di output di esempio e configurazione delle risorse per il modello.- Model registration accepts sample input data, sample output data and resource configuration for the model.
  + **azureml-automl-core**
    + Il training di un'iterazione viene eseguito in un processo figlio solo quando vengono impostati vincoli di runtime.
    + Aggiunto un guardrail per le attività di previsione, per verificare se una max_horizon specificata causerà un problema di memoria sul computer specificato o meno. In caso affermativo, verrà visualizzato un messaggio del guardrail.
    + Aggiunto il supporto per frequenze complesse come 2 anni e 1 mese. -Aggiunto messaggio di errore comprensibile se la frequenza non può essere determinata.
    + Aggiungere le impostazioni predefinite di azureml a conda env generato automaticamente per risolvere l'errore di distribuzione del modelloAdd azureml-defaults to auto generated conda env to solve the model deployment failure
    + Consentire la conversione dei dati intermedi in Azure `AutoMLStep`Machine Learning Pipeline in un set di dati tabulare e usare in .
    + Aggiornamento scopo colonna implementata per lo streaming.
    + Implementazione dell'aggiornamento dei parametri del trasformatore per Imputer e HashOneHotEncoder per lo streaming.
    + Sono state aggiunte le dimensioni dei dati correnti e la dimensione minima richiesta per i dati ai messaggi di errore di convalida.
    + Aggiornato la dimensione minima richiesta dei dati per la convalida incrociata per garantire un minimo di due campioni in ogni piega di convalida.
  + **azureml-cli-common**
    + CLI supporta ora l'imballaggio del modello.
    + I modelli possono essere registrati con due nuovi framework, Onnx e Tensorflow.
    + La registrazione del modello accetta dati di input di esempio, dati di output di esempio e configurazione delle risorse per il modello.
  + **azureml-contrib-gbdt**
    + fissazione del canale di rilascio per il notebook
    + Aggiunto un avviso per la destinazione di calcolo non AmlCompute che non è supportata
    + Aggiunto Estimatore LightGMB al pacchetto azureml-contrib-gbdt
  + [**azureml-core**](https://docs.microsoft.com/python/api/azureml-core)
    + CLI supporta ora l'imballaggio del modello.
    + Aggiungere un avviso di deprecazione per le API del set di dati deprecate. Vedere Avviso di https://aka.ms/tabular-datasetmodifica dell'API del set di dati all'indirizzo .
    + Modificare [`Dataset.get_by_id`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) per restituire il nome e la versione di registrazione se il set di dati è registrato.
    + Correggere un bug che ScriptRunConfig con set di dati come argomento non può essere utilizzato ripetutamente per inviare l'esecuzione dell'esperimento.
    + I set di dati recuperati durante un'esecuzione verranno rilevati [`run.get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--) e potranno essere visualizzati nella pagina dei dettagli dell'esecuzione o chiamando dopo il completamento dell'esecuzione.
    + Consentire la conversione dei dati intermedi in Azure [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep)Machine Learning Pipeline in un set di dati tabulare e usare in .
    + Aggiunto il supporto per la distribuzione e la creazione di pacchetti di modelli supportati (ONNX, scikit-learn e TensorFlow) senza un'istanza di InferenceConfig.Added support for deploying and packaging supported models (ONNX, scikit-learn, and TensorFlow) without an InferenceConfig instance.
    + Aggiunto il flag di sovrascrittura per la distribuzione del servizio (ACI e AKS) in SDK e CLI. Se specificato, sovrascriverà il servizio esistente se il servizio con nome esiste già. Se il servizio non esiste, verrà creato un nuovo servizio.
    +  I modelli possono essere registrati con due nuovi framework, Onnx e Tensorflow. La registrazione del modello accetta dati di input di esempio, dati di output di esempio e configurazione delle risorse per il modello.
    + Aggiunto nuovo archivio dati per il database di Azure per MySQL.Added new datastore for Azure Database for MySQL. Aggiunto esempio per l'uso di Database di Azure per MySQL in DataTransferStep nelle pipeline di Azure Machine Learning.Added example for using Azure Database for MySQL in DataTransferStep in Azure Machine Learning Pipelines.
    + Aggiunta funzionalità per aggiungere e rimuovere tag dagli esperimenti Aggiunta funzionalità per rimuovere i tag dalle esecuzioni
    + Aggiunto il flag di sovrascrittura per la distribuzione del servizio (ACI e AKS) in SDK e CLI. Se specificato, sovrascriverà il servizio esistente se il servizio con nome esiste già. Se il servizio non esiste, verrà creato un nuovo servizio.
  + [**azureml-datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift)
    + Spostato `azureml-contrib-datadrift` da in`azureml-datadrift`
    + Aggiunto il supporto per il monitoraggio di set di dati di serie temporali per deriva e altre misure statistiche
    + Nuovi `create_from_model()` metodi `create_from_dataset()` e [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) alla classe. Il `create()` metodo sarà deprecato.
    + Regolazioni alle visualizzazioni in Python e dell'interfaccia utente in Azure Machine Learning Studio.
    + Supporta la pianificazione dei monitor settimanali e mensili, oltre al quotidiano per i monitoraggi dei set di dati.
    + Supporta il riempimento delle metriche di monitoraggio dei dati per analizzare i dati cronologici per i monitoraggi dei set di dati.
    + Varie correzioni di bug
  + [**azureml-pipeline-core**](https://docs.microsoft.com/python/api/azureml-pipeline-core)
    + azureml-dataprep non è più necessario per inviare una `yaml` pipeline di Azure Machine Learning eseguita dal file della pipeline.
  + [**azureml-train-automl**](/python/api/azureml-train-automl-runtime/)
    + Aggiungere le impostazioni predefinite di azureml a conda env generato automaticamente per risolvere l'errore di distribuzione del modelloAdd azureml-defaults to auto generated conda env to solve the model deployment failure
    + Il training remoto AutoML ora include le impostazioni predefinite di azureml per consentire il riutilizzo di training env per l'inferenza.
  + **azureml-train-core**
    + Aggiunto il supporto PyTorch [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch) 1.3 in Estimator

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>Interfaccia visiva (anteprima)Visual interface (preview)

+ L'interfaccia visiva di Azure Machine Learning (anteprima) è stata revisionata per l'esecuzione nelle pipeline di [Azure Machine Learning.](concept-ml-pipelines.md) Le pipeline (precedentemente note come esperimenti) create nell'interfaccia visiva sono ora completamente integrate con l'esperienza di Azure Machine Learning di base.
  + Esperienza di gestione unificata con le risorse SDK
  + Controllo delle versioni e rilevamento per modelli di interfacce visive, pipeline ed endpointVersioning and tracking for visual interface models, pipelines, and endpoints
  + Interfaccia utente riprogettata
  + Aggiunta distribuzione dell'inferenza batch
  + Aggiunto il supporto del servizio Azure Kubernetes (AKS) per le destinazioni di calcolo dell'inferenzaAdded Azure Kubernetes Service (AKS) support for inference compute targets
  + Nuovo flusso di lavoro per la creazione di pipeline Python-step
  + Nuova [pagina di destinazione](https://ml.azure.com) per gli strumenti di creazione visiva

+ **Nuovi moduli**
  + Applicare l'operazione matematica
  + Applica trasformazione SQL
  + Valori delle clip
  + Riepilogare i dati
  + Importare da database SQLImport from SQL database

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Azure Machine Learning SDK per Python v1.0.69

+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-core**
    + Limitare le spiegazioni del modello alla migliore esecuzione piuttosto che le spiegazioni di calcolo per ogni esecuzione. Modifica di questo comportamento per locale, remoto e ADB.
    + Aggiunto il supporto per le spiegazioni del modello su richiesta per l'interfaccia utenteAdded support for on-demand model explanations for UI
    + È stato aggiunto psutil `automl` come dipendenza di psutil come dipendenza conda in amlcompute.
    + Risolto il problema con ritardi euristici e dimensioni delle finestre mobili sui set di dati di previsione alcune serie dei quali possono causare errori di algebra lineare
      + Aggiunta la stampa per i parametri determinati euristicamente nelle tirature di previsione.
  + **azureml-contrib-datadrift**
    + Aggiunta la protezione durante la creazione di metriche di output se il livello di set di dati deriva non è nella prima sezione.
  + **azureml-contrib-interpret**
    + Azureml-contrib-explain-model pacchetto è stato rinominato in azureml-contrib-interpret
  + **azureml-core**
    + Aggiunta API per annullare la registrazione dei set di dati. `dataset.unregister_all_versions()`
    + Azureml-contrib-explain-model pacchetto è stato rinominato in azureml-contrib-interpret.
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core)**
    + Aggiunta API per annullare la registrazione dei set di dati. Dataset. [unregister_all_versions()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + Aggiunta l'API del set di dati per controllare l'ora di modifica dei dati. `dataset.data_changed_time`.
    + Essere in `FileDataset` grado `TabularDataset` di utilizzare `PythonScriptStep` `EstimatorStep`e `HyperDriveStep` come input per , e in Azure Machine Learning Pipeline
    + Prestazioni `FileDataset.mount` di è stato migliorato per le cartelle con un gran numero di file
    + Essere in grado di utilizzare [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset) e [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset) come input per [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep), [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)e [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) nella Pipeline di Apprendimento automatico di Azure.
    + Prestazioni di FileDataset. [mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-) è stato migliorato per le cartelle con un gran numero di file
    + Aggiunto l'URL ai suggerimenti per gli errori noti nei dettagli dell'esecuzione.
    + Corretto un bug in run.get_metrics in cui le richieste potrebbero non riuscire se un'esecuzione ha troppi figliFixed a bug in run.get_metrics where requests would fail if a run had to o many children
    + Corretto un bug in run.get_metrics in cui le richieste potrebbero avere esito negativo se un'esecuzione ha troppi figliFixed a bug in [run.get_metrics](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) where requests would fail if a run had to o many children
    + Aggiunto il supporto per l'autenticazione nel cluster Arcadia.
    + La creazione di un oggetto Experiment ottiene o crea l'esperimento nell'area di lavoro di Azure Machine Learning per il rilevamento della cronologia di esecuzione. L'ID esperimento e l'ora archiviata vengono popolati nell'oggetto Esperimento al momento della creazione. Esempio: experiment -Experiment(workspace, "New Experiment") experiment_id : experiment.id archive() e reactivate() sono funzioni che possono essere chiamate in un esperimento per nascondere e ripristinare l'esperimento dalla visualizzazione nell'esperienza utente o restituite per impostazione predefinita in una chiamata per elencare gli esperimenti. Se viene creato un nuovo esperimento con lo stesso nome di un esperimento archiviato, è possibile rinominare l'esperimento archiviato durante la riattivazione passando un nuovo nome. Può essere presente un solo esperimento attivo con un determinato nome. Esempio: experiment1 - Experiment(workspace, "Active Experiment") experiment1.archive() - Crea un nuovo esperimento attivo con lo stesso nome dell'archivio. esperimento2. Esperimento (spazio di lavoro, "Esperimento attivo") experiment1.reactivate(new_name :"Precedente Esperimento attivo") L'elenco di metodi statici() in Esperimento può accettare un filtro nome e un filtro ViewType. I valori ViewType sono "ACTIVE_ONLY", "ARCHIVED_ONLY" e "ALL" Esempio: archived_experiments : Experiment.list(workspace, view_type"ARCHIVED_ONLY") all_first_experiments : Experiment.list(workspace, name, "First Experiment", view_type "ALL")
    + Supporto per l'utilizzo dell'ambiente per la distribuzione del modello e l'aggiornamento del servizioSupport using environment for model deploy, and service update
  + **azureml-datadrift**
    + L'attributo show della classe DataDriftDector non supporterà più l'argomento facoltativo 'with_details'. L'attributo show presenterà solo il coefficiente di deriva dei dati e il contributo della deriva dei dati delle colonne di entità geografiche.
    + Il comportamento dell'attributo 'get_output' dell'attributo DataDriftDetector viene modificato:DataDriftDetector attribute 'get_output' behavior changes:
      + I start_time dei parametri di input end_time sono facoltativi anziché obbligatori;
      + Immettere start_time e/o end_time specifici con un run_id specifico nella stessa chiamata genererà un'eccezione di errore di valore perché si escludono a vicenda
      + Per base all'input specifico start_time e/o end_time, verranno restituiti solo i risultati delle esecuzioni pianificate;
      + Il parametro 'daily_latest_only' è deprecato.
    + Supporto per il recupero di output Data Drift basati su set di dati.
  + **azureml-explain-model**
    + Rinomina il pacchetto AzureML-explain-model in AzureML-interprete, mantenendo il pacchetto precedente per la compatibilità con le versioni precedenti per il momento
    + corretto `automl` bug con spiegazioni non elaborate impostate per l'attività di classificazione invece di regressione per impostazione predefinita al download da ExplanationClient
    + Aggiungere il `ScoringExplainer` supporto per la creazione diretta tramite`MimicWrapper`
  + **azureml-pipeline-core**
    + Prestazioni migliorate per la creazione di pipeline di grandi dimensioniImproved performance for large Pipeline creation
  + **azureml-train-core**
    + Aggiunto il supporto TensorFlow 2.0 in TensorFlow Estimator
  + **azureml-train-automl**
    + La creazione di un oggetto [Experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment) ottiene o crea l'esperimento nell'area di lavoro di Azure Machine Learning per il rilevamento della cronologia di esecuzione. L'ID esperimento e l'ora archiviata vengono popolati nell'oggetto Esperimento al momento della creazione. Esempio:

        ```py
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [archive()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#archive--) e [reactivate()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) sono funzioni che possono essere chiamate su un esperimento per nascondere e ripristinare l'esperimento dalla visualizzazione nell'esperienza utente o restituite per impostazione predefinita in una chiamata agli esperimenti di elenco. Se viene creato un nuovo esperimento con lo stesso nome di un esperimento archiviato, è possibile rinominare l'esperimento archiviato durante la riattivazione passando un nuovo nome. Può essere presente un solo esperimento attivo con un determinato nome. Esempio:

        ```py
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        Il metodo statico [list()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) in Experiment può accettare un filtro nome e un filtro ViewType. I valori ViewType sono "ACTIVE_ONLY", "ARCHIVED_ONLY" e "ALL". Esempio:

        ```py
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + Supporto per l'utilizzo dell'ambiente per la distribuzione del modello e l'aggiornamento del servizio.
  + **[azureml-datadrift](https://docs.microsoft.com/python/api/azureml-datadrift)**
    + L'attributo show della classe [DataDriftDetector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) non supporterà più l'argomento facoltativo 'with_details'. L'attributo show presenterà solo il coefficiente di deriva dei dati e il contributo della deriva dei dati delle colonne di entità geografiche.
    + Il comportamento della funzionehttps://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-) DataDriftDetector [get_output] cambia il comportamento:DataDriftDetector function [get_output] behavior changes:
      + I start_time dei parametri di input end_time sono facoltativi anziché obbligatori;
      + Immettere start_time e/o end_time specifici con un run_id specifico nella stessa chiamata genererà un'eccezione di errore di valore perché si escludono a vicenda;
      + Per base all'input specifico start_time e/o end_time, verranno restituiti solo i risultati delle esecuzioni pianificate;
      + Il parametro 'daily_latest_only' è deprecato.
    + Supporto per il recupero di output Data Drift basati su set di dati.
  + **[azureml-explain-model](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + Rinomina il pacchetto AzureML-explain-model in AzureML-interprete, mantenendo il pacchetto precedente per la compatibilità con le versioni precedenti per il momento.
    + corretto il bug AutoML con spiegazioni non elaborate impostate per l'attività di classificazione anziché la regressione per impostazione predefinita al download da ExplanationClient.
    + Aggiungere il supporto per ScoringExplainer da creare direttamente utilizzando [MimicWrapperAdd](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic_wrapper.mimicwrapper) support for [ScoringExplainer](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer?view=azure-ml-py) to be created directly using MimicWrapper
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Prestazioni migliorate per la creazione di pipeline di grandi dimensioni.
  + **[azureml-train-core](https://docs.microsoft.com/python/api/azureml-train-core)**
    + Aggiunto il supporto TensorFlow 2.0 in [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow) Estimator.
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + L'esecuzione padre non avrà più esito negativo quando l'iterazione dell'installazione non è riuscita, poiché l'orchestrazione si occupa già di esso.
    + Aggiunto il supporto locale-docker e local-conda per gli esperimenti AutoML
    + Aggiunto il supporto locale-docker e local-conda per gli esperimenti AutoML.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nuova esperienza Web (anteprima) per le aree di lavoro di Azure Machine Learning

La scheda Esperimento nel [nuovo portale dell'area di lavoro](https://ml.azure.com) è stata aggiornata in modo che i data scientist possano monitorare gli esperimenti in modo più performante. È possibile esplorare le seguenti funzionalità:
+ Metadati dell'esperimento per filtrare e ordinare facilmente l'elenco degli esperimenti
+ Pagine dei dettagli dell'esperimento semplificate e performanti che consentono di visualizzare e confrontare le corse
+ Nuovo design per l'esecuzione delle pagine dei dettagli per comprendere e monitorare le esecuzioni di formazione

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Azure Machine Learning SDK per Python v1.0.65

  + **Nuove funzionalità**
    + Aggiunti ambienti curati. Questi ambienti sono stati preconfigurati con librerie per le attività comuni di apprendimento automatico e sono stati pre-compila e memorizzati nella cache come immagini Docker per un'esecuzione più rapida. Vengono visualizzati per impostazione predefinita nell'elenco di ambiente dell'area di lavoro, con prefisso "AzureML".
    + Aggiunti ambienti curati. Questi ambienti sono stati preconfigurati con librerie per le attività comuni di apprendimento automatico e sono stati pre-compila e memorizzati nella cache come immagini Docker per un'esecuzione più rapida. Vengono visualizzati per impostazione predefinita nell'elenco di ambiente dell'area di [lavoro,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29)con prefisso "AzureML".

  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Aggiunto il supporto di conversione ONNX per ADB e HDI

+ **Funzionalità di anteprima**
  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + BERT e BiLSTM supportati come eturizzatore di testo (solo anteprima)
    + Personalizzazione della featurizzazione supportata per i parametri relativi allo scopo colonna e al trasformatore (solo anteprima)
    + Spiegazioni non elaborate supportate quando l'utente abilita la spiegazione del modello durante il training (solo anteprima)Supported raw explanations when user enables model explanation during training (preview only)
    + Aggiunto Profeta per la previsione come pipeline addestrabile (solo anteprima)Added Prophet for `timeseries` forecasting as a trainable pipeline (preview only)

  + **azureml-contrib-datadrift**
    + Pacchetti riposizionati da azureml-contrib-datadrift a azureml-datadrift; il `contrib` pacchetto verrà rimosso in una versione futura

+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-core**
    + Introdotto FeaturizationConfig a AutoMLConfig e AutoMLBaseSettings
    + Introdotto FeaturizationConfig a [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) e AutoMLBaseSettings
      + Sostituisci colonna Scopo della Featurizzazione con la colonna e il tipo di feature in base a i dati
      + Ignora parametri del trasformatore
    + Aggiunto messaggio di deprecazione per explain_model() e retrieve_model_explanations()
    + Aggiunto Profeta come pipeline addestrabile (solo anteprima)Added Prophet as a trainable pipeline (preview only)
    + Aggiunto il messaggio di deprecazione per explain_model() e retrieve_model_explanations().
    + Aggiunto Profeta come pipeline addestrabile (solo anteprima).
    + Aggiunto il supporto per il rilevamento automatico dei ritardi di destinazione, le dimensioni della finestra di rotolamento e l'orizzonte massimo. Se uno di target_lags, target_rolling_window_size o max_horizon è impostato su 'auto', l'euristica verrà applicata per stimare il valore del parametro corrispondente in base ai dati di training.
    + Previsione fissa nel caso in cui il set di dati contenga una colonna di grano, questo grano è di tipo numerico ed è presente uno spazio tra treno e set di test
    + Correzione del messaggio di errore relativo all'indice duplicato nell'esecuzione remota nelle attività di previsione
    + Correzione delle previsioni nel caso in cui il set di dati contenga una colonna di grano, questo grano è di tipo numerico ed è presente uno spazio tra il training e il set di test.
    + Corretto il messaggio di errore relativo all'indice duplicato nell'esecuzione remota nelle attività di previsione.
    + Aggiunto un guardrail per verificare se un set di dati è sbilanciato o meno. In caso affermativo, un messaggio del guardrail verrebbe scritto nella console.
  + **azureml-core**
    + Aggiunta la possibilità di recuperare l'URL di accesso condiviso per modellare nell'archiviazione tramite l'oggetto modello. Ad esempio: model.get_sas_url()
    + Introdurre `run.get_details()['datasets']` per ottenere i set di dati associati all'esecuzione inviata
    + Aggiungere `Dataset.Tabular.from_json_lines_files` API per creare un tabularData dai file di linee JSON. Per informazioni su questi dati tabulari nei file https://aka.ms/azureml-data di linee JSON in TabularDataset, vedere la documentazione.
    + Aggiunti ulteriori campi delle dimensioni della macchina virtuale (disco del sistema operativo, numero di GPU) alla funzione supported_vmsizes ()
    + Aggiunti campi aggiuntivi alla funzione list_nodes () per mostrare l'esecuzione, l'IP privato e quello pubblico, la porta ecc.
    + Possibilità di specificare un nuovo campo durante il provisioning del cluster: remotelogin_port_public_access che può essere impostato su abilitato o disabilitato a seconda che si desideri lasciare la porta SSH aperta o chiusa al momento della creazione del cluster. Se non viene specificato, il servizio aprirà o chiuderà in modo intelligente la porta a seconda che si stia distribuendo il cluster all'interno di una rete virtuale.
  + **azureml-explain-model**
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core/azureml.core)**
    + Aggiunta la possibilità di recuperare l'URL di accesso condiviso per modellare nell'archiviazione tramite l'oggetto modello. Ex: modello. [get_sas_url()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + Introdurre run. [get_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--)['dataset'] per ottenere i set di dati associati all'esecuzione inviata
    + Aggiungi `Dataset.Tabular`API . [from_json_lines_files()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) per creare un tabularDataset da file di linee JSON. Per informazioni su questi dati tabulari nei file https://aka.ms/azureml-data di linee JSON in TabularDataset, vedere la documentazione.
    + Aggiunti ulteriori campi di dimensione VM (disco del sistema operativo, numero di GPU) alla funzione [supported_vmsizes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
    + Aggiunti campi aggiuntivi alla funzione [list_nodes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) per mostrare l'esecuzione, l'IP privato e quello pubblico, la porta ecc.
    + Possibilità di specificare un nuovo campo durante il [provisioning](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--) `--remotelogin_port_public_access` del cluster che può essere impostato su abilitato o disabilitato a seconda che si desideri lasciare la porta SSH aperta o chiusa al momento della creazione del cluster. Se non viene specificato, il servizio aprirà o chiuderà in modo intelligente la porta a seconda che si stia distribuendo il cluster all'interno di una rete virtuale.
  + **[azureml-explain-model](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + È stata migliorata la documentazione per gli output di Spiegazione nello scenario di classificazione.
    + Aggiunta la possibilità di caricare i valori y previsti nella spiegazione per gli esempi di valutazione. Sblocca visualizzazioni più utili.
    + Aggiunta la proprietà esplicativo a MimicWrapper per consentire l'ottenimento del MimicExplainer sottostante.
  + **azureml-pipeline-core**
    + Aggiunto notebook per descrivere Module, ModuleVersion e ModuleStep
  + **azureml-pipeline-steps**
    + Aggiunto RScriptStep per supportare l'esecuzione dello script R tramite pipeline AML.
    + Correzione dell'analisi dei parametri dei metadati in AzureBatchStep che causava il messaggio di errore "l'assegnazione per il parametro SubscriptionId non è specificata".
  + **azureml-train-automl**
    + Supportato training_data, validation_data, label_column_name weight_column_name come formato di input datiSupported training_data, validation_data, label_column_name weight_column_name as data input format
    + Aggiunto messaggio di deprecazione per explain_model() e retrieve_model_explanations()
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Aggiunto un [blocco appunti](https://aka.ms/pl-modulestep) per descrivere [Module](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.module(class)), [ModuleVersion](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.moduleversion) e [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep).
  + **[azureml-pipeline-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps)**
    + Aggiunto [RScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) per supportare l'esecuzione dello script R tramite pipeline AML.
    + Correzione dei parametri dei metadati in [AzureBatchStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) che causava il messaggio di errore "l'assegnazione per il parametro SubscriptionId non è specificata".
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Supportato training_data, validation_data label_column_name weight_column_name come formato di immissione dati.
    + Aggiunto il messaggio di deprecazione per [explain_model()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) e [retrieve_model_explanations()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-).


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Azure Machine Learning SDK per Python v1.0.62

+ **Nuove funzionalità**
  + Introdotto `timeseries` il tratto su TabularDataset. Questa tratto consente di filtrare facilmente il timestamp sui dati di un TabularDataset, ad esempio l'utilizzo di tutti i dati tra un intervallo di tempo o i dati più recenti. Per informazioni su `timeseries` questo tratto su TabularDataset, si prega di visitare https://aka.ms/azureml-data per la documentazione o https://aka.ms/azureml-tsd-notebook per un blocco appunti di esempio.
  + Formazione abilitata con TabularDataset e FileDataset.Enabled training with TabularDataset and FileDataset. Si https://aka.ms/dataset-tutorial prega di visitare per un blocco appunti di esempio.

  + **azureml-train-core**
      + Aggiunto `Nccl` `Gloo` e supporto nello stimatore PyTorch

+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-core**
    + Deprecate l'impostazione AutoML 'lag_length' e LaggingTransformer.
    + Correzione della convalida corretta dei dati di input se sono specificati in un formato Flusso di datiFixed correct validation of input data if they are specified in a Dataflow format
    + Modificato il fit_pipeline.py per generare il grafico json e caricare in artefatti.
    + Rendering del `userrun` grafico `Cytoscape`in con .
  + **azureml-core**
    + Rivista la gestione delle eccezioni nel codice ADB e apportare le modifiche in base alla nuova gestione degli errori
    + Aggiunta l'autenticazione MSI automatica per le macchine virtuali notebook.
    + Risolve il bug in cui è possibile caricare modelli danneggiati o vuoti a causa di tentativi non riusciti.
    + Corretto il `DataReference` bug in `DataReference` cui il nome cambia quando `as_upload` `as_download`cambia `as_mount`la modalità (ad esempio quando si chiama , , o ).
    + Rendere `mount_point` `target_path` e `FileDataset.mount` opzionale per e `FileDataset.download`.
    + Eccezione che non è possibile trovare la colonna timestamp verrà generata se l'API correlata serie serials viene chiamata senza colonna timestamp fine assegnata o le colonne timestamp assegnate vengono eliminate.
    + Time serials colonne devono essere assegnate con colonna il cui tipo è Data, altrimenti è prevista l'eccezione
    + Le colonne Time serials che assegnano l'API 'with_timestamp_columns' possono accettare il nome della colonna timestamp none fine/coarse, che cancellerà le colonne timestamp assegnate in precedenza.
    + Eccezione verrà generata quando la colonna timestamp granulosa o granulosa fine viene eliminata con l'indicazione per l'utente che il rilascio può essere eseguito dopo aver escluso la colonna timestamp nell'elenco di rilascio o chiamare with_time_stamp con valore None per rilasciare timestamp Colonne
    + Eccezione verrà generata quando la colonna timestamp granulosa o granulosa fine non è inclusa nell'elenco Keep columns con l'indicazione per l'utente che la conservazione può essere eseguita dopo aver incluso la colonna timestamp nell'elenco delle colonne keep o chiamare with_time_stamp con None per rilasciare le colonne timestamp.
    + Aggiunta la registrazione per le dimensioni di un modello registrato.
  + **azureml-explain-model**
    + Risolto un avviso stampato sulla console quando non è installato il pacchetto python "packaging": "Utilizzando la versione precedente a quella supportata di lightgbm, eseguire l'aggiornamento alla versione maggiore di 2.2.1"
    + Correzione della spiegazione del modello di download con partizionamento per le spiegazioni globali con molte funzionalità
    + Correzione di esempi di inizializzazione mancanti dell'espediente simulato nella spiegazione dell'output
    + Correzione dell'errore non modificabile sulle proprietà impostate durante il caricamento con il client di spiegazione utilizzando due diversi tipi di modelliFixed immutable error on set properties when uploading with explanation client using two different types of models
    + È stato aggiunto un get_raw param all'esplicatore di punteggio .explain() in modo che uno esplicatore di punteggio possa restituire sia valori progettati che non elaborati.
  + **azureml-train-automl**
    + Sono state introdotte API pubbliche da AutoML per il supporto di spiegazioni da `automl` explain SDK - Newer way di supporto alle spiegazioni AutoML mediante la disaccoppiamento della fattibilizzazione di AutoML e l'SDK - Supporto integrato per le spiegazioni non elaborate da azureml explain SDK for AutoML models.
    + Rimozione di impostazioni predefinite di azureml da ambienti di formazione remoti.
    + Modificato il percorso predefinito dell'archivio cache da FileCacheStore in base a AzureFileCacheStore uno per AutoML nel percorso del codice di Azure Databricks.Changed default cache store location from FileCacheStore based one to AzureFileCacheStore one for AutoML on Azure Databricks code path.
    + Correzione della convalida corretta dei dati di input se sono specificati in un formato Flusso di datiFixed correct validation of input data if they are specified in a Dataflow format
  + **azureml-train-core**
    + Ripristinato source_directory_data_store deprecazione.
    + Aggiunta la possibilità di ignorare le versioni del pacchetto installato azureml.
    + Aggiunto il supporto `environment_definition` dockerfile nel parametro in stimatori.
    + Parametri di formazione distribuiti semplificati negli stimatori.

         ```py
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nuova esperienza Web (anteprima) per le aree di lavoro di Azure Machine Learning
La nuova esperienza Web consente ai data scientist e agli ingegneri di dati di completare il loro ciclo di vita di apprendimento automatico end-to-end, dalla preparazione e visualizzazione dei dati al training e alla distribuzione di modelli in un'unica posizione.

![Interfaccia utente dell'area di lavoro di Azure Machine Learning (anteprima)Azure Machine Learning workspace UI (preview)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Caratteristiche principali:**

Usando questa nuova interfaccia di Azure Machine Learning, è ora possibile:Using this new Azure Machine Learning interface, you can now:
+ Gestire i blocchi appunti o collegarsi a Jupyter
+ [Esegui esperimenti ML automatizzati](tutorial-first-experiment-automated-ml.md)
+ [Creare set di dati da file locali, archivi dati & file Web](how-to-create-register-datasets.md)
+ Esplorare & preparare i set di dati per la creazione di modelliExplore and prepare datasets for model creation
+ Monitora la deriva dei dati per i tuoi modelli
+ Visualizzare le risorse recenti da un dashboard

Al momento di questa versione, sono supportati i seguenti browser: Chrome, Firefox, Safari e Microsoft Edge Preview.

**Problemi noti:**

1. Aggiorna il browser se vedi "Qualcosa è andato storto! Errore durante il caricamento dei file di blocco"quando è in corso la distribuzione.

1. Impossibile eliminare o rinominare il file in Blocchi appunti e file. Durante l'anteprima pubblica è possibile usare Jupyter UI o Terminale nella macchina virtuale Notebook per eseguire operazioni sui file di aggiornamento. Poiché si tratta di un file system di rete montato, tutte le modifiche apportate nella macchina virtuale del blocco appunti vengono immediatamente riflesse nell'area di lavoro Blocco appunti.

1. A SSH nella macchina virtuale del blocco appunti:To SSH into the Notebook VM:
   1. Trovare le chiavi SSH create durante l'installazione della macchina virtuale. In alternativa, trovare le chiavi nell'area di lavoro di Azure Machine Learning > aprire la scheda Calcolo > individuare la macchina virtuale del blocco appunti nell'elenco > aprire le proprietà: copiare le chiavi dalla finestra di dialogo.
   1. Importare le chiavi SSH pubbliche e private nel computer locale.
   1. Usarli per SSH nella macchina virtuale del blocco appunti.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Azure Machine Learning SDK per Python v1.0.60

+ **Nuove funzionalità**
  + FileDataset, che fa riferimento a uno o più file negli archivi dati o negli URL pubblici. I file possono essere di qualsiasi formato. FileDataset consente di scaricare o montare i file per il calcolo. Per informazioni su FileDataset, visitare https://aka.ms/file-dataset.
  + Aggiunto il supporto di Pipeline Yaml per PythonScript Step, Adla Step, Databricks Step, DataTransferStep e AzureBatch Step

+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-core**
    + AutoArima è ora una pipeline suggerita solo per l'anteprima.
    + Segnalazione errori migliorata per le previsioni.
    + È stata migliorata la registrazione utilizzando eccezioni personalizzate anziché generiche nelle attività di previsione.
    + È stato rimosso il controllo sui max_concurrent_iterations inferiore al numero totale di iterazioni.
    + I modelli AutoML ora restituiscono AutoMLExceptions
    + Questa versione migliora le prestazioni di esecuzione delle esecuzioni locali di apprendimento automatico automatiche.
  + **azureml-core**
    + Introdurre Dataset.get_all(workspace), che restituisce `FileDataset` un dizionario di oggetti con chiave e in base al nome di `TabularDataset` registrazione.

    ```py
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + Introdurre `parition_format` come `Dataset.Tabular.from_delimited_files` argomento `Dataset.Tabular.from_parquet.files`per e . Le informazioni sulla partizione di ogni percorso dati verranno estratte in colonne in base al formato specificato. La colonna di tipo ''column_name'' crea una colonna di tipo stringa e ' column_name s:yyyy/MM/dd/HH/mm/ss' crea la colonna datetime, dove 'aaaa', 'MM', 'dd', 'HH', 'mm' e 'ss' vengono utilizzati per estrarre anno, mese, giorno, ora, minuto e secondo per il tipo datetime. Il partition_format deve iniziare dalla posizione della prima chiave di partizione fino alla fine del percorso del file. Ad esempio, dato il percorso '.. /USA/2019/01/01/01/data.csv' in cui la partizione è in base al paese e all'ora, partition_format ''/'Paese'///////////////////Dati'/data.csv' crea la colonna stringa 'Paese' con il valore 'USA' e la colonna datetime 'PartitionDate' con il valore '2019-01-01'.
        ```py
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + Introdurre `partition_format` come `Dataset.Tabular.from_delimited_files` argomento `Dataset.Tabular.from_parquet.files`per e . Le informazioni sulla partizione di ogni percorso dati verranno estratte in colonne in base al formato specificato. La colonna di tipo ''column_name'' crea una colonna di tipo stringa e ' column_name s:yyyy/MM/dd/HH/mm/ss' crea la colonna datetime, dove 'aaaa', 'MM', 'dd', 'HH', 'mm' e 'ss' vengono utilizzati per estrarre anno, mese, giorno, ora, minuto e secondo per il tipo datetime. Il partition_format deve iniziare dalla posizione della prima chiave di partizione fino alla fine del percorso del file. Ad esempio, dato il percorso '.. /USA/2019/01/01/01/data.csv' in cui la partizione è in base al paese e all'ora, partition_format ''/'Paese'///////////////////Dati'/data.csv' crea la colonna stringa 'Paese' con il valore 'USA' e la colonna datetime 'PartitionDate' con il valore '2019-01-01'.
    + `to_csv_files`e `to_parquet_files` i metodi `TabularDataset`sono stati aggiunti a . Questi metodi consentono `TabularDataset` la `FileDataset` conversione tra a e a convertendo i dati in file del formato specificato.
    + Accedere automaticamente al registro delle immagini di base quando si salva un Dockerfile generato da Model.package().
    + "gpu_support" non è più necessario; AML ora rileva e utilizza automaticamente l'estensione della finestra mobile nvidia quando è disponibile. Verrà rimosso in una versione futura.
    + Aggiunto il supporto per creare, aggiornare e utilizzare PipelineDrafts.Added support to create, update, and use PipelineDrafts.
    + Questa versione migliora le prestazioni di esecuzione delle esecuzioni locali di apprendimento automatico automatiche.
    + Gli utenti possono eseguire query sulle metriche dalla cronologia di esecuzione in base al nome.
    + È stata migliorata la registrazione utilizzando eccezioni personalizzate anziché generiche nelle attività di previsione.
  + **azureml-explain-model**
    + Aggiunto feature_maps parametro al nuovo MimicWrapper, consentendo agli utenti di ottenere spiegazioni di funzionalità non elaborate.
    + I caricamenti di set di dati sono ora disattivati per impostazione predefinita per il caricamento delle spiegazioni e possono essere riabilitati con upload_datasets: True
    + Aggiunti parametri di filtraggio "is_law" all'elenco delle spiegazioni e alle funzioni di download.
    + Aggiunge `get_raw_explanation(feature_maps)` il metodo agli oggetti di spiegazione globale e locale.
    + Aggiunto controllo della versione a lightgbm con avviso stampato se sotto la versione supportata
    + Utilizzo ottimizzato della memoria durante l'invio in batch delle spiegazioni
    + I modelli AutoML ora restituiscono AutoMLExceptions
  + **azureml-pipeline-core**
    + Aggiunto il supporto per creare, aggiornare e utilizzare PipelineDrafts - può essere utilizzato per mantenere le definizioni di pipeline modificabili e usarle in modo interattivo per l'esecuzione
  + **azureml-train-automl**
    + Creata caratteristica per installare versioni specifiche di pytorch gpu-capable v1.1.0, :::no-loc text="cuda"::: toolkit 9.0, trasformatori di piros, che è necessario per abilitare BERT / XLNet nell'ambiente di runtime python remoto.
  + **azureml-train-core**
    + Errore iniziale di alcuni errori di definizione dello spazio dell'iperparametro direttamente nell'sdk anziché sul lato server.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning Data Prep SDK v1.1.14
+ **Correzioni di bug e miglioramenti**
  + Abilitata la scrittura in ADLS/ADLSGen2 utilizzando il percorso e le credenziali non elaborati.
  + Corretto un bug `include_path=True` che causava il fatto di non funzionare per `read_parquet`.
  + Corretto `to_pandas_dataframe()` un errore causato dall'eccezione "Valore della proprietà non valido: hostSecret".
  + È stato corretto un bug in cui i file non potevano essere letti su DBFS in modalità Spark.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK per Python v1.0.57
+ **Nuove funzionalità**
  + Abilitato `TabularDataset` per essere utilizzato da AutomatedML. Per saperne `TabularDataset`di https://aka.ms/azureml/howto/createdatasetspiù su , si prega di visitare .

+ **Correzioni di bug e miglioramenti**
  + **automl-client-core-nativeclient**
    + Corretto l'errore, generato durante il training e/o le etichette di convalida (y e y_valid) vengono fornite sotto forma di frame dati panda ma non come matrice numpy.
    + Interfaccia aggiornata `RawDataContext` per creare un per `AutoMLBaseSettings` richiedere solo i dati e l'oggetto.
    +  Consentire agli utenti AutoML di abbandonare serie di formazione non sufficientemente lunghe durante la previsione. - Consentire agli utenti AutoML di eliminare i grani dal set di test che non esiste nel set di training durante la previsione.- Allow AutoML users to drop grains from the test set that does not exist in the training set when forecasting.
  + **azure-cli-ml**
    + È ora possibile aggiornare il certificato SSL per l'endpoint di punteggio distribuito nel cluster AKS sia per il certificato generato da Microsoft che per il certificato del cliente.
  + **azureml-automl-core**
    + Risolto un problema in AutoML in cui le righe con etichette mancanti non venivano rimosse correttamente.
    + Miglioramento della registrazione degli errori in AutoML; i messaggi di errore completi verranno sempre scritti nel file di registro.
    + AutoML ha aggiornato il `azureml-defaults`blocco `azureml-explain-model`del `azureml-dataprep`pacchetto per includere , e . AutoML non avviserà più in caso `azureml-train-automl` di mancata corrispondenza dei pacchetti (ad eccezione del pacchetto).
    + Risolto un `timeseries` problema in cui le divisioni cv sono di dimensioni diverse causando un errore nel calcolo della collocazione.
    + Quando si esegue l'iterazione dell'insieme per il tipo di training Cross-Validation, se abbiamo finito per avere problemi a scaricare i modelli sottoposti a training sull'intero set di dati, si verificava un'incoerenza tra i pesi del modello e i modelli che venivano inseriti nella votazione Ensemble.
    + Corretto l'errore, generato durante il training e/o le etichette di convalida (y e y_valid) vengono fornite sotto forma di frame dati panda ma non come matrice numpy.
    + Risolto il problema con le attività di previsione quando non è stato rilevato nessuno nelle colonne booleane delle tabelle di input.
    + Consentire agli utenti AutoML di abbandonare serie di formazione non sufficientemente lunghe durante la previsione. - Consentire agli utenti AutoML di eliminare i grani dal set di test che non esiste nel set di training durante la previsione.- Allow AutoML users to drop grains from the test set that does not exist in the training set when forecasting.
  + **azureml-core**
    + Risolto un problema relativo all'ordinazione dei parametri blob_cache_timeout.
    + Aggiunti tipi di eccezioni esterni e di adattamento per gli errori di sistema.
    + Aggiunto il supporto per i segreti dell'insieme di credenziali delle chiavi per le esecuzioni remote. Aggiungere una classe azureml.core.keyvault.Keyvault per aggiungere, ottenere ed elencare i segreti dall'oggetto keyvault associato all'area di lavoro. Le operazioni supportate sono:
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.keyvault.Keyvault.set_secret (nome, valore)
      + azureml.core.keyvault.Keyvault.set_secrets(secrets_dict)
      + azureml.core.keyvault.Keyvault.get_secret(name)
      + azureml.core.keyvault.Keyvault.get_secrets(secrets_list)
      + azureml.core.keyvault.Keyvault.list_secrets()
    + Metodi aggiuntivi per ottenere keyvault predefinito e ottenere segreti durante l'esecuzione remota:Additional methods to obtain default keyvault and get secrets during remote run:
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.run.Run.get_secret(name)
      + azureml.core.run.Run.get_secrets(secrets_list)
    + Aggiunti parametri di sostituzione aggiuntivi al comando submit-hyperdrive CLI.
    + Migliorare l'affidabilità delle chiamate API espandendo i tentativi alle eccezioni comuni della libreria delle richieste.
    + Aggiungere il supporto per l'invio di esecuzioni da un'esecuzione inviata.
    + Risolto il problema del token di firma di accesso condiviso in FileWatcher, che causava l'interruzione del caricamento dei file dopo la scadenza del token iniziale.
    + Importazione di file csv/tsv HTTP supportati nell'SDK di python del set di dati.
    + Deprecato il metodo Workspace.setup(). Il messaggio di avviso visualizzato agli utenti suggerisce di utilizzare create() o get()/from_config().
    + Aggiunto Environment.add_private_pip_wheel(), che consente di `whl`caricare pacchetti python privati personalizzati nell'area di lavoro e di utilizzarli in modo sicuro per compilare/materializzare l'ambiente.
    + È ora possibile aggiornare il certificato SSL per l'endpoint di punteggio distribuito nel cluster AKS sia per il certificato generato da Microsoft che per il certificato del cliente.
  + **azureml-explain-model**
    + Aggiunto parametro per aggiungere un ID modello alle spiegazioni al caricamento.
    + Aggiunto `is_raw` l'aggiunta di tag alle spiegazioni in memoria e upload.
    + Aggiunto il supporto per il pirosa e i test per il pacchetto azureml-explain-model.
  + **azureml-opendatasets**
    + Supportare il rilevamento e la registrazione dell'ambiente di test automatico.
    + Aggiunte classi per ottenere la popolazione statunitense per contea e zip.
  + **azureml-pipeline-core**
    + Aggiunta la proprietà label alle definizioni delle porte di input e di output.
  + **azureml-telemetry**
    + Correzione di una configurazione di telemetria non corretta.
  + **azureml-train-automl**
    + Risolto il bug in cui in caso di errore di installazione, errore non veniva registrato nel campo "errori" per l'esecuzione dell'installazione e quindi non è stato archiviato nell'esecuzione padre "errori".
    + Risolto un problema in AutoML in cui le righe con etichette mancanti non venivano rimosse correttamente.
    + Consentire agli utenti AutoML di abbandonare serie di formazione non sufficientemente lunghe durante la previsione.
    + Consentire agli utenti AutoML di eliminare i grani dal set di test che non esistono nel set di training durante la previsione.
    + Ora AutoMLStep `automl` passa attraverso la configurazione al back-end per evitare problemi di modifiche o aggiunte di nuovi parametri di configurazione.
    + AutoML Data Guardrail è ora in anteprima pubblica. L'utente vedrà un rapporto Data Guardrail (per le attività di classificazione/regressione) dopo il training e potrà accedervi anche tramite l'API SDK.
  + **azureml-train-core**
    + Aggiunto il supporto per la torcia 1.2 in PyTorch Estimator.
  + **azureml-widgets**
    + Miglioramento dei grafici a matrice di confusione per il training di classificazione.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning Data Prep SDK v1.1.12
+ **Nuove funzionalità**
  + Gli elenchi di stringhe possono ora `read_*` essere passati come input ai metodi.

+ **Correzioni di bug e miglioramenti**
  + Le prestazioni `read_parquet` di è stato notevolmente migliorato durante l'esecuzione in Spark.
  + Risolto un `column_type_builder` problema in cui non è riuscito in caso di una singola colonna con formati di data ambigui.

### <a name="azure-portal"></a>Portale di Azure
+ **Funzionalità di anteprima**
  + Lo streaming dei file di log e output è ora disponibile per le pagine dei dettagli di esecuzione. I file trasmetteranno gli aggiornamenti in tempo reale quando l'interruttore di anteprima è attivato.
  + La possibilità di impostare la quota a livello di area di lavoro viene rilasciata in anteprima. Le quote AmlCompute vengono allocate a livello di sottoscrizione, ma ora è possibile distribuire tale quota tra le aree di lavoro e allocarla per una condivisione e una governance eque. È sufficiente fare clic sul pannello **Utilizzo-Quote** nella barra di spostamento sinistra dell'area di lavoro e selezionare la scheda **Configura quote.**

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK per Python v1.0.55

+ **Nuove funzionalità**
  + L'autenticazione basata su token è ora supportata per le chiamate effettuate all'endpoint di assegnazione del punteggio distribuito in AKS. Continueremo a supportare l'autenticazione basata sulla chiave corrente e gli utenti possono utilizzare uno di questi meccanismi di autenticazione alla volta.
  + Possibilità di registrare un'archiviazione BLOB che si trova dietro la rete virtuale (VNet) come archivio dati.

+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-core**
    + Corregge un bug in cui le dimensioni di convalida per le divisioni CV sono piccole e si ottiene grafici stimati e vere per la regressione e la previsione.
    + La registrazione delle attività di previsione sulle esecuzioni remote è stata migliorata, ora all'utente viene fornito un messaggio di errore completo se l'esecuzione non è riuscita.
    + Correzione degli `Timeseries` errori di se il flag di pre-elaborazione è True.Fixed failures of if preprocess flag is True.
    + Più utilizzabili alcuni messaggi di errore di convalida dei dati di previsione.
    + Riduzione del consumo di memoria di AutoML mediante l'eliminazione e/o il caricamento lazy dei set di dati, in particolare tra le riproduzioni dei processi
  + **azureml-contrib-explain-model**
    + Aggiunta model_task flag agli esplicatori per consentire all'utente di ignorare la logica di inferenza automatica predefinita per il tipo di modello
    + Modifiche al widget: `contrib`installa automaticamente `nbextension` con , non più installare / abilitare - spiegazione del supporto con solo l'importanza globale delle funzionalità (ad esempio Permutative)
    + Modifiche al dashboard: - Box plot e `beeswarm` grafici di violino oltre `beeswarm` alla stampa sulla pagina di riepilogo - Rerendering molto più veloce della stampa su 'Top -k' cursore di modifica - messaggio utile che spiega come viene calcolato top-k - Messaggi personalizzabili utili al posto dei grafici quando i dati non vengono forniti
  + **azureml-core**
    + Aggiunto il metodo Model.package() per creare immagini Docker e Dockerfiles che incapsulano i modelli e le relative dipendenze.
    + Aggiornamento dei servizi Web locali per accettare InferenceConfigs contenenti oggetti Environment.
    + Fixed Model.register() producendo modelli non validi quando '.' (per la directory corrente) viene passato come parametro model_path.
    + Aggiungere Run.submit_child, la funzionalità rispecchia Experiment.submit specificando la concisia come padre dell'esecuzione figlio inviata.
    + Supportare le opzioni di configurazione da Model.register in Run.register_model.Support configuration options from Model.register in Run.register_model.
    + Possibilità di eseguire processi JAR nel cluster esistente.
    + Ora supporta i parametri instance_pool_id e cluster_log_dbfs_path.
    + Aggiunto il supporto per l'utilizzo di un oggetto Environment durante la distribuzione di un modello in un servizio Web.Added support for using an Environment object when deploying a Model to a Webservice. L'oggetto Environment può ora essere fornito come parte dell'oggetto InferenceConfig.
    + Aggiungi la mappatura appinsifht per nuove regioni - centralus - westus - northcentralus
    + Aggiunta la documentazione per tutti gli attributi in tutte le classi Datastore.
    + Aggiunto blob_cache_timeout `Datastore.register_azure_blob_container`parametro a .
    + Aggiunti save_to_directory e load_from_directory metodi a azureml.core.environment.Environment.Added save_to_directory and load_from_directory methods to azureml.core.environment.Environment.
    + Sono stati aggiunti i comandi "az ml environment download" e "az ml environment register" all'interfaccia della riga di comando.
    + Aggiunto metodo Environment.add_private_pip_wheel.
  + **azureml-explain-model**
    + È stato aggiunto il rilevamento del set di dati alle spiegazioni tramite il servizio Set di dati (anteprima).
    + Dimensioni del batch predefinite ridotte durante lo streaming di spiegazioni globali da 10k a 100.
    + Aggiunto model_task flag agli esplicatori per consentire all'utente di ignorare la logica di inferenza automatica predefinita per il tipo di modello.
  + **azureml-mlflow**
    + Corretto bug in mlflow.azureml.build_image in cui le directory nidificate vengono ignorate.
  + **azureml-pipeline-steps**
    + Aggiunta la possibilità di eseguire processi JAR nel cluster Azure Databricks esistente.
    + Aggiunti i parametri di supporto instance_pool_id e cluster_log_dbfs_path per il passaggio DatabricksStep.
    + Aggiunto il supporto per i parametri della pipeline nel passaggio DatabricksStep.Added support for pipeline parameters in DatabricksStep step.
  + **azureml-train-automl**
    + Aggiunto `docstrings` per i file correlati all'Ensemble.
    + Documenti aggiornati per una `max_cores_per_iteration` lingua più appropriata per e`max_concurrent_iterations`
    + La registrazione delle attività di previsione sulle esecuzioni remote è stata migliorata, ora all'utente viene fornito un messaggio di errore completo se l'esecuzione non è riuscita.
    + Sono stati `automlstep` rimossi get_data dal notebook della pipeline.
    + Avviato `dataprep` `automlstep`il supporto in .

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning Data Prep SDK v1.1.10

+ **Nuove funzionalità**
  + È ora possibile richiedere l'esecuzione di ispettori specifici (ad esempio istogramma, grafico a dispersione e così via) su colonne specifiche.
  + È stato aggiunto `append_columns`un argomento parallelize a . Se True, i dati verranno caricati in memoria ma l'esecuzione verrà eseguita in parallelo; se False, l'esecuzione verrà trasmessa ma a thread singolo.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK per Python v1.0.53

+ **Nuove funzionalità**
  + Machine Learning automatizzato supporta ora la formazione di modelli ONNX sulla destinazione di calcolo remota
  + Azure Machine Learning offre ora la possibilità di riprendere il training da un'esecuzione precedente, checkpoint o file di modello.
    + Scopri come [utilizzare gli stimatori per riprendere l'allenamento da una corsa precedente](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Correzioni di bug e miglioramenti**
  + **automl-client-core-nativeclient**
    + Correggere il bug sul cedimento dei tipi di colonne dopo la trasformazione (bug collegato);
    + Consentire a y_query di essere un tipo di oggetto contenente Nessuno o gli elementi all'inizio (#459519).
  + **azure-cli-ml**
    + I comandi dell'interfaccia della riga di comando "model deploy" e "service update" ora accettano parametri, file di configurazione o una combinazione dei due. I parametri hanno la precedenza sugli attributi nei file.
    + La descrizione del modello può ora essere aggiornata dopo la registrazione
  + **azureml-automl-core**
    + Aggiornare la dipendenza NimbusML alla versione 1.2.0 (ultima corrente).
    + Aggiunta del supporto per gli estimatori NimbusML & pipeline da utilizzare all'interno degli estimatori AutoML.
    + Correggere un bug nella procedura di selezione Ensemble che stava inutilmente facendo crescere l'ensemble risultante anche se le partiture rimanevano costanti.
    + Consenti il riutilizzo di alcune featurizations tra le divisioni CV per le attività di previsione. Questo velocizza il tempo di esecuzione della configurazione eseguire da circa un fattore di n_cross_validations per le imprese costose come ritardi e finestre di rotolamento.
    + Risoluzione di un problema se il tempo è fuori dall'intervallo di tempo supportato dai panda. Ora generiamo un DataException se il tempo è inferiore a pd. Timestamp.min o maggiore di pd. Timestamp.max
    + Le previsioni ora consentono frequenze diverse nei set di treni e test se possono essere allineate. Ad esempio, "trimestrale a partire da gennaio" e da "trimestrale a partire da ottobre" può essere allineato.
    + La proprietà "parameters" è stata aggiunta a TimeSeriesTransformer.
    + Rimuovere le classi di eccezione precedenti.
    + Nelle attività di `target_lags` previsione, il parametro accetta ora un singolo valore intero o un elenco di numeri interi. Se è stato fornito il numero intero, verrà creato un solo intervallo. Se viene fornito un elenco, verranno presi i valori univoci dei ritardi. target_lags'[1, 2, 2, 4] creerà ritardi di uno, 2 e 4 periodi.
    + Correggere il bug sulla perdita di tipi di colonna dopo la trasformazione (bug collegato);
    + In `model.forecast(X, y_query)`, consentire y_query essere un tipo di oggetto contenente Nessuno o i quali sono all'inizio (#459519).
    + Aggiungere i `automl` valori previsti all'output
  + **azureml-contrib-datadrift**
    +  Miglioramenti al blocco appunti di esempio, incluso il passaggio a azureml-opendatasets anziché azureml-contrib-opendatasets e miglioramenti delle prestazioni durante l'arricchimento dei dati
  + **azureml-contrib-explain-model**
    + Argomento trasformazioni fisse per lo explainer LIME per l'importanza delle feature non elaborate nel pacchetto azureml-contrib-explain-modelFixed transformations argument for LIME explainer for raw feature importance in azureml-contrib-explain-model package
    + Aggiunte segmentazioni alle spiegazioni delle immagini nell'elaborazione delle immagini per il pacchetto AzureML-contrib-explain-model
    + Aggiungi supporto sparse scipy per LimeExplainer
    + Aggiunto `batch_size` per simulare `include_local=False`l'elaborazione quando , per lo streaming di spiegazioni globali in batch per migliorare il tempo di esecuzione di DecisionTreeExplainableModel
  + **azureml-contrib-featureengineering**
    + Correzione per la chiamata set_featurizer_timeseries_params(): modifica del tipo `timeseries` di valore dict e controllo null - Aggiungi blocco appunti per featurizer
    + Aggiornare la dipendenza NimbusML alla versione 1.2.0 (ultima corrente).
  + **azureml-core**
    + Aggiunta la possibilità di collegare gli archivi dati DBFS nell'interfaccia della riga di comando di AzureMLAdded the ability to attach DBFS datastores in the AzureML CLI
    + Risolto il bug con il caricamento dell'archivio dati in cui viene creata una cartella vuota se `target_path` avviata con`/`
    + Corretto `deepcopy` problema in ServicePrincipalAuthentication.Fixed issue in ServicePrincipalAuthentication.
    + Aggiunti i comandi "az ml environment show" e "az ml environment list" all'interfaccia della riga di comando.
    + Gli ambienti supportano ora la specifica di un base_dockerfile come alternativa a un base_image già costruito.
    + L'impostazione RunConfiguration inutilizzata auto_prepare_environment è stata contrassegnata come deprecata.
    + La descrizione del modello può ora essere aggiornata dopo la registrazione
    + Bugfix: Model e Image delete ora forniscono ulteriori informazioni sul recupero di oggetti a monte che dipendono da essi se l'eliminazione non riesce a causa di una dipendenza upstream.
    + Corretto un bug che stampava la durata vuota per le distribuzioni che si verificano durante la creazione di un'area di lavoro per alcuni ambienti.
    + L'area di lavoro migliorata crea eccezioni di errore. In modo che gli utenti non vengano visualizzati "Impossibile creare l'area di lavoro. Impossibile trovare..." come messaggio e vedere invece l'errore di creazione effettivo.
    + Aggiungere il supporto per l'autenticazione token nei webservices AKS.Add support for token authentication in AKS webservices.
    + Aggiungere `get_token()` il `Webservice` metodo agli oggetti.
    + Aggiunto il supporto CLI per gestire i set di dati di machine learning.
    + `Datastore.register_azure_blob_container`ora facoltativamente `blob_cache_timeout` accetta un valore (in secondi) che configura i parametri di montaggio di blobfuse per abilitare la scadenza della cache per questo archivio dati. Il valore predefinito è nessun timeout, ovvero quando un BLOB viene letto, rimarrà nella cache locale fino al termine del processo. La maggior parte dei processi preferisce questa impostazione, ma alcuni processi devono leggere più dati da un set di dati di grandi dimensioni rispetto ai relativi nodi. Per questi processi, l'ottimizzazione di questo parametro li aiuterà ad avere successo. Prestare attenzione quando si ottimizza questo parametro: l'impostazione di un valore troppo basso può comportare una riduzione delle prestazioni, poiché i dati utilizzati in un'epoca potrebbero scadere prima di essere usati di nuovo. Ciò significa che tutte le letture verranno eseguite dall'archiviazione BLOB (ad esempio la rete) anziché dalla cache locale, con un impatto negativo sui tempi di training.
    + La descrizione del modello può ora essere aggiornata correttamente dopo la registrazione
    + L'eliminazione di modelli e immagini ora fornisce ulteriori informazioni sugli oggetti a monte che dipendono da essi, il che causa l'esito negativo dell'eliminazione
    + Migliorare l'utilizzo delle risorse di esecuzioni remote utilizzando azureml.mlflow.Improve resource utilization of remote runs using azureml.mlflow.
  + **azureml-explain-model**
    + Argomento trasformazioni fisse per lo explainer LIME per l'importanza delle feature non elaborate nel pacchetto azureml-contrib-explain-modelFixed transformations argument for LIME explainer for raw feature importance in azureml-contrib-explain-model package
    + aggiungere supporto scipy sparse per LimeExplainer
    + aggiunto forma lineare esplicativo wrapper, così come un altro livello di spiegazione tabulare per spiegare modelli lineari
    + per l'esplicatore di microfoni nella libreria del modello esplicativo, è stato corretto l'errore durante include_local-False per l'input di dati sparse
    + aggiungere i `automl` valori previsti all'output
    + importanza della funzione di permutazione fissa quando le trasformazioni argomento fornito per ottenere l'importanza della funzione non elaborata
    + aggiunto `batch_size` per simulare `include_local=False`l'elaborazione quando , per lo streaming di spiegazioni globali in batch per migliorare il tempo di esecuzione di DecisionTreeExplainableModel
    + per la libreria di spiegazione del modello, gli esplicatori di caselle di colore fisse in cui è necessario l'input del frame dati dei panda per la stima
    + Corretto un `explanation.expected_values` bug in cui a volte restituiva un float anziché un elenco con un float.
  + **azureml-mlflow**
    + Migliorare le prestazioni di mlflow.set_experiment(experiment_name)
    + Correggere il bug in uso di InteractiveLoginAuthentication per tracking_uri mlflowFix bug in use of InteractiveLoginAuthentication for mlflow tracking_uri
    + Migliorare l'utilizzo delle risorse di esecuzioni remote utilizzando azureml.mlflow.Improve resource utilization of remote runs using azureml.mlflow.
    + Migliorare la documentazione del pacchetto azureml-mlflow
    + Patch bug in cui mlflow.log_artifacts("my_dir") salverebbe gli elementi in "my_dir/<artefatto-percorsi>" anziché "<artefatto-percorsi>"
  + **azureml-opendatasets**
    + Pin `pyarrow` `opendatasets` di vecchie versioni (<0.14.0) a causa di un problema di memoria appena introdotto lì.
    + Spostare azureml-contrib-opendatasets in azureml-opendatasets.
    + Consentire la registrazione delle classi di set di dati aperte nell'area di lavoro di Azure Machine Learning e sfruttare senza problemi le funzionalità del set di dati AML.
    + Migliorare In modo significativo le prestazioni di NoaaIsdWeather nella versione non SPARK.
  + **azureml-pipeline-steps**
    + DBFS Datastore è ora supportato per gli ingressi e gli output in DatabricksStep.
    + Documentazione aggiornata per il passaggio batch di Azure per quanto riguarda gli input/output.
    + In AzureBatchStep, modificato *delete_batch_job_after_finish* valore predefinito *su true*.
  + **azureml-telemetry**
    +  Spostare azureml-contrib-opendatasets in azureml-opendatasets.
    + Consentire la registrazione delle classi di set di dati aperte nell'area di lavoro di Azure Machine Learning e sfruttare senza problemi le funzionalità del set di dati AML.
    + Migliorare In modo significativo le prestazioni di NoaaIsdWeather nella versione non SPARK.
  + **azureml-train-automl**
    + Documentazione aggiornata su get_output per riflettere il tipo restituito effettivo e fornire note aggiuntive sul recupero delle proprietà chiave.
    + Aggiornare la dipendenza NimbusML alla versione 1.2.0 (ultima corrente).
    + aggiungere i `automl` valori previsti all'output
  + **azureml-train-core**
    + Le stringhe vengono ora accettate come destinazione di calcolo per l'ottimizzazione automatica degli iperparametri
    + L'impostazione RunConfiguration inutilizzata auto_prepare_environment è stata contrassegnata come deprecata.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning Data Prep SDK v1.1.9

+ **Nuove funzionalità**
  + Aggiunto il supporto per la lettura di un file direttamente da un URL http o https.

+ **Correzioni di bug e miglioramenti**
  + Messaggio di errore migliorato quando si tenta di leggere un set di dati Parquet da un'origine remota (che non è attualmente supportata).
  + Corretto un bug durante la scrittura nel formato di file di parquet in ADLS Gen 2 e l'aggiornamento del nome del contenitore ADLS Gen 2 nel percorso.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Interfaccia visiva
+ **Funzionalità di anteprima**
  + Aggiunto il modulo "Esegui script R" nell'interfaccia visiva.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK per Python v1.0.48

+ **Nuove funzionalità**
  + **azureml-opendatasets**
    + **azureml-contrib-opendatasets** è ora disponibile come **azureml-opendatasets**. Il pacchetto precedente può ancora funzionare, ma è consigliabile usare **azureml-opendatasets** per ottenere funzionalità e miglioramenti più avanzati.
    + Questo nuovo pacchetto consente di registrare set di dati aperti come set di dati nell'area di lavoro di Azure Machine Learning e di sfruttare tutte le funzionalità offerte dal set di dati.
    + Include inoltre funzionalità esistenti, ad esempio l'utilizzo di set di dati aperti come frame di dati Pandas/SPARK e l'aggiunta di posizioni per alcuni set di dati, ad esempio il meteo.

+ **Funzionalità di anteprima**
    + HyperDriveConfig può ora accettare l'oggetto pipeline come parametro per supportare l'ottimizzazione degli iperparametri tramite una pipeline.

+ **Correzioni di bug e miglioramenti**
  + **azureml-train-automl**
    + Corretto il bug sulla perdita di tipi di colonna dopo la trasformazione.
    + Corretto il bug per consentire a y_query di essere un tipo di oggetto contenente Nessuno o gli elementi iniziali.
    + Risolto il problema nella procedura di selezione Ensemble che stava inutilmente facendo crescere l'ensemble risultante anche se le partiture rimanevano costanti.
    + Risolto il problema con le impostazioni whitelist_models e blacklist_models in AutoMLStep.
    + Corretto il problema che impediva l'utilizzo della pre-elaborazione quando AutoML sarebbe stato usato nel contesto delle pipeline di Azure ML.
  + **azureml-opendatasets**
    + Spostati azureml-contrib-opendatasets in azureml-opendatasets.
    + Sono consentite le classi di set di dati aperti per la registrazione nell'area di lavoro di Azure Machine Learning e sfruttare senza problemi le funzionalità del set di dati AML.
    + NoaisdWeather migliorato arricchire le prestazioni nella versione non-SPARK in modo significativo.
  + **azureml-explain-model**
    + Documentazione online aggiornata per gli oggetti di interpretità.
    + Aggiunto `batch_size` per simulare `include_local=False`l'elaborazione quando , per lo streaming di spiegazioni globali in batch per migliorare il tempo di esecuzione di DecisionTreeExplainableModel per la libreria di spiegabilità del modello.
    + Risolto il `explanation.expected_values` problema a cui a volte restituiva un float anziché un elenco con un float.
    + Aggiunti i `automl` valori previsti all'output per l'esplicativo simulato nella libreria del modello esplicativo.
    + Correzione dell'importanza della funzionalità di permutazione quando viene fornito l'argomento trasformazioni per ottenere l'importanza non elaborata della funzionalità.
  + **azureml-core**
    + Aggiunta la possibilità di collegare gli archivi dati DBFS nell'interfaccia della riga di comando di AzureML.
    + Risolto il problema con il caricamento dell'archivio dati in cui viene creata una cartella vuota se `target_path` inizia con `/`.
    + È stato abilitato il confronto di due set di dati.
    + Model and Image delete ora fornisce ulteriori informazioni sul recupero di oggetti a monte che dipendono da essi se delete non riesce a causa di una dipendenza upstream.
    + Deprecata l'impostazione RunConfiguration inutilizzata in auto_prepare_environment.
  + **azureml-mlflow**
    + Miglioramento dell'utilizzo delle risorse delle esecuzioni remote che usano azureml.mlflow.Improved resource utilization of remote runs that use azureml.mlflow.
    + Migliorata la documentazione del pacchetto azureml-mlflow.
    + Risolto il problema in cui mlflow.log_artifacts("my_dir") salvava gli elementi in "my_dir/artefact-paths" anziché in "artifact-paths".
  + **azureml-pipeline-core**
    + Il hash_paths per tutti i passaggi della pipeline è deprecato e verrà rimosso in futuro. Per impostazione predefinita, viene eseguito l'hashing del contenuto dell'source_directory (ad eccezione dei file elencati in .amlignore o .gitignore)
    + Continuo miglioramento di Module e ModuleStep per supportare i moduli specifici del tipo di calcolo, per preparare l'integrazione di RunConfiguration e altre modifiche per sbloccare l'utilizzo del modulo specifico del tipo di calcolo nelle pipeline.
  + **azureml-pipeline-steps**
    + AzureBatchStep: documentazione migliorata per quanto riguarda gli input/output.
    + AzureBatchStep: modificato delete_batch_job_after_finish valore predefinito su true.
  + **azureml-train-core**
    + Le stringhe vengono ora accettate come destinazione di calcolo per l'ottimizzazione automatica degli iperparametri.
    + Deprecata l'impostazione RunConfiguration inutilizzata in auto_prepare_environment.
    + Parametri `conda_dependencies_file_path` deprecati `pip_requirements_file_path` e `conda_dependencies_file` a `pip_requirements_file` favore e rispettivamente.
  + **azureml-opendatasets**
    + Migliorare In modo significativo le prestazioni di NoaaIsdWeather nella versione non SPARK.

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Azure Machine Learning Data Prep SDK v1.1.8

+ **Nuove funzionalità**
 + Gli oggetti flusso di dati possono ora essere iterati, producendo una sequenza di record. Vedere la `Dataflow.to_record_iterator`documentazione relativa a .
  + Gli oggetti flusso di dati possono ora essere iterati, producendo una sequenza di record. Vedere la `Dataflow.to_record_iterator`documentazione relativa a .

+ **Correzioni di bug e miglioramenti**
 + Aumentata l'affidabilità di DataPrep SDK.
 + Gestione migliorata dei frame di dati panda con indici di colonna non stringa.
 + Migliorate le `to_pandas_dataframe` prestazioni dei dataset.
 + Corretto un bug in cui l'esecuzione di Spark dei set di dati non è riuscita durante l'esecuzione in un ambiente a più nodi.
  + Aumentata l'affidabilità di DataPrep SDK.
  + Gestione migliorata dei frame di dati panda con indici di colonna non stringa.
  + Migliorate le `to_pandas_dataframe` prestazioni dei dataset.
  + Corretto un bug in cui l'esecuzione di Spark dei set di dati non è riuscita durante l'esecuzione in un ambiente a più nodi.

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Azure Machine Learning Data Prep SDK v1.1.7

È stata ripristinata una modifica che ha migliorato le prestazioni, in quanto causava problemi per alcuni clienti che usano Azure Databricks.We reverted a change that improved performance, as it was causing issues for some customers using Azure Databricks. Se si è verificato un problema in Azure Databricks, è possibile eseguire l'aggiornamento alla versione 1.1.7 usando uno dei metodi seguenti:If you experienced an issue on Azure Databricks, you can upgrade to version 1.1.7 using one of the methods below:
1. Eseguire questo script per eseguire l'aggiornamento:Run this script to upgrade:`%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. Ricreare il cluster, che installerà la versione più recente di Data Prep SDK.

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>Azure Machine Learning SDK per Python v1.0.45

+ **Nuove funzionalità**
  + Aggiungere il modello surrogato dell'albero delle decisioni per simulare l'esplicatore nel pacchetto azureml-explain-modelAdd decision tree surrogate model to mimic explainer in azureml-explain-model package
  + Possibilità di specificare una versione CUDA da installare nelle immagini in ferencing. Supporto per CUDA 9.0, 9.1 e 10.0.
  + Le informazioni sulle immagini di base di formazione di Azure ML sono ora disponibili in Contenitori di [Azure ML GitHub Repository](https://github.com/Azure/AzureML-Containers) e [DockerHub](https://hub.docker.com/_/microsoft-azureml)
  + Aggiunto il supporto dell'interfaccia della riga di comando per la pianificazione della pipeline. Eseguire "az ml pipeline -h" per ulteriori informazioni
  + È stato aggiunto il parametro dello spazio dei nomi Kubernetes personalizzato alla configurazione della distribuzione del servizio Web AKS e all'interfaccia della riga di comando.
  + Parametro hash_paths deprecato per tutti i passaggi della pipelineDeprecated hash_paths parameter for all pipeline steps
  + Model.register supporta ora la registrazione di più file singoli `child_paths` come singolo modello con l'utilizzo del parametro.

+ **Funzionalità di anteprima**
    + Gli esplicatori di punteggio possono ora facoltativamente salvare le informazioni di conda e pip per una serializzazione e una deserializzazione più affidabili.
    + Correzione di bug per il selettore automatico delle funzioni.
    + Aggiornamento di mlflow.azureml.build_image alla nuova api, bug con patch esposti dalla nuova implementazione.

+ **Correzioni di bug e miglioramenti**
  + Dipendenza `paramiko` rimossa da azureml-core. Aggiunti avvisi di deprecazione per i metodi di associazione della destinazione di calcolo legacy.
  + Migliorare le prestazioni di run.create_children
  + In mimic explainer con classificatore binario, correggere l'ordine delle probabilità quando la probabilità dell'insegnante viene utilizzata per scalare i valori della forma.
  + Gestione degli errori e messaggi migliorati per l'apprendimento automatico automatizzato.
  + Corretto il problema di timeout dell'iterazione per l'apprendimento automatico automatico.
  + Sono state migliorate le prestazioni di trasformazione della serie temporale per l'apprendimento automatico automatizzato.

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Azure Machine Learning Data Prep SDK v1.1.6

+ **Nuove funzionalità**
  + Sono state aggiunte`SummaryFunction.TOPVALUES`funzioni di riepilogo`SummaryFunction.BOTTOMVALUES`per i primi valori ( ) e i valori inferiori ( ).

+ **Correzioni di bug e miglioramenti**
  + Significativamente migliorato le `read_pandas_dataframe`prestazioni di .
  + Corretto un bug `get_profile()` che causava l'esito negativo di un Dataflow che punta a file binari.
  + Esposto `set_diagnostics_collection()` per consentire l'abilitazione/disabilitazione a livello di codice della raccolta di telemetria.
  + Modificato il `get_profile()`comportamento di . I valori NaN vengono ora ignorati per Min, Mean, Std e Sum, che si allinea con il comportamento dei Panda.


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>Azure Machine Learning SDK per Python v1.0.43

+ **Nuove funzionalità**
  + Azure Machine Learning ora fornisce supporto di prima classe per l'apprendimento automatico e il framework di analisi dei dati più diffusi Scikit-learn. Utilizzando [ `SKLearn` estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py), gli utenti possono facilmente addestrare e distribuire modelli Scikit-learn.
    + Informazioni su come [eseguire l'ottimizzazione degli iperparametri con Scikit-learn con HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb).
  + Aggiunto il supporto per la creazione di ModuleStep nelle pipeline insieme alle classi Module e ModuleVersion per gestire le unità di calcolo riutilizzabili.
  + I servizi Web ACI ora supportano scoring_uri persistenti tramite gli aggiornamenti. Il scoring_uri cambierà da IP a FQDN. L'etichetta nome DNS per FQDN può essere configurata impostando la dns_name_label su deploy_configuration.
  + Nuove funzionalità di apprendimento automatico:
    + StL featurizer per la previsione
    + Il clustering KMeans è abilitato per lo sweep delle funzioni
  + Le approvazioni amlCompute Quota sono diventate più veloci! Ora abbiamo automatizzato il processo per approvare le richieste di quote entro una soglia. Per ulteriori informazioni sul funzionamento delle quote, vedere [come gestire le quote.](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas)

+ **Funzionalità di anteprima**
    + Integrazione con il rilevamento [MLflow](https://mlflow.org) 1.0.0 tramite pacchetto azureml-mlflow[(blocchi appunti di esempio).](https://aka.ms/azureml-mlflow-examples)
    + Inviare il notebook Jupyter come corsa. [Documentazione di riferimento API](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + Anteprima pubblica di [Data Drift Detector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) tramite il pacchetto azureml-contrib-datadrift[(blocchi appunti di esempio).](https://aka.ms/azureml-datadrift-example) La deriva dei dati è uno dei motivi principali per cui la precisione del modello si riduce nel tempo. Succede quando i dati serviti per il modello nell'ambiente di produzione sono diversi dai dati su cui è stato eseguito il training del modello. AmL Data Drift detector aiuta il cliente a monitorare la deriva dei dati e invia un avviso ogni volta che viene rilevata una deriva.

+ **Modifiche che causano un'interruzione**

+ **Correzioni di bug e miglioramenti**
  + Il caricamento e il salvataggio di RunConfiguration supportano la specifica di un percorso di file completo con back-compat completo per il comportamento precedente.
  + Aggiunta la memorizzazione nella cache in ServicePrincipalAuthentication, disattivata per impostazione predefinita.
  + Abilitare la registrazione di più grafici con lo stesso nome di metrica.
  + Classe modello ora importabile correttamente da`from azureml.core import Model`azureml.core ( ).
  + Nei passaggi `hash_path` della pipeline, il parametro è ora deprecato. Nuovo comportamento consiste nell'hash source_directory completa, ad eccezione dei file elencati in .amlignore o .gitignore.
  + Nei pacchetti di `get_all` `get_all_*` pipeline, vari metodi e `list` `list_*`sono stati deprecati a favore e , rispettivamente.
  + azureml.core.get_run non richiede più l'importazione di classi prima di restituire il tipo di esecuzione originale.
  + Risolto un problema a quando alcune chiamate a WebService Update non attivavano un aggiornamento.
  + Il timeout del punteggio nei servizi Web AKS deve essere compreso tra 5 ms e 300000ms. Max ha permesso scoring_timeout_ms per le richieste di punteggio è stato urtato da 1 min a 5 min.
  + Gli oggetti LocalWebservice ora hanno `scoring_uri` e `swagger_uri` le proprietà.
  + Spostato restituisce la creazione della directory e restituisce il caricamento della directory dal processo utente. Abilitato l'SDK della cronologia di esecuzione per l'esecuzione in ogni processo utente. In questo modo è necessario risolvere alcuni problemi di sincronizzazione riscontrati dalle esecuzioni di formazione distribuite.
  + Il nome del log di azureml scritto dal nome del processo utente includerà ora il nome del processo (solo per il training distribuito) e il PID.

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Azure Machine Learning Data Prep SDK v1.1.5

+ **Correzioni di bug e miglioramenti**
  + Per i valori datetime interpretati con un formato di anno a 2 cifre, l'intervallo di anni validi è stato aggiornato in modo da corrispondere a Windows May Release. La gamma è stata modificata da 1930-2029 a 1950-2049.
  + Durante la lettura di `handleQuotedLineBreaks=True` `\r` un file e l'impostazione di , verrà considerata come una nuova riga.
  + Correzione di un `read_pandas_dataframe` bug che in alcuni casi ha causato un errore.
  + Miglioramento delle `get_profile`prestazioni di .
  + Messaggi di errore migliorati.

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Azure Machine Learning Data Prep SDK v1.1.4

+ **Nuove funzionalità**
  + È ora possibile usare le seguenti funzioni del linguaggio delle espressioni per estrarre e analizzare i valori datetime in nuove colonne.
    + `RegEx.extract_record()`estrae gli elementi datetime in una nuova colonna.
    + `create_datetime()`crea oggetti datetime da elementi datetime separati.
  + Quando `get_profile()`si chiama , è ora possibile vedere che le colonne quantili sono etichettate come (est.) per indicare chiaramente che i valori sono approssimazioni.
  + È ora possibile usare il globbing di Esempio durante la lettura da Archiviazione BLOB di Azure.You can now use s globbing when reading from Azure Blob Storage.
    + Ad esempio: `dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **Correzioni**
  + Correzione di un bug relativo alla lettura di un file di parquet da un'origine remota (BLOB di Azure).

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Azure Machine Learning SDK per Python v1.0.39
+ **Modifiche**
  + L'opzione Esegui configurazione auto_prepare_environment è deprecata, con la preparazione automatica che diventa l'impostazione predefinita.

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Azure Machine Learning Data Prep SDK v1.1.3

+ **Nuove funzionalità**
  + Aggiunto il supporto per leggere da un database PostgresSQL, chiamando read_postgresql o utilizzando un Datastore.
    + Vedere gli esempi nelle guide pratiche:See examples in how-to guides:
      + [Blocco appunti per l'inserimento dei dati](https://aka.ms/aml-data-prep-ingestion-nb)
      + [Blocco appunti dell'archivio dati](https://aka.ms/aml-data-prep-datastore-nb)

+ **Correzioni di bug e miglioramenti**
  + Risolti i problemi relativi alla conversione dei tipi di colonna:Fixed issues with column type conversion:
  + Ora converte correttamente una colonna booleana o numerica in una colonna booleana.
  + Ora non ha esito negativo quando si tenta di impostare una colonna data per essere tipo di data.
  + Tipi JoinType migliorati e documentazione di riferimento associata. Quando si uniscono due flussi di dati, è ora possibile specificare uno di questi tipi di join:
    + NONE, MATCH, INNER, UNMATCHLEFT, LEFTANTI, LEFTOUTER, UNMATCHRIGHT, RIGHTANTI, RIGHTOUTER, FULLANTI, FULL.
  + Miglioramento dell'inferenza dei tipi di dati per riconoscere più formati di data.

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Portale di Azure

Nel portale di Azure è ora possibile:In Azure portal, you can now:
+ Creare ed eseguire esperimenti ML automatizzati
+ Creare una macchina virtuale del blocco appunti per provare blocchi appunti Jupyter di esempio o per blocchi personalizzati.
+ Nuova sezione Creazione e modifica (anteprima) nell'area di lavoro di Azure Machine Learning, che include Machine Learning automatizzato, interfaccia visiva e macchine virtuali appunti ospitate
    + Creare automaticamente un modello usando l'apprendimento automaticoAutomatically create a model using Automated machine learning
    + Usare un'interfaccia visiva di trascinamento della selezione per eseguire esperimentiUse a drag and drop Visual Interface to run experiments
    + Creare una macchina virtuale del blocco appunti per esplorare i dati, creare modelli e distribuire servizi.
+ Aggiornamento di grafici e metriche in tempo reale nei report eseguiti e nelle pagine dei dettagli di esecuzione
+ Visualizzatore di file aggiornato per log, output e snapshot nelle pagine dei dettagli di Esegui.
+ Esperienza di creazione di report nuova e migliorata nella scheda Esperimenti.
+ Aggiunta la possibilità di scaricare il file config.json dalla pagina Panoramica dell'area di lavoro di Azure Machine Learning.
+ Supportare la creazione dell'area di lavoro di Azure Machine Learning dall'area di lavoro di Azure Databricks.Support Azure Machine Learning workspace creation from the Azure Databricks workspace.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>Azure Machine Learning SDK per Python v1.0.33
+ **Nuove funzionalità**
  + Il metodo _Workspace.create_ ora accetta configurazioni cluster predefinite per i cluster CPU e GPU.
  + Se la creazione dell'area di lavoro non riesce, le risorse dipendenti vengono pulite.
  + SKU del Registro di sistema contenitore di Azure predefinito è stato passato a basic.Default Azure Container Registry SKU was switched to basic.
  + Registro contenitori di Azure viene creato in modo non uniforme, quando necessario per l'esecuzione o la creazione di immagini.
  + Supporto per ambienti per le esecuzioni di formazione.

### <a name="notebook-virtual-machine"></a>Macchina virtuale notebook 

Usare una macchina virtuale Notebook come ambiente di hosting sicuro e pronto per l'azienda per gli appunti Jupyter in cui è possibile programmare esperimenti di apprendimento automatico, distribuire modelli come endpoint Web ed eseguire tutte le altre operazioni supportate da Azure Machine Learning SDK tramite Python.Use a Notebook VM as a secure, enterprise-ready hosting environment for Jupyter notebooks in which you can program machine learning experiments, deploy models as web endpoints and perform all other operations supported by Azure Machine Learning SDK using Python.Fornisce diverse funzionalità:
+ [Creare rapidamente una macchina virtuale](tutorial-1st-experiment-sdk-setup.md) del blocco appunti preconfigurata con la versione più recente di Azure Machine Learning SDK e i pacchetti correlati.
+ L'accesso è protetto tramite tecnologie collaudate, ad esempio HTTPS, autenticazione e autorizzazione di Azure Active Directory.Access is secured through collaudata technologies, such as HTTPS, Azure Active Directory authentication and authorization.
+ Archiviazione cloud affidabile di blocchi appunti e codice nell'account di archiviazione BLOB di Azure Machine Learning Workspace.Reliable cloud storage of notebooks and code in your Azure Machine Learning Workspace blob storage account.È possibile eliminare in modo sicuro la macchina virtuale del blocco appunti senza perdere il lavoro.
+ Blocchi appunti di esempio preinstallati per esplorare e sperimentare le funzionalità di Azure Machine Learning.Preinstalled sample notebooks to explore and experiment with Azure Machine Learning features.
+ Funzionalità di personalizzazione complete delle macchine virtuali di Azure, qualsiasi tipo di macchina virtuale, qualsiasi pacchetto, qualsiasi driver. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Versione di Azure Machine Learning SDK per Python v1.0.33.

+ I modelli accelerati hardware di Azure ML in [FPGA](how-to-deploy-fpga-web-service.md) sono generalmente disponibili.
  + È ora possibile [usare il pacchetto azureml-accel-models per:You can now use the azureml-accel-models package](how-to-deploy-fpga-web-service.md) to:
    + Allenare i pesi di una rete neurale profonda supportata (ResNet 50, ResNet 152, DenseNet-121, VGG-16 e SSD-VGG)
    + Usare l'apprendimento del trasferimento con il DNN supportato
    + Registrare il modello con il servizio di gestione modelli e contenitore il modelloRegister the model with Model Management Service and containerize the model
    + Distribuire il modello in una macchina virtuale di Azure con un FPGA in un cluster di servizio Kubernetes di AzureDeploy the model to an Azure VM with an FPGA in an Azure Kubernetes Service (AKS) cluster
  + Distribuire il contenitore in un dispositivo server [Azure Data Box EdgeDeploy](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) the container to an Azure Data Box Edge server device
  + Assegnare un punteggio ai dati con l'endpoint gRPC con questo [esempioScore](https://github.com/Azure-Samples/aml-hardware-accelerated-models) your data with the gRPC endpoint with this sample

### <a name="automated-machine-learning"></a>Funzionalità automatizzate di Machine Learning

+ Sweeping delle funzionalità :::no-loc text="featurizers"::: per consentire l'aggiunta dinamica per l'ottimizzazione delle prestazioni. Nuovo :::no-loc text="featurizers":::: incorporamenti di lavoro, peso dell'evidenza, codifiche di destinazione, codifica della destinazione del testo, distanza del cluster
+ Smart CV per gestire il treno / divisioni valide all'interno di ML automatizzato
+ Poche modifiche all'ottimizzazione della memoria e miglioramento delle prestazioni di runtime
+ Miglioramento delle prestazioni nella spiegazione del modello
+ Conversione del modello ONNX per l'esecuzione locale
+ Aggiunto il supporto per il sottocampionamento
+ Arresto intelligente quando non sono stati definiti criteri di uscita
+ Ensemble impilati

+ Previsione di una serie temporale
  + Nuova funzione di previsione previsione
  + È ora possibile utilizzare la convalida incrociata di origine continua sui dati della serie temporale
  + Nuova funzionalità aggiunta per configurare i ritardi nelle serie temporali
  + Nuove funzionalità aggiunte per supportare le funzionalità di aggregazione in sequenza
  + Nuovo rilevamento delle vacanze e featurizzatore quando il codice paese è definito nelle impostazioni dell'esperimento

+ Azure Databricks
  + Abilitata la funzionalità di previsione e spiegazione dei modelli per serie temporali
  + Ora è possibile annullare e riprendere (continuare) gli esperimenti ML automatizzati
  + Aggiunto il supporto per l'elaborazione multicore

### <a name="mlops"></a>MLOps
+ **Debug del & della distribuzione locale per i contenitori di assegnazione del punteggioLocal deployment & debugging for scoring containers**<br/> È ora possibile distribuire un modello di ML in locale e scorrere rapidamente il file di punteggio e le dipendenze per assicurarsi che si comportino come previsto.

+ **Introdotto InferenceConfig & Model.deploy()**<br/> La distribuzione del modello supporta ora la specifica di una cartella di origine con uno script di immissione, come un RunConfig.Model deployment now supports specifying a source folder with an entry script, the same as a RunConfig.  Inoltre, la distribuzione del modello è stata semplificata in un singolo comando.

+ **Monitoraggio dei riferimenti Git**<br/> I clienti hanno richiesto le funzionalità di integrazione Git di base per un certo periodo di tempo in quanto consente di mantenere un audit trail end-to-end. È stato implementato il rilevamento tra le entità principali in Azure ML per i metadati correlati a Git (repo, commit, stato pulito). Queste informazioni verranno raccolte automaticamente dall'SDK e dall'interfaccia della riga di comando.

+ **Servizio di convalida della profilatura & del modello**<br/> I clienti spesso si lamentano della difficoltà di dimensionare correttamente il calcolo associato al loro servizio di inferenza. Con il nostro servizio di profilatura del modello, il cliente può fornire input di esempio e verrà profilato tra 16 diverse configurazioni di CPU / memoria per determinare il dimensionamento ottimale per la distribuzione.

+ **Porta la tua immagine di base per l'inferenza**<br/> Un'altra lamentela comune è stata la difficoltà di passare dalla sperimentazione all'inferenza delle dipendenze di condivisione RE. Con la nostra nuova funzionalità di condivisione delle immagini di base, è ora possibile riutilizzare le immagini di base della sperimentazione, le dipendenze e tutto, per l'inferenza. Questo dovrebbe accelerare le distribuzioni e ridurre il divario dal ciclo interno a quello esterno.

+ **Esperienza di generazione dello schema Swagger migliorataImproved Swagger schema generation experience**<br/> Il nostro precedente metodo di generazione swagger era soggetto a errori e impossibile da automatizzare. Abbiamo un nuovo modo in linea di generare schemi swagger da qualsiasi funzione Python tramite decoratori. Abbiamo open-source questo codice e il nostro protocollo di generazione dello schema non è associato alla piattaforma Azure ML.

+ **L'interfaccia della riga di comando di Azure ML è in genere disponibile (GA)Azure ML CLI is generally available (GA)**<br/> I modelli possono ora essere distribuiti con un singolo comando CLI. Abbiamo ricevuto il feedback comune dei clienti che nessuno distribuisce un modello di ML da un notebook Jupyter. La documentazione di [**riferimento dell'interfaccia della riga**](https://aka.ms/azmlcli) di comando è stata aggiornata.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK per Python v1.0.30 è stato rilasciato.

È [`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) stato introdotto per aggiungere una nuova versione di una pipeline pubblicata mantenendo lo stesso endpoint.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure Machine Learning Data Prep SDK v1.1.2

Nota: Data Prep Python `numpy` SDK `pandas` non verrà più installato e pacchetti. Vedere [le istruzioni di installazione aggiornate](https://github.com/Microsoft/AMLDataPrepDocs).

+ **Nuove funzionalità**
  + È ora possibile utilizzare la trasformazione Pivot.
    + Guida alle procedure: [Blocco appunti Pivot](https://aka.ms/aml-data-prep-pivot-nb)
  + È ora possibile usare espressioni regolari nelle funzioni native.
    + Esempi:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + È ora `to_upper`  possibile `to_lower`  utilizzare e funzioni nel linguaggio delle espressioni.
  + È ora possibile visualizzare il numero di valori univoci di ogni colonna in un profilo dati.
  + Per alcuni dei passaggi del lettore comunemente `infer_column_types` utilizzati, è ora possibile passare l'argomento. Se è impostata su `True`, Preparazione dati tenterà di rilevare e convertire automaticamente i tipi di colonna.
    + `inference_arguments`è ora deprecato.
  + È ora `Dataflow.shape`possibile chiamare .

+ **Correzioni di bug e miglioramenti**
  + `keep_columns` ora accetta un argomento `validate_column_exists`facoltativo aggiuntivo, `keep_columns` che controlla se il risultato di conterrà colonne.
  + Tutti i passaggi del lettore (che leggono `verify_exists`da un file) ora accettano un argomento facoltativo aggiuntivo.
  + Miglioramento delle prestazioni di lettura dal frame di dati pandas e recupero dei profili dati.
  + Corretto un bug in cui il sezionamento di un singolo passaggio da un flusso di dati non è riuscito con un singolo indice.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Portale di Azure
  + È ora possibile inviare nuovamente uno script esistente in un cluster di calcolo remoto esistente.
  + È ora possibile eseguire una pipeline pubblicata con nuovi parametri nella scheda Pipeline.You can now run a published pipeline with new parameters on the Pipelines tab.
  + I dettagli di esecuzione ora supportano un nuovo visualizzatore di file di snapshot. È possibile visualizzare uno snapshot della directory quando è stata inviata un'esecuzione specifica. È inoltre possibile scaricare il blocco appunti inviato per avviare l'esecuzione.
  + È ora possibile annullare le esecuzioni padre dal portale di Azure.You can now cancel parent runs from the Azure portal.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK per Python v1.0.23

+ **Nuove funzionalità**
  + Azure Machine Learning SDK ora supporta Python 3.7.
  + Gli estimatori DNN di Azure Machine Learning ora forniscono il supporto integrato per più versioni. Ad esempio, `TensorFlow`  lo stimatore `framework_version` ora accetta un parametro e gli utenti possono specificare la versione '1.10' o '1.12'. Per un elenco delle versioni supportate dalla `get_supported_versions()` versione corrente dell'SDK, `TensorFlow.get_supported_versions()`chiamare la classe del framework desiderata, ad esempio .
  Per un elenco delle versioni supportate dalla versione più recente dell'SDK, vedere la documentazione relativa a [DNN Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py).

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure Machine Learning Data Prep SDK v1.1.1

+ **Nuove funzionalità**
  + È possibile leggere più origini Datastore/DataPath/DataReference utilizzando le trasformazioni di read_.
  + È possibile eseguire le seguenti operazioni sulle colonne per creare una nuova colonna: divisione, pavimento, modulo, potenza, lunghezza.
  + Preparazione dati fa ora parte della suite di diagnostica di Azure ML e registrerà le informazioni di diagnostica per impostazione predefinita.
    + Per disattivare questa opzione, impostare questa variabile di ambiente su true: DISABLE_DPREP_LOGGER

+ **Correzioni di bug e miglioramenti**
  + È stata migliorata la documentazione del codice per le funzioni e le classi di uso comune.
  + Correzione di un bug in auto_read_file che non è riuscito a leggere i file di Excel.Fixed a bug in auto_read_file that failed to read Excel files.
  + Aggiunta l'opzione per sovrascrivere la cartella in read_pandas_dataframe.
  + Miglioramento delle prestazioni dell'installazione delle dipendenze di dotnetcore2 e supporto aggiuntivo per Fedora 27/28 e Ubuntu 1804.
  + Sono state migliorate le prestazioni di lettura dai BLOB di Azure.Improved the performance of reading from Azure Blobs.
  + Il rilevamento dei tipi di colonna ora supporta le colonne di tipo Long.Column type detection now supports columns of type Long.
  + È stato corretto un bug in cui alcuni valori di data venivano visualizzati come timestamp anziché come oggetti datetime Python.Fixed a bug where some date values were being displayed as timestamps instead of Python datetime objects.
  + Corretto un bug in cui alcuni conteggi di tipi venivano visualizzati come valori double anziché come numeri interi.


## <a name="2019-03-25"></a>25/03/2019

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK per Python v1.0.21

+ **Nuove funzionalità**
  + Il metodo *azureml.core.Run.create_children* consente la creazione a bassa latenza di più esecuzioni figlio con una singola chiamata.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure Machine Learning Data Prep SDK v1.1.0

+ **Modifiche che causano un'interruzione**
  + Il concetto di Data Prep Package è deprecato e non è più supportato. Anziché rendere persistenti più flussi di dati in un unico pacchetto, è possibile rendere persistenti i flussi di dati singolarmente.
    + Guida alle procedure: [Apertura e salvataggio del blocco appunti dei flussi di dati](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **Nuove funzionalità**
  + Preparazione dati è ora in grado di riconoscere le colonne che corrispondono a un determinato tipo semantico e di suddivisioni di conseguenza. Gli STypes attualmente supportati includono: indirizzo di posta elettronica, coordinate geografiche (latitudine & longitudine), indirizzi IPv4 e IPv6, numero di telefono degli Stati Uniti e codice postale degli Stati Uniti.
    + Guida alle procedure: [Blocco appunti Tipi semantici](https://aka.ms/aml-data-prep-semantic-types-nb)
  + Data Prep supporta ora le seguenti operazioni per generare una colonna risultante da due colonne numeriche: sottrarre, moltiplicare, dividere e modulo.
  + È possibile `verify_has_data()` chiamare su un flusso di dati per verificare se il flusso di dati produrrebbe record se eseguito.

+ **Correzioni di bug e miglioramenti**
  + È ora possibile specificare il numero di raccoglitori da utilizzare in un istogramma per i profili di colonna numerici.
  + La `read_pandas_dataframe` trasformazione richiede ora che il frame di dati abbia nomi di colonna tipizzato di stringa o byte.
  + Correzione di un `fill_nulls` bug nella trasformazione, in cui i valori non venivano compilati correttamente se la colonna non era presente.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK per Python v1.0.18

 + **Modifiche**
   + Il pacchetto azureml-tensorboard sostituisce azureml-contrib-tensorboard.
   + Con questa versione, è possibile configurare un account utente nel cluster di calcolo gestito (amlcompute), durante la creazione. Questa operazione può essere eseguita passando queste proprietà nella configurazione di provisioning. Ulteriori dettagli sono disponibili nella [documentazione di riferimento dell'SDK.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning Data Prep SDK v1.0.17

+ **Nuove funzionalità**
  + Ora supporta l'aggiunta di due colonne numeriche per generare una colonna risultante utilizzando il linguaggio delle espressioni.

+ **Correzioni di bug e miglioramenti**
  + È stata migliorata la documentazione e il controllo dei parametri per random_split.

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning Data Prep SDK v1.0.16

+ **Correzione di bug**
  + È stato corretto un problema di autenticazione dell'entità servizio causato da una modifica dell'API.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK per Python v1.0.17

+ **Nuove funzionalità**

  + Azure Machine Learning ora fornisce supporto di prima classe per popolare DNN framework Chainer. Utilizzando [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) gli utenti di classe è possibile eseguire facilmente il training e distribuire i modelli Chainer.Using class users can easily train and deploy Chainer models.
    + Scopri come eseguire corsi [di formazione distribuiti con ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + Informazioni su come [eseguire l'ottimizzazione degli iperparametri con Chainer con HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Pipeline di Azure Machine Learning ha aggiunto la possibilità di attivare un'esecuzione della pipeline in base alle modifiche dell'archivio dati. Il [blocco appunti](https://aka.ms/pl-schedule) della pianificazione della pipeline viene aggiornato per mostrare questa funzionalità.

+ **Correzioni di bug e miglioramenti**
  + È stato aggiunto il supporto nelle pipeline di Azure Machine Learning per l'impostazione della proprietà source_directory_data_store su un archivio dati desiderato(ad esempio un archivio BLOB) in [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) fornite a [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py). Per impostazione predefinita, i passaggi usano l'archivio file di Azure come archivio dati di backup, che potrebbe verificarsi problemi di limitazione quando viene eseguito contemporaneamente un numero elevato di passaggi.

### <a name="azure-portal"></a>Portale di Azure

+ **Nuove funzionalità**
  + Nuova esperienza nell'editor di tabelle per il trascinamento della selezione per i report. Gli utenti possono trascinare una colonna dal pozzo all'area della tabella in cui verrà visualizzata un'anteprima della tabella. Le colonne possono essere riorganizzate.
  + Visualizzatore nuovi file di registro
  + Collegamenti a esecuzioni, calcolo, modelli, immagini e distribuzioni della sperimentazione dalla scheda Attività

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure Machine Learning Data Prep SDK v1.0.15

+ **Nuove funzionalità**
  + Data Prep supporta ora la scrittura di flussi di file da un flusso di dati. Fornisce anche la possibilità di modificare i nomi dei flussi di file per creare nuovi nomi di file.
    + Guida alle procedure: [Utilizzo del blocco appunti di File Streams](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Correzioni di bug e miglioramenti**
  + Miglioramento delle prestazioni di t-Digest su set di dati di grandi dimensioni.
  + Data Prep now supports reading data from a DataPath.
  + Una codifica a caldo ora funziona su colonne booleane e numeriche.
  + Altre correzioni di bug varie.

## <a name="2019-02-11"></a>11 febbraio 2019

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Azure Machine Learning SDK per Python v1.0.15

+ **Nuove funzionalità**
  + Le pipeline di Azure Machine Learning hanno aggiunto AzureBatchStep ([blocco appunti),](https://aka.ms/pl-azbatch)HyperDriveStep (notebook) e funzionalità di pianificazione basate sul tempo ([notebook](https://aka.ms/pl-schedule)).
  +  È stato effettuato l'aggiornamento di DataTranferStep per garantire il funzionamento con il server SQL Azure e il database di Azure per PostgreSQL ([notebook](https://aka.ms/pl-data-trans)).

+ **Modifiche**
  + `PublishedPipeline.get_published_pipeline` deprecato a favore di `PublishedPipeline.get`.
  + `Schedule.get_schedule` deprecato a favore di `Schedule.get`.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning Data Prep SDK v1.0.12

+ **Nuove funzionalità**
  + Data Prep ora supporta la lettura da un database SQL di Azure con l'archivio dati.

+ **Modifiche**
  + È stata migliorata la prestazione di memoria di determinate operazioni su dati di grandi dimensioni.
  + Per la specifica di `read_pandas_dataframe()` è ora necessario `temp_folder`.
  + La proprietà `name` in `ColumnProfile` è deprecata. Usare `column_name`.

## <a name="2019-01-28"></a>28 gennaio 2019

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning SDK per Python v1.0.10

+ **Modifiche**:
  + Azure ML SDK non dispone più di pacchetti azure-cli come dipendenza. In particolare, le dipendenze di azure-cli-core e azure-cli-profilo sono state rimosse da azureml-core. Le modifiche con un impatto per l'utente sono:
      + Se si esegue "az login" e quindi si usa azureml-sdk, l'SDK eseguirà il log del codice del browser o del dispositivo in un'altra volta. Non utilizza nessuno stato di credenziali creato da "az login".
    + Per l'autenticazione di Azure CLI, ad esempio usando "az login", usare la classe _azureml.core.authentication.AzureCliAuthentication_. Per l'autenticazione di Azure CLI, eseguire _i parametri di inizializzazione del programma per installare azure-cli_ nell'ambiente di Python in cui è stato installato azureml-sdk.
    + Se si esegue "az login" usando un'entità di servizio per l'automazione, è consigliabile usare la classe _azureml.core.authentication.ServicePrincipalAuthentication_, in quanto azure ml-sdk non userà credenziali di stato create dal comando Azure CLI.

+ **Correzioni di bug**: Questa versione contiene principalmente correzioni di bug minori

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning Data Prep SDK v1.0.8

+ **Correzioni**
  + Migliorate le prestazioni di recupero dei profili dati.
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

+ **Correzioni di bug**: Questa versione contiene principalmente correzioni di bug minori

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Azure Machine Learning Data Prep SDK v1.0.7

+ **Nuove funzionalità**
  + Miglioramenti dell'archivio dati (documentati in [Manuale delle procedure dell'archivio dati](https://aka.ms/aml-data-prep-datastore-nb))
    + Maggiore capacità di lettura e scrittura di Azure File Share e archivi dati ADLS nel ridimensionamento verticale.
    + Quando si usano archivi dati, la preparazione dei dati ora supporta l'utilizzo dell'autenticazione principale del servizio invece dell'autenticazione interattiva.
    + Aggiunto supporto per gli URL wasb e wasbs.

## <a name="2019-01-09"></a>09-01-2019

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Azure Machine Learning Data Prep SDK v1.0.6

+ **Correzioni**
  + Correzione di un bug relativo alla lettura da contenitori BLOB di Azure leggibili pubblicamente in Spark

## <a name="2018-12-20"></a>20-12-2018

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning SDK per Python v1.0.6
+ **Correzioni di bug**: Questa versione contiene principalmente correzioni di bug minori

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning Data Prep SDK v1.0.4

+ **Nuove funzionalità**
  + La funzione `to_bool` consente ora la conversione di valori non corrispondenti in valori di errore. Questo è il nuovo comportamento predefinito per `to_bool` e `set_column_types` in caso di mancata corrispondenza. Il comportamento predefinito precedente consisteva nel convertire i valori non corrispondenti in valori False.
  + Quando si chiama `to_pandas_dataframe`, è disponibile una nuova opzione per interpretare i valori Null o mancanti nelle colonne numeriche come NaN.
  + È stata aggiunta la possibilità controllare il tipo restituito di alcune espressioni per garantire la coerenza dei tipi e la risposta anticipata agli errori.
  + È ora possibile chiamare `parse_json` per analizzare i valori in una colonna come oggetti JSON ed espanderli in più colonne.

+ **Correzioni**
  + È stato corretto un bug che causava l'arresto anomalo di `set_column_types` in Python 3.5.2.
  + È stato corretto un bug che causava l'arresto anomalo del sistema durante la connessione all'archivio dati con un'immagine di Azure Machine Learning.

+ **Aggiornamenti**
  * [Notebook di esempio](https://aka.ms/aml-data-prep-notebooks) per esercitazioni introduttive, case study e guide pratiche.

## <a name="2018-12-04-general-availability"></a>2018-12-04: Disponibilità generale

Azure Machine Learning è ora disponibile in genere.

### <a name="azure-machine-learning-compute"></a>Ambiente di calcolo di Azure Machine Learning
Con questa versione, Microsoft presenta una nuova esperienza di calcolo gestita tramite l'[ambiente di calcolo di Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute). Questa destinazione di calcolo sostituisce l'ambiente di calcolo di Azure Batch per intelligenza artificiale per Azure Machine Learning.

La nuova destinazione di calcolo presenta queste caratteristiche:
+ Viene utilizzato per il training del modello e l'inferenza di batch/punteggio
+ È un ambiente di calcolo a uno o più nodi
+ Esegue la gestione del cluster e la pianificazione dei processi per l'utente
+ Viene ridimensionata automaticamente per impostazione predefinita
+ Include il supporto per le risorse di CPU e GPU
+ Consente l'uso di macchine virtuali con priorità bassa per un risparmio sui costi

L'ambiente di calcolo di Azure Machine Learning può essere creato in Python, tramite il portale di Azure o l'interfaccia della riga di comando. Deve essere creato nella zona dell'area di lavoro e non può essere collegato ad altre aree di lavoro. Questa destinazione di calcolo usa un contenitore Docker per l'esecuzione e crea pacchetti delle dipendenze per replicare lo stesso ambiente in tutti i nodi.

> [!Warning]
> È consigliabile creare una nuova area di lavoro per usare l'ambiente di calcolo di Azure Machine Learning. C'è una possibilità remota che gli utenti che tentano di creare l'ambiente di calcolo di Azure Machine Learning da un'area di lavoro esistente visualizzino un errore. L'ambiente di calcolo esistente nell'area di lavoro deve continuare a lavorare senza problemi.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Azure Machine Learning SDK per Python v1.0.2
+ **Modifiche che causano un'interruzione**
  + Con questa versione, si rimuoverà il supporto per la creazione di una macchina virtuale da Azure Machine Learning. È comunque possibile associare una macchina virtuale del cloud esistente o un server remoto in locale.
  + Verrà rimosso anche il supporto per BatchAI, che ora dovrà essere supportato tramite l'ambiente di calcolo di Azure Machine Learning.

+ **Nuova**
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
+ **Modifiche che causano un'interruzione**
  * `SummaryFunction.N` è stato rinominato `SummaryFunction.Count`.

+ **Correzioni**
  * Utilizzare il token di esecuzione AML più recente durante la lettura e la scrittura negli archivi dati in esecuzione remota. Precedentemente, se il Token di esecuzione di Azure Machine Learning viene aggiornato in Python, il runtime di preparazione dei dati non viene aggiornato con il token di esecuzione di Azure Machine Learning aggiornato.
  * Altri messaggi di errore più chiari
  * Non si verifica più l'arresto anomalo di to_spark_dataframe() quando Spark usa la serializzazione `Kryo`
  * L'ispettore del conteggio del valore può ora mostrare più di 1000 valori univoci
  * Suddivisione casuale non ha più esito negativo se il flusso di dati originale non ha un nome

+ **Ulteriori informazioni**
  * [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Documentazione e notebook
+ Pipeline di Machine Learning
  + Notebook nuovi e aggiornati per iniziare con le pipeline, la definizione dell'ambito di batch e gli esempi di trasferimenti di stile: https://aka.ms/aml-pipeline-notebooks
  + Informazioni su come [creare la prima pipeline](how-to-create-your-first-pipeline.md)
  + Informazioni su come [eseguire previsioni in batch utilizzando le pipeline](how-to-use-parallel-run-step.md)
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

+ **Modifiche che causano un'interruzione**
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

+ **Correzioni**
   * È stata corretta la conversione da tipo di dati long a double
   * È stata corretta un'asserzione dopo qualsiasi aggiunta di colonna
   * È stato corretto un problema di Raggruppamento fuzzy che impediva il rilevamento dei gruppi in alcuni casi
   * È stata corretta la funzione di ordinamento per rispettare l'ordinamento a più colonne
   * Le espressioni and/or sono state corrette in modo che siano simili a come vengono gestite da `pandas`
   * È stata corretta la lettura dal percorso dbfs
   * I messaggi di errore sono stati resi più comprensibili
   * Ora non ha più esito negativo durante la lettura su destinazione di calcolo remoto utilizzando un token AML
   * Non si verificano più errori in Data Science Virtual Machine Linux
   * Non si verifica più un arresto anomalo quando predicati di stringa contengono valori non di tipo stringa
   * Gli errori di asserzione in caso di errore del flusso di dati vengono ora gestiti correttamente
   * Le posizioni di archiviazione montate in dbutils sono ora supportate in Azure Databricks

## <a name="2018-11-05"></a>05-11-2018

### <a name="azure-portal"></a>Portale di Azure
Il portale di Azure per Azure Machine Learning include gli aggiornamenti seguenti:The Azure portal for Azure Machine Learning has the following updates:
  * Una nuova scheda **Pipeline** per le pipeline pubblicate.
  * Aggiunta del supporto per il collegamento di un cluster HDInsight esistente come destinazione di calcolo.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Azure Machine Learning SDK per Python v0.1.74

+ **Modifiche che causano un'interruzione**
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
  * Quando si utilizza Sostituisci, lasciando vuoto "trova" non avrà più esito negativo

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

+ **Modifiche che causano un'interruzione**
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
  * Risolti :::no-loc text="ensembling"::: i problemi di iterazione.
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

Una nuova versione aggiornata di Azure Machine Learning: altre informazioni su questa versione:A new, refreshed release of Azure Machine Learning: More about this release:https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>Passaggi successivi

Leggere la panoramica di [Azure Machine Learning](overview-what-is-azure-ml.md).
