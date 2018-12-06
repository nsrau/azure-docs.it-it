---
title: Eseguire il debug dell'accesso Single Sign-On basato su SAML in Azure Active Directory | Microsoft Docs
description: Eseguire il debug dell'accesso Single Sign-On basato su SAML su applicazioni in Azure Active Directory.
services: active-directory
author: CelesteDG
documentationcenter: na
manager: mtillman
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/15/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin, dastrock, smalser
ms.openlocfilehash: 7f653eca0c768cc35df039cbd51153484710d80a
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52422254"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Eseguire il debug dell'accesso Single Sign-On basato su SAML su applicazioni in Azure Active Directory

Informazioni su come individuare e correggere i problemi di [accesso Single Sign-On](../manage-apps/what-is-single-sign-on.md) per applicazioni in Azure Active Directory (Azure AD) che supportano [Security Assertion Markup Language (SAML) 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language). 

## <a name="before-you-begin"></a>Prima di iniziare
È consigliabile installare l'[estensione My Apps Secure Sign-in](../user-help/active-directory-saas-access-panel-user-help.md#i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension). Questa estensione di browser semplifica la raccolta delle informazioni relative alle richieste e alle risposte SAML necessarie per risolvere i problemi con l'accesso Single Sign-On. Nel caso non sia possibile installare l'estensione, questo articolo descrive come risolvere i problemi anche senza l'estensione installata.

Per scaricare e installare l'estensione My Apps Secure Sign-in, usare uno dei collegamenti seguenti.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)


## <a name="test-saml-based-single-sign-on"></a>Testare l'accesso Single Sign-On basato su SAML

Per testare l'accesso Single Sign-On basato su SAML tra Azure Active Directory e un'applicazione di destinazione:

1.  Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale e altro amministratore autorizzato a gestire le applicazioni.
2.  Nel pannello di sinistra fare clic su **Azure Active Directory** e quindi su **Applicazioni aziendali**. 
3.  Nell'elenco Applicazioni aziendali fare clic sull'applicazione per cui si vuole testare l'accesso Single Sign-On, quindi nelle opzioni a sinistra fare clic su **Single Sign-On**.
4.  Per aprire l'esperienza di test dell'accesso Single Sign-On basato su SAML, nella sezione **Dominio e URL** fare clic su **Test impostazioni SAML**. Se il pulsante Test impostazioni SAML appare disattivato, occorre prima immettere e salvare gli attributi necessari.
5.  Nel pannello **Test dell'accesso Single Sign-On** usare le credenziali aziendali per accedere all'applicazione di destinazione. È possibile accedere come utente corrente o come altro utente. Se si accede come altro utente, verrà chiesto di eseguire l'autenticazione.

    ![Pagina Test SAML](./media/howto-v1-debug-saml-sso-issues/testing.png)


Se è stato effettuato l'accesso, è stato superato il test. In questo caso, Azure AD ha rilasciato un token di risposta SAML all'applicazione. L'applicazione ha usato il token SAML per consentire l'accesso.

Se si verifica un errore nella pagina di accesso aziendale o nella pagina dell'applicazione, usare una delle sezioni seguenti per correggere l'errore.


## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Risolvere un errore di accesso nella pagina di accesso aziendale

Quando si tenta di eseguire l'accesso, è possibile che si verifichi un errore nella pagina di accesso aziendale. 

![Errore di accesso](./media/howto-v1-debug-saml-sso-issues/error.png)

Per eseguire il debug di questo errore, sono necessari il messaggio di errore e la richiesta SAML. L'estensione My Apps Secure Sign-in raccoglie automaticamente queste informazioni e visualizza le istruzioni per risolvere il problema in Azure AD. 

Per risolvere l'errore di accesso con l'estensione MyApps Secure Sign-in installata:

1.  Quando si verifica un errore, l'estensione reindirizza l'utente al pannello **Test dell'accesso Single Sign-On** di Azure AD. 
2.  Nel pannello **Test dell'accesso Single Sign-On** fare clic su **Scaricare la richiesta SAML**. 
3.  Vengono visualizzate le istruzioni di risoluzione specifiche basate sull'errore e sui valori presenti nella richiesta SAML. Esaminare le istruzioni.

Per risolvere l'errore senza installare l'estensione MyApps Secure Sign-in:

1. Copiare il messaggio di errore nell'angolo inferiore destro della pagina. Il messaggio di errore include:
    - Un ID correlazione e il timestamp. Questi valori sono importanti quando si crea un caso di supporto con Microsoft, perché aiutano i tecnici a identificare il problema e a proporre una risoluzione precisa.
    - Una dichiarazione che identifica la causa radice del problema.
2.  Tornare ad Azure AD e individuare il pannello **Test dell'accesso Single Sign-On**.
3.  Nella casella di testo sopra **Ottieni procedure per la risoluzione** incollare il messaggio di errore.
3.  Fare clic su **Ottieni procedure per la risoluzione** per visualizzare i passaggi per risolvere il problema. Le procedure fornite potrebbero richiedere informazioni dalla richiesta o dalla risposta SAML. Se non si usa l'estensione MyApps Secure Sign-in, potrebbe essere necessario usare uno strumento come [Fiddler](https://www.telerik.com/fiddler) per recuperare la richiesta e la risposta SAML.
4.  Verificare che la destinazione nella richiesta SAML corrisponda all'URL del servizio Single Sign-On SAML ottenuto da Azure Active Directory
5.  Verificare che l'emittente nella richiesta SAML sia lo stesso identificatore che è stato configurato per l'applicazione in Azure Active Directory. Azure AD usa l'emittente per trovare un'applicazione nella directory.
6.  Verificare che AssertionConsumerServiceURL sia l'URL in cui l'applicazione prevede di ricevere il token SAML da Azure Active Directory. È possibile configurare questo valore in Azure Active Directory, ma non è obbligatorio se fa parte della richiesta SAML.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Risolvere un errore di accesso nella pagina dell'applicazione

Capita talvolta che si riesca a eseguire l'accesso correttamente ma che successivamente venga visualizzato un errore nella pagina dell'applicazione. Questa situazione si verifica quando Azure AD ha emesso un token per l'applicazione, ma questa non accetta la risposta.   

Per risolvere l'errore:

1. Se l'applicazione si trova nella raccolta di Azure Active Directory, verificare di avere seguito tutti i passaggi per integrare l'applicazione con Azure AD. Per trovare le istruzioni di integrazione per l'applicazione, vedere l'[elenco di esercitazioni sull'integrazione di applicazioni SaaS](../saas-apps/tutorial-list.md).
2. Recuperare la risposta SAML.
    - Se l'estensione My Apps Secure Sign-in è installata, nel pannello **Test dell'accesso Single Sign-On** fare clic su **Scaricare la richiesta SAML**.
    - Se l'estensione non è installata, usare uno strumento quale [Fiddler](https://www.telerik.com/fiddler) per recuperare la risposta SAML. 
3. Nel token della risposta SAML sono presenti gli elementi seguenti:
    - Identificatore univoco di utente del valore NameID e formato
    - Attestazioni rilasciate nel token
    - Certificato usato per firmare il token. Per informazioni su come esaminare la risposta SAML, vedere [Protocollo SAML per Single Sign-On](single-sign-on-saml-protocol.md).
4. Per altre informazioni sulla risposta SAML, vedere [Protocollo SAML per Single Sign-On](single-sign-on-saml-protocol.md).
5. Dopo aver esaminato la risposta SAML, vedere [Errore nella pagina dell'applicazione dopo l'accesso](../manage-apps/application-sign-in-problem-application-error.md) per indicazioni sulla risoluzione del problema. 
6. Se non si è ancora in grado di accedere correttamente, è possibile chiedere al fornitore dell'applicazione le informazioni mancanti nella risposta SAML.


## <a name="next-steps"></a>Passaggi successivi
Con l'accesso Single Sign-On operativo nell'applicazione, è possibile a questo punto [automatizzare il provisioning e il deprovisioning degli utenti nelle applicazioni SaaS](../manage-apps/user-provisioning.md) o eseguire le [attività iniziali per l'accesso condizionale](../conditional-access/app-based-conditional-access.md).


