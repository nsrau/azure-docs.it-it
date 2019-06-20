---
title: File di inclusione
description: File di inclusione
services: iot-hub
author: chrissie926
ms.service: iot-hub
ms.topic: include
ms.date: 04/26/2018
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: e78c9a490d2ad02fb132d62b0ab0b55f15d3d4ed
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180800"
---
## <a name="create-a-module-identity"></a>Creare un'identità del modulo

In questa sezione si scriverà un'app console .NET che crea un'identità del dispositivo e un'identità del modulo nel registro delle identità dell'hub IoT. Un dispositivo o un modulo non può connettersi all'hub IoT se non ha una voce nel registro delle identità. Per altre informazioni, vedere la sezione [Registro di identità della Guida per gli sviluppatori dell'hub IoT](../articles/iot-hub/iot-hub-devguide-identity-registry.md). Quando si esegue questa app console vengono generati un ID e una chiave univoci sia per il dispositivo che per il modulo. Il dispositivo e il modulo usano questi valori per identificarsi quando inviano messaggi da dispositivo a cloud all'hub IoT. Negli ID viene fatta distinzione tra maiuscole e minuscole.


1. **Creare un progetto di Visual Studio** - In Visual Studio aggiungere un progetto desktop di Windows classico Visual C# a una nuova soluzione usando il modello di progetto **App console (.NET Framework)** . Verificare che la versione di .NET Framework sia 4.6.1 o successiva. Assegnare al progetto il nome **CreateIdentities** e alla soluzione il nome **IoTHubGetStarted**.

    ![Creare una soluzione di Visual Studio](./media/iot-hub-get-started-create-module-identity-csharp/create-identities-csharp1.JPG)

2. **Installare Azure IoT SDK per servizi .NET V1.16.0-preview-001** - L'identità del modulo e il modulo gemello sono in anteprima pubblica. Sono disponibili solo nella versione preliminare di Azur hub IoT SDK per servizi. In Visual Studio aprire Strumenti > Gestione pacchetti NuGet > Gestisci pacchetti NuGet per la soluzione. Cercare Microsoft.Azure.Devices. Verificare di aver selezionato la casella di controllo Includi versione preliminare. Selezionare la versione 1.16.0-preview-001 e avviare l'installazione. Ora si ha accesso a tutte le funzionalità dei moduli. 

    ![Installare Azure IoT SDK per dispositivi .NET V1.16.0-preview-001](./media/iot-hub-get-started-create-module-identity-csharp/install-sdk.png)

3. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Common.Exceptions;
   ```

4. Aggiungere i campi seguenti alla classe **Program** . Sostituire il valore del segnaposto con la stringa di connessione dell'hub IoT creato nella sezione precedente.

   ```csharp
   const string connectionString = "<replace_with_iothub_connection_string>";
   const string deviceID = "myFirstDevice";
   const string moduleID = "myFirstModule";
   ```

5. Aggiungere il codice seguente alla classe **Main**.
   
   ```csharp
   static void Main(string[] args)
   {
       AddDeviceAsync().Wait();
       AddModuleAsync().Wait();
   }
   ```

6. Aggiungere i metodi seguenti alla classe **Program**:

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

    Il metodo AddDeviceAsync() crea un'identità del dispositivo con ID **myFirstDevice**. Se questo ID dispositivo è già disponibile nel registro delle identità, il codice recupera semplicemente le informazioni sul dispositivo esistenti. L'app visualizzerà quindi la chiave primaria per l'identità. Questa chiave verrà usata dall'app per dispositivo simulato per connettersi all'hub IoT.

    Il metodo AddModuleAsync() crea un'identità del modulo con ID **myFirstModule** nel dispositivo **myFirstDevice**. Se questo ID modulo è già presente nel registro delle identità, il codice recupera semplicemente le informazioni esistenti sul modulo. L'app visualizzerà quindi la chiave primaria per l'identità. Questa chiave verrà usata dall'app per modulo simulato per connettersi all'hub IoT.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

7. Eseguire l'applicazione e prendere nota della chiave del dispositivo e della chiave del modulo.

> [!NOTE]
> Il registro delle identità dell'hub IoT archivia solo le identità del dispositivo e del modulo per abilitare l'accesso sicuro all'hub. Il registro delle identità archivia gli ID dispositivo e le chiavi da usare come credenziali di sicurezza. Il registro delle identità archivia anche un flag di abilitazione/disabilitazione per ogni dispositivo che consente di disabilitare l'accesso per un dispositivo. Se l'applicazione deve archiviare altri metadati specifici del dispositivo, dovrà usare un archivio specifico dell'applicazione. Non esiste alcun flag abilitato/disabilitato per le identità del modulo. Per altre informazioni, vedere la [Guida per gli sviluppatori dell'hub IoT](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
