---
title: Uso degli avvisi e correzione dei problemi dei dispositivi nella soluzione di monitoraggio remoto - Azure | Microsoft Docs
description: Questa esercitazione illustra come usare gli avvisi per identificare e correggere i problemi con dispositivi connessi all'acceleratore della soluzione di monitoraggio remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/18/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 9607705220450b30d2ffaf0f2be9fa2a5664b879
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081789"
---
# <a name="troubleshoot-and-remediate-device-issues"></a>Risolvere e correggere i problemi dei dispositivi

In questa esercitazione si usa l'acceleratore della soluzione di monitoraggio remoto per identificare e correggere problemi con i dispositivi IoT connessi. Gli avvisi visualizzati nel dashboard dell'acceleratore della soluzione consentono di identificare i problemi e di eseguire processi remoti per risolverli.

Contoso sta testando un nuovo dispositivo **Prototype** (Prototipo) sul campo. Un operatore di Contoso durante i test nota che il dispositivo **Prototype** (Prototipo) attiva in modo imprevisto un avviso relativo alla temperatura nel dashboard. È necessario esaminare il comportamento di questo dispositivo **Prototype** (Prototipo) difettoso e correggere il problema.

In questa esercitazione:

>[!div class="checklist"]
> * Analizzare un avviso di un dispositivo
> * Risolvere il problema del dispositivo

## <a name="prerequisites"></a>Prerequisiti

Per seguire questa esercitazione, è necessaria un'istanza distribuita dell'acceleratore della soluzione di monitoraggio remoto nella sottoscrizione di Azure.

Se l'acceleratore della soluzione di monitoraggio remoto non è stato ancora distribuito, completare la guida introduttiva [Distribuire la soluzione di monitoraggio remoto basata su cloud](quickstart-remote-monitoring-deploy.md).

## <a name="investigate-an-alert"></a>Analisi di un avviso

Nella pagina **Dashboard** è possibile vedere alcuni avvisi imprevisti relativi alla temperatura provenienti dalla regola associata ai dispositivi **Prototype** (Prototipo):

[![Avvisi visualizzati nel dashboard](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-expanded.png#lightbox)

Per esaminare in modo più approfondito il problema, scegliere l'opzione **Explore Alert** (Esplora avviso) accanto all'allarme:

[![Esplorare l'avviso nel dashboard](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-expanded.png#lightbox)

La visualizzazione dettagli dell'avviso mostra:

* Data e ora di attivazione dell'avviso
* Informazioni sullo stato dei dispositivi associati all'avviso
* Dati di telemetria dei dispositivi associati all'avviso

[![Dettagli dell'avviso](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-expanded.png#lightbox)

Per confermare di aver visto l'avviso, selezionare tutte le occorrenze in **Alert occurrences** (Occorrenze avviso) e scegliere **Acknowledge** (Conferma). In questo modo, altri operatori sapranno che l'avviso è stato visto e che qualcuno ci sta lavorando.

[![Confermare gli avvisi.](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-expanded.png#lightbox)

Quando si conferma l'avviso, lo stato dell'occorrenza passa a **Confermato**.

Nell'elenco è possibile vedere il dispositivo **Prototype** (Prototipo) responsabile della generazione dell'avviso relativo alla temperatura:

[![Elencare i dispositivi che causano l'avviso](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-expanded.png#lightbox)

## <a name="resolve-the-issue"></a>Correzione del problema

Per risolvere il problema per il dispositivo **Prototype** (Prototipo), è necessario chiamare il metodo **DecreaseTemperature** sul dispositivo.

Per eseguire operazioni su un dispositivo, selezionarlo nell'elenco dei dispositivi e quindi scegliere **Processi**. Il modello di dispositivo **Prototype** (Prototipo) specifica sei metodi che un dispositivo deve supportare:

[![Visualizzare i metodi supportati dai dispositivi](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-expanded.png#lightbox)

Scegliere **DecreaseTemperature** e impostare il nome del processo su **DecreaseTemperature**. Scegliere quindi **Apply** (Applica):

[![Creare il processo per ridurre la temperatura](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-expanded.png#lightbox)

Per tenere traccia dello stato del processo, fare clic su **View job status** (Visualizza stato del processo). Usare la visualizzazione **Jobs** (Processi) per tenere traccia di tutti i processi e le chiamate ai metodi nella soluzione:

[![Monitorare il processo per ridurre la temperatura](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-expanded.png#lightbox)

È possibile verificare che la temperatura del dispositivo sia diminuita visualizzando i dati di telemetria sulla pagina **Dashboard**:

[![Visualizzare il calo della temperatura](./media/iot-accelerators-remote-monitoring-maintain/jobresult-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/jobresult-expanded.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato come usare gli avvisi per identificare i problemi con i dispositivi e su come agire sui dispositivi per risolvere i problemi. Per informazioni su come connettere un dispositivo fisico all'acceleratore della soluzione, passare alle procedure dettagliate.

Ora che si è appreso come gestire i problemi dei dispositivi, il passaggio successivo consigliato consente di apprendere come [connettere un dispositivo fisico all'acceleratore della soluzione di monitoraggio remoto](iot-accelerators-connecting-devices.md).
