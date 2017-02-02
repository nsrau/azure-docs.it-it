---
title: Introduzione ai dispositivi gemelli dell&quot;hub IoT di Azure (Node) | Documentazione Microsoft
description: Come usare i dispositivi gemelli dell&quot;hub IoT di Azure per aggiungere tag e quindi usare una query dell&quot;hub IoT. Usare Azure IoT SDK per Node.js per implementare l&quot;app per dispositivo simulato e un&quot;app di servizio che aggiunge i tag ed esegue la query dell&quot;hub IoT.
services: iot-hub
documentationcenter: node
author: fsautomata
manager: timlt
editor: 
ms.assetid: 314c88e4-cce1-441c-b75a-d2e08e39ae7d
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: a243e4f64b6cd0bf7b0776e938150a352d424ad1
ms.openlocfilehash: 527aed57517f04d1b0fdcad5feac5488123b89c7


---
# <a name="get-started-with-device-twins-node"></a>Introduzione ai dispositivi gemelli (Node)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Al termine di questa esercitazione si avranno due app console Node.js:

* **AddTagsAndQuery.js**, un'app back-end di Node.js, che aggiunge tag ed esegue query sui dispositivi gemelli.
* **TwinSimulatedDevice.js**, un'app Node.js che simula un dispositivo che si connette all'hub IoT con l'identità del dispositivo creata prima e segnala la condizione della connettività.

> [!NOTE]
> L'articolo [Azure IoT SDK][lnk-hub-sdks] contiene informazioni sui componenti Azure IoT SDK che consentono di compilare le app back-end e per dispositivi.
> 
> 

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Node.js 0.10.x o versione successiva.
* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito][lnk-free-trial] in pochi minuti.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-service-app"></a>Creare l'app di servizio
In questa sezione si crea a un'app console Node.js che aggiunge i metadati della posizione al dispositivo gemello associato a **myDeviceId**. Viene quindi effettua una query dei dispositivi gemelli archiviati nell'hub IoT selezionando i dispositivi situati negli Stati Uniti e quindi quelli che segnalano una rete cellulare.

1. Creare una nuova cartella vuota denominata **addtagsandqueryapp**. Nella cartella **addtagsandqueryapp** creare un nuovo file package.json immettendo il comando seguente al prompt dei comandi. Accettare tutte le impostazioni predefinite:
   
    ```
    npm init
    ```
2. Eseguire questo comando al prompt dei comandi nella cartella **addtagsandqueryapp** per installare il pacchetto **azure-iothub**:
   
    ```
    npm install azure-iothub --save
    ```
3. Usando un editor di testo, creare un nuovo file **AddTagsAndQuery.js** nella cartella **addtagsandqueryapp**.
4. Aggiungere il codice seguente al file **AddTagsAndQuery.js** e sostituire il segnaposto **{iot hub connection string}** con la stringa di connessione dell'hub IoT copiata quando è stato creato l'hub:
   
        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
   
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };
   
                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });
   
    L'oggetto **Registry** espone tutti i metodi necessari per interagire con i dispositivi gemelli dal servizio. Il codice precedente inizializza prima l'oggetto **Registry**, quindi recupera il dispositivo gemello per **myDeviceId** e infine ne aggiorna i tag con le informazioni sulla posizione desiderate.
   
    Dopo avere aggiornato i tag, chiama la funzione **queryTwins**.
5. Aggiungere il codice seguente alla fine di **AddTagsAndQuery.js** per implementare la funzione **queryTwins**:
   
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
   
            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };
   
    Il codice precedente esegue due query: la prima seleziona solo i dispositivi gemelli tra quelli situati nello stabilimento **Redmond43** e la seconda affina la query per selezionare solo i dispositivi che sono anche connessi tramite la rete cellulare.
   
    Si noti che il codice precedente, quando crea l'oggetto **query**, specifica un numero massimo di documenti restituiti. L'oggetto **query** contiene una proprietà booleana **hasMoreResults** che è possibile usare per richiamare i metodi **nextAsTwin** più volte per recuperare tutti i risultati. Un metodo chiamato **next** è disponibile per i risultati che non sono dispositivi gemelli, ad esempio i risultati delle query di aggregazione.
6. Eseguire l'applicazione con:
   
        node AddTagsAndQuery.js
   
    Nei risultati si noterà un dispositivo per la query che cerca tutti i dispositivi situati in **Redmond43** e nessuno per la query che limita i risultati ai dispositivi che usano una rete cellulare.
   
    ![][1]

Nella sezione successiva si crea un'app per dispositivo che segnala le informazioni sulla connettività e modifica il risultato della query nella sezione precedente.

## <a name="create-the-device-app"></a>Creare l'app per dispositivo
In questa sezione si crea un'app console Node.js che si connette all'hub come **myDeviceId** e quindi aggiorna le proprietà segnalate del dispositivo gemello per poter contenere le informazioni relative alla connessione usando una rete cellulare.

> [!NOTE]
> Al momento i dispositivi gemelli sono accessibili solo dai dispositivi che si connettono all'hub IoT tramite il protocollo MQTT. Per istruzioni su come convertire l'app per dispositivo esistente in modo che usi MQTT, vedere l'articolo [Supporto di MQTT][lnk-devguide-mqtt].
> 
> 

1. Creare una nuova cartella vuota denominata **reportconnectivity**. Nella cartella **reportconnectivity** creare un nuovo file package.json immettendo il comando seguente al prompt dei comandi. Accettare tutte le impostazioni predefinite:
   
    ```
    npm init
    ```
2. Eseguire questo comando al prompt dei comandi nella cartella **reportconnectivity** per installare i pacchetti **azure-iot-device** e **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Usando un editor di testo, creare un nuovo file **ReportConnectivity.js** nella cartella **reportconnectivity**.
4. Aggiungere il codice seguente al file **ReportConnectivity.js** e sostituire il segnaposto **{device connection string}** con la stringa di connessione del dispositivo copiata quando si è creata l'identità del dispositivo **myDeviceId**:
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
   
            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };
   
                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
   
    L'oggetto **Client** espone tutti i metodi necessari per interagire con i dispositivi gemelli dal dispositivo. Il codice precedente, dopo avere inizializzato l'oggetto **Client**, recupera il dispositivo gemello per **myDeviceId** e aggiorna le proprietà segnalate con le informazioni sulla connettività.
5. Eseguire l'app per dispositivo
   
        node ReportConnectivity.js
   
    Dovrebbe essere visualizzato il messaggio `twin state reported`.
6. Ora che il dispositivo ha segnalato le informazioni sulla connettività, verrà visualizzato in entrambe le query. Tornare alla cartella **addtagsandqueryapp** ed eseguire di nuovo le query:
   
        node AddTagsAndQuery.js
   
    Questa volta **myDeviceId** verrà visualizzato nei risultati di entrambe le query.
   
    ![][3]

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato configurato un nuovo hub IoT nel Portale di Azure ed è stata quindi creata un'identità del dispositivo nel registro di identità dell'hub IoT. Sono stati aggiunti i metadati del dispositivo come tag da un'app back-end ed è stata scritta un'app per dispositivo simulato per segnalare le informazioni sulla connettività del dispositivo nel dispositivo gemello. Si è anche appreso come effettuare una query di queste informazioni usando il linguaggio di query simile a SQL dell'hub IoT.

Per altre informazioni, vedere le risorse seguenti:

* Per inviare dati di telemetria dai dispositivi, vedere l'esercitazione [Introduzione all'hub IoT][lnk-iothub-getstarted].
* Per configurare i dispositivi usando le proprietà desiderate del dispositivo gemello, vedere l'esercitazione [Usare le proprietà desiderate per configurare i dispositivi][lnk-twin-how-to-configure].
* Per controllare i dispositivi in modo interattivo, ad esempio per attivare un ventilatore da un'app controllata dall'utente, vedere l'esercitazione [Use direct methods][lnk-methods-tutorial] (Usare metodi diretti).

<!-- images -->
[1]: media/iot-hub-node-node-twin-getstarted/service1.png
[3]: media/iot-hub-node-node-twin-getstarted/service2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: iot-hub-node-node-twin-how-to-configure.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md



<!--HONumber=Dec16_HO1-->


