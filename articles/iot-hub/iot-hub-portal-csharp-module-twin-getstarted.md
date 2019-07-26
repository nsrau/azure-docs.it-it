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
ms.date: 04/26/2018
ms.openlocfilehash: ce71c64aff66ea94282a82c1f1b1ee564e74f192
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403897"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Creare l'identità del modulo e i moduli gemelli dell'hub IoT con il portale e l'SDK per dispositivi .NET

> [!NOTE]
> [Le identità del modulo e i moduli gemelli](iot-hub-devguide-module-twins.md) sono simili alle identità del dispositivo e ai dispositivi gemelli dell'hub IoT di Azure, ma offrono una granularità superiore. Mentre l'identità del dispositivo e il dispositivo gemello dell'hub IoT di Azure consentono all'applicazione back-end di configurare un dispositivo e forniscono visibilità sulle condizioni del dispositivo, l'identità del modulo e il modulo gemello forniscono queste funzionalità per i singoli componenti di un dispositivo. Nei dispositivi con più componenti, ad esempio i dispositivi basati su sistema operativo o i dispositivi firmware, è possibile isolare la configurazione e le condizioni di ogni componente.
>

In questa esercitazione si apprenderà:

1. Come creare un'identità del modulo nel portale.

2. Come usare un SDK per dispositivi .NET aggiornare il modulo gemello dal dispositivo.

> [!NOTE]
> Per informazioni sugli Azure IoT SDK che consentono di compilare le applicazioni da eseguire nei dispositivi e i back-end della soluzione, vedere [Azure IoT SDK](iot-hub-devguide-sdks.md).
>

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Visual Studio.
* Un account Azure attivo. Se non si dispone di un account, è possibile crearne uno [gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrare un nuovo dispositivo nell'hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>Creare un'identità del modulo nel portale

In una identità del dispositivo è possibile creare fino a 20 identità del modulo. Fare clic sul pulsante **Add Module Identity** (Aggiungi identità modulo) in alto per creare la prima identità del modulo, denominata **myFirstModule**.

  ![Device details](./media/iot-hub-portal-csharp-module-twin-getstarted/create-module-id.png)

Salvare e fare clic sull'identità del modulo appena creata. Verranno visualizzati i dettagli dell'identità del modulo. Salvare la stringa di connessione - chiave primaria Verrà usata nelle prossima sezione per configurare il modulo nel dispositivo.

  ![Device details](./media/iot-hub-portal-csharp-module-twin-getstarted/module-details.png)

## <a name="update-the-module-twin-using-net-device-sdk"></a>Aggiornare il modulo gemello usando l'SDK per dispositivi .NET

L'identità del modulo è stata correttamente creata nell'hub IoT. Proviamo ora a comunicare con il cloud dal dispositivo simulato. Una volta creata un'identità del modulo, viene implicitamente creato anche un modulo gemello nell'hub IoT. In questa sezione, nel dispositivo simulato verrà creata un'app console .NET che aggiorna le proprietà segnalate del modulo gemello.

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

In Visual Studio aggiungere un progetto desktop di Windows classico Visual C# alla soluzione esistente usando il modello di progetto **App console (.NET Framework)** . Verificare che la versione di .NET Framework sia 4.6.1 o successiva. Assegnare al progetto il nome **UpdateModuleTwinReportedProperties**.

  ![Creare un progetto di Visual Studio](./media/iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.png)

## <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>Installare la versione più recente di Azure IoT Hub SDK per dispositivi .NET

Le funzionalità di identità del modulo e di modulo gemello sono disponibili in anteprima pubblica. È disponibile solo nella versione non definitiva degli SDK per dispositivi. In Visual Studio aprire Strumenti > Gestione pacchetti NuGet > Gestisci pacchetti NuGet per la soluzione. Cercare Microsoft.Azure.Devices.Client. Assicurarsi di aver selezionato la casella di controllo Includi versione preliminare. Selezionare la versione più recente e installarla. Ora si ha accesso a tutte le funzionalità dei moduli.

  ![Installare Azure IoT SDK per dispositivi .NET V1.16.0-preview-005](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

## <a name="get-your-module-connection-string"></a>Ottenere la stringa di connessione del modulo

Accedere al [portale di Azure](https://portal.azure.com/). Passare all'hub IoT e fare clic su Dispositivi IoT. Trovare myFirstDevice e aprirlo. Si vedrà che myFirstModule è stato creato correttamente. Copiare la stringa di connessione del modulo. Sarà necessaria nel prossimo passaggio.

  ![Dettagli del modulo nel portale di Azure](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.png)

## <a name="create-updatemoduletwinreportedproperties-console-app"></a>Creare un'app console UpdateModuleTwinReportedProperties

Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :

```csharp
using Microsoft.Azure.Devices.Client;
using Microsoft.Azure.Devices.Shared;
```

Aggiungere i campi seguenti alla classe **Program** . Sostituire il valore del segnaposto con la stringa di connessione del modulo.

```csharp
private const string ModuleConnectionString = "<Your module connection string>";
private static ModuleClient Client = null;
```

Aggiungere il metodo **OnDesiredPropertyChanged** seguente alla classe **Program**:

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

Aggiungere infine le righe seguenti al metodo **Main** :

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

Questo esempio di codice mostra come recuperare il modulo gemello e aggiornare le proprietà segnalate con il protocollo AMQP. L'anteprima pubblica supporta AMQP solo per le operazioni di modulo gemello.

## <a name="run-the-apps"></a>Eseguire le app

A questo punto è possibile eseguire le app. In Esplora soluzioni in Visual Studio fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Imposta progetti di avvio**. Selezionare **Progetti di avvio multipli** e quindi selezionare **Avvia** come azione per l'app console. Premere F5 per avviare entrambe le app in esecuzione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle attività iniziali con l'hub IoT e per esplorare altri scenari IoT, vedere:

* [Introduzione all'identità del modulo dell'hub Internet e al modulo gemello con il backup .NET e il dispositivo .NET](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [Introduzione a IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
