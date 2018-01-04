---
title: Linee guida per gli sviluppatori per l'accesso condizionale di Azure Active Directory | Microsoft Docs
description: Scenari e linee guida per gli sviluppatori per l'accesso condizionale di Azure AD
services: active-directory
keywords: 
author: danieldobalian
manager: mtillman
editor: PatAltimore
ms.author: dadobali
ms.date: 07/19/2017
ms.assetid: 115bdab2-e1fd-4403-ac15-d4195e24ac95
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.openlocfilehash: c3b691022b02aa2f3836c4e3a96dd5db7affad76
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2017
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Linee guida per gli sviluppatori per l'accesso condizionale di Azure Active Directory

Azure Active Directory (AD) offre diversi modi per proteggere l'app e un servizio.  Una di queste funzionalità uniche è l'accesso condizionale.  L'accesso condizionale consente agli sviluppatori e ai clienti aziendali di proteggere i servizi in molti modi, tra cui:

* Autenticazione a più fattori
* Autorizzazione dell'accesso a servizi specifici solo per dispositivi registrati in Intune
* Limitazione delle posizioni dell'utente e degli intervalli IP

Per altre informazioni sulle complete funzionalità di accesso condizionale, vedere [Accesso condizionale nel portale di Azure classico](../active-directory-conditional-access-azure-portal.md). 

In questo articolo viene illustrato il significato dell'accesso condizionale per gli sviluppatori che creano app per Azure AD.  Si presuppone una conoscenza delle app a [tenant singolo](active-directory-integrating-applications.md) e [multi-tenant](active-directory-devhowto-multi-tenant-overview.md) e dei [modelli di autenticazione comuni](active-directory-authentication-scenarios.md).

Verrà esaminato l'impatto dell'accesso alle risorse su cui non si ha il controllo a cui potrebbero essere applicati criteri di accesso condizionale.  Verranno inoltre analizzate le implicazioni dell'accesso condizionale sul flusso on-behalf-of, sulle app Web, sull'accesso a Microsoft Graph e sulle chiamate di API.

## <a name="how-does-conditional-access-impact-an-app"></a>Qual è l'impatto dell'accesso condizionale su un'app?

### <a name="app-types-impacted"></a>Tipi di app interessati

Nella maggior parte dei casi, l'accesso condizionale non modifica il comportamento di un'app né richiede modifiche da parte dello sviluppatore.  Solo in alcuni casi, quando un'app richiede in modo indiretto o automatico un token per un servizio, sono necessarie modifiche al codice per gestire le richieste di accesso condizionale.  L'operazione può essere semplice quanto l'esecuzione di una richiesta di accesso interattiva. 

In particolare, gli scenari seguenti richiedono che il codice gestisca le richieste di accesso condizionale: 

* App che accedono a Microsoft Graph
* App che eseguono il flusso on-behalf-of
* App che accedono a più servizi o risorse
* App a pagina singola che usano ADAL.js
* App Web che chiamano una risorsa

I criteri di accesso condizionale possono essere applicati all'app, ma anche a un'API Web a cui l'app accede. Per altre informazioni su come configurare i criteri di accesso condizionale, vedere [Introduzione all'accesso condizionale di Azure Active Directory](../active-directory-conditional-access-azure-portal-get-started.md).

A seconda dello scenario, un cliente aziendale può applicare e rimuovere i criteri di accesso condizionale in qualsiasi momento.  Affinché l'app continui a funzionare quando vengono applicati nuovi criteri, è necessario implementare la gestione delle richieste. Gli esempi seguenti illustrano la gestione delle richieste. 

### <a name="conditional-access-examples"></a>Esempi di accesso condizionale

Alcuni scenari richiedono modifiche al codice per la gestione dell'accesso condizionale, mentre altri funzionano senza modifiche.  Ecco alcuni scenari in cui l'accesso condizionale viene usato per l'autenticazione a più fattori e che aiutano a comprendere la differenza.

* Si sta creando un'app iOS a tenant singolo e si applicano criteri di accesso condizionale.  L'app consente l'accesso di un utente e non richiede l'accesso a un'API.  Quando l'utente accede, i criteri vengono richiamati automaticamente e l'utente deve eseguire l'autenticazione a più fattori (MFA). 
* Si sta creando un'app Web multi-tenant che usa Microsoft Graph per accedere a Exchange, tra gli altri servizi.  Un cliente aziendale che usa questa app imposta i criteri in Exchange.  Quando l'app Web richiede un token per Microsoft Graph, non verrà visualizzata una richiesta di conformarsi ai criteri.  L'accesso dell'utente finale viene eseguito con token validi. Quando l'app tenta di usare questo token con Microsoft Graph per accedere ai dati di Exchange, viene restituita una richiesta di autenticazione claims all'app Web tramite l'intestazione ```WWW-Authenticate```.  L'app può quindi usare ```claims``` in una nuova richiesta e all'utente finale verrà richiesto di conformarsi alle condizioni. 
* Si sta creando un'app nativa che usa un servizio di livello intermedio per accedere a un'API downstream.  Un cliente aziendale in azienda che usa questa app applica i criteri all'API downstream.  Quando un utente finale esegue l'accesso, l'app nativa richiede l'accesso al livello intermedio e invia il token.  Il livello intermedio esegue il flusso on-behalf-of per richiedere l'accesso all'API downstream.  A questo punto, viene presentata una richiesta di attestazioni al livello intermedio. Il livello intermedio invia la richiesta di nuovo all'app nativa, che deve rispettare i criteri di accesso condizionale.

### <a name="complying-with-a-conditional-access-policy"></a>Conformità ai criteri di accesso condizionale

Per diverse topologie di app, i criteri di accesso condizionale vengono valutati quando viene stabilita la sessione.  Poiché i criteri di accesso condizionale operano a livello di app e servizi, il punto in corrispondenza del quale vengono richiamati dipende principalmente dallo scenario specifico.

Quando l'app tenta di accedere a un servizio con criteri di accesso condizionale, potrebbe riscontrare una richiesta di accesso condizionale.  La richiesta è codificata nel parametro `claims` fornito in una risposta da Azure AD o Microsoft Graph.  Ecco un esempio del parametro della richiesta: 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Gli sviluppatori possono aggiungere questa richiesta a una nuova richiesta per Azure AD.  Quando viene passato questo stato, l'utente finale deve eseguire qualsiasi azione necessaria per la conformità ai criteri di accesso condizionale. Negli scenari seguenti vengono illustrate informazioni specifiche sull'errore e su come estrarre il parametro. 

## <a name="scenarios"></a>Scenari

### <a name="prerequisites"></a>Prerequisiti

L'accesso condizionale di Azure AD è una funzionalità inclusa in [Azure AD Premium](../active-directory-whatis.md#choose-an-edition).  Per altre informazioni sui requisiti relativi alle licenza, vedere [Report Utilizzo senza licenza](../active-directory-conditional-access-unlicensed-usage-report.md).  Gli sviluppatori possono entrare a far parte di [Microsoft Developer Network](https://msdn.microsoft.com/dn308572.aspx) e ottenere così una sottoscrizione gratuita a Enterprise Mobility Suite, che include Azure AD Premium.

### <a name="considerations-for-specific-scenarios"></a>Considerazioni per scenari specifici

Le informazioni seguenti si applicano solo a questi scenari di accesso condizionale:

* App che accedono a Microsoft Graph
* App che eseguono il flusso on-behalf-of
* App che accedono a più servizi o risorse
* App a pagina singola che usano ADAL.js

Nelle sezioni seguenti vengono descritti scenari comuni più complessi.  Il concetto principale in relazione al funzionamento è che i criteri di accesso condizionale vengono valutati nel momento in cui viene richiesto il token per il servizio a cui i criteri sono applicati, a meno che l'accesso non avvenga tramite Microsoft Graph.

## <a name="scenario-app-accessing-microsoft-graph"></a>Scenario: app che accede a Microsoft Graph

In questo scenario viene illustrato il caso in cui un'app Web richiede l'accesso a Microsoft Graph. In questo caso i criteri di accesso condizionale potrebbero essere assegnati a SharePoint, Exchange o qualche altro servizio a cui viene eseguito l'accesso per il carico di lavoro tramite Microsoft Graph.  In questo esempio si presuppone che i criteri di accesso condizionale siano applicati a SharePoint Online.

![Diagramma di flusso per le app che accedono a Microsoft Graph](media/active-directory-conditional-access-developer/app-accessing-microsoft-graph-scenario.png)

L'app richiede prima di tutto l'autorizzazione per Microsoft Graph, che richiede l'accesso a un carico di lavoro downstream senza accesso condizionale.  La richiesta ha esito positivo senza richiamare alcun criterio e l'applicazione riceve i token per Microsoft Graph.  A questo punto, l'app può usare il token di accesso in una richiesta di bearer token per l'endpoint necessario. L'app deve quindi accedere all'endpoint SharePoint Online di Microsoft Graph, ad esempio: `https://graph.microsoft.com/v1.0/me/mySite`

L'app ha già un token valido per Microsoft Graph, quindi può eseguire la nuova richiesta senza che venga emesso un nuovo token. Questa richiesta non riesce e viene generata una richiesta di attestazioni da Microsoft Graph sotto forma di risposta HTTP 403 - accesso negato con una richiesta ```WWW-Authenticate```.
Ecco un esempio di risposta: 

```
HTTP 403; Forbidden 
error=insufficient_claims
www-authenticate="Bearer realm="", authorization_uri="https://login.windows.net/common/oauth2/authorize", client_id="<GUID>", error=insufficient_claims, claims={"access_token":{"polids":{"essential":true,"values":["<GUID>"]}}}"
```

La richiesta di attestazioni si trova all'interno dell'intestazione ```WWW-Authenticate```, che può essere analizzata per estrarre il parametro claims per la richiesta successiva.  Dopo l'aggiunta alla nuova richiesta, Azure AD è in grado di valutare i criteri di accesso condizionale al momento dell'accesso dell'utente e l'app è ora conforme ai criteri di accesso condizionale.  Una nuova richiesta all'endpoint SharePoint Online avrà esito positivo.

L'intestazione ```WWW-Authenticate``` presenta una struttura univoca e non è semplice da analizzare per estrarre i valori.  Di seguito è riportato un metodo rapido.

```C#
        /// <summary>
        /// This method extracts the claims value from the 403 error response from MS Graph. 
        /// </summary>
        /// <param name="wwwAuthHeader"></param>
        /// <returns>Value of the claims entry. This should be considered an opaque string. 
        /// Returns null if the wwwAuthheader does not contain the claims value. </returns>
        private String extractClaims(String wwwAuthHeader)
        {
            String ClaimsKey = "claims=";
            String ClaimsSubstring = "";
            if (wwwAuthHeader.Contains(ClaimsKey))
            {
                int Index = wwwAuthHeader.IndexOf(ClaimsKey);
                ClaimsSubstring = wwwAuthHeader.Substring(Index, wwwAuthHeader.Length - Index);
                string ClaimsChallenge;
                if (Regex.Match(ClaimsSubstring, @"}$").Success)
                {
                    ClaimsChallenge = ClaimsSubstring.Split('=')[1];
                }
                else
                {
                    ClaimsChallenge = ClaimsSubstring.Substring(0, ClaimsSubstring.IndexOf("},") + 1);
                }
                return ClaimsChallenge;
            }
            return null; 
        }
```

Per esempi di codice che illustrano come gestire la richiesta di autenticazione claims, vedere l'[esempio di codice on-behalf-of](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) per ADAL .NET.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Scenario: App che esegue il flusso on-behalf-of

In questo scenario viene illustrato il caso in cui un'app nativa chiama un'API o un servizio Web.  A sua volta, il servizio esegue il [flusso "on-behalf-of"](active-directory-authentication-scenarios.md#application-types-and-scenarios) per chiamare un servizio downstream.  In questo caso sono stati applicati criteri di accesso condizionale al servizio downstream (API Web 2) e viene usata un'app nativa invece di un'app demon/server. 

![Diagramma per le app che eseguono il flusso on-behalf-of](media/active-directory-conditional-access-developer/app-performing-on-behalf-of-scenario.png)

La richiesta di token iniziale per l'API Web 1 non comporta la richiesta all'utente finale dell'autenticazione a più fattori in quanto l'API Web 1 non sempre raggiunge l'API downstream.  Quando l'API Web 1 prova a richiedere un token per conto dell'utente per l'API Web 2, la richiesta non riesce perché l'utente non ha eseguito l'accesso con l'autenticazione a più fattori.

Azure AD restituisce una risposta HTTP con alcuni dati interessanti: 

> [!NOTE]
> In questa istanza c'è una descrizione dell'errore di autenticazione a più fattori, ma ci sono molti elementi `interaction_required` possibili correlati all'accesso condizionale.  

```
HTTP 400; Bad Request 
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Nell'API Web 1 viene intercettato l'errore `error=interaction_required` e viene inviata nuovamente la richiesta `claims` all'app desktop.  A questo punto, l'app desktop può chiamare di nuovo `acquireToken()` e aggiungere la richiesta `claims` come parametro di stringa di query aggiuntivo.  La nuova richiesta impone all'utente l'uso dell'autenticazione a più fattori e quindi invia il nuovo token all'API Web 1 e completa il flusso on-behalf-of.

Per provare questo scenario, vedere l'[esempio di codice .NET](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca).  Viene illustrato come passare la richiesta di attestazioni dall'API Web 1 all'app nativa e costruire una nuova richiesta all'interno dell'app client. 

## <a name="scenario-app-accessing-multiple-services"></a>Scenario: App che accede a più servizi

In questo scenario viene illustrato il caso in cui un'app Web accede a due servizi di cui uno ha criteri di accesso condizionale assegnati.  A seconda della logica dell'app, potrebbe esserci un percorso in cui l'app non richiede l'accesso a entrambi i servizi Web.  In questo scenario, l'ordine in cui si richiede un token ha un ruolo importante nell'esperienza dell'utente finale.

Si supponga di avere i servizi Web A e B e che al servizio Web B siano applicati i criteri di accesso condizionale.  Mentre la richiesta di autenticazione interattiva iniziale richiede il consenso per entrambi i servizi, i criteri di accesso condizionale non sono necessari in tutti i casi.  Se l'app richiede un token per il servizio Web B, i criteri vengono richiamati e anche le richieste successive per il servizio Web A hanno esito positivo, come indicato di seguito.

![Diagramma di flusso per le app che accedono a più servizi](media/active-directory-conditional-access-developer/app-accessing-multiple-services-scenario.png)

In alternativa, se l'app richiede inizialmente un token per il servizio Web A, l'utente finale non richiama i criteri di accesso condizionale.  In questo modo, lo sviluppatore dell'app può controllare l'esperienza dell'utente finale senza imporre che i criteri di accesso condizionale vengano richiamati in tutti i casi. La situazione diventa complessa se l'app successivamente richiede un token per il servizio Web B. A questo punto, l'utente finale deve rispettare i criteri di accesso condizionale.  Quando l'app prova a eseguire `acquireToken`, può venire generato l'errore seguente (illustrato nel diagramma seguente): 

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
``` 

![App che accede a più servizi e richiede un nuovo token](media/active-directory-conditional-access-developer/app-accessing-multiple-services-new-token.png)

Se l'app usa la libreria ADAL, in caso di errore di acquisizione del token viene sempre eseguito un nuovo tentativo in modo interattivo.  Quando si verifica questa richiesta interattiva, l'utente finale ha la possibilità di rispettare l'accesso condizionale.  Questo vale solo se la richiesta non è `AcquireTokenSilentAsync` o `PromptBehavior.Never`, nel qual caso l'app deve eseguire una richiesta ```AcquireToken``` interattiva per consentire all'utente finale di rispettare i criteri. 

## <a name="scenario-single-page-app-spa-using-adaljs"></a>Scenario: App a pagina singola che usa ADAL.js

In questo scenario viene illustrato il caso di un'app a pagina singola (SPA) che usa ADAL.js per chiamare un'API Web protetta con accesso condizionale.  Si tratta di un'architettura semplice, ma ci sono alcuni aspetti da prendere in considerazione in caso di sviluppo incentrato sull'accesso condizionale.

In ADAL.js ci sono poche funzioni che ottengono token: `login()`, `acquireToken(...)`, `acquireTokenPopup(…)` e `acquireTokenRedirect(…)`. 

* `login()` ottiene un token ID tramite una richiesta di accesso interattiva, ma non ottiene token di accesso per alcun servizio (inclusa un'API Web protetta con accesso condizionale).  
* È quindi possibile usare `acquireToken(…)` per ottenere automaticamente un token di accesso, senza visualizzazione dell'interfaccia utente in nessun caso.  
* `acquireTokenPopup(…)` e `acquireTokenRedirect(…)` vengono entrambi usati per richiedere in modo interattivo un token per una risorsa, quindi l'interfaccia utente di accesso viene sempre visualizzata.

Quando un'app necessita di un token di accesso per chiamare un'API Web, viene eseguito un tentativo di chiamata a `acquireToken(…)`.  Se la sessione del token è scaduta o è necessaria la conformità ai criteri di accesso condizionale, la funzione *acquireToken* ha esito negativo e l'app usa `acquireTokenPopup()` o `acquireTokenRedirect()`.

![Diagramma di flusso per le app a pagina singola che usano ADAL](media/active-directory-conditional-access-developer/spa-using-adal-scenario.png)

Verrà ora esaminato un esempio con lo scenario di accesso condizionale.  L'utente finale è appena arrivato nel sito e non ha una sessione.  Si esegue una chiamata a `login()` e si ottiene un token ID senza autenticazione a più fattori.  L'utente preme quindi un pulsante che comporta una richiesta di dati dall'app a un'API Web.  L'app prova a eseguire una chiamata a `acquireToken()`, che ha però esito negativo perché l'utente non ha eseguito ancora l'autenticazione a più fattori e deve rispettare i criteri di accesso condizionale.

Azure AD invia la risposta HTTP seguente: 

```
HTTP 400; Bad Request 
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

L'app deve intercettare l'errore `error=interaction_required`.  L'applicazione può quindi usare `acquireTokenPopup()` o `acquireTokenRedirect()` sulla stessa risorsa.  L'utente deve eseguire l'autenticazione a più fattori. Dopo che l'utente ha completato l'autenticazione a più fattori, viene emesso un nuovo token di accesso per l'app per la risorsa richiesta.

Per provare questo scenario, vedere l'[esempio di codice on-behalf-of JS SPA](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca).  Questo esempio di codice usa i criteri di accesso condizionale e l'API Web registrata in precedenza con JS SPA per illustrare questo scenario. Mostra come gestire correttamente la richiesta di attestazioni e ottenere un token di accesso che può essere usato per l'API Web. In alternativa, vedere l'[esempio di codice Angular.js](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) generale, per informazioni su Angular SPA


## <a name="see-also"></a>Vedere anche 

* Per altre informazioni sulle funzionalità, vedere [Accesso condizionale in Azure AD](../active-directory-conditional-access-azure-portal.md).
* Per altri esempi di codice di Azure AD, vedere il [repository GitHub di esempi di codice](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory). 
* Per altre informazioni su ADAL SDK e sull'accesso alla documentazione di riferimento, vedere la [guida alle librerie](active-directory-authentication-libraries.md).
* Per altre informazioni sugli scenari multi-tenant, vedere [Come consentire l'accesso a qualsiasi utente di Azure Active Directory (AD) usando il modello di applicazione multi-tenant](active-directory-devhowto-multi-tenant-overview.md).
