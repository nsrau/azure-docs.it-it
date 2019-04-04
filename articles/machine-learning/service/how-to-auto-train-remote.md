---
title: Destinazioni di calcolo remote per Machine Learning automatizzato
titleSuffix: Azure Machine Learning service
description: Informazioni su come creare modelli usando un processo di Machine Learning automatizzato in una destinazione di calcolo remota DSVM (Data Science Virtual Machine) con il servizio Azure Machine Learning
services: machine-learning
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 6f2d71abeacee531b21a8276f621367dd39a39d9
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58891668"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Eseguire il training di modelli di apprendimento automatico nel cloud

In Azure Machine Learning è possibile eseguire il training di un modello su diversi tipi di risorse di calcolo gestite. La destinazione di calcolo può essere un computer locale o un computer nel cloud.

È possibile aumentare facilmente le prestazioni o l'estensione dell'esperimento di Machine Learning aggiungendo altre destinazioni di calcolo. Le opzioni di destinazione di calcolo disponibili includono una Data Science Virtual Machine (DSVM) basata su Ubuntu e l'ambiente di calcolo di Azure Machine Learning. La DSVM è un'immagine di VM personalizzata sul cloud di Microsoft Azure creata in modo specifico per data science. Include molti data science comuni e altri strumenti preinstallati e preconfigurati.  

Questo articolo descrive come compilare un modello usando una ML automatizzata in DSVM.

## <a name="how-does-remote-differ-from-local"></a>In che modo il remote e il locale sono diversi?

L'esercitazione "[Eseguire il training di un modello di classificazione con machine learning automatizzata](tutorial-auto-train-models.md)" illustra come usare un computer locale per eseguire il training del modello con una machine learning automatizzata.  Il flusso di lavoro durante il training in locale si applica anche nelle destinazioni remoto. Tuttavia, con il calcolo remoto, le iterazioni dell'esperimento ML automatizzate vengono eseguite in modo asincrono. Questa funzionalità consente di annullare un'iterazione specifica, controllare lo stato dell'esecuzione o continuare a lavorare su altre celle nel notebook di Jupyter. Per eseguire il training in modalità remota, è innanzitutto necessario creare una destinazione di calcolo remoto, ad esempio una DSVM di Azure.  È quindi possibile configurare la risorsa remota e inviare il codice.

Questo articolo illustra i passaggi aggiuntivi necessari per eseguire un esperimento di ML automatizzato in una DSVM remota.  L'oggetto dell'area di lavoro, `ws`, creato nell'esercitazione viene usato in tutto il codice qui.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Crea risorsa

Creare la DSVM nell'area di lavoro (`ws`) se non esiste già. Se la DSVM è stato creata in precedenza, questo codice ignora il processo di creazione e carica i dettagli di risorse esistente nell'oggetto `dsvm_compute`.  

**Tempo stimato**: la creazione della macchina virtuale richiede circa 5 minuti.

```python
from azureml.core.compute import DsvmCompute

dsvm_name = 'mydsvm' #Name your DSVM
try:
    dsvm_compute = DsvmCompute(ws, dsvm_name)
    print('found existing dsvm.')
except:
    print('creating new dsvm.')
    # Below is using a VM of SKU Standard_D2_v2 which is 2 core machine. You can check Azure virtual machines documentation for additional SKUs of VMs.
    dsvm_config = DsvmCompute.provisioning_configuration(vm_size = "Standard_D2_v2")
    dsvm_compute = DsvmCompute.create(ws, name = dsvm_name, provisioning_configuration = dsvm_config)
    dsvm_compute.wait_for_completion(show_output = True)
```

È ora possibile usare l'oggetto `dsvm_compute` come destinazione di calcolo remota.

Restrizioni relative ai nomi della Data Science Virtual Machine includono:
+ Non deve contenere più di 64 caratteri.  
+ Non può contenere i caratteri seguenti: `\` ~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \\\\ | ; : \' \\" , < > / ?.`

>[!Warning]
>Se la creazione ha esito negativo con un messaggio sull'idoneità all'acquisto di Marketplace:
>    1. Accedere al [portale di Azure](https://portal.azure.com)
>    1. Avviare la creazione di una DSVM 
>    1. Selezionare "da creare a livello di codice" per abilitare la creazione a livello di codice
>    1. Uscire senza creare effettivamente la macchina virtuale
>    1. Eseguire nuovamente il codice di creazione

Questo codice non crea un nome utente o password per la DSVM su cui viene eseguito il provisioning. Se si vuole connettersi direttamente alla macchina virtuale, passare al [portale di Azure](https://portal.azure.com) per creare le credenziali.  

### <a name="attach-existing-linux-dsvm"></a>Collegare una DSVM Linux esistente

Come destinazione di calcolo è anche possibile collegare una DSVM Linux esistente. Questo esempio usa una DSVM esistente, ma non crea una nuova risorsa.

> [!NOTE]
>
> Il codice seguente usa il [RemoteCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.remote.remotecompute?view=azure-ml-py) classe collegare una macchina virtuale esistente come destinazione di calcolo di destinazione.
> Il [DsvmCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.dsvmcompute?view=azure-ml-py) classe sarà deprecata nelle versioni future a favore di questo modello di progettazione.

Eseguire il codice seguente per creare la destinazione di calcolo da una DSVM Linux già esistente.

```python
from azureml.core.compute import ComputeTarget, RemoteCompute 

attach_config = RemoteCompute.attach_configuration(username='<username>',
                                                   address='<ip_address_or_fqdn>',
                                                   ssh_port=22,
                                                   private_key_file='./.ssh/id_rsa')
compute_target = ComputeTarget.attach(workspace=ws,
                                      name='attached-vm',
                                      attach_configuration=attach_config)

compute_target.wait_for_completion(show_output=True)
```

È ora possibile usare l'oggetto `compute_target` come destinazione di calcolo remota.

## <a name="access-data-using-getdata-file"></a>Accedere ai dati tramite il file get_data

Fornire l'accesso alla risorsa remota per i dati di training. Per esperimenti automatizzati di apprendimento automatico in esecuzione su un calcolo remoto, i dati devono essere recuperati tramite una funzione `get_data()`.  

Per fornire l'accesso, è necessario:
+ Creare un file get_data.py contenente una funzione `get_data()` 
+ Inserire tale file in una directory accessibile come percorso assoluto 

È possibile incapsulare il codice per leggere i dati da un archivio blob o un disco locale nel file get_data.py. Nell'esempio di codice seguente, i dati provengono dal pacchetto sklearn.

>[!Warning]
>Se si usa calcolo remoto, è quindi necessario usare `get_data()` in cui vengono eseguite le trasformazioni di dati. Se è necessario installare librerie aggiuntive per le trasformazioni di dati come parte di get_data(), esistono passaggi aggiuntivi da seguire. Fare riferimento al [notebook di esempio auto-ml-dataprep](https://aka.ms/aml-auto-ml-data-prep ) per informazioni dettagliate.


```python
# Create a project_folder if it doesn't exist
if not os.path.exists(project_folder):
    os.makedirs(project_folder)

#Write the get_data file.
%%writefile $project_folder/get_data.py

from sklearn import datasets
from scipy import sparse
import numpy as np

def get_data():
    
    digits = datasets.load_digits()
    X_digits = digits.data[10:,:]
    y_digits = digits.target[10:]

    return { "X" : X_digits, "y" : y_digits }
```

## <a name="configure-experiment"></a>Configurare l'esperimento

Specificare le impostazioni per `AutoMLConfig`.  (Vedere un [elenco completo di parametri](how-to-configure-auto-train.md#configure-experiment) e i relativi valori possibili.)

Nelle impostazioni, `run_configuration` è impostato sull'oggetto `run_config`, che contiene le impostazioni e la configurazione per la DSVM.  

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "iteration_timeout_minutes": 10,
    "iterations": 20,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "preprocess": False,
    "max_concurrent_iterations": 10,
    "verbosity": logging.INFO
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target = dsvm_compute,
                             data_script=project_folder + "/get_data.py",
                             **automl_settings,
                            )
```

### <a name="enable-model-explanations"></a>Abilitare le spiegazioni del modello

Impostare il parametro facoltativo `model_explainability` nel costruttore `AutoMLConfig`. Per usare la funzionalità di spiegabilità del modello è inoltre necessario passare un oggetto frame di dati di convalida come parametro `X_valid`.

```python
automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target = dsvm_compute,
                             data_script=project_folder + "/get_data.py",
                             **automl_settings,
                             model_explainability=True,
                             X_valid = X_test
                            )
```

## <a name="submit-training-experiment"></a>Inviare l'esperimento di training

A questo punto inviare la configurazione per selezionare automaticamente l'algoritmo, i parametri hyper e il training del modello.

```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

L'output sarà simile all'esempio seguente:

    Running on remote compute: mydsvmParent Run ID: AutoML_015ffe76-c331-406d-9bfd-0fd42d8ab7f6
    ***********************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE:  A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ***********************************************************************************************
    
     ITERATION     PIPELINE                               DURATION                METRIC      BEST
             2      Standardize SGD classifier            0:02:36                  0.954     0.954
             7      Normalizer DT                         0:02:22                  0.161     0.954
             0      Scale MaxAbs 1 extra trees            0:02:45                  0.936     0.954
             4      Robust Scaler SGD classifier          0:02:24                  0.867     0.954
             1      Normalizer kNN                        0:02:44                  0.984     0.984
             9      Normalizer extra trees                0:03:15                  0.834     0.984
             5      Robust Scaler DT                      0:02:18                  0.736     0.984
             8      Standardize kNN                       0:02:05                  0.981     0.984
             6      Standardize SVM                       0:02:18                  0.984     0.984
            10      Scale MaxAbs 1 DT                     0:02:18                  0.077     0.984
            11      Standardize SGD classifier            0:02:24                  0.863     0.984
             3      Standardize gradient boosting         0:03:03                  0.971     0.984
            12      Robust Scaler logistic regression     0:02:32                  0.955     0.984
            14      Scale MaxAbs 1 SVM                    0:02:15                  0.989     0.989
            13      Scale MaxAbs 1 gradient boosting      0:02:15                  0.971     0.989
            15      Robust Scaler kNN                     0:02:28                  0.904     0.989
            17      Standardize kNN                       0:02:22                  0.974     0.989
            16      Scale 0/1 gradient boosting           0:02:18                  0.968     0.989
            18      Scale 0/1 extra trees                 0:02:18                  0.828     0.989
            19      Robust Scaler kNN                     0:02:32                  0.983     0.989


## <a name="explore-results"></a>Esplorare i risultati

È possibile usare lo stesso widget di Jupyter di quello [dell'esercitazione del training](tutorial-auto-train-models.md#explore-the-results) per visualizzare un grafico e tabella dei risultati.

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```
Ecco un'immagine statica del widget.  Nel notebook, è possibile fare clic su una qualsiasi riga della tabella per visualizzare le proprietà di esecuzione e i log di output per quell'esecuzione.   È possibile usare l'elenco a discesa sopra il grafico per visualizzare un grafico di ogni metrica disponibile per ogni iterazione.

![tabella widget](./media/how-to-auto-train-remote/table.png)
![tracciato widget](./media/how-to-auto-train-remote/plot.png)

Il widget visualizza un URL da usare per visualizzare ed esplorare i singoli dettagli dell'esecuzione.
 
### <a name="view-logs"></a>Visualizzare i log

I log si trovano nel percorso `/tmp/azureml_run/{iterationid}/azureml-logs` della DSVM.

## <a name="best-model-explanation"></a>Spiegazione del modello migliore

Il recupero dei dati di spiegazione dei modelli consente di visualizzare informazioni dettagliate sui modelli per rendere più trasparente il processo in esecuzione nel back-end. In questo esempio si eseguono le spiegazioni del modello solo per il modello migliore. L'esecuzione delle spiegazioni per tutti i modelli della pipeline richiede una notevole quantità di tempo. Le informazioni di spiegazione del modello includono:

* shap_values: le informazioni di spiegazione generate da shap lib.
* expected_values: il valore previsto del modello applicato al set di dati X_train.
* overall_summary: i valori di importanza delle caratteristiche a livello di modello disposti in ordine decrescente.
* overall_imp: i nomi delle caratteristiche disposti nello stesso ordine usato in overall_summary.
* per_class_summary: i valori di importanza delle caratteristiche a livello di classe disposti in ordine decrescente. Disponibile solo in caso di classificazione.
* per_class_imp: i nomi delle caratteristiche disposti nello stesso ordine usato in per_class_summary. Disponibile solo in caso di classificazione.

Usare il codice seguente per selezionare la pipeline migliore dalle iterazioni. Il metodo `get_output` restituisce l'esecuzione migliore e il modello più adatto per l'ultima chiamata alla funzione di fit.

```python
best_run, fitted_model = remote_run.get_output()
```

Importare la funzione `retrieve_model_explanation` ed eseguirla sul modello migliore.

```python
from azureml.train.automl.automlexplainer import retrieve_model_explanation

shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
    retrieve_model_explanation(best_run)
```

Stampare i risultati per le variabili di spiegazione di `best_run` da visualizzare.

```python
print(overall_summary)
print(overall_imp)
print(per_class_summary)
print(per_class_imp)
```

La stampa delle variabili di riepilogo della spiegazione `best_run` ha come risultato l'output seguente.

![Output della console di spiegabilità del modello](./media/how-to-auto-train-remote/expl-print.png)

È anche possibile visualizzare l'importanza delle caratteristiche tramite l'interfaccia utente del widget e l'interfaccia utente Web nel portale di Azure all'interno dell'area di lavoro.

![Interfaccia utente di spiegabilità del modello](./media/how-to-auto-train-remote/model-exp.png)

## <a name="example"></a>Esempio

Il notebook [how-to-use-azureml/automated-machine-learning/remote-execution/auto-ml-remote-execution.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-execution/auto-ml-remote-execution.ipynb) illustra i concetti presentati in questo articolo. 

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [How to configure settings for automatic training](how-to-configure-auto-train.md) (Come configurare le impostazioni per il training automatico).
