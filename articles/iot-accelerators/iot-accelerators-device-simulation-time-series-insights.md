---
title: Visualizzare i dati di telemetria simulati con Time Series Insights-Azure | Microsoft Docs
description: Informazioni su come configurare l'ambiente Time Series Insights per esplorare e analizzare i dati di telemetria generati dall'acceleratore della soluzione Simulazione dispositivi.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 2bbd7911a40d6a256d478e2533ad2469b8fd6973
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889337"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Usare Time Series Insights per visualizzare i dati di telemetria inviati dall'acceleratore della soluzione Simulazione dispositivi

L'acceleratore della soluzione Simulazione dispositivi consente di generare dati di telemetria da dispositivi simulati per testare le soluzioni IoT dell'utente. Questa guida illustra come visualizzare e analizzare i dati di telemetria simulati usando un ambiente Time Series Insights.

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura descritta in questa guida pratica, è necessaria una sottoscrizione di Azure attiva. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

I passaggi descritti in questa guida presuppongono di aver distribuito l'acceleratore della soluzione Simulazione dispositivi alla sottoscrizione di Azure. Se non è stato distribuito l'acceleratore della soluzione, seguire i passaggi nella guida introduttiva [Distribuire ed eseguire una soluzione di simulazione dei dispositivi basata sul cloud](quickstart-device-simulation-deploy.md).

Questo articolo presuppone che l'acceleratore della soluzione sia denominato **contoso-simulation**. Sostituire **contoso-simulation** con il nome dell'acceleratore della soluzione durante il completamento dei passaggi seguenti.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>Creare un gruppo di consumer

È necessario creare un gruppo di consumer dedicato nell'hub IoT, che verrà usato per lo streaming dei dati di telemetria in Time Series Insights. Un'origine evento di Time Series Insights dovrebbe avere l'uso esclusivo di un gruppo di consumer dell'hub IoT.

I passaggi seguenti usano l'interfaccia della riga di comando di Azure in Azure Cloud Shell per creare il gruppo di consumer:

1. L'hub IoT è una delle diverse risorse create quando è stato distribuito l'acceleratore di soluzioni Simulazione dispositivi. Eseguendo il comando seguente si trova il nome dell'hub IoT: ricordarsi di usare il nome dell'acceleratore di soluzioni:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    L'hub IoT è la risorsa di tipo **Microsoft.Devices/IotHubs**.

1. Aggiungere un gruppo di consumer denominato **devicesimulationtsi** all'hub. Usare il nome dell'acceleratore di soluzioni e dell'hub nel comando seguente:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    Ora è possibile chiudere Azure Cloud Shell.

## <a name="create-a-new-time-series-insights-environment"></a>Creare un nuovo ambiente Azure Time Series Insights

[Azure Time Series Insights](../../articles/time-series-insights/time-series-insights-overview.md) è un servizio di analisi, archiviazione e visualizzazione completamente gestito per la gestione di dati delle serie temporali su scala IoT sul cloud. Per creare un nuovo ambiente Time Series Insights:

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare **Crea una risorsa** > **Internet delle cose** > **Time Series Insights**:

    ![Nuovo ambiente Azure Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. Per creare l'ambiente Time Series Insights nello stesso gruppo di risorse dell'acceleratore di soluzioni, usare i valori nella tabella seguente:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome ambiente | Nella screenshot seguente viene usato il nome **Contoso-TSI**. Quando si completa questo passaggio, scegliere il proprio nome univoco. |
    | Sottoscrizione | Selezionare la sottoscrizione di Azure nell'elenco a discesa. |
    | Resource group | **contoso-simulation**. Usare il nome dell'acceleratore di soluzioni. |
    | Percorso | In questo esempio viene usato **Stati Uniti orientali**. Creare l'ambiente nella stessa area dell'acceleratore di Simulazione dispositivi. |
    | Sku |**S1** |
    | Capacità | **1** |

    ![Creare Azure Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > Aggiungere l'ambiente Time Series Insights allo stesso gruppo di risorse dell'acceleratore di soluzioni significa che verrà eliminato quando si elimina l'acceleratore di soluzioni.

1. Fare clic su **Create**. La creazione dell'ambiente può richiedere qualche minuto.

## <a name="create-event-source"></a>Creare un'origine evento

Creare una nuova origine evento per la connessione all'hub IoT. Usare il gruppo di consumer creato nei passaggi precedenti. Un'origine evento Time Series Insights richiede che ogni servizio disponga di un gruppo consumer dedicato, non usato da un altro servizio.

1. Nel portale di Azure aprire il nuovo ambiente Time Series.

1. A sinistra, selezionare **Origine evento**:

    ![Visualizzare le origini evento](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. Fare clic su **Aggiungi**:

    ![Aggiungere un'origine evento](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. Per configurare l'hub IoT come nuova origine evento, usare i valori nella tabella seguente:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome Origine evento | Nella screenshot seguente viene usato il nome **contoso-iot-hub**. Quando si completa questo passaggio, usare il proprio nome univoco. |
    | Source | **Hub IoT** |
    | Opzione di importazione | **Usare un hub IoT delle sottoscrizioni disponibili** |
    | ID sottoscrizione | Selezionare la sottoscrizione di Azure nell'elenco a discesa. |
    | Nome dell'hub IoT | **contoso-simulation7d894**. Usare il nome dell'hub IoT dall'acceleratore di soluzioni Simulazione dispositivi. |
    | Nome dei criteri dell'hub IoT | **iothubowner** |
    | Chiave dei criteri dell'hub IoT | Il campo viene popolato automaticamente. |
    | Gruppo di consumer dell'hub IoT | **devicesimulationtsi** |
    | Formato di serializzazione eventi | **JSON** |
    | Nome della proprietà Timestamp | Lasciare vuoto |

    ![Creare un'origine evento](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. Fare clic su **Create**.

> [!NOTE]
> È possibile [concedere l'accesso ad altri utenti](../../articles/time-series-insights/time-series-insights-data-access.md#grant-data-access) allo strumento di esplorazione di Time Series Insights.

## <a name="start-a-simulation"></a>Avviare una simulazione

Prima di usare lo strumento di esplorazione di Azure Time Series Insights, configurare l'acceleratore della soluzione Simulazione dispositivi per generare alcuni dati di telemetria. La screenshot seguente mostra una simulazione in esecuzione con 10 dispositivi refrigeratore:

![Simulazione dispositivi in esecuzione](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Strumento di esplorazione di Time Series Insights

Lo strumento di esplorazione di Azure Time Series Insights è un'app Web che si può usare per visualizzare i dati di telemetria.

1. Nel portale di Azure selezionare la scheda **Panoramica** di Time Series Insights.

1. Fare clic su **Vai all'ambiente** per visualizzare l'app Web dello strumento di esplorazione di Time Series Insights:

    ![Strumento di esplorazione di Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. Nel riquadro di selezione oraria scegliere **Ultimi 30 minuti** dal menu dei tempi rapidi e fare clic su **Cerca**:

    ![Funzione Cerca dello strumento di esplorazione di Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. Nel riquadro delle condizioni a sinistra selezionare **temperature** sotto **Misura** e **iothub-connection-device-id** come valore per **Dividi per**:

    ![Query dello strumento di esplorazione di Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Fare clic con il pulsante destro del mouse sul grafico e selezionare **Esplora eventi**:

    ![Eventi dello strumento di esplorazione di Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. I dati dell'evento sono riportati in una griglia:

    ![Tabella dello strumento di esplorazione di Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. Fare clic sul pulsante Visualizzazione prospettiva:

    ![Prospettiva dello strumento di esplorazione di Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Fare clic su **+** per aggiungere una nuova query alla prospettiva:

    ![Aggiunta di query dello strumento di esplorazione di Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Selezionare **Ultimi 30 minuti** come intervallo di tempo, **Umidità** come **Misura** e **iothub-connection-device-id** come valore di **Dividi per**:

    ![Query dello strumento di esplorazione di Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. Fare clic sul pulsante Visualizzazione prospettiva per visualizzare il dashboard dei dati di telemetria del dispositivo:

    ![Dashboard dello strumento di esplorazione di Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di esplorare la soluzione ulteriormente, lasciare distribuito l'acceleratore di soluzione.

Se l'acceleratore di soluzione non è più necessario, eliminarlo dalla pagina [Soluzioni di cui è stato effettuato il provisioning](https://www.azureiotsolutions.com/Accelerators#dashboard) selezionandolo e facendo clic su **Elimina soluzione**.

Se è stato aggiunto l'ambiente Time Series Insights al gruppo di risorse dell'acceleratore di soluzioni significa che viene eliminato quando si elimina l'acceleratore di soluzioni. In caso contrario, è necessario rimuovere manualmente l'ambiente Time Series Insights dal portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come esplorare ed eseguire query sui dati nello strumento di esplorazione di Time Series Insights, vedere [Strumento di esplorazione di Azure Time Series Insights](../time-series-insights/time-series-insights-explorer.md).
