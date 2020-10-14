---
title: Analizzare i log attività usando i log di Monitoraggio di Azure | Microsoft Docs
description: Informazioni su come analizzare i log attività di Azure Active Directory con i log di Monitoraggio di Azure
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4535ae65-8591-41ba-9a7d-b7f00c574426
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: a48a72aa021a17c59adb86bece66cec966e234bd
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92056141"
---
# <a name="analyze-azure-ad-activity-logs-with-azure-monitor-logs"></a>Analizzare i log attività di Azure AD con i log di Monitoraggio di Azure

Dopo aver [integrato i log attività di Azure AD con i log di Monitoraggio di Azure](howto-integrate-activity-logs-with-log-analytics.md), è possibile usare le funzionalità dei log di Monitoraggio di Azure per ottenere informazioni dettagliate sull'ambiente. È anche possibile installare le [viste di analisi dei log per i log attività di Azure AD](howto-install-use-log-analytics-views.md) per ottenere l'accesso a report predefiniti sugli eventi di controllo e di accesso nell'ambiente in uso.

Questo articolo descrive come analizzare i log attività di Azure AD nell'area di lavoro Log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prerequisiti 

Per seguire la procedura, è necessario:

* Un'area di lavoro Log Analytics nella sottoscrizione di Azure. Informazioni su [come creare un'area di lavoro Log Analytics](../../azure-monitor/learn/quick-create-workspace.md).
* Completare prima di tutto la procedura per [indirizzare i log attività di Azure AD all'area di lavoro Log Analytics](howto-integrate-activity-logs-with-log-analytics.md).
*  [Accedere](../../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions) all'area di lavoro Log Analytics
* I ruoli seguenti in Azure Active Directory se si accede a Log Analytics tramite il portale di Azure Active Directory.
    - Amministrazione della protezione
    - Ruolo con autorizzazioni di lettura per la sicurezza
    - Lettore di report
    - Amministratore globale
    
## <a name="navigate-to-the-log-analytics-workspace"></a>Passare all'area di lavoro Log Analytics

1. Accedere al [portale di Azure](https://portal.azure.com). 

2. Selezionare **Azure Active Directory** e quindi selezionare **Log** nella sezione **Monitoraggio** per aprire l'area di lavoro Log Analytics. L'area di lavoro verrà aperta con una query predefinita.

    ![Query predefinita](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>Visualizzare lo schema per i log attività di Azure AD

I log vengono inviati alle tabelle **AuditLogs** e **SigninLogs** nell'area di lavoro. Per visualizzare lo schema per queste tabelle:

1. Dalla visualizzazione della query predefinita nella sezione precedente, selezionare **Schema** ed espandere l'area di lavoro. 

2. Espandere la sezione **Gestione log** e quindi **AuditLogs** o **SignInLogs** per visualizzare lo schema del log.
    ![Log di controllo](./media/howto-analyze-activity-logs-log-analytics/auditlogschema.png) ![Log di accesso](./media/howto-analyze-activity-logs-log-analytics/signinlogschema.png)

## <a name="query-the-azure-ad-activity-logs"></a>Eseguire query sui log attività di Azure AD

Ora che i log sono disponibili nell'area di lavoro, è possibile eseguire query su di essi. Ad esempio, per ottenere le principali applicazioni usate nell'ultima settimana, sostituire la query predefinita con la seguente e selezionare **Esegui**

```
SigninLogs 
| where CreatedDateTime >= ago(7d)
| summarize signInCount = count() by AppDisplayName 
| sort by signInCount desc 
```

Per ottenere i principali eventi di controllo nell'ultima settimana, usare la query seguente:

```
AuditLogs 
| where TimeGenerated >= ago(7d)
| summarize auditCount = count() by OperationName 
| sort by auditCount desc 
```
## <a name="alert-on-azure-ad-activity-log-data"></a>Avvisi per i dati dei log attività di Azure AD

È anche possibile impostare avvisi nella query. Ad esempio, per configurare un avviso quando più di 10 applicazioni sono state usate nell'ultima settimana:

1. Nell'area di lavoro selezionare **Imposta avviso** per aprire la pagina **Crea regola**.

    ![imposta avviso](./media/howto-analyze-activity-logs-log-analytics/setalert.png)

2. Selezionare i **criteri di avviso** predefiniti creati nell'avviso e aggiornare il valore **Soglia** nella metrica predefinita su 10.

    ![Criteri di avviso](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. Immettere un nome e una descrizione per l'avviso, quindi scegliere il livello di gravità. In questo esempio, si potrebbe impostare il livello **Informativo**.

4. Selezionare il **Gruppo di azioni** che riceverà l'avviso quando si verifica il segnale. È possibile scegliere di inviare notifiche al team tramite posta elettronica o SMS oppure è possibile automatizzare l'azione con webhook, funzioni di Azure o app per la logica. Vedere altre informazioni sulla [creazione e gestione di gruppi di azioni nel portale di Azure](../../azure-monitor/platform/action-groups.md).

5. Dopo aver configurato l'avviso, selezionare **Crea avviso** per abilitarlo. 

## <a name="use-pre-built-workbooks-for-azure-ad-activity-logs"></a>Usare cartelle di lavoro predefinite per Azure AD log attività

Le cartelle di lavoro di forniscono diversi report correlati a scenari comuni che coinvolgono gli eventi di controllo, di accesso e di provisioning. È possibile anche impostare un avviso per tutti i dati disponibili nei report, seguendo la procedura descritta nella sezione precedente.

* **Analisi del provisioning**: questa [cartella di lavoro](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-log-analytics) Mostra i report relativi alle attività di provisioning di controllo, ad esempio il numero di nuovi utenti di cui è stato effettuato il provisioning e il provisioning degli errori, il numero di utenti aggiornati e gli errori di aggiornamento e il numero di utenti di cui è stato effettuato il deprovisioning e gli errori    
* **Eventi di accesso**: questa cartella di lavoro Mostra i report più rilevanti correlati al monitoraggio dell'attività di accesso, ad esempio accessi in base all'applicazione, all'utente, al dispositivo, oltre a una visualizzazione riepilogativa che verifica il numero di accessi nel tempo.
* **Informazioni dettagliate sull'accesso condizionale**: la [cartella di lavoro](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting) di report e informazioni sull'accesso condizionale consente di comprendere l'effetto dei criteri di accesso condizionale nell'organizzazione nel tempo. 

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione alle query nei log di Monitoraggio di Azure](../../azure-monitor/log-query/get-started-queries.md)
* [Creare e gestire gruppi di azione nel portale di Azure](../../azure-monitor/platform/action-groups.md)
* [Installare e usare le viste di analisi dei log per Azure Active Directory](howto-install-use-log-analytics-views.md)
