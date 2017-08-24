---
title: "Creare avvisi del log attività | Microsoft Docs"
description: "Ricevere una notifica SMS, webhook e posta elettronica quando si verificano determinati eventi nel log attività."
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
ms.date: 08/03/2017
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: c44ecc0ee61c58ab7590e9a21a5fe002068c6b83
ms.contentlocale: it-it
ms.lasthandoff: 08/04/2017

---
# <a name="create-activity-log-alerts"></a>Creare avvisi del log attività

## <a name="overview"></a>Panoramica
Gli avvisi del log attività vengono attivati quando si verifica un nuovo evento di log attività che corrisponde alle condizioni specificate nell'avviso. Questi avvisi sono risorse di Azure, pertanto possono essere creati usando un modello di Resource Manager e possono essere creati, aggiornati o eliminati nel portale di Azure. Questo articolo illustra i concetti di base degli avvisi del log attività e spiega come usare il portale di Azure per impostare un avviso per gli eventi del log attività.

Gli avvisi del log attività vengono in genere creati per uno dei due scopi seguenti:
1. Per ricevere una notifica quando si verificano modifiche specifiche nelle risorse presenti nella sottoscrizione di Azure. Questi avvisi sono spesso limitati a risorse o gruppi di risorse specifici. Potrebbe ad esempio essere utile ricevere una notifica quando viene eliminata una macchina virtuale in myProductionResourceGroup o se vengono assegnati nuovi ruoli a un utente nella sottoscrizione.
2. Per ricevere una notifica quando si verifica un evento di integrità del servizio. Gli eventi di integrità del servizio includono la notifica di eventi imprevisti e di eventi di manutenzione che si applicano alle risorse nella sottoscrizione.

L'avviso del log attività esegue in entrambi i casi il monitoraggio solo degli eventi nella sottoscrizione in cui è stato creato.

È possibile configurare un avviso del log attività in base a qualsiasi proprietà di primo livello nell'oggetto JSON per un evento del log attività, tuttavia il portale mostra le opzioni più comuni:
- **Categoria**: amministrazione, integrità del servizio, scalabilità automatica, indicazione. [Vedere questo articolo per informazioni sulle categorie del log attività](./monitoring-overview-activity-logs.md#categories-in-the-activity-log). [Altre informazioni sugli eventi di integrità del servizio sono disponibili qui](./monitoring-activity-log-alerts-on-service-notifications.md).
- **Gruppo di risorse**
- **Risorsa**
- **Tipo di risorsa**
- **Nome operazione**: il nome dell'operazione di Controllo degli accessi in base al ruolo di Resource Manager.
- **Livello**: il livello di gravità dell'evento (dettagliato, informativo, avvertenza, errore, critico)
- **Stato**: lo stato dell'evento, in genere "Avviato," "Non riuscito" o "Riuscito"
- **Evento avviato da**: noto anche come "chiamante". L'indirizzo di posta elettronica o un identificatore di Active Directory dell'utente che ha eseguito l'operazione.

>[!NOTE]
>È necessario specificare nell'avviso almeno due dei criteri precedenti e uno di questi deve rappresentare la categoria. Non è possibile creare un avviso che viene attivato ogni volta che viene creato un evento nei log attività.
>
>

Quando un avviso del log di attività viene attivato, usa un gruppo di azioni per generare azioni o notifiche. Un gruppo di azioni è un set riutilizzabile di destinatari della notifica (indirizzi di posta elettronica, URL di webhook o numeri di telefono SMS) a cui più avvisi possono fare riferimento per centralizzare e raggruppare i canali di notifica. È possibile usare nell'avviso del log attività un gruppo di azioni esistente o crearne uno nuovo mentre si definisce l'avviso del log attività. Altre informazioni sui [gruppi di azione sono disponibili qui](monitoring-action-groups.md)

Altre informazioni sugli avvisi del log attività per le notifiche di integrità del servizio sono disponibili [qui](monitoring-activity-log-alerts-on-service-notifications.md).

## <a name="create-an-alert-on-an-activity-log-event-with-a-new-action-group-with-the-azure-portal"></a>Creare un avviso per un evento del log attività con un nuovo gruppo di azioni usando il portale di Azure
1.  Nel [portale](https://portal.azure.com) passare al pannello **Monitoraggio**.

    ![Monitorare](./media/monitoring-activity-log-alerts/home-monitor.png)
2.  Fare clic sull'opzione **Monitoraggio** per aprire il relativo pannello. Verrà aperto per prima cosa il pannello **Log attività**.

3.  Fare clic sul pannello **Avvisi**.

    ![Avvisi](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.  Selezionare il comando **Aggiungi avviso del log attività** e compilare i campi.

5.  Scegliere un **nome** e una **descrizione** per l'avviso del log attività.

    ![Aggiungere un avviso](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

6.  L'avviso del log attività viene salvato nella **Sottoscrizione** selezionata. Viene compilato automaticamente nella sottoscrizione attualmente in uso. Questa è la sottoscrizione in cui viene distribuita la risorsa dell'avviso e in cui vengono monitorati gli avvisi del log attività.

    ![Aggiungere-avvisi-a-nuovo-gruppo-di-azione](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

7.  Scegliere il **gruppo di risorse** in cui verrà creata la risorsa dell'avviso. Questo non è il gruppo di risorse che verrà monitorato *dall'*avviso, bensì il gruppo di risorse in cui la *risorsa dell'avviso stessa verrà distribuita.*

8.  Selezionare facoltativamente una **Categoria evento**, che modifica il set di filtri aggiuntivi visualizzato. Per gli eventi amministrativi, questo set include le opzioni per filtrare in base ai valori dei campi **Gruppo di risorse**, **Risorsa**, **Tipo di risorsa**, **Nome operazione**, **Livello**, **Stato** ed **Evento avviato da** per identificare quali eventi devono essere monitorati dall'avviso.

>[!NOTE]
>È necessario specificare nell'avviso almeno uno dei criteri precedenti. Non è possibile creare un avviso che viene attivato ogni volta che viene creato un evento nei log attività.
>
>

9.  Creare un **Nuovo** gruppo di risorse assegnandogli un **Nome** e un **Nome breve**. Il nome breve sarà indicato nelle notifiche quando viene attivato l'avviso.

10. Definire quindi un elenco di azioni, fornendo i dati dell'azione seguenti

    a. **Nome:** nome, alias o identificatore dell'azione.

    b. **Tipo di azione:** scegliere il tipo di azione: webhook, posta elettronica o SMS

    c. **Dettagli:** in base al tipo di azione selezionato, specificare un numero di telefono, un indirizzo di posta elettronica o l'URI del webhook.

11. Al termine fare clic su **OK** per creare l'avviso.

L'avviso richiederà alcuni minuti per completare la propagazione e successivamente diventerà attivo e si attiverà quando si verificheranno nuovi eventi corrispondenti ai criteri dell'avviso.

[Per informazioni dettagliate sullo schema di webhook per gli avvisi del log attività, vedere questo documento](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>Il gruppo di azioni definito in questi passaggi è riutilizzabile come gruppo di azioni esistente, per tutte le future definizioni di avviso.
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-with-the-azure-portal"></a>Creare un avviso per un evento del log attività con un gruppo di azioni esistente usando il portale di Azure
1.  Nel [portale](https://portal.azure.com) passare al pannello **Monitoraggio**.

    ![Monitorare](./media/monitoring-activity-log-alerts/home-monitor.png)
2.  Fare clic sull'opzione **Monitoraggio** per aprire il relativo pannello. Per prima cosa si apre la sezione **Log di attività**.

3.  Fare clic sulla sezione **Avvisi**.

    ![Avvisi](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.  Selezionare il comando **Aggiungi avviso del log attività** e compilare i campi.

5.  Scegliere un **Nome** e una **Descrizione** per l'avviso del log attività. Tali informazioni verranno visualizzate nelle notifiche inviate quando viene generato l'avviso.

    ![Aggiungere un avviso](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)
6.  L'avviso del log attività viene salvato nella **Sottoscrizione** selezionata. Viene compilato automaticamente nella sottoscrizione attualmente in uso. Questa è la sottoscrizione in cui viene distribuita la risorsa dell'avviso e in cui vengono monitorati gli avvisi del log attività.

    ![Aggiungere un avviso esistente a un gruppo di azione](./media/monitoring-activity-log-alerts/activity-log-alert-existing-action-group.png)
7.  Scegliere il **gruppo di risorse** in cui verrà creata la risorsa dell'avviso. Questo non è il gruppo di risorse che verrà monitorato *dall'*avviso, bensì il gruppo di risorse in cui la *risorsa dell'avviso stessa verrà distribuita.*

8.  Selezionare facoltativamente una **Categoria evento**, che modifica il set di filtri aggiuntivi visualizzato. Per gli eventi amministrativi, questo set include le opzioni per filtrare in base ai valori dei campi **Gruppo di risorse**, **Risorsa**, **Tipo di risorsa**, **Nome operazione**, **Livello**, **Stato** ed **Evento avviato da** per identificare quali eventi devono essere monitorati dall'avviso.

9.  Scegliere la **Notifica tramite** un **gruppo di azione esistente**. Selezionare un gruppo di azioni esistente.

10. Al termine fare clic su **OK** per creare l'avviso.

L'avviso richiederà alcuni minuti per completare la propagazione e successivamente diventerà attivo e si attiverà quando si verificheranno nuovi eventi corrispondenti ai criteri dell'avviso.

## <a name="managing-your-alerts"></a>Gestione degli avvisi

L'avviso creato viene visualizzato nella sezione Avvisi del pannello Monitoraggio. Selezionare l'avviso che si desidera gestire per:
* **Modificarlo**.
* **Eliminarlo**.
* **Disabilitarlo** o **abilitarlo** per interrompere temporaneamente o riprendere la ricezione delle notifiche relative all'avviso.

## <a name="next-steps"></a>Passaggi successivi
- Ottenere dei [cenni preliminari sugli avvisi](monitoring-overview-alerts.md)
- Informazioni sulla [limitazione della frequenza delle notifiche](monitoring-alerts-rate-limiting.md)
- Esaminare lo [schema webhook degli avvisi del log attività](monitoring-activity-log-alerts-webhook.md)
- Altre informazioni sui [gruppi di azioni](monitoring-action-groups.md)  
- Informazioni sulle [notifiche sull'integrità del servizio](monitoring-service-notifications.md)
- [Creare un avviso di log attività per monitorare tutte le operazioni del motore di scalabilità automatica della sottoscrizione.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Creare un avviso di log attività per monitorare tutte le operazioni di scalabilità automatica in riduzione e in aumento non riuscite per la sottoscrizione.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

