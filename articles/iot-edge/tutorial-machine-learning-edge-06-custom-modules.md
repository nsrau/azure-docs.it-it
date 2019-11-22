---
title: 'Esercitazione: Creare e distribuire moduli personalizzati - Machine Learning in Azure IoT Edge'
description: "Esercitazione: Creare e distribuire moduli IoT Edge che elaborano i dati di dispositivi foglia tramite un modello di Machine Learning e quindi inviano le informazioni all'hub IoT."
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7bfe620510d5ff88a20c518be1f4dd1fb422daa2
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74106554"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>Esercitazione: Creare e distribuire moduli IoT Edge personalizzati

> [!NOTE]
> Questo articolo fa parte di una serie di documenti relativi a un'esercitazione sull'uso di Azure Machine Learning in IoT Edge. Se l'articolo è stato aperto direttamente, è consigliabile iniziare con il [primo articolo](tutorial-machine-learning-edge-01-intro.md) della serie per risultati ottimali.

In questo articolo vengono creati tre moduli IoT Edge che ricevono messaggi da dispositivi foglia, elaborano i dati tramite un modello di Machine Learning e quindi inoltrano le informazioni all'hub IoT.

L'hub di IoT Edge facilita la comunicazione tra moduli. L'uso dell'hub IoT Edge come broker di messaggi mantiene i moduli indipendenti gli uni dagli altri. Per i moduli è sufficiente specificare gli input su cui accettano i messaggi e gli output su cui scrivono i messaggi.

Il dispositivo IoT Edge dovrà eseguire quattro operazioni:

* Ricevere dati da dispositivi foglia
* Stimare la vita utile rimanente del dispositivo che ha inviato i dati
* Inviare un messaggio all'hub IoT contenente solo la vita utile rimanente del dispositivo (questa funzione può essere modificata per inviare i dati solo se la vita utile rimanente scende al di sotto di un certo livello)
* Salvare i dati del dispositivo foglia in un file locale nel dispositivo IoT Edge. Questo file di dati viene caricato periodicamente nell'hub IoT tramite caricamento file per affinare il training del modello di Machine Learning. L'uso del caricamento file invece dello streaming costante di messaggi è più conveniente.

Per eseguire queste attività, vengono usati tre moduli personalizzati:

* **Classificatore vita utile rimanente:** il modulo turboFanRulClassifier creato nell'articolo [Eseguire il training e la distribuzione di un modello di Machine Learning di Azure](tutorial-machine-learning-edge-04-train-model.md) è un modulo di Machine Learning standard, che espone un input denominato "amlInput" e un output denominato "amlOutput". L'input "amlInput" dovrà essere esattamente uguale all'input inviato al servizio Web basato su Istanze di Azure Container. Analogamente, "amlOutput" restituisce gli stessi dati del servizio Web.

* **Writer Avro:** questo modulo riceve i messaggi nell'input "avroModuleInput" e li rende persistenti in formato Avro su disco per il successivo caricamento nell'hub IoT.

* **Modulo Router:** questo modulo riceve i messaggi dai dispositivi foglia downstream, quindi li formatta e li invia al classificatore. Riceve quindi i messaggi dal classificatore e li inoltra al modulo writer Avro. Infine, il modulo invia solo la previsione della vita utile rimanente all'hub IoT.

  * Input:
    * **deviceInput**: riceve i messaggi dai dispositivi foglia
    * **rulInput:** riceve i messaggi da "amlOutput"

  * Output:
    * **classify:** invia i messaggi a "amlInput"
    * **writeAvro:** invia i messaggi a "avroModuleInput"
    * **toIotHub:** invia i messaggi a $upstream, che li passa all'hub IoT connesso

Il diagramma seguente illustra i moduli, gli input, gli output e le route dell'hub IoT Edge per la soluzione completa:

![Diagramma dell'architettura dei moduli con tre dispositivi IoT Edge](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

I passaggi di questo articolo vengono in genere eseguiti da sviluppatori cloud.

## <a name="create-a-new-iot-edge-solution"></a>Creare una nuova soluzione IoT Edge

Durante l'esecuzione del secondo dei due notebook di Azure, è stata creata e pubblicata un'immagine del contenitore contenente il modello di vita utile rimanente. Azure Machine Learning, come parte del processo di creazione dell'immagine, ha creato un pacchetto di tale modello in modo che l'immagine sia distribuibile come modulo Azure IoT Edge. In questo passaggio verrà creata una soluzione Azure IoT Edge usando il modulo "Azure Machine Learning" e puntandolo all'immagine pubblicata con Azure Notebooks.

1. Aprire una sessione Desktop remoto nel computer di sviluppo.

2. Aprire la cartella **C:\\source\\IoTEdgeAndMlSample** in Visual Studio Code.

3. Fare clic con il pulsante destro del mouse in uno spazio vuoto del pannello di esplorazione e scegliere **New IoT Edge Solution** (Nuova soluzione IoT Edge).

    ![Creare una nuova soluzione IoT Edge](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

4. Accettare il nome predefinito **EdgeSolution** per la soluzione.

5. Scegliere **Azure Machine Learning** come modello del modulo.

6. Assegnare al modulo il nome **turbofanRulClassifier**.

7. Scegliere l'area di lavoro di Machine Learning.

8. Selezionare l'immagine creata durante l'esecuzione del notebook di Azure.

9. Esaminare la soluzione. Sono stati creati i file seguenti:

   * **deployment.template.json:** questo file contiene la definizione di ogni modulo della soluzione. Ecco le tre sezioni a cui prestare attenzione:

     * **RegistryCredentials:** definisce il set di registri contenitori personalizzati usati nella soluzione. Al momento, dovrebbe contenere il registro dell'area di lavoro di Machine Learning, in cui è stata archiviata l'immagine di Azure Machine Learning. È possibile avere un numero qualsiasi di registri contenitori, ma per semplicità verrà usato solo questo per tutti i moduli

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io"
         }
       }
       ```

     * **Modules:** questa sezione contiene il set di moduli definiti dall'utente usati con questa soluzione. Come si può notare, questa sezione attualmente contiene due moduli: SimulatedTemperatureSensor e turbofanRulClassifier. Il modulo SimulatedTemperatureSensor è stato installato dal modello di Visual Studio Code, ma non è necessario per questa soluzione. È possibile eliminarne la definizione dalla sezione modules. La definizione del modulo turbofanRulClassifier punta all'immagine del registro contenitori. Gli altri moduli aggiunti alla soluzione verranno visualizzati in questa sezione.

       ```json
       "modules": {
         "SimulatedTemperatureSensor": {
           "version": "1.0",
           "type": "docker",
           "status": "running",
           "restartPolicy": "always",
           "settings": {
             "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
             "createOptions": {}
           }
         },
         "turbofanRulClassifier": {
           "version": "1.0",
           "type": "docker",
           "status": "running",
           "restartPolicy": "always",
           "settings": {
             "image": "<your registry>.azurecr.io/edgemlsample:1",
             "createOptions": {}
           }
         }
       }
       ```

     * **Routes:** le route verranno usate spesso in questa esercitazione. Definiscono il modo in cui i moduli comunicano tra loro. Le due route definite dal modello non corrispondono al routing necessario. La prima route invia tutti i dati di qualsiasi output del classificatore all'hub IoT ($upstream). L'altra è per SimulatedTemperatureSensor, che è stato appena eliminato. Eliminare le due route predefinite.

       ```json
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
             "turbofanRulClassifierToIoTHub": "FROM /messages/modules/turbofanRulClassifier/outputs/\* INTO $upstream",
             "sensorToturbofanRulClassifier": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\\"/modules/turbofanRulClassifier/inputs/input1\\")"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       }
       ```

   * **deployment.debug.template.json:** questo file è la versione di debug del file deployment.template.json. È necessario riflettere in questo file tutte le modifiche apportate nel file deployment.template.json.

   * **.env:** in questo file sarà necessario specificare il nome utente e la password per l'accesso al registro.

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

10. Fare clic con il pulsante destro del mouse sul file deployment.template.json nella finestra di esplorazione di Visual Studio Code e scegliere **Build IoT Edge Solution** (Crea soluzione IoT Edge).

11. Si noti che questo comando crea una cartella config con un file deployment.amd64.json. Questo file è il modello di distribuzione effettivo per la soluzione.

## <a name="add-router-module"></a>Aggiungere il modulo Router

A questo punto, aggiungere il modulo Router alla soluzione. Il modulo Router gestisce diverse operazioni della soluzione:

* **Ricezione di messaggi inviati dai dispositivi foglia:** quando i messaggi dei dispositivi downstream arrivano nel dispositivo IoT Edge, il modulo Router li riceve e inizia a orchestrarne il routing.
* **Invio di messaggi al modulo Classificatore vita utile rimanente:** quando viene ricevuto un nuovo messaggio inviato da un dispositivo downstream, il modulo Router lo trasforma nel formato previsto dal modulo Classificatore vita utile rimanente. Il modulo Router invia il messaggio al classificatore per la stima della vita utile rimanente. Dopo aver effettuato la stima, il classificatore rinvia il messaggio al modulo Router.
* **Invio di messaggi sulla vita utile rimanente all'hub IoT:** quando il modulo Router riceve i messaggi dal classificatore, li trasforma in modo che contengano solo le informazioni essenziali, ossia ID dispositivo e vita utile rimanente, quindi invia la versione abbreviata all'hub IoT. Un ulteriore perfezionamento, che non viene effettuato in questo esempio, prevede l'invio di messaggi all'hub IoT solo quando la stima della vita utile rimanente scende al di sotto di una soglia, ad esempio meno di 100 cicli. Con l'applicazione di questo filtro, si ridurrebbe la quantità di messaggi e il costo dell'hub IoT.
* **Invio del messaggio al modulo Writer Avro:** per preservare tutti i dati inviati dal dispositivo downstream, il modulo Router invia l'intero messaggio ricevuto dal classificatore al modulo Writer Avro, che rende persistenti i dati e li carica tramite caricamento file dell'hub IoT.

> [!NOTE]
> La descrizione delle funzionalità dei moduli potrebbe far pensare che l'elaborazione sia sequenziale, mentre il flusso è basato su messaggi/eventi. Ecco perché è necessario un modulo di orchestrazione come Router.

### <a name="create-module-and-copy-files"></a>Creare il modulo e copiare i file

1. Fare clic con il pulsante destro del mouse sulla cartella modules in Visual Studio Code e scegliere **Aggiungi modulo IoT Edge**.

2. Scegliere **C# module** (Modulo C#).

3. Assegnare al modulo il nome **turbofanRouter**.

4. Quando viene chiesto di specificare il repository di immagini Docker, usare il registro dell'area di lavoro di Machine Learning, disponibile nel nodo registryCredentials del file *deployment.template.json*. Questo valore corrisponde all'indirizzo completo del registro, ad esempio **\<registro\>.azurecr.io/turbofanrouter**.

    > [!NOTE]
    > In questo articolo viene usato il Registro Azure Container creato dall'area di lavoro di Machine Learning, che è stata usata per il training e la distribuzione del classificatore. Questa scelta viene fatta esclusivamente per praticità. Sarebbe stato possibile creare un nuovo registro contenitori in cui pubblicare i moduli.

5. Aprire una nuova finestra del terminale in Visual Studio Code (**Visualizza** > **Terminale**) e copiare i file dalla directory modules.

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

6. Quando viene chiesto se sovrascrivere program.cs, premere `y` e quindi `Enter`.

### <a name="build-router-module"></a>Creare il modulo Router

1. In Visual Studio Code selezionare **Terminale** > **Configure Default Build Task** (Configura attività di compilazione predefinita).

2. Fare clic su **Create tasks.json file from template** (Crea file tasks.json da modello).

3. Fare clic su **.NET Core**.

4. Quando il file tasks.json si apre, sostituirne il contenuto con:

    ```json
    {
      // See https://go.microsoft.com/fwlink/?LinkId=733558
      // for the documentation about the tasks.json format
      "version": "2.0.0",
      "tasks": [
        {
          "label": "build",
          "command": "dotnet",
          "type": "shell",
          "group": {
            "kind": "build",
            "isDefault": true
          },
          "args": [
            "build",
            "${workspaceFolder}/modules/turbofanRouter"
          ],
          "presentation": {
            "reveal": "always"
          },
          "problemMatcher": "$msCompile"
        }
      ]
    }
    ```

5. Salvare e chiudere il file tasks.json.

6. Eseguire la compilazione premendo `Ctrl + Shift + B` o selezionando **Terminale** > **Esegui attività di compilazione**.

### <a name="set-up-module-routes"></a>Configurare le route del modulo

Come accennato prima, il runtime IoT Edge usa route configurate nel file *deployment.template.json* per gestire la comunicazione tra moduli ad accoppiamento debole. In questa sezione verrà approfondita la procedura di configurazione delle route per il modulo turbofanRouter. Verranno prima descritte le route di input per poi passare agli output.

#### <a name="inputs"></a>Input

1. Nel metodo Init() del file Program.cs registrare due callback per il modulo:

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. Il primo callback resta in ascolto dei messaggi inviati al sink **deviceInput**. Come si può notare nel diagramma precedente, i messaggi provenienti da qualsiasi dispositivo figlio dovranno essere instradati in questo input. Nel file *deployment.template.json* aggiungere una route che indica all'hub Edge di instradare qualsiasi messaggio ricevuto dal dispositivo IoT Edge e non inviato da un modulo IoT Edge nell'input denominato "deviceInput" del modulo turbofanRouter:

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. Aggiungere quindi una route per i messaggi inviati dal modulo rulClassifier al modulo turbofanRouter:

   ```json
   "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>Output

Aggiungere altre quattro route al parametro di route $edgeHub per gestire gli output del modulo Router.

1. Program.cs definisce il metodo SendMessageToClassifier(), che usa il client del modulo per inviare un messaggio al classificatore di vita utile rimanente tramite la route:

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")"
   ```

2. SendRulMessageToIotHub() usa i client del modulo per inviare solo i dati sulla vita utile rimanente del dispositivo all'hub IoT tramite la route:

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. SendMessageToAvroWriter() usa il client del modulo per inviare il messaggio con i dati sulla vita utile rimanente aggiunti al modulo avroFileWriter.

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. HandleBadMessage() invia i messaggi non riusciti upstream all'hub IoT, in cui possono essere instradati in seguito.

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

Con tutte le route definite, il nodo "$edgeHub" sarà simile al codice JSON seguente:

```json
"$edgeHub": {
  "properties.desired": {
    "schemaVersion": "1.0",
    "routes": {
      "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")",
      "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amlOutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")",
      "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")",
      "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream",
      "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")",
      "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
    },
    "storeAndForwardConfiguration": {
      "timeToLiveSecs": 7200
    }
  }
}
```

> [!NOTE]
> Con l'aggiunta del modulo turbofanRouter è stata creata la route aggiuntiva seguente: `turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream`. Rimuovere questa route, lasciando nel file deployment.template.json solo quelle indicate sopra.

#### <a name="copy-routes-to-deploymentdebugtemplatejson"></a>Copiare le route nel file deployment.debug.template.json

Come passaggio finale, per mantenere i file sincronizzati, riflettere le modifiche apportate a deployment.template.json in deployment.debug.template.json.

## <a name="add-avro-writer-module"></a>Aggiungere il modulo Writer Avro

Il modulo Writer Avro esegue due operazioni nella soluzione, ossia l'archiviazione dei messaggi e il caricamento dei file.

* **Archiviazione dei messaggi**: quando il modulo Writer Avro riceve un messaggio, lo scrive nel file system locale in formato Avro. Viene usato un bind mount, che monta una directory, in questo caso /data/avrofiles, nel contenitore del modulo. In questo modo il modulo può scrivere in un percorso locale (/avrofiles) e rendere questi file direttamente accessibili dal dispositivo IoT Edge.

* **Caricamento dei file**: il modulo Writer Avro usa la funzionalità di caricamento file dell'hub IoT di Azure per caricare i file in un account di archiviazione di Azure. Dopo aver caricato i file, il modulo li elimina dal disco

### <a name="create-module-and-copy-files"></a>Creare il modulo e copiare i file

1. Nel riquadro comandi cercare e selezionare **Python: Select Interpreter** (Python: Seleziona interprete).

1. Scegliere l'interprete disponibile in C:\\Python37.

1. Aprire di nuovo il riquadro comandi, quindi cercare e selezionare **Terminal: Select Default Shell** (Terminale: Seleziona shell predefinita).

1. Quando richiesto, scegliere **Prompt dei comandi**.

1. Aprire una nuova shell del terminale, **Terminale** > **Nuovo terminale**.

1. Fare clic con il pulsante destro del mouse sulla cartella modules in Visual Studio Code e scegliere **Aggiungi modulo IoT Edge**.

1. Scegliere **Modulo Python**.

1. Assegnare al modulo il nome "avroFileWriter".

1. Quando viene chiesto di specificare il repository di immagini Docker, scegliere lo stesso registro usato per l'aggiunta del modulo Router.

1. Copiare i file del modulo di esempio nella soluzione.

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. Se viene chiesto se sovrascrivere main.py, digitare `y` e quindi premere `Enter`.

1. Si noti che nella soluzione sono stati aggiunti i file filemanager.py e schema.py e che il file main.py è stato aggiornato.

> [!NOTE]
> Quando si apre un file Python, è possibile che venga richiesto di installare pylint. Non è necessario installare il linter per completare questa esercitazione.

### <a name="bind-mount-for-data-files"></a>Bind mount per i file di dati

Come accennato nell'introduzione, il modulo writer si basa sulla presenza del bind mount per scrivere i file Avro nel file system del dispositivo.

#### <a name="add-directory-to-device"></a>Aggiungere la directory al dispositivo

1. Connettersi alla VM del dispositivo IoT Edge tramite SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Creare la directory in cui memorizzare i messaggi salvati dei dispositivi foglia.

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

3. Aggiornare le autorizzazioni per la directory per renderla scrivibile dal contenitore.

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

4. Verificare se ora utente, gruppo e proprietario hanno autorizzazioni in scrittura (w) per la directory.

   ```bash
   ls -la /data
   ```

   ![Autorizzazioni per la directory per avrofiles](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>Aggiungere la directory al modulo

Per aggiungere la directory al contenitore del modulo, i Dockerfile associati al modulo avroFileWriter verranno modificati. Al modulo sono associati tre Dockerfile: Dockerfile.amd64, Dockerfile.amd64.debug e Dockerfile.arm32v7. Questi file devono essere mantenuti sincronizzati nel caso si desideri eseguire il debug o la distribuzione in un dispositivo arm32. Per questo articolo, verrà usato solo Dockerfile.amd64.

1. Nel computer di sviluppo aprire il file **Dockerfile.amd64**.

2. Modificare il file in base all'esempio seguente:

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

   I comandi `mkdir` e `chown` indicano al processo di compilazione Docker di creare una directory di primo livello denominata /avrofiles nell'immagine e quindi di impostare moduleuser come relativo proprietario. È importante inserire questi comandi dopo aver aggiunto l'utente del modulo all'immagine con il comando `useradd` e prima che il contesto passi a moduleuser (moduleuser USER).

3. Apportare le modifiche corrispondenti nei file Dockerfile.amd64.debug e Dockerfile.arm32v7.

#### <a name="update-the-module-configuration"></a>Aggiornare la configurazione del modulo

Il passaggio finale nella creazione del bind consiste nell'aggiornare i file deployment.template.json e deployment.debug.template.json con le informazioni del bind.

1. Aprire deployment.template.json.

2. Modificare la definizione del modulo avroFileWriter aggiungendo il parametro `Binds` che punta la directory del contenitore /avrofiles alla directory locale nel dispositivo Edge. La definizione del modulo dovrà corrispondere a questo esempio:

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles"
           ]
         }
       }
     }
   }
   ```

3. Apportare le modifiche corrispondenti nel file deployment.debug.template.json.

### <a name="bind-mount-for-access-to-configyaml"></a>Bind mount per l'accesso a config.yaml

È necessario aggiungere un altro bind per il modulo writer. Questo bind fornisce al modulo l'accesso per leggere la stringa di connessione del file /etc/iotedge/config.yaml nel dispositivo IoT Edge. La stringa di connessione è necessaria per creare un oggetto IoTHubClient, in modo da poter chiamare il metodo upload\_blob\_async per caricare i file nell'hub IoT. I passaggi per aggiungere questo bind sono simili a quelli della sezione precedente.

#### <a name="update-directory-permission"></a>Aggiornare l'autorizzazione per la directory

1. Connettersi al dispositivo IoT Edge tramite SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Aggiungere l'autorizzazione in lettura per il file config.yaml.

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

3. Verificare se le autorizzazioni siano impostate correttamente.

   ```bash
   ls -la /etc/iotedge/
   ```

4. Assicurarsi che le autorizzazioni per config.yaml siano **-r--r--r--** .

#### <a name="add-directory-to-module"></a>Aggiungere la directory al modulo

1. Nel computer di sviluppo aprire il file **Dockerfile.amd64**.

2. Aggiungere un altro set di comandi `mkdir` e `chown` al file in questo modo:

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig

   USER moduleuser

   CMD "python3", "-u", "./main.py"]
   ```

3. Apportare le modifiche corrispondenti nei file Dockerfile.amd64.debug e Dockerfile.arm32v7.

#### <a name="update-the-module-configuration"></a>Aggiornare la configurazione del modulo

1. Aprire il file **deployment.template.json**.

2. Modificare la definizione del modulo avroFileWriter aggiungendo una seconda riga al parametro `Binds` che punta la directory del contenitore (/app/iotconfig) alla directory locale nel dispositivo (/etc/iotedge).

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles",
             "/etc/iotedge:/app/iotconfig"
           ]
         }
       }
     }
   }
   ```

3. Apportare le modifiche corrispondenti nel file deployment.debug.template.json.

## <a name="install-dependencies"></a>Installare le dipendenze

Il modulo writer accetta una dipendenza da due librerie Python, fastavro e PyYAML. È necessario installare le dipendenze nel computer di sviluppo e indicare al processo di compilazione Docker di installarle nell'immagine del modulo.

### <a name="pyyaml"></a>PyYAML

1. Nel computer di sviluppo aprire il file **requirements.txt** e aggiungere pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

2. Aprire il file **Dockerfile.amd64** e aggiungere un comando `pip install` per aggiornare setuptools.

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && \
       apt-get install -y --no-install-recommends libcurl4-openssl-dev python3-pip libboost-python1.58-dev libpython3-dev && \
       rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   RUN pip install -U pip setuptools
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

3. Apportare le modifiche corrispondenti nel file Dockerfile.amd64.debug. <!--may not be necessary. Add 'if needed'?-->

4. Installare pyyaml in locale aprendo un terminale in Visual Studio Code e digitando

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Fastavro

1. Nel file requirements.txt aggiungere fastavro dopo pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

2. Installare fastavro nel computer di sviluppo usando il terminale di Visual Studio Code.

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>Riconfigurare l'hub IoT

Introducendo il dispositivo e i moduli IoT Edge nel sistema, le aspettative sui dati che verranno inviati all'hub e sul relativo scopo sono cambiate. È necessario riconfigurare il routing nell'hub per gestire questa nuova realtà.

> [!NOTE]
> Riconfigurare l'hub prima di distribuire i moduli, perché alcune impostazioni dell'hub, in particolare quelle relative al caricamento file, devono essere configurate correttamente per la corretta esecuzione del modulo FileWriter

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>Configurare la route per i messaggi sulla vita utile rimanente nell'hub IoT

Con il router e il classificatore implementati, si dovrebbero ricevere messaggi regolari contenenti solo l'ID dispositivo e la relativa stima della vita utile rimanente. I dati sulla vita utile rimanente dovranno essere instradati alla rispettiva posizione di archiviazione, in cui è possibile monitorare lo stato dei dispositivi, creare report e attivare avvisi secondo necessità. Allo stesso tempo, i dati sui dispositivi che vengono ancora inviati direttamente da un dispositivo foglia non ancora collegato al dispositivo IoT Edge dovranno continuare a essere instradati alla posizione di archiviazione corrente.

#### <a name="create-a-rul-message-route"></a>Creare una route per i messaggi sulla vita utile rimanente

1. Nel portale di Azure passare all'hub IoT.

2. Nel riquadro di spostamento sinistro scegliere **Routing messaggi**.

3. Selezionare **Aggiungi**.

4. Assegnare alla route il nome **RulMessageRoute**.

5. Selezionare **Aggiungi** accanto al selettore **Endpoint** e scegliere **Archiviazione BLOB**.

6. Nel modulo **Aggiungi un endpoint di archiviazione** assegnare all'endpoint il nome **ruldata**.

7. Scegliere **Selezionare un contenitore**.

8. Scegliere l'account di archiviazione usato in tutta l'esercitazione, denominato **iotedgeandml\<suffisso univoco\>** .

9. Scegliere il contenitore **ruldata** e fare clic su **Seleziona**.

10. Fare clic su **Crea** per creare l'endpoint di archiviazione.

11. In **Query di routing** immettere la query seguente:

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

12. Espandere la sezione **Test** e quindi la sezione **Corpo del messaggio**. Sostituire il messaggio con questo esempio di messaggi previsti:

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

13. Selezionare **Testa route**. Se il test riesce, viene visualizzato il messaggio "Il messaggio corrisponde alla query".

14. Fare clic su **Save**.

#### <a name="update-turbofandevicetostorage-route"></a>Aggiornare la route turbofanDeviceToStorage

I nuovi dati di stima non dovranno essere instradati alla posizione di archiviazione precedente, quindi è necessario aggiornare la route per impedirlo.

1. Nella pagina **Routing messaggi** dell'hub IoT selezionare la scheda **Route**.

2. Selezionare **turbofanDeviceDataToStorage** o qualsiasi altro nome assegnato alla route iniziale dei dati dei dispositivi.

3. Aggiornare la query di routing in questo modo

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

4. Espandere la sezione **Test** e quindi la sezione **Corpo del messaggio**. Sostituire il messaggio con questo esempio di messaggi previsti:

   ```json
   {
     "Sensor13": 2387.96,
     "OperationalSetting1": -0.0008,
     "Sensor6": 21.61,
     "Sensor11": 47.2,
     "Sensor9": 9061.45,
     "Sensor4": 1397.86,
     "Sensor14": 8140.39,
     "Sensor18": 2388.0,
     "Sensor12": 522.87,
     "Sensor2": 642.42,
     "Sensor17": 391.0,
     "OperationalSetting3": 100.0,
     "Sensor1": 518.67,
     "OperationalSetting2": 0.0002,
     "Sensor20": 39.03,
     "DeviceId": 19.0,
     "Sensor5": 14.62,
     "PredictedRul": 212.00132402791962,
     "Sensor8": 2388.01,
     "Sensor16": 0.03,
     "CycleTime": 42.0,
     "Sensor21": 23.3188,
     "Sensor15": 8.3773,
     "Sensor3": 1580.09,
     "Sensor10": 1.3,
     "Sensor7": 554.57,
     "Sensor19": 100.0
   }
   ```

5. Selezionare **Testa route**. Se il test riesce, viene visualizzato il messaggio "Il messaggio corrisponde alla query".

6. Selezionare **Salva**.

### <a name="configure-file-upload"></a>Configurare il caricamento di file

Configurare la funzionalità di caricamento file dell'hub IoT in modo da consentire al modulo writer di caricare file nello spazio di archiviazione.

1. Nel riquadro di spostamento sinistro dell'hub IoT scegliere **Caricamento file**.

2. Selezionare **Contenitore di archiviazione di Azure**.

3. Selezionare l'account di archiviazione dall'elenco.

4. Selezionare il contenitore **uploadturbofanfiles** e fare clic su **Seleziona**.

5. Selezionare **Salva**. Al termine del salvataggio, nel portale viene visualizzata una notifica.

> [!Note]
> Per questa esercitazione la notifica sul caricamento non è attivata, ma per informazioni su come gestirla vedere [Ricevere la notifica di caricamento di un file](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification).

## <a name="build-publish-and-deploy-modules"></a>Compilare, pubblicare e distribuire i moduli

Dopo aver apportato le modifiche di configurazione, è possibile creare le immagini e pubblicarle nel Registro Azure Container. Per determinare quali moduli compilare, viene usato il file deployment.template.json. Le impostazioni di ogni modulo, tra cui la versione, si trovano nel file module.json nella cartella module. Per creare un'immagine, viene prima eseguita una compilazione Docker nei Dockerfile corrispondente alla configurazione corrente disponibile nel file module.json. Quindi l'immagine viene pubblicata nel registro dal file module.json con un tag di versione che corrisponde a quello di questo file. Infine, viene prodotto un manifesto della distribuzione specifico della configurazione, ad esempio deployment.amd64.json, che verrà distribuito nel dispositivo IoT Edge. Il dispositivo IoT Edge legge le informazioni del manifesto della distribuzione e, in base alle istruzioni, scarica i moduli, configura le route e imposta le proprietà desiderate. Questo metodo di distribuzione presenta due effetti collaterali che è necessario tenere presenti:

* **Ritardo della distribuzione:** poiché il runtime IoT Edge deve riconoscere la modifica apportata alle proprietà desiderate prima di avviare la riconfigurazione, può essere necessario del tempo dopo la distribuzione dei moduli prima che il runtime li prelevi e inizi ad aggiornare il dispositivo IoT Edge.

* **Versioni dei moduli:** se si pubblica una nuova versione del contenitore di un modulo nel registro contenitori usando gli stessi tag di versione del modulo precedente, il runtime non scaricherà la nuova versione del modulo. Confronta il tag di versione dell'immagine locale con quello dell'immagine desiderata del manifesto della distribuzione. Se le versioni corrispondono, il runtime non esegue alcuna azione. Pertanto, è importante incrementare la versione del modulo ogni volta che si vogliono distribuire nuove modifiche. Per incrementare la versione, cambiare la proprietà **version** nella proprietà **tag** del file module.json per il modulo da modificare. Quindi compilare e pubblicare il modulo.

    ```json
    {
      "$schema-version": "0.0.1",
      "description": "",
      "image": {
        "repository": "<your registry>.azurecr.io/avrofilewriter",
        "tag": {
          "version": "0.0.1",
          "platforms": {
            "amd64": "./Dockerfile.amd64",
            "amd64.debug": "./Dockerfile.amd64.debug",
            "arm32v7": "./Dockerfile.arm32v7"
          }
        },
        "buildOptions": []
      },
      "language": "python"
    }
    ```

### <a name="build-and-publish"></a>Compilare e pubblicare

1. Nella VM di sviluppo aprire una finestra del terminale di Visual Studio Code e accedere al registro contenitori.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. In Visual Studio Code fare clic con il pulsante destro del mouse sul file deployment.template.json e scegliere **Build and Push IoT Edge Solution** (Crea ed esegui il push della soluzione IoT Edge).

### <a name="view-modules-in-the-registry"></a>Visualizzare i moduli nel registro

Dopo aver completato la compilazione, è possibile usare il portale di Azure per esaminare i moduli pubblicati.

1. Nel portale di Azure passare all'area di lavoro di Machine Learning e fare clic sul collegamento ipertestuale **Registro**.

    ![Passare al registro dall'area di lavoro del servizio Machine Learning](media/tutorial-machine-learning-edge-06-custom-modules/follow-registry-link.png)

2. Nel riquadro di spostamento laterale del registro selezionare **Repository**.

3. Entrambi i moduli creati, **avrofilewriter** e **turbofanrouter**, vengono visualizzati come repository.

4. Selezionare **turbofanrouter** e notare che è stata pubblicata un'immagine contrassegnata dal tag 0.0.1-amd64.

   ![Visualizzare la prima versione con tag di turbofanrouter](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>Distribuire moduli in un dispositivo IoT Edge

Una volta creati e configurati, i moduli della soluzione verranno distribuiti nel dispositivo IoT Edge.

1. In Visual Studio Code fare clic con il pulsante destro del mouse sul file **deployment.amd64.json** nella cartella config.

2. Scegliere **Create Deployment for Single Device** (Crea la distribuzione per un unico dispositivo).

3. Scegliere il dispositivo IoT Edge, **aaTurboFanEdgeDevice**.

4. Aggiornare il pannello di dispositivi dell'hub IoT di Azure nella finestra di esplorazione di Visual Studio Code. Come si può notare, i tre nuovi moduli sono stati distribuiti ma non sono ancora in esecuzione.

5. Aggiornare di nuovo dopo alcuni minuti per vedere i moduli in esecuzione.

   ![Visualizzazione dei moduli in esecuzione in Visual Studio Code](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> Possono essere necessari diversi minuti prima che i moduli vengano avviati e rimangano stabilmente in esecuzione. Durante questo periodo, i moduli vengono avviati e arrestati mentre provano a stabilire una connessione con il modulo dell'hub IoT Edge.

## <a name="diagnosing-failures"></a>Diagnosi degli errori

In questa sezione vengono descritte alcune tecniche per capire quale errore si è verificato in uno o più moduli. Spesso un errore può essere individuato prima di tutto dallo stato in Visual Studio Code.

### <a name="identify-failed-modules"></a>Identificare i moduli in errore

* **Visual Studio Code:** esaminare il pannello dei dispositivi dell'hub IoT di Azure. Se la maggior parte dei moduli è in esecuzione ma uno si è arrestato, è necessario analizzare ulteriormente quest'ultimo modulo. Se tutti i moduli rimangono nello stato arrestato per un periodo prolungato di tempo, è possibile che si sia verificato un errore anche in questo caso.

* **Portale di Azure:** se si passa all'hub IoT nel portale e quindi si trova la pagina di dettagli del dispositivo (in IoT Edge passare al dispositivo) è possibile scoprire se un modulo ha segnalato un errore o non ha mai segnalato niente all'hub IoT.

### <a name="diagnosing-from-the-device"></a>Diagnosi dal servizio

Accedendo al dispositivo IoT Edge, è possibile trovare numerose informazioni sullo stato dei moduli. Il meccanismo principale da usare è costituito dai comandi Docker che consentono di esaminare i contenitori e le immagini nel dispositivo.

1. Elencare tutti i dispositivi in esecuzione. Si dovrebbe vedere un contenitore per ogni modulo con un nome che corrisponde al modulo. Inoltre, questo comando genera l'immagine esatta del contenitore, inclusa la versione, quindi è possibile verificare se corrisponde a quella prevista. È anche possibile elencare le immagini sostituendo "image" a "container" nel comando.

   ```bash
   sudo docker container ls
   ```

2. Ottenere i log per un contenitore. L'output di questo comando include tutti i contenuti scritti in StdErr e StdOut nel contenitore. Questo comando funziona per i contenitori che sono stati avviati e poi si sono arrestati per qualche motivo. È anche utile per capire cosa succede nei contenitori edgeAgent o edgeHub.

   ```bash
   sudo docker container logs <container name>
   ```

3. Esaminare un contenitore. Questo comando fornisce moltissime informazioni sull'immagine. I dati possono essere filtrati in base all'informazione da cercare. Se ad esempio si vuole verificare se i bind di avroFileWriter sono corretti, è possibile usare il comando:

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

4. Connettersi a un contenitore in esecuzione. Questo comando può risultare utile se si vuole esaminare il contenitore durante l'esecuzione:

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata creata una soluzione IoT Edge in Visual Studio Code con tre moduli, ossia un classificatore, un router e un writer/strumento di caricamento file. Sono state configurate le route per consentire ai moduli di comunicare tra loro nel dispositivo Edge, è stata modificata la configurazione di tale dispositivo e sono stati aggiornati i Dockerfile per installare le dipendenze e aggiungere bind mount ai contenitori dei moduli. In seguito è stata aggiornata la configurazione dell'hub IoT per instradare i messaggi in base al tipo e per gestire il caricamento dei file. Dopo aver implementato il tutto, i moduli sono stati distribuiti nel dispositivo IoT Edge, verificando se vengono eseguiti correttamente.

Altre informazioni sono disponibili nelle pagine seguenti:

* [Informazioni su come distribuire moduli e definire route in IoT Edge](module-composition.md)
* [Sintassi query per il routing dei messaggi di hub IoT](../iot-hub/iot-hub-devguide-routing-query-syntax.md)
* [Routing dei messaggi dell'hub IoT di Azure: ora disponibile il routing nel corpo del messaggio](https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/)
* [Caricare file con l'hub IoT](../iot-hub/iot-hub-devguide-file-upload.md)
* [Caricare file da un dispositivo al cloud con l'hub IoT](../iot-hub/iot-hub-python-python-file-upload.md)

Continuare con l'articolo successivo per iniziare a inviare dati e vedere come funziona la soluzione.

> [!div class="nextstepaction"]
> [Inviare dati tramite gateway trasparente](tutorial-machine-learning-edge-07-send-data-to-hub.md)
