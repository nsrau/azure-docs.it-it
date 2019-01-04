---
title: Sviluppare i moduli per Azure IoT Edge | Microsoft Docs
description: Sviluppare moduli personalizzati per Azure IoT Edge in grado di comunicare con il runtime e l'hub IoT
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 50e9c0667761e43c63b03fbaf5f8ce93eb49e749
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53100090"
---
# <a name="understand-the-requirements-and-tools-for-developing-iot-edge-modules"></a>Informazioni sui requisiti e gli strumenti per sviluppare moduli di IoT Edge

Questo articolo illustra quali funzionalità sono disponibili quando si scrivono applicazioni che vengono eseguite come modulo di IoT Edge e come sfruttarne i vantaggi.

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

Vedere [Sviluppare e distribuire un modulo di IoT Edge in un dispositivo simulato](tutorial-csharp-module.md) per un esempio di un'applicazione del modulo che invia i messaggi da dispositivo a cloud e usa il modulo gemello.

### <a name="device-to-cloud-messages"></a>Messaggi da dispositivo a cloud
Per abilitare la complessa elaborazione di messaggi da dispositivo a cloud, l'hub di IoT Edge offre un routing dichiarativo dei messaggi tra moduli e tra moduli e hub IoT. Il routing dichiarativo consente ai moduli di intercettare ed elaborare i messaggi inviati da altri moduli e di propagarli in pipeline complesse. L'articolo [Composizione di un modulo](module-composition.md) illustra come comporre i moduli in pipeline complesse usando le route.

Un modulo di IoT Edge, contrariamente a una normale applicazione del dispositivo dell'hub IoT, può ricevere messaggi da dispositivo a cloud che vengono trasmessi tramite proxy dall'hub di IoT Edge locale per elaborarli.

L'hub di IoT Edge propaga i messaggi al modulo in base alle route dichiarative descritte nell'articolo [Composizione di un modulo](module-composition.md). Quando si sviluppa un modulo IoT Edge, è possibile ricevere questi messaggi impostando gestori di messaggi.

Per semplificare la creazione delle route, IoT Edge aggiunge il concetto di endpoint di *input* e *output* del modulo. Un modulo può ricevere tutti i messaggi da dispositivo a cloud indirizzati ad esso senza specificare l'input e può inviare i messaggi da dispositivo a cloud senza specificare l'output.
Tuttavia,l'uso di input e output espliciti rende le regole di routing più semplici da comprendere. Per altre informazioni sulle regole di routing e gli endpoint di input e output per i moduli, vedere [Composizione di un modulo](module-composition.md).

Infine, i messaggi da dispositivo a cloud gestiti dall'hub Edge vengono contrassegnati con le proprietà di sistema seguenti:

| Proprietà | DESCRIZIONE |
| -------- | ----------- |
| $connectionDeviceId | L'ID dispositivo del client che ha inviato il messaggio |
| $connectionModuleId | L'ID modulo del modulo che ha inviato il messaggio |
| $inputName | L'input che ha ricevuto il messaggio. Può essere vuoto. |
| $outputName | L'output usato per inviare il messaggio. Può essere vuoto. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Connessione all'hub di IoT Edge da un modulo
La connessione all'hub di IoT Edge locale da un modulo prevede due passaggi: 
1. Usare la stringa di connessione fornita dal runtime IoT Edge all'avvio del modulo.
2. Verificare che l'applicazione accetti il certificato presentato dall'hub di IoT Edge su tale dispositivo.

La stringa di connessione da usare viene inserita dal runtime di IoT Edge nella variabile di ambiente `EdgeHubConnectionString`. Questo la rende disponibile per tutti i programmi che desiderano usarla.

Analogamente, il certificato da usare per convalidare la connessione dell'hub di IoT Edge viene inserito dal runtime di IoT Edge in un file il cui percorso è disponibile nella variabile di ambiente `EdgeModuleCACertificateFile`.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver sviluppato un modulo, vedere come [Distribuire e monitorare i moduli di IoT Edge su larga scala](how-to-deploy-monitor.md).

