---
title: IoT Edge e la cronologia di esplorazione della versione Azure IoT Edge
description: Scopri le novità di IoT Edge con informazioni sulle nuove funzionalità e funzionalità nelle versioni più recenti.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c5d66355cd68aaaa5f80e938bcacb1b59d12eb70
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94447675"
---
# <a name="azure-iot-edge-versions-and-release-notes"></a>Versioni di Azure IoT Edge e note sulla versione

Azure IoT Edge è un prodotto creato dal progetto open source IoT Edge ospitato su GitHub. Tutte le nuove versioni vengono rese disponibili nel [progetto Azure IOT Edge](https://github.com/Azure/azure-iotedge). I contributi e i report sui bug possono essere effettuati nel [progetto open source IOT Edge](https://github.com/Azure/iotedge).

## <a name="documented-versions"></a>Versioni documentate

La documentazione di IoT Edge in questo sito è disponibile per due versioni diverse del prodotto, in modo che sia possibile scegliere il contenuto che si applica all'ambiente IoT Edge. Attualmente, le due versioni supportate sono:

* **IOT Edge 1.0.10** copre tutte le funzionalità e le funzionalità della versione più recente disponibile a livello generale: [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10).
* **IoT Edge 1,2 (anteprima)** contiene contenuti aggiuntivi per le funzionalità e le funzionalità disponibili nella versione di anteprima più recente: [1,2-RC1](https://github.com/Azure/azure-iotedge/releases/tag/1.2-rc1)
  * Mentre IoT Edge 1,2 è in anteprima, è necessario installare le versioni di release candidate. Per ulteriori informazioni, vedere [installazione di versioni offline o specifiche](how-to-install-iot-edge.md?tabs=linux#offline-or-specific-version-installation).

## <a name="version-history"></a>Cronologia delle versioni

Questa tabella fornisce la cronologia delle versioni recenti per IoT Edge versioni dei pacchetti ed evidenzia gli aggiornamenti della documentazione effettuati per ogni versione.

| Note sulla versione e asset | Tipo | Date | In evidenza |
| ------------------------ | ---- | ---- | ---------- |
| [1,2-RC1](https://github.com/Azure/azure-iotedge/releases/tag/1.2-rc1) | Anteprima | Novembre 2020 | [Dispositivi IoT Edge dietro i gateway](how-to-connect-downstream-iot-edge-device.md?view=iotedge-2020-11&preserve-view=true)<br>[Broker IoT Edge MQTT](how-to-publish-subscribe.md?view=iotedge-2020-11&preserve-view=true) |
| [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10) | Stable | Ottobre 2020 | [Metodo diretto UploadSupportBundle](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics)<br>[Caricare le metriche di runtime](how-to-access-built-in-metrics.md)<br>[Priorità di route e durata (TTL)](module-composition.md#priority-and-time-to-live)<br>[Ordine di avvio del modulo](module-composition.md#configure-modules)<br>[Provisioning manuale X. 509](how-to-manual-provision-x509.md) |
| [1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) | Stable | Marzo 2020 | [Provisioning automatico X. 509 con DPS](how-to-auto-provision-x509-certs.md)<br>[Metodo diretto RestartModule](how-to-edgeagent-direct-method.md#restart-module)<br>[comando support-bundle](troubleshoot.md#gather-debug-information-with-support-bundle-command) |

## <a name="next-steps"></a>Passaggi successivi

* [Visualizza tutte le versioni di Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)
* [Eseguire o rivedere le richieste di funzionalità nel forum dei commenti](https://feedback.azure.com/forums/907045-azure-iot-edge)