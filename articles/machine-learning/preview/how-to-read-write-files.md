---
title: Come leggere e scrivere file di dati di grandi dimensioni | Microsoft Docs
description: Come leggere e scrivere file di grandi dimensioni negli esperimenti di Azure Machine Learning.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/10/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: fb3158ef786ad73440a59c07b38476a98ced0768
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="persisting-changes-and-dealing-with-large-files"></a>Rendere persistenti le modifiche e gestire file di grandi dimensioni

## <a name="execution-isolation-portability-and-reproducibility"></a>Isolamento, portabilità e riproducibilità dell'esecuzione
Il Servizio Sperimentazione di Azure Machine Learning consente di configurare diverse destinazioni di esecuzione, alcune locali come un computer locale o un contenitore Docker in un computer locale, altre remote, ad esempio un contenitore Docker in un computer remoto o un cluster HDInsight. Per altre informazioni vedere [Configuration Experimentation Execution](experiment-execution-configuration.md) (Configurazione dell'esecuzione di esperimenti). Prima di avviare qualsiasi esecuzione, la cartella del progetto viene copiata nella destinazione di calcolo. Questo vale anche nel caso di un'esecuzione locale in cui a questo scopo viene usata una cartella temporanea locale. 

La finalità di questa progettazione è garantire la portabilità, la riproducibilità e l'isolamento dell'esecuzione. Se si esegue lo stesso script due volte, nella stessa destinazione di calcolo o in una differente, si otterranno necessariamente gli stessi risultati. Le modifiche apportate dalla prima esecuzione non hanno impatto sulla seconda. Questa struttura consente di considerare le destinazioni di calcolo come risorse di calcolo senza stato che non hanno affinità con i processi eseguiti al loro termine.

## <a name="challenges"></a>Problematiche
Oltre ai vantaggi offerti da portabilità e ripetibilità, questa soluzione offre anche:
### <a name="persisting-state-changes"></a>Modifiche di stato persistenti
Se lo script modifica lo stato nel contesto di calcolo, le modifiche non vengono mantenute nell'esecuzione successiva, né propagate automaticamente nei computer client. 

Più in concreto, se lo script crea una sottocartella o scrive un file, tale cartella o file non saranno visibili nella directory del progetto dopo l'esecuzione. Rimangono in una cartella temporanea nell'ambiente di destinazione del calcolo ovunque esso sia. Possono essere usati a scopo di debug, ma è bene non creare elementi dipendenti dalla loro esistenza.

### <a name="dealing-with-large-files-in-project-folder"></a>Gestione di file di grandi dimensioni nella cartella di progetto

Se la cartella di progetto contiene file di grandi dimensioni, è soggetta a latenza quando viene copiata nell'ambiente di destinazione del calcolo, all'inizio di ogni esecuzione. Anche se l'esecuzione avviene in locale, è comunque consigliabile evitare il traffico non necessario su disco. Ecco perché il limite massimo alle dimensioni di un progetto è stabilito a 25 MB.

## <a name="option-1-use-the-outputs-folder"></a>Opzione 1: usare la cartella di output
Questo è l'opzione da preferire se lo script produce file e si prevede che questi file vengano modificati ogni volta che viene eseguito un esperimento eseguito e se si vuole conservare una cronologia dei file. Sono casi d'uso comune l'esecuzione del training di un modello, la creazione di un set di dati, la traccia di un grafico come file di immagine durante l'esecuzione del training del modello. L'utente vuole confrontare questi output a fronte di più esecuzioni, quindi tornare indietro e selezionare un file di output (ad esempio un modello) prodotto da un'esecuzione precedente e usarlo per un'attività successiva, ad esempio, l'assegnazione dei punteggi.

In pratica, è possibile scrivere i file in una cartella denominata `outputs` relativa alla directory radice. Si tratta di una cartella speciale che riceve un trattamento speciale dal Servizio Sperimentazione. Qualsiasi _elemento_ creato in questa posizione dallo script durante l'esecuzione, ad esempio un file di modello, un file di dati o un file di immagine tracciata, viene copiato nell'Archiviazione BLOB di Azure associato all'account di Sperimentazione al termine dell'esecuzione. Gli elementi diventano parte del record della cronologia di esecuzione.

Di seguito un semplice esempio che consente l'archiviazione di un modello nella cartella `outputs`:
```python
import os
import pickle

# m is a scikit-learn model. 
# we serialize it into a mode.plk file under the ./outputs folder.
with open(os.path.join('.', 'outputs', 'model.pkl'), 'wb') as f:    
    pickle.dump(m, f)
```
È possibile scaricare qualsiasi _elemento_ scorrendo la sezione **File di output** nella pagina dei dettagli dell'esecuzione di un'esecuzione specifica in Azure Machine Learning Workbench, selezionando l'elemento e facendo clic sul pulsante **Download**. In alternativa, usare il comando `az ml asset download` dalla finestra dell'interfaccia della riga di comando.

Per un esempio più esaustivo, vedere lo script `iris_sklearn.py` di Python nel progetto di esempio relativo alla _Classificazione Iris_.

## <a name="option-2-use-the-shared-folder"></a>Opzione 2: usare la cartella condivisa
L'utilizzo di una cartella condivisa a cui è possibile accedere durante più esecuzioni indipendenti può rivelarsi un'utile opzione negli gli scenari seguenti, a condizione che non sia necessario conservare la cronologia dei file per ogni esecuzione: 
- Lo script deve caricare i dati da file locali, ad esempio file con estensione csv o directory di file di testo o immagini, ad esempio dati di training o test. 
- Lo script elabora dati non elaborati e scrive i risultati intermedi, ad esempio dati di training con definizione delle caratteristiche da file di testo o immagine, usati in un'esecuzione di training successiva. 
- Lo script genera un modello e il successivo script di assegnazione dei punteggi deve prelevare tale modello e usarlo per la valutazione. 

Un problema importante è che la cartella condivisa risiede in locale nella stessa destinazione di calcolo selezionata. Questo metodo pertanto funziona solo se tutte le esecuzioni di script che fanno riferimento a questa cartella condivisa vengono eseguite nella stessa destinazione di calcolo e tale destinazione non viene riciclata tra un'esecuzione e l'altra.

La funzionalità delle cartelle condivise consente di leggere o scrivere in una cartella speciale identificata dalla variabile di ambiente `AZUREML_NATIVE_SHARE_DIRECTORY`. 

### <a name="example"></a>Esempio
Di seguito alcuni esempi di codice Python che consentono di usare questa cartella condivisa per leggere e scrivere un file di testo:
```python
import os

# write to the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'wb') as f:
    f.write(“Hello World”)

# read from the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'r') as f:
    text = file.read()
```

Per un esempio più esaustivo, vedere il file `iris_sklearn_shared_folder.py` nel progetto di esempio relativo alla _Classificazione Iris_.

Prima di poter usare questa funzionalità è necessario impostare alcune semplici configurazioni nel file `.compute` che rappresenta il contesto di esecuzione di destinazione nella cartella `aml_config`. Il percorso effettivo di questa cartella può variare a seconda della destinazione di calcolo scelta e dai valori configurati.

### <a name="configure-local-compute-context"></a>Configurare il contesto di calcolo locale

Per abilitare questa funzionalità in un contesto di calcolo locale, aggiungere la riga seguente al file `.compute` che rappresenta l'ambiente _local_, in genere denominato `local.compute`.
```
# local.runconfig
...
nativeSharedDirectory: ~/.azureml/share
...
```

Il percorso della cartella di base predefinita è `~/.azureml/share`. È possibile modificarlo in qualsiasi percorso assoluto locale accessibile dall'esecuzione dello script. Il nome dell'account di sperimentazione, il nome dell'area di lavoro e il nome del progetto vengono aggiunti automaticamente alla directory di base, che costituisce il percorso completo della directory condivisa. Ad esempio, i file possono essere scritti nel percorso seguente e da questo recuperati se si usa il valore predefinito precedente:

```
# on Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# on macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

### <a name="configure-docker-compute-context-local-or-remote"></a>Configurare il contesto di calcolo Docker (locale o remoto)
Per abilitare questa funzionalità in un contesto di calcolo Docker, è necessario aggiungere le due righe seguenti al file _.compute_ Docker locale o remoto.

```
# docker.compute
...
sharedVolumes: true
nativeSharedDirectory: ~/.azureml/share
...
```
>[!IMPORTANT]
>`sharedVolume` deve essere impostato su `true` quando si usa la variabile di ambiente `AZUREML_NATIVE_SHARE_DIRECTORY` per accedere alla cartella condivisa; in caso contrario l'esecuzione avrà esito negativo.

Il codice in esecuzione nel contenitore Docker rileva questa cartella condivisa come `/azureml-share/`. Questo percorso della cartella, così come viene riconosciuto dal contenitore Docker, non è configurabile. Non è consigliabile creare elementi dipendenti dal nome di questa cartella nel codice. È consigliabile usare sempre il nome della variabile di ambiente `AZUREML_NATIVE_SHARE_DIRECTORY` per risolvere questa cartella. Questo viene mappato a una cartella locale nel contesto della macchina host/contesto di calcolo. La directory di base di questa cartella locale è il valore configurabile dell'impostazione `nativeSharedDirectory` nel file `.compute`. Il percorso locale della cartella condivisa nel computer host, se si usa il valore predefinito precedente, è il seguente:
```
# Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/

# Ubuntu Linux
/home/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

>[!TIP]
>Si noti che il percorso della cartella condivisa sul disco locale è uguale a quello tra un contesto di calcolo locale e un contesto di calcolo locale Docker. Ciò significa che è anche possibile condividere file tra un'esecuzione locale e un'esecuzione locale Docker.

È possibile inserire i dati di input direttamente in queste cartelle e presupporre che le esecuzioni in locale o Docker su tale computer possono prelevarli. È anche possibile scrivere i file in questa cartella dalle esecuzioni in locale o Docker e presupporre che i file restino in tale anche dopo il ciclo di vita dell'esecuzione.

Per altre informazioni sui file di configurazione nel servizio di esecuzione di Azure Machine Learning, consultare l'articolo relativo ai [file di configurazione dell'esecuzione](experiment-execution-configuration-reference.md).

>[!NOTE]
>La variabile di ambiente `AZUREML_NATIVE_SHARE_DIRECTORY` non è supportata nel contesto di calcolo di HDInsight. Tuttavia, è facile ottenere lo stesso risultato in modo esplicito usando un percorso assoluto WASB per la lettura o la scrittura nell'archiviazione BLOB associata.

## <a name="option-3-use-external-durable-storage"></a>Opzione 3: Utilizzo di una risorsa di archiviazione durevole esterna

Ovviamente è possibile usare una risorsa di archiviazione durevole esterna per mantenere la persistenza dello stato durante l'esecuzione. Ciò è utile negli scenari seguenti:
- I dati di input sono già archiviati in una risorsa di archiviazione durevole accessibile dall'ambiente di calcolo di destinazione.
- I file non devono far parte dei record della cronologia di esecuzione.
- I file devono essere condivisi dalle esecuzioni in ambiente di calcolo diversi.
- I file devono essere conservati anche dopo l'eliminazione del contesto di calcolo.

Ne è un esempio l'utilizzo l'archiviazione BLOB di Azure dal codice Python/PySpark.

Di seguito è riportato un esempio di utilizzo dell'archiviazione BLOB di Azure dal codice Python:
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

# Export the mydata.csv file to blob storage.
for name in glob.iglob('mydata.csv'):
    blob_service.create_blob_from_path(CONTAINER_NAME, 'single_file.csv', name)
```

Di seguito è riportato anche un esempio per associare qualsiasi archiviazione BLOB di Azure arbitraria a un runtime Spark HDI:
```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)
 
attach_storage_container(spark, "<storage account name>", "<storage key>”)
```

## <a name="conclusion"></a>Conclusioni
Poiché Azure Machine Learning esegue gli script copiando l'intera cartella di progetto nel contesto di calcolo di destinazione, è consigliabile prestare particolare attenzione a file di input, output e intermedi di grandi dimensioni. È possibile usare la speciale cartella `outputs`, la cartella condivisa accessibile con la variabile di ambiente `AZUREML_NATIVE_SHARE_DIRECTORY` o una risorsa di archiviazione durevole esterna per le transazioni di file di grandi dimensioni. 

## <a name="next-steps"></a>Passaggi successivi
- Rivedere l'articolo sulla [configurazione dell'esecuzione di esperimenti](experiment-execution-configuration-reference.md).
- Vedere come l'esercitazione [Classificazione Iris](tutorial-classifying-iris-part-1.md) usa la cartella `outputs` per conservare in modo permanente il modello di training.
