---
title: Single Sign-On SAML per le app locali con il proxy dell'app di Azure AD
description: Informazioni su come fornire l'accesso Single Sign-On per le applicazioni locali protette con l'autenticazione SAML. Fornisci l'accesso remoto alle app locali con il proxy di applicazione.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3d2117e913f292e92f37f31d2e123587c70a189
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803298"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>Single Sign-On SAML per le applicazioni locali con proxy di applicazione

È possibile fornire Single Sign-On (SSO) alle applicazioni locali protette con l'autenticazione SAML e fornire l'accesso remoto a tali applicazioni tramite il proxy di applicazione. Con l'accesso Single Sign-On SAML, Azure Active Directory (Azure AD) esegue l'autenticazione nell'applicazione usando l'account Azure AD dell'utente. Azure AD comunica le informazioni di accesso all'applicazione tramite un protocollo di connessione. È inoltre possibile eseguire il mapping degli utenti a ruoli applicazione specifici in base alle regole definite nelle attestazioni SAML. Abilitando il proxy di applicazione oltre a SAML SSO, gli utenti avranno accesso esterno all'applicazione e un'esperienza SSO senza soluzione di continuità.

Le applicazioni devono essere in grado di utilizzare i token SAML emessi da **Azure Active Directory**. Questa configurazione non si applica alle applicazioni che usano un provider di identità locale. Per questi scenari, è consigliabile esaminare le risorse per la migrazione delle [applicazioni ad Azure AD.](migration-resources.md)

SAML SSO con il proxy di applicazione funziona anche con la funzionalità di crittografia del token SAML. Per altre informazioni, vedere Configurare la crittografia del [token SAML di Azure AD.](howto-saml-token-encryption.md)

I diagrammi di protocollo riportati di seguito descrivono la sequenza Single Sign-On sia per un flusso avviato dal provider di servizi (avviato da SP) che per un flusso avviato dal provider di identità (avviato da IdP). Il proxy di applicazione funziona con SAML SSO memorizzando nella cache la richiesta e la risposta SAML da e verso l'applicazione locale.

  ![Flusso SP SAML](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![Flusso SP SAML](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>Creare un'applicazione e configurare SAML SSO

1. Nel portale di Azure selezionare **Azure Active Directory > applicazioni Enterprise** e selezionare Nuova **applicazione**.

2. Immettere il nome visualizzato per la nuova applicazione, selezionare **Integra qualsiasi altra applicazione non trovata nella raccolta**, quindi selezionare **Crea**.

3. Nella pagina **Panoramica** dell'app selezionare **Single Sign-On**.

4. Selezionare **SAML** come metodo Single Sign-On.

5. Configurare innanzitutto SAML SSO per lavorare mentre si è sulla rete aziendale. Nella pagina **Configura Single Sign-On con SAML,** vai all'intestazione **Configurazione SAML** di base e seleziona l'icona **Modifica** (una matita). Seguire i passaggi descritti in Immettere la [configurazione SAML di base](configure-single-sign-on-non-gallery-applications.md#step-1-edit-the-basic-saml-configuration) per configurare l'autenticazione basata su SAML per l'applicazione.

6. Aggiungere almeno un utente all'applicazione e assicurarsi che l'account di test abbia accesso all'applicazione. Quando si è connessi alla rete aziendale, utilizzare l'account di test per verificare se si dispone dell'accesso Single Sign-On all'applicazione. 

   > [!NOTE]
   > Dopo aver configurato il proxy di applicazione, si tornerà e aggiornare **l'URL**di risposta SAML .

## <a name="publish-the-on-premises-application-with-application-proxy"></a>Pubblicare l'applicazione locale con il proxy di applicazionePublish the on-premises application with Application Proxy

Prima di poter fornire SSO per le applicazioni locali, è necessario abilitare il proxy di applicazione e installare un connettore. Vedere [l'esercitazione Aggiungere un'applicazione locale per l'accesso remoto tramite il proxy](application-proxy-add-on-premises-application.md) di applicazione in Azure AD per informazioni su come preparare l'ambiente locale, installare e registrare un connettore e testare il connettore. Seguire quindi questi passaggi per pubblicare la nuova applicazione con il proxy di applicazione. Per altre impostazioni non menzionate di seguito, vedere la sezione [Aggiungere un'app locale ad Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) nell'esercitazione.

1. Con l'applicazione ancora aperta nel portale di Azure, selezionare **Proxy applicazione**. Specificare **l'URL interno** per l'applicazione. Se si utilizza un dominio personalizzato, è inoltre necessario caricare il certificato TLS/SSL per l'applicazione. 
   > [!NOTE]
   > Come procedura consigliata, utilizzare domini personalizzati quando possibile per un'esperienza utente ottimizzata. Altre informazioni [sull'utilizzo dei domini personalizzati in Proxy di applicazione di Azure AD](application-proxy-configure-custom-domain.md).

2. Selezionare **Azure Active Directory** come metodo di **preautenticazione** per l'applicazione.

3. Copiare **l'URL esterno** per l'applicazione. Questo URL è necessario per completare la configurazione SAML.

4. Utilizzando l'account di test, provare ad aprire l'applicazione con **l'URL esterno** per verificare che il proxy di applicazione sia impostato correttamente. In caso di problemi, vedere [Risolvere i problemi relativi al proxy](application-proxy-troubleshoot.md)di applicazione e i messaggi di errore .

## <a name="update-the-saml-configuration"></a>Aggiornare la configurazione SAML

1. Con l'applicazione ancora aperta nel portale di Azure, selezionare **Single Sign-On**. 

2. Nella pagina **Configura Single Sign-On con SAML,** vai all'intestazione **Configurazione SAML** di base e seleziona l'icona **Modifica** (una matita). Assicurarsi che **l'URL esterno** configurato in Proxy applicazione sia popolato nei campi **Identificatore**, **URL di risposta**e URL di **disconnessione.** Questi URL sono necessari per il corretto funzionamento del proxy di applicazione. 

3. Modificare **l'URL** di risposta configurato in precedenza in modo che il relativo dominio sia raggiungibile su Internet tramite il proxy di applicazione. Ad esempio, se **l'URL esterno** `https://contosotravel-f128.msappproxy.net` è e l'URL di **risposta** originale è `https://contosotravel.com/acs`, sarà necessario aggiornare l'URL di **risposta** originale a `https://contosotravel-f128.msappproxy.net/acs`.

    ![Immettere i dati di configurazione SAML di base](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. Selezionare la casella di controllo accanto **all'URL** di risposta aggiornato per contrassegnarlo come predefinito.

   * Dopo aver contrassegnato **l'URL** di risposta richiesto come predefinito, è anche possibile eliminare l'URL di **risposta** configurato in precedenza che utilizzava l'URL interno.

   * Per un flusso avviato da SP, assicurarsi che l'applicazione back-end specifichi **l'URL** di risposta o il servizio consumer di asserzione corretto per la ricezione del token di autenticazione.

    > [!NOTE]
    > Se l'applicazione back-end prevede che **l'URL** di risposta sia l'URL interno, è necessario utilizzare [domini personalizzati](application-proxy-configure-custom-domain.md) per avere URL interni ed esterni corrispondenti o installare l'estensione di accesso sicuro delle app personali nei dispositivi degli utenti. Questa estensione reindirizzerà automaticamente al servizio proxy di applicazione appropriato. Per installare l'estensione, vedere [My Apps secure sign-in extension](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).
    
## <a name="test-your-app"></a>Test dell'app

Dopo aver completato tutti questi passaggi, l'app dovrebbe funzionare. Per testare l'app:

1. Aprire un browser e passare **all'URL esterno** creato al momento della pubblicazione dell'app. 
1. Accedere con l'account di test assegnato all'app. Dovrebbe essere possibile caricare l'applicazione e avere SSO nell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

- [Come viene offerto l'accesso Single Sign-On dal proxy di applicazione di Azure AD?](application-proxy-single-sign-on.md)
- [Risolvere i problemi del Proxy applicazione](application-proxy-troubleshoot.md)
