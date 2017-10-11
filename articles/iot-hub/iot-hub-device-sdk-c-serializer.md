---
title: Azure IoT SDK per dispositivi per C - Serializzatore | Documentazione Microsoft
description: Come usare la libreria Serializer in Azure IoT SDK per dispositivi per C per creare app per dispositivi che comunicano con un hub IoT.
services: iot-hub
documentationcenter: 
author: olivierbloch
manager: timlt
editor: 
ms.assetid: defbed34-de73-429c-8592-cd863a38e4dd
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2016
ms.author: obloch
ms.openlocfilehash: aa03c29c54d75538b1fdf987cac5f09d5d344f73
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="azure-iot-device-sdk-for-c--more-about-serializer"></a>Azure IoT SDK per dispositivi C: altre informazioni sul serializzatore
Il [primo articolo](iot-hub-device-sdk-c-intro.md) di questa serie ha introdotto **Azure IoT SDK per dispositivi per C**. L'articolo successivo fornisce una descrizione più dettagliata di [**IoTHubClient**](iot-hub-device-sdk-c-iothubclient.md). Questo articolo completa l'illustrazione dell'SDK fornendo una descrizione più dettagliata del componente restante, la libreria **serializer**.

L'articolo introduttivo ha descritto come usare la libreria **serializer** per inviare eventi e ricevere messaggi dall'hub IoT. Questo articolo estende la discussione, fornendo una spiegazione più completa della modellazione dei dati con il linguaggio macro **serializer** . Include anche altri dettagli su come vengono serializzati i messaggi da parte della libreria e, in alcuni casi, come è possibile controllare il comportamento della serializzazione. Verranno descritti anche alcuni parametri modificabili che determinano le dimensioni dei modelli creati.

L'articolo si conclude rivedendo alcuni argomenti trattati negli articoli precedenti, quale la gestione dei messaggi e delle proprietà. Come si scoprirà, alcune funzionalità hanno lo stesso comportamento quando si usa sia la libreria **serializer** che la libreria **IoTHubClient**.

Tutto ciò che viene descritto in questo articolo si basa sugli esempi dell'SDK per **serializer** . Se si vuole seguire la procedura, vedere le applicazioni **simplesample\_amqp** e **simplesample\_http** incluse in Azure IoT SDK per dispositivi per C.

È possibile trovare il repository GitHub di [**Azure IoT SDK per dispositivi per C**](https://github.com/Azure/azure-iot-sdk-c) e visualizzare i dettagli dell'API nelle [informazioni di riferimento per l'API C](https://azure.github.io/azure-iot-sdk-c/index.html).

## <a name="the-modeling-language"></a>Linguaggio di modellazione
L'[articolo introduttivo](iot-hub-device-sdk-c-intro.md) di questa serie ha presentato il linguaggio di modellazione di **Azure IoT SDK per dispositivi per C** tramite l'esempio fornito nell'applicazione **simplesample\_amqp**:

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

Come si può notare, il linguaggio di modellazione si basa su macro C. Si inizia sempre la definizione con **BEGIN\_NAMESPACE** e si termina sempre con **END\_NAMESPACE**. È normale denominare lo spazio dei nomi in base alla propria società o, come in questo caso, in base al progetto su cui si sta lavorando.

Nello spazio dei nomi sono incluse le definizioni dei modelli. In questo caso è presente un singolo modello per un anemometro. Al modello, come detto, può essere assegnato un nome qualsiasi, ma in genere si usa un nome che faccia riferimento al dispositivo o al tipo di dati che si vuole scambiare con l'hub IoT.  

I modelli contengono una definizione degli eventi che è possibile inserire nell'hub IoT (*dati*), oltre ai messaggi che si possono ricevere dall'hub IoT (*azioni*). Come si può vedere dall'esempio, gli eventi hanno un tipo e un nome, mentre le azioni hanno un nome e parametri facoltativi, ognuno con un tipo.

In questo esempio non vengono illustrati i tipi di dati aggiuntivi supportati dall'SDK, che saranno trattati in seguito.

> [!NOTE]
> L'hub IoT fa riferimento ai dati inviati da un dispositivo come *eventi*, mentre il linguaggio di modellazione li definisce *dati*, usando **WITH_DATA**. In modo analogo, l'hub IoT fa riferimento ai dati inviati ai dispositivi come *messaggi*, mentre il linguaggio di modellazione li definisce *azioni*, usando **WITH_ACTION**. Tenere presente che questi termini possono essere usati in modo intercambiabile in questo articolo.
> 
> 

### <a name="supported-data-types"></a>Tipi di dati supportati
I tipi di dati seguenti sono supportati nei modelli creati con la libreria **serializer** .

| Tipo | Descrizione |
| --- | --- |
| double |Numero a virgola mobile a precisione doppia |
| int |Intero a 32 bit |
| float |Numero a virgola mobile a precisione singola |
| long |Intero lungo |
| int8\_t |Intero a 8 bit |
| int16\_t |Intero a 16 bit |
| int32\_t |Intero a 32 bit |
| int64\_t |Intero a 64 bit |
| bool |boolean |
| ascii\_char\_ptr |Stringa ASCII |
| EDM\_DATE\_TIME\_OFFSET |Offset data/ora |
| EDM\_GUID |GUID |
| EDM\_BINARY |binary |
| DECLARE\_STRUCT |Tipo di dati complesso |

Si inizierà con l'ultimo tipo di dati. **DECLARE\_STRUCT** consente di definire i tipi di dati complessi, che sono raggruppamenti degli altri tipi primitivi. Questi raggruppamenti consentono di definire un modello simile al seguente:

```
DECLARE_STRUCT(TestType,
double, aDouble,
int, aInt,
float, aFloat,
long, aLong,
int8_t, aInt8,
uint8_t, auInt8,
int16_t, aInt16,
int32_t, aInt32,
int64_t, aInt64,
bool, aBool,
ascii_char_ptr, aAsciiCharPtr,
EDM_DATE_TIME_OFFSET, aDateTimeOffset,
EDM_GUID, aGuid,
EDM_BINARY, aBinary
);

DECLARE_MODEL(TestModel,
WITH_DATA(TestType, Test)
);
```

Il modello contiene un singolo evento dati di tipo **TestType**. **TestType** è un tipo complesso che include diversi membri, i quali dimostrano collettivamente i tipi primitivi supportati dal linguaggio di modellazione **serializer**.

Con un modello come questo è possibile scrivere codice per l'invio di dati all'hub IoT con l'aspetto seguente:

```
TestModel* testModel = CREATE_MODEL_INSTANCE(MyThermostat, TestModel);

testModel->Test.aDouble = 1.1;
testModel->Test.aInt = 2;
testModel->Test.aFloat = 3.0f;
testModel->Test.aLong = 4;
testModel->Test.aInt8 = 5;
testModel->Test.auInt8 = 6;
testModel->Test.aInt16 = 7;
testModel->Test.aInt32 = 8;
testModel->Test.aInt64 = 9;
testModel->Test.aBool = true;
testModel->Test.aAsciiCharPtr = "ascii string 1";

time_t now;
time(&now);
testModel->Test.aDateTimeOffset = GetDateTimeOffset(now);

EDM_GUID guid = { { 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F } };
testModel->Test.aGuid = guid;

unsigned char binaryArray[3] = { 0x01, 0x02, 0x03 };
EDM_BINARY binaryData = { sizeof(binaryArray), &binaryArray };
testModel->Test.aBinary = binaryData;

SendAsync(iotHubClientHandle, (const void*)&(testModel->Test));
```

Sostanzialmente, si assegna un valore a ogni membro della struttura **Test** e quindi si chiama **SendAsync** per inviare l'evento dati **Test** nel cloud. **SendAsync** è una funzione helper che invia un singolo evento dati all'hub IoT:

```
void SendAsync(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const void *dataEvent)
{
    unsigned char* destination;
    size_t destinationSize;
    if (SERIALIZE(&destination, &destinationSize, *(const unsigned char*)dataEvent) ==
    {
        // null terminate the string
        char* destinationAsString = (char*)malloc(destinationSize + 1);
        if (destinationAsString != NULL)
        {
            memcpy(destinationAsString, destination, destinationSize);
            destinationAsString[destinationSize] = '\0';
            IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromString(destinationAsString);
            if (messageHandle != NULL)
            {
                IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)0);

                IoTHubMessage_Destroy(messageHandle);
            }
            free(destinationAsString);
        }
        free(destination);
    }
}
```

Questa funzione serializza l'evento dati specificato e lo invia all'hub IoT tramite **IoTHubClient\_SendEventAsync**. È lo stesso codice descritto negli articoli precedenti,**SendAsync** incapsula la logica in una funzione pratica.

Un'altra funzione helper usata nel codice precedente è **GetDateTimeOffset**. Questa funzione trasforma l'ora specificata in un valore di tipo **EDM\_DATE\_TIME\_OFFSET**:

```
EDM_DATE_TIME_OFFSET GetDateTimeOffset(time_t time)
{
    struct tm newTime;
    gmtime_s(&newTime, &time);
    EDM_DATE_TIME_OFFSET dateTimeOffset;
    dateTimeOffset.dateTime = newTime;
    dateTimeOffset.fractionalSecond = 0;
    dateTimeOffset.hasFractionalSecond = 0;
    dateTimeOffset.hasTimeZone = 0;
    dateTimeOffset.timeZoneHour = 0;
    dateTimeOffset.timeZoneMinute = 0;
    return dateTimeOffset;
}
```

Se si esegue questo codice, viene inviato il messaggio seguente all'hub IoT:

```
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Si noti che la serializzazione è in JSON, il formato generato dalla libreria **serializer** . Si noti anche che ogni membro dell'oggetto JSON serializzato corrisponde ai membri di **TestType** definiti nel modello. Anche i valori corrispondono esattamente a quelli usati nel codice. Notare tuttavia che i dati binari sono codificati in Base 64: "AQID" è la codifica Base 64 di {0x01, 0x02, 0x03}.

Questo esempio dimostra il vantaggio che deriva dall'uso della libreria **serializer** che abilita l'invio di codice JSON nel cloud, senza doversi occupare esplicitamente della serializzazione nell'applicazione. Tutto ciò di cui ci si deve occupare è l'impostazione dei valori degli eventi dati nel modello e la successiva chiamata di API semplici per inviare tali eventi nel cloud.

Con queste informazioni è possibile definire modelli che includono la gamma di tipi di dati supportati, compresi i tipi complessi. Volendo, si potrebbero anche includere tipi complessi in altri tipi complessi. Il codice JSON generato dall'esempio precedente solleva tuttavia un punto importante. *Come* si inviano dati con la libreria **serializer** determina esattamente come viene formato JSON. Di seguito viene trattato questo punto specifico.

## <a name="more-about-serialization"></a>Altre informazioni sulla serializzazione
La sezione precedente evidenzia un esempio dell'output generato dalla libreria **serializer** . Questa sezione descrive in che modo la libreria serializza i dati e come si può controllare questo comportamento tramite le API di serializzazione.

Per procedere nella discussione sulla serializzazione, si utilizzerà un nuovo modello basato su un termostato. Prima di tutto verrà fornito un insieme di informazioni di base sullo scenario che si proverà a usare.

Si vuole creare il modello di un termostato che misuri la temperatura e l'umidità. I singoli dati saranno inviati all'hub IoT in modo diverso. Per impostazione predefinita, il termostato immette un evento temperatura ogni 2 minuti e un evento umidità ogni 15 minuti. Con l'evento immesso dovrà essere incluso un timestamp che indichi l'ora in cui è stata misurata la temperatura o l'umidità corrispondente.

Dato lo scenario, verranno illustrati due diversi metodi per modellare i dati e verrà spiegato l'effetto della modellazione sull'output serializzato.

### <a name="model-1"></a>Modello 1
Ecco la prima versione di un modello che supporta lo scenario precedente:

```
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(TemperatureEvent,
int, Temperature,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_STRUCT(HumidityEvent,
int, Humidity,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureEvent, Temperature),
WITH_DATA(HumidityEvent, Humidity)
);

END_NAMESPACE(Contoso);
```

Il modello include due eventi dati: **Temperature** e **Humidity**. A differenza degli esempi precedenti, il tipo di ogni evento è una struttura definita tramite **DECLARE\_STRUCT**. **TemperatureEvent** include una misura della temperatura e un timestamp. **HumidityEvent** contiene una misura dell'umidità e un timestamp. Questo modello offre un modo naturale per modellare i dati per lo scenario descritto sopra. Quando si invia un evento nel cloud, si invia una coppia temperatura/timestamp o umidità/timestamp.

È possibile inviare un evento temperatura nel cloud usando codice come il seguente:

```
time_t now;
time(&now);
thermostat->Temperature.Temperature = 75;
thermostat->Temperature.Time = GetDateTimeOffset(now);

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Nel codice di esempio si useranno valori hardcoded per temperatura e umidità, ma si immagini di recuperare effettivamente questi valori mediante il campionamento dei sensori corrispondenti nel termostato.

Il codice precedente usa la funzione helper **GetDateTimeOffset** introdotta in precedenza. Per motivi che diventeranno più chiari in seguito, il codice separa in modo esplicito l'attività di serializzazione e di invio dell'evento. Il codice precedente serializza l'evento temperatura in un buffer. Quindi **sendMessage**, una funzione helper inclusa in **simplesample\_amqp**, invia l'evento all'hub IoT:

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId);

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
}
```

Questo codice è un subset della funzione helper **SendAsync** descritta nella sezione precedente, che quindi non verrà illustrata di nuovo.

Quando si esegue il codice precedente per inviare l'evento temperatura, questo formato serializzato dell'evento viene inviato all'hub IoT:

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Viene inviato un evento temperatura di tipo **TemperatureEvent** la cui struttura contiene un membro **Temperature** e **Time**. Questa condizione viene riflessa direttamente dei dati serializzati.

In modo analogo è possibile inviare un evento umidità con questo codice:

```
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Il formato serializzato inviato all'hub IoT è simile al seguente:

```
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Anche in questo caso, è un comportamento previsto.

Con questo modello si può immaginare come sia facile aggiungere altri eventi. Si possono definire altre strutture con **DECLARE\_STRUCT** e includere l'evento corrispondente nel modello tramite **WITH\_DATA**.

Ora si modificherà il modello in modo da includere gli stessi dati, ma con una struttura diversa.

### <a name="model-2"></a>Modello 2
Considerare questo modello come alternativa a quello precedente:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

In questo caso sono state eliminate le macro **DECLARE\_STRUCT** e sono stati semplicemente definiti gli elementi di dati dello scenario usando tipi semplici del linguaggio di modellazione.

Per il momento si ignorerà l'evento **Time** . Ecco quindi il codice per l'immissione dell'evento **Temperature**:

```
time_t now;
time(&now);
thermostat->Temperature = 75;

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Questo codice invia l'evento serializzato seguente all'hub IoT:

```
{"Temperature":75}
```

Il codice per inviare l'evento Humidity è simile al seguente:

```
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Questo codice invia queste informazioni all'hub IoT:

```
{"Humidity":45}
```

Di nuovo nulla di insolito fino a questo punto. Si modificherà ora la modalità d'uso della macro SERIALIZE.

La macro **SERIALIZE** può accettare più eventi dati come argomenti. In questo modo si possono serializzare insieme gli eventi **Temperature** e **Humidity** e inviarli all'hub IoT con una sola chiamata:

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Si potrebbe supporre che il risultato di questo codice sia l'invio di due eventi dati all'hub IoT:

[

{"Temperature":75},

{"Humidity":45}

]

In altre parole, si potrebbe ritenere che questo codice corrisponda all'invio di **Temperature** e **Humidity** separatamente e che passare entrambi gli eventi a **SERIALIZE** nella stessa chiamata sia solo questione di praticità. Non è però così. Al contrario, il codice precedente invia questo singolo evento dati all'hub IoT:

{"Temperature":75, "Humidity":45}

Può sembrare strano perché il modello definisce **Temperature** e **Humidity** come due eventi *distinti*:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Più precisamente, questi eventi non sono stati modellati con **Temperature** e **Humidity** nella stessa struttura:

```
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Se fosse stato usato questo modello, sarebbe stato più facile capire in che modo **Temperature** e **Humidity** sarebbero inviati nello stesso messaggio serializzato. Potrebbe tuttavia non essere chiaro perché funziona in questo modo quando si passano entrambi gli eventi dati a **SERIALIZE** con il modello n. 2.

Questo comportamento è più facile da comprendere se si conoscono i presupposti della libreria **serializer** . Per chiarire questo aspetto, si riesaminerà il modello:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Pensare al modello in termini di "orientato a oggetti". In questo caso si modella un dispositivo fisico (Thermostat), che include attributi come **Temperature** e **Humidity**.

Si può inviare l'intero stato del modello con un codice come il seguente:

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Presupponendo che i valori di Temperature, Humidity e Time siano impostati, si vedrà un evento come questo inviato all'hub IoT:

```
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

A volte è consigliabile inviare solo *alcune* proprietà del modello nel cloud. Questo è vero soprattutto se il modello contiene un numero elevato di eventi dati. È utile inviare solo un subset di eventi dati, come nell'esempio precedente:

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Questo genera esattamente lo stesso evento serializzato come se si fosse definito **TemperatureEvent** con un membro **Temperature** e un membro **Time**, come è stato fatto nel modello n. 1. In questo caso è stato possibile generare esattamente lo stesso evento serializzato usando un modello diverso (modello n. 2), perché la chiamata a **SERIALIZE** è stata eseguita in un modo diverso.

Il punto importante è che se si passano più eventi dati a **SERIALIZE** , presuppone che ogni evento sia una proprietà in un singolo oggetto JSON.

L'approccio migliore dipende dall'utente e da come viene considerato il modello. Se si inviano "eventi" nel cloud e ogni evento contiene un set di proprietà definito, è più appropriato il primo approccio. In tal caso, usare **DECLARE\_STRUCT** per definire la struttura di ogni evento e quindi includere gli eventi nel modello usando la macro **WITH\_DATA**. Inviare quindi ogni evento come illustrato sopra nel primo esempio. Con questo approccio si passerà solo un singolo evento dati a **SERIALIZER**.

Se si considera il modello come se fosse orientato a oggetti, potrebbe essere più appropriato il secondo approccio. In questo caso, gli elementi definiti con **WITH\_DATA** diventano le "proprietà" dell'oggetto. Si passerà il subset di eventi che si preferisce a **SERIALIZE** , a seconda del livello di stato degli "oggetti" che si vuole inviare nel cloud.

Nessun approccio è giusto o sbagliato. Tenere solo presente il funzionamento della libreria **serializer** e scegliere l'approccio di modellazione che meglio soddisfa le proprie esigenze.

## <a name="message-handling"></a>Gestione dei messaggi
Fino ad ora si è discusso solo dell'invio di eventi all'hub IoT, ma non è stata considerata la ricezione dei messaggi. Il motivo è che quello che occorre sapere sulla ricezione dei messaggi è stato ampiamente illustrato in un [articolo precedente](iot-hub-device-sdk-c-intro.md). Tenere presente, come detto in quell'articolo, che i messaggi vengono elaborati registrando una funzione di richiamata dei messaggi:

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

Questa implementazione di **IoTHubMessage** chiama la funzione specifica per ogni azione del modello. Ad esempio, se il modello definisce questa azione:

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

**SetAirResistance** .

Non è stato invece ancora illustrato qual è l'aspetto della versione serializzata del messaggio. In altre parole, se si vuole inviare un messaggio **SetAirResistance** al dispositivo, quale sarà il suo aspetto?

Se si invia un messaggio a un dispositivo, si dovrà usare Azure IoT service SDK. È comunque necessario sapere quale stringa inviare per richiamare un'azione particolare. Il formato generale per l'invio di un messaggio è simile al seguente:

```
{"Name" : "", "Parameters" : "" }
```

Se invia un oggetto JSON serializzato con due proprietà: **Name** è il nome dell'azione, ovvero il messaggio, e **Parameters** contiene i parametri dell'azione.

Ad esempio, per richiamare **SetAirResistance** è possibile inviare questo messaggio a un dispositivo:

```
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

Il nome dell'azione deve corrispondere esattamente a un'azione definita nel modello. Anche i nomi dei parametri devono corrispondere. Tenere presente anche la distinzione tra maiuscole e minuscole. **Name** e **Parameters** sono sempre in maiuscolo. Assicurarsi di rispettare la corrispondenza maiuscole/minuscole per i nomi di azioni e i parametri del modello. In questo esempio, il nome dell'azione è "SetAirResistance" e non "setairresistance".

Le altre due azioni **TurnFanOn** e **TurnFanOff** possono essere richiamate inviando i messaggi seguenti a un dispositivo:

```
{"Name" : "TurnFanOn", "Parameters" : {}}
{"Name" : "TurnFanOff", "Parameters" : {}}
```

Questo descrive tutto ciò che è necessario sapere quando si inviano eventi e si ricevono messaggi con la libreria **serializer** . Prima di proseguire, si esamineranno alcuni parametri che è possibile configurare per controllare le dimensioni del modello.

## <a name="macro-configuration"></a>Configurazione delle macro
Se si usa la libreria **Serializer** , una parte importante dell'SDK che è opportuno tenere presente si trova nella libreria azure-c-shared-utility.
Se è stato clonato il repository Azure-iot-sdk-c da GitHub usando l'opzione ricorsiva, la libreria delle utilità condivise è disponibile qui:

```
.\\c-utility
```

Se la libreria non è stata clonata, è possibile trovarla [qui](https://github.com/Azure/azure-c-shared-utility).

Nella libreria delle utilità condivise, si troverà la seguente cartella:

```
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Questa cartella contiene una soluzione di Visual Studio chiamata **macro\_utils\_h\_generator.sln**:

  ![](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.PNG)

Il programma in questa soluzione genera il file **macro\_utils.h**. L'SDK include un file macro\_utils.h predefinito. Questa soluzione consente tuttavia di modificare alcuni parametri e quindi di ricreare il file di intestazione in base a questi parametri.

I due parametri chiave da considerare sono **nArithmetic** e **nMacroParameters**, definiti in queste due righe del file macro\_utils.tt:

```
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>

```

Questi valori sono i parametri predefiniti inclusi nell'SDK. Ecco il significato di ogni parametro:

* nMacroParameters: controlla la quantità di parametri che possono essere inclusi in una definizione di macro DECLARE\_MODEL.
* nArithmetic: controlla il numero totale di membri consentiti in un modello.

Il motivo per cui questi parametri sono importanti è il fatto che controllano le dimensioni massime del modello. Considerare ad esempio questa definizione di modello:

```
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Come già accennato, **DECLARE\_MODEL** è semplicemente una macro C. I nomi del modello e l'istruzione **WITH\_DATA** (un'altra macro) sono parametri di **DECLARE\_MODEL**. **nMacroParameters** definisce il numero di parametri che può essere incluso in **DECLARE\_MODELLO**. In realtà definisce la quantità di eventi dati e dichiarazioni di azione supportata. Quindi, il limite predefinito di 124 significa che è possibile definire un modello con una combinazione di circa 60 azioni ed eventi dati. Se si prova a superare questo limite, verranno visualizzati errori del compilatore simili al seguente:

  ![](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.PNG)

Il parametro **nArithmetic** riguarda più il funzionamento interno del linguaggio macro che l'applicazione.  Controlla il numero totale dei membri che si possono usare nel modello, incluse le macro **DECLARE_STRUCT**. Se iniziano a essere visualizzati errori del compilatore come questo, provare ad aumentare il valore di **nArithmetic**:

   ![](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.PNG)

Per modificare questi parametri, modificare i valori nel file macro\_utils.tt, ricompilare la soluzione macro\_utils\_h\_generator.sln ed eseguire il programma compilato. In questo caso, verrà generato un nuovo file macro\_utils.h che sarà inserito nella directory .\\common\\inc.

Per usare la nuova versione di macro\_utils.h, rimuovere il pacchetto NuGet **serializer** dalla soluzione e al suo posto includere il progetto di Visual Studio **serializer**. In questo modo si consente la compilazione del codice in base al codice sorgente della libreria serializer, incluso il file macro\_utils.h aggiornato. Se si vuole procedere in questo modo per **simplesample\_amqp**, iniziare rimuovendo il pacchetto NuGet della libreria serializer dalla soluzione:

   ![](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.PNG)

Aggiungere quindi questo progetto alla soluzione Visual Studio:

> .\\c\\serializer\\build\\windows\\serializer.vcxproj
> 
> 

Al termine, la soluzione dovrebbe avere un aspetto analogo al seguente:

   ![](media/iot-hub-device-sdk-c-serializer/05-serializer-project.PNG)

A questo punto quando si compila la soluzione, il file macro\_utils.h aggiornato viene incluso nel file binario.

Se si aumentano molto questi valori, potrebbero essere superati i limiti del compilatore. A questo proposito, **nMacroParameters** è il parametro principale a cui prestare attenzione. La specifica C99 definisce che è consentito un minimo di 127 parametri in una definizione di macro. Il compilatore Microsoft segue esattamente la specifica e ha un limite di 127, quindi non si potrà aumentare **nMacroParameters** oltre il valore predefinito. Altri compilatori potrebbero consentirlo, ad esempio il compilatore GNU supporta un limite più alto.

A questo punto è stato discusso praticamente tutto ciò che occorre sapere sulla scrittura del codice con la libreria **serializer** . Prima di concludere, ecco di seguito alcuni argomenti degli articoli precedenti che potrebbero risultare interessanti.

## <a name="the-lower-level-apis"></a>API di livello inferiore
L'applicazione di esempio illustrata in questo articolo è **simplesample\_amqp**. Questo esempio usa le API di livello superiore (non "LL") per inviare eventi e ricevere messaggi. Se si usano queste API, viene eseguito un thread in background che si occupa dell'invio degli eventi e della ricezione dei messaggi. Queste API (LL) di livello inferiore possono tuttavia essere usate per eliminare il thread in background e assumere il controllo esplicito dei tempi di invio di eventi e ricezione dei messaggi dal cloud.

Come descritto in un [articolo precedente](iot-hub-device-sdk-c-iothubclient.md)è disponibile un set di funzioni costituito dalle API di livello superiore:

* IoTHubClient\_CreateFromConnectionString
* IoTHubClient\_SendEventAsync
* IoTHubClient\_SetMessageCallback
* IoTHubClient\_Destroy

Queste API sono illustrate in **simplesample\_amqp**.

È disponibile anche set analogo di API di livello inferiore.

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy

Tenere presente che le API di livello inferiore funzionano esattamente come descritto negli articoli precedenti. È possibile usare il primo set di API se si vuole avere un thread in background che gestisce l'invio degli eventi e la ricezione dei messaggi. Usare il secondo set di API se si vuole avere il controllo esplicito dei tempi di invio e ricezione dei dati dall'hub IoT. Entrambi i set di API funzionano correttamente con la libreria **serializer** .

Per un esempio dell'uso delle API di livello inferiore con la libreria **serializer**, vedere l'applicazione **simplesample\_http**.

## <a name="additional-topics"></a>Argomenti aggiuntivi
Alcuni altri argomenti che vale la pena citare riguardano la gestione delle proprietà, l'uso di credenziali del dispositivo alternative e le opzioni di configurazione. Ecco tutti gli argomenti illustrati in un [articolo precedente](iot-hub-device-sdk-c-iothubclient.md). L'aspetto principale in questo caso è che tutte queste funzionalità hanno lo stesso comportamento quando si usa sia la libreria **serializer** che la libreria **IoTHubClient**. Ad esempio, per associare proprietà a un evento dal modello, usare **IoTHubMessage\_Properties** e **Map**\_**AddorUpdate** come descritto in precedenza:

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Non ha importanza se l'evento è stato generato dalla libreria **serializer** o creato manualmente con la libreria **IoTHubClient**.

Per quanto riguarda le credenziali del dispositivo alternative, **IoTHubClient\_LL\_Create** funziona altrettanto bene di **IoTHubClient\_CreateFromConnectionString** per l'allocazione di un **IOTHUB\_CLIENT\_HANDLE**.

Infine, se si usa la libreria **serializer**, è possibile impostare le opzioni di configurazione con **IoTHubClient\_LL\_SetOption** esattamente come è stato fatto con la libreria **IoTHubClient**.

Una funzionalità esclusiva nella libreria **serializer** riguarda le API di inizializzazione. Prima di iniziare a utilizzare la libreria, è necessario chiamare **serializer\_init**:

```
serializer_init(NULL);
```

Questa operazione viene eseguita subito prima di chiamare **IoTHubClient\_CreateFromConnectionString**.

In modo analogo, una volta che si è terminato di utilizzare la libreria, l'ultima chiamata da eseguire sarà **serializer\_deinit**:

```
serializer_deinit();
```

Per il resto, tutte le altre funzionalità elencate sopra hanno lo stesso comportamento sia nella libreria **serializer** che nella libreria **IoTHubClient**. Per altre informazioni su uno qualsiasi di questi argomenti, vedere l' [articolo precedente](iot-hub-device-sdk-c-iothubclient.md) di questa serie.

## <a name="next-steps"></a>Passaggi successivi
Questo articolo descrive in dettaglio gli aspetti univoci della libreria **serializer** inclusa in **Azure IoT SDK per dispositivi per C**. Con le informazioni fornite si dovrebbe avere una buona conoscenza di come usare i modelli per inviare eventi e ricevere messaggi dall'hub IoT.

Questo articolo conclude anche la serie in tre parti relativa allo sviluppo di applicazioni con **Azure IoT SDK per dispositivi per C**. Le informazioni dovrebbero essere sufficienti non solo per iniziare, ma anche per avere una conoscenza approfondita del funzionamento delle API. Per altre informazioni, nell'SDK sono disponibili alcuni esempi non illustrati in questo articolo. Anche la [documentazione dell'SDK](https://github.com/Azure/azure-iot-sdk-c) è una risorsa molto utile per altre informazioni.

Per altre informazioni sullo sviluppo dell'hub IoT, vedere gli [Azure IoT SDK][lnk-sdks].

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Simulazione di un dispositivo con Azure IoT Edge][lnk-iotedge]

[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
