---
title: Come abilitare la pubblicazione delle app client native con le applicazioni proxy | Documentazione Microsoft
description: Illustra come abilitare la comunicazione tra le app client native e il connettore del proxy di applicazione di Azure AD per consentire l&quot;accesso remoto sicuro alle app locali.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: f0cae145-e346-4126-948f-3f699747b96e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 8bf2d7d35f7cf14b4f5430624f23d5b3b9c3c6be


---
# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>Come abilitare le app client native per l'interazione con le applicazioni proxy
Il proxy di applicazione di Azure Active Directory è ampiamente usato per pubblicare applicazioni browser come SharePoint, Outlook Web Access e applicazioni line-of-business personalizzate. Può essere usato anche per pubblicare app client native, che differiscono dalle app Web perché vengono installate in un dispositivo. A tale scopo, è necessario il supporto di token di Azure AD inviati in intestazioni HTTP Authorize standard.

![Relazione tra utenti finali, Azure Active Directory e applicazioni pubblicate](./media/active-directory-application-proxy-native-client/richclientflow.png)

Il metodo consigliato per la pubblicazione di tali applicazioni è l'uso della libreria di autenticazione di Azure AD che si occupa di tutte le questioni correlate all'autenticazione e supporta molti ambienti client diversi. Il proxy di applicazione si integra con [lo scenario Da applicazione nativa ad API Web](active-directory-authentication-scenarios.md#native-application-to-web-api). A tale scopo, seguire questa procedura:

## <a name="step-1-publish-your-application"></a>Passaggio 1: Pubblicare l'applicazione
Pubblicare il proxy di applicazione come qualsiasi altra applicazione, assegnare gli utenti e concedere loro licenze di base o premium. Per altre informazioni, vedere [Pubblicare applicazioni mediante il proxy di applicazione](active-directory-application-proxy-publish.md).

## <a name="step-2-configure-your-application"></a>Passaggio 2: Configurare l'applicazione
Configurare l'applicazione nativa come indicato di seguito:

1. Accedere al portale di Microsoft Azure classico.
2. Scegliere l'icona Active Directory dal menu a sinistra e quindi selezionare la directory.
3. Nel menu principale fare clic su **applicazioni**. Se alla directory non è stata aggiunta alcuna applicazione, la pagina mostrerà solo il collegamento **Aggiungi app**. Fare clic sul collegamento o, in alternativa, fare clic sul pulsante **Aggiungi** sulla barra dei comandi.
4. Nella pagina **Come procedere** fare clic sul collegamento **Aggiungi un'applicazione che l'organizzazione sta sviluppando**.
5. Nella pagina **Informazioni sull'applicazione** specificare un nome per l'applicazione e scegliere **Applicazione client nativa**. Fare clic sull'icona a forma di freccia per continuare.
6. Nella pagina **Informazioni sull'applicazione** specificare un valore in **URI di reindirizzamento** per l'applicazione client nativa, quindi fare clic sul segno di spunta per completare l'operazione.

L'applicazione viene aggiunta e si viene quindi reindirizzati alla pagina Avvio rapido per l'applicazione.

## <a name="step-3-grant-access-to-other-applications"></a>Passaggio 3: Concedi accesso ad altre applicazioni
Abilitare l'applicazione nativa da esporre ad altre applicazioni nella directory:

1. Scegliere **Applicazioni** dal menu in alto, selezionare la nuova applicazione nativa e quindi fare clic su **Configura**.
2. Scorrere verso il basso fino alla sezione **autorizzazioni per altre applicazioni** . Fare clic sul pulsante **Aggiungi applicazione** e selezionare l'applicazione proxy a cui si desidera concedere l'accesso dell'applicazione nativa e fare clic sul segno di spunta nell'angolo inferiore destro. Nel menu a discesa **Autorizzazioni delegate** selezionare la nuova autorizzazione.

![Autorizzazioni per altre applicazioni - Aggiungi applicazione.](./media/active-directory-application-proxy-native-client/delegate_native_app.png)

## <a name="step-4-edit-the-active-directory-authentication-library"></a>Passaggio 4: Modificare Active Directory Authentication Library
Modificare il codice dell'applicazione nativa nel contesto di autenticazione di Active Directory Authentication Library (ADAL) per includere quanto segue:

        // Acquire Access Token from AAD for Proxy Application
        AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<TenantId>");
        AuthenticationResult result = authContext.AcquireToken("< Frontend Url of Proxy App >",
                                                        "< Client Id of the Native app>",
                                                        new Uri("< Redirect Uri of the Native App>"),
                                                        PromptBehavior.Never);

        //Use the Access Token to access the Proxy Application
        HttpClient httpClient = new HttpClient();
        httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
        HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");

Le variabili devono essere sostituite come segue:

* **TenantId** è reperibile nel GUID nell'URL della pagina **Configurazione** dell'applicazione, subito dopo "/Directory/".
* **URL front-end** è l'URL front-end immesso nell'applicazione proxy e si trova nella pagina **Configurazione** dell'app proxy.
* **ID client** dell'app nativa che può trovarsi nella pagina **Configura** dell'applicazione nativa.
* **URI di reindirizzamento** dell'app nativa che può trovarsi nella pagina **Configura** dell'applicazione nativa.

![Schermata della pagina di configurazione della nuova applicazione nativa](./media/active-directory-application-proxy-native-client/new_native_app.png)

Per altre informazioni sul flusso dell'applicazione nativa, vedere [Da applicazione nativa ad API Web](active-directory-authentication-scenarios.md#native-application-to-web-api).

## <a name="see-also"></a>Vedere anche
* [Pubblicare applicazioni mediante il proprio nome di dominio](active-directory-application-proxy-custom-domains.md)
* [Abilitare l'accesso condizionale](active-directory-application-proxy-conditional-access.md)
* [Lavorare con applicazioni grado di riconoscere attestazioni](active-directory-application-proxy-claims-aware-apps.md)
* [Abilitare l'accesso Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)

Per le notizie e gli aggiornamenti più recenti, vedere [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/)




<!--HONumber=Nov16_HO3-->


