---
title: 'Esercitazione: Creare e distribuire moduli personalizzati - Machine Learning in Azure IoT Edge'
description: Questa esercitazione mostra come creare e distribuire moduli IoT Edge che elaborano i dati da dispositivi foglia tramite un modello di Machine Learning e quindi inviano le informazioni dettagliate all'hub IoT.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: 199da0586a061bccdf8a6ff8a1f53df2f703512f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94959442"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>Esercitazione: Creare e distribuire moduli IoT Edge personalizzati

In questo articolo vengono creati tre moduli IoT Edge che ricevono messaggi da dispositivi IoT foglia, elaborano i dati tramite un modello di Machine Learning e quindi inoltrano le informazioni all'hub IoT.

L'hub di IoT Edge facilita la comunicazione tra moduli. L'uso dell'hub IoT Edge come broker di messaggi mantiene i moduli indipendenti gli uni dagli altri. Per i moduli è sufficiente specificare gli input su cui accettano i messaggi e gli output su cui scrivono i messaggi.

Il dispositivo IoT Edge dovrà eseguire quattro operazioni:

* Ricevere dati da dispositivi foglia.
* Prevedere la vita utile rimanente del dispositivo che ha inviato i dati.
* Inviare un messaggio con la vita utile rimanente per il dispositivo all'hub IoT. Questa funzione può essere modificata per inviare dati solo se la vita utile rimanente scende al di sotto di un livello specificato.
* Salvare i dati del dispositivo foglia in un file locale nel dispositivo IoT Edge. Questo file di dati viene caricato periodicamente nell'hub IoT per affinare il training del modello di Machine Learning. L'uso del caricamento file invece dello streaming costante di messaggi è più conveniente.

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

## <a name="prerequisites"></a>Prerequisiti

Questo articolo fa parte di una serie di documenti relativi a un'esercitazione sull'uso di Azure Machine Learning in IoT Edge. Ogni articolo della serie si basa sulle attività di quello precedente. Se questo articolo è stato aperto direttamente, vedere il [primo articolo](tutorial-machine-learning-edge-01-intro.md) della serie.

## <a name="create-a-new-iot-edge-solution"></a>Creare una nuova soluzione IoT Edge

Durante l'esecuzione del secondo dei due notebook di Azure, è stata creata e pubblicata un'immagine del contenitore contenente il modello di vita utile rimanente. Azure Machine Learning, come parte del processo di creazione dell'immagine, ha creato un pacchetto di tale modello in modo che l'immagine sia distribuibile come modulo Azure IoT Edge.

In questo passaggio verrà creata una soluzione Azure IoT Edge usando il modulo "Azure Machine Learning" e puntandolo all'immagine pubblicata con Azure Notebooks.

1. Aprire una sessione Desktop remoto nella VM di sviluppo.

1. Aprire la cartella **C:\\source\\IoTEdgeAndMlSample** in Visual Studio Code.

1. Fare clic con il pulsante destro del mouse in uno spazio vuoto del pannello di esplorazione e scegliere **New IoT Edge Solution** (Nuova soluzione IoT Edge).

    ![Creare una nuova soluzione IoT Edge](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

1. Accettare il nome predefinito **EdgeSolution** per la soluzione.

1. Scegliere **Azure Machine Learning** come modello del modulo.

1. Assegnare al modulo il nome **turbofanRulClassifier**.

1. Scegliere l'area di lavoro di Machine Learning. Si tratta dell'area di lavoro **turboFanDemo** creata nell'articolo [Esercitazione: Eseguire il training e la distribuzione di un modello di Azure Machine Learning](tutorial-machine-learning-edge-04-train-model.md)

1. Selezionare l'immagine creata durante l'esecuzione del notebook di Azure.

1. Esaminare la soluzione. Sono stati creati i file seguenti:

   * **deployment.template.json:** questo file contiene la definizione di ogni modulo della soluzione. Ecco le tre sezioni a cui prestare attenzione:

     * **RegistryCredentials:** definisce il set di registri contenitori personalizzati usati nella soluzione. Al momento, dovrebbe contenere il registro dell'area di lavoro di Machine Learning, in cui è stata archiviata l'immagine di Azure Machine Learning. È possibile avere un numero qualsiasi di registri contenitori, ma per semplicità verrà usato solo questo per tutti i moduli.

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io"
         }
       }
       ```

     * **Modules:** questa sezione contiene il set di moduli definiti dall'utente usati con questa soluzione. La definizione del modulo turbofanRulClassifier punta all'immagine del registro contenitori. Gli altri moduli aggiunti alla soluzione verranno visualizzati in questa sezione.

       ```json
        "modules": {
          "turbofanRulClassifier": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "turbofandemo2cd74296.azurecr.io/edgemlsample:1",
              "createOptions": {}
            }
          }
        }
       ```

     * **Routes:** le route verranno usate spesso in questa esercitazione. Definiscono il modo in cui i moduli comunicano tra loro. La route esistente definita dal modello non corrisponde al routing necessario. Eliminare la route `turbofanRulClassifierToIoTHub`.

       ```json
        "$edgeHub": {
          "properties.desired": {
            "schemaVersion": "1.0",
            "routes": {
              "turbofanRulClassifierToIoTHub": "FROM /messages/modules/turbofanRulClassifier/outputs/* INTO $upstream"
            },
            "storeAndForwardConfiguration": {
              "timeToLiveSecs": 7200
            }
          }
        }
       ```

   * **deployment.debug.template.json:** questo file è la versione di debug del file deployment.template.json. In genere, questo file deve essere mantenuto sincronizzato con il contenuto del file deployment.template.json, ma questa operazione non è necessaria per questa esercitazione.

   * **.env:** in questo file sarà necessario specificare il nome utente e la password per l'accesso al registro.

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

1. Fare clic con il pulsante destro del mouse sul file deployment.template.json nella finestra di esplorazione di Visual Studio Code e scegliere **Build IoT Edge Solution** (Crea soluzione IoT Edge).

1. Si noti che questo comando crea una cartella config con un file deployment.amd64.json. Questo file è il modello di distribuzione effettivo per la soluzione.

## <a name="add-router-module"></a>Aggiungere il modulo Router

A questo punto, aggiungere il modulo Router alla soluzione. Il modulo Router gestisce diverse operazioni della soluzione:

* **Ricezione di messaggi inviati dai dispositivi foglia:** quando i messaggi dei dispositivi downstream arrivano nel dispositivo IoT Edge, il modulo Router li riceve e inizia a orchestrarne il routing.
* **Invio di messaggi al modulo Classificatore vita utile rimanente:** quando viene ricevuto un nuovo messaggio inviato da un dispositivo downstream, il modulo Router lo trasforma nel formato previsto dal modulo Classificatore vita utile rimanente. Il modulo Router invia il messaggio al classificatore per la stima della vita utile rimanente. Dopo aver effettuato la stima, il classificatore rinvia il messaggio al modulo Router.
* **Invio di messaggi sulla vita utile rimanente all'hub IoT:** quando il modulo Router riceve i messaggi dal classificatore, li trasforma in modo che contengano solo le informazioni essenziali, ossia ID dispositivo e vita utile rimanente, quindi invia la versione abbreviata all'hub IoT. Un ulteriore perfezionamento, che non viene effettuato in questo esempio, prevede l'invio di messaggi all'hub IoT solo quando la stima della vita utile rimanente scende al di sotto di una soglia, ad esempio meno di 100 cicli. Con l'applicazione di questo filtro, si ridurrebbe la quantità di messaggi e il costo dell'hub IoT.
* **Invio del messaggio al modulo Writer Avro:** per preservare tutti i dati inviati dal dispositivo downstream, il modulo Router invia l'intero messaggio ricevuto dal classificatore al modulo Writer Avro, che rende persistenti i dati e li carica tramite caricamento file dell'hub IoT.

Il modulo Router è una parte importante della soluzione che garantisce che i messaggi vengano elaborati nell'ordine corretto.

### <a name="create-the-module-and-copy-files"></a>Creare il modulo e copiare i file

1. Fare clic con il pulsante destro del mouse sulla cartella modules in Visual Studio Code e scegliere **Aggiungi modulo IoT Edge**.

1. Scegliere **C# Module** (Modulo C#) come modello di modulo.

1. Assegnare al modulo il nome **turbofanRouter**.

1. Quando viene chiesto di specificare il repository di immagini Docker, usare il registro dell'area di lavoro di Machine Learning, disponibile nel nodo registryCredentials del file *deployment.template.json*. Questo valore corrisponde all'indirizzo completo del registro, ad esempio **\<your registry\>.azurecr.io/turbofanrouter**.

    > [!NOTE]
    > In questo articolo viene usato il Registro Azure Container creato dall'area di lavoro di Machine Learning. Questa scelta viene fatta esclusivamente per praticità. Sarebbe stato possibile creare un nuovo registro contenitori in cui pubblicare i moduli.

1. Nel terminale, usando una shell del prompt dei comandi, copiare i file del modulo di esempio nella soluzione.

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

1. Accettare la richiesta di confermare la sovrascrittura del file program.cs.

### <a name="build-router-module"></a>Creare il modulo Router

1. In Visual Studio Code selezionare **Terminale** > **Configure Default Build Task** (Configura attività di compilazione predefinita).

1. Selezionare **Crea il file tasks.json dal modello**.

1. Selezionare **.NET Core**.

1. Sostituire il contenuto del file tasks.json con il codice seguente.

    ```json
    {
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

1. Salvare e chiudere il file tasks.json.

1. Eseguire la compilazione premendo `Ctrl + Shift + B` o selezionando **Terminale** > **Esegui attività di compilazione**.

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

## <a name="add-avro-writer-module"></a>Aggiungere il modulo Writer Avro

Il modulo Writer Avro esegue due operazioni nella soluzione, ossia l'archiviazione dei messaggi e il caricamento dei file.

* **Archiviazione dei messaggi**: quando il modulo Writer Avro riceve un messaggio, lo scrive nel file system locale in formato Avro. Viene usato un bind mount, che monta una directory, in questo caso /data/avrofiles, nel contenitore del modulo. In questo modo il modulo può scrivere in un percorso locale (/avrofiles) e rendere questi file direttamente accessibili dal dispositivo IoT Edge.

* **Caricamento dei file**: il modulo Writer Avro usa la funzionalità di caricamento file dell'hub IoT di Azure per caricare i file in un account di archiviazione di Azure. Dopo aver caricato i file, il modulo li elimina dal disco

### <a name="create-module-and-copy-files"></a>Creare il modulo e copiare i file

1. In Visual Studio Code selezionare **Visualizza** > **Riquadro comandi**, quindi cercare e selezionare **Python: Select Interpreter** (Python: Seleziona interprete).

1. Selezionare la versione 3.7 o successiva di Python installata.

1. Fare clic con il pulsante destro del mouse sulla cartella modules in Visual Studio Code e scegliere **Aggiungi modulo IoT Edge**.

1. Scegliere **Modulo Python**.

1. Assegnare al modulo il nome `avroFileWriter`.

1. Quando viene chiesto di specificare il repository di immagini Docker, scegliere lo stesso registro usato per l'aggiunta del modulo Router.

1. Copiare i file del modulo di esempio nella soluzione.

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. Accettare la sovrascrittura di main.py.

1. Si noti che nella soluzione sono stati aggiunti i file filemanager.py e schema.py e che il file main.py è stato aggiornato.

> [!NOTE]
> Quando si apre un file Python, è possibile che venga richiesto di installare pylint. Non è necessario installare il linter per completare questa esercitazione.

### <a name="bind-mount-for-data-files"></a>Bind mount per i file di dati

Come accennato in precedenza, il modulo writer si basa sulla presenza del bind mount per scrivere i file Avro nel file system del dispositivo.

#### <a name="add-directory-to-device"></a>Aggiungere la directory al dispositivo

1. Nel portale di Azure avviare la VM del dispositivo IoT Edge se non è in esecuzione. Connettersi tramite SSH. Per la connessione è necessario il nome DNS che è possibile copiare dalla pagina di panoramica della macchina virtuale nel portale di Azure.

   ```cmd
   ssh -l <user>@<vm name>.<region>.cloudapp.azure.com
   ```

1. Dopo aver eseguito l'accesso, creare la directory in cui memorizzare i messaggi salvati dei dispositivi foglia.

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

1. Aggiornare le autorizzazioni per la directory per renderla scrivibile dal contenitore.

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

1. Verificare se ora utente, gruppo e proprietario hanno autorizzazioni in scrittura (w) per la directory.

   ```bash
   ls -la /data
   ```

   ![Autorizzazioni per la directory per avrofiles](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>Aggiungere la directory al modulo

Per aggiungere la directory al contenitore del modulo, i Dockerfile associati al modulo avroFileWriter verranno modificati. Al modulo sono associati tre Dockerfile: Dockerfile.amd64, Dockerfile.amd64.debug e Dockerfile.arm32v7. Questi file devono essere mantenuti sincronizzati nel caso si desideri eseguire il debug o la distribuzione in un dispositivo arm32. Per questo articolo, verrà usato solo Dockerfile.amd64.

1. Nella macchina virtuale di sviluppo aprire il file **C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avoFileWriter\Dockerfile.amd64**.

1. Modificare il file in base all'esempio seguente:

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

1. Se necessario, apportare le modifiche corrispondenti nei file Dockerfile.amd64.debug e Dockerfile.arm32v7.

#### <a name="add-bind-configuration-to-the-avrofilewriter"></a>Aggiungere la configurazione di binding a avroFileWriter

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

### <a name="bind-mount-for-access-to-configyaml"></a>Bind mount per l'accesso a config.yaml

È necessario aggiungere un altro bind per il modulo writer. Questo bind fornisce al modulo l'accesso per leggere la stringa di connessione del file /etc/iotedge/config.yaml nel dispositivo IoT Edge. La stringa di connessione è necessaria per creare un oggetto IoTHubClient, in modo da poter chiamare il metodo upload\_blob\_async per caricare i file nell'hub IoT. I passaggi per aggiungere questo bind sono simili a quelli della sezione precedente.

#### <a name="update-directory-permission"></a>Aggiornare l'autorizzazione per la directory

1. Connettersi al dispositivo IoT Edge tramite SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

1. Aggiungere l'autorizzazione in lettura per il file config.yaml.

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

1. Verificare se le autorizzazioni siano impostate correttamente.

   ```bash
   ls -la /etc/iotedge/
   ```

1. Assicurarsi che le autorizzazioni per config.yaml siano **-r--r--r--** .

#### <a name="add-directory-to-module"></a>Aggiungere la directory al modulo

1. Nel computer di sviluppo aprire il file **Dockerfile.amd64**.

1. Aggiungere un altro set di comandi `mkdir` e `chown` al file in questo modo:

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

1. Apportare le modifiche corrispondenti nei file Dockerfile.amd64.debug e Dockerfile.arm32v7.

#### <a name="update-the-module-configuration"></a>Aggiornare la configurazione del modulo

1. Aprire il file **deployment.template.json**.

1. Modificare la definizione del modulo avroFileWriter aggiungendo una seconda riga al parametro `Binds` che punta la directory del contenitore (/app/iotconfig) alla directory locale nel dispositivo (/etc/iotedge).

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

1. Apportare le modifiche corrispondenti nel file deployment.debug.template.json.

## <a name="install-dependencies"></a>Installare le dipendenze

Il modulo writer accetta una dipendenza da due librerie Python, fastavro e PyYAML. È necessario installare le dipendenze nel computer di sviluppo e indicare al processo di compilazione Docker di installarle nell'immagine del modulo.

### <a name="pyyaml"></a>PyYAML

1. Nel computer di sviluppo aprire il file `C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avoFileWriter\requirements.txt` e aggiungere "pyyaml" in una nuova riga.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

1. Aprire il file **Dockerfile.amd64** e aggiungere un comando `pip install` per aggiornare setuptools.

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

1. Al prompt dei comandi installare pyyaml nel computer di sviluppo.

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

1. Installare fastavro nel computer di sviluppo.

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

1. Dal menu del riquadro sinistro, in **Messaggistica**, scegliere **Routing messaggi**.

1. Nella scheda **Route** selezionare **Aggiungi**.

1. Assegnare alla route il nome **RulMessageRoute**.

1. Selezionare **Aggiungi endpoint** accanto al selettore **Endpoint** e scegliere **Archiviazione**.

1. Nella pagina **Aggiungi un endpoint di archiviazione** assegnare all'endpoint il nome **ruldata**.

1. Scegliere **Selezionare un contenitore**.

1. Nella pagina **Account di archiviazione** individuare l'account di archiviazione in uso in questa esercitazione, denominato **iotedgeandml\<unique suffix\>** .

1. Selezionare il contenitore **ruldata** e fare clic su **Seleziona**.

1. Tornare nella pagina **Aggiungi un endpoint di archiviazione** e selezionare **Crea** per creare l'endpoint di archiviazione.

1. Tornare nella pagina **Aggiungi una route** e per **Query di routing** sostituire `true` con la query seguente:

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

1. Espandere la sezione **Test** e quindi la sezione **Corpo del messaggio**. Sostituire il corpo del messaggio con questo esempio di messaggi previsti:

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

1. Selezionare **Testa route**. Se il test riesce, viene visualizzato il messaggio "Il messaggio corrisponde alla query".

1. Fare clic su **Salva**.

#### <a name="update-turbofandevicedatatostorage-route"></a>Aggiornare la route turbofanDeviceDataToStorage

I nuovi dati di stima non dovranno essere instradati alla posizione di archiviazione precedente, quindi è necessario aggiornare la route per impedirlo.

1. Nella pagina **Routing messaggi** dell'hub IoT selezionare la scheda **Route**.

1. Selezionare **turbofanDeviceDataToStorage** o qualsiasi altro nome assegnato alla route iniziale dei dati dei dispositivi.

1. Aggiornare la query di routing in questo modo

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

1. Espandere la sezione **Test** e quindi la sezione **Corpo del messaggio**. Sostituire il messaggio con questo esempio di messaggi previsti:

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

1. Selezionare **Testa route**. Se il test riesce, viene visualizzato il messaggio "Il messaggio corrisponde alla query".

1. Selezionare **Salva**.

### <a name="configure-file-upload"></a>Configurare il caricamento di file

Configurare la funzionalità di caricamento file dell'hub IoT in modo da consentire al modulo writer di caricare file nello spazio di archiviazione.

1. Dal menu del riquadro sinistro nell'hub IoT, in **Messaggistica**, scegliere **Caricamento file**.

1. Selezionare **Contenitore di archiviazione di Azure**.

1. Selezionare l'account di archiviazione dall'elenco.

1. Selezionare il contenitore che inizia con **azureml-blobstore** e termina con un GUID e fare clic su **Seleziona**.

1. Selezionare **Salva**. Al termine del salvataggio, nel portale viene visualizzata una notifica.

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

1. Nella macchina virtuale di sviluppo avviare Docker se non è in esecuzione.

1. In Visual Studio Code avviare un nuovo terminale con un prompt dei comandi e accedere al Registro Azure Container.

  È possibile trovare i valori necessari di nome utente, password e server di accesso nel portale di Azure. Il formato del nome del registro contenitori è "turbofandemo\<unique id\>". Dal menu del riquadro sinistro, in **Impostazioni**, scegliere **Chiavi di accesso** per visualizzarle.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. In Visual Studio Code fare clic con il pulsante destro del mouse sul file deployment.template.json e scegliere **Build and Push IoT Edge Solution** (Crea ed esegui il push della soluzione IoT Edge).

### <a name="view-modules-in-the-registry"></a>Visualizzare i moduli nel registro

Dopo aver completato la compilazione, è possibile usare il portale di Azure per esaminare i moduli pubblicati.

1. Aprire il Registro Azure Container per questa esercitazione. Il formato del nome del registro contenitori è "turbofandemo\<unique id\>". 

1. Dal menu del riquadro sinistro, in **Servizi**, scegliere **Repository**.

1. Entrambi i moduli creati, **avrofilewriter** e **turbofanrouter**, vengono visualizzati come repository.

1. Selezionare **turbofanrouter** e notare che è stata pubblicata un'immagine contrassegnata dal tag 0.0.1-amd64.

   ![Visualizzare la prima versione con tag di turbofanrouter](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>Distribuire moduli in un dispositivo IoT Edge

Una volta creati e configurati, i moduli della soluzione verranno distribuiti nel dispositivo IoT Edge.

1. In Visual Studio Code fare clic con il pulsante destro del mouse sul file **deployment.amd64.json** nella cartella config.

1. Scegliere **Create Deployment for Single Device** (Crea la distribuzione per un unico dispositivo).

1. Scegliere il dispositivo IoT Edge, **aaTurboFanEdgeDevice**.

1. Aggiornare il pannello di dispositivi dell'hub IoT di Azure nella finestra di esplorazione di Visual Studio Code. Come si può notare, i tre nuovi moduli sono stati distribuiti ma non sono ancora in esecuzione.

1. Aggiornare di nuovo dopo alcuni minuti per vedere i moduli in esecuzione.

   ![Visualizzazione dei moduli in esecuzione in Visual Studio Code](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> Possono essere necessari diversi minuti prima che i moduli vengano avviati e rimangano stabilmente in esecuzione. Durante questo periodo, i moduli vengono avviati e arrestati mentre provano a stabilire una connessione con il modulo dell'hub IoT Edge.

## <a name="diagnosing-failures"></a>Diagnosi degli errori

In questa sezione vengono descritte alcune tecniche per capire quale errore si è verificato in uno o più moduli. Spesso un errore può essere individuato prima di tutto dallo stato in Visual Studio Code.

### <a name="identify-failed-modules"></a>Identificare i moduli in errore

* **Visual Studio Code:** esaminare il pannello dei dispositivi dell'hub IoT di Azure. Se la maggior parte dei moduli è in esecuzione ma uno si è arrestato, è necessario analizzare ulteriormente quest'ultimo modulo. Se tutti i moduli rimangono nello stato arrestato per un periodo prolungato di tempo, è possibile che si sia verificato un errore anche in questo caso.

* **Portale di Azure:** se si passa all'hub IoT nel portale e quindi si trova la pagina di dettagli del dispositivo (in IoT Edge passare al dispositivo) è possibile scoprire se un modulo ha segnalato un errore o non ha mai segnalato niente all'hub IoT.

### <a name="diagnosing-from-the-device"></a>Diagnosi dal servizio

Accedendo al dispositivo IoT Edge (in questo caso la VM Linux), è possibile trovare numerose informazioni sullo stato dei moduli. Il meccanismo principale da usare è costituito dai comandi Docker che consentono di esaminare i contenitori e le immagini nel dispositivo.

1. Accedere al dispositivo IoT Edge:

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

1. Elencare tutti i dispositivi in esecuzione. Si dovrebbe vedere un contenitore per ogni modulo con un nome che corrisponde al modulo. Inoltre, questo comando genera l'immagine esatta del contenitore, inclusa la versione, quindi è possibile verificare se corrisponde a quella prevista. È anche possibile elencare le immagini sostituendo "image" a "container" nel comando.

   ```bash
   sudo docker container ls
   ```

1. Ottenere i log per un contenitore. L'output di questo comando include tutti i contenuti scritti in StdErr e StdOut nel contenitore. Questo comando funziona per i contenitori che sono stati avviati e poi si sono arrestati per qualche motivo. È anche utile per capire cosa succede nei contenitori edgeAgent o edgeHub.

   ```bash
   sudo docker container logs <container id>
   ```

1. Esaminare un contenitore. Questo comando fornisce moltissime informazioni sull'immagine. I dati possono essere filtrati in base all'informazione da cercare. Se ad esempio si vuole verificare se i bind di avroFileWriter sono corretti, è possibile usare il comando:

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

1. Connettersi a un contenitore in esecuzione. Questo comando può risultare utile se si vuole esaminare il contenitore durante l'esecuzione:

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata creata una soluzione IoT Edge in Visual Studio Code con tre moduli, ossia un classificatore, un router e un writer/strumento di caricamento file. Sono state configurate le route per consentire le comunicazioni tra i moduli nel dispositivo perimetrale. È stata modificata la configurazione di tale dispositivo e sono stati aggiornati i Dockerfile per installare le dipendenze e aggiungere bind mount ai contenitori dei moduli. 

In seguito è stata aggiornata la configurazione dell'hub IoT per instradare i messaggi in base al tipo e per gestire il caricamento dei file. Dopo aver implementato il tutto, i moduli sono stati distribuiti nel dispositivo IoT Edge, verificando se vengono eseguiti correttamente.

Continuare con l'articolo successivo per iniziare a inviare dati e vedere come funziona la soluzione.

> [!div class="nextstepaction"]
> [Inviare dati tramite gateway trasparente](tutorial-machine-learning-edge-07-send-data-to-hub.md)