---
title: Soluzioni di Azure per Internet delle cose (IoT Suite) | Documentazione Microsoft
description: Panoramica di un&quot;architettura di soluzione IoT di esempio e di come si leghi a dispositivi, al servizio hub IoT di Azure, ad Azure IoT SDK per dispositivi, ad Azure IoT SDK per servizi e ad altri servizi di Azure.
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
ms.date: 03/24/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: e95d02e706b87eaa355951a34bbaa3abf2ac2370
ms.lasthandoff: 02/16/2017


---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>Passaggi successivi

Hub IoT di Azure è un servizio che consente comunicazioni bidirezionali affidabili e sicure tra il back-end della soluzione e milioni di dispositivi. Consente al back-end della soluzione di:

* Ricevere dati di telemetria su larga scala dai dispositivi.
* Instradare i dati dai dispositivi a un processore di eventi di flusso.
* Ricevere caricamenti di file dai dispositivi.
* Inviare messaggi da cloud a dispositivo a dispositivi specifici.

È possibile usate l'hub IoT per implementare il back-end della propria soluzione. L'hub IoT include anche un registro di identità usato per effettuare il provisioning di dispositivi, credenziali di sicurezza e relativi diritti per connettersi all'hub IoT. Per altre informazioni sull'hub IoT, vedere [Informazioni sull'hub IoT][lnk-iot-hub].

Per informazioni su come l'hub IoT di Azure consente la gestione dei dispositivi basata su standard per gestire, configurare e aggiornare i dispositivi in modalità remota, vedere [Panoramica della gestione dei dispositivi con l'hub IoT][lnk-device-management].

Per implementare le applicazioni client su una vasta gamma di piattaforme hardware e sistemi operativi per dispositivi, è possibile usare Azure IoT SDK per dispositivi. Gli SDK per dispositivi includono librerie che facilitano l'invio di dati di telemetria a un hub IoT e la ricezione di messaggi da cloud a dispositivo. Quando si usano gli SDK per dispositivi, è possibile scegliere tra diversi protocolli di rete per comunicare con l'hub IoT. Per altre informazioni, vedere [Informazioni sugli SDK per dispositivi][lnk-device-sdks].

Per iniziare a scrivere codice ed eseguire alcuni esempi, vedere l'esercitazione [Introduzione all'hub IoT][lnk-getstarted].

Si potrebbe anche essere interessati a [Azure IoT Suite][lnk-iot-suite], che è una raccolta di soluzioni preconfigurate. IoT Suite consente di iniziare rapidamente e scalare progetti IoT per indirizzare scenari di IoT comuni, quali il monitoraggio remoto, la gestione degli asset e la manutenzione predittiva.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-overview.md

