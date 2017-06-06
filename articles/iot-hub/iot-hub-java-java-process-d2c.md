---
title: Elaborare messaggi da dispositivo a cloud dell&quot;hub IoT di Azure (Java) | Documentazione Microsoft
description: Come elaborare i messaggi da dispositivo a cloud dell&quot;hub IoT usando le regole di routing e gli endpoint personalizzati per inviare i messaggi agli altri servizi di back-end.
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/25/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 1915044f252984f6d68498837e13c817242542cf
ms.openlocfilehash: 616bca96eaff12fa1929605f3480098bd8b867c2
ms.contentlocale: it-it
ms.lasthandoff: 01/31/2017


---
# <a name="process-iot-hub-device-to-cloud-messages-java"></a>Elaborare messaggi da dispositivo a cloud dell'hub IoT (Java)
[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## <a name="introduction"></a>Introduzione
L'hub IoT di Azure è un servizio completamente gestito che consente comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi e un back-end della soluzione. Altre esercitazioni, ad esempio [Get started with IoT Hub] e [Inviare messaggi da cloud a dispositivo con l'hub IoT][lnk-c2d], illustrano come usare le funzionalità di messaggistica di base da dispositivo a cloud e da cloud a dispositivo dell'hub IoT.

Questa esercitazione è basata sul codice mostrato nell'esercitazione [Get started with IoT Hub] (Introduzione all'hub IoT) e come usare il routing dei messaggi per elaborare i messaggi da dispositivo a cloud in modo scalabile. L'esercitazione illustra come elaborare i messaggi che richiedono un intervento immediato dal back-end della soluzione. Ad esempio, un dispositivo potrebbe inviare un messaggio di avviso che attiva l'inserimento di un ticket in un sistema CRM. Al contrario, i messaggi di punto dati vengono semplicemente inseriti in un motore di analisi. Ad esempio, i dati di telemetria sulla temperatura di un dispositivo che devono essere archiviati per una successiva analisi costituiscono un messaggio di punti dati.

Al termine di questa esercitazione, vengono eseguite tre app console Java:

* **SimulatedDevice**, una versione modificata dell'app creata nell'esercitazione [Get started with IoT Hub] , che invia messaggi di punti dati da dispositivo a cloud ogni secondo e messaggi interattivi da dispositivo a cloud ogni 10 secondi. Questa app usa il protocollo AMQP per comunicare con l'hub IoT.
* **read-d2c-messages** consente di visualizzare i dati di telemetria inviati dall'app per dispositivo simulato.
* **read-critical-queue** rimuove dalla coda i messaggi dalla coda importanti della coda del bus di servizio collegato all'hub IoT.

> [!NOTE]
> L'hub IoT offre il supporto SDK per molte piattaforme e linguaggi, inclusi C, Java e JavaScript. Per istruzioni su come sostituire il dispositivo simulato in questa esercitazione con un dispositivo fisico e su come connettere dispositivi a un hub IoT, fare riferimento al [Centro per sviluppatori Azure IoT].
> 
> 

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Una versione di lavoro completa dell'esercitazione di [Get started with IoT Hub] .
* Java SE 8. <br/> [Preparare l'ambiente di sviluppo][lnk-dev-setup] descrive come installare Java per questa esercitazione in Windows o Linux.
* Maven 3.  <br/> [Preparare l'ambiente di sviluppo][lnk-dev-setup] descrive come installare Maven per questa esercitazione in Windows o Linux.
* Un account Azure attivo. <br/>Se non si ha un account, è possibile creare un [account gratuito](https://azure.microsoft.com/free/) in pochi minuti.

È necessaria una conoscenza di base di [Archiviazione di Azure] e del [bus di servizio di Azure].

## <a name="send-interactive-messages-from-a-simulated-device-app"></a>Inviare messaggi interattivi da un'app per dispositivo simulato
In questa sezione viene modificata l'app per il dispositivo simulato creata nell'esercitazione [Get started with IoT Hub] (Introduzione all'hub IoT) per inviare occasionalmente messaggi che richiedono un intervento immediato.

1. Usare un editor di testo per aprire il file simulated-device\src\main\java\com\mycompany\app\App.java. Questo file contiene il codice dell'app **simulated-device** creata durante l'esercitazione di [Get started with IoT Hub] .
2. Sostituire la classe **MessageSender** con il codice seguente:
   
    ```
    private static class MessageSender implements Runnable {

        public void run()  {
            try {
                double minTemperature = 20;
                double minHumidity = 60;
                Random rand = new Random();

                while (true) {
                    String msgStr;
                    Message msg;
                    if (new Random().nextDouble() > 0.7) {
                        msgStr = "This is a critical message.";
                        msg = new Message(msgStr);
                        msg.setProperty("level", "critical");
                    } else {
                        double currentTemperature = minTemperature + rand.nextDouble() * 15;
                        double currentHumidity = minHumidity + rand.nextDouble() * 20; 
                        TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
                        telemetryDataPoint.deviceId = deviceId;
                        telemetryDataPoint.temperature = currentTemperature;
                        telemetryDataPoint.humidity = currentHumidity;

                        msgStr = telemetryDataPoint.serialize();
                        msg = new Message(msgStr);
                    }
                    
                    System.out.println("Sending: " + msgStr);

                    Object lockobj = new Object();
                    EventCallback callback = new EventCallback();
                    client.sendEventAsync(msg, callback, lockobj);

                    synchronized (lockobj) {
                        lockobj.wait();
                    }
                    Thread.sleep(1000);
                }
            } catch (InterruptedException e) {
                System.out.println("Finished.");
            }
        }
    }
    ```
   
    Con questo metodo la proprietà `"level": "critical"` verrà aggiunta in modo casuale ai messaggi inviati dal dispositivo simulato, che simula un messaggio che richiede un intervento immediato del back-end dell'applicazione. L'applicazione passa queste informazioni nelle proprietà del messaggio anziché nel corpo del messaggio, in modo che l'hub IoT possa indirizzare il messaggio alla destinazione messaggi appropriata.
   
   > [!NOTE]
   > È possibile usare le proprietà del messaggio per indirizzare i messaggi in diversi scenari, tra cui l'elaborazione del percorso a freddo, oltre all'esempio del percorso a caldo mostrato qui.
   > 
   > 

2. Salvare e chiudere il file simulated-device\src\main\java\com\mycompany\app\App.java.
   
   > [!NOTE]
   > Per semplicità, questa esercitazione non implementa alcun criterio di ripetizione. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come indicato nell'articolo di MSDN relativo alla [Transient Fault Handling](Gestione degli errori temporanei).
   > 
   > 

3. Per compilare l'app **simulated-device** con Maven, eseguire questo comando al prompt dei comandi nella cartella simulated-device:
   
    ```
    mvn clean package -DskipTests
    ```

## <a name="add-a-queue-to-your-iot-hub-and-route-messages-to-it"></a>Aggiungere una coda all'hub IoT e indirizzarvi i messaggi
In questa sezione, viene illustrato come creare una coda del bus di servizio, connetterla all'hub IoT e configurare l'hub IoT per inviare messaggi alla coda in base alla presenza di una proprietà del messaggio. Per altre informazioni su come elaborare i messaggi dalle code del bus di servizio, vedere [Get started with queues][Service Bus queue] (Introduzione alle code).

1. Creare una coda del bus di servizio, come descritto in [Get started with queues][Service Bus queue] (Introduzione alle code). Prendere nota dello spazio dei nomi e del nome della coda.

2. Nel Portale di Azure, aprire l'hub IoT e fare clic su **Endpoint**.
    
    ![Endpoint in hub IoT][30]

3. Nel pannello **Endpoint** fare clic su **Aggiungi** in alto per aggiungere la coda all'hub IoT. Denominare l'endpoint **CriticalQueue** e usare il menu a discesa per selezionare **Coda del bus di servizio**, lo spazio dei nomi del bus di servizio in cui si trova la coda e il nome della coda. Al termine, fare clic su **Salva** nella parte inferiore.
    
    ![Aggiunta di un endpoint][31]
    
4. Fare clic su **Route** nell'hub IoT. Fare clic su **Aggiungi** nella parte superiore del pannello per creare una regola di routing che indirizzi i messaggi alla coda appena aggiunta. Selezionare **DeviceTelemetry** come origine dei dati. Immettere `level="critical"` come condizione, quindi scegliere la coda appena aggiunta come endpoint personalizzato, come endpoint della regola di routing. Al termine, fare clic su **Salva** nella parte inferiore.
    
    ![Aggiunta di un route][32]
    
    Assicurarsi che il route di fallback sia impostato su **ON**. Questa impostazione è la configurazione predefinita dell'hub IoT.
    
    ![Route di fallback][33]


## <a name="optional-read-from-the-queue-endpoint"></a>(Facoltativo) Lettura dell'endpoint della coda
È facoltativamente possibile leggere i messaggi dall'endpoint della coda seguendo le istruzioni contenute in [Get started with queues][lnk-sb-queues-java] (Introduzione alle code). Denominare l'app **read-critical-queue**.

## <a name="run-the-applications"></a>Eseguire le applicazioni
A questo punto è possibile eseguire le tre applicazioni.

1. Per eseguire l'applicazione **read-d2c-messages** in un prompt dei comandi o nella shell passare alla cartella read-d2c ed eseguire il comando seguente:
   
   ```
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Eseguire read-d2c-messages][readd2c]
2. Per eseguire l'applicazione **read-critical-queue** in un prompt dei comandi o nella shell passare alla cartella read-critical-queue ed eseguire il comando seguente:
   
   ```
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Eseguire read-critical-queue][readqueue]

3. Per eseguire l'app **simulated-device**, in un prompt dei comandi o nella shell passare alla cartella simulated-device ed eseguire questo comando:
   
   ```
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Eseguire simulated-device][simulateddevice]


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato descritto come inviare i messaggi da dispositivo a cloud in modo affidabile usando la funzionalità di routing dei messaggi dell'hub IoT.


L'esercitazione [Come inviare messaggi da cloud a dispositivo con l'hub IoT][lnk-c2d] descrive le modalità di invio dei messaggi ai dispositivi dal back-end della soluzione.

Per avere esempi di soluzioni complete che usano l'hub IoT, vedere [Azure IoT Suite][lnk-suite].

Per altre informazioni sullo sviluppo delle soluzioni con l'hub IoT, vedere la [Guida per sviluppatori dell'hub IoT].

Per ulteriori informazioni sul routing dei messaggi nell'hub IoT, vedere [Inviare e ricevere messaggi con l'hub IoT][lnk-devguide-messaging].

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-java-java-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-java-java-process-d2c/route-creation.png
[33]: ./media/iot-hub-java-java-process-d2c/fallback-route.png

<!-- Links -->

[Azure blob storage]: ../storage/storage-dotnet-how-to-use-blobs.md
[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Service Bus queue]: ../service-bus-messaging/service-bus-java-how-to-use-queues.md
[lnk-sb-queues-java]: ../service-bus-messaging/service-bus-java-how-to-use-queues.md

[IoT Hub developer guide - Device to cloud]: iot-hub-devguide-messaging.md

[Archiviazione di Azure]: https://azure.microsoft.com/documentation/services/storage/
[bus di servizio di Azure]: https://azure.microsoft.com/documentation/services/service-bus/

[Guida per sviluppatori dell'hub IoT]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Get started with IoT Hub]: iot-hub-java-java-getstarted.md
[Centro per sviluppatori Azure IoT]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[About Azure Storage]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Get Started with Event Hubs]: ../event-hubs/event-hubs-java-ephjava-getstarted.md
[Azure Storage scalability Guidelines]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: https://github.com/Azure/azure-event-hubs/tree/master/java/azure-eventhubs-eph
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-java-java-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java
[lnk-create-an-iot-hub]: iot-hub-java-java-getstarted.md#create-an-iot-hub

