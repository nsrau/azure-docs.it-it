---
title: Leggere e scrivere file di dati di grandi dimensioni | Microsoft Docs
description: Leggere e scrivere file di grandi dimensioni negli esperimenti di Azure Machine Learning.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/10/2017
ms.openlocfilehash: f5c75b95d9019c15bb402313ce7407fa9abb81d4
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2017
---
# <a name="persisting-changes-and-working-with-large-files"></a>Rendere persistenti le modifiche e usare file di grandi dimensioni
Con il servizio Sperimentazione di Azure Machine Learning, è possibile configurare svariate destinazioni di esecuzione. Alcune destinazioni sono locali, ad esempio un computer locale o un contenitore Docker in un computer locale. Altre sono remote, ad esempio un contenitore Docker in un computer remoto o un cluster HDInsight. Per altre informazioni, vedere [Overview of Azure Machine Learning experiment execution service](experimentation-service-configuration.md) (Panoramica del servizio di esecuzione degli esperimenti di Azure Machine Learning). 

Prima di poter effettuare l'esecuzione in una destinazione, è necessario copiare la cartella del progetto nella destinazione di calcolo. Questa operazione è necessaria anche con un'esecuzione locale che usa una cartella temporanea locale a questo scopo. 

## <a name="execution-isolation-portability-and-reproducibility"></a>Isolamento, portabilità e riproducibilità dell'esecuzione
La finalità di questa progettazione è garantire la portabilità, la riproducibilità e l'isolamento dell'esecuzione. Se si esegue lo stesso script due volte nella stessa destinazione di calcolo o in un'altra, si ottengono gli stessi risultati. Le modifiche apportate durante la prima esecuzione non hanno impatto su quelle nella seconda. Questa progettazione consente di considerare le destinazioni di calcolo come risorse di calcolo senza stato, ognuna senza affinità con i processi eseguiti al loro termine.

## <a name="challenges"></a>Problematiche
Questa progettazione, anche se offre i vantaggi della portabilità e della ripetibilità, comporta tuttavia alcune problematiche specifiche.

### <a name="persisting-state-changes"></a>Modifiche di stato persistenti
Se lo script modifica lo stato del contesto di calcolo, le modifiche non vengono mantenute nell'esecuzione successiva e non vengono propagate automaticamente nel computer client. 

Più in particolare, se lo script crea una sottocartella o scrive un file, tale cartella o file non sarà presente nella directory del progetto dopo l'esecuzione. I file vengono archiviati in una cartella temporanea nell'ambiente di destinazione di calcolo. È possibile usarli a scopo di debug, ma non servirsene a tempo indeterminato.

### <a name="working-with-large-files-in-the-project-folder"></a>Utilizzo di file di grandi dimensioni nella cartella del progetto

Se la cartella del progetto contiene file di grandi dimensioni, è soggetta a latenza quando viene copiata nell'ambiente di calcolo di destinazione all'inizio di un'esecuzione. Anche se l'esecuzione avviene in locale, è ugualmente presente traffico non necessario su disco da evitare. Per questo motivo, attualmente la dimensione massima del progetto è limitata a 25 MB.

## <a name="option-1-use-the-outputs-folder"></a>Opzione 1: Usare la cartella *outputs*
Questa opzione è preferibile se si verificano tutte le condizioni seguenti:
* Lo script genera file.
* Si prevede che i file vengano modificati a ogni esperimento.
* Si vuole conservare una cronologia di questi file. 

I casi d'uso comuni sono:
* Training a model
* Creazione di un set di dati
* Tracciato di un grafo come file di immagine nell'ambito dell'esecuzione del training del modello 

Si vuole anche confrontare gli output nelle esecuzioni, selezionare un file di output (ad esempio, un modello) generato da un'esecuzione precedente e quindi usarlo per un'attività successiva (ad esempio, l'assegnazione di punteggi).

È possibile scrivere i file in una cartella denominata *outputs* relativa alla directory radice. La cartella riceve un trattamento speciale dal servizio Sperimentazione. Qualsiasi _elemento_ creato nella cartella dallo script durante l'esecuzione, ad esempio un file di modello, un file di dati o un file di immagine tracciata, viene copiato nell'account di archiviazione BLOB di Azure associato all'account di Sperimentazione al termine dell'esecuzione. I file diventano parte del record della cronologia di esecuzione.

Ecco un breve esempio di codice per archiviare un modello nella cartella *outputs*:
```python
import os
import pickle

# m is a scikit-learn model. 
# we serialize it into a mode.plk file under the ./outputs folder.
with open(os.path.join('.', 'outputs', 'model.pkl'), 'wb') as f:    
    pickle.dump(m, f)
```
È possibile scaricare qualsiasi elemento scorrendo la sezione **File di output** nella pagina dei dettagli di un'esecuzione specifica in Azure Machine Learning Workbench. È sufficiente selezionare l'esecuzione e quindi il pulsante **Download**. In alternativa, è possibile immettere il comando `az ml asset download` nella finestra dell'interfaccia della riga di comando.

Per un esempio più esaustivo, vedere lo script `iris_sklearn.py` di Python nel progetto di esempio _Classificazione di dati Iris_.

## <a name="option-2-use-the-shared-folder"></a>Opzione 2: Usare la cartella condivisa
Se non è necessario conservare la cronologia dei file di ogni esecuzione, l'uso di una cartella condivisa a cui è possibile accedere durante più esecuzioni indipendenti può rivelarsi un'utile opzione negli gli scenari seguenti: 
- Lo script deve caricare i dati da file locali, ad esempio file con estensione csv o directory di file di testo o immagini, ad esempio dati di training o test. 
- Lo script elabora dati non elaborati e scrive i risultati intermedi, ad esempio dati di training con definizione delle caratteristiche da file di testo o immagine, usati in un'esecuzione di training successiva. 
- Lo script genera un modello e il successivo script di assegnazione dei punteggi deve selezionare tale modello e usarlo per la valutazione. 

È importante notare che la cartella condivisa si trova in locale nella destinazione di calcolo scelta. Questa opzione quindi funziona solo se tutte le esecuzioni di script che fanno riferimento a questa cartella condivisa vengono eseguite nella stessa destinazione di calcolo e tale destinazione non viene riciclata tra un'esecuzione e l'altra.

Sfruttando la funzionalità della cartella condivisa, è possibile leggere o scrivere in una cartella speciale identificata dalla variabile di ambiente `AZUREML_NATIVE_SHARE_DIRECTORY`. 

### <a name="example"></a>Esempio
Di seguito alcuni esempi di codice Python che consentono di usare questa cartella condivisa per leggere e scrivere in un file di testo:
```python
import os

# write to the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'wb') as f:
    f.write(“Hello World”)

# read from the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'r') as f:
    text = file.read()
```

Per un esempio più esaustivo, vedere il file *iris_sklearn_shared_folder.py* nel progetto di esempio _Classificazione di dati Iris_.

Prima di poter usare questa funzionalità, è necessario impostare nel file con estensione *compute* alcune semplici configurazioni che rappresentano il contesto di esecuzione di destinazione nella cartella *aml_config*. Il percorso effettivo della cartella può variare a seconda della destinazione di calcolo scelta e dai valori configurati.

### <a name="configure-local-compute-context"></a>Configurare il contesto di calcolo locale

Per abilitare questa funzionalità in un contesto di calcolo locale, aggiungere al file con estensione *compute* la riga seguente, che rappresenta l'ambiente _locale_, in genere denominato *local.compute*.
```
# local.runconfig
...
nativeSharedDirectory: ~/.azureml/share
...
```

~/.azureml/share è il percorso della cartella di base predefinita. È possibile modificarlo in qualsiasi percorso assoluto locale accessibile dall'esecuzione dello script. Il nome dell'account di sperimentazione, il nome dell'area di lavoro e il nome del progetto vengono aggiunti automaticamente al nome della directory di base, che diventa il percorso completo della directory condivisa. Ad esempio, i file possono essere scritti nel percorso seguente e da questo recuperati se si usa il valore predefinito precedente:

```
# on Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# on macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

### <a name="configure-the-docker-compute-context-local-or-remote"></a>Configurare il contesto di calcolo Docker (locale o remoto)
Per abilitare questa funzionalità in un contesto di calcolo Docker, è necessario aggiungere le due righe seguenti al file con estensione *compute* Docker locale o remoto.

```
# docker.compute
...
sharedVolumes: true
nativeSharedDirectory: ~/.azureml/share
...
```
>[!IMPORTANT]
>La proprietà **sharedVolumes** deve essere impostata su *true* quando si usa la variabile di ambiente `AZUREML_NATIVE_SHARE_DIRECTORY` per accedere alla cartella condivisa. In caso contrario, l'esecuzione ha esito negativo.

Il codice in esecuzione nel contenitore Docker rileva questa cartella condivisa come */azureml-share/*. Il percorso della cartella, così come viene riconosciuto dal contenitore Docker, non è configurabile. Non usare questo nome di cartella nel codice. È consigliabile usare sempre il nome della variabile di ambiente `AZUREML_NATIVE_SHARE_DIRECTORY` per fare riferimento a questa cartella. Ne viene eseguito il mapping a una cartella locale nel computer host Docker o nel contesto di calcolo. La directory di base di questa cartella locale è il valore configurabile dell'impostazione `nativeSharedDirectory` nel file con estensione *compute*. Il percorso locale della cartella condivisa nel computer host, se si usa il valore predefinito, è il seguente:
```
# Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/

# Ubuntu Linux
/home/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

>[!NOTE]
>Il percorso della cartella condivisa sul disco locale è lo stesso sia che si tratti di un contesto di calcolo locale o di un contesto di calcolo locale Docker. Ciò significa che è anche possibile condividere file tra un'esecuzione locale e un'esecuzione locale Docker.

È possibile inserire i dati di input direttamente in queste cartelle e presupporre che le esecuzioni locali o Docker sul computer possono selezionarli. È anche possibile scrivere i file in questa cartella dalle esecuzioni in locale o Docker e presupporre che i file restino in tale anche dopo il ciclo di vita dell'esecuzione.

Per altre informazioni, vedere [File di configurazione di esecuzione di Azure Machine Learning Workbench](experimentation-service-configuration-reference.md).

>[!NOTE]
>La variabile di ambiente `AZUREML_NATIVE_SHARE_DIRECTORY` non è supportata nel contesto di calcolo di HDInsight. È tuttavia facile ottenere lo stesso risultato in modo esplicito usando un percorso di archiviazione BLOB di Azure assoluto per la lettura e la scrittura nell'archivio BLOB associato.

## <a name="option-3-use-external-durable-storage"></a>Opzione 3: Utilizzo di una risorsa di archiviazione durevole esterna

È possibile usare una risorsa di archiviazione durevole esterna per mantenere la persistenza dello stato durante l'esecuzione. Questa opzione è utile negli scenari seguenti:
- I dati di input sono già archiviati in una risorsa di archiviazione durevole accessibile dall'ambiente di calcolo di destinazione.
- I file non devono far parte dei record della cronologia di esecuzione.
- I file devono essere condivisi dalle esecuzioni in diversi ambienti di calcolo.
- I file devono poter essere conservati anche dopo l'eliminazione del contesto di calcolo.

Un approccio di questo genere prevede l'uso dell'archivio BLOB di Azure dal codice Python o PySpark. Ecco un breve esempio:

```python
from azure.storage.blob import BlockBlobService
import glob
import os

ACCOUNT_NAME = "<your blob storage account name>"
ACCOUNT_KEY = "<account key>"
CONTAINER_NAME = "<container name>"

blob_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

## Create a new container if necessary, or use an existing one
my_service.create_container(CONTAINER_NAME, fail_on_exist=False, public_access=PublicAccess.Container)

# df is a pandas DataFrame
df.to_csv('mydata.csv', sep='\t', index=False)

# Export the mydata.csv file to Blob storage.
for name in glob.iglob('mydata.csv'):
    blob_service.create_blob_from_path(CONTAINER_NAME, 'single_file.csv', name)
```

Di seguito è riportato un breve esempio per associare qualsiasi archivio BLOB di Azure arbitrario a un runtime Spark HDI:
```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)
 
attach_storage_container(spark, "<storage account name>", "<storage key>”)
```

## <a name="conclusion"></a>Conclusioni
Poiché Azure Machine Learning esegue gli script copiando l'intera cartella di progetto nel contesto di calcolo di destinazione, prestare particolare attenzione a file di input, output e intermedi di grandi dimensioni. Per le transazioni di file di grandi dimensioni, è possibile usare la speciale cartella outputs, la cartella condivisa accessibile con la variabile di ambiente `AZUREML_NATIVE_SHARE_DIRECTORY`, o una risorsa di archiviazione durevole esterna. 

## <a name="next-steps"></a>Passaggi successivi
- Vedere l'articolo [File di configurazione di esecuzione di Azure Machine Learning Workbench](experimentation-service-configuration-reference.md).
- Vedere come il progetto dell'esercitazione [Classificazione di dati Iris](tutorial-classifying-iris-part-1.md) usa la cartella outputs per conservare in modo permanente un modello con training.
