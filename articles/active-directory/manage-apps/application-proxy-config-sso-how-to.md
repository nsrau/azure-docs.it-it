---
title: "Procedura: Configurazione dell'accesso Single Sign-On all'applicazione Proxy di applicazione | Microsoft Docs"
description: "Procedura: Configurazione rapida dell'accesso Single Sign-On all'applicazione Proxy di applicazione"
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 91bbbf6cced8dc3ade4906961d68a99f810d9fd9
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "49956987"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Procedura: Configurazione dell'accesso Single Sign-On all'applicazione Proxy di applicazione

L'accesso Single sign-on (SSO) consente agli utenti di accedere a un'applicazione senza effettuare l'autenticazione più volte. Consente di eseguire l'autenticazione solo nel cloud, in Azure Active Directory e consente al servizio o al connettore di rappresentare l'utente per completare eventuali richieste di autenticazione aggiuntive dall'applicazione.

## <a name="how-to-configure-single-sign-on"></a>Come configurare l'accesso Single Sign-On
Per configurare SSO, verificare innanzitutto che l'applicazione sia configurata per la preautenticazione tramite Azure Active Directory. Per eseguire la configurazione, accedere a **Azure Active Directory**  - &gt; **Applicazioni aziendali**  - &gt; **Tutte le applicazioni**  - &gt;L'applicazione  **- &gt;Proxy di applicazione**. Assicurarsi che il campo "Pre-autenticazione" visualizzato in questa pagina sia impostato su Azure Active Directory. 

Per altre informazioni sui metodi di pre-autenticazione, vedere il passaggio 4 del [documento sulla pubblicazione dell'app](application-proxy-publish-azure-portal.md).

   ![Metodo di preautenticazione nel portale di Azure](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Configurazione delle modalità Single Sign-On per le applicazioni Proxy di applicazione
Configurare il tipo specifico di Single Sign-On. I metodi di accesso sono classificati in base al tipo di autenticazione usato dall'applicazione back-end. Le applicazioni Proxy di applicazione supportano tre tipi di accesso:

-   **Accesso basato su password**: l'accesso basato su password può essere usato per qualsiasi applicazione che usa i campi nome utente e password per l'accesso. I passaggi per la configurazione sono illustrati in [Configure password Single sign-on for an Azure AD gallery application](configure-password-single-sign-on-gallery-applications.md)(Configurare un accesso Single Sign-On basato su password per un'applicazione della raccolta di Azure AD).

-   **Autenticazione integrata di Windows**: per le applicazioni che usano l'autenticazione integrata di Windows (IWA), l'accesso Single Sign-On è abilitato tramite delega vincolata Kerberos (KCD). Questo metodo consente di connettori Proxy di applicazione in Active Directory di rappresentare gli utenti e inviare e ricevere i token per loro conto. I dettagli relativi alla configurazione della delega vincolata Kerberos sono reperibili nella [documentazione relativa all'accesso Single Sign-On con delega vincolata Kerberos](application-proxy-configure-single-sign-on-with-kcd.md).

-   **Accesso basato su intestazione**: l'accesso basato su intestazione viene abilitato mediante una relazione e non richiede un'ulteriore configurazione. Per informazioni dettagliate sulla relazione e istruzioni dettagliate per la configurazione dell'accesso Single Sign-On per un'applicazione che usa le intestazioni per l'autenticazione, vedere la [documentazione di PingAccess per Azure AD](application-proxy-configure-single-sign-on-with-ping-access.md).

Ognuna di queste opzioni è reperibile da "Applicazioni aziendali" nella propria applicazione e aprendo la pagina **Single Sign-On** nel menu a sinistra. Si noti che se l'applicazione è stata creata nel portale precedente, potrebbero non essere visibili tutte queste opzioni.

In questa pagina viene visualizzata inoltre un opzione di accesso aggiuntiva: Linked sign-on (Accesso collegato). Questa opzione è supportata anche da Proxy di applicazione. Tuttavia, questa opzione non aggiunge l'accesso Single Sign-On all'applicazione. L'applicazione potrebbe comunque avere già implementato l'accesso Single Sign-On usando un altro servizio, ad esempio Active Directory Federation Services. 

Questa opzione consente a un amministratore di creare un collegamento a una prima applicazione a cui accedono gli utenti quando accedono all'applicazione. Ad esempio, se è presente un'applicazione configurata per l'autenticazione degli utenti tramite Active Directory Federation Services 2.0, un amministratore può usare l'opzione "Linked sign-on (Accesso collegato)" per creare un collegamento ad essa sul pannello di accesso.

## <a name="next-steps"></a>Passaggi successivi
[Fornire l'accesso Single Sign-On alle app con il proxy di applicazione](application-proxy-configure-single-sign-on-with-kcd.md)
