---
title: Messaggi da cloud a dispositivo con l'hub IoT di Azure (.NET) | Microsoft Docs
description: Come inviare messaggi da cloud a dispositivo a un dispositivo da un hub IoT di Azure usando gli SDK di Azure IoT per .NET. Si modifica un'app per dispositivi per ricevere i messaggi diretti dal cloud al dispositivo e si modifica un'app back-end per inviare i messaggi diretti dal cloud al dispositivo.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: elioda
ms.openlocfilehash: e744ffe9eb6e58c9226802f0196cb5acf1427bdf
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047720"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Inviare messaggi dal cloud al dispositivo con Hub IoT (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introduzione

L'hub IoT di Azure è un servizio completamente gestito che consente di abilitare comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi e un back-end della soluzione. La guida introduttiva [Inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-dotnet.md) mostra come creare un hub IoT, eseguire il provisioning dell'identità di un dispositivo al suo interno e creare il codice di un'app per dispositivo che invia messaggi da dispositivo a cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Questa esercitazione è basata sulla guida introduttiva [Inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-dotnet.md). Illustra come eseguire i passaggi seguenti:

* Dal back-end della soluzione inviare messaggi da cloud a dispositivo a un singolo dispositivo tramite l'hub IoT.

* Ricevere messaggi da cloud a dispositivo in un dispositivo.

* Dal back-end della soluzione richiedere l'acknowledgement di recapito (*feedback*) per i messaggi inviati a un dispositivo dall'hub IoT.

Altre informazioni sui messaggi da cloud a dispositivo sono disponibili in [Messaggistica da dispositivo a cloud e da cloud a dispositivo con l'hub IoT](iot-hub-devguide-messaging.md).

Al termine di questa esercitazione vengono eseguite due app console .NET:

* **SimulatedDevice**, una versione modificata dell'app creata in [Inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-dotnet.md), che si connette all'hub IoT e riceve messaggi da cloud a dispositivo.

* **SendCloudToDevice**, che invia un messaggio diretto dal cloud al dispositivo all'app per dispositivi tramite l'hub IoT e quindi riceve la conferma di recapito.

> [!NOTE]
> L’hub IoT dispone del supporto SDK per molte piattaforme e linguaggi, tra cui C, Java e Javascript, tramite gli [SDK del dispositivo IoT Azure](iot-hub-devguide-sdks.md). Per istruzioni dettagliate su come connettere il dispositivo al codice dell'esercitazione e in generale all'hub IoT di Azure, vedere la [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md).
> 

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Visual Studio 2017

* Un account Azure attivo. Se non si dispone di un account, è possibile crearne uno [gratuito](http://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

## <a name="receive-messages-in-the-device-app"></a>Ricevere messaggi nell'app per dispositivi

In questa sezione si modificherà l'app per dispositivi creata in [Inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-dotnet.md) per ricevere messaggi da cloud a dispositivo dall'hub IoT.

1. In Visual Studio, nel progetto **SimulatedDevice** aggiungere il seguente metodo alla classe **Program**.

   ```csharp   
    private static async void ReceiveC2dAsync()
    {
        Console.WriteLine("\nReceiving cloud to device messages from service");
        while (true)
        {
            Message receivedMessage = await deviceClient.ReceiveAsync();
            if (receivedMessage == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received message: {0}", 
            Encoding.ASCII.GetString(receivedMessage.GetBytes()));
            Console.ResetColor();

            await deviceClient.CompleteAsync(receivedMessage);
        }
    }
   ```

   Il metodo `ReceiveAsync` restituisce in modo asincrono il messaggio ricevuto nel momento in cui viene ricevuto dal dispositivo. Restituisce *null* dopo un periodo di timeout specificabile (in questo caso viene usato il valore predefinito di un minuto). Quando l'app riceve un valore *Null*, deve rimanere in attesa di nuovi messaggi. Questo requisito è il motivo per la riga `if (receivedMessage == null) continue`.
   
    La chiamata a `CompleteAsync()` notifica all'hub IoT che il messaggio è stato elaborato correttamente. Il messaggio può essere rimosso dalla coda del dispositivo in modo sicuro. Se si è verificato un evento che ha impedito all'app per dispositivo di completare l'elaborazione del messaggio, l'hub IoT lo recapita nuovamente. È quindi importante che la logica di elaborazione del messaggio nell'app per dispositivo sia *idempotente*, in modo che la ricezione dello stesso messaggio più volte produca lo stesso risultato. 
    
    Un'applicazione può anche abbandonare temporaneamente un messaggio e, di conseguenza, l'hub IoT mantiene il messaggio nella coda per un uso futuro. In alternativa, l'applicazione può rifiutare un messaggio, rimuovendolo così definitivamente dalla coda. Per altre informazioni sul ciclo di vita dei messaggi da cloud a dispositivo, vedere [Messaggistica da dispositivo a cloud e da cloud a dispositivo con l'hub IoT](iot-hub-devguide-messaging.md).
   
   > [!NOTE]
   > Quando si usa HTTPS invece di MQTT o AMQP come trasporto, il metodo `ReceiveAsync` verrà restituito immediatamente. Il modello supportato per i messaggi da cloud a dispositivo con HTTPS è rappresentato da dispositivi collegati in modo intermittente che controllano raramente la presenza di messaggi (meno di ogni 25 minuti). La generazione di altre ricezioni HTTPS comporta la limitazione delle richieste da parte dell'hub IoT. Per altre informazioni sulle differenze tra il supporto di MQTT, AMQP e HTTPS e sulla limitazione delle richieste dell'hub IoT, vedere [Messaggistica da dispositivo a cloud e da cloud a dispositivo con l'hub IoT](iot-hub-devguide-messaging.md).
   > 
   > 
2. Aggiungere il metodo seguente al metodo **Main** immediatamente prima della riga `Console.ReadLine()`:
   
   ``` csharp   
   ReceiveC2dAsync();
   ```

## <a name="send-a-cloud-to-device-message"></a>Inviare un messaggio da cloud a dispositivo
In questa sezione viene scritta un'app console di .NET che invia messaggi da cloud a dispositivo all'app per dispositivi.

1. Nella soluzione di Visual Studio corrente creare un progetto di applicazioni desktop in Visual C# usando il modello di progetto **Applicazione console**. Denominare il progetto **SendCloudToDevice**.
   
   ![Nuovo progetto in Visual Studio](./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png)

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Gestisci pacchetti NuGet per la soluzione**. 
   
    L'azione consente di visualizzare la finestra **Gestisci pacchetti NuGet**.

3. Cercare **Microsoft.Azure.Devices**, fare clic su **Installa** e quindi accettare le condizioni per l'uso. 
   
    Verrà quindi scaricato e installato il [pacchetto NuGet Azure IoT Service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) e verrà aggiunto un riferimento a tale pacchetto.

4. Aggiungere l'istruzione `using` seguente all'inizio del file **Program.cs** :

   ``` csharp   
   using Microsoft.Azure.Devices;
   ```

5. Aggiungere i campi seguenti alla classe **Program** . Sostituire il valore del segnaposto con la stringa di connessione all'hub IoT ottenuta in [Inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-dotnet.md):

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

6. Aggiungere il metodo seguente alla classe **Program** :
   
   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new 
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myFirstDevice", commandMessage);
   }
   ```

   Questo metodo invia un nuovo messaggio da cloud a dispositivo al dispositivo con ID `myFirstDevice`. Modificare questo parametro solo se è stato cambiato rispetto a quello usato in [Inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-dotnet.md).

7. Aggiungere infine le righe seguenti al metodo **Main** :

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

È possibile richiedere la conferma della ricezione (o della scadenza) dall'hub IoT per ogni messaggio da cloud a dispositivo. Questa opzione consente al back-end della soluzione di informare facilmente la logica di ripetizione o di compensazione. Per altre informazioni sul feedback dei messaggi da cloud a dispositivo, vedere [Messaggistica da dispositivo a cloud e da cloud a dispositivo con l'hub IoT](iot-hub-devguide-messaging.md).

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

2. Aggiungere il metodo seguente nel metodo **Main`serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` immediatamente dopo la riga** :
   
   ``` csharp
   ReceiveFeedbackAsync();
   ```

3. Per richiedere feedback del recapito del messaggio da cloud a dispositivo, è necessario specificare una proprietà nel metodo **SendCloudToDeviceMessageAsync** . Aggiungere la riga seguente, subito dopo la riga `var commandMessage = new Message(...);` :
   
   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

4. Premere **F5**per eseguire le app. Si dovrebbero avviare tutte e tre le applicazioni. Selezionare la finestra **SendCloudToDevice** e premere **INVIO**. Verrà visualizzata la ricezione del messaggio da parte dell'app per dispositivi e, dopo alcuni secondi, la ricezione del messaggio di feedback da parte dell'applicazione **SendCloudToDevice**.
   
   ![Ricezione di un messaggio da parte dell'app](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> Per semplicità, in questa esercitazione non si implementa alcun criterio di nuovi tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come indicato nell'articolo di MSDN [Transient Fault Handling](https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx)(Gestione degli errori temporanei).
> 

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato come inviare e ricevere messaggi da cloud a dispositivo. 

Per avere degli esempi di soluzioni complete che usano l'hub IoT, vedere l'[Acceleratore di soluzioni di monitoraggio remoto di Azure IoT](https://docs.microsoft.com/azure/iot-suite/).

Per altre informazioni sullo sviluppo delle soluzioni con l'hub IoT, vedere la [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md).