---
title: Esplorare i dati e il modello in Windows
titleSuffix: Azure Data Science Virtual Machine
description: Eseguire attività di esplorazione e modellazione dei dati nel Data Science Virtual Machine di Windows.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: dba0f22f1dfece7edd92a80ae1c3a91616a4236c
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815913"
---
# <a name="data-science-with-a-windows-data-science-virtual-machine"></a>Data Science con Windows Data Science Virtual Machine

Windows Data Science Virtual Machine (DSVM) è un potente ambiente di sviluppo data science in cui è possibile eseguire attività di esplorazione e modellazione dei dati. L'ambiente è già compilato e in bundle con diversi strumenti di analisi dei dati diffusi che consentono di iniziare a usare in modo semplice l'analisi per distribuzioni locali, cloud o ibride. 

Il DSVM funziona a stretto contatto con i servizi di Azure. Consente di leggere ed elaborare i dati già archiviati in Azure, in una sinapsi di Azure (in precedenza SQL DW), Azure Data Lake, archiviazione di Azure o Azure Cosmos DB. Può anche sfruttare altri strumenti di analisi, ad esempio Azure Machine Learning.

In questo articolo si apprenderà come usare il DSVM per eseguire data science attività e interagire con altri servizi di Azure. Ecco alcune attività che è possibile eseguire con DSVM:

- Usare un notebook di Jupyter per sperimentare i dati in un browser usando Python 2, Python 3 e Microsoft R. Microsoft R è una versione aziendale di R progettata per migliorare le prestazioni.
- Esplorare i dati e sviluppare modelli in locale in DSVM usando Microsoft Machine Learning Server e Python.
- Amministrare le risorse di Azure usando il portale di Azure o PowerShell.
- Estendi lo spazio di archiviazione e Condividi set di impostazioni/codice su larga scala nell'intero team creando una condivisione File di Azure come unità montabile nella tua DSVM.
- Condividere il codice con il team usando GitHub. Accedere al repository usando i client Git preinstallati: git bash e git GUI.
- Accedi ai servizi dati e analisi di Azure, ad esempio archiviazione BLOB di Azure, Azure Cosmos DB, sinapsi di Azure (in precedenza SQL DW) e database SQL di Azure.
- Creare report e un dashboard usando l'istanza di Power BI Desktop preinstallata in DSVM e distribuirli nel cloud.

- Installare altri strumenti nella macchina virtuale.   

> [!NOTE]
> Per molti dei servizi di archiviazione e analisi dei dati elencati in questo articolo sono applicati addebiti aggiuntivi sull'utilizzo. Per informazioni dettagliate, vedere la pagina dei [prezzi di Azure](https://azure.microsoft.com/pricing/) .
> 
> 

## <a name="prerequisites"></a>Prerequisiti

* È necessaria una sottoscrizione di Azure. È possibile [iscriversi per una versione di valutazione gratuita di Azure](https://azure.microsoft.com/free/).
* Per la [creazione di una macchina virtuale](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016)sono disponibili istruzioni per il provisioning di un Data Science Virtual Machine nel portale di Azure.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="use-jupyter-notebooks"></a>Usare i notebook di Jupyter
Il Jupyter Notebook offre un IDE basato sul browser per l'esplorazione e la modellazione dei dati. È possibile usare Python 2, Python 3 o R (sia open source che Microsoft R Server) in un notebook di Jupyter.

Per avviare la Jupyter Notebook, selezionare l'icona **Jupyter notebook** dal menu **Start** o sul desktop. Al prompt dei comandi di DSVM è anche possibile eseguire il comando ```jupyter notebook``` dalla directory in cui sono presenti notebook o in cui si desidera creare nuovi notebook.  

Dopo l'avvio di Jupyter, passare alla `/notebooks` Directory per i notebook di esempio pre-assemblati in DSVM. A questo punto è possibile:

* Selezionare il notebook per visualizzare il codice.
* Eseguire ogni cella selezionando MAIUSC + INVIO.
* Eseguire l'intero notebook selezionando **Cell**  >  **esecuzione**cella.
* Creare un nuovo notebook selezionando l'icona Jupyter (angolo superiore sinistro), selezionando il pulsante **nuovo** a destra e quindi scegliendo la lingua del notebook (nota anche come kernel).   

> [!NOTE]
> Attualmente sono supportati i kernel Python 2,7, Python 3,6, R, Julia e PySpark in Jupyter. Il kernel R supporta la programmazione sia in R open source che in Microsoft R.   
> 
> 

Quando ci si trova nel notebook, è possibile esplorare i dati, compilare il modello ed eseguire il test del modello usando le librerie scelte.

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Esplorare i dati e sviluppare modelli con Microsoft Machine Learning Server
È possibile usare linguaggi come R e Python per eseguire l'analisi dei dati direttamente in DSVM.

Per R, è possibile usare un IDE come RStudio, disponibile nel menu Start o sul desktop. In alternativa, è possibile usare R Tools per Visual Studio. Microsoft ha fornito librerie aggiuntive oltre all'Open Source CRAN R per consentire l'analisi scalabile e la possibilità di analizzare i dati di dimensioni superiori a quelle consentite nell'analisi parallela in blocchi. 

Per Python è possibile usare un IDE come Visual Studio Community Edition, in cui è preinstallata l'estensione Python Tools for Visual Studio (PTVS). Per impostazione predefinita, in PTVS è configurato solo Python 3,6, l'ambiente conda radice. Per abilitare Anaconda Python 2,7, seguire questa procedura:

1. Per creare ambienti personalizzati per ogni versione, passare a **strumenti**strumenti  >  **Python**  >  **ambienti Python**e quindi selezionare **+ personalizzato** in Visual Studio Community Edition.
1. Assegnare una descrizione e impostare il percorso del prefisso dell'ambiente come **c:\anaconda\envs\python2** per anaconda Python 2,7.
1. Selezionare **rilevamento automatico**  >  **applica** per salvare l'ambiente.

Vedere la [documentazione di PTVS](https://aka.ms/ptvsdocs) per altre informazioni su come creare ambienti Python.

A questo punto è stato configurato per creare un nuovo progetto Python. Passare a **file**  >  **nuovo**  >  **progetto**  >  **Python** e selezionare il tipo di applicazione Python che si sta compilando. È possibile impostare l'ambiente Python per il progetto corrente sulla versione desiderata (Python 2,7 o 3,6) facendo clic con il pulsante destro del mouse su **ambienti Python** e quindi scegliendo **Aggiungi/Rimuovi ambienti Python**. Per ulteriori informazioni sull'utilizzo di PTVS, vedere la [documentazione del prodotto](https://aka.ms/ptvsdocs).



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

```bash
git clone https://github.com/Azure/DataScienceVM.git
```

In Visual Studio è possibile eseguire la stessa operazione di clonazione. Lo screenshot seguente illustra come accedere agli strumenti Git e GitHub in Visual Studio:

![Screenshot di Visual Studio con la connessione GitHub visualizzata](./media/vm-do-ten-things/VSGit.png)

È possibile trovare altre informazioni sull'uso di Git per lavorare con il repository GitHub dalle risorse disponibili in github.com. Il [foglio informativo](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) è un riferimento utile.

## <a name="access-azure-data-and-analytics-services"></a>Accedere ai servizi dati e analisi di Azure
### <a name="azure-blob-storage"></a>Archiviazione BLOB di Azure
Archiviazione BLOB di Azure è un servizio di archiviazione cloud affidabile e conveniente per i dati di grandi e piccole dimensioni. Questa sezione descrive come è possibile spostare i dati nell'archivio BLOB e accedere ai dati archiviati in un BLOB di Azure.

#### <a name="prerequisites"></a>Prerequisiti

* Creare l'account di archiviazione BLOB di Azure dal [portale di Azure](https://portal.azure.com).

   ![Screenshot del processo di creazione dell'account di archiviazione nell'portale di Azure](./media/vm-do-ten-things/create-azure-blob.png)

* Verificare che lo strumento AzCopy da riga di comando sia pre-installato: ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe``` . La directory che contiene azcopy.exe è già presente nella variabile di ambiente PATH, quindi è possibile evitare di digitare il percorso completo dei comandi quando si esegue questo strumento. Per ulteriori informazioni sullo strumento AzCopy, vedere la [documentazione di AzCopy](../../storage/common/storage-use-azcopy.md).
* Avviare lo strumento Esplora archivi di Azure. È possibile scaricarlo dalla  [pagina web Storage Explorer](https://storageexplorer.com/). 

   ![Screenshot di Azure Storage Explorer accedere a un account di archiviazione](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>Spostare i dati da una macchina virtuale a un BLOB di Azure: AzCopy

Per spostare i dati tra i file locali e l'archiviazione BLOB, è possibile usare AzCopy nella riga di comando o in PowerShell:

```powershell
AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt
```

Sostituire **c:\MyFolder.** con il percorso in cui è archiviato il file, **mystorageaccount** con il nome dell' **account di archiviazione BLOB, il** contenitore con il nome del contenitore e la chiave dell'account di **archiviazione** con la chiave di accesso all'archiviazione BLOB. È possibile trovare le credenziali dell'account di archiviazione nell' [portale di Azure](https://portal.azure.com).

Eseguire il comando AzCopy in PowerShell o da un prompt dei comandi. Di seguito è riportato un esempio di utilizzo del comando AzCopy:

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

Dopo aver eseguito il comando AzCopy per la copia in un BLOB di Azure, il file verrà visualizzato in Azure Storage Explorer.

![Screenshot dell'account di archiviazione, che Visualizza il file CSV caricato](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>Spostare i dati da una macchina virtuale a un BLOB di Azure: Azure Storage Explorer

È anche possibile caricare i dati dal file locale nella macchina virtuale usando Azure Storage Explorer:

* Per caricare i dati in un contenitore, selezionare il contenitore di destinazione e selezionare il pulsante **carica** . ![ Screenshot del pulsante carica in Azure Storage Explorer](./media/vm-do-ten-things/storage-accounts.png)
* Selezionare i puntini di sospensione (**...**) a destra della casella **file** , selezionare uno o più file da caricare dal file System e selezionare **carica** per iniziare a caricare i file. ![ Screenshot della finestra di dialogo Carica file](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>Leggere dati da un BLOB di Azure: Python ODBC

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

![Screenshot delle prime 10 righe di dati](./media/vm-do-ten-things/IPNB_data_readin.png)


### <a name="azure-synapse-analytics-formerly-sql-dw-and-databases"></a>Azure sinapsi Analytics (in precedenza SQL DW) e database
Azure sinapsi Analytics (noto in precedenza come SQL DW) è un data warehouse elastico come servizio con un'esperienza di SQL Server di livello aziendale.

È possibile eseguire il provisioning di Azure sinapsi Analytics seguendo le istruzioni riportate in [questo articolo](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). Dopo aver eseguito il provisioning del data warehouse SQL, è possibile usare [questa procedura dettagliata](../team-data-science-process/sqldw-walkthrough.md) per eseguire operazioni di caricamento, esplorazione e modellazione dei dati usando i dati all'interno di SQL data warehouse.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB è un database NoSQL sul cloud. È possibile usarlo per lavorare con documenti come JSON e per archiviare ed eseguire query sui documenti.

Usare i seguenti passaggi dei prerequisiti per accedere a Azure Cosmos DB da DSVM:

1. Il Azure Cosmos DB Python SDK è già installato in DSVM. Per aggiornarlo, eseguire ```pip install pydocumentdb --upgrade``` da un prompt dei comandi.
2. Creare un account e un database Azure Cosmos DB dal [portale di Azure](https://portal.azure.com).
3. Scaricare lo strumento di migrazione dei dati Azure Cosmos DB dall' [area download Microsoft](https://www.microsoft.com/download/details.aspx?id=53595) ed estrarlo in una directory di propria scelta.
4. Importare i dati JSON (Volcano Data) archiviati in un [BLOB pubblico](https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json) in Azure Cosmos DB con i parametri di comando seguenti per lo strumento di migrazione. Usare dtui.exe dalla directory in cui è stato installato lo strumento di migrazione dei dati Azure Cosmos DB. Immettere il percorso di origine e di destinazione con questi parametri:
   
    `/s:JsonFile /s.Files:https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

Dopo aver importato i dati, è possibile passare a Jupyter e aprire il notebook denominato *DocumentDBSample*. Contiene codice Python per accedere Azure Cosmos DB ed eseguire alcune query di base. Per ulteriori informazioni su Azure Cosmos DB, visitare la [pagina della documentazione](https://docs.microsoft.com/azure/cosmos-db/)del servizio.

## <a name="use-power-bi-reports-and-dashboards"></a>Usare report e dashboard di Power BI 
È possibile visualizzare il file JSON Volcano dall'esempio precedente di Azure Cosmos DB in Power BI Desktop per ottenere informazioni visive sui dati. I passaggi dettagliati sono disponibili nell' [articolo relativo a Power BI](../../cosmos-db/powerbi-visualize.md). Ecco i passaggi principali:

1. Aprire Power BI Desktop e selezionare **Recupera dati**. Specificare l'URL come: `https://cahandson.blob.core.windows.net/samples/volcano.json` .
2. Verranno visualizzati i record JSON importati come elenco. Convertire l'elenco in una tabella in modo che Power BI possibile utilizzarlo.
4. Espandere le colonne selezionando l'icona Espandi (freccia).
5. Si noti che il percorso è un campo di **record** . Espandere il record e selezionare solo le coordinate. **Coordinata** è una colonna dell'elenco.
6. Aggiungere una nuova colonna per convertire la colonna della coordinata elenco in una colonna **LatLong** con valori delimitati da virgole. Concatena i due elementi nel campo dell'elenco di coordinate usando la formula ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})``` .
7. Convertire la colonna di **elevazione** in decimale e selezionare i pulsanti **Chiudi** e **applica** .

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
> Vengono addebitati i costi di calcolo se si usa solo il pulsante di arresto per il sistema operativo della macchina virtuale. È invece necessario deallocare la DSVM usando il portale di Azure o Cloud Shell.
> 
> 

Potrebbe essere necessario gestire alcune analisi su larga scala e richiedere maggiore capacità di CPU, memoria o disco. In tal caso, è possibile trovare una scelta delle dimensioni delle macchine virtuali in termini di core CPU, istanze basate su GPU per l'apprendimento avanzato, capacità di memoria e tipi di disco (incluse le unità SSD) che soddisfano le esigenze di calcolo e di budget. L'elenco completo delle macchine virtuali, oltre ai prezzi di calcolo oraria, è disponibile nella pagina dei [prezzi di macchine virtuali di Azure](https://azure.microsoft.com/pricing/details/virtual-machines/) .


## <a name="add-more-tools"></a>Aggiungi altri strumenti
Gli strumenti predefiniti in DSVM possono soddisfare molte esigenze comuni di analisi dei dati. Questo consente di risparmiare tempo perché non è necessario installare e configurare gli ambienti uno alla volta. Ti consente anche di risparmiare denaro, perché paghi solo per le risorse che usi.

Per migliorare l'ambiente di analisi, è possibile usare altri servizi dati e analisi di Azure profilati in questo articolo. In alcuni casi, potrebbero essere necessari strumenti aggiuntivi, inclusi alcuni strumenti per partner proprietari. Si dispone dell'accesso amministrativo completo nella macchina virtuale per installare i nuovi strumenti necessari. È anche possibile installare pacchetti aggiuntivi in Python e in R non preinstallati. Per Python, è possibile usare ```conda``` o ```pip``` . Per r è possibile usare ```install.packages()``` nella console di r oppure usare l'IDE e selezionare **pacchetti**  >  **Installa pacchetti**.

## <a name="deep-learning"></a>Deep Learning

Oltre agli esempi basati su Framework, è possibile ottenere un set di procedure dettagliate complete che sono state convalidate nella DSVM. Queste procedure dettagliate consentono di iniziare a sviluppare applicazioni di apprendimento avanzato in domini come la comprensione delle immagini e del testo e della lingua.   


- [Esecuzione di reti neurali in diversi framework](https://github.com/ilkarman/DeepLearningFrameworks): questa procedura dettagliata illustra come eseguire la migrazione del codice da un Framework a un altro. Viene inoltre illustrato come confrontare i modelli e le prestazioni di runtime tra i Framework. 

- [Guida dettagliata alla creazione di una soluzione end-to-end per individuare prodotti all'interno di immagini](https://github.com/Azure/cortana-intelligence-product-detection-from-images): il rilevamento immagine è una tecnica che consente di individuare e classificare gli oggetti all'interno delle immagini. Questa tecnologia può comportare notevoli vantaggi in molti domini aziendali reali. Ad esempio, i negozianti possono usare questa tecnica per determinare quale prodotto un cliente ha prelevato dallo scaffale. Queste informazioni a loro volta consentono ai negozi di gestire l'inventario dei prodotti. 

- [Deep learning per audio](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): questa esercitazione illustra come eseguire il training di un modello di apprendimento approfondito per il rilevamento di eventi audio nel [set di dati dei suoni urbani](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html). Viene inoltre fornita una panoramica su come utilizzare i dati audio.

- [Classificazione dei documenti di testo](https://github.com/anargyri/lstm_han): in questa procedura dettagliata viene illustrato come compilare ed eseguire il training di due architetture di rete neurale: rete gerarchica attention Network e Long Short term memory (LSTM). Queste reti neurali usano l'API Keras per l'apprendimento avanzato per classificare i documenti di testo. 

## <a name="summary"></a>Riepilogo
In questo articolo sono state descritte alcune delle operazioni che è possibile eseguire in Microsoft Data Science Virtual Machine. Per rendere DSVM un ambiente di analisi efficace, è possibile eseguire molte altre operazioni.

