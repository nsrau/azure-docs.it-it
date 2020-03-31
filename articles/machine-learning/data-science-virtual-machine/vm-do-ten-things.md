---
title: Esplorare i dati e il modello in Windows
titleSuffix: Azure Data Science Virtual Machine
description: Eseguire attività di esplorazione e modellazione dei dati nella macchina virtuale di analisi scientifica dei dati di Windows.Perform data exploration and modeling tasks on the Windows Data Science Virtual Machine.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 147f43148c0f804adf70f1a792ba1b8a772fdae4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294486"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>Dieci cose da fare con la macchina virtuale per l'analisi scientifica dei dati di Windows

Windows Data Science Virtual Machine (DSVM) è un potente ambiente di sviluppo di data science in cui è possibile eseguire attività di esplorazione e modellazione dei dati. L'ambiente è già stato creato e fornito con diversi strumenti di analisi dei dati più diffusi che semplificano l'introduzione all'analisi per distribuzioni locali, cloud o ibride. 

DSVM lavora a stretto contatto con i servizi di Azure.The DSVM works closely with Azure services. Può leggere ed elaborare i dati già archiviati in Azure, in Azure SQL Data Warehouse, Azure Data Lake, Archiviazione di Azure o Database Cosmos di Azure.It can read and process data that's already stored on Azure, in Azure SQL Data Warehouse, Azure Data Lake, Azure Storage, or Azure Cosmos DB. Può anche sfruttare altri strumenti di analisi, ad esempio Azure Machine Learning e Azure Data Factory.It can also take advantage of other analytics tools, such as Azure Machine Learning and Azure Data Factory.

In questo articolo si apprenderà come usare DSVM per eseguire attività di data science e interagire con altri servizi di Azure.In this article, you'll learn how to use your DSVM to perform data science tasks and interact with other Azure services. Ecco alcune attività che è possibile eseguire con DSVM:

- Esplora i dati e sviluppa i modelli in locale su DSVM usando Microsoft Machine Learning Server e Python.
- Usare un blocco appunti di Jupyter per sperimentare i dati in un browser usando Python 2, Python 3 e Microsoft R. (Microsoft R è una versione di R per l'azienda progettata per le prestazioni).
- Distribuire modelli basati su R e Python in Azure Machine Learning in modo che le applicazioni client possano accedere ai modelli usando una semplice interfaccia del servizio Web.Deploy models built through R and Python on Azure Machine Learning so client applications can access your models by using a simple web service interface.
- Amministrare le risorse di Azure usando il portale di Azure o PowerShell.Administer your Azure resources by using the Azure portal or PowerShell.
- Estendi lo spazio di archiviazione e condividi set di dati/codice su larga scala in tutto il team creando una condivisione File di Azure come unità installabile nella dSVM.
- Condividi il codice con il tuo team usando GitHub. Accedere al repository utilizzando i client Git preinstallati: Git Bash e Git GUI.
- Accedere ai servizi di dati e analisi di Azure come Archiviazione BLOB di Azure, Azure Data Lake, Azure HDInsight (Hadoop), database Cosmos di Azure, Data warehouse SQL di Azure e Database SQL di Azure.Access Azure data and analytics services like Azure Blob storage, Azure Data Lake, Azure HDInsight (Hadoop), Azure Cosmos DB, Azure SQL Data Warehouse, and Azure SQL Database.
- Creare report e un dashboard usando l'istanza di Power BI Desktop preinstallata nella dSVM e distribuirli nel cloud.
- Ridimensiona dinamicamente il tuo DSVM per soddisfare le esigenze del tuo progetto.
- Installare strumenti aggiuntivi nella macchina virtuale.   

> [!NOTE]
> Ulteriori costi di utilizzo si applicano a molti dei servizi di analisi e archiviazione dati elencati in questo articolo. Per informazioni dettagliate, vedere la pagina [dei prezzi di Azure.For details,](https://azure.microsoft.com/pricing/) see the Azure pricing page.
> 
> 

## <a name="prerequisites"></a>Prerequisiti

* È necessaria una sottoscrizione di Azure. È possibile [iscriversi per una versione di valutazione gratuita di Azure](https://azure.microsoft.com/free/).
* Le istruzioni per il provisioning di una macchina virtuale di analisi scientifica dei dati nel portale di Azure sono disponibili in [Creazione di una macchina virtuale.](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016)


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Esplora i dati e sviluppa modelli con Microsoft Machine Learning Server
È possibile usare linguaggi come R e Python per eseguire l'analisi dei dati direttamente in DSVM.

Per R, è possibile usare un IDE come RStudio che è disponibile nel menu Start o sul desktop. Or you can use R Tools for Visual Studio. Microsoft ha fornito librerie aggiuntive in cima al CRAN R open-source per consentire l'analisi scalabile e la possibilità di analizzare i dati più grandi della dimensione della memoria consentita nell'analisi in blocchi paralleli. 

Per Python è possibile usare un IDE come Visual Studio Community Edition, in cui è preinstallata l'estensione Python Tools for Visual Studio (PTVS). Per impostazione predefinita, solo Python 3.6, l'ambiente Conda radice, è configurato su PTVS. Per abilitare Anaconda Python 2.7, attenersi alla seguente procedura:

1. Creare ambienti personalizzati per ogni versione accedendo a **Strumenti** > Python**Tools Python** > **Environments**, quindi selezionando **.**
1. Dare una descrizione e impostare il percorso del prefisso dell'ambiente come **c:**
1. Selezionare **Rileva** > automaticamente**Applica** per salvare l'ambiente.

Vedere la documentazione di [PTVS](https://aka.ms/ptvsdocs) per ulteriori dettagli su come creare ambienti Python.

Ora sei impostato per creare un nuovo progetto Python. Vai a **File** > **New** > **Project** > **Python** e seleziona il tipo di applicazione Python che stai creando. È possibile impostare l'ambiente Python per il progetto corrente sulla versione desiderata (Python 2.7 o 3.6) facendo clic con il pulsante destro del mouse su **ambienti Python** e quindi selezionando **Aggiungi/Rimuovi ambienti Python**. Ulteriori informazioni sull'utilizzo di PTVS sono disponibili nella documentazione del [prodotto.](https://aka.ms/ptvsdocs)

## <a name="use-jupyter-notebooks"></a>Usare i notebook di Jupyter
Il blocco appunti Jupyter fornisce un IDE basato su browser per l'esplorazione e la modellazione dei dati. È possibile utilizzare Python 2, Python 3 o R (sia open source che Microsoft R Server) in un blocco appunti Jupyter.You can use Python 2, Python 3 or R (both open source and Microsoft R Server) in a Jupyter notebook.

Per avviare Jupyter Notebook, selezionare l'icona **Jupyter Notebook** nel menu **Start** o sul desktop. Nel prompt dei comandi DSVM è ```jupyter notebook``` inoltre possibile eseguire il comando dalla directory in cui sono presenti blocchi appunti esistenti o da cui si desidera creare nuovi blocchi appunti.  

Dopo aver avviato Jupyter, verrà visualizzata una directory che contiene alcuni blocchi appunti di esempio preconfezionati nella dSVM. A questo punto è possibile:

* Selezionare il blocco appunti per visualizzare il codice.
* Eseguire ogni cella selezionando Maiusc-Invio.
* Eseguire l'intero blocco appunti selezionando **Cell** > **Run**.
* Creare un nuovo blocco appunti selezionando l'icona Jupyter (angolo superiore sinistro), selezionando il pulsante **Nuovo** a destra e quindi scegliendo la lingua del blocco appunti (nota anche come kernel).   

> [!NOTE]
> Attualmente sono supportati Python 2.7, Python 3.6, R, Julia e PySpark kernel in Jupyter. Il kernel R supporta la programmazione sia in R open source che in Microsoft R.   
> 
> 

Nel blocco appunti è possibile esplorare i dati, compilare il modello e testare il modello usando le librerie scelte.

## <a name="train-and-deploy-models-by-using-azure-machine-learning"></a>Eseguire il training e distribuire modelli usando Azure Machine Learning
Dopo aver creato e convalidato il modello, il passaggio successivo consiste in genere nel distribuirlo nell'ambiente di produzione. Questo passaggio consente alle applicazioni client di richiamare le stime del modello in tempo reale o in modalità batch. Azure Machine Learning offre un meccanismo per rendere operativo il modello compilato in R o Python.

Quando si esegue l'operativiizzazione del modello in Azure Machine Learning, viene esposto un servizio Web.When you operationalize your model in Azure Machine Learning, a web service is exposed. Consente ai client di effettuare chiamate REST che passano parametri di input e ricevere stime dal modello come output.

### <a name="build-and-operationalize-python-models"></a>Creare e rendere operativi i modelli PythonBuild and operationalize Python models
Ecco un frammento di codice sviluppato in un blocco appunti Python Jupyter che crea un modello semplice usando la libreria Scikit-learn:Here's a snippet of code developed in a Python Jupyter notebook that builds a simple model by using the Scikit-learn library:

```python
# IRIS classification
from sklearn import datasets
from sklearn import svm
clf = svm.SVC()
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)
```

Il metodo usato per distribuire i modelli Python in Azure Machine Learning esegue il wrapping della stima del modello in una funzione e lo decora con gli attributi forniti dalla libreria Python di Azure Machine Learning preinstallata. Gli attributi indicano l'ID dell'area di lavoro di Azure Machine Learning, la chiave API e i parametri di input e restituzione.  

```python
from azureml import services
@services.publish(workspaceid, auth_token)
@services.types(sep_l=float, sep_w=float, pet_l=float, pet_w=float)
@services.returns(int)  # 0, or 1, or 2
def predictIris(sep_l, sep_w, pet_l, pet_w):
    inputArray = [sep_l, sep_w, pet_l, pet_w]


return clf.predict(inputArray)
```

Un client può ora effettuare chiamate al servizio Web. I wrapper pratici costruiscono le richieste dell'API REST. Ecco il codice di esempio per usare il servizio Web:Here's sample code to consume the web service:

```python
# Consume through web service URL and keys
from azureml import services
@services.service(url, api_key)
@services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
@services.returns(float)
def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
pass

IrisPredictor(3,2,3,4)
```

> [!NOTE]
> Attualmente, la libreria di Azure Machine Learning è supportata solo in Python 2.7.Currently, the Azure Machine Learning library is supported only on Python 2.7.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>Creare e rendere operativi i modelli RBuild and operationalize R models
È possibile distribuire modelli R basati sulla macchina virtuale di data science o altrove in Azure Machine Learning in un modo simile a come viene eseguito per Python.You can deploy R models built on the Data Science Virtual Machine or elsewhere onto Azure Machine Learning in a way that's similar as it's done for Python. I passaggi necessari sono i seguenti:

1. Creare un file settings.json per fornire l'ID dell'area di lavoro e il token di autenticazione. 
2. Scrivere un wrapper per la funzione di stima del modello.
3. Chiamare ```publishWebService``` la libreria di Azure Machine Learning per passare il wrapper della funzione.  

Usare la procedura e i frammenti di codice seguenti per configurare, compilare, pubblicare e usare un modello come servizio Web in Azure Machine Learning.Use the following procedure and code snippets to set up, build, publish, and consume a model as a web service in Azure Machine Learning.

#### <a name="set-up"></a>Configurare

Creare un file settings.json ```.azureml``` in una directory denominata nella home directory. Immettere i parametri dall'area di lavoro di Azure Machine Learning.Enter the parameters from your Azure Machine Learning workspace.

Ecco la struttura del file settings.json:Here's the settings.json file structure:

```json
{"workspace":{
"id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
"authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
}}
```

#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>Compilare un modello in R e pubblicarlo in Azure Machine Learning

```r
library(AzureML)
ws <- workspace(config="~/.azureml/settings.json")

if(!require("lme4")) install.packages("lme4")
library(lme4)
set.seed(1)
train <- sleepstudy[sample(nrow(sleepstudy), 120),]
m <- lm(Reaction ~ Days + Subject, data = train)

# Define a prediction function to publish based on the model:
sleepyPredict <- function(newdata){
        predict(m, newdata=newdata)
}

ep <- publishWebService(ws, fun = sleepyPredict, name="sleepy lm", inputSchema = sleepstudy, data.frame=TRUE)
```

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Utilizzare il modello distribuito in Azure Machine Learning
Per usare il modello da un'applicazione client, usare la libreria di Azure Machine Learning per cercare il servizio Web pubblicato in base al nome. Utilizzare `services` la chiamata API per determinare l'endpoint. Sarà quindi sufficiente chiamare la funzione `consume` e passare il frame di dati di cui eseguire la stima.

Usare il codice seguente per usare il modello pubblicato come servizio Web di Azure Machine Learning:Use the following code to consume the model published as an Azure Machine Learning web service:

```r
library(AzureML)
library(lme4)
ws <- workspace(config="~/.azureml/settings.json")

s <-  services(ws, name = "sleepy lm")
s <- tail(s, 1) # use the last published function, in case of duplicate function names

ep <- endpoints(ws, s)

# Try this out, and compare with raw data
ans = consume(ep, sleepstudy)$ans
```

Per altre informazioni sui [pacchetti R in Machine Learning Studio](/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning).

## <a name="manage-azure-resources"></a>Gestire le risorse di Azure
DSVM non consente solo di compilare la soluzione di analisi in locale nella macchina virtuale. Consente inoltre di accedere ai servizi nella piattaforma cloud di Azure.It also allows you to access services on the Azure cloud platform. Azure provides several compute, storage, data analytics, and other services that you can administer and access from your DSVM.

Per amministrare la sottoscrizione di Azure e le risorse cloud, sono disponibili due opzioni:To administer your Azure subscription and cloud resources, you have two options:
+ Usare il browser e passare al [portale di Azure](https://portal.azure.com).

+ Usare gli script di PowerShell.Use PowerShell scripts. Eseguire Azure PowerShell da un collegamento sul desktop o dal menu **Start.Run** Azure PowerShell from a shortcut on the desktop or from the Start menu. Per informazioni [dettagliate,](../../powershell-azure-resource-manager.md) vedere la documentazione di Microsoft Azure PowerShell.See the Microsoft Azure PowerShell documentation for full details. 

## <a name="extend-storage-by-using-shared-file-systems"></a>Estendere lo spazio di archiviazione utilizzando file system condivisi
I data scientist possono condividere grandi set di dati, codice o alte risorse all'interno del team. Il DSVM dispone di circa 45 GB di spazio disponibile. Per estendere lo spazio di archiviazione, è possibile usare File di Azure e montarlo in una o più istanze DSVM o accedervi tramite un'API REST. È anche possibile usare il portale di [Azure](../../virtual-machines/windows/attach-managed-disk-portal.md) o [Azure PowerShell](../../virtual-machines/windows/attach-disk-ps.md) per aggiungere dischi dati dedicati aggiuntivi. 

> [!NOTE]
> Lo spazio massimo nella condivisione File di Azure è 5 TB. Il limite di dimensione per ogni file è 1 TB. 

È possibile usare questo script in Azure PowerShell per creare una condivisione File di Azure:You can use this script in Azure PowerShell to create an Azure Files share:

```powershell
# Authenticate to Azure.
Connect-AzAccount
# Select your subscription
Get-AzSubscription –SubscriptionName "<your subscription name>" | Select-AzSubscription
# Create a new resource group.
New-AzResourceGroup -Name <dsvmdatarg>
# Create a new storage account. You can reuse existing storage account if you want.
New-AzStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
# Set your current working storage account
Set-AzCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

# Create an Azure Files share
$s = New-AzStorageShare <<teamsharename>>
# Create a directory under the file share. You can give it any name
New-AzStorageDirectory -Share $s -Path <directory name>
# List the share to confirm that everything worked
Get-AzStorageFile -Share $s
```

Dopo aver creato una condivisione File di Azure, è possibile montarla in qualsiasi macchina virtuale in Azure.Now that you have created an Azure Files share, you can mount it in any virtual machine in Azure. È consigliabile inserire la macchina virtuale nello stesso data center di Azure dell'account di archiviazione, per evitare costi di latenza e trasferimento dei dati. Ecco i comandi di Azure PowerShell per montare l'unità in DSVM:Here are the Azure PowerShell commands to mount the drive on the DSVM:

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

Ora è possibile accedere a questa unità come a qualsiasi altra unità della VM.

## <a name="share-code-in-github"></a>Condividere il codice in GitHubShare code in GitHub
GitHub è un repository di codice in cui è possibile trovare esempi di codice e origini per vari strumenti usando tecnologie condivise dalla comunità di sviluppatori. Usa Git come tecnologia per tenere traccia delle versioni dei file di codice e per archiviarle. GitHub è anche una piattaforma in cui è possibile creare il proprio repository per archiviare il codice condiviso del team e la documentazione, implementare il controllo della versione e controllare chi ha accesso per visualizzare e contribuire al codice. 

Per altre informazioni sull'uso di Git, visitare le [pagine della guida di GitHub](https://help.github.com/). È possibile utilizzare GitHub come uno dei modi per collaborare con il team, utilizzare il codice sviluppato dalla community e contribuire codice alla community.

DSVM viene caricato con gli strumenti client nella riga di comando e nella GUI per accedere al repository GitHub. Lo strumento da riga di comando che funziona con Git e GitHub è denominato Git Bash.The command-line tool that works with Git and GitHub is called Git Bash. Visual Studio è installato in DSVM e dispone delle estensioni Git. È possibile trovare le icone per questi strumenti nel menu **Start** e sul desktop.

Per scaricare il codice da un repository GitHub, usare il comando ```git clone```. Ad esempio, per scaricare il repository di data science pubblicato da Microsoft nella directory corrente, è possibile eseguire il comando seguente in Git Bash:

    git clone https://github.com/Azure/DataScienceVM.git

In Visual Studio è possibile eseguire la stessa operazione di clonazione. La schermata seguente mostra come accedere agli strumenti Git e GitHub in Visual Studio:The following screenshot shows how to access Git and GitHub tools in Visual Studio:

![Screenshot di Visual Studio con la connessione GitHub visualizzata](./media/vm-do-ten-things/VSGit.PNG)

È possibile trovare ulteriori informazioni sull'utilizzo di Git per lavorare con il repository GitHub dalle risorse disponibili su github.com. Il [foglio informativo](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) è un riferimento utile.

## <a name="access-azure-data-and-analytics-services"></a>Accedere ai servizi di dati e analisi di AzureAccess Azure data and analytics services
### <a name="azure-blob-storage"></a>Archiviazione BLOB di Azure
L'archiviazione BLOB di Azure è un servizio di archiviazione cloud affidabile ed economico per grandi e piccoli dati. Questa sezione descrive come spostare i dati nell'archiviazione BLOB e accedere ai dati archiviati in un BLOB di Azure.This section describes how you can move data to Blob storage and access data stored in an Azure blob.

#### <a name="prerequisites"></a>Prerequisiti

* Creare l'account di archiviazione BLOB di Azure dal portale di Azure.Create your Azure Blob storage account from the [Azure portal.](https://portal.azure.com)

   ![Screenshot del processo di creazione dell'account di archiviazione nel portale di Azure](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Verificare che lo strumento AzCopy della ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```riga di comando sia preinstallato: . La directory che contiene azcopy.exe è già presente nella variabile di ambiente PATH, pertanto è possibile evitare di digitare il percorso completo del comando quando si esegue questo strumento. Per ulteriori informazioni sullo strumento AzCopy, consultate la documentazione di [AzCopy](../../storage/common/storage-use-azcopy.md).
* Avviare lo strumento Esplora archivi di Azure. È possibile scaricarlo dalla [pagina Web di Storage Explorer](https://storageexplorer.com/). 

   ![Screenshot dell'accesso di Azure Storage Explorer a un account di archiviazione](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>Spostare i dati da una macchina virtuale a un BLOB di Azure: AzCopyMove data from a VM to an Azure blob: AzCopy

Per spostare i dati tra i file locali e l'archiviazione BLOB, è possibile usare AzCopy nella riga di comando o in PowerShell:To move data between your local files and Blob storage, you can use AzCopy on the command line or in PowerShell:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Sostituire **La cartella C:,** con il percorso in cui è archiviato il file, **mystorageaccount** con il nome dell'account di archiviazione BLOB, **mycontainer** con il nome del contenitore e la chiave dell'account di **archiviazione** con la chiave di accesso di archiviazione BLOB. È possibile trovare le credenziali dell'account di archiviazione nel portale di Azure.You can find your storage account credentials in the [Azure portal](https://portal.azure.com).

Eseguire il comando AzCopy in PowerShell o da un prompt dei comandi. Di seguito è riportato un esempio di utilizzo del comando AzCopy:Here is some example usage of the AzCopy command:

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

Dopo aver eseguito il comando AzCopy per copiare in un BLOB di Azure, il file verrà visualizzato in Esplora archivi di Azure.After you run the AzCopy command to copy to an Azure blob, your file will appear in Azure Storage Explorer.

![Screenshot dell'account di archiviazione, che visualizza il file CSV caricato](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>Spostare dati da una macchina virtuale a un BLOB di Azure: Esplora archivi di AzureMove data from a VM to an Azure blob: Azure Storage Explorer

È anche possibile caricare dati dal file locale nella macchina virtuale usando Azure Storage Explorer:You can also upload data from the local file in your VM by using Azure Storage Explorer:

* Per caricare i dati in un contenitore, selezionare il contenitore di destinazione e fare clic sul pulsante **Carica.To upload** data to a container, select the target container and select the Upload button. ![Screenshot del pulsante di caricamento in Esplora archivi di Azure](./media/vm-do-ten-things/storage-accounts.png)
* Selezionare i trattini (**...**) a destra della casella **File,** selezionare uno o più file da caricare dal file system e selezionare **Carica** per iniziare a caricare i file. ![Screenshot della finestra di dialogo Carica file](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-machine-learning-reader-module"></a>Leggere i dati da un BLOB di Azure: Modulo lettore di Machine LearningRead data from an Azure blob: Machine Learning reader module

In Azure Machine Learning Studio è possibile usare il modulo Importa dati per leggere i dati dal BLOB.

![Screenshot del modulo Import Data in Machine Learning Studio](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>Leggere dati da un BLOB di Azure: ODBC di PythonRead data from an Azure blob: Python ODBC

È possibile usare la libreria BlobService per leggere i dati direttamente da un BLOB in un blocco appunti di Jupyter o in un programma Python.You can use the BlobService library to read data directly from a blob in a Jupyter notebook or in a Python program.

In primo luogo, importare i pacchetti richiesti:

```python
import pandas as pd
from pandas import Series, DataFrame
import numpy as np
import matplotlib.pyplot as plt
from time import time
import pyodbc
import os
from azure.storage.blob import BlobService
import tables
import time
import zipfile
import random
```

Collegare quindi le credenziali dell'account di archiviazione BLOB e leggere i dati dal BLOB:Then, plug in your Blob storage account credentials and read data from the blob:

```python
CONTAINERNAME = 'xxx'
STORAGEACCOUNTNAME = 'xxxx'
STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
localfilename = 'trip_data_1.csv'
LOCALDIRECTORY = os.getcwd()
LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

#download from blob
t1 = time.time()
blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
t2 = time.time()
print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

#unzip downloaded files if needed
#with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
#    z.extractall(LOCALDIRECTORY)

df1 = pd.read_csv(LOCALFILE, header=0)
df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
print 'the size of the data is: %d rows and  %d columns' % df1.shape
```

I dati vengono letti come frame di dati:The data is read as a data frame:

![Screenshot delle prime 10 righe di dati](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Azure Data Lake Storage è un repository di iperscalabili per i carichi di lavoro di analisi dei Big Data ed è compatibile con Hadoop Distributed File System (HDFS). Funziona con Hadoop, Spark e Azure Data Lake Analytics. In this section, you'll learn how you can move data into Azure Data Lake Storage and run analytics by using Azure Data Lake Analytics.

#### <a name="prerequisites"></a>Prerequisiti

* Creare l'istanza di Azure Data Lake Analytics nel portale di Azure.Create your Azure Data Lake Analytics instance in the [Azure portal](https://portal.azure.com).

   ![Screenshot della creazione di un'istanza di Data Lake Analytics dal portale di Azure](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* Gli [strumenti Di Azure Data Lake e Stream Analytics Tools per Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) sono già installati in Visual Studio Community Edition nella macchina virtuale. Dopo aver avviato Visual Studio e aver eseguito l'accesso alla sottoscrizione di Azure, l'account e l'archiviazione di Azure Data Analytics dovrebbero essere visualizzati nel riquadro sinistro di Visual Studio.After you start Visual Studio and sign in to your Azure subscription, you should see your Azure Data Analytics account and storage in the left panel of Visual Studio.

   ![Screenshot del plug-in per gli strumenti Data Lake in Visual Studio](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

#### <a name="move-data-from-a-vm-to-data-lake-azure-data-lake-explorer"></a>Spostare i dati da una macchina virtuale a Data Lake: Azure Data Lake ExplorerMove data from a VM to Data Lake: Azure Data Lake Explorer

È possibile usare Azure Data Lake Explorer per caricare i [dati dai file locali della macchina virtuale in Archiviazione data lake.](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)

È anche possibile compilare una pipeline di dati per rendere operativo lo spostamento dei dati da o verso Azure Data Lake usando [Azure Data Factory.](https://azure.microsoft.com/services/data-factory/) [In questo articolo](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) vengono illustrati i passaggi per compilare le pipeline di dati.

#### <a name="read-data-from-an-azure-blob-to-data-lake-u-sql"></a>Leggere i dati da un BLOB di Azure a Data Lake: U-SQLRead data from an Azure blob to Data Lake: U-SQL

Se i dati risiedono nell'archivio BLOB di Azure, è possibile leggere direttamente i dati da un BLOB di Azure in una query U-SQL. Prima di comporre la query U-SQL, assicurarsi che l'account di archiviazione BLOB sia collegato all'istanza di Azure Data Lake. Passare al portale di Azure, trovare il dashboard di Analisi di Azure Data Lake, selezionare **Aggiungi origine dati**, selezionare un tipo di archiviazione Archiviazione di Azure e collegare il nome e la chiave dell'account di archiviazione di Azure.Go to the Azure Azure Data Lake Analytics dashboard, select Add Data Source , select a storage type of Azure **Storage,** and plug in your Azure storage account name and key. È quindi possibile fare riferimento ai dati archiviati nell'account di archiviazione.

![Screenshot della finestra di dialogo Aggiungi origine dati](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

In Visual Studio è possibile leggere i dati dall'archiviazione BLOB, modificare i dati, progettare le funzionalità e inviare i dati risultanti all'archiviazione BLOB di Azure Data Lake o Azure.In Visual Studio, you can read data from Blob storage, manipulate data, engineer features, and send the resulting data to either Azure Data Lake or Azure Blob storage. Quando si fa riferimento ai dati nell'archivio BLOB, usare **wasb://**. Quando si fa riferimento ai dati in Azure Data Lake, usare **swbhdfs://**.

È possibile utilizzare le query U-SQL seguenti in Visual Studio:You can use the following U-SQL queries in Visual Studio:

```usql
@a =
    EXTRACT medallion string,
            hack_license string,
            vendor_id string,
            rate_code string,
            store_and_fwd_flag string,
            pickup_datetime string,
            dropoff_datetime string,
            passenger_count int,
            trip_time_in_secs double,
            trip_distance double,
            pickup_longitude string,
            pickup_latitude string,
            dropoff_longitude string,
            dropoff_latitude string

    FROM "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Input Data File Name>"
    USING Extractors.Csv();

@b =
    SELECT vendor_id,
    COUNT(medallion) AS cnt_medallion,
    SUM(passenger_count) AS cnt_passenger,
    AVG(trip_distance) AS avg_trip_dist,
    MIN(trip_distance) AS min_trip_dist,
    MAX(trip_distance) AS max_trip_dist,
    AVG(trip_time_in_secs) AS avg_trip_time
    FROM @a
    GROUP BY vendor_id;

OUTPUT @b   
TO "swebhdfs://<Azure Data Lake Storage Account Name>.azuredatalakestore.net/<Folder Name>/<Output Data File Name>"
USING Outputters.Csv();

OUTPUT @b   
TO "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Output Data File Name>"
USING Outputters.Csv();
```

Dopo l'invio della query al server, un diagramma mostra lo stato del processo.

![Screenshot del diagramma di stato del processo](./media/vm-do-ten-things/USQL_Job_Status.PNG)

#### <a name="query-data-in-data-lake-u-sql"></a>Effettuare una query dei dati in Data Lake: U-SQL

Dopo aver ingerito il set di dati in Azure Data Lake, è possibile usare il [linguaggio U-SQL](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) per eseguire query ed esplorare i dati. Il linguaggio U-SQL è simile a T-SQL, ma combina alcune funzionalità di C, in modo che gli utenti possano scrivere moduli personalizzati e funzioni definite dall'utente. È possibile usare gli script del passaggio precedente.

Dopo che la query è stata inviata al server, tripdata_summary. CSV viene visualizzato in Azure Data Lake Explorer. È possibile visualizzare l'anteprima dei dati facendo clic con il pulsante destro del mouse sul file.

![Screenshot del file CSV in Esplora dati Lake](./media/vm-do-ten-things/USQL_create_summary.png)

Vengono visualizzate le informazioni sul file:

![Screenshot delle informazioni di riepilogo del file](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>Cluster HDInsight Hadoop
Azure HDInsight è un servizio Gestito Apache Hadoop, Spark, HBase e Storm nel cloud. È possibile usare facilmente i cluster di Azure HDInsight dalla macchina virtuale di analisi scientifica dei dati.

#### <a name="prerequisites"></a>Prerequisiti

* Creare l'account di archiviazione BLOB di Azure dal portale di Azure.Create your Azure Blob storage account from the [Azure portal.](https://portal.azure.com) Questo account di archiviazione viene usato per archiviare i dati per i cluster HDInsight.

   ![Screenshot della creazione di un account di archiviazione dal portale di Azure](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Personalizzare i cluster Hadoop di Azure HDInsight dal portale di Azure.Customize Azure HDInsight Hadoop clusters from the [Azure portal](../team-data-science-process/customize-hadoop-cluster.md).
  
   Collegare l'account di archiviazione creato con il cluster HDInsight quando viene creato. Questo account di archiviazione viene usato per accedere ai dati che possono essere elaborati all'interno del cluster.

   ![Selezioni per il collegamento dell'account di archiviazione creato con un cluster HDInsightSelections for linking the storage account created with an HDInsight cluster](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* Abilitare l'accesso Desktop remoto al nodo head del cluster dopo averlo creato. Ricordare le credenziali di accesso remoto specificate qui, perché saranno necessarie nella procedura successiva.

   ![Pulsante Desktop remoto per abilitare l'accesso remoto al cluster HDInsight](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Creare un'area di lavoro di Azure Machine Learning. Gli esperimenti di Machine Learning vengono archiviati in questa area di lavoro di Machine Learning.Your Machine Learning experiments are stored in this Machine Learning workspace. Selezionare le opzioni evidenziate nel portale, come illustrato nella schermata seguente:

   ![Creare un'area di lavoro di Machine Learning di Azure](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Immettere i parametri per l'area di lavoro.

   ![Immettere i parametri dell'area di lavoro di Machine Learning](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* Caricare i dati utilizzando IPython Notebook. Importare i pacchetti necessari, collegare le credenziali, creare un database nell'account di archiviazione e quindi caricare i dati nei cluster HDI.

```python
# Import required packages
import pyodbc
import time as time
import json
import os
import urllib
import urllib2
import warnings
import re
import pandas as pd
import matplotlib.pyplot as plt
from azure.storage.blob import BlobService
warnings.filterwarnings("ignore", category=UserWarning, module='urllib2')


# Create the connection to Hive by using ODBC
SERVER_NAME = 'xxx.azurehdinsight.net'
DATABASE_NAME = 'nyctaxidb'
USERID = 'xxx'
PASSWORD = 'xxxx'
DB_DRIVER = 'Microsoft Hive ODBC Driver'
driver = 'DRIVER={' + DB_DRIVER + '}'
server = 'Host=' + SERVER_NAME + ';Port=443'
database = 'Schema=' + DATABASE_NAME
hiveserv = 'HiveServerType=2'
auth = 'AuthMech=6'
uid = 'UID=' + USERID
pwd = 'PWD=' + PASSWORD
CONNECTION_STRING = ';'.join(
    [driver, server, database, hiveserv, auth, uid, pwd])
connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
cursor = connection.cursor()


# Create the Hive database and tables
queryString = "create database if not exists nyctaxidb;"
cursor.execute(queryString)

queryString = """
                create external table if not exists nyctaxidb.trip
                (
                    medallion string,
                    hack_license string,
                    vendor_id string,
                    rate_code string,
                    store_and_fwd_flag string,
                    pickup_datetime string,
                    dropoff_datetime string,
                    passenger_count int,
                    trip_time_in_secs double,
                    trip_distance double,
                    pickup_longitude double,
                    pickup_latitude double,
                    dropoff_longitude double,
                    dropoff_latitude double)  
                PARTITIONED BY (month int)
                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');
            """
cursor.execute(queryString)

queryString = """
                create external table if not exists nyctaxidb.fare
                (
                    medallion string,
                    hack_license string,
                    vendor_id string,
                    pickup_datetime string,
                    payment_type string,
                    fare_amount double,
                    surcharge double,
                    mta_tax double,
                    tip_amount double,
                    tolls_amount double,
                    total_amount double)
                PARTITIONED BY (month int)
                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
            """
cursor.execute(queryString)


# Upload data from Blob storage to an HDI cluster
for i in range(1, 13):
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
```

In alternativa, è possibile seguire [questa procedura dettagliata](../team-data-science-process/hive-walkthrough.md) per caricare i dati di NYC Taxi nel cluster HDI. I passaggi più importanti includono:
  
* Usare AzCopy per scaricare i file CSV compressi dal BLOB pubblico nella cartella locale.
* Usare AzCopy per caricare i file CSV decompressi dalla cartella locale in un cluster HDI.
* Accedere al nodo centrale del cluster Hadoop e prepararsi per l'analisi esplorativa dei dati.

Dopo aver caricato i dati nel cluster HDI, è possibile archiviarli in Esplora archivi di Azure.After the data is loaded into the HDI cluster, you can check your data in Azure Storage Explorer. E il database nyctaxidb è stato creato nel cluster HDI.

#### <a name="data-exploration-hive-queries-in-python"></a>Esplorazione dei dati: query Hive in Python

Poiché i dati si trova in un cluster Hadoop, è possibile usare il pacchetto pyodbc per connettersi ai cluster Hadoop e interrogare i database usando Hive per eseguire l'esplorazione e la progettazione delle funzionalità. È possibile visualizzare le tabelle esistenti create nel passaggio dei prerequisiti.

```python
queryString = """
    show tables in nyctaxidb2;
    """
pd.read_sql(queryString, connection)
```

![Visualizzare tabelle esistenti](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Verrà ora esaminato il numero di record per ogni mese e le frequenze delle corse per le quali è stata lasciata una mancia o meno nella tabella delle corse:

```python
queryString = """
    select month, count(*) from nyctaxidb.trip group by month;
    """
results = pd.read_sql(queryString,connection)

%matplotlib inline

results.columns = ['month', 'trip_count']
df = results.copy()
df.index = df['month']
df['trip_count'].plot(kind='bar')
```

![Tracciato del numero di record in ogni mese](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

```python
queryString = """
    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;
    """
results = pd.read_sql(queryString, connection)

results.columns = ['tipped', 'trip_count']
df = results.copy()
df.index = df['tipped']
df['trip_count'].plot(kind='bar')
```

![Tracciato della frequenze delle mance](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

È inoltre possibile calcolare la distanza tra la posizione di prelievo e la posizione di consegna e quindi confrontarla con la distanza di viaggio.

```python
queryString = """
                select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
                    3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
                    *radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
                    *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
                    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
                    +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*
                    pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance
                    from nyctaxidb.trip
                    where month=1
                        and pickup_longitude between -90 and -30
                        and pickup_latitude between 30 and 90
                        and dropoff_longitude between -90 and -30
                        and dropoff_latitude between 30 and 90;
            """
results = pd.read_sql(queryString, connection)
results.head(5)
```

![Principali righe della tabella di prelievo e di rilascio](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

```python
results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                   'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
df = results.loc[results['trip_distance'] <= 100]  # remove outliers
df = df.loc[df['direct_distance'] <= 100]  # remove outliers
plt.scatter(df['direct_distance'], df['trip_distance'])
```

![Grafico della distanza di prelievo/consegna alla distanza di viaggio](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Ora prepariamo un set di dati downsampled (1 percent) per la modellazione. È possibile utilizzare questi dati nel modulo Lettore di Machine Learning.You can use this data in the Machine Learning reader module.

```python
queryString = """
create  table if not exists nyctaxi_downsampled_dataset_testNEW (
medallion string,
hack_license string,
vendor_id string,
rate_code string,
store_and_fwd_flag string,
pickup_datetime string,
dropoff_datetime string,
pickup_hour string,
pickup_week string,
weekday string,
passenger_count int,
trip_time_in_secs double,
trip_distance double,
pickup_longitude double,
pickup_latitude double,
dropoff_longitude double,
dropoff_latitude double,
direct_distance double,
payment_type string,
fare_amount double,
surcharge double,
mta_tax double,
tip_amount double,
tolls_amount double,
total_amount double,
tipped string,
tip_class string
)
row format delimited fields terminated by ','
lines terminated by '\\n'
stored as textfile;
"""
cursor.execute(queryString)
```

Ora inserire il contenuto del join nella tabella interna precedente.

```python
queryString = """
insert overwrite table nyctaxi_downsampled_dataset_testNEW
select
t.medallion,
t.hack_license,
t.vendor_id,
t.rate_code,
t.store_and_fwd_flag,
t.pickup_datetime,
t.dropoff_datetime,
hour(t.pickup_datetime) as pickup_hour,
weekofyear(t.pickup_datetime) as pickup_week,
from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
t.passenger_count,
t.trip_time_in_secs,
t.trip_distance,
t.pickup_longitude,
t.pickup_latitude,
t.dropoff_longitude,
t.dropoff_latitude,
t.direct_distance,
f.payment_type,
f.fare_amount,
f.surcharge,
f.mta_tax,
f.tip_amount,
f.tolls_amount,
f.total_amount,
if(tip_amount>0,1,0) as tipped,
if(tip_amount=0,0,
if(tip_amount>0 and tip_amount<=5,1,
if(tip_amount>5 and tip_amount<=10,2,
if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
from
(
select
medallion,
hack_license,
vendor_id,
rate_code,
store_and_fwd_flag,
pickup_datetime,
dropoff_datetime,
passenger_count,
trip_time_in_secs,
trip_distance,
pickup_longitude,
pickup_latitude,
dropoff_longitude,
dropoff_latitude,
3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
+cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance,
rand() as sample_key

from trip
where pickup_latitude between 30 and 90
    and pickup_longitude between -90 and -30
    and dropoff_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
)t
join
(
select
medallion,
hack_license,
vendor_id,
pickup_datetime,
payment_type,
fare_amount,
surcharge,
mta_tax,
tip_amount,
tolls_amount,
total_amount
from fare
)f
on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
where t.sample_key<=0.01
"""
cursor.execute(queryString)
```

Dopo un po', è possibile vedere che i dati sono stati caricati nei cluster Hadoop:

```python
queryString = """
    select * from nyctaxi_downsampled_dataset limit 10;
    """
cursor.execute(queryString)
pd.read_sql(queryString, connection)
```

![Prime righe dei dati dalla tabella](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

#### <a name="read-data-from-hdi-by-using-azure-machine-learning-studio-classic-reader-module"></a>Leggere i dati da HDI usando Azure Machine Learning Studio (classico): modulo lettoreRead data from HDI by using Azure Machine Learning Studio (classic): reader module

È anche possibile usare il modulo lettore in Azure Machine Learning Studio (classico) per accedere al database in un cluster Hadoop.You can also use the reader module in Azure Machine Learning Studio (classic) to access the database in a Hadoop cluster. Collegare le credenziali dei cluster HDI e dell'account di archiviazione di Azure per abilitare la creazione di modelli di Machine Learning usando un database in cluster HDI.

![Proprietà del modulo Reader](./media/vm-do-ten-things/AML_Reader_Hive.PNG)

È quindi possibile visualizzare il set di dati con punteggio:You can then view the scored dataset:

![Visualizzare il set di dati con i punteggi](./media/vm-do-ten-things/AML_Model_Results.PNG)

### <a name="azure-sql-data-warehouse-and-databases"></a>Database e database SQL di AzureAzure SQL Data Warehouse and databases
Azure SQL Data Warehouse è un data warehouse elastico come servizio con un'esperienza SQL Server di livello enterprise.

È possibile eseguire il provisioning del data warehouse SQL di Azure seguendo le istruzioni riportate in [questo articolo.](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) Dopo aver eseguito il provisioning del data warehouse SQL, è possibile usare [questa procedura dettagliata](../team-data-science-process/sqldw-walkthrough.md) per eseguire il caricamento, l'esplorazione e la modellazione dei dati utilizzando i dati all'interno del data warehouse SQL.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB è un database NoSQL sul cloud. È possibile utilizzarlo per lavorare con documenti come JSON e per archiviare ed eseguire query sui documenti.

Usare la procedura preliminare seguente per accedere a Azure Cosmos DB da DSVM:Use the following prerequisite steps to access Azure Cosmos DB from the DSVM:

1. Azure Cosmos DB Python SDK è già installato in DSVM. Per aggiornarlo, ```pip install pydocumentdb --upgrade``` eseguire da un prompt dei comandi.
2. Creare un account e un database di Azure Cosmos dal portale di [Azure.](https://portal.azure.com)
3. Scaricare lo strumento di migrazione dei dati di Azure Cosmos DB [dall'Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=53595) ed estrarre in una directory di propria scelta.
4. Importare dati JSON (dati vulcanici) archiviati in un [BLOB pubblico](https://cahandson.blob.core.windows.net/samples/volcano.json) in Azure Cosmos DB con i parametri di comando seguenti nello strumento di migrazione. (Usare dtui.exe dalla directory in cui è stato installato lo strumento di migrazione dei dati di Azure Cosmos DB.) Immettere il percorso di origine e di destinazione con i parametri seguenti:
   
    `/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

Dopo aver importato i dati, è possibile passare a Jupyter e aprire il blocco appunti intitolato *DocumentDBSample*. Contiene il codice Python per accedere a Azure Cosmos DB ed eseguire alcune query di base. Per altre informazioni su Azure Cosmos DB, visitare la pagina della [documentazione](https://docs.microsoft.com/azure/cosmos-db/)del servizio.

## <a name="use-power-bi-reports-and-dashboards"></a>Usare report e dashboard di Power BI 
È possibile visualizzare il file JSON Del vulcano dall'esempio di database Cosmos di Azure precedente in Power BI Desktop per ottenere informazioni visive sui dati. I passaggi dettagliati sono disponibili nell' [articolo relativo a Power BI](../../cosmos-db/powerbi-visualize.md). Ecco i passaggi principali:

1. Aprire Power BI Desktop e selezionare **Recupera dati**. Specificare l'URL come: `https://cahandson.blob.core.windows.net/samples/volcano.json`.
2. I record JSON vengono importati come elenco. Convertire l'elenco in una tabella in modo che Power BI possa usarlo.
4. Espandere le colonne selezionando l'icona di espansione (freccia).
5. Si noti che la posizione è un campo **Record.** Espandere il record e selezionare solo le coordinate. **Coordinata** è una colonna di elenco.
6. Aggiungere una nuova colonna per convertire la colonna delle coordinate dell'elenco in una colonna **LatLong** separata da virgole. Concatenare i due elementi nel campo dell'elenco di coordinate utilizzando la formula ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```.
7. Convertire la colonna **Quota altimetrica** in decimale e selezionare i pulsanti **Chiudi** e **Applica.**

Anziché eseguire i passaggi precedenti, è possibile incollare il codice seguente. Vengono eseguiti gli script usati nell'Editor avanzato in Power BI per scrivere le trasformazioni dei dati in un linguaggio di query.

```pqfl
let
    Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
    #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
    #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
    #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
    #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
in
    #"Changed Type"
```

I dati sono ora inclusi nel modello di dati di Power BI. L'istanza di Power BI Desktop dovrebbe essere visualizzata come segue:

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

È possibile iniziare a creare report e visualizzazioni utilizzando il modello di dati. È possibile seguire i passaggi descritti in questo articolo di [Power BI](../../cosmos-db/powerbi-visualize.md#build-the-reports) per creare un report.

## <a name="scale-the-dsvm-dynamically"></a>Ridimensionare la DSVM in modo dinamico 
È possibile scalare verso l'alto e verso il basso la DSVM per soddisfare le esigenze del progetto. Se non è necessario usare la macchina virtuale la sera o nei fine settimana, è possibile arrestare la macchina virtuale dal portale di [Azure.](https://portal.azure.com)

> [!NOTE]
> Si verificano costi di calcolo se si usa solo il pulsante di arresto per il sistema operativo nella macchina virtuale.  
> 
> 

Potrebbe essere necessario gestire alcune analisi su larga scala e richiedere più CPU, memoria o capacità del disco. In tal caso, è possibile trovare una scelta di dimensioni delle macchine virtuali in termini di core della CPU, istanze basate su GPU per il deep learning, la capacità di memoria e i tipi di disco (incluse le unità a stato solido) che soddisfano le esigenze di elaborazione e budget. L'elenco completo delle macchine virtuali, insieme ai relativi prezzi di calcolo orari, è disponibile nella pagina [dei prezzi di Macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/) di Azure.The full list of VMs, along with their hourly compute pricing, is available on the Azure Virtual Machines pricing page.

Analogamente, la necessità di capacità di elaborazione della macchina virtuale potrebbe diminuire. Ad esempio, è stato spostato un carico di lavoro principale in un cluster Hadoop o Spark. È quindi possibile ridurre il cluster dal portale di [Azure](https://portal.azure.com) e passare alle impostazioni dell'istanza della macchina virtuale. 

## <a name="add-more-tools"></a>Aggiungere altri strumenti
Gli strumenti predefiniti in DSVM sono in grado di soddisfare molte esigenze comuni di analisi dei dati. Ciò consente di risparmiare tempo perché non è necessario installare e configurare gli ambienti uno per uno. Inoltre, consente di risparmiare denaro, perché si paga solo per le risorse che si utilizzano.

È possibile usare altri servizi di analisi e dati di Azure profilati in questo articolo per migliorare l'ambiente di analisi. In alcuni casi, potrebbero essere necessari strumenti aggiuntivi, inclusi alcuni strumenti partner proprietari. Si dispone dell'accesso amministrativo completo sulla macchina virtuale per installare i nuovi strumenti necessari. È anche possibile installare pacchetti aggiuntivi in Python e in R non preinstallati. Per Python, è ```conda``` possibile ```pip```utilizzare o . Per R, è ```install.packages()``` possibile utilizzare nella console R oppure utilizzare l'IDE e selezionare **Pacchetti** > **di installazione pacchetti**.

## <a name="deep-learning"></a>Deep Learning

Oltre agli esempi basati su framework, è possibile ottenere una serie di procedure dettagliate complete che sono state convalidate nella dSVM. Queste procedure dettagliate consentono di avviare rapidamente lo sviluppo di applicazioni di deep learning in domini come la comprensione di immagini e testo/lingua.   


- Esecuzione di [reti neurali in framework diversi:](https://github.com/ilkarman/DeepLearningFrameworks)questa procedura dettagliata viene illustrato come eseguire la migrazione di codice da un framework a un altro. Viene inoltre illustrato come confrontare modelli e prestazioni di runtime tra framework. 

- [Guida dettagliata alla creazione di una soluzione end-to-end per individuare prodotti all'interno di immagini](https://github.com/Azure/cortana-intelligence-product-detection-from-images): il rilevamento immagine è una tecnica che consente di individuare e classificare gli oggetti all'interno delle immagini. Questa tecnologia ha il potenziale per portare enormi ricompense in molti domini di business reali. Ad esempio, i negozianti possono usare questa tecnica per determinare quale prodotto un cliente ha prelevato dallo scaffale. Queste informazioni a loro volta consentono ai negozi di gestire l'inventario dei prodotti. 

- [Deep learning per l'audio](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): Questa esercitazione mostra come eseguire il training di un modello di deep learning per il rilevamento di eventi audio sul set di [dati dei suoni urbani.](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html) Fornisce inoltre una panoramica su come lavorare con i dati audio.

- [Classificazione di documenti di testo](https://github.com/anargyri/lstm_han): questa procedura dettagliata viene illustrato come compilare ed eseguire il training di due architetture di rete neurale: rete di attenzione gerarchica e rete lSTM (Long Short Term Memory). Queste reti neurali usano l'API Keras per l'apprendimento avanzato per classificare i documenti di testo. Keras è un front-end a tre dei più popolari framework di deep learning: Microsoft Cognitive Toolkit, TensorFlow e Theano.

## <a name="summary"></a>Riepilogo
In questo articolo sono descritte alcune delle operazioni che è possibile eseguire nella macchina virtuale Microsoft Data Science.This article described some of the do you can do on the Microsoft Data Science Virtual Machine. Ci sono molte altre cose che puoi fare per rendere la DSVM un ambiente di analisi efficace.

