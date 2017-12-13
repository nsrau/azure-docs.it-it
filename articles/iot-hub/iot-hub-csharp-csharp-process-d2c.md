---
title: Routing dei messaggi tramite Hub IoT di Azure (.NET) | Microsoft Docs
description: Come elaborare i messaggi da dispositivo a cloud di Hub IoT di Azure usando le regole di routing e gli endpoint personalizzati per inviare i messaggi agli altri servizi di back-end.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 5177bac9-722f-47ef-8a14-b201142ba4bc
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: d8fed08aa22577574b30b360ec164daf592ed456
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2017
---
# <a name="routing-messages-with-iot-hub-net"></a>Routing dei messaggi tramite Hub IoT (.NET)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Questa esercitazione si basa sull'esercitazione [Introduzione a hub IoT]. L'esercitazione:

* Illustra come usare le regole di routing per inviare i messaggi da dispositivo a cloud con un semplice metodo basato sulla configurazione.
* Illustra come isolare i messaggi interattivi che richiedono un intervento immediato del back-end della soluzione per essere elaborati in seguito. Ad esempio, un dispositivo potrebbe inviare un messaggio di avviso che attiva l'inserimento di un ticket in un sistema CRM. Al contrario, i messaggi di punto dati, come ad esempio la telemetria di temperatura, vengono semplicemente inseriti in un motore di analisi.

Al termine di questa esercitazione vengono eseguite tre app di console .NET:

* **SimulatedDevice**, una versione modificata dell'app creata nell'esercitazione [Introduzione a hub IoT], che invia messaggi di punti dati da dispositivo a cloud ogni secondo e messaggi interattivi da dispositivo a cloud ogni 10 secondi.
* **ReadDeviceToCloudMessages** mostra i dati di telemetria non fondamentali inviati dall'app per dispositivi.
* **ReadCriticalQueue** rimuove i messaggi importanti inviati dall'app per dispositivi da una coda del bus di servizio. Questa coda è collegata all'hub IoT.

> [!NOTE]
> L'hub IoT offre il supporto SDK per molte piattaforme e linguaggi, inclusi C, Java e JavaScript. Per informazioni su come sostituire il dispositivo simulato in questa esercitazione con un dispositivo fisico, fare riferimento al [Centro per sviluppatori Azure IoT].

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Visual Studio 2015 o Visual Studio 2017.
* Un account Azure attivo. <br/>Se non si ha un account, è possibile creare un [account gratuito](https://azure.microsoft.com/free/) in pochi minuti.

Si consiglia anche la lettura della documentazione su [Archiviazione di Azure] e [Bus di servizio di Azure].

## <a name="send-interactive-messages"></a>Inviare messaggi interattivi

Modificare l'app per dispositivi creata nell'esercitazione [Introduzione a hub IoT] per inviare occasionalmente messaggi interattivi.

In Visual Studio, nel progetto **SimulatedDevice** sostituire il metodo `SendDeviceToCloudMessagesAsync` con il codice seguente:

```csharp
private static async void SendDeviceToCloudMessagesAsync()
{
    double minTemperature = 20;
    double minHumidity = 60;
    Random rand = new Random();

    while (true)
    {
        double currentTemperature = minTemperature + rand.NextDouble() * 15;
        double currentHumidity = minHumidity + rand.NextDouble() * 20;

        var telemetryDataPoint = new
        {
            deviceId = "myFirstDevice",
            temperature = currentTemperature,
            humidity = currentHumidity
        };
        var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
        string levelValue;

        if (rand.NextDouble() > 0.7)
        {
            if (rand.NextDouble() > 0.5)
            {
                messageString = "This is a critical message";
                levelValue = "critical";
            }
            else 
            {
                messageString = "This is a storage message";
                levelValue = "storage";
            }
        }
        else
        {
            levelValue = "normal";
        }
        
        var message = new Message(Encoding.ASCII.GetBytes(messageString));
        message.Properties.Add("level", levelValue);
        
        await deviceClient.SendEventAsync(message);
        Console.WriteLine("{0} > Sent message: {1}", DateTime.Now, messageString);

        await Task.Delay(1000);
    }
}
```

Questo metodo aggiunge in modo casuale la proprietà `"level": "critical"` e `"level": "storage"` ai messaggi inviati dal dispositivo, simulando così un messaggio che richiede l'intervento immediato del back-end dell'applicazione o uno che richiede l'archiviazione permanente. L'applicazione passa queste informazioni nelle proprietà del messaggio anziché nel corpo del messaggio, in modo che l'hub IoT possa indirizzare il messaggio alla destinazione messaggi appropriata.

> [!NOTE]
> È possibile usare le proprietà del messaggio per indirizzare i messaggi in diversi scenari, tra cui l'elaborazione del percorso a freddo, oltre all'esempio del percorso a caldo mostrato qui.

> [!NOTE]
> È consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come indicato nell'articolo di MSDN relativo alla [Transient Fault Handling](Gestione degli errori temporanei).

## <a name="route-messages-to-a-queue-in-your-iot-hub"></a>Eseguire il routing dei messaggi a una coda nell'hub IoT

In questa sezione verrà illustrato come:

* Creare una coda del bus di servizio.
* Collegarla all'hub IoT.
* Configurare l'hub IoT per inviare messaggi alla coda in base alla presenza di una proprietà del messaggio.

Per altre informazioni su come elaborare i messaggi dalle code del bus di servizio, vedere [Get started with queues][Service Bus queue] (Introduzione alle code).

1. Creare una coda del bus di servizio, come descritto in [Get started with queues][Service Bus queue] (Introduzione alle code). La coda deve trovarsi nella stessa area e nella stessa sottoscrizione dell'hub IoT. Prendere nota dello spazio dei nomi e del nome della coda.

    > [!NOTE]
    > Nelle code e negli argomenti del bus di servizio usati come endpoint dell'hub IoT non devono essere abilitati le **sessioni** e il **rilevamento duplicati**. Se una di queste opzioni è abilitata, l'endpoint risulta **non raggiungibile** nel portale di Azure.

2. Nel Portale di Azure, aprire l'hub IoT e fare clic su **Endpoint**.
    
    ![Endpoint in hub IoT][30]

3. Nel pannello **Endpoint** fare clic su **Aggiungi** in alto per aggiungere la coda all'hub IoT. Denominare l'endpoint **CriticalQueue** e usare il menu a discesa per selezionare **Coda del bus di servizio**, lo spazio dei nomi del bus di servizio in cui si trova la coda e il nome della coda. Al termine, fare clic su **Salva** nella parte inferiore.
    
    ![Aggiunta di un endpoint][31]
    
4. Fare clic su **Route** nell'hub IoT. Fare clic su **Aggiungi** nella parte superiore del pannello per creare una regola di routing che indirizzi i messaggi alla coda appena aggiunta. Selezionare **DeviceTelemetry** come origine dei dati. Immettere `level="critical"` come condizione, quindi scegliere la coda appena aggiunta come endpoint personalizzato, come endpoint della regola di routing. Al termine, fare clic su **Salva** nella parte inferiore.
    
    ![Aggiunta di un route][32]
    
    Assicurarsi che il route di fallback sia impostato su **ON**. Questo valore rappresenta la configurazione predefinita dell'hub IoT.
    
    ![Route di fallback][33]

## <a name="read-from-the-queue-endpoint"></a>Lettura dell'endpoint della coda

In questa sezione è possibile leggere i messaggi dell'endpoint della coda.

1. In Visual Studio aggiungere un progetto desktop di Windows classico in Visual C# usando il modello di progetto **App console (.NET Framework)**. Denominare il progetto **ReadCriticalQueue**.

2. In Esplora soluzioni fare clic con il tasto destro del mouse sul progetto **ReadCriticalQueue** e quindi scegliere **Gestisci pacchetti NuGet**. Viene visualizzata la finestra **Gestione pacchetti NuGet**.

3. Cercare **WindowsAzure.ServiceBus**, fare clic su **Installa** e quindi accettare le condizioni per l'uso. Viene quindi scaricato e installato il bus di servizio di Azure e viene aggiunto un riferimento ad esso, insieme alle relative dipendenze.

4. All'inizio del file **Program.cs** aggiungere le istruzioni **using** seguenti:
   
    ```csharp
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Aggiungere infine le righe seguenti al metodo **Main** . Sostituire la stringa di connessione con le autorizzazioni **Ascolto** per la coda:
   
    ```csharp
    Console.WriteLine("Receive critical messages. Ctrl-C to exit.\n");
    var connectionString = "{service bus listen string}";
    var queueName = "{queue name}";
    
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);

    client.OnMessage(message =>
        {
            Stream stream = message.GetBody<Stream>();
            StreamReader reader = new StreamReader(stream, Encoding.ASCII);
            string s = reader.ReadToEnd();
            Console.WriteLine(String.Format("Message body: {0}", s));
        });
        
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Eseguire le applicazioni
A questo punto è possibile eseguire le applicazioni.

1. In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Imposta progetti di avvio**. Selezionare **Progetti di avvio multipli**, quindi selezionare **Avvio** come azione per i progetti **ReadDeviceToCloudMessages**, **SimulatedDevice** e **ReadCriticalQueue**.
2. Premere **F5** per avviare le tre app console. L'app **ReadDeviceToCloudMessages** include solo i messaggi non critici inviati dall'applicazione **SimulatedDevice**, mentre l'app **ReadCriticalQueue** include solo i messaggi critici.
   
   ![Tre app console][50]

## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(Facoltativo) Aggiungere un contenitore di archiviazione all'hub IoT ed eseguirvi il routing dei messaggi

In questa sezione, viene illustrato come creare un account di archiviazione, come connettere tale account all'hub IoT e come configurare l'hub IoT per inviare messaggi all'account in base alla presenza di una proprietà del messaggio. Per altre informazioni su come gestire l'archiviazione, vedere la [documentazione di Archiviazione di Azure][Archiviazione di Azure].

 > [!NOTE]
   > Se lo scenario non è limitato a un **endpoint**, è possibile configurare l'endpoint **StorageContainer** oltre all'endpoint **CriticalQueue** ed eseguire entrambi contemporaneamente.

1. Creare un account di archiviazione come descritto nella [documentazione di Archiviazione di Azure][lnk-storage]. Prendere nota del nome dell'account.

2. Nel Portale di Azure, aprire l'hub IoT e fare clic su **Endpoint**.

3. Nel pannello **Endpoint** selezionare l'endpoint **CriticalQueue** e fare clic su **Elimina**. Fare clic su **Sì** e quindi su **Aggiungi**. Assegnare un nome all'endpoint **StorageContainer**, usare gli elenchi a discesa per selezionare **Contenitore di archiviazione di Azure** e creare un **account di archiviazione** e un **contenitore di archiviazione**.  Prendere nota dei nomi.  Al termine, fare clic su **OK** nella parte inferiore. 

 > [!NOTE]
   > Se lo scenario non è limitato a un **endpoint**, non è necessario eliminare l'endpoint **CriticalQueue**.

4. Fare clic su **Route** nell'hub IoT. Fare clic su **Aggiungi** nella parte superiore del pannello per creare una regola di routing che indirizzi i messaggi alla coda appena aggiunta. Selezionare **Messaggi del dispositivo** come origine dei dati. Immettere `level="storage"` come condizione e scegliere **StorageContainer** come endpoint personalizzato, come endpoint della regola di routing. Fare clic su **Salva** nella parte inferiore.  

    Assicurarsi che il route di fallback sia impostato su **ON**. Questa impostazione è la configurazione predefinita dell'hub IoT.

1. Verificare che le applicazioni precedenti siano ancora in esecuzione. 

1. Nel portale di Azure passare all'account di archiviazione, in **Servizio BLOB** fare clic su **Esplora BLOB**.  Selezionare il contenitore, passare al file JSON e fare clic su di esso e quindi fare clic su **Download** per visualizzare i dati.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato descritto come inviare i messaggi da dispositivo a cloud in modo affidabile usando la funzionalità di routing dei messaggi dell'hub IoT.

L'esercitazione [Come inviare messaggi da cloud a dispositivo con l'hub IoT][lnk-c2d] descrive le modalità di invio dei messaggi ai dispositivi dal back-end della soluzione.

Per avere esempi di soluzioni complete che usano l'hub IoT, vedere [Azure IoT Suite][lnk-suite].

Per altre informazioni sullo sviluppo delle soluzioni con l'hub IoT, vedere la [Guida per sviluppatori dell'hub IoT].

Per ulteriori informazioni sul routing dei messaggi nell'hub IoT, vedere [Inviare e ricevere messaggi con l'hub IoT][lnk-devguide-messaging].

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png
[30]: ./media/iot-hub-csharp-csharp-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/route-creation.png
[33]: ./media/iot-hub-csharp-csharp-process-d2c/fallback-route.png

<!-- Links -->
[Service Bus queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[Archiviazione di Azure]: https://azure.microsoft.com/documentation/services/storage/
[Bus di servizio di Azure]: https://azure.microsoft.com/documentation/services/service-bus/
[Guida per sviluppatori dell'hub IoT]: iot-hub-devguide.md
[Introduzione a hub IoT]: iot-hub-csharp-csharp-getstarted.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Centro per sviluppatori Azure IoT]: https://azure.microsoft.com/develop/iot
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-c2d]: iot-hub-csharp-csharp-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
