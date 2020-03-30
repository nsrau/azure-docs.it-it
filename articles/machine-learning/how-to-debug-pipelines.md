---
title: Eseguire il debug e risolvere i problemi delle pipeline di Machine Learning
titleSuffix: Azure Machine Learning
description: Debug and troubleshoot machine learning pipelines in the Azure Machine Learning SDK for Python. Informazioni sulle insidie comuni per lo sviluppo di pipeline e suggerimenti per il debug degli script prima e durante l'esecuzione remota. Informazioni su come usare Visual Studio Code per eseguire il debug interattivo delle pipeline di Machine Learning.Learn how to use Visual Studio Code to interactively debug your machine learning pipelines.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 03/18/2020
ms.openlocfilehash: b68efbb64e9634ade001373e8cd9d61355bf786f
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388985"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Eseguire il debug e risolvere i problemi delle pipeline di Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo viene illustrato come eseguire il debug e la risoluzione dei problemi relativi alle pipeline di [Machine Learning](concept-ml-pipelines.md) in Azure Machine [Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) e azure Machine Learning [Designer (anteprima).](https://docs.microsoft.com/azure/machine-learning/concept-designer) Vengono fornite informazioni su come:

* Eseguire il debug con Azure Machine Learning SDK
* Eseguire il debug usando la finestra di progettazione di Azure Machine LearningDebug using the Azure Machine Learning designer
* Debug using Application Insights
* Eseguire il debug in modo interattivo usando Visual Studio Code (codice VS) e Python Tools per Visual Studio (PTVSD)

## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>Debug and troubleshoot in the Azure Machine Learning SDK
Nelle sezioni seguenti viene fornita una panoramica delle insidie comuni durante la compilazione di pipeline e delle diverse strategie per il debug del codice in esecuzione in una pipeline. Usare i suggerimenti seguenti quando si verificano problemi nell'esecuzione di una pipeline come previsto.

### <a name="testing-scripts-locally"></a>Test degli script in locale

Uno degli errori più comuni in una pipeline è che uno script collegato (script di pulizia dei dati, script di assegnazione dei dati e così via) non è in esecuzione come previsto o contiene errori di runtime nel contesto di calcolo remoto che sono difficili da eseguire il debug nell'area di lavoro nel computer Azure Studio di apprendimento. 

Le pipeline stesse non possono essere eseguite localmente, ma l'esecuzione degli script in isolamento nel computer locale consente di eseguire il debug più velocemente perché non è necessario attendere il processo di compilazione del calcolo e dell'ambiente. A tale scopo, è necessario un lavoro di sviluppo:Some development work is required to do this:

* Se i dati si trovano in un archivio dati cloud, sarà necessario scaricare i dati e renderli disponibili per lo script. L'uso di un piccolo campione di dati è un buon modo per ridurre il runtime e ottenere rapidamente feedback sul comportamento degli script
* Se si sta tentando di simulare un passaggio intermedio della pipeline, potrebbe essere necessario compilare manualmente i tipi di oggetto previsti dallo script specifico dal passaggio precedente
* Sarà inoltre necessario definire il proprio ambiente e replicare le dipendenze definite nell'ambiente di calcolo remoto

Dopo aver configurato uno script da eseguire nell'ambiente locale, è molto più semplice eseguire attività di debug come:Once you have a script setup to run on your local environment, it is much easier to do debugging tasks like:

* Associazione di una configurazione di debug personalizzataAttaching a custom debug configuration
* Sospensione dell'esecuzione e controllo dello stato dell'oggetto
* Intercettazione di errori logici o di tipo che non verranno esposti fino al runtime

> [!TIP] 
> Dopo aver verificato che lo script è in esecuzione come previsto, un buon passaggio successivo è l'esecuzione dello script in una pipeline a passaggio singolo prima di tentare di eseguirlo in una pipeline con più passaggi.

### <a name="debugging-scripts-from-remote-context"></a>Debug di script da un contesto remotoDebugging scripts from remote context

Il test degli script in locale è un ottimo modo per eseguire il debug di frammenti di codice principali e logica complessa prima di iniziare a compilare una pipeline, ma a un certo punto sarà probabilmente necessario eseguire il debug degli script durante l'esecuzione effettiva della pipeline, soprattutto durante la diagnosi del comportamento che si verifica durante l'interazione tra i passaggi della pipeline. È consigliabile `print()` usare le istruzioni liberali negli script di passaggio in modo da poter visualizzare lo stato dell'oggetto e i valori previsti durante l'esecuzione remota, in modo simile a come si esegue il debug del codice JavaScript.

Il file `70_driver_log.txt` di registro contiene: 

* Tutte le istruzioni stampate durante l'esecuzione dello script
* Traccia dello stack per lo script 

Per trovare questo e altri file di log nel portale, fare innanzitutto clic sull'esecuzione della pipeline nell'area di lavoro.

![Pagina elenco esecuzione pipeline](./media/how-to-debug-pipelines/pipelinerun-01.png)

Passare alla pagina dei dettagli dell'esecuzione della pipeline.

![Pagina dei dettagli dell'esecuzione della pipelinePipeline run detail page](./media/how-to-debug-pipelines/pipelinerun-02.png)

Fare clic sul modulo per il passaggio specifico. Passare alla scheda **Registri.** Altri log includono informazioni sul processo di compilazione dell'immagine dell'ambiente e sugli script di preparazione dei passaggi.

![Scheda del registro della pagina dei dettagli dell'esecuzione della pipelinePipeline run detail page log tab](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> Le *esecuzioni* per le pipeline pubblicate sono disponibili nella scheda **Endpoint** nell'area di lavoro. In **Esperimenti** o **Pipeline**è possibile eseguire per *pipeline non pubblicate* .

### <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi

La tabella seguente contiene problemi comuni durante lo sviluppo della pipeline, con possibili soluzioni.

| Problema | Possibile soluzione |
|--|--|
| Impossibile trasmettere `PipelineData` dati alla directory | Assicurarsi di aver creato una directory nello script che corrisponde alla posizione in cui la pipeline prevede i dati di output del passaggio. Nella maggior parte dei casi, un argomento di input definirà la directory di output e quindi creerà la directory in modo esplicito. Utilizzare `os.makedirs(args.output_dir, exist_ok=True)` per creare la directory di output. Vedere [l'esercitazione](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) per un esempio di script di punteggio che mostra questo modello di progettazione. |
| Bug di dipendenza | Se gli script sono stati sviluppati e testati localmente ma si rilevano problemi di dipendenza durante l'esecuzione in un calcolo remoto nella pipeline, verificare che le dipendenze e le versioni dell'ambiente di calcolo corrispondano all'ambiente di test. (Vedere [Creazione, memorizzazione nella cache e riutilizzo dell'ambiente](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)|
| Errori ambigui con destinazioni di calcolo | L'eliminazione e la ricreazione delle destinazioni di calcolo possono risolvere determinati problemi con le destinazioni di calcolo. |
| Pipeline che non riutilizza i passaggi | Il riutilizzo del passaggio è abilitato per impostazione predefinita, ma assicurarsi di non averlo disabilitato in un passaggio della pipeline. Se il riutilizzo `allow_reuse` è disabilitato, il parametro nel passaggio verrà impostato su `False`. |
| La pipeline viene rieseguita inutilmentePipeline is rerunning innecessarily | Per garantire che i passaggi vengano rieseguiti solo quando cambiano i dati o gli script sottostanti, separare le directory per ogni passaggio. Se si utilizza la stessa directory di origine per più passaggi, potrebbero verificarsi riesecuzioni non necessarie. Usare `source_directory` il parametro in un oggetto passaggio della pipeline per puntare alla directory `source_directory` isolata per tale passaggio e assicurarsi di non usare lo stesso percorso per più passaggi. |

### <a name="logging-options-and-behavior"></a>Opzioni e comportamento della registrazione

Nella tabella seguente vengono fornite informazioni per diverse opzioni di debug per le pipeline. Non è un elenco completo, poiché esistono altre opzioni oltre a quelle di Azure Machine Learning, Python e OpenCensus illustrate di seguito.

| Libreria                    | Type   | Esempio                                                          | Destination                                  | Risorse                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | Metrica | `run.log(name, val)`                                             | Azure Machine Learning Portal UI             | [Come tenere traccia degli esperimenti](how-to-track-experiments.md#available-metrics-to-track)<br>[classe azureml.core.Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Stampa/registrazione Python    | File di log    | `print(val)`<br>`logging.info(message)`                          | Log dei driver, finestra di progettazione di Azure Machine Learning | [Come tenere traccia degli esperimenti](how-to-track-experiments.md#available-metrics-to-track)<br><br>[Registrazione Python](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | File di log    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights - tracce                | [Debug delle pipeline in Application Insights](how-to-debug-pipelines-application-insights.md)<br><br>[Esportatori di Monitoraggio di Azure per OpenCensus](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Libro di cucina per la registrazione dei Python](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Esempio di opzioni di registrazione

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

## <a name="debug-and-troubleshoot-in-azure-machine-learning-designer-preview"></a>Eseguire il debug e la risoluzione dei problemi in Progettazione Azure Machine Learning (anteprima)Debug and troubleshoot in Azure Machine Learning designer (preview)

In questa sezione viene fornita una panoramica della risoluzione dei problemi relativi alle pipeline nella finestra di progettazione.
Per le pipeline create nella finestra di progettazione, è possibile trovare i file di **log** nella pagina di creazione e modifica o nella pagina dei dettagli di esecuzione della pipeline.

### <a name="access-logs-from-the-authoring-page"></a>Accedere ai log dalla pagina di creazione

Quando si invia un'esecuzione della pipeline e si rimane nella pagina di creazione, è possibile trovare i file di log generati per ogni modulo.

1. Selezionare un modulo nell'area di disegno di creazione.
1. Nel riquadro di destra del modulo, passare alla scheda **Output e registri.**
1. Selezionare il `70_driver_log.txt`file di registro .

    ![Creazione dei registri dei moduli delle pagine](./media/how-to-debug-pipelines/pipelinerun-05.png)

### <a name="access-logs-from-pipeline-runs"></a>Accedere ai log dalle esecuzioni della pipelineAccess logs from pipeline runs

È inoltre possibile trovare i file di log di esecuzioni specifiche nella pagina dei dettagli dell'esecuzione della pipeline nelle sezioni Pipeline o **Esperimenti.You** can also find the log files of specific runs in the pipeline run detail page in either the **Pipelines** or Experiments sections.

1. Selezionare un'esecuzione della pipeline creata nella finestra di progettazione.
    ![Pagina di esecuzione della pipelinePipeline run page](./media/how-to-debug-pipelines/pipelinerun-04.png)
1. Selezionare un modulo qualsiasi nel riquadro di anteprima.
1. Nel riquadro di destra del modulo, passare alla scheda **Output e registri.**
1. Selezionare il `70_driver_log.txt`file di registro .

## <a name="debug-and-troubleshoot-in-application-insights"></a>Debug and troubleshoot in Application Insights
Per altre informazioni sull'uso della libreria OpenCensus Python in questo modo, vedere questa guida: Debug e risoluzione dei problemi relativi alle pipeline di apprendimento automatico in Application InsightsFor more information on using the OpenCensus Python library in this manner, see this guide: [Debug and troubleshoot machine learning pipelines in Application Insights](how-to-debug-pipelines-application-insights.md)

## <a name="debug-and-troubleshoot-in-visual-studio-code"></a>Debug and troubleshoot in Visual Studio Code

In alcuni casi, potrebbe essere necessario eseguire il debug interattivo del codice Python usato nella pipeline di Machine ML. Utilizzando Visual Studio Code (codice VS) e Python Tools per Visual Studio (PTVSD), è possibile connettersi al codice durante l'esecuzione nell'ambiente di training.

### <a name="prerequisites"></a>Prerequisiti

* Area di lavoro di Azure Machine Learning configurata per l'uso di una rete virtuale di __Azure.An Azure Machine Learning workspace__ that is configured to use an Azure Virtual __Network__.
* Una pipeline di __Azure Machine Learning__ che usa gli script Python come parte dei passaggi della pipeline. Ad esempio, un PythonScriptStep.For example, a PythonScriptStep.
* Un cluster di Azure Machine Learning Compute, che si trova __nella rete virtuale__ e viene usato dalla pipeline per il __training.__
* Ambiente __di sviluppo__ presente nella rete __virtuale.__ L'ambiente di sviluppo potrebbe essere uno dei seguenti:

    * Una macchina virtuale di Azure nella rete virtualeAn Azure Virtual Machine in the virtual network
    * Un'istanza di calcolo della macchina virtuale del blocco appunti nella rete virtualeA Compute instance of Notebook VM in the virtual network
    * Un computer client connesso alla rete virtuale da una rete privata virtuale (VPN).

Per altre informazioni sull'uso di una rete virtuale di Azure con Azure Machine Learning, vedere Proteggere i processi di sperimentazione e inferenza di Azure ML all'interno di una rete virtuale di Azure.For more information on using an Azure Virtual Network with Azure Machine Learning, see [Secure Azure ML experimentation and inference jobs within an Azure Virtual Network.](how-to-enable-virtual-network.md)

### <a name="how-it-works"></a>Funzionamento

I passaggi della pipeline di ML eseguono script Python.Your ML pipeline steps run Python scripts. Questi script vengono modificati per eseguire le seguenti azioni:
    
1. Registrare l'indirizzo IP dell'host su cui sono in esecuzione. Utilizzare l'indirizzo IP per connettere il debugger allo script.

2. Avviare il componente di debug PTVSD e attendere la connessione di un debugger.

3. Dall'ambiente di sviluppo è possibile monitorare i log creati dal processo di training per trovare l'indirizzo IP in cui è in esecuzione lo script.

4. Dire codice VS l'indirizzo IP per connettere il debugger a utilizzando un `launch.json` file.

5. Collegare il debugger ed eseguire lo script in modo interattivo.

### <a name="configure-python-scripts"></a>Configurare gli script Python

Per abilitare il debug, apportare le modifiche seguenti agli script Python usati dai passaggi nella pipeline di ML:

1. Aggiungere le seguenti istruzioni import:

    ```python
    import ptvsd
    import socket
    from azureml.core import Run
    ```

1. Aggiungere gli argomenti seguenti. Questi argomenti consentono di abilitare il debugger in base alle esigenze e impostare il timeout per la connessione del debugger:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the Azure ML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
    ```

1. Aggiungere le istruzioni seguenti. Queste istruzioni caricano il contesto di esecuzione corrente in modo che sia possibile registrare l'indirizzo IP del nodo in cui è in esecuzione il codice:

    ```python
    global run
    run = Run.get_context()
    ```

1. Aggiungere `if` un'istruzione che avvia PTVSD e attende la connessione di un debugger. Se nessun debugger si connette prima del timeout, lo script continua normalmente.

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        ip = socket.gethostbyname(socket.gethostname())
        print(f'ip_address: {ip}')
        ptvsd.enable_attach(address=('0.0.0.0', 5678),
                            redirect_output=True)
        # Wait for the timeout for debugger to attach
        ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
        print(f'Debugger attached = {ptvsd.is_attached()}')
    ```

L'esempio Python seguente `train.py` mostra un file di base che abilita il debug:The following Python example shows a basic file that enables debugging:

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import ptvsd
import socket
from azureml.core import Run

print("In train.py")
print("As a data scientist, this is where I use my training code.")

parser = argparse.ArgumentParser("train")

parser.add_argument("--input_data", type=str, help="input data")
parser.add_argument("--output_train", type=str, help="output_train directory")

# Argument check for remote debugging
parser.add_argument('--remote_debug', action='store_true')
parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the AML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    ip = socket.gethostbyname(socket.gethostname())
    print(f'ip_address: {ip}')
    ptvsd.enable_attach(address=('0.0.0.0', 5678),
                        redirect_output=True)
    # Wait for the timeout for debugger to attach
    ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
    print(f'Debugger attached = {ptvsd.is_attached()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>Configurare la pipeline di MLConfigure ML pipeline

Per fornire i pacchetti Python necessari per avviare PTVSD e `pip_packages=['ptvsd', 'azureml-sdk==1.0.83']`ottenere il contesto di esecuzione, creare un ambiente e impostare . Modificare la versione dell'SDK in modo che corrisponda a quella in uso. Il frammento di codice seguente illustra come creare un ambiente:The following code snippet demonstrates how to create an environment:

```python
# Use a RunConfiguration to specify some additional requirements for this step.
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_CPU_IMAGE

# create a new runconfig object
run_config = RunConfiguration()

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image for execution
run_config.environment.python.user_managed_dependencies = False

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
                                                                           pip_packages=['ptvsd', 'azureml-sdk==1.0.83'])
```

Nella sezione [Configura script Python](#configure-python-scripts) sono stati aggiunti due nuovi argomenti agli script utilizzati dai passaggi della pipeline di ML. Nel frammento di codice seguente viene illustrato come utilizzare questi argomenti per abilitare il debug per il componente e impostare un timeout. Viene inoltre illustrato come utilizzare l'ambiente creato in precedenza impostando `runconfig=run_config`:

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300],
                         compute_target=aml_compute, 
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

Quando la pipeline viene eseguita, ogni passaggio crea un'esecuzione figlio. Se il debug è abilitato, lo script modificato `70_driver_log.txt` registra informazioni simili al testo seguente nell'esecuzione figlio:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Salvare `ip_address` il valore. Verranno usate nella sezione successiva.

> [!TIP]
> È inoltre possibile trovare l'indirizzo IP dai log di esecuzione per l'esecuzione figlio per questo passaggio della pipeline. Per altre informazioni sulla visualizzazione di queste informazioni, vedere Monitorare le [esecuzioni e le metriche dell'esperimento azure ML.](how-to-track-experiments.md)

### <a name="configure-development-environment"></a>Configurare l'ambiente di sviluppo

1. Per installare Python Tools per Visual Studio (PTVSD) nell'ambiente di sviluppo del codice VS, utilizzare il comando seguente:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Per ulteriori informazioni sull'utilizzo di PTVSD con codice VS, vedere [Debug remoto](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Per configurare il codice VS per comunicare con il calcolo di Azure Machine Learning che esegue il debugger, creare una nuova configurazione di debug:To configure VS Code to communicate with the Azure Machine Learning compute that is running the debugger, create a new debug configuration:

    1. Da Codice VS, selezionare il menu __Debug__ e quindi selezionare __Apri configurazioni__. Viene aperto un file denominato __launch.json.__

    1. Nel file __launch.json__ individuare la `"configurations": [`riga che contiene e inserire il testo seguente dopo di esso. Modificare `"host": "10.3.0.5"` la voce con l'indirizzo IP restituito nei log della sezione precedente. Modificare `"localRoot": "${workspaceFolder}/code/step"` la voce in una directory locale contenente una copia dello script sottoposto a debug:

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "10.3.0.5",
            "redirectOutput": true,
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}/code/step1",
                    "remoteRoot": "."
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Se sono già presenti altre voci nella sezione delle configurazioni, aggiungere una virgola (,) dopo il codice inserito.

        > [!TIP]
        > La procedura consigliata consiste nel mantenere le risorse per `localRoot` gli `/code/step1`script in directory separate, motivo per cui il valore di esempio fa riferimento a .
        >
        > Se si esegue il debug di più script, in directory diverse creare una sezione di configurazione separata per ogni script.

    1. Salvare il file __launch.json.__

### <a name="connect-the-debugger"></a>Connettere il debugger

1. Aprire il codice VS e aprire una copia locale dello script.
2. Impostare i punti di interruzione in cui si desidera che lo script si interrompa dopo aver allegato.
3. Mentre il processo figlio esegue lo `Timeout for debug connection` script e viene visualizzato nei registri, utilizzare il tasto F5 o selezionare __Debug__. Quando richiesto, selezionare la configurazione di __debug remoto di Azure Machine Learning: .When__ prompted, select the Azure Machine Learning Compute: remote debug configuration. È anche possibile selezionare l'icona di debug dalla barra laterale, la voce di debug remoto di __Azure Machine Learning:__ dal menu a discesa Debug e quindi usare la freccia verde per connettere il debugger.

    A questo punto, il codice VS si connette a PTVSD sul nodo di calcolo e si arresta in corrispondenza del punto di interruzione impostato in precedenza. È ora possibile eseguire il codice un'istruzione alla volta durante l'esecuzione, visualizzare le variabili e così via.

    > [!NOTE]
    > Se nel registro `Debugger attached = False`viene visualizzata una voce che indica , il timeout è scaduto e lo script è continuato senza il debugger. Inviare nuovamente la pipeline e `Timeout for debug connection` connettere il debugger dopo il messaggio e prima della scadenza del timeout.

## <a name="next-steps"></a>Passaggi successivi

* Vedere le informazioni di riferimento sull'SDK per informazioni sul pacchetto [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) e sul pacchetto [azureml-pipelines-steps.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)

* Vedere l'elenco delle [eccezioni della finestra](algorithm-module-reference/designer-error-codes.md)di progettazione e dei codici di errore .
