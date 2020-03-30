---
title: File di inclusione
description: File di inclusione
services: iot-hub
author: chrissie926
ms.service: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: a5c1ddd085ae65b9920d73f50f993f4646785a69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68883771"
---
## <a name="create-a-module-identity"></a>Creare un'identità del modulo

In questa sezione viene creata un'app console .NET che crea un'identità del dispositivo e un'identità del modulo nel Registro di sistema delle identità nell'hub. Un dispositivo o un modulo non può connettersi all'hub a meno che non disponga di una voce nel Registro di sistema delle identità. Per ulteriori informazioni, vedere la [sezione Registro delle identità della guida per gli sviluppatori dell'hub IoT](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

Quando si esegue questa app console vengono generati un ID e una chiave univoci sia per il dispositivo che per il modulo. Il dispositivo e il modulo usano questi valori per identificarsi quando inviano messaggi da dispositivo a cloud all'hub IoT.Your device and module use these values to identify themselves when it sends device-to-cloud messages to IoT Hub. Negli ID viene fatta distinzione tra maiuscole e minuscole.

1. Aprire Visual Studio e selezionare **Crea un nuovo progetto**.

1. In **Crea un nuovo progetto**selezionare App console **(.NET Framework)**.

1. Selezionare **Avanti** per aprire **Configura il nuovo progetto**. Assegnare al progetto il nome *CreateIdentities* e alla soluzione il nome *IoTHubGetStarted*. Verificare che la versione di .NET Framework sia 4.6.1 o successiva.

    ![Immettere il nome e il framework per la soluzione di Visual Studio](./media/iot-hub-get-started-create-module-identity-csharp/configure-createidentities-project.png)

1. In Visual Studio aprire **Strumenti** > **Gestione** > pacchetti NuGet Gestisci pacchetti**NuGet per soluzione**. Selezionare la scheda **Sfoglia**.

1. Cercare **Microsoft.Azure.Devices**. Selezionarlo e quindi selezionare **Installa**.

    ![Installare la versione corrente di Azure IoT Hub .NET service SDKInstall Azure IoT Hub .NET service SDK current version](./media/iot-hub-get-started-create-module-identity-csharp/install-service-sdk.png)

1. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Common.Exceptions;
   ```

1. Aggiungere i campi seguenti alla classe **Program** . Sostituire il valore del segnaposto con la stringa di connessione dell'hub IoT creato nella sezione precedente.

   ```csharp
   const string connectionString = "<replace_with_iothub_connection_string>";
   const string deviceID = "myFirstDevice";
   const string moduleID = "myFirstModule";
   ```

1. Aggiungere il codice seguente alla classe **Main**.

   ```csharp
   static void Main(string[] args)
   {
       AddDeviceAsync().Wait();
       AddModuleAsync().Wait();
   }
   ```

1. Aggiungere i metodi seguenti alla classe **Program**:

    ```csharp
    private static async Task AddDeviceAsync()
    {
       RegistryManager registryManager = 
         RegistryManager.CreateFromConnectionString(connectionString);
       Device device;

       try
       {
           device = await registryManager.AddDeviceAsync(new Device(deviceID));
       }
       catch (DeviceAlreadyExistsException)
        {
            device = await registryManager.GetDeviceAsync(deviceID);
        }

        Console.WriteLine("Generated device key: {0}", 
          device.Authentication.SymmetricKey.PrimaryKey);
    }

    private static async Task AddModuleAsync()
    {
        RegistryManager registryManager = 
          RegistryManager.CreateFromConnectionString(connectionString);
        Module module;

        try
        {
            module = 
              await registryManager.AddModuleAsync(new Module(deviceID, moduleID));
        }
        catch (ModuleAlreadyExistsException)
        {
            module = await registryManager.GetModuleAsync(deviceID, moduleID);
        }

        Console.WriteLine("Generated module key: {0}", module.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    Il `AddDeviceAsync` metodo crea un'identità del dispositivo con ID **myFirstDevice**. Se tale ID dispositivo esiste già nel registro delle identità, il codice recupera semplicemente le informazioni sul dispositivo esistente. L'app visualizzerà quindi la chiave primaria per l'identità. Questa chiave viene usata nell'app per dispositivi simulata per connettersi all'hub.

    Il `AddModuleAsync` metodo crea un'identità del modulo con ID **myFirstModule** nel dispositivo **myFirstDevice**. Se tale ID modulo esiste già nel registro delle identità, il codice recupera semplicemente le informazioni sul modulo esistente. L'app visualizzerà quindi la chiave primaria per l'identità. Questa chiave viene usata nell'app del modulo simulato per connettersi all'hub.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Eseguire questa applicazione e prendere nota del tasto del dispositivo e il tasto del modulo.

> [!NOTE]
> Il registro delle identità dell'hub IoT archivia solo le identità di dispositivi e moduli per consentire l'accesso sicuro all'hub. Il registro delle identità archivia gli ID dispositivo e le chiavi da usare come credenziali di sicurezza. Il registro delle identità archivia anche un flag di abilitazione/disabilitazione per ogni dispositivo che consente di disabilitare l'accesso per un dispositivo. Se l'app deve archiviare altri metadati specifici del dispositivo, deve usare un archivio specifico dell'applicazione. Non esiste alcun flag abilitato/disabilitato per le identità del modulo. Per altre informazioni, vedere [La guida per gli sviluppatori dell'hub IoT](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
