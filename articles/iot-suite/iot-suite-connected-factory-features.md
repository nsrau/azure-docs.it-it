---
title: Funzionalità della soluzione Fabbrica connessa - Azure | Microsoft Docs
description: Panoramica delle funzionalità della soluzione Factory connessa preconfigurata.
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2018
ms.author: dobett
ms.openlocfilehash: fea9ccc53bd019039cf1e989d72db7a218e4517c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2018
---
# <a name="what-is-azure-iot-suite-connected-factory"></a>Cos'è Fabbrica connessa di Azure IoT Suite?

Fabbrica connessa è un'implementazione dell'architettura di riferimento IoT industriale di Azure Microsoft, disponibile come soluzione open source. È possibile usarla come punto di partenza per un prodotto commerciale. È possibile distribuire una versione precompilata della soluzione Fabbrica connessa nella sottoscrizione di Azure da [Azure IoT Suite](https://www.azureiotsuite.com/#solutions/types/CF).

![Dashboard della soluzione Fabbrica connessa](media/iot-suite-connected-factory-features/dashboard.png)

La soluzione Fabbrica connessa include le funzionalità seguenti:

## <a name="industrial-device-interoperability"></a>Interoperabilità dei dispositivi industriali

- Connettersi alle risorse industriali con un'interfaccia OPC UA.
- Usare linee di produzione simulate (esecuzione di server OPC UA in contenitori Docker) per visualizzare in tempo reale dati di telemetria da tali linee.
- Esplorare il modello informativo OPC UA dei server OPC UA da un dashboard cloud.

## <a name="remote-management"></a>Gestione remota

- Configurare le risorse OPC UA dal dashboard cloud (chiamata di metodi, lettura e scrittura di dati).
- Pubblicare e annullare la pubblicazione di dati di telemetria dalle risorse OPC UA da un dashboard cloud.

## <a name="cloud-dashboard"></a>Dashboard cloud

- Visualizzare anteprime dei dati dei telemetria direttamente in un dashboard cloud.
- Visualizzare le tendenze nei dati di telemetria e creare correlazioni tramite il dashboard Time Series Insights Explorer.
- Visualizzare l'efficienza complessiva delle attrezzature e gli indicatori di prestazioni chiave (KPI) da un dashboard cloud.
- Visualizzare gerarchie delle risorse industriali in una topologia ad albero, nonché su una mappa interattiva.
- Visualizzare, confermare e chiudere gli avvisi da un dashboard cloud.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) è una soluzione che consente di archiviare, visualizzare ed eseguire query su grandi quantità di dati relativi a serie temporali. La soluzione Fabbrica connessa sfrutta questo servizio.
- La soluzione Fabbrica connessa si integra con questo servizio consentendo di eseguire analisi approfondite e in tempo reale dei dati dei dispositivi.

## <a name="rules-and-alerts"></a>Regole e avvisi

[Configurare regole basate su soglie per gli avvisi](iot-suite-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Sicurezza end-to-end

- Configurare le autorizzazioni di sicurezza per gli utenti usando il controllo degli accessi in base al ruolo.
- La crittografia end-to-end viene implementata usando l'autenticazione OPC UA (con certificati X.509), nonché token di sicurezza.

## <a name="customizability"></a>Personalizzabilità

- È possibile [personalizzare](iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md) la soluzione per soddisfare requisiti aziendali specifici.
- Il codice sorgente completo della soluzione è disponibile in GitHub. Vedere il repository della [soluzione preconfigurata Fabbrica connessa](https://github.com/Azure/azure-iot-connected-factory).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla soluzione preconfigurata Fabbrica connessa sono disponibili negli articoli seguenti:

* [Procedura dettagliata per la soluzione preconfigurata Fabbrica connessa](iot-suite-connected-factory-sample-walkthrough.md)
* [Distribuire un gateway per Fabbrica connessa]( iot-suite-connected-factory-gateway-deployment.md)
