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
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 202e7ec116e5e49beaad8c2d570a3659236e9b0f
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Creare gli avvisi del log attività nelle notifiche del servizio
## <a name="overview"></a>Panoramica
Questo articolo descrive come impostare gli avvisi del log attività per le notifiche sull'integrità del servizio usando il portale di Azure.  

È possibile ricevere un avviso quando Azure invia le notifiche sull'integrità del servizio alla sottoscrizione di Azure.  
È possibile configurare l'avviso in base agli elementi seguenti:
- Classe di notifica dell'integrità del servizio (Evento imprevisto, Manutenzione, Informazioni e così via)
- I servizi interessati
- Le aree interessate
- Stato della notifica (Attivo o Risolto)
- Livello delle notifiche (Informativo, Avviso, Errore)

È anche possibile configurare l'utente a cui deve essere inviato l'avviso:
- Selezionare un gruppo di azione esistente
- Creare un nuovo gruppo di azione (che può essere usato successivamente per avvisi futuri)

Altre informazioni sui gruppi di azione sono disponibili [qui](monitoring-action-groups.md)

Per informazioni su come configurare gli avvisi di notifica di integrità del servizio usando i modelli Azure Resource Manager: [modelli di Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-with-the-azure-portal"></a>Creare un avviso per una notifica di integrità del servizio per un nuovo gruppo di azione con il portale di Azure
1.  Nel [portale](https://portal.azure.com) passare al servizio **Monitoraggio**

    ![Monitoraggio](./media/monitoring-activity-log-alerts-on-service-notifications/home-monitor.png)

2.  Fare clic sull'opzione **Monitoraggio** per aprire il relativo pannello. Per prima cosa si apre la sezione **Log di attività**.

3.  Fare clic sulla sezione **Avvisi**.

    ![Avvisi](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades.png)

4.  Selezionare il comando **Aggiungi avviso del log attività** e compilare i campi

    ![Aggiungere un avviso](./media/monitoring-activity-log-alerts-on-service-notifications/add-activity-log-alert.png)

5.  Specificare un **Nome** e una **Descrizione** per l'avviso del log attività.

    ![Aggiungere un avviso a nuovo gruppo di azione](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-new-action-group.png)

6.  L'avviso del log attività verrà salvato in **Sottoscrizione**. Verrà compilato automaticamente nella sottoscrizione attualmente in uso. Questa è la sottoscrizione che verrà monitorata e per la quale verranno distribuiti gli avvisi.

7.  Scegliere il **Gruppo di risorse** a cui verrà associato l'avviso nella **Sottoscrizione**.

8.  In **Categoria evento** selezionare l'opzione 'Integrità del servizio'. Specificare **Servizio, Area, Tipo, Stato** e **Livello** delle notifiche sull'integrità del servizio che si vuole ricevere.

9.  Creare un gruppo di azione **Nuovo** specificando **Nome** e **Nome breve**; il nome breve viene visualizzato nelle notifiche quando viene generato l'avviso.

10. Definire quindi un elenco di ricevitori specificando i dati seguenti relativi al ricevitore

    a. **Nome:** nome, alias o identificatore del ricevitore.

    b. **Tipo azione:** scegliere di contattare il ricevitore tramite SMS, posta elettronica o webhook

    c. **Dettagli:** in base al tipo di azione selezionata, specificare un numero di telefono, un indirizzo di posta elettronica o l'URI del webhook.

11. Al termine fare clic su **OK** per creare l'avviso.

Dopo pochi minuti l'avviso è funzionante e si attiva come descritto in precedenza.

Per altre informazioni sullo schema webhook per gli avvisi del log attività [fare clic qui](monitoring-activity-log-alerts-webhook.md)

>[!NOTE]
>Il gruppo di azioni definito in questi passaggi sarà riutilizzabile come gruppo di azioni esistente per tutte le definizioni di avviso future.
>
>

## <a name="create-an-alert-on-a-service-health-notification-using-an-existing-action-group-with-the-azure-portal"></a>Creare un avviso per una notifica di integrità del servizio usando un gruppo di azioni esistente con il portale di Azure
1.  Nel [portale](https://portal.azure.com), passare al servizio **Monitoraggio**

    ![Monitoraggio](./media/monitoring-activity-log-alerts-on-service-notifications/home-monitor.png)
2.  Fare clic sull'opzione **Monitoraggio** per aprire il relativo pannello. Per prima cosa si apre la sezione **Log di attività**.

3.  Fare clic sulla sezione **Avvisi**.

    ![Avvisi](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades.png)
4.  Selezionare il comando **Aggiungi avviso del log attività** e compilare i campi

    ![Aggiungere un avviso](./media/monitoring-activity-log-alerts-on-service-notifications/add-activity-log-alert.png)
5.  Specificare un **Nome** e una **Descrizione** per l'avviso del log attività.

    ![Aggiungere un avviso esistente a un gruppo di azione](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-existing-action-group.png)
6.  L'avviso del log attività verrà salvato in **Sottoscrizione**. Verrà compilato automaticamente nella sottoscrizione attualmente in uso. Questa è la sottoscrizione che verrà monitorata e per la quale verranno distribuiti gli avvisi.

7.  Scegliere il **Gruppo di risorse** a cui verrà associato l'avviso nella **Sottoscrizione**.

8.  In **Categoria evento** selezionare l'opzione 'Integrità del servizio'. Specificare **Servizio, Area, Tipo, Stato** e **Livello** delle notifiche sull'integrità del servizio che si vuole ricevere.

9.  Scegliere la **Notifica tramite** un **gruppo di azione esistente**. Selezionare il gruppo di azioni appropriato.

10. Al termine fare clic su **OK** per creare l'avviso.

Dopo pochi minuti l'avviso è funzionante e si attiva come descritto in precedenza.

## <a name="managing-your-alerts"></a>Gestione degli avvisi

Dopo aver creato un avviso, esso sarà visibile nella sezione Avvisi del servizio Monitoraggio. Selezionare l'avviso che si vuole gestire per:
* **Modificarlo**.
* **Eliminarlo**.
* **Disabilitarlo** o **abilitarlo** per interrompere temporaneamente o riprendere la ricezione delle notifiche relative all'avviso.

## <a name="next-steps"></a>Passaggi successivi:
- Informazioni sulle [notifiche sull'integrità del servizio](monitoring-service-notifications.md)
- Informazioni sulla [limitazione della frequenza delle notifiche](monitoring-alerts-rate-limiting.md)
- Esaminare lo [schema webhook degli avvisi del log attività](monitoring-activity-log-alerts-webhook.md)
- Ottenere una [panoramica degli avvisi del log attività](monitoring-overview-alerts.md) e informazioni sulla ricezione degli avvisi  
- Altre informazioni sui [gruppi di azioni](monitoring-action-groups.md)

