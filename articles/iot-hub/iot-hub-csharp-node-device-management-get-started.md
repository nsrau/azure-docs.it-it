---
title: Introduzione a Gestione dei dispositivi dell&quot;hub IoT di Azure | Documentazione Microsoft
description: Questa esercitazione illustra come iniziare a usare Gestione dei dispositivi nell&quot;hub IoT di Azure
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: e044006d-ffd6-469b-bc63-c182ad066e31
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/17/2016
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: cf9741e7bc30ccb5e6b8f79dad7c8ef725cd683a


---
# <a name="tutorial-get-started-with-device-management"></a>Esercitazione: Introduzione alla gestione dei dispositivi

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]
## <a name="introduction"></a>Introduzione
Le app back-end IoT possono usare primitive nell'hub IoT di Azure, ovvero i metodi diretti e il dispositivo gemello, per avviare e monitorare le operazioni di gestione del dispositivo in modalità remota.  Questo articolo fornisce indicazioni e codice che illustrano il modo in cui le app back-end IoT e un dispositivo interagiscono per avviare e monitorare un riavvio remoto del dispositivo usando l'hub IoT.

Per avviare e monitorare le operazioni di gestione dei dispositivi da un'applicazione back-end basata su cloud in modalità remota, usare le primitive dell'hub IoT, ad esempio [dispositivo gemello][lnk-devtwin] e [metodi diretti][lnk-c2dmethod]. Questa esercitazione illustra come un'applicazione back-end e un dispositivo collaborano per avviare e monitorare il riavvio di un dispositivo in modalità remota dall'hub IoT.

Usare un metodo diretto per avviare le operazioni di gestione dei dispositivi, ad esempio il riavvio, il ripristino delle impostazioni predefinite e l'aggiornamento del firmware, da un'applicazione back-end nel cloud. Il dispositivo è responsabile per:

* La gestione della richiesta di metodo inviata dall'hub IoT.
* L'avvio di un'operazione specifica del dispositivo corrispondente sul dispositivo.
* Gli aggiornamenti di stato tramite le proprietà segnalate nell'hub IoT.

È possibile usare un'applicazione back-end nel cloud per eseguire query di un dispositivo gemello in modo da creare report sullo stato di avanzamento delle operazioni di gestione del dispositivo.

Questa esercitazione illustra come:

* Creare un hub IoT nel portale di Azure e un'identità del dispositivo nell'hub IoT.
* Creare un'app per dispositivo simulato con un metodo diretto che abilita il riavvio e può essere chiamato dal cloud.
* Creare un'applicazione console che chiama il metodo diretto di riavvio nell'app per dispositivo simulato tramite l'hub IoT.

Al termine dell'esercitazione saranno disponibili un'app per il dispositivo console Node.js e un'app back-end console .NET (C#):

**dmpatterns_getstarted_device.js**, che si connette all'hub IoT con l'identità del dispositivo creata in precedenza, riceve un metodo diretto per il riavvio, simula un riavvio fisico e segnala il tempo impiegato per l'ultimo riavvio.

**TriggerReboot**, che chiama un metodo diretto nell'app per dispositivo simulato, visualizza la risposta e le proprietà segnalate aggiornate.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Microsoft Visual Studio 2015
* Node.js 0.12.x o versione successiva. <br/>  [Prepare your development environment][lnk-dev-setup] (Preparare l'ambiente di sviluppo) descrive come installare Node.js per questa esercitazione in Windows o Linux.
* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito][lnk-free-trial] in pochi minuti.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Attivare un riavvio remoto nel dispositivo con un metodo diretto
In questa sezione si crea un'app console .NET (usando C#) che avvia un riavvio remoto in un dispositivo con un metodo diretto e usa le query del dispositivo gemello per ottenere l'ora dell'ultimo riavvio di tale dispositivo.

1. In Visual Studio aggiungere un progetto desktop di Windows classico in Visual C# usando il modello di progetto **Applicazione console** . Assegnare al progetto il nome **TriggerReboot**.

    ![Nuovo progetto desktop di Windows classico in Visual C#][img-createapp]

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **TriggerReboot** e quindi scegliere **Gestisci pacchetti NuGet**.
3. Nella finestra **Gestione pacchetti NuGet** selezionare **Esplora**, cercare **microsoft.azure.devices**, selezionare **Installa** per installare il pacchetto **Microsoft.Azure.Devices** e accettare le condizioni per l'uso. Questa procedura scarica, installa e aggiunge un riferimento al pacchetto NuGet [Microsoft Azure IoT Service SDK][lnk-nuget-service-sdk] e alle relative dipendenze.

    ![Finestra Gestione pacchetti NuGet][img-servicenuget]
4. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :
   
        using Microsoft.Azure.Devices;
        
5. Aggiungere i campi seguenti alla classe **Program** . Sostituire il valore del segnaposto con la stringa di connessione dell'hub IoT creato nella sezione precedente e il dispositivo di destinazione.
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        static string targetDevice = "{deviceIdForTargetDevice}";
        
6. Aggiungere il metodo seguente alla classe **Program**.  Questo codice ottiene il dispositivo gemello per il dispositivo in fase di riavvio e restituisce le proprietà segnalate.
   
        public static async Task QueryTwinRebootReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
7. Aggiungere il metodo seguente alla classe **Program**.  Il codice attiva il riavvio nel dispositivo usando un metodo diretto.

        public static async Task StartReboot()
        {
            client = ServiceClient.CreateFromConnectionString(connString);
            CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
            method.ResponseTimeout = TimeSpan.FromSeconds(30);

            CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

            Console.WriteLine("Invoked firmware update on device.");
        }

7. Aggiungere infine le righe seguenti al metodo **Main** :
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartReboot().Wait();
        QueryTwinRebootReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
8. Compilare la soluzione.

## <a name="create-a-simulated-device-app"></a>Creare un'app di dispositivo simulato
Questa sezione consente di:

* Creare un'app console Node.js che risponde a un metodo diretto chiamato dal cloud
* Attivare un riavvio del dispositivo simulato
* Usare le proprietà segnalate per abilitare le query nei dispositivi gemelli in modo da identificare i dispositivi e l'ora dell'ultimo riavvio

1. Creare una nuova cartella vuota denominata **manageddevice**.  Nella cartella **manageddevice** creare un file package.json eseguendo questo comando al prompt dei comandi.  Accettare tutte le impostazioni predefinite:
   
    ```
    npm init
    ```
2. Eseguire questo comando al prompt dei comandi nella cartella **manageddevice** per installare il pacchetto SDK per dispositivi **azure-iot-device** e il pacchetto **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Con un editor di testo creare un nuovo file **dmpatterns_getstarted_device.js** nella cartella **manageddevice**.
4. Aggiungere le istruzioni "require" seguenti all'inizio del file **dmpatterns_getstarted_device.js**:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Aggiungere una variabile **connectionString** e usarla per creare un client dispositivo.  Sostituire la stringa di connessione con la stringa di connessione del dispositivo.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Aggiungere la funzione seguente per implementare il metodo diretto nel dispositivo
   
    ```
    var onReboot = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };
   
        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
   
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```
7. Aggiungere il codice seguente per aprire la connessione all'hub IoT e avviare il listener del metodo diretto:
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```
8. Salvare e chiudere il file **dmpatterns_getstarted_device.js**.
   
   [AZURE.NOTE] Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri per i tentativi, ad esempio un backoff esponenziale, come illustrato nell'articolo di MSDN [Transient Fault Handling][lnk-transient-faults] (Gestione degli errori temporanei).


## <a name="run-the-apps"></a>Eseguire le app
A questo punto è possibile eseguire le app.

1. Al prompt dei comandi nella cartella **manageddevice** eseguire questo comando per iniziare l'ascolto del metodo diretto di riavvio.
   
    ```
    node dmpatterns_getstarted_device.js
    ```
2. Eseguire l'app console C# **TriggerReboot**: fare clic con il pulsante destro del mouse sul progetto **TriggerReboot**, scegliere **Debug** e **Avvia nuova istanza**.

3. Nella console viene visualizzata la risposta del dispositivo al metodo diretto.

## <a name="customize-and-extend-the-device-management-actions"></a>Personalizzare ed estendere le operazioni di gestione dei dispositivi
Le soluzioni IoT possono espandere il set definito di modelli di gestione di dispositivi o abilitare modelli personalizzati tramite l'uso delle primitive dispositivo remoto e metodo da cloud a dispositivo. Altri esempi di operazioni di gestione dei dispositivi includono il ripristino delle informazioni predefinite, l'aggiornamento del firmware, l'aggiornamento del software, il risparmio energia, la gestione di rete e connettività e la crittografia dei dati.

## <a name="device-maintenance-windows"></a>Finestre di manutenzione del dispositivo
Configurare i dispositivi in modo che eseguano le azioni in un momento che riduce al minimo le interruzioni e i tempi di inattività.  Le finestre di manutenzione del dispositivo costituiscono un modello comunemente usato per definire il momento in cui un dispositivo deve eseguire l'aggiornamento della configurazione. Le soluzioni di back-end possono usare le proprietà desiderate del dispositivo gemello per definire e attivare un criterio sul dispositivo che attiva una finestra di manutenzione. Quando un dispositivo riceve il criterio della finestra di manutenzione, può usare la proprietà segnalata del dispositivo gemello per segnalare lo stato del criterio. L'applicazione back-end può quindi usare le query del dispositivo gemello per attestare la conformità dei dispositivi e di tutti i criteri.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato usato un metodo diretto per attivare un riavvio remoto di un dispositivo, sono state usate le proprietà segnalate per segnalare l'ora di ultimo riavvio del dispositivo ed è stata eseguita una query per ottenere l'ora di ultimo riavvio del dispositivo dal cloud.

Per altre informazioni sull'hub IoT e sui modelli di gestione dei dispositivi, ad esempio in modalità remota tramite l'aggiornamento del firmware air, vedere:

[Esercitazione: Come eseguire un aggiornamento del firmware][lnk-fwupdate]

Per informazioni su come estendere la soluzione IoT e pianificare le chiamate al metodo su più dispositivi, vedere l'esercitazione [Pianificare e trasmettere processi][lnk-tutorial-jobs].

Per altre informazioni sulle attività iniziali con l'hub IoT, vedere [Getting started with the IoT Gateway SDK][lnk-gateway-SDK] (Introduzione a IoT Gateway SDK).

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png
[img-servicenuget]: media/iot-hub-csharp-node-device-management-get-started/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-device-management-get-started/createnetapp.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/


<!--HONumber=Nov16_HO5-->


