---
title: Sviluppare i moduli per Azure IoT Edge | Microsoft Docs
description: Informazioni su come creare moduli personalizzati per Azure IoT Edge
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 95b1d5d4e5e11f96b6abb17f0aeba935cc65512d
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="understand-the-requirements-and-tools-for-developing-iot-edge-modules---preview"></a>Informazioni sui requisiti e gli strumenti per sviluppare i moduli IoT Edge: anteprima

Questo articolo illustra quali funzionalità sono disponibili quando si scrivono applicazioni che vengono eseguite come modulo di IoT Edge e come sfruttarne i vantaggi.

## <a name="iot-edge-runtime-environment"></a>Ambiente di runtime di IoT Edge
Il runtime di IoT Edge offre l'infrastruttura per integrare le funzionalità di più moduli di IoT Edge e distribuirle nei dispositivi IoT Edge. A livello generale, qualsiasi programma può essere inserito in un pacchetto come modulo di IoT Edge. Tuttavia, per sfruttare al meglio le funzionalità di gestione e comunicazione di IoT Edge, un programma in esecuzione in un modulo può connettersi all'hub di IoT Edge locale, integrato nel runtime di IoT Edge.

## <a name="using-the-iot-edge-hub"></a>Tramite l'hub di IoT Edge
L'hub di IoT Edge offre due funzionalità principali: proxy per l'hub IoT e comunicazioni locali.

### <a name="iot-hub-primitives"></a>Primitive di hub IoT
Hub IoT visualizza un'istanza del modulo analogamente a un dispositivo, nel senso che:

* dispone di un modulo gemello distinto e isolato dal [dispositivo gemello][lnk-devicetwin] e dagli altri moduli gemelli del dispositivo;
* può inviare [messaggi da dispositivo a cloud][lnk-iothub-messaging];
* può ricevere [metodi diretti][lnk-methods] destinati in particolare alla propria identità.

Attualmente, un modulo non può ricevere messaggi da cloud a dispositivo né usare la funzionalità di caricamento dei file.

Durante la scrittura di un modulo, basta usare [Azure IoT SDK per dispositivi][lnk-devicesdk] per connettersi all'hub di IoT Edge e usare le funzionalità descritte sopra come quando si usa l'hub IoT con un'applicazione del dispositivo. L'unica differenza è che dal back-end dell'applicazione è necessario fare riferimento all'identità del modulo invece che all'identità del dispositivo.

Vedere [Sviluppare e distribuire un modulo di IoT Edge in un dispositivo simulato][lnk-tutorial2] per un esempio di un'applicazione del modulo che invia i messaggi da dispositivo a cloud e usa il modulo gemello.

### <a name="device-to-cloud-messages"></a>Messaggi da dispositivo a cloud
Per abilitare la complessa elaborazione di messaggi da dispositivo a cloud, l'hub di IoT Edge offre un routing dichiarativo dei messaggi tra moduli e tra moduli e hub IoT.
Questo consente ai moduli di intercettare ed elaborare i messaggi inviati da altri moduli e di propagarli in pipeline complesse.
L'articolo sulla [composizione del modulo][lnk-module-comp] illustra come comporre i moduli in pipeline complesse usando le route.

Un modulo di IoT Edge, diversamente da una normale applicazione del dispositivo dell'hub IoT, può ricevere messaggi da dispositivo a cloud che vengono trasmessi tramite proxy dall'hub di IoT Edge locale per elaborarli.

L'hub di IoT Edge propaga i messaggi al modulo in base alle route dichiarative descritte nell'articolo sulla [composizione del modulo][lnk-module-comp]. Quando si sviluppa un modulo di IoT Edge, è possibile ricevere questi messaggi impostando i gestori dei messaggi, come illustrato nell'esercitazione [Sviluppare e distribuire un modulo di IoT Edge in un dispositivo simulato][lnk-tutorial2].

Per semplificare la creazione delle route, IoT Edge aggiunge il concetto di endpoint di *input* e *output* del modulo. Un modulo può ricevere tutti i messaggi da dispositivo a cloud indirizzati ad esso senza specificare l'input e può inviare i messaggi da dispositivo a cloud senza specificare l'output.
Tuttavia,l'uso di input e output espliciti rende le regole di routing più semplici da comprendere. Vedere [Composizione del modulo][lnk-module-comp] per altre informazioni sulle regole di routing e gli endpoint di input e outpui per i moduli.

Infine, i messaggi da dispositivo a cloud gestiti dall'hub Edge vengono contrassegnati con le proprietà di sistema seguenti:

| Proprietà | DESCRIZIONE |
| -------- | ----------- |
| $connectionDeviceId | L'ID dispositivo del client che ha inviato il messaggio |
| $connectionModuleId | L'ID modulo del modulo che ha inviato il messaggio |
| $inputName | L'input che ha ricevuto il messaggio. Può essere vuoto. |
| $outputName | L'output usato per inviare il messaggio. Può essere vuoto. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Connessione all'hub di IoT Edge da un modulo
La connessione all'hub di IoT Edge locale da un modulo prevede due passaggi: usare la stringa di connessione generata dal runtime di IoT Edge all'avvio di un modulo e assicurarsi che l'applicazione accetti il certificato presentato dall'hub di IoT Edge sul dispositivo.

La stringa di connessione da usare viene inserita dal runtime di IoT Edge nella variabile di ambiente `EdgeHubConnectionString`. Questo la rende disponibile per tutti i programmi che desiderano usarla.

Analogamente, il certificato da usare per convalidare la connessione dell'hub di IoT Edge viene inserito dal runtime di IoT Edge in un file il cui percorso è disponibile nella variabile di ambiente `EdgeModuleCACertificateFile`.

L'esercitazione [Sviluppare e distribuire un modulo di IoT Edge in un dispositivo simulato][lnk-tutorial2] illustra come assicurarsi che il certificato si trovi nell'archivio del computer dell'applicazione del modulo. Chiaramente, qualsiasi altro metodo per considerare attendibili le connessioni che usano tale certificato funziona correttamente.

## <a name="packaging-as-an-image"></a>Creazione del pacchetto come immagine
I moduli di IoT Edge vengono inseriti nei pacchetti come immagini Docker.
È possibile usare direttamente la toolchain di Docker o Visual Studio Code, come illustrato nell'esercitazione [Sviluppare e distribuire un modulo di IoT Edge in un dispositivo simulato][lnk-tutorial2].

## <a name="next-steps"></a>Passaggi successivi

Dopo aver sviluppato un modulo, imparare a [Distribuire e monitorare i moduli di IoT Edge su larga scala][lnk-howto-deploy].

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-devicetwin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-messaging]: ../iot-hub/iot-hub-devguide-messaging.md
[lnk-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-tutorial2]: tutorial-csharp-module.md
[lnk-module-comp]: module-composition.md
[lnk-howto-deploy]: how-to-deploy-monitor.md
