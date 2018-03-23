---
title: Azure IoT SDK per dispositivi per C | Documentazione Microsoft
description: Introduzione ad Azure IoT SDK per dispositivi per C e informazioni su come creare app per dispositivi in grado di comunicare con un hub IoT.
services: iot-hub
documentationcenter: ''
author: yzhong94
manager: timlt
editor: ''
ms.assetid: e448b061-6bdd-470a-a527-15ec03cca7b9
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: yizhon
ms.openlocfilehash: cc559a19f6d7c31b3bc00a74851f4d42719832de
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="azure-iot-device-sdk-for-c"></a>Azure IoT SDK per dispositivi per C

**Azure IoT SDK per dispositivi** è un set di librerie concepite per semplificare l'invio di messaggi e la ricezione di messaggi nel servizio **Hub IoT di Azure**. Esistono diverse varianti dell'SDK, ognuna destinata a una piattaforma specifica, ma questo articolo illustrerà **Azure IoT device SDK per C**.

Azure IoT device SDK per C è scritto in ANSI C (C99) per ottimizzare la portabilità. Questa funzionalità rende le librerie particolarmente adatte al funzionamento su più dispositivi e piattaforme, specialmente quando la riduzione del footprint del disco e della memoria costituisce una priorità.

Esiste una vasta gamma di piattaforme in cui è stato testato l'SDK. Per altri dettagli, vedere l'[elenco dei dispositivi Azure Certified per IoT](https://catalog.azureiotsuite.com/). Anche se questo articolo include procedure dettagliate di codice di esempio eseguito su piattaforma Windows, il codice descritto è identico per tutta la gamma di piattaforme supportate.

Il video seguente offre una panoramica di Azure IoT SDK per C:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-C-SDK-insights/Player]

Questo articolo introduce l'architettura di Azure IoT SDK per dispositivi per C. Illustra come inizializzare la libreria di dispositivi, inviare dati all'hub IoT e riceverne i messaggi. Le informazioni contenute in questo articolo dovrebbero essere sufficienti per iniziare a usare l'SDK, ma forniscono anche puntatori per ottenere informazioni aggiuntive sulle librerie.

## <a name="sdk-architecture"></a>Architettura dell'SDK

È possibile trovare il repository GitHub di [**Azure IoT SDK per dispositivi per C**](https://github.com/Azure/azure-iot-sdk-c) e visualizzare i dettagli dell'API nelle [informazioni di riferimento per l'API C](https://azure.github.io/azure-iot-sdk-c/index.html).

L'ultima versione delle librerie è disponibile nel ramo **master** del repository:

  ![](media/iot-hub-device-sdk-c-intro/01-MasterBranch.PNG)

* L'implementazione di base dell'SDK è nella cartella **iothub\_client** contenente l'implementazione del livello di API più basso nell'SDK: la libreria **IoTHubClient**. La libreria **IoTHubClient** contiene le API di implementazione di messaggistica non elaborata per inviare all'hub IoT i messaggi e per ricevere i messaggi dall'hub IoT. Quando si usa questa libreria, si dovrà implementare la serializzazione dei messaggi, mente gli altri dettagli della comunicazione con l'hub IoT vengono gestiti automaticamente.
* La cartella **serializer** contiene le funzioni di supporto e gli esempi che mostrano come serializzare i dati prima dell'invio all'hub IoT di Azure tramite la libreria client. L'uso del serializzatore, fornito per comodità, non è obbligatorio. Per usare la libreria **serializer**, si definisce un modello che specifica i dati da inviare all'hub IoT e i messaggi che si prevede di ricevere dall'hub stesso. Dopo la definizione del modello, l'SDK fornisce tuttavia una superficie dell'API che consente di usare facilmente messaggi da dispositivo a cloud e da cloud a dispositivo senza doversi occupare dei dettagli relativi alla serializzazione. La libreria dipende dall'implementazione del trasporto tramite protocolli come MQTT e AMQP da parte di altre librerie open source.
* La libreria **IoTHubClient** dipende da altre librerie open source:
  * La libreria [Azure C shared utility](https://github.com/Azure/azure-c-shared-utility), che offre funzionalità comuni per le attività di base, ad esempio stringhe, manipolazione elenco e I/O, necessarie in diversi SDK per C relativi ad Azure.
  * La libreria [Azure uAMQP](https://github.com/Azure/azure-uamqp-c), che è un'implementazione lato client di AMQP ottimizzata per i dispositivi con vincoli di risorse.
  * La libreria [Azure uMQTT](https://github.com/Azure/azure-umqtt-c), che è una libreria generica per l'implementazione del protocollo MQTT, ottimizzata per i dispositivi con vincoli di risorse.

L'uso di queste librerie è più facilmente comprensibile osservando esempi di codice. Le sezioni seguenti illustrano in dettaglio alcune applicazioni di esempio incluse nell'SDK. Questa procedura guidata offre una panoramica delle diverse funzionalità dei livelli architetturali dell'SDK e un'introduzione al funzionamento delle API.

## <a name="before-you-run-the-samples"></a>Prima di eseguire gli esempi

Prima di poter eseguire gli esempi in Azure IoT SDK per dispositivi per C, è necessario [creare un'istanza del servizio hub IoT](iot-hub-create-through-portal.md) nella sottoscrizione di Azure. Completare quindi le attività seguenti:

* Preparare l'ambiente di sviluppo
* Ottenere le credenziali del dispositivo.

### <a name="prepare-your-development-environment"></a>Preparare l'ambiente di sviluppo

Sono disponibili pacchetti per le piattaforme comuni (ad esempio, NuGet per Windows o apt_get per Debian e Ubuntu), che vengono usati negli esempi. In alcuni casi, è necessario compilare l'SDK per o nel dispositivo. Se è necessario compilare l'SDK, vedere [Prepare your development environment](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) (Preparare l'ambiente di sviluppo) nel repository GitHub.

Per ottenere il codice di applicazione di esempio, scaricare una copia dell'SDK da GitHub. Ottenere la copia del codice sorgente dal ramo **master** del [repository GitHub](https://github.com/Azure/azure-iot-sdk-c).


### <a name="obtain-the-device-credentials"></a>Ottenere le credenziali del dispositivo

Quando il codice sorgente di esempio è disponibile, è necessario ottenere un set di credenziali del dispositivo. Perché un dispositivo possa accedere all'hub IoT, è necessario aggiungere prima di tutto il dispositivo al registro delle identità dell'hub IoT. Quando si aggiunge il dispositivo, si ottiene un set di credenziali del dispositivo, che sono necessarie per consentire la connessione del dispositivo all'hub IoT. Le applicazioni di esempio illustrate nella sezione successiva richiedono queste credenziali sotto forma di **stringa di connessione del dispositivo**.

Esistono diversi strumenti open source che consentono di gestire l'hub IoT.

* Un'applicazione Windows denominata [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).
* Uno strumento dell'interfaccia della riga di comando multipiattaforma di Python denominato [estensione IoT dell'interfaccia della riga di comando di Azure 2.0](https://github.com/Azure/azure-iot-cli-extension).

Questa esercitazione usa lo strumento grafico *Device Explorer*. È anche possibile usare *l'estensione IoT per l'interfaccia della riga di comando di Azure 2.0* se si preferisce uno strumento dell'interfaccia della riga di comando.

Lo strumento Device Explorer usa le librerie del servizio IoT di Azure per eseguire diverse funzioni nell'hub IoT, inclusa l'aggiunta di dispositivi. Se si usa lo strumento Device Explorer per aggiungere un dispositivo, si ottiene una stringa di connessione per il dispositivo, che sarà necessaria per eseguire le applicazioni di esempio.

Se non si ha familiarità con lo strumento Device Explorer, la procedura seguente descrive come usarlo per aggiungere un dispositivo e ottenere una stringa di connessione del dispositivo.

Per installare lo strumento Device Explorer, vedere [How to use the Device Explorer for IoT Hub devices](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) (Come usare Device Explorer per i dispositivi dell'hub IoT).

Durante l'esecuzione del programma, viene visualizzata l'interfaccia seguente:

  ![](media/iot-hub-device-sdk-c-intro/03-DeviceExplorer.PNG)

Immettere la **stringa di connessione all'hub IoT** nel primo campo e fare clic su **Aggiorna**. Questo passaggio configura lo strumento per la comunicazione con l'hub IoT.

Dopo la configurazione della stringa di connessione all'hub IoT, fare clic sulla scheda **Management** (Gestione):

  ![](media/iot-hub-device-sdk-c-intro/04-ManagementTab.PNG)

In questa scheda si gestiscono i dispositivi registrati nell'hub IoT.

Per creare un dispositivo, fare clic sul pulsante **Create** (Crea). Viene visualizzata una finestra di dialogo con un set di chiavi, primaria e secondaria, prepopolato. Immettere un valore in **Device ID** (ID dispositivo) e fare clic su **Create** (Crea).

  ![](media/iot-hub-device-sdk-c-intro/05-CreateDevice.PNG)

Dopo la creazione del dispositivo, l'elenco Devices (Dispositivi) viene aggiornato con tutti i dispositivi registrati, incluso quello appena creato. Facendo clic con il pulsante destro del mouse sul nuovo dispositivo, viene visualizzato questo menu:

  ![](media/iot-hub-device-sdk-c-intro/06-RightClickDevice.PNG)

Se si sceglie **Copy connection string for selected device** (Copia stringa di connessione per il dispositivo selezionato), la stringa di connessione del dispositivo viene copiata negli Appunti. Conservare una copia della stringa di connessione del dispositivo. È necessaria quando si eseguono le applicazioni di esempio descritte nelle sezioni seguenti.

Dopo aver completato i passaggi precedenti, si è pronti per iniziare a eseguire il codice. Entrambi gli esempi hanno una costante all'inizio del file di origine principale che consente di immettere una stringa di connessione. Ecco, ad esempio, la stringa di connessione dell'applicazione **iothub\_client\_sample\_mqtt**.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>Usare la libreria IoTHubClient

Nella cartella **iothub\_client** del repository [azure-iot-sdk-c](https://github.com/azure/azure-iot-sdk-c) è presente una cartella **samples** che contiene un'applicazione denominata **iothub\_client\_sample\_mqtt**.

La versione per Windows dell'applicazione **iothub\_client\_sample\_mqtt** include la soluzione di Visual Studio seguente:

  ![](media/iot-hub-device-sdk-c-intro/12-iothub-client-sample-mqtt.PNG)

> [!NOTE]
> Se si apre questo progetto in Visual Studio 2017, accettare la richiesta di ridestinare il progetto alla versione più recente.

Questa soluzione contiene un singolo progetto. In questa soluzione sono installati quattro pacchetti NuGet:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.umqtt

Il pacchetto **Microsoft.Azure.C.SharedUtility** è sempre necessario quando si usa l'SDK. Questo esempio usa il protocollo MQTT, quindi è necessario includere i pacchetti **Microsoft.Azure.umqtt** e **Microsoft.Azure.IoTHub.MqttTransport**. Sono disponibili pacchetti equivalenti per AMQP e HTTPS. L'esempio usa la libreria **IoTHubClient**, quindi è necessario includere il pacchetto **Microsoft.Azure.IoTHub.IoTHubClient** nella soluzione.

L'implementazione dell'applicazione di esempio si trova nel file di origine **iothub\_client\_sample\_mqtt.c**.

I passaggi seguenti usano questa applicazione di esempio per illustrare i requisiti per l'uso della libreria **IoTHubClient**.

### <a name="initialize-the-library"></a>Inizializzare la libreria

> [!NOTE]
> Prima di iniziare a usare le librerie, è necessario eseguire alcune operazioni di inizializzazione specifiche della piattaforma. Ad esempio, se si prevede di usare AMQP in Linux è necessario inizializzare la libreria OpenSSL. Negli esempi del [repository GitHub](https://github.com/Azure/azure-iot-sdk-c) viene chiamata la funzione di utilità **platform\_init** all'avvio del client e la funzione **platform\_deinit** prima dell'uscita. Queste funzioni sono dichiarate nel file di intestazione platform.h. Esaminare le definizioni di queste funzioni per la piattaforma di destinazione nel [repository](https://github.com/Azure/azure-iot-sdk-c) per determinare se è necessario includere un codice di inizializzazione specifico della piattaforma nel client.

Per iniziare a lavorare con le librerie, allocare prima di tutto un handle del client per l'hub IoT:

```c
if ((iotHubClientHandle = IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol)) == NULL)
{
    (void)printf("ERROR: iotHubClientHandle is NULL!\r\n");
}
else
{
    ...
```

Si passa una copia della stringa di connessione del dispositivo ottenuta dallo strumento Device Explorer a questa funzione. Si stabilisce anche il protocollo da usare per le comunicazioni. In questo esempio si usa MQTT, ma è possibile usare anche AMQP e HTTPS.

Quando è disponibile uno **IOTHUB\_CLIENT\_HANDLE** valido, si potrà iniziare a chiamare le API per l'invio e la ricezione di messaggi nell'hub IoT.

### <a name="send-messages"></a>Inviare messaggi

L'applicazione di esempio configura un ciclo per inviare messaggi all'hub IoT. Il frammento di codice seguente:

- Crea un messaggio.
- Aggiunge una proprietà al messaggio.
- Invia un messaggio.

Prima di tutto, creare un messaggio:

```c
size_t iterator = 0;
do
{
    if (iterator < MESSAGE_COUNT)
    {
        sprintf_s(msgText, sizeof(msgText), "{\"deviceId\":\"myFirstDevice\",\"windSpeed\":%.2f}", avgWindSpeed + (rand() % 4 + 2));
        if ((messages[iterator].messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText))) == NULL)
        {
            (void)printf("ERROR: iotHubMessageHandle is NULL!\r\n");
        }
        else
        {
            messages[iterator].messageTrackingId = iterator;
            MAP_HANDLE propMap = IoTHubMessage_Properties(messages[iterator].messageHandle);
            (void)sprintf_s(propText, sizeof(propText), "PropMsg_%zu", iterator);
            if (Map_AddOrUpdate(propMap, "PropName", propText) != MAP_OK)
            {
                (void)printf("ERROR: Map_AddOrUpdate Failed!\r\n");
            }

            if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messages[iterator].messageHandle, SendConfirmationCallback, &messages[iterator]) != IOTHUB_CLIENT_OK)
            {
                (void)printf("ERROR: IoTHubClient_LL_SendEventAsync..........FAILED!\r\n");
            }
            else
            {
                (void)printf("IoTHubClient_LL_SendEventAsync accepted message [%d] for transmission to IoT Hub.\r\n", (int)iterator);
            }
        }
    }
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1);

    iterator++;
} while (g_continueRunning);
```

Ogni volta che si invia un messaggio, si specifica un riferimento a una funzione di callback che viene richiamata quando i dati vengono inviati. In questo esempio la funzione di callback è denominata **SendConfirmationCallback**. Il frammento seguente illustra questa funzione di callback:

```c
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %zu with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Si noti la chiamata alla funzione **IoTHubMessage\_Destroy** dopo il completamento dell'invio del messaggio. Questa funzione libera le risorse allocate quando si crea il messaggio.

### <a name="receive-messages"></a>Ricevere messaggi

La ricezione di un messaggio è un'operazione asincrona. Prima di tutto registrare il callback da richiamare quando il dispositivo riceve un messaggio:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext) != IOTHUB_CLIENT_OK)
{
    (void)printf("ERROR: IoTHubClient_LL_SetMessageCallback..........FAILED!\r\n");
}
else
{
    (void)printf("IoTHubClient_LL_SetMessageCallback...successful.\r\n");
...
```

L'ultimo parametro è un puntatore nullo a qualsiasi elemento. Nell'esempio è un puntatore a un intero, ma potrebbe essere un puntatore a una struttura dei dati più complessa. Questo parametro consente alla funzione di callback di operare in uno stato condiviso con il chiamante della funzione.

Quando il dispositivo riceve un messaggio, viene richiamata la funzione di callback registrata. Questa funzione di callback recupera:

* L'ID messaggio e l'ID correlazione dal messaggio.
* Il contenuto del messaggio.
* Eventuali proprietà personalizzate dal messaggio.

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    MAP_HANDLE mapProperties;
    const char* messageId;
    const char* correlationId;

    // Message properties
    if ((messageId = IoTHubMessage_GetMessageId(message)) == NULL)
    {
        messageId = "<null>";
    }

    if ((correlationId = IoTHubMessage_GetCorrelationId(message)) == NULL)
    {
        correlationId = "<null>";
    }

    // Message content
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        (void)printf("unable to retrieve the message data\r\n");
    }
    else
    {
        (void)printf("Received Message [%d]\r\n Message ID: %s\r\n Correlation ID: %s\r\n Data: <<<%.*s>>> & Size=%d\r\n", *counter, messageId, correlationId, (int)size, buffer, (int)size);
        // If we receive the work 'quit' then we stop running
        if (size == (strlen("quit") * sizeof(char)) && memcmp(buffer, "quit", size) == 0)
        {
            g_continueRunning = false;
        }
    }

    // Retrieve properties from the message
    mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                size_t index;

                printf(" Message Properties:\r\n");
                for (index = 0; index < propertyCount; index++)
                {
                    (void)printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                (void)printf("\r\n");
            }
        }
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Usare la funzione **IoTHubMessage\_GetByteArray** per recuperare il messaggio, che in questo esempio è una stringa.

### <a name="uninitialize-the-library"></a>Annullare l'inizializzazione della libreria

Al termine dell'invio degli eventi e della ricezione dei messaggi, è possibile annullare l'inizializzazione della libreria IoT. A questo scopo, effettuare la chiamata di funzione seguente:

```
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Questa chiamata libera le risorse allocate in precedenza dalla funzione **IoTHubClient\_CreateFromConnectionString**.

Come si può vedere, è facile inviare e ricevere messaggi con la libreria **IoTHubClient**. La libreria gestisce i dettagli della comunicazione con l'hub IoT, ad esempio il protocollo da usare. Dal punto di vista dello sviluppatore, questa è una semplice opzione di configurazione.

La libreria **IoTHubClient** offre anche un controllo preciso della modalità di serializzazione dei dati che il dispositivo invia all'hub IoT. In alcuni casi questo livello di controllo rappresenta un vantaggio, ma in altri è un dettaglio di implementazione di cui si preferirebbe non occuparsi. In questo caso, è possibile considerare l'uso della libreria **serializer**, che viene illustrata nella sezione successiva.

## <a name="use-the-serializer-library"></a>Usare la libreria serializer

A livello concettuale, la libreria **serializer** si basa sulla libreria **IoTHubClient** nell'SDK. Usa la libreria **IoTHubClient** per la comunicazione sottostante con l'hub IoT, ma aggiunge anche funzionalità di modellazione che consentono allo sviluppatore di evitare l'impegno di doversi occupare della serializzazione dei messaggi. Il funzionamento di questa libreria può essere illustrato meglio con un esempio.

Nella cartella **serializer** del [repository azure-iot-sdk-c](https://github.com/Azure/azure-iot-sdk-c) è presente una cartella **samples** che contiene un'applicazione denominata **simplesample\_mqtt**. La versione per Windows di questo esempio include la soluzione di Visual Studio seguente:

  ![](media/iot-hub-device-sdk-c-intro/14-simplesample_mqtt.PNG)

> [!NOTE]
> Se si apre questo progetto in Visual Studio 2017, accettare la richiesta di ridestinare il progetto alla versione più recente.

Come con l'esempio precedente, questo include diversi pacchetti NuGet:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.IoTHub.Serializer
* Microsoft.Azure.umqtt

La maggior parte di questi pacchetti è stata illustrata nell'esempio precedente, ma **Microsoft.Azure.IoTHub.Serializer** è nuovo. Questo pacchetto è necessario quando si usa la libreria **serializer**.

L'implementazione dell'applicazione di esempio si trova nel file **simplesample\_mqtt.c**.

Le sezioni seguenti illustrano gli elementi chiave di questo esempio.

### <a name="initialize-the-library"></a>Inizializzare la libreria

Per iniziare a lavorare con la libreria **serializer**, chiamare le API di inizializzazione:

```c
if (serializer_init(NULL) != SERIALIZER_OK)
{
    (void)printf("Failed on serializer_init\r\n");
}
else
{
    IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle = IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol);
    srand((unsigned int)time(NULL));
    int avgWindSpeed = 10;

    if (iotHubClientHandle == NULL)
    {
        (void)printf("Failed on IoTHubClient_LL_Create\r\n");
    }
    else
    {
        ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
        if (myWeather == NULL)
        {
            (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
        }
        else
        {
...
```

La chiamata alla funzione **serializer\_init** è una chiamata singola e inizializza la libreria sottostante. Si chiamerà quindi la funzione **IoTHubClient\_LL\_CreateFromConnectionString**, che è la stessa API presente nell'esempio **IoTHubClient**. Questa chiamata imposta la stringa di connessione del dispositivo e consente anche di scegliere il protocollo da usare. Questo esempio usa MQTT come trasporto, ma potrebbe usare AMQP o HTTPS.

Chiamare infine la funzione **CREATE\_MODEL\_INSTANCE**. **WeatherStation** è lo spazio dei nomi del modello e **ContosoAnemometer** è il nome del modello. Dopo la creazione dell'istanza del modello, è possibile usarla per iniziare a inviare e ricevere messaggi. Prima è però importante comprendere che cos'è un modello.

### <a name="define-the-model"></a>Definire il modello

Un modello nella libreria **serializer** definisce i messaggi che il dispositivo può inviare all'hub IoT e i messaggi, chiamati *azioni* nel linguaggio di modellazione, che può ricevere. Per definire un modello, usare un set di macro C come nell'applicazione di esempio **simplesample\_mqtt**:

```c
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(int, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

Le macro **BEGIN\_NAMESPACE** ed **END\_NAMESPACE** accettano entrambe lo spazio dei nomi del modello come argomento. Tutto ciò che si trova tra queste macro è la definizione del modello o dei modelli e le strutture dei dati usate dal modello.

In questo esempio è presente un singolo modello chiamato **ContosoAnemometer**. Questo modello definisce due parti di dati che il dispositivo può inviare all'hub IoT, ovvero **DeviceId** e **WindSpeed**. Definisce anche tre azioni (messaggi) che il dispositivo può ricevere, cioè **TurnFanOn**, **TurnFanOff** e **SetAirResistance**. Ogni elemento dati ha un tipo e ogni azione un nome e facoltativamente un set di parametri.

I dati e le azioni specificati nel modello definiscono una superficie dell'API che si può usare per inviare messaggi all'hub IoT e per rispondere ai messaggi inviati al dispositivo. L'uso di questo modello può essere illustrato meglio con un esempio.

### <a name="send-messages"></a>Inviare messaggi

Il modello definisce i dati che è possibile inviare all'hub IoT. In questo esempio è uno dei due elementi dati definiti con la macro **WITH_DATA**. Sono necessari diversi passaggi per inviare i valori **DeviceId** e **WindSpeed** a un hub IoT. Il primo consiste nell'impostare i dati da inviare:

```c
myWeather->DeviceId = "myFirstDevice";
myWeather->WindSpeed = avgWindSpeed + (rand() % 4 + 2);
```

Il modello definito prima consente di impostare i valori impostando i membri di una **struct**. Serializzare quindi il messaggio che si vuole inviare:

```c
unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, myWeather->DeviceId, myWeather->WindSpeed) != CODEFIRST_OK)
{
    (void)printf("Failed to serialize\r\n");
}
else
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
    free(destination);
}
```

Questo codice serializza il passaggio da dispositivo a cloud in un buffer, detto **destination**. Il codice richiama quindi la funzione **sendMessage** per inviare il messaggio all'hub IoT:

```c
static void sendMessage(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle == NULL)
    {
        printf("unable to create a new IoTHubMessage\r\n");
    }
    else
    {
        if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }
        IoTHubMessage_Destroy(messageHandle);
    }
    messageTrackingId++;
}
```


Il penultimo parametro di **IoTHubClient\_LL\_SendEventAsync** è un riferimento a una funzione di callback chiamata dopo che l'invio dei dati è riuscito. Ecco la funzione di callback nell'esempio:

```c
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    unsigned int messageTrackingId = (unsigned int)(uintptr_t)userContextCallback;

    (void)printf("Message Id: %u Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

Il secondo parametro è un puntatore al contesto utente, lo stesso puntatore passato a **IoTHubClient\_LL\_SendEventAsync**. In questo caso, il contesto è un semplice contatore, ma potrebbe essere qualsiasi altro elemento.

Non sono necessarie altre operazioni per l'invio di messaggi da dispositivo a cloud. Rimane solo da illustrare come ricevere i messaggi.

### <a name="receive-messages"></a>Ricevere messaggi

La ricezione di un messaggi funziona in modo simile alla modalità d'uso dei messaggi nella libreria **IoTHubClient** . Prima di tutto occorre registrare la funzione di callback di un messaggio.

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather) != IOTHUB_CLIENT_OK)
{
    printf("unable to IoTHubClient_SetMessageCallback\r\n");
}
else
{
...
```

Si scrive quindi la funzione di callback che viene richiamata alla ricezione di un messaggio:

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = IOTHUBMESSAGE_ABANDONED;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = IOTHUBMESSAGE_ABANDONED;
        }
        else
        {
            (void)memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

Questo codice è un boilerplate, ovvero è lo stesso per qualsiasi soluzione. La funzione riceve il messaggio e si occupa del routing alla funzione appropriata tramite la chiamata a **EXECUTE\_COMMAND**. La funzione chiamata a questo punto dipende della definizione delle azioni nel modello.

Quando si definisce un'azione nel modello, viene richiesto di implementare una funzione che viene chiamata quando il dispositivo riceve il messaggio corrispondente. Ad esempio, se il modello definisce questa azione:

```c
WITH_ACTION(SetAirResistance, int, Position)
```

Definire una funzione con questa firma:

```c
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

Si noti come il nome della funzione corrisponda al nome dell'azione nel modello e che i parametri della funzione corrispondono a quelli specificati per l'azione. Il primo parametro è sempre obbligatorio e contiene un puntatore all'istanza del modello.

Quando il dispositivo riceve un messaggio che corrisponde alla firma, viene chiamata la funzione corrispondente. Quindi, a parte la necessità di includere il codice boilerplate da **IoTHubMessage**, per la ricezione dei messaggi si tratta solo di definire una semplice funzione per ogni azione definita nel modello.

### <a name="uninitialize-the-library"></a>Annullare l'inizializzazione della libreria

Dopo avere completato l'invio dei dati e la ricezione di messaggi, è possibile annullare l'inizializzazione della libreria IoT:

```c
...
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_LL_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Ognuna di queste tre funzioni è allineata con le tre funzioni di inizializzazione descritte in precedenza. Chiamando questi API si assicura che siano liberate le risorse allocate in precedenza.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo descrive le nozioni di base relative all'uso delle librerie in **Azure IoT SDK per dispositivi per C**. Fornisce informazioni sufficienti per comprendere il contenuto dell'SDK, la relativa architettura e come iniziare a usare gli esempi per Windows. L'articolo successivo continua la descrizione dell'SDK, fornendo [altre informazioni sulla libreria IoTHubClient](iot-hub-device-sdk-c-iothubclient.md).

Per altre informazioni sullo sviluppo dell'hub IoT, vedere gli [Azure IoT SDK][lnk-sdks].

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Distribuzione dell'intelligenza artificiale in dispositivi perimetrali con Azure IoT Edge][lnk-iotedge]

[lnk-file upload]: iot-hub-csharp-csharp-file-upload.md
[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
