---
title: 'Pubblicare app client native: Azure AD | Microsoft Docs'
description: Illustra come abilitare la comunicazione tra le app client native e il connettore del proxy di applicazione di Azure AD per consentire l'accesso remoto sicuro alle app locali.
services: active-directory
documentationcenter: 
author: kgremban
manager: mtillman
ms.assetid: f0cae145-e346-4126-948f-3f699747b96e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: de49a8343a3db6c8b890050e7791f77105a09a3a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>Come abilitare le app client native per l'interazione con le applicazioni proxy

Oltre che per le applicazioni Web, è possibile usare il proxy di applicazione di Azure Active Directory per pubblicare app client native configurate con Azure AD Authentication Library (ADAL). Le app client native si differenziano dalle app Web perché vengono installate su un dispositivo, mentre le app Web sono accessibili tramite un browser. 

Il proxy di applicazione supporta le app client native accettando i token rilasciati da Azure AD e inviati nell'intestazione. Il servizio Proxy di applicazione esegue l'autenticazione per conto degli utenti. Questa soluzione non prevede l'utilizzo di token di applicazione per l'autenticazione. 

![Relazione tra utenti finali, Azure Active Directory e applicazioni pubblicate](./media/active-directory-application-proxy-native-client/richclientflow.png)

Per pubblicare applicazioni native, usare Azure AD Authentication Library, che gestisce l'autenticazione e supporta molti ambienti client. Il proxy di applicazione si integra con [lo scenario Da applicazione nativa ad API Web](develop/active-directory-authentication-scenarios.md#native-application-to-web-api). 

Questo articolo illustra i quattro passaggi necessari per pubblicare un'applicazione nativa con il proxy di applicazione e Azure AD Authentication Library. 

## <a name="step-1-publish-your-application"></a>Passaggio 1: Pubblicare l'applicazione
Pubblicare l'applicazione proxy come qualsiasi altra applicazione e assegnare agli utenti l'accesso all'applicazione. Per altre informazioni, vedere [Pubblicare applicazioni mediante il proxy di applicazione](active-directory-application-proxy-publish.md).

## <a name="step-2-configure-your-application"></a>Passaggio 2: Configurare l'applicazione
Configurare l'applicazione nativa come indicato di seguito:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare ad **Azure Active Directory** > **Registrazioni per l'app**.
3. Selezionare **Registrazione nuova applicazione**.
4. Specificare un nome, selezionare **Nativa** come tipo di applicazione e immettere l'URI di reindirizzamento per l'applicazione. 

   ![Creare una nuova registrazione di app](./media/active-directory-application-proxy-native-client/create.png)
5. Selezionare **Create**.

Per informazioni più dettagliate sulla creazione di una nuova registrazione di app, vedere [Integrazione di applicazioni con Azure Active Directory](.//develop/active-directory-integrating-applications.md).


## <a name="step-3-grant-access-to-other-applications"></a>Passaggio 3: Concedi accesso ad altre applicazioni
Abilitare l'applicazione nativa da esporre ad altre applicazioni nella directory:

1. In **Registrazioni per l'app** selezionare la nuova applicazione nativa appena creata.
2. Selezionare **Autorizzazioni necessarie**.
3. Selezionare **Aggiungi**.
4. Aprire il primo passaggio, **Selezionare un'API**.
5. Usare la barra di ricerca per trovare l'app del proxy di applicazione pubblicata nella prima sezione. Scegliere tale app e quindi fare clic su **Seleziona**. 

   ![Cercare l'app proxy](./media/active-directory-application-proxy-native-client/select_api.png)
6. Aprire il secondo passaggio, **Selezionare le autorizzazioni**.
7. Usare la casella di controllo per concedere all'applicazione proxy l'accesso all'applicazione nativa e quindi fare clic su **Seleziona**.

   ![Concedere l'accesso all'app proxy](./media/active-directory-application-proxy-native-client/select_perms.png)
8. Selezionare **Operazione completata**.


## <a name="step-4-edit-the-active-directory-authentication-library"></a>Passaggio 4: Modificare Active Directory Authentication Library
Modificare il codice dell'applicazione nativa nel contesto di autenticazione di Active Directory Authentication Library (ADAL) per includere il testo seguente:

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = authContext.AcquireToken("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

Le variabili nel codice di esempio devono essere sostituite come segue:

* Il valore di **Tenant ID** è riportato nel portale di Azure. Passare ad **Azure Active Directory** > **Proprietà** e copiare l'ID directory. 
* Il valore di **External URL** è l'URL front-end immesso nell'applicazione proxy. Per trovare questo valore, passare alla sezione **Proxy dell'applicazione** dell'app proxy.
* Il valore di **App ID of the Native app** è riportato nella pagina **Proprietà** dell'applicazione nativa.
* Il valore di **Redirect URI of the native app** è riportato nella pagina **URI di reindirizzamento** dell'applicazione nativa.

Dopo la modifica di ADAL con questi parametri, gli utenti dovrebbero essere in grado di eseguire l'autenticazione alle app client native, anche se si trovano all'esterno della rete aziendale. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul flusso delle applicazioni native, vedere [Da applicazione nativa ad API Web](develop/active-directory-authentication-scenarios.md#native-application-to-web-api)

Ottenere informazioni su come configurare [l'accesso Single Sign-On per il proxy di applicazione](application-proxy-sso-overview.md)
