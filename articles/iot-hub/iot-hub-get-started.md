---
title: Hub IoT Azure - Introduzione alla connessione dei dispositivi IoT nel cloud | Documentazione Microsoft
description: "Informazioni su come connettere i dispositivi IoT nell&quot;Hub IoT Azure. I dispositivi possono inviare dati di telemetria all&quot;hub IoT e l&quot;hub Iot può monitorare e gestire i dispositivi."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
keywords: esercitazione sull&quot;hub iot azure
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: aeb0b665b8295bba30d8c6c47cc88e446693c91f
ms.contentlocale: it-it
ms.lasthandoff: 05/01/2017


---
# <a name="azure-iot-hub-get-started-tutorials"></a>Esercitazioni introduttive sull'Hub IoT Azure

È possibile utilizzare Hub IoT Azure e gli SDK per dispositivi Azure IoT per creare soluzioni Internet of Things (IoT).

* Hub IoT Azure è un servizio completamente gestito nel cloud che si connette, monitora e gestisce i dispositivi IoT in modo sicuro. Usare gli SDK per dispositivi Azure IoT per implementare i dispositivi IoT.
* Usare un gateway IoT in scenari IoT più complessi in cui è necessario prendere in considerazione fattori quali i dispositivi legacy, i costi della larghezza di banda, i criteri di protezione e privacy o l'elaborazione dei dati perimetrali. In questi scenari si usa Azure IoT Gateway SDK per creare un dispositivo gateway che connette i dispositivi all'hub IoT.

## <a name="what-do-the-tutorials-cover"></a>Argomenti trattati nelle esercitazioni

Queste esercitazioni presentano l'hub IoT Azure e gli SDK per dispositivi. Le esercitazioni concernono scenari IoT comuni per illustrare le funzionalità dell'hub IoT. Le esercitazioni spiegano anche come combinare l'hub IoT con altri servizi di Azure e strumenti per creare soluzioni IoT più potenti. Nelle esercitazioni è possibile scegliere di usare dispositivi IoT simulati o reali. È anche possibile imparare a usare un gateway per consentire ai dispositivi di connettersi all'hub IoT.

## <a name="device-setup-scenario-connect-iot-device-or-gateway-to-azure-iot-hub"></a>Scenario di configurazione dei dispositivi: connettere un dispositivo IoT o un gateway all'hub IoT Azure

È possibile scegliere il dispositivo reale o simulato per iniziare.

| Dispositivo IoT                       | Linguaggio di programmazione |
|---------------------------------|----------------------|
| Raspberry Pi                    | [Node.js][Pi_Nd], [C][Pi_C]           |
| Intel Edison                    | [Node.js][Ed_Nd], [C][Ed_C]           |
| Adafruit Feather HUZZAH ESP8266 | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev      | [Arduino][Th_Ard]              |
| Adafruit Feather M0             | [Arduino][M0_Ard]              |
| Dispositivo simulato                | [.NET][Sim_NET], [Java][Sim_Jav], [Node.js][Sim_Nd], [Python][Sim_Pyth]              |

È anche possibile usare un gateway per consentire ai dispositivi di connettersi all'hub IoT.

| Dispositivo gateway               | Linguaggio di programmazione | Piattaforma         |
|------------------------------|----------------------|------------------|
| Intel NUC (modello DE3815TYKE) | C                    | [Wind River Linux][NUC_Lnx] |
| Gateway simulato            | C                    | [Linux][Sim_Lnx], [Windows][Sim_Win] |

## <a name="extended-iot-scenarios-use-other-azure-services-and-tools"></a>Scenari IoT estesi: usare altri strumenti e servizi di Azure

Dopo la connessione del dispositivo all'hub IoT, è possibile esplorare altri scenari che usano altri strumenti e servizi di Azure:

| Scenario                                    | Servizio o strumento di Azure              |
|---------------------------------------------|------------------------------------|
| [Gestire i messaggi dell'hub IoT][Mg_IoT_Hub_Msg]                    | strumento iothub-explorer               |
| [Gestire un dispositivo IoT][Mg_IoT_Dv]               | strumento iothub-explorer               |
| [Salvare i messaggi dell'hub IoT nell'archiviazione di Azure][Sv_IoT_Msg_Stor]                      | Archiviazione tabelle di Azure               |
| [Visualizzare i dati dei sensori][Vis_Data]             | Microsoft Power BI, App Web di Azure |
| [Previsioni meteorologiche con i dati dei sensori][Weather_Forecast] | Azure Machine Learning             |
| [Rilevamento e risposta automatica alle anomalie][Anomaly_Detect]    | App per la logica di Azure                   |

## <a name="next-steps"></a>Passaggi successivi

Dopo aver completato queste esercitazioni, è possibile esplorare ulteriormente le funzionalità dell'hub IoT nella [Guida per gli sviluppatori][lnk-dev-guide]. È possibile trovare ulteriori esercitazioni nella sezione [Procedure][lnk-how-to].


[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[Sim_NET]: iot-hub-csharp-csharp-getstarted.md
[Sim_Jav]: iot-hub-java-java-getstarted.md
[Sim_Nd]: iot-hub-node-node-getstarted.md
[Sim_Pyth]: iot-hub-python-getstarted.md
[NUC_Lnx]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md
[Sim_Lnx]: iot-hub-linux-gateway-sdk-get-started.md
[Sim_Win]: iot-hub-windows-gateway-sdk-get-started.md
[Mg_IoT_Hub_Msg]: iot-hub-explorer-cloud-device-messaging.md
[Mg_IoT_Dv]: iot-hub-device-management-iothub-explorer.md
[Sv_IoT_Msg_Stor]: iot-hub-store-data-in-azure-table-storage.md
[Vis_Data]: iot-hub-live-data-visualization-in-power-bi.md
[Weather_Forecast]: iot-hub-weather-forecast-machine-learning.md
[Anomaly_Detect]: iot-hub-monitoring-notifications-with-azure-logic-apps.md
[lnk-dev-guide]: iot-hub-devguide.md
[lnk-how-to]: iot-hub-how-to.md
