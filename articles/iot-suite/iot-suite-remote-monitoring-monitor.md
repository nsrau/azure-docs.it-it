---
title: Monitoraggio avanzato nella soluzione di monitoraggio remoto - Azure | Microsoft Docs
description: Questa esercitazione mostra come monitorare i dispositivi con il dashboard della soluzione di monitoraggio remoto.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 02/22/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 63d5d8de82d97e7f8ca65ad04cdd4357cace0be1
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="perform-advanced-monitoring-using-the-remote-monitoring-solution"></a>Eseguire il monitoraggio avanzato tramite la soluzione di monitoraggio remoto

Questa esercitazione mostra le principali funzionalità del dashboard di monitoraggio remoto. Per presentare queste funzionalità, l'esercitazione usa uno scenario nell'applicazione IoT Contoso.

In questa esercitazione si usano due dispositivi veicolo Contoso simulati per capire come monitorare i dispositivi dal dashboard degli acceleratori di soluzioni. Un operatore di Contoso deve monitorare la posizione e il comportamento dei veicoli sul campo.

In questa esercitazione si apprenderà come:

>[!div class="checklist"]
> * Filtrare i dispositivi nel dashboard
> * Visualizzare i dati di telemetria in tempo reale
> * Visualizzazione dei dettagli del dispositivo
> * Visualizzare gli avvisi dei dispositivi
> * Visualizzare gli indicatori KPI di sistema

## <a name="prerequisites"></a>prerequisiti

Per seguire questa esercitazione, è necessaria un'istanza distribuita della soluzione di monitoraggio remoto nella sottoscrizione di Azure.

Se la soluzione di monitoraggio remoto non è stata ancora distribuita, completare l'esercitazione [Distribuire l'acceleratore di soluzioni di monitoraggio remoto](iot-suite-remote-monitoring-deploy.md).

## <a name="choose-the-devices-to-display"></a>Scegliere i dispositivi da visualizzare

Per selezionare i dispositivi da visualizzare nella pagina **Dashboard**, usare i filtri. Per visualizzare solo i dispositivi **Truck** (Veicolo), scegliere il filtro predefinito **Trucks** (Veicoli) nell'elenco a discesa di filtri:

![Applicare un filtro per visualizzare solo i veicoli nel dashboard](media/iot-suite-remote-monitoring-monitor/dashboardtruckfilter.png)

Quando si applica un filtro, nella mappa della pagina **Dashboard** vengono visualizzati solo i dispositivi che soddisfano le condizioni di filtro:

![Veicoli visualizzati sulla mappa](media/iot-suite-remote-monitoring-monitor/dashboardtruckmap.png)

Il filtro determina anche quali dispositivi vengono visualizzati nel grafico **Telemetria**:

![Dati di telemetria dei veicoli visualizzati nel dashboard](media/iot-suite-remote-monitoring-monitor/dashboardtelemetry.png)

Per creare, modificare ed eliminare filtri, scegliere **Manage filters** (Gestisci filtri).

## <a name="view-real-time-telemetry"></a>Visualizzare i dati di telemetria in tempo reale

I tracciati degli acceleratori di soluzioni mostrano in tempo reale i dati di telemetria dettagliati sul grafico nella pagina **Dashboard**. Il grafico di telemetria mostra le informazioni di telemetria per i dispositivi selezionati dal filtro corrente:

![Tracciato dei dati di telemetria dei veicoli](media/iot-suite-remote-monitoring-monitor/dashboardtelemetryview.png)

Per selezionare i valori di telemetria da visualizzare, scegliere il tipo di telemetria nella parte superiore del grafico:

![Tracciato dei dati di telemetria dei veicoli](media/iot-suite-remote-monitoring-monitor/dashboardselecttelemetry.png)

<!-- 05/01 - this features appears to have been removed
To pause the live telemetry display, choose **Flowing**. To re-enable the live display, choose **Pause**:

![Pause and restart telemetry display](media/iot-suite-remote-monitoring-monitor/dashboardtelemetrypause.png)-->

## <a name="use-the-map"></a>Usare la mappa

La mappa visualizza le informazioni sui veicoli simulati selezionati dal filtro corrente. È possibile eseguire lo zoom e la panoramica sulla mappa per visualizzare le posizioni con maggiore o minore livello di dettaglio. Le icone di dispositivi sulla mappa indicano **Avvisi** attivi per il dispositivo. A sinistra della mappa sono visualizzati i valori di riepilogo per gli **Avvisi**.

<!-- 05/01 - cannot select a deice on the map
To view the device details, pan and zoom the map to locate the devices, then click the device on the map. The details include:

* Recent telemetry values
* Methods the device supports
* Device properties

![View device details on the dashboard](media/iot-suite-remote-monitoring-monitor/dashboarddevicedetail.png)-->

## <a name="view-alerts-from-your-devices"></a>Visualizzare gli avvisi dei dispositivi

La mappa mette in evidenza i dispositivi selezionati con il filtro corrente con gli **Avvisi**. Il pannello **Alerts** (Avvisi) mostra informazioni dettagliate sugli allarmi più recenti dei dispositivi:

![Visualizzare gli avvisi di sistema nel dashboard](media/iot-suite-remote-monitoring-monitor/dashboardsystemalarms.png)

È possibile usare il filtro **Dashboard** per modificare l'intervallo di tempo per gli avvisi recenti. Per impostazione predefinita, il pannello mostra gli avvisi per l'ultima ora:

![Filtrare gli avvisi in base all'ora](media/iot-suite-remote-monitoring-monitor/dashboardalarmsfilter.png)

## <a name="view-the-system-kpis"></a>Visualizzare gli indicatori KPI di sistema

La pagina **Dashboard** mostra gli indicatori KPI di sistema:

![Indicatori KPI del dashboard](media/iot-suite-remote-monitoring-monitor/dashboardkpis.png)

È possibile usare il filtro **Dashboard** per regolare l'intervallo di tempo per l'aggregazione degli indicatori KPI. Per impostazione predefinita, il pannello mostra gli indicatori KPI aggregati per l'ultima ora.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha mostrato come usare la pagina **Dashboard** per filtrare e monitorare i veicoli simulati di cui è stato effettuato il provisioning nella soluzione di monitoraggio remoto:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Filtrare i dispositivi nel dashboard
> * Visualizzare i dati di telemetria in tempo reale
> * Visualizzazione dei dettagli del dispositivo
> * Visualizzare gli avvisi dei dispositivi
> * Visualizzare gli indicatori KPI di sistema

Ora che si è appreso come monitorare i dispositivi, i passaggi successivi consigliati consentono di apprendere come:

* [Rilevare i problemi usando regole basate su soglie](./iot-suite-remote-monitoring-automate.md).
* [Gestire e configurare i dispositivi](./iot-suite-remote-monitoring-manage.md).
* [Risolvere e correggere i problemi dei dispositivi](./iot-suite-remote-monitoring-maintain.md).
* [Testare la soluzione con dispositivi simulati](iot-suite-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->