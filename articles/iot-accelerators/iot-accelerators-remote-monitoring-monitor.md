---
title: 'Esercitazione: Monitorare i dispositivi IoT da una soluzione di Azure | Microsoft Docs'
description: In questa esercitazione si impara a monitorare i dispositivi IoT usando l'acceleratore della soluzione di monitoraggio remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: d8afe474f88f0881de5b0e7b132009c6e03ac2af
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2018
ms.locfileid: "51824745"
---
# <a name="tutorial-monitor-your-iot-devices"></a>Esercitazione: monitorare i dispositivi IoT

In questa esercitazione si usa l'acceleratore della soluzione di monitoraggio remoto per controllare i dispositivi IoT connessi. Usare il dashboard della soluzione per visualizzare i dati di telemetria, le informazioni sul dispositivo, gli avvisi e gli indicatori KPI.

L'esercitazione usa due veicoli simulati che inviano i dati di telemetria relativi a posizione, velocità e temperatura del carico. I veicoli sono gestiti da un'organizzazione denominata Contoso e sono connessi all'acceleratore della soluzione di monitoraggio remoto. Un operatore di Contoso deve monitorare la posizione e il comportamento di uno dei veicoli (truck-02) sul campo.

In questa esercitazione:

>[!div class="checklist"]
> * Filtrare i dispositivi nel dashboard
> * Visualizzare i dati di telemetria in tempo reale
> * Visualizzazione dei dettagli del dispositivo
> * Visualizzare gli avvisi dei dispositivi
> * Visualizzare gli indicatori KPI di sistema

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>Scegliere i dispositivi da visualizzare

Per selezionare i dispositivi connessi da visualizzare nella pagina **Dashboard**, usare i filtri. Per visualizzare solo i dispositivi **Truck** (Veicolo), scegliere il filtro predefinito **Trucks** (Veicoli) nell'elenco a discesa di filtri:

[![Applicare un filtro per visualizzare solo i veicoli nel dashboard](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-expanded.png#lightbox)

Quando si applica un filtro, nella mappa e nel pannello di telemetria vengono visualizzati solo i dispositivi che soddisfano le condizioni di filtro. Come si può vedere, ci sono due veicoli collegati all'acceleratore di soluzioni, tra cui truck-02:

[![Sulla mappa sono visualizzati solo i veicoli](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-expanded.png#lightbox)

Per creare, modificare ed eliminare filtri, fare clic su **Gestire i gruppi di dispositivi**.

## <a name="view-real-time-telemetry"></a>Visualizzare i dati di telemetria in tempo reale

I tracciati degli acceleratori di soluzioni mostrano in tempo reale la telemetria sul grafico nella pagina **Dashboard**. La parte superiore del grafico di telemetria mostra i tipi di telemetria disponibili per i dispositivi, incluso truck-02, selezionati dal filtro corrente. Per impostazione predefinita, il grafico mostra la latitudine dei veicoli e truck-02 risulta fermo:

[![Tipi di telemetria dei veicoli](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-expanded.png#lightbox)

Per visualizzare i dati di telemetria sulla temperatura dei veicoli, fare clic su **Temperatura**. È possibile vedere che la temperatura di truck-02 è variata nell'ultima ora:

[![Tracciato della telemetria sulla temperatura del veicolo](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-expanded.png#lightbox)

## <a name="view-the-map"></a>Visualizzare la mappa

La mappa visualizza le informazioni sui veicoli simulati selezionati dal filtro corrente. È possibile eseguire lo zoom e la panoramica sulla mappa per visualizzare le posizioni con maggiore o minore livello di dettaglio. Il colore dell'icona di un dispositivo sulla mappa indica se sono attivi **Avvisi di colore blu scuro** o **Avvisi di colore rosso** per il dispositivo. A sinistra della mappa sono visualizzati i valori di riepilogo per gli **Avvisi**.

Per visualizzare i dettagli per truck-02, usare la panoramica e lo zoom sulla mappa per individuare il veicolo e quindi selezionarlo nella mappa. Quindi fare clic sull'etichetta del dispositivo per aprire il pannello **Dettagli dispositivo**. I dettagli dispositivo includono:

* Valori di telemetria recenti
* Metodi supportati dai dispositivi
* Proprietà dei dispositivi

[![Visualizzare i dettagli dei dispositivi nel dashboard](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-expanded.png#lightbox)

## <a name="view-alerts"></a>Visualizzare gli avvisi

Il pannello **Avvisi** mostra informazioni dettagliate sugli avvisi più recenti dei dispositivi. Gli avvisi relativi a truck-02 indicano una temperatura del carico maggiore del normale:

[![Visualizzare gli avvisi sui dispositivi nel dashboard](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-expanded.png#lightbox)

È possibile usare un filtro per modificare l'intervallo di tempo per gli avvisi recenti. Per impostazione predefinita, il pannello mostra gli avvisi per l'ultima ora.

## <a name="view-the-system-kpis"></a>Visualizzare gli indicatori KPI di sistema

La pagina **Dashboard** visualizza gli indicatori KPI di sistema calcolati dall'acceleratore della soluzione nel pannello **Analytics**:

[![Indicatori KPI del dashboard](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-expanded.png#lightbox)

Il dashboard mostra tre indicatori KPI per gli avvisi selezionati dai filtri del dispositivo e dell'intervallo di tempo correnti:

* Numero di avvisi attivi per le regole che hanno attivato più avvisi.
* Percentuale di avvisi in base al tipo di dispositivo.
* Percentuale di avvisi che costituiscono avvisi critici.

Gli avvisi relativi a truck-02 sono tutti relativi a una temperatura del carico maggiore del normale.

Gli stessi filtri che impostano l'intervallo di tempo per gli avvisi e controllano quali dispositivi vengono visualizzati determinano come vengono aggregati gli indicatori KPI. Per impostazione predefinita, il pannello mostra gli indicatori KPI aggregati per l'ultima ora.

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha mostrato come usare la pagina **Dashboard** nell'acceleratore della soluzione di monitoraggio remoto per filtrare e monitorare i veicoli simulati. Per informazioni su come usare l'acceleratore della soluzione per rilevare problemi con i dispositivi collegati, passare all'esercitazione successiva.

> [!div class="nextstepaction"]
> [Rilevare i problemi con i dispositivi connessi alla soluzione di monitoraggio](iot-accelerators-remote-monitoring-automate.md)