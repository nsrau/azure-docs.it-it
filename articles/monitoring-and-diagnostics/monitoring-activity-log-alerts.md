---
title: "Creare avvisi del log attività | Microsoft Docs"
description: "Ricevere una notifica tramite SMS, webhook e posta elettronica quando si verificano determinati eventi nel log attività."
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
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 3885469ec0e1fcc31386dd0ad7fe6cb5d03ab28e
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---
# <a name="create-activity-log-alerts"></a>Creare avvisi del log attività

## <a name="overview"></a>Panoramica
Gli avvisi del log attività vengono attivati quando si verifica un nuovo evento del log attività che corrisponde alle condizioni specificate nell'avviso. Si tratta di risorse di Azure e possono essere create usando un modello di Azure Resource Manager. Possono essere create, aggiornate o eliminate anche nel portale di Azure. In questo articolo vengono presentati i concetti alla base degli avvisi del log attività. Questo articolo illustra come usare il portale di Azure per configurare un avviso sugli eventi del log attività.

In genere, si creano avvisi del log attività per ricevere notifiche quando:

* Si verificano modifiche specifiche nelle risorse nella sottoscrizione di Azure. Questi avvisi sono spesso limitati a risorse o gruppi di risorse specifici. Potrebbe ad esempio essere utile ricevere una notifica quando viene eliminata una macchina virtuale in myProductionResourceGroup o se vengono assegnati nuovi ruoli a un utente nella sottoscrizione.
* Si verifica un evento di integrità del servizio. Gli eventi di integrità del servizio includono la notifica di eventi imprevisti e di manutenzione che si applicano alle risorse nella sottoscrizione.

In ogni caso, un avviso del log attività monitora solo degli eventi nella sottoscrizione in cui è stato creato l'evento.

È possibile configurare un avviso del log attività in base a qualsiasi proprietà di primo livello nell'oggetto JSON per un evento del log attività. Tuttavia il portale mostra le opzioni più comuni:

- **Categoria**: amministrazione, integrità del servizio, scalabilità automatica e indicazione. Per altre informazioni, vedere [Panoramica del log attività di Azure](./monitoring-overview-activity-logs.md#categories-in-the-activity-log). Per altre informazioni sugli eventi di integrità del servizio, vedere [Ricevere gli avvisi del log attività sulle notifiche del servizio](./monitoring-activity-log-alerts-on-service-notifications.md).
- **Gruppo di risorse**
- **Risorsa**
- **Tipo di risorsa**
- **Nome operazione**: il nome dell'operazione del controllo degli accessi in base al ruolo di Resource Manager.
- **Livello**: il livello di gravità dell'evento (dettagliato, informativo, avvertenza, errore o critico).
- **Stato**: lo stato dell'evento, in genere Avviato, Non riuscito o Riuscito.
- **Evento avviato da**: noto anche come "chiamante". L'indirizzo di posta elettronica o un identificatore di Azure Active Directory dell'utente che ha eseguito l'operazione.

>[!NOTE]
>È necessario specificare nell'avviso almeno due dei criteri precedenti e uno di questi deve rappresentare la categoria. Non è possibile creare un avviso che viene attivato ogni volta che si crea un evento nei log attività.
>
>

Quando un avviso del log di attività viene attivato, usa un gruppo di azione per generare azioni o notifiche. Un gruppo di azione è un set riutilizzabile di ricevitori di notifica, ad esempio gli indirizzi di posta elettronica, gli URL webhook o i numeri di telefono di SMS. Più avvisi possono fare riferimento ai ricevitori per centralizzare e raggruppare i canali di notifica. Quando si definisce l'avviso del log di attività, sono disponibili due opzioni. È possibile:

* Usare un gruppo di azione esistente nell'avviso del log attività. 
* Creare un nuovo gruppo di azione. 

Per altre informazioni sui gruppi di azione, vedere [Creare e gestire gruppi di azione nel portale di Azure](monitoring-action-groups.md).

Per altre informazioni sulle notifiche sull'integrità del servizio, vedere [Ricevere gli avvisi del log attività per le notifiche sull'integrità del servizio](monitoring-activity-log-alerts-on-service-notifications.md).

## <a name="create-an-alert-on-an-activity-log-event-with-a-new-action-group-by-using-the-azure-portal"></a>Creare un avviso per un evento del log attività con un nuovo gruppo di azione usando il portale di Azure
1. Nel [portale](https://portal.azure.com)selezionare **Monitoraggio**.

    ![Servizio "Monitoraggio"](./media/monitoring-activity-log-alerts/home-monitor.png)
2. Nella sezione **Log attività** selezionare **Avvisi**.

    ![Scheda "Avvisi"](./media/monitoring-activity-log-alerts/alerts-blades.png)
3. Selezionare il comando **Aggiungi avviso del log attività** e compilare i campi.

4. Immettere un nome per la casella **Nome avviso del log attività** e selezionare una **descrizione**.

    ![Comando "Aggiungi avviso del log attività"](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

5. Nella casella **Sottoscrizione** viene inserita automaticamente la sottoscrizione corrente. Il gruppo di azione verrà salvato in questa sottoscrizione. Questa è la sottoscrizione in cui verrà distribuita la risorsa di avviso e in cui verranno monitorati gli eventi del log attività.

    ![Finestra di dialogo "Aggiungi avviso del log attività"](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

6. Selezionare il **gruppo di risorse** in cui verrà creata la risorsa di avviso. Non è il gruppo di risorse che viene monitorato dall'avviso, ma è quello in cui si trova la risorsa di avviso.

7. Selezionare facoltativamente una **categoria di eventi** per modificare i filtri aggiuntivi visualizzati. Per gli eventi amministrativi, i filtri includono **Gruppo di risorse**, **Risorse**, **Tipo di risorsa**, **Nome dell'operazione**, **Livello**, **Stato** ed **Evento avviato da**. Questi valori identificano gli eventi che devono essere monitorati da questo avviso.

    >[!NOTE]
    >È necessario specificare nell'avviso almeno uno dei criteri precedenti. Non è possibile creare un avviso che viene attivato ogni volta che si crea un evento nei log attività.
    >
    >

8. Immettere un nome nella casella **Nome gruppo di azione** e un nome nella casella **Nome breve gruppo di azione**. Il nome breve viene usato al posto del nome completo di un gruppo di azione quando le notifiche vengono inviate usando questo gruppo.

9.  Definire un elenco di azioni, fornendo i dati dell'azione seguenti:

    a. **Nome:**: immettere il nome, l'alias o l'identificatore dell'azione.

    b. **Tipo di azione**: selezionare webhook, posta elettronica o SMS.

    c. **Dettagli**: in base al tipo di azione immettere un numero di telefono, un indirizzo di posta elettronica o l'URI del webhook.

10. Fare clic su **OK** per creare l'avviso.

La propagazione completa dell'avviso richiede alcuni minuti, quindi l'avviso diventa attivo. Si attiva quando nuovi eventi corrispondono ai criteri dell'avviso.

Per altre informazioni, vedere [Informazioni sullo schema webhook degli avvisi del log attività](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>Il gruppo di azione definito in questi passaggi è riutilizzabile come gruppo di azione esistente per tutte le future definizioni di avviso.
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-by-using-the-azure-portal"></a>Creare un avviso per un evento del log attività con un gruppo di azione esistente usando il portale di Azure
1. Seguire i passaggi da 1 a 7 nella sezione precedente per creare l'avviso del log attività.

2. In **Notifica tramite** selezionare il pulsante Gruppo di azione **esistente**. Selezionare un gruppo di azione esistente dall'elenco.

3. Fare clic su **OK** per creare l'avviso.

La propagazione completa dell'avviso richiede alcuni minuti, quindi l'avviso diventa attivo. Si attiva quando nuovi eventi corrispondono ai criteri dell'avviso.

## <a name="manage-your-alerts"></a>Gestire gli avvisi

Dopo la creazione, l'avviso sarà visibile nella sezione Avvisi del pannello Monitoraggio. Selezionare l'avviso da gestire per:

* Modificarlo.
* Eliminarlo.
* Disabilitarlo o abilitarlo per interrompere temporaneamente o riprendere la ricezione delle notifiche relative all'avviso.

## <a name="next-steps"></a>Passaggi successivi
- Ottenere una [panoramica degli avvisi](monitoring-overview-alerts.md).
- Informazioni sulla [limitazione della frequenza delle notifiche](monitoring-alerts-rate-limiting.md).
- Esaminare lo [schema webhook degli avvisi del log attività](monitoring-activity-log-alerts-webhook.md).
- Altre informazioni sui [gruppi di azione](monitoring-action-groups.md).  
- Informazioni sulle [notifiche per l'integrità del servizio](monitoring-service-notifications.md).
- Creare un [avviso del log attività per monitorare tutte le operazioni del motore di scalabilità automatica della sottoscrizione](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert).
- Creare un [avviso del log attività per monitorare tutte le operazioni di scalabilità automatica in riduzione e in aumento non riuscite per la sottoscrizione](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).

