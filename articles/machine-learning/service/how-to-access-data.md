---
title: Usare gli archivi dati in Azure Machine Learning per accedere ai dati
description: Come usare gli archivi dati per accedere alle risorse di archiviazione dei dati di accesso durante il training
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 79e26d4d2cf5743abae6dc0f1fb58585e1b9b9db
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2018
ms.locfileid: "50977907"
---
# <a name="how-to-access-data-during-training"></a>Procedura per accedere ai dati durante il training
Usare un archivio dati per interagire con i dati nei flussi di lavoro di Azure Machine Learning e accedervi.

Nel servizio Azure Machine Learning un archivio dati è un'astrazione per [Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction). L'archivio dati può usare un contenitore [Blob di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) o una [Condivisione file di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) come risorsa di archiviazione sottostante. 

## <a name="create-a-datastore"></a>Creare un archivio dati
Per usare archivi dati, è necessaria prima di tutto un'[area di lavoro](concept-azure-machine-learning-architecture.md#workspace). Per iniziare, [creare una nuova area di lavoro](quickstart-create-workspace-with-python.md) o recuperarne una esistente:

```Python
import azureml.core
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="use-the-default-datastore"></a>Usare l'archivio dati predefinito
Non è necessario creare o configurare un account di archiviazione.  Ogni area di lavoro include un archivio dati predefinito che è possibile iniziare a usare immediatamente.

Per ottenere l'archivio dati predefinito dell'area di lavoro:
```Python
ds = ws.get_default_datastore()
```

### <a name="register-a-datastore"></a>Registrare un archivio dati
Se si ha già una risorsa di Archiviazione di Azure, è possibile registrarla come archivio dati nell'area di lavoro. È possibile registrare un contenitore BLOB di Azure o una condivisione file di Azure come archivio dati. Tutti i metodi di registrazione si trovano nella classe `Datastore` e hanno il formato `register_azure_*`.

#### <a name="azure-blob-container-datastore"></a>Archivio dati del contenitore Blob di Azure
Per registrare un archivio dati del contenitore Blob di Azure:

```Python
ds = Datastore.register_azure_blob_container(workspace=ws, 
                                             datastore_name='your datastore name', 
                                             container_name='your azure blob container name',
                                             account_name='your storage account name', 
                                             account_key='your storage account key',
                                             create_if_not_exists=True)
```

#### <a name="azure-file-share-datastore"></a>Archivio dati di Condivisione file di Azure
Per registrare un archivio dati di Condivisione file di Azure:

```Python
ds = Datastore.register_azure_file_share(workspace=ws, 
                                         datastore_name='your datastore name', 
                                         container_name='your file share name',
                                         account_name='your storage account name', 
                                         account_key='your storage account key',
                                         create_if_not_exists=True)
```

### <a name="get-an-existing-datastore"></a>Ottenere un archivio dati esistente
Per eseguire una query per un archivio dati registrato in base al nome:
```Python
ds = Datastore.get(ws, datastore_name='your datastore name')
```

È anche possibile ottenere tutti gli archivi dati per area di lavoro:
```Python
datastores = ws.datastores()
for name, ds in datastores.items(),
    print(name, ds.datastore_type)"
```

Per praticità, impostare uno degli archivi dati registrati come archivio dati predefinito per l'area di lavoro:
```Python
ws.set_default_datastore('your datastore name')
```

## <a name="upload-and-download-data"></a>Caricamento e download dei dati
### <a name="upload"></a>Caricamento
Caricare una directory o singoli file nell'archivio dati tramite Python SDK.

Per caricare una directory in un archivio dati `ds`:
```Python
ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```
`target_path` specifica il percorso nella condivisione file (o contenitore Blob) da caricare. Per impostazione predefinita in `None`, in tal caso i dati vengono caricati nella radice. `overwrite=True` sovrascriverà tutti i dati esistenti in `target_path`.

In alternativa, è possibile caricare un elenco di singoli file nell'archivio dati tramite il metodo `upload_files()` dell'archivio dati.

### <a name="download"></a>Download
Analogamente, è possibile scaricare dati da un archivio dati al file system locale.

```Python
ds.download(target_path='your target path',
            prefix='your prefix',
            show_progress=True)
```
`target_path` è la posizione della directory locale in cui scaricare i dati. Per specificare un percorso della cartella o condivisione file (o contenitore Blob) per il download, fornire tale percorso a `prefix`. Se `prefix` è `None`, tutto il contenuto della condivisione file (o contenitore Blob) verrà scaricato.

## <a name="access-datastores-for-training"></a>Accesso agli archivi dati per il training
È possibile accedere a un archivio dati durante l'esecuzione di un training (ad esempio per dati di training o convalida) in una destinazione di calcolo remota tramite Python SDK. 

Esistono due modi per rendere disponibile l'archivio dati in un ambiente di calcolo remoto:
* **Eseguire il montaggio**  
`ds.as_mount()`: specificando questa modalità di esecuzione del montaggio, l'archivio dati verrà montato automaticamente nell'ambiente di calcolo remoto. 
* **Download/caricamento**  
    * `ds.as_download(path_on_compute='your path on compute')`: scarica dati dall'archivio dati alla destinazione di calcolo remota, nel percorso specificato da `path_on_compute`.
    * `ds.as_upload(path_on_compute='yourfilename'` carica dati nell'archivio dati.  Si supponga che lo script di training crei un file `foo.pkl` nella directory di lavoro corrente nella destinazione di calcolo remota. Caricare questo file nell'archivio dati con `ds.as_upload(path_on_compute='./foo.pkl')` dopo che lo script crea il file. Il file viene caricato nella radice dell'archivio dati.
    
Per fare riferimento a una cartella o un file specifico nell'archivio dati, usare la funzione **`path`** dell'archivio dati. Ad esempio, per scaricare il contenuto della directory `./bar` dall'archivio dati alla destinazione di calcolo, usare `ds.path('./bar').as_download()`.

Eventuali oggetti `ds` oppure `ds.path` si risolvono in un nome di variabile di ambiente nel formato `"$AZUREML_DATAREFERENCE_XXXX"` il cui valore rappresenta il percorso di esecuzione del montaggio o download in attività di calcolo remoto. Il percorso di archivio dati nell'attività di calcolo remoto potrebbe non essere lo stesso utilizzato per il percorso di esecuzione dello script.

Per accedere all'archivio dati durante il training, passarlo nello script di training come argomento della riga di comando tramite `script_params`:

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py')
```
`as_mount()` è la modalità predefinita per un archivio dati, pertanto è inoltre possibile passare direttamente `ds` all'argomento `'--data_dir'`.

In alternativa, è possibile passare un elenco di archivi dati al parametro `inputs` del costruttore dell'oggetto di stima per eseguire il montaggio o la copia dagli/agli archivi dati:

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```
Il codice sopra riportato:
* effettua il download di tutti i contenuti dell'archivio dati `ds1` nell'ambiente di calcolo remoto prima che lo script di training `train.py` venga eseguito
* effettua il download della cartella `'./foo'` dell'archivio dati `ds2` nell'ambiente di calcolo remoto prima che `train.py` venga eseguito
* carica il file `'./bar.pkl'` dall'ambiente di calcolo remoto fino all'archivio dati `d3` dopo l'esecuzione dello script

## <a name="next-steps"></a>Passaggi successivi
* [Eseguire il training di un modello](how-to-train-ml-models.md)
