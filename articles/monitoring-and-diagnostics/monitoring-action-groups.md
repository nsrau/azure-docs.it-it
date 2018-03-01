---
title: Creare e gestire gruppi di azione nel portale di Azure | Microsoft Docs
description: Informazioni su come creare e gestire gruppi di azione nel portale di Azure.
author: dkamstra
manager: chrad
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: dukek
ms.openlocfilehash: 772b9c2b9532bd2cc37ad89db92545297eecd903
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Creare e gestire gruppi di azione nel portale di Azure
## <a name="overview"></a>Panoramica ##
Questo articolo illustra come creare e gestire gruppi di azione nel portale di Azure.

I gruppi di azione consentono di configurare un elenco di azioni. Questi gruppi possono quindi essere usati da ogni avviso che viene definito, assicurandosi che vengono eseguite le stesse azioni ogni volta che viene generato un avviso.

Un gruppo di azione può avere fino a 10 tipi di azioni. Ogni azione è composta dalle seguenti proprietà:

* **Nome:** un identificatore univoco all'interno del gruppo di azione.  
* **Tipo di azione**: inviare un SMS, inviare un messaggio di posta elettronica, chiamare un webhook, inviare dati a uno strumento ITSM, chiamare un'app Azure o eseguire un runbook di Automazione.
* **Dettagli**: l'URI del webhook, il numero di telefono o l'indirizzo di posta elettronica oppure le informazioni di connessione ITSM corrispondenti.

Per informazioni sull'uso dei modelli di Azure Resource Manager per configurare i gruppi di azione: [Modelli di Resource Manager per il gruppo di azione](monitoring-create-action-group-with-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Creare un gruppo di azione usando il portale di Azure ##
1. Nel [portale](https://portal.azure.com)selezionare **Monitoraggio**. Il pannello **Monitoraggio** consolida tutte le impostazioni e i dati di monitoraggio in una vista.

    ![Servizio "Monitoraggio"](./media/monitoring-action-groups/home-monitor.png)
2. Nella sezione **Impostazioni** selezionare **Gruppi di azioni**.

    ![Scheda "Gruppi di azione"](./media/monitoring-action-groups/action-groups-blade.png)
3. Selezionare **Aggiungi gruppo di azione** e compilare i campi.

    ![Comando "Aggiungi gruppo di azione"](./media/monitoring-action-groups/add-action-group.png)
4. Immettere un nome nella casella **Nome gruppo di azione** e un nome nella casella **Nome breve gruppo di azione**. Il nome breve viene usato al posto del nome completo di un gruppo di azione quando le notifiche vengono inviate usando questo gruppo.

      ![Finestra di dialogo "Aggiungi gruppo di azione"](./media/monitoring-action-groups/action-group-define.png)

5. Nella casella **Sottoscrizione** viene inserita automaticamente la sottoscrizione corrente. Il gruppo di azione verrà salvato in questa sottoscrizione.

6. Selezionare il **gruppo di risorse** in cui verrà salvato il gruppo di azione.

7. Definire un elenco di azioni fornendo i dati di ogni azione:

    a. **Nome**: immettere un identificatore univoco per questa azione.

    b. **Tipo di azione**: selezionare Massaggio di posta elettronica/SMS/App Azure, Webhook, ITSM o Runbook di Automazione.

    c. **Dettagli**: in base al tipo di azione, immettere un numero di telefono, un indirizzo di posta elettronica, l'URI del webhook, l'app Azure, la connessione ITSM o il runbook di Automazione. Per l'azione ITSM, specificare anche **Elemento di lavoro** e altri campi richiesti dallo strumento ITSM.

   > [!NOTE]
   > L'azione ITSM richiede una connessione ITSM. Informazioni su come creare una [connessione ITSM](../log-analytics/log-analytics-itsmc-overview.md). 

8. Fare clic su **OK** per creare il gruppo di azione.

## <a name="manage-your-action-groups"></a>Gestire i gruppi di azione ##
Dopo la creazione, il gruppo di azione sarà visibile nella sezione **Gruppi di azione** del pannello **Monitoraggio**. Selezionare il gruppo di azione da gestire per:

* Aggiungere, modificare o rimuovere azioni.
* Eliminare il gruppo di azione.

## <a name="next-steps"></a>Passaggi successivi ##
* Altre informazioni sul [Comportamento degli avvisi SMS](monitoring-sms-alert-behavior.md).  
* Leggere le [informazioni sullo schema webhook degli avvisi del log attività](monitoring-activity-log-alerts-webhook.md).  
* Altre informazioni sul [connettore ITSM](../log-analytics/log-analytics-itsmc-overview.md)
* Altre informazioni sulla [limitazione della frequenza](monitoring-alerts-rate-limiting.md) degli avvisi.
* Leggere una [panoramica degli avvisi del log attività](monitoring-overview-alerts.md) e informazioni su come ricevere gli avvisi.  
* Informazioni su come [configurare gli avvisi ogni volta che viene inviata una notifica sull'integrità del servizio](monitoring-activity-log-alerts-on-service-notifications.md).
