---
title: Debug & risolvere i problemi delle pipeline ML
titleSuffix: Azure Machine Learning
description: Eseguire il debug delle pipeline di Azure Machine Learning in Python. Informazioni sui problemi più comuni per lo sviluppo di pipeline e suggerimenti per semplificare il debug degli script prima e durante l'esecuzione remota. Informazioni su come usare Visual Studio Code per eseguire il debug interattivo delle pipeline di machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 03/18/2020
ms.openlocfilehash: 4f0eb6aa92dd8999baed6868a159c86d5e7bd0c8
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594627"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Eseguire il debug e risolvere i problemi delle pipeline di Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come eseguire il debug e risolvere i problemi relativi alle [pipeline di Machine Learning](concept-ml-pipelines.md) in [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) e [Azure Machine Learning Designer (anteprima)](https://docs.microsoft.com/azure/machine-learning/concept-designer). Vengono fornite informazioni su come:

* Eseguire il debug con Azure Machine Learning SDK
* Eseguire il debug con la finestra di progettazione Azure Machine Learning
* Eseguire il debug con Application Insights
* Eseguire il debug in modo interattivo usando Visual Studio Code (VS Code) e il Python Tools for Visual Studio (PTVSD)

## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>Debug e risoluzione dei problemi in Azure Machine Learning SDK
Le sezioni seguenti forniscono una panoramica dei problemi comuni quando si compilano pipeline e strategie diverse per il debug del codice in esecuzione in una pipeline. Usare i suggerimenti seguenti quando si verificano problemi durante l'esecuzione di una pipeline come previsto.

### <a name="testing-scripts-locally"></a>Testing degli script in locale

Uno degli errori più comuni in una pipeline è che uno script associato (script per la pulizia dei dati, script di assegnazione dei punteggi e così via) non è in esecuzione come previsto o contiene errori di runtime nel contesto di calcolo remoto difficili da eseguire il debug nell'area di lavoro in Azure Machine Learning Studio. 

Le pipeline non possono essere eseguite localmente, ma l'esecuzione degli script in isolamento nel computer locale consente di eseguire il debug più velocemente perché non è necessario attendere il processo di compilazione di calcolo e ambiente. Per eseguire questa operazione, è necessario eseguire alcune operazioni di sviluppo:

* Se i dati si trova in un archivio dati cloud, sarà necessario scaricare i dati e renderli disponibili per lo script. L'uso di un piccolo campione di dati è un modo efficace per ridurre il tempo di esecuzione e ottenere commenti e suggerimenti sul comportamento degli script
* Se si sta tentando di simulare un passaggio della pipeline intermedia, potrebbe essere necessario compilare manualmente i tipi di oggetto previsti dallo script specifico dal passaggio precedente
* Sarà inoltre necessario definire un ambiente personalizzato e replicare le dipendenze definite nell'ambiente di calcolo remoto

Una volta completata l'installazione di uno script nell'ambiente locale, è molto più semplice eseguire attività di debug come:

* Associazione di una configurazione di debug personalizzata
* Sospensione dell'esecuzione e controllo dello stato dell'oggetto
* Rilevamento di errori logici o di tipo che non verranno esposti fino al runtime

> [!TIP] 
> Quando è possibile verificare che lo script sia in esecuzione come previsto, un passaggio successivo consiste nell'eseguire lo script in una pipeline a singolo passaggio prima di provare a eseguirlo in una pipeline con più passaggi.

### <a name="debugging-scripts-from-remote-context"></a>Debug di script dal contesto remoto

Il testing degli script in locale è un ottimo modo per eseguire il debug di frammenti di codice e logica complessa prima di iniziare a creare una pipeline, ma a un certo punto è probabile che sia necessario eseguire il debug degli script durante l'esecuzione effettiva della pipeline, soprattutto quando si diagnostica il comportamento che si verifica durante l'interazione tra i passaggi della pipeline. È consigliabile usare le istruzioni `print()` in modo liberato negli script dei passaggi, in modo che sia possibile visualizzare lo stato dell'oggetto e i valori previsti durante l'esecuzione remota, in modo analogo al debug del codice JavaScript.

Il file `70_driver_log.txt` di log contiene: 

* Tutte le istruzioni stampate durante l'esecuzione dello script
* Traccia dello stack per lo script 

Per trovare questo e altri file di log nel portale, fare prima clic sull'esecuzione della pipeline nell'area di lavoro.

![Pagina elenco esecuzione pipeline](./media/how-to-debug-pipelines/pipelinerun-01.png)

Passare alla pagina dei dettagli dell'esecuzione della pipeline.

![Pagina Dettagli esecuzione pipeline](./media/how-to-debug-pipelines/pipelinerun-02.png)

Fare clic sul modulo per il passaggio specifico. Passare alla scheda **logs** . Altri log includono informazioni sul processo di compilazione dell'immagine dell'ambiente e gli script di preparazione dei passaggi.

![Scheda log della pagina Dettagli esecuzione pipeline](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> È possibile trovare le esecuzioni per le *pipeline pubblicate* nella scheda **endpoint** dell'area di lavoro. Le esecuzioni per le *pipeline non pubblicate* si trovano in **esperimenti** o **pipeline**.

### <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi

La tabella seguente contiene problemi comuni durante lo sviluppo di pipeline, con potenziali soluzioni.

| Problema | Possibile soluzione |
|--|--|
| Non è possibile passare i `PipelineData` dati alla directory | Assicurarsi di aver creato una directory nello script che corrisponde alla posizione in cui la pipeline prevede i dati di output del passaggio. Nella maggior parte dei casi, un argomento di input definirà la directory di output, quindi la directory verrà creata in modo esplicito. Utilizzare `os.makedirs(args.output_dir, exist_ok=True)` per creare la directory di output. Vedere l' [esercitazione](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) per un esempio di script di assegnazione dei punteggi che mostra questo schema progettuale. |
| Bug di dipendenza | Se gli script sono stati sviluppati e testati localmente, ma sono stati riscontrati problemi di dipendenza durante l'esecuzione in un calcolo remoto nella pipeline, assicurarsi che le dipendenze e le versioni dell'ambiente di calcolo corrispondano all'ambiente di test. (Vedere [compilazione, memorizzazione nella cache e riutilizzo dell'ambiente](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)|
| Errori ambigui con le destinazioni di calcolo | Eliminando e ricreando le destinazioni di calcolo è possibile risolvere determinati problemi con le destinazioni di calcolo. |
| La pipeline non riusa i passaggi | Il riutilizzo dei passaggi è abilitato per impostazione predefinita, ma assicurarsi che non sia stato disabilitato in un passaggio della pipeline. Se il riutilizzo è disabilitato, il `allow_reuse` parametro nel passaggio verrà impostato `False`su. |
| La pipeline è stata rieseguita inutilmente | Per assicurarsi che i passaggi vengano rieseguiti solo quando cambiano i dati o gli script sottostanti, separare le directory per ogni passaggio. Se si usa la stessa directory di origine per più passaggi, è possibile che si verifichino riesecuzioni non necessarie. Usare il `source_directory` parametro in un oggetto step della pipeline per puntare alla directory isolata per questo passaggio e assicurarsi che non si usi lo stesso `source_directory` percorso per più passaggi. |

### <a name="logging-options-and-behavior"></a>Opzioni di registrazione e comportamento

La tabella seguente fornisce informazioni per diverse opzioni di debug per le pipeline. Non si tratta di un elenco esaustivo, perché esistono altre opzioni oltre a Azure Machine Learning, Python e OpenCensus.

| Libreria                    | Type   | Esempio                                                          | Destination                                  | Risorse                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | Metrica | `run.log(name, val)`                                             | Interfaccia utente del portale di Azure Machine Learning             | [Come tenere traccia degli esperimenti](how-to-track-experiments.md#available-metrics-to-track)<br>[Classe azureml. Core. Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Stampa/registrazione Python    | Registro    | `print(val)`<br>`logging.info(message)`                          | Log driver, progettazione Azure Machine Learning | [Come tenere traccia degli esperimenti](how-to-track-experiments.md#available-metrics-to-track)<br><br>[Registrazione Python](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| Python OpenCensus          | Registro    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights-TRACES                | [Debug delle pipeline in Application Insights](how-to-debug-pipelines-application-insights.md)<br><br>[Esportatori di Monitoraggio di Azure per OpenCensus](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Cookbook per la registrazione di Python](https://docs.python.org/3/howto/logging-cookbook.html) |

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

## <a name="debug-and-troubleshoot-in-azure-machine-learning-designer-preview"></a>Debug e risoluzione dei problemi in Azure Machine Learning Designer (anteprima)

In questa sezione viene fornita una panoramica su come risolvere i problemi relativi alle pipeline nella finestra di progettazione. Per le pipeline create nella finestra di progettazione, è possibile trovare il file **70_driver_log** nella pagina Creazione e modifica o nella pagina Dettagli esecuzione pipeline.

### <a name="get-logs-from-the-authoring-page"></a>Ottenere i log dalla pagina Creazione e modifica

Quando si invia un'esecuzione della pipeline e si rimane nella pagina di creazione, è possibile trovare i file di log generati per ogni modulo al termine dell'esecuzione di ogni modulo.

1. Selezionare un modulo che ha terminato l'esecuzione nell'area di disegno di creazione.
1. Nel riquadro destro del modulo andare alla scheda **output + log** .
1. Espandere il riquadro destro e selezionare il file **70_driver_log. txt** per visualizzare il file nel browser. È anche possibile scaricare i log localmente.

    ![Riquadro di output espanso nella finestra di progettazione](./media/how-to-debug-pipelines/designer-logs.png)

### <a name="get-logs-from-pipeline-runs"></a>Ottenere i log dalle esecuzioni di pipeline

È anche possibile trovare i file di log per esecuzioni specifiche nella pagina dei dettagli dell'esecuzione della pipeline, disponibile nella sezione **pipeline** o **esperimenti** di studio.

1. Consente di selezionare un'esecuzione di pipeline creata nella finestra di progettazione.

    ![Pagina esecuzione pipeline](./media/how-to-debug-pipelines/designer-pipelines.png)

1. Selezionare un modulo nel riquadro di anteprima.
1. Nel riquadro destro del modulo andare alla scheda **output + log** .
1. Espandere il riquadro destro per visualizzare il file **70_driver_log. txt** nel browser oppure selezionare il file per scaricare i log localmente.

> [!IMPORTANT]
> Per aggiornare una pipeline dalla pagina dei dettagli dell'esecuzione della pipeline, è necessario **clonare** l'esecuzione della pipeline in una nuova bozza della pipeline. Un'esecuzione di pipeline è uno snapshot della pipeline. È simile a un file di log e non può essere modificato. 

## <a name="debug-and-troubleshoot-in-application-insights"></a>Debug e risoluzione dei problemi in Application Insights
Per altre informazioni sull'uso della libreria Python di OpenCensus in questo modo, vedere questa guida: [eseguire il debug e risolvere i problemi delle pipeline di Machine Learning in Application Insights](how-to-debug-pipelines-application-insights.md)

## <a name="debug-and-troubleshoot-in-visual-studio-code"></a>Debug e risoluzione dei problemi in Visual Studio Code

In alcuni casi, potrebbe essere necessario eseguire il debug interattivo del codice Python usato nella pipeline ML. Utilizzando Visual Studio Code (VS Code) e il Python Tools for Visual Studio (PTVSD), è possibile connettersi al codice durante l'esecuzione nell'ambiente di training.

### <a name="prerequisites"></a>Prerequisiti

* Un' __area di lavoro Azure Machine Learning__ configurata per l'uso di una __rete virtuale di Azure__.
* Una __pipeline Azure Machine Learning__ che usa script Python come parte dei passaggi della pipeline. Ad esempio, un PythonScriptStep.
* Un cluster Azure Machine Learning calcolo, che si trova __nella rete virtuale__ e viene __usato dalla pipeline per il training__.
* Un __ambiente di sviluppo__ che si trova __nella rete virtuale__. L'ambiente di sviluppo può essere uno dei seguenti:

    * Una macchina virtuale di Azure nella rete virtuale
    * Un'istanza di calcolo della VM del notebook nella rete virtuale
    * Un computer client connesso alla rete virtuale da una rete privata virtuale (VPN).

Per altre informazioni sull'uso di una rete virtuale di Azure con Azure Machine Learning, vedere [proteggere i processi di sperimentazione e inferenza di Azure ml in una rete virtuale di Azure](how-to-enable-virtual-network.md).

### <a name="how-it-works"></a>Funzionamento

I passaggi della pipeline di ML eseguono script Python. Questi script vengono modificati per eseguire le azioni seguenti:
    
1. Registrare l'indirizzo IP dell'host in cui sono in esecuzione. Per connettere il debugger allo script, usare l'indirizzo IP.

2. Avviare il componente di debug PTVSD e attendere la connessione di un debugger.

3. Dall'ambiente di sviluppo, è possibile monitorare i log creati dal processo di training per trovare l'indirizzo IP in cui lo script è in esecuzione.

4. Si indica VS Code indirizzo IP per la connessione del debugger a utilizzando un `launch.json` file.

5. Il debugger viene collegato ed è possibile scorrere lo script in modo interattivo.

### <a name="configure-python-scripts"></a>Configurare gli script Python

Per abilitare il debug, apportare le modifiche seguenti agli script Python usati dai passaggi della pipeline ML:

1. Aggiungere le istruzioni Import seguenti:

    ```python
    import ptvsd
    import socket
    from azureml.core import Run
    ```

1. Aggiungere gli argomenti seguenti. Questi argomenti consentono di abilitare il debugger in base alle esigenze e di impostare il timeout per il fissaggio del debugger:

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

1. Aggiungere un' `if` istruzione che avvia PTVSD e attende la connessione di un debugger. Se nessun debugger si connette prima del timeout, lo script continua come di consueto.

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

Nell'esempio Python seguente viene illustrato un `train.py` file di base che consente il debug:

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

### <a name="configure-ml-pipeline"></a>Configurare la pipeline ML

Per fornire i pacchetti Python necessari per avviare PTVSD e ottenere il contesto di esecuzione, creare un ambiente e `pip_packages=['ptvsd', 'azureml-sdk==1.0.83']`impostarlo. Modificare la versione dell'SDK in modo che corrisponda a quella usata. Il frammento di codice seguente illustra come creare un ambiente:

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

Nella sezione [Configure Python Scripts](#configure-python-scripts) sono stati aggiunti due nuovi argomenti agli script usati dai passaggi della pipeline ml. Nel frammento di codice seguente viene illustrato come utilizzare questi argomenti per abilitare il debug per il componente e impostare un timeout. Viene inoltre illustrato come utilizzare l'ambiente creato in precedenza impostando `runconfig=run_config`:

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

Quando viene eseguita la pipeline, ogni passaggio crea un'esecuzione figlio. Se il debug è abilitato, lo script modificato registra informazioni simili al testo seguente nell'oggetto `70_driver_log.txt` per l'esecuzione figlio:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Salvare il `ip_address` valore. Verranno usate nella sezione successiva.

> [!TIP]
> È anche possibile trovare l'indirizzo IP del passaggio Esegui log per l'esecuzione figlio per questo passaggio della pipeline. Per altre informazioni sulla visualizzazione di queste informazioni, vedere [monitorare le esecuzioni e le metriche dell'esperimento di Azure ml](how-to-track-experiments.md).

### <a name="configure-development-environment"></a>Configurare l'ambiente di sviluppo

1. Per installare il Python Tools for Visual Studio (PTVSD) nell'ambiente di sviluppo VS Code, utilizzare il comando seguente:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Per ulteriori informazioni sull'utilizzo di PTVSD con VS Code, vedere [Remote Debugging](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Per configurare VS Code per comunicare con l'Azure Machine Learning calcolo che esegue il debugger, creare una nuova configurazione di debug:

    1. Da VS Code selezionare il menu __debug__ e quindi selezionare __Apri configurazioni__. Viene aperto un file denominato __Launch. JSON__ .

    1. Nel file __Launch. JSON__ trovare la riga che contiene `"configurations": [`e inserire il testo seguente dopo di esso. Modificare la `"host": "10.3.0.5"` voce nell'indirizzo IP restituito nei log della sezione precedente. Modificare la `"localRoot": "${workspaceFolder}/code/step"` voce in una directory locale che contiene una copia dello script di cui è in corso il debug:

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
        > Se nella sezione configurazioni sono già presenti altre voci, aggiungere una virgola (,) dopo il codice inserito.

        > [!TIP]
        > La procedura consigliata consiste nel memorizzare le risorse per gli script in directory separate, motivo per `localRoot` cui il valore `/code/step1`di esempio fa riferimento a.
        >
        > Se si esegue il debug di più script, in directory diverse creare una sezione di configurazione separata per ogni script.

    1. Salvare il file __Launch. JSON__ .

### <a name="connect-the-debugger"></a>Connettere il debugger

1. Aprire VS Code e aprire una copia locale dello script.
2. Impostare i punti di interruzione in cui si desidera che lo script venga arrestato una volta collegato.
3. Mentre il processo figlio esegue lo script e `Timeout for debug connection` viene visualizzato nei log, utilizzare il tasto F5 o selezionare __debug__. Quando richiesto, selezionare il __Azure Machine Learning calcolo: configurazione di debug remoto__ . È anche possibile selezionare l'icona debug dalla barra laterale, la voce __Azure Machine Learning: Remote Debug__ dal menu a discesa debug e quindi usare la freccia verde per allineare il debugger.

    A questo punto, VS Code si connette a PTVSD sul nodo di calcolo e si arresta in corrispondenza del punto di interruzione impostato in precedenza. È ora possibile esaminare il codice durante l'esecuzione, visualizzare le variabili e così via.

    > [!NOTE]
    > Se il log Visualizza una voce che informa `Debugger attached = False`, il timeout è scaduto e lo script continua senza il debugger. Inviare di nuovo la pipeline e connettere il debugger dopo `Timeout for debug connection` il messaggio e prima della scadenza del timeout.

## <a name="next-steps"></a>Passaggi successivi

* Vedere le informazioni di riferimento su SDK per il pacchetto [azureml-Pipelines-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) e il pacchetto [azureml-Pipelines-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) .

* Vedere l'elenco di [eccezioni e codici di errore della finestra di progettazione](algorithm-module-reference/designer-error-codes.md).
