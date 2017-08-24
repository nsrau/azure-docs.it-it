---
title: Creare un modulo Azure IoT Edge con C# | Microsoft Docs
description: "Questa esercitazione illustra come scrivere un modulo convertitore di dati BLE usando i pacchetti NuGet di Azure IoT Edge più recenti, Visual Studio Code e C#."
services: iot-hub
author: jeffreyCline
manager: timlt
keywords: azure, iot, esercitazione, modulo, nuget, vscode, csharp, edge
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: jcline
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 91ff3c96e4d7928131eba83c0e8c1951782447be
ms.contentlocale: it-it
ms.lasthandoff: 07/04/2017

---
# <a name="create-an-azure-iot-edge-module-with-cx23"></a>Creare un modulo Azure IoT Edge con C&#x23;

Questa esercitazione illustra come creare un modulo per `Azure IoT Edge` usando `Visual Studio Code` e `C#`.

L'esercitazione illustra i passaggi per configurare l'ambiente e spiega come scrivere un modulo convertitore di dati [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) con la versione più recente dei pacchetti `Azure IoT Edge NuGet`. 

>[!NOTE]
L'esercitazione usa `.NET Core SDK`, che supporta la compatibilità multipiattaforma. L'esercitazione seguente è scritta usando il sistema operativo `Windows 10`. Alcuni dei comandi in questa esercitazione potrebbero variare a seconda del `development environment` in uso. 

## <a name="prerequisites"></a>Prerequisiti

In questa sezione si configura l'ambiente per lo sviluppo del modulo `Azure IoT Edge`. Si applica sia a **Windows a 64 bit** che a **Linux a 64 bit (Ubuntu/Debian 8)**.

È richiesto il software seguente:

- [Client Git](https://git-scm.com/downloads)
- [ASP.NET Core SDK](https://www.microsoft.com/net/core#windowscmd)
- [Visual Studio Code](https://code.visualstudio.com/)

Non è necessario clonare il repository per questo esempio, comunque tutto il codice di esempio illustrato in questa esercitazione si trova nel repository seguente:

- `git clone https://github.com/Azure-Samples/iot-edge-samples.git`.
- `cd iot-edge-samples/dotnetcore/simulated_ble`

## <a name="getting-started"></a>Introduzione

1. Installare `.NET Core SDK`.
2. Installare `Visual Studio Code` e la `C# extension` dal Visual Studio Code Marketplace.

Vedere questo [video di esercitazione rapida](https://channel9.msdn.com/Blogs/dotnet/Get-started-VSCode-Csharp-NET-Core-Windows) di introduzione all'utilizzo di `Visual Studio Code` e della `.NET Core SDK`.

## <a name="creating-the-azure-iot-edge-converter-module"></a>Creazione del modulo convertitore di Azure IoT Edge

1. Inizializzare un nuovo progetto C# di libreria di classi `.NET Core`:
    - Aprire il prompt dei comandi (`Windows + R` -> `cmd` -> `enter`).
    - Passare alla cartella in cui si desidera creare il progetto `C#`.
    - Digitare **dotnet new classlib -o IoTEdgeConverterModule -f netstandard1.3**. 
    - Questo comando crea una classe vuota denominata `Class1.cs` nella directory dei progetti.
2. Passare alla cartella in cui si è appena creato il progetto libreria di classi digitando **cd IoTEdgeConverterModule**.
3. Aprire il progetto in `Visual Studio Code` digitando **code .**.
4. Dopo avere aperto il progetto in `Visual Studio Code`, fare clic su **IoTEdgeConverterModule.csproj** per aprire il file come illustrato nell'immagine seguente:

    ![Finestra di modifica di Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-edit-csproj.png)

5. Inserire il BLOB `XML` mostrato nel frammento di codice seguente tra i tag di chiusura `PropertyGroup` e `Project`, riga sei nell'immagine precedente e salvare il file premendo `Ctrl` + `S`.

   ```xml
     <ItemGroup>
       <PackageReference Include="Microsoft.Azure.Devices.Gateway.Module.NetStandard" Version="1.0.5" />
       <PackageReference Include="System.Threading.Thread" Version="4.3.0" />
       <PackageReference Include="Newtonsoft.Json" Version="10.0.2" />
     </ItemGroup> 
   ```

6. Dopo avere salvato il file con estensione `.csproj`, `Visual Studio Code` dovrebbe visualizzare una finestra `unresolved dependencies` come mostrato nell'immagine seguente: 

    ![Finestra di Visual Studio Code per il ripristino delle dipendenze](media/iot-hub-iot-edge-create-module/vscode-restore.png)

    a) Fare clic su `Restore` per ripristinare tutti i riferimenti nel file `.csproj` di progetto, inclusi i `PackageReferences` precedentemente aggiunti. 

    b) `Visual Studio Code` crea automaticamente il file `project.assets.json` nella cartella `obj` di progetto. Questo file contiene informazioni sulle dipendenze del progetto allo scopo di rendere più veloci i ripristini successivi.
 
    >[!NOTE]
    `.NET Core Tools`sono ora basati su MSBuild. Ciò significa che viene creato un file di progetto `.csproj` anziché un file `project.json`.

    - Se `Visual Studio Code` non riesce a crearlo automaticamente, è possibile eseguire l'operazione manualmente. Aprire la finestra Terminale integrato di `Visual Studio Code` premendo `Ctrl` + `backtick` o usando i menu `View` -> `Integrated Terminal`.
    - Nella finestra `Integrated Terminal` digitare **dotnet restore**.
    
7. Rinominare il file `Class1.cs` in `BleConverterModule.cs`. 

    a) Per rinominare il file fare prima clic sul file e quindi premere `F2`.
    
    b) Digitare il nuovo nome **BleConverterModule**, come mostrato nell'immagine seguente:

    ![Assegnazione di un nuovo nome a una classe da parte di Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-rename.png)

8. Sostituire il codice esistente nel file `BleConverterModule.cs` copiando e incollando il frammento di codice seguente nel file `BleConverterModule.cs`.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Globalization;
   using System.Linq;
   using System.Text;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Gateway;
   using Newtonsoft.Json;
   
   namespace IoTEdgeConverterModule
   {
       public class BleConverterModule : IGatewayModule, IGatewayModuleStart
       {
           private Broker broker;
           private string configuration;
           private int messageCount;

           public void Create(Broker broker, byte[] configuration)
           {
               this.broker = broker;
               this.configuration = System.Text.Encoding.UTF8.GetString(configuration);
           }
   
           public void Start()
           {
           }

           public void Destroy()
           {
           }

           public void Receive(Message received_message)
           {
               string recMsg = Encoding.UTF8.GetString(received_message.Content, 0, received_message.Content.Length);
               BleData receivedData = JsonConvert.DeserializeObject<BleData>(recMsg);

               float temperature = float.Parse(receivedData.Temperature, CultureInfo.InvariantCulture.NumberFormat); 
               Dictionary<string, string> receivedProperties = received_message.Properties;
            
               Dictionary<string, string> properties = new Dictionary<string, string>();
               properties.Add("source", receivedProperties["source"]);
               properties.Add("macAddress", receivedProperties["macAddress"]);
               properties.Add("temperatureAlert", temperature > 30 ? "true" : "false");
   
               String content = String.Format("{0} \"deviceId\": \"Intel NUC Gateway\", \"messageId\": {1}, \"temperature\": {2} {3}", "{", ++this.messageCount, temperature, "}");
               Message messageToPublish = new Message(content, properties);
   
               this.broker.Publish(messageToPublish);
           }
       }
   }
   ```

9. Salvare il file premendo `Ctrl` + `S`.

10. Creare un nuovo file denominato `Untitled-1` premendo `Ctrl` + `N` come mostrato nell'immagine seguente:

    ![Nuovo file di Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-new-file.png)

11. Per deserializzare l'oggetto `JSON` che si riceve dal dispositivo `BLE` simulato, copiare il codice seguente nella finestra dell'editor di codice del file `Untitled-1`. 

   ```csharp
   using System;
   using Newtonsoft.Json;

   namespace IoTEdgeConverterModule
   {
       public class BleData
       {
           [JsonProperty(PropertyName = "temperature")]
           public string Temperature { get; set; }
       }
   }
   ```

12. Salvare il file come `BleData.cs` premendo `Ctrl` + `Shift` + `S`.
    - Nel menu a discesa `Save as Type` della finestra di dialogo Salva con nome selezionare `C# (*.cs;*.csx)` come mostrato nell'immagine seguente:

    ![Finestra di dialogo Salva con nome di Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-save-as.png)

13. Creare un nuovo file denominato `Untitled-1` premendo `Ctrl` + `N`.

14. Copiare e incollare il frammento di codice seguente nel file `Untitled-1`. Questa classe è un modulo di `Azure IoT Edge` che si usa per produrre i dati ricevuti dal `BleConverterModule`.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Gateway;
   using Newtonsoft.Json;
   
   namespace PrinterModule
   {
       public class DotNetPrinterModule : IGatewayModule
       {
           private string configuration;
           public void Create(Broker broker, byte[] configuration)
           {
               this.configuration = System.Text.Encoding.UTF8.GetString(configuration);
           }
   
           public void Destroy()
           {
           }
   
           public void Receive(Message received_message)
           {
               string recMsg = System.Text.Encoding.UTF8.GetString(received_message.Content, 0, received_message.Content.Length);
               Dictionary<string, string> receivedProperties = received_message.Properties;
               
               BleConverterData receivedData = JsonConvert.DeserializeObject<BleConverterData>(recMsg);
   
               Console.WriteLine();
               Console.WriteLine("Module           : [DotNetPrinterModule]");
               Console.WriteLine("received_message : {0}", recMsg);
   
               if(received_message.Properties["source"] == "bleTelemetry")
               {
                   Console.WriteLine("Source           : {0}", receivedProperties["source"]);
                   Console.WriteLine("MAC address      : {0}", receivedProperties["macAddress"]);
                   Console.WriteLine("Temperature Alert: {0}", receivedProperties["temperatureAlert"]);
                   Console.WriteLine("Deserialized Obj : [BleConverterData]");
                   Console.WriteLine("  DeviceId       : {0}", receivedData.DeviceId);
                   Console.WriteLine("  MessageId      : {0}", receivedData.MessageId);
                   Console.WriteLine("  Temperature    : {0}", receivedData.Temperature);
               }
   
               Console.WriteLine();
           }
       }
   }
   ```

15. Salvare il file come `DotNetPrinterModule.cs` premendo `Ctrl` + `Shift` + `S`.
    - Nel menu a discesa `Save as Type` della finestra di dialogo Salva con nome selezionare `C# (*.cs;*.csx)`.

16. Creare un nuovo file denominato `Untitled-1` premendo `Ctrl` + `N`.

17. Per deserializzare l'oggetto `JSON` che si riceve da `BleConverterModule`, copiare e incollare il frammento di codice seguente nel file `Untitled-1`. 

   ```csharp
   using System;
   using Newtonsoft.Json;

   namespace PrinterModule
   {
       public class BleConverterData
       {
           [JsonProperty(PropertyName = "deviceId")]
           public string DeviceId { get; set; }
   
           [JsonProperty(PropertyName = "messageId")]
           public string MessageId { get; set; }
   
           [JsonProperty(PropertyName = "temperature")]
           public string Temperature { get; set; }
       }
   }
   ```

18. Salvare il file come `BleConverterData.cs` premendo `Ctrl` + `Shift` + `S`.
    - Nel menu a discesa `Save as Type` della finestra di dialogo Salva con nome selezionare `C# (*.cs;*.csx)`.

19. Creare un nuovo file denominato `Untitled-1` premendo `Ctrl` + `N`.

20. Copiare e incollare il frammento di codice seguente nel file `Untitled-1`.

   ```json
   {
       "loaders": [
           {
               "type": "dotnetcore",
               "name": "dotnetcore",
               "configuration": {
                   "binding.path": "dotnetcore.dll",
                   "binding.coreclrpath": "C:\\Program Files\\dotnet\\shared\\Microsoft.NETCore.App\\1.1.1\\coreclr.dll",
                   "binding.trustedplatformassemblieslocation": "C:\\Program Files\\dotnet\\shared\\Microsoft.NETCore.App\\1.1.1\\"
               }
           }
       ],
          "modules": [
           {
               "name": "simulated_device",
               "loader": {
                   "name": "native",
                   "entrypoint": {
                       "module.path": "simulated_device.dll"
                   }
               },
               "args": {
                   "macAddress": "01:02:03:03:02:01",
                   "messagePeriod": 500
               }
           },
           {
               "name": "ble_converter_module",
               "loader": {
                   "name": "dotnetcore",
                   "entrypoint": {
                       "assembly.name": "IoTEdgeConverterModule",
                       "entry.type": "IoTEdgeConverterModule.BleConverterModule"
                   }
               },
               "args": ""
           },
           {
               "name": "printer_module",
               "loader": {
                   "name": "dotnetcore",
                   "entrypoint": {
                       "assembly.name": "IoTEdgeConverterModule",
                       "entry.type": "PrinterModule.DotNetPrinterModule"
                   }
               },
               "args": ""
           }
       ],
       "links": [
           {
               "source": "simulated_device", "sink": "ble_converter_module"
           },
           {
               "source": "ble_converter_module", "sink": "printer_module"
           }
   ]
   }
   ```

21. Salvare il file come `gw-config.json` premendo `Ctrl` + `Shift` + `S`.
    - Nel menu a discesa `Save as Type` della finestra di dialogo Salva con nome selezionare `JSON (*.json;*.bowerrc;*.jshintrc;*.jscsrc;*.eslintrc;*.babelrc;*webmanifest)`.

22. Per abilitare la copia del file di configurazione nella directory di output, aggiornare il file `IoTEdgeConverterModule.csproj` con il BLOB XML seguente:

   ```xml
     <ItemGroup>
       <None Update="gw-config.json" CopyToOutputDirectory="PreserveNewest" />
     </ItemGroup>
   ```
    
   - Il file `IoTEdgeConverterModule.csproj` aggiornato dovrebbe essere simile all'immagine seguente:

    ![File con estensione csproj aggiornato di Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-update-csproj.png)

23. Creare un nuovo file denominato `Untitled-1` premendo `Ctrl` + `N`.

24. Copiare e incollare il frammento di codice seguente nel file `Untitled-1`.

   ```powershell
   Copy-Item -Path $env:userprofile\.nuget\packages\microsoft.azure.devices.gateway.native.windows.x64\1.1.3\runtimes\win-x64\native\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.runtime.serialization.formatters\4.3.0\lib\netstandard1.4\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.runtime.serialization.primitives\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\newtonsoft.json\10.0.2\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.componentmodel.typeconverter\4.3.0\lib\netstandard1.5\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.collections.nongeneric\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.collections.specialized\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   ```

25. Salvare il file come `binplace.ps1` premendo `Ctrl` + `Shift` + `S`.
    - Nel menu a discesa `Save as Type` della finestra di dialogo Salva con nome selezionare `PowerShell (*.ps1;*.psm1;*.psd1;*.pssc;*.psrc)`.

26. Compilare il progetto premendo `Ctrl` + `Shift` + `B`. Quando si compila il progetto per la prima volta, `Visual Studio Code` visualizza la finestra di dialogo `No build task defined.` come mostrato nell'immagine seguente:

    ![Finestra di dialogo dell'attività di compilazione di Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-build-task.png)

    a) Fare clic sul pulsante `Configure Build Task`.

    b) Nel menu a discesa `Select a Task Runner` della finestra di dialogo selezionare `.NET Core` come mostrato nell'immagine seguente: 

    ![Finestra di dialogo di selezione di un'attività di Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-build-task-runner.png)

    c) Se si fa clic sulla voce `.NET Core`, viene creato il file `tasks.json` nella directory `.vscode` e il file viene aperto nella finestra `code editor`. Non è necessario modificare il file, quindi chiudere la scheda.

27.  Aprire la finestra Terminale integrato di `Visual Studio Code` premendo `Ctrl` + `backtick` o usando i menu `View` -> `Integrated Terminal` e digitare **.\binplace.ps1** al prompt dei comandi di `PowerShell`. Questo comando copia tutte le dipendenze nella directory di output.

28. Passare alla directory di output dei progetti nella finestra `Integrated Terminal` digitando **cd .\bin\Debug\netstandard1.3**.

29. Eseguire il progetto di esempio digitando **.\gw.exe gw-config.json** al prompt della finestra `Integrated Terminal`. 
    - Se sono stati seguiti attentamente i passaggi dell'esercitazione, si dovrebbe eseguire il progetto di esempio `Azure IoT Edge BLE Data Converter Module` come mostrato nell'immagine seguente:
    
        ![Esempio di dispositivo simulato in esecuzione in Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-run.png)
    
    - Se si intende terminare l'applicazione premere `<Enter>`.

>[!IMPORTANT]
Non è consigliabile usare `Ctrl` + `C` per terminare l'applicazione gateway `IoT Edge` (vale a dire **gw.exe**) in quanto questa azione può determinare un arresto anomalo del processo.


