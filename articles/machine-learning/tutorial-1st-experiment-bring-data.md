---
title: 'Esercitazione: Usare i propri dati'
titleSuffix: Azure Machine Learning
description: La quarta parte della serie introduttiva su Azure Machine Learning illustra come usare i propri dati in un'esecuzione di training remoto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: tracking-python
ms.openlocfilehash: 123e55202de8a33bca88afcfd1f0dc0c7edeae77
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93320092"
---
# <a name="tutorial-use-your-own-data-part-4-of-4"></a>Esercitazione: Usare i propri dati (parte 4 di 4)

Questa esercitazione illustra come caricare e usare i propri dati per eseguire il training dei modelli di Machine Learning in Azure Machine Learning.

Questa esercitazione è la *quarta di una serie in quattro parti* in cui vengono illustrati i concetti fondamentali su Azure Machine Learning e vengono completate attività di Machine Learning basate su processi in Azure. Questa esercitazione si basa sull'attività completata nella [Parte 1: Configurare](tutorial-1st-experiment-sdk-setup-local.md), [Parte 2: Eseguire "Hello World"](tutorial-1st-experiment-hello-world.md) e nella [Parte 3: Eseguire il training di un modello](tutorial-1st-experiment-sdk-train.md).

Nella [Parte 3: Eseguire il training di un modello](tutorial-1st-experiment-sdk-train.md) i dati sono stati scaricati usando il metodo `torchvision.datasets.CIFAR10` incorporato nell'API PyTorch. In molti casi tuttavia potrebbe essere necessario usare i propri dati in un'esecuzione di training remoto. Questo articolo illustra il flusso di lavoro che è possibile usare per lavorare con i propri dati in Azure Machine Learning.

In questa esercitazione:

> [!div class="checklist"]
> * Configurare uno script di training per usare i dati in una directory locale.
> * Testare lo script di training in locale.
> * Caricare i dati in Azure.
> * Creare uno script di controllo.
> * Acquisire i nuovi concetti di Azure Machine Learning (passaggio di parametri, set di dati, archivi dati).
> * Inviare ed eseguire lo script di training.
> * Visualizzare l'output del codice nel cloud.

## <a name="prerequisites"></a>Prerequisiti

* Completamento della [parte 3](tutorial-1st-experiment-sdk-train.md) della serie.
* Conoscenze di base del linguaggio Python e dei flussi di lavoro di Machine Learning.
* Ambiente di sviluppo locale, come Visual Studio Code, Jupyter o PyCharm.
* Python (versione 3.5-3.7).

## <a name="adjust-the-training-script"></a>Modificare lo script di training
A questo punto lo script di training (tutorial/src/train.py) è in esecuzione in Azure Machine Learning ed è possibile monitorare le prestazioni del modello. Ora lo script di training verrà parametrizzato introducendo gli argomenti. L'uso degli argomenti consente di confrontare facilmente iperparametri diversi.

Lo script di training è ora impostato in modo da scaricare il set di dati CIFAR10 a ogni esecuzione. Il codice di Python seguente è stato modificato per leggere i dati da una directory.

>[!NOTE] 
> L'uso di `argparse` parametrizza lo script.

```python
# tutorial/src/train.py
import os
import argparse
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net
from azureml.core import Run

run = Run.get_context()

if __name__ == "__main__":
    parser = argparse.ArgumentParser()
    parser.add_argument('--data_path', type=str, help='Path to the training data')
    parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
    parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
    args = parser.parse_args()
    
    print("===== DATA =====")
    print("DATA PATH: " + args.data_path)
    print("LIST FILES IN DATA PATH...")
    print(os.listdir(args.data_path))
    print("================")
    
    # prepare DataLoader for CIFAR10 data
    transform = transforms.Compose([transforms.ToTensor(), transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))])
    trainset = torchvision.datasets.CIFAR10(
        root=args.data_path,
        train=True,
        download=False,
        transform=transform,
    )
    trainloader = torch.utils.data.DataLoader(trainset, batch_size=4, shuffle=True, num_workers=2)

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(
        net.parameters(),
        lr=args.learning_rate,
        momentum=args.momentum,
    )

    # train the network
    for epoch in range(2):

        running_loss = 0.0
        for i, data in enumerate(trainloader, 0):
            # unpack the data
            inputs, labels = data

            # zero the parameter gradients
            optimizer.zero_grad()

            # forward + backward + optimize
            outputs = net(inputs)
            loss = criterion(outputs, labels)
            loss.backward()
            optimizer.step()

            # print statistics
            running_loss += loss.item()
            if i % 2000 == 1999:
                loss = running_loss / 2000
                run.log('loss', loss) # log loss metric to AML
                print(f'epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}')
                running_loss = 0.0

    print('Finished Training')
```

### <a name="understanding-the-code-changes"></a>Informazioni sulle modifiche al codice

Il codice in `train.py` ha usato la libreria `argparse` per configurare `data_path`, `learning_rate` e `momentum`.

```python
# .... other code
parser = argparse.ArgumentParser()
parser.add_argument('--data_path', type=str, help='Path to the training data')
parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
args = parser.parse_args()
# ... other code
```

Lo script `train.py` è stato anche adattato per aggiornare l'utilità di ottimizzazione per l'uso di parametri definiti dall'utente:

```python
optimizer = optim.SGD(
    net.parameters(),
    lr=args.learning_rate,     # get learning rate from command-line argument
    momentum=args.momentum,    # get momentum from command-line argument
)
```

## <a name="test-the-script-locally"></a>Testare lo script in locale

Lo script ora accetta _data path_ come argomento. Per iniziare, testarlo in locale. Aggiungere una cartella denominata `data` alla struttura di directory dell'esercitazione. La struttura di directory sarà simile alla seguente:

```txt
tutorial
└──.azureml
|  └──config.json
|  └──pytorch-env.yml
└──data
└──src
|  └──hello.py
|  └──model.py
|  └──train.py
└──01-create-workspace.py
└──02-create-compute.py
└──03-run-hello.py
└──04-run-pytorch.py
```

Se non si è eseguito `train.py` in locale nell'esercitazione precedente, la directory `data/` non sarà presente. In questo caso, eseguire in locale il metodo `torchvision.datasets.CIFAR10` con `download=True` nello script `train.py`.

Per eseguire in locale lo script di training modificato, chiamare:

```bash
python src/train.py --data_path ./data --learning_rate 0.003 --momentum 0.92
```

Per evitare di scaricare il set di dati CIFAR10, passare un percorso locale ai dati. È anche possibile sperimentare con valori diversi per gli iperparametri _learning rate_ e _momentum_ senza doverli impostare come hardcoded nello script di training.

## <a name="upload-the-data-to-azure"></a>Caricare i dati in Azure

Per eseguire questo script in Azure Machine Learning, è necessario rendere i dati di training disponibili in Azure. L'area di lavoro di Azure Machine Learning include un archivio dati _predefinito_. Si tratta di un account di archiviazione BLOB di Azure in cui è possibile archiviare i dati di training.

>[!NOTE] 
> Azure Machine Learning consente di connettere altri archivi dati basati sul cloud in cui vengono archiviati i dati. Per altri dettagli, vedere la [documentazione degli archivi dati](./concept-data.md).  

Creare un nuovo script di controllo di Python denominato `05-upload-data.py` nella directory `tutorial`:

```python
# tutorial/05-upload-data.py
from azureml.core import Workspace
ws = Workspace.from_config()
datastore = ws.get_default_datastore()
datastore.upload(src_dir='./data', target_path='datasets/cifar10', overwrite=True)
```

Il valore `target_path` specifica il percorso dell'archivio dati in cui verranno caricati i dati CIFAR10.

>[!TIP] 
> Anche se si usa Azure Machine Learning per caricare i dati, è possibile usare [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) per caricare file ad hoc. Se è necessario uno strumento di estrazione, trasformazione e caricamento, è possibile usare [Azure Data Factory](../data-factory/introduction.md) per inserire i dati in Azure.

Eseguire il file di Python per caricare i dati. il caricamento dovrebbe richiedere meno di 60 secondi.

```bash
python 05-upload-data.py
```
Verrà visualizzato l'output standard seguente:
```txt
Uploading ./data\cifar-10-batches-py\data_batch_2
Uploaded ./data\cifar-10-batches-py\data_batch_2, 4 files out of an estimated total of 9
.
.
Uploading ./data\cifar-10-batches-py\data_batch_5
Uploaded ./data\cifar-10-batches-py\data_batch_5, 9 files out of an estimated total of 9
Uploaded 9 files
```


## <a name="create-a-control-script"></a>Creare uno script di controllo

Come è stato fatto in precedenza, creare un nuovo script di controllo di Python denominato `06-run-pytorch-data.py`:

```python
# tutorial/06-run-pytorch-data.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig
from azureml.core import Dataset

if __name__ == "__main__":
    ws = Workspace.from_config()
    
    datastore = ws.get_default_datastore()
    dataset = Dataset.File.from_files(path=(datastore, 'datasets/cifar10'))

    experiment = Experiment(workspace=ws, name='day1-experiment-data')

    config = ScriptRunConfig(
        source_directory='./src',
        script='train.py',
        compute_target='cpu-cluster',
        arguments=[
            '--data_path', dataset.as_named_input('input').as_mount(),
            '--learning_rate', 0.003,
            '--momentum', 0.92],
        )
    
    # set up pytorch environment
    env = Environment.from_conda_specification(name='pytorch-env',file_path='.azureml/pytorch-env.yml')
    config.run_config.environment = env

    run = experiment.submit(config)
    aml_url = run.get_portal_url()
    print("Submitted to an Azure Machine Learning compute cluster. Click on the link below")
    print("")
    print(aml_url)
```

### <a name="understand-the-code-changes"></a>Informazioni sulle modifiche al codice

Lo script di controllo è simile a quello della [terza parte di questa serie](tutorial-1st-experiment-sdk-train.md), con le nuove righe seguenti:

:::row:::
   :::column span="":::
      `dataset = Dataset.File.from_files( ... )`
   :::column-end:::
   :::column span="2":::
      Un [set di dati](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py) viene usato per fare riferimento ai dati caricati in archiviazione BLOB di Azure. I set di dati sono un livello di astrazione superiore a quello dei dati e sono progettati per migliorare l'affidabilità e l'attendibilità.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig(...)`
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) è stato modificato in modo da includere un elenco di argomenti che verranno passati in `train.py`. L'argomento `dataset.as_named_input('input').as_mount()` indica che la directory specificata verrà _montata_ nella destinazione di calcolo.
   :::column-end:::
:::row-end:::

## <a name="submit-the-run-to-azure-machine-learning"></a>Inviare l'esecuzione ad Azure Machine Learning

A questo punto inviare di nuovo l'esecuzione per usare la nuova configurazione:

```bash
python 06-run-pytorch-data.py
```

Questo codice stamperà un URL nell'esperimento in Azure Machine Learning Studio. Passando a tale collegamento, sarà possibile vedere il codice in esecuzione.

### <a name="inspect-the-log-file"></a>Esaminare il file di log

In Studio passare all'esecuzione dell'esperimento, selezionando l'output dell'URL precedente, seguito da **Output e log**. Selezionare il file `70_driver_log.txt`. Viene visualizzato l'output seguente:

```txt
Processing 'input'.
Processing dataset FileDataset
{
  "source": [
    "('workspaceblobstore', 'datasets/cifar10')"
  ],
  "definition": [
    "GetDatastoreFiles"
  ],
  "registration": {
    "id": "XXXXX",
    "name": null,
    "version": null,
    "workspace": "Workspace.create(name='XXXX', subscription_id='XXXX', resource_group='X')"
  }
}
Mounting input to /tmp/tmp9kituvp3.
Mounted input to /tmp/tmp9kituvp3 as folder.
Exit __enter__ of DatasetContextManager
Entering Run History Context Manager.
Current directory:  /mnt/batch/tasks/shared/LS_root/jobs/dsvm-aml/azureml/tutorial-session-3_1600171983_763c5381/mounts/workspaceblobstore/azureml/tutorial-session-3_1600171983_763c5381
Preparing to call script [ train.py ] with arguments: ['--data_path', '$input', '--learning_rate', '0.003', '--momentum', '0.92']
After variable expansion, calling script [ train.py ] with arguments: ['--data_path', '/tmp/tmp9kituvp3', '--learning_rate', '0.003', '--momentum', '0.92']

Script type = None
===== DATA =====
DATA PATH: /tmp/tmp9kituvp3
LIST FILES IN DATA PATH...
['cifar-10-batches-py', 'cifar-10-python.tar.gz']
```

Notare:

- Azure Machine Learning ha montato automaticamente l'archiviazione BLOB nel cluster di calcolo.
- ``dataset.as_named_input('input').as_mount()`` usato nello script di controllo viene risolto nel punto di montaggio.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

È anche possibile mantenere il gruppo di risorse ma eliminare una singola area di lavoro. Visualizzare le proprietà dell'area di lavoro e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è visto come caricare i dati in Azure usando `Datastore`. L'archivio dati è stato usato come risorsa di archiviazione nel cloud per l'area di lavoro, fornendo uno spazio di archiviazione permanente e flessibile in cui conservare i dati.

È stato illustrato come modificare lo script di training per accettare un percorso dati tramite la riga di comando. Usando `Dataset` è stato possibile montare una directory nell'esecuzione remota. 

Ora che è stato creato un modello, vedere:

* [Distribuire modelli con Azure Machine Learning](how-to-deploy-and-where.md).