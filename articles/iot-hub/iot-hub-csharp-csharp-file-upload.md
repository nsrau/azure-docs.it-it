---
title: Caricare file dai dispositivi con l'hub IoT | Microsoft Docs
description: Seguire questa esercitazione per informazioni su come caricare file dai dispositivi usando l'hub IoT di Azure con C#.
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2016
ms.author: elioda

---
# Esercitazione: caricare file dai dispositivi al cloud con l'hub IoT
## Introduzione
L'hub IoT di Azure è un servizio completamente gestito che consente comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi IoT e un back-end applicazioni. Le esercitazioni precedenti ([Introduzione all'hub IoT] e [Inviare messaggi da cloud a dispositivo con l'hub IoT]) illustrano le funzionalità di messaggistica di base da dispositivo a cloud e da cloud a dispositivo dell'hub IoT, mentre l'esercitazione [Elaborare i messaggi da dispositivo a cloud] fornisce istruzioni sull'archiviazione affidabile dei messaggi da dispositivo a cloud nell'archiviazione BLOB di Azure. Tuttavia in alcuni scenari non è possibile mappare facilmente i dati che i dispositivi inviano in messaggi relativamente ridotti da dispositivo a cloud, che l'hub IoT accetta. Gli esempi includono file di grandi dimensioni contenenti immagini, video, dati di esempio sulle vibrazioni ad alta frequenza o contenenti qualche tipo di dati pre-elaborati. Questi dati in genere vengono elaborati nel cloud con strumenti come [Data factory di Azure] o lo stack [Hadoop]. Quando si preferisce caricare un file da un dispositivo invece di inviare eventi, è ugualmente possibile usare le funzionalità di sicurezza e affidabilità dell'hub IoT.

Questa esercitazione si basa sul codice dell'esercitazione [Inviare messaggi da cloud a dispositivo con l'hub IoT] che illustra come usare le funzionalità di caricamento di file dell'hub IoT. L'esercitazione illustra come fornire a un dispositivo, in modo sicuro, un URI del BLOB di Azure per caricare un file e come usare le notifiche di caricamento file dell'hub IoT per attivare l'elaborazione del file nel back-end dell'app.

Al termine di questa esercitazione si eseguiranno due applicazioni console Windows:

* **SimulatedDevice**: una versione modificata dell'applicazione creata nell'esercitazione [Inviare messaggi da cloud a dispositivo con l'hub IoT] che consente di caricare un file nell'archiviazione tramite un URI con firma di accesso condiviso fornito dall'hub IoT.
* **ReadFileUploadNotification** riceve le notifiche di caricamento file dall'hub IoT.

> [!NOTE]
> L'hub IoT supporta numerose piattaforme e linguaggi (inclusi C, Java e Javascript) tramite gli Azure IoT SDK per dispositivi. Consultare il [Centro per sviluppatori Azure IoT] per istruzioni dettagliate su come connettere il dispositivo al codice dell'esercitazione e in generale all'hub IoT di Azure.
> 
> 

Per completare questa esercitazione, sono necessari gli elementi seguenti:

* Microsoft Visual Studio 2015
* Un account Azure attivo. Se non si ha un account è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere [Crea subito il tuo account Azure gratuito][lnk-free-trial].

## Associare un account di archiviazione di Azure all'hub IoT
Poiché il dispositivo simulato caricherà un file in un BLOB di archiviazione di Azure, è necessario avere un account di [Archiviazione di Azure] associato all'hub IoT. Quando si associa un account di archiviazione a un hub IoT, l'hub può generare un URI di firma di accesso condiviso che un dispositivo può usare per caricare in modo sicuro un file in un contenitore BLOB. Il servizio hub IoT e gli SDK di dispositivo coordinano il processo che genera l'URI di firma di accesso condiviso e lo rendono disponibile per un dispositivo che lo userà per caricare un file.

Per associare un account di archiviazione di Azure all'hub IoT, seguire le istruzioni in [Gestire hub IoT tramite il portale di Azure].

## Caricare un file da un dispositivo simulato
In questa sezione si modifica l'applicazione del dispositivo simulato creata in [Inviare messaggi da cloud a dispositivo con l'hub IoT e Java] per ricevere i messaggi da cloud a dispositivo dall'hub IoT.

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto **SimulatedDevice**, scegliere **Aggiungi** e quindi **Elemento esistente**. Passare a un file di immagine e includerlo nel progetto. Nell'esercitazione si presuppone che l'immagine sia denominata `image.jpg`.
2. Fare clic con il pulsante destro del mouse sull'immagine e quindi scegliere **Proprietà**. Controllare che **Copia nella directory di output** sia impostato su **Copia sempre**.
   
    ![][1]
3. Nel file **Program.cs** aggiungere le istruzioni seguenti all’inizio del file:
   
        using System.IO;
4. Aggiungere il metodo seguente alla classe **Program**:
   
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
   
    Il metodo `UploadToBlobAsync` accetta il nome del file e l'origine del flusso del file da caricare e gestisce il caricamento nella risorsa di archiviazione. Nell'applicazione console viene visualizzato il tempo necessario per caricare il file.
5. Aggiungere il metodo seguente al metodo **Main** immediatamente prima della riga `Console.ReadLine()`:
   
        SendToBlobAsync();

> [!NOTE]
> Per semplicità, in questa esercitazione non si implementa alcun criterio di nuovi tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come indicato nell'articolo di MSDN [Transient Fault Handling] \(Gestione degli errori temporanei).
> 
> 

## Ricevere la notifica di caricamento di un file
In questa sezione verrà scritta un'app console di Windows che riceve messaggi di notifica di caricamento dall'hub IoT.

1. Nella soluzione di Visual Studio corrente creare un nuovo progetto di Windows in Visual C# usando il modello di progetto **Applicazione console**. Denominare il progetto **ReadFileUploadNotification**.
   
    ![Nuovo progetto in Visual Studio][2]
2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ReadFileUploadNotification** e quindi scegliere **Gestisci pacchetti NuGet**.
   
    Verrà visualizzata la finestra Gestisci pacchetti NuGet.
3. Cercare `Microsoft.Azure.Devices`, fare clic su **Installa** e accettare le condizioni per l'uso.
   
    Verrà quindi scaricato e installato il pacchetto NuGet [Microsoft Azure IoT Service SDK] nel progetto **ReadFileUploadNotification** e verrà aggiunto un riferimento a tale pacchetto.
4. Nel file **Program.cs** aggiungere le istruzioni seguenti all’inizio del file:
   
        using Microsoft.Azure.Devices;
5. Aggiungere i campi seguenti alla classe **Program**. Sostituire il valore del segnaposto con la stringa di connessione all'hub IoT da [Introduzione all'hub IoT di Azure]\:
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. Aggiungere il metodo seguente alla classe **Program**:
   
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
7. Aggiungere infine le righe seguenti al metodo **Main**:
   
        Console.WriteLine("Receive file upload notifications\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        ReceiveFileUploadNotificationAsync().Wait();
        Console.ReadLine();

## Eseguire le applicazioni
A questo punto è possibile eseguire le applicazioni.

1. In Visual Studio fare clic con il pulsante destro del mouse sulla soluzione e selezionare **Imposta progetti di avvio**. Selezionare **Progetti di avvio multipli**, quindi selezionare l'azione **Avvia** per **ReadFileUploadNotification** e **SimulatedDevice**.
2. Premere **F5**. Si avviano entrambe le applicazioni. Verrà visualizzato il messaggio di completamento del caricamento in un'applicazione console e il messaggio di notifica di caricamento ricevuto da altre app console. È possibile usare il [portale di Azure] o Esplora server di Visual Studio per verificare la presenza del file caricato nell'account di archiviazione.
   
   ![][50]

## Passaggi successivi
In questa esercitazione si è appreso come sfruttare le funzionalità di caricamento file dell'hub IoT per semplificare i caricamenti di file dai dispositivi. È possibile continuare a esplorare le funzionalità e gli scenari dell'hub IoT consultando i seguenti articoli:

* [Creare un hub IoT a livello di codice][lnk-create-hub]
* [Introduzione a C SDK][lnk-c-sdk]
* [SDK hub IoT][lnk-sdks]

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Progettare una soluzione][lnk-design]
* [Esplorare la Gestione dei dispositivi dell'hub IoT di Azure usando l'interfaccia utente di esempio][lnk-dmui]
* [Simulazione di un dispositivo con Gateway SDK][lnk-gateway]
* [Gestire hub IoT tramite il portale di Azure][lnk-portal]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/create-identity-csharp1.png

<!-- Links -->

[portale di Azure]: https://portal.azure.com/

[Data factory di Azure]: https://azure.microsoft.com/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/documentation/services/hdinsight/

[Inviare messaggi da cloud a dispositivo con l'hub IoT]: iot-hub-csharp-csharp-c2d.md
[Inviare messaggi da cloud a dispositivo con l'hub IoT e Java]: iot-hub-csharp-csharp-c2d.md
[Elaborare i messaggi da dispositivo a cloud]: iot-hub-csharp-csharp-process-d2c.md
[Introduzione all'hub IoT]: iot-hub-csharp-csharp-getstarted.md
[Introduzione all'hub IoT di Azure]: iot-hub-csharp-csharp-getstarted.md
[Centro per sviluppatori Azure IoT]: http://www.azure.com/develop/iot

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Archiviazione di Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Gestire hub IoT tramite il portale di Azure]: iot-hub-manage-through-portal.md#file-upload
[Microsoft Azure IoT Service SDK]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-sdks-summary.md

[lnk-design]: iot-hub-guidance.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0914_2016-->