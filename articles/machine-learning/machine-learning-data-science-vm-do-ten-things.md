---
title: Dieci cose da fare con la macchina virtuale per l&quot;analisi scientifica dei dati | Documentazione Microsoft
description: "Eseguire diverse attività di esplorazione e modellazione dei dati nella macchina virtuale per l&quot;analisi scientifica dei dati."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 145dfe3e-2bd2-478f-9b6e-99d97d789c62
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: gokuma;weig;bradsev
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c35d1548262f25e65c391c927919b8acf1411e10
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---
# <a name="ten-things-you-can-do-on-the-data-science-virtual-machine"></a>Dieci cose da fare con la macchina virtuale per l'analisi scientifica dei dati
La macchina virtuale per l'analisi scientifica dei dati (DSVM, Data Science Virtual Machine) di Microsoft è un ambiente di sviluppo di analisi scientifica dei dati avanzato che consente di eseguire diverse attività di esplorazione e modellazione dati. L'ambiente è già compilato e in bundle in diversi strumenti comuni di analisi dei dati che consentono di iniziare a usare rapidamente e facilmente l'analisi per le distribuzioni locali, cloud o ibride. DSVM è ben integrata con diversi servizi di Azure e può leggere ed elaborare dati già archiviati in Azure, in Azure SQL Data Warehouse, Azure Data Lake, Archiviazione di Azure o in Azure Cosmos DB. Può anche sfruttare altri strumenti di analisi come Azure Machine Learning e Azure Data Factory.

Questo articolo illustra come usare DSVM per eseguire diverse attività di analisi scientifica dei dati e interagire con altri servizi di Azure. Ecco alcune attività che è possibile eseguire con DSVM:

1. Esplorare dati e sviluppare modelli in locale in DSVM usando Microsoft R Server e Python
2. Usare un notebook di Jupyter per sperimentare con i dati in un browser usando Python 2, Python 3 e Microsoft R, una versione di R pronta per l'azienda progettata per la scalabilità e le prestazioni
3. Rendere operativi i modelli compilati usando R e Python in Azure Machine Learning in modo che le applicazioni client possano accedere ai modelli con una semplice interfaccia di servizi Web
4. Amministrare le risorse di Azure usando il portale di Azure o PowerShell
5. Estendere lo spazio di archiviazione e condividere set di dati o codice su larga scala con l'intero team creando un archivio file di Azure come unità installabile in DSVM
6. Condividere il codice con il team usando GitHub e accedere all'archivio con i client Git preinstallati (Git Bash, Git GUI).
7. Accedere a diversi servizi dati e analisi di Azure, ad esempio Archiviazione BLOB di Azure, Azure Data Lake, Azure HDInsight (Hadoop), Azure Cosmos DB, Azure SQL Data Warehouse e database
8. Compilare report e dashboard usando Power BI Desktop preinstallato in DSVM e distribuirli nel cloud
9. Ridimensionare in modo dinamico DSVM per poter soddisfare le esigenze del progetto
10. Installare strumenti aggiuntivi nella macchina virtuale   

> [!NOTE]
> Per molti dei servizi di archiviazione e analisi dei dati aggiuntivi elencati in questo articolo verranno applicati costi di utilizzo aggiuntivi. Per altri dettagli, vedere la pagina [Prezzi di Azure](https://azure.microsoft.com/pricing/) .
> 
> 

**Prerequisiti**

* È necessaria una sottoscrizione di Azure. È possibile iscriversi per una versione di valutazione gratuita di Azure [qui](https://azure.microsoft.com/free/).
* Le istruzioni per il provisioning di una macchina virtuale per l'analisi scientifica dei dati nel portale di Azure sono disponibili nel documento relativo alla [creazione di una macchina virtuale](https://portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm).

## <a name="1-explore-data-and-develop-models-using-microsoft-r-server-or-python"></a>1. Esplorare dati e sviluppare modelli usando Microsoft R Server o Python
È possibile usare linguaggi come R e Python per eseguire l'analisi dei dati direttamente in DSVM.

Per R, è possibile usare un IDE chiamato "Revolution R Enterprise 8.0" presente nel menu Start o nel desktop. Microsoft ha fornito librerie aggiuntive oltre a Open source/CRAN-R per consentire l'analisi scalabile e offrire la possibilità di analizzare dati di dimensioni maggiori di quelle consentite dalla memoria eseguendo un'analisi parallela in blocchi. È anche possibile installare l'IDE R di desiderato, ad esempio [RStudio](https://www.rstudio.com/products/rstudio-desktop/).

Per Python, è possibile usare un IDE come Visual Studio Community Edition in cui è preinstallata l'estensione Python Tools for Visual Studio (PTVS). Per impostazione predefinita, in PTVS è configurato solo Python 2.7 di base, senza librerie di analisi come SciKit o Pandas. Per abilitare Anaconda Python 2.7 e 3.5, è necessario eseguire queste operazioni:

* Creare ambienti personalizzati per ogni versione passando a **Strumenti** -> **Python Tools** -> **Python Environments** (Strumenti Python -> Ambienti Python) e quindi facendo clic su "**+ Custom**" (+ Personalizza) in Visual Studio 2015 Community Edition.
* Immettere una descrizione e impostare i percorsi con il prefisso dell'ambiente come *c:\anaconda* per Anaconda Python 2.7 OPPURE *c:\anaconda\envs\py35* per Anaconda Python 3.5.
* Fare clic su **Rilevamento automatico** e quindi su **Applica** per salvare l'ambiente.

Ecco come appare la configurazione dell'ambiente personalizzato in Visual Studio.

![Configurazione di PTVS](./media/machine-learning-data-science-vm-do-ten-things/PTVSSetup.png)

Per altri dettagli su come creare gli ambienti Python, vedere la [documentazione relativa a PTVS](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it) .

A questo punto è possibile iniziare a creare un nuovo progetto Python. Passare a **File** -> **Nuovo** -> **Progetto** -> **Python** e selezionare il tipo di applicazione Python da compilare. È possibile impostare l'ambiente Python per il progetto corrente sulla versione desiderata, ad esempio Anaconda 2.7 o 3.5. Fare clic con il pulsante destro del mouse su **Python Environments** (Ambienti Python), selezionare **Aggiungi/Rimuovi Python Environments** e quindi selezionare l'ambiente desiderato da associare al progetto. È possibile trovare altre informazioni sull'uso di PTVS nella pagina di [documentazione](https://github.com/Microsoft/PTVS/wiki) del prodotto.

## <a name="2-using-a-jupyter-notebook-to-explore-and-model-your-data-with-python-or-r"></a>2. Uso di un notebook di Jupyter per esplorare e modellare i dati con Python o R
Il notebook di Jupyter è un ambiente avanzato che fornisce un "IDE" basato su browser per l'esplorazione e la modellazione dei dati. È possibile usare Python 2, Python 3 o R, sia open source che Microsoft R Server, in un notebook di Jupyter.

Per avviare il notebook di Jupyter, fare clic sull'icona del menu Start o sull'icona del desktop denominata **Notebook di Jupyter**. In DSVM è anche possibile passare a "https://localhost:9999/" per accedere al notebook di Jupyter. Se viene richiesta una password, seguire le istruzioni fornite nella sezione ***Come creare una password complessa nel server notebook di Jupyter*** dell'argomento [Eseguire il provisioning di una macchina virtuale per l'analisi scientifica dei dati di Microsoft](machine-learning-data-science-provision-vm.md) per creare una password complessa per l'accesso a Jupyter Notebook. 

Una volta effettuato l'accesso al notebook, verrà visualizzata una directory contenente alcuni notebook di esempio inclusi nel pacchetto DSVM. A questo punto è possibile:

* Fare clic sul notebook per visualizzare il codice.
* Eseguire ogni cella premendo **MAIUSC+INVIO**.
* Eseguire l'intero notebook facendo clic su **Cell** -> **Run** (Cella -> Esegui).
* Creare un nuovo notebook facendo clic sull'icona di Jupyter nell'angolo in alto a sinistra. Fare quindi clic sul pulsante **New** (Nuovo) a destra e infine scegliere il linguaggio del notebook, detto anche kernel.   

> [!NOTE]
> Attualmente sono supportati Python 2.7, Python 3.5 e R. Il kernel R supporta la programmazione sia in R open source che in Microsoft R Server scalabile aziendale.   
> 
> 

Una volta effettuato l'accesso al notebook, è possibile esplorare i dati, compilare il modello e testare il modello usando le librerie preferite.

## <a name="3-build-models-using-r-or-python-and-operationalize-them-using-azure-machine-learning"></a>3. Compilare modelli usando R o Python e renderli operativi con Azure Machine Learning
Una volta compilato e convalidato il modello, il passaggio successivo consiste in genere nel distribuirlo nell'ambiente di produzione. Ciò consente alle applicazioni client di richiamare le stime del modello in tempo reale o in modalità batch. Azure Machine Learning offre un meccanismo per rendere operativo il modello compilato in R o Python.

Quando si rende operativo il modello in Azure Machine Learning, viene esposto un servizio Web che consente ai client di effettuare chiamate REST che passano i parametri di input e ricevono le stime dal modello come output.   

> [!NOTE]
> Se non si è ancora iscritti ad Azure Machine Learning, è possibile ottenere un'area di lavoro gratuita o standard visitando la home page di [Azure Machine Learning Studio](https://studio.azureml.net/) e facendo clic su "Get Started" (Per iniziare).   
> 
> 

### <a name="build-and-operationalize-python-models"></a>Compilare e rendere operativi i modelli Python
Di seguito è riportato un frammento di codice sviluppato in un notebook di Jupyter con Python che compila un semplice modello con la libreria SciKit-learn.

    #IRIS classification
    from sklearn import datasets
    from sklearn import svm
    clf = svm.SVC()
    iris = datasets.load_iris()
    X, y = iris.data, iris.target
    clf.fit(X, y)

Il metodo usato per distribuire i modelli di Python in Azure Machine Learning consiste nell'eseguire il wrapping della stima del modello in una funzione e nel decorarla con gli attributi forniti dalla libreria di Azure Machine Learning che identificano l'ID, la chiave API, i parametri di input e i parametri restituiti dell'area di lavoro di Azure Machine Learning.  

    from azureml import services
    @services.publish(workspaceid, auth_token)
    @services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
    @services.returns(int) #0, or 1, or 2
    def predictIris(sep_l, sep_w, pet_l, pet_w):
     inputArray = [sep_l, sep_w, pet_l, pet_w]
    return clf.predict(inputArray)

Un client può ora effettuare chiamate al servizio Web. Esistono pratici wrapper che costruiscono le richieste dell'API REST. Ecco un codice di esempio per utilizzare il servizio Web.

    # Consume through web service URL and keys
    from azureml import services
    @services.service(url, api_key)
    @services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
    @services.returns(float)
    def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
    pass

    IrisPredictor(3,2,3,4)


> [!NOTE]
> La libreria di Azure Machine Learning è attualmente supportata solo in Python 2.7.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>Compilare e rendere operativi i modelli R
È possibile distribuire in Azure Machine Learning i modelli R compilati nella macchina virtuale per l'analisi scientifica dei dati o altrove in modo simile a come è stato fatto per Python. Ecco i passaggi necessari:

* Creare un file settings.json come quello seguente per fornire l'ID area di lavoro e il token di autorizzazione.
* Scrivere un wrapper per la funzione di stima del modello.
* Chiamare ```publishWebService``` nella libreria di Azure Machine Learning per passare il wrapper della funzione.  

Ecco la procedura e i frammenti di codice che è possibile usare per configurare, creare, pubblicare e usare un modello come servizio Web in Azure Machine Learning.

#### <a name="setup"></a>Configurazione
1. Installare il pacchetto R di Machine Learning digitando ```install.packages("AzureML")``` nell'IDE Revolution R Enterprise 8.0 o nell'IDE R.
2. Scaricare RTools da [qui](https://cran.r-project.org/bin/windows/Rtools/). Per rendere operativo il pacchetto R in Machine Learning, l'utilità zip, denominata zip.exe, deve trovarsi nel percorso.
3. Nella home directory creare un file settings.json in una directory denominata ```.azureml``` e immettere i parametri dall'area di lavoro di Azure Machine Learning:

Struttura del file settings.json:

    {"workspace":{
    "id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
    "authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
    }}


#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>Compilare un modello in R e pubblicarlo in Azure Machine Learning
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

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Utilizzare il modello distribuito in Azure Machine Learning
Per usare il modello da un'applicazione client si usa la libreria di Azure Machine Learning per cercare per nome il servizio Web pubblicato usando la chiamata API `services` per determinare l'endpoint. Sarà quindi sufficiente chiamare la funzione `consume` e passare il frame di dati di cui eseguire la stima.
Il codice seguente consente di utilizzare il modello pubblicato come servizio Web di Azure Machine Learning.

    library(AzureML)
    library(lme4)
    ws <- workspace(config="~/.azureml/settings.json")

    s <-  services(ws, name = "sleepy lm")
    s <- tail(s, 1) # use the last published function, in case of duplicate function names

    ep <- endpoints(ws, s)

    # OK, try this out, and compare with raw data
    ans = consume(ep, sleepstudy)$ans

Altre informazioni sulla libreria R di Azure Machine Learning sono disponibili [qui](https://cran.r-project.org/web/packages/AzureML/AzureML.pdf).

## <a name="4-administer-your-azure-resources-using-azure-portal-or-powershell"></a>4. Amministrare le risorse di Azure usando il portale di Azure o PowerShell
DSVM non solo consente di compilare la soluzione di analisi in locale nella macchina virtuale, ma anche di accedere ai servizi nel cloud di Microsoft Azure. Azure offre diversi servizi di calcolo, archiviazione e analisi dei dati e altri servizi amministrabili e accessibili da DSVM.

Per amministrare la sottoscrizione di Azure e le risorse cloud, è possibile usare il browser e accedere al [portale di Azure](https://portal.azure.com). È anche possibile usare Azure PowerShell per amministrare le risorse e la sottoscrizione di Azure con uno script.
È possibile eseguire Azure PowerShell da un collegamento "Microsoft Azure PowerShell" sul desktop o nel menu Start. Per altre informazioni su come amministrare le risorse e la sottoscrizione di Azure con gli script di Windows PowerShell, vedere la [documentazione di Microsoft Azure PowerShell](../powershell-azure-resource-manager.md) .

## <a name="5-extend-your-storage-space-with-a-shared-file-system"></a>5. Estendere lo spazio di archiviazione con un file system condiviso
Gli esperti di gestione dati possono condividere grandi set di dati, codice o alte risorse all'interno del team. In DSVM sono disponibili circa 70 GB di spazio. Per estendere lo spazio di archiviazione, è possibile usare il Servizio file di Azure e installarlo in DSVM o accedervi tramite l'API REST.   

> [!NOTE]
> La quantità massima di spazio della condivisione del Servizio file di Azure è di 5 TB e il limite delle dimensioni dei singoli file è di 1 TB.   
> 
> 

È possibile usare Azure Powershell per creare una condivisione del Servizio file di Azure. Ecco lo script da eseguire in Azure PowerShell per creare una condivisione del Servizio file di Azure.

    # Authenticate to Azure.
    Login-AzureRmAccount
    # Select your subscription
    Get-AzureRmSubscription –SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
    # Create a new resource group.
    New-AzureRmResourceGroup -Name <dsvmdatarg>
    # Create a new storage account. You can reuse existing storage account if you wish.
    New-AzureRmStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
    # Set your current working storage account
    Set-AzureRmCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

    # Create a Azure File Service Share
    $s = New-AzureStorageShare <<teamsharename>>
    # Create a directory under the FIle share. You can give it any name
    New-AzureStorageDirectory -Share $s -Path <directory name>
    # List the share to confirm that everything worked
    Get-AzureStorageFile -Share $s


A questo punto, dopo avere creato una condivisione file di Azure, è possibile installarla in una macchina virtuale in Azure. È consigliabile che la VM sia nello stesso data center di Azure dell'account di archiviazione per evitare la latenza e gli addebiti per il trasferimento dei dati. Ecco i comandi per installare l'unità in DSVM, che è possibile eseguire in Azure PowerShell.

    # Get storage key of the storage account that has the Azure file share from Azure portal. Store it securely on the VM to avoid prompted in next command.
    cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

    # Mount the Azure file share as Z: drive on the VM. You can chose another drive letter if you wish
    net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>


Ora è possibile accedere a questa unità come a qualsiasi altra unità della VM.

## <a name="6-share-code-with-your-team-using-github"></a>6. Condividere il codice con il team usando GitHub
GitHub è un archivio di codice dove è possibile trovare numerosi codici di esempio e origini per strumenti diversi usando svariate tecnologie condivise dalla community degli sviluppatori. Usa Git come tecnologia per tenere traccia delle versioni dei file di codice e per archiviarle. GitHub è anche una piattaforma in cui è possibile creare il proprio archivio in cui archiviare la documentazione e il codice condiviso del team, implementare il controllo della versione e anche controllare chi ha l'accesso per visualizzare il codice e per contribuirvi. Per altre informazioni sull'uso di Git, visitare le [pagine della guida di GitHub](https://help.github.com/). È possibile usare GitHub per collaborare con il team, usare il codice sviluppato dalla community e contribuire al codice della community.

In DSVM sono già caricati gli strumenti client sia nella riga di comando che nella GUI per accedere all'archivio GitHub. Lo strumento da riga di comando per usare Git e GitHub si chiama Git Bash. Visual Studio installato in DSVM include le estensioni Git. È possibile trovare le icone di avvio per questi strumenti nel menu Start e sul desktop.

Per scaricare il codice da un archivio GitHub, usare il comando ```git clone```. Ad esempio, per scaricare il repository di analisi scientifica dei dati pubblicato da Microsoft nella directory corrente, è possibile eseguire il comando seguente in ```git-bash```.

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

In Visual Studio è possibile eseguire la stessa operazione di clonazione. Per accedere agli strumenti Git e GitHub in Visual Studio, vedere lo screenshot seguente.

![Git in Visual Studio](./media/machine-learning-data-science-vm-do-ten-things/VSGit.PNG)

In github.com sono disponibili altre informazioni sull'uso di Git per lavorare con l'archivio GitHub da diverse risorse. Il [foglio informativo](https://training.github.com/kit/downloads/github-git-cheat-sheet.pdf) è un riferimento utile.

## <a name="7-access-various-azure-data-and-analytics-services"></a>7. Accedere a diversi servizi dati e analisi di Azure
### <a name="azure-blob"></a>BLOB Azure
BLOB di Azure è una risorsa di archiviazione cloud conveniente e affidabile per piccole e grandi quantità di dati. Si vedrà ora come è possibile spostare dati nel BLOB di Azure e accedere ai dati archiviati in un BLOB di Azure.

**Prerequisito**

* **Creare l'account di archiviazione BLOB di Azure nel [portale di Azure](https://portal.azure.com).**

![Create_Azure_Blob](./media/machine-learning-data-science-vm-do-ten-things/Create_Azure_Blob.PNG)

* Verificare che lo strumento da riga di comando AzCopy preinstallato sia disponibile in ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```. È possibile aggiungere la directory contenente il file azcopy.exe alla variabile di ambiente PATH per evitare di digitare il percorso completo del comando quando si esegue lo strumento. Per altre informazioni su AzCopy, vedere la [documentazione di AzCopy](../storage/storage-use-azcopy.md)
* Avviare lo strumento Esplora archivi di Azure. È possibile scaricarlo da [Esplora archivi di Microsoft Azure](http://storageexplorer.com/). 

![AzureStorageExplorer_v4](./media/machine-learning-data-science-vm-do-ten-things/AzureStorageExplorer_v4.png)

**Spostare i dati dalla VM al BLOB di Azure: AzCopy**

Per spostare i dati tra i file locali e l'archivio BLOB, è possibile usare AzCopy nella riga di comando o in PowerShell:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Sostituire **C:\myfolder** con il percorso di archiviazione del file, **mystorageaccount** con il nome dell'account di archiviazione BLOB, **mycontainer** con il nome del contenitore e **storage account key** con la chiave di accesso alle risorse di archiviazione BLOB. È possibile trovare le credenziali dell'account di archiviazione nel [portale di Azure](https://portal.azure.com).

![StorageAccountCredential_v2](./media/machine-learning-data-science-vm-do-ten-things/StorageAccountCredential_v2.png)

Eseguire il comando AzCopy in PowerShell o al prompt dei comandi. Ecco un esempio di utilizzo del comando AzCopy:

    # Copy *.sql from local machine to a Azure Blob
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

    # Copy back all files from Azure Blob container to Local machine

    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S



Una volta eseguito il comando AzCopy per la copia in un BLOB di Azure, il file viene visualizzato in Azure Storage Explorer entro breve.

![AzCopy_run_finshed_Storage_Explorer_v3](./media/machine-learning-data-science-vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

**Spostare i dati dalla VM al BLOB di Azure: Azure Storage Explorer**

È anche possibile caricare i dati dal file locale nella VM usando Azure Storage Explorer:

* Per caricare dati in un contenitore, selezionare il contenitore di destinazione e fare clic sul pulsante **Carica**.![Caricare in Storage Explorer](./media/machine-learning-data-science-vm-do-ten-things/storage-accounts.png)
* Fare clic su **...** a destra della casella **File**, selezionare uno o più file da caricare dal file system e fare clic su **Carica** per iniziare a caricare i file.![Caricare i file nel BLOB](./media/machine-learning-data-science-vm-do-ten-things/upload-files-to-blob.png)

**Leggere i dati dal BLOB di Azure: modulo Reader di Machine Learning**

In Azure Machine Learning Studio è possibile usare un **modulo Import Data** per leggere i dati dal BLOB.

![AML_ReaderBlob_Module_v3](./media/machine-learning-data-science-vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

**Leggere i dati dal BLOB di Azure: ODBC Python**

È possibile usare la libreria **BlobService** per leggere i dati direttamente dal BLOB in Jupyter Notebook o in un programma Python.

Prima di tutto importare i pacchetti necessari:

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

Quindi collegare le credenziali dell'account BLOB di Azure e leggere i dati dal BLOB:

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

    #unzipping downloaded files if needed
    #with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
    #    z.extractall(LOCALDIRECTORY)

    df1 = pd.read_csv(LOCALFILE, header=0)
    df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
    print 'the size of the data is: %d rows and  %d columns' % df1.shape

I dati vengono letti come frame di dati:

![IPNB_data_readin](./media/machine-learning-data-science-vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Un Archivio Azure Data Lake è un repository con iperscalabilità per i carichi di lavoro di analisi dei Big Data compatibile con Hadoop Distributed File System (HDFS). Funziona con l'ecosistema Hadoop e con Azure Data Lake Analytics. Verrà illustrato come è possibile spostare i dati nell'Archivio Azure Data Lake ed eseguire analisi con Analisi Azure Data Lake.

**Prerequisito**

* Creare Azure Data Lake Analytics nel [portale di Azure](https://portal.azure.com).

![Azure_Data_Lake_Create_v2](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* **Azure Data Lake Tools** in **Visual Studio** disponibile da questo [collegamento](https://www.microsoft.com/download/details.aspx?id=49504) è già installato in Visual Studio Community Edition nella macchina virtuale. Dopo avere avviato Visual Studio e avere effettuato l'accesso alla sottoscrizione di Azure, l'archivio e l'account di Azure Data Analytics verranno visualizzati nel panello sinistro di Visual Studio.

![Azure_Data_Lake_PlugIn_v2](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

**Spostare i dati dalla VM a Data Lake: Azure Data Lake Explorer**

È possibile usare **Azure Data Lake Explorer** per caricare i dati dai file locali della macchina virtuale nell'archivio di Data Lake.

![Azure_Data_Lake_UploadData](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Lake_UploadData.PNG)

È anche possibile creare una pipeline di dati per lo spostamento dei dati da e verso Azure Data Lake tramite [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/). Per una guida dettagliata della compilazione di pipeline di dati, vedere questo [articolo](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) .

**Leggere i dati dal BLOB di Azure a Data Lake: U-SQL**

Se i dati si trovano nell'archivio BLOB di Azure, è possibile leggerli direttamente dal BLOB di archiviazione di Azure nella query U-SQL. Prima di scrivere la query U-SQL, verificare che l'account di archiviazione BLOB sia collegato ad Azure Data Lake. Passare al **portale di Azure**, individuare il dashboard di Azure Data Lake Analytics, fare clic su **Aggiungi origine dati**, selezionare **Archiviazione di Azure** come tipo di archiviazione e inserire il nome e la chiave dell'account di archiviazione di Azure. Sarà quindi possibile fare riferimento ai dati archiviati nell'account di archiviazione.

![Immettere l'account di archiviazione e la chiave](./media/machine-learning-data-science-vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

In Visual Studio è possibile leggere i dati dall'archivio BLOB, modificarli, progettare funzionalità e pubblicare i dati risultanti in Azure Data Lake o nell'archivio BLOB di Azure. Quando si fa riferimento ai dati nell'archiviazione BLOB, usare **wasb://**; quando si fa riferimento ai dati in Azure Data Lake, usare **swbhdfs://**

![Frame di dati](./media/machine-learning-data-science-vm-do-ten-things/USQL_Read_Blob_v2.PNG)

È possibile usare le query U-SQL seguenti in Visual Studio:

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



Dopo che la query sarà stata inviata al server, verrà visualizzato un diagramma che illustra lo stato del processo.

![Diagramma dello stato del processo](./media/machine-learning-data-science-vm-do-ten-things/USQL_Job_Status.PNG)

**Effettuare una query dei dati in Data Lake: U-SQL**

Dopo che il set di dati è stato inserito in Azure Data Lake, è possibile usare il [linguaggio U-SQL](../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) per l'esecuzione di query e l'esplorazione dei dati. Il linguaggio U-SQL è simile a T-SQL, ma combina alcune funzionalità di C# in modo che gli utenti possano scrivere moduli personalizzati, funzioni definite dall'utente e così via. È possibile usare gli script del passaggio precedente.

Dopo l'invio della query al server, in **Azure Data Lake Explorer** sarà disponibile il file tripdata_summary.CSV. Per visualizzare i dati in anteprima, fare clic sul file con il pulsante destro del mouse.

![File in Azure Data Lake Explorer](./media/machine-learning-data-science-vm-do-ten-things/USQL_create_summary.png)

Per visualizzare le informazioni del file:

![Riepilogo del file](./media/machine-learning-data-science-vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>Cluster Hadoop di HDInsight
Azure HDInsight è un servizio di Apache Hadoop, Spark, HBase e Storm gestito nel cloud. È possibile utilizzare facilmente i cluster Azure HDInsight dalla macchina virtuale di analisi scientifica dei dati.

**Prerequisito**

* Creare l'account di archiviazione BLOB di Azure nel [portale di Azure](https://portal.azure.com). Questo account di archiviazione viene usato per archiviare i dati per i cluster HDInsight.

![Creare un account di archiviazione BLOB di Azure](./media/machine-learning-data-science-vm-do-ten-things/Create_Azure_Blob.PNG)

* Personalizzare i cluster Hadoop di Azure HDInsight nel [portale di Azure](machine-learning-data-science-customize-hadoop-cluster.md)
  
  * È necessario collegare l'account di archiviazione creato al cluster HDInsight al momento della creazione. Questo account di archiviazione viene usato per accedere ai dati che possono essere elaborati all'interno del cluster.

![Collegamento all'account di archiviazione creato con il cluster HDInsight](./media/machine-learning-data-science-vm-do-ten-things/Create_HDI_v4.PNG)

* È necessario abilitare l' **accesso remoto** al nodo head del cluster dopo la creazione. Ricordare le credenziali di accesso remoto specificate in questa posizione (diverse da quelle specificate per il cluster durante la creazione), che saranno necessarie in seguito.

![Abilitare l'accesso remoto](./media/machine-learning-data-science-vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Creare un'area di lavoro di Azure Machine Learning. Gli esperimenti di Machine Learning verranno archiviati in questa area di lavoro di Machine Learning. Selezionare le opzioni evidenziate nel portale, come illustrato nello screenshot seguente.

![Creare un'area di lavoro di Machine Learning di Azure](./media/machine-learning-data-science-vm-do-ten-things/Create_ML_Space.PNG)

* Immettere quindi i parametri per l'area di lavoro

![Immettere i parametri dell'area di lavoro di Machine Learning](./media/machine-learning-data-science-vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* Caricare i dati con IPython Notebook. Importare prima di tutto i pacchetti necessari, inserire le credenziali, creare un database nell'account di archiviazione, quindi caricare i dati nei cluster HDI.

        #Import required Packages
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


        #Create the connection to Hive using ODBC
        SERVER_NAME='xxx.azurehdinsight.net'
        DATABASE_NAME='nyctaxidb'
        USERID='xxx'
        PASSWORD='xxxx'
        DB_DRIVER='Microsoft Hive ODBC Driver'
        driver = 'DRIVER={' + DB_DRIVER + '}'
        server = 'Host=' + SERVER_NAME + ';Port=443'
        database = 'Schema=' + DATABASE_NAME
        hiveserv = 'HiveServerType=2'
        auth = 'AuthMech=6'
        uid = 'UID=' + USERID
        pwd = 'PWD=' + PASSWORD
        CONNECTION_STRING = ';'.join([driver,server,database,hiveserv,auth,uid,pwd])
        connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
        cursor=connection.cursor()


        #Create Hive database and tables
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


        #Upload data from blob storage to HDI cluster
        for i in range(1,13):
            queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);"%(i,i)
            cursor.execute(queryString)
            queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);"%(i,i)  
            cursor.execute(queryString)


* In alternativa è possibile seguire questa [procedura dettagliata](machine-learning-data-science-process-hive-walkthrough.md) per caricare i dati relativi ai taxi di New York nel cluster HDI. I passaggi più importanti includono:
  
  * AzCopy: scaricare i file CSV compressi dal BLOB pubblico alla cartella locale
  * AzCopy: caricare i file CSV decompressi dalla cartella locale al cluster HDI
  * Accedere al nodo head del cluster Hadoop e preparare l'analisi esplorativa dei dati

Dopo aver caricato i dati nel cluster HDI, è possibile controllarli in Azure Storage Explorer. Nel cluster HDI è stato creato un database nyctaxidb.

**Esplorazione dei dati: query Hive in Python**

Poiché i dati si trovano nel cluster Hadoop, è possibile usare il pacchetto pyodbc per connettersi ai cluster Hadoop ed eseguire una query sul database con Hive per l'esplorazione e la progettazione di funzionalità. È possibile visualizzare le tabelle esistenti create nel passaggio del prerequisito.

    queryString = """
        show tables in nyctaxidb2;
        """
    pd.read_sql(queryString,connection)


![Visualizzare tabelle esistenti](./media/machine-learning-data-science-vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Verrà ora esaminato il numero di record per ogni mese e le frequenze delle corse per le quali è stata lasciata una mancia o meno nella tabella delle corse:

    queryString = """
        select month, count(*) from nyctaxidb.trip group by month;
        """
    results = pd.read_sql(queryString,connection)

    %matplotlib inline

    results.columns = ['month', 'trip_count']
    df = results.copy()
    df.index = df['month']
    df['trip_count'].plot(kind='bar')


![Tracciato del numero di record in ogni mese](./media/machine-learning-data-science-vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

    queryString = """
        SELECT tipped, COUNT(*) AS tip_freq
        FROM
        (
            SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
            FROM nyctaxidb.fare
        )tc
        GROUP BY tipped;
        """
    results = pd.read_sql(queryString,connection)

    results.columns = ['tipped', 'trip_count']
    df = results.copy()
    df.index = df['tipped']
    df['trip_count'].plot(kind='bar')


![Tracciato della frequenze delle mance](./media/machine-learning-data-science-vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

È anche possibile calcolare la distanza tra il punto di partenza e il punto di arrivo e quindi confrontarla con la distanza della corsa.

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
    results = pd.read_sql(queryString,connection)
    results.head(5)


![Tabella di punti di partenza e punti di arrivo](./media/machine-learning-data-science-vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

    results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                       'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
    df = results.loc[results['trip_distance']<=100] #remove outliers
    df = df.loc[df['direct_distance']<=100] #remove outliers
    plt.scatter(df['direct_distance'], df['trip_distance'])


![Tracciato della distanza partenza/arrivo rispetto alla distanza delle corse](./media/machine-learning-data-science-vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Verrà ora preparato un set di dati sottocampionati (1%) per la modellazione. È possibile usare questi dati nel modulo Reader di Machine Learning.

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

        --- now insert contents of the join into the above internal table

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

Dopo poco, è possibile vedere che i dati sono stati caricati nei cluster Hadoop:

    queryString = """
        select * from nyctaxi_downsampled_dataset limit 10;
        """
    cursor.execute(queryString)
    pd.read_sql(queryString,connection)


![Tabella di dati](./media/machine-learning-data-science-vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

**Leggere i dati da HDI con il modulo Reader di Machine Learning**

È anche possibile usare il modulo **Reader** in Machine Learning Studio per accedere al database nel cluster Hadoop. Collegare le credenziali dei cluster HDI e l'account di archiviazione di Azure per poter compilare modelli di Machine Learning usando il database nei cluster HDI.

![Proprietà del modulo Reader](./media/machine-learning-data-science-vm-do-ten-things/AML_Reader_Hive.PNG)

È quindi possibile visualizzare il set di dati con i punteggi:

![Visualizzare il set di dati con i punteggi](./media/machine-learning-data-science-vm-do-ten-things/AML_Model_Results.PNG)

### <a name="azure-sql-data-warehouse--databases"></a>Azure SQL Data Warehouse e database
Azure SQL Data Warehouse è un data warehouse elastico distribuito come servizio con l'esperienza di classe enterprise di SQL Server.

È possibile effettuare il provisioning di Azure SQL Data Warehouse seguendo le istruzioni fornite in questo [articolo](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). Dopo il provisioning di Azure SQL Data Warehouse, è possibile seguire questa [procedura dettagliata](machine-learning-data-science-process-sqldw-walkthrough.md) per il caricamento dei dati, l'esplorazione e la modellazione usando i dati disponibili in SQL Data Warehouse.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB è un database NoSQL sul cloud. Consente di utilizzare documenti come JSON e di archiviarli ed eseguire query su di essi.

Per accedere ad Azure Cosmos DB da DSVM, è necessario eseguire questa procedura preliminare.

1. Installare l'SDK DocumentDB Python eseguendo ```pip install pydocumentdb``` al prompt dei comandi
2. Creare l'account e il database Azure Cosmos DB nel [portale di Azure](https://portal.azure.com)
3. Scaricare "Azure Cosmos DB Migration Tool" da [qui](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) ed estrarlo nella directory desiderata
4. Importare in Cosmos DB i dati JSON (dati sui vulcani) archiviati in un [BLOB pubblico](https://cahandson.blob.core.windows.net/samples/volcano.json) con i parametri di comando seguenti per lo strumento di migrazione, ovvero dtui.exe dalla directory in cui è stato installato Cosmos DB Migration Tool. Immettere i parametri dei percorsi di origine e di destinazione seguenti.
   
    /s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1

Dopo avere importato i dati è possibile passare a Jupyter e aprire il notebook denominato *DocumentDBSample* contenente il codice Python per accedere a DocumentDB ed eseguire alcune query di base. Per altre informazioni su DocumentDB, vedere la [pagina della documentazione](https://azure.microsoft.com/documentation/learning-paths/documentdb/)

## <a name="8-build-reports-and-dashboard-using-the-power-bi-desktop"></a>8. Compilare report e dashboard usando Power BI Desktop
Verrà ora visualizzato in Power BI il file JSON sui vulcani usato nell'esempio precedente di Cosmos DB per ottenere informazioni visive sui dati. I passaggi dettagliati sono disponibili nell' [articolo relativo a Power BI](../documentdb/documentdb-powerbi-visualize.md). I passaggi generali sono i seguenti:

1. Aprire Power BI Desktop ed eseguire "Recupera dati". Specificare l'URL come: https://cahandson.blob.core.windows.net/samples/volcano.json
2. Verranno visualizzati i record JSON importati come elenco.
3. Convertire l'elenco in una tabella in modo che Power BI possa usare gli stessi dati.
4. Espandere le colonne facendo clic sull'icona di espansione, ovvero l'icona con "una freccia verso sinistra e una freccia verso destra" a destra della colonna.
5. La posizione è un campo "Record". Espandere il record e selezionare solo le coordinate. La coordinata è una colonna elenco.
6. Aggiungere una nuova colonna per convertire la colonna dell'elenco di coordinate in una colonna LatLong con valori delimitati da virgole concatenando i due elementi nel campo dell'elenco di coordinate con la formula ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```.
7. Convertire infine la colonna ```Elevation``` in Decimali e fare clic su **Chiudi** e **Applica**.

Invece di eseguire i passaggi precedenti, è possibile incollare il codice seguente che inserisce nello script i passaggi precedenti nell'editor avanzato in Power BI che consente di scrivere le trasformazioni dei dati in un linguaggio di query.

    let
        Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
        #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
        #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
        #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
        #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
        #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
    in
        #"Changed Type"



I dati sono ora inclusi nel modello di dati di Power BI. Power BI Desktop dovrebbe avere un aspetto simile al seguente.

![Power BI Desktop](./media/machine-learning-data-science-vm-do-ten-things/PowerBIVolcanoData.png)

È possibile avviare la compilazione di report e visualizzazioni usando il modello di dati. Per compilare un report, è possibile seguire i passaggi in questo [articolo relativo a Power BI](../documentdb/documentdb-powerbi-visualize.md#build-the-reports) . Il risultato finale sarà un report simile al seguente.

![Visualizzazione report di Power BI Desktop - Connettore Power BI](./media/machine-learning-data-science-vm-do-ten-things/power_bi_connector_pbireportview2.png)

## <a name="9-dynamically-scale-your-dsvm-to-meet-your-project-needs"></a>9. Ridimensionare in modo dinamico DSVM per poter soddisfare le esigenze del progetto
È possibile aumentare e ridurre le prestazioni di DSVM per soddisfare le esigenze del progetto. Se non è necessario usare la VM la sera o nei fine settimana, è sufficiente arrestarla dal [portale di Azure](https://portal.azure.com).

> [!NOTE]
> Verranno addebitati i costi di calcolo se si usa solo il pulsante di arresto del sistema operativo nella VM.  
> 
> 

Se è necessario gestire analisi su larga scala e si ha bisogno di più CPU e/o memoria e/o capacità disco, è possibile trovare un'ampia scelta di dimensioni VM in termini di core CPU, capacità di memoria e tipi di disco (incluse le unità SSD) che soddisfano le esigenze di calcolo e di budget. L'elenco completo di VM con i rispettivi prezzi di calcolo orario è disponibile nella pagina [Prezzi di Macchine virtuali di Azure](https://azure.microsoft.com/pricing/details/virtual-machines/) .

Allo stesso modo, se le esigenze di capacità di elaborazione della VM diminuiscono, ad esempio se si è spostato un carico di lavoro principale in un cluster Hadoop o Spark, è possibile passare a un piano inferiore per il cluster dal [portale di Azure](https://portal.azure.com) tramite le impostazioni dell'istanza della VM. Ecco uno screenshot.

![Impostazioni dell'istanza della VM](./media/machine-learning-data-science-vm-do-ten-things/VMScaling.PNG)

## <a name="10-install-additional-tools-on-your-virtual-machine"></a>10. Installare strumenti aggiuntivi nella macchina virtuale
Sono stati inclusi in un pacchetto diversi strumenti che permettono di soddisfare molte delle comuni esigenze di analisi dei dati e di risparmiare tempo, perché si evita di dover installare e configurare gli ambienti uno alla volta, nonché denaro perché si pagano solo le risorse usate.

È possibile sfruttare altri servizi dati e di analisi di Azure, come illustrato in questo articolo, per migliorare l'ambiente di analisi. È probabile che in alcuni casi siano necessari strumenti aggiuntivi, inclusi alcuni strumenti proprietari di terze parti. L'accesso amministrativo completo alla macchina virtuale consente di installare i nuovi strumenti necessari. È anche possibile installare pacchetti aggiuntivi in Python e in R non preinstallati. Per Python è possibile usare ```conda``` o ```pip```. Per R è possibile usare ```install.packages()``` nella console di R oppure usare l'IDE e scegliere "**Packages** -> **Install Packages...**" (Pacchetti -> Installa pacchetti...).

## <a name="summary"></a>Riepilogo
Queste sono solo alcune delle attività che è possibile eseguire nella macchina virtuale per l'analisi scientifica dei dati di Microsoft. Ce ne sono molte altre che è possibile eseguire per renderla un ambiente di analisi efficace.


