---
title: Ricevere gli avvisi del log attività nelle notifiche del servizio di Azure
description: Ricevere le notifiche tramite SMS, posta elettronica o webhook nel servizio di Azure.
author: shawntabrizi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/09/2018
ms.author: shtabriz
ms.openlocfilehash: 3157cef6952f01d689d3cb4a30c9d7c843095809
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071689"
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

Per altre informazioni sui gruppi di azioni, vedere [Creare e gestire gruppi di azioni](../../azure-monitor/platform/action-groups.md).

Per informazioni su come configurare gli avvisi di notifica sull'integrità del servizio usando i modelli Azure Resource Manager, vedere [Modelli di Resource Manager](alerts-activity-log.md).

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>Guardare un video su come configurare il primo avviso dell'integrità del servizio di Azure

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="alert-and-new-action-group-using-azure-portal"></a>Avviso e nuovo gruppo di azioni usando il portale di Azure
1. Nel [portale](https://portal.azure.com) selezionare **Integrità del servizio**.

    ![Il servizio "Integrità del servizio"](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. Nella sezione **Avvisi** selezionare **Avvisi integrità**.

    ![La scheda "Avvisi di integrità"](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. Selezionare **Create service health alert** (Crea avviso di integrità del servizio) e compilare i campi.

    ![Il comando "Create service health alert" (Crea avviso di integrità del servizio)](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Selezionare la **Sottoscrizione**, i **Servizi** e le **Aree** per cui si vogliono ricevere gli avvisi.

    ![Finestra di dialogo "Aggiungi avviso del log attività"](media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)

> [!NOTE]
> Questa sottoscrizione viene usata per salvare l'avviso del log attività. Questa è la sottoscrizione in cui verrà distribuita la risorsa di avviso e in cui verranno monitorati gli eventi nel log attività.

1. Scegliere i **tipi di evento** per cui si desidera ricevere avvisi: *Problema del servizio*, *Manutenzione pianificata* e *Avvisi di integrità* 

1. Definire i dettagli dell'avviso immettendo un **Nome regola di avviso** e una **Descrizione**.

1. Selezionare il **Gruppo di risorse** in cui si vuole che venga salvato l'avviso.

1. Creare un nuovo gruppo di azioni selezionando **Nuovo gruppo di azioni**. Immettere un nome nella casella **Nome gruppo di azione** e un nome nella casella **Nome breve gruppo di azione**. Viene fatto riferimento al nome breve nelle notifiche inviate all'attivazione dell'avviso.

    ![Creare un nuovo gruppo di azioni](media/alerts-activity-log-service-notifications/action-group-creation.png)

1. Definire un elenco di ricevitori specificando i dati seguenti relativi al ricevitore:

    a. **Nome**: immettere il nome, l'alias o l'identificatore del ricevitore.

    b. **Tipo di azione**: selezionare SMS, posta elettronica, webhook, app di Azure e altro.

    c. **Dettagli**: in base al tipo di azione selezionato, immettere un numero di telefono, un indirizzo di posta elettronica, l'URI del webhook, ecc.

1. Selezionare **OK** per creare il gruppo di azioni e quindi **Crea regola di avviso** per completare l'avviso.

Entro pochi minuti, l'avviso diventa attivo e inizia ad attivarsi in base alle condizioni specificate al momento della creazione.

Informazioni su come [configurare le notifiche di webhook per i sistemi di gestione dei problemi esistenti](../../service-health/service-health-alert-webhook-guide.md). Per informazioni sullo schema webhook per gli avvisi del log attività, vedere [Webhook per gli avvisi del log attività di Azure](../../azure-monitor/platform/activity-log-alerts-webhook.md).

>[!NOTE]
>Il gruppo di azione definito in questi passaggi è riutilizzabile come gruppo di azione esistente per tutte le future definizioni di avviso.
>
>

## <a name="alert-with-existing-action-group-using-azure-portal"></a>Avviso con un gruppo di azioni esistente usando il portale di Azure

1. Seguire i passaggi da 1 a 6 nella sezione precedente per creare la notifica di integrità del servizio. 

1. In **Definire il gruppo di azioni** fare clic sul pulsante **Seleziona gruppo di azioni**. Selezionare il gruppo di azioni appropriato.

1. Selezionare **Aggiungi** per creare il gruppo di azioni e quindi **Crea regola di avviso** per completare l'avviso.

Entro pochi minuti, l'avviso diventa attivo e inizia ad attivarsi in base alle condizioni specificate al momento della creazione.

## <a name="alert-and-new-action-group-using-the-azure-resource-manager-templates"></a>Avviso e nuovo gruppo di azioni usando i modelli di Azure Resource Manager

Di seguito è riportato un esempio che crea un gruppo di azioni con una destinazione del messaggio di posta elettronica e abilita tutte le notifiche sull'integrità del servizio per la sottoscrizione di destinazione.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "actionGroups_name": {
            "defaultValue": "SubHealth",
            "type": "String"
        },
        "activityLogAlerts_name": {
            "defaultValue": "ServiceHealthActivityLogAlert",
            "type": "String"
        },
        "emailAddress":{
            "type":"string"
        }
    },
    "variables": {
        "alertScope":"[concat('/','subscriptions','/',subscription().subscriptionId)]"
    },
    "resources": [
        {
            "comments": "Action Group",
            "type": "microsoft.insights/actionGroups",
            "name": "[parameters('actionGroups_name')]",
            "apiVersion": "2017-04-01",
            "location": "Global",
            "tags": {},
            "scale": null,
            "properties": {
                "groupShortName": "[parameters('actionGroups_name')]",
                "enabled": true,
                "emailReceivers": [
                    {
                        "name": "[parameters('actionGroups_name')]",
                        "emailAddress": "[parameters('emailAddress')]"
                    }
                ],
                "smsReceivers": [],
                "webhookReceivers": []
            },
            "dependsOn": []
        },
        {
            "comments": "Service Health Activity Log Alert",
            "type": "microsoft.insights/activityLogAlerts",
            "name": "[parameters('activityLogAlerts_name')]",
            "apiVersion": "2017-04-01",
            "location": "Global",
            "tags": {},
            "scale": null,
            "properties": {
                "scopes": [
                    "[variables('alertScope')]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ServiceHealth"
                        },
                        {
                            "field": "properties.incidentType",
                            "equals": "Incident"
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
                            "webhookProperties": {}
                        }
                    ]
                },
                "enabled": true,
                "description": ""
            },
            "dependsOn": [
                "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
            ]
        }
    ]
}
```

## <a name="manage-your-alerts"></a>Gestire gli avvisi

Dopo la creazione, l'avviso sarà visibile nella sezione **Avvisi** di **Monitoraggio**. Selezionare l'avviso da gestire per:

* Modificarlo.
* Eliminarlo.
* Disabilitarlo o abilitarlo per interrompere temporaneamente o riprendere la ricezione delle notifiche relative all'avviso.

## <a name="next-steps"></a>Passaggi successivi
- Scopri [procedure consigliate per la configurazione degli avvisi di Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Informazioni su come [configurare le notifiche push per dispositivi mobili per Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Informazioni su come [configurare le notifiche di webhook per i sistemi di gestione dei problemi esistenti](../../service-health/service-health-alert-webhook-guide.md).
- Informazioni sulle [notifiche per l'integrità del servizio](../../azure-monitor/platform/service-notifications.md).
- Informazioni sulla [limitazione della frequenza delle notifiche](../../azure-monitor/platform/alerts-rate-limiting.md).
- Esaminare lo [schema webhook degli avvisi del log attività](../../azure-monitor/platform/activity-log-alerts-webhook.md).
- Leggere una [panoramica degli avvisi del log attività](../../azure-monitor/platform/alerts-overview.md) e informazioni su come ricevere gli avvisi. 
- Altre informazioni sui [gruppi di azione](../../azure-monitor/platform/action-groups.md).
