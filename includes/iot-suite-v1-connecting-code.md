## <a name="specify-the-behavior-of-the-iot-device"></a>Specificare il comportamento del dispositivo IoT

La libreria client serializzatrice di hub IoT usa un modello per specificare il formato dei messaggi che il dispositivo scambia con hub IoT.

1. Aggiungere le seguenti dichiarazioni di variabili dopo le istruzioni `#include` . Sostituire i valori segnaposto [ID dispositivo] e [Chiave dispositivo] con i valori annotati per il dispositivo nel dashboard della soluzione di monitoraggio remoto. Usare il nome host hub IoT del dashboard della soluzione per sostituire [Nome IoTHub]. Ad esempio, se il nome host dell'hub IoT è **contoso.azure-devices.net**, sostituire [Nome IoTHub] con **contoso**:
   
    ```c
    static const char* deviceId = "[Device Id]";
    static const char* connectionString = "HostName=[IoTHub Name].azure-devices.net;DeviceId=[Device Id];SharedAccessKey=[Device Key]";
    ```

1. Aggiungere il codice seguente per definire il modello che consente al dispositivo di comunicare con l'hub IoT. Questo modello consente di specificare che il dispositivo:

   - Può inviare temperatura, temperatura esterna, umidità e un ID dispositivo come dati di telemetria.
   - Può inviare i metadati relativi al dispositivo all'hub IoT. Il dispositivo invia i metadati di base in un oggetto **DeviceInfo** all'avvio.
   - Può inviare proprietà segnalate al dispositivo gemello nell'hub IoT. Queste proprietà segnalate sono raggruppate in proprietà di configurazione, dispositivo e di sistema.
   - Può ricevere e agire sulle proprietà desiderate impostate nel dispositivo gemello nell'hub IoT.
   - È in grado di rispondere al **riavvio** e ai metodi diretti **InitiateFirmwareUpdate** richiamati tramite il portale della soluzione. Il dispositivo invia le informazioni sui metodi diretti che supporta usando le proprietà segnalate.
   
    ```c
    // Define the Model
    BEGIN_NAMESPACE(Contoso);

    /* Reported properties */
    DECLARE_STRUCT(SystemProperties,
      ascii_char_ptr, Manufacturer,
      ascii_char_ptr, FirmwareVersion,
      ascii_char_ptr, InstalledRAM,
      ascii_char_ptr, ModelNumber,
      ascii_char_ptr, Platform,
      ascii_char_ptr, Processor,
      ascii_char_ptr, SerialNumber
    );

    DECLARE_STRUCT(LocationProperties,
      double, Latitude,
      double, Longitude
    );

    DECLARE_STRUCT(ReportedDeviceProperties,
      ascii_char_ptr, DeviceState,
      LocationProperties, Location
    );

    DECLARE_MODEL(ConfigProperties,
      WITH_REPORTED_PROPERTY(double, TemperatureMeanValue),
      WITH_REPORTED_PROPERTY(uint8_t, TelemetryInterval)
    );

    /* Part of DeviceInfo */
    DECLARE_STRUCT(DeviceProperties,
      ascii_char_ptr, DeviceID,
      _Bool, HubEnabledState
    );

    DECLARE_DEVICETWIN_MODEL(Thermostat,
      /* Telemetry (temperature, external temperature and humidity) */
      WITH_DATA(double, Temperature),
      WITH_DATA(double, ExternalTemperature),
      WITH_DATA(double, Humidity),
      WITH_DATA(ascii_char_ptr, DeviceId),

      /* DeviceInfo */
      WITH_DATA(ascii_char_ptr, ObjectType),
      WITH_DATA(_Bool, IsSimulatedDevice),
      WITH_DATA(ascii_char_ptr, Version),
      WITH_DATA(DeviceProperties, DeviceProperties),

      /* Device twin properties */
      WITH_REPORTED_PROPERTY(ReportedDeviceProperties, Device),
      WITH_REPORTED_PROPERTY(ConfigProperties, Config),
      WITH_REPORTED_PROPERTY(SystemProperties, System),

      WITH_DESIRED_PROPERTY(double, TemperatureMeanValue, onDesiredTemperatureMeanValue),
      WITH_DESIRED_PROPERTY(uint8_t, TelemetryInterval, onDesiredTelemetryInterval),

      /* Direct methods implemented by the device */
      WITH_METHOD(Reboot),
      WITH_METHOD(InitiateFirmwareUpdate, ascii_char_ptr, FwPackageURI),

      /* Register direct methods with solution portal */
      WITH_REPORTED_PROPERTY(ascii_char_ptr_no_quotes, SupportedMethods)
    );

    END_NAMESPACE(Contoso);
    ```

## <a name="implement-the-behavior-of-the-device"></a>Implementare il comportamento del dispositivo
Aggiungere il codice che implementa il comportamento definito nel modello.

1. Aggiungere le funzioni seguenti che gestiscono le proprietà desiderate impostate nel dashboard della soluzione. Tali proprietà desiderate sono definite nel modello:

    ```c
    void onDesiredTemperatureMeanValue(void* argument)
    {
      /* By convention 'argument' is of the type of the MODEL */
      Thermostat* thermostat = argument;
      printf("Received a new desired_TemperatureMeanValue = %f\r\n", thermostat->TemperatureMeanValue);

    }

    void onDesiredTelemetryInterval(void* argument)
    {
      /* By convention 'argument' is of the type of the MODEL */
      Thermostat* thermostat = argument;
      printf("Received a new desired_TelemetryInterval = %d\r\n", thermostat->TelemetryInterval);
    }
    ```

1. Aggiungere le funzioni seguenti che gestiscono i metodi diretti richiamati tramite l'hub IoT. Questi metodi diretti sono definiti nel modello:

    ```c
    /* Handlers for direct methods */
    METHODRETURN_HANDLE Reboot(Thermostat* thermostat)
    {
      (void)(thermostat);

      METHODRETURN_HANDLE result = MethodReturn_Create(201, "\"Rebooting\"");
      printf("Received reboot request\r\n");
      return result;
    }

    METHODRETURN_HANDLE InitiateFirmwareUpdate(Thermostat* thermostat, ascii_char_ptr FwPackageURI)
    {
      (void)(thermostat);

      METHODRETURN_HANDLE result = MethodReturn_Create(201, "\"Initiating Firmware Update\"");
      printf("Recieved firmware update request. Use package at: %s\r\n", FwPackageURI);
      return result;
    }
    ```

1. Aggiungere la funzione seguente che invia un messaggio alla soluzione preconfigurata:
   
    ```c
    /* Send data to IoT Hub */
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

1. Aggiungere il gestore di callback seguente che viene eseguito quando il dispositivo ha inviato i nuovi valori di proprietà segnalati alla soluzione preconfigurata:

    ```c
    /* Callback after sending reported properties */
    void deviceTwinCallback(int status_code, void* userContextCallback)
    {
      (void)(userContextCallback);
      printf("IoTHub: reported properties delivered with status_code = %u\n", status_code);
    }
    ```

1. Aggiungere la funzione seguente per connettere il dispositivo alla soluzione preconfigurata nel cloud e scambiare dati. Questa funzione esegue questa procedura:

    - Inizializza la piattaforma.
    - Registra lo spazio dei nomi Contoso con la libreria di serializzazione.
    - Inizializza il client con la stringa di connessione del dispositivo.
    - Creare un'istanza del modello **Thermostat**.
    - Crea e invia i valori delle proprietà segnalate.
    - Invia un oggetto **DeviceInfo**.
    - Crea un ciclo per inviare dati di telemetria ogni secondo.
    - Deinizializza tutte le risorse.

    ```c
    void remote_monitoring_run(void)
    {
      if (platform_init() != 0)
      {
        printf("Failed to initialize the platform.\n");
      }
      else
      {
        if (SERIALIZER_REGISTER_NAMESPACE(Contoso) == NULL)
        {
          printf("Unable to SERIALIZER_REGISTER_NAMESPACE\n");
        }
        else
        {
          IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, MQTT_Protocol);
          if (iotHubClientHandle == NULL)
          {
            printf("Failure in IoTHubClient_CreateFromConnectionString\n");
          }
          else
          {
    #ifdef MBED_BUILD_TIMESTAMP
            // For mbed add the certificate information
            if (IoTHubClient_SetOption(iotHubClientHandle, "TrustedCerts", certificates) != IOTHUB_CLIENT_OK)
            {
                printf("Failed to set option \"TrustedCerts\"\n");
            }
    #endif // MBED_BUILD_TIMESTAMP
            Thermostat* thermostat = IoTHubDeviceTwin_CreateThermostat(iotHubClientHandle);
            if (thermostat == NULL)
            {
              printf("Failure in IoTHubDeviceTwin_CreateThermostat\n");
            }
            else
            {
              /* Set values for reported properties */
              thermostat->Config.TemperatureMeanValue = 55.5;
              thermostat->Config.TelemetryInterval = 3;
              thermostat->Device.DeviceState = "normal";
              thermostat->Device.Location.Latitude = 47.642877;
              thermostat->Device.Location.Longitude = -122.125497;
              thermostat->System.Manufacturer = "Contoso Inc.";
              thermostat->System.FirmwareVersion = "2.22";
              thermostat->System.InstalledRAM = "8 MB";
              thermostat->System.ModelNumber = "DB-14";
              thermostat->System.Platform = "Plat 9.75";
              thermostat->System.Processor = "i3-7";
              thermostat->System.SerialNumber = "SER21";
              /* Specify the signatures of the supported direct methods */
              thermostat->SupportedMethods = "{\"Reboot\": \"Reboot the device\", \"InitiateFirmwareUpdate--FwPackageURI-string\": \"Updates device Firmware. Use parameter FwPackageURI to specify the URI of the firmware file\"}";

              /* Send reported properties to IoT Hub */
              if (IoTHubDeviceTwin_SendReportedStateThermostat(thermostat, deviceTwinCallback, NULL) != IOTHUB_CLIENT_OK)
              {
                printf("Failed sending serialized reported state\n");
              }
              else
              {
                printf("Send DeviceInfo object to IoT Hub at startup\n");
    
                thermostat->ObjectType = "DeviceInfo";
                thermostat->IsSimulatedDevice = 0;
                thermostat->Version = "1.0";
                thermostat->DeviceProperties.HubEnabledState = 1;
                thermostat->DeviceProperties.DeviceID = (char*)deviceId;

                unsigned char* buffer;
                size_t bufferSize;

                if (SERIALIZE(&buffer, &bufferSize, thermostat->ObjectType, thermostat->Version, thermostat->IsSimulatedDevice, thermostat->DeviceProperties) != CODEFIRST_OK)
                {
                  (void)printf("Failed serializing DeviceInfo\n");
                }
                else
                {
                  sendMessage(iotHubClientHandle, buffer, bufferSize);
                }

                /* Send telemetry */
                thermostat->Temperature = 50;
                thermostat->ExternalTemperature = 55;
                thermostat->Humidity = 50;
                thermostat->DeviceId = (char*)deviceId;

                while (1)
                {
                  unsigned char*buffer;
                  size_t bufferSize;

                  (void)printf("Sending sensor value Temperature = %f, Humidity = %f\n", thermostat->Temperature, thermostat->Humidity);

                  if (SERIALIZE(&buffer, &bufferSize, thermostat->DeviceId, thermostat->Temperature, thermostat->Humidity, thermostat->ExternalTemperature) != CODEFIRST_OK)
                  {
                    (void)printf("Failed sending sensor value\r\n");
                  }
                  else
                  {
                    sendMessage(iotHubClientHandle, buffer, bufferSize);
                  }

                  ThreadAPI_Sleep(1000);
                }

                IoTHubDeviceTwin_DestroyThermostat(thermostat);
              }
            }
            IoTHubClient_Destroy(iotHubClientHandle);
          }
          serializer_deinit();
        }
      }
      platform_deinit();
    }
    ```
   
    Per riferimento, ecco un esempio di messaggio di **Telemetria** inviato alla soluzione preconfigurata:
   
    ```
    {"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
    ```