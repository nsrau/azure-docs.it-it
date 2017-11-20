---
title: Soluzioni di Azure per Internet delle cose (IoT Edge) | Microsoft Docs
description: Panoramica di un'architettura di soluzione IoT di esempio e di come si leghi a dispositivi, al servizio hub IoT di Azure, ad Azure IoT SDK per dispositivi, ad Azure IoT SDK per servizi e ad altri servizi di Azure.
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a859e379-dca7-42fa-bdf6-1125c86ad140
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: dobett
ms.openlocfilehash: 587b733106d511ec63d71f67a06e520324a3e594
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>Passaggi successivi

Azure IoT Edge è un servizio di Azure che abilita l'analisi e l'elaborazione dei dati nella rete perimetrale. Con IoT Edge, è possibile migliorare i dispositivi con il codice basato su contenitori che include la logica di cui viene eseguito il pull direttamente dai servizi di Azure già in uso o il codice specifico della soluzione. Consente ai dispositivi di:

* Fungere da dispositivi gateway, aggregando ed elaborando i dati da più dispositivi foglia.
* Eseguire il rilevamento anomalie e reagire alle modifiche apportate nell'ambiente senza dover attendere istruzioni dal cloud.
* Ridurre al minimo il costo per larghezza di banda e archiviazione pre-elaborando i dati e inviando i risultati. 

IoT Edge include anche un'interfaccia cloud che consente la gestione remota dei dispositivi. Senza dover accedere fisicamente ai dispositivi, è possibile distribuire il codice, monitorare lo stato e aggiornarli. È possibile gestire in remoto i singoli dispositivi o creare distribuzioni che interessano set completi di dispositivi definiti. Per altre informazioni, vedere [Understand IoT Edge deployments for single devices or at scale (Informazioni sulle distribuzioni IoT Edge per singoli dispositivi o su vasta scala)][lnk-deployment].

Per informazioni su componenti che abilitano IoT Edge, vedere [Come funziona Azure IoT Edge][lnk-overview].

Se non si è usato prima Hub IoT di Azure, è possibile iniziare con una [panoramica del servizio hub IoT di Azure][lnk-iot-hub].

[lnk-deployment]: module-deployment-monitoring.md
[lnk-overview]: how-iot-edge-works.md
[lnk-iot-hub]: ../iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: ../iot-hub/iot-hub-device-management-overview.md
