---
title: Configurazione del servizio Sperimentazione di Azure Machine Learning | Microsoft Docs
description: Questo articolo offre una panoramica generale del servizio Sperimentazione di Azure Machine Learning con istruzioni su come configurarlo.
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/28/2017
ms.openlocfilehash: f93c74d0c2f66e6a5001289efca07f074e3d3c5a
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="configuring-azure-machine-learning-experimentation-service"></a>Configurazione del servizio Sperimentazione di Azure Machine Learning

## <a name="overview"></a>Panoramica
Il servizio Sperimentazione di Azure Machine Learning consente ai data scientist di svolgere i propri esperimenti tramite l'esecuzione di Azure Machine Learning e le funzionalità di gestione dell'esecuzione. Fornisce un framework per la sperimentazione agile con iterazioni veloci. Azure Machine Learning Workbench consente di avviare le esecuzioni locali nel computer e offre semplice scalabilità verticale e orizzontale in altri ambienti, come le macchine virtuali di data science remote con GPU o i cluster HDInsight che eseguono Spark.

Il servizio Sperimentazione è stato creato per offrire esecuzioni isolate, riproducibili e coerenti degli esperimenti. Consente di gestire i database di destinazione del calcolo, gli ambienti di esecuzione e le configurazioni di esecuzione. Tramite l'esecuzione di Azure Machine Learning Workbench e le funzionalità di gestione dell'esecuzione, è possibile spostarsi facilmente tra ambienti diversi. 

È possibile eseguire uno script Python o PySpark in un progetto Workbench a livello locale o su larga scala nel cloud. 

È possibile eseguire gli script in: 

* Ambiente Python (3.5.2) nel computer locale installato da Workbench
* Ambiente di Conda Python all'interno di un contenitore Docker nel computer locale
* Ambiente Python di proprietà dell'utente e gestito in un computer Linux remoto
* Ambiente di Conda Python all'interno di un contenitore Docker in un computer Linux remoto. Ad esempio, una [macchina virtuale di data science basata su Ubuntu in Azure] (https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* [HDInsight per Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) in Azure

>[!IMPORTANT]
>Il servizio Sperimentazione di Azure Machine Learning supporta attualmente Python 3.5.2 e Spark 2.1.11, rispettivamente come versioni di runtime di Python e Spark. 


### <a name="key-concepts-in-experimentation-service"></a>Concetti chiave del servizio Sperimentazione
È importante comprendere i concetti seguenti nell'esecuzione dell'esperimento di Azure Machine Learning. Nelle sezioni successive si illustra come usare questi concetti in modo dettagliato. 

#### <a name="compute-target"></a>Destinazione del calcolo
La _destinazione di calcolo_ specifica la posizione in cui eseguire il programma, ad esempio il desktop, un Docker remoto in una macchina virtuale o un cluster. La destinazione di calcolo deve essere indirizzabile e accessibile dall'utente. Workbench offre la possibilità di creare destinazioni di calcolo e di gestirle usando l'applicazione Workbench e l'interfaccia della riga di comando. 

Il comando _collegamento a computetarget az ml_ nell'interfaccia della riga di comando consente di creare una destinazione di calcolo che è possibile usare durante le esecuzioni.

Le destinazioni di calcolo supportate sono:
* Ambiente Python (3.5.2) locale nel computer installato da Workbench.
* Docker locale sul computer in uso
* Ambiente Python gestito dall'utente in macchine virtuali Linux-Ubuntu remote. Ad esempio, un [DSVM basato su Ubuntu in Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* Docker remoto in macchine virtuali Ubuntu Linux. Ad esempio, un [DSVM basato su Ubuntu in Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* [HDInsight per il cluster Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) in Azure

Il servizio Sperimentazione supporta attualmente Python 3.5.2 e Spark 2.1.11, rispettivamente come versioni di runtime di Python e Spark. 

>[!IMPORTANT]
> Le macchine virtuali di Windows che eseguono Docker **non** sono supportate come destinazioni di calcolo remote.

#### <a name="execution-environment"></a>Ambiente di esecuzione
L'_ambiente di esecuzione_ definisce la configurazione di runtime e le dipendenze necessarie per eseguire il programma in Workbench.

L'utente gestisce l'ambiente di esecuzione locale con gli strumenti e i gestori di pacchetti preferiti, se l'esecuzione avviene nel runtime predefinito di Workbench. 

Conda viene usato per gestire le esecuzioni di Docker locale e Docker remoto, oltre alle esecuzioni basate su HDInsight. Per queste destinazioni di calcolo, la configurazione dell'ambiente di esecuzione viene gestita attraverso i file **Conda_dependencies.yml** e **Spark_dependencies.yml**. Questi file si trovano nella cartella **aml_config** all'interno del progetto.

**I runtime supportati per gli ambienti di esecuzione sono:**
* Python 3.5.2
* Spark 2.1.11

### <a name="run-configuration"></a>Configurazione di esecuzione
Oltre alla destinazione di calcolo e all'ambiente di esecuzione, Azure Machine Learning fornisce un framework per definire e modificare le *configurazioni di esecuzione*. Diverse esecuzioni dell'esperimento potrebbero richiedere una configurazione diversa come parte di una sperimentazione iterativa. Potrebbe esserci lo sweep di intervalli di parametri diversi, usando origini dati diverse e regolando i parametri di spark. Il servizio Sperimentazione fornisce un framework per la gestione delle configurazioni di esecuzione.

Eseguendo il comando _az ml computetarget attach_ vengono creati due file nella cartella **aml_config** nel progetto: un file con estensione "compute" e un file con estensione "runconfig" denominati in base a questa convenzione: _<nome_destinazione_calcolo>.compute_ e _<nome_destinazione_calcolo>.runconfig_. Il file .runconfig viene creato automaticamente per praticità quando si crea una destinazione di calcolo. È possibile creare e gestire altre configurazioni di esecuzione usnado il comando _az ml runconfigurations_ nell'interfaccia della riga di comando. È anche possibile crearli e modificarli nel file system.

La configurazione di esecuzione in Workbench consente anche di specificare le variabili di ambiente. È possibile specificare le variabili di ambiente e usarle nel codice, aggiungendo la seguente sezione nel file .runconfig. 

```
EnvironmentVariables:
    "EXAMPLE_ENV_VAR1": "Example Value1"
    "EXAMPLE_ENV_VAR2": "Example Value2"
```

Queste variabili di ambiente sono accessibili nel codice. Ad esempio, il frammento di codice di phyton stampa la variabile di ambiente denominata "EXAMPLE_ENV_VAR1"
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

_**Nella figura seguente viene illustrato il flusso di alto livello per l'esecuzione del test iniziale.**_
![](media/experimentation-service-configuration/experiment-execution-flow.png)

## <a name="experiment-execution-scenarios"></a>Scenari di esecuzione dell'esperimento
Questa sezione esplora gli scenari di esecuzione e contiene informazioni sulle modalità di esecuzione degli esperimenti in Azure Machine Learning, in particolare riguardo all'esecuzione di un esperimento in locale, in una VM remota e in un cluster HDInsight. Questa sezione è una procedura dettagliata che va a partire dalla creazione di una destinazione di calcolo all'esecuzione degli esperimenti.

>[!NOTE]
>Per il resto di questo articolo vengono usati i comandi dell'interfaccia della riga di comando (CLI) per mostrare i concetti e le funzionalità. Le funzionalità descritte di seguito possono anche essere usate da Workbench.

## <a name="launching-the-cli"></a>Avvio dell'interfaccia della riga di comando
Un modo semplice per avviare l'interfaccia della riga di comando è aprire un progetto in Workbench e passare a **File-->Apri prompt dei comandi**.

![](media/experimentation-service-configuration/opening-cli.png)

Questo comando avvia una finestra del terminale in cui è possibile immettere dei comandi per eseguire gli script nella cartella del progetto corrente. Questa finestra del terminale è configurata con l'ambiente Python 3.5.2, che viene installato da Workbench.

>[!NOTE]
> Quando si esegue qualsiasi comando _ml az_ dalla finestra di comando, è necessario essere autenticati in Azure. L'interfaccia della riga di comando usa una cache di autenticazione indipendente e quindi l'app desktop. L'accesso a Workbench non implica quindi l'autenticazione nell'ambiente dell'interfaccia della riga di comando. Per l'autenticazione, seguire la procedura seguente. Il token di autenticazione è memorizzato nella cache in locale per un periodo di tempo pertanto è necessario solo ripetere questi passaggi quando il token scade. Quando il token scade o se si visualizzano errori di autenticazione, eseguire i comandi seguenti:

```
# to authenticate 
$ az login

# to list subscriptions
$ az account list -o table

# to set current subscription to a particular subscription ID 
$ az account set -s <subscription_id>

# to verify your current Azure subscription
$ az account show
```

>[!NOTE] 
>Quando si esegue il comando _az ml_ all'interno di una cartella del progetto, verificare che il progetto appartenga a un account di Sperimentazione di Azure Machine Learning nella sottoscrizione _corrente_ di Azure. In caso contrario si potrebbero verificare degli errori di esecuzione.


## <a name="running-scripts-and-experiments"></a>Esecuzione degli script e sperimentazioni
Workbench permette di eseguire gli script di Python e PySpark in varie destinazioni di calcolo usando il comando _az ml experiment submit_. Questo comando richiede una definizione di configurazione di esecuzione. 

Workbench crea un file con estensione runconfig corrispondente quando si crea una destinazione di calcolo, ma è possibile creare altre configurazioni di esecuzione usando il comando _az ml runconfiguration create_. È anche possibile modificare manualmente i file di configurazione di esecuzione.

Le configurazioni di esecuzione compaiono come parte dell'esperienza di esecuzione dell'esperimento in Workbench. 

>[!NOTE]
>Altre informazioni sul file di configurazione di esecuzione nella sezione [Experiment Execution Configuration Reference](experimentation-service-configuration-reference.md) (Riferimento alla configurazione dell'esecuzione dell'esperimento).

## <a name="running-a-script-locally-on-workbench-installed-runtime"></a>Esecuzione di uno script in locale nel runtime installato da Workbench
Workbench consente di eseguire gli script direttamente nel runtime Python 3.5.2 installato da Workbench. Il runtime predefinito viene installato in fase di installazione di Workbench e include le librerie e le dipendenze di Azure Machine Learning. I risultati di esecuzione e gli elementi per le esecuzioni locali vengono ancora salvati nel servizio di cronologia di esecuzione nel cloud.

A differenza delle esecuzioni basate su Docker, questa configurazione _non_ è gestita da Conda. È necessario effettuare manualmente il provisioning delle dipendenze dei pacchetti per l'ambiente Python locale in Workbench.

Il comando seguente permette di eseguire lo script in locale nell'ambiente Python installato da Workbench. 

```
$az ml experiment submit -c local myscript.py
```

Per trovare il percorso dell'ambiente Python predefinito è possibile digitare il comando seguente nella finestra dell'interfaccia della riga di comando di Workbench.
```
$ conda env list
```

>[!NOTE]
>L'esecuzione di PySpark direttamente in locale negli ambienti locali di PySpark **non** è attualmente supportata. Workbench supporta gli script di PySpark in esecuzione nel Docker locale. Nell'immagine Docker di base di Azure Machine Learning è preinstallato Spark 2.1.11. 

_**Panoramica dell'esecuzione locale per uno script di Python:**_
![](media/experimentation-service-configuration/local-native-run.png)

## <a name="running-a-script-on-local-docker"></a>Esecuzione di uno script in un Docker locale
È anche possibile eseguire i progetti in un contenitore Docker nel computer locale tramite il servizio di Sperimentazione. Workbench offre un'immagine Docker di base che include librerie di Azure Machine Learning e il runtime di Spark 2.1.11 per facilitare le esecuzioni di Spark in locale. Docker deve essere già in esecuzione nel computer locale.

Per eseguire lo script di Python o PySpark nel Docker locale, è possibile eseguire i comandi seguenti nell'interfaccia della riga di comando.

```
$az ml experiment submit -c docker myscript.py
```
oppure
```
az ml experiment submit --run-configuration docker myscript.py
```

L'ambiente di esecuzione nel Docker locale viene preparato usando l'immagine Docker di base di Azure Machine Learning. Workbench scarica l'immagine quando viene eseguito per la prima volta e la sovrappone ai pacchetti specificati nel file conda_dependencies.yml. Questa operazione rende l'esecuzione iniziale più lenta, ma le esecuzioni successive sono molto più veloci grazie a Workbench che riutilizza i livelli memorizzati nella cache. 

>[!IMPORTANT]
>È necessario eseguire prima il comando _az ml experiment prepare -c docker_ per preparare l'immagine di Docker per la prima esecuzione. È inoltre possibile impostare il parametro **PrepareEnvironment** su true nel file docker.runconfig. Questa azione prepara automaticamente l'ambiente come parte dell'esecuzione.  

>[!NOTE]
>Se si esegue uno script di PySpark in Spark, viene usato anche spark_dependencies.yml oltre a conda_dependencies.yml.

L'esecuzione degli script in un'immagine Docker offre i vantaggi seguenti:

1. Assicura che lo script possa essere eseguito in modo affidabile in altri ambienti di esecuzione. L'esecuzione in un contenitore Docker consente di individuare ed evitare eventuali riferimenti locali che potrebbero influire sulla portabilità. 

2. Consente di testare rapidamente il codice nei runtime e nei framework che sono complessi da installare e configurare, ad esempio Apache Spark, senza doverli installare manualmente.


_**Panoramica dell'esecuzione di Docker locale per uno script di Python:**_
![](media/experimentation-service-configuration/local-docker-run.png)

## <a name="running-a-script-on-a-remote-docker"></a>Esecuzione di uno script in un Docker remoto
In alcuni casi le risorse disponibili nel computer locale potrebbero non essere sufficiente per eseguire il training del modello da usare. In tal caso il servizio Sperimentazione permette di eseguire facilmente gli script di Python o PySpark nelle macchine virtuali più potenti con un'esecuzione Docker in remoto. 

Le macchine virtuali remote devono soddisfare i requisiti seguenti:
* Le macchine virtuali remote devono eseguire Ubuntu Linux e devono essere accessibili tramite SSH. 
* Le macchine virtuali remote devono disporre di Docker in esecuzione.

>[!IMPORTANT]
> Le macchine virtuali di Windows che eseguono Docker **non** sono supportate come destinazioni di calcolo remote


Il comando seguente permette di creare sia la definizione della destinazione di calcolo sia la configurazione dell'esecuzione per esecuzioni remote basate su Docker.

```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" 
```

Una volta configurata la destinazione del calcolo, è possibile usare il comando seguente per eseguire lo script.
```
$ az ml experiment submit -c remotevm myscript.py
```
>[!NOTE]
>Tenere presente che l'ambiente di esecuzione è configurato usando le specifiche in conda_dependencies.yml. spark_dependencies.yml viene usato anche se il framework di PySpark è specificato nel file .runconfig. 

Il processo di costruzione di Docker per le macchine virtuali remote è esattamente uguale al processo per le esecuzioni di Docker locale pertanto è opportuno prevedere un'esperienza di esecuzione simile.

>[!TIP]
>Se si preferisce evitare la latenza introdotta dalla compilazione dell'immagine di Docker per la prima esecuzione, è possibile usare il comando seguente per preparare la destinazione del calcolo prima di eseguire lo script. az ml experiment prepare -c remotedocker

_**Panoramica dell'esecuzione della macchina virtuale remota per uno script di Python:**_
![](media/experimentation-service-configuration/remote-vm-run.png)

## <a name="running-a-script-on-a-remote-vm-targeting-user-managed-environments"></a>Esecuzione di uno script in una macchina virtuale remota con ambienti di destinazione gestiti dagli utenti
Il servizio Sperimentazione supporta anche l'esecuzione di uno script in un ambiente Python di un utente all'interno di una macchina virtuale Ubuntu remota. In questo modo, è possibile gestire l'ambiente per l'esecuzione e usare comunque le funzionalità di Azure Machine Learning. 

Seguire la procedura illustrata di seguito per eseguire lo script nel proprio ambiente.
* Preparare l'ambiente Python in una macchina virtuale Ubuntu remota o una macchina virtuale di data science installando le dipendenze.
* Installare i requisiti di Azure Machine Learning usando il comando seguente.

```
pip install -I --index-url https://azuremldownloads.azureedge.net/python-repository/preview --extra-index-url https://pypi.python.org/simple azureml-requirements
```

>[!TIP]
>In alcuni casi può essere necessario eseguire questo comando in modalità sudo, a seconda dei privilegi dell'utente. 
```
sudo pip install -I --index-url https://azuremldownloads.azureedge.net/python-repository/preview --extra-index-url https://pypi.python.org/simple azureml-requirements
```
 
* Usare il comando seguente per creare sia la definizione della destinazione di calcolo sia la configurazione di esecuzione per le esecuzioni gestite dall'utente in esecuzioni di macchine virtuali remote.
```
az ml computetarget attach remote --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" 
```
>[!NOTE]
>In questo modo, il parametro "userManagedEnvironment" nel file di configurazione con estensione compute viene impostato su true.

* Impostare la posizione dell'eseguibile del runtime Python nel file con estensione compute. Fare riferimento al percorso completo dell'eseguibile Python. 
```
pythonLocation: python3
```

Una volta configurata la destinazione del calcolo, è possibile usare il comando seguente per eseguire lo script.
```
$ az ml experiment submit -c remotevm myscript.py
```

>[!NOTE]
> Quando l'esecuzione avviene in una macchina virtuale di data science, usare i comandi seguenti

Se si vuole che l'esecuzione avvenga direttamente nell'ambiente Python globale della macchina virtuale di data science, eseguire questo comando.
```
sudo /anaconda/envs/py35/bin/pip install <package>
```


## <a name="running-a-script-on-an-hdinsight-cluster"></a>Esecuzione di uno script in un cluster HDInsight
HDInsight è una piattaforma comune per l'analisi dei big data che supporta Apache Spark. Workbench consente la sperimentazione sui Big Data tramite cluster HDInsight Spark. 

>[!NOTE]
>Il cluster HDInsight deve usare BLOB di Azure come risorsa di archiviazione primaria. L'archiviazione in Azure Data Lake non è ancora supportata.

È possibile creare una destinazione del calcolo ed eseguire la configurazione per un cluster di HDInsight Spark usando il comando seguente:

```
$ az ml computetarget attach cluster --name "myhdi" --address "<FQDN or IP address>" --username "sshuser" --password "sshpassword"  
```

>[!NOTE]
>Se si usa FQDN anziché un indirizzo IP e il cluster di Spark HDI viene denominato _foo_, l'endpoint SSH è sul nodo del driver denominato _foo ssh.azurehdinsight.net_. Non dimenticare il suffisso **-ssh** nel nome del server quando si usa il FQDN per il parametro _--address_.


Dopo aver ottenuto il contesto del calcolo, è possibile eseguire il comando seguente per eseguire lo script di PySpark.

```
$ az ml experiment submit -c myhdi myscript.py
```

Workbench prepara e gestisce l'ambiente di esecuzione nel cluster HDInsight tramite Conda. La configurazione è gestita dai file di configurazione _conda_dependencies.yml_ e _spark_dependencies.yml_. 

Per eseguire esperimenti in questa modalità è necessario l'accesso SSH al cluster HDInsight. 

>[!NOTE]
>La configurazione supportata sono i cluster di HDInsight Spark che eseguono Linux (Ubuntu con Python/PySpark 3.5.2 e Spark 2.1.11).

_**Panoramica dell'esecuzione basata su HDInsight per uno script di PySpark**_
![](media/experimentation-service-configuration/hdinsight-run.png)


## <a name="running-a-script-on-gpu"></a>Esecuzione di uno script su GPU
Per eseguire gli script in GPU, è possibile seguire le indicazioni contenute nell'articolo [Come usare le unità GPU in Azure Machine Learning](how-to-use-gpu.md)

## <a name="using-ssh-key-based-authentication-for-creating-and-using-compute-targets"></a>Uso dell'autenticazione basata su chiavi SSH per la creazione e l'uso di destinazioni di calcolo
Azure Machine Learning Workbench consente di creare e usare destinazioni di calcolo tramite l'autenticazione basata su chiavi SSH oltre allo schema basato su nome utente/password. È possibile usare questa funzionalità quando si usa remotedocker o il cluster come destinazione di calcolo. Quando si usa questo schema, Workbench crea una coppia di chiavi pubblica/privata e restituisce la chiave pubblica. Accodare la chiave pubblica ai file~/.ssh/authorized_keys per il proprio nome utente. Azure Machine Learning Workbench usa quindi l'autenticazione basata su chiavi ssh per l'accesso e l'esecuzione in questa destinazione di calcolo. Poiché la chiave privata per la destinazione di calcolo viene salvata nell'archivio chiavi per l'area di lavoro, altri utenti dell'area di lavoro possono usare la destinazione di calcolo allo stesso modo immettendo il nome utente specificato per la creazione della destinazione di calcolo.  

Per usare questa funzionalità eseguire la procedura seguente. 

- Creare una destinazione di calcolo usando uno dei comandi seguenti.

```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
```
oppure
```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" -k
```
- Accodare la chiave pubblica generata da Workbench al file ~/.ssh/authorized_keys sulla destinazione di calcolo associata. 

>[!IMPORTANT]
>È necessario effettuare l'accesso alla destinazione di calcolo usando lo stesso nome utente usato per creare la destinazione di calcolo. 

- È ora possibile preparare e usare la destinazione di calcolo tramite l'autenticazione basata su chiavi SSH.

```
az ml experiment prepare -c remotevm
```

## <a name="next-steps"></a>Passaggi successivi
* [Creare e installare istanze di Azure Machine Learning](quickstart-installation.md)
* [Gestione modelli](model-management-overview.md)
