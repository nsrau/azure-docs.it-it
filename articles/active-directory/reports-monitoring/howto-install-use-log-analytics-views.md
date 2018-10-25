---
title: Come installare e usare le viste di Log Analytics per Azure Active Directory (anteprima) | Microsoft Docs
description: Informazioni su come installare e usare le viste di Log Analytics per Azure Active Directory (anteprima)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 2290de3c-2858-4da0-b4ca-a00107702e26
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 10/01/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: b1333eda0fa0bffdf3852f24414511f55acc090e
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49395532"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>Installare e usare le viste di Log Analytics per Azure Active Directory

Le viste di Log Analytics per Azure Active Directory consentono di analizzare ed eseguire ricerche nei log attività di Azure AD nel tenant di Azure AD. I log attività di Azure AD includono:

* Log di controllo: il [report delle attività del log di controllo](concept-audit-logs.md) consente di accedere alla cronologia di ogni attività eseguita nel tenant.
* Log di accesso: il [report delle attività di accesso](concept-sign-ins.md) consente di determinare chi ha eseguito le attività segnalate nei log di controllo.

## <a name="prerequisites"></a>Prerequisiti

Per usare le viste di Log Analytics, è necessario:

* Disporre di un'area di lavoro di Log Analytics nella sottoscrizione di Azure. Informazioni su [come creare un'area di lavoro di Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Completare prima di tutto la procedura per [indirizzare i log attività di Azure AD all'area di lavoro di Log Analytics](howto-integrate-activity-logs-with-log-analytics.md).
* Scaricare le viste dal [repository GitHub](https://aka.ms/AADLogAnalyticsviews) nel computer locale.

## <a name="install-the-log-analytics-views"></a>Installare le viste di Log Analytics

1. Passare all'area di lavoro di Log Analytics. A tale scopo, passare innanzitutto al [portale di Azure](https://portal.azure.com) e selezionare **Tutti i servizi**. Digitare **Log Analytics** nella casella di testo e selezionare **Log Analytics**. Selezionare l'area di lavoro a cui sono stati indirizzati i log attività, come parte dei prerequisiti.
2. Selezionare **Visualizza finestra di progettazione**, selezionare **Importa** e quindi selezionare **Scegli file** per importare le viste dal computer locale.
3. Selezionare le viste scaricate dai prerequisiti e selezionare **Salva** per salvare l'importazione. Eseguire questa operazione per la vista **Azure AD Account Provisioning Events** (Eventi di provisioning account Azure AD), la vista **Sign-ins Events** (Eventi di accesso) e la vista **Users Performing Consent** (Consenso dagli utenti).

## <a name="use-the-views"></a>Usare le viste

1. Passare all'area di lavoro di Log Analytics. A tale scopo, passare innanzitutto al [portale di Azure](https://portal.azure.com) e selezionare **Tutti i servizi**. Digitare **Log Analytics** nella casella di testo e selezionare **Log Analytics**. Selezionare l'area di lavoro a cui sono stati indirizzati i log attività, come parte dei prerequisiti.

2. Nell'area di lavoro selezionare **Riepilogo dell'area di lavoro**. Dovrebbero essere visualizzate le tre viste seguenti:

    * **Azure AD Account Provisioning Events**(Eventi di provisioning account Azure AD): questa vista mostra i report relativi al controllo delle attività di provisioning, ad esempio il numero di nuovi utenti di cui è stato effettuato il provisioning e gli errori di provisioning, il numero di utenti aggiornati e gli errori di aggiornamento, nonché il numero di utenti di cui è stato annullato il provisioning e gli errori corrispondenti.    
    * **Sign-ins Events** (Eventi di accesso): questa vista mostra i report rilevanti relativi al monitoraggio delle attività di accesso, ad esempio gli accessi per applicazione, utente e dispositivo, nonché una visualizzazione di riepilogo che riporta il numero di accessi nel tempo.
    * **Users Performing Consent** (Consenso dagli utenti): questa vista mostra i report relativi al consenso dell'utente, ad esempio i consensi per utente, gli accessi di utenti che hanno concesso il consenso e gli accessi per applicazione per tutte le applicazioni basate sul consenso dell'utente. 

3. Selezionare una di queste viste per passare ai singoli report. È anche possibile impostare avvisi per qualsiasi parametro dei report. Ad esempio, di seguito viene descritto come impostare un avviso per ogni errore di accesso. Selezionare prima di tutto la vista **Sign-ins Events** (Eventi di accesso), selezionare il report **Sign-in errors over time** (Errori di accesso nel tempo) e quindi selezionare **Analytics** (Analisi) per aprire la pagina dei dettagli con la query effettiva associata al report. 

    ![Dettagli](./media/howto-install-use-log-analytics-views/details.png)


4. Selezionare **Imposta avviso** e quindi selezionare **Ogni volta che l'operazione di ricerca log personalizzata è &lt;logica non definita&gt;** nella sezione **Criteri di avviso**. Poiché si vuole generare un avviso ogni volta che si verifica un errore di accesso, impostare la **Soglia** della logica di avviso predefinita su **1** e quindi selezionare **Fine**. 

    ![Configurare la logica dei segnali](./media/howto-install-use-log-analytics-views/configure-signal-logic.png)

5. Immettere un nome e descrizione per l'avviso e impostare il livello di gravità su **Avviso**.

    ![Creare una regola](./media/howto-install-use-log-analytics-views/create-rule.png)

6. Selezionare il gruppo di azioni per l'avviso. In generale, può trattarsi di un team a cui si vuole inviare notifica tramite posta elettronica o SMS o può essere un'attività automatizzata tramite webhook, runbook, funzioni, app per la logica o soluzioni di gestione dei servizi IT esterne. Vedere le informazioni su come [Creare e gestire gruppi di azione nel portale di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

7. Selezionare **Crea regola di avviso** per creare l'avviso. A questo punto si riceverà un avviso ogni volta che si verifica un errore di accesso.

## <a name="next-steps"></a>Passaggi successivi

* [Analizzare i log attività in Log Analytics](howto-analyze-activity-logs-log-analytics.md)
* [Introduzione a Log Analytics nel portale di Azure](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal)