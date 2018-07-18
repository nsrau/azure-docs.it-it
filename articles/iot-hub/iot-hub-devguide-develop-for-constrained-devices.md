---
title: Hub IoT di Azure - Sviluppare per dispositivi vincolati | Microsoft Docs
description: 'Guida per sviluppatori: materiale sussidiario sulle modalità di sviluppo tramite Azure IoT SDK per i dispositivi vincolati.'
services: iot-hub
documentationcenter: c
author: yzhong94
manager: timlt
editor: ''
ms.assetid: 979136db-c92d-4288-870c-f305e8777bdd
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2018
ms.author: yizhon
ms.openlocfilehash: 15fc5794c71428b5fb1036060af3e9c4a6890f4f
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969062"
---
# <a name="develop-for-constrained-devices-using-azure-iot-sdks"></a>Sviluppare per dispositivi vincolati tramite Azure IoT SDK

## <a name="develop-using-azure-iot-hub-c-sdk"></a>Sviluppare tramite l'SDK C per l'hub IoT di Azure
L'SDK C dell'hub IoT di Azure è scritto in ANSI C (C99), che lo rende particolarmente adatto per il funzionamento su una vasta gamma di piattaforme con footprint di memoria e disco ridotto.  La quantità di RAM consigliata è almeno 64 KB, ma il footprint di memoria esatto varia in base al protocollo usato, al numero di connessioni aperte e alla piattaforma di destinazione.

L'SDK C è disponibile in forma di pacchetto da apt-get, NuGet e MBED.  Per fare riferimento ai dispositivi vincolati, è possibile compilare l'SDK in locale per la piattaforma di destinazione. Questa documentazione illustra come rimuovere alcune funzionalità per ridurre il footprint dell'SDK C usando [cmake][lnk-cmake].  Inoltre, questa documentazione presenta i modelli di programmazione consigliati per l'uso con i dispositivi vincolati.

### <a name="building-the-c-sdk-for-constrained-devices"></a>Compilazione dell'SDK C per i dispositivi vincolati
#### <a name="prerequisites"></a>prerequisiti
Seguire questa [guida all'installazione][lnk-devbox-setup] per preparare l'ambiente di sviluppo per la compilazione dell'SDK C.  Prima di arrivare al passaggio per la compilazione con cmake, è possibile richiamare flag cmake per rimuovere le funzionalità non usate.

#### <a name="remove-additional-protocol-libraries"></a>Rimuovere le librerie di protocollo aggiuntive
L'SDK C supporta attualmente cinque protocolli: MQTT, MQTT su WebSocket, AMQPs, AMQP su WebSocket e HTTPS.    La maggior parte degli scenari richiede uno o due protocolli in esecuzione in un client, pertanto è possibile rimuovere dall'SDK la libreria di protocollo non usata.  Altre informazioni sulla scelta del protocollo di comunicazione appropriato per uno specifico scenario sono disponibili in questo [documento][lnk-choosing-protocol].  Ad esempio, MQTT è un protocollo leggero che spesso è più indicato per i dispositivi vincolati.

È possibile rimuovere le librerie AMQP e HTTP usando il comando cmake seguente:
```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

#### <a name="remove-sdk-logging-capability"></a>Rimuovere la funzionalità di registrazione dall'SDK
L'SDK C assicura una registrazione completa per supportare il debug. È possibile rimuovere la funzionalità di registrazione per i dispositivi di produzione usando il comando cmake seguente:
```
cmake -Dno_logging=OFF <Path_to_cmake>
```

#### <a name="remove-upload-to-blob-capability"></a>Rimuovere la funzionalità di caricamento nel BLOB
È possibile caricare file di grandi dimensioni in Archiviazione di Azure tramite la funzionalità predefinita dell'SDK.  L'hub IoT di Azure funge da dispatcher per un account di Archiviazione di Azure associato.  È possibile usare questa funzionalità per inviare file multimediali, batch di dati di telemetria di grandi dimensioni e log.  Altre informazioni sul caricamento dei file con l'hub IoT sono disponibili in questo [documento][lnk-hub-file-upload].  Se l'applicazione non necessita di questa funzionalità, è possibile rimuoverla con il comando cmake seguente:
```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```
#### <a name="running-strip-on-linux-environment"></a>Esecuzione di strip nell'ambiente Linux
Se i file binari sono eseguiti in un sistema Linux, è possibile usare il [comando strip][lnk-strip] per ridurre le dimensioni dell'applicazione finale dopo la compilazione.
```
strip -s <Path_to_executable>
```

### <a name="programming-models-for-constrained-devices"></a>Modelli di programmazione per i dispositivi vincolati
#### <a name="avoid-using-the-serializer"></a>Evitare l'uso del serializzatore
L'SDK C dispone di un [serializzatore][lnk-serializer] facoltativo che consente di usare tabelle di mapping dichiarative per definire i metodi e le proprietà dei dispositivi gemelli.  Il serializzatore è progettato per semplificare lo sviluppo, ma aggiunge un sovraccarico, non indicato per i dispositivi vincolati.  In questo caso, provare a usare le API client primitive e analizzare json tramite un parser leggero come [parson][lnk-parson].

#### <a name="use-the-lower-layer-ll"></a>Usare il livello inferiore (_LL_)
L'SDK C supporta due modelli di programmazione.  Un set dispone di API con un infisso _LL_, che indica il livello inferiore.  Questo set di API è più leggero e non avvia thread di lavoro, il che significa che l'utente deve controllare manualmente la pianificazione.  Ad esempio, per il client del dispositivo, le API _LL_ sono disponibili in questo [file di intestazione](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h).  Un altro set di API senza l'indice _LL_ viene definito livello dedicato, in cui un thread di lavoro viene avviato automaticamente.  Ad esempio, le API del livello dedicato per il client del dispositivo sono disponibili in questo [file di intestazione](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h).  Per i dispositivi vincolati in cui ogni thread aggiuntivo può richiedere una percentuale notevole delle risorse di sistema, prendere in considerazione l'uso delle API _LL_.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'architettura dell'SDK C per l'hub IoT di Azure:
-   [Codice sorgente dell'SDK C per l'hub IoT di Azure](https://github.com/Azure/azure-iot-sdk-c/)
-   [Introduzione ad Azure IoT SDK per dispositivi per C](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-sdk-c-intro)

------
[lnk-cmake]: https://cmake.org/
[lnk-devbox-setup]:  https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md
[lnk-choosing-protocol]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols
[lnk-hub-file-upload]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-file-upload
[lnk-strip]: https://en.wikipedia.org/wiki/Strip_(Unix)
[lnk-serializer]: https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer
[lnk-parson]: https://github.com/kgabis/parson