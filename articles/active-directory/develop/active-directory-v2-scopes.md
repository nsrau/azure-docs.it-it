---
title: Ambiti, autorizzazioni e consenso in Azure Active Directory v2.0 | Documentazione Microsoft
description: Descrizione dell&quot;autorizzazione nell&quot;endpoint v2.0 di Azure AD, inclusi gli ambiti, le autorizzazioni e il consenso.
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 8f98cbf0-a71d-4e34-babf-e644ad9ff423
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: c579135f798ea0c2a5461fdd7c88244d2d6d78c6
ms.openlocfilehash: e00ceb8dd87e00bbdd05146e107c72e6182eb474
ms.contentlocale: it-it
ms.lasthandoff: 01/18/2017


---
# <a name="scopes-permissions-and-consent-in-the-azure-active-directory-v20-endpoint"></a>Ambiti, autorizzazioni e consenso nell'endpoint di Azure Active Directory v2.0
Le app che si integrano con Azure Active Directory (Azure AD) seguono un modello di autorizzazione che consente agli utenti di controllare la modalità di accesso ai dati da parte di un'app. L'implementazione v2.0 di questo modello di autorizzazione è stata aggiornata, modificando la modalità di interazione di un'app con Azure AD. Questo articolo illustra i concetti di base di questo modello di autorizzazione, inclusi gli ambiti, le autorizzazioni e il consenso.

> [!NOTE]
> Non tutti gli scenari e le funzionalità di Azure Active Directory sono supportati dall'endpoint v2.0. Per determinare se è necessario usare l'endpoint 2.0, vedere l'articolo relativo alle [limitazioni della versione 2.0](active-directory-v2-limitations.md).
>
>

## <a name="scopes-and-permissions"></a>Ambiti e autorizzazioni
Azure AD implementa il protocollo di autorizzazione [OAuth 2.0](active-directory-v2-protocols.md). OAuth 2.0 è un metodo tramite cui un'applicazione di terze parti può accedere alle risorse ospitate sul Web per conto dell'utente. Qualsiasi risorsa ospitata sul Web che si integra con Azure AD dispoen di un identificatore di risorsa o di un *URI ID dell'applicazione*. Ad esempio, alcune delle risorse ospitate sul Web di Microsoft includono:

* L'API di posta unificata di Office 365: `https://outlook.office.com`
* L'API Graph di Azure AD: `https://graph.windows.net`
* Microsoft Graph: `https://graph.microsoft.com`

Anche le risorse di terze parti integrate con Azure AD devono disporre di tale identificatore. Tali risorse possono anche definire un set di autorizzazioni che può essere usato per suddividere le funzionalità di tale risorsa in blocchi più piccoli. Ad esempio, [Microsoft Graph](https://graph.microsoft.io) ha definito le autorizzazioni per eseguire le attività seguenti, tra le altre:

* Lettura del calendario dell'utente
* Scrittura nel calendario dell'utente
* Invio di messaggi di posta elettronica come utente

Con la definizione di questi tipi di autorizzazioni, la risorsa può avere un controllo accurato dei dati e dell'esposizione degli stessi. Un'app di terze parti può richiedere tali autorizzazioni da un utente del'app. L'utente dell'app deve approvare le autorizzazioni prima che l'applicazione possa agire a suo nome. Suddividendo le funzionalità della risorsa in set di autorizzazioni più piccoli, è possibile creare le app di terze parti affinché richiedano solo le autorizzazioni specifiche necessarie per il relativo funzionamento. Gli utenti dell'app possono sapere esattamente come un'app userà i propri dati e possono essere più sicuri che l'app non agisca per fini dannosi.

In Azure AD e OAuth queste autorizzazioni vengono definite *ambiti* o *oAuth2Permissions*. Un ambito è rappresentato in Azure AD come valore stringa. Nell'esempio relativo a Microsoft Graph il valore di ambito per ogni autorizzazione è:

* Lettura del calendario dell'utente tramite `Calendar.Read`
* Scrittura del calendario dell'utente tramite `Mail.ReadWrite`
* Invio di messaggi di posta elettronica come utente tramite `Mail.Send`

Un'app può richiedere tali autorizzazioni specificando gli ambiti nelle richieste all'endpoint 2.0.

## <a name="openid-connect-scopes"></a>Ambiti di OpenID Connect
L'implementazione della versione 2.0 di OpenID Connect presenta alcuni ambiti ben definiti che non si applicano a una risorsa specifica: `openid`, `email`, `profile` e `offline_access`.

### <a name="openid"></a>openid
Se un'app esegue l'accesso usando [OpenID Connect](active-directory-v2-protocols.md), deve richiedere l'ambito `openid`. L'ambito `openid` viene visualizzato nella pagina di consenso dell'account aziendale come autorizzazione di accesso e nella pagina di consenso dell'account personale Microsoft come autorizzazione per la visualizzazione del profilo e la connessione ad app e servizi tramite l'account Microsoft. Questa autorizzazione consente a un'app di ricevere un identificatore univoco per l'utente sotto forma di attestazione `sub` e concede all'app l'accesso all'endpoint delle informazioni utente. L'ambito `openid` può essere usato nell'endpoint del token 2.0 per acquisire token ID, che possono essere usati per proteggere le chiamate HTTP tra diversi componenti di un'app.

### <a name="email"></a>email
L'ambito `email` può essere usato con l'ambito `openid` e con tutti gli altri. Consente all'applicazione di accedere all'indirizzo di posta elettronica primario dell'utente sotto forma di attestazione `email`. L'attestazione `email` è inclusa nei token solo quando un indirizzo di posta elettronica è associato all'account utente, condizione che non è sempre vera. Se si usa l'ambito `email`, l'applicazione deve essere pronta per gestire il caso in cui l'attestazione `email` non esiste nel token.

### <a name="profile"></a>Profilo
L'ambito `profile` può essere usato con l'ambito `openid` e con tutti gli altri. Consente all'applicazione di accedere a numerose informazioni sull'utente, tra cui il nome e il cognome dell'utente, il nome utente preferito, l'ID dell'oggetto e così via. Per un elenco completo delle attestazioni profilo disponibili nel parametro token ID per un determinato utente, vedere il [riferimento al token della versione 2.0](active-directory-v2-tokens.md).

### <a name="offlineaccess"></a>offline_access
L'ambito [`offline_access`](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) consente all'app di accedere alle risorse per conto dell'utente per un periodo di tempo prolungato. Nella pagina di consenso dell'account aziendale l'ambito viene visualizzato come autorizzazione per l'accesso ai dati in qualsiasi momento. Nella pagina di consenso dell'account personale Microsoft viene visualizzato come autorizzazione per l'accesso alle informazioni in qualsiasi momento. Quando un utente approva l'ambito `offline_access`, l'app è in grado di ricevere token di aggiornamento dall'endpoint del token 2.0. I token di aggiornamento sono di lunga durata. L'applicazione può ottenere nuovi token di accesso quando i vecchi scadono.

Se l'app non richiede l'ambito `offline_access`, non riceverà i token di aggiornamento. Pertanto, se si riscatta un codice di autorizzazione nel [flusso del codice di autorizzazione di OAuth 2.0](active-directory-v2-protocols.md), si riceve solo un token di accesso dall'endpoint `/token`. Il token di accesso è valido per un breve periodo. Il token di accesso ha in genere una durata di un'ora. A questo punto, l'app reindirizza l'utente all'endpoint `/authorize` per recuperare un nuovo codice di autorizzazione. Durante il reindirizzamento, a seconda del tipo di app, l'utente potrebbe dover immettere nuovamente le proprie credenziali o fornire il consenso per le autorizzazioni.

Per altre informazioni su come ottenere e usare i token di aggiornamento, vedere il [riferimento al protocollo della versione 2.0](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Richiesta di consenso per un singolo utente
In una richiesta di autorizzazione [OpenID Connect o OAuth 2.0](active-directory-v2-protocols.md) un'app può richiedere le autorizzazioni necessarie usando il parametro di query `scope`. Ad esempio, quando un utente accede a un'app, questa può inviare una richiesta simile alla seguente (con interruzioni di riga aggiunte per leggibilità):

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendar.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

Il parametro `scope` è un elenco di ambiti separati da spazi richiesti dall'app. Ogni ambito è indicato dall'aggiunta del valore dell'ambito all'identificatore della risorsa (URI ID dell'app). Nella richiesta di esempio precedente, l'app richiede l'autorizzazione per la lettura del calendario dell'utente e l'invio di messaggi di posta elettronica come utente.

Dopo che l'utente immette le proprie credenziali, l'endpoint 2.0 verifica la disponibilità di un record corrispondente di *consenso dell'utente*. Se l'utente non ha fornito in precedenza il consenso per nessuna delle autorizzazioni necessarie, l'endpoint 2.0 chiede all'utente di fornire il consenso per le autorizzazioni obbligatorie.

![Consenso dell'account aziendale](../../media/active-directory-v2-flows/work_account_consent.png)

Quando l'utente approva l'autorizzazione, il consenso viene registrato in modo che non debba essere fornito nuovamente agli accessi successivi da parte degli account.

## <a name="requesting-consent-for-an-entire-tenant"></a>Richiesta di consenso per un intero tenant
Quando un'organizzazione acquista una licenza o una sottoscrizione a un'applicazione, desidera fornire accesso completo ai dipendenti. Come parte di questo processo, un amministratore può concedere il consenso all'applicazione ad agire per conto di qualsiasi dipendente. Se l'amministratore concede il consenso per l'intero tenant, i dipendenti dell'organizzazione non visualizzeranno la pagina di consenso per l'applicazione.

Per richiedere il consenso per tutti gli utenti in un tenant, è possibile usare l'applicazione dell'endpoint di consenso dell'amministratore.

## <a name="admin-restricted-scopes"></a>Ambiti riservati all'amministratore
Alcune autorizzazioni con privilegi elevati nell'ecosistema Microsoft possono essere impostati come *riservati all'amministratore*. Gli esempi di questi tipi di ambiti includono le autorizzazioni seguenti:

* Lettura di dati in una directory aziendale tramite `Directory.Read`
* Scrittura di dati in una directory aziendale tramite `Directory.ReadWrite`
* Lettura dei gruppi di sicurezza nella directory aziendale tramite `Groups.Read.All`

Sebbene un utente consumer possa concedere a un'applicazione l'accesso a questi tipi di dati, gli utenti aziendali non possono concedere accesso allo stesso set di dati riservati dell'azienda. Se l'applicazione richiede l'accesso a una di queste autorizzazioni da un utente aziendale, l'utente riceve un messaggio di errore che indica che non è autorizzato a fornire il consenso alle autorizzazioni dell'applicazione.

Se l'applicazione richiede accesso ad ambiti riservati all'amministratore delle organizzazioni, è necessario richiederle direttamente all'amministratore dell'azienda anche tramite l'endpoint di consenso dell'amministratore, come descritto di seguito.

Quando un amministratore concede le autorizzazioni tramite l'endpoint di consenso dell'amministratore, il consenso viene concesso a tutti gli utenti del tenant.

## <a name="using-the-admin-consent-endpoint"></a>Uso dell'endpoint di consenso dell'amministratore
Seguendo questa procedura, l'applicazione può ottenere le autorizzazioni per tutti gli utenti di un tenant, inclusi gli ambiti riservati all'amministratore. Per un esempio di codice che implementa la procedura, vedere l'[esempio sugli ambiti riservati all'amministratore](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Richiedere le autorizzazioni nel portale di registrazione dell'app
1. Passare all'applicazione nel [portale di registrazione delle applicazioni](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) o [creare un'app](active-directory-v2-app-registration.md), se non se ne possiede già una.
2. Individuare la sezione **Autorizzazioni di Microsoft Graph**  e aggiungere le autorizzazioni richieste dall'applicazione.
3. **Salvare** la registrazione dell'app.

### <a name="recommended-sign-the-user-in-to-your-app"></a>Consigliato: connettere l'utente dall'applicazione
In genere, durante la compilazione di un'applicazione che usa l'endpoint di consenso dell'amministratore, l'app necessita di una pagina o vista che consenta all'amministratore di approvare le autorizzazioni dell'applicazione. Questa pagina può essere parte del flusso di iscrizione all'app, delle impostazioni dell'applicazione o di un flusso di "connessione" dedicato. In molti casi, è utile per l'applicazione visualizzare la pagina di "connessione" solo dopo che un utente ha eseguito l'accesso con un account di lavoro o dell'istituto di istruzione Microsoft.

L'accesso dell'utente nell'app consente di identificarne l'organizzazione di appartenenza dell'amministratore prima di richiedere l'approvazione delle autorizzazioni necessarie. Sebbene non sia strettamente necessario, questo consente di creare un'esperienza più intuitiva per gli utenti dell'organizzazione. Per l'accesso utente, seguire le [esercitazioni sui protocolli 2.0](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Richiedere le autorizzazioni da un amministratore di directory
Quando si è pronti per richiedere le autorizzazioni dall'amministratore dell'azienda, è possibile reindirizzare l'utente sull'*endpoint di consenso dell'amministratore* v2.0.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parametro | Condizione | Descrizione |
| --- | --- | --- |
| tenant |Obbligatorio |Il tenant della directory da cui si desidera richiedere autorizzazioni. Può essere fornito nel formato di nome descrittivo o GUID. |
| client_id |Obbligatorio |ID applicazione che il [portale di registrazione delle applicazioni](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) ha assegnato all'app. |
| redirect_uri |Obbligatorio |URI di reindirizzamento in cui si desidera che venga inviata la risposta per la gestione da parte dell'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale di registrazione delle applicazioni. |
| state |Consigliato |Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Usare questo stato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |

A questo punto, Azure AD richiede che solo un amministratore tenant possa accedere per completare la richiesta. L'amministratore deve approvare tutte le autorizzazioni richieste per l'app nel portale di registrazione delle applicazioni.

#### <a name="successful-response"></a>Risposta con esito positivo
Se l'amministratore approva le autorizzazioni per l'app, la risposta con esito positivo si presenta come segue:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametro | Descrizione |
| --- | --- | --- |
| tenant |Tenant della directory che ha concesso all'applicazione le autorizzazioni richieste, in formato GUID. |
| state |Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Lo stato viene usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| admin_consent |Verrà impostato su **true**. |

#### <a name="error-response"></a>Risposta di errore
Se l'amministratore non approva le autorizzazioni per l'app, la risposta di errore si presenta come segue:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametro | Descrizione |
| --- | --- | --- |
| error |Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| error_description |Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore. |

Dopo aver ricevuto una risposta con esito positivo dall'endpoint di consenso dell'amministratore, l'applicazione ha ottenuto le autorizzazioni richieste. Successivamente, è possibile richiedere un token per la risorsa desiderata.

## <a name="using-permissions"></a>Uso delle autorizzazioni
Dopo che l'utente fornisce il consenso alle autorizzazioni relative all'app, quest'ultima può acquisire token di accesso che rappresentano l'autorizzazione dell'app ad accedere a una risorsa. Un token di accesso può essere usato per una sola risorsa, ma al suo interno saranno codificate tutte le autorizzazioni della risorsa specifica per cui l'app ha ottenuto il consenso. Per ottenere un token di accesso, l'app può eseguire una richiesta all'endpoint del token 2.0 come quella seguente:

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

È possibile usare il token di accesso risultante nelle richieste HTTP per la risorsa. Esso indica alla risorsa, in modo affidabile, che l'applicazione dispone delle autorizzazioni appropriate per eseguire un'attività specifica.  

Per altre informazioni sul protocollo OAuth 2.0 e su come ottenere i token di accesso, vedere il [riferimento al protocollo dell'endpoint v2.0](active-directory-v2-protocols.md).

