---
title: Eseguire il debug dell'accesso Single Sign-On basato su SAML in Azure Active Directory | Microsoft Docs
description: Eseguire il debug dell'accesso Single Sign-On basato su SAML su applicazioni in Azure Active Directory.
services: active-directory
author: rwike77
documentationcenter: na
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: luleon, hirsin, smalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4331acf639af90448b5508e3487f4979e9b82c45
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482717"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Eseguire il debug dell'accesso Single Sign-On basato su SAML su applicazioni in Azure Active Directory

Informazioni su come individuare e correggere i problemi di [accesso Single Sign-On](../manage-apps/what-is-single-sign-on.md) per applicazioni in Azure Active Directory (Azure AD) che supportano [Security Assertion Markup Language (SAML) 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language). 

## <a name="before-you-begin"></a>Prima di iniziare

È consigliabile installare l'[estensione My Apps Secure Sign-in](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension). Questa estensione del browser rende più semplice raccogliere la richiesta SAML e alle informazioni relative alla risposta SAML che è necessario per la risoluzione dei problemi con single sign-on. Nel caso non sia possibile installare l'estensione, questo articolo descrive come risolvere i problemi anche senza l'estensione installata.

Per scaricare e installare l'estensione My Apps Secure Sign-in, usare uno dei collegamenti seguenti.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

## <a name="test-saml-based-single-sign-on"></a>Testare l'accesso Single Sign-On basato su SAML

Per eseguire il test basato su SAML single sign-on tra Azure AD e un'applicazione di destinazione:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale e altro amministratore autorizzato a gestire le applicazioni.
1. Nel pannello a sinistra, selezionare **Azure Active Directory**, quindi selezionare **applicazioni aziendali**. 
1. Nell'elenco delle applicazioni aziendali, selezionare l'applicazione per cui si desidera testare single sign-on, quindi tra le opzioni a sinistra selezionare **l'accesso Single sign-on**.
1. Per aprire basato su SAML single sign-on di esperienza di test, passare a **testare single sign-on** (passaggio 5). Se il **Test** pulsante è disabilitato, è necessario compilare prima di tutto e salvare gli attributi richiesti **base di configurazione SAML** sezione.
1. Nel pannello **Test dell'accesso Single Sign-On** usare le credenziali aziendali per accedere all'applicazione di destinazione. È possibile accedere come utente corrente o come altro utente. Se si accede come altro utente, verrà chiesto di eseguire l'autenticazione.

    ![Screenshot che mostra il test pagina SAML SSO](./media/howto-v1-debug-saml-sso-issues/test-single-sign-on.png)

Se è stato effettuato l'accesso, è stato superato il test. In questo caso, Azure AD ha rilasciato un token di risposta SAML all'applicazione. L'applicazione ha usato il token SAML per consentire l'accesso.

Se si verifica un errore nella pagina di accesso aziendale o nella pagina dell'applicazione, usare una delle sezioni seguenti per correggere l'errore.

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Risolvere un errore di accesso nella pagina di accesso aziendale

Quando si tenta di eseguire l'accesso, si potrebbe essere visualizzato un errore sulla pagina di accesso società che è simile all'esempio seguente.

![Esempio che mostra un errore nella pagina di accesso aziendale](./media/howto-v1-debug-saml-sso-issues/error.png)

Per eseguire il debug di questo errore, sono necessari il messaggio di errore e la richiesta SAML. L'estensione My Apps Secure Sign-in raccoglie automaticamente queste informazioni e visualizza le istruzioni per risolvere il problema in Azure AD.

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>Per risolvere l'errore di accesso con l'estensione accesso sicuro alle App personali installato

1. Quando si verifica un errore, l'estensione reindirizza l'utente ad Azure AD **testare single sign-on** pannello.
1. Nel **testare single sign-on** blade, selezionare **scaricare la richiesta SAML**.
1. Vengono visualizzate le istruzioni di risoluzione specifiche basate sull'errore e sui valori presenti nella richiesta SAML.
1. Si noterà una **risolverlo** pulsante per aggiornare automaticamente la configurazione in Azure AD per risolvere il problema. Se questo pulsante non è visualizzato, quindi il problema di accesso non è a causa di un errore di configurazione in Azure AD.

Se non è disponibile alcuna soluzione per l'errore di accesso, si consiglia di usare la casella di testo di commenti e suggerimenti di informare Microsoft.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>Per risolvere l'errore senza installare l'estensione accesso sicuro alle App personali

1. Copiare il messaggio di errore nell'angolo inferiore destro della pagina. Il messaggio di errore include:
    - Un ID correlazione e il timestamp. Questi valori sono importanti quando si crea un caso di supporto con Microsoft, perché aiutano i tecnici a identificare il problema e a proporre una risoluzione precisa.
    - Una dichiarazione che identifica la causa radice del problema.
1. Tornare ad Azure AD e individuare il pannello **Test dell'accesso Single Sign-On**.
1. Nella casella di testo sopra **Ottieni procedure per la risoluzione** incollare il messaggio di errore.
1. Fare clic su **Ottieni procedure per la risoluzione** per visualizzare i passaggi per risolvere il problema. Le procedure fornite potrebbero richiedere informazioni dalla richiesta o dalla risposta SAML. Se non si usa l'estensione accesso sicuro alle App personali, è necessario uno strumento, ad esempio [Fiddler](https://www.telerik.com/fiddler) per recuperare la richiesta SAML e la risposta.
1. Verificare che la destinazione nella richiesta SAML corrisponda al SAML Single Sign-On di URL del servizio ottenuto da Azure AD.
1. Verificare che l'autorità di certificazione nella richiesta SAML sia lo stesso identificatore che è stato configurato per l'applicazione in Azure AD. Azure AD usa l'emittente per trovare un'applicazione nella directory.
1. Verificare che sia AssertionConsumerServiceURL in cui l'applicazione si aspetta di ricevere il token SAML da Azure AD. È possibile configurare questo valore in Azure AD, ma non è obbligatorio se fa parte della richiesta SAML.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Risolvere un errore di accesso nella pagina dell'applicazione

Capita talvolta che si riesca a eseguire l'accesso correttamente ma che successivamente venga visualizzato un errore nella pagina dell'applicazione. Questa situazione si verifica quando Azure AD ha emesso un token per l'applicazione, ma questa non accetta la risposta.

Per risolvere l'errore, seguire questa procedura:

1. Se l'applicazione è nella raccolta di Azure AD, verificare di aver seguito tutti i passaggi per l'integrazione dell'applicazione con Azure AD. Per trovare le istruzioni di integrazione per l'applicazione, vedere l'[elenco di esercitazioni sull'integrazione di applicazioni SaaS](../saas-apps/tutorial-list.md).
1. Recuperare la risposta SAML.
    - Se l'estensione My Apps Secure Sign-in è installata, nel pannello **Test dell'accesso Single Sign-On** fare clic su **Scaricare la richiesta SAML**.
    - Se l'estensione non è installata, usare uno strumento quale [Fiddler](https://www.telerik.com/fiddler) per recuperare la risposta SAML.
1. Nel token della risposta SAML sono presenti gli elementi seguenti:
   - Identificatore univoco di utente del valore NameID e formato
   - Attestazioni rilasciate nel token
   - Certificato usato per firmare il token.

     Per altre informazioni sulla risposta SAML, vedere [Protocollo SAML per Single Sign-On](single-sign-on-saml-protocol.md).

1. Dopo aver esaminato la risposta SAML, vedere [errore nella pagina di un'applicazione dopo l'accesso](../manage-apps/application-sign-in-problem-application-error.md) per indicazioni su come risolvere il problema. 
1. Se non si riesce ad accedere correttamente, è possibile chiedere al fornitore dell'applicazione che cos'è mancante dalla risposta SAML.

## <a name="next-steps"></a>Passaggi successivi

Ora che l'accesso single sign-on funziona all'applicazione, è Impossibile [automatizzare il provisioning e deprovisioning di applicazioni SaaS](../manage-apps/user-provisioning.md) oppure [Introduzione all'accesso condizionale](../conditional-access/app-based-conditional-access.md).
