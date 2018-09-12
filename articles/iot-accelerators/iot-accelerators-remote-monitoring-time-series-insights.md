---
title: Visualizzare i dati di telemetria di monitoraggio remoto usando Azure Time Series Insights | Microsoft Docs
description: Informazioni su come configurare l'ambiente di Time Series Insights per esplorare e analizzare i dati di telemetria generati dall'acceleratore della soluzione di monitoraggio remoto.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 02b3afbc62be7a7a9c396de888378d762405248a
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382865"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-remote-monitoring-solution-accelerator"></a>Usare Time Series Insights per visualizzare i dati di telemetria inviati dall'acceleratore di soluzioni di monitoraggio remoto

Come operatore è possibile voler estendere le funzionalità di visualizzazione dei dati dell'acceleratore di soluzioni di monitoraggio remoto. Questa guida pratica illustra come usare un ambiente Time Series Insights per visualizzare e analizzare dati di telemetria inviati all'acceleratore di soluzioni di monitoraggio remoto.

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura descritta in questa guida pratica, è necessaria una sottoscrizione di Azure attiva. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

I passaggi descritti in questa guida presuppongono di aver distribuito l'acceleratore di soluzioni di monitoraggio remoto alla sottoscrizione di Azure. Se non è stato distribuito l'acceleratore di soluzioni, seguire i passaggi nella guida introduttiva [Distribuire ed eseguire una soluzione di monitoraggio remoto basata sul cloud](quickstart-remote-monitoring-deploy.md).

Questo articolo presuppone che l'acceleratore della soluzione sia denominato **contoso-simulation**. Sostituire **contoso-simulation** con il nome dell'acceleratore della soluzione durante il completamento dei passaggi seguenti.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-accelerators-create-tsi.md](../../includes/iot-accelerators-create-tsi.md)]

## <a name="time-series-insights-explorer"></a>Strumento di esplorazione di Time Series Insights

Lo strumento di esplorazione di Azure Time Series Insights è un'app Web che si può usare per visualizzare i dati di telemetria.

1. Nel portale di Azure selezionare la scheda **Panoramica** di Time Series Insights.

1. Fare clic su **Vai all'ambiente** per visualizzare l'app Web dello strumento di esplorazione di Time Series Insights:

    ![Strumento di esplorazione di Time Series Insights](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-environment.png)

1. Nel riquadro di selezione oraria scegliere **Ultimi 30 minuti** dal menu dei tempi rapidi e fare clic su **Cerca**:

    ![Funzione Cerca dello strumento di esplorazione di Time Series Insights](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-search-time.png)

1. Nel riquadro delle condizioni a sinistra selezionare **temperature** sotto **Misura** e **iothub-connection-device-id** come valore per **Dividi per**:

    ![Query dello strumento di esplorazione di Time Series Insights](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-query1.png)

1. Fare clic con il pulsante destro del mouse sul grafico e selezionare **Esplora eventi**:

    ![Eventi dello strumento di esplorazione di Time Series Insights](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-explore-events.png)

1. I dati dell'evento sono riportati in una griglia:

    ![Tabella dello strumento di esplorazione di Time Series Insights](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-table.png)

1. Fare clic sul pulsante Visualizzazione prospettiva:

    ![Prospettiva dello strumento di esplorazione di Time Series Insights](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-explorer-perspective.png)

1. Fare clic su **+** per aggiungere una nuova query alla prospettiva:

    ![Aggiunta di query dello strumento di esplorazione di Time Series Insights](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-new-query.png)

1. Selezionare **Ultimi 30 minuti** come intervallo di tempo, **Umidità** come **Misura** e **iothub-connection-device-id** come valore di **Dividi per**:

    ![Query dello strumento di esplorazione di Time Series Insights](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-query2.png)

1. Fare clic sul pulsante Visualizzazione prospettiva per visualizzare il dashboard dei dati di telemetria del dispositivo:

    ![Dashboard dello strumento di esplorazione di Time Series Insights](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-dashboard.png)

[!INCLUDE [iot-accelerators-cleanup-tsi.md](../../includes/iot-accelerators-cleanup-tsi.md)]

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come esplorare ed eseguire query sui dati nello strumento di esplorazione di Azure Time Series Insights, vedere [Strumento di esplorazione di Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).
