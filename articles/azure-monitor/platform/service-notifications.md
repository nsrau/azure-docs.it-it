---
title: Visualizzare le notifiche sull'integrità del servizio tramite il portale di Azure
description: Le notifiche sull'integrità del servizio consentono di visualizzare i messaggi sull'integrità del servizio pubblicati da Microsoft Azure.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2018
ms.author: dukek
ms.subservice: logs
ms.openlocfilehash: 8cf8c3eb86f55b5595ef9a1af83ea50580bf638b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069348"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Visualizzare le notifiche sull'integrità del servizio tramite il portale di Azure

Notifiche sull'integrità del servizio sono pubblicate dall'infrastruttura di Azure. Contengono informazioni sulle risorse nella sottoscrizione. Queste notifiche sono una sottoclasse dell'attività gli eventi del log e quindi, sono disponibili anche nel log attività. Le notifiche sull'integrità del servizio possono essere informative o operative a seconda della classe.

Per altre informazioni su varie classi di notifiche sull'integrità del servizio, vedere [le proprietà delle notifiche di integrità del servizio](../../service-health/service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Visualizzare le notifiche sull'integrità del servizio nel portale di Azure

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Monitoraggio**.

    ![Schermata del menu del portale di Azure, con Monitoraggio selezionato](./media/service-notifications/home-monitor.png)

    Monitoraggio di Azure riunisce tutte le impostazioni e i dati di monitoraggio in un'unica vista consolidata. Per prima cosa si apre la sezione **Log di attività**.

1. Selezionare **Avvisi**.

    ![Schermata di Log attività in Monitoraggio, con Avvisi selezionati](./media/service-notifications/service-health-summary.png)

1. Selezionare **+ Aggiungi avviso del log attività** e configurare un avviso per assicurarsi di ricevere le future notifiche del servizio. Per maggiori informazioni, vedere [Creare gli avvisi del log attività per le notifiche del servizio](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="next-steps"></a>Passaggi successivi

* Ricevere [notifiche di avviso per ogni notifica sull'integrità del servizio](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).  
* Altre informazioni sugli [avvisi del log attività](../../azure-monitor/platform/activity-log-alerts.md).
