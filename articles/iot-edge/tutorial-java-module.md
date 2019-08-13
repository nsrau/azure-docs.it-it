---
title: 'Esercitazione: creare un modulo Java personalizzato - Azure IoT Edge | Microsoft Docs'
description: Questa esercitazione illustra come creare un modulo per IoT Edge con codice Java e distribuirlo in un dispositivo perimetrale.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 1aebe97b27902d37587ec6ac7009a5076b732f0f
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840118"
---
# <a name="tutorial-develop-a-java-iot-edge-module-for-linux-devices"></a>Esercitazione: Sviluppare un modulo IoT Edge in Java per dispositivi Linux

È possibile usare i moduli di Azure IoT Edge per distribuire codice che implementa la logica di business direttamente nei dispositivi di IoT Edge. Questa esercitazione illustra la creazione e distribuzione di un modulo IoT Edge che filtra i dati del sensore. Verrà usato il dispositivo IoT Edge simulato che è stato creato nell'argomento di avvio rapido relativo alla distribuzione di Azure IoT Edge in un dispositivo simulato in [Linux](quickstart-linux.md). In questa esercitazione si apprenderà come:    

> [!div class="checklist"]
> * Usare Visual Studio Code per creare un modulo Java per IoT Edge basato sul pacchetto di modelli Maven per Azure IoT Edge e su Azure IoT SDK per dispositivi Java.
> * Usare Visual Studio Code e Docker per creare un'immagine Docker e pubblicarla nel registro.
> * Distribuire il modulo nel dispositivo IoT Edge.
> * Visualizzare i dati generati.


Il modulo di IoT Edge creato in questa esercitazione filtra i dati relativi alla temperatura generati dal dispositivo. Invia messaggi upstream solo quando la temperatura è superiore a una soglia specificata. Questo tipo di analisi alla rete perimetrale è utile per ridurre la quantità di dati comunicati e archiviati nel cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Ambito della soluzione

Questa esercitazione illustra come sviluppare un modulo in **Java** usando **Visual Studio Code** e come distribuirlo in un **dispositivo Linux**. IoT Edge non supporta i moduli Java per dispositivi Windows. 

Usare la tabella seguente per informazioni sulle opzioni disponibili per lo sviluppo e la distribuzione di moduli Java: 

| Java | Visual Studio Code | Visual Studio 2017/2019 | 
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Usare Visual Studio Code per i moduli Java su Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Usare Visual Studio Code per i moduli Java su Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione è necessario aver completato l'esercitazione precedente per configurare l'ambiente di sviluppo per i contenitori Linux: [Sviluppare moduli IoT Edge per i dispositivi Linux](tutorial-develop-for-linux.md). Completando una delle due esercitazioni, saranno soddisfatti i prerequisiti seguenti: 

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Gratuito o Standard in Azure.
* Un [dispositivo Linux che esegue Azure IoT Edge](quickstart-linux.md)
* Un registro contenitori, ad esempio [Registro Azure Container](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) configurato con [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configurato per eseguire i contenitori Linux.

Per sviluppare un modulo IoT Edge in Java, installare i prerequisiti aggiuntivi seguenti nel computer di sviluppo: 

* [Java Extension Pack](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) per Visual Studio Code.
* [Java SE Development Kit 10](https://aka.ms/azure-jdks) e [impostare la variabile di ambiente `JAVA_HOME` ](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) in modo che punti all'installazione di JDK.
* [Maven](https://maven.apache.org/)

## <a name="create-a-module-project"></a>Creare un progetto di modulo
La procedura seguente consente di creare un progetto di modulo IoT Edge basato sul pacchetto di modelli Maven per Azure IoT Edge e su Azure IoT SDK per dispositivi Java. Il progetto viene creato mediante Visual Studio Code e gli strumenti di Azure IoT.

### <a name="create-a-new-project"></a>Creare un nuovo progetto

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

### <a name="select-your-target-architecture"></a>Selezionare l'architettura di destinazione

Attualmente, Visual Studio Code consente di sviluppare moduli Java per dispositivi Linux AMD64 e Linux ARM32v7. È necessario selezionare l'architettura di destinazione per ogni soluzione, perché il contenitore viene creato ed eseguito in modo diverso in base al tipo di architettura. L'impostazione predefinita è Linux AMD64. 

1. Aprire il riquadro comandi e cercare **Azure IoT Edge: Set Default Target Platform for Edge Solution** (Azure IoT Edge: Imposta la piattaforma di destinazione predefinita per la soluzione Edge) oppure selezionare l'icona del collegamento sulla barra laterale nella parte inferiore della finestra. 

2. Nel riquadro comandi selezionare l'architettura di destinazione nell'elenco di opzioni. Per questa esercitazione si usa una macchina virtuale Ubuntu come dispositivo IoT Edge, quindi si manterrà il valore predefinito **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Aggiornare il modulo con il codice personalizzato

1. Nello strumento di esplorazione di VS Code aprire **modules** > **JavaModule** > **src** > **main** > **java** > **com** > **edgemodule** > **App.java**.

2. Aggiungere il codice seguente all'inizio del file per importare nuove classi di riferimento.

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

3. Aggiungere la definizione seguente alla classe **App**. Questa variabile imposta una soglia di temperatura. La temperatura misurata della macchina non viene segnalata all'hub IoT finché non supera questo valore. 

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

4. Sostituire il metodo execute **MessageCallbackMqtt** con il codice seguente. Questo metodo viene chiamato ogni volta che il modulo riceve un messaggio MQTT dall'hub di IoT Edge. Filtra i messaggi con un valore della temperatura inferiore alla soglia relativa alla temperatura configurata tramite il modulo gemello.

    ```java
    protected static class MessageCallbackMqtt implements MessageCallback {
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
    }
    ```

5. Aggiungere le due classi interne statiche seguenti alla classe **App**. Queste classi aggiornano la variabile tempThreshold al variare della proprietà desiderata del modulo gemello. Tutti i moduli hanno un modulo gemello che consente di configurare il codice in esecuzione all'interno di un modulo direttamente dal cloud.

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

6. Aggiungere le righe seguenti al metodo **main** dopo **client.open()** per eseguire la sottoscrizione agli aggiornamenti del modulo gemello.

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

7. Salvare il file App.java.

8. Nello strumento di esplorazione di VS Code aprire il file **deployment.template.json** nell'area di lavoro della soluzione IoT Edge.

9. Aggiungere il modulo gemello **JavaModule** al manifesto della distribuzione. Inserire il contenuto JSON seguente alla fine della sezione **moduleContent** dopo il modulo gemello **$edgeHub**: 

   ```json
     "JavaModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Aggiungere il modulo gemello al modello di distribuzione](./media/tutorial-java-module/module-twin.png)

10. Salvare il file deployment.template.json.

## <a name="build-and-push-your-module"></a>Creare il modulo ed eseguirne il push

Nella sezione precedente è stata creata una soluzione IoT Edge ed è stato aggiunto codice a **JavaModule** per filtrare i messaggi in cui la temperatura segnalata del computer è inferiore al limite accettabile. Compilare ora la soluzione come immagine del contenitore ed eseguirne il push nel registro contenitori. 

1. Aprire il terminale integrato di VS Code selezionando **Visualizza** > **Terminale**.

1. Accedere a Docker immettendo il comando seguente nel terminale. Accedere con il nome utente, la password e il server di accesso del registro contenitori di Azure. È possibile recuperare questi valori dalla sezione **Chiavi di accesso** del registro nel portale di Azure.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Potrebbe venire visualizzato un avviso di sicurezza in cui si consiglia l'uso di `--password-stdin`. Sebbene si tratti di una procedura consigliata per gli scenari di produzione, esula dell'ambito di questa esercitazione. Per altri dettagli, vedere le informazioni di riferimento sull'[accesso a docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

2. Nello strumento di esplorazione di Visual Studio Code fare clic con il pulsante destro del mouse sul file **deployment.template.json** e scegliere **Build and Push IoT Edge solution** (Compila ed esegui il push della soluzione IoT Edge).

   Il comando di creazione e push avvia tre operazioni. Prima di tutto, crea una nuova cartella nella soluzione denominata **config** che contiene il manifesto completo della distribuzione, basato sulle informazioni del modello di distribuzione e di altri file della soluzione. In secondo luogo, esegue `docker build` per creare l'immagine del contenitore in base al documento dockerfile appropriato per l'architettura di destinazione. Infine, esegue `docker push` per eseguire il push del repository di immagini nel registro contenitori.

## <a name="deploy-modules-to-device"></a>Distribuire i moduli nel dispositivo

Usare Esplora risorse di Visual Studio Code e l'estensione Azure IoT Tools per distribuire il progetto di modulo nel dispositivo IoT Edge. Il manifesto della distribuzione, il file **deployment.json**, è già disponibile per questo scenario nella cartella config. Ora è sufficiente selezionare un dispositivo che riceverà la distribuzione.

Assicurarsi che il dispositivo IoT Edge sia in esecuzione.

1. In Esplora risorse di Visual Studio Code espandere la sezione **Azure IoT Hub Devices** (Dispositivi dell'hub IoT di Azure).

2. Fare clic con il pulsante destro del mouse sul nome del dispositivo IoT Edge, quindi selezionare **Create Deployment for Single Device** (Crea la distribuzione per un unico dispositivo).

3. Selezionare il file **deployment.json** nella cartella **config** e quindi fare clic su **Select Edge Deployment Manifest** (Seleziona il manifesto della distribuzione di Edge). Non usare il file deployment.template.json.

4. Fare clic sul pulsante Aggiorna. Dovrebbe essere visualizzato il nuovo **JavaModule** in esecuzione insieme al modulo **SimulatedTemperatureSensor** e a **$edgeAgent** e **$edgeHub**.  

## <a name="view-generated-data"></a>Visualizzare i dati generati

Dopo aver applicato il manifesto della distribuzione al dispositivo IoT Edge, il runtime di IoT Edge nel dispositivo raccoglie le informazioni della nuova distribuzione e si avvia all'interno di questa. Tutti i moduli in esecuzione nel dispositivo che non sono inclusi nel manifesto della distribuzione vengono arrestati. Tutti i moduli mancanti dal dispositivo vengono avviati.

È possibile visualizzare lo stato del dispositivo IoT Edge tramite la sezione **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure) della finestra di esplorazione di Visual Studio Code. Espandere i dettagli del dispositivo per visualizzare un elenco dei moduli distribuiti e in esecuzione.

1. Nella finestra di esplorazione di Visual Studio Code fare clic con il pulsante destro del mouse sul nome del dispositivo IoT Edge e scegliere **Start Monitoring Built-in Event Endpoint** (Avvia monitoraggio endpoint eventi predefinito).

2. Visualizzare i messaggi in arrivo nell'hub IoT. L'arrivo dei messaggi potrebbe richiedere del tempo, perché il dispositivo IoT Edge deve ricevere la nuova distribuzione e avviare tutti i moduli. Quindi, a causa delle modifiche apportate, il codice JavaModule aspetta che la temperatura del computer raggiunga i 25 gradi prima di inviare messaggi. Ai messaggi che indicano il raggiungimento della soglia di temperatura viene inoltre aggiunto il tipo di messaggio **Alert**. 

## <a name="edit-the-module-twin"></a>Modificare il modulo gemello

Si è usato il modulo gemello JavaModule nel manifesto della distribuzione per impostare la soglia di temperatura su 25 gradi. È possibile usare il modulo gemello per cambiare la funzionalità senza dover aggiornare il codice del modulo.

1. In Visual Studio Code espandere i dettagli relativi al dispositivo IoT Edge per vedere i moduli in esecuzione. 

2. Fare clic con il pulsante destro del mouse su **JavaModule** e scegliere **Modifica modulo gemello**. 

3. Trovare **TemperatureThreshold** nelle proprietà desiderate. Sostituire il valore con una nuova temperatura più alta di 5-10 gradi rispetto all'ultima temperatura segnalata. 

4. Salvare il file del modulo gemello.

5. Fare clic con il pulsante destro del mouse in qualsiasi punto del riquadro di modifica del modulo gemello e scegliere **Update module twin** (Aggiorna modulo gemello). 

6. Monitorare i messaggi da dispositivo a cloud in ingresso. L'arrivo dei messaggi dovrebbe interrompersi finché non viene raggiunta la nuova soglia di temperatura. 

## <a name="clean-up-resources"></a>Pulire le risorse 

Se si intende continuare con il prossimo articolo consigliato, è possibile conservare le risorse e le configurazioni create e riutilizzarle. È anche possibile continuare a usare lo stesso dispositivo IoT Edge come dispositivo di test. 

In caso contrario, è possibile eliminare le risorse di Azure e le configurazioni locali create in questo articolo per evitare addebiti. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un modulo di IoT Edge contenente il codice per filtrare i dati non elaborati generati dal dispositivo di IoT Edge. Quando si è pronti a creare moduli personalizzati, si può passare agli argomenti su come [sviluppare moduli IoT Edge personalizzati](module-development.md) o [sviluppare moduli con Visual Studio Code](how-to-vs-code-develop-module.md). Continuare con le esercitazioni successive per informazioni sul modo in cui Azure IoT Edge semplifica la distribuzione di servizi cloud di Azure per elaborare e analizzare i dati nei dispositivi perimetrali.

> [!div class="nextstepaction"]
> [Funzioni](tutorial-deploy-function.md)
> [Analisi di flusso](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Servizio Visione personalizzata](tutorial-deploy-custom-vision.md)
