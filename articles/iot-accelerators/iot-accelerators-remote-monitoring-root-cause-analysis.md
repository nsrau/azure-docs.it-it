---
title: Eseguire un'analisi della causa radice di un avviso - Azure | Microsoft Docs
description: In questa esercitazione viene descritto come eseguire un'analisi della causa radice di un avviso usando Azure Time Series Insights.
author: aditidugar
ms.author: adugar
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 70d29359d4a4bcf9f5badbbf0c553d7bed88a02b
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284564"
---
# <a name="tutorial-conduct-a-root-cause-analysis-on-an-alert"></a>Esercitazione: Eseguire un'analisi della causa radice di un avviso

In questa esercitazione si apprenderà come usare l'acceleratore di soluzione Monitoraggio remoto per diagnosticare la causa radice di un avviso. Si vedrà che è stato attivato un avviso nel dashboard della soluzione Monitoraggio remoto e quindi si userà Azure Time Series Insights Explorer per ricercare la causa radice.

L'esercitazione usa due veicoli simulati che inviano i dati di telemetria relativi a posizione, altitudine, velocità e temperatura del carico. I veicoli sono gestiti da un'organizzazione denominata Contoso e sono connessi all'acceleratore della soluzione di monitoraggio remoto. In quanto operatore di Contoso, è necessario capire il motivo per cui uno dei veicoli (delivery-truck-02) ha registrato un avviso di temperatura bassa.

In questa esercitazione:

>[!div class="checklist"]
> * Filtrare i dispositivi nel dashboard
> * Visualizzare i dati di telemetria in tempo reale
> * Esplorare i dati in Time Series Insights Explorer
> * Eseguire un'analisi della causa radice
> * Creare una nuova regola in base alle informazioni apprese

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>Scegliere i dispositivi da visualizzare

Per selezionare i dispositivi connessi da visualizzare nella pagina **Dashboard**, usare i filtri. Per visualizzare solo i dispositivi **Truck** (Veicolo), scegliere il filtro predefinito **Trucks** (Veicoli) nell'elenco a discesa di filtri:

[![Applicare un filtro per visualizzare solo i veicoli nel dashboard](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-expanded.png#lightbox)

Quando si applica un filtro, nella mappa e nel pannello di telemetria in **Dashboard** vengono visualizzati solo i dispositivi che soddisfano le condizioni di filtro. Come si può vedere, ci sono due veicoli collegati all'acceleratore di soluzione, tra cui **truck-02**.

## <a name="view-real-time-telemetry"></a>Visualizzare i dati di telemetria in tempo reale

I tracciati degli acceleratori di soluzioni mostrano in tempo reale la telemetria sul grafico nella pagina **Dashboard**. Per impostazione predefinita, il grafico mostra i dati di telemetria per l'altitudine, che varia nel tempo:

[![Tracciato dei dati di telemetria dell'altitudine dei veicoli](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-expanded.png#lightbox)

Per visualizzare i dati di telemetria sulla temperatura dei veicoli, fare clic su **Temperature** (Temperatura) nel **pannello Telemetria**. È possibile vedere che la temperatura per entrambi i veicoli è variata negli ultimi 15 minuti. È anche possibile vedere che è stato attivato un avviso per temperatura bassa per delivery-truck-02 nel riquadro degli avvisi.

[![Dashboard di Monitoraggio remoto con avviso di temperatura bassa](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-expanded.png#lightbox)

## <a name="explore-the-data"></a>Esplorare i dati

Per identificare la causa dell'allarme relativo alla temperatura bassa, aprire i dati di telemetria del veicolo in Time Series Insights Explorer. Fare clic su uno qualsiasi dei collegamenti **Explore in Time Series Insights** (Esplora in Time Series Insights) nel dashboard:

[![Dashboard di Monitoraggio remoto con i collegamenti a Time Series Insights evidenziati](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-expanded.png#lightbox)

All'avvio di Explorer saranno elencati tutti i dispositivi:

[![Visualizzazione iniziale di Time Series Insights Explorer](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-expanded.png#lightbox)

Filtrare i dispositivi digitando **delivery-truck** nella casella di filtro e selezionare **temperature** (temperatura) come **Misura** nel pannello a sinistra:

[![Temperatura del veicolo in Time Series Insights Explorer](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-expanded.png#lightbox)

Viene visualizzata una vista analoga a quella del dashboard di monitoraggio remoto. È ora possibile anche fare zoom avanti per esaminare l'intervallo di tempo in cui è stato attivato l'avviso:

[![Zoom di Time Series Insights Explorer](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-expanded.png#lightbox)

È anche possibile aggiungere altri flussi di dati di telemetria provenienti dai veicoli. Fare clic sul pulsante **Aggiungi** nell'angolo superiore sinistro. Viene visualizzato un nuovo riquadro:

[![Time Series Insights Explorer con il nuovo riquadro](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-expanded.png#lightbox)

Nel nuovo riquadro modificare il nome della nuova etichetta impostando **Devices** (Dispositivi) in modo che corrisponda a quella precedente. Selezionare **altitude** (altitudine) come **Misura** e **iothub-connection-device-id** come valore **Dividi per** per aggiungere i dati di telemetria relativi all'altitudine nella vista:

[![Time Series Insights Explorer con temperatura e altitudine](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-expanded.png#lightbox)

## <a name="diagnose-the-alert"></a>Diagnosticare l'avviso

Quando si esaminano i flussi nella vista corrente, è possibile notare che i profili di altitudine per i due veicoli sono diversi. Inoltre, il calo di temperatura in **delivery-truck-02** si verifica quando il veicolo raggiunge un'altitudine elevata. Si è sorpresi della scoperta, perché era previsto che i veicoli seguissero lo stesso percorso.

Per confermare il sospetto che i veicoli abbiano preso percorsi diversi, aggiungere un altro riquadro al pannello laterale usando il pulsante **Add** (Aggiungi). Nel nuovo riquadro modificare il nome della nuova etichetta impostando **Devices** (Dispositivi) in modo che corrisponda a quella precedente. Selezionare **longitude** (longitudine) come **Measure** (Misura) e **iothub-connection-device-id** come valore **Split By** (Diviso per) da aggiungere ai dati di telemetria sulla longitudine nella visualizzazione. È possibile verificare che i veicoli hanno preso percorsi diversi esaminando la differenza tra i flussi della **longitudine**:

[![Time Series Insights Explorer con temperatura, altitudine e longitudine](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-expanded.png#lightbox)

## <a name="create-a-new-rule"></a>Creare una nuova regola

Anche se i percorsi per i veicoli sono in genere ottimizzati in anticipo, ci si rende conto che il traffico, le condizioni meteo e altri eventi imprevedibili possono causare ritardi e quindi si lasciano le decisioni dell'ultimo minuto agli autisti in base al loro giudizio. Tuttavia, dato che la temperatura delle merci all'interno del veicolo è cruciale, è necessario creare una regola aggiuntiva nella soluzione di monitoraggio remoto. Questa regola serve per assicurarsi di ricevere un avviso se l'altitudine media per un intervallo di 1 minuto supera i 350 piedi:

[![Impostazione di una regola per l'altitudine nella scheda delle regole di monitoraggio](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-expanded.png#lightbox)

Per informazioni su come creare e modificare le regole, vedere l'esercitazione precedente sul [rilevamento dei problemi del dispositivo](iot-accelerators-remote-monitoring-automate.md).

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è visto come usare Time Series Insights Explorer con l'acceleratore di soluzione Monitoraggio remoto per diagnosticare la causa radice di un avviso. Per informazioni su come usare l'acceleratore della soluzione per individuare e correggere i problemi con i dispositivi collegati, passare all'esercitazione successiva.

> [!div class="nextstepaction"]
> [Usare gli avvisi dei dispositivi per individuare e correggere i problemi con i dispositivi connessi alla soluzione di monitoraggio](iot-accelerators-remote-monitoring-maintain.md)
