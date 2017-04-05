---
title: Visualizzazione in tempo reale dei dati del sensore dall&quot;hub IoT di Azure - App Web | Microsoft Docs
description: "Usare le app Web di Azure per visualizzare i dati di temperatura e umidità raccolti dal sensore e inviati all&quot;hub IoT di Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: visualizzazione dei dati in tempo reale, visualizzazione dei dati dal vivo, visualizzazione dei dati del sensore
ms.assetid: e42b07a8-ddd4-476e-9bfb-903d6b033e91
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/29/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 788b3a6ce98c6668a8efc41f66c7e39b8aeda2db
ms.lasthandoff: 03/30/2017


---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-azure-web-apps"></a>Visualizzare i dati del sensore in tempo reale dall'hub IoT di Azure usando le app Web di Azure

![Connessione tra il sensore, dispositivi IoT, hub IoT e app Web di Azure](media/iot-hub-live-data-visualization-in-web-apps/1_sensor-iot-device-azure-iot-hub-web-app-connection.png)

> [!NOTE]
> Prima di iniziare questa esercitazione, assicurarsi di aver completato [Collegare ESP8266 all'hub IoT di Azure](/iot-hub-arduino-huzzah-esp8266-get-started.md). In [Collegare ESP8266 all'hub IoT di Azure](/iot-hub-arduino-huzzah-esp8266-get-started.md), configurare il dispositivo IoT e l'hub IoT e distribuire un'applicazione di esempio per l'esecuzione sul dispositivo. L'applicazione invia i dati del sensore raccolti all'hub IoT.

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

Questa lezione contiene informazioni su come visualizzare i dati del sensore in tempo reale che l'hub IoT di Azure riceve tramite l'esecuzione di un'applicazione Web ospitata nel sito web di Azure. Se si desidera provare a visualizzare i dati nell'hub IoT con Power BI, consultare [Usare Power BI per visualizzare i dati del sensore in tempo reale da Azure IoT Hub](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Operazioni da fare

- Creare un'app Web di Azure nel portale di Azure.
- Preparare l'hub IoT per l'accesso dei dati mediante l'aggiunta di un gruppo di consumer.
- Configurare l'app Web per leggere i dati del sensore dall'hub IoT.
- Caricare un'applicazione Web che deve essere ospitata dall'app Web.
- Aprire l'app Web per visualizzare i dati di temperatura e umidità in tempo reale dall'hub IoT.

## <a name="what-you-need"></a>Elementi necessari

- Completare l'esercitazione [Collegare ESP8266 all'hub IoT di Azure](iot-hub-arduino-huzzah-esp8266-get-started.md) che prevede i requisiti seguenti:
  - Una sottoscrizione di Azure attiva.
  - Un hub IoT di Azure nella sottoscrizione.
  - Un'applicazione client che invia messaggi all'hub IoT di Azure.
- Git. ([Scaricare Git](https://www.git-scm.com/downloads)).

## <a name="create-an-azure-web-app"></a>Creare un'app Web di Azure

1. Nel [portale di Azure](https://ms.portal.azure.com/) fare clic su **Nuovo** > **Web e dispositivi mobili** > **App Web**.
1. Immettere un nome univoco del processo, verificare la sottoscrizione, specificare un gruppo di risorse e il percorso, selezionare **Aggiungi al dashboard**, quindi fare clic su **Crea**.

   È consigliabile selezionare la stessa posizione in cui si trova il gruppo di risorse. Questo aumenta la velocità di elaborazione riduce i costi del trasferimento dati.

   ![Creare un'app Web di Azure](media/iot-hub-live-data-visualization-in-web-apps/2_create-web-app-azure.png)

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Aggiungere un gruppo di consumer dell'hub IoT

I gruppi di consumer vengono usati dalle applicazioni per eseguire il pull dei dati dall'hub IoT di Azure. In questa lezione viene creato un gruppo di consumer che l'applicazione Web userà per leggere i dati dall'hub IoT.

Per aggiungere un gruppo di consumer all'hub IoT, seguire questa procedura:

1. Nel [portale di Azure](https://ms.portal.azure.com/), aprire l'hub IoT.
1. Fare clic su **Endpoint** nel riquadro a sinistra, selezionare **Eventi** nel riquadro centrale, immettere un nome in **Gruppi di consumer** nel riquadro a destra, quindi **Salva**.

   ![Creare un gruppo di consumer nell'hub IoT di Azure](media/iot-hub-live-data-visualization-in-web-apps/3_add-consumer-group-iot-hub-azure.png)

## <a name="configure-the-web-app-to-read-data-from-your-iot-hub"></a>Configurare l'app Web per leggere i dati dall'hub IoT

1. Aprire l'app Web su cui è stato appena effettuato il provisioning.
1. Fare clic su **Impostazioni applicazione**, quindi aggiungere le seguenti coppie chiave/valore in **Impostazioni app**:

   | Chiave                                   | Valore                                                        |
   |---------------------------------------|--------------------------------------------------------------|
   | Azure.IoT.IoTHub.ConnectionString     | Ottenuto da IoT Hub Explorer                               |
   | Azure.IoT.IoTHub.DeviceId             | Ottenuto da IoT Hub Explorer                               |
   | Azure.IoT.IoTHub.ConsumerGroup        | Il nome del gruppo di consumer che si aggiunge all'hub IoT  |

   ![Aggiungere le impostazioni all'app Web di Azure con coppie chiave-valore](media/iot-hub-live-data-visualization-in-web-apps/4_web-app-settings-key-value-azure.png)

## <a name="upload-a-web-application-to-be-hosted-by-the-web-app"></a>Caricare un'applicazione Web che deve essere ospitata dall'app Web

Su GitHub è disponibile un'applicazione Web che consente di visualizzare i dati del sensore in tempo reale dall'hub IoT. È sufficiente configurare l'app Web che dovrà usare un repository Git, scaricare l'applicazione Web da GitHub e caricarla in Azure affinché l'applicazione Web possa ospitarla.

1. Nell'app Web, fare clic su **Opzioni di distribuzione** > **Scegli origine** > **Repository Git locale**.

   ![Configurare la distribuzione dell'app Web di Azure per l'uso del repository git locale](media/iot-hub-live-data-visualization-in-web-apps/5_configure-web-app-deployment-local-git-repository-azure.png)

1. Fare clic su **Configura connessione**, creare un nome utente e una password che verranno usati per connettersi al repository Git in Azure e quindi fare clic su **OK**.

   ![Impostare nome utente e password per il repository git in Azure per l'app Web](media/iot-hub-live-data-visualization-in-web-apps/6_web-app-set-user-password-git-repo-azure.png)

1. Fare clic su **OK** per completare la configurazione.
1. Fare clic su **Panoramica** e prendere nota del valore di **URL clone GIT**.

   ![Ottenere l'URL clone Git dell'app Web di Azure](media/iot-hub-live-data-visualization-in-web-apps/7_web-app-git-clone-url-azure.png)

1. Aprire una finestra del comando o del terminale nel computer locale.
1. Scaricare l'applicazione Web da GitHub e caricarla in Azure affinché ospiti l'applicazione Web. A tale scopo, eseguire i comandi seguenti:

   ```bash
   git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
   git remote add webapp <Git clone URL>
   git push webapp master:master
   ```

   > [!Note]
   > \<URL clone GIT\> è l'URL del repository Git che si trova nella pagina **Panoramica** dell'app Web.

## <a name="open-the-web-app-to-see-real-time-temperature-and-humidity-data-from-your-iot-hub"></a>Aprire l'app Web per visualizzare i dati di temperatura e umidità in tempo reale dall'hub IoT

Nella pagina **Panoramica** dell'app Web fare clic sull'URL per aprire l'app Web.

![Ottenere l'URL dell'app Web di Azure](media/iot-hub-live-data-visualization-in-web-apps/8_web-app-url-azure.png)

Vengono visualizzati i dati di temperatura e umidità in tempo reale dall'hub IoT.

![Pagina dell'app Web di Azure che mostra temperatura e umidità in tempo reale](media/iot-hub-live-data-visualization-in-web-apps/9_web-app-page-show-real-time-temperature-humidity-azure.png)

## <a name="next-steps"></a>Passaggi successivi
Si è utilizzato correttamente un'app Web per visualizzare i dati del sensore in tempo reale dall'hub IoT di Azure.

Esiste un modo alternativo per visualizzare i dati dall'hub IoT di Azure. Vedere [Usare Power BI per visualizzare i dati del sensore in tempo reale dall'hub IoT di Azure](iot-hub-live-data-visualization-in-power-bi.md).

Per altre informazioni sulle attività iniziali con l'hub IoT e per esplorare altri scenari IoT, vedere:

- [Gestire la messaggistica dei dispositivi cloud con iothub-explorer](iot-hub-explorer-cloud-device-messaging.md)
- [Salvare i messaggi dell'hub IoT nell'archivio dati di Azure](iot-hub-store-data-in-azure-table-storage.md)

