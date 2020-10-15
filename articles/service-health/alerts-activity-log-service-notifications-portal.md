---
title: Ricevere gli avvisi del log attività nelle notifiche del servizio di Azure usando portale di Azure
description: Informazioni su come usare il portale di Azure per configurare gli avvisi del log attività per le notifiche sull'integrità del servizio usando il portale di Azure.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 88fbdeeb8bdcc239f1591e053aaf4fb1c36b9b19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91289787"
---
# <a name="create-activity-log-alerts-on-service-notifications-using-the-azure-portal"></a>Creare avvisi del log attività per le notifiche del servizio usando il portale di Azure
## <a name="overview"></a>Panoramica

Questo articolo illustra come usare la portale di Azure per configurare gli avvisi del log attività per le notifiche sull'integrità del servizio usando il portale di Azure.  

Le notifiche sull'integrità del servizio vengono archiviate nel [log attività di Azure](../azure-monitor/platform/platform-logs-overview.md). Dato il volume elevato di informazioni archiviate nel log attività, è disponibile un'interfaccia utente separata che semplifica la visualizzazione e la configurazione degli avvisi per le notifiche sull'integrità del servizio. 

È possibile ricevere un avviso quando Azure invia le notifiche sull'integrità del servizio alla sottoscrizione di Azure. È possibile configurare l'avviso in base a:

- La classe di notifica sull'integrità del servizio (problemi del servizio, manutenzione pianificata, avvisi di integrità, avvisi di sicurezza).
- La sottoscrizione interessata.
- I servizi interessati.
- Le aree interessate.

> [!NOTE]
> Le notifiche sull'integrità del servizio non inviano avvisi per gli eventi di integrità delle risorse.

È anche possibile configurare l'utente a cui deve essere inviato l'avviso:

- Selezionare un gruppo di azione esistente.
- Creare un nuovo gruppo di azione che può essere usato per avvisi futuri.

Per altre informazioni sui gruppi di azioni, vedere [Creare e gestire gruppi di azioni](../azure-monitor/platform/action-groups.md).

Per informazioni su come configurare gli avvisi di notifica sull'integrità del servizio usando i modelli Azure Resource Manager, vedere [Modelli di Resource Manager](../azure-monitor/platform/alerts-activity-log.md).

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>Guarda un video sulla configurazione del primo avviso di integrità dei servizi di Azure

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="create-service-health-alert-using-azure-portal"></a>Creare un avviso di integrità del servizio utilizzando portale di Azure
1. Nel [portale](https://portal.azure.com) selezionare **Integrità del servizio**.

    ![Il servizio "Integrità del servizio"](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. Nella sezione **Avvisi** selezionare **Avvisi integrità**.

    ![La scheda "Avvisi di integrità"](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. Selezionare **Aggiungi avviso integrità servizio** e compilare i campi.

    ![Il comando "Create service health alert" (Crea avviso di integrità del servizio)](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Selezionare la **sottoscrizione**, i **Servizi**e le **aree** per cui si desidera ricevere avvisi.

    [![Finestra di dialogo "Aggiungi avviso del log attività"](./media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)](./media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png#lightbox)

> [!NOTE]
>Questa sottoscrizione viene usata per salvare l'avviso del log attività. Questa è la sottoscrizione in cui verrà distribuita la risorsa di avviso e in cui verranno monitorati gli eventi nel log attività.

5. Scegliere i **tipi di evento** per i quali si desidera ricevere un avviso: *problema del servizio*, *manutenzione pianificata*, *avvisi di integrità*e *avvisi di sicurezza*.

6. Fare clic su **Seleziona gruppo di azioni** per scegliere un gruppo di azioni esistente o creare un nuovo gruppo di azioni. Per ulteriori informazioni sui gruppi di azioni, vedere [creare e gestire gruppi di azioni nella portale di Azure](../azure-monitor/platform/action-groups.md).


7. Definire i dettagli dell'avviso immettendo un **Nome regola di avviso** e una **Descrizione**.

8. Selezionare il **Gruppo di risorse** in cui si vuole che venga salvato l'avviso.



Entro pochi minuti, l'avviso diventa attivo e inizia ad attivarsi in base alle condizioni specificate al momento della creazione.

Informazioni su come [configurare le notifiche di webhook per i sistemi di gestione dei problemi esistenti](service-health-alert-webhook-guide.md). Per informazioni sullo schema webhook per gli avvisi del log attività, vedere [Webhook per gli avvisi del log attività di Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).


## <a name="next-steps"></a>Passaggi successivi
- Informazioni sulle [procedure consigliate per la configurazione degli avvisi di integrità dei servizi di Azure](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Informazioni su come [configurare le notifiche push per dispositivi mobili per l'integrità dei servizi di Azure](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Informazioni su come [configurare le notifiche webhook per i sistemi di gestione dei problemi esistenti](service-health-alert-webhook-guide.md).
- Informazioni sulle [notifiche per l'integrità del servizio](service-notifications.md).
- Informazioni sulla [limitazione della frequenza delle notifiche](../azure-monitor/platform/alerts-rate-limiting.md).
- Esaminare lo [schema webhook degli avvisi del log attività](../azure-monitor/platform/activity-log-alerts-webhook.md).
- Leggere una [panoramica degli avvisi del log attività](../azure-monitor/platform/alerts-overview.md) e informazioni su come ricevere gli avvisi.
- Altre informazioni sui [gruppi di azione](../azure-monitor/platform/action-groups.md).
