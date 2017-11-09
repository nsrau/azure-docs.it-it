---
title: File di configurazione del servizio Sperimentazione di Azure Machine Learning
description: Questo documento illustra in dettaglio le impostazioni di configurazione per il servizio Sperimentazione di Azure Machine Learning.
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/28/2017
ms.openlocfilehash: 6a247c225af734757ab0cb0a7502f39535299ca7
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2017
---
# <a name="azure-machine-learning-experimentation-service-configuration-files"></a>File di configurazione del servizio Sperimentazione di Azure Machine Learning

Quando si esegue uno script in Azure Machine Learning (Azure ML) Workbench, il comportamento dell'esecuzione viene controllato dai file nella cartella **aml_config**. Questa cartella si trova sotto la radice della cartella di progetto. È importante comprendere il contenuto di questi file per ottenere il risultato desiderato per un'esecuzione ottimale.

Di seguito sono riportati i file rilevanti in questa cartella:
- conda_dependencies.yml
- spark_dependencies.yml
- file di destinazione di calcolo
    - \<nome destinazione di calcolo>.compute
- file configurazione di esecuzione
    - \<nome configurazione esecuzione>.runconfig

>[!NOTE]
>In genere sono disponibili un file di destinazione del calcolo e un file di configurazione di esecuzione per ogni destinazione di calcolo creata. Tuttavia è possibile creare questi file in modo indipendente e disporre di più file di configurazione di esecuzione che puntano alla stessa destinazione di calcolo.

## <a name="condadependenciesyml"></a>conda_dependencies.yml
Questo file è un [file dell'ambiente conda](https://conda.io/docs/using/envs.html#create-environment-file-by-hand) che specifica la versione del runtime e i pacchetti Python da cui dipende il codice in uso. Quando Azure ML Workbench esegue uno script in un contenitore Docker o in cluster HDInsight, viene creato un [ambiente conda](https://conda.io/docs/using/envs.html) per l'esecuzione dello script. 

In questo file si specificano i pacchetti Python di cui lo script necessita per l'esecuzione. Il servizio Sperimentazione di Azure Machine Learning crea l'ambiente conda nell'immagine Docker in base all'elenco di dipendenze specificato. L'elenco di pacchetti indicato qui deve essere raggiungibile dal motore di esecuzione. Per questo motivo, i pacchetti devono essere elencati nei canali, ad esempio:

* [continuum.io](https://anaconda.org/conda-forge/repo)
* [PyPI](https://pypi.python.org/pypi)
* un endpoint accessibile pubblicamente (URL)
* o un percorso file locale
* altri canali raggiungibili dal motore di esecuzione

>[!NOTE]
>Quando l'esecuzione avviene nel cluster HDInsight, Azure ML Workbench crea un ambiente conda solo per l'esecuzione specifica. In questo modo utenti diversi possono procedere all'esecuzione in ambienti Python diversi nello stesso cluster.  

Di seguito è riportato un esempio di un tipico file **conda_dependencies.yml**.
```yaml
name: project_environment
dependencies:
  # Python version
  - python=3.5.2
  
  # some conda packages
  - scikit-learn
  - cryptography
  
  # use pip to install some more packages
  - pip:
     # a package in PyPi
     - azure-storage
     
     # a package hosted in a public URL endpoint
     - https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp35-cp35m-win_amd64.whl
     
     # a wheel file available locally on disk (this only works if you are executing against local target)
     - C:\temp\my_private_python_pkg.whl
```

Azure ML Workbench usa lo stesso ambiente conda senza eseguire la ricompilazione finché il file **conda_dependencies.yml** rimane invariato. Tuttavia, se un valore viene modificato in questo file, l'immagine Docker viene rigenerata.

>[!NOTE]
>Se si usa come destinazione dell'esecuzione il contesto di calcolo _locale_, il file **conda_dependencies.yml** **non** viene usato. È necessario installare manualmente le dipendenze dei pacchetti per l'ambiente Python locale in Azure ML Workbench.

## <a name="sparkdependenciesyml"></a>spark_dependencies.yml
Questo file specifica il nome dell'applicazione Spark quando si invia uno script PySpark e i pacchetti Spark che devono essere installati. È possibile anche specificare qualsiasi archivo Maven pubblico e pacchetto Spark che può essere presente in questi archivi Maven.

Di seguito è fornito un esempio:

```yaml
configuration:
  # Spark application name
  "spark.app.name": "ClassifyingIris"
  
repositories:
  # Maven repository hosted in Azure CDN
  - "https://mmlspark.azureedge.net/maven"
  
  # Maven repository hosted in spark-packages.org
  - "https://spark-packages.org/packages"
  
packages:
  # MMLSpark package hosted in the Azure CDN Maven
  - group: "com.microsoft.ml.spark"
    artifact: "mmlspark_2.11"
    version: "0.5"
    
  # spark-sklearn packaged hosted in the spark-packages.org Maven
  - group: "databricks"
    artifact: "spark-sklearn"
    version: "0.2.0"
```

>[!NOTE]
>I parametri di ottimizzazione del cluster come le dimensioni dei processi di lavoro, i core devono essere presenti nella sezione "configuration" nel file spark_dependecies.yml 

>[!NOTE]
>Se si esegue lo script nell'ambiente Python, il file *spark_dependencies.yml* viene ignorato. Ha effetto solo se viene eseguito con Spark nel cluster HDInsight e in quello Docker.

## <a name="run-configuration"></a>Configurazione di esecuzione
Per specificare una configurazione di esecuzione particolare, è necessaria una coppia di file. In genere questi file vengono generati usando un comando dell'interfaccia della riga di comando. È possibile tuttavia anche clonare file esistenti, rinominarli e modificarli.

```azurecli
# create a compute target pointing to a VM via SSH
$ az ml computetarget attach -n <compute target name> -a <IP address or FQDN of VM> -u <username> -w <password> --type remotedocker

# create a compute context pointing to an HDI cluster head-node via SSH
$ az ml computetarget attach -n <compute target name> -a <IP address or FQDN of HDI cluster> -u <username> -w <password> --type cluster
```

Questo comando crea una coppia di file in base alla destinazione di calcolo specificata. Si supponga che la destinazione del calcolo sia stata denominata _foo_. Questo comando genera _foo.compute_ e _foo.runconfig_ nella cartella **aml_config**.

>[!NOTE]
> I nomi _local_ o _docker_ per i file di configurazione di esecuzione sono arbitrari. Azure ML Workbench aggiunge queste due configurazioni di esecuzione quando si crea un progetto vuoto per comodità. È possibile rinominare i file "<run configuration name>.runconfig" forniti con il modello di progetto o creare nuovi file con il nome desiderato.

### <a name="compute-target-namecompute"></a>\<nome destinazione di calcolo>.compute
Il file _\<nome destinazione di calcolo>.compute_ specifica le informazioni di connessione e configurazione per la destinazione di calcolo. È un elenco di coppie nome-valore. Di seguito sono riportate le impostazioni supportate.

**type**: tipo di ambiente di calcolo. I valori supportati sono:
  - local
  - docker
  - remotedocker
  - cluster

**baseDockerImage**: immagine Docker usata per eseguire lo script Python/PySpark. Il valore predefinito è _microsoft/mmlspark:plus-0.7.91_. È supportata anche un'altra immagine, _microsoft/mmlspark:plus-gpu-0.7.91_, che consente l'accesso a livello di GPU, se presente, nel computer host.

**address**: l'indirizzo IP o FQDN (nome di dominio completo) della macchina virtuale o del nodo head del cluster HDInsight.

**username**: nome utente SSH per l'accesso alla macchina virtuale o al nodo head HDInsight.

**password**: la password crittografata per la connessione SSH.

**sharedVolumes**: flag per segnalare che il motore di esecuzione deve usare la funzionalità di volume condiviso Docker per fornire i file di progetto in una direzione e nella direzione inversa. L'attivazione di questo flag può velocizzare l'esecuzione perché Docker può accedere ai progetti direttamente senza la necessità di copiarli. È consigliabile impostare _false_ se il motore Docker è in esecuzione su Windows poiché la condivisione volume per Docker in Windows può essere non attendibile. Impostare su _true_, se è in esecuzione su macOS o Linux.

**nvidiaDocker**: questo flag, se impostato su _true_, indica al servizio Sperimentazione di Azure Machine Learning di usare il comando _nvidia-docker_, a differenza del normale comando _docker_ per avviare l'immagine Docker. Il motore _nvidia-docker_ consente al contenitore Docker di accedere all'hardware GPU. L'impostazione è obbligatoria se si vuole usare l'esecuzione GPU nel contenitore Docker. Solo l'host Linux supporta _nvidia-docker_. Ad esempio, DSVM basata su Linux in Azure viene fornita con il comando _nvidia-docker_. Il comando _nvidia-docker_ al momento non è supportato in Windows.

**nativeSharedDirectory**: questa proprietà specifica la directory di base, ad esempio: _~/.azureml/share/_, in cui i file possono essere salvati per essere condivisi tra le esecuzioni nella stessa destinazione calcolo. Se questa impostazione viene usata durante l'esecuzione in un contenitore Docker, _sharedVolumes_ deve essere impostato su true. In caso contrario, l'esecuzione ha esito negativo.

### <a name="run-configuration-namerunconfig"></a>\<nome configurazione esecuzione>.runconfig
_\<nome della configurazione di esecuzione>.runconfig_ specifica il comportamento di esecuzione di Sperimentazione di Azure Machine Learning. È possibile configurare comportamenti di esecuzione come il rilevamento della cronologia di esecuzione o la destinazione di calcolo da usare e molto altro. I nomi dei file di configurazione di esecuzione vengono usati per popolare l'elenco a discesa dei contesti di esecuzione nell'applicazione desktop di Azure ML Workbench.

**ArgumentVector**: in questa sezione viene specificato lo script da eseguire come parte di questa esecuzione e i parametri per lo script. È il caso, ad esempio, in cui nel file "<run configuration name>.runconfig" esiste il frammento seguente 

```
 "ArgumentVector":[
  - "myscript.py"
  - 234
  - "-v" 
 ] 
```
_"az ml experiment submit foo.runconfig"_ esegue automaticamente il comando con il file _myscript.py_ passando 234 come parametro e imposta il flag --verbose.

**Target**: questo parametro è il nome del file _.compute_ che viene referenziato dal file _runconfig_. In genere punta al file _foo.compute_, ma è possibile modificare questo valore in modo che punti a una diversa destinazione di calcolo.

**Environment Variables**: questa sezione consente agli utenti di impostare le variabili di ambiente come parte delle proprie esecuzioni. L'utente può specificare le variabili di ambiente usando coppie nome-valore nel formato seguente:
```
EnvironmentVariables:
"EXAMPLE_ENV_VAR1": "Example Value1"
"EXAMPLE_ENV_VAR2": "Example Value2"
```

Queste variabili di ambiente sono accessibili nel codice dell'utente. Ad esempio, il codice Phyton stampa la variabile di ambiente denominata "EXAMPLE_ENV_VAR"
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

**Framework**: questa proprietà specifica se Azure ML Workbench deve avviare una sessione Spark per eseguire lo script. Il valore predefinito è _PySpark_. Impostarla su _Python_ se non si esegue codice PySpark, che consente di avviare il processo più rapidamente con un minore sovraccarico.

**CondaDependenciesFile**: questa proprietà fa riferimento al file che specifica le dipendenze di ambiente conda nella cartella *aml_config*. Se impostata su _null_, punta al file **conda_dependencies.yml** predefinito.

**SparkDependenciesFile**: questa proprietà punta al file che specifica le dipendenze Spark nella cartella **aml_config**. È impostata su _null_ per impostazione predefinita e punta al file **spark_dependencies.yml** predefinito.

**PrepareEnvironment**: questa proprietà, se impostata su _true_, indica al servizio Sperimentazione di preparare l'ambiente conda in base alle dipendenze conda specificate durante l'esecuzione iniziale. Questa proprietà è effettiva solo quando l'esecuzione avviene in un ambiente Docker. Questa impostazione non ha alcun effetto se l'esecuzione avviene in un ambiente _locale_. 

**TrackedRun**: questo flag segnala al servizio Sperimentazione se rilevare l'esecuzione nell'infrastruttura della cronologia di esecuzioni di Azure ML Workbench. Il valore predefinito è _true_. 

**UseSampling**: _UseSampling_ specifica se i set di dati di esempio attivi per le origini dati vengono usati per l'esecuzione. Se impostato su _false_, le origini dati inseriscono e usano i dati completi letti dall'archivio dati. Se impostato su _true_, vengono usati gli esempi attivi. Gli utenti possono usare **DataSourceSettings"per specificare i set di dati di esempio specifici da usare, se si vuole eseguire l'override dell'esempio attivo. 

**DataSourceSettings**: questa sezione di configurazione specifica le impostazioni relative alle origini dati. In questa sezione l'utente specifica quale esempio di dati esistente per una determinata origine dati viene usato come parte dell'esecuzione. 

L'impostazione di configurazione seguente specifica che l'esempio, denominato "MySample", viene usato per l'origine dati, denominata "MyDataSource"
```
DataSourceSettings:
    MyDataSource.dsource:
    Sampling:
    Sample: MySample
```

**DataSourceSubstitutions**: è possibile usare le sostituzioni di origini dati quando l'utente desidera passare da un'origine dati a un'altra senza modificare il proprio codice. Ad esempio, gli utenti possono passare da un file locale derivato da esempio al set di dati originale e più grande archiviato nel BLOB di Azure modificando il riferimento all'origine dati. Quando viene usata una sostituzione, Azure ML Workbench esegue le origini dati e i pacchetti di preparazione dei dati referenziando l'origine dati di sostituzione.

Nell'esempio seguente i riferimenti "mylocal.datasource" nelle origini dati in Azure Machine Learning e i pacchetti di preparazione dei dati vengono sostituiti con "myremote.dsource". 
 
```
DataSourceSubstitutions:
    myocal.dsource: myremote.dsource
```

In base alla sostituzione precedente, l'esempio di codice seguente legge ora da "myremote.dsource" anziché da "mylocal.dsource" senza che gli utenti abbiano modificato il proprio codice.
```
df = datasource.load_datasource('mylocal.dsource')
```
## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulla [configurazione del servizio Sperimentazione](experimentation-service-configuration.md).
