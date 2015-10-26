<properties
	pageTitle="Utilizzare l’SDK per dispositivi IoT Azure per C | Microsoft Azure"
	description="Altre informazioni e introduzione all'uso del codice di esempio nel dispositivo Azure IoT SDK per C"
	services="iot-hub"
	documentationCenter=""
	authors="MichelBarnett"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/30/2015"
     ms.author="michelb"/>

# Introduzione ad Azure IoT device SDK per C

**Azure IoT device SDK** è un set di librerie concepite per semplificare l'invio di eventi e la ricezione di messaggi dal servizio **Hub IoT di Azure**. Esistono diverse varianti dell'SDK, ognuna destinata a una piattaforma specifica, ma questo articolo illustrerà **Azure IoT device SDK per C**.

Azure IoT device SDK per C è scritto in ANSI C (C99) per ottimizzare la portabilità. Questo aspetto lo rende particolarmente adatto al funzionamento su numerosi dispositivi e piattaforme, specialmente quando la riduzione del footprint del disco e della memoria costituisce una priorità.

L'SDK è stato testato su un'ampia gamma di piattaforme. Per dettagli vedere la [documentazione dell'SDK](https://github.com/Azure/azure-iot-sdks/tree/master/c). Anche se questo articolo include procedure dettagliate con codice di esempio eseguito sulla piattaforma *Windows*, il codice descritto è esattamente lo stesso per tutta la gamma di applicazioni supportate.

In questo articolo verrà introdotta l'architettura di Azure IoT device SDK per C. Verrà illustrato come inizializzare la libreria di dispositivi, inviare eventi all'hub IoT e riceverne i messaggi. Le informazioni contenute in questo articolo dovrebbero essere sufficienti per iniziare a usare l'SDK, ma forniscono anche puntatori per ottenere informazioni aggiuntive sulle librerie.


## Architettura dell'SDK

Si può trovare **Azure IoT device SDK per C** nel repository GitHub seguente:

[azure-iot-sdks](https://github.com/Azure/azure-iot-sdks)

L'ultima versione delle librerie è disponibile nel ramo **master** di questo repository:

  ![](media/iot-hub-device-sdk-c-intro/01-MasterBranch.PNG)

Il repository include l'intera famiglia di Azure IoT device SDK. Questo articolo riguarda tuttavia Azure IoT device SDK *per C*, disponibile nella cartella **c**.

  ![](media/iot-hub-device-sdk-c-intro/02-CFolder.PNG)

L'implementazione di base dell'SDK si trova nelle cartelle **common**, **iothub\_client** e **serializer** del repository. La cartella **common** include il codice condiviso usato in tutte le librerie, ma in genere non si usa direttamente il codice nella cartella **common**. Le cartelle **iothub\_client** e **serializer** includono comunque le implementazioni dei due livelli distinti dell'SDK che si useranno nel codice.

-   **IoTHubClient**: la cartella **iothub\_client** include l'implementazione del livello API inferiore dell'SDK, la libreria **IoTHubClient**. La libreria **IoTHubClient** include le API per inviare eventi all'hub IoT e per riceverne i messaggi. Se si usa questa libreria, , si dovrà implementare la serializzazione dei messaggi, mente gli altri dettagli della comunicazione con l'hub IoT vengono gestiti automaticamente.

-   **serializer**: la cartella **serializer** include l'implementazione della libreria **serializer**. La libreria **serializer** aggiunge funzionalità di modellazione alle funzionalità fornite da **IoTHubClient**. Se si usa la libreria **serializer**, iniziare definendo un modello che specifica gli eventi da inviare all'hub IoT, oltre ai messaggi che si prevede di ricevere dall'hub stesso. Una volta definito il modello, l'SDK fornisce tuttavia una superficie dell'API che consente di utilizzare facilmente eventi e messaggi senza doversi occupare dei dettagli relativi alla serializzazione.

Tutti questi dettagli sono più facilmente comprensibili osservando alcuni esempi di codice. Le sezioni seguenti illustrano in dettaglio un paio di applicazioni di esempio incluse nell'SDK. Dovrebbero fornire una panoramica delle diverse funzionalità dei livelli architetturali dell'SDK, oltre a un'introduzione al funzionamento delle API.

## Prima di eseguire gli esempi

Prima di poter eseguire gli esempi inclusi in Azure IoT device SDK per C, è necessario completare due attività, cioè preparare l'ambiente di sviluppo e ottenere le credenziali del dispositivo. Il [file readme](https://github.com/Azure/azure-iot-sdks/tree/master/c) incluso nell'SDK fornisce istruzioni per entrambe le attività. Le sezioni seguenti includono tuttavia alcuni commenti aggiuntivi su queste istruzioni.

### Preparazione dell'ambiente di sviluppo

Prima di tutto è necessario ottenere una copia dell'SDK da GitHub e quindi compilare il codice sorgente. È consigliabile recuperare una copia del codice sorgente dal ramo **master** del repository GitHub:

<https://github.com/Azure/azure-iot-sdks>

Dopo aver scaricato una copia del codice sorgente, è necessario completare alcuni passaggi prima di poter eseguire il codice. Nell'articolo sull'SDK che descrive come [preparare l'ambiente di sviluppo](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md) è riportato un riepilogo di questi passaggi. La maggior parte delle attività da eseguire per Windows riguarda la [preparazione delle librerie QPID Proton](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md#preparing-qpid-proton-libraries-in-windows), oltre alla [verifica dell'ambiente](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md#verify-your-environment). Ecco comunque alcuni suggerimenti per completare più facilmente la procedura descritta nella guida alla preparazione:

-   Quando si crea la variabile di ambiente **PROTON\_PATH**, impostarla come variabile di ambiente di **sistema** come illustrato di seguito:

  ![](media/iot-hub-device-sdk-c-intro/07-EnvironmentVariables.PNG)

-   Quando si installa l'utilità **CMake**, scegliere l'opzione per aggiungere **CMake** alla variabile di sistema PATH per **tutti gli utenti**. Funziona correttamente anche l'aggiunta all'**utente corrente**:

  ![](media/iot-hub-device-sdk-c-intro/08-CMake.PNG)

-   Assicurarsi di installare la versione corretta di Python. In Windows è in genere consigliabile installare la versione x86:

  ![](media/iot-hub-device-sdk-c-intro/09-Python.PNG)

È possibile usare le opzioni predefinite del programma di installazione.

- Aggiungere la directory Python alla variabile di ambiente di sistema **PATH**. In Windows la schermata è simile alla seguente: ![](media/iot-hub-device-sdk-c-intro/16-PythonPath.PNG)

-   Prima di aprire **Prompt dei comandi per gli sviluppatori per VS2015**, installare gli *strumenti da riga di comando di Git*. Per installare questi strumenti, completare i passaggi seguenti:

	1. Avviare il programma di installazione di **Visual Studio 2015** oppure scegliere **Microsoft Visual Studio 2015** da **Programmi e funzionalità** nel Pannello di controllo e selezionare **Modifica**.
	2. Assicurarsi che la funzionalità **Git per Windows** sia selezionata nel programma di installazione, ma è anche possibile selezionare l'opzione **Estensione GitHub per Visual Studio** per fornire l'integrazione con l'IDE: ![](media/iot-hub-device-sdk-c-intro/10-GitTools.PNG)
	3. Completare l'installazione guidata per installare gli strumenti.
	4. Aggiungere la directory **bin** degli strumenti Git alla variabile di ambiente di sistema **PATH**. In Windows la schermata è simile alla seguente: ![](media/iot-hub-device-sdk-c-intro/11-GitToolsPath.PNG)

Quando al prompt dei comandi si esegue lo script **build\_proton.cmd**, quest'ultimo recupera il codice sorgente di Proton dal relativo repository GitHub e quindi compila le librerie Proton.

Se si sceglie di eseguire gli esempi MQTT, è consigliabile seguire le istruzioni relative alla [compilazione della libreria MQTT di Apache](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md#building-the-apache-mqtt-library-in-windows) nella documentazione dell'SDK. In ogni caso, l'ultima operazione consiste nell'eseguire **build.cmd**.

Dopo aver completato i passaggi precedenti, si è pronti a compilare le applicazioni di esempio.

### Ottenere le credenziali del dispositivo

Dopo aver configurato l'ambiente di sviluppo, l'ultima cosa da fare è ottenere un set di credenziali del dispositivo. Perché un dispositivo possa accedere all'hub IoT, è necessario aggiungere prima di tutto il dispositivo al registro del dispositivo nell'hub IoT. Quando si aggiunge il dispositivo, si ottiene un set di credenziali del dispositivo, che saranno necessarie per consentire la connessione del dispositivo a un hub IoT. Le applicazioni di esempio che saranno esaminate nella sezione successiva richiedono queste credenziali sotto forma di **stringa di connessione del dispositivo**.

Lo strumento [Device Explorer](https://github.com/Azure/azure-iot-sdks/tree/master/tools/DeviceExplorer), incluso in Azure IoT device SDK, usa le librerie del servizio IoT di Azure per eseguire diverse funzioni nell'hub IoT, inclusa l'aggiunta di dispositivi. Se si usa Device Explorer per aggiungere un dispositivo, si otterrà una stringa di connessione corrispondente che sarà necessaria per eseguire le applicazioni di esempio.

Se non si ha già familiarità con il processo, la procedura seguente descrivo come usare Device Explorer per aggiungere un dispositivo e ottenere una stringa di connessione del dispositivo.

Aprire **[DeviceExplorer.sln](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/DeviceExplorer.sln)** in **Visual Studio 2015** e compilare la soluzione. Durante l'esecuzione del programma verrà visualizzata l'interfaccia seguente:

  ![](media/iot-hub-device-sdk-c-intro/03-DeviceExplorer.PNG)

Immettere la **stringa di connessione all'hub IoT ** nel primo campo e fare clic sul pulsante **Update**. Lo strumento viene configurato per la comunicazione con l'hub IoT.

Dopo la configurazione della stringa di connessione all'hub IoT, fare clic sulla scheda **Management**:

  ![](media/iot-hub-device-sdk-c-intro/04-ManagementTab.PNG)

In questa scheda si gestiranno i dispositivi registrati nell'hub IoT.

È possibile creare un dispositivo facendo clic sul pulsante **Create**. Verrà visualizzata una finestra di dialogo con un set di chiavi, primaria e secondaria, già compilate. È sufficiente immettere un valore in **Device ID** e fare clic sul pulsante **Create**.

  ![](media/iot-hub-device-sdk-c-intro/05-CreateDevice.PNG)

Dopo la creazione del dispositivo, l'elenco Devices viene aggiornato con tutti i dispositivi registrati, incluso quello appena creato. Facendo clic con il pulsante destro del mouse sul nuovo dispositivo, verrà visualizzato questo menu:

  ![](media/iot-hub-device-sdk-c-intro/06-RightClickDevice.PNG)

Se si sceglie l'opzione **Copy connection string for selected device**, la stringa di connessione del dispositivo viene copiata negli Appunti. Conservarne una copia. Sarà necessaria quando si eseguono le applicazioni di esempio descritte di seguito.

Dopo aver completato i passaggi precedenti, si è pronti per iniziare a eseguire il codice. Entrambi gli esempi descritti di seguito hanno una costante all'inizio del file di origine principale che consente di immettere una stringa di connessione. Ecco ad esempio la stringa di connessione dell'applicazione **iothub\_client\_sample\_amqp**.

```
static const char* connectionString = "[device connection string]";
```

Se si vuole seguire la procedura, immettere qui la stringa di connessione del proprio dispositivo e ricompilare la soluzione. Si potrà quindi eseguire l'esempio.

## IoTHubClient

Nella cartella **iothub\_client** all'interno del repository azure-iot-sdks è disponibile una cartella **samples** che contiene un'applicazione chiamata **iothub\_client\_sample\_amqp**.

La versione per Windows dell'applicazione **iothub\_client\_sample\_ampq** include la soluzione di Visual Studio seguente:

  ![](media/iot-hub-device-sdk-c-intro/12-iothub-client-sample-amqp.PNG)

Questa soluzione contiene un singolo progetto. È tuttavia importante notare che in questa soluzione sono installati quattro pacchetti GitHub:

  ![](media/iot-hub-device-sdk-c-intro/17-iothub-client-sample-amqp-githubpackages.PNG)

Il pacchetto **Microsoft.Azure.IoTHub.Common** è sempre necessario quando si utilizza l'SDK. Poiché questo esempio si basa su AMQP, è necessario includere anche i pacchetti **Apache.QPID.Proton.AzureIoT** e **Microsoft.Azure.IoTHub.AmqpTransport**. Sono disponibili pacchetti equivalenti per HTTP. Poiché l'esempio usa la libreria **IoTHubClient**, è necessario includere anche il progetto **Microsoft.Azure.IoTHub.IoTHubClient** nella soluzione.

L'implementazione dell'applicazione di esempio si trova nel file di origine **iothub\_client\_sample\_amqp.c**:

  ![](media/iot-hub-device-sdk-c-intro/13-iothub_client_sample_amqp_c.PNG)

Questa applicazione di esempio sarà usata per illustrare i requisiti per l'uso della libreria **IoTHubClient**.


### Inizializzazione della libreria

Per iniziare a lavorare con le librerie, è necessario allocare prima di tutto un handle del client per l'hub IoT:

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

A questa funzione viene passata una copia della stringa di connessione del dispositivo, ottenuta da Device Explorer. Viene anche designato il protocollo da usare. In questo esempio si usa AMQP, ma è anche possibile scegliere HTTP.

Quando è disponibile uno **IOTHUB\_CLIENT\_HANDLE** valido, si potrà iniziare a chiamare le API per l'invio di eventi e la ricezione di messaggi dall'hub IoT, come si vedrà in seguito.

### Invio di eventi

L'invio di eventi all'hub IoT richiede il completamento dei passaggi seguenti:

Prima di tutto, creare un messaggio:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_Over_AMQP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText);
```

Inviare quindi il messaggio:

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

Ogni volta che si invia un messaggio, si specifica un riferimento a una funzione di callback che viene richiamata quando i dati vengono inviati.

```
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %d with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Si noti la chiamata alla funzione **IoTHubMessage\_Destroy** una volta completato l'invio del messaggio. Questa operazione è necessaria per liberare le risorse allocate quando si crea il messaggio.

### Ricezione di messaggi

La ricezione di un messaggio è un'operazione asincrona. Prima di tutto registrare un callback da richiamare quando il dispositivo riceve un messaggio:

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

L'ultimo parametro è un puntatore nullo a qualsiasi elemento. Nell'esempio è un puntatore a un intero, ma potrebbe essere un puntatore a una struttura dei dati più complessa. Consente alla funzione di callback di operare in uno stato condiviso con il chiamante della funzione.

Quando il dispositivo riceve un messaggio, viene richiamata la funzione di callback registrata:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) == IOTHUB_MESSAGE_OK)
    {
        (void)printf("Received Message [%d] with Data: <<<%.*s>>> & Size=%d\r\n", *counter, (int)size, buffer, (int)size);
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Notare come si usa la funzione **IoTHubMessage\_GetByteArray** per recuperare il messaggio, che in questo esempio è una stringa.

### Annullamento dell'inizializzazione della libreria

Una volta completato l'invio di eventi e la ricezione di messaggi, è possibile annullare l'inizializzazione della libreria IoT usando la chiamata di funzione seguente:

```
IoTHubClient_Destroy(iotHubClientHandle);
```

In questo modo si liberano le risorse allocate in precedenza dalla funzione **IoTHubClient\_CreateFromConnectionString**.

Come si può vedere, è facile inviare eventi e ricevere messaggi con la libreria **IoTHubClient**. La libreria gestisce i dettagli della comunicazione con l'hub IoT, ad esempio il protocollo da usare che, dal punto di vista dello sviluppatore, è una semplice opzione di configurazione.

La libreria **IoTHubClient** offre anche un controllo preciso della modalità di serializzazione degli eventi che il dispositivo invia all'hub IoT. In alcuni casi rappresenta un vantaggio, ma in altri è un dettaglio di implementazione di cui si preferirebbe non occuparsi. In questo caso, è possibile considerare l'uso della libreria **serializer**, che sarà illustrata di seguito.

## Serializer

A livello concettuale, la libreria **serializer** si basa sulla libreria **IoTHubClient** nell'SDK. Usa la libreria **IoTHubClient** per la comunicazione sottostante con l'hub IoT, ma aggiunge anche funzionalità di modellazione che consentono allo sviluppatore di evitare l'impegno di doversi occupare della serializzazione dei messaggi. Il funzionamento di questa libreria può essere illustrato meglio con un esempio.

Nella cartella **serializer** all'interno del repository azure-iot-sdks è disponibile una cartella **samples** che contiene un'applicazione chiamata **simplesample\_amqp**. La versione per Windows di questo esempio include la soluzione di Visual Studio seguente:

  ![](media/iot-hub-device-sdk-c-intro/14-simplesample_amqp.PNG)

Come con l'esempio precedente, questo include diversi pacchetti NuGet:

  ![](media/iot-hub-device-sdk-c-intro/18-simplesample_amqp-githubpackages.PNG)

Quasi tutti sono stati esaminati nell'esempio precedente, ma **Microsoft.Azure.IoTHub.Serializer** è nuovo ed è richiesto quando si usa la libreria **serializer**.

L'implementazione dell'applicazione di esempio si trova nel file di codice **simplesample\_amqp.c**:

  ![](media/iot-hub-device-sdk-c-intro/15-simplesample_amqp_c.PNG)

Le sezioni seguenti illustrano gli elementi chiave di questo esempio.

### Inizializzazione della libreria

Per iniziare a lavorare con la libreria **serializer**, è necessario chiamare le API di inizializzazione:

```
serializer_init(NULL);

IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);

ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
```

La chiamata alla funzione **serializer\_init** è una chiamata singola usata per inizializzare la libreria sottostante. Si chiamerà quindi la funzione **IoTHubClient\_CreateFromConnectionString**, che è la stessa API presente nell'esempio **IoTHubClient**. Questa chiamata imposta la stringa di connessione del dispositivo e consente anche di scegliere il protocollo da usare. Questo esempio usa AMQP come trasporto, ma si sarebbe potuto usare HTTP.

Chiamare infine la funzione **CREATE\_MODEL\_INSTANCE**. Si noti che **WeatherStation** è lo spazio dei nomi del modello e **ContosoAnemometer** è il nome del modello. Una volta creata l'istanza del modello, è possibile usarla per iniziare a inviare eventi e ricevere messaggi. Prima è però importante comprendere che cos'è un modello.

### Definizione del modello

Un modello nella libreria **serializer** definisce gli eventi che il dispositivo può inviare all'hub IoT e i messaggi, chiamati *azioni* nel linguaggio di modellazione, che può ricevere. Per definire un modello, usare un set di macro C come nell'applicazione di esempio **simplesample\_amqp**:

```
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

Le macro **BEGIN\_NAMESPACE** e **END\_NAMESPACE** accettano entrambe lo spazio dei nomi del modello come argomento. Tutto ciò che si trova tra queste macro è la definizione del modello o dei modelli e le strutture dei dati usate dal modello.

In questo esempio è presente un singolo modello chiamato **ContosoAnemometer**. Questo modello definisce due eventi che il dispositivo può inviare all'hub IoT, ovvero **DeviceId** e **WindSpeed**. Definisce anche tre azioni (messaggi) che il dispositivo può ricevere, cioè **TurnFanOn**, **TurnFanOff** e **SetAirResistance**. Ogni evento ha un tipo e ogni azione un nome e facoltativamente un set di parametri.

Gli eventi e le azioni specificati nel modello definiscono una superficie dell'API che si può usare per inviare eventi all'hub IoT e per rispondere ai messaggi inviati al dispositivo. Questo aspetto può essere illustrato meglio con un esempio.

### Invio di eventi

Il modello definisce gli eventi che è possibile inviare all'hub IoT. In questo esempio è uno dei due eventi definiti con la macro **WITH\_DATA**. Ad esempio, se si vuole inviare un evento **WindSpeed** a un hub IoT, è necessario eseguire alcuni passaggi perché ciò avvenga. Il primo consiste nell'impostare i dati da inviare:

```
myWeather->WindSpeed = 15;
```

Il modello definito in precedenza consente di eseguire questa operazione semplicemente impostando un membro di uno struct. Si dovrà quindi serializzare l'evento da inviare:

```
unsigned char* destination;
size_t destinationSize;

SERIALIZE(&destination, &destinationSize, myWeather->WindSpeed);
```

Questo codice serializza l'evento in un buffer, detto **destination**. Infine si invierà l'evento all'hub IoT con questo codice:

```
sendMessage(iotHubClientHandle, destination, destinationSize);
```

Si tratta di una funzione helper nell'applicazione di esempio che invia gli eventi serializzati all'hub IoT:

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
    messageTrackingId++;
}
```

Questo codice è molto simile a quello usato nell'applicazione di esempio **iothub\_client\_sample\_amqp** in cui è stato creato un messaggio da una matrice di byte e dove si è quindi usato **IoTHubClient\_SendEventAsync** per inviarlo all'hub IoT. Si dovranno quindi liberare semplicemente l'handle del messaggio e il buffer dei dati serializzato allocati in precedenza.

Il penultimo parametro di **IoTHubClient\_SendEventAsync** è un riferimento a una funzione di callback chiamata dopo che l'invio dei dati è riuscito. Ecco un esempio di funzione di callback:

```
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    int messageTrackingId = (intptr_t)userContextCallback;

    (void)printf("Message Id: %d Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

Il secondo parametro è un puntatore al contesto utente, lo stesso puntatore passato a **IoTHubClient\_SendEventAsync**. In questo caso il contesto è un semplice contatore, ma potrebbe essere qualsiasi altro elemento.

Questo è tutto ciò che occorre fare per inviare eventi. Rimane solo da illustrare come ricevere i messaggi.

### Ricezione di messaggi

La ricezione di un messaggi funziona in modo simile alla modalità d'uso dei messaggi nella libreria **IoTHubClient**. Prima di tutto occorre registrare la funzione di callback di un messaggio.

```
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Si scrive quindi la funzione di callback che viene richiamata alla ricezione di un messaggio:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy(temp, buffer, size);
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

Questo codice è un boilerplate, è lo stesso per qualsiasi soluzione. La funzione riceve il messaggio e si occupa del routing alla funzione appropriata tramite la chiamata a **EXECUTE\_COMMAND**. Sostanzialmente, la funzione chiamata a questo punto dipende della definizione delle azioni nel modello.

Quando si definisce un'azione nel modello, viene richiesto di implementare una funzione chiamata quando il dispositivo riceve il messaggio corrispondente. Ad esempio, se il modello definisce questa azione:

```
WITH_ACTION(SetAirResistance, int, Position)
```

È necessario definire una funzione con questa firma.

```
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

Si noti che il nome della funzione corrisponde al nome dell'azione nel modello e che i parametri della funzione corrispondono a quelli specificati per l'azione. Il primo parametro è sempre richiesto e contiene un puntatore all'istanza del modello.

Quando il dispositivo riceve un messaggio che corrisponde alla firma, viene chiamata la funzione corrispondente. Quindi, a parte la necessità di includere il codice boilerplate da **IoTHubMessage**, per la ricezione dei messaggi si tratta solo di definire una semplice funzione per ogni azione definita nel modello.

### Annullamento dell'inizializzazione della libreria

Una volta completato l'invio dei dati e la ricezione di messaggi, è possibile annullare l'inizializzazione della libreria IoT:

```
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Ognuna di queste tre funzioni è allineata con le tre funzioni di inizializzazione descritte in precedenza. Chiamando questi API si assicura che siano liberate le risorse allocate in precedenza.

## Passaggi successivi

Questo articolo descrive le nozioni di base relative all'uso delle librerie in **Azure IoT device SDK per C**. Dovrebbe fornire informazioni sufficienti per comprendere il contenuto dell'SDK, la relativa architettura e come iniziare a usare gli esempi per Windows. L'articolo successivo continua la descrizione dell'SDK, fornendo [altre informazioni sulla libreria IoTHubClient](iot-hub-device-sdk-c-iothubclient.md).

<!---HONumber=Oct15_HO3-->