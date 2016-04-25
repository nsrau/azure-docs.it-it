<properties
   pageTitle="Connettere un dispositivo utilizzando C in mbed | Microsoft Azure"
   description="Descrive come connettere un dispositivo alla soluzione di monitoraggio remoto preconfigurata Azure IoT Suite con un’applicazione scritta in C in esecuzione in un dispositivo mbed."
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/05/2016"
   ms.author="dobett"/>


# Connettere il dispositivo alla soluzione preconfigurata per il monitoraggio remoto IoT Suite

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Compilare ed eseguire la soluzione di esempio C in mbed

Le istruzioni seguenti descrivono i passaggi per connettere un dispositivo [Freescale FRDM-K64F abilitato per mbed][lnk-mbed-home] alla soluzione di monitoraggio remoto.

### Connettere il dispositivo alla rete e al computer desktop

1. Connettere il dispositivo mbed alla rete con un cavo Ethernet. Questo passaggio è necessario perché l'applicazione di esempio richiede l'accesso a Internet.

2. Vedere [Guida introduttiva a mbed][lnk-mbed-getstarted] per connettere il dispositivo mbed al PC desktop.

3. Se il PC desktop esegue Windows, vedere [Configurazione PC][lnk-mbed-pcconnect] per configurare l'accesso alla porta seriale al dispositivo mbed.

### Creare un progetto mbed e importare il codice di esempio

1. Nel Web browser passare al [sito per sviluppatori](https://developer.mbed.org/) mbed.org. Se non si è iscritti, verrà visualizzata un'opzione per creare un nuovo account (gratuito). In caso contrario, accedere con le credenziali dell'account. Fare quindi clic su **Compiler** nell'angolo superiore destro della pagina. Si dovrebbe passare all'interfaccia di Workspace Management.

2. Verificare che la piattaforma hardware usata venga visualizzata nell'angolo superiore destro della finestra oppure fare clic sull'icona nell'angolo destro per selezionare la piattaforma hardware.

3. Fare clic su **Import** nel menu principale. Fare quindi clic sul collegamento **Click here** per importare da URL accanto al logo con il mondo di mbed.

    ![][6]

4. Nella finestra popup immettere il collegamento per il codice di esempio https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/ quindi fare clic su **Import**.

    ![][7]

5. Nella finestra del compilatore mbed è possibile osservare che l'importazione del progetto include diverse librerie. Alcune vengono messe a disposizione e gestite dal team IoT di Azure ([azureiot\_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/), [iothub\_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/), [iothub\_amqp\_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/), [azure-uamqp](https://developer.mbed.org/users/AzureIoTClient/code/azure_uamqp/)), altre invece sono librerie di terze parti disponibili nel catalogo delle librerie di mbed.

    ![][8]

6. Aprire il file remote\_monitoring\\remote\_monitoring.c e trovare il codice seguente nel file:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

7. Sostituire [Device Id] e [Device Key] con i dati del dispositivo per abilitare il programma di esempio per la connessione all'hub IoT. Usare il nome host dell'hub IoT Hub per sostituire i segnaposto [IoTHub Name] e [IoTHub Suffix, ovvero azure-devices.net]. Ad esempio, se il nome host dell'hub IoT è contoso.azure-devices.net, contoso sarà **hubName** e tutto ciò che segue sarà **hubSuffix**:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

    ![][9]

### Esaminare il codice

Se si è interessati a conoscere la modalità di funzionamento del programma, in questa sezione vengono descritte alcune parti chiave del codice di esempio. Se invece si vuole solo eseguire il codice, passare direttamente alla sezione [Compilare ed eseguire il programma](#buildandrun).

#### Definizione del modello

In questo esempio viene usata la libreria [per la serializzazione][lnk-serializer] per definire un modello che specifica i messaggi che il dispositivo può scambiare con l'hub IoT. In questo esempio lo spazio dei nomi **Contoso** definisce un modello **Thermostat** che specifica i dati di telemetria **Temperature**, **ExternalTemperature** e **Humidity** insieme con i metadati come ad esempio l'ID e le proprietà del dispositivo e i comandi cui il dispositivo risponde:

```
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(SystemProperties,
    ascii_char_ptr, DeviceID,
    _Bool, Enabled
);

DECLARE_STRUCT(DeviceProperties,
ascii_char_ptr, DeviceID,
_Bool, HubEnabledState
);

DECLARE_MODEL(Thermostat,

    /* Event data (temperature, external temperature and humidity) */
    WITH_DATA(int, Temperature),
    WITH_DATA(int, ExternalTemperature),
    WITH_DATA(int, Humidity),
    WITH_DATA(ascii_char_ptr, DeviceId),

    /* Device Info - This is command metadata + some extra fields */
    WITH_DATA(ascii_char_ptr, ObjectType),
    WITH_DATA(_Bool, IsSimulatedDevice),
    WITH_DATA(ascii_char_ptr, Version),
    WITH_DATA(DeviceProperties, DeviceProperties),
    WITH_DATA(ascii_char_ptr_no_quotes, Commands),

    /* Commands implemented by the device */
    WITH_ACTION(SetTemperature, int, temperature),
    WITH_ACTION(SetHumidity, int, humidity)
);

END_NAMESPACE(Contoso);
```

Alla definizione del modello sono correlate anche le definizioni per i comandi **SetTemperature** e **SetHumidity** cui il dispositivo risponde:

```
EXECUTE_COMMAND_RESULT SetTemperature(Thermostat* thermostat, int temperature)
{
    (void)printf("Received temperature %d\r\n", temperature);
    thermostat->Temperature = temperature;
    return EXECUTE_COMMAND_SUCCESS;
}

EXECUTE_COMMAND_RESULT SetHumidity(Thermostat* thermostat, int humidity)
{
    (void)printf("Received humidity %d\r\n", humidity);
    thermostat->Humidity = humidity;
    return EXECUTE_COMMAND_SUCCESS;
}
```

#### Connettere il modello alla libreria

Le funzioni **sendMessage** e **IoTHubMessage** sono codice boilerplate per l'invio di dati di telemetria dal dispositivo e di messaggi di connessione dall'hub IoT ai gestori del comando.

#### La funzione remote\_monitoring\_run

La funzione **principale** del programma chiama la funzione **remote\_monitoring\_run** quando l'applicazione inizia a eseguire il comportamento del dispositivo come un client di dispositivo dell'hub IoT. La funzione **remote\_monitoring\_run** è costituita principalmente da coppie di funzioni nidificate:

- **platform\_init** e **platform\_deinit** eseguono operazioni di inizializzazione e di arresto specifiche della piattaforma.
- **serializer\_init** e **serializer\_deinit** inizializzano e rimuovono la libreria per la serializzazione.
- **IoTHubClient\_Create** e **IoTHubClient\_Destroy** creano un handle del client, **iotHubClientHandle**, usando le credenziali del dispositivo per la connessione all'hub IoT.

Nella sezione principale della funzione **remote\_monitoring\_run** il programma esegue le operazioni seguenti usando l'handle **iotHubClientHandle**:

- Crea un'istanza del modello termostato Contoso e configura i callback di messaggio per i due comandi.
- Invia all'hub IoT le informazioni sul dispositivo, inclusi i comandi supportati, tramite la libreria per la serializzazione. Quando l'hub riceve questo messaggio, modifica lo stato del dispositivo nel dashboard da **In sospeso** a **In esecuzione**.
- Avvia un ciclo **while** che invia i valori di temperatura, temperatura esterna e umidità all'hub IoT ogni secondo.

Per riferimento, ecco un esempio di messaggio relativo alle **informazioni sul dispositivo** inviato all'hub IoT all'avvio:

```
{
  "ObjectType":"DeviceInfo",
  "Version":"1.0",
  "IsSimulatedDevice":false,
  "DeviceProperties":
  {
    "DeviceID":"mydevice01", "HubEnabledState":true
  }, 
  "Commands":
  [
    {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
    { "Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
  ]
}
```

Per riferimento, ecco un esempio di messaggio relativo alla **telemetria** inviato all'hub IoT:

```
{"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
```

Per riferimento, ecco un esempio di messaggio relativo ai **comandi** ricevuto dall'hub IoT:

```
{
  "Name":"SetHumidity",
  "MessageId":"2f3d3c75-3b77-4832-80ed-a5bb3e233391",
  "CreatedTime":"2016-03-11T15:09:44.2231295Z",
  "Parameters":{"humidity":23}
}
```

<a id="buildandrun"/>
### Compilare ed eseguire il programma

1. Fare clic su **Compila** per compilare il programma. È possibile ignorare eventuali avvisi, ma, se la compilazione genera errori, correggerli prima di continuare.

2. Se la compilazione ha esito positivo, il sito Web del compilatore mbed genera un file .bin con il nome del progetto che viene scaricato nel computer locale. Copiare il file bin sul dispositivo. Il salvataggio del file .bin nel dispositivo causa il riavvio del dispositivo e l'esecuzione del programma contenuto nel file .bin. È possibile riavviare manualmente il programma in qualsiasi momento facendo clic sul pulsante reset sul dispositivo mbed.

3. Connettersi al dispositivo con un'applicazione client SSH, ad esempio PuTTY. È possibile determinare la porta seriale usata dal dispositivo controllando Gestione dispositivi di Windows.

    ![][11]

4. In PuTTY fare clic sul tipo di connessione **Serial**. Poiché il dispositivo si connette in genere a 115200 baud, immettere 115200 nella casella **Velocità**. Fare quindi clic su **Apri**.

5. Inizia l'esecuzione del programma. Potrebbe essere necessario reimpostare la scheda (premere CTRL+INTERR o premere il pulsante reset della scheda) se il programma non si avvia automaticamente quando ci si connette.

    ![][10]

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[9]: ./media/iot-suite-connecting-devices-mbed/suite6.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration
[lnk-serializer]: https://azure.microsoft.com/documentation/articles/iot-hub-device-sdk-c-intro/#serializer

<!---HONumber=AcomDC_0413_2016-->