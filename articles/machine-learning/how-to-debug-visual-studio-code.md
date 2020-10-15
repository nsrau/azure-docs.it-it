---
title: Debug interattivo con Visual Studio Code
titleSuffix: Azure Machine Learning
description: Esegui il debug interattivo di Azure Machine Learning codice, pipeline e distribuzioni usando Visual Studio Code
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 09/30/2020
ms.openlocfilehash: 374cc79b42d2dcaed0312c0ec205073906ce1fc5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91530675"
---
# <a name="interactive-debugging-with-visual-studio-code"></a>Debug interattivo con Visual Studio Code



Informazioni su come eseguire il debug interattivo di Azure Machine Learning esperimenti, pipeline e distribuzioni usando Visual Studio Code (VS Code) e [depugpy](https://github.com/microsoft/debugpy/).

## <a name="run-and-debug-experiments-locally"></a>Eseguire ed eseguire il debug di esperimenti localmente

Usare l'estensione Azure Machine Learning per convalidare, eseguire ed eseguire il debug degli esperimenti di Machine Learning prima di inviarli al cloud.

### <a name="prerequisites"></a>Prerequisiti

* Azure Machine Learning VS Code Extension (anteprima). Per ulteriori informazioni, vedere [set up Azure Machine Learning vs code Extension](tutorial-setup-vscode-extension.md).
* [Docker](https://www.docker.com/get-started)
  * Desktop Docker per Mac e Windows
  * Motore Docker per Linux.
* [Python 3](https://www.python.org/downloads/)

> [!NOTE]
> In Windows, assicurarsi di [configurare Docker per l'uso di contenitori Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

> [!TIP]
> Per Windows, sebbene non sia obbligatorio, è consigliabile [usare Docker con il sottosistema Windows per Linux (WSL) 2](https://docs.microsoft.com/windows/wsl/tutorials/wsl-containers#install-docker-desktop).

> [!IMPORTANT]
> Prima di eseguire l'esperimento localmente, assicurarsi che Docker sia in esecuzione.

### <a name="debug-experiment-locally"></a>Debug dell'esperimento localmente

1. In VS Code aprire la visualizzazione dell'estensione del Azure Machine Learning.
1. Espandere il nodo sottoscrizione che contiene l'area di lavoro. Se non ne è già presente uno, è possibile [creare un'area di lavoro Azure Machine Learning](how-to-manage-resources-vscode.md#create-a-workspace) usando l'estensione.
1. Espandere il nodo dell'area di lavoro.
1. Fare clic con il pulsante destro del mouse sul nodo **esperimenti** e selezionare **Crea esperimento**. Quando viene visualizzato il prompt, specificare un nome per l'esperimento.
1. Espandere il nodo **esperimenti** , fare clic con il pulsante destro del mouse sull'esperimento che si vuole eseguire e scegliere **Esegui esperimento**.
1. Dall'elenco di opzioni per eseguire l'esperimento, selezionare **localmente**.
1. **Primo utilizzo solo in Windows**. Quando viene richiesto di consentire la condivisione file, selezionare **Sì**. Quando si Abilita la condivisione file, consente a Docker di montare la directory contenente lo script nel contenitore. Consente inoltre a Docker di archiviare i log e gli output dall'esecuzione in una directory temporanea nel sistema.
1. Selezionare **Sì** per eseguire il debug dell'esperimento. In caso contrario, selezionare **No**. Se si seleziona No, l'esperimento viene eseguito localmente senza essere collegato al debugger.
1. Selezionare **Crea una nuova configurazione di esecuzione** per creare la configurazione di esecuzione. La configurazione di esecuzione definisce lo script che si desidera eseguire, le dipendenze e i set di impostazioni utilizzati. In alternativa, se si dispone già di un oggetto, selezionarlo dall'elenco a discesa.
    1. Scegliere l'ambiente. È possibile scegliere uno qualsiasi dei [Azure Machine Learning curati](resource-curated-environments.md) o crearne uno personalizzato.
    1. Consente di specificare il nome dello script che si desidera eseguire. Il percorso è relativo alla directory aperta in VS Code.
    1. Scegliere se si desidera utilizzare un set di dati Azure Machine Learning. È possibile creare [set di impostazioni di Azure Machine Learning](how-to-manage-resources-vscode.md#create-dataset) usando l'estensione.
    1. Debugpy è necessario per consentire a di aggiungere il debugger al contenitore che esegue l'esperimento. Per aggiungere debugpy come dipendenza, selezionare **Aggiungi debugpy**. In caso contrario, selezionare **Ignora**. Se non si aggiunge debugpy come dipendenza, viene eseguito l'esperimento senza connettersi al debugger.
    1. Un file di configurazione contenente le impostazioni di configurazione di esecuzione viene aperto nell'editor. Se si è soddisfatti delle impostazioni, selezionare **Invia esperimento**. In alternativa, aprire il riquadro comandi (**visualizzare > riquadro comandi**) dalla barra dei menu e immettere il `Azure ML: Submit experiment` comando nella casella di testo.
1. Una volta inviato l'esperimento, viene creata un'immagine Docker contenente lo script e le configurazioni specificate nella configurazione di esecuzione.

    Quando inizia il processo di compilazione dell'immagine Docker, il contenuto del `60_control_log.txt` flusso di file nella console di output viene vs code.

    > [!NOTE]
    > La prima volta che l'immagine Docker viene creata può richiedere diversi minuti.

1. Una volta compilata l'immagine, viene visualizzato un prompt per avviare il debugger. Impostare i punti di interruzione nello script e selezionare **Avvia debugger** quando si è pronti per avviare il debug. In questo modo, il debugger VS Code viene collegato al contenitore che esegue l'esperimento. In alternativa, nell'estensione Azure Machine Learning passare il puntatore del mouse sul nodo per l'esecuzione corrente e selezionare l'icona Riproduci per avviare il debugger.

    > [!IMPORTANT]
    > Non è possibile avere più sessioni di debug per un singolo esperimento. È tuttavia possibile eseguire il debug di due o più esperimenti usando più istanze di VS Code.

A questo punto, si dovrebbe essere in grado di eseguire il debug e il debug del codice usando VS Code.

Se in qualsiasi momento si vuole annullare l'esecuzione, fare clic con il pulsante destro del mouse sul nodo Run e scegliere **Annulla esecuzione**.

Analogamente alle esecuzioni di esperimento remote, è possibile espandere il nodo Run per esaminare i log e gli output.

> [!TIP]
> Le immagini Docker che usano le stesse dipendenze definite nell'ambiente vengono riutilizzate tra le esecuzioni. Tuttavia, se si esegue un esperimento utilizzando un ambiente nuovo o diverso, viene creata una nuova immagine. Poiché queste immagini vengono salvate nella risorsa di archiviazione locale, è consigliabile rimuovere le immagini Docker obsolete o inutilizzate. Per rimuovere le immagini dal sistema, usare l'interfaccia della riga di comando di [Docker](https://docs.docker.com/engine/reference/commandline/rmi/) o l' [estensione Docker vs code](https://code.visualstudio.com/docs/containers/overview).

## <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Eseguire il debug e risolvere i problemi delle pipeline di Machine Learning

In alcuni casi, potrebbe essere necessario eseguire il debug interattivo del codice Python usato nella pipeline ML. Utilizzando VS Code e debugpy, è possibile connettersi al codice durante l'esecuzione nell'ambiente di training.

### <a name="prerequisites"></a>Prerequisiti

* Un' __area di lavoro Azure Machine Learning__ configurata per l'uso di una __rete virtuale di Azure__.
* Una __pipeline Azure Machine Learning__ che usa script Python come parte dei passaggi della pipeline. Ad esempio, un PythonScriptStep.
* Un cluster Azure Machine Learning calcolo, che si trova __nella rete virtuale__ e viene __usato dalla pipeline per il training__.
* Un __ambiente di sviluppo__ che si trova __nella rete virtuale__. L'ambiente di sviluppo può essere uno dei seguenti:

  * Una macchina virtuale di Azure nella rete virtuale
  * Un'istanza di calcolo della VM del notebook nella rete virtuale
  * Un computer client con connettività di rete privata alla rete virtuale, tramite VPN o tramite ExpressRoute.

Per altre informazioni sull'uso di una rete virtuale di Azure con Azure Machine Learning, vedere [Panoramica dell'isolamento e della privacy della rete virtuale](how-to-network-security-overview.md).

> [!TIP]
> Sebbene sia possibile utilizzare Azure Machine Learning risorse che non si trovano dietro una rete virtuale, è consigliabile utilizzare una rete virtuale.

### <a name="how-it-works"></a>Funzionamento

I passaggi della pipeline di ML eseguono script Python. Questi script vengono modificati per eseguire le azioni seguenti:

1. Registrare l'indirizzo IP dell'host in cui sono in esecuzione. Per connettere il debugger allo script, usare l'indirizzo IP.

2. Avviare il componente di debug debugpy e attendere la connessione di un debugger.

3. Dall'ambiente di sviluppo, è possibile monitorare i log creati dal processo di training per trovare l'indirizzo IP in cui lo script è in esecuzione.

4. Si indica VS Code indirizzo IP per la connessione del debugger a utilizzando un `launch.json` file.

5. Il debugger viene collegato ed è possibile scorrere lo script in modo interattivo.

### <a name="configure-python-scripts"></a>Configurare gli script Python

Per abilitare il debug, apportare le modifiche seguenti agli script Python usati dai passaggi della pipeline ML:

1. Aggiungere le istruzioni Import seguenti:

    ```python
    import argparse
    import os
    import debugpy
    import socket
    from azureml.core import Run
    ```

1. Aggiungere gli argomenti seguenti. Questi argomenti consentono di abilitare il debugger in base alle esigenze e di impostare il timeout per il fissaggio del debugger:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                        default=300,
                        help=f'Defines how much time the AML compute target '
                        f'will await a connection from a debugger client (VSCODE).')
    parser.add_argument('--remote_debug_client_ip', type=str,
                        help=f'Defines IP Address of VS Code client')
    parser.add_argument('--remote_debug_port', type=int,
                        default=5678,
                        help=f'Defines Port of VS Code client')
    ```

1. Aggiungere le istruzioni seguenti. Queste istruzioni caricano il contesto di esecuzione corrente in modo che sia possibile registrare l'indirizzo IP del nodo in cui è in esecuzione il codice:

    ```python
    global run
    run = Run.get_context()
    ```

1. Aggiungere un' `if` istruzione che avvia debugpy e attende la connessione di un debugger. Se nessun debugger si connette prima del timeout, lo script continua come di consueto. Assicurarsi di sostituire i `HOST` valori e `PORT` è la `listen` funzione con il proprio.

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        try:
            ip = args.remote_debug_client_ip
        except:
            print("Need to supply IP address for VS Code client")
        print(f'ip_address: {ip}')
        debugpy.listen(address=(ip, args.remote_debug_port))
        # Wait for the timeout for debugger to attach
        debugpy.wait_for_client()
        print(f'Debugger attached = {debugpy.is_client_connected()}')
    ```

Nell'esempio Python seguente viene illustrato un semplice `train.py` file che consente il debug:

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import debugpy
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
parser.add_argument('--remote_debug_client_ip', type=str,
                    help=f'Defines IP Address of VS Code client')
parser.add_argument('--remote_debug_port', type=int,
                    default=5678,
                    help=f'Defines Port of VS Code client')

# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    # ip = socket.gethostbyname(socket.gethostname())
    try:
        ip = args.remote_debug_client_ip
    except:
        print("Need to supply IP address for VS Code client")
    print(f'ip_address: {ip}')
    debugpy.listen(address=(ip, args.remote_debug_port))
    # Wait for the timeout for debugger to attach
    debugpy.wait_for_client()
    print(f'Debugger attached = {debugpy.is_client_connected()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>Configurare la pipeline ML

Per fornire i pacchetti Python necessari per avviare debugpy e ottenere il contesto di esecuzione, creare un ambiente e impostarlo `pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>']` . Modificare la versione dell'SDK in modo che corrisponda a quella usata. Il frammento di codice seguente illustra come creare un ambiente:

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
                                                                           pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>'])
```

Nella sezione [Configure Python Scripts](#configure-python-scripts) sono stati aggiunti nuovi argomenti agli script usati dai passaggi della pipeline ml. Nel frammento di codice seguente viene illustrato come utilizzare questi argomenti per abilitare il debug per il componente e impostare un timeout. Viene inoltre illustrato come utilizzare l'ambiente creato in precedenza impostando `runconfig=run_config` :

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300,'--remote_debug_client_ip','<VS-CODE-CLIENT-IP>','--remote_debug_port',5678],
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

1. Per installare debugpy nell'ambiente di sviluppo VS Code, usare il comando seguente:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    Per ulteriori informazioni sull'utilizzo di debugpy con VS Code, vedere [Remote Debugging](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection).

1. Per configurare VS Code per comunicare con l'Azure Machine Learning calcolo che esegue il debugger, creare una nuova configurazione di debug:

    1. Da VS Code selezionare il menu __Debug__, quindi selezionare __Apri configurazioni__. Viene aperto un file denominato __launch.json__.

    1. Nel __launch.jssu__ file trovare la riga che contiene `"configurations": [` e inserire il testo seguente dopo di esso. Modificare la `"host": "<IP-ADDRESS>"` voce nell'indirizzo IP restituito nei log della sezione precedente. Modificare la `"localRoot": "${workspaceFolder}/code/step"` voce in una directory locale che contiene una copia dello script di cui è in corso il debug:

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "<IP-ADDRESS>",
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
        > Se nella sezione Configurazioni sono già presenti altre voci, aggiungere una virgola (,) dopo il codice inserito.

        > [!TIP]
        > La procedura consigliata, in particolare per le pipeline, consiste nel memorizzare le risorse per gli script in directory separate, in modo che il codice sia pertinente solo per ogni passaggio. In questo esempio il `localRoot` valore di esempio fa riferimento a `/code/step1` .
        >
        > Se si esegue il debug di più script, in directory diverse creare una sezione di configurazione separata per ogni script.

    1. Salvare il file __launch.json__.

### <a name="connect-the-debugger"></a>Connettere il debugger

1. Aprire VS Code e aprire una copia locale dello script.
2. Impostare i punti di interruzione in cui si desidera che lo script venga arrestato una volta collegato.
3. Mentre il processo figlio esegue lo script e `Timeout for debug connection` viene visualizzato nei log, utilizzare il tasto F5 o selezionare __debug__. Quando richiesto, selezionare il __Azure Machine Learning calcolo: configurazione di debug remoto__ . È anche possibile selezionare l'icona debug dalla barra laterale, la voce __Azure Machine Learning: Remote Debug__ dal menu a discesa debug e quindi usare la freccia verde per allineare il debugger.

    A questo punto, VS Code si connette a debugpy sul nodo di calcolo e si arresta in corrispondenza del punto di interruzione impostato in precedenza. È ora possibile scorrere il codice durante l'esecuzione, visualizzare le variabili e così via.

    > [!NOTE]
    > Se il log Visualizza una voce che informa `Debugger attached = False` , il timeout è scaduto e lo script continua senza il debugger. Inviare di nuovo la pipeline e connettere il debugger dopo il `Timeout for debug connection` messaggio e prima della scadenza del timeout.

## <a name="debug-and-troubleshoot-deployments"></a>Debug e risoluzione dei problemi delle distribuzioni

In alcuni casi, potrebbe essere necessario eseguire il debug interattivo del codice Python contenuto nella distribuzione del modello. Ad esempio, se lo script di immissione ha esito negativo e il motivo non può essere determinato da una registrazione aggiuntiva. Usando VS Code e debugpy, è possibile connettersi al codice in esecuzione all'interno del contenitore docker.

> [!IMPORTANT]
> Questo metodo di debug non funziona quando si usano `Model.deploy()` e `LocalWebservice.deploy_configuration` per distribuire un modello localmente. Al contrario, è necessario creare un'immagine usando il metodo [Model.package()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=truepackage-workspace--models--inference-config-none--generate-dockerfile-false-).

Per le distribuzioni di servizi Web locali è necessaria un'installazione Docker funzionante nel sistema locale. Per altre informazioni sull'uso di Docker, vedere la [Documentazione di Docker](https://docs.docker.com/). Si noti che quando si lavora con le istanze di calcolo, Docker è già installato.

### <a name="configure-development-environment"></a>Configurare l'ambiente di sviluppo

1. Per installare debugpy nell'ambiente di sviluppo VS Code locale, usare il comando seguente:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    Per ulteriori informazioni sull'utilizzo di debugpy con VS Code, vedere [Remote Debugging](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection).

1. Per configurare VS Code per la comunicazione con l'immagine Docker, creare una nuova configurazione di debug:

    1. Da VS Code selezionare il menu __Debug__, quindi selezionare __Apri configurazioni__. Viene aperto un file denominato __launch.json__.

    1. Nel file __launch.json__ trovare la riga che contiene `"configurations": [` e inserire il testo seguente dopo di essa:

        ```json
        {
            "name": "Azure Machine Learning Deployment: Docker Debug",
            "type": "python",
            "request": "attach",
            "connect": {
                "port": 5678,
                "host": "0.0.0.0",
            },
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Se nella sezione Configurazioni sono già presenti altre voci, aggiungere una virgola (,) dopo il codice inserito.

        Questa sezione si connette al contenitore Docker usando la porta 5678.

    1. Salvare il file __launch.json__.

### <a name="create-an-image-that-includes-debugpy"></a>Creare un'immagine che includa debugpy

1. Modificare l'ambiente conda per la distribuzione in modo che includa debugpy. L'esempio seguente illustra come aggiungerlo usando il parametro `pip_packages`:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.83', 'debugpy'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Per avviare debugpy e attendere la connessione all'avvio del servizio, aggiungere quanto segue all'inizio del `score.py` file:

    ```python
    import debugpy
    # Allows other computers to attach to debugpy on this IP address and port.
    debugpy.listen(('0.0.0.0', 5678))
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    debugpy.wait_for_client()
    print("Debugger attached...")
    ```

1. Creare un'immagine in base alla definizione dell'ambiente ed eseguire il pull dell'immagine nel Registro di sistema locale. 

    > [!NOTE]
    > In questo esempio si presuppone che `ws` punti all'area di lavoro di Azure Machine Learning e che `model` sia il modello distribuito. Il file `myenv.yml` contiene le dipendenze Conda create nel passaggio 1.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    Una volta che l'immagine è stata creata e scaricata, il percorso dell'immagine (inclusi repository, nome e tag, che in questo caso è anche il codice hash) viene visualizzato in un messaggio simile al seguente:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Per semplificare le operazioni con l'immagine, usare il comando seguente per aggiungere un tag. Sostituire `myimagepath` con il valore della posizione del passaggio precedente.

    ```bash
    docker tag myimagepath debug:1
    ```

    Per i passaggi rimanenti, è possibile fare riferimento all'immagine locale come `debug:1` anziché al valore del percorso dell'immagine completa.

### <a name="debug-the-service"></a>Eseguire il debug del servizio

> [!TIP]
> Se si imposta un timeout per la connessione debugpy nel `score.py` file, è necessario connettere vs code alla sessione di debug prima della scadenza del timeout. Avviare VS Code, aprire la copia locale di `score.py`, impostare un punto di interruzione e fare in modo che sia pronto prima di usare i passaggi descritti in questa sezione.
>
> Per altre informazioni sul debug e sull'impostazione di punti di interruzione, vedere [Debug](https://code.visualstudio.com/Docs/editor/debugging).

1. Per avviare un contenitore Docker usando l'immagine, usare il comando seguente:

    ```bash
    docker run -it --name debug -p 8000:5001 -p 5678:5678 -v <my_path_to_score.py>:/var/azureml-apps/score.py debug:1 /bin/bash
    ```

    In questo modo il `score.py` locale viene collegato a quello nel contenitore. Pertanto, tutte le modifiche apportate nell'editor vengono riflesse automaticamente nel contenitore.

1. All'interno del contenitore eseguire il comando seguente nella shell

    ```bash
    runsvdir /var/runit
    ```

1. Per aggiungere VS Code a debugpy all'interno del contenitore, aprire VS Code e usare il tasto F5 oppure selezionare __debug__. Quando richiesto, selezionare la configurazione della __distribuzione Azure Machine Learning: Docker debug__ . È anche possibile selezionare l'icona debug dalla barra laterale, la voce __Azure Machine Learning Deployment: Docker debug__ dal menu a discesa debug e quindi usare la freccia verde per collegare il debugger.

    ![Icona Debug, pulsante Avvia debug e selettore della configurazione](./media/how-to-troubleshoot-deployment/start-debugging.png)

A questo punto, VS Code si connette a debugpy all'interno del contenitore Docker e si arresta in corrispondenza del punto di interruzione impostato in precedenza. È ora possibile scorrere il codice durante l'esecuzione, visualizzare le variabili e così via.

Per altre informazioni sull'uso di VS Code per eseguire il debug di Python, vedere [Eseguire il debug del codice Python](https://code.visualstudio.com/docs/python/debugging).

### <a name="stop-the-container"></a>Arrestare il contenitore

Per arrestare il contenitore, usare il comando seguente:

```bash
docker stop debug
```

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato configurato VS Code remoto, è possibile usare un'istanza di calcolo come calcolo remoto da VS Code per eseguire il debug interattivo del codice. 

In [Esercitazione: Eseguire il training del primo modello di Machine Learning](tutorial-1st-experiment-sdk-train.md) viene illustrato come usare un'istanza di calcolo con un notebook integrato.
