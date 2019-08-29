---
title: Introduzione all'identità del modulo e ai moduli gemelli dell'hub IoT di Azure (portale e .NET) | Microsoft Docs
description: Come creare l'identità del modulo e aggiornare il modulo gemello usando il portale e .NET.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 395b1fd3301925db0607f775c6b7367979ba367b
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147425"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Creare l'identità del modulo e i moduli gemelli dell'hub IoT con il portale e l'SDK per dispositivi .NET

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Le identità del modulo e i moduli gemelli](iot-hub-devguide-module-twins.md) sono simili alle identità del dispositivo e ai dispositivi gemelli dell'hub IoT di Azure, ma offrono una granularità superiore. Mentre l'identità del dispositivo e il dispositivo gemello dell'hub Azure è possibile abilitare l'applicazione back-end per configurare un dispositivo e fornire visibilità sulle condizioni del dispositivo, un'identità del modulo e un modulo gemello forniscono queste funzionalità per i singoli componenti di un dispositivo. Nei dispositivi compatibili con più componenti, ad esempio dispositivi basati sul sistema operativo o dispositivi firmware, le identità dei moduli e i moduli gemelli consentono la configurazione e le condizioni isolate per ogni componente.
>

In questa esercitazione si apprenderà:

* Come creare un'identità del modulo nel portale.

* Come usare un SDK per dispositivi .NET per aggiornare il modulo gemello dal dispositivo.

> [!NOTE]
> Per informazioni sugli SDK di Azure per le cose che è possibile usare per compilare entrambe le applicazioni da eseguire nei dispositivi e il back-end della soluzione, vedere gli [SDK di Azure](iot-hub-devguide-sdks.md).
>

## <a name="prerequisites"></a>Prerequisiti

* Visual Studio.

* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

## <a name="create-a-hub"></a>Creare un hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-hub"></a>Registrare un nuovo dispositivo nell'hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>Creare un'identità del modulo nel portale

In una identità del dispositivo è possibile creare fino a 20 identità del modulo. Per aggiungere un'identità, attenersi alla procedura seguente:

1. Per il dispositivo creato nella sezione precedente, scegliere **Aggiungi identità modulo** per creare la prima identità del modulo.

1. Immettere il nome *myFirstModule*. Salvare l'identità del modulo.

    ![Aggiungi identità modulo](./media/iot-hub-portal-csharp-module-twin-getstarted/add-module-identity.png)

    La nuova identità del modulo viene visualizzata nella parte inferiore della schermata. Selezionarlo per visualizzare i dettagli relativi all'identità del modulo.

    ![Vedere i dettagli relativi all'identità del modulo](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

Salvare la **stringa di connessione-chiave primaria**. Viene usato nella sezione successiva per configurare il modulo nel dispositivo.

## <a name="update-the-module-twin-using-net-device-sdk"></a>Aggiornare il modulo gemello usando l'SDK per dispositivi .NET

L'identità del modulo è stata correttamente creata nell'hub IoT. Proviamo ora a comunicare con il cloud dal dispositivo simulato. Una volta creata un'identità del modulo, viene implicitamente creato anche un modulo gemello nell'hub IoT. In questa sezione, nel dispositivo simulato verrà creata un'app console .NET che aggiorna le proprietà segnalate del modulo gemello.

### <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

Per creare un'app che aggiorna le proprietà segnalate dal modulo gemello, attenersi alla procedura seguente:

1. In Visual Studio selezionare **Crea un nuovo progetto**, quindi scegliere **App Console (.NET Framework)** e fare clic su **Avanti**.

1. In **Configura il nuovo progetto**, immettere *UpdateModuleTwinReportedProperties* come **nome del progetto**. Fare clic su **Crea** per continuare.

    ![Configura sei un progetto di Visual Studio](./media/iot-hub-portal-csharp-module-twin-getstarted/configure-twins-project.png)

### <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>Installare la versione più recente di Azure IoT Hub SDK per dispositivi .NET

Le funzionalità di identità del modulo e di modulo gemello sono disponibili in anteprima pubblica. È disponibile solo nella versione non definitiva degli SDK per dispositivi. Per installarlo, attenersi alla seguente procedura:

1. In Visual Studio aprire **strumenti** >  > **Gestione pacchetti NuGet** **Gestisci pacchetti NuGet per la soluzione**.

1. Selezionare **Sfoglia**, quindi selezionare **Includi versione preliminare**. Cercare *Microsoft. Azure. Devices. client*. Selezionare la versione più recente e installarla.

    ![Installare l'anteprima dell'SDK del servizio .NET dell'hub Azure.](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

    Ora si ha accesso a tutte le funzionalità dei moduli.

### <a name="get-your-module-connection-string"></a>Ottenere la stringa di connessione del modulo

È necessaria la stringa di connessione del modulo per l'app console. A tale scopo, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Passare all'hub Internet e selezionare **dispositivi**. Aprire **myFirstDevice** . si noterà che **myFirstModule** è stato creato correttamente.

1. Selezionare **myFirstModule** in **identità modulo**. In **Dettagli identità modulo**copiare la **stringa di connessione (chiave primaria)** .

    ![Dettagli del modulo nel portale di Azure](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

### <a name="create-updatemoduletwinreportedproperties-console-app"></a>Creare un'app console UpdateModuleTwinReportedProperties

Per creare l'app, seguire questa procedura:

1. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :

  ```csharp
  using Microsoft.Azure.Devices.Client;
  using Microsoft.Azure.Devices.Shared;
  using Newtonsoft.Json;
  ```

2. Aggiungere i campi seguenti alla classe **Program** . Sostituire il valore del segnaposto con la stringa di connessione del modulo.

  ```csharp
  private const string ModuleConnectionString = "<Your module connection string>";
  private static ModuleClient Client = null;
  ```

3. Aggiungere il metodo **OnDesiredPropertyChanged** seguente alla classe **Program**:

  ```csharp
  private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
      {
          Console.WriteLine("desired property change:");
          Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
          Console.WriteLine("Sending current time as reported property");
          TwinCollection reportedProperties = new TwinCollection
          {
              ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
          };
  
          await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
      }
  ```

4. Sostituire infine il metodo **Main** con il codice seguente:

  ```csharp
  static void Main(string[] args)
  {
      Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;
  
      try
      {
          Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
          Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
          Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();
  
          Console.WriteLine("Retrieving twin");
          var twinTask = Client.GetTwinAsync();
          twinTask.Wait();
          var twin = twinTask.Result;
          Console.WriteLine(JsonConvert.SerializeObject(twin));
  
          Console.WriteLine("Sending app start time as reported property");
          TwinCollection reportedProperties = new TwinCollection();
          reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;
  
          Client.UpdateReportedPropertiesAsync(reportedProperties);
      }
      catch (AggregateException ex)
      {
          Console.WriteLine("Error in sample: {0}", ex);
      }
  
      Console.WriteLine("Waiting for Events.  Press enter to exit...");
      Console.ReadKey();
      Client.CloseAsync().Wait();
  }
  
  private static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
  {
      Console.WriteLine($"Status {status} changed: {reason}");
  }
  ```
  
  È possibile compilare ed eseguire l'app con **F5**.

Questo esempio di codice mostra come recuperare il modulo gemello e aggiornare le proprietà segnalate con il protocollo AMQP. L'anteprima pubblica supporta AMQP solo per le operazioni di modulo gemello.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle attività iniziali con l'hub IoT e per esplorare altri scenari IoT, vedere:

* [Introduzione all'identità del modulo dell'hub Internet e al modulo gemello con il backup .NET e il dispositivo .NET](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [Introduzione a IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
