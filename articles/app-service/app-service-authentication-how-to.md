---
title: Personalizzare l'autenticazione e l'autorizzazione in Servizio app di Azure | Microsoft Docs
description: Viene illustrato come personalizzare l'autenticazione e l'autorizzazione nel servizio app e ottenere attestazioni utente e token diversi.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/14/2018
ms.author: cephalin
ms.openlocfilehash: 688ea090384755b9a6d60a4968d958678edc27ad
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "36337179"
---
# <a name="customize-authentication-and-authorization-in-azure-app-service"></a>Personalizzare l'autenticazione e l'autorizzazione in Servizio app di Azure

Questo articolo illustra come personalizzare i processi di [autenticazione e autorizzazione nel servizio app](app-service-authentication-overview.md) e gestire l'identità dall'applicazione. 

Se si vuole iniziare subito, vedere una delle esercitazioni seguenti:

* [Esercitazione: Autenticare e autorizzare gli utenti end-to-end nel servizio app di Azure (Windows)](app-service-web-tutorial-auth-aad.md)
* [Esercitazione: Autenticare e autorizzare gli utenti end-to-end nel servizio app di Azure per Linux](containers/tutorial-auth-aad.md)
* [Come configurare un'applicazione per usare l'account di accesso di Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md)
* [Come configurare un'applicazione per usare l'account di accesso di Facebook](app-service-mobile-how-to-configure-facebook-authentication.md)
* [Come configurare un'applicazione per usare l'account di accesso di Google](app-service-mobile-how-to-configure-google-authentication.md)
* [Come configurare un'applicazione per usare l'account di accesso Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md)
* [Come configurare un'applicazione per usare l'account di accesso di Twitter](app-service-mobile-how-to-configure-twitter-authentication.md)

## <a name="configure-multiple-sign-in-options"></a>Configurare più opzioni di accesso

La configurazione del portale non offre una soluzione pronta all'uso per presentare agli utenti più opzioni di accesso (ad esempio tramite Facebook e Twitter). È tuttavia possibile aggiungere facilmente questa funzionalità all'app Web. Seguire questa procedura:

Prima di tutto, nella pagina **Autenticazione/Autorizzazione** nel portale di Azure configurare ogni provider di identità che si vuole abilitare.

In **Azione da eseguire quando la richiesta non è autenticata** selezionare **Consenti richieste anonime (nessuna azione)**.

Nella pagina di accesso, sulla barra di spostamento o in qualsiasi altra posizione nell'app Web aggiungere un collegamento per l'accesso a ognuno dei provider abilitati (`/.auth/login/<provider>`). Ad esempio: 

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Quando l'utente fa clic su uno dei collegamenti, viene aperta la pagina di accesso corrispondente per l'accesso dell'utente.

## <a name="access-user-claims"></a>Accedere alle attestazioni utente

Il servizio app passa le attestazioni utente all'applicazione usando intestazioni speciali. Alle richieste esterne non è consentito impostare queste intestazioni, che sono quindi presenti solo se impostate dal servizio app. Ecco alcune intestazioni di esempio:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

Il codice scritto in qualsiasi linguaggio o framework può ottenere le informazioni necessarie da queste intestazioni. Per le app ASP.NET 4.6, i valori appropriati per **ClaimsPrincipal** vengono impostati automaticamente.

L'applicazione può anche ottenere dettagli aggiuntivi sull'utente autenticato chiamando `/.auth/me`. Gli SDK server delle app per dispositivi mobili forniscono metodi di supporto per l'uso di questi dati. Per altre informazioni, vedere [Come usare Node.js SDK per le app per dispositivi mobili di Azure](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity) e [Usare l'SDK del server back-end .NET per le app per dispositivi mobili di Azure](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Recuperare i token nel codice dell'app

Dal codice server, i token specifici del provider vengono inseriti nell'intestazione della richiesta, in modo che sia possibile accedervi facilmente. La tabella seguente mostra i nomi di intestazioni token possibili:

| | |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Token di Facebook | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Account Microsoft | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Dal codice client (ad esempio un'app per dispositivi mobili o codice JavaScript nel browser), inviare una richiesta `GET` HTTP a `/.auth/me`. Il codice JSON restituito include i token specifici del provider.

> [!NOTE]
> I token di accesso consentono l'accesso alle risorse del provider, quindi sono presenti solo se si configura un provider con un segreto client. Per informazioni su come ottenere i token di aggiornamento, vedere [Aggiornare i token di accesso](#refresh-access-tokens).

## <a name="refresh-access-tokens"></a>Aggiornare i token di accesso

Quando il token di accesso del provider scade, è necessario ripetere l'autenticazione dell'utente. È possibile evitare la scadenza del token eseguendo una chiamata di `GET` nell'endpoint `/.auth/refresh` dell'applicazione. Quando il metodo viene chiamato, il servizio app aggiorna automaticamente i token di accesso nell'archivio di token per l'utente autenticato. Le successive richieste di token dal codice dell'app ottengono i token aggiornati. Affinché l'aggiornamento dei token funzioni, tuttavia, l'archivio di token deve contenere i [token di aggiornamento](https://auth0.com/learn/refresh-tokens/) per il provider. Il modo per ottenere i token di aggiornamento dipende dal provider, ma di seguito viene riportato un breve riepilogo:

- **Google**: aggiungere un parametro di stringa di query `access_type=offline` alla chiamata API di `/.auth/login/google`. Se si usa Mobile Apps SDK, è possibile aggiungere il parametro a uno degli overload `LogicAsync`. Vedere [Google Refresh Tokens](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens) (Token di aggiornamento di Google).
- **Facebook**: non vengono forniti token di aggiornamento. I token di lunga durata scadono dopo 60 giorni. Vedere [Scadenza ed estensione dei token d'accesso di Facebook](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension).
- **Twitter**: i token di accesso non scadono. Vedere le [domande frequenti su OAuth di Twitter](https://developer.twitter.com/en/docs/basics/authentication/guides/oauth-faq).
- **Account Microsoft**: in fase di [configurazione delle impostazioni di autenticazione dell'account Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md), selezionare l'ambito `wl.offline_access`.
- **Azure Active Directory**: in [https://resources.azure.com](https://resources.azure.com) seguire questa procedura:
    1. Nella parte superiore della pagina selezionare **Lettura/scrittura**.
    1. Nella finestra del browser a sinistra passare a **subscriptions** > **_\<subscription\_name_** > **resourceGroups** > _**\<resource\_group\_name>**_ > **providers** > **Microsoft.Web** > **sites** > _**\<app\_name>**_ > **config** > **authsettings**. 
    1. Fare clic su **Modifica**.
    1. Modificare la proprietà seguente. Sostituire _\<app\_id>_ con l'ID dell'applicazione Azure Active Directory del servizio a cui si vuole accedere.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    1. Fare clic su **Put**. 

Dopo aver configurato il provider è possibile [trovare il token di aggiornamento e l'ora di scadenza del token di accesso](#retrieve-tokens-in-app-code) nell'archivio token. 

Per aggiornare il token di accesso in qualsiasi momento, chiamare `/.auth/refresh` in qualsiasi linguaggio. Il frammento di codice seguente usa jQuery per aggiornare i token di accesso da un client JavaScript.

```JavaScript
function refreshTokens() {
  var refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Se un utente revoca le autorizzazioni concesse all'app, la chiamata a `/.auth/me` può avere esito negativo, con una risposta `403 Forbidden`. Per diagnosticare gli errori, controllare i log dell'applicazione per informazioni dettagliate.

## <a name="extend-session-expiration-grace-period"></a>Estendere il periodo di tolleranza della scadenza della sessione

Dopo la scadenza di una sessione autenticata, è previsto per impostazione predefinita un periodo di tolleranza di 72 ore. Durante questo periodo di tolleranza, è possibile aggiornare il cookie di sessione o il token di sessione con il servizio app senza dover eseguire di nuovo l'autenticazione dell'utente. È sufficiente chiamare `/.auth/refresh` quando il cookie di sessione o il token di sessione non è più valido e non è necessario tenere traccia della scadenza del token. Una volta trascorso il periodo di tolleranza di 72 ore, l'utente deve eseguire di nuovo l'accesso per ottenere un token di sessione o un cookie di sessione valido.

Se 72 ore non sono sufficienti, è possibile estendere questo intervallo di scadenza. L'estensione della scadenza per un lungo periodo potrebbe avere implicazioni significative sulla sicurezza (ad esempio quando un token di autenticazione viene perso o rubato). È quindi consigliabile lasciare l'impostazione predefinita di 72 ore o impostare il periodo di estensione sul valore più piccolo possibile.

Per estendere l'intervallo di scadenza predefinito, eseguire il comando seguente in [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> Il periodo di tolleranza si applica solo alla sessione autenticata del servizio app e non ai token dei provider di identità. Non è previsto alcun periodo di tolleranza per i token dei provider scaduti. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Limitare il dominio degli account di accesso

Sia l'account Microsoft che Azure Active Directory consentono l'accesso da più domini. L'account Microsoft consente, ad esempio, gli account _outlook.com_, _live.com_ e _hotmail.com_. Azure Active Directory consente un numero qualsiasi di domini personalizzati per gli account di accesso. Questo comportamento potrebbe essere inaccettabile per un'app interna a cui non si vuole concedere l'accesso agli utenti con un account _outlook.com_. Per limitare il nome di dominio degli account di accesso, seguire questa procedura.

In [https://resources.azure.com](https://resources.azure.com) passare a **subscriptions** > **_\<subscription\_name_** > **resourceGroups** > _**\<resource\_group\_name>**_ > **providers** > **Microsoft.Web** > **sites** > _**\<app\_name>**_ > **config** > **authsettings**. 

Fare clic su **Modifica**, modificare la proprietà seguente e quindi fare clic su **Put**. Assicurarsi di sostituire _\<domain\_name>_ con il dominio desiderato.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```
## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Autenticare e autorizzare gli utenti end-to-end (Windows)](app-service-web-tutorial-auth-aad.md)
> [Esercitazione: Autenticare e autorizzare gli utenti end-to-end (Linux)](containers/tutorial-auth-aad.md)