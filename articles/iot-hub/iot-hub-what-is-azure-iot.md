---
title: Soluzioni di Azure per l&quot;Internet delle cose | Documentazione Microsoft
description: "Una panoramica di IoT in Azure, inclusa un&quot;architettura della soluzione di esempio e la sua relazione con l’hub IoT di Azure, gli SDK  per dispositivi e le soluzioni preconfigurate"
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
ms.date: 10/05/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2dbf639abfa505eb329769bcc346efb5f1db443e


---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>Passaggi successivi
Hub IoT di Azure è un servizio che consente comunicazioni bidirezionali affidabili e sicure tra il back-end dell'applicazione e milioni di dispositivi. Consente al back-end dell'applicazione di:

* Ricevere dati di telemetria su larga scala dai dispositivi.
* Instradare i dati dai dispositivi a un processore di eventi di flusso.
* Ricevere caricamenti di file dai dispositivi.
* Inviare comandi da cloud a dispositivo a dispositivi specifici.

È possibile usate l'hub IoT per implementare il back-end della propria soluzione. L'hub IoT include anche un registro delle identità dei dispositivi che è possibile usare per effettuare il provisioning dei dispositivi, le credenziali di sicurezza e i relativi diritti per connettersi all'hub. Per altre informazioni sull'hub IoT, vedere [Informazioni sull'hub IoT][lnk-iot-hub].

Per sapere come l'hub IoT di Azure consente la gestione dei dispositivi basata su standard per gestire, configurare e aggiornare i dispositivi in modalità remota, vedere [Panoramica di Gestione dei dispositivi dell'hub IoT di Azure][lnk-device-management].

Per implementare le applicazioni client su una vasta gamma di piattaforme hardware e sistemi operativi per dispositivi, è possibile usare gli SDK per dispositivi IoT. GLI SDK per dispositivi IoT includono librerie che facilitano l'invio di dati di telemetria a un hub IoT e la ricezione di comandi da cloud a dispositivo. Quando si usano gli SDK, è possibile scegliere tra diversi protocolli di rete per comunicare con l'hub IoT. Per altre informazioni, vedere la pagina relativa agli [SDK per dispositivi][lnk-device-sdks].

Per iniziare a scrivere una parte di codice ed eseguire alcuni esempi, vedere l'esercitazione [Introduzione all'hub IoT di Azure][lnk-getstarted].

Si potrebbe anche essere interessati a [Azure IoT Suite][lnk-iot-suite], che è una raccolta di soluzioni preconfigurate. IoT Suite consente di iniziare rapidamente e scalare progetti IoT per indirizzare scenari di IoT comuni, quali il monitoraggio remoto, la gestione degli asset e la manutenzione predittiva.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-overview.md



<!--HONumber=Nov16_HO2-->


