---
title: Creare una data factory di Azure tramite Python | Microsoft Docs
description: Creare una data factory di Azure per copiare dati da un percorso a un altro in Archiviazione BLOB di Azure.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 9c6f7c8e052aed2ab5aba2ee54065bf40e81a022
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="create-a-data-factory-and-pipeline-using-python"></a>Creare una data factory e una pipeline con Python
Azure Data Factory è un servizio di integrazione di dati basato sul cloud che consente di creare flussi di lavoro basati sui dati nel cloud per orchestrare e automatizzare lo spostamento e la trasformazione dei dati stessi. Usando Azure Data Factory è possibile creare e pianificare flussi di lavoro (denominati pipeline) basati sui dati che possono inserire dati da archivi dati diversi, elaborarli e trasformarli tramite servizi di calcolo come Hadoop di Azure HDInsight, Spark, Azure Data Lake Analytics e Azure Machine Learning e pubblicare l'output in archivi come Azure SQL Data Warehouse per l'uso da parte di applicazioni di business intelligence (BI). 

Questa guida introduttiva descrive come usare Python per creare una data factory di Azure. La pipeline in questa data factory copia dati da una cartella a un'altra cartella in un archivio BLOB di Azure.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* **Account di archiviazione di Azure**. Usare l'archivio BLOB come archivio dati di **origine** e **sink**. Se non si ha un account di archiviazione di Azure, vedere l'articolo [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account) per informazioni su come crearne uno. 
* **Creare un'applicazione in Azure Active Directory** seguendo [queste istruzioni](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Annotare i valori seguenti, da usare nei passaggi successivi: **ID applicazione**, **chiave di autenticazione** e **ID tenant**. Assegnare l'applicazione al ruolo "**Collaboratore**" seguendo le istruzioni disponibili nello stesso articolo. 

### <a name="create-and-upload-an-input-file"></a>Creare e caricare un file di input

1. Avviare il Blocco note. Copiare il testo seguente e salvarlo come file **input.txt** sul disco.
    
    ```
    John|Doe
    Jane|Doe
    ```
2.  Usare strumenti come [Azure Storage Explorer](http://storageexplorer.com/) per creare il contenitore **adfv2tutorial** e per caricare il file input.txt nel contenitore. 

## <a name="install-the-python-package"></a>Installare il pacchetto Python
1. Aprire un terminale o un prompt dei comandi con privilegi di amministratore.  
2. Per installare il pacchetto Python per Data Factory, eseguire questo comando:

    ```
    pip install azure-mgmt-datafactory
    ```

    [Python SDK per Data Factory](https://github.com/Azure/azure-sdk-for-python) supporta Python 2.7, 3.3, 3.4, 3.5 e 3.6.
## <a name="create-a-data-factory-client"></a>Creare un client di Data Factory

1. Creare un file denominato **datafactory.py**. Aggiungere le istruzioni seguenti per aggiungere riferimenti a spazi dei nomi.
    
    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from msrestazure.azure_cloud import Cloud, CloudEndpoints, CloudSuffixes
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.datafactory import DataFactoryManagementClient
    from azure.mgmt.datafactory.models import *
    import json
    import time    
    ```
2. Aggiungere le funzioni seguenti, che consentono di stampare informazioni. 

    ```python
    def print_item(group):
      """Print an Azure object instance."""
      print("\tName: {}".format(group.name))
      print("\tId: {}".format(group.id))
      if hasattr(group, 'location'):
          print("\tLocation: {}".format(group.location))
      if hasattr(group, 'tags'):
          print("\tTags: {}".format(group.tags))
      if hasattr(group, 'properties'):
          print_properties(group.properties)
    
    def print_properties(props):
      """Print a ResourceGroup properties instance."""
      if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
          print("\tProperties:")
          print("\t\tProvisioning State: {}".format(props.provisioning_state))
      print("\n\n")    
    ```
3. Aggiungere il codice seguente al metodo **Main** per creare un'istanza della classe DataFactoryManagementClient. Usare questo oggetto per creare la data factory, il servizio collegato, i set di dati e la pipeline. È possibile usare questo oggetto anche per monitorare i dettagli sull'esecuzione della pipeline.

    ```python   
    subscription_id = '<your subscription ID where the factory resides>'
    credentials = ServicePrincipalCredentials(
            client_id='<yourClientId>',
            secret='<YourPassword>',
            tenant='<YourTenandId>'
    )
    resource_client = ResourceManagementClient(credentials, subscription_id)
    adf_client = DataFactoryManagementClient(credentials, subscription_id)
    
    rg_params = {'location':'eastus'}
    df_params = {'location':'eastus'}
    rg_name = '<Your Resource Group Name>'
    df_name = '<Your Data Factory Name>'
    ```

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory

Aggiungere il codice seguente al metodo **Main** per creare una **data factory**. 

```python
#Create Resource Group
resource_client.resource_groups.create_or_update(rg_name, rg_params)

#Create Data Factory
df_resource = Factory(location='eastus')
df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
print_item(df)
while df.provisioning_state != 'Succeeded':
    df = adf_client.factories.get(rg_name, df_name)
    time.sleep(1)
print_item(adf_client.factories.get(rg_name, df_name))
```

## <a name="create-a-linked-service"></a>Creare un servizio collegato

Aggiungere il codice seguente al metodo **Main** per creare un **servizio collegato di Archiviazione di Azure**.

Si creano servizi collegati in una data factory per collegare gli archivi dati e i servizi di calcolo alla data factory. In questa guida introduttiva è necessario creare solo un servizio collegato di Archiviazione di Azure come archivio di origine e sink della copia, denominato "AzureStorageLinkedService" nell'esempio.

```python
#Create Storage Linked Service
ls_name = 'storageLinkedService'

#Replace Storage String with your credentials
storage_string = SecureString('DefaultEndpointsProtocol=https;AccountName=<replace>;AccountKey=<replace>')

ls_azure_storage = AzureStorageLinkedService(connection_string=storage_string)
ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
print_item(ls)
```
## <a name="create-datasets"></a>Creare set di dati
In questa sezione vengono creati due set di dati: uno per l'origine e l'altro per il sink.

### <a name="create-a-dataset-for-source-azure-blob"></a>Creare un set di dati per il BLOB di Azure di origine
Aggiungere il codice seguente al metodo Main per creare un set di dati BLOB di Azure. Per informazioni sulle proprietà del set di dati BLOB di Azure, vedere l'articolo [Azure blob connector](connector-azure-blob-storage.md#dataset-properties) (Connettore BLOB di Azure). 

Definire un set di dati che rappresenta i dati di origine nel BLOB di Azure. Questo set di dati BLOB fa riferimento al servizio collegato di Archiviazione di Azure creato nel passaggio precedente.

```python
#Create Dataset Input
ds_name = 'ds_in'
ds_ls = LinkedServiceReference(ls_name)
blob_path= 'adfv2tutorial/'
blob_filename = 'input.txt'
ds_azure_blob= AzureBlobDataset(ds_ls, folder_path=blob_path, file_name = blob_filename)
ds = adf_client.datasets.create_or_update(rg_name, df_name, ds_name, ds_azure_blob)
print_item(ds)
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>Creare un set di dati per il BLOB di Azure sink
Aggiungere il codice seguente al metodo Main per creare un set di dati BLOB di Azure. Per informazioni sulle proprietà del set di dati BLOB di Azure, vedere l'articolo [Azure blob connector](connector-azure-blob-storage.md#dataset-properties) (Connettore BLOB di Azure). 

Definire un set di dati che rappresenta i dati di origine nel BLOB di Azure. Questo set di dati BLOB fa riferimento al servizio collegato di Archiviazione di Azure creato nel passaggio precedente.

```python
#Create Dataset Output
dsOut_name = 'ds_out'
output_blobpath = 'output/'
dsOut_azure_blob = AzureBlobDataset(ds_ls, folder_path=output_blobpath)
dsOut = adf_client.datasets.create_or_update(rg_name, df_name, dsOut_name, dsOut_azure_blob)
print_item(dsOut)
```

## <a name="create-a-pipeline"></a>Creare una pipeline

Aggiungere il codice seguente al metodo **Main** per creare una **pipeline con un'attività di copia**.

```python
#Create 1st activity: Copy Activity
act_name =  'copyBlobtoBlob'
blob_source = BlobSource()
blob_sink = BlobSink()
dsin_ref = DatasetReference(ds_name)
dsOut_ref = DatasetReference(dsOut_name)
copy_activity = CopyActivity(act_name,inputs=[dsin_ref], outputs=[dsOut_ref], source=blob_source, sink=blob_sink)

#Create Pipeline
p_name =  'copyPipeline'
params_for_pipeline = {}
p_obj = PipelineResource(activities=[copy_activity], parameters=params_for_pipeline)
p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
print_item(p)
```


## <a name="create-a-pipeline-run"></a>Creare un'esecuzione della pipeline

Aggiungere il codice seguente al metodo **Main** per **attivare un'esecuzione della pipeline**.

```python
#Create Pipeline Run
print(adf_client.pipelines.create_run(rg_name, df_name, p_name,
    {
    }
))
```

## <a name="run-the-code"></a>Eseguire il codice
Compilare e avviare l'applicazione, quindi verificare l'esecuzione della pipeline.

La console stampa lo stato di creazione della data factory, del servizio collegato, dei set di dati, della pipeline e dell'esecuzione della pipeline. Attendere fino a quando non vengono visualizzati i dettagli sull'esecuzione dell'attività di copia con le dimensioni dei dati letti/scritti. Usare quindi strumenti come [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) per verificare che i BLOB siano stati copiati da "inputBlobPath" a "outputBlobPath", come specificato nelle variabili.


## <a name="clean-up-resources"></a>Pulire le risorse
Per eliminare una data factory a livello di codice, aggiungere al programma le righe di codice seguenti: 

```csharp
adf_client.data_factories.delete(rg_name, df_name)
```

## <a name="next-steps"></a>Passaggi successivi
La pipeline in questo esempio copia i dati da una posizione a un'altra in un archivio BLOB di Azure. Per informazioni sull'uso di Data Factory in più scenari, fare riferimento alle [esercitazioni](tutorial-copy-data-dot-net.md). 
