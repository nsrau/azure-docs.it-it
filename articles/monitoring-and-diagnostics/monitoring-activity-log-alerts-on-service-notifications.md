---
title: Ricevere gli avvisi del log attività nelle notifiche del servizio di Azure
description: Ricevere le notifiche tramite SMS, posta elettronica o webhook nel servizio di Azure.
author: shawntabrizi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/09/2018
ms.author: shtabriz
ms.component: alerts
ms.openlocfilehash: 6e1a72c428425c73ff0446fc0d41b1b18333c3e3
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423889"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Creare gli avvisi del log attività per le notifiche del servizio
## <a name="overview"></a>Panoramica
Questo articolo descrive come impostare gli avvisi del log attività per le notifiche sull'integrità del servizio usando il portale di Azure.  

È possibile ricevere un avviso quando Azure invia le notifiche sull'integrità del servizio alla sottoscrizione di Azure. È possibile configurare l'avviso in base a:

- La classe di notifica dell'integrità del servizio (problemi del servizio, manutenzione pianificata, avvisi di integrità).
- La sottoscrizione interessata.
- I servizi interessati.
- Le aree interessate.

> [!NOTE]
> Le notifiche sull'integrità del servizio non inviano un avviso relativo a eventi di integrità delle risorse.

È anche possibile configurare l'utente a cui deve essere inviato l'avviso:

- Selezionare un gruppo di azione esistente.
- Creare un nuovo gruppo di azione che può essere usato per avvisi futuri.

Per altre informazioni sui gruppi di azioni, vedere [Creare e gestire gruppi di azioni](monitoring-action-groups.md).

Per informazioni su come configurare gli avvisi di notifica sull'integrità del servizio usando i modelli Azure Resource Manager, vedere [Modelli di Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md).

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-by-using-the-azure-portal"></a>Creare un avviso per una notifica sull'integrità del servizio per un nuovo gruppo di azione usando il portale di Azure
1. Nel [portale](https://portal.azure.com) selezionare **Integrità del servizio**.

    ![Il servizio "Integrità del servizio"](./media/monitoring-activity-log-alerts-on-service-notifications/home-servicehealth.png)

1. Nella sezione **Avvisi** selezionare **Avvisi integrità**.

    ![La scheda "Avvisi di integrità"](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades-sh.png)

1. Selezionare **Create service health alert** (Crea avviso di integrità del servizio) e compilare i campi.

    ![Il comando "Create service health alert" (Crea avviso di integrità del servizio)](./media/monitoring-activity-log-alerts-on-service-notifications/service-health-alert.png)

1. Selezionare la **Sottoscrizione**, i **Servizi** e le **Aree** per cui si vogliono ricevere gli avvisi.

    ![Finestra di dialogo "Aggiungi avviso del log attività"](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-new-ux.png)

> [!NOTE]
> Questa sottoscrizione viene usata per salvare l'avviso del log attività. Questa è la sottoscrizione in cui verrà distribuita la risorsa di avviso e in cui verranno monitorati gli eventi nel log attività.

1. Scegliere i **Tipi di evento** per i quali si vogliono ricevere gli avvisi relativi a: *Problema del servizio*, *Manutenzione pianificata* e *Avvisi di integrità* 

1. Definire i dettagli dell'avviso immettendo un **Nome regola di avviso** e una **Descrizione**.

1. Selezionare il **Gruppo di risorse** in cui si vuole che venga salvato l'avviso.

1. Creare un nuovo gruppo di azioni selezionando **Nuovo gruppo di azioni**. Immettere un nome nella casella **Nome gruppo di azione** e un nome nella casella **Nome breve gruppo di azione**. Viene fatto riferimento al nome breve nelle notifiche inviate all'attivazione dell'avviso.

    ![Creare un nuovo gruppo di azioni](./media/monitoring-activity-log-alerts-on-service-notifications/action-group-creation.png)

1. Definire un elenco di ricevitori specificando i dati seguenti relativi al ricevitore:

    a. **Nome**: immettere il nome, l'alias o l'identificatore del ricevitore.

    b. **Tipo di azione**: selezionare SMS, posta elettronica, webhook, app di Azure e altro.

    c. **Dettagli:** in base al tipo di azione selezionato, immettere un numero di telefono, un indirizzo di posta elettronica, l'URI del webhook, ecc.

1. Selezionare **OK** per creare il gruppo di azioni e quindi **Crea regola di avviso** per completare l'avviso.

Entro pochi minuti, l'avviso diventa attivo e inizia ad attivarsi in base alle condizioni specificate al momento della creazione.

Informazioni su come [configurare le notifiche di webhook per i sistemi di gestione dei problemi esistenti](../service-health/service-health-alert-webhook-guide.md). Per informazioni sullo schema webhook per gli avvisi del log attività, vedere [Webhook per gli avvisi del log attività di Azure](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>Il gruppo di azione definito in questi passaggi è riutilizzabile come gruppo di azione esistente per tutte le future definizioni di avviso.
>
>

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-by-using-the-azure-portal"></a>Creare un avviso per una notifica sull'integrità del servizio per un gruppo di azione esistente usando il portale di Azure

1. Seguire i passaggi da 1 a 7 nella sezione precedente per creare una notifica sull'integrità del servizio. 

1. In **Definire il gruppo di azioni** fare clic sul pulsante **Seleziona gruppo di azioni**. Selezionare il gruppo di azioni appropriato.

1. Selezionare **Aggiungi** per creare il gruppo di azioni e quindi **Crea regola di avviso** per completare l'avviso.

Entro pochi minuti, l'avviso diventa attivo e inizia ad attivarsi in base alle condizioni specificate al momento della creazione.

## <a name="manage-your-alerts"></a>Gestire gli avvisi

Dopo la creazione, l'avviso sarà visibile nella sezione **Avvisi** di **Monitoraggio**. Selezionare l'avviso da gestire per:

* Modificarlo.
* Eliminarlo.
* Disabilitarlo o abilitarlo per interrompere temporaneamente o riprendere la ricezione delle notifiche relative all'avviso.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [configurare le notifiche di webhook per i sistemi di gestione dei problemi esistenti](../service-health/service-health-alert-webhook-guide.md).
- Informazioni sulle [notifiche per l'integrità del servizio](monitoring-service-notifications.md).
- Informazioni sulla [limitazione della frequenza delle notifiche](monitoring-alerts-rate-limiting.md).
- Esaminare lo [schema webhook degli avvisi del log attività](monitoring-activity-log-alerts-webhook.md).
- Leggere una [panoramica degli avvisi del log attività](monitoring-overview-alerts.md) e informazioni su come ricevere gli avvisi. 
- Altre informazioni sui [gruppi di azione](monitoring-action-groups.md).
