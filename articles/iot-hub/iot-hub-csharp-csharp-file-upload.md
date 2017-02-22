---
title: Caricare file dai dispositivi con l&quot;hub IoT di Azure | Documentazione Microsoft
description: Come caricare file da un dispositivo al cloud usando Azure IoT SDK per dispositivi per .NET. I file caricati vengono salvati in un contenitore BLOB di archiviazione di Azure.
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: a243e4f64b6cd0bf7b0776e938150a352d424ad1
ms.openlocfilehash: ddc181bde6a154cb3fb35254da6b76a91450ba6b


---
# <a name="upload-files-from-devices-to-the-cloud-with-iot-hub"></a>Caricare file dai dispositivi al cloud con l'hub IoT
## <a name="introduction"></a>Introduzione
L'hub IoT di Azure è un servizio completamente gestito che consente comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi e un back-end della soluzione. Nelle esercitazioni precedenti ([Introduzione all'hub IoT] e [Inviare messaggi da cloud a dispositivo con l'hub IoT]) sono illustrate le funzionalità di messaggistica di base da dispositivo a cloud e da cloud a dispositivo dell'hub IoT, mentre nell'esercitazione [Elaborare messaggi da dispositivo a cloud] vengono fornite istruzioni sull'archiviazione affidabile dei messaggi da dispositivo a cloud nell'Archiviazione BLOB di Azure. Tuttavia in alcuni scenari non è possibile mappare facilmente i dati che i dispositivi inviano in messaggi relativamente ridotti da dispositivo a cloud, che l'hub IoT accetta. Gli esempi includono file di grandi dimensioni contenenti immagini, video, dati di esempio sulle vibrazioni ad alta frequenza o contenenti qualche tipo di dati pre-elaborati. Questi dati in genere vengono elaborati in batch nel cloud con strumenti come [Azure Data Factory] o lo stack [Hadoop]. Quando si preferisce caricare un file da un dispositivo invece di inviare eventi, è ugualmente possibile usare le funzionalità di sicurezza e affidabilità dell'hub IoT.

Questa esercitazione si basa sul codice dell'esercitazione [Inviare messaggi da cloud a dispositivo con l'hub IoT] che illustra come usare le funzionalità di caricamento di file dell'hub IoT. Illustra le operazioni seguenti:

- Specificare in modo sicuro un dispositivo con un URI del BLOB di Azure per il caricamento di un file.
- Usare le notifiche di caricamento di file dell'hub IoT per attivare l'elaborazione del file nel back-end dell'app.

Al termine di questa esercitazione vengono eseguite due app console .NET:

* **SimulatedDevice**, una versione modificata dell'applicazione creata nell'esercitazione [Inviare messaggi da cloud a dispositivo con l'hub IoT]. Ciò consente di caricare un file nell'archivio tramite un URI con firma di accesso condiviso fornito dall'hub IoT.
* **ReadFileUploadNotification**riceve le notifiche di caricamento file dall'hub IoT.

> [!NOTE]
> L'hub IoT supporta numerose piattaforme e linguaggi (inclusi C, Java e Javascript) tramite gli Azure IoT SDK per dispositivi. Vedere il [Centro per sviluppatori di IoT di Azure] per istruzioni dettagliate su come connettere il dispositivo all'Hub IoT di Azure.
> 
> 

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Microsoft Visual Studio 2015
* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito][lnk-free-trial] in pochi minuti.

## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Associare un account di archiviazione di Azure all'hub IoT
Poiché l'app per dispositivo simulato carica un file in un BLOB, è necessario avere un account di [Archiviazione di Azure] associato all'hub IoT. Quando si associa un account di archiviazione di Azure a un hub IoT, l'hub IoT può generare un URI di firma di accesso condiviso che un dispositivo può usare per caricare in modo sicuro un file in un contenitore BLOB. Il servizio hub IoT e gli SDK di dispositivo coordinano il processo che genera l'URI di firma di accesso condiviso e lo rendono disponibile per un dispositivo che lo userà per caricare un file.

Seguire le istruzioni in [Configure file uploads using the Azure portal][lnk-configure-upload] (Configurare i caricamenti di file tramite il portale di Azure) per associare un account di archiviazione di Azure all'hub IoT.

## <a name="upload-a-file-from-a-simulated-device-app"></a>Caricare un file da un'app per dispositivo simulato
In questa sezione si modifica l'app per dispositivo simulato creata in [Inviare messaggi da cloud a dispositivo con l'hub IoT] per ricevere i messaggi da cloud a dispositivo dall'hub IoT.

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto **SimulatedDevice**, scegliere **Aggiungi** e quindi **Elemento esistente**. Passare a un file di immagine e includerlo nel progetto. Nell'esercitazione si presuppone che l'immagine sia denominata `image.jpg`.
2. Fare clic con il pulsante destro del mouse sull'immagine e quindi scegliere **Proprietà**. Controllare che **Copia nella directory di output** sia impostato su **Copia sempre**.
   
    ![][1]
3. Nel file **Program.cs** aggiungere le istruzioni seguenti all’inizio del file:
   
        using System.IO;
4. Aggiungere il metodo seguente alla classe **Program** :
   
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
   
    Il metodo `UploadToBlobAsync` accetta il nome del file e l'origine del flusso del file da caricare e gestisce il caricamento nell'archiviazione. Nell'app console viene visualizzato il tempo necessario per caricare il file.
5. Aggiungere il metodo seguente al metodo **Main** immediatamente prima della riga `Console.ReadLine()`:
   
        SendToBlobAsync();

> [!NOTE]
> Per semplicità, in questa esercitazione non si implementa alcun criterio di nuovi tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come indicato nell'articolo di MSDN [Transient Fault Handling](Gestione degli errori temporanei).
> 
> 

## <a name="receive-a-file-upload-notification"></a>Ricevere la notifica di caricamento di un file
In questa sezione verrà scritta un'app console di .NET che riceve messaggi di notifica di caricamento dall'hub IoT.

1. Nella soluzione di Visual Studio corrente creare un progetto di Windows in Visual C# usando il modello di progetto **Applicazione console** . Denominare il progetto **ReadFileUploadNotification**.
   
    ![Nuovo progetto in Visual Studio][2]
2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ReadFileUploadNotification** e quindi scegliere **Gestisci pacchetti NuGet**.
   
    Questa azione visualizza la finestra Gestisci pacchetti NuGet.
3. Cercare `Microsoft.Azure.Devices`, fare clic su **Installa**e accettare le condizioni per l'uso. 
   
    L'azione consente di scaricare, installare e aggiungere un riferimento al [pacchetto NuGet Azure IoT Service SDK] nel progetto **ReadFileUploadNotification**.

4. Nel file **Program.cs** aggiungere le istruzioni seguenti all’inizio del file:
   
        using Microsoft.Azure.Devices;
5. Aggiungere i campi seguenti alla classe **Program** . Sostituire il valore del segnaposto con la stringa di connessione all'hub IoT da [Introduzione all'hub IoT]:
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. Aggiungere il metodo seguente alla classe **Program** :
   
        private async static Task ReceiveFileUploadNotificationAsync()
        {
            var notificationReceiver = serviceClient.GetFileNotificationReceiver();
   
            Console.WriteLine("\nReceiving file upload notification from service");
            while (true)
            {
                var fileUploadNotification = await notificationReceiver.ReceiveAsync();
                if (fileUploadNotification == null) continue;
   
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received file upload noticiation: {0}", string.Join(", ", fileUploadNotification.BlobName));
                Console.ResetColor();
   
                await notificationReceiver.CompleteAsync(fileUploadNotification);
            }
        }
   
    Si noti che il modello di ricezione è identico a quello utilizzato per ricevere messaggi da cloud a dispositivo dall'app dispositivo.
7. Aggiungere infine le righe seguenti al metodo **Main** :
   
        Console.WriteLine("Receive file upload notifications\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        ReceiveFileUploadNotificationAsync().Wait();
        Console.ReadLine();

## <a name="run-the-applications"></a>Eseguire le applicazioni
A questo punto è possibile eseguire le applicazioni.

1. In Visual Studio fare clic con il pulsante destro del mouse sulla soluzione e selezionare **Imposta progetti di avvio**. Selezionare **Progetti di avvio multipli**, quindi selezionare l'azione **Avvia** per **ReadFileUploadNotification** e **SimulatedDevice**.
2. Premere **F5**. Si avviano entrambe le applicazioni. Verrà visualizzato il messaggio di completamento del caricamento in un'applicazione console e il messaggio di notifica di caricamento ricevuto da altre app console. È possibile usare il [portale di Azure] o Esplora server di Visual Studio per verificare la presenza del file caricato nell'account di archiviazione di Azure.
   
   ![][50]

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come sfruttare le funzionalità di caricamento file dell'hub IoT per semplificare i caricamenti di file dai dispositivi. È possibile continuare a esplorare le funzionalità e gli scenari dell'hub IoT vedendo i seguenti articoli:

* [Creare un hub IoT a livello di codice][lnk-create-hub]
* [Introduzione a C SDK][lnk-c-sdk]
* [Azure IoT SDKs][lnk-sdks] (SDK di IoT di Azure)

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Simulazione di un dispositivo con IoT Gateway SDK][lnk-gateway]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/create-identity-csharp1.png

<!-- Links -->

[portale di Azure]: https://portal.azure.com/

[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/documentation/services/hdinsight/

[Inviare messaggi da cloud a dispositivo con l'hub IoT]: iot-hub-csharp-csharp-c2d.md
[Elaborare messaggi da dispositivo a cloud]: iot-hub-csharp-csharp-process-d2c.md
[Introduzione all'hub IoT]: iot-hub-csharp-csharp-getstarted.md
[Centro per sviluppatori di IoT di Azure]: http://www.azure.com/develop/iot

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Archiviazione di Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[lnk-configure-upload]: iot-hub-configure-file-upload.md
[pacchetto NuGet Azure IoT Service SDK]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md





<!--HONumber=Dec16_HO1-->


