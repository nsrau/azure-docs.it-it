---
title: 'Introduzione al server Web ASP.NET per Azure AD v2: test | Microsoft Docs'
description: Implementazione di accessi Microsoft in una soluzione ASP.NET con un&quot;applicazione tradizionale basata su Web browser tramite lo standard OpenID Connect
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: 00cb963e85111274c36c3a84489894811ad2dabd
ms.contentlocale: it-it
ms.lasthandoff: 05/25/2017


---
## <a name="test-your-code"></a>Testare il codice

Premere `F5` per eseguire il progetto in Visual Studio. Verrà aperto il browser e l'utente verrà indirizzato a *http://localhost:{porta}*, in cui verrà visualizzato il pulsante *Accedi con Microsoft*. Fare clic per eseguire l'accesso.

Quando si è pronti per il test, usare un account aziendale o dell'istituto di istruzione (Azure Active Directory) oppure un account personale (live.com, outlook.com) per accedere. 

![Finestra del browser con accesso con Microsoft](media/active-directory-serversidewebapp-aspnetwebappowin-test/aspnetbrowsersignin.png)

![Finestra del browser con accesso con Microsoft](media/active-directory-serversidewebapp-aspnetwebappowin-test/aspnetbrowsersignin2.png)

#### <a name="expected-results"></a>Risultati previsti
Dopo l'accesso, l'utente viene reindirizzato alla home page del sito Web, ossia all'URL HTTPS specificato nelle informazioni di registrazione dell'applicazione nel portale di registrazione delle applicazioni Microsoft. Nella pagina vengono ora visualizzati *Hello {utente}*, un collegamento per la disconnessione e uno per visualizzare le attestazioni dell'utente, ossia un collegamento al controller Authorize creato in precedenza.

### <a name="see-users-claims"></a>Visualizzare le attestazioni dell'utente
Selezionare il collegamento ipertestuale per visualizzare le attestazioni dell'utente. Si accederà così al controller e alla visualizzazione disponibile solo per gli utenti autenticati.

#### <a name="expected-results"></a>Risultati previsti
 Verrà visualizzata una tabella contenente le proprietà di base dell'utente connesso:

| Proprietà | Valore | Descrizione|
|---|---|---|
| Nome | {Nome e cognome utente} | Nome e cognome dell'utente
|Nome utente | <span>user@domain.com</span>| Nome utente usato per identificare l'utente connesso
| Subject| {Soggetto}|Stringa che identifica in modo univoco l'account di accesso dell'utente sul Web|
| ID tenant| {GUID}| *GUID* che rappresenta in modo univoco l'organizzazione di Azure Active Directory dell'utente.|

Verrà visualizzata anche una tabella contenente tutte le attestazioni utente incluse nella richiesta di autenticazione. Per un elenco e una spiegazione di tutte le attestazioni in un token ID, vedere questo [articolo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims "Elenco delle attestazioni nei token ID").


### <a name="test-accessing-a-method-that-has-an-authorize-attribute-optional"></a>Testare l'accesso a un metodo con attributo *[Authorize]* (facoltativo)
In questo passaggio si testerà l'accesso al controller Authenticated come utente anonimo:<br/>
Selezionare il collegamento per la disconnessione dell'utente e completare il processo di disconnessione.<br/>
Nel browser digitare http://localhost:{porta}/authenticated per accedere al controller protetto con l'attributo `[Authorize]`.

#### <a name="expected-results"></a>Risultati previsti
Verrà visualizzata la richiesta di eseguire l'autenticazione per accedere alla visualizzazione.

## <a name="additional-information"></a>Informazioni aggiuntive

<!--start-collapse-->
### <a name="protect-your-entire-web-site"></a>Proteggere l'intero sito Web
Per proteggere l'intero sito Web, aggiungere `AuthorizeAttribute` a `GlobalFilters` nel metodo `Application_Start` di `Global.asax`:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

<div></div>
<br/>

> [!NOTE]
> **Come limitare l'accesso all'applicazione agli utenti di una sola organizzazione**

> Per impostazione predefinita, all'applicazione possono accedere account personali (ad esempio outlook.com, live.com e altri) e account aziendali o di istituti di istruzione di qualsiasi organizzazione o azienda che abbia eseguito l'integrazione con Azure Active Directory. 

> Se si vuole che l'applicazione accetti accessi da una sola organizzazione di Azure Active Directory, sostituire il parametro `Tenant` in *web.config* passando da `Common` al nome del tenant dell'organizzazione, ad esempio *contoso.onmicrosoft.com*. Al termine, modificare l'argomento `ValidateIssuer` nella *classe di avvio OWIN* impostandolo su `true`.

> Per consentire l'accesso solo agli utenti di un elenco di organizzazioni specifiche, impostare `ValidateIssuer` su true e usare il parametro `ValidIssuers` per specificare un elenco di organizzazioni.

> Un'altra opzione consiste nell'implementare un metodo personalizzato per convalidare le autorità di certificazione usando il parametro IssuerValidator. Per altre informazioni su `TokenValidationParameters`, vedere [questo](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx "Articolo di MSDN su TokenValidationParameters") articolo di MSDN.


