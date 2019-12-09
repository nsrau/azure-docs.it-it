---
title: Eseguire il debug dell'accesso Single Sign-On basato su SAML in Azure Active Directory | Microsoft Docs
description: Eseguire il debug dell'accesso Single Sign-On basato su SAML su applicazioni in Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: luleon, hirsin, paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26c3502567df7776e106ae9301aa7ba315cc12cc
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917608"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Eseguire il debug dell'accesso Single Sign-On basato su SAML su applicazioni in Azure Active Directory

Informazioni su come individuare e correggere i problemi di [accesso Single Sign-On](../manage-apps/what-is-single-sign-on.md) per applicazioni in Azure Active Directory (Azure AD) che supportano [Security Assertion Markup Language (SAML) 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language). 

## <a name="before-you-begin"></a>Prima di iniziare

È consigliabile installare l'[estensione My Apps Secure Sign-in](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension). Questa estensione del browser consente di raccogliere facilmente la richiesta SAML e le informazioni di risposta SAML necessarie per risolvere i problemi con Single Sign-On. Nel caso non sia possibile installare l'estensione, questo articolo descrive come risolvere i problemi anche senza l'estensione installata.

Per scaricare e installare l'estensione My Apps Secure Sign-in, usare uno dei collegamenti seguenti.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

## <a name="test-saml-based-single-sign-on"></a>Testare l'accesso Single Sign-On basato su SAML

Per testare Single Sign-On basate su SAML tra Azure AD e un'applicazione di destinazione:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale e altro amministratore autorizzato a gestire le applicazioni.
1. Nel pannello sinistro selezionare **Azure Active Directory**e quindi selezionare **applicazioni aziendali**. 
1. Dall'elenco di applicazioni aziendali, selezionare l'applicazione per cui si desidera eseguire il test Single Sign-On, quindi dalle opzioni a sinistra selezionare **Single Sign-on**.
1. Per aprire l'esperienza di test di Single Sign-On basata su SAML, passare a **Test Single Sign-on** (passaggio 5). Se il pulsante **test** è disattivato, è necessario compilare e salvare prima gli attributi necessari nella sezione di **configurazione SAML di base** .
1. Nel pannello **Test dell'accesso Single Sign-On** usare le credenziali aziendali per accedere all'applicazione di destinazione. È possibile accedere come utente corrente o come altro utente. Se si accede come altro utente, verrà chiesto di eseguire l'autenticazione.

    ![Screenshot che mostra la pagina test SSO SAML](./media/howto-v1-debug-saml-sso-issues/test-single-sign-on.png)

Se è stato effettuato l'accesso, è stato superato il test. In questo caso, Azure AD ha rilasciato un token di risposta SAML all'applicazione. L'applicazione ha usato il token SAML per consentire l'accesso.

Se si verifica un errore nella pagina di accesso aziendale o nella pagina dell'applicazione, usare una delle sezioni seguenti per correggere l'errore.

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Risolvere un errore di accesso nella pagina di accesso aziendale

Quando si tenta di eseguire l'accesso, è possibile che venga visualizzato un errore nella pagina di accesso dell'azienda simile all'esempio seguente.

![Esempio che mostra un errore nella pagina di accesso dell'azienda](./media/howto-v1-debug-saml-sso-issues/error.png)

Per eseguire il debug di questo errore, sono necessari il messaggio di errore e la richiesta SAML. L'estensione My Apps Secure Sign-in raccoglie automaticamente queste informazioni e visualizza le istruzioni per risolvere il problema in Azure AD.

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>Per risolvere l'errore di accesso con l'estensione per l'accesso sicuro app personali installata

1. Quando si verifica un errore, l'estensione reindirizza di nuovo al pannello Azure AD **Test Single Sign-on** .
1. Nel pannello **Single Sign-on test** selezionare **Scarica la richiesta SAML**.
1. Vengono visualizzate le istruzioni di risoluzione specifiche basate sull'errore e sui valori presenti nella richiesta SAML.
1. Verrà visualizzato un pulsante **Correggi** per aggiornare automaticamente la configurazione in Azure ad per risolvere il problema. Se questo pulsante non è visualizzato, il problema di accesso non è dovuto a una configurazione errata in Azure AD.

Se non viene fornita alcuna risoluzione per l'errore di accesso, si consiglia di usare la casella di testo feedback per informare Microsoft.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>Per risolvere l'errore senza installare l'estensione per l'accesso sicuro alle app personali

1. Copiare il messaggio di errore nell'angolo inferiore destro della pagina. Il messaggio di errore include:
    - Un ID correlazione e il timestamp. Questi valori sono importanti quando si crea un caso di supporto con Microsoft, perché aiutano i tecnici a identificare il problema e a proporre una risoluzione precisa.
    - Una dichiarazione che identifica la causa radice del problema.
1. Tornare ad Azure AD e individuare il pannello **Test dell'accesso Single Sign-On**.
1. Nella casella di testo sopra **Ottieni procedure per la risoluzione** incollare il messaggio di errore.
1. Fare clic su **Ottieni procedure per la risoluzione** per visualizzare i passaggi per risolvere il problema. Le procedure fornite potrebbero richiedere informazioni dalla richiesta o dalla risposta SAML. Se non si usa l'estensione per l'accesso sicuro alle app personali, potrebbe essere necessario uno strumento come [Fiddler](https://www.telerik.com/fiddler) per recuperare la richiesta e la risposta SAML.
1. Verificare che la destinazione nella richiesta SAML corrisponda all'URL del servizio Single Sign-On SAML ottenuto da Azure AD.
1. Verificare che l'autorità emittente nella richiesta SAML corrisponda allo stesso identificatore configurato per l'applicazione in Azure AD. Azure AD usa l'emittente per trovare un'applicazione nella directory.
1. Verificare che AssertionConsumerServiceURL sia il punto in cui l'applicazione prevede di ricevere il token SAML dal Azure AD. È possibile configurare questo valore in Azure AD, ma non è obbligatorio se fa parte della richiesta SAML.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Risolvere un errore di accesso nella pagina dell'applicazione

Capita talvolta che si riesca a eseguire l'accesso correttamente ma che successivamente venga visualizzato un errore nella pagina dell'applicazione. Questa situazione si verifica quando Azure AD ha emesso un token per l'applicazione, ma questa non accetta la risposta.

Per risolvere l'errore, seguire questa procedura:

1. Se l'applicazione si trova nella raccolta di Azure AD, verificare di aver seguito tutti i passaggi per l'integrazione dell'applicazione con Azure AD. Per trovare le istruzioni di integrazione per l'applicazione, vedere l'[elenco di esercitazioni sull'integrazione di applicazioni SaaS](../saas-apps/tutorial-list.md).
1. Recuperare la risposta SAML.
    - Se l'estensione My Apps Secure Sign-in è installata, nel pannello **Test dell'accesso Single Sign-On** fare clic su **Scaricare la richiesta SAML**.
    - Se l'estensione non è installata, usare uno strumento quale [Fiddler](https://www.telerik.com/fiddler) per recuperare la risposta SAML.
1. Nel token della risposta SAML sono presenti gli elementi seguenti:
   - Identificatore univoco di utente del valore NameID e formato
   - Attestazioni rilasciate nel token
   - Certificato usato per firmare il token.

     Per altre informazioni sulla risposta SAML, vedere [Protocollo SAML per Single Sign-On](single-sign-on-saml-protocol.md).

1. Ora che è stata esaminata la risposta SAML, vedere [errore nella pagina di un'applicazione dopo l'accesso](../manage-apps/application-sign-in-problem-application-error.md) per istruzioni su come risolvere il problema. 
1. Se non si riesce ancora ad accedere correttamente, è possibile richiedere al fornitore dell'applicazione cosa manca dalla risposta SAML.

## <a name="next-steps"></a>Passaggi successivi

Ora che Single Sign-On sta funzionando con l'applicazione, è possibile [automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS](../manage-apps/user-provisioning.md) o iniziare a [usare l'accesso condizionale](../conditional-access/app-based-conditional-access.md).
