<properties
   pageTitle="Connettere un dispositivo a una soluzione preconfigurata | Microsoft Azure"
   description="Descrive come connettere un dispositivo alla soluzione di monitoraggio remoto preconfigurata Azure IoT Suite con un esempio che utilizza dati di temperatura e umidità."
   services=""
   documentationCenter="na"
   authors="hegate"
   manager="timlt"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/10/2015"
   ms.author="hegate"/>


# Connessione del dispositivo alla soluzione per il monitoraggio remoto Azure IoT Suite


## Panoramica dello scenario

In questo esempio verranno usate tre origini dati simulate: temperatura esterna, temperatura interna e umidità. Per semplicità, non verranno usati veri sensori (i dati vengono generati automaticamente nel codice client), ma come passaggio successivo è consigliabile connettere il sensore preferito e inviare dati reali. Per altre informazioni, vedere le risorse collegate nella sezione Estensione della soluzione.

## Prerequisiti

### Effettuare il provisioning della suite IoT

Se non si è ancora effettuato il provisioning della soluzione preconfigurata di monitoraggio remoto, è possibile farlo [qui](http://www.microsoft.com/it-IT/server-cloud/internet-of-things/getting-started.aspx).


### Effettuare il provisioning del dispositivo nella soluzione di monitoraggio remoto
```
Note: if you have already provisioned a device on your solution, you can skip this step.
```
Per connettere il dispositivo alla soluzione preconfigurata, è necessario ottenere dal dashboard le credenziali del dispositivo che verranno usate nell'applicazione client per identificare il dispositivo. Attenersi alla procedura descritta di seguito

1.  Nell'angolo inferiore sinistro del dashboard fare clic su "Aggiungi un dispositivo".

    ![][1]

2.  In Dispositivo personalizzato fare clic sul pulsante "Aggiungi nuovo".

    ![][2]

3.  Scegliere l'ID dispositivo immettendo un nome, ad esempio realdevice1, e fare clic su Controlla ID per assicurarsi che il nome non sia ancora stato usato.

    ![][3]

5. Copiare le credenziali fornite (ID dispositivo, nome host dell'hub IoT e chiave dispositivo). Saranno necessarie in seguito nell'applicazione client per connettere il dispositivo alla soluzione.

    ![][4]

6. Assicurarsi che il dispositivo venga visualizzato correttamente nella sezione dei dispositivi. Lo stato sarà "In sospeso". Questo è lo stato previsto finché non viene stabilita la connessione del dispositivo al cloud.

    ![][5]

[1]: ./media/iot-suite-connecting-devices/suite0.png
[2]: ./media/iot-suite-connecting-devices/suite1.png
[3]: ./media/iot-suite-connecting-devices/suite2.png
[4]: ./media/iot-suite-connecting-devices/suite3.png
[5]: ./media/iot-suite-connecting-devices/suite5new.png

Scegliere ora il linguaggio da usare per proseguire con l'esempio. In questa esercitazione è stato creato un codice di esempio per C e node.js, ma è possibile implementarlo anche in C Sharp e Java.

## Invio dei dati del dispositivo alla soluzione di monitoraggio remoto con C


### Esecuzione del dispositivo su Linux

1. Configurare l'ambiente: se non si è mai usato prima l'SDK per dispositivi, [qui](https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#linux) sono disponibili informazioni per configurare l'ambiente in Linux.

1. Aprire il file **c/serializer/samples/serializer/remote\_monitoring.c** in un editor di testo.

2. Trovare il codice seguente nel file:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```
    
3. Sostituire "[Device Id]" e "[Device Key]" con i dati del dispositivo.

4. Immettere i dati del dispositivo del nome host dell'hub IoT come nome dell'hub IoT e suffisso dell'hub IoT. A questo scopo, è necessario dividere in IoTHub + IoTHubSuffix. Ad esempio: se il nome host dell'hub IoT è "Contoso.azure-devices.net", "Contoso" sarà il nome dell'hub IoT e la parte rimanente sarà il suffisso. L'aspetto dovrebbe risultare simile al seguente:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

5. Salvare le modifiche e compilare gli esempi. Per compilare l'esempio, è possibile eseguire lo script build.sh nella directory **c/build\_all/linux**.

6. Eseguire l'applicazione di esempio **c/serializer/samples/remote\_monitoring/linux/remote\_monitoring**.

#### Visualizzare il dispositivo registrato e i dati

7. Tornare al dashboard della soluzione di monitoraggio remoto. Nell'elenco di dispositivi lo stato del dispositivo dovrebbe essere ora impostato su In esecuzione.

    ![][18]

8. Fare clic sul dashboard per visualizzare i dati. L'esempio è configurato per inviare 50 unità per la temperatura interna, 55 unità per la temperatura esterna e 50 per l'umidità. Si noti che, per impostazione predefinita, il dashboard mostra solo la temperatura e l'umidità.

8. Passare ora alla sezione [Comandare e controllare](#command) per informazioni su come modificare la temperatura sul dispositivo dalla soluzione di monitoraggio remoto.


### Esecuzione del dispositivo su Windows


1. Configurare l'ambiente: se non si è mai usato prima l'SDK per dispositivi, [qui](https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#windows) sono disponibili informazioni per configurare l'ambiente in Windows.

1. Avviare una nuova istanza di Visual Studio 2015. Aprire la soluzione **remote\_monitoring.sln** nella cartella **c\\serializer\\build\\windows** nella copia locale del repository.

2. In Visual Studio, in **Esplora soluzioni** passare alla cartella degli esempi. Nel progetto **remote\_monitoring** aprire il file **remote\_monitoring.c**.

2. Trovare il codice seguente nel file:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

3. Sostituire "[Device Id]", "[Device Key]" con i dati del dispositivo.

4. Immettere i dati del dispositivo del nome host dell'hub IoT come nome dell'hub IoT e suffisso dell'hub IoT. A questo scopo, è necessario dividerlo nel modo seguente:

    Se il nome host dell'hub IoT è Contoso.azure-devices.net, Contoso sarà il nome dell'hub IoTHub e tutto ciò che viene dopo sarà il suffisso. L'aspetto dovrebbe risultare simile al seguente:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

6. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **remote\_monitoring**, scegliere **Debug** e quindi fare clic su **Avvia nuova istanza** per compilare ed eseguire l'esempio. La console visualizza i messaggi quando l'applicazione invia all'hub IoT i messaggi dal dispositivo al cloud.

#### Visualizzare il dispositivo registrato e i dati

7. Tornare al dashboard della soluzione di monitoraggio remoto. Nell'elenco di dispositivi lo stato del dispositivo dovrebbe essere ora impostato su In esecuzione.

    ![][18]

8. Fare clic sul dashboard per visualizzare i dati. L'esempio è configurato per inviare 50 unità per la temperatura interna, 55 unità per la temperatura esterna e 50 per l'umidità. Si noti che, per impostazione predefinita, il dashboard mostra solo la temperatura e l'umidità.

8. Passare ora alla sezione [Comandare e controllare](#command) per informazioni su come modificare la temperatura sul dispositivo dalla soluzione di monitoraggio remoto.

### Esecuzione del dispositivo su mbed

Le istruzioni seguenti descrivono i passaggi per connettere un dispositivo [Freescale FRDM-K64F abilitato per mbed](https://developer.mbed.org/platforms/FRDM-K64F/) all'Hub IoT di Azure.


#### Requisiti

- Requisiti hardware: [Freescale K64F abilitato per mbed](https://developer.mbed.org/platforms/FRDM-K64F/) o simile.

#### Connettere il dispositivo

- Connettere la scheda alla rete con un cavo Ethernet. Questo passaggio è obbligatorio, perché l'esempio dipende dall'accesso a Internet.

- Collegare il dispositivo al computer con un cavo USB micro. Assicurarsi di collegare il cavo alla porta USB corretta sul dispositivo, come illustrato [qui](https://developer.mbed.org/platforms/frdm-k64f/), nella sezione introduttiva.

- Seguire le [istruzioni del manuale di mbed](https://developer.mbed.org/handbook/SerialPC) per configurare la connessione seriale al dispositivo dal computer di sviluppo. Se si usa Windows, installare i driver della porta seriale Windows disponibili [qui](http://developer.mbed.org/handbook/Windows-serial-configuration#1-download-the-mbed-windows-serial-port).

#### Creare il progetto mbed e importare il codice di esempio

- Nel Web browser passare al [sito per sviluppatori](https://developer.mbed.org/) mbed.org. Se non si è iscritti, verrà visualizzata un'opzione per creare un nuovo account (gratuito). In caso contrario, accedere con le credenziali dell'account. Fare quindi clic su **Compiler** nell'angolo superiore destro della pagina. Si dovrebbe passare all'interfaccia di Workspace Management.

- Verificare che la piattaforma hardware usata venga visualizzata nell'angolo superiore destro della finestra oppure fare clic sull'icona nell'angolo destro per selezionare la piattaforma hardware.

- Fare clic su **Import** nel menu principale. Fare quindi clic sul collegamento **Fare clic qui** per importare dal collegamento URL accanto al logo con il mondo di mbed.

    ![][6]

- Nella finestra popup immettere il collegamento per il codice di esempio https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/

    ![][7]

- È possibile osservare che il compilatore mbed, importando questo progetto, ha importato diverse librerie. Alcune sono fornite e gestite dal team IoT di Azure ([azureiot\_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/), [iothub\_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/), [iothub\_amqp\_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/), [iothub\_http\_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_http_transport/), [proton-c-mbed](https://developer.mbed.org/users/AzureIoTClient/code/proton-c-mbed/)), altre invece sono librerie di terze parti disponibili nel catalogo delle librerie di mbed.

    ![][8]

- Aprire remote\_monitoring\\remote\_monitoring.c e trovare il codice seguente nel file:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

3. Sostituire [Device Id] e [Device Key] con i dati del dispositivo.

4. Immettere i dati del dispositivo del nome host dell'hub IoT come nome dell'hub IoT e suffisso dell'hub IoT. A questo scopo, è necessario dividerlo nel modo seguente:

    Se il nome host dell'hub IoT è Contoso.azure-devices.net, Contoso sarà il nome dell'hub IoTHub e tutto ciò che viene dopo sarà il suffisso. L'aspetto dovrebbe risultare simile al seguente:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

    ![][9]

#### Compilare ed eseguire il programma

- Fare clic su **Compilare** per compilare il programma. È possibile ignorare eventuali avvisi, ma, se la compilazione genera errori, correggerli prima di continuare.

- Se la compilazione viene completata correttamente, viene generato un file bin con il nome del progetto. Copiare il file bin sul dispositivo. Salvando il file bin sul dispositivo, la sessione del terminale corrente con il dispositivo viene reimpostata. Quando si riconnette, reimpostare di nuovo il terminale manualmente o avviare un nuovo terminale. In questo modo il dispositivo mbed può essere reimpostato e può avviare l'esecuzione del programma.

- Connettersi al dispositivo con un'applicazione client SSH, ad esempio PuTTY. È possibile determinare la porta seriale usata dal dispositivo controllando Gestione dispositivi di Windows:


- In PuTTY fare clic sul tipo di connessione **Serial**. Il dispositivo molto probabilmente si connette a 115200, quindi immettere tale valore nella casella **Velocità**. Fare quindi clic su **Apri**:

    ![][11]

Inizia l'esecuzione del programma. Potrebbe essere necessario reimpostare la scheda (premere CTRL+INTERR o premere il pulsante reset della scheda) se il programma non si avvia automaticamente quando ci si connette.

#### Visualizzare il dispositivo registrato e i dati

7. Tornare al dashboard della soluzione di monitoraggio remoto. Nell'elenco di dispositivi lo stato del dispositivo dovrebbe essere ora impostato su In esecuzione. ![][18]

8. Fare clic sul dashboard per visualizzare i dati. L'esempio è configurato per inviare 50 unità per la temperatura interna, 55 unità per la temperatura esterna e 50 per l'umidità. Si noti che, per impostazione predefinita, il dashboard mostra solo la temperatura e l'umidità.

8. Passare ora alla sezione [Comandare e controllare](#command) per informazioni su come modificare la temperatura sul dispositivo dalla soluzione di monitoraggio remoto.



[6]: ./media/iot-suite-connecting-devices/mbed1.png
[7]: ./media/iot-suite-connecting-devices/mbed2a.png
[8]: ./media/iot-suite-connecting-devices/mbed3a.png
[9]: ./media/iot-suite-connecting-devices/suite6.png
[10]: ./media/iot-suite-connecting-devices/mbed5a.png
[11]: ./media/iot-suite-connecting-devices/mbed6.png
[12]: ./media/iot-suite-connecting-devices/mbed7.png

Per informazioni sulle funzionalità di comando e controllo, andare alla relativa sezione più avanti in questa esercitazione.

## Invio dei dati del dispositivo alla soluzione di monitoraggio remoto con node.js



-   Nel repository azure-iot-sdks trovare i file seguenti: packages.json (in /node/common) e remote\_monitoring.js (in node/device/samples/). Copiarli sul dispositivo e inserirli nella stessa cartella.

- Aprire il file remote-monitoring.js e cercare le variabili seguenti:


   ```
   var deviceID = "[DeviceID]";
   var deviceKey = "[Device Key]";
   var hubName = "[IoT Hub Name]";
   var hubSuffix = "[IoT Hub Suffix i.e azure-devices.net]";
   ```

-  Sostituire "[Device Id]", "[Device Key]" con i dati del dispositivo.

-  Immettere i dati del dispositivo del nome host dell'hub IoT come nome dell'hub IoT e suffisso dell'hub IoT. A questo scopo, è necessario dividerlo nel modo seguente:

   Se il nome host dell'hub IoT è Contoso.azure-devices.net, Contoso sarà il nome dell'hub IoTHub e tutto ciò che viene dopo sarà il suffisso. L'aspetto dovrebbe risultare simile al seguente:


   ```
   var deviceID = "mydevice";
   var deviceKey = "mykey";
   var hubName = "Contoso";
   var hubSuffix = "azure-devices.net";
   ```


- Salvare il file. Eseguire il comando seguente nella cartella di destinazione:

```
npm install
node .
```

3.  Sostituire ciascuna delle variabili con le informazioni raccolte nel passaggio precedente. Salvare le modifiche.


4. Aprire una shell (Linux) o un prompt dei comandi Node.js (Windows) e passare alla cartella **node\\samples**. Eseguire quindi l'applicazione di esempio con il comando seguente:

    ```
    node simple_sample_remotemonitoring.js
    ```

#### Visualizzare il dispositivo e i dati in ingresso

6. Nel portale della soluzione preconfigurata fare clic sulla sezione dei dispositivi per assicurarsi che lo stato del dispositivo sia stato impostato su "In esecuzione" e che sia possibile visualizzare tutti i dati del produttore.

7. Fare clic sul dashboard e selezionare il dispositivo in "Dispositivo da visualizzare". I dati di telemetria dovrebbero essere monitorati nella soluzione di monitoraggio remoto.


## <a name="command"></a>Comandare e controllare il dispositivo dal dashboard

Ora che il dispositivo è connesso e invia dati sulla temperatura generati automaticamente, è possibile comandarlo e controllarlo in modalità remota dall'hub IoT. È possibile implementare più tipi di comandi adatti all'applicazione aziendale. In questo caso, è stata implementata una modifica nella temperatura, come se fosse necessario controllarla dalla soluzione. Per inviare il comando, è necessario:

-  Fare clic sull'ID dispositivo nell'elenco dei dispositivi. La sezione dei dispositivi si trova nel menu a sinistra.

    ![][13]

- Nel menu a destra, dove vengono mostrati i dettagli dei dispositivi, fare clic su "Invia comando"


- Selezionare il comando da eseguire: In questo caso scegliere "Set temperature", perché si vuole modificare la temperatura su cui è impostato il dispositivo. Selezionare il comando e scegliere il valore della temperatura. Fare clic su Invia comando per inviare la nuova temperatura al dispositivo. Nota: è possibile osservare che nella cronologia dei comandi il risultato del comando è "In sospeso". Infatti, per semplicità, questo esempio non ha implementato alcuna logica nel dispositivo per rispondere all'hub IoT. A tale scopo, è possibile estendere la soluzione.

    ![][14]

- Tornare al dashboard e verificare che i dati aggiornati vengano visualizzati. Dovrebbero essere visualizzate le statistiche aggiornate sulla temperatura e i nuovi dati dovrebbero essere inclusi nella cronologia della telemetria.




[13]: ./media/iot-suite-connecting-devices/suite4.png
[14]: ./media/iot-suite-connecting-devices/suite7-1.png
[15]: ./media/iot-suite-connecting-devices/suite8a.png
[16]: ./media/iot-suite-connecting-devices/mbed4a.png
[17]: ./media/iot-suite-connecting-devices/suite9.png
[18]: ./media/iot-suite-connecting-devices/suite10.png


## Passaggi successivi

Esistono diversi modi per estendere la funzionalità di questo esempio: connettere un vero sensore al dispositivo per inviare dati reali, implementare la funzionalità di comando e controllo e così via. Per altre informazioni in proposito, usare la [guida](articles/iot-suite/iot-suite-guidance-on-customizing-preconfigured-solutions.md) su come estendere la soluzione di monitoraggio remoto.

<!---HONumber=Nov15_HO3-->