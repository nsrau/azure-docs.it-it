---
title: "Usare le proprietà di un dispositivo gemello dell'hub IoT di Azure (Node) | Documentazione Microsoft"
description: "Come usare le proprietà di un dispositivo gemello dell'hub IoT di Azure per configurare dispositivi. Usare Azure IoT SDK per Node.js per implementare un'app per dispositivo simulato e un'app di servizio che modifica la configurazione di un dispositivo usando un dispositivo gemello."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: d0bcec50-26e6-40f0-8096-733b2f3071ec
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: elioda
ms.translationtype: HT
ms.sourcegitcommit: 9b7316a5bffbd689bdb26e9524129ceed06606d5
ms.openlocfilehash: 6ff6f1c331d5a77e7ac0a47af6806f5d90fb0fdc
ms.contentlocale: it-it
ms.lasthandoff: 09/08/2017

---
# <a name="use-desired-properties-to-configure-devices-node"></a>Usare le proprietà desiderate per configurare i dispositivi (Node)
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

Al termine di questa esercitazione si avranno due app console Node.js:

* **SimulateDeviceConfiguration.js**, un'app per dispositivo simulata che attende un aggiornamento della configurazione desiderata e segnala lo stato di un processo di aggiornamento della configurazione simulata.
* **SetDesiredConfigurationAndQuery.js**, un'app back-end di Node.js, che imposta la configurazione desiderata in un dispositivo ed esegue query sul processo di aggiornamento della configurazione.

> [!NOTE]
> L'articolo [Azure IoT SDK][lnk-hub-sdks] contiene informazioni sui componenti Azure IoT SDK che consentono di compilare le app back-end e per dispositivi.
> 
> 

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Node.js 4.0.x o versione successiva.
* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito][lnk-free-trial] in pochi minuti.

Se si è seguita l'esercitazione [Introduzione ai dispositivi gemelli][lnk-twin-tutorial] sono già disponibili un hub IoT e un'identità del dispositivo denominata **myDeviceId**. È quindi possibile saltare la sezione [Creare l'app per dispositivo simulato][lnk-how-to-configure-createapp].

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-simulated-device-app"></a>Creare l'app per dispositivo simulata
In questa sezione si crea un'app console Node.js che si connette all'hub come **myDeviceId**, attende un aggiornamento della configurazione desiderata e quindi segnala gli aggiornamenti nel processo di aggiornamento della configurazione simulata.

1. Creare una nuova cartella vuota denominata **simulatedeviceconfiguration**. Nella cartella **simulatedeviceconfiguration** creare un nuovo file package.json immettendo il comando seguente al prompt dei comandi. Accettare tutte le impostazioni predefinite:
   
    ```
    npm init
    ```
2. Eseguire questo comando al prompt dei comandi nella cartella **simulatedeviceconfiguration** per installare i pacchetti **azure-iot-device** e **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Usando un editor di testo, creare un nuovo file **SimulateDeviceConfiguration.js** nella cartella **simulatedeviceconfiguration**.
4. Aggiungere il codice seguente al file **SimulateDeviceConfiguration.js** e sostituire il segnaposto **{device connection string}** con la stringa di connessione copiata quando si è creata l'identità del dispositivo **myDeviceId**:
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
            if (err) {
                console.error('could not open IotHub client');
            } else {
                client.getTwin(function(err, twin) {
                    if (err) {
                        console.error('could not get twin');
                    } else {
                        console.log('retrieved device twin');
                        twin.properties.reported.telemetryConfig = {
                            configId: "0",
                            sendFrequency: "24h"
                        }
                        twin.on('properties.desired', function(desiredChange) {
                            console.log("received change: "+JSON.stringify(desiredChange));
                            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
                            if (desiredChange.telemetryConfig &&desiredChange.telemetryConfig.configId !== currentTelemetryConfig.configId) {
                                initConfigChange(twin);
                            }
                        });
                    }
                });
            }
        });
   
    L'oggetto **Client** espone tutti i metodi necessari per interagire con i dispositivi gemelli dal dispositivo. Il codice precedente, dopo avere inizializzato l'oggetto **Client**, recupera il dispositivo gemello per **myDeviceId** e collega un gestore per l'aggiornamento nelle proprietà desiderate. Il gestore verifica che esista una richiesta di modifica della configurazione effettiva confrontando gli elementi configId, quindi richiama un metodo che avvia la modifica della configurazione.
   
    Si noti che per semplicità il codice precedente usa un valore predefinito hardcoded per la configurazione iniziale. Una vera app probabilmente caricherebbe tale configurazione da una risorsa di archiviazione locale.
   
   > [!IMPORTANT]
   > Gli eventi di modifica delle proprietà desiderate vengono generati sempre una volta al momento della connessione del dispositivo. Controllare che esista una modifica effettiva nelle proprietà desiderate prima di eseguire eventuali azioni.
   > 
   > 
5. Aggiungere i metodi seguenti prima della chiamata a `client.open()`:
   
        var initConfigChange = function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.pendingConfig = twin.properties.desired.telemetryConfig;
            currentTelemetryConfig.status = "Pending";
   
            var patch = {
            telemetryConfig: currentTelemetryConfig
            };
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.log('Could not report properties');
                } else {
                    console.log('Reported pending config change: ' + JSON.stringify(patch));
                    setTimeout(function() {completeConfigChange(twin);}, 60000);
                }
            });
        }
   
        var completeConfigChange =  function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.configId = currentTelemetryConfig.pendingConfig.configId;
            currentTelemetryConfig.sendFrequency = currentTelemetryConfig.pendingConfig.sendFrequency;
            currentTelemetryConfig.status = "Success";
            delete currentTelemetryConfig.pendingConfig;
   
            var patch = {
                telemetryConfig: currentTelemetryConfig
            };
            patch.telemetryConfig.pendingConfig = null;
   
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.error('Error reporting properties: ' + err);
                } else {
                    console.log('Reported completed config change: ' + JSON.stringify(patch));
                }
            });
        };
   
    Il metodo **initConfigChange** aggiorna le proprietà segnalate nell'oggetto dispositivo gemello locale con la richiesta di aggiornamento della configurazione e imposta lo stato su **Pending** (Sospeso), quindi aggiorna il dispositivo gemello nel servizio. Dopo l'aggiornamento del dispositivo gemello, simula un processo a esecuzione prolungata che termina con l'esecuzione di **completeConfigChange**. Questo metodo aggiorna le proprietà segnalate del dispositivo gemello locale impostando lo stato su **Success** e rimuovendo l'oggetto **pendingConfig**, quindi aggiorna il dispositivo gemello nel servizio.
   
    Si noti che, per risparmiare larghezza di banda, le proprietà segnalate vengono aggiornate specificando solo le proprietà da modificare (denominate **patch** nel codice precedente), invece di sostituire l'intero documento.
   
   > [!NOTE]
   > Questa esercitazione non simula alcun comportamento per gli aggiornamenti della configurazione simultanei. Alcuni processi di aggiornamento della configurazione potrebbero consentire modifiche della configurazione di destinazione mentre l'aggiornamento è in esecuzione, altre potrebbero doverle accodare e altri ancora rifiutarle con una condizione di errore. È importante tenere in considerazione il comportamento desiderato per il processo di configurazione specifico e aggiungere la logica appropriata prima di iniziare la modifica della configurazione.
   > 
   > 
6. Eseguire l'app per dispositivo:
   
        node SimulateDeviceConfiguration.js
   
    Dovrebbe essere visualizzato il messaggio `retrieved device twin`. Mantenere l'app in esecuzione.

## <a name="create-the-service-app"></a>Creare l'app di servizio
In questa sezione si creerà un'app console Node.js che aggiorna le *proprietà desiderate* nel dispositivo gemello associato a **myDeviceId** con un nuovo oggetto di configurazione di telemetria. Viene quindi effettuata una query dei dispositivi gemelli archiviati nell'hub IoT e viene visualizzata la differenza tra la configurazione desiderata e quella segnalata del dispositivo.

1. Creare una nuova cartella vuota denominata **setdesiredandqueryapp**. Nella cartella **setdesiredandqueryapp** creare un nuovo file package.json immettendo il comando seguente al prompt dei comandi. Accettare tutte le impostazioni predefinite:
   
    ```
    npm init
    ```
2. Eseguire questo comando al prompt dei comandi nella cartella **setdesiredandqueryapp** per installare il pacchetto **azure-iothub**:
   
    ```
    npm install azure-iothub node-uuid --save
    ```
3. Con un editor di testo creare un nuovo file **SetDesiredAndQuery.js** nella cartella **setdesiredandqueryapp**.
4. Aggiungere il codice seguente al file **SetDesiredAndQuery.js** e sostituire il segnaposto **{iot hub connection string}** con la stringa di connessione dell'hub IoT copiata quando è stato creato l'hub:
   
        'use strict';
        var iothub = require('azure-iothub');
        var uuid = require('node-uuid');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
   
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var newConfigId = uuid.v4();
                var newFrequency = process.argv[2] || "5m";
                var patch = {
                    properties: {
                        desired: {
                            telemetryConfig: {
                                configId: newConfigId,
                                sendFrequency: newFrequency
                            }
                        }
                    }
                }
                twin.update(patch, function(err) {
                    if (err) {
                        console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                    } else {
                        console.log(twin.deviceId + ' twin updated successfully');
                    }
                });
                setInterval(queryTwins, 10000);
            }
        });

    L'oggetto **Registry** espone tutti i metodi necessari per interagire con i dispositivi gemelli dal servizio. Il codice precedente, dopo avere inizializzato l'oggetto **Registry**, recupera il dispositivo gemello per **myDeviceId** e ne aggiorna le proprietà desiderate con un nuovo oggetto di configurazione della telemetria. quindi chiama l'evento della funzione **queryTwins** ogni 10 secondi.

    > [!IMPORTANT]
    > Questa applicazione effettua una query dell'hub IoT ogni 10 secondi a scopo illustrativo. Usare le query per generare i report destinati all'utente in più dispositivi e non per rilevare le modifiche. Se la soluzione richiede notifiche in tempo reale degli eventi del dispositivo, usare le [notifiche relative al dispositivo gemello][lnk-twin-notifications].
    > 
    >.

1. Aggiungere il codice seguente subito prima della chiamata a `registry.getDeviceTwin()` per implementare la funzione **queryTwins**:
   
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log();
                    results.forEach(function(twin) {
                        var desiredConfig = twin.properties.desired.telemetryConfig;
                        var reportedConfig = twin.properties.reported.telemetryConfig;
                        console.log("Config report for: " + twin.deviceId);
                        console.log("Desired: ");
                        console.log(JSON.stringify(desiredConfig, null, 2));
                        console.log("Reported: ");
                        console.log(JSON.stringify(reportedConfig, null, 2));
                    });
                }
            });
        };
   
    Il codice precedente effettua una query dei dispositivi gemelli archiviati nell'hub IoT e visualizza la configurazione della telemetria desiderata e quella segnalata. Vedere il [linguaggio di query dell'hub IoT][lnk-query] per informazioni su come generare report avanzati in tutti i dispositivi.
2. Con **SimulateDeviceConfiguration.js** in esecuzione, eseguire l'applicazione con:
   
        node SetDesiredAndQuery.js 5m
   
    Si potrà osservare la configurazione segnalata passare da **Success** a **Pending** e di nuovo a **Success** con la nuova frequenza di invio attiva di cinque minuti e non più di 24 ore.
   
   > [!IMPORTANT]
   > Tra l'operazione relativa al report del dispositivo e il risultato della query si verifica un ritardo fino a un minuto, che consente all'infrastruttura della query di funzionare su una scala molto ampia. Per recuperare visualizzazioni coerenti di un solo dispositivo gemello, usare il metodo **getDeviceTwin** nella classe **Registry**.
   > 
   > 

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stata impostata una configurazione desiderata come *proprietà desiderate* da un'app back-end ed è stata scritta un'app per dispositivo simulato per rilevare tale modifica e simulare un processo di aggiornamento in più fasi che segnala lo stato come *proprietà segnalate* al dispositivo gemello.

Per altre informazioni, vedere le risorse seguenti:

* Per inviare dati di telemetria dai dispositivi, vedere l'esercitazione [Introduzione all'hub IoT][lnk-iothub-getstarted].
* Per pianificare o eseguire operazioni su grandi set di dispositivi, vedere l'esercitazione [Pianificare e trasmettere processi][lnk-schedule-jobs].
* Per controllare i dispositivi in modo interattivo, ad esempio per attivare un ventilatore da un'app controllata dall'utente, vedere l'esercitazione [Use direct methods][lnk-methods-tutorial] (Usare metodi diretti).

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app

