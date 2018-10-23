---
title: Esercitazione per Node.js per Azure IoT Edge | Microsoft Docs
description: Questa esercitazione illustra come creare un modulo per IoT Edge con codice Node.js e distribuirlo in un dispositivo perimetrale
services: iot-edge
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: defdebec158f763003e90957687f4565176cb76a
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166849"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-to-your-simulated-device"></a>Esercitazione: Sviluppare e distribuire un modulo Node.js per IoT Edge in un dispositivo simulato

È possibile usare i moduli di IoT Edge per distribuire codice che implementa la logica di business direttamente nei dispositivi di IoT Edge. Questa esercitazione illustra la creazione e distribuzione di un modulo IoT Edge che filtra i dati del sensore. Verrà utilizzato il dispositivo IoT Edge simulato che è stato creato nelle esercitazioni Distribuire Azure IoT Edge in un dispositivo simulato in [Windows][lnk-tutorial1-win] e [Linux][lnk-tutorial1-lin]. In questa esercitazione si apprenderà come:    

> [!div class="checklist"]
> * Usare Visual Studio Code per creare un modulo Node.js per IoT Edge
> * Usare Visual Studio Code e Docker per creare un'immagine Docker e pubblicarla nel registro 
> * Distribuire il modulo nel dispositivo IoT Edge
> * Visualizzare i dati generati


Il modulo di IoT Edge creato in questa esercitazione filtra i dati relativi alla temperatura generati dal dispositivo. Invia messaggi upstream solo quando la temperatura è superiore a una soglia specificata. Questo tipo di analisi alla rete perimetrale è utile per ridurre la quantità di dati comunicati e archiviati nel cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Un dispositivo Azure IoT Edge:

* È possibile usare il computer per lo sviluppo o una macchina virtuale come dispositivo perimetrale seguendo la procedura illustrata nella guida introduttiva per dispositivi [Linux](quickstart-linux.md) o [Windows](quickstart.md).

Risorse cloud:

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Gratuito o Standard in Azure. 

Risorse per lo sviluppo:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Estensione Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) per Visual Studio Code. 
* [Docker CE](https://docs.docker.com/engine/installation/). 
* [Node.js e npm](https://nodejs.org). Poiché il pacchetto npm viene distribuito con Node.js, quando si scarica Node.js npm viene automaticamente installato nel computer.

## <a name="create-a-container-registry"></a>Creare un registro di contenitori
In questa esercitazione viene usata l'estensione Azure IoT Edge per Visual Studio Code per creare un modulo e un'**immagine del contenitore** dai file. Eseguire quindi il push dell'immagine in un **registro** che archivia e gestisce le immagini. Distribuire infine l'immagine dal registro nel dispositivo IoT Edge.  

È possibile usare qualsiasi registro compatibile con Docker per questa esercitazione. Due servizi molto diffusi per il registro Docker disponibili sul cloud sono il [Registro contenitori di Azure](https://docs.microsoft.com/azure/container-registry/) e [Hub Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Questa esercitazione usa il Registro contenitori di Azure. 

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa** > **Contenitori** > **Registro contenitori di Azure**.
2. Assegnare un nome al registro, scegliere una sottoscrizione e un gruppo di risorse e impostare lo SKU su **Di base**. 
3. Selezionare **Crea**.
4. Dopo aver creato il registro contenitori, passare al registro e selezionare **Chiavi di accesso**. 
5. Impostare **Utente amministratore** su **Abilita**.
6. Copiare i valori nei campi **Server di accesso**, **Nome utente** e **Password**. Questi valori verranno usati più avanti nell'esercitazione. 

## <a name="create-an-iot-edge-module-project"></a>Creare un progetto di modulo IoT Edge
La procedura seguente illustra come creare un modulo Node.js per IoT Edge tramite Visual Studio Code e l'estensione Azure IoT Edge.

### <a name="create-a-new-solution"></a>Creare una nuova soluzione

Usare **npm** per creare un modello di soluzione Node.js a partire dalla quale eseguire la compilazione. 

1. In Visual Studio Code selezionare **Visualizza** > **Terminale integrato** per aprire il terminale integrato di Visual Studio Code.

2. Nel terminale integrato immettere il comando seguente per installare **yeoman** e il generatore per il modulo Node.js per Azure IoT Edge: 

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3. Selezionare **Visualizza** > **Riquadro comandi** per aprire il riquadro comandi di VS Code. 

3. Nel riquadro comandi digitare ed eseguire il comando **Azure: Sign in** (Azure: Accedi) e seguire le istruzioni per accedere all'account Azure. Se è stato già effettuato l'accesso, è possibile ignorare questo passaggio.

4. Nel riquadro comandi digitare ed eseguire il comando **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nuova soluzione IoT Edge). Nel riquadro comandi immettere le informazioni seguenti per creare la soluzione: 

   1. Selezionare la cartella in cui si vuole creare la soluzione. 
   2. Specificare un nome per la soluzione o accettare quello predefinito **EdgeSolution**.
   3. Scegliere **Node.js Module** (Modulo Node.js) come modello di modulo. 
   4. Assegnare al modulo il nome **NodeModule**. 
   5. Specificare il registro contenitori di Azure creato nella sezione precedente come repository di immagini per il primo modulo. Sostituire **localhost:5000** con il valore del server di accesso copiato. La stringa finale è simile a **\<nome registro\>.azurecr.io/nodemodule**.

   ![Specificare il repository di immagini Docker](./media/tutorial-node-module/repository.png)

La finestra di VS Code carica l'area di lavoro della soluzione IoT Edge. L'area di lavoro della soluzione contiene cinque componenti di livello superiore. In questa esercitazione non verranno modificati né la cartella **\.vscode** né il file **\.gitignore**. La cartella **modules** contiene il codice Node.js per il modulo e i file Dockerfile per creare il modulo come un'immagine del contenitore. Il file **\.env** archivia le credenziali del registro contenitori. Il file **deployment.template.json** contiene le informazioni che il runtime IoT Edge usa per distribuire i moduli in un dispositivo. 

Se non è stato specificato un registro contenitori durante la creazione della soluzione, ma è stato accettato il valore predefinito localhost:5000, non sarà presente il file con estensione \. env. 

   ![Area di lavoro della soluzione Node.js](./media/tutorial-node-module/workspace.png)

### <a name="add-your-registry-credentials"></a>Aggiungere le credenziali del registro

Il file dell'ambiente archivia le credenziali per il repository di contenitori e le condivide con il runtime IoT Edge. Queste credenziali sono necessarie al runtime per eseguire il pull delle immagini private nel dispositivo IoT Edge. 

1. Nello strumento di esplorazione di VS Code aprire il file con estensione **env**. 
2. Aggiornare i campi con i valori di **nome utente** e **password** copiati dal registro contenitori di Azure. 
3. Salvare questo file. 

### <a name="update-the-module-with-custom-code"></a>Aggiornare il modulo con il codice personalizzato

In ogni modello è incluso il codice di esempio, che accetta i dati simulati del sensore dal modulo **tempSensor** e li indirizza all'hub IoT. In questa sezione aggiungere il codice per fare in modo che NodeModule analizzi i messaggi prima di inviarli. 

1. Nello strumento di esplorazione di VS Code aprire **modules** > **NodeModule** > **app.js**.

5. Aggiungere una variabile di soglia della temperatura sotto i moduli Node richiesti. La soglia della temperatura imposta il valore che la temperatura misurata deve superare per inviare i dati all'hub IoT.

    ```javascript
    var temperatureThreshold = 25;
    ```

6. Sostituire l'intera funzione `PipeMessage` con la funzione `FilterMessage`.
    
    ```javascript
    // This function filters out messages that report temperatures below the temperature threshold.
    // It also adds the MessageType property to the message with the value set to Alert.
    function filterMessage(client, inputName, msg) {
        client.complete(msg, printResultFor('Receiving message'));
        if (inputName === 'input1') {
            var message = msg.getBytes().toString('utf8');
            var messageBody = JSON.parse(message);
            if (messageBody && messageBody.machine && messageBody.machine.temperature && messageBody.machine.temperature > temperatureThreshold) {
                console.log(`Machine temperature ${messageBody.machine.temperature} exceeds threshold ${temperatureThreshold}`);
                var outputMsg = new Message(message);
                outputMsg.properties.add('MessageType', 'Alert');
                client.sendOutputEvent('output1', outputMsg, printResultFor('Sending received message'));
            }
        }
    }

    ```

7. Sostituire il nome della funzione `pipeMessage` con `filterMessage` nella funzione `client.on()`.

    ```javascript
    client.on('inputMessage', function (inputName, msg) {
        filterMessage(client, inputName, msg);
        });
    ```

8. Copiare il frammento di codice seguente nel callback della funzione `client.open()`, dopo `client.on()` nell'istruzione `else`. Questa funzione viene richiamata quando le proprietà desiderate vengono aggiornate.

    ```javascript
    client.getTwin(function (err, twin) {
        if (err) {
            console.error('Error getting twin: ' + err.message);
        } else {
            twin.on('properties.desired', function(delta) {
                if (delta.TemperatureThreshold) {
                    temperatureThreshold = delta.TemperatureThreshold;
                }
            });
        }
    });
    ```

9. Salvare questo file.

## <a name="build-your-iot-edge-solution"></a>Compilare la soluzione IoT Edge

Nella sezione precedente è creata una soluzione IoT Edge ed è stato aggiunto a NodeModule il codice per filtrare i messaggi in cui la temperatura del computer segnalata è inferiore alla soglia accettabile. È ora necessario compilare la soluzione come immagine del contenitore ed eseguirne il push nel registro contenitori. 

1. Accedere a Docker immettendo il comando seguente nel terminale integrato di Visual Studio Code, per poter eseguire il push dell'immagine del modulo nel record di controllo di accesso: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Usare il nome utente, la password e il server di accesso copiati dal registro contenitori di Azure nella prima sezione oppure recuperarli nuovamente dalla sezione **Chiavi di accesso** del registro nel portale di Azure.

2. Nello strumento di esplorazione di VS Code aprire il file **deployment.template.json** nell'area di lavoro della soluzione IoT Edge. 

   Questo file comunica a `$edgeAgent` di distribuire due moduli: **tempSensor**, che simula i dati del dispositivo, e **NodeModule**. Il valore `NodeModule.image` è impostato su una versione di Linux amd64 dell'immagine. Per altre informazioni sui manifesti di distribuzione, vedere [Informazioni su come usare, configurare e riusare i moduli IoT Edge](module-composition.md).

   Questo file contiene anche le credenziali del registro. Nel file del modello il nome utente e la password vengono popolati con segnaposto. Quando si genera il manifesto della distribuzione, i campi vengono aggiornati con i valori aggiunti al file **ENV**. 

4. Aggiungere il modulo gemello NodeModule al manifesto della distribuzione. Inserire il contenuto JSON seguente alla fine della sezione `moduleContent`, dopo il modulo gemello `$edgeHub`: 
    ```json
        "NodeModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```
5. Salvare questo file.
6. Nello strumento di esplorazione di Visual Studio Code fare clic con il pulsante destro del mouse sul file **deployment.template.json** e scegliere **Build and Push IoT Edge solution** (Compila ed esegui il push della soluzione IoT Edge). 

Quando si comunica a Visual Studio Code di compilare la soluzione, prima di tutto con le informazioni del modello di distribuzione viene generato un file `deployment.json` in una nuova cartella **config**. Vengono quindi eseguiti due comandi nel terminale integrato: `docker build` e `docker push`. Questi due comandi compilano il codice, includono il codice Node.js in un contenitore e ne eseguono il push nel registro contenitori specificato quando è stata inizializzata la soluzione. 

È possibile visualizzare l'indirizzo completo dell'immagine del contenitore con tag nel comando `docker build` eseguito nel terminale integrato di VS Code. L'indirizzo dell'immagine è costituito dalle informazioni presenti nel file `module.json`, nel formato **\<repository\>:\<versione\>-\<piattaforma\>**. Per questa esercitazione, il risultato sarà simile a **registryname.azurecr.io/nodemodule:0.0.1-amd64**.

## <a name="deploy-and-run-the-solution"></a>Distribuire ed eseguire la soluzione

Nell'articolo della guida introduttiva usato per configurare il dispositivo IoT Edge è stato distribuito un modulo usando il portale di Azure. È possibile distribuire i moduli anche usando l'estensione Azure IoT Toolkit per Visual Studio Code. Il manifesto della distribuzione, il file **deployment.json**, è già disponibile per questo scenario. Ora è sufficiente selezionare un dispositivo che riceverà la distribuzione.

1. Nel riquadro comandi di VS Code eseguire **Azure IoT Hub: Select IoT Hub** (Hub IoT di Azure: Seleziona l'hub IoT). 

2. Scegliere la sottoscrizione e l'hub IoT che contiene il dispositivo IoT Edge che si vuole configurare. 

3. Nello strumento di esplorazione di VS Code espandere la sezione **Azure IoT Hub dispositivi** (Dispositivi dell'hub IoT di Azure). 

4. Fare clic con il pulsante destro del mouse sul nome del dispositivo IoT Edge, quindi selezionare **Create Deployment for Single Device** (Crea la distribuzione per un unico dispositivo). 

   ![Create deployment for single device (Crea la distribuzione per un unico dispositivo)](./media/tutorial-node-module/create-deployment.png)

5. Selezionare il file **deployment.json** nella cartella **config** e quindi fare clic su **Select Edge Deployment Manifest** (Seleziona il manifesto della distribuzione di Edge). Non usare il file deployment.template.json. 

6. Fare clic sul pulsante Aggiorna. Dovrebbe essere visualizzato il nuovo **NodeModule** in esecuzione insieme al modulo **TempSensor** e a **$edgeAgent** e **$edgeHub**. 


## <a name="view-generated-data"></a>Visualizzare i dati generati

Dopo aver applicato il manifesto della distribuzione al dispositivo IoT Edge, il runtime di IoT Edge nel dispositivo raccoglie le informazioni della nuova distribuzione e si avvia all'interno di questa. Tutti i moduli in esecuzione nel dispositivo che non sono inclusi nel manifesto della distribuzione vengono arrestati. Tutti i moduli mancanti dal dispositivo vengono avviati. 

È possibile visualizzare lo stato del dispositivo IoT Edge tramite la sezione **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure) della finestra di esplorazione di Visual Studio Code. Espandere i dettagli del dispositivo per visualizzare un elenco dei moduli distribuiti e in esecuzione. 

Nel dispositivo IoT Edge stesso è possibile visualizzare lo stato dei moduli della distribuzione usando il comando `iotedge list`. Devono essere presenti quattro moduli: i due moduli di runtime di IoT Edge, tempSensor e il modulo personalizzato creato in questa esercitazione. L'avvio di tutti i moduli può richiedere qualche minuto. Rieseguire quindi il comando se inizialmente non sono visualizzati tutti. 

Per visualizzare i messaggi generati da un qualsiasi modulo, usare il comando `iotedge logs <module name>`. 

È possibile visualizzare i messaggi man mano che arrivano nell'hub IoT tramite Visual Studio Code. 

1. Per monitorare i dati in arrivo all'hub IoT, fare clic su **...** e selezionare **Start Monitoring D2C Messages** (Avvia il monitoraggio dei messaggi D2C).
2. Per monitorare il messaggio D2C per un dispositivo specifico, fare clic con il pulsante destro del mouse sul dispositivo nell'elenco e scegliere **Start Monitoring D2C Messages** (Avvia il monitoraggio dei messaggi D2C).
3. Per arrestare il monitoraggio dei dati, eseguire il comando **Azure IoT Hub: Stop monitoring D2C message** (Hub IoT di Azure: Interrompi il monitoraggio del messaggio D2C) nel riquadro comandi. 
4. Per visualizzare o aggiornare il modulo gemello, fare clic con il pulsante destro del mouse sul modulo nell'elenco e scegliere **Edit module twin** (Modifica il modulo gemello). Per aggiornare il modulo gemello, salvare il file JSON gemello e fare clic con il pulsante destro del mouse sull'area degli editor e scegliere **Update Module Twin** (Aggiorna il modulo gemello).
5. Per visualizzare i log di Docker, è possibile installare [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) per VS Code e trovare i moduli in esecuzione in locale nello strumento di esplorazione di Docker. Dal menu di scelta rapida scegliere **Mostra log** per visualizzarli nel terminale integrato. 

## <a name="clean-up-resources"></a>Pulire le risorse 

Se si intende continuare con il prossimo articolo consigliato, è possibile conservare le risorse e le configurazioni create e riutilizzarle. È anche possibile continuare a usare lo stesso dispositivo IoT Edge come dispositivo di test. 

In caso contrario, è possibile eliminare le risorse di Azure e le configurazioni locali create in questo articolo per evitare addebiti. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un modulo di IoT Edge contenente il codice per filtrare i dati non elaborati generati dal dispositivo di IoT Edge. È possibile continuare con una delle esercitazioni seguenti per ottenere informazioni sugli altri modi in cui Azure IoT Edge può contribuire alla trasformazione dei dati in informazioni dettagliate aziendali nei dispositivi perimetrali.

> [!div class="nextstepaction"]
> [Distribuire Funzione di Azure come modulo](tutorial-deploy-function.md)
> [Distribuire Analisi di flusso di Azure come modulo](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
