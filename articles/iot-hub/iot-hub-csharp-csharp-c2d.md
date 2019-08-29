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
ms.openlocfilehash: 99acd43128bedcf3dba470f84c0a406861d77e2d
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147784"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Inviare messaggi dal cloud al dispositivo con Hub IoT (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

L'hub IoT di Azure è un servizio completamente gestito che consente di abilitare comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi e un back-end della soluzione. La Guida introduttiva inviare dati di telemetria [da un dispositivo a un hub](quickstart-send-telemetry-dotnet.md) Internet viene illustrato come creare un hub Internet, effettuare il provisioning di un'identità del dispositivo al suo interno e scrivere codice per un'app per dispositivi che invia messaggi da dispositivo a cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Questa esercitazione si basa sull' [invio di dati di telemetria da un dispositivo a un hub](quickstart-send-telemetry-dotnet.md)Internet. Viene illustrato come eseguire le attività seguenti:

* Dal back-end della soluzione inviare messaggi da cloud a dispositivo a un singolo dispositivo tramite l'hub IoT.

* Ricevere messaggi da cloud a dispositivo in un dispositivo.

* Dal back-end della soluzione, richiedere il riconoscimento del recapito (*feedback*) per i messaggi inviati a un dispositivo dall'hub Internet.

Altre informazioni sui messaggi da cloud a dispositivo sono disponibili in [Messaggistica da dispositivo a cloud e da cloud a dispositivo con l'hub IoT](iot-hub-devguide-messaging.md).

Al termine di questa esercitazione vengono eseguite due app console .NET.

* **SimulatedDevice**. Questa app si connette all'hub Internet e riceve i messaggi da cloud a dispositivo. Questa app è una versione modificata dell'app creata in inviare dati di telemetria [da un dispositivo a un hub](quickstart-send-telemetry-dotnet.md)Internet.

* **SendCloudToDevice**. Questa app invia un messaggio da cloud a dispositivo all'app per dispositivo tramite l'hub Internet e quindi riceve la conferma di recapito.

> [!NOTE]
> L'hub Internet è dotato di supporto SDK per molte piattaforme e linguaggi per dispositivi, tra cui C, Java, Python e JavaScript, tramite gli [SDK per dispositivi Azure](iot-hub-devguide-sdks.md). Per istruzioni dettagliate su come connettere il dispositivo al codice dell'esercitazione e in generale all'hub IoT di Azure, vedere la [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md).
>

## <a name="prerequisites"></a>Prerequisiti

* Visual Studio

* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

## <a name="receive-messages-in-the-device-app"></a>Ricevere messaggi nell'app per dispositivi

In questa sezione si modifica l'app per dispositivi creata in inviare dati di telemetria [da un dispositivo a un hub](quickstart-send-telemetry-dotnet.md) Internet per ricevere i messaggi da cloud a dispositivo dall'hub Internet delle cose.

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

Il metodo `ReceiveAsync` restituisce in modo asincrono il messaggio ricevuto nel momento in cui viene ricevuto dal dispositivo. Restituisce *null* dopo un periodo di timeout specificabile. In questo esempio viene usato il valore predefinito di un minuto. Quando l'app riceve un valore *Null*, deve rimanere in attesa di nuovi messaggi. Questo requisito è il motivo per la riga `if (receivedMessage == null) continue`.

La chiamata a `CompleteAsync()` notifica all'hub IoT che il messaggio è stato elaborato correttamente. Il messaggio può essere rimosso dalla coda del dispositivo in modo sicuro. Se si è verificato un evento che ha impedito all'app per dispositivo di completare l'elaborazione del messaggio, l'hub IoT lo recapita nuovamente. La logica di elaborazione del messaggio nell'app per dispositivo deve essere *idempotente*, in modo che la ricezione dello stesso messaggio più volte produca lo stesso risultato.

Un'applicazione può anche abbandonare temporaneamente un messaggio e, di conseguenza, l'hub IoT mantiene il messaggio nella coda per un uso futuro. In alternativa, l'applicazione può rifiutare un messaggio, rimuovendolo così definitivamente dalla coda. Per altre informazioni sul ciclo di vita dei messaggi da cloud a dispositivo, vedere [Messaggistica da dispositivo a cloud e da cloud a dispositivo con l'hub IoT](iot-hub-devguide-messaging.md).

   > [!NOTE]
   > Quando si usa HTTPS invece di MQTT o AMQP come trasporto, il metodo `ReceiveAsync` verrà restituito immediatamente. Il modello supportato per i messaggi da cloud a dispositivo con HTTPS è rappresentato da dispositivi collegati in modo intermittente che controllano raramente la presenza di messaggi (meno di ogni 25 minuti). La generazione di altre ricezioni HTTPS comporta la limitazione delle richieste da parte dell'hub IoT. Per altre informazioni sulle differenze tra il supporto di MQTT, AMQP e HTTPS e sulla limitazione delle richieste dell'hub IoT, vedere [Messaggistica da dispositivo a cloud e da cloud a dispositivo con l'hub IoT](iot-hub-devguide-messaging.md).
   >

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub Internet

In questo articolo viene creato un servizio back-end per l'invio di messaggi da cloud a dispositivo tramite l'hub Internet delle cose creato in inviare dati di telemetria [da un dispositivo a un hub](quickstart-send-telemetry-dotnet.md)Internet. Per inviare messaggi da cloud a dispositivo, il servizio richiede l'autorizzazione **Connect del servizio** . Per impostazione predefinita, ogni hub tutto viene creato con un criterio di accesso condiviso denominato **Service** che concede l'autorizzazione.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Inviare un messaggio da cloud a dispositivo

A questo punto si scrive un'app console .NET che invia messaggi da cloud a dispositivo all'app per dispositivo.

1. Nella soluzione di Visual Studio corrente selezionare **file** > **nuovo** > **progetto**. In **Crea un nuovo progetto**selezionare **App Console (.NET Framework)** e quindi fare clic su **Avanti**.

1. Denominare il progetto *SendCloudToDevice*. In **soluzione**selezionare **Aggiungi a soluzione** e accettare la versione più recente del .NET Framework. Selezionare **Crea** per creare il progetto.

   ![Configurare un nuovo progetto in Visual Studio](./media/iot-hub-csharp-csharp-c2d/sendcloudtodevice-project-configure.png)

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla nuova soluzione e quindi scegliere **Gestisci pacchetti NuGet**.

1. In **Gestisci pacchetti NuGet**selezionare **Sfoglia**, quindi cercare e selezionare **Microsoft. Azure. Devices**. Selezionare **Installa**.

   Questo passaggio consente di scaricare, installare e aggiungere un riferimento al [pacchetto NuGet Azure Internets Service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

1. Aggiungere l'istruzione `using` seguente all'inizio del file **Program.cs** .

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

1. Aggiungere i campi seguenti alla classe **Program** . Sostituire il valore del segnaposto con la stringa di connessione dell'hub Internet che è stata copiata in precedenza in [ottenere la stringa di connessione dell'hub Internet](#get-the-iot-hub-connection-string).

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

1. Aggiungere il metodo seguente alla classe **Program**. Impostare il nome del dispositivo su quello usato durante la definizione del dispositivo in inviare dati di telemetria [da un dispositivo a un hub](quickstart-send-telemetry-dotnet.md)Internet.

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myFirstDevice", commandMessage);
   }
   ```

   Questo metodo invia un nuovo messaggio da cloud a dispositivo al dispositivo con ID `myFirstDevice`. Modificare questo parametro solo se è stato modificato da quello usato in inviare dati di telemetria [da un dispositivo a un hub](quickstart-send-telemetry-dotnet.md)Internet.

1. Aggiungere infine le righe seguenti al metodo **Main** .

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e selezionare **Imposta progetti di avvio**.

1. In**progetto di avvio** **Proprietà** > comuni selezionare **progetti di avvio multipli**, quindi selezionare l'azione di **avvio** per **ReadDeviceToCloudMessages**, **SimulatedDevice**e **SendCloudToDevice** . Selezionare **OK** per salvare le modifiche.

1. Premere **F5**. Devono avviarsi tutte e tre le applicazioni. Selezionare la finestra **SendCloudToDevice** e premere **INVIO**. Verrà visualizzato il messaggio ricevuto dall'app per dispositivi.

   ![Ricezione di un messaggio da parte dell'app](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>Ricevere feedback di recapito

È possibile richiedere il recapito (o la scadenza) dei riconoscimenti dall'hub Internet per ogni messaggio da cloud a dispositivo. Questa opzione consente al back-end della soluzione di informare facilmente la logica di ripetizione o di compensazione. Per altre informazioni sul feedback dei messaggi da cloud a dispositivo, vedere [Messaggistica da dispositivo a cloud e da cloud a dispositivo con l'hub IoT](iot-hub-devguide-messaging.md).

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

1. Aggiungere la riga seguente nel metodo **Main** , subito dopo `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)`.

   ``` csharp
   ReceiveFeedbackAsync();
   ```

1. Per richiedere feedback del recapito del messaggio da cloud a dispositivo, è necessario specificare una proprietà nel metodo **SendCloudToDeviceMessageAsync** . Aggiungere la riga seguente subito dopo la `var commandMessage = new Message(...);` riga.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

1. Premere **F5**per eseguire le app. Si dovrebbero avviare tutte e tre le applicazioni. Selezionare la finestra **SendCloudToDevice** e premere **INVIO**. Verrà visualizzata la ricezione del messaggio da parte dell'app per dispositivi e, dopo alcuni secondi, la ricezione del messaggio di feedback da parte dell'applicazione **SendCloudToDevice**.

   ![Ricezione di un messaggio da parte dell'app](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> Per semplicità, questa esercitazione non implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio backoff esponenziali, come suggerito nella [gestione degli errori temporanei](/azure/architecture/best-practices/transient-faults).
>

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato come inviare e ricevere messaggi da cloud a dispositivo.

Per avere degli esempi di soluzioni complete che usano l'hub IoT, vedere l'[acceleratore di soluzioni di monitoraggio remoto di Azure IoT](https://docs.microsoft.com/azure/iot-suite/).

Per altre informazioni sullo sviluppo delle soluzioni con l'hub IoT, vedere la [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md).
