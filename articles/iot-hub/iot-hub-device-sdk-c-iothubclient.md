<properties
	pageTitle="SDK per dispositivi Azure IoT per C: altre informazioni su IoTHubClient | Microsoft Azure"
	description="Ulteriori informazioni sull’uso della libreria IoTHubClient in SDK per dispositivi Azure IoT per C"
	services="iot-hub"
	documentationCenter=""
	authors="olivierbloch"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="05/17/2016"
     ms.author="obloch"/>

# Microsoft Azure IoT device SDK per C: altre informazioni su IoTHubClient

Il [primo articolo](iot-hub-device-sdk-c-intro.md) di questa serie ha introdotto **Microsoft Azure IoT device SDK per C** e spiegato che l'SDK comprende due livelli architetturali. Al livello di base è presente la libreria **IoTHubClient** che gestisce direttamente la comunicazione con l'hub IoT, È inclusa anche libreria **serializer**, che si basa sulla libreria IoTHubClient per fornire i servizi di serializzazione. Questo articolo fornisce dettagli aggiuntivi sulla libreria **IoTHubClient**.

L'articolo precedente descrive come usare la libreria **IoTHubClient** per inviare eventi all'hub IoT e ricevere messaggi. Questo articolo estende la discussione, spiegando come gestire con più precisione *quando* inviare e ricevere dati, introducendo le **API di livello inferiore**. Viene illustrato anche come associare le proprietà agli eventi, e recuperarle dai messaggi, usando le funzionalità di gestione delle proprietà nella libreria **IoTHubClient**. Saranno infine descritti diversi metodi aggiuntivi per gestire i messaggi ricevuti dall'hub IoT.

Questo articolo si conclude con il riepilogo di un paio di vari argomenti, incluse altre informazioni sulle credenziali del dispositivo e come gestire il comportamento della libreria **IoTHubClient** tramite le opzioni di configurazione.

Per illustrare questi argomenti, si useranno esempi dell'SDK relativi a **IoTHubClient**. Se si vuole seguire la procedura, vedere le applicazioni **iothub\_client\_sample\_http** e **iothub\_client\_sample\_amqp** incluse in Azure IoT device SDK per C. Tutto ciò che viene descritto nelle sezioni seguenti è illustrato in questi esempi.

## API di livello inferiore

L'articolo precedente descrive il funzionamento di base di **IotHubClient** nel contesto dell'applicazione **iothub\_client\_sample\_amqp**. Spiega ad esempio come inizializzare la libreria con questo codice.

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Spiega anche come inviare eventi con questa chiamata di funzione.

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

Viene anche descritto come ricevere messaggi registrando una funzione di callback.

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

L'articolo illustra anche come liberare risorse con codice simile al seguente.

```
IoTHubClient_Destroy(iotHubClientHandle);
```

Ci sono tuttavia funzioni complementari per ognuna di queste API:

-   IoTHubClient\_LL\_CreateFromConnectionString

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_Destroy


Il nome dell'API per tutte queste funzioni include "LL". I parametri di ognuna di queste funzioni sono inoltre identici ai rispettivi elementi analoghi non LL. Il comportamento di queste funzioni è tuttavia diverso per un aspetto importante.

Quando si chiama **IoTHubClient\_CreateFromConnectionString**, le librerie sottostanti creano un nuovo thread che viene eseguito in background. Questo thread invia eventi all'hub IoT e ne riceve i messaggi. Questi thread non vengono creati quando si utilizzano le API "LL". La creazione del thread in background è un aspetto pratico per lo sviluppatore. Non occorre preoccuparsi in modo esplicito dell'invio di eventi e della ricezione di messaggi dall'hub IoT, perché tutto avviene automaticamente in background. Al contrario, le API "LL" forniscono il controllo esplicito sulla comunicazione con l'hub IoT, se necessario.

Per comprendere meglio questo aspetto, si esaminerà un esempio:

Quando si chiama **IoTHubClient\_SendEventAsync**, si inserisce in effetti l'evento in un buffer. Il thread in background creato quando si chiama **IoTHubClient\_CreateFromConnectionString** monitora continuamente questo buffer e invia tutti i dati che contiene all'hub IoT. Ciò avviene in background contemporaneamente all'esecuzione di altre attività da parte del thread principale.

In modo analogo, quando si registra una funzione di callback per i messaggi tramite **IoTHubClient\_SetMessageCallback**, si indica all'SDK di fare in modo che il thread in background richiami la funzione di callback quando si riceve un messaggio, indipendentemente dal thread principale.

Le API "LL" non creano un thread in background. È necessario invece chiamare una nuova API per inviare e ricevere esplicitamente i dati dall'hub IoT. Questo approccio è illustrato nell'esempio seguente.

L'applicazione **iothub\_client\_sample\_http** inclusa nell'SDK fornisce una dimostrazione delle API di livello inferiore. In questo esempio, si inviano eventi all'hub IoT con codice simile al seguente:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Le prime tre righe creano il messaggio e l'ultima riga invia l'evento. Tuttavia, come detto in precedenza, "inviare" l'evento significa che i dati vengono semplicemente inseriti in un buffer. Non viene trasmesso nulla in rete quando si chiama **IoTHubClient\_LL\_SendEventAsync**. Per inserire effettivamente i dati nell'hub IoT, è necessario chiamare **IoTHubClient\_LL\_DoWork** come in questo esempio:

```
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Questo codice dall'applicazione **iothub\_client\_sample\_http** chiama ripetutamente **IoTHubClient\_LL\_DoWork**. Ogni volta che si chiama **IoTHubClient\_LL\_DoWork**, invia alcuni eventi dal buffer all'hub IoT e recupera un messaggio in coda inviato al dispositivo. In quest'ultimo caso, se è stata registrata una funzione di callback per i messaggi, verrà richiamato il callback, presupponendo la presenza di messaggi nella coda. Questa funzione di callback sarà stata registrata con codice simile al seguente:

```
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

Il motivo per cui si chiama spesso **IoTHubClient\_LL\_DoWork** in un ciclo, è dovuto al fatto che a ogni chiamata vengono inviati *alcuni* eventi memorizzati nel buffer all'hub IoT e viene recuperato *il successivo* messaggio in coda per il dispositivo. Non è garantito che a ogni chiamata siano inviati tutti gli eventi nel buffer o siano recuperati tutti i messaggi in coda. Se si vuole inviare tutti gli eventi nel buffer e quindi continuare con altre attività di elaborazione, è possibile sostituire questo ciclo con codice simile al seguente:

```
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Questo codice chiama **IoTHubClient\_LL\_DoWork** finché tutti gli eventi non saranno stati inviati all'hub IoT. Non significa però che si siano ricevuti anche tutti i messaggi in coda. Questo comportamento è dovuto in parte al fatto che la verifica di "tutti" i messaggi non è un'azione deterministica. Cosa accadrebbe che si recuperassero "tutti" i messaggi, ma subito dopo ne venisse inviato un altro al dispositivo? Un modo migliore per gestire questo meccanismo consiste nell'usare un timeout programmato. Ad esempio, la funzione di callback dei messaggi potrebbe reimpostare un timer ogni volta che viene richiamata. Si può quindi scrivere la logica per continuare l'elaborazione, ad esempio, non sono stati ricevuti messaggi negli ultimi *X* secondi.

Una volta completato l'inserimento degli eventi e la ricezione dei messaggi, accertarsi di chiamare la funzione corrispondente per pulire le risorse.

```
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Sostanzialmente è disponibile un solo set di API per inviare e ricevere dati con un thread in background e un altro set di API che esegue la stessa operazione senza il thread in background. Molti sviluppatori potrebbero preferire le API non LL, ma le API di livello inferiore sono utili quando lo sviluppatore vuole avere il controllo esplicito delle trasmissioni in rete. Ad esempio, alcuni dispositivi raccolgono dati nel tempo e solo eventi in ingresso a intervalli specificati, come una volta all'ora o una volta al giorno. Le API di livello inferiore consentono di controllare in modo esplicito quando inviare e ricevere dati dall'hub IoT. Altri preferiscono la semplicità offerta dalle API di livello inferiore. Tutto avviene nel thread principale, invece di eseguire una parte delle operazioni in background.

Qualunque sia il modello scelto, assicurarsi di usare le API in modo coerente. Se si inizia a chiamare **IoTHubClient\_LL\_CreateFromConnectionString**, assicurarsi di usare solo le API di livello inferiore corrispondenti per tutte le attività successive:

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_Destroy

-   IoTHubClient\_LL\_DoWork

È vero comunque anche il contrario. Se si inizia con **IoTHubClient\_CreateFromConnectionString**, continuare a usare le API non LL per tutte le attività di elaborazione successive.

Per un esempio completo delle API di livello inferiore, vedere l'applicazione **iothub\_client\_sample\_http** in Azure IoT device SDK per C. Si può fare riferimento all'applicazione **iothub\_client\_sample\_amqp** per un esempio completo delle API non LL.

## Gestione delle proprietà

Fino a questo punto, nel descrivere l'invio di dati si è fatto riferimento al corpo del messaggio. Si consideri ad esempio questo codice:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Questo esempio invia l'evento all'hub IoT con il testo "Hello World". L'hub IoT consente tuttavia di associare proprietà a ogni messaggio. Le proprietà sono coppie nome/valore che possono essere associate al messaggio. Ad esempio, è possibile modificare il codice precedente per associare una proprietà al messaggio.

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Iniziare chiamando **IoTHubMessage\_Properties** e passare l'handle del messaggio. Verrà restituito un riferimento a **MAP\_HANDLE** che consente di iniziare ad aggiungere proprietà. Quest'ultima operazione si esegue chiamando **Map\_AddOrUpdate** che accetta un riferimento a MAP\_HANDLE, il nome della proprietà e il valore della proprietà. Con questa API è possibile aggiungere tutte le proprietà necessarie.

Quando l'evento viene letto da **Hub eventi**, il ricevitore può enumerare le proprietà e recuperare i valori corrispondenti. Ad esempio, in .NET questa operazione verrebbe eseguita con l'accesso alla [raccolta delle proprietà nell'oggetto EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

Nell'esempio precedente le proprietà vengono associate a un evento inviato all'hub IoT. Le proprietà possono anche essere associate ai messaggi ricevuti dall'hub IoT. Per recuperare le proprietà da un messaggio, è possibile usare codice come il seguente nella funzione di callback del messaggio:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

La chiamata a **IoTHubMessage\_Properties** restituisce il riferimento a **MAP\_HANDLE**. Il riferimento viene quindi passato a **Map\_GetInternals** per ottenere un riferimento a una matrice di coppie nome/valore, oltre a un conteggio delle proprietà. A questo punto, si tratta semplicemente di enumerare le proprietà per ottenere i valori necessari.

Non si devono usare proprietà nell'applicazione, ma se è necessario impostarle negli eventi o recuperarle dai messaggi, la libreria **IoTHubClient** facilita l'operazione.

## Gestione dei messaggi

Come spiegato in precedenza, quando arriva un messaggio dall'hub IoT, la libreria **IoTHubClient** risponde richiamando una funzione di callback registrata. Un parametro restituito da questa funzione merita tuttavia qualche spiegazione aggiuntiva. Ecco un estratto della funzione di callback nell'applicazione di esempio **iothub\_client\_sample\_http**:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
	. . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Il tipo restituito è **IOTHUBMESSAGE\_DISPOSITION\_RESULT** e in questo caso specifico verrà restituito **IOTHUBMESSAGE\_ACCEPTED**. Sono presenti altri valori che possono essere restituiti da questa funzione e che modificano il modo in cui la libreria **IoTHubClient** risponde al callback del messaggio. Ecco le opzioni.

-   **IOTHUBMESSAGE\_ACCEPTED**: il messaggio è stato elaborato correttamente. La libreria **IoTHubClient** non richiamerà di nuovo la funzione di callback con lo stesso messaggio.

-   **IOTHUBMESSAGE\_REJECTED**: il messaggio non è stato elaborato e non si prevede di farlo in futuro. La libreria **IoTHubClient** non dovrà richiamare di nuovo la funzione di callback con lo stesso messaggio.

-   **IOTHUBMESSAGE\_ABANDONED**: il messaggio non è stato elaborato, ma la libreria **IoTHubClient** dovrà richiamare di nuovo la funzione di callback con lo stesso messaggio.

Per i primo due codici restituiti la libreria **IoTHubClient** invia un messaggio all'hub IoT indicando che il messaggio dovrà essere eliminato dalla coda del dispositivo e non essere recapitato di nuovo. L'effetto in definitiva è lo stesso, ovvero il messaggio viene eliminato dalla coda del dispositivo, ma rimane ancora la registrazione se il messaggio è stato accettato o rifiutato. La registrazione di questa distinzione è utile per i mittenti del messaggio, che possono rimanere in ascolto dei commenti e scoprire se un dispositivo ha accettato o rifiutato un messaggio specifico.

Nell'ultimo caso, viene inviato un messaggio anche all'hub IoT, ma con l'indicazione che il messaggio dovrà essere recapitato di nuovo. In genere, un messaggio viene abbandonato se si verifica un errore, ma si vuole provare a elaborarlo di nuovo. Al contrario, è opportuno rifiutare un messaggio quando si verifica un errore irreversibile o si decide semplicemente che non si vuole elaborare il messaggio.

In ogni caso, è sufficiente conoscere i diversi codici restituiti per poter dedurre il comportamento che si vuole ottenere dalla libreria **IoTHubClient**.

## Credenziali del dispositivo alternative

Come spiegato in precedenza, la prima cosa da fare quando si utilizza la libreria **IoTHubClient** è ottenere un **IOTHUB\_CLIENT\_HANDLE** con una chiamata come la seguente:

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Gli argomenti di **IoTHubClient\_CreateFromConnectionString** sono la stringa di connessione del dispositivo e un parametro che indica il protocollo da usare per comunicare con l'hub IoT. La stringa di connessione ha un formato simile al seguente:

```
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Questa stringa contiene quattro informazioni: nome dell'hub IoT, suffisso dell'hub IoT, ID dispositivo e chiave di accesso condivisa. Si ottiene il nome di dominio completo (FQDN) di un hub IoT quando si crea l'istanza dell'hub IoT nel portale di Azure. Si avrà così il nome dell'hub IoT (la prima parte dell'FQDN) e il suffisso dell'hub IoT (il resto dell'FQDN). L'ID dispositivo e la chiave di accesso condivisa si ottengono al momento della registrazione del dispositivo con l'hub IoT, come descritto nell'[articolo precedente](iot-hub-device-sdk-c-intro.md).

**IoTHubClient\_CreateFromConnectionString** offre un modo per inizializzare la libreria. Se si preferisce, è possibile creare un nuovo **IOTHUB\_CLIENT\_HANDLE** usando i singoli parametri invece della stringa di connessione. Questo risultato si ottiene con il codice seguente:

```
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Si ottiene lo stesso risultato di **IoTHubClient\_CreateFromConnectionString**.

Può sembrare ovvio che si preferisca usare **IoTHubClient\_CreateFromConnectionString** invece di questo metodo di inizializzazione più dettagliato. Tenere però presente che quando si registra un dispositivo nell'hub IoT, si ottiene un ID dispositivo e una chiave del dispositivo, non una stringa di connessione. Lo strumento dell'SDK **Device Manager** introdotto nell'[articolo precedente](iot-hub-device-sdk-c-intro.md) usa le librerie di **Azure IoT service SDK** per creare la stringa di connessione usando l'ID dispositivo, la chiave del dispositivo e il nome host dell'hub IoT. Può quindi essere preferibile chiamare **IoTHubClient\_LL\_Create**, perché evita il passaggio per la generazione della stringa di connessione. Usare il metodo più pratico.

## Opzioni di configurazione

Fino a questo punto, tutto ciò che è stato illustrato sul funzionamento della libreria **IoTHubClient** riflette il relativo comportamento predefinito. Sono tuttavia disponibili alcune opzioni che si possono impostare per modificare il funzionamento della libreria. Questa operazione viene eseguita sfruttando l'API**IoTHubClient\_LL\_SetOption**. Considerare questo esempio:

```
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Ci sono un paio di opzioni usate comunemente:

-   **SetBatching** (bool): se **true**, i dati destinati all'hub IoT vengono inviati in batch. Se **false**, i messaggi vengono inviati singolarmente. Il valore predefinito è **false**. Si noti che l'opzione **SetBatching** si applica solo al protocollo HTTP e non ai protocolli MQTT o AMQP.

-   **Timeout** (unsigned int): questo valore è rappresentato in millisecondi. Se l'invio di una richiesta HTTP o la ricezione di una risposta richiede più tempo di questo intervallo, si verifica il timeout della connessione.

L'opzione di invio in batch è importante. Per impostazione predefinita, la libreria accetta eventi in ingresso singolarmente. Un singolo evento è qualsiasi elemento passato a **IoTHubClient\_LL\_SendEventAsync**. Se l'opzione di invio in batch è **true**, la libreria raccoglie dal buffer quanti più eventi possibile, fino alle dimensioni massime dei messaggi accettate dall'hub IoT. Il batch di eventi viene inviato all'hub IoT in una singola chiamata HTTP. I singoli elementi vengono aggregati in una matrice JSON. In genere l'abilitazione dell'invio in batch consente di ottenere un miglioramento significativo delle prestazioni, perché si riducono le sequenze di andata e ritorno in rete. Si riduce in modo significativo anche la larghezza di banda, perché si invia un unico set di intestazioni HTTP con un batch di eventi, invece di un set di intestazioni per ogni singolo evento. A meno di avere un motivo specifico per agire diversamente, è consigliabile abilitare l'invio in batch.

## Passaggi successivi

Questo articolo descrive in dettaglio il comportamento della libreria **IoTHubClient** inclusa in **Azure IoT device SDK per C**. Grazie a queste informazioni si dovrebbe avere acquisito una buona conoscenza delle funzionalità della libreria **IoTHubClient**. L'[articolo successivo](iot-hub-device-sdk-c-serializer.md) fornisce dettagli simili sulla libreria **serializer**.

<!---HONumber=AcomDC_0615_2016-->