---
title: "Ricevere gli avvisi del log attività nelle notifiche del servizio | Microsoft Docs"
description: Ricevere le notifiche tramite SMS, posta elettronica o webhook nel servizio di Azure.
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: bf6a98fd7e7e11764bef174f9efd0635fa7efe9a
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Creare gli avvisi del log attività per le notifiche del servizio
## <a name="overview"></a>Panoramica
Questo articolo descrive come impostare gli avvisi del log attività per le notifiche sull'integrità del servizio usando il portale di Azure.  

È possibile ricevere un avviso quando Azure invia le notifiche sull'integrità del servizio alla sottoscrizione di Azure. È possibile configurare l'avviso in base a:

- La classe di notifica sull'integrità del servizio, ad esempio evento imprevisto, manutenzione, informazioni.
- I servizi interessati.
- Le aree interessate.
- Lo stato della notifica (attivo o risolto).
- Il livello delle notifiche (informativo, avviso, errore).

È anche possibile configurare l'utente a cui deve essere inviato l'avviso:

- Selezionare un gruppo di azione esistente.
- Creare un nuovo gruppo di azione che può essere usato per avvisi futuri.

Per altre informazioni sui gruppi di azioni, vedere [Creare e gestire gruppi di azioni](monitoring-action-groups.md).

Per informazioni su come configurare gli avvisi di notifica sull'integrità del servizio usando i modelli Azure Resource Manager, vedere [Modelli di Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md).

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-by-using-the-azure-portal"></a>Creare un avviso per una notifica sull'integrità del servizio per un nuovo gruppo di azione usando il portale di Azure
1. Nel [portale](https://portal.azure.com)selezionare **Monitoraggio**.

    ![Servizio "Monitoraggio"](./media/monitoring-activity-log-alerts-on-service-notifications/home-monitor.png)

2. Nella sezione **Log attività** selezionare **Avvisi**.

    ![Scheda "Avvisi"](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades.png)

3. Selezionare il comando **Aggiungi avviso del log attività** e compilare i campi.

    ![Comando "Aggiungi avviso del log attività"](./media/monitoring-activity-log-alerts-on-service-notifications/add-activity-log-alert.png)

4. Immettere un nome nella casella **Nome avviso del log attività** e specificare una **descrizione**.

    ![Finestra di dialogo "Aggiungi avviso del log attività"](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-new-action-group.png)

5. Nella casella **Sottoscrizione** viene inserita automaticamente la sottoscrizione corrente. Questa sottoscrizione viene usata per salvare l'avviso del log attività. Questa è la sottoscrizione in cui verrà distribuita la risorsa di avviso e in cui verranno monitorati gli eventi nel log attività.

6. Selezionare il **gruppo di risorse** in cui verrà creata la risorsa di avviso. Non è il gruppo di risorse che viene monitorato dall'avviso, ma è quello in cui si trova la risorsa di avviso.

7. Nella casella **Categoria di eventi** , selezionare **Integrità dei servizi**. Facoltativamente, selezionare **Servizio**, **Area**, **Tipo**, **Stato** e **Livello** delle notifiche sull'integrità del servizio che si vuole ricevere.

8. In **Avvisi tramite** selezionare il pulsante **Nuovo** gruppo di azione. Immettere un nome nella casella **Nome gruppo di azione** e un nome nella casella **Nome breve gruppo di azione**. Viene fatto riferimento al nome breve nelle notifiche inviate all'attivazione dell'avviso.

9. Definire un elenco di ricevitori specificando i dati seguenti relativi al ricevitore:

    a. **Nome**: immettere il nome, l'alias o l'identificatore del ricevitore.

    b. **Tipo di azione**: selezionare webhook, posta elettronica o SMS.

    c. **Dettagli:** in base al tipo di azione selezionato, immettere un numero di telefono, un indirizzo di posta elettronica o l'URI del webhook.

10. Fare clic su **OK** per creare l'avviso.

Entro pochi minuti, l'avviso diventa attivo e inizia ad attivarsi in base alle condizioni specificate al momento della creazione.

Per informazioni sullo schema webhook per gli avvisi del log attività, vedere [Webhook per gli avvisi del log attività di Azure](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>Il gruppo di azione definito in questi passaggi è riutilizzabile come gruppo di azione esistente per tutte le future definizioni di avviso.
>
>

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-by-using-the-azure-portal"></a>Creare un avviso per una notifica sull'integrità del servizio per un gruppo di azione esistente usando il portale di Azure

1. Seguire i passaggi da 1 a 7 nella sezione precedente per creare una notifica sull'integrità del servizio. 

2. In **Avvisi tramite** selezionare il pulsante Gruppo di azione **esistente**. Selezionare il gruppo di azioni appropriato.

3. Fare clic su **OK** per creare l'avviso.

Entro pochi minuti, l'avviso diventa attivo e inizia ad attivarsi in base alle condizioni specificate al momento della creazione.

## <a name="manage-your-alerts"></a>Gestire gli avvisi

Dopo la creazione, l'avviso sarà visibile nella sezione **Avvisi** del pannello **Monitoraggio**. Selezionare l'avviso da gestire per:

* Modificarlo.
* Eliminarlo.
* Disabilitarlo o abilitarlo per interrompere temporaneamente o riprendere la ricezione delle notifiche relative all'avviso.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sulle [notifiche per l'integrità del servizio](monitoring-service-notifications.md).
- Informazioni sulla [limitazione della frequenza delle notifiche](monitoring-alerts-rate-limiting.md).
- Esaminare lo [schema webhook degli avvisi del log attività](monitoring-activity-log-alerts-webhook.md).
- Leggere una [panoramica degli avvisi del log attività](monitoring-overview-alerts.md) e informazioni su come ricevere gli avvisi. 
- Altre informazioni sui [gruppi di azione](monitoring-action-groups.md).

