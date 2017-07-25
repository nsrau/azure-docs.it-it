## <a name="typical-output"></a>Output tipico

L'esempio seguente mostra l'output scritto nel file di log dall'esempio Hello World. L'output è formattato per migliorare la leggibilità:

```json
[{
    "time": "Mon Apr 11 13:48:07 2016",
    "content": "Log started"
}, {
    "time": "Mon Apr 11 13:48:48 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:48:55 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:01 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:04 2016",
    "content": "Log stopped"
}]
```

## <a name="code-snippets"></a>Frammenti di codice

In questa sezione vengono descritte alcune sezioni chiave del codice nell'esempio hello\_world.

### <a name="iot-edge-gateway-creation"></a>Creazione del gateway IoT Edge

È necessario implementare un *processo del gateway*. Questo programma crea l'infrastruttura interna, ovvero il broker, carica i moduli di IoT Edge e configura il processo del gateway. IoT Edge specifica la funzione **Gateway\_Create\_From\_JSON** per consentire di avviare un gateway da un file JSON. Per usare la funzione **Gateway\_Create\_From\_JSON**, chiamarla dal percorso di un file JSON che specifica i moduli di IoT Edge da caricare.

È possibile trovare il codice per il processo del gateway nell'esempio *Hello World* nel file [main.c][lnk-main-c]. Per migliorare la leggibilità, il frammento di codice seguente illustra una versione abbreviata del codice del processo del gateway. Questo programma di esempio crea un gateway e quindi attende che l'utente prema **INVIO** prima di rimuove il gateway.

```c
int main(int argc, char** argv)
{
    GATEWAY_HANDLE gateway;
    if ((gateway = Gateway_Create_From_JSON(argv[1])) == NULL)
    {
        printf("failed to create the gateway from JSON\n");
    }
    else
    {
        printf("gateway successfully created from JSON\n");
        printf("gateway shall run until ENTER is pressed\n");
        (void)getchar();
        Gateway_LL_Destroy(gateway);
    }
    return 0;
}
```

Il file di impostazioni JSON contiene un elenco di moduli di IoT Edge da caricare e i collegamenti tra i moduli. Ogni modulo di IoT Edge deve specificare:

* **name**: nome univoco per il modulo.
* **loader**: un caricatore che riesca a caricare il modulo appropriato. I caricatori sono punti di estensione per il caricamento di tipi diversi di moduli. IoT Edge offre caricatori da usare con moduli scritti in C, Node.js, Java e .NET nativi. L'esempio Hello World usa solo il caricatore C nativo perché tutti i moduli in questo esempio sono librerie dinamiche scritte in C. Per altre informazioni su come usare moduli di IoT Edge scritti in linguaggi diversi, vedere gli esempi [Node.js](https://github.com/Azure/iot-edge/blob/master/samples/nodejs_simple_sample/), [Java](https://github.com/Azure/iot-edge/tree/master/samples/java_sample) o [.NET](https://github.com/Azure/iot-edge/tree/master/samples/dotnet_binding_sample).
    * **name**: nome del caricatore usato per caricare il modulo.
    * **entrypoint**: percorso della libreria che contiene il modulo. In Linux questa libreria è un file con estensione so, in Windows è un file con estensione dll. Il punto di ingresso è specifico per il tipo di caricatore in uso. Il punto di ingresso del caricatore Node.js è un file con estensione js. Il punto di ingresso del caricatore Java è un percorso di classe e un nome di classe. Il punto di ingresso del caricatore .NET è un nome di assembly e un nome di classe.

* **args**: le informazioni di configurazione necessarie per il modulo.

Il codice seguente illustra l'uso di JSON per dichiarare tutti i moduli di IoT Edge per l'esempio Hello World in Linux. L'uso di argomenti nei moduli dipende dalla loro struttura. In questo esempio, il modulo logger include un argomento che specifica il percorso del file di output e il modulo hello\_world non include nessun argomento.

```json
"modules" :
[
    {
        "name" : "logger",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "./modules/logger/liblogger.so"
        }
        },
        "args" : {"filename":"log.txt"}
    },
    {
        "name" : "hello_world",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "./modules/hello_world/libhello_world.so"
        }
        },
        "args" : null
    }
]
```

Il file JSON contiene anche i collegamenti tra i moduli che vengono passati al broker. Un collegamento ha due proprietà:

* **source**: il nome di un modulo dalla sezione `modules` oppure `\*`.
* **sink**: il nome di un modulo dalla sezione `modules`.

Ogni collegamento definisce una route messaggi e una direzione. I messaggi dal modulo **source** vengono recapitati al modulo **sink**. È possibile impostare il modulo **source** su `\*`, che indica che il modulo **sink** riceve messaggi da qualsiasi modulo.

Il codice seguente illustra l'uso di JSON per configurare i collegamenti tra i moduli usati nell'esempio hello\_world in Linux. Tutti i messaggi generati dal modulo `hello_world` vengono utilizzati dal modulo `logger`.

```json
"links":
[
    {
        "source": "hello_world",
        "sink": "logger"
    }
]
```

### <a name="helloworld-module-message-publishing"></a>Pubblicazione dei messaggi del modulo hello\_world

Il codice usato dal modulo hello\_world per pubblicare i messaggi è disponibile nel file ["hello_world.c"][lnk-helloworld-c]. Il frammento di codice seguente riporta una versione modificata, per una maggior leggibilità, in cui sono stati aggiunti commenti ed è stata rimossa parte del codice per la gestione degli errori:

```c
int helloWorldThread(void *param)
{
    // create data structures used in function.
    HELLOWORLD_HANDLE_DATA* handleData = param;
    MESSAGE_CONFIG msgConfig;
    MAP_HANDLE propertiesMap = Map_Create(NULL);

    // add a property named "helloWorld" with a value of "from Azure IoT
    // Gateway SDK simple sample!" to a set of message properties that
    // will be appended to the message before publishing it. 
    Map_AddOrUpdate(propertiesMap, "helloWorld", "from Azure IoT Gateway SDK simple sample!")

    // set the content for the message
    msgConfig.size = strlen(HELLOWORLD_MESSAGE);
    msgConfig.source = HELLOWORLD_MESSAGE;

    // set the properties for the message
    msgConfig.sourceProperties = propertiesMap;

    // create a message based on the msgConfig structure
    MESSAGE_HANDLE helloWorldMessage = Message_Create(&msgConfig);

    while (1)
    {
        if (handleData->stopThread)
        {
            (void)Unlock(handleData->lockHandle);
            break; /*gets out of the thread*/
        }
        else
        {
            // publish the message to the broker
            (void)Broker_Publish(handleData->brokerHandle, helloWorldMessage);
            (void)Unlock(handleData->lockHandle);
        }

        (void)ThreadAPI_Sleep(5000); /*every 5 seconds*/
    }

    Message_Destroy(helloWorldMessage);

    return 0;
}
```

### <a name="helloworld-module-message-processing"></a>Elaborazione dei messaggi del modulo hello\_world

Il modulo hello\_world non elabora mai messaggi pubblicati da altri moduli di IoT Edge nel broker. L'implementazione del callback dei messaggi nel modulo hello\_world è quindi una funzione no-op.

```c
static void HelloWorld_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{
    /* No action, HelloWorld is not interested in any messages. */
}
```

### <a name="logger-module-message-publishing-and-processing"></a>Elaborazione e pubblicazione dei messaggi del modulo di logger

Il modulo logger riceve messaggi dal broker e li scrive in un file, ma non pubblica mai messaggi. Il codice del modulo logger quindi non chiama mai la funzione **Broker_Publish**.

La funzione **Logger_Receive** nel file [logger.c][lnk-logger-c] è il callback che viene richiamato dal broker per recapitare i messaggi al modulo logger. Il frammento di codice seguente riporta una versione modificata, per una maggior leggibilità, in cui sono stati aggiunti commenti ed è stata rimossa parte del codice per la gestione degli errori:

```c
static void Logger_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{

    time_t temp = time(NULL);
    struct tm* t = localtime(&temp);
    char timetemp[80] = { 0 };

    // Get the message properties from the message
    CONSTMAP_HANDLE originalProperties = Message_GetProperties(messageHandle); 
    MAP_HANDLE propertiesAsMap = ConstMap_CloneWriteable(originalProperties);

    // Convert the collection of properties into a JSON string
    STRING_HANDLE jsonProperties = Map_ToJSON(propertiesAsMap);

    //  base64 encode the message content
    const CONSTBUFFER * content = Message_GetContent(messageHandle);
    STRING_HANDLE contentAsJSON = Base64_Encode_Bytes(content->buffer, content->size);

    // Start the construction of the final string to be logged by adding
    // the timestamp
    STRING_HANDLE jsonToBeAppended = STRING_construct(",{\"time\":\"");
    STRING_concat(jsonToBeAppended, timetemp);

    // Add the message properties
    STRING_concat(jsonToBeAppended, "\",\"properties\":"); 
    STRING_concat_with_STRING(jsonToBeAppended, jsonProperties);

    // Add the content
    STRING_concat(jsonToBeAppended, ",\"content\":\"");
    STRING_concat_with_STRING(jsonToBeAppended, contentAsJSON);
    STRING_concat(jsonToBeAppended, "\"}]");

    // Write the formatted string
    LOGGER_HANDLE_DATA *handleData = (LOGGER_HANDLE_DATA *)moduleHandle;
    addJSONString(handleData->fout, STRING_c_str(jsonToBeAppended);
}
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato eseguito un gateway IoT Edge semplice che ha scritto messaggi in un file di log. Per eseguire un esempio che invia messaggi all'hub IoT, vedere [IoT Edge: inviare messaggi da dispositivo a cloud con un dispositivo simulato usando Linux][lnk-gateway-simulated-linux] o [IoT Edge: inviare messaggi da dispositivo a cloud con un dispositivo simulato usando Windows][lnk-gateway-simulated-windows].


<!-- Links -->
[lnk-main-c]: https://github.com/Azure/iot-edge/blob/master/samples/hello_world/src/main.c
[lnk-helloworld-c]: https://github.com/Azure/iot-edge/blob/master/modules/hello_world/src/hello_world.c
[lnk-logger-c]: https://github.com/Azure/iot-edge/blob/master/modules/logger/src/logger.c
[lnk-iot-edge]: https://github.com/Azure/iot-edge/
[lnk-gateway-simulated-linux]: ../articles/iot-hub/iot-hub-linux-iot-edge-simulated-device.md
[lnk-gateway-simulated-windows]: ../articles/iot-hub/iot-hub-windows-iot-edge-simulated-device.md