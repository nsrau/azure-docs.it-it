---
title: Aggiornamento firmware del dispositivo con l&quot;hub IoT di Azure (.NET/Node) | Documentazione Microsoft
description: Come usare la gestione dei dispositivi nell&quot;hub IoT di Azure per avviare un aggiornamento del firmware del dispositivo. Usare Azure IoT SDK per dispositivi per Node.js per implementare un&quot;app per dispositivo simulato e Azure IoT SDK per servizi per .NET per implementare un&quot;app di servizio che attiva l&quot;aggiornamento del firmware.
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/17/2017
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: c2192328a152e955d182c4a07b391c98a5960964
ms.lasthandoff: 04/03/2017


---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnode"></a>Usare la gestione dei dispositivi per avviare un aggiornamento del firmware del dispositivo (.NET/Node)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>Introduzione
Nell'esercitazione [Introduzione alla gestione dei dispositivi][lnk-dm-getstarted] è stato illustrato come usare il [dispositivo gemello][lnk-devtwin] e le primitive dei [metodi diretti][lnk-c2dmethod] per riavviare un dispositivo in modalità remota. Questa esercitazione usa le stesse primitive dell'hub IoT e illustra come eseguire un aggiornamento del firmware simulato completo.  Questo modello viene usato nell'implementazione dell'aggiornamento del firmware per l'[esempio di implementazione del dispositivo Raspberry Pi][lnk-rpi-implementation].

Questa esercitazione illustra come:

* Creare un'app console .NET che chiama il metodo diretto firmwareUpdate nell'app per dispositivo simulato tramite l'hub IoT.
* Creare un'app di dispositivo simulato che implementa un metodo diretto **firmwareUpdate**. Questo metodo avvia un processo in più fasi che attende di scaricare l'immagine del firmware, quindi la scarica e infine la applica. Durante l'esecuzione di ogni fase, il dispositivo usa le proprietà segnalate per aggiornare lo stato.

Al termine dell'esercitazione saranno disponibili un'app per il dispositivo console Node.js e un'app back-end console .NET (C#):

**dmpatterns_fwupdate_service.js**, che chiama un metodo diretto nell'app per dispositivo simulato, visualizza la risposta e visualizza regolarmente (ogni 500 ms) le proprietà segnalate aggiornate.

**TriggerFWUpdate**, che connette all'hub IoT con l'identità del dispositivo creata prima, riceve un metodo diretto firmwareUpdate, esegue un processo in più stati per simulare un aggiornamento del firmware, inclusi l'attesa del download dell'immagine, il download della nuova immagine e infine l'applicazione dell'immagine.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Visual Studio 2015 o Visual Studio 2017.
* Node.js 0.12.x o versione successiva. <br/>  [Prepare your development environment][lnk-dev-setup] (Preparare l'ambiente di sviluppo) descrive come installare Node.js per questa esercitazione in Windows o Linux.
* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito][lnk-free-trial] in pochi minuti.

Vedere l'articolo [Introduzione alla gestione dei dispositivi](iot-hub-csharp-node-device-management-get-started.md) per creare l'hub IoT e ottenere la stringa di connessione relativa.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Attivare un aggiornamento del firmware remoto nel dispositivo con un metodo diretto
In questa sezione viene creata un'app console .NET (tramite C#) che avvia un aggiornamento del firmware remoto in un dispositivo. L'applicazione usa un metodo diretto per avviare l'aggiornamento e usa le query di dispositivo gemello per ottenere periodicamente lo stato di aggiornamento del firmware attivo.

1. In Visual Studio aggiungere un progetto desktop di Windows classico in Visual C# usando il modello di progetto **Applicazione console** . Assegnare al progetto il nome **TriggerFWUpdate**.

    ![Nuovo progetto desktop di Windows classico in Visual C#][img-createapp]

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **TriggerFWUpdate** e quindi scegliere **Gestisci pacchetti NuGet**.
1. Nella finestra **Gestione pacchetti NuGet** selezionare **Esplora**, cercare **microsoft.azure.devices**, selezionare **Installa** per installare il pacchetto **Microsoft.Azure.Devices** e accettare le condizioni per l'uso. Questa procedura scarica, installa e aggiunge un riferimento al [pacchetto NuGet Azure IoT - SDK per dispositivi][lnk-nuget-service-sdk] e alle relative dipendenze.

    ![Finestra Gestione pacchetti NuGet][img-servicenuget]
1. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Shared;
        
1. Aggiungere i campi seguenti alla classe **Program** . Sostituire il valore dei segnaposti multipli con la stringa di connessione dell'hub IoT creato nella sezione precedente e l'ID del dispositivo.
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        static string targetDevice = "{deviceIdForTargetDevice}";
        
1. Aggiungere il metodo seguente alla classe **Program** :
   
        public static async Task QueryTwinFWUpdateReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
1. Aggiungere il metodo seguente alla classe **Program** :

        public static async Task StartFirmwareUpdate()
        {
            client = ServiceClient.CreateFromConnectionString(connString);
            CloudToDeviceMethod method = new CloudToDeviceMethod("firmwareUpdate");
            method.ResponseTimeout = TimeSpan.FromSeconds(30);
            method.SetPayloadJson(
                @"{
                    fwPackageUri : 'https://someurl'
                }");

            CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

            Console.WriteLine("Invoked firmware update on device.");
        }

1. Aggiungere infine le righe seguenti al metodo **Main** :
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartFirmwareUpdate().Wait();
        QueryTwinFWUpdateReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
1. In Esplora soluzioni aprire **Imposta progetti di avvio** e assicurarsi che l'**azione** per il progetto **TriggerFWUpdate** sia impostata su **Avvio**.

1. Compilare la soluzione.

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>Eseguire le app
A questo punto è possibile eseguire le app.

1. Al prompt dei comandi nella cartella **manageddevice** eseguire questo comando per iniziare l'ascolto del metodo diretto di riavvio.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. In Visual Studio, fare clic con il pulsante destro del mouse sul progetto **TriggerFWUpdate**. Eseguire l'app console C# quindi scegliere **Debug** e **Avvia nuova istanza**.

3. Nella console viene visualizzata la risposta del dispositivo al metodo diretto.

    ![Firmware aggiornato][img-fwupdate]

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato usato un metodo diretto per attivare un aggiornamento del firmware remoto in un dispositivo e sono state usate le proprietà segnalate per conoscere lo stato del processo di aggiornamento del firmware.

Per informazioni su come estendere la soluzione IoT e pianificare le chiamate al metodo su più dispositivi, vedere l'esercitazione [Pianificare e trasmettere processi][lnk-tutorial-jobs].

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-firmware-update/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-firmware-update/createnetapp.png
[img-fwupdate]: media/iot-hub-csharp-node-firmware-update/fwupdated.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
