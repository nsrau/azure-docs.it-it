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
ms.openlocfilehash: 615ab592c040eedf7d31e3a3036f558ea6c09740
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990808"
---
# <a name="how-to-access-data-during-training"></a>Procedura per accedere ai dati durante il training
Nei servizi Azure Machine Learning, un archivio dati è un'astrazione su [Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction). L'archivio dati può usare un contenitore [Blob di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) o una [Condivisione file di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) come risorsa di archiviazione sottostante. Gli archivi dati consentono di accedere e interagire facilmente con l'archiviazione dei dati durante i flussi di lavoro di Azure Machine Learning tramite Python SDK o interfaccia della riga di comando.

## <a name="create-a-datastore"></a>Creare un archivio dati
Per usare gli archivi dati, è necessaria prima di tutto un'[Area di lavoro](concept-azure-machine-learning-architecture.md#workspace). È possibile creare una nuova area di lavoro o recuperarne una esistente:

```Python
import azureml.core
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="use-the-default-datastore"></a>Usare l'archivio dati predefinito
Ogni area di lavoro possiede un archivio dati predefinito che è possibile iniziare a usare immediatamente e che consente di risparmiare il lavoro di creazione e configurazione dei propri account di archiviazione.

Per ottenere l'archivio dati predefinito dell'area di lavoro:
```Python
ds = ws.get_default_datastore()
```

### <a name="register-a-datastore"></a>Registrare un archivio dati
Se si dispone già di Archiviazione di Azure, è possibile eseguirne la registrazione come un archivio dati nell'area di lavoro. Azure Machine Learning consente di registrare un contenitore Blob di Azure o una Condivisione file di Azure come un archivio dati. Tutti i metodi di registrazione si trovano nella classe `Datastore` e hanno il formato `register_azure_*`.

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

Per praticità, se si desidera impostare uno dei propri archivi di dati registrati come archivio dati predefinito per la propria area di lavoro, è possibile eseguire la seguente procedura:
```Python
ws.set_default_datastore('your datastore name')
```

## <a name="upload-and-download-data"></a>Caricamento e download dei dati
### <a name="upload"></a>Caricamento
È possibile caricare una directory o i singoli file nell'archivio dati usando Python SDK.

Per caricare una directory in un archivio dati `ds`:
```Python
ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```
`target_path` specifica il percorso nella condivisione file (o contenitore Blob) da caricare. Per impostazione predefinita in `None`, in tal caso i dati vengono caricati nella radice. `overwrite=True` sovrascriverà tutti i dati esistenti in `target_path`.

In alternativa, è possibile caricare un elenco di singoli file nell'archivio dati tramite il metodo di archivio dati `upload_files()`.

### <a name="download"></a>Download
Analogamente, è possibile effettuare il download dei dati da un archivio dati al file system locale.

```Python
ds.download(target_path='your target path',
            prefix='your prefix',
            show_progress=True)
```
`target_path` è la posizione della directory locale in cui scaricare i dati. Per specificare un percorso della cartella o condivisione file (o contenitore Blob) per il download, fornire tale percorso a `prefix`. Se `prefix` è `None`, tutto il contenuto della condivisione file (o contenitore Blob) verrà scaricato.

## <a name="access-datastores-for-training"></a>Accesso agli archivi dati per il training
È possibile accedere a un archivio dati durante l'esecuzione di un training (ad esempio, per i dati di training o la convalida degli stessi) in una destinazione di calcolo remoto tramite Python SDK. 

Esistono due modi per rendere disponibile l'archivio dati in un ambiente di calcolo remoto:
* **Eseguire il montaggio**  
`ds.as_mount()`: specificando questa modalità di esecuzione del montaggio, l'archivio dati verrà montato automaticamente nell'ambiente di calcolo remoto. 
* **Download/caricamento**  
    * `ds.as_download(path_on_compute='your path on compute')`: con questa modalità di download, i dati verranno vengono scaricati dall'archivio dati all'ambiente di calcolo remoto nel percorso specificato da `path_on_compute`.
    * Viceversa, è inoltre possibile caricare i dati generati dall'esecuzione del training in un archivio dati. Ad esempio, se lo script di training crea un file `foo.pkl` nella directory di lavoro corrente nell'ambiente calcolo remoto, è possibile specificare che venga caricato nell'archivio dati dopo l'esecuzione dello script: `ds.as_upload(path_on_compute='./foo.pkl')`. Questo caricherà il file alla radice dell'archivio dati.
    
Se si desidera fare riferimento a una cartella specifica o a un file nell'archivio dati, è possibile usare la funzione di archivio dati **`path`**. Ad esempio, se l'archivio dati dispone di una directory con il percorso relativo `./bar` e se si vuole solo effettuare il download del contenuto di questa cartella nella destinazione di calcolo, è possibile eseguire la seguente procedura: `ds.path('./bar').as_download()`

Eventuali oggetti `ds` oppure `ds.path` si risolvono in un nome di variabile di ambiente nel formato `"$AZUREML_DATAREFERENCE_XXXX"` il cui valore rappresenta il percorso di esecuzione del montaggio o download in attività di calcolo remoto. Il percorso di archivio dati nell'attività di calcolo remoto potrebbe non essere lo stesso utilizzato per il percorso di esecuzione dello script.

Per accedere all'archivio dati durante il training, è possibile eseguirne il trasferimento allo script di training come argomento della riga di comando tramite `script_params`:

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

In alternativa, è possibile passare un elenco di archivi dati al parametro `inputs` del costruttore dell'oggetto di stima per eseguire il montaggio o copiare da/in archivi dati:

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
