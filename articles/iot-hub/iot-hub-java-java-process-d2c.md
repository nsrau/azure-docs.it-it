---
title: Routing dei messaggi tramite Hub IoT di Azure (Java) | Microsoft Docs
description: Come elaborare i messaggi da dispositivo a cloud di Hub IoT di Azure usando le regole di routing e gli endpoint personalizzati per inviare i messaggi agli altri servizi di back-end.
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
ms.date: 06/29/2017
ms.author: dobett
ms.openlocfilehash: 92ab10e5b8487e03d92b69114a2e3c5302f95ed6
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="routing-messages-with-iot-hub-java"></a>Routing dei messaggi tramite Hub IoT (Java)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

L'hub IoT di Azure è un servizio completamente gestito che consente comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi e un back-end della soluzione. Altre esercitazioni, ad esempio [Get started with IoT Hub] e [Inviare messaggi da cloud a dispositivo con l'hub IoT][lnk-c2d], illustrano come usare le funzionalità di messaggistica di base da dispositivo a cloud e da cloud a dispositivo dell'hub IoT.

Questa esercitazione è basata sul codice mostrato nell'esercitazione [Get started with IoT Hub] (Introduzione all'hub IoT) e come usare il routing dei messaggi per elaborare i messaggi da dispositivo a cloud in modo scalabile. L'esercitazione illustra come elaborare i messaggi che richiedono un intervento immediato dal back-end della soluzione. Ad esempio, un dispositivo potrebbe inviare un messaggio di avviso che attiva l'inserimento di un ticket in un sistema CRM. Al contrario, i messaggi di punto dati vengono semplicemente inseriti in un motore di analisi. Ad esempio, i dati di telemetria sulla temperatura di un dispositivo che devono essere archiviati per una successiva analisi costituiscono un messaggio di punti dati.

Al termine di questa esercitazione, vengono eseguite tre app console Java:

* **SimulatedDevice**, una versione modificata dell'app creata nell'esercitazione [Get started with IoT Hub] , che invia messaggi di punti dati da dispositivo a cloud ogni secondo e messaggi interattivi da dispositivo a cloud ogni 10 secondi. Questa app usa il protocollo AMQP per comunicare con l'hub IoT.
* **read-d2c-messages** consente di visualizzare i dati di telemetria inviati dall'app per dispositivo.
* **read-critical-queue** rimuove dalla coda i messaggi dalla coda importanti della coda del bus di servizio collegato all'hub IoT.

> [!NOTE]
> L'hub IoT offre il supporto SDK per molte piattaforme e linguaggi, inclusi C, Java e JavaScript. Per istruzioni su come sostituire il dispositivo in questa esercitazione con un dispositivo fisico e su come connettere dispositivi a un hub IoT, fare riferimento al [Centro per sviluppatori Azure IoT].

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Una versione di lavoro completa dell'esercitazione di [Get started with IoT Hub] .
* [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) più recente
* [Maven 3](https://maven.apache.org/install.html)
* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito][lnk-free-trial] in pochi minuti.

Si consiglia anche la lettura della documentazione su [Archiviazione di Azure] e [Bus di servizio di Azure].

## <a name="send-interactive-messages-from-a-device-app"></a>Inviare messaggi interattivi da un'app per dispositivi
In questa sezione viene modificata l'app per dispositivi creata nell'esercitazione [Get started with IoT Hub] per inviare occasionalmente messaggi che richiedono un intervento immediato.

1. Usare un editor di testo per aprire il file simulated-device\src\main\java\com\mycompany\app\App.java. Questo file contiene il codice dell'app **simulated-device** creata durante l'esercitazione di [Get started with IoT Hub] .

2. Sostituire la classe **MessageSender** con il codice seguente:

    ```java
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
                        if (new Random().nextDouble() > 0.5) {
                            msgStr = "This is a critical message.";
                            msg = new Message(msgStr);
                            msg.setProperty("level", "critical");
                        } else {
                            msgStr = "This is a storage message.";
                            msg = new Message(msgStr);
                            msg.setProperty("level", "storage");
                        }
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
   
    Questo metodo aggiunge in modo casuale la proprietà `"level": "critical"` e `"level": "storage"` ai messaggi inviati dal dispositivo, simulando così un messaggio che richiede l'intervento immediato del back-end dell'applicazione o uno che richiede l'archiviazione permanente. L'applicazione supporta il routing dei messaggi in base al corpo del messaggio.
   
   > [!NOTE]
   > È possibile usare le proprietà del messaggio per indirizzare i messaggi in diversi scenari, tra cui l'elaborazione del percorso a freddo, oltre all'esempio del percorso a caldo mostrato qui.

2. Salvare e chiudere il file simulated-device\src\main\java\com\mycompany\app\App.java.

    > [!NOTE]
    > È consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come indicato nell'articolo di MSDN relativo alla [Transient Fault Handling](Gestione degli errori temporanei).

3. Per compilare l'app **simulated-device** con Maven, eseguire questo comando al prompt dei comandi nella cartella simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="add-a-queue-to-your-iot-hub-and-route-messages-to-it"></a>Aggiungere una coda all'hub IoT e indirizzarvi i messaggi

In questa sezione, viene illustrato come creare una coda del bus di servizio, connetterla all'hub IoT e configurare l'hub IoT per inviare messaggi alla coda in base alla presenza di una proprietà del messaggio. Per altre informazioni su come elaborare i messaggi dalle code del bus di servizio, vedere [Get started with queues][lnk-sb-queues-java] (Introduzione alle code).

1. Creare una coda del bus di servizio, come descritto in [Get started with queues][lnk-sb-queues-java] (Introduzione alle code). Prendere nota dello spazio dei nomi e del nome della coda.

    > [!NOTE]
    > Nelle code e negli argomenti del bus di servizio usati come endpoint dell'hub IoT non devono essere abilitati le **sessioni** e il **rilevamento duplicati**. Se una di queste opzioni è abilitata, l'endpoint risulta **non raggiungibile** nel portale di Azure.

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

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Eseguire read-d2c-messages][readd2c]

2. Per eseguire l'applicazione **read-critical-queue** in un prompt dei comandi o nella shell passare alla cartella read-critical-queue ed eseguire il comando seguente:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Eseguire read-critical-queue][readqueue]

3. Per eseguire l'app **simulated-device**, in un prompt dei comandi o nella shell passare alla cartella simulated-device ed eseguire questo comando:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Eseguire simulated-device][simulateddevice]

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
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-java-java-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-java-java-process-d2c/route-creation.png
[33]: ./media/iot-hub-java-java-process-d2c/fallback-route.png

<!-- Links -->

[lnk-sb-queues-java]: ../service-bus-messaging/service-bus-java-how-to-use-queues.md

[Archiviazione di Azure]: https://azure.microsoft.com/documentation/services/storage/
[Bus di servizio di Azure]: https://azure.microsoft.com/documentation/services/service-bus/

[Guida per sviluppatori dell'hub IoT]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Get started with IoT Hub]: iot-hub-java-java-getstarted.md
[Centro per sviluppatori Azure IoT]: https://azure.microsoft.com/develop/iot
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-c2d]: iot-hub-java-java-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
