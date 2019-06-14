---
title: Analizzare i log attività di Azure Active Directory usando i log di monitoraggio di Azure | Microsoft Docs
description: Informazioni su come analizzare i log attività di Azure Active Directory usando i log di monitoraggio di Azure
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4535ae65-8591-41ba-9a7d-b7f00c574426
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: priyamo
ms.reviewer: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1651bb2898a2bd1e3e0c3fbbce77dc4106d76e7a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60438245"
---
# <a name="analyze-azure-ad-activity-logs-with-azure-monitor-logs"></a>Azure AD analizzare i log attività con i log di monitoraggio di Azure

Dopo aver [integrato i log attività di Azure AD con i log di Monitoraggio di Azure](howto-integrate-activity-logs-with-log-analytics.md), è possibile usare le funzionalità dei log di Monitoraggio di Azure per ottenere informazioni dettagliate sull'ambiente. È anche possibile installare le [viste di analisi dei log per i log attività di Azure AD](howto-install-use-log-analytics-views.md) per ottenere l'accesso a report predefiniti sugli eventi di controllo e di accesso nell'ambiente in uso.

Questo articolo descrive come analizzare i log attività di Azure AD nell'area di lavoro Log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prerequisiti 

Per seguire la procedura, è necessario:

* Un'area di lavoro Log Analytics nella sottoscrizione di Azure. Informazioni su [come creare un'area di lavoro Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Completare prima di tutto la procedura per [indirizzare i log attività di Azure AD all'area di lavoro Log Analytics](howto-integrate-activity-logs-with-log-analytics.md).

## <a name="navigate-to-the-log-analytics-workspace"></a>Passare all'area di lavoro Log Analytics

1. Accedere al [portale di Azure](https://portal.azure.com). 

2. Selezionare **Azure Active Directory** e quindi selezionare **Log** nella sezione **Monitoraggio** per aprire l'area di lavoro Log Analytics. L'area di lavoro verrà aperta con una query predefinita.

    ![Query predefinita](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>Visualizzare lo schema per i log attività di Azure AD

I log vengono inviati alle tabelle **AuditLogs** e **SigninLogs** nell'area di lavoro. Per visualizzare lo schema per queste tabelle:

1. Dalla visualizzazione della query predefinita nella sezione precedente, selezionare **Schema** ed espandere l'area di lavoro. 

2. Espandere la sezione **Gestione log** e quindi espandere **AuditLogs** o **SignInLogs** per visualizzare lo schema del log.
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

4. Selezionare il **Gruppo di azioni** che riceverà l'avviso quando si verifica il segnale. È possibile scegliere di inviare notifiche al team tramite posta elettronica o SMS oppure è possibile automatizzare l'azione con webhook, funzioni di Azure o app per la logica. Vedere altre informazioni sulla [creazione e gestione di gruppi di azioni nel portale di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

5. Dopo aver configurato l'avviso, selezionare **Crea avviso** per abilitarlo. 

## <a name="install-and-use-pre-built-views-for-azure-ad-activity-logs"></a>Installare e usare le viste predefinite per i log attività di Azure AD

È anche possibile scaricare le viste predefinite di analisi dei log per i log attività di Azure AD. Le viste includono numerosi report relativi a scenari comuni che interessano gli eventi di accesso e controllo. È possibile anche impostare un avviso per tutti i dati disponibili nei report, seguendo la procedura descritta nella sezione precedente.

* **Azure AD Account Provisioning Events** (Eventi di provisioning degli account di Azure AD): questa vista mostra i report relativi al controllo delle attività di provisioning, ad esempio il numero di nuovi utenti di cui è stato effettuato il provisioning e gli errori di provisioning, il numero di utenti aggiornati e gli errori di aggiornamento, nonché il numero di utenti di cui è stato annullato il provisioning e gli errori corrispondenti.    
* **Sign-ins Events** (Eventi di accesso): questa vista mostra i report rilevanti relativi al monitoraggio delle attività di accesso, ad esempio gli accessi per applicazione, utente e dispositivo, nonché una visualizzazione di riepilogo che tiene traccia del numero di accessi nel tempo.
* **Users Performing Consent** (Consenso dagli utenti): questa vista mostra i report relativi al consenso dell'utente, ad esempio i consensi per utente, gli accessi di utenti che hanno concesso il consenso e gli accessi per applicazione per tutte le applicazioni basate sul consenso dell'utente. 

Vedere come [installare e usare le viste di analisi dei log per i log attività di Azure AD](howto-install-use-log-analytics-views.md). 


## <a name="next-steps"></a>Passaggi successivi

* [Introduzione alle query nei log di Monitoraggio di Azure](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)
* [Creare e gestire gruppi di azione nel portale di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)
* [Installare e usare le viste di analisi dei log per Azure Active Directory](howto-install-use-log-analytics-views.md)