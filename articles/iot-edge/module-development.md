---
title: Sviluppare i moduli per Azure IoT Edge | Microsoft Docs
description: Sviluppare moduli personalizzati per Azure IoT Edge in grado di comunicare con il runtime e l'hub IoT
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5285490ca1a27494cbcd3ea3d6527b78c7d38c8c
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833429"
---
# <a name="develop-your-own-iot-edge-modules"></a>Sviluppare moduli di IoT Edge personalizzati

I moduli di IoT Edge di Azure possono connettersi con altri servizi di Azure e contribuire alla pipeline di dati cloud più estesa. Questo articolo descrive come sviluppare i moduli per comunicare con il runtime di IoT Edge e l'hub IoT e di conseguenza il resto del cloud di Azure. 

## <a name="iot-edge-runtime-environment"></a>Ambiente di runtime di IoT Edge
Il runtime di IoT Edge offre l'infrastruttura per integrare le funzionalità di più moduli di IoT Edge e distribuirle nei dispositivi IoT Edge. A livello generale, qualsiasi programma può essere inserito in un pacchetto come modulo di IoT Edge. Tuttavia, per sfruttare al meglio le funzionalità di gestione e comunicazione di IoT Edge, un programma in esecuzione in un modulo può connettersi all'hub di IoT Edge locale, integrato nel runtime di IoT Edge.

## <a name="using-the-iot-edge-hub"></a>Tramite l'hub di IoT Edge
L'hub di IoT Edge offre due funzionalità principali: proxy per l'hub IoT e comunicazioni locali.

### <a name="iot-hub-primitives"></a>Primitive di hub IoT
Hub IoT visualizza un'istanza del modulo analogamente a un dispositivo, nel senso che:

* Ha un modulo gemello distinto e isolato dal [dispositivo gemello](../iot-hub/iot-hub-devguide-device-twins.md) e dagli altri moduli gemelli del dispositivo.
* Può inviare [messaggi da dispositivo a cloud](../iot-hub/iot-hub-devguide-messaging.md);
* Può ricevere [metodi diretti](../iot-hub/iot-hub-devguide-direct-methods.md) destinati in particolare alla propria identità.

Attualmente, un modulo non può ricevere messaggi da cloud a dispositivo né usare la funzionalità di caricamento dei file.

Durante la scrittura di un modulo, è possibile usare [Azure IoT SDK per dispositivi](../iot-hub/iot-hub-devguide-sdks.md) per connettersi all'hub di IoT Edge e usare le funzionalità descritte sopra come quando si usa l'hub IoT con un'applicazione del dispositivo. L'unica differenza è che dal back-end dell'applicazione è necessario fare riferimento all'identità del modulo invece che all'identità del dispositivo.

### <a name="device-to-cloud-messages"></a>Messaggi da dispositivo a cloud
Per abilitare la complessa elaborazione di messaggi da dispositivo a cloud, l'hub di IoT Edge offre un routing dichiarativo dei messaggi tra moduli e tra moduli e hub IoT. Il routing dichiarativo consente ai moduli di intercettare ed elaborare i messaggi inviati da altri moduli e di propagarli in pipeline complesse. Per altre informazioni, vedere [Informazioni su come distribuire moduli e definire route in IoT Edge](module-composition.md).

Un modulo di IoT Edge, contrariamente a una normale applicazione del dispositivo dell'hub IoT, può ricevere messaggi da dispositivo a cloud che vengono trasmessi tramite proxy dall'hub di IoT Edge locale per elaborarli.

L'hub di IoT Edge propaga i messaggi al modulo in base alle route dichiarative descritte nel [manifesto della distribuzione](module-composition.md). Quando si sviluppa un modulo IoT Edge, è possibile ricevere questi messaggi impostando gestori di messaggi.

Per semplificare la creazione delle route, IoT Edge aggiunge il concetto di endpoint di *input* e *output* del modulo. Un modulo può ricevere tutti i messaggi da dispositivo a cloud indirizzati ad esso senza specificare l'input e può inviare i messaggi da dispositivo a cloud senza specificare l'output. Tuttavia,l'uso di input e output espliciti rende le regole di routing più semplici da comprendere. 

Infine, i messaggi da dispositivo a cloud gestiti dall'hub Edge vengono contrassegnati con le proprietà di sistema seguenti:

| Proprietà | Descrizione |
| -------- | ----------- |
| $connectionDeviceId | L'ID dispositivo del client che ha inviato il messaggio |
| $connectionModuleId | L'ID modulo del modulo che ha inviato il messaggio |
| $inputName | L'input che ha ricevuto il messaggio. Può essere vuoto. |
| $outputName | L'output usato per inviare il messaggio. Può essere vuoto. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Connessione all'hub di IoT Edge da un modulo
La connessione all'hub di IoT Edge locale da un modulo prevede due passaggi: 
1. Creare un'istanza di ModuleClient nell'applicazione.
2. Verificare che l'applicazione accetti il certificato presentato dall'hub di IoT Edge su tale dispositivo.

Creare un'istanza di ModuleClient per connettere il modulo all'hub IoT Edge in esecuzione nel dispositivo, nello stesso modo in cui le istanze di DeviceClient connettono i dispositivi IoT all'hub IoT. Per altre informazioni sulla classe ModuleClient e i relativi metodi di comunicazione, vedere le informazioni si riferimento sull'API per il linguaggio preferito per l'SDK: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C e Python](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable) o [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).


## <a name="next-steps"></a>Passaggi successivi

[Preparare l'ambiente di sviluppo e test per IoT Edge](development-environment.md)

[Usare Visual Studio per lo sviluppo di C# moduli di IoT Edge](how-to-visual-studio-develop-module.md)

[Usare Visual Studio Code per sviluppare moduli di IoT Edge](how-to-vs-code-develop-module.md)

