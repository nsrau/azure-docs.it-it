---
title: 'Esercitazione: creare un modulo Java personalizzato - Azure IoT Edge | Microsoft Docs'
description: Questa esercitazione illustra come creare un modulo per IoT Edge con codice Java e distribuirlo in un dispositivo perimetrale.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 9a541f42670b3ccf83331e3e2e9069289bb9b4b3
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224074"
---
# <a name="tutorial-develop-a-java-iot-edge-module-and-deploy-to-your-simulated-device"></a>Esercitazione: Sviluppare un modulo Java per IoT Edge e distribuirlo in un dispositivo simulato

È possibile usare i moduli di Azure IoT Edge per distribuire codice che implementa la logica di business direttamente nei dispositivi di IoT Edge. Questa esercitazione illustra la creazione e distribuzione di un modulo IoT Edge che filtra i dati del sensore. Verrà usato il dispositivo IoT Edge simulato che è stato creato nelle guide introduttive relative alla distribuzione di Azure IoT Edge in un dispositivo simulato in [Windows](quickstart.md) e [Linux](quickstart-linux.md). In questa esercitazione si apprenderà come:    

> [!div class="checklist"]
> * Usare Visual Studio Code per creare un modulo Java per IoT Edge basato sul pacchetto di modelli Maven per Azure IoT Edge e su Azure IoT SDK per dispositivi Java.
> * Usare Visual Studio Code e Docker per creare un'immagine Docker e pubblicarla nel registro.
> * Distribuire il modulo nel dispositivo IoT Edge.
> * Visualizzare i dati generati.


Il modulo di IoT Edge creato in questa esercitazione filtra i dati relativi alla temperatura generati dal dispositivo. Invia messaggi upstream solo quando la temperatura è superiore a una soglia specificata. Questo tipo di analisi alla rete perimetrale è utile per ridurre la quantità di dati comunicati e archiviati nel cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Prerequisiti

Un dispositivo Azure IoT Edge:

* È possibile configurare un dispositivo IoT Edge seguendo i passaggi descritti negli argomenti di avvio rapido per [Linux](quickstart-linux.md) p [Windows](quickstart.md).
* Per IoT Edge nei dispositivi Windows, la versione 1.0.5 non supporta i moduli Java. Per altre informazioni, vedere le [Note sulla versione 1.0.5](https://github.com/Azure/azure-iotedge/releases/tag/1.0.5). Per istruzioni su come installare una versione specifica, vedere [Aggiornare il daemon di sicurezza e il runtime di IoT Edge](how-to-update-iot-edge.md).

Risorse cloud:

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Gratuito o Standard in Azure. 

Risorse per lo sviluppo:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Java Extension Pack](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) per Visual Studio Code.
* [Strumenti di Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) per Visual Studio Code. 
* [Java SE Development Kit 10](https://aka.ms/azure-jdks) e [impostare la variabile di ambiente `JAVA_HOME` ](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) in modo che punti all'installazione di JDK.
* [Maven](https://maven.apache.org/)
* [Docker CE](https://docs.docker.com/install/)
   * Per lo sviluppo in un dispositivo Windows, verificare che Docker sia [configurato per l'uso di contenitori Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers). 


## <a name="create-a-container-registry"></a>Creare un registro contenitori

In questa esercitazione vengono usati gli strumenti di Azure IoT per Visual Studio Code per creare un modulo e un'**immagine del contenitore** dai file. Eseguire quindi il push dell'immagine in un **registro** che archivia e gestisce le immagini. Distribuire infine l'immagine dal registro nel dispositivo IoT Edge.  

È possibile usare qualsiasi registro compatibile con Docker per inserire le immagini dei contenitori. Due servizi di registro Docker molto diffusi sono [Registro Azure Container](https://docs.microsoft.com/azure/container-registry/) e [Hub Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Questa esercitazione usa il Registro Azure Container. 

Se non è ancora disponibile alcun registro contenitori, seguire questa procedura per crearne uno nuovo in Azure:

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa** > **Contenitori** > **Registro Container**.

2. Specificare i valori seguenti per creare il registro contenitori:

   | Campo | Valore | 
   | ----- | ----- |
   | Nome registro | Specificare un nome univoco. |
   | Sottoscrizione | Selezionare una sottoscrizione nell'elenco a discesa. |
   | Gruppo di risorse | Per una gestione più semplice, usare lo stesso gruppo di risorse per tutte le risorse di test create durante le esercitazioni e le guide di avvio rapido di IoT Edge. Ad esempio, **IoTEdgeResources**. |
   | Località | Scegliere una località vicina. |
   | Utente amministratore | Impostare su **Abilita**. |
   | SKU | Selezionare **Basic**. | 

5. Selezionare **Create**.

6. Dopo aver creato il registro contenitori, passare al registro e quindi selezionare **Chiavi di accesso**. 

7. Copiare i valori nei campi **Server di accesso**, **Nome utente** e **Password**. Usare questi valori più avanti nell'esercitazione per fornire l'accesso al registro contenitori. 

## <a name="create-an-iot-edge-module-project"></a>Creare un progetto di modulo IoT Edge
La procedura seguente consente di creare un progetto di modulo IoT Edge basato sul pacchetto di modelli Maven per Azure IoT Edge e su Azure IoT SDK per dispositivi Java. Il progetto viene creato mediante Visual Studio Code e gli strumenti di Azure IoT.

### <a name="create-a-new-solution"></a>Creare una nuova soluzione

Creare un modello di soluzione Java da poter personalizzare con il proprio codice. 

1. In Visual Studio Code selezionare **Visualizza** > **Riquadro comandi** per aprire il riquadro comandi di VS Code. 

2. Nel riquadro comandi immettere ed eseguire il comando **Azure IoT Edge: Nuova soluzione IoT Edge**. Seguire i prompt nel riquadro comandi per creare la soluzione.

   | Campo | Valore |
   | ----- | ----- |
   | Selezionare la cartella | Nel computer di sviluppo scegliere la posizione in cui Visual Studio Code dovrà creare i file della soluzione. |
   | Provide a solution name (Specificare un nome per la soluzione) | Immettere un nome descrittivo per la soluzione oppure accettare quello predefinito **EdgeSolution**. |
   | Select module template (Selezionare un modello di modulo) | Scegliere **Java Module** (Modulo Java). |
   | Specificare il valore per l'ID gruppo | Immettere un valore per l'ID gruppo o accettare quello predefinito **com.edgemodule**. |
   | Provide a module name (Specificare un nome per il modulo) | Assegnare il modulo il nome **JavaModule**. |
   | Provide Docker image repository for the module (Specificare il repository di immagini Docker per il modulo) | Un repository di immagini include il nome del registro contenitori e il nome dell'immagine del contenitore. L'immagine del contenitore viene preinserita in base al nome specificato nell'ultimo passaggio. Sostituire **localhost:5000** con il valore del server di accesso in Registro Azure Container. È possibile recuperare il server di accesso dalla pagina Panoramica del registro contenitori nel portale di Azure. <br><br>Il repository di immagini finale sarà simile a \<nome registro\>.azurecr.io/javamodule. |
 
   ![Specificare il repository di immagini Docker](./media/tutorial-java-module/repository.png)
   
Se si crea il modulo Java per la prima volta, il download dei pacchetti Maven potrebbe richiedere alcuni minuti. La finestra di VS Code carica quindi l'area di lavoro della soluzione IoT Edge. L'area di lavoro della soluzione contiene cinque componenti di livello superiore. La cartella **modules** contiene il codice Java per il modulo e i Dockerfile per creare il modulo come un'immagine del contenitore. Il file **\.env** archivia le credenziali del registro contenitori. Il file **deployment.template.json** contiene le informazioni che il runtime IoT Edge usa per distribuire i moduli in un dispositivo. Il file **deployment.debug.template.json** contiene invece la versione di debug dei moduli. In questa esercitazione non verranno modificati né la cartella **\.vscode** né il file **\.gitignore**. 

Se non è stato specificato un registro contenitori durante la creazione della soluzione, ma è stato accettato il valore predefinito localhost:5000, non sarà presente il file con estensione \. env. 

### <a name="add-your-registry-credentials"></a>Aggiungere le credenziali del registro

Il file dell'ambiente archivia le credenziali per il registro contenitori e le condivide con il runtime IoT Edge. Queste credenziali sono necessarie al runtime per eseguire il pull delle immagini private nel dispositivo IoT Edge. 

1. Nello strumento di esplorazione di Visual Studio Code aprire il file con estensione env. 
2. Aggiornare i campi con i valori di **nome utente** e **password** copiati dal registro contenitori di Azure. 
3. Salvare questo file. 

### <a name="update-the-module-with-custom-code"></a>Aggiornare il modulo con il codice personalizzato

1. Nello strumento di esplorazione di VS Code aprire **modules** > **JavaModule** > **src** > **main** > **java** > **com** > **edgemodule** > **App.java**.

5. Aggiungere il codice seguente all'inizio del file per importare nuove classi di riferimento.

    ```java
    import java.io.StringReader;
    import java.util.concurrent.atomic.AtomicLong;
    import java.util.HashMap;
    import java.util.Map;
    
    import javax.json.Json;
    import javax.json.JsonObject;
    import javax.json.JsonReader;
    
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Pair;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Property;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.TwinPropertyCallBack;
    ```

5. Aggiungere la definizione seguente alla classe **App**. Questa variabile imposta una soglia di temperatura. La temperatura misurata della macchina non viene segnalata all'hub IoT finché non supera questo valore. 

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

7. Sostituire il metodo execute **MessageCallbackMqtt** con il codice seguente. Questo metodo viene chiamato ogni volta che il modulo riceve un messaggio MQTT dall'hub di IoT Edge. Filtra i messaggi con un valore della temperatura inferiore alla soglia relativa alla temperatura configurata tramite il modulo gemello.

    ```java
        private int counter = 0;
       @Override
        public IotHubMessageResult execute(Message msg, Object context) {
            this.counter += 1;
 
            String msgString = new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET);
            System.out.println(
                   String.format("Received message %d: %s",
                            this.counter, msgString));
            if (context instanceof ModuleClient) {
                try (JsonReader jsonReader = Json.createReader(new StringReader(msgString))) {
                    final JsonObject msgObject = jsonReader.readObject();
                    double temperature = msgObject.getJsonObject("machine").getJsonNumber("temperature").doubleValue();
                    long threshold = App.tempThreshold.get();
                    if (temperature >= threshold) {
                        ModuleClient client = (ModuleClient) context;
                        System.out.println(
                            String.format("Temperature above threshold %d. Sending message: %s",
                            threshold, msgString));
                        client.sendEventAsync(msg, eventCallback, msg, App.OUTPUT_NAME);
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            return IotHubMessageResult.COMPLETE;
        }
    ```

8. Aggiungere le due classi interne statiche seguenti alla classe **App**. Queste classi aggiornano la variabile tempThreshold al variare della proprietà desiderata del modulo gemello. Tutti i moduli hanno un modulo gemello che consente di configurare il codice in esecuzione all'interno di un modulo direttamente dal cloud.

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
 
    protected static class OnProperty implements TwinPropertyCallBack {
        @Override
        public void TwinPropertyCallBack(Property property, Object context) {
            if (!property.getIsReported()) {
                if (property.getKey().equals(App.TEMP_THRESHOLD)) {
                    try {
                        long threshold = Math.round((double) property.getValue());
                        App.tempThreshold.set(threshold);
                    } catch (Exception e) {
                        System.out.println("Faile to set TemperatureThread with exception");
                        e.printStackTrace();
                    }
                }
            }
        }
    }
    ```

9. Aggiungere le righe seguenti al metodo **main** dopo **client.open()** per eseguire la sottoscrizione agli aggiornamenti del modulo gemello.

    ```java
    client.startTwin(new DeviceTwinStatusCallBack(), null, new OnProperty(), null);
    Map<Property, Pair<TwinPropertyCallBack, Object>> onDesiredPropertyChange = new HashMap<Property, Pair<TwinPropertyCallBack, Object>>() {
        {
            put(new Property(App.TEMP_THRESHOLD, null), new Pair<TwinPropertyCallBack, Object>(new OnProperty(), null));
        }
    };
    client.subscribeToTwinDesiredProperties(onDesiredPropertyChange);
    client.getTwin();
    ```

11. Salvare il file App.java.

12. Nello strumento di esplorazione di VS Code aprire il file **deployment.template.json** nell'area di lavoro della soluzione IoT Edge. Questo file indica all'agente di IoT Edge quali moduli distribuire, in questo caso **tempSensor** e **JavaModule**, e indica all'hub di IoT Edge come indirizzare i messaggi tra i moduli. L'estensione di Visual Studio Code popola automaticamente la maggior parte delle informazioni necessarie nel modello di distribuzione, ma occorre assicurarsi che le informazioni siano appropriate per la soluzione specifica: 

   1. La piattaforma predefinita del dispositivo IoT Edge è impostata su **amd64** nella barra di stato di VS Code, di conseguenza **JavaModule** è impostato sulla versione amd64 Linux dell'immagine. Cambiare la piattaforma predefinita nella barra di stato da **amd64** a **arm32v7** o **windows-amd64** se corrisponde all'architettura del dispositivo IoT Edge. 

      ![Aggiornare la piattaforma di immagini del modulo](./media/tutorial-java-module/image-platform.png)

   2. Verificare che il modello abbia il nome modulo corretto, non il nome **SampleModule** predefinito che è stato modificato durante la creazione della soluzione IoT Edge.

   3. La sezione **registryCredentials** archivia le credenziali del registro Docker, in modo che l'agente di IoT Edge possa eseguire il pull dell'immagine del modulo. Le coppie effettive di username e password vengono archiviate nel file ENV, che viene ignorato da Git. Aggiungere le credenziali al file con estensione env, se non è già stato fatto.  

   4. Per altre informazioni sui manifesti della distribuzione, vedere [Informazioni su come distribuire moduli e definire route in IoT Edge](module-composition.md).

13. Aggiungere il modulo gemello **JavaModule** al manifesto della distribuzione. Inserire il contenuto JSON seguente alla fine della sezione **moduleContent** dopo il modulo gemello **$edgeHub**: 

   ```json
     "JavaModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Aggiungere il modulo gemello al modello di distribuzione](./media/tutorial-java-module/module-twin.png)

14. Salvare il file deployment.template.json.

## <a name="build-your-iot-edge-solution"></a>Compilare la soluzione IoT Edge

Nella sezione precedente è stata creata una soluzione IoT Edge ed è stato aggiunto codice a **JavaModule** per filtrare i messaggi in cui la temperatura segnalata del computer è inferiore al limite accettabile. Compilare ora la soluzione come immagine del contenitore ed eseguirne il push nel registro contenitori. 

1. Accedere a Docker immettendo il comando seguente nel terminale di Visual Studio Code. È quindi possibile eseguire il push dell'immagine del modulo nel registro contenitori di Azure.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Usare il nome utente, la password e il server di accesso copiati dal registro contenitori di Azure nella prima sezione. È anche possibile recuperare questi valori dalla sezione **Chiavi di accesso** del registro nel portale di Azure.

2. Nello strumento di esplorazione di Visual Studio Code fare clic con il pulsante destro del mouse sul file deployment.template.json e scegliere **Build and Push IoT Edge solution** (Compila ed esegui il push della soluzione IoT Edge). 

Quando si comunica a Visual Studio Code di compilare la soluzione, prima di tutto con le informazioni del modello di distribuzione viene generato un file deployment.json in una nuova cartella denominata **config**. Vengono quindi eseguiti due comandi nel terminale integrato: `docker build` e `docker push`. Questi due comandi compilano il codice, includono l'app Java in contenitori ed eseguono il push del codice nel registro contenitori specificato quando è stata inizializzata la soluzione. 

È possibile visualizzare l'indirizzo completo dell'immagine del contenitore con tag nel terminale integrato di VS Code. L'indirizzo dell'immagine è costituito dalle informazioni presenti nel file module.json nel formato \<repository\>:\<versione\>-\<piattaforma\>. Per questa esercitazione, il risultato sarà simile a registryname.azurecr.io/javamodule:0.0.1-amd64.

>[!TIP]
>Se quando si prova a compilare ed eseguire il push del modulo viene visualizzato un errore, effettuare i controlli seguenti:
>* L'accesso a Docker in Visual Studio Code è stato eseguito con le credenziali del registro contenitori? Queste credenziali sono diverse rispetto a quelle usate per accedere al portale di Azure.
>* Il repository di contenitori è corretto? Aprire **modules** > **cmodule** > **module.json** e trovare il campo **repository**. Il repository di immagini sarà simile a **\<nomeregistro\>.azurecr.io/javamodule**. 
>* Si sta compilando lo stesso tipo di contenitori eseguito dal computer di sviluppo? L'impostazione predefinita in Visual Studio Code è costituita dai contenitori Linux amd64. Se il computer di sviluppo esegue contenitori Windows o Linux arm32v7, aggiornare la piattaforma sulla barra di stato blu nella parte inferiore della finestra di VS Code in modo che corrisponda alla piattaforma dei contenitori.

## <a name="deploy-and-run-the-solution"></a>Distribuire ed eseguire la soluzione

Nell'articolo della guida introduttiva usato per configurare il dispositivo IoT Edge è stato distribuito un modulo usando il portale di Azure. È possibile distribuire i moduli anche usando l'estensione Azure hub IoT Toolkit (in precedenza estensione Azure IoT Toolkit) per Visual Studio Code. Il manifesto della distribuzione, il file **deployment.json**, è già disponibile per questo scenario. Ora è sufficiente selezionare un dispositivo che riceverà la distribuzione.

1. Nel riquadro comandi di VS Code eseguire il comando **Azure: Accedi** e seguire le istruzioni per accedere all'account Azure. Se è stato già effettuato l'accesso, è possibile ignorare questo passaggio.

2. Nel riquadro comandi di VS Code eseguire **Hub IoT di Azure: Selezionare l'hub IoT**. 

3. Scegliere la sottoscrizione e l'hub IoT che contiene il dispositivo IoT Edge che si vuole configurare. 

4. Nello strumento di esplorazione di VS Code espandere la sezione **Azure IoT Hub dispositivi** (Dispositivi dell'hub IoT di Azure). 

5. Fare clic con il pulsante destro del mouse sul nome del dispositivo IoT Edge, quindi selezionare **Create Deployment for Single Device** (Crea la distribuzione per un unico dispositivo). 

   ![Create deployment for single device (Crea la distribuzione per un unico dispositivo)](./media/tutorial-java-module/create-deployment.png)

6. Selezionare il file **deployment.json** nella cartella **config** e quindi fare clic su **Select Edge Deployment Manifest** (Seleziona il manifesto della distribuzione di Edge). Non usare il file deployment.template.json. 

7. Fare clic sul pulsante Aggiorna. Dovrebbe essere visualizzato il nuovo **JavaModule** in esecuzione insieme al modulo **TempSensor** e a **$edgeAgent** e **$edgeHub**.  

## <a name="view-generated-data"></a>Visualizzare i dati generati

Dopo aver applicato il manifesto della distribuzione al dispositivo IoT Edge, il runtime di IoT Edge nel dispositivo raccoglie le informazioni della nuova distribuzione e si avvia all'interno di questa. Tutti i moduli in esecuzione nel dispositivo che non sono inclusi nel manifesto della distribuzione vengono arrestati. Tutti i moduli mancanti dal dispositivo vengono avviati. 

È possibile visualizzare lo stato del dispositivo IoT Edge tramite la sezione **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure) della finestra di esplorazione di Visual Studio Code. Espandere i dettagli del dispositivo per visualizzare un elenco dei moduli distribuiti e in esecuzione. 

Nel dispositivo IoT Edge è possibile visualizzare lo stato dei moduli della distribuzione usando il comando `iotedge list`. Devono essere presenti quattro moduli: i due moduli di runtime di IoT Edge, tempSensor e il modulo personalizzato creato in questa esercitazione. L'avvio di tutti i moduli può richiedere qualche minuto. Rieseguire quindi il comando se inizialmente non sono visualizzati tutti. 

Per visualizzare i messaggi generati da un qualsiasi modulo, usare il comando `iotedge logs <module name>`. 

È possibile visualizzare i messaggi man mano che arrivano nell'hub IoT tramite Visual Studio Code. 

1. Per monitorare i dati che arrivano all'hub IoT, selezionare i puntini di sospensione (**...**) e quindi selezionare **Start Monitoring D2C Messages** (Avvia il monitoraggio dei messaggi D2C).
2. Per monitorare il messaggio D2C per un dispositivo specifico, fare clic con il pulsante destro del mouse sul dispositivo nell'elenco e scegliere **Start Monitoring D2C Messages** (Avvia il monitoraggio dei messaggi D2C).
3. Per interrompere il monitoraggio dei dati, eseguire il comando **hub IoT di Azure: Interrompere il monitoraggio di messaggi D2C** nel riquadro comandi. 
4. Per visualizzare o aggiornare il modulo gemello, fare clic con il pulsante destro del mouse sul modulo nell'elenco e scegliere **Edit module twin** (Modifica il modulo gemello). Per aggiornare il modulo gemello, salvare il file JSON gemello, fare clic con il pulsante destro del mouse sull'area degli editor e scegliere **Update Module Twin** (Aggiorna il modulo gemello).
5. Per visualizzare i log di Docker, installare [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) per Visual Studio Code. È possibile trovare i moduli in esecuzione in locale nello strumento di esplorazione di Docker. Dal menu di scelta rapida selezionare **Mostra log** per visualizzarli nel terminale integrato.
 
## <a name="clean-up-resources"></a>Pulire le risorse 

Se si intende continuare con il prossimo articolo consigliato, è possibile conservare le risorse e le configurazioni create e riutilizzarle. È anche possibile continuare a usare lo stesso dispositivo IoT Edge come dispositivo di test. 

In caso contrario, è possibile eliminare le risorse di Azure e le configurazioni locali create in questo articolo per evitare addebiti. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un modulo di IoT Edge con codice per filtrare i dati non elaborati generati dal dispositivo di IoT Edge. È possibile continuare con le esercitazioni successive per ottenere informazioni sugli altri modi in cui Azure IoT Edge può contribuire alla trasformazione dei dati in informazioni dettagliate aziendali nei dispositivi perimetrali.

> [!div class="nextstepaction"]
> [Archiviare dati sul perimetro con database di SQL Server](tutorial-store-data-sql-server.md)

