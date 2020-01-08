---
title: Visualizzare le notifiche sull'integrità del servizio tramite il portale di Azure
description: Le notifiche sull'integrità del servizio consentono di visualizzare i messaggi sull'integrità del servizio pubblicati da Microsoft Azure.
ms.topic: conceptual
ms.date: 6/27/2019
ms.openlocfilehash: c8017d23e0f7e3ab7bf48c9d7ba6b930fa80d0da
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451428"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Visualizzare le notifiche sull'integrità del servizio tramite il portale di Azure

Le notifiche sull'integrità del servizio vengono pubblicate dall'infrastruttura di Azure nel [log attività di Azure](../azure-monitor/platform/activity-logs-overview.md).  Le notifiche contengono informazioni sulle risorse nella sottoscrizione. Dato il notevole volume di informazioni archiviate nel log attività, è disponibile un'interfaccia utente separata che semplifica la visualizzazione e la configurazione degli avvisi per le notifiche sull'integrità del servizio. 

Le notifiche sull'integrità del servizio possono essere informative o operative a seconda della classe.

Per ulteriori informazioni sulle varie classi di notifiche sull'integrità del servizio, vedere [proprietà delle notifiche sull'integrità del servizio](service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Visualizzare le notifiche sull'integrità del servizio nel portale di Azure

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Monitoraggio**.

    ![Schermata del menu del portale di Azure, con Monitoraggio selezionato](./media/service-notifications/home-monitor.png)

    Monitoraggio di Azure riunisce tutte le impostazioni e i dati di monitoraggio in un'unica vista consolidata. Per prima cosa si apre la sezione **Log di attività**.

1. Selezionare **Avvisi**.

    ![Schermata di Log attività in Monitoraggio, con Avvisi selezionati](./media/service-notifications/service-health-summary.png)

1. Selezionare **+ Aggiungi avviso del log attività** e configurare un avviso per assicurarsi di ricevere le future notifiche del servizio. Per maggiori informazioni, vedere [Creare gli avvisi del log attività per le notifiche del servizio](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sugli [avvisi del log attività](../azure-monitor/platform/activity-log-alerts.md).
