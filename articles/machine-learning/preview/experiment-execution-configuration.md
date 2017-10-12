---
title: Panoramica del servizio di esecuzione dell'esperimento di Azure Machine Learning
description: Questo documento fornisce una panoramica ad alto livello per il servizio di esecuzione dell'esperimento di Azure Machine Learning
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/17/2017
ms.openlocfilehash: bb1c7d318939c42edb9a51e28dec31593f2485f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-azure-machine-learning-experiment-execution-service"></a>Panoramica del servizio di esecuzione dell'esperimento di Azure Machine Learning
Il servizio di esecuzione dell'esperimento di Azure ML (Azure Machine Learning) consente agli esperti di science data di svolgere i propri esperimenti tramite l'esecuzione di Azure ML e di eseguire le funzionalità di gestione. Fornisce un framework per la sperimentazione agile con iterazioni veloci. Il Workbench di Azure ML consente di avviare delle esecuzioni locali sul computer e un semplice percorso per la scalabilità verticale e orizzontale in altri ambienti, come ad esempio le macchine virtuali remote di data science con GPU o i cluster HDInsight che eseguono Spark.

Il servizio di esecuzione dell'esperimento viene compilato per fornire esecuzioni isolate, riproducibile e coerenti degli esperimenti. Consente di gestire i database di destinazione del calcolo, gli ambienti di esecuzione e le configurazioni di esecuzione. Tramite l'esecuzione del Workbench di Azure ML Workbench, ed eseguendo le funzionalità di gestione, è possibile spostarsi facilmente tra ambienti diversi. 

È possibile eseguire uno script Python o PySpark in un progetto di Workbench di Azure ML in locale o su larga scala nel cloud. 

È possibile eseguire gli script in: 

* Ambiente di Python (3.5.2) nel computer locale installato dal Workbench di Azure ML.
* Ambiente di Conda Python all'interno di un contenitore Docker nel computer locale
* Ambiente di Conda Python all'interno di un contenitore Docker in un computer Linux remoto. Ad esempio, un [DSVM basato su Ubuntu in Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* [HDInsight per Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) in Azure

>[!IMPORTANT]
>Il servizio di esecuzione di Azure ML supporta attualmente Python 3.5.2 e Spark 2.1.11 come versioni di runtime di Python e Spark, rispettivamente. 


## <a name="key-concepts-in-azure-ml-experiment-execution"></a>Concetti chiave nell'esecuzione dell'esperimento di Azure ML
È importante comprendere i concetti seguenti nell'esecuzione dell'esperimento di Azure ML. Nelle sezioni successive si illustra come usare questi concetti in modo dettagliato. 
### <a name="compute-target"></a>Destinazione del calcolo
La destinazione del calcolo specifica la posizione in cui eseguire il programma dell'utente, ad esempio il desktop dell'utente, il Docker remoto in una macchina virtuale o un cluster. La destinazione di calcolo deve essere indirizzabile e accessibile dall'utente. Il Workbench di Azure ML offre la possibilità di creare destinazioni di calcolo e di gestirle usando l'applicazione di Workbench e l'interfaccia della riga di comando. 

Il comando _collegamento a computetarget az ml_ nell'interfaccia della riga di comando consente di creare una destinazione di calcolo che è possibile usare durante le esecuzioni.

### <a name="supported-compute-targets-are"></a>Le destinazioni di calcolo supportate sono:
* Ambiente di Python (3.5.2) locale sul computer installato dal Workbench di Azure ML.
* Docker locale sul computer in uso
* Docker remoto in macchine virtuali Ubuntu Linux. Ad esempio, un [DSVM basato su Ubuntu in Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* [HDInsight per il cluster Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) in Azure

Il servizio di esecuzione di Azure ML supporta attualmente Python 3.5.2 e Spark 2.1.11 come versioni di runtime di Python e Spark, rispettivamente. 

>[!IMPORTANT]
> Le macchine virtuali di Windows che eseguono Docker **non** sono supportate come destinazioni di calcolo remote.

### <a name="execution-environment"></a>Ambiente di esecuzione
L'ambiente di esecuzione definisce la configurazione della fase di esecuzione e le dipendenze necessarie per eseguire il programma in Azure ML Workbench.

L'utente gestisce l'ambiente di esecuzione locale tramite i suoi strumenti preferiti e i gestori di pacchetti se in esecuzione sul runtime predefinito di Azure ML Workbench. 

Conda viene usato per gestire le esecuzioni di Docker locale e Docker remoto, nonché le esecuzioni basate su HDInsigh. Per queste destinazioni di calcolo, la configurazione dell'ambiente di esecuzione viene gestita tramite **Conda_dependencies.yml** e **Spark_dependencies.yml file**. Questi file si trovano nella cartella **aml_config** all'interno del progetto.

**I runtime supportati per gli ambienti di esecuzione sono:**
* Python 3.5.2
* Spark 2.1.11

### <a name="run-configuration"></a>Configurazione di esecuzione
Oltre alla destinazione del calcolo e all'ambiente di esecuzione, Azure Machine Learning fornisce un framework per definire e modificare le configurazioni di esecuzione. Diverse esecuzioni dell'esperimento potrebbero richiedere una configurazione diversa come parte di una sperimentazione iterativa. Potrebbe esserci lo sweep di intervalli di parametri diversi, usando origini dati diverse e regolando i parametri di spark. Il servizio di esecuzione di Azure ML fornisce un framework per la gestione della configurazione di esecuzione.

Eseguendo il comando _collegare computetarget az ml_ si generano due file nella cartella **aml_config** nel progetto: un .compute e un .runconfig che seguono questa convenzione: _<your_computetarget_name>.compute_ e _<your_computetarget_name>.runconfig_. Il file .runconfig viene creato automaticamente per praticità quando si crea una destinazione di calcolo. È possibile creare e gestire altre configurazioni di esecuzione usnado il comando _az ml runconfigurations_ nell'interfaccia della riga di comando. È anche possibile crearli e modificarli nel file system.

La configurazione di esecuzione in Azure ML Workbench consente inoltre di specificare le variabili di ambiente. È possibile specificare le variabili di ambiente e usarle nel codice, aggiungendo la seguente sezione nel file .runconfig. 

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
![](media/experiment-execution-configuration/experiment-execution-flow.png)

## <a name="azure-ml-experiment-execution-scenarios"></a>Scenari di esecuzione dell'esperimento di Azure ML
In questa sezione si esplorano gli scenari di esecuzione e si ottengono informazioni sulle modalità con cui Azure ML esegue gli esperimenti, in particolare in relazione all'esecuzione di un esperimento in locale, in una VM remota e in un cluster HDInsight. Questa sezione è una procedura dettagliata che va a partire dalla creazione di una destinazione di calcolo all'esecuzione degli esperimenti.

>[!NOTE]
>Per il resto di questo articolo si stanno usando i comandi della CLI (interfaccia della riga di comando) per mostrare i concetti e le funzionalità. Le funzionalità descritte di seguito possono anche essere usate dall'applicazione desktop Workbench.

## <a name="launching-the-cli"></a>Avvio dell'interfaccia della riga di comando
Un modo semplice per avviare l'interfaccia della riga di comando è l'apertura di un progetto nell'applicazione desktop Workbench di Azure ML e passare a **File--> Apri prompt dei comandi**.

![](media/experiment-execution-configuration/opening-cli.png)

Questo comando avvia una finestra del terminale in cui è possibile immettere dei comandi per eseguire gli script nella cartella del progetto corrente. Questa finestra del terminale è configurata con l'ambiente di Python 3.5.2 che viene installato dal Workbench.

>[!NOTE]
> Quando si esegue qualsiasi comando _ml az_ dalla finestra di comando, è necessario essere autenticati in Azure. L'interfaccia della riga di comando usa una cache di autenticazione indipendente e quindi l'app desktop e pertanto l'accesso all'app desktop Workbench non significa che si è autenticati nell'ambiente dell'interfaccia della riga di comando. Per l'autenticazione, seguire la procedura seguente. Il token di autenticazione è memorizzato nella cache in locale per un periodo di tempo pertanto è necessario solo ripetere questi passaggi quando il token scade. Quando il token scade o se si visualizzano errori di autenticazione, eseguire i comandi seguenti:

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
>Quando si esegue il comando _ml az_ all'interno di una cartella del progetto, verificare che il progetto appartenga a un account di Sperimentazione di Azure Machine Learning nella sottoscrizione _corrente_ di Azure. In caso contrario si potrebbero verificare degli errori di esecuzione.


## <a name="running-scripts-and-experiments"></a>Esecuzione degli script e sperimentazioni
Con Azure ML Workbench, è possibile eseguire gli script di Python e PySpark nelle varie destinazioni di calcolo tramite il comando _az ml experiment submit_. Questo comando richiede una definizione di configurazione di esecuzione. 

Azure ML Workbench crea un file .runconfig corrispondente quando si crea una destinazione di calcolo, ma è possibile creare altre configurazioni di esecuzione usando il comando _az ml runconfiguration create_. È anche possibile modificare manualmente i file di configurazione di esecuzione.

Le configurazioni di esecuzione appaiono come parte dell'esperienza di esecuzione dell'esperimento nell'applicazione del Workbench. 

>[!NOTE]
>Altre informazioni sul file di configurazione di esecuzione nella sezione [Experiment Execution Configuration Reference](experiment-execution-configuration-reference.md) (Riferimento alla configurazione dell'esecuzione dell'esperimento).

## <a name="running-a-script-locally-on-azure-ml-workbench-installed-runtime"></a>Esecuzione di uno script in locale nel runtime installato su Azure ML Workbench
Azure ML Workbench consente di eseguire gli script direttamente sul runtime di Python 3.5.2 installato su Azure ML Workbench. Il runtime predefinito viene installato in fase di installazione di Azure ML Workbench e include le librerie e le dipendenze di Azure ML. I risultati di esecuzione e gli elementi per le esecuzioni locali vengono ancora salvati nel servizio di cronologia di esecuzione nel cloud.

A differenza delle esecuzioni basate su Docker, questa configurazione _non_ è gestita da Conda. È necessario specificare manualmente le dipendenze dei pacchetti per l'ambiente di Python locale in Azure ML Workbench.

È possibile eseguire il comando seguente per eseguire lo script in locale nell'ambiente di Python installato su Workbench. 

```
$az ml experiment submit -c local myscript.py
```

È possibile trovare il percorso per l'ambiente di Python predefinito digitando il comando seguente nella finestra dell'interfaccia della riga di comando di Azure ML Workbench.
```
$ conda env list
```

>[!NOTE]
>L'esecuzione di PySpark direttamente in locale negli ambienti locali di PySpark **non** è attualmente supportata. Azure ML Workbench supporta gli script di PySpark in esecuzione sul Docker locale. L'immagine di Docker di base di Azure ML è dotata di Spark 2.1.11 pre-installato. 

_**Panoramica dell'esecuzione locale per uno script di Python:**_
![](media/experiment-execution-configuration/local-native-run.png)

## <a name="running-a-script-on-local-docker"></a>Esecuzione di uno script in un Docker locale
Inoltre, è possibile eseguire i progetti in un contenitore Docker nel computer locale tramite il servizio di esecuzione di Azure ML. Azure ML Workbench fornisce un'immagine di base di Docker che è dotata delle librerie di Azure ML e del runtime di Spark 2.1.11 per facilitare le esecuzioni dello Spark locale. Docker deve essere già in esecuzione nel computer locale.

Per eseguire lo script di Python o PySpark nel Docker locale, è possibile eseguire i comandi seguenti nell'interfaccia della riga di comando.

```
$az ml experiment submit -c docker myscript.py
```
oppure
```
az ml experiment submit --run-configuration docker myscript.py
```

L'ambiente di esecuzione nel Docker locale viene preparato usando l'immagine di base di Docker di Azure ML. Azure ML Workbench scarica questa immagine quando viene eseguito per la prima volta e la sovrappone a dei pacchetti specificati nel file dell'utente conda_dependencies.yml. Questa operazione rende l'esecuzione iniziale più lenta, ma le esecuzioni successive sono molto più veloci grazie a Workbench che riutilizza i livelli memorizzati nella cache. 

>[!IMPORTANT]
>È necessario eseguire prima il comando _az ml experiment prepare -c docker_ per preparare l'immagine di Docker per la prima esecuzione. È inoltre possibile impostare il parametro **PrepareEnvironment** su true nel file docker.runconfig. Questa azione preparerà automaticamente l'ambiente come parte della fase di esecuzione.  

>[!NOTE]
>Se si esegue uno script di PySpark in Spark, viene usato anche spark_dependencies.yml oltre a conda_dependencies.yml.

L'esecuzione degli script in un'immagine Docker offre i vantaggi seguenti:

1. Assicura che lo script possa essere eseguito in modo affidabile in altri ambienti di esecuzione. L'esecuzione in un contenitore Docker consente di individuare ed evitare eventuali riferimenti locali che potrebbero influire sulla portabilità. 

2. Consente di testare rapidamente il codice nei runtime e nei framework che sono complessi da installare e configurare, ad esempio Apache Spark, senza doverli installare manualmente.


_**Panoramica dell'esecuzione di Docker locale per uno script di Python:**_
![](media/experiment-execution-configuration/local-docker-run.png)

## <a name="running-a-script-on-a-remote-docker"></a>Esecuzione di uno script in un Docker remoto
In alcuni casi le risorse disponibili sul computer locale dell'utente potrebbero non essere sufficienti per eseguire il training del modello desiderato. In questo caso il servizio di esecuzione di Azure ML consente un modo semplice per eseguire gli script di Python o PySpark in macchine virtuali più potenti tramite l'esecuzione del Docker remoto. 

Le macchine virtuali remote devono soddisfare i requisiti seguenti:
* Le macchine virtuali remote devono eseguire Ubuntu Linux e devono essere accessibili tramite SSH. 
* Le macchine virtuali remote devono disporre di Docker in esecuzione.

>[!IMPORTANT]
> Le macchine virtuali di Windows che eseguono Docker **non** sono supportate come destinazioni di calcolo remote


È possibile usare il comando seguente per creare sia la definizione della destinazione di calcolo sia la configurazione dell'esecuzione per delle esecuzioni remote basate su Docker.

```
az ml computetarget attach --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" --type remotedocker
```

Una volta configurata la destinazione del calcolo, è possibile usare il comando seguente per eseguire lo script.
```
$ az ml experiment submit -c remotevm myscript.py
```
>[!NOTE]
>Tenere presente che l'ambiente di esecuzione è configurato usando le specifiche in conda_dependencies.yml. spark_dependencies.yml viene usato anche se il framework di PySpark è specificato nel file .runconfig. 

Il processo di costruzione di Docker per le macchine virtuali remote è esattamente uguale al processo per le esecuzioni di Docker locale pertanto è opportuno prevedere un'esperienza di esecuzione simile.

>[!TIP]
>Se si preferisce evitare la latenza introdotta dalla compilazione dell'immagine di Docker per la prima esecuzione, è possibile usare il comando seguente per preparare la destinazione del calcolo prima di eseguire lo script. az ml experiment prepare -c <remotedocker>


_**Panoramica dell'esecuzione della macchina virtuale remota per uno script di Python:**_
![](media/experiment-execution-configuration/remote-vm-run.png)


## <a name="running-a-script-on-hdinsight-cluster"></a>Esecuzione di uno script nel cluster HDInsight
HDInsight è una piattaforma comune per l'analisi dei big data che supporta Apache Spark. Azure ML Workbench consente la sperimentazione sui big data usando cluster di HDInsight Spark. 

È possibile creare una destinazione del calcolo ed eseguire la configurazione per un cluster di HDInsight Spark usando il comando seguente:

```
$ az ml computetarget attach --name "myhdi" --address "<FQDN or IP address>" --username "sshuser" --password "sshpassword" --type cluster 
```

>[!NOTE]
>Se si usa FQDN anziché un indirizzo IP e il cluster di Spark HDI viene denominato _foo_, l'endpoint SSH è sul nodo del driver denominato _foo ssh.azurehdinsight.net_. Non dimenticare il suffisso **-ssh** nel nome del server quando si usa il FQDN per il parametro _--address_.


Dopo aver ottenuto il contesto del calcolo, è possibile eseguire il comando seguente per eseguire lo script di PySpark.

```
$ az ml experiment submit -c myhdi myscript.py
```

Azure ML Workbench prepara e gestisce l'ambiente di esecuzione nel cluster di HDInsight tramite Conda. La configurazione è gestita dai file di configurazione _conda_dependencies.yml_ e _spark_dependencies.yml_. 

L'utente necessita dell'accesso SSH al cluster di HDInsight per eseguire gli esperimenti in questa modalità. 

>[!NOTE]
>La configurazione supportata sono i cluster di HDInsight Spark che eseguono Linux (Ubuntu con Python/PySpark 3.5.2 e Spark 2.1.11).

_**Panoramica dell'esecuzione basata su HDInsight per uno script di PySpark**_
![](media/experiment-execution-configuration/hdinsight-run.png)


## <a name="running-a-script-on-gpu"></a>Esecuzione di uno script su GPU
Per eseguire gli script in GPU, è possibile seguire le indicazioni fornite in questo articolo:["How to use GPU in Azure Machine Learning"](how-to-use-gpu.md) (Come usare GPU in Azure Machine Learning)


## <a name="next-steps"></a>Passaggi successivi
* [Creare e installare istanze di Azure Machine Learning](quickstart-installation.md)
* [Gestione modelli](model-management-overview.md)
