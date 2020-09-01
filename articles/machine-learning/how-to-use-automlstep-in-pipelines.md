---
title: Usare le pipeline automatiche in ML
titleSuffix: Azure Machine Learning
description: AutoMLStep consente di usare Machine Learning automatizzato nelle pipeline.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
manager: cgronlun
ms.date: 08/26/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 0daa094a6d804cd8a40c4ba76b696e3c9b580f8a
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230348"
---
# <a name="use-automated-ml-in-an-azure-machine-learning-pipeline-in-python"></a>Usare Machine Learning automatiche in una pipeline Azure Machine Learning in Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

La funzionalità di Machine Learning automatizzata di Azure Machine Learning consente di individuare i modelli ad alte prestazioni senza riimplementare ogni possibile approccio. Insieme a pipeline di Azure Machine Learning, è possibile creare flussi di lavoro distribuibili che consentono di individuare rapidamente l'algoritmo più adatto ai propri dati. Questo articolo illustra come unire in modo efficiente un passaggio di preparazione dei dati a un passaggio di Machine Learning automatico. Machine Learning Machine Learning consente di individuare rapidamente l'algoritmo più adatto ai propri dati, mentre è in corso la fase di MLOps e la messa in funzione del ciclo di vita del modello con le pipeline.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* Un'area di lavoro di Azure Machine Learning. Vedere [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).  

* Familiarità di base con le infrastrutture e l'SDK di machine learning e di [Machine Learning](concept-ml-pipelines.md) [automatizzati](concept-automated-ml.md) di Azure.

## <a name="review-automated-mls-central-classes"></a>Esaminare le classi centrali di Machine Learning automatiche

L'elaborazione automatica di ML in una pipeline è rappresentata da un `AutoMLStep` oggetto. La classe `AutoMLStep` è una sottoclasse di `PipelineStep`. Un grafico di `PipelineStep` oggetti definisce un oggetto `Pipeline` .

Sono disponibili diverse sottoclassi di `PipelineStep` . Oltre a, in `AutoMLStep` questo articolo verrà illustrato un `PythonScriptStep` per la preparazione dei dati e un altro per la registrazione del modello.

Il modo migliore per spostare inizialmente i dati _in_ una pipeline di ml è con `Dataset` gli oggetti. Per spostare i dati _tra_ i passaggi, la modalità preferita è con `PipelineData` gli oggetti. Per poter essere utilizzato con `AutoMLStep` , l' `PipelineData` oggetto deve essere trasformato in un `PipelineOutputTabularDataset` oggetto. Per altre informazioni, vedere [dati di input e di output dalle pipeline di ml](how-to-move-data-in-out-of-pipelines.md).


> [!TIP]
> Un'esperienza migliorata per il passaggio di dati temporanei tra i passaggi della pipeline è disponibile nelle classi di anteprima pubblica,  [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py) e [`OutputTabularDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.output_dataset_config.outputtabulardatasetconfig?view=azure-ml-py) .  Queste classi sono funzionalità di anteprima [sperimentale](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py#stable-vs-experimental) e possono cambiare in qualsiasi momento.

`AutoMLStep`Viene configurato tramite un `AutoMLConfig` oggetto. `AutoMLConfig` è una classe flessibile, come descritto in [configurare esperimenti di Machine Learning automatici in Python](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#configure-your-experiment-settings). 

Un oggetto `Pipeline` viene eseguito in un oggetto `Experiment` . La pipeline `Run` ha, per ogni passaggio, un elemento figlio `StepRun` . Gli output di Machine Learning automatici `StepRun` sono le metriche di training e il modello con le prestazioni più elevate.

Per rendere le cose concrete, questo articolo crea una semplice pipeline per un'attività di classificazione. L'attività sta stimando la sopravvivenza titanica, ma non verranno illustrati i dati o l'attività tranne che per il passaggio.

## <a name="get-started"></a>Introduzione

### <a name="retrieve-initial-dataset"></a>Recupera set di dati iniziale

Spesso un flusso di lavoro ML inizia con dati di base preesistenti. Si tratta di uno scenario valido per un set di dati registrato. I set di impostazioni sono visibili nell'area di lavoro, supportano il controllo delle versioni e possono essere esplorati in modo interattivo. Esistono diversi modi per creare e popolare un set di dati, come illustrato in [creare set](how-to-create-register-datasets.md)di dati Azure Machine Learning. Poiché verrà usato Python SDK per creare la pipeline, usare l'SDK per scaricare i dati di base e registrarli con il nome "titanic_ds".

```python
from azureml.core import Workspace, Dataset

ws = Workspace.from_config()
if not 'titanic_ds' in ws.datasets.keys() :
    # create a TabularDataset from Titanic training data
    web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
                 'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
    titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

    titanic_ds.register(workspace = ws,
                                     name = 'titanic_ds',
                                     description = 'Titanic baseline data',
                                     create_new_version = True)

titanic_ds = Dataset.get_by_name(ws, 'titanic_ds')
```

Il Code First accede all'area di lavoro Azure Machine Learning definita in **config.json** . per una spiegazione, vedere [esercitazione: Introduzione alla creazione del primo esperimento ml con Python SDK](tutorial-1st-experiment-sdk-setup.md). Se non è già presente un set di dati denominato `'titanic_ds'` registered, ne crea uno. Il codice Scarica i dati CSV dal Web, li usa per creare un'istanza di `TabularDataset` e quindi registra il set di dati con l'area di lavoro. Infine, la funzione `Dataset.get_by_name()` assegna `Dataset` a `titanic_ds` . 

### <a name="configure-your-storage-and-compute-target"></a>Configurare la destinazione di calcolo e archiviazione

Le risorse aggiuntive che saranno necessarie per la pipeline sono archiviazione e, in genere, Azure Machine Learning risorse di calcolo. 

```python
from azureml.core import Datastore
from azureml.core.compute import AmlCompute, ComputeTarget

datastore = ws.get_default_datastore()

compute_name = 'cpu-cluster'
if not compute_name in ws.compute_targets :
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                min_nodes=0,
                                                                max_nodes=1)
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)

    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # Show the result
    print(compute_target.get_status().serialize())

compute_target = ws.compute_targets[compute_name]
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

I dati intermedi tra la preparazione dei dati e il passaggio di Machine Learning automatico possono essere archiviati nell'archivio dati predefinito dell'area di lavoro, pertanto non è necessario eseguire più di una chiamata `get_default_datastore()` per l' `Workspace` oggetto. 

Successivamente, il codice controlla se la destinazione di calcolo AML `'cpu-cluster'` esiste già. In caso contrario, si specifica che si vuole una piccola destinazione di calcolo basata su CPU. Se si prevede di usare le funzionalità di apprendimento avanzato di Machine Learning (ad esempio, conteggi di testo con supporto per DNN), è consigliabile scegliere un calcolo con supporto GPU sicuro, come descritto in [dimensioni delle macchine virtuali ottimizzate per GPU](https://docs.microsoft.com/azure/virtual-machines/sizes-gpu). 

Il codice si blocca fino a quando non viene eseguito il provisioning della destinazione e quindi stampa alcuni dettagli della destinazione di calcolo appena creata. Infine, la destinazione di calcolo denominata viene recuperata dall'area di lavoro e assegnata a `compute_target` . 

### <a name="configure-the-training-run"></a>Configurare l'esecuzione del training

Il passaggio successivo consiste nel verificare che l'esecuzione del training remoto includa tutte le dipendenze necessarie per i passaggi di training. Le dipendenze e il contesto di runtime vengono impostati tramite la creazione e la configurazione di un `RunConfiguration` oggetto. 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import Environment 

aml_run_config = RunConfiguration()
# Use just-specified compute target ("cpu-cluster")
aml_run_config.target = compute_target

USE_CURATED_ENV = True
if USE_CURATED_ENV :
    curated_environment = Environment.get(workspace=ws, name="AzureML-Tutorial")
    aml_run_config.environment = curated_environment
else:
    aml_run_config.environment.python.user_managed_dependencies = False
    
    # Add some packages relied on by data prep step
    aml_run_config.environment.python.conda_dependencies = CondaDependencies.create(
        conda_packages=['pandas','scikit-learn'], 
        pip_packages=['azureml-sdk[automl,explain]', 'azureml-dataprep[fuse,pandas]'], 
        pin_sdk_version=False)
```

Il codice precedente mostra due opzioni per la gestione delle dipendenze. Come illustrato, con `USE_CURATED_ENV = True` la configurazione si basa su un ambiente curato. Gli ambienti curati sono "precotti" con librerie interdipendenti comuni e possono essere notevolmente più veloci da portare online. Gli ambienti curati hanno immagini Docker predefinite in [Microsoft container Registry](https://hub.docker.com/publishers/microsoftowner). Il percorso adottato se si modifica `USE_CURATED_ENV` per `False` visualizzare il modello per l'impostazione esplicita delle dipendenze. In questo scenario verrà creata e registrata una nuova immagine Docker personalizzata in un Container Registry di Azure all'interno del gruppo di risorse. vedere [Introduzione ai registri di contenitori Docker privati in Azure](https://docs.microsoft.com/azure/container-registry/container-registry-intro). La compilazione e la registrazione di questa immagine possono richiedere alcuni minuti. 

## <a name="prepare-data-for-automated-machine-learning"></a>Preparare i dati per Machine Learning automatizzato

### <a name="write-the-data-preparation-code"></a>Scrivere il codice di preparazione dei dati

Il set di dati di base Titanic è costituito da dati numerici e di testo misti, con alcuni valori mancanti. Per preparare il sistema per l'apprendimento automatico automatico, il passaggio della pipeline di preparazione dei dati sarà:

- Riempire i dati mancanti con dati casuali o una categoria corrispondente a "Unknown"
- Trasformare i dati categorici in numeri interi
- Rilasciare le colonne che non si intende usare
- Suddividere i dati in set di training e di testing
- Scrivere i dati trasformati in
    - `PipelineData` percorsi di output

```python
%%writefile dataprep.py
from azureml.core import Run

import pandas as pd 
import numpy as np 
import pyarrow as pa
import pyarrow.parquet as pq
import argparse

RANDOM_SEED=42

def prepare_age(df):
    # Fill in missing Age values from distribution of present Age values 
    mean = df["Age"].mean()
    std = df["Age"].std()
    is_null = df["Age"].isnull().sum()
    # compute enough (== is_null().sum()) random numbers between the mean, std
    rand_age = np.random.randint(mean - std, mean + std, size = is_null)
    # fill NaN values in Age column with random values generated
    age_slice = df["Age"].copy()
    age_slice[np.isnan(age_slice)] = rand_age
    df["Age"] = age_slice
    df["Age"] = df["Age"].astype(int)
    
    # Quantize age into 5 classes
    df['Age_Group'] = pd.qcut(df['Age'],5, labels=False)
    df.drop(['Age'], axis=1, inplace=True)
    return df

def prepare_fare(df):
    df['Fare'].fillna(0, inplace=True)
    df['Fare_Group'] = pd.qcut(df['Fare'],5,labels=False)
    df.drop(['Fare'], axis=1, inplace=True)
    return df 

def prepare_genders(df):
    genders = {"male": 0, "female": 1, "unknown": 2}
    df['Sex'] = df['Sex'].map(genders)
    df['Sex'].fillna(2, inplace=True)
    df['Sex'] = df['Sex'].astype(int)
    return df

def prepare_embarked(df):
    df['Embarked'].replace('', 'U', inplace=True)
    df['Embarked'].fillna('U', inplace=True)
    ports = {"S": 0, "C": 1, "Q": 2, "U": 3}
    df['Embarked'] = df['Embarked'].map(ports)
    return df
    
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()
    
titanic_ds = Run.get_context().input_datasets['titanic_ds']
df = titanic_ds.to_pandas_dataframe().drop(['PassengerId', 'Name', 'Ticket', 'Cabin'], axis=1)
df = prepare_embarked(prepare_genders(prepare_fare(prepare_age(df))))

os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
pq.write_table(pa.Table.from_pandas(df), args.output_path)

print(f"Wrote test to {args.output_path} and train to {args.output_path}")
```

Il frammento di codice precedente è un esempio completo, ma minimo, di preparazione dei dati per i dati Titanic. Il frammento inizia con Jupyter "Magic Command" per generare il codice in un file. Se non si usa un notebook Jupyter, rimuovere la riga e creare il file manualmente.

Le varie `prepare_` funzioni del frammento di codice precedente modificano la colonna pertinente nel set di dati di input. Queste funzioni funzionano sui dati dopo che è stata modificata in un oggetto Pandas `DataFrame` . In ogni caso, i dati mancanti vengono compilati con dati casuali rappresentativi o dati categorici che indicano "sconosciuti". Viene eseguito il mapping dei dati categorici basati su testo a numeri interi. Le colonne che non sono più necessarie vengono sovrascritte o eliminate. 

Quando il codice definisce le funzioni di preparazione dei dati, il codice analizza l'argomento di input, ovvero il percorso in cui si desidera scrivere i dati. Questi valori verranno determinati da `PipelineData` oggetti che verranno discussi nel passaggio successivo. Il codice recupera l'oggetto registrato `'titanic_cs'` `Dataset` , lo converte in un Pandas `DataFrame` e chiama le varie funzioni di preparazione dei dati. 

Poiché `output_path` è completo, la funzione `os.makedirs()` viene utilizzata per preparare la struttura di directory. A questo punto, è possibile usare `DataFrame.to_csv()` per scrivere i dati di output, ma i file parquet sono più efficienti. Questa efficienza sarebbe probabilmente irrilevante con un set di dati di questo tipo, ma l'uso delle funzioni e del pacchetto **PyArrow** `from_pandas()` `write_table()` sono solo poche sequenze di tasti `to_csv()` .

I file parquet sono supportati in modo nativo dal passaggio di Machine Learning automatizzato descritto di seguito, quindi non è necessaria alcuna elaborazione speciale per utilizzarli. 

### <a name="write-the-data-preparation-pipeline-step-pythonscriptstep"></a>Scrivere il passaggio della pipeline di preparazione dati ( `PythonScriptStep` )

Il codice di preparazione dei dati descritto in precedenza deve essere associato a un `PythonScripStep` oggetto da usare con una pipeline. Il percorso in cui viene scritto l'output di preparazione dei dati parquet viene generato da un `PipelineData` oggetto. `ComputeTarget` `RunConfig` `'titanic_ds' Dataset` Per completare la specifica vengono usate le risorse preparate in precedenza, ad esempio, e.

Utenti PipelineData
```python
from azureml.pipeline.core import PipelineData

from azureml.pipeline.steps import PythonScriptStep
prepped_data_path = PipelineData("titanic_train", datastore).as_dataset()

dataprep_step = PythonScriptStep(
    name="dataprep", 
    script_name="dataprep.py", 
    compute_target=compute_target, 
    runconfig=aml_run_config,
    arguments=["--output_path", prepped_data_path],
    inputs=[titanic_ds.as_named_input("titanic_ds")],
    outputs=[prepped_data_path],
    allow_reuse=True
)
```
L' `prepped_data_path` oggetto è di tipo `PipelineOutputFileDataset` . Si noti che è specificato in entrambi gli `arguments` `outputs` argomenti e. Se si esamina il passaggio precedente, si noterà che all'interno del codice di preparazione dei dati, il valore dell'argomento `'--output_path'` è il percorso del file in cui è stato scritto il file parquet. 

> [!TIP]
> Con la classe di anteprima pubblica è disponibile un'esperienza migliorata per il passaggio di dati intermedi tra i passaggi della pipeline `OutputFileDatasetConfig` . Altre informazioni sui `OutputFileDatasetConfig` modelli e i metodi di progettazione sono disponibili nella [documentazione di riferimento di SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py).

## <a name="train-with-automlstep"></a>Eseguire il training con AutoMLStep

La configurazione di un passaggio automatico della pipeline di Machine Learning viene eseguita con la `AutoMLConfig` classe. Questa classe flessibile è descritta in [configurare esperimenti di Machine Learning automatici in Python](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train). L'input e l'output dei dati sono gli unici aspetti della configurazione che richiedono particolare attenzione in una pipeline ML. L'input e l'output per `AutoMLConfig` nelle pipeline sono descritti in dettaglio di seguito. Oltre ai dati, un vantaggio delle pipeline di ML è la possibilità di usare destinazioni di calcolo diverse per diversi passaggi. È possibile scegliere di usare un più potente `ComputeTarget` solo per il processo di Machine Learning automatizzato. Questa operazione è semplice come assegnare un più potente `RunConfiguration` al `AutoMLConfig` parametro dell'oggetto `run_configuration` .

### <a name="send-data-to-automlstep"></a>Invia dati a `AutoMLStep`

In una pipeline ML i dati di input devono essere un `Dataset` oggetto. Il modo più elevato consiste nel fornire i dati di input sotto forma di `PipelineOutputTabularDataset` oggetti. Si crea un oggetto di quel tipo con `parse_parquet_files()` o `parse_delimited_files()` in un `PipelineOutputFileDataset` , ad esempio l' `prepped_data_path` oggetto.

```python
# type(prepped_data_path) == PipelineOutputFileDataset
# type(prepped_data) == PipelineOutputTabularDataset
prepped_data = prepped_data_path.parse_parquet_files(file_extension=None)
```

Il frammento di codice precedente crea prestazioni elevate `PipelineOutputTabularDataset` dall' `PipelineOutputFileDataset` output del passaggio di preparazione dei dati.

> [!TIP]
> La classe di anteprima pubblica `OutputFileDatasetConfig` è inoltre in grado di convertire un oggetto `OutputFileDatasetConfig` in un oggetto [`OutputTabularDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.output_dataset_config.outputtabulardatasetconfig?view=azure-ml-py) per l'utilizzo nelle esecuzioni di AutoML. Altre informazioni sui `OutputFileDatasetConfig` modelli e i metodi di progettazione sono disponibili nella [documentazione di riferimento di SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py).

Un'altra opzione consiste nell'usare `Dataset` gli oggetti registrati nell'area di lavoro:

```python
prepped_data = Dataset.get_by_name(ws, 'Data_prepared')
```

Confronto tra le due tecniche:

| Tecnica | Vantaggi e svantaggi | 
|-|-|
|`PipelineOutputTabularDataset`| Prestazioni più elevate | 
|| Route naturale da `PipelineData` | 
|| I dati non vengono mantenuti dopo l'esecuzione della pipeline |
|| [Notebook che mostra la `PipelineOutputTabularDataset` tecnica](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) |
| Registrato `Dataset` | Prestazioni inferiori |
| | Può essere generato in molti modi | 
| | I dati vengono mantenuti ed è visibile nell'area di lavoro |
| | [Notebook che mostra la `Dataset` tecnica registrata](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/continuous-retraining/auto-ml-continuous-retraining.ipynb)


### <a name="specify-automated-ml-outputs"></a>Specificare gli output di ML automatici

Gli output di `AutoMLStep` rappresentano i punteggi della metrica finale del modello con prestazioni più elevate e il modello stesso. Per usare questi output in altri passaggi della pipeline, preparare `PipelineData` gli oggetti per riceverli.

```python

from azureml.pipeline.core import TrainingOutput

metrics_data = PipelineData(name='metrics_data',
                            datastore=datastore,
                            pipeline_output_name='metrics_output',
                            training_output=TrainingOutput(type='Metrics'))

model_data = PipelineData(name='best_model_data',
                          datastore=datastore,
                          pipeline_output_name='model_output',
                          training_output=TrainingOutput(type='Model'))
```

Il frammento di codice precedente crea i due `PipelineData` oggetti per le metriche e l'output del modello. Ogni è denominato, assegnato all'archivio dati predefinito recuperato in precedenza e associato al particolare `type` di `TrainingOutput` da `AutoMLStep` . Poiché l'assegnazione viene assegnata `pipeline_output_name` a questi `PipelineData` oggetti, i relativi valori saranno disponibili non solo dal singolo passaggio della pipeline, ma dall'intera pipeline, come verrà illustrato di seguito nella sezione "esaminare i risultati della pipeline". 

### <a name="configure-and-create-the-automated-ml-pipeline-step"></a>Configurare e creare il passaggio automatizzato della pipeline ML

Una volta definiti gli input e gli output, è possibile creare `AutoMLConfig` e `AutoMLStep` . I dettagli della configurazione dipenderanno dall'attività, come descritto in configurare gli esperimenti di Machine Learning [automatici in Python](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train). Per l'attività di classificazione di sopravvivenza Titanic, il frammento di codice seguente illustra una semplice configurazione.

```python
from azureml.train.automl import AutoMLConfig
from azureml.pipeline.steps import AutoMLStep

# Change iterations to a reasonable number (50) to get better accuracy
automl_settings = {
    "iteration_timeout_minutes" : 10,
    "iterations" : 2,
    "experiment_timeout_hours" : 0.25,
    "primary_metric" : 'AUC_weighted'
}

automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)

train_step = AutoMLStep(name='AutoML_Classification',
    automl_config=automl_config,
    outputs=[metrics_data,model_data],
    enable_default_model_output=False,
    enable_default_metrics_output=False,
    allow_reuse=True)
```
Il frammento di codice mostra un idioma comunemente usato con `AutoMLConfig` . Gli argomenti più fluidi (iperparametri-ish) vengono specificati in un dizionario separato, mentre i valori meno probabili da modificare vengono specificati direttamente nel `AutoMLConfig` costruttore. In questo caso, `automl_settings` specificare una breve esecuzione: l'esecuzione si arresterà dopo solo 2 iterazioni o 15 minuti, a seconda del valore che viene raggiunto per primo.

Il `automl_settings` dizionario viene passato al `AutoMLConfig` costruttore come kwarg. Gli altri parametri non sono complessi:

- `task` è impostato su `classification` per questo esempio. Altri valori validi sono `regression` e `forecasting`
- `path` e `debug_log` descrivono il percorso del progetto e un file locale in cui verranno scritte le informazioni di debug 
- `compute_target` è definito in precedenza `compute_target` che, in questo esempio, è un computer basato su CPU economico. Se si usano le funzionalità di apprendimento avanzato di AutoML, è necessario modificare la destinazione di calcolo in base alla GPU
- `featurization` è impostato su `auto`. Per altri dettagli, vedere la sezione [Data conteggi](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#data-featurization) del documento di configurazione di Machine Learning automatico 
- `label_column_name` indica la colonna da stimare 
- `training_data` viene impostato `PipelineOutputTabularDataset` sugli oggetti creati dagli output del passaggio di preparazione dei dati 

Lo `AutoMLStep` stesso accetta `AutoMLConfig` e ha, come output, gli `PipelineData` oggetti creati per conservare le metriche e i dati del modello. 

>[!Important]
> È necessario impostare `enable_default_model_output` e `enable_default_metrics_output`  su `True` solo se si utilizza  `AutoMLStepRun` .

In questo esempio, il processo di Machine Learning automatico eseguirà convalide incrociate in `training_data` . È possibile controllare il numero di convalide incrociate con l' `n_cross_validations` argomento. Se i dati di training sono già stati divisi nell'ambito dei passaggi di preparazione dei dati, è possibile impostare `validation_data` il proprio `Dataset` .

Occasionalmente è possibile vedere l'uso delle `X` funzionalità dei dati e `y` delle etichette dati. Questa tecnica è deprecata ed è consigliabile utilizzare `training_data` per l'input. 

## <a name="register-the-model-generated-by-automated-ml"></a>Registrare il modello generato da Machine Learning automatico 

L'ultimo passaggio in una pipeline Basic ML consiste nel registrare il modello creato. Aggiungendo il modello al registro di sistema del modello dell'area di lavoro, sarà disponibile nel portale e sarà possibile eseguire il controllo delle versioni. Per registrare il modello, scrivere un altro oggetto `PythonScriptStep` che accetta l' `model_data` output di `AutoMLStep` .

### <a name="write-the-code-to-register-the-model"></a>Scrivere il codice per registrare il modello

Un modello viene registrato in un oggetto `Workspace` . È probabile che si abbia familiarità con `Workspace.from_config()` l'uso di per accedere all'area di lavoro nel computer locale, ma esiste un altro modo per ottenere l'area di lavoro dall'interno di una pipeline di ml in esecuzione. `Run.get_context()`Recupera l'oggetto attivo `Run` . Questo `run` oggetto fornisce l'accesso a molti oggetti importanti, incluso quello `Workspace` utilizzato qui.

```python
%%writefile register_model.py
from azureml.core.model import Model, Dataset
from azureml.core.run import Run, _OfflineRun
from azureml.core import Workspace
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("--model_name", required=True)
parser.add_argument("--model_path", required=True)
args = parser.parse_args()

print(f"model_name : {args.model_name}")
print(f"model_path: {args.model_path}")

run = Run.get_context()
ws = Workspace.from_config() if type(run) == _OfflineRun else run.experiment.workspace

model = Model.register(workspace=ws,
                       model_path=args.model_path,
                       model_name=args.model_name)

print("Registered version {0} of model {1}".format(model.version, model.name))
```

### <a name="write-the-pythonscriptstep-code"></a>Scrivere il codice PythonScriptStep

La registrazione del modello `PythonScriptStep` utilizza un oggetto `PipelineParameter` per uno degli argomenti. I parametri della pipeline sono argomenti delle pipeline che possono essere facilmente impostati al momento dell'esecuzione dell'invio. Una volta dichiarate, vengono passate come argomenti normali. 

```python

from azureml.pipeline.core.graph import PipelineParameter

# The model name with which to register the trained model in the workspace.
model_name = PipelineParameter("model_name", default_value="TitanicSurvivalInitial")

register_step = PythonScriptStep(script_name="register_model.py",
                                       name="register_model",
                                       allow_reuse=False,
                                       arguments=["--model_name", model_name, "--model_path", model_data],
                                       inputs=[model_data],
                                       compute_target=compute_target,
                                       runconfig=aml_run_config)
```

## <a name="create-and-run-your-automated-ml-pipeline"></a>Creare ed eseguire la pipeline di Machine Learning automatizzata

La creazione e l'esecuzione di una pipeline che contiene un oggetto `AutoMLStep` non è diversa da una normale pipeline. 

```python
from azureml.pipeline.core import Pipeline
from azureml.core import Experiment

pipeline = Pipeline(ws, [dataprep_step, train_step, register_step])

experiment = Experiment(workspace=ws, name='titanic_automl')

run = experiment.submit(pipeline, show_output=True)
run.wait_for_completion()
```

Il codice precedente combina i passaggi di preparazione dei dati, di Machine Learning automatizzati e di registrazione del modello in un `Pipeline` oggetto. Viene quindi creato un `Experiment` oggetto. Il `Experiment` Costruttore recupererà l'esperimento denominato, se esistente, oppure lo creerà se necessario. Invia `Pipeline` a `Experiment` , creando un `Run` oggetto che eseguirà la pipeline in modo asincrono. La `wait_for_completion()` funzione si blocca fino al completamento dell'esecuzione.

### <a name="examine-pipeline-results"></a>Esaminare i risultati della pipeline 

Al `run` termine dell'operazione, è possibile recuperare `PipelineData` gli oggetti a cui è stato assegnato un `pipeline_output_name` . È possibile scaricare i risultati e caricarli per un'ulteriore elaborazione.  

```python
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

I file scaricati vengono scritti nella sottodirectory `azureml/{run.id}/` . Il file delle metriche è in formato JSON e può essere convertito in un dataframe di Pandas per l'esame.

Per l'elaborazione locale, potrebbe essere necessario installare i pacchetti pertinenti, ad esempio Pandas, Pickle, AzureML SDK e così via. Per questo esempio, è probabile che il modello migliore trovato da Machine Learning automatico dipende da XGBoost.

```bash
!pip install xgboost==0.90
```

```python
import pandas as pd
import json

metrics_filename = metrics_output._path_on_datastore
# metrics_filename = path to downloaded file
with open(metrics_filename) as f:
   metrics_output_result = f.read()
   
deserialized_metrics_output = json.loads(metrics_output_result)
df = pd.DataFrame(deserialized_metrics_output)
df
```

Il frammento di codice precedente Mostra il caricamento del file di metrica dalla relativa posizione nell'archivio dati di Azure. È anche possibile caricarlo dal file scaricato, come illustrato nel commento. Una volta deserializzata e convertita in un dataframe di Pandas, è possibile visualizzare le metriche dettagliate per ogni iterazione del passaggio di Machine Learning automatico.

Il file del modello può essere deserializzato in un `Model` oggetto che è possibile usare per l'inferenza, ulteriori analisi delle metriche e così via. 

```python
import pickle

model_filename = model_output._path_on_datastore
# model_filename = path to downloaded file

with open(model_filename, "rb" ) as f:
    best_model = pickle.load(f)

# ... inferencing code not shown ...
```

Per ulteriori informazioni sul caricamento e sull'utilizzo dei modelli esistenti, vedere [utilizzare un modello esistente con Azure Machine Learning](how-to-deploy-existing-model.md).

### <a name="download-the-results-of-an-automated-ml-run"></a>Scarica i risultati di un'esecuzione automatica di Machine Learning

Se si è stati seguiti insieme all'articolo, si avrà un oggetto di cui è stata creata un'istanza `run` . È anche possibile recuperare `Run` gli oggetti completati da `Workspace` tramite un `Experiment` oggetto.

L'area di lavoro contiene un record completo di tutti gli esperimenti ed esecuzioni. È possibile usare il portale per trovare e scaricare gli output degli esperimenti o usare il codice. Per accedere ai record da un'esecuzione cronologica, usare Azure Machine Learning per trovare l'ID dell'esecuzione a cui si è interessati. Con tale ID, è possibile scegliere l'oggetto specifico `run` per mezzo di `Workspace` e `Experiment` .

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['titanic_automl']
run = next(run for run in ex.get_runs() if run.id == run_id)
```

Sarebbe necessario modificare le stringhe del codice precedente in base alle specifiche dell'esecuzione cronologica. Il frammento di codice precedente presuppone che sia stato assegnato all' `ws` oggetto pertinente `Workspace` con il normale `from_config()` . L'esperimento di interesse viene recuperato direttamente e quindi il codice trova l'elemento `Run` di interesse abbinando il `run.id` valore.

Una volta ottenuto un `Run` oggetto, è possibile scaricare la metrica e il modello. 

```python
automl_run = next(r for r in run.get_children() if r.name == 'AutoML_Classification')
outputs = automl_run.get_outputs()
metrics = outputs['default_metrics_AutoML_Classification']
model = outputs['default_model_AutoML_Classification']

metrics.get_port_data_reference().download('.')
model.get_port_data_reference().download('.')
```

Ogni `Run` oggetto contiene `StepRun` oggetti contenenti informazioni sull'esecuzione del singolo passaggio della pipeline. `run`Viene eseguita la ricerca dell' `StepRun` oggetto per l'oggetto `AutoMLStep` . Le metriche e il modello vengono recuperati usando i nomi predefiniti, disponibili anche se non si passano `PipelineData` oggetti al `outputs` parametro di `AutoMLStep` . 

Infine, le metriche e il modello effettivi vengono scaricati nel computer locale, come illustrato nella sezione "esaminare i risultati della pipeline" riportata in precedenza.

## <a name="next-steps"></a>Passaggi successivi

- Eseguire questo Jupyter notebook con un [esempio completo di Machine Learning automatico in una pipeline](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) che usa la regressione per stimare le tariffe dei taxi
- [Creare esperimenti di Machine Learning automatici senza scrivere codice](how-to-use-automated-ml-for-ml-models.md)
- Esplora una vasta gamma di [notebook di Jupyter che dimostrano](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) l'apprendimento automatico
- Informazioni sull'integrazione della pipeline in per [MLOps end-to-end](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment#automate-the-ml-lifecycle) o sull'analisi del [repository GitHub MLOps](https://github.com/Microsoft/MLOpspython) 
