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
ms.openlocfilehash: 629342e44af16b6d23f9ed85f8c5306c807b8bfc
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621892"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Inviare messaggi dal cloud al dispositivo con Hub IoT (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introduzione

L'hub IoT di Azure è un servizio completamente gestito che consente di abilitare comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi e un back-end della soluzione. Il [inviare i dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-dotnet.md) Guida introduttiva illustra come creare un hub IoT, eseguire il provisioning di un'identità del dispositivo in esso e codice di un'app di dispositivo che invia messaggi da dispositivo a cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Questa esercitazione si basa sul [inviare i dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-dotnet.md). Illustra come eseguire i passaggi seguenti:

* Dal back-end della soluzione inviare messaggi da cloud a dispositivo a un singolo dispositivo tramite l'hub IoT.

* Ricevere messaggi da cloud a dispositivo in un dispositivo.

* Dal back-end della soluzione, chiedere una conferma di recapito (*commenti e suggerimenti*) per i messaggi inviati a un dispositivo dall'IoT Hub.

Altre informazioni sui messaggi da cloud a dispositivo sono disponibili in [Messaggistica da dispositivo a cloud e da cloud a dispositivo con l'hub IoT](iot-hub-devguide-messaging.md).

Al termine di questa esercitazione, si eseguite due App console .NET.

* **SimulatedDevice**, una versione modificata dell'app creata nel [inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-dotnet.md), che si connette all'hub IoT e riceve messaggi da cloud a dispositivo.

* **SendCloudToDevice**, che invia un messaggio da cloud a dispositivo all'app del dispositivo tramite l'IoT Hub e riceve quindi relativo acknowledgment di recapito.

> [!NOTE]
> L’hub IoT dispone del supporto SDK per molte piattaforme e linguaggi, tra cui C, Java e Javascript, tramite gli [SDK del dispositivo IoT Azure](iot-hub-devguide-sdks.md). Per istruzioni dettagliate su come connettere il dispositivo al codice dell'esercitazione e in generale all'hub IoT di Azure, vedere la [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md).
>

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Visual Studio

* Un account Azure attivo. Se non si dispone di un account, è possibile crearne uno [gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

## <a name="receive-messages-in-the-device-app"></a>Ricevere messaggi nell'app per dispositivi

In questa sezione si modificherà l'app per dispositivi creata in [inviare i dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-dotnet.md) per ricevere messaggi da cloud a dispositivo dall'hub IoT.

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

   Il metodo `ReceiveAsync` restituisce in modo asincrono il messaggio ricevuto nel momento in cui viene ricevuto dal dispositivo. Restituisce *null* dopo un periodo di timeout specificabile (in questo caso viene usato il valore predefinito di un minuto). Quando l'app riceve un valore *Null*, deve rimanere in attesa di nuovi messaggi. Questo requisito è il motivo per la riga `if (receivedMessage == null) continue`.

    La chiamata a `CompleteAsync()` notifica all'hub IoT che il messaggio è stato elaborato correttamente. Il messaggio può essere rimosso dalla coda del dispositivo in modo sicuro. Se si è verificato un evento che ha impedito all'app per dispositivo di completare l'elaborazione del messaggio, l'hub IoT lo recapita nuovamente. È quindi importante che la logica di elaborazione del messaggio nell'app per dispositivo sia *idempotente*, in modo che la ricezione dello stesso messaggio più volte produca lo stesso risultato. 

    Un'applicazione può anche abbandonare temporaneamente un messaggio e, di conseguenza, l'hub IoT mantiene il messaggio nella coda per un uso futuro. In alternativa, l'applicazione può rifiutare un messaggio, rimuovendolo così definitivamente dalla coda. Per altre informazioni sul ciclo di vita dei messaggi da cloud a dispositivo, vedere [Messaggistica da dispositivo a cloud e da cloud a dispositivo con l'hub IoT](iot-hub-devguide-messaging.md).

   > [!NOTE]
   > Quando si usa HTTPS invece di MQTT o AMQP come trasporto, il metodo `ReceiveAsync` verrà restituito immediatamente. Il modello supportato per i messaggi da cloud a dispositivo con HTTPS è rappresentato da dispositivi collegati in modo intermittente che controllano raramente la presenza di messaggi (meno di ogni 25 minuti). La generazione di altre ricezioni HTTPS comporta la limitazione delle richieste da parte dell'hub IoT. Per altre informazioni sulle differenze tra il supporto di MQTT, AMQP e HTTPS e sulla limitazione delle richieste dell'hub IoT, vedere [Messaggistica da dispositivo a cloud e da cloud a dispositivo con l'hub IoT](iot-hub-devguide-messaging.md).
   >

2. Aggiungere il metodo seguente al metodo **Main** immediatamente prima della riga `Console.ReadLine()`:

   ```csharp
   ReceiveC2dAsync();
   ```

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'IoT Hub

In primo luogo, è possibile recuperare la stringa di connessione dell'IoT Hub dal portale.

1. Accedi per il [portale di Azure](https://portal.azure.com), selezionare **gruppi di risorse**.

2. Selezionare il gruppo di risorse in uso per questa procedura.

3. Selezionare l'IoT Hub in uso.

4. Nel riquadro per l'hub, selezionare **criteri di accesso condiviso**.

5. selezionare **iothubowner**. Mostra stringhe di connessione sul **iothubowner** pannello. Selezionare l'icona di copia per il **stringa di connessione - chiave primaria**. Salvare la stringa di connessione per usarla successivamente.

   ![Ottenere la stringa di connessione dell'IoT Hub](./media/iot-hub-csharp-csharp-c2d/get-iot-hub-connection-string.png)

## <a name="send-a-cloud-to-device-message"></a>Inviare un messaggio da cloud a dispositivo

Ora possibile scrivere un'app console .NET che invia messaggi da cloud a dispositivo all'app del dispositivo.

1. Nella soluzione corrente di Visual Studio, fare doppio clic sulla soluzione e scegliere Aggiungi > Nuovo progetto. Selezionare **Desktop di Windows** e quindi **App Console (.NET Framework)** . Denominare il progetto **SendCloudToDevice** e selezionare la versione più recente di .NET Framework, quindi selezionare **OK** per creare il progetto.

   ![Nuovo progetto in Visual Studio](./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png)

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Gestisci pacchetti NuGet per la soluzione**.

   L'azione consente di visualizzare la finestra **Gestisci pacchetti NuGet**.

3. Cercare **Microsoft.Azure.Devices**, selezionare la scheda Sfoglia. Quando si trova il pacchetto, fare clic su **installare**e accettare le condizioni d'uso.

   Verrà quindi scaricato e installato il [pacchetto NuGet Azure IoT Service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) e verrà aggiunto un riferimento a tale pacchetto.

4. Aggiungere il codice seguente `using` istruzione in cima il **Program.cs** file.

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

5. Aggiungere i campi seguenti alla classe **Program** . Sostituire il valore del segnaposto con la stringa di connessione dell'hub IoT che è stato salvato in precedenza in questa sezione. 

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

6. Aggiungere il metodo seguente alla classe **Program**. Impostare il nome del dispositivo a quella usata durante la definizione di dispositivo nel [inviare i dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-dotnet.md).

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myDevice", commandMessage);
   }
   ```

   Questo metodo invia un nuovo messaggio da cloud a dispositivo al dispositivo con ID `myFirstDevice`. Modificare questo parametro solo se è stato modificato rispetto a quello usato nel [inviare i dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-dotnet.md).

7. Aggiungere infine le righe seguenti al metodo **Main** .

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

8. In Visual Studio fare clic con il pulsante destro del mouse sulla soluzione e selezionare **Imposta progetti di avvio**. Selezionare **Progetti di avvio multipli**, quindi selezionare l'azione **Start** per **ReadDeviceToCloudMessages**, **SimulatedDevice** e **SendCloudToDevice**.

9. Premere **F5**. Devono avviarsi tutte e tre le applicazioni. Selezionare la finestra **SendCloudToDevice** e premere **INVIO**. Verrà visualizzato il messaggio ricevuto dall'app per dispositivi.

   ![Ricezione di un messaggio da parte dell'app](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>Ricevere feedback di recapito

È possibile per i riconoscimenti di richiesta (o della scadenza) dall'IoT Hub per ogni messaggio da cloud a dispositivo. Questa opzione consente al back-end della soluzione di informare facilmente la logica di ripetizione o di compensazione. Per altre informazioni sul feedback dei messaggi da cloud a dispositivo, vedere [Messaggistica da dispositivo a cloud e da cloud a dispositivo con l'hub IoT](iot-hub-devguide-messaging.md).

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

2. Aggiungere il metodo seguente nel **Main** metodo, subito dopo il `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` riga.

   ``` csharp
   ReceiveFeedbackAsync();
   ```

3. Per richiedere feedback del recapito del messaggio da cloud a dispositivo, è necessario specificare una proprietà nel metodo **SendCloudToDeviceMessageAsync** . Aggiungere la riga seguente, subito dopo il `var commandMessage = new Message(...);` riga.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

4. Premere **F5**per eseguire le app. Si dovrebbero avviare tutte e tre le applicazioni. Selezionare la finestra **SendCloudToDevice** e premere **INVIO**. Verrà visualizzata la ricezione del messaggio da parte dell'app per dispositivi e, dopo alcuni secondi, la ricezione del messaggio di feedback da parte dell'applicazione **SendCloudToDevice**.

   ![Ricezione di un messaggio da parte dell'app](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> Per semplicità, questa esercitazione non implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come suggerito nell'articolo [Gestione degli errori temporanei](/azure/architecture/best-practices/transient-faults).
>

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato come inviare e ricevere messaggi da cloud a dispositivo.

Per avere degli esempi di soluzioni complete che usano l'hub IoT, vedere l'[acceleratore di soluzioni di monitoraggio remoto di Azure IoT](https://docs.microsoft.com/azure/iot-suite/).

Per altre informazioni sullo sviluppo delle soluzioni con l'hub IoT, vedere la [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md).
