---
title: "Creare avvisi del log attività | Microsoft Docs"
description: "Ricevere una notifica SMS, webhook e posta elettronica quando si verificano determinati eventi nel log attività."
author: anirudhcavale
manager: carmonm
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
ms.author: ancav
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: cbdb1f866c51f2a2622d14e1987660e34cb1b5ff
ms.lasthandoff: 04/03/2017


---
# <a name="create-activity-log-alerts"></a>Creare avvisi del log attività

## <a name="overview"></a>Panoramica
Gli avvisi del log attività sono risorse di Azure, pertanto possono essere gestite tramite il portale di Azure o Azure Resource Manager (ARM). Questo articolo illustra come usare il portale di Azure per configurare gli avvisi sugli eventi del log attività.

È possibile ricevere avvisi sulle operazioni eseguite sulle risorse nella sottoscrizione o sugli eventi di integrità del servizio che possono influire sull'integrità delle risorse. Un avviso del log attività esegue il monitoraggio degli eventi del log attività per la sottoscrizione specifica in cui viene distribuito l'avviso.

È possibile configurare l'avviso in base a:
* Categoria di eventi (per [eventi di integrità del servizio fare clic qui](monitoring-activity-log-alerts-on-service-notifications.md))
- Gruppo di risorse
- Risorsa
- Tipo di risorsa
- Nome operazione
- Livello delle notifiche (dettagliato, informativo, avviso, errore, critico)
- Status
- Evento avviato da

È inoltre possibile configurare a chi deve essere inviato l'avviso:
* Selezionare un gruppo di azione esistente
- Creare un nuovo gruppo di azione (che può essere usato successivamente per avvisi futuri)

Altre informazioni sui [gruppi di azione sono disponibili qui](monitoring-action-groups.md)

È possibile configurare e ottenere informazioni sugli avvisi di notifica dello stato del servizio usando
* [Portale di Azure](monitoring-activity-log-alerts.md)
- [Modelli di Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)

## <a name="create-an-alert-on-an-activity-log-event-with-a-new-action-group-with-the-azure-portal"></a>Creare un avviso per un evento del log attività con un nuovo gruppo di azione con il portale di Azure
1.    Nel [portale](https://portal.azure.com), passare al servizio **Monitoraggio**

    ![Monitoraggio](./media/monitoring-activity-log-alerts/home-monitor.png)
2.    Fare clic sull'opzione **Monitoraggio** per aprire il relativo pannello. Per prima cosa si apre la sezione **Log di attività**.

3.    Fare clic sulla sezione **Avvisi**.

    ![Avvisi](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.    Selezionare il comando **Aggiungi avviso del log attività** e compilare i campi

5.    Scegliere un **nome** e una **descrizione** per l'avviso del log attività. Tali valori verranno visualizzati nelle notifiche inviate quando viene generato questo avviso.

    ![Aggiungere-un-avviso](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

6.    Il campo **Sottoscrizione** deve essere automaticamente compilato con la sottoscrizione attualmente in uso. Questa è la sottoscrizione che verrà monitorata e per la quale verranno distribuiti gli avvisi.

    ![Aggiungere-avvisi-a-nuovo-gruppo-di-azione](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

7.    Scegliere il **Gruppo di risorse** a cui verrà associato l'avviso nella **Sottoscrizione**.

8.    Fornire i valori **Categoria di eventi**, **Gruppo di risorse**, **Risorse**, **Tipo di risorsa**, **Nome operazione**, **Livello**, **Stato** e **Evento avviato da** per identificare gli eventi che devono essere monitorati dall'avviso.

9.    Creare un **nuovo** gruppo di azione assegnando un **nome** e un **nome breve**; nelle notifiche inviate quando viene attivato l'avviso verrà fatto riferimento al nome breve.

10.    Definire quindi un elenco di destinatari, fornendo i dati del ricevitore seguenti

    a. **Nome:** nome, alias o identificatore del ricevitore.

    b. **Tipo di azione:** scegliere di contattare il ricevitore tramite SMS, posta elettronica o Webhook

    c. **Dettagli:** in base al tipo di azione selezionata, fornire un numero di telefono, un indirizzo di posta elettronica o l'URI del webhook.

11.    Al termine fare clic su **OK** per creare l'avviso.

Dopo pochi minuti l'avviso è funzionante e si attiva come descritto in precedenza.

Per altre informazioni sullo schema webhook per gli avvisi del log attività [fare clic qui](monitoring-activity-log-alerts-webhook.md)

>[!NOTE]
>Il gruppo di azione definito in questi passaggi sarà riutilizzabile, come un gruppo di azione esistente, per tutte le future definizioni dell'avviso.
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-with-the-azure-portal"></a>Creare un avviso per un evento del log attività con un gruppo di azione esistente con il portale di Azure
1.    Nel [portale](https://portal.azure.com), passare al servizio **Monitoraggio**

    ![Monitoraggio](./media/monitoring-activity-log-alerts/home-monitor.png)
2.    Fare clic sull'opzione **Monitoraggio** per aprire il relativo pannello. Per prima cosa si apre la sezione **Log di attività**.

3.    Fare clic sulla sezione **Avvisi**.

    ![Avvisi](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.    Selezionare il comando **Aggiungi avviso del log attività** e compilare i campi

5.    Scegliere un **nome** e una **descrizione** per l'avviso del log attività. Tali valori verranno visualizzati nelle notifiche inviate quando viene generato questo avviso.

    ![Aggiungere-un-avviso](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)
6.    Il campo **Sottoscrizione** deve essere automaticamente compilato con la sottoscrizione attualmente in uso.

    ![Aggiungere-un-avviso-esistente-a-un-gruppo di azione](./media/monitoring-activity-log-alerts/activity-log-alert-existing-action-group.png)
7.    Scegliere il **Gruppo di risorse** per questo avviso.

8.    Fornire i valori **Categoria di eventi**, **Gruppo di risorse**, **Risorse**, **Tipo di risorsa**, **Nome operazione**, **Livello**, **Stato** e **Evento avviato da** per stabilire l'ambito degli eventi a cui applicare l'avviso.

9.    Scegliere la **Notifica tramite** un **gruppo di azione esistente**. Selezionare il gruppo di azione corrispondente.

10.    Al termine fare clic su **OK** per creare l'avviso.

Dopo pochi minuti l'avviso è funzionante e si attiva come descritto in precedenza.

## <a name="managing-your-alerts"></a>Gestione degli avvisi

Dopo aver creato un avviso, esso sarà visibile nella sezione degli avvisi del servizio di monitoraggio. Selezionare l'avviso che si desidera gestire, per:
* **Modificarlo**.
* **Eliminarlo**.
* **Disabilitarlo** o **abilitarlo** per interrompere temporaneamente o riprendere la ricezione delle notifiche relative all'avviso.

## <a name="next-steps"></a>Passaggi successivi:
Ottenere dei [cenni preliminari sugli avvisi](monitoring-overview-alerts.md)  
Esaminare lo [schema webhook degli avvisi del log attività](monitoring-activity-log-alerts-webhook.md). Altre informazioni sui [gruppi di azione](monitoring-action-groups.md)  
Informazioni su [Notifiche sull'integrità del servizio](monitoring-service-notifications.md)

