---
title: Caricare file dai dispositivi nell'hub IoT con .NET | Microsoft Docs
description: Come caricare file da un dispositivo al cloud usando Azure IoT SDK per dispositivi per .NET. I file caricati vengono salvati in un contenitore BLOB di archiviazione di Azure.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 14472e84d425bf03a3c6a0c2dc558d4b8225caec
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733407"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>Caricare file dal dispositivo al cloud con L'hub IoT (.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Questa esercitazione si basa sul codice nell'esercitazione Invia messaggi da cloud a dispositivo con hub IoT per illustrare come usare le funzionalità di caricamento dei file dell'hub IoT.This tutorial builds on the code in the [Send cloud-to-device messages with IoT Hub](iot-hub-csharp-csharp-c2d.md) tutorial to show you how to use the file upload capabilities of IoT Hub. Illustra le operazioni seguenti:

* Specificare in modo sicuro un dispositivo con un URI del BLOB di Azure per il caricamento di un file.

* Usare le notifiche di caricamento di file dell'hub IoT per attivare l'elaborazione del file nel back-end dell'app.

L'esercitazione [Invia dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-dotnet.md) e Invia messaggi da cloud a dispositivo con hub IoT mostra la funzionalità di messaggistica da dispositivo a cloud di base e da cloud a dispositivo dell'hub IoT.The Send telemetry from a device to an IoT hub quickstart and [Send cloud-to-device messages with IoT Hub](iot-hub-csharp-csharp-c2d.md) tutorial show the basic device-to-cloud and cloud-to-device messaging functionality of IoT Hub. L'esercitazione [Configurare il routing dei messaggi con l'hub IoT](tutorial-routing.md) descrive un modo per archiviare in modo affidabile i messaggi da dispositivo a cloud nell'archivio BLOB di Microsoft Azure.The Configure Message Routing with IoT Hub tutorial describes a way to ablyably store device-to-cloud messages in Microsoft Azure Blob storage. Tuttavia, in alcuni scenari non è possibile eseguire facilmente il mapping dei dati inviati dai dispositivi nei messaggi da dispositivo a cloud relativamente piccoli accettati dall'hub IoT.However, in some scenarios you can't easily map the data your devices send into the relatively small device-to-cloud messages that IoT Hub accepts. Ad esempio:

* File di grandi dimensioni che contengono immagini

* Video

* Dati di vibrazione campionati ad alta frequenza

* Qualche tipo di dati pre-elaborati

Questi dati in genere vengono elaborati in batch nel cloud con strumenti come [Azure Data Factory](../data-factory/introduction.md) o lo stack [Hadoop](../hdinsight/index.yml). Quando è necessario caricare i file da un dispositivo, è comunque possibile usare la sicurezza e affidabilità dell'hub IoT.

Al termine di questa esercitazione vengono eseguite due app console .NET:

* **SimulatedDevice**. Ciò consente di caricare un file nell'archivio tramite un URI con firma di accesso condiviso fornito dall'hub IoT. Si tratta di una versione modificata dell'app creata nell'esercitazione Invia messaggi da cloud a dispositivo con l'hub IoT.It is a modified version of the app created in the [Send cloud-to-device tutorial with IoT Hub.](iot-hub-csharp-csharp-c2d.md)

* **ReadFileUploadNotification**. Questa app riceve le notifiche di caricamento dei file dall'hub IoT.This app receives file upload notifications from your IoT hub.

> [!NOTE]
> IoT Hub supports many device platforms and languages, including C, Java, Python, and Javascript, through Azure IoT device SDKs. Vedere il [Centro per sviluppatori di IoT di Azure](https://azure.microsoft.com/develop/iot) per istruzioni dettagliate su come connettere il dispositivo all'Hub IoT di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Visual Studio

* Un account Azure attivo. Se non hai un account, puoi creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

* Assicurarsi che la porta 8883 sia aperta nel firewall. L'esempio di dispositivo in questo articolo usa il protocollo MQTT, che comunica tramite la porta 8883.The device sample in this article uses MQTT protocol, which communicates over port 8883. Questa porta potrebbe essere bloccata in alcuni ambienti di rete aziendali e didattici. Per altre informazioni e soluzioni alternative per questo problema, vedere [Connettersi all'hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Caricare un file da un'app per dispositivi

In questa sezione si modifica l'app per dispositivi creata in Invia messaggi da cloud a dispositivo con hub IoT per ricevere messaggi da cloud a dispositivo dall'hub IoT.In this section, you modify the device app you created in [Send cloud-to-device messages with IoT Hub](iot-hub-csharp-csharp-c2d.md) to receive cloud-to-device messages from the IoT hub.

1. In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul progetto **SimulatedDevice** e scegliere **Aggiungi** > **elemento esistente**. Trovare un file di immagine e includerlo nel progetto. Nell'esercitazione si presuppone che l'immagine sia denominata `image.jpg`.

1. Fare clic con il pulsante destro del mouse sull'immagine, quindi scegliere **Proprietà**. Controllare che **Copia nella directory di output** sia impostato su **Copia sempre**.

    ![Mostra la posizione in cui aggiornare le proprietà dell'immagine per la copia nella directory di output](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

1. Nel file **Program.cs** aggiungere le istruzioni seguenti all’inizio del file:

    ```csharp
    using System.IO;
    ```

1. Aggiungere il seguente metodo alla classe **Program**:

    ```csharp
    private static async void SendToBlobAsync()
    {
        string fileName = "image.jpg";
        Console.WriteLine("Uploading file: {0}", fileName);
        var watch = System.Diagnostics.Stopwatch.StartNew();

        using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
        {
            await deviceClient.UploadToBlobAsync(fileName, sourceData);
        }

        watch.Stop();
        Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
    }
    ```

    Il metodo `UploadToBlobAsync` accetta il nome del file e l'origine del flusso del file da caricare e gestisce il caricamento nell'archiviazione. Nell'app console viene visualizzato il tempo necessario per caricare il file.

1. Aggiungere la riga seguente nel metodo `Console.ReadLine()` **Main,** subito prima di :

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> Per semplicità, in questa esercitazione non si implementa alcun criterio di nuovi tentativi. Nel codice di produzione, è necessario implementare criteri di ripetizione dei tentativi, ad esempio backoff esponenziale, come suggerito in [Gestione degli errori temporanei](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub IoTGet the IoT hub connection string

In questo articolo viene creato un servizio back-end per ricevere messaggi di notifica di caricamento file dall'hub IoT creato in [Inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-dotnet.md). Per ricevere i messaggi di notifica di caricamento dei file, il servizio deve disporre dell'autorizzazione di connessione del **servizio.** Per impostazione predefinita, ogni hub IoT viene creato con un criterio di accesso condiviso denominato **servizio** che concede questa autorizzazione.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Ricevere la notifica di caricamento di un file

In questa sezione verrà scritta un'app console di .NET che riceve messaggi di notifica di caricamento dall'hub IoT.

1. Nella soluzione di Visual Studio corrente selezionare **File** > **Nuovo** > **progetto**. In **Crea un nuovo progetto**selezionare App console **(.NET Framework)** e quindi **avanti**.

1. Denominare il progetto *ReadFileUploadNotification*. In **Soluzione**selezionare **Aggiungi alla soluzione**. Selezionare **Crea** per creare il progetto.

    ![Configurare il progetto ReadFileUploadNotification in Visual StudioConfigure the ReadFileUploadNotification project in Visual Studio](./media/iot-hub-csharp-csharp-file-upload/read-file-upload-project-configure.png)

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ReadFileUploadNotification** e scegliere **Gestisci pacchetti NuGet**.

1. In **Gestione pacchetti NuGet**selezionare **Sfoglia**. Cercare e selezionare **Microsoft.Azure.Devices**, quindi selezionare **Installa**.

    Questo passaggio scarica, installa e aggiunge un riferimento al pacchetto SDK NuGet del servizio Azure IoT nel progetto **ReadFileUploadNotification.This** step downloads, installs, and adds a reference to the [Azure IoT service SDK NuGet package](https://www.nuget.org/packages/Microsoft.Azure.Devices/) in the ReadFileUploadNotification project.

1. Nel **file Program.cs** per questo progetto, aggiungere la seguente istruzione all'inizio del file:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. Aggiungere i campi seguenti alla classe **Program** . Sostituire `{iot hub connection string}` il valore segnaposto con la stringa di connessione dell'hub IoT copiata in precedenza in Ottenere la stringa di [connessione dell'hub IoT:](#get-the-iot-hub-connection-string)

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. Aggiungere il seguente metodo alla classe **Program**:

    ```csharp
    private async static void ReceiveFileUploadNotificationAsync()
    {
        var notificationReceiver = serviceClient.GetFileNotificationReceiver();

        Console.WriteLine("\nReceiving file upload notification from service");
        while (true)
        {
            var fileUploadNotification = await notificationReceiver.ReceiveAsync();
            if (fileUploadNotification == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received file upload notification: {0}", 
              string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();

            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    Si noti che il modello di ricezione è identico a quello usato per ricevere messaggi da cloud a dispositivo dall'app per dispositivo.

1. Infine, aggiungere le seguenti righe al metodo **Main:**

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e scegliere Imposta progetti di **avvio**.

1. In**Progetto di avvio** **delle proprietà** > comuni selezionare **Progetti di avvio multipli**, quindi selezionare l'azione **Avvia** per **ReadFileUploadNotification** e **SimulatedDevice**. Selezionare **OK** per salvare le modifiche.

1. Premere **F5**. Si avviano entrambe le applicazioni. Verrà visualizzato il messaggio di completamento del caricamento in un'applicazione console e il messaggio di notifica di caricamento ricevuto da altre applicazioni console. È possibile usare il [portale di Azure](https://portal.azure.com/) o Esplora server di Visual Studio per verificare la presenza del file caricato nell'account di archiviazione di Azure.

    ![Schermata che mostra le opzioni di output](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare le funzionalità di caricamento file dell'hub IoT per semplificare i caricamenti di file dai dispositivi. È possibile continuare a esplorare le funzionalità e gli scenari dell'hub IoT con gli articoli seguenti:You can continue to explore IoT Hub features and scenarios with the following articles:

* [Creare un hub IoT a livello di codice](iot-hub-rm-template-powershell.md)

* [Introduzione a C SDK](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Distribuzione dell'intelligenza artificiale in dispositivi perimetrali con Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
