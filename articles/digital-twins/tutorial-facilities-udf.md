---
title: 'Esercitazione: Monitorare uno spazio di dispositivi IoT - Gemelli digitali di Azure | Microsoft Docs'
description: Informazioni su come effettuare il provisioning delle risorse spaziali e monitorare le condizioni di lavoro con Gemelli digitali di Azure usando i passaggi descritti in questa esercitazione.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 11/13/2019
ms.openlocfilehash: 80fd1275f3bf9585ff8e40a94d0de2d422baec71
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383230"
---
# <a name="tutorial-provision-your-building-and-monitor-working-conditions-with-azure-digital-twins-preview"></a>Esercitazione: Effettuare il provisioning dell'edificio e monitorare le condizioni di lavoro con Anteprima di Gemelli digitali di Azure

Questa esercitazione illustra come usare Anteprima di Gemelli digitali di Azure per monitorare la temperatura e il livello di comfort dei propri spazi. Dopo aver [configurato l'edificio di esempio](tutorial-facilities-setup.md), è possibile effettuare il provisioning dell'edificio ed eseguire funzioni personalizzate sui dati dei sensori usando la procedura descritta in questa esercitazione.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Definire le condizioni da monitorare
> * Creare una funzione definita dall'utente
> * Simulare i dati dei sensori
> * Ottenere i risultati di una funzione definita dall'utente

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione presuppone che sia stata [completata l'installazione di Gemelli digitali di Azure](tutorial-facilities-setup.md). Prima di procedere, assicurarsi di avere:

- Un [account Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un'istanza di Gemelli digitali in esecuzione. 
- Gli [esempi C# di Gemelli digitali](https://github.com/Azure-Samples/digital-twins-samples-csharp) scaricati ed estratti nel computer di lavoro. 
- [.NET Core SDK versione 2.1.403 o successiva](https://www.microsoft.com/net/download) nel computer di sviluppo per compilare ed eseguire l'esempio. Eseguire `dotnet --version` per verificare se è installata la versione corretta. 
- [Visual Studio Code](https://code.visualstudio.com/) per esplorare il codice di esempio. 

> [!TIP]
> Se si esegue il provisioning di una nuova istanza, usare un nome istanza di Gemelli digitali univoco.

## <a name="define-conditions-to-monitor"></a>Definire le condizioni da monitorare

È possibile definire un set di condizioni specifiche da monitorare nei dati dei dispositivi o dei sensori, denominate *matcher*. È quindi possibile definire funzioni chiamate *funzioni definite dall'utente*. che eseguono la logica personalizzata sui dati provenienti dagli spazi e dai dispositivi, quando si verificano le condizioni specificate dai matcher. Per altre informazioni, vedere [Elaborazione dati e funzioni definite dall'utente](concepts-user-defined-functions.md). 

Dal progetto di esempio **occupancy-quickstart** aprire il file **src\actions\provisionSample.yaml** in Visual Studio Code. Si noti la sezione che inizia con il tipo **matchers**. Ogni voce di questo tipo crea un matcher con il **nome** specificato, che monitorerà un sensore di tipo **dataTypeValue**. Si noti che è correlato allo spazio *Focus Room A1*, che ha un nodo **devices**, contenente alcuni sensori. Per effettuare il provisioning di un matcher che terrà traccia di questi sensori, assicurarsi che **dataTypeValue** corrisponda all'elemento **dataType** del sensore. 

Aggiungere il matcher seguente sotto i matcher esistenti. Assicurarsi che le chiavi siano allineate e gli spazi non vengano sostituiti da caratteri di tabulazione. Queste righe sono presenti anche nel file *provisionSample.yaml* come righe commentate. Per rimuovere il commento, rimuovere il carattere `#` all'inizio di ogni riga.

```yaml
      - name: Matcher Temperature
        dataTypeValue: Temperature
```

Tale matcher terrà traccia del sensore `SAMPLE_SENSOR_TEMPERATURE` aggiunto nella [prima esercitazione](tutorial-facilities-setup.md). 

## <a name="create-a-user-defined-function"></a>Creare una funzione definita dall'utente

Le funzioni definite dall'utente consentono di personalizzare l'elaborazione dei dati dei sensori. Sono costituite da codice JavaScript personalizzato che può essere eseguito all'interno dell'istanza di Gemelli digitali di Azure, quando si verificano condizioni specifiche descritte dai matcher. È possibile creare matcher e funzioni definite dall'utente per ogni sensore che si vuole monitorare. Per altre informazioni, vedere [Elaborazione dati e funzioni definite dall'utente](concepts-user-defined-functions.md). 

Nel file *provisionSample.yaml* di esempio cercare una sezione che inizia con il tipo **userdefinedfunctions**. Questa sezione effettua il provisioning di una funzione definita dall'utente con un **nome** specificato, che agisce sull'elenco di matcher in **matcherNames**. Tenere presente che è possibile fornire il proprio file JavaScript per la funzione definita dall'utente come **script**.

Si noti anche la sezione denominata **roleassignments** Assegna il ruolo di Amministratore dello spazio alla funzione definita dall'utente. Questo ruolo consente di accedere agli eventi provenienti da uno qualsiasi degli spazi con provisioning. 

1. Configurare la funzione definita dall'utente per includere il matcher relativo alla temperatura aggiungendo la riga seguente o rimuovendone il commento nel nodo `matcherNames` del file *provisionSample.yaml*:

    ```yaml
            - Matcher Temperature
    ```

1. Aprire il file **src\actions\userDefinedFunctions\availability.js** nell'editor. Si tratta del file a cui si fa riferimento nell'elemento **script** di *provisionSample.yaml*. La funzione definita dall'utente in questo file cerca le condizioni in cui non viene rilevato alcun movimento nel locale e i livelli di emissione di anidride carbonica sono inferiori a 1,000 ppm. 

   Modificare il file JavaScript per monitorare la temperatura e le altre condizioni. Aggiungere le righe di codice seguenti per cercare le condizioni in non viene rilevato alcun movimento nel locale, i livelli di emissione di anidride carbonica sono inferiori a 1,000 ppm e la temperatura è inferiore 78 gradi Fahrenheit.

   > [!NOTE]
   > Questa sezione modifica il file *src\actions\userDefinedFunctions\availability.js* ed è quindi possibile vedere in dettaglio come scrivere una funzione definita dall'utente. È tuttavia possibile scegliere di usare il file [src\actions\userDefinedFunctions\availabilityForTutorial.js](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) nella configurazione. Questo file contiene tutte le modifiche necessarie per questa esercitazione. Se si usa invece questo file, verificare di usare il nome file corretto per la chiave **script** in [src\actions\provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml).

    a. All'inizio del file aggiungere le righe seguenti per la temperatura sotto il commento `// Add your sensor type here`:

    ```JavaScript
        var temperatureType = "Temperature";
        var temperatureThreshold = 78;
    ```

    b. Aggiungere le righe seguenti dopo l'istruzione che definisce `var motionSensor`, sotto il commento `// Add your sensor variable here`:

     ```JavaScript
        var temperatureSensor = otherSensors.find(function(element) {
            return element.DataType === temperatureType;
        });
    ```

    c. Aggiungere le righe seguenti dopo l'istruzione che definisce `var carbonDioxideValue`, sotto il commento `// Add your sensor latest value here`:

    ```JavaScript
        var temperatureValue = getFloatValue(temperatureSensor.Value().Value);
    ```

    d. Rimuovere le righe di codice seguenti sotto il commento `// Modify this line to monitor your sensor value`:

     ```JavaScript
        if(carbonDioxideValue === null || motionValue === null) {
            sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide or motion are null, returning");
            return;
        }
    ```

    Sostituirle con le righe seguenti:

    ```JavaScript
        if(carbonDioxideValue === null || motionValue === null || temperatureValue === null){
            sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide, motion, or temperature are null, returning");
            return;
        }
    ```

    e. Rimuovere le righe di codice seguenti sotto il commento `// Modify these lines as per your sensor`:

    ```JavaScript
        var availableFresh = "Room is available and air is fresh";
        var noAvailableOrFresh = "Room is not available or air quality is poor";
    ```

    Sostituirle con le righe seguenti:

    ```JavaScript
        var alert = "Room with fresh air and comfortable temperature is available.";
        var noAlert = "Either room is occupied, or working conditions are not right.";
    ```

    f. Rimuovere il blocco di codice *if-else* seguente dopo il commento `// Modify this code block for your sensor`:

    ```JavaScript
        // If carbonDioxide less than threshold and no presence in the room => log, notify and set parent space computed value
        if(carbonDioxideValue < carbonDioxideThreshold && !presence) {
            log(`${availableFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
            setSpaceValue(parentSpace.Id, spaceAvailFresh, availableFresh);
        }
        else {
            log(`${noAvailableOrFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
            setSpaceValue(parentSpace.Id, spaceAvailFresh, noAvailableOrFresh);

            // Set up custom notification for poor air quality
            parentSpace.Notify(JSON.stringify(noAvailableOrFresh));
        }
    ```

    Sostituirlo con il blocco *if-else* seguente:

    ```JavaScript
        // If sensor values are within range and room is available
        if(carbonDioxideValue < carbonDioxideThreshold && temperatureValue < temperatureThreshold && !presence) {
            log(`${alert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);

            // log, notify and set parent space computed value
            setSpaceValue(parentSpace.Id, spaceAvailFresh, alert);

            // Set up notification for this alert
            parentSpace.Notify(JSON.stringify(alert));
        }
        else {
            log(`${noAlert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);

            // log, notify and set parent space computed value
            setSpaceValue(parentSpace.Id, spaceAvailFresh, noAlert);
        }
    ```

    La funzione definita dall'utente modificata cercherà una condizione in cui viene reso disponibile un locale con le emissioni di anidride carbonica e la temperatura comprese entro limiti tollerabili. Genererà una notifica con l'istruzione `parentSpace.Notify(JSON.stringify(alert));` quando questa condizione sarà soddisfatta. Imposterà il valore dello spazio monitorato indipendentemente dal fatto che la condizione venga soddisfatta, con il messaggio corrispondente.

    g. Salvare il file.

1. Aprire una finestra di comando e passare alla cartella **occupancy-quickstart\src**. Eseguire il comando seguente per effettuare il provisioning del grafico di intelligenza spaziale e della funzione definita dall'utente:

    ```cmd/sh
    dotnet run ProvisionSample
    ```

   > [!IMPORTANT]
   > Per evitare l'accesso non autorizzato all'API di gestione di Gemelli digitali, l'applicazione **occupancy-quickstart** richiede di accedere con le credenziali dell'account Azure. Salva le credenziali per un breve periodo, quindi non dovrebbe essere necessario eseguire l'accesso ogni volta che viene eseguita. Quando questo programma viene eseguito per la prima volta o quando le credenziali salvate scadono, l'applicazione indirizzerà a una pagina di accesso e si riceverà un codice specifico della sessione da immettere in tale pagina. Seguire le istruzioni per accedere con l'account Azure.

1. Dopo l'autenticazione dell'account, l'applicazione avvia la creazione di un grafico spaziale di esempio configurato in *provisionSample.yaml*. Attendere finché non viene completato il processo di provisioning. L'operazione richiederà alcuni minuti. Al termine, esaminare i messaggi nella finestra di comando. Si noterà che il grafico spaziale è stato creato. Si noti che l'applicazione crea un hub IoT nel nodo radice o `Venue`.

1. Dall'output della finestra di comando copiare negli Appunti il valore di `ConnectionString`, nella sezione `Devices`. Questo valore sarà necessario per simulare la connessione del dispositivo nella sezione successiva.

    [![Effettuare il provisioning dell'esempio](./media/tutorial-facilities-udf/run-provision-sample.png)](./media/tutorial-facilities-udf/run-provision-sample.png#lightbox)

> [!TIP]
> Se viene visualizzato un messaggio di errore simile a "The I/O operation has been aborted because of either a thread exit or an application request", durante il provisioning, provare a eseguire nuovamente il comando. Questa situazione può verificarsi se il client HTTP ha raggiunto il timeout a causa di un problema di rete.

## <a name="simulate-sensor-data"></a>Simulare i dati dei sensori

In questa sezione si userà il progetto denominato *device-connectivity* nell'esempio. Si simuleranno i dati del sensore per il rilevamento del movimento, della temperatura e delle emissioni di anidride carbonica. Questo progetto genera valori casuali per i sensori e li invia all'hub IoT usando la stringa di connessione.

1. In una finestra di comando separata passare all'esempio di Gemelli digitali di Azure e quindi alla cartella **device-connectivity**.

1. Eseguire questo comando per verificare che le dipendenze del progetto siano corrette:

    ```cmd/sh
    dotnet restore
    ```

1. Aprire il file [appsettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) nell'editor e modificare i valori seguenti:

   a. **DeviceConnectionString**: assegnare il valore di `ConnectionString` nella finestra di output della sezione precedente. Copiare questa stringa completamente, all'interno delle virgolette, per consentire al simulatore di connettersi senza problemi all'hub IoT.

   b. **HardwareId** nella matrice **Sensors**: dal momento che si stanno simulando eventi dai sensori di cui è stato effettuato il provisioning nell'istanza di Gemelli digitali di Azure, l'ID hardware e i nomi dei sensori in questo file corrisponderanno a quelli del nodo `sensors` del file *provisionSample.yaml*.

      Aggiungere una nuova voce per il sensore della temperatura. Il nodo **Sensors** in *appsettings.json* sarà simile al seguente:

      ```JSON
      "Sensors": [{
        "DataType": "Motion",
        "HardwareId": "SAMPLE_SENSOR_MOTION"
      },{
        "DataType": "CarbonDioxide",
        "HardwareId": "SAMPLE_SENSOR_CARBONDIOXIDE"
      },{
        "DataType": "Temperature",
        "HardwareId": "SAMPLE_SENSOR_TEMPERATURE"
      }]
      ```

1. Eseguire questo comando per avviare la simulazione di eventi del dispositivo per temperatura, movimento ed emissioni di anidride carbonica:

    ```cmd/sh
    dotnet run
    ```

   > [!NOTE]
   > Dal momento che l'esempio di simulazione non comunica direttamente con l'istanza Gemelli digitali, non richiede l'autenticazione.

## <a name="get-results-of-the-user-defined-function"></a>Ottenere i risultati della funzione definita dall'utente

La funzione definita dall'utente viene eseguita ogni volta che l'istanza riceve i dati dei sensori e dei dispositivi. Questa sezione esegue una query dell'istanza di Gemelli digitali di Azure per ottenere i risultati della funzione definita dall'utente. Si noterà quasi in tempo reale quando un locale è disponibile, l'aria è fresca e la temperatura è giusta. 

1. Aprire la finestra di comando usata per effettuare il provisioning dell'esempio o una nuova finestra di comando e passare nuovamente alla cartella **occupancy-quickstart\src** dell'esempio.

1. Eseguire il comando seguente e accedere quando richiesto:

    ```cmd/sh
    dotnet run GetAvailableAndFreshSpaces
    ```

La finestra di output visualizza come viene eseguita la funzione definita dall'utente e intercetta gli eventi della simulazione del dispositivo. 

   [![Output per la funzione definita dall'utente](./media/tutorial-facilities-udf/udf-running.png)](./media/tutorial-facilities-udf/udf-running.png#lightbox)

Se la condizione monitorata viene soddisfatta, la funzione definita dall'utente imposta il valore dello spazio con il messaggio pertinente, come è stato illustrato nella [sezione precedente](#create-a-user-defined-function). La funzione `GetAvailableAndFreshSpaces` visualizza il messaggio nella console.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si non si vuole esplorare ulteriormente Gemelli digitali di Azure, è possibile eliminare le risorse create in questa esercitazione:

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Tutte le risorse** nel menu a sinistra, selezionare il gruppo di risorse di Gemelli digitali e quindi fare clic su **Elimina**.

    > [!TIP]
    > Se si sono riscontrati problemi durante l'eliminazione dell'istanza di Gemelli digitali, è stato reso disponibile un aggiornamento del servizio con la correzione. Riprovare a eliminare l'istanza.

2. Se necessario, eliminare le applicazioni di esempio nel computer di lavoro.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato effettuato il provisioning degli spazi ed è stato creato un framework per attivare le notifiche personalizzate, è possibile continuare con una delle esercitazioni seguenti:

> [!div class="nextstepaction"]
> [Esercitazione: Ricevere notifiche dagli spazi di Gemelli digitali di Azure usando App per la logica](tutorial-facilities-events.md)

> [!div class="nextstepaction"]
> [Esercitazione: Visualizzare e analizzare gli eventi dagli spazi di Gemelli digitali di Azure usando Time Series Insights](tutorial-facilities-analyze.md)
