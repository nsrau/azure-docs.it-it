---
title: Introduzione all'identità del modulo e ai moduli gemelli dell'hub IoT di Azure (C) | Microsoft Docs
description: Come creare l'identità del modulo e aggiornare il modulo gemello usando gli SDK per IoT per C.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 06/25/2018
ms.author: menchi
ms.openlocfilehash: 94c7bd50b9c331370aaac802e0a453c5cdd8b7c1
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2018
ms.locfileid: "42141138"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-c-backend-and-c-device"></a>Introduzione all'identità del modulo e ai moduli gemelli dell'hub IoT usando il back-end C e i dispositivi C

> [!NOTE]
> [Le identità del modulo e i moduli gemelli](iot-hub-devguide-module-twins.md) sono simili alle identità del dispositivo e ai dispositivi gemelli dell'hub IoT di Azure, ma offrono una granularità superiore. Mentre l'identità del dispositivo e il dispositivo gemello dell'hub IoT di Azure consentono all'applicazione back-end di configurare un dispositivo e forniscono visibilità sulle condizioni del dispositivo, l'identità del modulo e il modulo gemello forniscono queste funzionalità per i singoli componenti di un dispositivo. Nei dispositivi con più componenti, ad esempio i dispositivi basati su sistema operativo o i dispositivi firmware, è possibile isolare la configurazione e le condizioni di ogni componente.

Al termine di questa esercitazione si hanno due app C:

* **CreateIdentities**, che crea un'identità del dispositivo, un'identità del modulo e una chiave di sicurezza associata per connettere i client dispositivo e modulo.

* **UpdateModuleTwinReportedProperties**, che invia le proprietà segnalate aggiornate del modulo gemello all'hub IoT.

> [!NOTE]
> Per informazioni sugli Azure IoT SDK che consentono di compilare le applicazioni da eseguire nei dispositivi e i back-end della soluzione, vedere [Azure IoT SDK](iot-hub-devguide-sdks.md).

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non si dispone di un account, è possibile creare un [account Azure gratuito](http://azure.microsoft.com/pricing/free-trial/) in pochi minuti.
* Un hub IoT.
* Versione più recente [dell'SDK C per l'hub IoT di Azure](https://github.com/Azure/azure-iot-sdk-c).

L'hub IoT è stato così creato e sono ora disponibili il nome host e la stringa di connessione dell'hub necessari per completare il resto di questa esercitazione.

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Creare un'identità del dispositivo e un'identità del modulo nell'hub IoT

In questa sezione si scriverà un'app console C che crea un'identità del dispositivo e un'identità del modulo nel registro delle identità dell'hub IoT. Un dispositivo o un modulo non può connettersi all'hub IoT se non ha una voce nel registro delle identità. Per altre informazioni, vedere la sezione **Registro di identità** della [Guida per gli sviluppatori dell'hub IoT](iot-hub-devguide-identity-registry.md). Quando si esegue questa app console vengono generati un ID e una chiave univoci sia per il dispositivo che per il modulo. Il dispositivo e il modulo usano questi valori per identificarsi quando inviano messaggi da dispositivo a cloud all'hub IoT. Negli ID viene fatta distinzione tra maiuscole e minuscole.

Aggiungere il codice seguente al file C:

```C
#include <stdio.h>
#include <stdlib.h>

#include "azure_c_shared_utility/crt_abstractions.h"
#include "azure_c_shared_utility/threadapi.h"
#include "azure_c_shared_utility/platform.h"

#include "iothub_service_client_auth.h"
#include "iothub_registrymanager.h"

static const char* hubConnectionString ="[your hub's connection string]"; // modify

static void createDevice(IOTHUB_REGISTRYMANAGER_HANDLE 
  iotHubRegistryManagerHandle, const char* deviceId)
{
    IOTHUB_REGISTRY_DEVICE_CREATE_EX deviceCreateInfo;
    IOTHUB_REGISTRYMANAGER_RESULT result;
    
    (void)memset(&deviceCreateInfo, 0, sizeof(deviceCreateInfo));
    deviceCreateInfo.version = 1;
    deviceCreateInfo.deviceId = deviceId;
    deviceCreateInfo.primaryKey = "";
    deviceCreateInfo.secondaryKey = "";
    deviceCreateInfo.authMethod = IOTHUB_REGISTRYMANAGER_AUTH_SPK;
    
    IOTHUB_DEVICE_EX deviceInfoEx;
    memset(&deviceInfoEx, 0, sizeof(deviceInfoEx));
    deviceInfoEx.version = 1;
    
    // Create device
    result = IoTHubRegistryManager_CreateDevice_Ex(iotHubRegistryManagerHandle, 
      &deviceCreateInfo, &deviceInfoEx);
    if (result == IOTHUB_REGISTRYMANAGER_OK)
    {
        (void)printf("IoTHubRegistryManager_CreateDevice: Device has been created successfully: deviceId=%s, primaryKey=%s\n", deviceInfoEx.deviceId, deviceInfoEx.primaryKey);
    }
    else if (result == IOTHUB_REGISTRYMANAGER_DEVICE_EXIST)
    {
        (void)printf("IoTHubRegistryManager_CreateDevice: Device already exists\n");
    }
    else if (result == IOTHUB_REGISTRYMANAGER_ERROR)
    {
        (void)printf("IoTHubRegistryManager_CreateDevice failed\n");
    }
    // You will need to Free the returned device information after it was created
    IoTHubRegistryManager_FreeDeviceExMembers(&deviceInfoEx);
}

static void createModule(IOTHUB_REGISTRYMANAGER_HANDLE iotHubRegistryManagerHandle, const char* deviceId, const char* moduleId)
{
    IOTHUB_REGISTRY_MODULE_CREATE moduleCreateInfo;
    IOTHUB_REGISTRYMANAGER_RESULT result;    
    
    (void)memset(&moduleCreateInfo, 0, sizeof(moduleCreateInfo));
    moduleCreateInfo.version = 1;
    moduleCreateInfo.deviceId = deviceId;
    moduleCreateInfo.moduleId = moduleId;
    moduleCreateInfo.primaryKey = "";
    moduleCreateInfo.secondaryKey = "";
    moduleCreateInfo.authMethod = IOTHUB_REGISTRYMANAGER_AUTH_SPK;
    
    IOTHUB_MODULE moduleInfo;
    memset(&moduleInfo, 0, sizeof(moduleInfo));
    moduleInfo.version = 1;
    
    // Create module
    result = IoTHubRegistryManager_CreateModule(iotHubRegistryManagerHandle, &moduleCreateInfo, &moduleInfo);
    if (result == IOTHUB_REGISTRYMANAGER_OK)
    {
        (void)printf("IoTHubRegistryManager_CreateModule: Module has been created successfully: deviceId=%s, moduleId=%s, primaryKey=%s\n", moduleInfo.deviceId, moduleInfo.moduleId, moduleInfo.primaryKey);
    }
    else if (result == IOTHUB_REGISTRYMANAGER_DEVICE_EXIST)
    {
        (void)printf("IoTHubRegistryManager_CreateModule: Module already exists\n");
    }
    else if (result == IOTHUB_REGISTRYMANAGER_ERROR)
    {
        (void)printf("IoTHubRegistryManager_CreateModule failed\n");
    }
    // You will need to Free the returned module information after it was created
    IoTHubRegistryManager_FreeModuleMembers(&moduleInfo);
}

int main(void)
{
    (void)platform_init();

    const char* deviceId = "myFirstDevice";
    const char* moduleId = "myFirstModule";
    IOTHUB_SERVICE_CLIENT_AUTH_HANDLE iotHubServiceClientHandle = NULL;
    IOTHUB_REGISTRYMANAGER_HANDLE iotHubRegistryManagerHandle = NULL;

    if ((iotHubServiceClientHandle = IoTHubServiceClientAuth_CreateFromConnectionString(hubConnectionString)) == NULL)
    {
        (void)printf("IoTHubServiceClientAuth_CreateFromConnectionString failed\n");
    }
    else if ((iotHubRegistryManagerHandle = IoTHubRegistryManager_Create(iotHubServiceClientHandle)) == NULL)
    {
        (void)printf("IoTHubServiceClientAuth_CreateFromConnectionString failed\n");
    }
    else
    {
        createDevice(iotHubRegistryManagerHandle, deviceId);
        createModule(iotHubRegistryManagerHandle, deviceId, moduleId);
    }

    if (iotHubRegistryManagerHandle != NULL)
    {
        (void)printf("Calling IoTHubRegistryManager_Destroy...\n");
        IoTHubRegistryManager_Destroy(iotHubRegistryManagerHandle);
    }

    if (iotHubServiceClientHandle != NULL)
    {
        (void)printf("Calling IoTHubServiceClientAuth_Destroy...\n");
        IoTHubServiceClientAuth_Destroy(iotHubServiceClientHandle);
    }
    
    platform_deinit();
    return 0;
}
```

Questa app crea un'identità del dispositivo con ID **myFirstDevice** e un'identità del modulo con ID **myFirstModule** per il dispositivo **myFirstDevice**. Se questo ID modulo è già presente nel registro delle identità, il codice recupera semplicemente le informazioni esistenti sul modulo. L'app visualizzerà quindi la chiave primaria per l'identità. Questa chiave verrà usata dall'app per modulo simulato per connettersi all'hub IoT.

> [!NOTE]
> Il registro delle identità dell'hub IoT archivia solo le identità del dispositivo e del modulo per abilitare l'accesso sicuro all'hub. Il registro delle identità archivia gli ID dispositivo e le chiavi da usare come credenziali di sicurezza. Il registro delle identità archivia anche un flag di abilitazione/disabilitazione per ogni dispositivo che consente di disabilitare l'accesso per un dispositivo. Se l'applicazione deve archiviare altri metadati specifici del dispositivo, dovrà usare un archivio specifico dell'applicazione. Non esiste alcun flag abilitato/disabilitato per le identità del modulo. Per altre informazioni, vedere la [Guida per gli sviluppatori dell'hub IoT](iot-hub-devguide-identity-registry.md).

## <a name="update-the-module-twin-using-c-device-sdk"></a>Aggiornare il modulo gemello usando l'SDK per dispositivi C

In questa sezione, nel dispositivo simulato viene creata un'app C che aggiorna le proprietà restituite del modulo gemello.

1. **Ottenere la stringa di connessione del modulo** - Accedere al [portale di Azure](https://portal.azure.com). Passare all'hub IoT e fare clic su Dispositivi IoT. Trovare myFirstDevice e aprirlo. Si vedrà che myFirstModule è stato creato correttamente. Copiare la stringa di connessione del modulo. Sarà necessaria nel prossimo passaggio.

    ![Dettagli del modulo nel portale di Azure](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG)

2. **Creare l'app UpdateModuleTwinReportedProperties** Aggiungere le istruzioni `using` seguenti all'inizio del file **Program.cs**:

    ```C
    #include <stdio.h>
    #include <stdlib.h>

    #include "azure_c_shared_utility/crt_abstractions.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"

    #include "iothub_service_client_auth.h"
    #include "iothub_devicetwin.h"

    const char* deviceId = "bugbash-test-2";
    const char* moduleId = "module-id-1";
    static const char* hubConnectionString ="[your hub's connection string]"; // modify
    const char* testJson = "{\"properties\":{\"desired\":{\"integer_property\": b-1234, \"string_property\": \"abcd\"}}}";

    int main(void)
    {
        (void)platform_init();

        IOTHUB_SERVICE_CLIENT_AUTH_HANDLE iotHubServiceClientHandle = NULL;
        IOTHUB_SERVICE_CLIENT_DEVICE_TWIN_HANDLE iothubDeviceTwinHandle = NULL;

        if ((iotHubServiceClientHandle = IoTHubServiceClientAuth_CreateFromConnectionString(moduleConnectionString)) == NULL)
        {
            (void)printf("IoTHubServiceClientAuth_CreateFromConnectionString failed\n");
        }
        else if ((iothubDeviceTwinHandle = IoTHubDeviceTwin_Create(iotHubServiceClientHandle)) == NULL)
        {
            (void)printf("IoTHubServiceClientAuth_CreateFromConnectionString failed\n");
        }
        else
        {
            char *result = IoTHubDeviceTwin_UpdateModuleTwin(iothubDeviceTwinHandle, deviceId, moduleId, testJson);
            printf("IoTHubDeviceTwin_UpdateModuleTwin returned %s\n", result);
        }

        if (iothubDeviceTwinHandle != NULL)
        {
            (void)printf("Calling IoTHubDeviceTwin_Destroy...\n");
            IoTHubDeviceTwin_Destroy(iothubDeviceTwinHandle);
        }

        if (iotHubServiceClientHandle != NULL)
        {
            (void)printf("Calling IoTHubServiceClientAuth_Destroy...\n");
            IoTHubServiceClientAuth_Destroy(iotHubServiceClientHandle);
        }
        
        platform_deinit();
        return 0;
    }
    ```

Questo esempio di codice mostra come recuperare il modulo gemello e aggiornare le proprietà segnalate. 

## <a name="get-updates-on-the-device-side"></a>Ottenere aggiornamenti sul lato dispositivo

Oltre al codice precedente, è possibile aggiungere il blocco di codice seguente per ottenere il messaggio di aggiornamento del modulo gemello nel dispositivo.

```C
#include <stdio.h>
#include <stdlib.h>

#include "azure_c_shared_utility/crt_abstractions.h"
#include "azure_c_shared_utility/macro_utils.h"
#include "azure_c_shared_utility/threadapi.h"
#include "azure_c_shared_utility/platform.h"
#include "iothub_module_client_ll.h"
#include "iothub_client_options.h"
#include "iothub_message.h"

// The protocol you wish to use should be uncommented
//
//#define SAMPLE_MQTT
//#define SAMPLE_MQTT_OVER_WEBSOCKETS
#define SAMPLE_AMQP
//#define SAMPLE_AMQP_OVER_WEBSOCKETS
//#define SAMPLE_HTTP

#ifdef SAMPLE_MQTT
    #include "iothubtransportmqtt.h"
#endif // SAMPLE_MQTT
#ifdef SAMPLE_MQTT_OVER_WEBSOCKETS
    #include "iothubtransportmqtt_websockets.h"
#endif // SAMPLE_MQTT_OVER_WEBSOCKETS
#ifdef SAMPLE_AMQP
    #include "iothubtransportamqp.h"
#endif // SAMPLE_AMQP
#ifdef SAMPLE_AMQP_OVER_WEBSOCKETS
    #include "iothubtransportamqp_websockets.h"
#endif // SAMPLE_AMQP_OVER_WEBSOCKETS
#ifdef SAMPLE_HTTP
    #include "iothubtransporthttp.h"
#endif // SAMPLE_HTTP

/* Paste in the your iothub connection string  */
static const char* connectionString = "[Fill in connection string]";

static bool g_continueRunning;
#define DOWORK_LOOP_NUM     3

static void deviceTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
{
    (void)userContextCallback;

    printf("Device Twin update received (state=%s, size=%zu): %s\r\n", 
        ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
}

static void reportedStateCallback(int status_code, void* userContextCallback)
{
    (void)userContextCallback;
    printf("Device Twin reported properties update completed with result: %d\r\n", status_code);

    g_continueRunning = false;
}

void iothub_module_client_sample_device_twin_run(void)
{
    IOTHUB_CLIENT_TRANSPORT_PROVIDER protocol;
    IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle;
    g_continueRunning = true;

    // Select the Protocol to use with the connection
#ifdef SAMPLE_MQTT
    protocol = MQTT_Protocol;
#endif // SAMPLE_MQTT
#ifdef SAMPLE_MQTT_OVER_WEBSOCKETS
    protocol = MQTT_WebSocket_Protocol;
#endif // SAMPLE_MQTT_OVER_WEBSOCKETS
#ifdef SAMPLE_AMQP
    protocol = AMQP_Protocol;
#endif // SAMPLE_AMQP
#ifdef SAMPLE_AMQP_OVER_WEBSOCKETS
    protocol = AMQP_Protocol_over_WebSocketsTls;
#endif // SAMPLE_AMQP_OVER_WEBSOCKETS
#ifdef SAMPLE_HTTP
    protocol = HTTP_Protocol;
#endif // SAMPLE_HTTP

    if (platform_init() != 0)
    {
        (void)printf("Failed to initialize the platform.\r\n");
    }
    else
    {
        if ((iotHubModuleClientHandle = IoTHubModuleClient_LL_CreateFromConnectionString(connectionString, protocol)) == NULL)
        {
            (void)printf("ERROR: iotHubModuleClientHandle is NULL!\r\n");
        }
        else
        {
            bool traceOn = true;
            const char* reportedState = "{ 'device_property': 'new_value'}";
            size_t reportedStateSize = strlen(reportedState);

            (void)IoTHubModuleClient_LL_SetOption(iotHubModuleClientHandle, OPTION_LOG_TRACE, &traceOn);

            // Check the return of all API calls when developing your solution. Return checks ommited for sample simplification.

            (void)IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, deviceTwinCallback, iotHubModuleClientHandle);
            (void)IoTHubModuleClient_LL_SendReportedState(iotHubModuleClientHandle, (const unsigned char*)reportedState, reportedStateSize, reportedStateCallback, iotHubModuleClientHandle);

            do
            {
                IoTHubModuleClient_LL_DoWork(iotHubModuleClientHandle);
                ThreadAPI_Sleep(1);
            } while (g_continueRunning);

            for (size_t index = 0; index < DOWORK_LOOP_NUM; index++)
            {
                IoTHubModuleClient_LL_DoWork(iotHubModuleClientHandle);
                ThreadAPI_Sleep(1);
            }

            IoTHubModuleClient_LL_Destroy(iotHubModuleClientHandle);
        }
        platform_deinit();
    }
}

int main(void)
{
    iothub_module_client_sample_device_twin_run();
    return 0;
}
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle attività iniziali con l'hub IoT e per esplorare altri scenari IoT, vedere:

* [Introduzione alla gestione dei dispositivi](iot-hub-node-node-device-management-get-started.md)
* [Introduzione a IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
