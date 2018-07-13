---
title: Monitorare i dispositivi IoT da una soluzione di Azure | Microsoft Docs
description: In questa esercitazione si impara a monitorare i dispositivi IoT usando l'acceleratore della soluzione di monitoraggio remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 5f42ed0fa5362959e5619f2d550ca1ae3711ed65
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097462"
---
# <a name="tutorial-monitor-your-iot-devices"></a>Esercitazione: monitorare i dispositivi IoT

In questa esercitazione si usa l'acceleratore della soluzione di monitoraggio remoto per controllare i dispositivi IoT connessi. Usare il dashboard della soluzione per visualizzare i dati di telemetria, le informazioni sul dispositivo, gli avvisi e gli indicatori KPI.

Per presentare queste funzioni di monitoraggio, l'esercitazione usa due veicoli simulati. I veicoli sono gestiti da un'organizzazione denominata Contoso e sono connessi all'acceleratore della soluzione di monitoraggio remoto. Un operatore di Contoso deve monitorare la posizione e il comportamento dei veicoli sul campo.

In questa esercitazione:

>[!div class="checklist"]
> * Filtrare i dispositivi nel dashboard
> * Visualizzare i dati di telemetria in tempo reale
> * Visualizzazione dei dettagli del dispositivo
> * Visualizzare gli avvisi dei dispositivi
> * Visualizzare gli indicatori KPI di sistema

## <a name="prerequisites"></a>Prerequisiti

Per seguire questa esercitazione, è necessaria un'istanza distribuita dell'acceleratore della soluzione di monitoraggio remoto nella sottoscrizione di Azure.

Se l'acceleratore della soluzione di monitoraggio remoto non è stato ancora distribuito, completare la guida introduttiva [Distribuire la soluzione di monitoraggio remoto basata su cloud](quickstart-remote-monitoring-deploy.md).

## <a name="choose-the-devices-to-display"></a>Scegliere i dispositivi da visualizzare

Per selezionare i dispositivi connessi da visualizzare nella pagina **Dashboard**, usare i filtri. Per visualizzare solo i dispositivi **Truck** (Veicolo), scegliere il filtro predefinito **Trucks** (Veicoli) nell'elenco a discesa di filtri:

[![Applicare un filtro per visualizzare solo i veicoli nel dashboard](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-expanded.png#lightbox)

Quando si applica un filtro, nella mappa della pagina **Dashboard** vengono visualizzati solo i dispositivi che soddisfano le condizioni di filtro:

[![Sulla mappa sono visualizzati solo i veicoli](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-expanded.png#lightbox)

Il filtro determina anche quali dispositivi vengono visualizzati nel grafico **Telemetria**:

[![I dati di telemetria dei veicoli sono visualizzati nel dashboard](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetry-expanded.png#lightbox)

Per creare, modificare ed eliminare filtri, scegliere **Gestire i gruppi di dispositivi**.

## <a name="view-real-time-telemetry"></a>Visualizzare i dati di telemetria in tempo reale

I tracciati degli acceleratori di soluzioni mostrano in tempo reale la telemetria sul grafico nella pagina **Dashboard**. La parte superiore del grafico di telemetria mostra i tipi di telemetria disponibili per i dispositivi selezionati dal filtro corrente:

[![Tipi di telemetria dei veicoli](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-expanded.png#lightbox)

Per visualizzare la telemetria sulla temperatura, fare clic su **Temperatura**:

[![Tracciato della telemetria sulla temperatura del veicolo](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-expanded.png#lightbox)

## <a name="use-the-map"></a>Usare la mappa

La mappa visualizza le informazioni sui veicoli simulati selezionati dal filtro corrente. È possibile eseguire lo zoom e la panoramica sulla mappa per visualizzare le posizioni con maggiore o minore livello di dettaglio. Il colore dell'icona di un dispositivo sulla mappa indica se sono presenti **Avvisi** attivi per il dispositivo. A sinistra della mappa sono visualizzati i valori di riepilogo per gli **Avvisi**.

Per visualizzare i dettagli per i dispositivi, usare la panoramica e lo zoom sulla mappa per individuare il dispositivo e quindi selezionare il dispositivo nella mappa. Quindi fare clic sull'etichetta del dispositivo per aprire il pannello **Dettagli dispositivo**. I dettagli dispositivo includono:

* Valori di telemetria recenti
* Metodi supportati dai dispositivi
* Proprietà dei dispositivi

[![Visualizzare i dettagli dei dispositivi nel dashboard](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-expanded.png#lightbox)

## <a name="view-alerts"></a>Visualizzare gli avvisi

Il pannello **Avvisi** mostra informazioni dettagliate sugli allarmi più recenti dei dispositivi:

[![Visualizzare gli avvisi sui dispositivi nel dashboard](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-expanded.png#lightbox)

È possibile usare un filtro per modificare l'intervallo di tempo per gli avvisi recenti. Per impostazione predefinita, il pannello mostra gli avvisi per l'ultima ora:

[![Filtrare gli avvisi in base all'ora](./media/iot-accelerators-remote-monitoring-monitor/dashboardalarmsfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardalarmsfilter-expanded.png#lightbox)

## <a name="view-the-system-kpis"></a>Visualizzare gli indicatori KPI di sistema

La pagina **Dashboard** visualizza gli indicatori KPI di sistema calcolati dall'acceleratore della soluzione nel pannello **Analytics**:

[![Indicatori KPI del dashboard](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-expanded.png#lightbox)

Il dashboard mostra tre indicatori KPI per gli avvisi selezionati dai filtri del dispositivo e dell'intervallo di tempo correnti:

* Numero di avvisi attivi per le regole che hanno attivato più avvisi.
* Percentuale di avvisi in base al tipo di dispositivo.
* Percentuale di avvisi che costituiscono avvisi critici.

Gli stessi filtri che impostano l'intervallo di tempo per gli avvisi e controllano quali dispositivi vengono visualizzati determinano come vengono aggregati gli indicatori KPI. Per impostazione predefinita, il pannello mostra gli indicatori KPI aggregati per l'ultima ora.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende passare all'esercitazione successiva, lasciare l'acceleratore della soluzione di monitoraggio remoto distribuito. Per ridurre i costi di esecuzione dell'acceleratore della soluzione mentre non è in uso, è possibile arrestare i dispositivi simulati nel pannello delle impostazioni:

[![Pause telemetry](./media/iot-accelerators-remote-monitoring-monitor/togglesimulation-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/togglesimulation-expanded.png#lightbox) (Sospendi telemetria)

Quando si è pronti per iniziare l'esercitazione successiva, è possibile riavviare i dispositivi simulati.

Se l'acceleratore della soluzione non è più necessario, eliminarlo dalla pagina [Soluzioni di cui è stato effettuato il provisioning](https://www.azureiotsolutions.com/Accelerators#dashboard):

![Eliminare la soluzione](media/iot-accelerators-remote-monitoring-monitor/deletesolution.png)

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha mostrato come usare la pagina **Dashboard** nell'acceleratore della soluzione di monitoraggio remoto per filtrare e monitorare i veicoli simulati. Per informazioni su come usare l'acceleratore della soluzione per rilevare problemi con i dispositivi collegati, passare all'esercitazione successiva.

> [!div class="nextstepaction"]
> [Rilevare i problemi con i dispositivi connessi alla soluzione di monitoraggio](iot-accelerators-remote-monitoring-automate.md)