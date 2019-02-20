---
title: Uso avanzato dell'autenticazione e dell'autorizzazione - Servizio app di Azure | Microsoft Docs
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
ms.date: 11/08/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: da3c90579dafebf1b5b362c7129e681663a0a7ab
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55891879"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Uso avanzato dell'autenticazione e dell'autorizzazione in Servizio app di Azure

Questo articolo illustra come personalizzare i processi predefiniti di [autenticazione e autorizzazione nel servizio app](overview-authentication-authorization.md) e come gestire l'identità dall'applicazione. 

Se si vuole iniziare subito, vedere una delle esercitazioni seguenti:

* [Esercitazione: Autenticare e autorizzare gli utenti end-to-end nel Servizio app di Azure (Windows)](app-service-web-tutorial-auth-aad.md)
* [Esercitazione: Autenticare e autorizzare gli utenti end-to-end nel servizio app di Azure per Linux](containers/tutorial-auth-aad.md)
* [Come configurare un'applicazione per usare l'account di accesso di Azure Active Directory](configure-authentication-provider-aad.md)
* [Come configurare un'applicazione per usare l'account di accesso di Facebook](configure-authentication-provider-facebook.md)
* [Come configurare un'applicazione per usare l'account di accesso di Google](configure-authentication-provider-google.md)
* [Come configurare un'applicazione per usare l'account di accesso Microsoft](configure-authentication-provider-microsoft.md)
* [Come configurare un'applicazione per usare l'account di accesso di Twitter](configure-authentication-provider-twitter.md)

## <a name="use-multiple-sign-in-providers"></a>Usare più provider di accesso

La configurazione del portale non offre una soluzione pronta all'uso per presentare agli utenti più provider di accesso, ad esempio tramite Facebook e Twitter. È tuttavia possibile aggiungere facilmente questa funzionalità all'app. Seguire questa procedura:

Prima di tutto, nella pagina **Autenticazione/Autorizzazione** nel portale di Azure configurare ogni provider di identità che si vuole abilitare.

In **Azione da eseguire quando la richiesta non è autenticata** selezionare **Consenti richieste anonime (nessuna azione)**.

Nella pagina di accesso, sulla barra di spostamento o in qualsiasi altra posizione nell'app aggiungere un collegamento per l'accesso a ognuno dei provider abilitati (`/.auth/login/<provider>`). Ad esempio: 

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Quando l'utente fa clic su uno dei collegamenti, viene aperta la pagina di accesso corrispondente per l'accesso dell'utente.

Per reindirizzare l'utente dopo l'accesso a un URL personalizzato, usare il parametro della stringa di query `post_login_redirect_url`, da non confondere con l'URI di reindirizzamento nella configurazione del provider di identità. Ad esempio, per passare l'utente a `/Home/Index` dopo l'accesso, usare il seguente codice HTML:

```HTML
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Convalidare i token dai provider

In un accesso client l'applicazione esegue l'accesso manuale dell'utente al provider e poi invia il token di autenticazione al servizio app per la convalida (vedere [Flusso di autenticazione](overview-authentication-authorization.md#authentication-flow)). La convalida non garantisce effettivamente l'accesso alle risorse dell'app desiderate, ma il completamento della convalida fornisce un token di sessione da usare per accedere alle risorse dell'app. 

Per convalidare il token del provider, l'app del servizio app deve essere innanzitutto configurata con il provider desiderato. In fase di esecuzione, dopo aver recuperato il token di autenticazione dal provider, inviare il token a `/.auth/login/<provider>` per la convalida. Ad esempio:  

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

Il formato del token varia leggermente in base al provider. Per informazioni dettagliate, vedere la tabella seguente:

| Valore provider | Obbligatorio nel corpo della richiesta | Commenti |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | La proprietà `expires_in` è facoltativa. <br/>Quando si richiede il token da servizi Live, richiedere sempre l'ambito `wl.basic`. |
| `google` | `{"id_token":"<id_token>"}` | La proprietà `authorization_code` è facoltativa. Se specificata, può essere facoltativamente accompagnata dalla proprietà `redirect_uri`. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Usare un [token di accesso valido dell'utente](https://developers.facebook.com/docs/facebook-login/access-tokens) da Facebook. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Se il token del provider viene convalidato, l'API restituisce un token `authenticationToken` nel corpo della risposta, che corrisponde al token di sessione. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Dopo aver ottenuto il token di sessione, è possibile accedere alle risorse dell'app protette aggiungendo l'intestazione `X-ZUMO-AUTH` alle richieste HTTP. Ad esempio:  

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Disconnettersi da una sessione

Gli utenti possono avviare una disconnessione inviando una richiesta `GET` all'endpoint `/.auth/logout` dell'app. La richiesta `GET` esegue le operazioni seguenti:

- Cancella i cookie di autenticazione dalla sessione corrente.
- Elimina i token dell'utente corrente dall'archivio di token.
- Per Azure Active Directory e Google esegue una disconnessione lato server nel provider di identità.

Ecco un semplice collegamento di disconnessione in una pagina Web:

```HTML
<a href="/.auth/logout">Sign out</a>
```

Per impostazione predefinita, una corretta disconnessione reindirizza il client all'URL `/.auth/logout/done`. È possibile cambiare la pagina di reindirizzamento post-connessione aggiungendo il parametro di query `post_logout_redirect_uri`. Ad esempio: 

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

È consigliabile [codificare](https://wikipedia.org/wiki/Percent-encoding) il valore di `post_logout_redirect_uri`.

Quando si usano URL completamente qualificati, l'URL deve essere ospitato nello stesso dominio o configurato come URL di reindirizzamento esterno consentito per l'app. Nell'esempio seguente per eseguire il reindirizzamento a `https://myexternalurl.com` che non è ospitato nello stesso dominio:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

È necessario eseguire il comando seguente in [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>Mantenere i frammenti di URL

Dopo che gli utenti hanno eseguito l'accesso all'app, di solito vogliono essere reindirizzati alla stessa sezione della stessa pagina, ad esempio `/wiki/Main_Page#SectionZ`. Tuttavia, poiché i [frammenti di URL](https://wikipedia.org/wiki/Fragment_identifier), ad esempio `#SectionZ`, non vengono mai inviati al server, non sono mantenuti per impostazione predefinita dopo che l'accesso OAuth si completa e ritorna all'app. Gli utenti otterranno un'esperienza ottimale quando dovranno tornare di nuovo al segnalibro. Questa limitazione si applica a tutte le soluzioni di autenticazione lato server.

Nell'autenticazione del servizio app è possibile mantenere i frammenti di URL durante l'accesso OAuth. Per farlo è necessario configurare l'impostazione app `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` su `true`. Si procede dal [portale di Azure](https://portal.azure.com) o semplicemente eseguendo il comando seguente in [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Accedere alle attestazioni utente

Il servizio app passa le attestazioni utente all'applicazione usando intestazioni speciali. Alle richieste esterne non è consentito impostare queste intestazioni, che sono quindi presenti solo se impostate dal servizio app. Ecco alcune intestazioni di esempio:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

Il codice scritto in qualsiasi linguaggio o framework può ottenere le informazioni necessarie da queste intestazioni. Per le app ASP.NET 4.6, i valori appropriati per **ClaimsPrincipal** vengono impostati automaticamente.

L'applicazione può anche ottenere dettagli aggiuntivi sull'utente autenticato chiamando `/.auth/me`. Gli SDK server delle app per dispositivi mobili forniscono metodi di supporto per l'uso di questi dati. Per altre informazioni, vedere [Come usare Node.js SDK per le app per dispositivi mobili di Azure](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity) e [Usare l'SDK del server back-end .NET per le app per dispositivi mobili di Azure](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Recuperare i token nel codice dell'app

Dal codice server, i token specifici del provider vengono inseriti nell'intestazione della richiesta, in modo che sia possibile accedervi facilmente. La tabella seguente mostra i nomi di intestazioni token possibili:

| Provider | Nomi dell'intestazione |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Token di Facebook | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Account Microsoft | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Dal codice client (ad esempio un'app per dispositivi mobili o codice JavaScript nel browser), inviare una richiesta `GET` HTTP a `/.auth/me`. Il codice JSON restituito include i token specifici del provider.

> [!NOTE]
> I token di accesso consentono l'accesso alle risorse del provider, quindi sono presenti solo se si configura un provider con un segreto client. Per informazioni su come ottenere i token di aggiornamento, vedere Aggiornare i token di accesso.

## <a name="refresh-identity-provider-tokens"></a>Aggiornare i token del provider di identità

Quando il token di accesso del provider (non il [token di sessione](#extend-session-token-expiration-grace-period)) scade, è necessario autenticare nuovamente l'utente prima di riutilizzare tale token. È possibile evitare la scadenza del token eseguendo una chiamata di `GET` nell'endpoint `/.auth/refresh` dell'applicazione. Quando il metodo viene chiamato, il servizio app aggiorna automaticamente i token di accesso nell'archivio di token per l'utente autenticato. Le successive richieste di token dal codice dell'app ottengono i token aggiornati. Affinché l'aggiornamento dei token funzioni, tuttavia, l'archivio di token deve contenere i [token di aggiornamento](https://auth0.com/learn/refresh-tokens/) per il provider. Il modo per ottenere i token di aggiornamento dipende dal provider, ma di seguito viene riportato un breve riepilogo:

- **Google**: Aggiungere un parametro di stringa di query `access_type=offline` alla chiamata API di `/.auth/login/google`. Se si usa Mobile Apps SDK, è possibile aggiungere il parametro a uno degli overload `LogicAsync`. Vedere [Google Refresh Tokens](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens) (Token di aggiornamento di Google).
- **Facebook**: Non vengono forniti token di aggiornamento. I token di lunga durata scadono dopo 60 giorni. Vedere [Scadenza ed estensione dei token d'accesso di Facebook](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension).
- **Twitter**: I token di accesso non scadono. Vedere le [domande frequenti su OAuth di Twitter](https://developer.twitter.com/en/docs/basics/authentication/FAQ).
- **Account Microsoft**: In fase di [configurazione delle impostazioni di autenticazione dell'account Microsoft](configure-authentication-provider-microsoft.md), selezionare l'ambito `wl.offline_access`.
- **Azure Active Directory**: In [https://resources.azure.com](https://resources.azure.com) seguire anche questa procedura:
    1. Nella parte superiore della pagina selezionare **Lettura/scrittura**.
    2. Nella finestra del browser a sinistra passare a **subscriptions** > **_\<subscription\_name_** > **resourceGroups** > _**\<resource\_group\_name>**_ > **providers** > **Microsoft.Web** > **sites** > _**\<app\_name>**_ > **config** > **authsettings**. 
    3. Fare clic su **Modifica**.
    4. Modificare la proprietà seguente. Sostituire _\<app\_id>_ con l'ID dell'applicazione Azure Active Directory del servizio a cui si vuole accedere.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Fare clic su **Put**. 

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

## <a name="extend-session-token-expiration-grace-period"></a>Estendere il periodo di tolleranza della scadenza del token di sessione

La sessione autenticata scade dopo 8 ore. Dopo la scadenza di una sessione autenticata, è previsto per impostazione predefinita un periodo di tolleranza di 72 ore. Durante questo periodo di tolleranza, è possibile aggiornare il token di sessione con il servizio app senza dover ripetere l'autenticazione dell'utente. È sufficiente chiamare `/.auth/refresh` quando il token di sessione non è più valido e non è necessario tenere traccia della scadenza del token. Una volta trascorso il periodo di tolleranza di 72 ore, l'utente deve eseguire di nuovo l'accesso per ottenere un token di sessione valido.

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
> [Esercitazione: Autenticare e autorizzare gli utenti end-to-end(Windows)](app-service-web-tutorial-auth-aad.md)
> [Esercitazione: Autenticare e autorizzare gli utenti end-to-end (Linux)](containers/tutorial-auth-aad.md)
