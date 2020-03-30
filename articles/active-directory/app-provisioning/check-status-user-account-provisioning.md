---
title: Segnalare il provisioning automatico degli account utente nelle applicazioni SaaS
description: Informazioni su come controllare lo stato dei processi di provisioning automatico dell'account utente e risolvere i problemi di provisioning di singoli utenti.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19d76f69669ffa13d1d55ffa807e6c4818b8840c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282189"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Esercitazione: creazione di report sul provisioning automatico degli account utente

Azure Active Directory (Azure AD) include un servizio di provisioning degli [account utente](user-provisioning.md) che consente di automatizzare il deprovisioning del provisioning degli account utente nelle app SaaS e in altri sistemi, ai fini della gestione del ciclo di vita delle identità end-to-end. Azure AD supporta connettori di provisioning utente preintegrati per tutte le applicazioni e i sistemi con esercitazioni sul provisioning degli utenti [qui.](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

In questo articolo viene descritto come controllare lo stato dei processi di provisioning in seguito alla loro configurazione e come risolvere i problemi di provisioning di singoli utenti e gruppi.

## <a name="overview"></a>Panoramica

I connettori di provisioning vengono impostati e configurati tramite il [portale di Azure](https://portal.azure.com) in base alla [documentazione disponibile](../saas-apps/tutorial-list.md) per l'applicazione supportata. Una volta configurati e in esecuzione, i processi di provisioning possono essere segnalati tramite uno dei due metodi seguenti:

* Portale di **Azure:** questo articolo descrive principalmente il recupero delle informazioni del report dal portale di [Azure,](https://portal.azure.com)che fornisce sia un report di riepilogo del provisioning che log di controllo dettagliati del provisioning per una determinata applicazione.
* **API di controllo**: Azure Active Directory offre anche un'API di controllo che consente di abilitare il recupero programmatico dei log di controllo dettagliati sul provisioning. Vedere [Informazioni di riferimento sull'API di controllo di Azure Active Directory](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) per la documentazione specifica sull'uso di questa API. Sebbene questo articolo non si concentri in particolare sull'uso dell'API, contiene informazioni dettagliate sui tipi di eventi di provisioning registrati nel log di controllo.

### <a name="definitions"></a>Definizioni

Questo articolo usa i termini seguenti, qui definiti:

* **Sistema di origine**: il repository degli utenti da cui il servizio di provisioning di Azure AD esegue la sincronizzazione. Azure Active Directory è il sistema di origine per la maggior parte dei connettori di provisioning preintegrati; esistono tuttavia alcune eccezioni, ad esempio la sincronizzazione in ingresso di Workday.
* **Sistema di destinazione**: il repository di utenti con cui il servizio di provisioning di Azure AD esegue la sincronizzazione. Si tratta in genere di un'applicazione SaaS (ad esempio: Salesforce, ServiceNow, G Suite, Dropbox for Business), ma in alcuni casi può essere un sistema locale come Active Directory (ad esempio: Workday Inbound Synchronization to Active Directory).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Recupero dei report di provisioning dal portale di AzureGetting provisioning reports from the Azure portal

Per ottenere informazioni sul report di provisioning per una determinata applicazione, avviare il portale di Azure e i log di provisioning **delle app** &gt; aziendali di Azure Active Directory (anteprima) nella sezione Attività.To get provisioning report information for a given application, start by launching the [Azure](https://portal.azure.com) **Active Directory** &gt; Enterprise Apps **Provisioning logs (preview)** in the **Activity** section. È inoltre possibile passare all'applicazione enterprise per cui è configurato il provisioning. Ad esempio, se si esegue il provisioning di utenti in LinkedIn Elevate, il percorso di navigazione per i dettagli dell'applicazione è:

**Azure Active Directory &gt; Applicazioni aziendali &gt; All applications (Tutte le applicazioni) &gt; LinkedIn Elevate**

Da qui è possibile accedere sia alla barra di avanzamento del provisioning che ai log di provisioning, descritti di seguito.

## <a name="provisioning-progress-bar"></a>Barra di avanzamento del provisioning

La barra di [avanzamento](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar) del provisioning è visibile nella scheda **Provisioning** per una determinata applicazione. Si trova nella sezione **Stato corrente** sotto **Impostazioni**e mostra lo stato del ciclo iniziale o incrementale corrente. Questa sezione mostra anche:

* Il numero totale di utenti e gruppi che sono stati sincronizzati e che attualmente si trovano nell'ambito per il provisioning tra il sistema di origine e il sistema di destinazione.
* Ora dell'ultima sincronizzazione. Le sincronizzazioni si verificano in genere ogni 20-40 minuti, al termine di un [ciclo iniziale.](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental)
* Se un [ciclo iniziale](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) è stato completato o meno.
* Eventuale quarantena applicata a un processo di provisioning e motivo dello stato di quarantena, ad esempio errore di comunicazione con il sistema di destinazione a causa di credenziali di amministratore non valide.

Lo **stato corrente** deve essere il primo punto in cui gli amministratori cercano di controllare l'integrità operativa del processo di provisioning.

 ![Report di riepilogo](./media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>Registri di provisioning (anteprima)Provisioning logs (preview)

Tutte le attività eseguite dal servizio di provisioning vengono registrate nei log di [provisioning](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)di Azure AD. È possibile accedere ai log di provisioning nel portale di Azure selezionando I log di provisioning delle app aziendali di Azure Active Directory (anteprima) nella sezione **Attività.You** can access the provisioning logs in the Azure portal by selecting **Azure Active Directory** &gt; **Enterprise Apps** &gt; Provisioning logs **(preview)** in the Activity section. È possibile cercare i dati di provisioning in base al nome dell'utente o all'identificatore nel sistema di origine o nel sistema di destinazione. Per informazioni dettagliate, vedere [Provisioning dei registri (anteprima)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). I tipi di eventi dell'attività registrati includono:

## <a name="troubleshooting"></a>Risoluzione dei problemi

Il report di riepilogo del provisioning e i log di provisioning svolgono un ruolo chiave che consente agli amministratori di risolvere vari problemi di provisioning degli account utente.

Per informazioni aggiuntive su come risolvere i problemi di provisioning automatico dell'utente in base agli scenari, vedere [Problemi di configurazione e provisioning degli utenti in un'applicazione](../app-provisioning/application-provisioning-config-problem.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
