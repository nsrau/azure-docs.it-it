---
title: Guida per gli sviluppatori di dispositivi (C)-Plug and Play Microsoft Docs
description: Descrizione delle Plug and Play Internet per gli sviluppatori di dispositivi C
author: rido-min
ms.author: rmpablos
ms.date: 09/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: dda08500ec1f622abdcdc59b2042d66d94600201
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580080"
---
# <a name="iot-plug-and-play-device-developer-guide-c"></a>Guida per gli sviluppatori del dispositivo Plug and Play (C)

[!INCLUDE [iot-pnp-device-devguide-selector.md](../../includes/iot-pnp-device-devguide-selector.md)]

[!INCLUDE [iot-pnp-device-devguide-intro.md](../../includes/iot-pnp-device-devguide-intro.md)]

## <a name="model-id-announcement"></a>Annuncio ID modello

Per annunciare l'ID modello, il dispositivo deve includerlo nelle informazioni di connessione:

```c
static const char g_ThermostatModelId[] = "dtmi:com:example:Thermostat;1";
IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceHandle = NULL;
deviceHandle = CreateDeviceClientLLHandle();
iothubResult = IoTHubDeviceClient_LL_SetOption(
    deviceHandle, OPTION_MODEL_ID, g_ThermostatModelId);
```

> [!TIP]
> Per i moduli e IoT Edge, usare `IoTHubModuleClient_LL` al posto di `IoTHubDeviceClient_LL` .

## <a name="dps-payload"></a>Payload DPS

I dispositivi che usano il [servizio Device provisioning (DPS)](../iot-dps/about-iot-dps.md) possono includere l'oggetto `modelId` da usare durante il processo di provisioning usando il payload JSON seguente.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>Implementare la telemetria, le proprietà e i comandi

Come descritto in [informazioni sui componenti nei modelli plug and Play](concepts-components.md), i generatori di dispositivi devono decidere se vogliono usare i componenti per descrivere i dispositivi. Quando si usano i componenti, i dispositivi devono seguire le regole descritte in questa sezione.

### <a name="telemetry"></a>Telemetria

Un componente predefinito non richiede alcuna proprietà speciale.

Quando si usano i componenti annidati, i dispositivi devono impostare una proprietà del messaggio con il nome del componente:

```c
void PnP_ThermostatComponent_SendTelemetry(
    PNP_THERMOSTAT_COMPONENT_HANDLE pnpThermostatComponentHandle,
    IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceClientLL)
{
    PNP_THERMOSTAT_COMPONENT* pnpThermostatComponent = (PNP_THERMOSTAT_COMPONENT*)pnpThermostatComponentHandle;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
    IOTHUB_CLIENT_RESULT iothubResult;

    char temperatureStringBuffer[32];

    if (snprintf(
        temperatureStringBuffer,
        sizeof(temperatureStringBuffer),
        g_temperatureTelemetryBodyFormat,
        pnpThermostatComponent->currentTemperature) < 0)
    {
        LogError("snprintf of current temperature telemetry failed");
    }
    else if ((messageHandle = PnP_CreateTelemetryMessageHandle(
        pnpThermostatComponent->componentName, temperatureStringBuffer)) == NULL)
    {
        LogError("Unable to create telemetry message");
    }
    else if ((iothubResult = IoTHubDeviceClient_LL_SendEventAsync(
        deviceClientLL, messageHandle, NULL, NULL)) != IOTHUB_CLIENT_OK)
    {
        LogError("Unable to send telemetry message, error=%d", iothubResult);
    }

    IoTHubMessage_Destroy(messageHandle);
}

// ...

PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle1, deviceClient);
```

### <a name="read-only-properties"></a>Proprietà di sola lettura

Per segnalare una proprietà del componente predefinito non è necessario alcun costrutto speciale:

```c
static const char g_maxTemperatureSinceRebootFormat[] = "{\"maxTempSinceLastReboot\":%.2f}";

char maxTemperatureSinceRebootProperty[256];

snprintf(
    maxTemperatureSinceRebootProperty,
    sizeof(maxTemperatureSinceRebootProperty),
    g_maxTemperatureSinceRebootFormat,
    38.7);

IOTHUB_CLIENT_RESULT iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(
    deviceClientLL,
    (const unsigned char*)maxTemperatureSinceRebootProperty,
    strlen(maxTemperatureSinceRebootProperty), NULL, NULL));
```

Il dispositivo gemello viene aggiornato con la proprietà segnalata successiva:

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

Quando si utilizzano i componenti annidati, è necessario creare le proprietà all'interno del nome del componente:

```c
STRING_HANDLE PnP_CreateReportedProperty(
    const char* componentName,
    const char* propertyName,
    const char* propertyValue
)
{
    STRING_HANDLE jsonToSend;

    if (componentName == NULL) 
    {
        jsonToSend = STRING_construct_sprintf(
            "{\"%s\":%s}",
            propertyName, propertyValue);
    }
    else 
    {
       jsonToSend = STRING_construct_sprintf(
            "{\"""%s\":{\"__t\":\"c\",\"%s\":%s}}",
            componentName, propertyName, propertyValue);
    }

    if (jsonToSend == NULL)
    {
        LogError("Unable to allocate JSON buffer");
    }

    return jsonToSend;
}

void PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(
    PNP_THERMOSTAT_COMPONENT_HANDLE pnpThermostatComponentHandle,
    IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceClientLL)
{
    PNP_THERMOSTAT_COMPONENT* pnpThermostatComponent =
        (PNP_THERMOSTAT_COMPONENT*)pnpThermostatComponentHandle;
    char maximumTemperatureAsString[32];
    IOTHUB_CLIENT_RESULT iothubClientResult;
    STRING_HANDLE jsonToSend = NULL;

    if (snprintf(maximumTemperatureAsString, sizeof(maximumTemperatureAsString),
        "%.2f", pnpThermostatComponent->maxTemperature) < 0)
    {
        LogError("Unable to create max temp since last reboot string for reporting result");
    }
    else if ((jsonToSend = PnP_CreateReportedProperty(
                pnpThermostatComponent->componentName,
                g_maxTempSinceLastRebootPropertyName,
                maximumTemperatureAsString)) == NULL)
    {
        LogError("Unable to build max temp since last reboot property");
    }
    else
    {
        const char* jsonToSendStr = STRING_c_str(jsonToSend);
        size_t jsonToSendStrLen = strlen(jsonToSendStr);

        if ((iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(
                deviceClientLL,
                (const unsigned char*)jsonToSendStr,
                jsonToSendStrLen, NULL, NULL)) != IOTHUB_CLIENT_OK)
        {
            LogError("Unable to send reported state, error=%d", iothubClientResult);
        }
        else
        {
            LogInfo("Sending maximumTemperatureSinceLastReboot property to IoTHub for component=%s",
                pnpThermostatComponent->componentName);
        }
    }

    STRING_delete(jsonToSend);
}

// ...

PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle1, deviceClient);
```

Il dispositivo gemello viene aggiornato con la proprietà segnalata successiva:

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTemperature" : 38.7
     }
  }
}
```

### <a name="writable-properties"></a>Proprietà scrivibili

Queste proprietà possono essere impostate dal dispositivo o aggiornate dalla soluzione. Se la soluzione aggiorna una proprietà, il client riceve una notifica come callback in `DeviceClient` o `ModuleClient` . Per seguire le convenzioni Plug and Play, il dispositivo deve informare il servizio che la proprietà è stata ricevuta correttamente.

#### <a name="report-a-writable-property"></a>Segnala una proprietà scrivibile

Quando un dispositivo segnala una proprietà scrivibile, deve includere i `ack` valori definiti nelle convenzioni.

Per segnalare una proprietà scrivibile dal componente predefinito:

```c
IOTHUB_CLIENT_RESULT iothubClientResult;
char targetTemperatureResponseProperty[256];

snprintf(
    targetTemperatureResponseProperty,
    sizeof(targetTemperatureResponseProperty),
    "{\"targetTemperature\":{\"value\":%.2f,\"ac\":%d,\"av\":%d,\"ad\":\"%s\"}}",
    23.2, 200, 3, "Successfully updated target temperature");

iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(
    deviceClientLL,
    (const unsigned char*)targetTemperatureResponseProperty,
    strlen(targetTemperatureResponseProperty), NULL, NULL);
```

Il dispositivo gemello viene aggiornato con la proprietà segnalata successiva:

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "Successfully updated target temperature"
      }
  }
}
```

Per segnalare una proprietà scrivibile da un componente annidato, il dispositivo gemello deve includere un marcatore:

```c
STRING_HANDLE PnP_CreateReportedPropertyWithStatus(const char* componentName,
    const char* propertyName, const char* propertyValue,
    int result, const char* description, int ackVersion
)
{
    STRING_HANDLE jsonToSend;

    if (componentName == NULL) 
    {
        jsonToSend = STRING_construct_sprintf(
            "{\"%s\":{\"value\":%s,\"ac\":%d,\"ad\":\"%s\",\"av\":%d}}",
            propertyName, propertyValue,
            result, description, ackVersion);
    }
    else
    {
       jsonToSend = STRING_construct_sprintf(
            "{\"""%s\":{\"__t\":\"c\",\"%s\":{\"value\":%s,\"ac\":%d,\"ad\":\"%s\",\"av\":%d}}}",
            componentName, propertyName, propertyValue,
            result, description, ackVersion);
    }

    if (jsonToSend == NULL)
    {
        LogError("Unable to allocate JSON buffer");
    }

    return jsonToSend;
}

// ...

char targetTemperatureAsString[32];
IOTHUB_CLIENT_RESULT iothubClientResult;
STRING_HANDLE jsonToSend = NULL;

snprintf(targetTemperatureAsString,
    sizeof(targetTemperatureAsString),
    "%.2f",
    23.2);
jsonToSend = PnP_CreateReportedPropertyWithStatus(
    "thermostat1",
    "targetTemperature",
    targetTemperatureAsString,
    200,
    "complete",
    3);

const char* jsonToSendStr = STRING_c_str(jsonToSend);
size_t jsonToSendStrLen = strlen(jsonToSendStr);

iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(
    deviceClientLL,
    (const unsigned char*)jsonToSendStr,
    jsonToSendStrLen, NULL, NULL);

STRING_delete(jsonToSend);
```

Il dispositivo gemello viene aggiornato con la proprietà segnalata successiva:

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Sottoscrivere gli aggiornamenti delle proprietà desiderate

I servizi possono aggiornare le proprietà desiderate che attivano una notifica nei dispositivi connessi. Questa notifica include le proprietà desiderate aggiornate, incluso il numero di versione che identifica l'aggiornamento. I dispositivi devono rispondere con lo stesso `ack` messaggio delle proprietà segnalate.

Un componente predefinito Visualizza la singola proprietà e crea l'oggetto segnalato `ack` con la versione ricevuta:

```c
static void Thermostat_DeviceTwinCallback(
    DEVICE_TWIN_UPDATE_STATE updateState,
    const unsigned char* payload,
    size_t size,
    void* userContextCallback)
{
    // The device handle associated with this request is passed as the context,
    // since we will need to send reported events back.
    IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceClientLL =
        (IOTHUB_DEVICE_CLIENT_LL_HANDLE)userContextCallback;

    char* jsonStr = NULL;
    JSON_Value* rootValue = NULL;
    JSON_Object* desiredObject;
    JSON_Value* versionValue = NULL;
    JSON_Value* targetTemperatureValue = NULL;

    jsonStr = CopyTwinPayloadToString(payload, size));
    rootValue = json_parse_string(jsonStr));
    desiredObject = GetDesiredJson(updateState, rootValue));
    targetTemperatureValue = json_object_get_value(desiredObject, "targetTemperature"));
    versionValue = json_object_get_value(desiredObject, "$version"));
    json_value_get_type(versionValue);
    json_value_get_type(targetTemperatureValue);

    double targetTemperature = json_value_get_number(targetTemperatureValue);
    int version = (int)json_value_get_number(versionValue);

    // ...

    // The device needs to let the service know that it has received the targetTemperature desired property.
    SendTargetTemperatureReport(deviceClientLL, targetTemperature, 200, version, "Successfully updated target temperature");

    json_value_free(rootValue);
    free(jsonStr);
}

// ...

IOTHUB_CLIENT_RESULT iothubResult;
iothubResult = IoTHubDeviceClient_LL_SetDeviceTwinCallback(
    deviceHandle, Thermostat_DeviceTwinCallback, (void*)deviceHandle))
```

Il dispositivo gemello Mostra la proprietà nelle sezioni desiderate e segnalate:

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "Successfully updated target temperature"
      }
  }
}
```

Un componente annidato riceve le proprietà desiderate di cui è stato eseguito il wrapper con il nome del componente e deve segnalare la `ack` Proprietà segnalata:

```c
bool PnP_ProcessTwinData(
    DEVICE_TWIN_UPDATE_STATE updateState,
    const unsigned char* payload,
    size_t size, const char** componentsInModel,
    size_t numComponentsInModel,
    PnP_PropertyCallbackFunction pnpPropertyCallback,
    void* userContextCallback)
{
    char* jsonStr = NULL;
    JSON_Value* rootValue = NULL;
    JSON_Object* desiredObject;
    bool result;

    jsonStr = PnP_CopyPayloadToString(payload, size));
    rootValue = json_parse_string(jsonStr));
    desiredObject = GetDesiredJson(updateState, rootValue));
    
    result = VisitDesiredObject(
        desiredObject, componentsInModel,
        numComponentsInModel, pnpPropertyCallback,
        userContextCallback);


    json_value_free(rootValue);
    free(jsonStr);

    return result;
}

// ...
static const char g_thermostatComponent1Name[] = "thermostat1";
static const size_t g_thermostatComponent1Size = sizeof(g_thermostatComponent1Name) - 1;
static const char g_thermostatComponent2Name[] = "thermostat2";

static const char* g_modeledComponents[] = {g_thermostatComponent1Name, g_thermostatComponent2Name};
static const size_t g_numModeledComponents = sizeof(g_modeledComponents) / sizeof(g_modeledComponents[0]);

static void PnP_TempControlComponent_DeviceTwinCallback(
    DEVICE_TWIN_UPDATE_STATE updateState,
    const unsigned char* payload,
    size_t size,
    void* userContextCallback
)
{
    PnP_ProcessTwinData(
        updateState, payload,
        size, g_modeledComponents,
        g_numModeledComponents,
        PnP_TempControlComponent_ApplicationPropertyCallback,
        userContextCallback);
}
```

Il dispositivo gemello per un componente annidato Mostra le sezioni desiderate e segnalate come indicato di seguito:

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>Comandi

Un componente predefinito riceve il nome del comando così come è stato richiamato dal servizio.

Un componente annidato riceve il nome del comando preceduto dal nome del componente e dal `*` separatore.

```c
void PnP_ParseCommandName(
    const char* deviceMethodName,
    unsigned const char** componentName,
    size_t* componentNameSize,
    const char** pnpCommandName
)
{
    const char* separator;

    if ((separator = strchr(deviceMethodName, "*")) != NULL)
    {
        *componentName = (unsigned const char*)deviceMethodName;
        *componentNameSize = separator - deviceMethodName;
        *pnpCommandName = separator + 1;
    }
    else
    {
        *componentName = NULL;
        *componentNameSize = 0;
        *pnpCommandName = deviceMethodName;
    }
}

static int PnP_TempControlComponent_DeviceMethodCallback(
    const char* methodName,
    const unsigned char* payload,
    size_t size,
    unsigned char** response,
    size_t* responseSize,
    void* userContextCallback)
{
    (void)userContextCallback;

    char* jsonStr = NULL;
    JSON_Value* rootValue = NULL;
    int result;
    unsigned const char *componentName;
    size_t componentNameSize;
    const char *pnpCommandName;

    *response = NULL;
    *responseSize = 0;

    // Parse the methodName into its componentName and CommandName.
    PnP_ParseCommandName(methodName, &componentName, &componentNameSize, &pnpCommandName);

    // Parse the JSON of the payload request.
    jsonStr = PnP_CopyPayloadToString(payload, size));
    rootValue = json_parse_string(jsonStr));
    if (componentName != NULL)
    {
        if (strncmp((const char*)componentName, g_thermostatComponent1Name, g_thermostatComponent1Size) == 0)
        {
            result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle1, pnpCommandName, rootValue, response, responseSize);
        }
        else if (strncmp((const char*)componentName, g_thermostatComponent2Name, g_thermostatComponent2Size) == 0)
        {
            result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle2, pnpCommandName, rootValue, response, responseSize);
        }
        else
        {
            LogError("PnP component=%.*s is not supported by TemperatureController", (int)componentNameSize, componentName);
            result = PNP_STATUS_NOT_FOUND;
        }
    }
    else
    {
        LogInfo("Received PnP command for TemperatureController component, command=%s", pnpCommandName);
        if (strcmp(pnpCommandName, g_rebootCommand) == 0)
        {
            result = PnP_TempControlComponent_InvokeRebootCommand(rootValue);
        }
        else
        {
            LogError("PnP command=s%s is not supported by TemperatureController", pnpCommandName);
            result = PNP_STATUS_NOT_FOUND;
        }
    }

    if (*response == NULL)
    {
        SetEmptyCommandResponse(response, responseSize, &result);
    }

    json_value_free(rootValue);
    free(jsonStr);

    return result;
}

// ...

PNP_DEVICE_CONFIGURATION g_pnpDeviceConfiguration;
g_pnpDeviceConfiguration.deviceMethodCallback = PnP_TempControlComponent_DeviceMethodCallback;
deviceClient = PnP_CreateDeviceClientLLHandle(&g_pnpDeviceConfiguration);
```

#### <a name="request-and-response-payloads"></a>Payload di richiesta e risposta

I comandi usano i tipi per definire i payload di richiesta e risposta. Un dispositivo deve deserializzare il parametro di input in ingresso e serializzare la risposta. Nell'esempio seguente viene illustrato come implementare un comando con tipi complessi definiti nei payload:

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

Nei frammenti di codice seguenti viene illustrato il modo in cui un dispositivo implementa questa definizione di comando, inclusi i tipi utilizzati per abilitare la serializzazione e la deserializzazione:

```c
static const char g_maxMinCommandResponseFormat[] = "{\"maxTemp\":%.2f,\"minTemp\":%.2f,\"avgTemp\":%.2f,\"startTime\":\"%s\",\"endTime\":\"%s\"}";

// ...

static bool BuildMaxMinCommandResponse(
    PNP_THERMOSTAT_COMPONENT* pnpThermostatComponent,
    unsigned char** response,
    size_t* responseSize)
{
    int responseBuilderSize = 0;
    unsigned char* responseBuilder = NULL;
    bool result;
    char currentTime[TIME_BUFFER_SIZE];

    BuildUtcTimeFromCurrentTime(currentTime, sizeof(currentTime));
    responseBuilderSize = snprintf(NULL, 0, g_maxMinCommandResponseFormat,
        pnpThermostatComponent->maxTemperature,
        pnpThermostatComponent->minTemperature,
        pnpThermostatComponent->allTemperatures /
        pnpThermostatComponent->numTemperatureUpdates,
        g_programStartTime, currentTime));

    responseBuilder = calloc(1, responseBuilderSize + 1));

    responseBuilderSize = snprintf(
        (char*)responseBuilder, responseBuilderSize + 1, g_maxMinCommandResponseFormat,
        pnpThermostatComponent->maxTemperature,
        pnpThermostatComponent->minTemperature,
        pnpThermostatComponent->allTemperatures / pnpThermostatComponent->numTemperatureUpdates,
        g_programStartTime,
        currentTime));

    *response = responseBuilder;
    *responseSize = (size_t)responseBuilderSize;

    return true;
}
```

> [!Tip]
> I nomi di richiesta e risposta non sono presenti nei payload serializzati trasmessi in rete.

[!INCLUDE [iot-pnp-device-devguide-summary.md](../../includes/iot-pnp-device-devguide-summary.md)]
