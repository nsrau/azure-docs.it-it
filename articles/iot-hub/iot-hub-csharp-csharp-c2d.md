---
title: Messaggi da cloud a dispositivo con l'hub IoT di Azure (.NET) | Documentazione Microsoft
description: Come inviare messaggi da cloud a dispositivo a un dispositivo da un hub IoT di Azure usando gli SDK di Azure IoT per .NET. Si modifica un'app per dispositivi per ricevere i messaggi diretti dal cloud al dispositivo e si modifica un'app back-end per inviare i messaggi diretti dal cloud al dispositivo.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: robinsh
ms.openlocfilehash: 7805b9b3f000b2bc2e45272ab9ff469d5711e581
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110209"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Inviare messaggi dal cloud al dispositivo con Hub IoT (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

L'hub IoT di Azure è un servizio completamente gestito che consente di abilitare comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi e un back-end della soluzione. La guida introduttiva [Invia dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-dotnet.md) mostra come creare un hub IoT, eseguire il provisioning di un'identità del dispositivo in esso e codificare un'app per dispositivi che invia messaggi da dispositivo a cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Questa esercitazione si basa [sull'invio di dati di telemetria da un dispositivo a un hub IoT.This](quickstart-send-telemetry-dotnet.md)tutorial builds on Send telemetry from a device to an IoT hub . Viene illustrato come eseguire le attività seguenti:It shows you how to do the following tasks:

* Dal back-end della soluzione inviare messaggi da cloud a dispositivo a un singolo dispositivo tramite l'hub IoT.

* Ricevere messaggi da cloud a dispositivo in un dispositivo.

* Dal back-end della soluzione, richiedere il riconoscimento di recapito ( feedback ) per i messaggi inviati a un dispositivo dall'hub IoT.From your solution back-end, request delivery acknowledgment (*feedback*) for messages sent to a device from ioT Hub.

Altre informazioni sui messaggi da cloud a dispositivo sono disponibili in [Messaggistica da dispositivo a cloud e da cloud a dispositivo con l'hub IoT](iot-hub-devguide-messaging.md).

Al termine di questa esercitazione, si eseguono due applicazioni console .NET.

* **SimulatedDevice**. Questa app si connette all'hub IoT e riceve messaggi da cloud a dispositivo. Questa app è una versione modificata dell'app creata in [Invia dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-dotnet.md).

* **SendCloudToDevice**. Questa app invia un messaggio da cloud a dispositivo all'app del dispositivo tramite l'hub IoT e quindi riceve il riconoscimento di recapito.

> [!NOTE]
> IoT Hub include il supporto SDK per molte piattaforme e linguaggi di dispositivi, tra cui C, Java, Python e Javascript, tramite gli SDK dei dispositivi [Azure IoT.](iot-hub-devguide-sdks.md) Per istruzioni dettagliate su come connettere il dispositivo al codice dell'esercitazione e in generale all'hub IoT di Azure, vedere la [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md).
>

## <a name="prerequisites"></a>Prerequisiti

* Visual Studio

* Un account Azure attivo. Se non hai un account, puoi creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

* Assicurarsi che la porta 8883 sia aperta nel firewall. L'esempio di dispositivo in questo articolo usa il protocollo MQTT, che comunica tramite la porta 8883.The device sample in this article uses MQTT protocol, which communicates over port 8883. Questa porta potrebbe essere bloccata in alcuni ambienti di rete aziendali e didattici. Per altre informazioni e soluzioni alternative per questo problema, vedere [Connettersi all'hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-device-app"></a>Ricevere messaggi nell'app per dispositivi

In questa sezione modificare l'app per dispositivi creata in [Inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-dotnet.md) per ricevere messaggi da cloud a dispositivo dall'hub IoT.In this section, modify the device app you created in Send telemetry from a device to an IoT hub to receive cloud-to-device messages from the IoT hub.

1. In Visual Studio, nel progetto **SimulatedDevice** aggiungere il seguente metodo alla classe **Program**.

   ```csharp
    private static async void ReceiveC2dAsync()
    {
        Console.WriteLine("\nReceiving cloud to device messages from service");
        while (true)
        {
            Message receivedMessage = await s_deviceClient.ReceiveAsync();
            if (receivedMessage == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received message: {0}", 
            Encoding.ASCII.GetString(receivedMessage.GetBytes()));
            Console.ResetColor();

            await s_deviceClient.CompleteAsync(receivedMessage);
        }
    }
   ```

1. Aggiungere il metodo seguente al metodo **Main** immediatamente prima della riga `Console.ReadLine()`:

   ```csharp
   ReceiveC2dAsync();
   ```

Il metodo `ReceiveAsync` restituisce in modo asincrono il messaggio ricevuto nel momento in cui viene ricevuto dal dispositivo. Restituisce *null* dopo un periodo di timeout specificabile. In questo esempio viene utilizzato il valore predefinito di un minuto. Quando l'app riceve un valore *Null*, deve rimanere in attesa di nuovi messaggi. Questo requisito è il motivo per la riga `if (receivedMessage == null) continue`.

La chiamata a `CompleteAsync()` notifica all'hub IoT che il messaggio è stato elaborato correttamente. Il messaggio può essere rimosso dalla coda del dispositivo in modo sicuro. Se si è verificato un evento che ha impedito all'app per dispositivo di completare l'elaborazione del messaggio, l'hub IoT lo recapita nuovamente. La logica di elaborazione dei messaggi nell'app per dispositivi deve essere *idempotente,* in modo che la ricezione dello stesso messaggio più volte produca lo stesso risultato.

Un'applicazione può anche abbandonare temporaneamente un messaggio e, di conseguenza, l'hub IoT mantiene il messaggio nella coda per un uso futuro. In alternativa, l'applicazione può rifiutare un messaggio, rimuovendolo così definitivamente dalla coda. Per altre informazioni sul ciclo di vita dei messaggi da cloud a dispositivo, vedere [Messaggistica da dispositivo a cloud e da cloud a dispositivo con l'hub IoT](iot-hub-devguide-messaging.md).

   > [!NOTE]
   > Quando si usa HTTPS invece di MQTT o AMQP come trasporto, il metodo `ReceiveAsync` verrà restituito immediatamente. Il modello supportato per i messaggi da cloud a dispositivo con HTTPS è rappresentato da dispositivi collegati in modo intermittente che controllano raramente la presenza di messaggi (meno di ogni 25 minuti). La generazione di altre ricezioni HTTPS comporta la limitazione delle richieste da parte dell'hub IoT. Per altre informazioni sulle differenze tra il supporto di MQTT, AMQP e HTTPS e sulla limitazione delle richieste dell'hub IoT, vedere [Messaggistica da dispositivo a cloud e da cloud a dispositivo con l'hub IoT](iot-hub-devguide-messaging.md).
   >

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub IoTGet the IoT hub connection string

In questo articolo viene creato un servizio back-end per l'invio di messaggi da cloud a dispositivo tramite l'hub IoT creato in [Inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-dotnet.md). Per inviare messaggi da cloud a dispositivo, il servizio deve disporre dell'autorizzazione di connessione del **servizio.** Per impostazione predefinita, ogni hub IoT viene creato con un criterio di accesso condiviso denominato **servizio** che concede questa autorizzazione.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Inviare un messaggio da cloud a dispositivo

A questo punto si scrive un'app console .NET che invia messaggi da cloud a dispositivo all'app per dispositivi.

1. Nella soluzione di Visual Studio corrente selezionare **File** > **Nuovo** > **progetto**. In **Crea un nuovo progetto**selezionare App console **(.NET Framework)** e quindi **avanti**.

1. Denominare il progetto *SendCloudToDevice*. In **Soluzione**selezionare **Aggiungi alla soluzione** e accettare la versione più recente di .NET Framework. Selezionare **Crea** per creare il progetto.

   ![Configurare un nuovo progetto in Visual StudioConfigure a new project in Visual Studio](./media/iot-hub-csharp-csharp-c2d/sendcloudtodevice-project-configure.png)

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla nuova soluzione, quindi **scegliere Gestisci pacchetti NuGet**.

1. In **Gestisci pacchetti NuGet**selezionare **Sfoglia**, quindi cercare e selezionare **Microsoft.Azure.Devices**. Selezionare **Installa**.

   Questo passaggio scarica, installa e aggiunge un riferimento al [pacchetto NuGet dell'SDK del servizio Azure IoT.](https://www.nuget.org/packages/Microsoft.Azure.Devices/)

1. Aggiungere la `using` seguente istruzione all'inizio del file **Program.cs.**

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

1. Aggiungere i campi seguenti alla classe **Program** . Sostituire il valore segnaposto con la stringa di connessione dell'hub IoT copiata in precedenza in Ottenere la stringa di [connessione dell'hub IoT](#get-the-iot-hub-connection-string).

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

1. Aggiungere il metodo seguente alla classe **Program.** Impostare il nome del dispositivo su quello usato quando si definisce il dispositivo in [Invia dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-dotnet.md).

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myFirstDevice", commandMessage);
   }
   ```

   Questo metodo invia un nuovo messaggio da cloud a dispositivo al dispositivo con ID `myFirstDevice`. Modificare questo parametro solo se è stato modificato da quello usato in [Invia dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-dotnet.md).

1. Aggiungere infine le righe seguenti al metodo **Main** .

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e scegliere Imposta progetti di **avvio**.

1. In**Progetto di avvio** **delle proprietà** > comuni selezionare **Progetti di avvio multipli**, quindi selezionare l'azione **Avvia** per **ReadDeviceToCloudMessages**, **SimulatedDevice**e **SendCloudToDevice**. Selezionare **OK** per salvare le modifiche.

1. Premere **F5**. Devono avviarsi tutte e tre le applicazioni. Selezionare la finestra **SendCloudToDevice** e premere **INVIO**. Verrà visualizzato il messaggio ricevuto dall'app per dispositivi.

   ![Ricezione di un messaggio da parte dell'app](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>Ricevere feedback di recapito

È possibile richiedere i riconoscimenti di recapito (o scadenza) dall'hub IoT per ogni messaggio da cloud a dispositivo. Questa opzione consente al back-end della soluzione di informare facilmente la logica di ripetizione o di compensazione. Per altre informazioni sul feedback dei messaggi da cloud a dispositivo, vedere [Messaggistica da dispositivo a cloud e da cloud a dispositivo con l'hub IoT](iot-hub-devguide-messaging.md).

In questa sezione si modifica l'app **SendCloudToDevice** per richiedere feedback e riceverlo dall'hub IoT.

1. In Visual Studio, nel progetto **SendCloudToDevice** aggiungere il seguente metodo alla classe **Programma**.

   ```csharp
   private async static void ReceiveFeedbackAsync()
   {
        var feedbackReceiver = serviceClient.GetFeedbackReceiver();

        Console.WriteLine("\nReceiving c2d feedback from service");
        while (true)
        {
            var feedbackBatch = await feedbackReceiver.ReceiveAsync();
            if (feedbackBatch == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received feedback: {0}",
              string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
            Console.ResetColor();

            await feedbackReceiver.CompleteAsync(feedbackBatch);
        }
    }
    ```

    Si noti che il modello di ricezione è identico a quello usato per ricevere messaggi da cloud a dispositivo dall'app per dispositivo.

1. Aggiungere la riga **Main** seguente nel metodo `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)`Main , subito dopo .

   ``` csharp
   ReceiveFeedbackAsync();
   ```

1. Per richiedere feedback del recapito del messaggio da cloud a dispositivo, è necessario specificare una proprietà nel metodo **SendCloudToDeviceMessageAsync** . Aggiungere la riga seguente, `var commandMessage = new Message(...);` subito dopo la riga.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

1. Premere **F5**per eseguire le app. Si dovrebbero avviare tutte e tre le applicazioni. Selezionare la finestra **SendCloudToDevice** e premere **INVIO**. Verrà visualizzata la ricezione del messaggio da parte dell'app per dispositivi e, dopo alcuni secondi, la ricezione del messaggio di feedback da parte dell'applicazione **SendCloudToDevice**.

   ![Ricezione di un messaggio da parte dell'app](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> Per semplicità, questa esercitazione non implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione, è necessario implementare criteri di ripetizione dei tentativi, ad esempio backoff esponenziale, come suggerito in [Gestione degli errori temporanei](/azure/architecture/best-practices/transient-faults).
>

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato come inviare e ricevere messaggi da cloud a dispositivo.

Per avere degli esempi di soluzioni complete che usano l'hub IoT, vedere l'[acceleratore di soluzioni di monitoraggio remoto di Azure IoT](https://docs.microsoft.com/azure/iot-suite/).

Per altre informazioni sullo sviluppo delle soluzioni con l'hub IoT, vedere la [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md).
