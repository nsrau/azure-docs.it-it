---
title: Esplorare i dati e il modello in Windows
titleSuffix: Azure Data Science Virtual Machine
description: Eseguire attività di esplorazione e modellazione dei dati nel Data Science Virtual Machine di Windows.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 6af9f2c80915745f995144c24ae1649fb02a31ae
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70192265"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>Dieci cose da fare con la macchina virtuale per l'analisi scientifica dei dati di Windows

Windows Data Science Virtual Machine (DSVM) è un potente ambiente di sviluppo data science in cui è possibile eseguire attività di esplorazione e modellazione dei dati. L'ambiente è già compilato e in bundle con diversi strumenti di analisi dei dati diffusi che consentono di iniziare a usare in modo semplice l'analisi per distribuzioni locali, cloud o ibride. 

Il DSVM funziona a stretto contatto con i servizi di Azure. Consente di leggere ed elaborare i dati già archiviati in Azure, in Azure SQL Data Warehouse, Azure Data Lake, archiviazione di Azure o Azure Cosmos DB. Può anche sfruttare altri strumenti di analisi, ad esempio Azure Machine Learning e Azure Data Factory.

In questo articolo si apprenderà come usare il DSVM per eseguire data science attività e interagire con altri servizi di Azure. Ecco alcune attività che è possibile eseguire con DSVM:

- Esplorare i dati e sviluppare modelli in locale in DSVM usando Microsoft Machine Learning Server e Python.
- Usare un notebook di Jupyter per sperimentare i dati in un browser usando Python 2, Python 3 e Microsoft R. Microsoft R è una versione di R pronta per l'uso aziendale progettata per le prestazioni.
- Distribuire i modelli creati tramite R e Python in Azure Machine Learning in modo che le applicazioni client possano accedere ai modelli usando un'interfaccia di servizio Web semplice.
- Amministrare le risorse di Azure usando il portale di Azure o PowerShell.
- Estendi lo spazio di archiviazione e Condividi set di impostazioni/codice su larga scala nell'intero team creando una condivisione File di Azure come unità montabile nella tua DSVM.
- Condividere il codice con il team usando GitHub. Accedere al repository usando i client Git preinstallati: Git bash e git GUI.
- Accedi ai servizi dati e analisi di Azure, ad esempio archiviazione BLOB di Azure, Azure Data Lake, Azure HDInsight (Hadoop), Azure Cosmos DB, Azure SQL Data Warehouse e database SQL di Azure.
- Creare report e un dashboard usando l'istanza di Power BI Desktop preinstallata in DSVM e distribuirli nel cloud.
- Ridimensiona in modo dinamico i DSVM per soddisfare le esigenze del tuo progetto.
- Installare altri strumenti nella macchina virtuale.   

> [!NOTE]
> Per molti dei servizi di archiviazione e analisi dei dati elencati in questo articolo sono applicati addebiti aggiuntivi sull'utilizzo. Per informazioni dettagliate, vedere la pagina dei [prezzi di Azure](https://azure.microsoft.com/pricing/) .
> 
> 

## <a name="prerequisites"></a>Prerequisiti

* È necessaria una sottoscrizione di Azure. È possibile [iscriversi per una versione di valutazione gratuita di Azure](https://azure.microsoft.com/free/).
* Per la [creazione di una macchina virtuale](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016)sono disponibili istruzioni per il provisioning di un Data Science Virtual Machine nel portale di Azure.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Esplorare i dati e sviluppare modelli con Microsoft Machine Learning Server
È possibile usare linguaggi come R e Python per eseguire l'analisi dei dati direttamente in DSVM.

Per R, è possibile usare un IDE come RStudio, disponibile nel menu Start o sul desktop. In alternativa, è possibile usare R Tools per Visual Studio. Microsoft ha fornito librerie aggiuntive oltre all'Open Source CRAN R per consentire l'analisi scalabile e la possibilità di analizzare i dati di dimensioni superiori a quelle consentite nell'analisi parallela in blocchi. 

Per Python è possibile usare un IDE come Visual Studio Community Edition, in cui è preinstallata l'estensione Python Tools for Visual Studio (PTVS). Per impostazione predefinita, in PTVS è configurato solo Python 3,6, l'ambiente conda radice. Per abilitare Anaconda Python 2,7, seguire questa procedura:

1. Per creare ambienti personalizzati per ogni versione, passare a **strumenti** > strumenti**Python** > **ambienti Python**e quindi selezionare **+ personalizzato** in Visual Studio Community Edition.
1. Assegnare una descrizione e impostare il percorso del prefisso dell'ambiente come **c:\anaconda\envs\python2** per anaconda Python 2,7.
1. Selezionare **rilevamento** > automatico**applica** per salvare l'ambiente.

Vedere la [documentazione di PTVS](https://aka.ms/ptvsdocs) per altre informazioni su come creare ambienti Python.

A questo punto è stato configurato per creare un nuovo progetto Python. Passare a **file** > **nuovo** > progettoPython > e selezionare il tipo di applicazione Python che si sta compilando. È possibile impostare l'ambiente Python per il progetto corrente sulla versione desiderata (Python 2,7 o 3,6) facendo clic con il pulsante destro del mouse su **ambienti Python** e quindi scegliendo **Aggiungi/Rimuovi ambienti Python**. Per ulteriori informazioni sull'utilizzo di PTVS, vedere la [documentazione del prodotto](https://aka.ms/ptvsdocs).

## <a name="use-jupyter-notebooks"></a>Usare i notebook di Jupyter
Il Jupyter Notebook offre un IDE basato sul browser per l'esplorazione e la modellazione dei dati. È possibile usare Python 2, Python 3 o R (sia open source che Microsoft R Server) in un notebook di Jupyter.

Per avviare la Jupyter Notebook, selezionare l'icona **Jupyter notebook** dal menu **Start** o sul desktop. Al prompt dei comandi di DSVM è anche possibile eseguire il comando ```jupyter notebook``` dalla directory in cui sono presenti notebook o in cui si desidera creare nuovi notebook.  

Dopo l'avvio di Jupyter, viene visualizzata una directory contenente alcuni notebook di esempio pre-assemblati in DSVM. A questo punto è possibile:

* Selezionare il notebook per visualizzare il codice.
* Eseguire ogni cella selezionando MAIUSC + INVIO.
* Eseguire l'intero notebook selezionando**esecuzione** **cella** > .
* Creare un nuovo notebook selezionando l'icona Jupyter (angolo superiore sinistro), selezionando il pulsante **nuovo** a destra e quindi scegliendo la lingua del notebook (nota anche come kernel).   

> [!NOTE]
> Attualmente sono supportati i kernel Python 2,7, Python 3,6, R, Julia e PySpark in Jupyter. Il kernel R supporta la programmazione sia in R open source che in Microsoft R.   
> 
> 

Quando ci si trova nel notebook, è possibile esplorare i dati, compilare il modello ed eseguire il test del modello usando le librerie scelte.

## <a name="train-and-deploy-models-by-using-azure-machine-learning"></a>Eseguire il training e distribuire modelli usando Azure Machine Learning
Dopo aver compilato e convalidato il modello, il passaggio successivo è in genere quello di distribuirlo nell'ambiente di produzione. Questo passaggio consente alle applicazioni client di richiamare le stime del modello in tempo reale o in base alla modalità batch. Azure Machine Learning offre un meccanismo per rendere operativo il modello compilato in R o Python.

Quando si rendere operativo il modello in Azure Machine Learning, viene esposto un servizio Web. Consente ai client di effettuare chiamate REST che passano parametri di input e ricevono stime dal modello come output.   

> [!NOTE]
> Se non è ancora stata effettuata l'iscrizione per Azure Machine Learning, è possibile ottenere un'area di lavoro gratuita o un'area di lavoro standard visitando il home page [Azure Machine Learning Studio](https://studio.azureml.net/) e selezionando **inizia**.   
> 
> 

### <a name="build-and-operationalize-python-models"></a>Compilare e rendere operativo i modelli Python
Ecco un frammento di codice sviluppato in un notebook di Jupyter Python che compila un modello semplice usando la libreria Scikit-learn:

```python
# IRIS classification
from sklearn import datasets
from sklearn import svm
clf = svm.SVC()
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)
```

Il metodo usato per distribuire i modelli Python per Azure Machine Learning esegue il wrapping della stima del modello in una funzione e lo decora con gli attributi forniti dalla libreria Azure Machine Learning Python pre-installato. Gli attributi indicano l'ID dell'area di lavoro Azure Machine Learning, la chiave API e i parametri di input e restituiti.  

```python
from azureml import services
@services.publish(workspaceid, auth_token)
@services.types(sep_l=float, sep_w=float, pet_l=float, pet_w=float)
@services.returns(int)  # 0, or 1, or 2
def predictIris(sep_l, sep_w, pet_l, pet_w):
    inputArray = [sep_l, sep_w, pet_l, pet_w]


return clf.predict(inputArray)
```

Un client può ora effettuare chiamate al servizio Web. I wrapper pratici creano le richieste API REST. Di seguito è riportato il codice di esempio per utilizzare il servizio Web:

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
> Attualmente la libreria Azure Machine Learning è supportata solo in Python 2,7.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>Compilare e rendere operativo i modelli R
È possibile distribuire modelli R compilati nella Data Science Virtual Machine o altrove in Azure Machine Learning in modo analogo a come avviene per Python. Di seguito sono riportati i passaggi necessari:

1. Creare un file Settings. JSON per fornire l'ID dell'area di lavoro e il token di autenticazione. 
2. Scrivere un wrapper per la funzione Predict del modello.
3. Chiamare ```publishWebService``` nella libreria Azure Machine Learning per passare il wrapper della funzione.  

Utilizzare la procedura e i frammenti di codice seguenti per configurare, compilare, pubblicare e utilizzare un modello come servizio Web in Azure Machine Learning.

#### <a name="set-up"></a>Imposta

Creare un file Settings. JSON in una directory denominata ```.azureml``` all'interno della Home Directory. Immettere i parametri dall'area di lavoro Azure Machine Learning.

Di seguito è illustrata la struttura del file Settings. JSON:

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
Per utilizzare il modello da un'applicazione client, utilizzare la libreria Azure Machine Learning per cercare il servizio Web pubblicato in base al nome. Utilizzare la `services` chiamata API per determinare l'endpoint. Sarà quindi sufficiente chiamare la funzione `consume` e passare il frame di dati di cui eseguire la stima.

Utilizzare il codice seguente per utilizzare il modello pubblicato come servizio Web Azure Machine Learning:

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

Per altre informazioni sui [pacchetti R](/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning), vedere Machine Learning Studio.

## <a name="manage-azure-resources"></a>Gestire le risorse di Azure
Il DSVM non consente solo di compilare localmente la soluzione di analisi nella macchina virtuale. Consente inoltre di accedere ai servizi sulla piattaforma cloud di Azure. Azure offre diversi servizi di calcolo, archiviazione, analisi dei dati e altri servizi che puoi amministrare e accedere dalla tua DSVM.

Per amministrare la sottoscrizione di Azure e le risorse cloud, sono disponibili due opzioni:
+ Usare il browser e passare al [portale di Azure](https://portal.azure.com).

+ Usare gli script di PowerShell. Eseguire Azure PowerShell da un collegamento sul desktop o dal menu **Start** . Per informazioni dettagliate, vedere la [documentazione Microsoft Azure PowerShell](../../powershell-azure-resource-manager.md) . 

## <a name="extend-storage-by-using-shared-file-systems"></a>Estendi archiviazione tramite file system condivisi
I data scientist possono condividere grandi set di dati, codice o alte risorse all'interno del team. Il DSVM ha circa 45 GB di spazio disponibile. Per estendere lo spazio di archiviazione, è possibile usare File di Azure e montarlo in una o più istanze di DSVM o accedervi tramite un'API REST. È anche possibile usare il [portale di Azure](../../virtual-machines/windows/attach-managed-disk-portal.md) o usare [Azure PowerShell](../../virtual-machines/windows/attach-disk-ps.md) per aggiungere dischi dati dedicati aggiuntivi. 

> [!NOTE]
> Lo spazio massimo nella condivisione File di Azure è di 5 TB. Il limite di dimensioni per ogni file è di 1 TB. 

È possibile usare questo script in Azure PowerShell per creare una condivisione File di Azure:

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

Ora che è stata creata una condivisione di File di Azure, è possibile montarla in qualsiasi macchina virtuale in Azure. È consigliabile inserire la macchina virtuale nello stesso data center di Azure dell'account di archiviazione, per evitare la latenza e gli addebiti per il trasferimento dei dati. Ecco i comandi Azure PowerShell per montare l'unità in DSVM:

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

Ora è possibile accedere a questa unità come a qualsiasi altra unità della VM.

## <a name="share-code-in-github"></a>Condividere il codice in GitHub
GitHub è un repository di codice in cui è possibile trovare esempi di codice e origini per diversi strumenti usando le tecnologie condivise dalla community degli sviluppatori. Usa Git come tecnologia per tenere traccia delle versioni dei file di codice e per archiviarle. GitHub è anche una piattaforma in cui è possibile creare un repository personalizzato per archiviare la documentazione e il codice condiviso del team, implementare il controllo della versione e controllare chi ha accesso per visualizzare e contribuire al codice. 

Per altre informazioni sull'uso di Git, visitare le [pagine della guida di GitHub](https://help.github.com/). È possibile usare GitHub come uno dei modi per collaborare con il team, usare il codice sviluppato dalla community e contribuire di nuovo al codice alla community.

Il DSVM viene caricato con gli strumenti client nella riga di comando e nella GUI per accedere al repository GitHub. Lo strumento da riga di comando che funziona con git e GitHub è denominato git bash. Visual Studio viene installato in DSVM e include le estensioni git. È possibile trovare le icone per questi strumenti nel menu **Start** e sul desktop.

Per scaricare il codice da un repository GitHub, usare il comando ```git clone```. Ad esempio, per scaricare il repository data science pubblicato da Microsoft nella directory corrente, è possibile eseguire il comando seguente in git bash:

    git clone https://github.com/Azure/DataScienceVM.git

In Visual Studio è possibile eseguire la stessa operazione di clonazione. Lo screenshot seguente illustra come accedere agli strumenti Git e GitHub in Visual Studio:

![Screenshot di Visual Studio con la connessione GitHub visualizzata](./media/vm-do-ten-things/VSGit.PNG)

È possibile trovare altre informazioni sull'uso di Git per lavorare con il repository GitHub dalle risorse disponibili in github.com. Il [foglio informativo](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) è un riferimento utile.

## <a name="access-azure-data-and-analytics-services"></a>Accedere ai servizi dati e analisi di Azure
### <a name="azure-blob-storage"></a>Archivio BLOB di Azure
Archiviazione BLOB di Azure è un servizio di archiviazione cloud affidabile e conveniente per i dati di grandi e piccole dimensioni. Questa sezione descrive come è possibile spostare i dati nell'archivio BLOB e accedere ai dati archiviati in un BLOB di Azure.

#### <a name="prerequisites"></a>Prerequisiti

* Creare l'account di archiviazione BLOB di Azure dal [portale di Azure](https://portal.azure.com).

   ![Screenshot del processo di creazione dell'account di archiviazione nell'portale di Azure](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Verificare che lo strumento AzCopy da riga di comando sia pre-installato ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```:. La directory che contiene azcopy. exe è già presente nella variabile di ambiente PATH, quindi è possibile evitare di digitare il percorso completo dei comandi quando si esegue questo strumento. Per ulteriori informazioni sullo strumento AzCopy, vedere la [documentazione di AzCopy](../../storage/common/storage-use-azcopy.md).
* Avviare lo strumento Esplora archivi di Azure. È possibile scaricarlo dalla [pagina web Storage Explorer](https://storageexplorer.com/). 

   ![Screenshot di Azure Storage Explorer accedere a un account di archiviazione](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>Spostare i dati da una macchina virtuale a un BLOB di Azure: AzCopy

Per spostare i dati tra i file locali e l'archiviazione BLOB, è possibile usare AzCopy nella riga di comando o in PowerShell:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Sostituire **c:\MyFolder.** con il percorso in cui è archiviato il file, **mystorageaccount** con il nome dell'account di archiviazione BLOB, il contenitore con il nome del contenitore e la **chiave dell'account di archiviazione** con la chiave di accesso all'archiviazione BLOB. È possibile trovare le credenziali dell'account di archiviazione nell' [portale di Azure](https://portal.azure.com).

Eseguire il comando AzCopy in PowerShell o da un prompt dei comandi. Di seguito è riportato un esempio di utilizzo del comando AzCopy:

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

Dopo aver eseguito il comando AzCopy per la copia in un BLOB di Azure, il file verrà visualizzato in Azure Storage Explorer.

![Screenshot dell'account di archiviazione, che Visualizza il file CSV caricato](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>Spostare i dati da una macchina virtuale a un BLOB di Azure: Esplora archivi Azure

È anche possibile caricare i dati dal file locale nella macchina virtuale usando Azure Storage Explorer:

* Per caricare i dati in un contenitore, selezionare il contenitore di destinazione e selezionare il pulsante **carica** . ![Screenshot del pulsante carica in Azure Storage Explorer](./media/vm-do-ten-things/storage-accounts.png)
* Selezionare i puntini di sospensione ( **...** ) a destra della casella **file** , selezionare uno o più file da caricare dal file System e selezionare **carica** per iniziare a caricare i file. ![Screenshot della finestra di dialogo Carica file](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-machine-learning-reader-module"></a>Leggere i dati da un BLOB di Azure: Modulo Reader Machine Learning

In Azure Machine Learning Studio, è possibile usare il modulo Import data per leggere i dati dal BLOB.

![Screenshot del modulo Import Data in Machine Learning Studio](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>Leggere i dati da un BLOB di Azure: ODBC Python

È possibile usare la libreria BlobService per leggere i dati direttamente da un BLOB in un notebook di Jupyter o in un programma Python.

Importare prima di tutto i pacchetti necessari:

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

Quindi, inserire le credenziali dell'account di archiviazione BLOB e leggere i dati dal BLOB:

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

I dati vengono letti come frame di dati:

![Screenshot delle prime 10 righe di dati](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Azure Data Lake Storage è un repository con iperscalabilità per i carichi di lavoro di Big Data Analytics ed è compatibile con Hadoop Distributed File System (HDFS). Funziona con Hadoop, Spark e Azure Data Lake Analytics. Questa sezione illustra come spostare i dati in Azure Data Lake Storage ed eseguire analisi usando Azure Data Lake Analytics.

#### <a name="prerequisites"></a>Prerequisiti

* Creare l'istanza di Azure Data Lake Analytics nell' [portale di Azure](https://portal.azure.com).

   ![Screenshot della creazione di un'istanza di Data Lake Analytics dalla portale di Azure](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* Il [plug-in di Azure Data Lake e gli strumenti di analisi di flusso per Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) è già installato in Visual Studio Community Edition nella macchina virtuale. Dopo l'avvio di Visual Studio e l'accesso alla sottoscrizione di Azure, l'account e l'archiviazione di Azure Data Analytics verranno visualizzati nel riquadro sinistro di Visual Studio.

   ![Screenshot del plug-in per Data Lake Tools in Visual Studio](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

#### <a name="move-data-from-a-vm-to-data-lake-azure-data-lake-explorer"></a>Spostare i dati da una macchina virtuale a Data Lake: Azure Data Lake Explorer

È possibile utilizzare Azure Data Lake Explorer per [caricare i dati dai file locali nella macchina virtuale a data Lake storage](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

È anche possibile creare una pipeline di dati per rendere operativo lo spostamento dei dati da e verso Azure Data Lake usando [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). [Questo articolo](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) illustra i passaggi per compilare le pipeline di dati.

#### <a name="read-data-from-an-azure-blob-to-data-lake-u-sql"></a>Leggere i dati da un BLOB di Azure a Data Lake: U-SQL

Se i dati si trovano nell'archiviazione BLOB di Azure, è possibile leggere direttamente i dati da un BLOB di Azure in una query U-SQL. Prima di comporre la query U-SQL, verificare che l'account di archiviazione BLOB sia collegato all'istanza di Azure Data Lake. Passare alla portale di Azure, trovare il dashboard Azure Data Lake Analytics, selezionare **Aggiungi origine dati**, selezionare un tipo di archiviazione di **archiviazione di Azure**e inserire il nome e la chiave dell'account di archiviazione di Azure. È quindi possibile fare riferimento ai dati archiviati nell'account di archiviazione.

![Screenshot della finestra di dialogo Aggiungi origine dati](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

In Visual Studio è possibile leggere i dati dall'archivio BLOB, manipolare i dati, progettare funzionalità e inviare i dati risultanti a Azure Data Lake o all'archivio BLOB di Azure. Quando si fa riferimento ai dati nell'archivio BLOB, usare **Wasb://** . Quando si fa riferimento ai dati in Azure Data Lake, usare **swbhdfs://** .

È possibile usare le query U-SQL seguenti in Visual Studio:

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

Dopo che la query è stata inviata al server, un diagramma mostra lo stato del processo.

![Screenshot del diagramma dello stato del processo](./media/vm-do-ten-things/USQL_Job_Status.PNG)

#### <a name="query-data-in-data-lake-u-sql"></a>Eseguire query sui dati in Data Lake: U-SQL

Dopo l'inserimento del set di dati in Azure Data Lake, è possibile usare il [linguaggio U-SQL](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) per eseguire query ed esplorare i dati. Il linguaggio U-SQL è simile a T-SQL, ma combina alcune funzionalità C# di in modo che gli utenti possano scrivere moduli personalizzati e funzioni definite dall'utente. È possibile usare gli script del passaggio precedente.

Dopo che la query è stata inviata al server, tripdata_summary. CSV viene visualizzato in Azure Data Lake Explorer. È possibile visualizzare in anteprima i dati facendo clic con il pulsante destro del mouse sul file.

![Screenshot del file CSV in Esplora Data Lake](./media/vm-do-ten-things/USQL_create_summary.png)

Verranno visualizzate le informazioni sul file:

![Screenshot delle informazioni di riepilogo del file](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>Cluster Hadoop di HDInsight
Azure HDInsight è un servizio Apache Hadoop, Spark, HBase e Storm gestito nel cloud. È possibile lavorare facilmente con i cluster HDInsight di Azure dalla Data Science Virtual Machine.

#### <a name="prerequisites"></a>Prerequisiti

* Creare l'account di archiviazione BLOB di Azure dal [portale di Azure](https://portal.azure.com). Questo account di archiviazione viene usato per archiviare i dati per i cluster HDInsight.

   ![Screenshot della creazione di un account di archiviazione dal portale di Azure](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Personalizzare i cluster Azure HDInsight Hadoop dalla [portale di Azure](../team-data-science-process/customize-hadoop-cluster.md).
  
   Collegare l'account di archiviazione creato con il cluster HDInsight al momento della creazione. Questo account di archiviazione viene usato per accedere ai dati che possono essere elaborati all'interno del cluster.

   ![Selezioni per il collegamento dell'account di archiviazione creato con un cluster HDInsight](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* Abilitare l'accesso Desktop remoto al nodo head del cluster dopo che è stato creato. Ricordare le credenziali di accesso remoto specificate qui, perché saranno necessarie nella procedura successiva.

   ![Pulsante di Desktop remoto per abilitare l'accesso remoto al cluster HDInsight](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Creare un'area di lavoro di Azure Machine Learning. Gli esperimenti di Machine Learning vengono archiviati in questa area di lavoro di Machine Learning. Selezionare le opzioni evidenziate nel portale, come illustrato nello screenshot seguente:

   ![Creare un'area di lavoro di Machine Learning di Azure](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Immettere i parametri per l'area di lavoro.

   ![Immettere i parametri dell'area di lavoro di Machine Learning](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* Caricare i dati con IPython notebook. Importare i pacchetti necessari, inserire le credenziali, creare un database nell'account di archiviazione e quindi caricare i dati in cluster HDI.

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
  
* Usare AzCopy per scaricare CSVs compressi dal BLOB pubblico nella cartella locale.
* Usare AzCopy per caricare CSVs decompressi dalla cartella locale a un cluster HDI.
* Accedere al nodo head del cluster Hadoop e preparare l'analisi esplorativa dei dati.

Una volta caricati i dati nel cluster HDI, è possibile controllare i dati in Azure Storage Explorer. Il database nyctaxidb è stato creato nel cluster HDI.

#### <a name="data-exploration-hive-queries-in-python"></a>Esplorazione dei dati: Query hive in Python

Poiché i dati si trova in un cluster Hadoop, è possibile usare il pacchetto pyodbc per connettersi a cluster Hadoop ed eseguire query sui database usando hive per eseguire l'esplorazione e la progettazione delle funzionalità. È possibile visualizzare le tabelle esistenti create nel passaggio dei prerequisiti.

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

È anche possibile calcolare la distanza tra la posizione di prelievo e il percorso di rilascio, quindi confrontarla con la distanza del viaggio.

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

![Prime righe della tabella di riepilogo e di ritiro](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

```python
results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                   'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
df = results.loc[results['trip_distance'] <= 100]  # remove outliers
df = df.loc[df['direct_distance'] <= 100]  # remove outliers
plt.scatter(df['direct_distance'], df['trip_distance'])
```

![Tracciato della distanza di ritiro/discesa alla distanza della corsa](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

A questo punto è possibile preparare un set di dati downsampling (1%) per la modellazione. È possibile usare questi dati nel modulo Machine Learning Reader.

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

Inserire ora il contenuto del join nella tabella interna precedente.

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

Dopo un certo periodo di tempo, è possibile osservare che i dati sono stati caricati nei cluster Hadoop:

```python
queryString = """
    select * from nyctaxi_downsampled_dataset limit 10;
    """
cursor.execute(queryString)
pd.read_sql(queryString, connection)
```

![Prime righe dei dati dalla tabella](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

#### <a name="read-data-from-hdi-by-using-machine-learning-reader-module"></a>Leggere i dati da HDI usando Machine Learning: modulo Reader

È anche possibile usare il modulo Reader in Machine Learning Studio per accedere al database in un cluster Hadoop. Inserire le credenziali dei cluster HDI e l'account di archiviazione di Azure per abilitare la compilazione di modelli di Machine Learning usando un database nei cluster HDI.

![Proprietà del modulo Reader](./media/vm-do-ten-things/AML_Reader_Hive.PNG)

È quindi possibile visualizzare il set di dati con Punteggio:

![Visualizzare il set di dati con i punteggi](./media/vm-do-ten-things/AML_Model_Results.PNG)

### <a name="azure-sql-data-warehouse-and-databases"></a>Azure SQL Data Warehouse e database
Azure SQL Data Warehouse è un data warehouse elastico come servizio con un'esperienza SQL Server di livello aziendale.

È possibile effettuare il provisioning del data warehouse SQL di Azure seguendo le istruzioni riportate in [questo articolo](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). Dopo aver eseguito il provisioning del data warehouse SQL, è possibile usare [questa procedura dettagliata](../team-data-science-process/sqldw-walkthrough.md) per eseguire operazioni di caricamento, esplorazione e modellazione dei dati usando i dati all'interno di SQL data warehouse.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB è un database NoSQL sul cloud. È possibile usarlo per lavorare con documenti come JSON e per archiviare ed eseguire query sui documenti.

Usare i seguenti passaggi dei prerequisiti per accedere a Azure Cosmos DB da DSVM:

1. Il Azure Cosmos DB Python SDK è già installato in DSVM. Per aggiornarlo, eseguire ```pip install pydocumentdb --upgrade``` da un prompt dei comandi.
2. Creare un account e un database Azure Cosmos DB dal [portale di Azure](https://portal.azure.com).
3. Scaricare lo strumento di migrazione dei dati Azure Cosmos DB dall' [area download Microsoft](https://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) ed estrarlo in una directory di propria scelta.
4. Importare i dati JSON (Volcano Data) archiviati in un [BLOB pubblico](https://cahandson.blob.core.windows.net/samples/volcano.json) in Azure Cosmos DB con i parametri di comando seguenti per lo strumento di migrazione. Usare dtui. exe dalla directory in cui è stato installato il Azure Cosmos DB strumento di migrazione dati. Immettere i percorsi di origine e di destinazione con questi parametri:
   
    `/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

Dopo aver importato i dati, è possibile passare a Jupyter e aprire il notebook denominato *DocumentDBSample*. Contiene codice Python per accedere Azure Cosmos DB ed eseguire alcune query di base. Per ulteriori informazioni su Azure Cosmos DB, visitare la [pagina della documentazione](https://docs.microsoft.com/azure/cosmos-db/)del servizio.

## <a name="use-power-bi-reports-and-dashboards"></a>Usare report e dashboard di Power BI 
È possibile visualizzare il file JSON Volcano dall'esempio precedente di Azure Cosmos DB in Power BI Desktop per ottenere informazioni visive sui dati. I passaggi dettagliati sono disponibili nell' [articolo relativo a Power BI](../../cosmos-db/powerbi-visualize.md). Ecco i passaggi principali:

1. Aprire Power BI Desktop e selezionare **Recupera dati**. Specificare l'URL come: https://cahandson.blob.core.windows.net/samples/volcano.json.
2. Verranno visualizzati i record JSON importati come elenco. Convertire l'elenco in una tabella in modo che Power BI possibile utilizzarlo.
4. Espandere le colonne selezionando l'icona Espandi (freccia).
5. Si noti che il percorso è un campo di **record** . Espandere il record e selezionare solo le coordinate. Coordinata è una colonna dell'elenco.
6. Aggiungere una nuova colonna per convertire la colonna della coordinata elenco in una colonna **LatLong** con valori delimitati da virgole. Concatena i due elementi nel campo dell'elenco di coordinate usando la formula ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```.
7. Convertire la colonna di elevazione in decimale e selezionare i pulsanti **Chiudi** e **applica** .

Anziché eseguire i passaggi precedenti, è possibile incollare il codice seguente. Inserisce nello script i passaggi utilizzati nella Editor avanzato Power BI per scrivere le trasformazioni dei dati in un linguaggio di query.

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

I dati sono ora inclusi nel modello di dati di Power BI. L'istanza di Power BI Desktop dovrebbe apparire come segue:

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

È possibile iniziare a creare report e visualizzazioni usando il modello di dati. Per compilare un report, è possibile seguire la procedura descritta in [questo articolo Power bi](../../cosmos-db/powerbi-visualize.md#build-the-reports) .

## <a name="scale-the-dsvm-dynamically"></a>Ridimensionare dinamicamente il DSVM 
È possibile aumentare e ridurre le prestazioni di DSVM per soddisfare le esigenze del progetto. Se non è necessario usare la macchina virtuale nella sera o nei fine settimana, è possibile arrestare la macchina virtuale dalla [portale di Azure](https://portal.azure.com).

> [!NOTE]
> Vengono addebitati i costi di calcolo se si usa solo il pulsante di arresto per il sistema operativo della macchina virtuale.  
> 
> 

Potrebbe essere necessario gestire alcune analisi su larga scala e richiedere maggiore capacità di CPU, memoria o disco. In tal caso, è possibile trovare una scelta delle dimensioni delle macchine virtuali in termini di core CPU, istanze basate su GPU per l'apprendimento avanzato, capacità di memoria e tipi di disco (incluse le unità SSD) che soddisfano le esigenze di calcolo e di budget. L'elenco completo delle macchine virtuali, oltre ai prezzi di calcolo oraria, è disponibile nella pagina dei [prezzi di macchine virtuali di Azure](https://azure.microsoft.com/pricing/details/virtual-machines/) .

Analogamente, la necessità di una capacità di elaborazione della macchina virtuale potrebbe diminuire. Ad esempio, è stato spostato un carico di lavoro principale in un cluster Hadoop o Spark. È quindi possibile ridimensionare il cluster dalla [portale di Azure](https://portal.azure.com) e passare alle impostazioni dell'istanza di VM. 

## <a name="add-more-tools"></a>Aggiungi altri strumenti
Gli strumenti predefiniti in DSVM possono soddisfare molte esigenze comuni di analisi dei dati. Questo consente di risparmiare tempo perché non è necessario installare e configurare gli ambienti uno alla volta. Ti consente anche di risparmiare denaro, perché paghi solo per le risorse che usi.

Per migliorare l'ambiente di analisi, è possibile usare altri servizi dati e analisi di Azure profilati in questo articolo. In alcuni casi, potrebbero essere necessari strumenti aggiuntivi, inclusi alcuni strumenti per partner proprietari. Si dispone dell'accesso amministrativo completo nella macchina virtuale per installare i nuovi strumenti necessari. È anche possibile installare pacchetti aggiuntivi in Python e in R non preinstallati. Per Python, è possibile usare ```conda``` o. ```pip``` Per r è ```install.packages()``` possibile usare nella console di r oppure usare l'IDE e selezionare **pacchetti** > **Installa pacchetti**.

## <a name="deep-learning"></a>Apprendimento avanzato

Oltre agli esempi basati su Framework, è possibile ottenere un set di procedure dettagliate complete che sono state convalidate nella DSVM. Queste procedure dettagliate consentono di iniziare a sviluppare applicazioni di apprendimento avanzato in domini come la comprensione delle immagini e del testo e della lingua.   


- [Esecuzione di reti neurali in diversi framework](https://github.com/ilkarman/DeepLearningFrameworks): In questa procedura dettagliata viene illustrato come eseguire la migrazione del codice da un Framework a un altro. Viene inoltre illustrato come confrontare i modelli e le prestazioni di runtime tra i Framework. 

- [Guida dettagliata per la creazione di una soluzione end-to-end per individuare prodotti all'interno di immagini](https://github.com/Azure/cortana-intelligence-product-detection-from-images): il rilevamento di immagini è una tecnica che consente di individuare e classificare gli oggetti all'interno delle immagini Questa tecnologia può comportare notevoli vantaggi in molti domini aziendali reali. Ad esempio, i negozianti possono usare questa tecnica per determinare quale prodotto un cliente ha prelevato dallo scaffale. Queste informazioni a loro volta consentono ai negozi di gestire l'inventario dei prodotti. 

- [Deep learning per audio](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): Questa esercitazione illustra come eseguire il training di un modello di apprendimento approfondito per il rilevamento di eventi audio nel [set di dati dei suoni urbani](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html). Viene inoltre fornita una panoramica su come utilizzare i dati audio.

- [Classificazione di documenti di testo](https://github.com/anargyri/lstm_han): Questa procedura dettagliata illustra come compilare ed eseguire il training di due architetture di rete neurale: Hierarchical Attention Network e Long Short Term Memory (LSTM). Queste reti neurali usano l'API Keras per l'apprendimento avanzato per classificare i documenti di testo. Keras è un front-end per tre dei framework di apprendimento avanzato più diffusi: Microsoft Cognitive Toolkit, TensorFlow e Theano.

## <a name="summary"></a>Riepilogo
In questo articolo sono state descritte alcune delle operazioni che è possibile eseguire in Microsoft Data Science Virtual Machine. Per rendere DSVM un ambiente di analisi efficace, è possibile eseguire molte altre operazioni.

