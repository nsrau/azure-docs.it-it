---
title: Pianificare processi con l&quot;hub IoT di Azure (.NET/Node) | Documentazione Microsoft
description: "Come pianificare un processo dell&quot;hub IoT di Azure per richiamare un metodo diretto su più dispositivi. Usare Azure IoT SDK per dispositivi per Node.js per implementare app per dispositivo simulato e Azure IoT SDK per servizi per .NET per implementare un&quot;app di servizio che esegue il processo."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/17/2016
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: a243e4f64b6cd0bf7b0776e938150a352d424ad1
ms.openlocfilehash: fd53e73d6a686581ea2b807ae66716fc36a99ad4


---
# <a name="schedule-and-broadcast-jobs"></a>Pianificare e trasmettere processi
[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

## <a name="introduction"></a>Introduzione
L'hub IoT di Azure è un servizio completamente gestito che consente a un'app back-end di creare e tenere traccia dei processi che pianificano e aggiornano milioni di dispositivi.  I processi possono essere usati per le azioni seguenti:

* Aggiornare le proprietà desiderate
* Aggiornare i tag
* Richiamare metodi diretti

Concettualmente, un processo esegue il wrapping di una di queste azioni e tiene traccia dell'avanzamento dell'esecuzione rispetto a un set di dispositivi, definito da una query di dispositivi gemelli.  Ad esempio, tramite un processo un'app back-end può richiamare un metodo di riavvio in 10.000 dispositivi, specificato da una query di dispositivi gemelli e pianificato in un secondo momento.  L'applicazione può quindi tenere traccia dell'avanzamento mentre ognuno dei dispositivi riceve ed esegue il metodo di riavvio.

Altre informazioni su queste funzionalità sono disponibili in questi articoli:

* Dispositivi gemelli e proprietà: [Introduzione ai dispositivi gemelli][lnk-get-started-twin] ed [Esercitazione: Come usare le proprietà dei dispositivi gemelli][lnk-twin-props]
* Metodi diretti: [Guida per sviluppatori dell'hub IoT - Metodi diretti][lnk-dev-methods] ed [Esercitazione: Usare metodi diretti][lnk-c2d-methods]

Questa esercitazione illustra come:

* Creare un'app per dispositivo simulato con un metodo diretto che abilita il metodo diretto **lockDoor** e può essere chiamato dall'app back-end.
* Creare un'app console .NET che chiama il metodo diretto **lockDoor** nell'app per dispositivo simulato tramite un processo e aggiorna le proprietà desiderate tramite un processo del dispositivo.

Al termine dell'esercitazione saranno disponibili un'app per il dispositivo console Node.js e un'app back-end console .NET (C#):

**simDevice.js**, che si connette all'hub IoT con l'identità del dispositivo e riceve un metodo diretto **lockDoor**.

**ScheduleJob**, che chiama un metodo diretto nell'app per dispositivo simulato e aggiorna le proprietà desiderate di un dispositivo gemello mediante un processo.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Microsoft Visual Studio 2015
* Node.js 0.12.x o versione successiva. <br/>  [Prepare your development environment][lnk-dev-setup] (Preparare l'ambiente di sviluppo) descrive come installare Node.js per questa esercitazione in Windows o Linux.
* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito][lnk-free-trial] in pochi minuti.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Pianificare i processi per chiamare un metodo diretto e aggiornare le proprietà dei dispositivi gemelli
In questa sezione si creerà un'app console .NET (usando C#) che avvia un **lockDoor** remoto su un dispositivo usando un metodo diretto e si aggiorneranno le proprietà del dispositivo gemello.

1. In Visual Studio aggiungere un progetto desktop di Windows classico in Visual C# usando il modello di progetto **Applicazione console** . Assegnare al progetto il nome **ScheduleJob**.

    ![Nuovo progetto desktop di Windows classico in Visual C#][img-createapp]

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ScheduleJob** e quindi scegliere **Gestisci pacchetti NuGet**.
3. Nella finestra **Gestione pacchetti NuGet** selezionare **Esplora**, cercare **microsoft.azure.devices**, selezionare **Installa** per installare il pacchetto **Microsoft.Azure.Devices** e accettare le condizioni per l'uso. Questa procedura scarica, installa e aggiunge un riferimento al [pacchetto NuGet Azure IoT - SDK per dispositivi][lnk-nuget-service-sdk] e alle relative dipendenze.

    ![Finestra Gestione pacchetti NuGet][img-servicenuget]
4. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :
   
        using Microsoft.Azure.Devices;
        
5. Aggiungere i campi seguenti alla classe **Program** . Sostituire il segnaposto con la stringa di connessione dell'hub IoT creato nella sezione precedente.
   
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        
6. Aggiungere il metodo seguente alla classe **Program** :
   
        public static async Task MonitorJob(string jobId)
        {
            JobResponse result;
            do
            {
                result = await jobClient.GetJobAsync(jobId);
                Console.WriteLine("Job Status : " + result.Status.ToString());
                Thread.Sleep(2000);
            } while ((result.Status != JobStatus.Completed) && (result.Status != JobStatus.Failed));
        }
                
7. Aggiungere il metodo seguente alla classe **Program** :

        public static async Task StartMethodJob(string jobId)
        {
            CloudToDeviceMethod directMethod = new CloudToDeviceMethod("lockDoor", TimeSpan.FromSeconds(5), TimeSpan.FromSeconds(5));

            JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
                "SELECT * from DEVICES",
                directMethod,
                DateTime.Now,
                10);

            Console.WriteLine("Started Method Job");
        }

8. Aggiungere il metodo seguente alla classe **Program** :

        public static async Task StartTwinUpdateJob(string jobId)
        {
            var twin = new Twin();
            twin.Properties.Desired["Building"] = "43";
            twin.Properties.Desired["Floor"] = "3";
            twin.ETag = "*";

            JobResponse result = await jobClient.ScheduleTwinUpdateAsync(jobId,
                "SELECT * from DEVICES", 
                twin,
                DateTime.Now,
                10);

            Console.WriteLine("Started Twin Update Job");
        }
 

9. Aggiungere infine le righe seguenti al metodo **Main** :
   
        jobClient = JobClient.CreateFromConnectionString(connString);

        string methodJobId = Guid.NewGuid().ToString();

        StartMethodJob(methodJobId);
        MonitorJob(methodJobId).Wait();
        Console.WriteLine("Press ENTER to run the next job.");
        Console.ReadLine();

        string twinUpdateJobId = Guid.NewGuid().ToString();

        StartTwinUpdateJob(twinUpdateJobId);
        MonitorJob(twinUpdateJobId).Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
                   
10. Compilare la soluzione.

## <a name="create-a-simulated-device-app"></a>Creare un'app di dispositivo simulato
In questa sezione si crea un'app console Node.js che risponde a un metodo diretto chiamato dal cloud, che attiva un riavvio del dispositivo simulato e usa le proprietà segnalate per abilitare le query del dispositivo gemello che consentono di identificare i dispositivi e di sapere quando sono stati riavviati l'ultima volta.

1. Creare una nuova cartella vuota chiamata **simDevice**.  Nella cartella **simDevice** creare un file package.json eseguendo questo comando al prompt dei comandi.  Accettare tutte le impostazioni predefinite:
   
    ```
    npm init
    ```
2. Eseguire questo comando al prompt dei comandi nella cartella **simDevice** per installare il pacchetto SDK per dispositivi **azure-iot-device** e il pacchetto **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Con un editor di testo creare un nuovo file **simDevice.js** nella cartella **simDevice**.
4. Aggiungere le istruzioni "require" seguenti all'inizio del file **simDevice.js**:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Aggiungere una variabile **connectionString** e usarla per creare un'istanza **Client**.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Aggiungere la funzione seguente per gestire il metodo **lockDoor**.
   
    ```
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```
7. Aggiungere il codice seguente per registrare il gestore per il metodo **lockDoor**.
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub.  Waiting for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```
8. Salvare e chiudere il file **simDevice.js**.

> [!NOTE]
> Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri per i tentativi, ad esempio un backoff esponenziale, come illustrato nell'articolo di MSDN [Transient Fault Handling][lnk-transient-faults] (Gestione degli errori temporanei).
> 
> 

## <a name="run-the-apps"></a>Eseguire le app
A questo punto è possibile eseguire le app.

1. Al prompt dei comandi nella cartella **simDevice** eseguire questo comando per iniziare l'ascolto del metodo diretto di riavvio.
   
    ```
    node simDevice.js
    ```
2. Eseguire l'app console C# **ScheduleJob**: fare clic con il pulsante destro del mouse sul progetto **ScheduleJob**, quindi scegliere **Debug** e **Avvia nuova istanza**.

3. L'output viene visualizzato sia dal dispositivo che dalle app back-end.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato usato un processo per pianificare un metodo diretto in un dispositivo e aggiornare le proprietà di un dispositivo gemello.

Per altre informazioni sull'hub IoT e sui modelli di gestione dei dispositivi, ad esempio in modalità remota tramite l'aggiornamento del firmware air, vedere:

[Esercitazione: Come eseguire un aggiornamento del firmware][lnk-fwupdate]

Per altre informazioni sulle attività iniziali con l'hub IoT, vedere [Getting started with the IoT Gateway SDK][lnk-gateway-SDK] (Introduzione a IoT Gateway SDK).

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-schedule-jobs/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-schedule-jobs/createnetapp.png

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/



<!--HONumber=Dec16_HO1-->


