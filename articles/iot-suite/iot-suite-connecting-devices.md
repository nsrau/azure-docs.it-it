---
title: Connettere un dispositivo tramite C in Windows | Microsoft Docs
description: Descrive come connettere un dispositivo alla soluzione di monitoraggio remoto preconfigurata Azure IoT Suite con un’applicazione scritta in C in esecuzione in Windows.
services: ''
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/14/2016
ms.author: dobett

---
# Connettere il dispositivo alla soluzione preconfigurata per il monitoraggio remoto (Windows)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Creare una soluzione di esempio C in Windows
La procedura seguente illustra come usare Visual Studio per creare una semplice applicazione client, scritta in C, che comunica con la soluzione di monitoraggio remoto preconfigurata.

Creare un progetto iniziale in Visual Studio 2015 e aggiungere i pacchetti NuGet del client dispositivo hub IoT:

1. In Visual Studio 2015 creare una nuova applicazione console C usando il modello **Applicazione console Win32**. Assegnare al progetto il nome **RMDevice**.
2. Nella pagina **Impostazioni applicazione** della **Creazione guidata applicazione Win32**, verificare che l'opzione **Applicazione console** sia selezionata e deselezionare **Intestazione precompilata** e **Controlli Security Development Lifecycle (SDL)**.
3. In **Esplora soluzioni** eliminare i file stdafx.h, targetver.h e stdafx.cpp.
4. In **Esplora soluzioni** rinominare il file RMDevice.cpp in RMDevice.c.
5. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **RMDevice** e quindi scegliere **Gestisci pacchetti NuGet**. Fare clic su **Sfoglia**, quindi cercare e installare i seguenti pacchetti NuGet nel progetto:
   
   * Microsoft.Azure.IoTHub.Serializer
   * Microsoft.Azure.IoTHub.IoTHubClient
   * Microsoft.Azure.IoTHub.HttpTransport
6. In **Esplora soluzioni** fare clic sul progetto **RMDevice** e quindi scegliere **Proprietà** per aprire la finestra di dialogo **Pagine delle proprietà** del progetto. Per informazioni dettagliate, vedere [Setting Visual C++ Project Properties][lnk-c-project-properties] \(Impostazione delle proprietà dei progetti Visual C++).
7. Fare clic sulla cartella **Linker** cartella, quindi fare clic sulla pagina delle proprietà **Input**.
8. Aggiungere **crypt32.lib** alla proprietà **Dipendenze aggiuntive**. Fare clic su **OK** e quindi scegliere nuovamente **OK** per salvare i valori delle proprietà del progetto.

## Specificare il comportamento del dispositivo hub IoT.
Le librerie client dell'hub IoT usano un modello per specificare il formato dei messaggi che il dispositivo invia all'hub IoT e i comandi dall'hub IoT a cui risponde il dispositivo.

1. In Visual Studio aprire il file RMDevice.c. Sostituire le istruzioni `#include` esistenti con le seguenti:
   
    ```
    #include "iothubtransporthttp.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    ```
2. Aggiungere le seguenti dichiarazioni di variabili dopo le istruzioni `#include`. Sostituire i valori segnaposto [Id dispositivo] e [Chiave dispositivo] con i valori per il dispositivo dal dashboard della soluzione di monitoraggio remoto. Usare il Nome host hub IoT dal dashboard per sostituire [Nome IoTHub]. Ad esempio, se il nome host dell'hub IoT è **contoso.azure-devices.net**, sostituire [Nome IoTHub] con **contoso**:
   
    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "azure-devices.net";
    ```
3. Aggiungere il codice seguente per definire il modello che consente al dispositivo di comunicare con l'hub IoT. Questo modello specifica che il dispositivo invia temperatura, temperatura esterna, umidità e un ID dispositivo come dati di telemetria. Il dispositivo invia anche metadati relativi al dispositivo all'hub IoT, compreso un elenco di comandi supportati dal dispositivo. Questo dispositivo risponde ai comandi **SetTemperature** e **SetHumidity**:
   
    ```
    // Define the Model
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

## Implementare il comportamento del dispositivo
È ora necessario aggiungere codice che implementa il comportamento definito nel modello.

1. Aggiungere le funzioni seguenti che vengono eseguite quando il dispositivo riceve i comandi **SetTemperature** e **SetHumidity** dall'hub IoT:
   
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
2. Aggiungere la funzione seguente che invia un messaggio all'hub IoT:
   
    ```
    static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
    {
      IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
      if (messageHandle == NULL)
      {
        printf("unable to create a new IoTHubMessage\r\n");
      }
      else
      {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, NULL, NULL) != IOTHUB_CLIENT_OK)
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
    }
    ```
3. Aggiungere la funzione seguente che collega la libreria di serializzazione nell'SDK:
   
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
4. Aggiungere la funzione seguente per connettersi all'hub IoT, inviare e ricevere messaggi e disconnettersi dall'hub. Si noti il modo in cui il dispositivo invia i metadati relativi a se stesso, inclusi i comandi che supporta, all'hub IoT non appena si connette; in questo modo, la soluzione aggiorna lo stato del dispositivo come **In esecuzione** nel dashboard:
   
    ```
    void remote_monitoring_run(void)
    {
      if (serializer_init(NULL) != SERIALIZER_OK)
      {
        printf("Failed on serializer_init\r\n");
      }
      else
      {
        IOTHUB_CLIENT_CONFIG config;
        IOTHUB_CLIENT_HANDLE iotHubClientHandle;
   
        config.deviceId = deviceId;
        config.deviceKey = deviceKey;
        config.iotHubName = hubName;
        config.iotHubSuffix = hubSuffix;
        config.protocol = HTTP_Protocol;
        config.deviceSasToken = NULL;
        config.protocolGatewayHostName = NULL;
        iotHubClientHandle = IoTHubClient_Create(&config);
        if (iotHubClientHandle == NULL)
        {
          (void)printf("Failed on IoTHubClient_CreateFromConnectionString\r\n");
        }
        else
        {
          Thermostat* thermostat = CREATE_MODEL_INSTANCE(Contoso, Thermostat);
          if (thermostat == NULL)
          {
            (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
          }
          else
          {
            STRING_HANDLE commandsMetadata;
   
            if (IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, thermostat) != IOTHUB_CLIENT_OK)
            {
              printf("unable to IoTHubClient_SetMessageCallback\r\n");
            }
            else
            {
   
              /* send the device info upon startup so that the cloud app knows
              what commands are available and the fact that the device is up */
              thermostat->ObjectType = "DeviceInfo";
              thermostat->IsSimulatedDevice = false;
              thermostat->Version = "1.0";
              thermostat->DeviceProperties.HubEnabledState = true;
              thermostat->DeviceProperties.DeviceID = (char*)deviceId;
   
              commandsMetadata = STRING_new();
              if (commandsMetadata == NULL)
              {
                (void)printf("Failed on creating string for commands metadata\r\n");
              }
              else
              {
                /* Serialize the commands metadata as a JSON string before sending */
                if (SchemaSerializer_SerializeCommandMetadata(GET_MODEL_HANDLE(Contoso, Thermostat), commandsMetadata) != SCHEMA_SERIALIZER_OK)
                {
                  (void)printf("Failed serializing commands metadata\r\n");
                }
                else
                {
                  unsigned char* buffer;
                  size_t bufferSize;
                  thermostat->Commands = (char*)STRING_c_str(commandsMetadata);
   
                  /* Here is the actual send of the Device Info */
                  if (SERIALIZE(&buffer, &bufferSize, thermostat->ObjectType, thermostat->Version, thermostat->IsSimulatedDevice, thermostat->DeviceProperties, thermostat->Commands) != IOT_AGENT_OK)
                  {
                    (void)printf("Failed serializing\r\n");
                  }
                  else
                  {
                    sendMessage(iotHubClientHandle, buffer, bufferSize);
                  }
   
                }
   
                STRING_delete(commandsMetadata);
              }
   
              thermostat->Temperature = 50;
              thermostat->ExternalTemperature = 55;
              thermostat->Humidity = 50;
              thermostat->DeviceId = (char*)deviceId;
   
              while (1)
              {
                unsigned char*buffer;
                size_t bufferSize;
   
                (void)printf("Sending sensor value Temperature = %d, Humidity = %d\r\n", thermostat->Temperature, thermostat->Humidity);
   
                if (SERIALIZE(&buffer, &bufferSize, thermostat->DeviceId, thermostat->Temperature, thermostat->Humidity, thermostat->ExternalTemperature) != IOT_AGENT_OK)
                {
                  (void)printf("Failed sending sensor value\r\n");
                }
                else
                {
                  sendMessage(iotHubClientHandle, buffer, bufferSize);
                }
   
                ThreadAPI_Sleep(1000);
              }
            }
   
            DESTROY_MODEL_INSTANCE(thermostat);
          }
          IoTHubClient_Destroy(iotHubClientHandle);
        }
        serializer_deinit();
      }
    }
    ```
   
    Per riferimento, ecco un esempio di messaggio **DeviceInfo** inviato all'hub IoT all'avvio:
   
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
   
    Per riferimento, ecco un esempio di messaggio **Telemetria** inviato all'hub IoT:
   
    ```
    {"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
    ```
   
    Per riferimento, ecco un esempio di messaggio **Comando** ricevuto dall'hub IoT:
   
    ```
    {
      "Name":"SetHumidity",
      "MessageId":"2f3d3c75-3b77-4832-80ed-a5bb3e233391",
      "CreatedTime":"2016-03-11T15:09:44.2231295Z",
      "Parameters":{"humidity":23}
    }
    ```
5. Sostituire la funzione **main** con il codice seguente per richiamare la funzione **remote\_monitoring\_run**:
   
    ```
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```
6. Fare clic su **Compila** e quindi **Compila soluzione** per compilare l'applicazione del dispositivo.
7. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **RMDevice**, scegliere **Debug** e quindi fare clic su **Avvia nuova istanza** per eseguire l'esempio. La console visualizza i messaggi quando l'applicazione invia all'hub IoT un esempio di telemetria e riceve i comandi.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-c-project-properties]: https://msdn.microsoft.com/library/669zx6zc.aspx

<!---HONumber=AcomDC_0720_2016-->