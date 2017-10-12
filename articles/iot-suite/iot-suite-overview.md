---
title: Panoramica di Microsoft Azure IoT Suite | Documentazione Microsoft
description: Panoramica di come Azure IoT Suite offra soluzioni preconfigurate Internet delle cose per raccogliere, analizzare e archiviare dati, fornire visualizzazioni ed eseguire l'integrazione con altri sistemi.
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 2d38d08a-4133-4e5c-8b28-f93cadb5df05
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bfa8dbbd0b1d943a9eb7a042df0bac25189d9ac9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-azure-iot-suite"></a>Panoramica di Azure IoT Suite

I servizi di Azure IoT (Internet delle cose) offrono una vasta gamma di funzionalità. Questi servizi di livello aziendale consentono di:

* Raccogliere dati dai dispositivi
* Analizzare i flussi dei dati in movimento
* Archiviare ed eseguire query su set di dati di grandi dimensioni
* Visualizzare i dati in tempo reale e cronologici
* Eseguire l'integrazione con i sistemi back-office
* Gestire i dispositivi

Per offrire queste funzionalità, Azure IoT Suite include più servizi di Azure con estensioni personalizzate come *soluzioni preconfigurate*. Queste soluzioni preconfigurate sono implementazioni di base dei modelli di soluzione IoT comuni che contribuiscono a ridurre i tempi di distribuzione delle soluzioni IoT. I [Software Development Kit IoT][lnk-sdks] consentono di personalizzare ed estendere queste soluzioni per soddisfare le proprie esigenze. È anche possibile usare queste soluzioni come esempi o modelli durante lo sviluppo di nuove soluzioni IoT.

Nel video seguente viene fornita un'introduzione ad Azure IoT Suite:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON309/player]
> 
> 

## <a name="azure-iot-services-in-azure-iot-suite"></a>Servizi di Azure IoT in Azure IoT Suite
Le soluzioni preconfigurate usano in genere i servizi seguenti:

* Il servizio principale di Azure IoT Suite è [Hub IoT di Azure][lnk-iot-hub]. Questo servizio fornisce funzionalità di messaggistica da dispositivo a cloud e da cloud a dispositivo e funge da gateway per il cloud e gli altri servizi chiave di IoT Suite. Il servizio consente di ricevere messaggi dai dispositivi su vasta scala e inviare i comandi ai dispositivi. Il servizio consente inoltre di [gestire i dispositivi][lnk-device-management]. È possibile, ad esempio, configurare, riavviare o eseguire un ripristino delle impostazioni predefinite per uno o più dispositivi connessi all'hub.
* [Analisi di flusso di Azure][lnk-asa] offre l'analisi dei dati in movimento. IoT Suite usa questo servizio per elaborare i dati di telemetria in entrata, eseguire operazioni di aggregazione e rilevare gli eventi. Le soluzioni preconfigurate usando anche l'analisi di flusso per elaborare i messaggi informativi che contengono dati, ad esempio i metadati o le risposte ai comandi dai dispositivi. Le soluzioni usano Analisi di flusso per elaborare i messaggi dei dispositivi e inviare i messaggi ad altri servizi.
* [Archiviazione di Azure][lnk-azure-storage] e [Azure Cosmos DB][lnk-document-db] offrono le funzionalità di archiviazione dati. Le soluzioni preconfigurate usano l'archivio BLOB per archiviare i dati di telemetria e renderli disponibili per l'analisi. Le soluzioni usano Cosmos DB per archiviare i metadati dei dispositivi e abilitare le funzionalità di gestione dispositivi delle soluzioni.
* [App Web di Azure][lnk-web-apps] e [Microsoft Power BI][lnk-power-bi] offrono le funzionalità di visualizzazione dei dati. La flessibilità di Power BI consente di creare rapidamente i propri dashboard interattivi che usano i dati di IoT Suite.

Per una panoramica dell'architettura di una tipica soluzione IoT, vedere [Microsoft Azure e Internet of Things (IoT)][iot-suite-what-is-azure-iot].

## <a name="preconfigured-solutions"></a>soluzioni preconfigurate

IoT Suite include soluzioni preconfigurate che consentono di iniziare rapidamente e di esplorare gli scenari comuni di IoT, ad esempio:

* Monitoraggio remoto
* Manutenzione predittiva
* Connected factory

È possibile distribuire queste soluzioni nella sottoscrizione di Azure e quindi eseguire uno scenario IoT end-to-end completo.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver acquisito una panoramica di IoT Suite e dei suoi componenti, è possibile ottenere maggiori informazioni sulle soluzioni preconfigurate in IoT Suite. Per altre informazioni vedere [Informazioni sulle soluzioni preconfigurate di Azure IoT Suite][lnk-what-are-preconfig]

[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md
[lnk-device-management]: ../iot-hub/iot-hub-device-management-overview.md
