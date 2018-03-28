## <a name="specify-the-behavior-of-the-iot-device"></a>Specificare il comportamento del dispositivo IoT

La libreria client serializzatrice di hub IoT usa un modello per specificare il formato dei messaggi che il dispositivo scambia con hub IoT.

1. Aggiungere le seguenti dichiarazioni di variabili dopo le istruzioni `#include` . Sostituire i valori segnaposto `[Device Id]` e `[Device connection string]` con i valori annotati per il dispositivo fisico aggiunto alla soluzione di monitoraggio remoto:

    ```c
    static const char* deviceId = "[Device Id]";
    static const char* connectionString = "[Device connection string]";
    ```

1. Aggiungere il codice seguente per definire il modello che consente al dispositivo di comunicare con l'hub IoT. Questo modello consente di specificare che il dispositivo:

    - Può inviare umidità, temperatura e pressione come dati di telemetria.
    - Può inviare proprietà segnalate al dispositivo gemello nell'hub IoT. Queste proprietà segnalate includono informazioni sullo schema di telemetria e sui metodi supportati.
    - Può ricevere e agire sulle proprietà desiderate impostate nel dispositivo gemello nell'hub IoT.
    - Può rispondere ai metodi diretti **Reboot**, **FirmwareUpdate**, **EmergencyValveRelease** e **IncreasePressure** richiamati dall'interfaccia utente. Il dispositivo invia le informazioni sui metodi diretti che supporta usando le proprietà segnalate.

    ```c
    // Define the Model
    BEGIN_NAMESPACE(Contoso);

    DECLARE_STRUCT(MessageSchema,
    ascii_char_ptr, Name,
    ascii_char_ptr, Format,
    ascii_char_ptr_no_quotes, Fields
    )

    DECLARE_STRUCT(TelemetrySchema,
    ascii_char_ptr, Interval,
    ascii_char_ptr, MessageTemplate,
    MessageSchema, MessageSchema
    )

    DECLARE_STRUCT(TelemetryProperties,
    TelemetrySchema, TemperatureSchema,
    TelemetrySchema, HumiditySchema,
    TelemetrySchema, PressureSchema
    )

    DECLARE_DEVICETWIN_MODEL(Chiller,
    /* Telemetry (temperature, external temperature and humidity) */
    WITH_DATA(double, temperature),
    WITH_DATA(ascii_char_ptr, temperature_unit),
    WITH_DATA(double, pressure),
    WITH_DATA(ascii_char_ptr, pressure_unit),
    WITH_DATA(double, humidity),
    WITH_DATA(ascii_char_ptr, humidity_unit),

    /* Manage firmware update process */
    WITH_DATA(ascii_char_ptr, new_firmware_URI),
    WITH_DATA(ascii_char_ptr, new_firmware_version),

    /* Device twin properties */
    WITH_REPORTED_PROPERTY(ascii_char_ptr, Protocol),
    WITH_REPORTED_PROPERTY(ascii_char_ptr, SupportedMethods),
    WITH_REPORTED_PROPERTY(TelemetryProperties, Telemetry),
    WITH_REPORTED_PROPERTY(ascii_char_ptr, Type),
    WITH_REPORTED_PROPERTY(ascii_char_ptr, Firmware),
    WITH_REPORTED_PROPERTY(ascii_char_ptr, FirmwareUpdateStatus),
    WITH_REPORTED_PROPERTY(ascii_char_ptr, Location),
    WITH_REPORTED_PROPERTY(double, Latitiude),
    WITH_REPORTED_PROPERTY(double, Longitude),

    WITH_DESIRED_PROPERTY(ascii_char_ptr, Interval, onDesiredInterval),

    /* Direct methods implemented by the device */
    WITH_METHOD(Reboot),
    WITH_METHOD(FirmwareUpdate, ascii_char_ptr, Firmware, ascii_char_ptr, FirmwareUri),
    WITH_METHOD(EmergencyValveRelease),
    WITH_METHOD(IncreasePressure)
    );

    END_NAMESPACE(Contoso);
    ```

## <a name="implement-the-behavior-of-the-device"></a>Implementare il comportamento del dispositivo

Aggiungere il codice che implementa il comportamento definito nel modello.

1. Aggiungere il gestore di callback seguente che viene eseguito quando il dispositivo ha inviato i nuovi valori di proprietà segnalati alla soluzione preconfigurata:

    ```c
    /* Callback after sending reported properties */
    void deviceTwinCallback(int status_code, void* userContextCallback)
    {
      (void)(userContextCallback);
      printf("IoTHub: reported properties delivered with status_code = %u\n", status_code);
    }
    ```

1. Aggiungere la funzione seguente che simula un processo di aggiornamento del firmware:

    ```c
    static int do_firmware_update(void *param)
    {
      Chiller *chiller = (Chiller *)param;
      printf("do_firmware_update('URI: %s, Version: %s')\r\n", chiller->new_firmware_URI, chiller->new_firmware_version);

      printf("Simulating download phase...\r\n");
      chiller->FirmwareUpdateStatus = "downloading";
      /* Send reported properties to IoT Hub */
      if (IoTHubDeviceTwin_SendReportedStateChiller(chiller, deviceTwinCallback, NULL) != IOTHUB_CLIENT_OK)
      {
        printf("Failed sending serialized reported state\r\n");
      }
      ThreadAPI_Sleep(5000);

      printf("Simulating applying phase...\r\n");
      chiller->FirmwareUpdateStatus = "applying";
      /* Send reported properties to IoT Hub */
      if (IoTHubDeviceTwin_SendReportedStateChiller(chiller, deviceTwinCallback, NULL) != IOTHUB_CLIENT_OK)
      {
        printf("Failed sending serialized reported state\r\n");
      }
      ThreadAPI_Sleep(5000);

      printf("Simulating reboot phase...\r\n");
      chiller->FirmwareUpdateStatus = "rebooting";
      /* Send reported properties to IoT Hub */
      if (IoTHubDeviceTwin_SendReportedStateChiller(chiller, deviceTwinCallback, NULL) != IOTHUB_CLIENT_OK)
      {
        printf("Failed sending serialized reported state\r\n");
      }
      ThreadAPI_Sleep(5000);

      chiller->Firmware = _strdup(chiller->new_firmware_version);
      chiller->FirmwareUpdateStatus = "waiting";
      /* Send reported properties to IoT Hub */
      if (IoTHubDeviceTwin_SendReportedStateChiller(chiller, deviceTwinCallback, NULL) != IOTHUB_CLIENT_OK)
      {
        printf("Failed sending serialized reported state\r\n");
      }

      return 0;
    }
    ```

1. Aggiungere la funzione seguente che gestisce le proprietà desiderate impostate nel dashboard della soluzione. Tali proprietà desiderate sono definite nel modello:

    ```c
    void onDesiredInterval(void* argument)
    {
      /* By convention 'argument' is of the type of the MODEL */
      Chiller* chiller = argument;
      printf("Received a new desired Interval value: %s \r\n", chiller->Interval);
    }
    ```

1. Aggiungere le funzioni seguenti che gestiscono i metodi diretti richiamati tramite l'hub IoT. Questi metodi diretti sono definiti nel modello:

    ```c
    /* Handlers for direct methods */
    METHODRETURN_HANDLE Reboot(Chiller* chiller)
    {
      (void)(chiller);

      METHODRETURN_HANDLE result = MethodReturn_Create(201, "\"Rebooting\"");
      printf("Received reboot request\r\n");
      return result;
    }

    METHODRETURN_HANDLE FirmwareUpdate(Chiller* chiller, ascii_char_ptr Firmware, ascii_char_ptr FirmwareUri)
    {
      printf("Recieved firmware update request request\r\n");
      METHODRETURN_HANDLE result = NULL;
      if (chiller->FirmwareUpdateStatus != "waiting")
      {
        LogError("Attempting to initiate a firmware update out of order");
        result = MethodReturn_Create(400, "\"Attempting to initiate a firmware update out of order\"");
      }
      else
      {
        chiller->new_firmware_version = _strdup(Firmware);
        chiller->new_firmware_URI = _strdup(FirmwareUri);
        THREAD_HANDLE thread_apply;
        THREADAPI_RESULT t_result = ThreadAPI_Create(&thread_apply, do_firmware_update, chiller);
        if (t_result == THREADAPI_OK)
        {
          result = MethodReturn_Create(201, "\"Starting firmware update thread\"");
        }
        else
        {
          LogError("Failed to start firmware update thread");
          result = MethodReturn_Create(500, "\"Failed to start firmware update thread\"");
        }
      }
      
      return result;
    }

    METHODRETURN_HANDLE EmergencyValveRelease(Chiller* chiller)
    {
      (void)(chiller);

      METHODRETURN_HANDLE result = MethodReturn_Create(201, "\"Releasing Emergency Valve\"");
      printf("Recieved emergency valve release request\r\n");
      return result;
    }

    METHODRETURN_HANDLE IncreasePressure(Chiller* chiller)
    {
      (void)(chiller);

      METHODRETURN_HANDLE result = MethodReturn_Create(201, "\"Increasing Pressure\"");
      printf("Received increase pressure request\r\n");
      return result;
    }
    ```

1. Aggiungere la funzione seguente che aggiunge una proprietà a un messaggio da dispositivo a cloud:

    ```c
    /* Add message property */
    static void addProperty(MAP_HANDLE propMap, char* propName, char* propValue)
    {
      if (Map_AddOrUpdate(propMap, propName, propValue) != MAP_OK)
      {
        (void)printf("ERROR: Map_AddOrUpdate Failed on %s!\r\n", propName);
      }
    }
    ```

1. Aggiungere la funzione seguente che invia un messaggio con proprietà alla soluzione preconfigurata:

    ```c
    static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size, char* schema)
    {
      IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
      if (messageHandle == NULL)
      {
        printf("unable to create a new IoTHubMessage\r\n");
      }
      else
      {
        // Add properties
        MAP_HANDLE propMap = IoTHubMessage_Properties(messageHandle);
        addProperty(propMap, "$$MessageSchema", schema);
        addProperty(propMap, "$$ContentType", "JSON");
        time_t now = time(0);
        struct tm* timeinfo;
        #pragma warning(disable: 4996)
        timeinfo = gmtime(&now);
        char timebuff[50];
        strftime(timebuff, 50, "%Y-%m-%dT%H:%M:%SZ", timeinfo);
        addProperty(propMap, "$$CreationTimeUtc", timebuff);

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

1. Aggiungere la funzione seguente per connettere il dispositivo alla soluzione preconfigurata nel cloud e scambiare dati. Questa funzione esegue questa procedura:

    - Inizializza la piattaforma.
    - Registra lo spazio dei nomi Contoso con la libreria di serializzazione.
    - Inizializza il client con la stringa di connessione del dispositivo.
    - Creare un'istanza del modello **Chiller**.
    - Crea e invia i valori delle proprietà segnalate.
    - Crea un ciclo per inviare dati di telemetria ogni cinque secondi mentre lo stato degli aggiornamenti del firmware è **in attesa**.
    - Deinizializza tutte le risorse.

    ```c
    void remote_monitoring_run(void)
    {
      if (platform_init() != 0)
      {
        printf("Failed to initialize the platform.\r\n");
      }
      else
      {
        if (SERIALIZER_REGISTER_NAMESPACE(Contoso) == NULL)
        {
          printf("Unable to SERIALIZER_REGISTER_NAMESPACE\r\n");
        }
        else
        {
          IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, MQTT_Protocol);
          if (iotHubClientHandle == NULL)
          {
            printf("Failure in IoTHubClient_CreateFromConnectionString\r\n");
          }
          else
          {
            Chiller* chiller = IoTHubDeviceTwin_CreateChiller(iotHubClientHandle);
            if (chiller == NULL)
            {
              printf("Failure in IoTHubDeviceTwin_CreateChiller\r\n");
            }
            else
            {
              /* Set values for reported properties */
              chiller->Protocol = "MQTT";
              chiller->SupportedMethods = "Reboot,FirmwareUpdate,EmergencyValveRelease,IncreasePressure";
              chiller->Telemetry.TemperatureSchema.Interval = "00:00:05";
              chiller->Telemetry.TemperatureSchema.MessageTemplate = "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\"}";
              chiller->Telemetry.TemperatureSchema.MessageSchema.Name = "chiller-temperature;v1";
              chiller->Telemetry.TemperatureSchema.MessageSchema.Format = "JSON";
              chiller->Telemetry.TemperatureSchema.MessageSchema.Fields = "{\"temperature\":\"Double\",\"temperature_unit\":\"Text\"}";
              chiller->Telemetry.HumiditySchema.Interval = "00:00:05";
              chiller->Telemetry.HumiditySchema.MessageTemplate = "{\"humidity\":${humidity},\"humidity_unit\":\"${humidity_unit}\"}";
              chiller->Telemetry.HumiditySchema.MessageSchema.Name = "chiller-humidity;v1";
              chiller->Telemetry.HumiditySchema.MessageSchema.Format = "JSON";
              chiller->Telemetry.HumiditySchema.MessageSchema.Fields = "{\"humidity\":\"Double\",\"humidity_unit\":\"Text\"}";
              chiller->Telemetry.PressureSchema.Interval = "00:00:05";
              chiller->Telemetry.PressureSchema.MessageTemplate = "{\"pressure\":${pressure},\"pressure_unit\":\"${pressure_unit}\"}";
              chiller->Telemetry.PressureSchema.MessageSchema.Name = "chiller-pressure;v1";
              chiller->Telemetry.PressureSchema.MessageSchema.Format = "JSON";
              chiller->Telemetry.PressureSchema.MessageSchema.Fields = "{\"pressure\":\"Double\",\"pressure_unit\":\"Text\"}";
              chiller->Type = "Chiller";
              chiller->Firmware = "1.0.0";
              chiller->FirmwareUpdateStatus = "waiting";
              chiller->Location = "Building 44";
              chiller->Latitiude = 47.638928;
              chiller->Longitude = -122.13476;

              /* Send reported properties to IoT Hub */
              if (IoTHubDeviceTwin_SendReportedStateChiller(chiller, deviceTwinCallback, NULL) != IOTHUB_CLIENT_OK)
              {
                printf("Failed sending serialized reported state\r\n");
              }
              else
              {
                /* Send telemetry */
                chiller->temperature_unit = "F";
                chiller->pressure_unit = "psig";
                chiller->humidity_unit = "%";

                srand((unsigned int)time(NULL));
                while (1)
                {
                  chiller->temperature = 50 + ((rand() % 10) - 5);
                  chiller->pressure = 55 + ((rand() % 10) - 5);
                  chiller->humidity = 30 + ((rand() % 10) - 5);
                  unsigned char*buffer;
                  size_t bufferSize;

                  if (chiller->FirmwareUpdateStatus == "waiting")
                  {
                    (void)printf("Sending sensor value Temperature = %f %s,\r\n", chiller->temperature, chiller->temperature_unit);

                    if (SERIALIZE(&buffer, &bufferSize, chiller->temperature, chiller->temperature_unit) != CODEFIRST_OK)
                    {
                      (void)printf("Failed sending sensor value\r\n");
                    }
                    else
                    {
                      sendMessage(iotHubClientHandle, buffer, bufferSize, chiller->Telemetry.TemperatureSchema.MessageSchema.Name);
                    }

                    (void)printf("Sending sensor value Humidity = %f %s,\r\n", chiller->humidity, chiller->humidity_unit);

                    if (SERIALIZE(&buffer, &bufferSize, chiller->humidity, chiller->humidity_unit) != CODEFIRST_OK)
                    {
                      (void)printf("Failed sending sensor value\r\n");
                    }
                    else
                    {
                      sendMessage(iotHubClientHandle, buffer, bufferSize, chiller->Telemetry.HumiditySchema.MessageSchema.Name);
                    }

                    (void)printf("Sending sensor value Pressure = %f %s,\r\n", chiller->pressure, chiller->pressure_unit);

                    if (SERIALIZE(&buffer, &bufferSize, chiller->pressure, chiller->pressure_unit) != CODEFIRST_OK)
                    {
                      (void)printf("Failed sending sensor value\r\n");
                    }
                    else
                    {
                      sendMessage(iotHubClientHandle, buffer, bufferSize, chiller->Telemetry.PressureSchema.MessageSchema.Name);
                    }
                  }

                  ThreadAPI_Sleep(5000);
                }

                IoTHubDeviceTwin_DestroyChiller(chiller);
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
    Device: [myCDevice],
    Data:[{"humidity":50.000000000000000, "humidity_unit":"%"}]
    Properties:
    '$$MessageSchema': 'chiller-humidity;v1'
    '$$ContentType': 'JSON'
    '$$CreationTimeUtc': '2017-09-12T09:17:13Z'
    ```