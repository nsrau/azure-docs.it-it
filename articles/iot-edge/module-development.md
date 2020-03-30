---
title: Sviluppare i moduli per Azure IoT Edge | Microsoft Docs
description: Sviluppare moduli personalizzati per Azure IoT Edge in grado di comunicare con il runtime e l'hub IoT
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 96bd6b461a5374b5f5bc578c5f58dbcd09cd7087
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271382"
---
# <a name="develop-your-own-iot-edge-modules"></a>Sviluppare moduli di IoT Edge personalizzati

I moduli di IoT Edge di Azure possono connettersi con altri servizi di Azure e contribuire alla pipeline di dati cloud più estesa. Questo articolo descrive come sviluppare i moduli per comunicare con il runtime di IoT Edge e l'hub IoT e di conseguenza il resto del cloud di Azure.

## <a name="iot-edge-runtime-environment"></a>Ambiente di runtime di IoT Edge

Il runtime di IoT Edge offre l'infrastruttura per integrare le funzionalità di più moduli di IoT Edge e distribuirle nei dispositivi IoT Edge. Qualsiasi programma può essere incluso nel pacchetto come modulo IoT Edge. Per sfruttare appieno le funzionalità di comunicazione e gestione di IoT Edge, un programma in esecuzione in un modulo può usare l'SDK del dispositivo IoT di Azure per connettersi all'hub IoT Edge locale.

## <a name="using-the-iot-edge-hub"></a>Tramite l'hub di IoT Edge

L'hub di IoT Edge offre due funzionalità principali: proxy per l'hub IoT e comunicazioni locali.

### <a name="iot-hub-primitives"></a>Primitive di hub IoT

Hub IoT visualizza un'istanza del modulo analogamente a un dispositivo, nel senso che:

* ha un modulo gemello che è distinto e isolato dal [dispositivo gemello](../iot-hub/iot-hub-devguide-device-twins.md) e gli altri gemelli modulo di quel dispositivo;
* Può inviare [messaggi da dispositivo a cloud](../iot-hub/iot-hub-devguide-messaging.md);
* Può ricevere [metodi diretti](../iot-hub/iot-hub-devguide-direct-methods.md) destinati in particolare alla propria identità.

Attualmente, i moduli non possono ricevere messaggi da cloud a dispositivo o utilizzare la funzionalità di caricamento dei file.

Quando si scrive un modulo, è possibile usare [L'SDK del dispositivo IoT](../iot-hub/iot-hub-devguide-sdks.md) di Azure per connettersi all'hub IoT Edge e usare la funzionalità precedente come quando si usa l'hub IoT con un'applicazione per dispositivi. L'unica differenza tra i moduli IoT Edge e le applicazioni per dispositivi IoT è che è necessario fare riferimento all'identità del modulo anziché all'identità del dispositivo.

### <a name="device-to-cloud-messages"></a>Messaggi da dispositivo a cloud

Per abilitare la complessa elaborazione di messaggi da dispositivo a cloud, l'hub di IoT Edge offre un routing dichiarativo dei messaggi tra moduli e tra moduli e hub IoT. Il routing dichiarativo consente ai moduli di intercettare ed elaborare i messaggi inviati da altri moduli e di propagarli in pipeline complesse. Per altre informazioni, vedere [Informazioni su come distribuire moduli e definire route in IoT Edge](module-composition.md).

Un modulo IoT Edge, a differenza di una normale applicazione per dispositivi Hub IoT, può ricevere messaggi da dispositivo a cloud che vengono inviati tramite proxy dall'hub IoT Edge locale per elaborarli.

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

Creare un'istanza di ModuleClient per connettere il modulo all'hub IoT Edge in esecuzione nel dispositivo, nello stesso modo in cui le istanze di DeviceClient connettono i dispositivi IoT all'hub IoT. Per ulteriori informazioni sulla classe ModuleClient e sui relativi metodi di comunicazione, vedere le informazioni di riferimento sulle API per il linguaggio SDK preferito: [C'](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)o [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

## <a name="language-and-architecture-support"></a>Supporto per linguaggi e architetture

IoT Edge supporta più sistemi operativi, architetture di dispositivi e linguaggi di sviluppo in modo da poter creare lo scenario che soddisfa le proprie esigenze. Utilizzare questa sezione per comprendere le opzioni disponibili per lo sviluppo di moduli IoT Edge personalizzati. Per altre informazioni sul supporto degli strumenti e sui requisiti per ogni lingua, vedere [Preparare l'ambiente di sviluppo e test per IoT Edge](development-environment.md).

### <a name="linux"></a>Linux

Per tutti i linguaggi nella tabella seguente, IoT Edge supporta lo sviluppo per i dispositivi Linux AMD64 e ARM32.

| Linguaggio di sviluppo | Strumenti di sviluppo |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>Il supporto dello sviluppo e del debug per i dispositivi Linux ARM64 è in [anteprima pubblica.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Per altre informazioni, vedere [Sviluppare ed eseguire il debug di moduli IoT Edge ARM64 in Visual Studio Code (anteprima)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

### <a name="windows"></a>WINDOWS

Per tutti i linguaggi nella tabella seguente, IoT Edge supporta lo sviluppo per i dispositivi Windows AMD64.

| Linguaggio di sviluppo | Strumenti di sviluppo |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Codice di Visual Studio (nessuna funzionalità di debug)Visual Studio Code (no debugging capabilities)<br>Visual Studio 2017/2019 |

## <a name="next-steps"></a>Passaggi successivi

[Preparare l'ambiente di sviluppo e test per IoT Edge](development-environment.md)

[Usare Visual Studio per sviluppare moduli di C, per IoT Edge](how-to-visual-studio-develop-module.md)

[Usare il codice di Visual Studio per sviluppare moduli per IoT EdgeUse Visual Studio Code to develop modules for IoT Edge](how-to-vs-code-develop-module.md)

[Comprendere e usare gli SDK per l'hub IoT di Azure](../iot-hub/iot-hub-devguide-sdks.md)
