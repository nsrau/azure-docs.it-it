---
title: Single sign-on SAML per applicazioni locali con Azure Active Directory Application Proxy (anteprima) | Microsoft Docs
description: Informazioni su come fornire accesso single sign-on per un'istanza locale applicazioni pubblicate tramite il Proxy di applicazione che sono protetti con l'autenticazione SAML.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 415b33dce42945c40aedd996d4dcfa5c6b987b44
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58336219"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy-preview"></a>Single sign-on SAML per applicazioni locali con il Proxy di applicazione (anteprima)

È possibile fornire accesso single sign-on (SSO) alle applicazioni locali che sono protetti con l'autenticazione SAML e forniscono l'accesso remoto a tali applicazioni tramite il Proxy di applicazione. Con SAML single sign-on, Azure Active Directory (Azure AD) per eseguire l'autenticazione all'applicazione usando l'account dell'utente Azure AD. Azure AD comunica le informazioni di accesso all'applicazione tramite un protocollo di connessione. È anche possibile eseguire il mapping degli utenti a ruoli specifici dell'applicazione in base alle regole definite nelle attestazioni SAML. Abilita Proxy di applicazione oltre a SAML SSO gli utenti avranno accesso esterno per l'applicazione e un'esperienza SSO facile.

Le applicazioni devono essere in grado di utilizzare i token SAML rilasciati da **Azure Active Directory**. Questa configurazione non è applicabile alle applicazioni tramite un provider di identità in locale. Per questi scenari è consigliabile esaminare [risorse per la migrazione ad Azure AD di applicazioni](migration-resources.md).

SAML SSO con il Proxy di applicazione funziona anche con la funzionalità di crittografia di token SAML. Per altre informazioni, vedi [crittografia di token SAML in Azure AD configurare](howto-saml-token-encryption.md).

## <a name="publish-the-on-premises-application-with-application-proxy"></a>Pubblicare l'applicazione in locale con il Proxy di applicazione

Prima di poter fornire l'accesso SSO per applicazioni locali, verificare che è stato abilitato il Proxy di applicazione e si dispone di un connettore installato. Visualizzare [aggiungere un'applicazione in locale per l'accesso remoto tramite il Proxy di applicazione in Azure AD](application-proxy-add-on-premises-application.md) per informazioni su come.

Quando si sta l'esercitazione, tenere presente quanto segue:

* Pubblicare l'applicazione seguendo le istruzioni riportate nell'esercitazione. Assicurarsi di selezionare **Azure Active Directory** come la **pre-autenticazione** metodo per l'applicazione (passaggio 4 [aggiungere un'app da sito locale ad Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad
)).
* Copia il **URL esterno** per l'applicazione.
* Come procedura consigliata, usare i domini personalizzati, ove possibile per un'esperienza utente ottimizzata. Altre informazioni sulle [uso di domini personalizzati nel Proxy applicazione Azure AD](application-proxy-configure-custom-domain.md).
* Aggiungere almeno un utente all'applicazione e verificare che l'account di test abbia accesso all'applicazione in locale.

## <a name="set-up-saml-sso"></a>Configurazione di SAML SSO

1. Nel portale di Azure, selezionare **Azure Active Directory > applicazioni aziendali** e selezionare l'applicazione dall'elenco.
1. Dell'app **Overview** pagina, selezionare **Single sign-on**.
1. Selezionare **SAML** come il metodo single sign-on.
1. Nel **impostata su Single Sign-On con SAML** pagina, modificare il **base SAML Configuration** dati e seguire i passaggi descritti in [configurazione SAML di base immettere](configure-single-sign-on-non-gallery-applications.md#saml-based-single-sign-on) configurare basato su SAML autenticazione per l'applicazione.

   * Assicurarsi che il **URL di risposta** radice corrisponde o è un percorso sotto il **URL esterno** per l'applicazione in locale che è stato aggiunto per l'accesso remoto tramite il Proxy di applicazione di Azure AD.

     ![Immettere i dati di configurazione di base SAML](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)

    > [!NOTE]
    > Se l'applicazione back-end prevede che il **URL di risposta** per essere l'URL interno, è necessario installare le app personali protetta estensione di accesso nei dispositivi degli utenti. Questa estensione verrà reindirizzata automaticamente al servizio Proxy di applicazione appropriato. Per installare l'estensione, vedere [estensione accesso sicuro alle App personali](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

## <a name="test-your-app"></a>Test dell'app

Dopo aver completato tutti questi passaggi, l'app dovrebbe funzionare. Per testare l'app:

1. Aprire un browser e passare all'URL esterno creato dopo aver pubblicato l'app. 
1. Accedere con l'account di test assegnato all'app.

## <a name="next-steps"></a>Passaggi successivi

- [Come viene offerto l'accesso Single Sign-On dal proxy di applicazione di Azure AD?](application-proxy-single-sign-on.md)
- [Risolvere i problemi del Proxy applicazione](application-proxy-troubleshoot.md)
