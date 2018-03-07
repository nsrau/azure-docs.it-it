---
title: Risolvere i problemi dei dispositivi nella soluzione di monitoraggio remoto - Azure | Microsoft Docs
description: Questa esercitazione mostra come risolvere e correggere i problemi dei dispositivi nella soluzione di monitoraggio remoto.
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 02/22/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: dd01246075a5c0db0ed49133ed51fb56d8fcf8e5
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="troubleshoot-and-remediate-device-issues"></a>Risolvere e correggere i problemi dei dispositivi

Questa esercitazione mostra come usare la pagina **Maintenance** (Manutenzione) nella soluzione per risolvere e correggere i problemi dei dispositivi. Per presentare queste funzionalità, l'esercitazione usa uno scenario nell'applicazione IoT Contoso.

Contoso sta testando un nuovo dispositivo **Prototype** (Prototipo) sul campo. Un operatore di Contoso durante i test nota che il dispositivo **Prototype** (Prototipo) attiva in modo imprevisto un allarme relativo alla temperatura nel dashboard. È necessario esaminare il comportamento di questo dispositivo **Prototype** (Prototipo) difettoso.

In questa esercitazione si apprenderà come:

>[!div class="checklist"]
> * Usare la pagina **Maintenance** (Manutenzione) per esaminare l'allarme
> * Chiamare un metodo del dispositivo per correggere il problema

## <a name="prerequisites"></a>prerequisiti

Per seguire questa esercitazione, è necessaria un'istanza distribuita della soluzione di monitoraggio remoto nella sottoscrizione di Azure.

Se la soluzione di monitoraggio remoto non è stata ancora distribuita, è necessario completare l'esercitazione [Distribuire la soluzione preconfigurata di monitoraggio remoto](iot-suite-remote-monitoring-deploy.md).

## <a name="use-the-maintenance-dashboard"></a>Usare il dashboard di manutenzione

Nella pagina **Dashboard** è possibile vedere alcuni allarmi imprevisti relativi alla temperatura provenienti dalla regola associata ai dispositivi **Prototype** (Prototipo):

![Allarmi visualizzati nel dashboard](media/iot-suite-remote-monitoring-maintain/dashboardalarm.png)

Per esaminare in modo più approfondito il problema, scegliere l'opzione **Explore Alarm** (Esplora allarme) accanto all'allarme:

![Esplorare l'allarme nel dashboard](media/iot-suite-remote-monitoring-maintain/dashboardexplorealarm.png)

La visualizzazione dettagli dell'allarme mostra:

* Data e ora di attivazione dell'allarme
* Informazioni sullo stato dei dispositivi associati all'allarme
* Dati di telemetria dei dispositivi associati all'allarme

![Dettagli dell'allarme](media/iot-suite-remote-monitoring-maintain/maintenancealarmdetail.png)

Per confermare di aver visto l'allarme, selezionare le occorrenze in **Alarm occurrences** (Occorrenze allarme) e scegliere **Acknowledge** (Conferma). In questo modo, altri operatori sapranno che l'allarme è stato visto e che qualcuno ci sta lavorando.

![Confermare gli allarmi](media/iot-suite-remote-monitoring-maintain/maintenanceacknowledge.png)

Quando si conferma l'allarme, lo stato dell'occorrenza passa a **Confermato**.

Nell'elenco è possibile vedere il dispositivo **Prototype** (Prototipo) responsabile della generazione dell'allarme relativo alla temperatura:

![Elencare i dispositivi che causano l'allarme](media/iot-suite-remote-monitoring-maintain/maintenanceresponsibledevice.png)

## <a name="remediate-the-issue"></a>Correggere il problema

Per correggere il problema per il dispositivo **Prototype** (Prototipo), è necessario chiamare il metodo **DecreaseTemperature** sul dispositivo.

Per eseguire operazioni su un dispositivo, selezionarlo nell'elenco dei dispositivi e quindi scegliere **Schedule** (Pianifica). Il modello di dispositivo **Prototype** (Prototipo) specifica quattro metodi che un dispositivo deve supportare:

![Visualizzare i metodi supportati dai dispositivi](media/iot-suite-remote-monitoring-maintain/maintenancemethods.png)

Scegliere **DecreaseTemperature** e impostare il nome del processo su **DecreaseTemperature**. Scegliere quindi **Apply** (Applica):

![Creare il processo per ridurre la temperatura](media/iot-suite-remote-monitoring-maintain/maintenancecreatejob.png)

Per tenere traccia dello stato del processo nella pagina **Maintenance** (Manutenzione), scegliere **Jobs** (Processi). Usare la visualizzazione **Jobs** (Processi) per tenere traccia di tutti i processi e le chiamate ai metodi nella soluzione:

![Monitorare il processo per ridurre la temperatura](media/iot-suite-remote-monitoring-maintain/maintenancerunningjob.png)

Per visualizzare i dettagli di un una chiamata a un metodo o di un processo specifico, scegliere l'elemento nell'elenco nella visualizzazione **Jobs** (Processi):

![Visualizza i dettagli dei processi](media/iot-suite-remote-monitoring-maintain/maintenancejobdetail.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è visto come:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Usare la pagina **Maintenance** (Manutenzione) per esaminare l'allarme
> * Chiamare un metodo del dispositivo per correggere il problema

Ora che si è appreso come gestire i problemi dei dispositivi, il passaggio successivo consigliato consente di apprendere come [Testare la soluzione con dispositivi simulati](iot-suite-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->