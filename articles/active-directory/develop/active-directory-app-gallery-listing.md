---
title: Inserimento dell'applicazione nella raccolta di applicazioni di Azure Active Directory | Microsoft Docs
description: Come inserire un'applicazione che supporta l'accesso Single Sign-On nella raccolta di app di Azure Active Directory
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
ms.openlocfilehash: 502fb555bd3b381c9be0ff04e210cc07f9bf6cd8
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory


##  <a name="what-is-the-azure-ad-application-gallery"></a>Definizione della raccolta di applicazioni di Azure AD

Azure Active Directory (Azure AD) è un servizio per la gestione delle identità basato sul cloud. La [raccolta di applicazioni di Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) si trova nell'app store Azure Marketplace, in cui vengono pubblicati tutti i connettori di applicazioni per l'accesso Single Sign-On e il provisioning degli utenti. I clienti che usano Azure AD come provider di identità possono trovare i diversi connettori di applicazioni SaaS pubblicati qui. Gli amministratori IT aggiungono i connettori dalla raccolta di app e li configurano e usano per l'accesso Single Sign-On e il provisioning. Azure AD supporta tutti i principali protocolli di federazione per l'accesso Single Sign-On, come SAML 2.0, OpenID Connect, OAuth e WS-Fed. 

## <a name="what-are-the-benefits-of-listing-an-application-in-the-gallery"></a>Quali sono i vantaggi di inserire un'applicazione nella raccolta?

*  Esperienza Single Sign-On ottimale per i clienti.

*  Configurazione minima e semplice dell'applicazione. 

*  Ricerca rapida per l'individuazione dell'applicazione nella raccolta.

*  Uso di questa integrazione per tutti i clienti con account Free, Basic e Premium di Azure AD. 

*  Esercitazione dettagliata per la configurazione per i clienti reciproci. 

*  Provisioning per la stessa app per i clienti che usano SCIM.


##  <a name="prerequisites-implement-federation-protocol"></a>Prerequisiti: implementare il protocollo di federazione

Per inserire un'applicazione nella raccolta di app di Azure AD, è innanzitutto necessario implementare uno dei protocolli di federazione seguenti supportati da Azure AD. Leggere le condizioni della raccolta di applicazioni Azure AD. 

*   **OpenID Connect**: creare l'applicazione multi-tenant in Azure AD e implementare il [framework di consenso di Azure AD](active-directory-integrating-applications.md#overview-of-the-consent-framework) per l'applicazione. Inviare la richiesta di accesso all'endpoint comune, in modo che qualsiasi cliente possa fornire il consenso all'applicazione. È possibile controllare l'accesso utente in base all'ID del tenant e all'UPN dell'utente ricevuti nel token. Per integrare l'applicazione con Azure AD, seguire le [istruzioni per gli sviluppatori](active-directory-authentication-scenarios.md).

*   **SAML 2.0** o **WS-Fed**: l'applicazione deve supportare l'integrazione SSO SAML/WS-Fed in modalità SP o IDP. Se l'app supporta SAML 2.0, può essere integrata direttamente con un tenant di Azure AD usando le [istruzioni per l'aggiunta di un'applicazione personalizzata](../active-directory-saas-custom-apps.md).

*   **SSO basato su password**: creare un'applicazione Web con una pagina di accesso HTML per configurare l'[accesso Single Sign-On basato su password](../active-directory-appssoaccess-whatis.md). L'SSO basato su password, definito anche insieme di credenziali delle password, consente di gestire l'accesso degli utenti e le password per le applicazioni Web che non supportano la federazione delle identità. Risulta utile anche negli scenari in cui più utenti devono condividere un unico account, ad esempio agli account di app di social media dell'organizzazione. 

## <a name="submit-the-request-in-the-portal"></a>Inviare la richiesta nel portale

Dopo aver verificato il funzionamento dell'integrazione dell'applicazione con Azure AD, inviare la richiesta per l'accesso nel [portale di rete delle applicazioni](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Se si dispone di un account Office 365, usarlo per accedere a questo portale. In caso contrario, accedere con l'account Microsoft (ad esempio, Outlook o Hotmail).

Dopo l'accesso, viene visualizzata la pagina seguente. Specificare una motivazione aziendale per la richiesta dell'accesso nella casella di testo e quindi selezionare **Richiedi accesso**. Il team esamina i dettagli e fornisce l'accesso di conseguenza. In seguito, è possibile accedere al portale e inviare la richiesta dettagliata per l'applicazione.

In caso di problemi di accesso, contattare il [team di integrazione SSO di Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

![Richiesta di accesso nel portale di SharePoint](./media/active-directory-app-gallery-listing/accessrequest.png)

## <a name="timelines"></a>Tempistica
    
La tempistica del processo di inserimento di un'applicazione SAML 2.0 o WS-Fed nella raccolta è di 7-10 giorni lavorativi.

   ![Tempistica per la presentazione di un'applicazione SAML nella raccolta](./media/active-directory-app-gallery-listing/timeline.png)

La tempistica del processo di inserimento di un'applicazione OpenID Connect nella raccolta è di 2-5 giorni lavorativi.

   ![Tempistica per la presentazione di un'applicazione SAML nella raccolta](./media/active-directory-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Escalation

Per le escalation, inviare un messaggio di posta elettronica al [team di integrazione SSO di Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) che fornirà assistenza nel più breve tempo possibile.

