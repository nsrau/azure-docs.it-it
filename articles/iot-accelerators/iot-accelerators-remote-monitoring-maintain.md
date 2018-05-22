---
title: Risolvere i problemi dei dispositivi nella soluzione di monitoraggio remoto - Azure | Microsoft Docs
description: Questa esercitazione mostra come risolvere e correggere i problemi dei dispositivi nella soluzione di monitoraggio remoto.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 1e6c74b5c952d5a387dbdac4d6e81d691758fb36
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
---
# <a name="troubleshoot-and-remediate-device-issues"></a>Risolvere e correggere i problemi dei dispositivi

Questa esercitazione mostra come usare la pagina **Maintenance** (Manutenzione) nella soluzione per risolvere e correggere i problemi dei dispositivi. Per presentare queste funzionalità, l'esercitazione usa uno scenario nell'applicazione IoT Contoso.

Contoso sta testando un nuovo dispositivo **Prototype** (Prototipo) sul campo. Un operatore di Contoso durante i test nota che il dispositivo **Prototype** (Prototipo) attiva in modo imprevisto un avviso relativo alla temperatura nel dashboard. È necessario esaminare il comportamento di questo dispositivo **Prototype** (Prototipo) difettoso.

In questa esercitazione si apprenderà come:

>[!div class="checklist"]
> * Usare la pagina **Maintenance** (Manutenzione) per esaminare l'avviso
> * Chiamare un metodo del dispositivo per correggere il problema

## <a name="prerequisites"></a>prerequisiti

Per seguire questa esercitazione, è necessaria un'istanza distribuita della soluzione di monitoraggio remoto nella sottoscrizione di Azure.

Se la soluzione di monitoraggio remoto non è stata ancora distribuita, completare l'esercitazione [Distribuire l'acceleratore di soluzioni di monitoraggio remoto](iot-accelerators-remote-monitoring-deploy.md).

## <a name="use-the-maintenance-dashboard"></a>Usare il dashboard di manutenzione

Nella pagina **Dashboard** è possibile vedere alcuni avvisi imprevisti relativi alla temperatura provenienti dalla regola associata ai dispositivi **Prototype** (Prototipo):

![Avvisi visualizzati nel dashboard](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm.png)

Per esaminare in modo più approfondito il problema, scegliere l'opzione **Explore Alert** (Esplora avviso) accanto all'allarme:

![Esplorare l'avviso nel dashboard](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm.png)

La visualizzazione dettagli dell'avviso mostra:

* Data e ora di attivazione dell'avviso
* Informazioni sullo stato dei dispositivi associati all'avviso
* Dati di telemetria dei dispositivi associati all'avviso

![Dettagli dell'avviso](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail.png)

Per confermare di aver visto l'avviso, selezionare le occorrenze in **Alert occurrences** (Occorrenze avviso) e scegliere **Acknowledge** (Conferma). In questo modo, altri operatori sapranno che l'avviso è stato visto e che qualcuno ci sta lavorando.

![Confermare gli avvisi.](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge.png)

Quando si conferma l'avviso, lo stato dell'occorrenza passa a **Confermato**.

Nell'elenco è possibile vedere il dispositivo **Prototype** (Prototipo) responsabile della generazione dell'avviso relativo alla temperatura:

![Elencare i dispositivi che causano l'avviso](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice.png)

## <a name="remediate-the-issue"></a>Correggere il problema

Per correggere il problema per il dispositivo **Prototype** (Prototipo), è necessario chiamare il metodo **DecreaseTemperature** sul dispositivo.

Per eseguire operazioni su un dispositivo, selezionarlo nell'elenco dei dispositivi e quindi scegliere **Processi**. Il modello di dispositivo **Prototype** (Prototipo) specifica sei metodi che un dispositivo deve supportare:

![Visualizzare i metodi supportati dai dispositivi](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods.png)

Scegliere **DecreaseTemperature** e impostare il nome del processo su **DecreaseTemperature**. Scegliere quindi **Apply** (Applica):

![Creare il processo per ridurre la temperatura](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob.png)

Per tenere traccia dello stato del processo nella pagina **Maintenance** (Manutenzione), scegliere **Jobs** (Processi). Usare la visualizzazione **Jobs** (Processi) per tenere traccia di tutti i processi e le chiamate ai metodi nella soluzione:

![Monitorare il processo per ridurre la temperatura](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob.png)

Per visualizzare i dettagli di un una chiamata a un metodo o di un processo specifico, scegliere l'elemento nell'elenco nella visualizzazione **Jobs** (Processi):

![Visualizza i dettagli dei processi](./media/iot-accelerators-remote-monitoring-maintain/maintenancejobdetail.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è visto come:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Usare la pagina **Maintenance** (Manutenzione) per esaminare l'avviso
> * Chiamare un metodo del dispositivo per correggere il problema

Ora che si è appreso come gestire i problemi dei dispositivi, il passaggio successivo consigliato consente di apprendere come [Testare la soluzione con dispositivi simulati](iot-accelerators-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->