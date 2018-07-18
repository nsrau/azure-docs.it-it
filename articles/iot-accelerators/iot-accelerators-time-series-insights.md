---
title: Visualizzare i dati della soluzione di monitoraggio remoto usando Azure Time Series Insights | Microsoft Docs
description: Informazioni su come configurare un ambiente di Azure Time Series Insights per esplorare e analizzare i dati delle serie temporali delle soluzioni di monitoraggio remoto.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: f16fdfca704b8f8cb175de637ad7f3ef143d3ed7
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968956"
---
# <a name="visualize-remote-monitoring-data-with-time-series-insights"></a>Visualizzare i dati della soluzione di monitoraggio remoto usando Azure Time Series Insights

Un operatore può dover estendere l'output della visualizzazione dei dati nella casella fornita dalla soluzione preconfigurata di monitoraggio dei dati. Il nostro acceleratore di soluzioni offre l'integrazione predefinita con STI. Questa procedura illustra come configurare Azure Time Series Insights per analizzare i dati di telemetria del dispositivo e rilevare anomalie.

## <a name="prerequisites"></a>prerequisiti

Per completare questa procedura, è necessario quanto segue:

* [Distribuire la soluzione preconfigurata di monitoraggio remoto](iot-accelerators-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Creare un gruppo di consumer

È necessario creare un gruppo di consumer dedicato nell'hub IoT, che verrà usato per i dati di streaming in Azure Time Series Insights.

> [!NOTE]
> I gruppi di consumer vengono usati dalle applicazioni per eseguire il pull dei dati dall'hub IoT di Azure. In ogni gruppo di consumer sono consentiti un massimo di cinque consumer di output. È consigliabile creare un nuovo gruppo di consumer ogni cinque sink di output; è possibile creare un massimo di 32 gruppi di consumer.

1. Nel portale di Azure, fare clic sul pulsante Cloud Shell.

1. Eseguire il comando seguente per creare un nuovo gruppo di consumer:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="create-a-new-time-series-insights-environment"></a>Creare un nuovo ambiente Azure Time Series Insights

Azure Time Series Insights è un servizio di analisi, archiviazione e visualizzazione completamente gestito per la gestione di dati delle serie temporali su scala IoT sul cloud. Fornisce l'archiviazione dei dati relativi alle serie temporali con scalabilità elevata e permette di esplorare e analizzare miliardi di eventi in streaming da tutto il mondo in pochi secondi. Azure Time Series Insights consente di archiviare e gestire terabyte di dati relativi alle serie temporali, esplorare e visualizzare miliardi di eventi contemporaneamente, eseguire analisi delle cause radice e confrontare più siti e asset.

1. Accedere al [portale di Azure](http://portal.azure.com/).

1. Selezionare **Crea una risorsa** > **Internet delle cose** > **Time Series Insights**.

    ![Nuovo ambiente Azure Time Series Insights](./media/iot-accelerators-time-series-insights/new-time-series-insights.png)

1. Per creare l'ambiente Azure Time Series Insights, usare i valori nella tabella seguente:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome ambiente | Nella schermata seguente viene usato il nome **contorosrmtsi**. Quando si completa questo passaggio, scegliere il proprio nome univoco. |
    | Sottoscrizione | Selezionare la sottoscrizione di Azure nell'elenco a discesa. |
    | Gruppo di risorse | **Creare un nuovo gruppo di risorse**. Nell'esempio viene usato il nome **ContosoRM**. |
    | Località | In questo esempio viene usata l'area **Stati Uniti orientali**. Creare l'ambiente nella stessa area della soluzione di monitoraggio remoto. |
    | Sku |**S1** |
    | Capacity | **1** |
    | Aggiungi al dashboard | **Sì** |

    ![Creare Azure Time Series Insights](./media/iot-accelerators-time-series-insights/new-time-series-insights-create.png)

1. Fare clic su **Crea**. La creazione dell'ambiente può richiedere qualche minuto.

## <a name="create-event-source"></a>Creare un'origine evento

Creare una nuova origine evento per la connessione all'hub IoT. Verificare di usare il gruppo di consumer creato nei passaggi precedenti. Azure Time Series Insights richiede che ogni servizio disponga di un gruppo consumer dedicato, non usato da un altro servizio.

1. Aprire il nuovo ambiente Azure Time Series Insights.

1. A sinistra, selezionare **Origine evento**.

    ![Visualizzare le origini evento](./media/iot-accelerators-time-series-insights/time-series-insights-event-sources.png)

1. Fare clic su **Aggiungi**.

    ![Aggiungere un'origine evento](./media/iot-accelerators-time-series-insights/time-series-insights-event-sources-add.png)

1. Per configurare l'hub IoT come nuova origine evento, usare i valori nella tabella seguente:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome Origine evento | Nella schermata seguente viene usato il nome **contosorm-iot-hub**. Quando si completa questo passaggio, usare il proprio nome univoco. |
    | Sorgente | **Hub IoT** |
    | Opzione di importazione | **Usare un hub IoT delle sottoscrizioni disponibili** |
    | ID sottoscrizione | Selezionare la sottoscrizione di Azure nell'elenco a discesa. |
    | Nome dell'hub IoT | **contosorma57a6**. Usare il nome dell'hub IoT della soluzione di monitoraggio remoto. |
    | Nome dei criteri dell'hub IoT | **iothubowner** Verificare che il criterio usato sia un criterio di proprietario. |
    | Chiave dei criteri dell'hub IoT | Il campo viene popolato automaticamente. |
    | Gruppo di consumer dell'hub IoT | **timeseriesinsights** |
    | Formato di serializzazione eventi | **JSON**     | Nome della proprietà Timestamp | Lasciare vuoto |

    ![Creare un'origine evento](./media/iot-accelerators-time-series-insights/time-series-insights-event-source-create.png)

1. Fare clic su **Crea**.

> [!NOTE]
> Se è necessario concedere ad altri utenti l'accesso allo strumento di esplorazione di Azure Time Series Insights, eseguire la procedura seguente per [concedere l'accesso ai dati](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access#grant-data-access).

## <a name="time-series-insights-explorer"></a>Strumento di esplorazione di Azure Time Series Insights

Lo strumento di esplorazione di Azure Time Series Insights è un'app Web che agevola la creazione delle visualizzazioni dati.

1. Selezionare la scheda **Panoramica**.

1. Fare clic su **Vai all'ambiente** per visualizzare l'app Web dello strumento di esplorazione di Azure Time Series Insights.

    ![Strumento di esplorazione di Azure Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-environment.png)

1. Nel riquadro di selezione oraria, scegliere **Ultime 12 ore** dal menu dei tempi rapidi e fare clic su **Cerca**.

    ![Funzione Cerca dello strumento di esplorazione di Azure Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-search-time.png)

1. Nel riquadro delle condizioni a sinistra, selezionare un valore misura di **temperatura** e un valore in base a cui dividere **iothub-connection-device-id**.

    ![Query dello strumento di esplorazione di Azure Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-query1.png)

1. Fare clic con il pulsante destro del mouse sul grafico e selezionare **Esplora eventi**.

    ![Eventi dello strumento di esplorazione di Azure Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-explore-events.png)

1. Gli eventi vengono visualizzati nella griglia in formato tabulare.

    ![Tabella dello strumento di esplorazione di Azure Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-table.png)

1. Fare clic sul pulsante Perspective View (Visualizzazione prospettiva).

    ![Prospettiva dello strumento di esplorazione di Azure Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-explorer-perspective.png)

1. Fare clic su **Aggiungi** per creare una nuova query nella prospettiva.

    ![Aggiungi query dello strumento di esplorazione di Azure Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-new-query.png)

1. Selezionare uno dei tempi rapidi nelle **Ultime 12 ore**, una misura di **Umidità** e un valore in base al quale dividere **iothub-connection-device-id**.

    ![Query dello strumento di esplorazione di Azure Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-query2.png)

1. Fare clic sul pulsante Perspective View (Visualizzazione prospettiva) per visualizzare il dashboard delle metriche del dispositivo.

    ![Dashboard dello strumento di esplorazione di Azure Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-dashboard.png)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come esplorare ed eseguire query sui dati nello strumento di esplorazione di Azure Time Series Insights, vedere [Strumento di esplorazione di Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).
