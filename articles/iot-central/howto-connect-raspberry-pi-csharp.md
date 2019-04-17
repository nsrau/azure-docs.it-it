---
title: Connettere un dispositivo Raspberry Pi all'applicazione Azure IoT Central (C#) | Microsoft Docs
description: Gli sviluppatori di dispositivo come connettere un dispositivo Raspberry Pi all'applicazione usando Azure IoT Central C#.
author: viv-liu
ms.author: viviali
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 8a7b2d15884f7a1cb8b6e72937b0f1e3b6cb8ca9
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2019
ms.locfileid: "59616017"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-c"></a>Connettere un dispositivo Raspberry Pi all'applicazione Azure IoT Central (C#)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Questo articolo descrive come connettere un dispositivo Raspberry Pi all'applicazione Microsoft Azure IoT Central usando il linguaggio di programmazione C# in qualità di sviluppatore di dispositivi.

## <a name="before-you-begin"></a>Prima di iniziare

Per seguire la procedura descritta in questo articolo sono necessari i componenti seguenti:

* Un'applicazione Azure IoT Central creata dal modello di applicazione **Sample Devkits**. Per altre informazioni, vedere la [guida introduttiva per la creazione di un'applicazione](quick-deploy-iot-central.md).
* Un dispositivo Raspberry Pi che esegue il sistema operativo Raspbian. Il dispositivo Raspberry Pi deve essere in grado di connettersi a internet. Per altre informazioni, vedere [configurazione di Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

## <a name="sample-devkits-application"></a>Applicazione **Sample Devkits**

Un'applicazione creata dal modello di applicazione **Sample Devkits** include un modello di dispositivo **Raspberry Pi** con le caratteristiche seguenti:

- I dati di telemetria includono le misure seguenti, che verranno raccolte dal dispositivo:
  - Umidità
  - Temperatura
  - Pressione
  - Magnetometro (X, Y, Z)
  - Accelerometro (X, Y, Z)
  - Giroscopio (X, Y, Z)
- Impostazioni
  - Tensione
  - Current
  - Velocità della ventola
  - Attiva/Disattiva runtime di integrazione.
- Properties
  - Proprietà Numero stampo del dispositivo
  - Proprietà cloud della posizione

Per i dettagli completi della configurazione del modello di dispositivo, vedere la [dettagli del dispositivo Raspberry Pi dispositivo modello](#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Aggiungere un dispositivo reale

Nell'applicazione Azure IoT Central, aggiungere un dispositivo reale dal **Raspberry Pi** modello del dispositivo. Prendere nota del dispositivo i dettagli della connessione (**ambito ID**, **ID dispositivo**, e **chiave primaria**). Per altre informazioni, vedere [Aggiungere un dispositivo reale all'applicazione Azure IoT Central](tutorial-add-device.md).

### <a name="create-your-net-application"></a>Creare l'applicazione .NET

Creare e testare l'applicazione di dispositivo nel computer desktop.

Per completare la procedura seguente è possibile usare Visual Studio Code. Per altre informazioni, vedere [Working with C#](https://code.visualstudio.com/docs/languages/csharp) (Uso di C#).

> [!NOTE]
> Se si preferisce, è possibile completare i seguenti passaggi usando un editor di codice diverso.

1. Per inizializzare il progetto .NET e aggiungervi i pacchetti NuGet necessari, eseguire i comandi seguenti:

   ```cmd/sh
   mkdir pisample
   cd pisample
   dotnet new console
   dotnet add package Microsoft.Azure.Devices.Client
   dotnet restore
   ```

1. Aprire la cartella `pisample` in Visual Studio Code. Quindi aprire il file di progetto **pisample.csproj**. Aggiungere il tag `<RuntimeIdentifiers>` mostrato nel frammento di codice seguente:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <RuntimeIdentifiers>win-arm;linux-arm</RuntimeIdentifiers>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Devices.Client" Version="1.19.0" />
      </ItemGroup>
    </Project>
    ```

    > [!NOTE]
    > Il numero di versione del pacchetto **Microsoft.Azure.Devices.Client** potrebbe essere maggiore di quello illustrato.

1. Salvare **pisample.csproj**. Se Visual Studio Code chiede di eseguire il comando di ripristino, scegliere **Restore** (Ripristina).

1. Aprire **Program.cs** e sostituire il contenuto con il codice seguente:

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;

    namespace pisample
    {
      class Program
      {
        static string DeviceConnectionString = "{your device connection string}";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();
        static CancellationTokenSource cts;
        static double baseTemperature = 60;
        static double basePressure = 500;
        static double baseHumidity = 50;
        static void Main(string[] args)
        {
          Console.WriteLine("Raspberry Pi Azure IoT Central example");

          try
          {
            InitClient();
            SendDeviceProperties();

            cts = new CancellationTokenSource();
            SendTelemetryAsync(cts.Token);

            Console.WriteLine("Wait for settings update...");
            Client.SetDesiredPropertyUpdateCallbackAsync(HandleSettingChanged, null).Wait();
            Console.ReadKey();
            cts.Cancel();
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        public static void InitClient()
        {
          try
          {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        public static async void SendDeviceProperties()
        {
          try
          {
            Console.WriteLine("Sending device properties:");
            Random random = new Random();
            TwinCollection telemetryConfig = new TwinCollection();
            reportedProperties["dieNumber"] = random.Next(1, 6);
            Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

            await Client.UpdateReportedPropertiesAsync(reportedProperties);
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        private static async void SendTelemetryAsync(CancellationToken token)
        {
          try
          {
            Random rand = new Random();

            while (true)
            {
              double currentTemperature = baseTemperature + rand.NextDouble() * 20;
              double currentPressure = basePressure + rand.NextDouble() * 100;
              double currentHumidity = baseHumidity + rand.NextDouble() * 20;

              var telemetryDataPoint = new
              {
                humidity = currentHumidity,
                pressure = currentPressure,
                temp = currentTemperature
              };
              var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
              var message = new Message(Encoding.ASCII.GetBytes(messageString));

              token.ThrowIfCancellationRequested();
              await Client.SendEventAsync(message);

              Console.WriteLine("{0} > Sending telemetry: {1}", DateTime.Now, messageString);

              await Task.Delay(1000);
            }
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Intentional shutdown: {0}", ex.Message);
          }
        }

        private static async Task HandleSettingChanged(TwinCollection desiredProperties, object userContext)
        {
          try
          {
            Console.WriteLine("Received settings change...");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            string setting = "fanSpeed";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "setVoltage";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "setCurrent";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "activateIR";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
          }

          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        private static void AcknowledgeSettingChange(TwinCollection desiredProperties, string setting)
        {
          reportedProperties[setting] = new
          {
            value = desiredProperties[setting]["value"],
            status = "completed",
            desiredVersion = desiredProperties["$version"],
            message = "Processed"
          };
        }
      }
    }
    ```

    > [!NOTE]
    > Il segnaposto `{your device connection string}` verrà aggiornato nel passaggio successivo.

## <a name="run-your-net-application"></a>Eseguire l'applicazione .NET

Aggiungere la stringa di connessione specifica del dispositivo al codice per il dispositivo per l'autenticazione con Azure IoT Central. Seguire queste istruzioni per [generare la stringa di connessione](howto-generate-connection-string.md) usando la **ID ambito**, **ID dispositivo**, e **chiave primaria** apportate una Si noti in precedenza di.

1. Sostituire `{your device connection string}` nella **Program.cs** file con la stringa di connessione è stato generato.

1. Eseguire il comando seguente nell'ambiente della riga di comando:

   ```cmd/sh
   dotnet restore
   dotnet publish -r linux-arm
   ```

1. Copiare la cartella `pisample\bin\Debug\netcoreapp2.1\linux-arm\publish` nel dispositivo Raspberry Pi. È possibile usare il comando **scp** per copiare i file, ad esempio:

    ```cmd/sh
    scp -r publish pi@192.168.0.40:publish
    ```

    Per altre informazioni, vedere [Raspberry Pi remote access](https://www.raspberrypi.org/documentation/remote-access/) (Accesso remoto a Raspberry Pi).

1. Accedere al dispositivo Raspberry Pi ed eseguire i comandi seguenti in una shell:

    ```cmd/sh
    sudo apt-get update
    sudo apt-get install libc6 libcurl3 libgcc1 libgssapi-krb5-2 liblttng-ust0 libstdc++6 libunwind8 libuuid1 zlib1g
    ```

1. Usare il comando seguente per aggiornare il dispositivo Raspberry Pi:

    ```cmd/sh
    cd publish
    chmod 777 pisample
    ./pisample
    ```

    ![Il programma inizia](./media/howto-connect-raspberry-pi-csharp/device_begin.png)

1. Nell'applicazione Azure IoT Central si può vedere in che modo il codice eseguito sul dispositivo Raspberry Pi interagisce con l'applicazione:

   * Nella pagina **Measurements** (Misure) per il dispositivo reale è possibile vedere dati di telemetria.
   * Nella pagina **Properties** (Proprietà) è possibile vedere il valore dichiarato per la proprietà **Die Number** (Numero modello).
   * Nella pagina **Settings** (Impostazioni) è possibile modificare diverse impostazioni nel dispositivo Raspberry Pi, ad esempio la tensione e la velocità della ventola.

     La schermata seguente mostra il dispositivo Raspberry Pi che riceve la modifica delle impostazioni:

     ![Raspberry P riceve la modifica delle impostazioni](./media/howto-connect-raspberry-pi-csharp/device_switch.png)

## <a name="raspberry-pi-device-template-details"></a>Raspberry Pi dispositivi i dettagli del modello

Un'applicazione creata dal modello di applicazione **Sample Devkits** include un modello di dispositivo **Raspberry Pi** con le caratteristiche seguenti:

### <a name="telemetry-measurements"></a>Misure di telemetria

| Nome campo     | Unità  | Minima | Massima | Cifre decimali |
| -------------- | ------ | ------- | ------- | -------------- |
| umidità       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Impostazioni

Impostazioni numeriche

| Nome visualizzato | Nome campo | Unità | Cifre decimali | Minima | Massima | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Tensione      | setVoltage | Volt | 0              | 0       | 240     | 0       |
| Current      | setCurrent | Amp  | 0              | 0       | 100     | 0       |
| Velocità della ventola    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Impostazioni attivazione/disattivazione

| Nome visualizzato | Nome campo | Testo attivato | Testo disattivato | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ATTIVA      | DISATTIVA      | Off     |

### <a name="properties"></a>Properties

| Type            | Nome visualizzato | Nome campo | Tipo di dati |
| --------------- | ------------ | ---------- | --------- |
| Proprietà dispositivo | Numero stampo   | dieNumber  | number    |
| Text            | Località     | location   | N/D       |

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come connettere Raspberry Pi all'applicazione Azure IoT Central, il passaggio successivo consigliato sia per informazioni su come [configurare un modello di dispositivo personalizzata](howto-set-up-template.md) per dispositivo IoT.
