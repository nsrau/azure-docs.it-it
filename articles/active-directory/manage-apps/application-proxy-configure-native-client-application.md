---
title: 'Pubblicare app client native: Azure AD | Microsoft Docs'
description: Illustra come abilitare la comunicazione tra le app client native e il connettore del proxy di applicazione di Azure AD per consentire l'accesso remoto sicuro alle app locali.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/12/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09e813e0dff09885f104e43099b10d6f6f8a7f9b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657432"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Come abilitare applicazioni client native per l'interazione con applicazioni proxy

Azure Active Directory (Azure AD) Application Proxy può essere usato per pubblicare app Web, ma anche per pubblicare applicazioni client native configurate con Microsoft Authentication Library (MSAL). Le applicazioni client native si differenziano dalle app Web perché vengono installate in un dispositivo, mentre le app Web sono accessibili tramite un browser.

Per supportare le applicazioni client native, Application Proxy accetta i token emessi da Azure AD inviati nell'intestazione. Il servizio Application Proxy esegue l'autenticazione per gli utenti. Questa soluzione non prevede l'uso dei token di applicazione per l'autenticazione.

![Relazione tra utenti finali, Azure AD e applicazioni pubblicate](./media/application-proxy-configure-native-client-application/richclientflow.png)

Per pubblicare applicazioni native, usare Microsoft Authentication Library, che si occupa dell'autenticazione e supporta molti ambienti client. Application Proxy si inserisce nello scenario [App desktop che chiama un'API Web per conto di un utente che ha eseguito l'accesso](../develop/authentication-flows-app-scenarios.md#desktop-app-that-calls-a-web-api-on-behalf-of-a-signed-in-user).

Questo articolo illustra i quattro passaggi necessari per pubblicare un'applicazione nativa con il proxy di applicazione e Azure AD Authentication Library.

## <a name="step-1-publish-your-proxy-application"></a>Passaggio 1: Pubblicare l'applicazione proxy

Pubblicare l'applicazione proxy come qualsiasi altra applicazione e assegnare agli utenti l'accesso all'applicazione. Per altre informazioni, vedere [Pubblicare applicazioni mediante il proxy di applicazione](application-proxy-add-on-premises-application.md).

## <a name="step-2-register-your-native-application"></a>Passaggio 2: Registrare l'applicazione nativa

È ora necessario registrare l'applicazione in Azure AD, come indicato nella procedura seguente:

1. Accedere al [portale di Azure Active Directory](https://aad.portal.azure.com/). Verrà visualizzata la finestra **Dashboard** dell'**interfaccia di amministrazione di Azure Active Directory**.
1. Nella barra laterale selezionare **Azure Active Directory**. Verrà visualizzata la pagina di panoramica di **Azure Active Directory**.
1. Nella barra laterale Panoramica di Azure AD selezionare **Registrazioni app**. Verrà visualizzato l'elenco di tutte le registrazioni delle app.
1. Selezionare **Nuova registrazione**. Verrà visualizzata la pagina **Registra un'applicazione**.

   ![Creare una nuova registrazione di app nel portale di Azure](./media/application-proxy-configure-native-client-application/create.png)

1. Nell'intestazione **Nome** specificare un nome visualizzato per l'interfaccia utente dell'applicazione.
1. Nell'intestazione **Tipi di account supportati** selezionare un livello di accesso in base alle linee guida seguenti:

   - Per impostare come destinazione solo gli account interni all'organizzazione, selezionare **Account solo in questa directory organizzativa**.
   - Per impostare come destinazione solo i clienti aziendali o di istituti di istruzione, selezionare **Account in qualsiasi directory organizzativa**.
   - Per impostare come destinazione il set di identità Microsoft più ampio, selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali**.
1. In **URI di reindirizzamento** selezionare **Client pubblico/nativo (per dispositivi mobili e desktop)** e quindi digitare l'URI di reindirizzamento `https://login.microsoftonline.com/common/oauth2/nativeclient` per l'applicazione.
1. Selezionare e leggere i **criteri della piattaforma Microsoft** e quindi selezionare **Registra**. Verrà creata e visualizzata una pagina di panoramica per la registrazione della nuova applicazione.

Per informazioni più dettagliate sulla creazione di una nuova registrazione di applicazione, vedere [Integrazione di applicazioni con Azure Active Directory](../develop/quickstart-register-app.md).

## <a name="step-3-grant-access-to-your-proxy-application"></a>Passaggio 3: Concedere l'accesso all'applicazione proxy

Dopo la registrazione, è possibile consentire all'applicazione nativa di accedere ad altre applicazioni nella directory, in questo caso all'applicazione proxy. Per consentire l'esposizione dell'applicazione nativa all'applicazione proxy:

1. Nella barra laterale della pagina relativa alla nuova registrazione di applicazione selezionare **Autorizzazioni API**. Verrà visualizzata la pagina **Autorizzazioni API** per la nuova registrazione di applicazione.
1. Selezionare **Aggiungi un'autorizzazione**. Verrà visualizzata la pagina **Richiedi le autorizzazioni dell'API**.
1. Nell'impostazione **Selezionare un'API** selezionare **API usate dall'organizzazione**. Verrà visualizzato un elenco contenente le applicazioni presenti nella directory che espongono API.
1. Digitare nella casella di ricerca o scorrere l'elenco per trovare l'applicazione proxy pubblicata in [Passaggio 1: Pubblicare l'applicazione proxy](#step-1-publish-your-proxy-application) e quindi selezionare l'applicazione proxy.
1. Nell'intestazione **Indicare le autorizzazioni necessarie per l'applicazione** selezionare il tipo di autorizzazione. Se l'applicazione nativa deve accedere all'API dell'applicazione proxy come utente che ha eseguito l'accesso, scegliere **Autorizzazioni delegate**.
1. Nell'intestazione **Selezionare le autorizzazioni** selezionare l'autorizzazione desiderata e quindi **Aggiungi autorizzazioni**. La pagina **Autorizzazioni API** dell'applicazione nativa visualizza ora l'applicazione proxy e l'API di autorizzazione aggiunta.

## <a name="step-4-add-the-microsoft-authentication-library-to-your-code-net-c-sample"></a>Passaggio 4: Aggiungere Microsoft Authentication Library al codice (esempio C# .NET)

Modificare il codice dell'applicazione nativa nel contesto di autenticazione di Microsoft Authentication Library (MSAL) per includere il testo seguente: 

```         
// Acquire Access Token from AAD for Proxy Application
IPublicClientApplication clientApp = PublicClientApplicationBuilder
.Create(<App ID of the Native app>)
.WithDefaultRedirectUri() // will automatically use the default Uri for native app
.WithAuthority("https://login.microsoftonline.com/{<Tenant ID>}")
.Build();

AuthenticationResult authResult = null;
var accounts = await clientApp.GetAccountsAsync();
IAccount account = accounts.FirstOrDefault();

IEnumerable<string> scopes = new string[] {"<Scope>"};

try
 {
    authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
 }
    catch (MsalUiRequiredException ex)
 {
     authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();                
 }

if (authResult != null)
 {
  //Use the Access Token to access the Proxy Application

  HttpClient httpClient = new HttpClient();
  HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
  HttpResponseMessage response = await httpClient.GetAsync("<Proxy App Url>");
 }
```

Le informazioni necessarie nel codice di esempio sono disponibili nel portale di Azure AD nei percorsi seguenti:

| Informazione necessaria | Come trovarla nel portale di Azure AD |
| --- | --- |
| \<Tenant ID> | **Azure Active Directory** > **Proprietà** > **ID directory** |
| \<App ID of the Native app> | **Registrazione dell'applicazione** > *applicazione nativa* > **Panoramica** > **ID applicazione** |
| \<Scope> | **Registrazione dell'applicazione** > *applicazione nativa* > **Autorizzazioni API** > Fare clic sull'API di autorizzazione (user_impersonation) > Sul lato destro viene visualizzato un riquadro con il sottotitolo **user_impersonation**. > L'ambito è costituito dall'URL presente nella casella di modifica.
| \<Proxy App Url> | URL esterno e percorso dell'API.

Dopo che il codice di MSAL è stato modificato con questi parametri, gli utenti possono eseguire l'autenticazione alle applicazioni client native anche quando si trovano al di fuori della rete aziendale.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul flusso delle applicazioni native, vedere [App native in Azure Active Directory](../azuread-dev/native-app.md).

Vedere le informazioni sulla configurazione dell'[accesso Single Sign-On alle applicazioni in Azure Active Directory](sso-options.md#choosing-a-single-sign-on-method).