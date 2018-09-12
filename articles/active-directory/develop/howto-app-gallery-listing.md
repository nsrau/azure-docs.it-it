---
title: Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory | Microsoft Docs
description: Informazioni su come inserire un'applicazione che supporta l'accesso Single Sign-On nella raccolta di app di Azure Active Directory
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/31/2018
ms.author: celested
ms.reviewer: elisol, bryanla
ms.custom: aaddev
ms.openlocfilehash: e5db7b9bed674011c2922f026c301172f347f53f
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666309"
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

## <a name="prerequisites"></a>Prerequisiti

- Per le applicazioni federate (Open ID e SAML/WS-Fed), l'applicazione deve supportare il modello SaaS per entrare a far parte della raccolta di Azure AD. Le applicazioni della raccolta enterprise devono supportare più configurazioni dei clienti e non un cliente specifico.

- Per Open ID Connect, l'applicazione deve essere multi-tenant e il [framework di consenso di Azure AD](quickstart-v1-integrate-apps-with-azure-ad.md#overview-of-the-consent-framework) deve essere implementato correttamente per l'applicazione. L'utente può inviare la richiesta di accesso all'endpoint comune, in modo che qualsiasi cliente possa fornire il consenso all'applicazione. È possibile controllare l'accesso utente in base all'ID del tenant e all'UPN dell'utente ricevuti nel token.

- Per SAML 2.0/WS-Fed l'applicazione deve supportare l'integrazione SSO SAML/WS-Fed in modalità SP o IDP. Assicurarsi che questa funzioni correttamente prima di inviare la richiesta.

- Per l'accesso SSO con password, assicurarsi che l'applicazione supporti l'autenticazione basata su modulo in modo che sia possibile eseguire l'insieme di credenziali delle password affinché l'accesso SSO funzioni come previsto.

- Per le richieste di Provisioning utenti automatico, l'applicazione deve essere presente nella raccolta con la funzionalità Single Sign-On abilitata per usare uno qualsiasi dei protocolli di federazione descritti in precedenza. È possibile richiedere sia l'accesso SSO che il provisioning utenti nel portale, se non sono già elencati.

##  <a name="implementing-sso-using-federation-protocol"></a>Implementazione dell'SSO usando il protocollo di federazione

Per inserire un'applicazione nella raccolta di app di Azure AD, è innanzitutto necessario implementare uno dei protocolli di federazione seguenti supportati da Azure AD e accettare i termini e condizioni della raccolta di applicazioni di Azure AD. Leggere [qui](https://azure.microsoft.com/en-us/support/legal/active-directory-app-gallery-terms/) le condizioni della raccolta di applicazioni di Azure AD.

*   **OpenID Connect**: per integrare l'applicazione con Azure AD mediante il protocollo Open ID Connect, seguire le [istruzioni per gli sviluppatori](authentication-scenarios.md).

    ![Sequenza temporale dell'inserimento di un'applicazione OpenID Connect nella raccolta](./media/howto-app-gallery-listing/openid.png)

    * Se si desidera aggiungere l'applicazione all'elenco nella raccolta usando OpenID Connect, selezionare **OpenID Connect & OAuth 2.0** (OpenID Connect e OAuth 2.0) come indicato in precedenza.

    * In caso di problemi di accesso, contattare il [team di integrazione SSO di Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

*   **SAML 2.0** o **WS-Fed**: se l'app supporta SAML 2.0, può essere integrata direttamente con un tenant di Azure AD usando le [istruzioni per l'aggiunta di un'applicazione personalizzata](../active-directory-saas-custom-apps.md).

    ![Sequenza temporale per l'inserimento di un'applicazione SAML 2.0 o WS-Fed nella raccolta](./media/howto-app-gallery-listing/saml.png)

    * Se si desidera aggiungere l'applicazione all'elenco nella raccolta usando **SAML 2.0** o **WS-Fed**, selezionare **SAMl 2.0/WS-Fed** come indicato in precedenza.

    * In caso di problemi di accesso, contattare il [team di integrazione SSO di Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implementing-sso-using-password-sso"></a>Implementazione dell'SSO con l'accesso SSO con password

Creare un'applicazione Web con una pagina di accesso HTML per configurare l'[accesso Single Sign-On basato su password](../manage-apps/what-is-single-sign-on.md). L'SSO basato su password, definito anche insieme di credenziali delle password, consente di gestire l'accesso degli utenti e le password per le applicazioni Web che non supportano la federazione delle identità. Risulta utile anche negli scenari in cui più utenti devono condividere un unico account, ad esempio agli account di app di social media dell'organizzazione.

![Sequenza temporale per l'inserimento di un'applicazione SSO con password nella raccolta](./media/howto-app-gallery-listing/passwordsso.png)

* Se si desidera aggiungere l'applicazione all'elenco nella raccolta usando SSO con password selezionare **Password SSO** (SSO con password) come indicato in precedenza.

* In caso di problemi di accesso, contattare il [team di integrazione SSO di Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

##  <a name="updateremove-existing-listing"></a>Aggiornare e rimuovere un elenco esistente

Per aggiornare o rimuovere un'applicazione esistente nella raccolta di app di Azure AD, è prima necessario inviare la richiesta nel [portale di rete delle applicazioni](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Se si dispone di un account Office 365, usarlo per accedere a questo portale. In caso contrario, accedere con l'account Microsoft (ad esempio, Outlook o Hotmail).

* Selezionare l'opzione appropriata dall'immagine sottostante

    ![Tempistica per la presentazione di un'applicazione SAML nella raccolta](./media/howto-app-gallery-listing/updateorremove.png)

    * Se si vuole aggiornare un'applicazione esistente, selezionare **Update existing application listing** (Aggiorna elenco applicazioni esistente).

    * Se si vuole rimuovere un'applicazione esistente dalla raccolta di Azure AD, selezionare **Remove existing application listing** (Rimuovi elenco applicazioni esistente)

    * In caso di problemi di accesso, contattare il [team di integrazione SSO di Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

## <a name="submit-the-request-in-the-portal"></a>Inviare la richiesta nel portale

Dopo aver verificato il funzionamento dell'integrazione dell'applicazione con Azure AD, inviare la richiesta per l'accesso nel [portale di rete delle applicazioni](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Se si dispone di un account Office 365, usarlo per accedere a questo portale. In caso contrario, accedere con l'account Microsoft (ad esempio, Outlook o Hotmail).

Dopo l'accesso, viene visualizzata la pagina seguente. Specificare una motivazione aziendale per la richiesta dell'accesso nella casella di testo e quindi selezionare **Richiedi accesso**. Il team esamina i dettagli e fornisce l'accesso di conseguenza. In seguito, è possibile accedere al portale e inviare la richiesta dettagliata per l'applicazione.

In caso di problemi di accesso, contattare il [team di integrazione SSO di Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

![Richiesta di accesso nel portale di SharePoint](./media/howto-app-gallery-listing/accessrequest.png)

## <a name="timelines"></a>Tempistica
    
La tempistica del processo di inserimento di un'applicazione SAML 2.0 o WS-Fed nella raccolta è di 7-10 giorni lavorativi.

   ![Tempistica per la presentazione di un'applicazione SAML nella raccolta](./media/howto-app-gallery-listing/timeline.png)

La tempistica del processo di inserimento di un'applicazione OpenID Connect nella raccolta è di 2-5 giorni lavorativi.

   ![Tempistica per la presentazione di un'applicazione SAML nella raccolta](./media/howto-app-gallery-listing/timeline2.png)

La sequenza temporale del processo di inserimento di un'applicazione nella raccolta con il supporto provisioning utenti è di 40-45 giorni lavorativi.

   ![Tempistica per la presentazione di un'applicazione SAML nella raccolta](./media/howto-app-gallery-listing/provisioningtimeline.png)

## <a name="escalations"></a>Escalation

Per le escalation, inviare un messaggio di posta elettronica al [team di integrazione SSO di Azure AD](mailto:SaaSApplicationIntegrations@service.microsoft.com) (SaaSApplicationIntegrations@service.microsoft.com) che fornirà assistenza nel più breve tempo possibile.