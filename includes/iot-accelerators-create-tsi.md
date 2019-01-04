---
title: File di inclusione
description: File di inclusione
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/20/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: ab3d4cbe8a200e91d02177e49446065cd9c228df
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2018
ms.locfileid: "53609073"
---
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

[Azure Time Series Insights](../articles/time-series-insights/time-series-insights-overview.md) è un servizio di analisi, archiviazione e visualizzazione completamente gestito per la gestione di dati delle serie temporali su scala IoT sul cloud. Per creare un nuovo ambiente Time Series Insights:

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare **Crea una risorsa** > **Internet delle cose** > **Time Series Insights**:

    ![Nuovo ambiente Azure Time Series Insights](./media/iot-accelerators-create-tsi/new-time-series-insights.png)

1. Per creare l'ambiente Time Series Insights nello stesso gruppo di risorse dell'acceleratore di soluzioni, usare i valori nella tabella seguente:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome ambiente | Nella screenshot seguente viene usato il nome **Contoso-TSI**. Quando si completa questo passaggio, scegliere il proprio nome univoco. |
    | Sottoscrizione | Selezionare la sottoscrizione di Azure nell'elenco a discesa. |
    | Gruppo di risorse | **contoso-simulation**. Usare il nome dell'acceleratore di soluzioni. |
    | Località | In questo esempio viene usato **Stati Uniti orientali**. Creare l'ambiente nella stessa area dell'acceleratore di Simulazione dispositivi. |
    | Sku |**S1** |
    | Capacity | **1** |

    ![Creare Azure Time Series Insights](./media/iot-accelerators-create-tsi/new-time-series-insights-create.png)

    > [!NOTE]
    > Aggiungere l'ambiente Time Series Insights allo stesso gruppo di risorse dell'acceleratore di soluzioni significa che verrà eliminato quando si elimina l'acceleratore di soluzioni.

1. Fare clic su **Create**(Crea). La creazione dell'ambiente può richiedere qualche minuto.

## <a name="create-event-source"></a>Creare un'origine evento

Creare una nuova origine evento per la connessione all'hub IoT. Usare il gruppo di consumer creato nei passaggi precedenti. Un'origine evento Time Series Insights richiede che ogni servizio disponga di un gruppo consumer dedicato, non usato da un altro servizio.

1. Nel portale di Azure aprire il nuovo ambiente Time Series.

1. A sinistra, selezionare **Origine evento**:

    ![Visualizzare le origini evento](./media/iot-accelerators-create-tsi/time-series-insights-event-sources.png)

1. Fare clic su **Aggiungi**:

    ![Aggiungere un'origine evento](./media/iot-accelerators-create-tsi/time-series-insights-event-sources-add.png)

1. Per configurare l'hub IoT come nuova origine evento, usare i valori nella tabella seguente:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome Origine evento | Nella screenshot seguente viene usato il nome **contoso-iot-hub**. Quando si completa questo passaggio, usare il proprio nome univoco. |
    | Sorgente | **Hub IoT** |
    | Opzione di importazione | **Usare un hub IoT delle sottoscrizioni disponibili** |
    | ID sottoscrizione | Selezionare la sottoscrizione di Azure nell'elenco a discesa. |
    | Nome dell'hub IoT | **contoso-simulation7d894**. Usare il nome dell'hub IoT dall'acceleratore di soluzioni Simulazione dispositivi. |
    | Nome dei criteri dell'hub IoT | **iothubowner** |
    | Chiave dei criteri dell'hub IoT | Il campo viene popolato automaticamente. |
    | Gruppo di consumer dell'hub IoT | **devicesimulationtsi** |
    | Formato di serializzazione eventi | **JSON** |
    | Nome della proprietà Timestamp | Lasciare vuoto |

    ![Creare un'origine evento](./media/iot-accelerators-create-tsi/time-series-insights-event-source-create.png)

1. Fare clic su **Create**(Crea).

> [!NOTE]
> È possibile [concedere l'accesso ad altri utenti](../articles/time-series-insights/time-series-insights-data-access.md#grant-data-access) allo strumento di esplorazione di Time Series Insights.