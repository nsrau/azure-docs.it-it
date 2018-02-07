---
title: Inserimento dell'applicazione nella raccolta di applicazioni Azure Active Directory
description: Come elencare un'applicazione che supporta l'accesso Single Sign-On nella raccolta di Azure Active Directory | Microsoft Azure
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/09/2018
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: feb09aa8f8e22ad6fbda6a490d251c500bedf3ee
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2018
---
# <a name="listing-your-application-in-the-azure-active-directory-application-gallery"></a>Inserimento dell'applicazione nella raccolta di applicazioni Azure Active Directory


##  <a name="what-is-azure-ad-app-gallery"></a>Informazioni sulla raccolta di app di Azure AD

Azure AD è un servizio di identità basato sul cloud. La [raccolta di app di Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) è un archivio comune in cui vengono pubblicati tutti i connettori di applicazioni per l'accesso Single Sign-On e il provisioning degli utenti. I clienti che usano Azure AD come provider di identità hanno bisogno di connettori di applicazioni SaaS diversi, pubblicati in questa raccolta. L'amministratore IT aggiunge il connettore dalla raccolta di app e lo configura e usa per l'accesso Single Sign-On e il provisioning. Azure AD supporta tutti i principali protocolli di federazione come SAML 2.0, OpenID Connect, OAuth e WS-Fed per l'accesso Single Sign-On. 

## <a name="what-are-the-benefits-of-listing-the-application-in-the-gallery"></a>Quali sono i vantaggi di presentare l'applicazione nella raccolta?

*  Offrire un'esperienza Single Sign-On ottimale ai clienti.

*  Configurazione minima e semplice dell'applicazione.

*  I clienti possono cercare l'applicazione e trovarla nella raccolta. 

*  Tutti i clienti possono usare questa integrazione indipendentemente dallo SKU di Azure AD, ovvero Gratuito, Basic o Premium.

*  Esercitazione dettagliata per la configurazione per i clienti reciproci.

*  Abilitare il provisioning degli utenti per la stessa app, se si usa SCIM.


##  <a name="what-are-the-pre-requisites"></a>Quali sono i prerequisiti?

Per presentare un'applicazione nella raccolta di Azure AD, l'applicazione deve prima di tutto implementare uno dei protocolli di federazione supportati da Azure AD. Leggere le condizioni della raccolta di applicazioni Azure AD. Se si usa: 

*   **OpenID Connect**: creare l'applicazione multi-tenant in Azure AD e implementare il [framework di consenso di Azure AD](active-directory-integrating-applications.md#overview-of-the-consent-framework) per l'applicazione. Inviare la richiesta di accesso all'endpoint comune in modo che qualsiasi cliente possa fornire il consenso all'applicazione. È possibile controllare l'accesso utente dei clienti in base all'ID del tenant e all'UPN dell'utente ricevuti nel token. Per integrare l'applicazione con Azure AD, è possibile seguire [le istruzioni per gli sviluppatori](active-directory-authentication-scenarios.md).

*   **SAML 2.0 o WS-Fed**: l'applicazione deve supportare l'integrazione SSO SAML/WS-Fed in modalità SP o IDP. Qualsiasi app che supporta SAML 2.0 può essere integrata direttamente con un tenant di Azure AD usando le [istruzioni per l'aggiunta di un'applicazione personalizzata](../active-directory-saas-custom-apps.md).

*   **SSO password**: creare un'applicazione Web con una pagina di accesso HTML per configurare l[accesso Single Sign-On basato su password](../active-directory-appssoaccess-whatis.md). L'SSO basato su password, definito anche insieme di credenziali delle password, consente di gestire l'accesso degli utenti e le password per le applicazioni Web che non supportano la federazione delle identità. Risulta utile anche negli scenari in cui più utenti devono condividere un unico account, ad esempio agli account di app di social media dell'organizzazione. 

## <a name="process-for-submitting-the-request-in-the-portal"></a>Processo per l'invio della richiesta nel portale

Dopo aver verificato che l'integrazione dell'applicazione funziona con Azure AD, è necessario inviare la richiesta per l'accesso nel [portale di rete delle applicazioni](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Se è disponibile un account Office 365, è possibile usarlo per accedere a questo portale. In caso contrario, usare l'ID Microsoft (Live ID, Outlook, Hotmail e così via) per l'accesso. Verrà visualizzata la pagina seguente per richiedere l'accesso. Specificare una motivazione aziendale nella casella di testo e fare clic su **Richiedi accesso**. Il team Microsoft esaminerà tutti i dettagli e consentirà l'accesso di conseguenza. In seguito, sarà possibile accedere al portale e inviare la richiesta dettagliata per l'applicazione.

In caso di problemi per l'accesso, contattare il [team di integrazione SSO di Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

![Richiesta di accesso nel portale di SharePoint](./media/active-directory-app-gallery-listing/accessrequest.png)

## <a name="timelines"></a>Tempistica
    
*   Elaborazione della presentazione di un'applicazione SAML 2.0 o WS-Fed nella raccolta: **7-10 giorni lavorativi**

   ![Tempistica per la presentazione di un'applicazione SAML nella raccolta](./media/active-directory-app-gallery-listing/timeline.png)

*   Elaborazione della presentazione di un'applicazione OpenID Connect nella raccolta: **2-5 giorni lavorativi**

   ![Tempistica per la presentazione di un'applicazione SAML nella raccolta](./media/active-directory-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Escalation

Per eventuali escalation, inviare un messaggio di posta elettronica al [team di integrazione SSO di Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) per essere contattati il più presto possibile.

