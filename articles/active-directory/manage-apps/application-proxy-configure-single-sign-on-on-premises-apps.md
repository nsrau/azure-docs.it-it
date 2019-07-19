---
title: SAML Single Sign-on per applicazioni locali con Azure Active Directory Application Proxy (anteprima) | Microsoft Docs
description: Informazioni su come fornire l'accesso Single Sign-on per le applicazioni locali protette con l'autenticazione SAML. Fornire l'accesso remoto alle app locali con il proxy di applicazione.
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
ms.date: 07/09/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9f4ab79a9fc9b8fec26cce98d9a878b6fa2a0c3
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67853737"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy-preview"></a>SAML Single Sign-on per le applicazioni locali con proxy di applicazione (anteprima)

È possibile fornire l'accesso Single Sign-on (SSO) alle applicazioni locali protette con l'autenticazione SAML e fornire l'accesso remoto a queste applicazioni tramite il proxy di applicazione. Con SAML Single Sign-on, Azure Active Directory (Azure AD) esegue l'autenticazione all'applicazione usando l'account Azure AD dell'utente. Azure AD comunica le informazioni di accesso all'applicazione tramite un protocollo di connessione. È anche possibile eseguire il mapping degli utenti a ruoli specifici dell'applicazione in base alle regole definite nelle attestazioni SAML. Abilitando il proxy di applicazione oltre a SAML SSO, gli utenti avranno accesso esterno all'applicazione e un'esperienza di accesso Single Sign-on facile.

Le applicazioni devono essere in grado di utilizzare i token SAML emessi da **Azure Active Directory**. Questa configurazione non si applica alle applicazioni che usano un provider di identità locale. Per questi scenari, è consigliabile esaminare [le risorse per la migrazione delle applicazioni a Azure ad](migration-resources.md).

SAML SSO con proxy di applicazione funziona anche con la funzionalità di crittografia dei token SAML. Per altre informazioni, vedere [configurare Azure ad crittografia dei token SAML](howto-saml-token-encryption.md).

I diagrammi di protocollo seguenti descrivono la sequenza di Single Sign-on per un flusso avviato dal provider di servizi (avviata da SP) e un flusso di identità avviato dal provider di identità. Il proxy dell'applicazione funziona con SAML SSO memorizzando nella cache la richiesta e la risposta SAML da e verso l'applicazione locale.

  ![Flusso SP SAML](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![Flusso SP SAML](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>Creare un'applicazione e configurare SSO SAML

1. Nella portale di Azure selezionare **Azure Active Directory > applicazioni aziendali** e selezionare **nuova applicazione**.

2. In **Aggiungi un'app**selezionare **un'applicazione non nella raccolta**.

3. Immettere il nome visualizzato per la nuova applicazione e quindi selezionare **Aggiungi**.

4. Nella pagina **Panoramica** dell'app selezionare **Single Sign-on**.

5. Selezionare **SAML** come metodo Single Sign-on.

6. Configurare prima di tutto SAML SSO per il funzionamento nella rete aziendale. Nella pagina **Configura accesso Single Sign-on con SAML** passare all'intestazione di **configurazione SAML di base** e selezionare l'icona di **modifica** (matita). Seguire i passaggi in [immettere la configurazione SAML di base](configure-single-sign-on-non-gallery-applications.md#saml-based-single-sign-on) per configurare l'autenticazione basata su SAML per l'applicazione.

7. Aggiungere almeno un utente all'applicazione e verificare che l'account di test abbia accesso all'applicazione. Quando si è connessi alla rete aziendale, usare l'account di test per verificare se l'applicazione è Single Sign-on. 

   > [!NOTE]
   > Dopo aver configurato il proxy di applicazione, si tornerà a aggiornare l'URL di **risposta**SAML.

## <a name="publish-the-on-premises-application-with-application-proxy"></a>Pubblicare l'applicazione locale con il proxy di applicazione

Prima di poter fornire l'accesso SSO per le applicazioni locali, è necessario abilitare il proxy di applicazione e installare un connettore. Vedere l'esercitazione [aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in Azure ad](application-proxy-add-on-premises-application.md) per informazioni su come preparare l'ambiente locale, installare e registrare un connettore e testare il connettore. Seguire quindi questa procedura per pubblicare la nuova applicazione con il proxy di applicazione. Per altre impostazioni non indicate di seguito, vedere la sezione [aggiungere un'app locale a Azure ad](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) nell'esercitazione.

1. Con l'applicazione ancora aperta nella portale di Azure, selezionare **proxy di applicazione**. Specificare l' **URL interno** per l'applicazione. Se si usa un dominio personalizzato, è necessario anche caricare il certificato SSL per l'applicazione. 
   > [!NOTE]
   > Come procedura consigliata, utilizzare domini personalizzati laddove possibile per un'esperienza utente ottimizzata. Altre informazioni sull' [uso di domini personalizzati nel proxy di applicazione Azure ad](application-proxy-configure-custom-domain.md).

2. Selezionare **Azure Active Directory** come metodo di **pre-autenticazione** per l'applicazione.

3. Copiare l' **URL esterno** per l'applicazione. Questo URL sarà necessario per completare la configurazione di SAML.

4. Usando l'account di test, provare ad aprire l'applicazione con l' **URL esterno** per verificare che il proxy di applicazione sia configurato correttamente. In caso di problemi, vedere [risolvere i problemi del proxy dell'applicazione e i messaggi di errore](application-proxy-troubleshoot.md).

## <a name="update-the-saml-configuration"></a>Aggiornare la configurazione SAML

1. Con l'applicazione ancora aperta nella portale di Azure selezionare **Single Sign-on**. 

2. Nella pagina **Configura accesso Single Sign-on con SAML** passare all'intestazione di **configurazione SAML di base** e selezionare l'icona di **modifica** (matita). L' **URL esterno** configurato nel proxy di applicazione popola automaticamente i campi **identificatore**, **URL di risposta**e **URL** di disconnessione. Non modificare questi URL perché sono necessari per il corretto funzionamento del proxy di applicazione.

3. Modificare l' **URL di risposta** configurato in precedenza in modo che il dominio sia raggiungibile dal proxy di applicazione. Ad esempio, se l' **URL esterno** è `https://contosotravel-f128.msappproxy.net` e l' **URL di risposta** `https://contosotravel.com/acs`originale è, sarà necessario aggiornare l'URL di **risposta** originale a `https://contosotravel-f128.msappproxy.net/acs`. 

    ![Immettere i dati di configurazione SAML di base](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. Selezionare la casella di controllo accanto all' **URL di risposta** aggiornato per contrassegnarlo come predefinito.

   * Se l' **URL di risposta** richiesto è già elencato, contrassegnare questo **URL di risposta** come predefinito ed eliminare l'URL di **risposta**configurato in precedenza.

   * Per un flusso avviato da SP, assicurarsi che l'applicazione back-end specifichi l'URL di **risposta** corretto o l'URL del servizio consumer di asserzione per la ricezione del token di autenticazione.

    > [!NOTE]
    > Se l'applicazione back-end prevede che l' **URL di risposta** sia l'URL interno, è necessario usare i [domini personalizzati](application-proxy-configure-custom-domain.md) per trovare URL interni ed esterni corrispondenti oppure installare l'estensione per l'accesso sicuro alle app personali nei dispositivi degli utenti. Questa estensione reindirizza automaticamente al servizio proxy applicazione appropriato. Per installare l'estensione, vedere [estensione per l'accesso sicuro alle app personali](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).
    
## <a name="test-your-app"></a>Test dell'app

Dopo aver completato tutti questi passaggi, l'app dovrebbe funzionare. Per testare l'app:

1. Aprire un browser e passare all' **URL esterno** creato al momento della pubblicazione dell'app. 
1. Accedere con l'account di test assegnato all'app. Dovrebbe essere possibile caricare l'applicazione e avere SSO nell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

- [Come viene offerto l'accesso Single Sign-On dal proxy di applicazione di Azure AD?](application-proxy-single-sign-on.md)
- [Risolvere i problemi del Proxy applicazione](application-proxy-troubleshoot.md)
