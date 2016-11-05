---
title: Ambiti, autorizzazioni e consenso dell'endpoint v2.0 di Azure AD | Microsoft Docs
description: Descrizione dell'autorizzazione nell'endpoint v2.0 di Azure AD, inclusi gli ambiti, le autorizzazioni e il consenso.
services: active-directory
documentationcenter: ''
author: dstrockis
manager: mbaldwin
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: dastrock

---
# <a name="scopes,-permissions,-&-consent-in-the-v2.0-endpoint"></a>Ambiti, autorizzazioni e consenso nell'endpoint v2.0
Le app che si integrano con Azure AD seguono un modello di autorizzazione che consente agli utenti di controllare la modalità di accesso ai dati da parte di un'app.  L'implementazione v2.0 di questo modello di autorizzazione è stata aggiornata, modificando la modalità di interazione di un'app con Azure AD.  Questo argomento illustra i concetti di base di questo modello di autorizzazione, inclusi gli ambiti, le autorizzazioni e il consenso.

> [!NOTE]
> Non tutti gli scenari e le funzionalità di Azure Active Directory sono supportati dall'endpoint 2.0.  Per determinare se è necessario usare l'endpoint v2.0, leggere le informazioni sulle [limitazioni v2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="scopes-&-permissions"></a>Ambiti e autorizzazioni
Azure AD implementa il protocollo di autorizzazione [OAuth 2.0](active-directory-v2-protocols.md) , ossia un metodo che consente a un'app di terze parti di accedere a risorse ospitate sul Web per conto dell'utente.  Qualsiasi risorsa ospitata sul Web che si integra con Azure AD dovrà disporre di un identificatore di risorsa o di un **URI ID dell'app**.  Ad esempio, alcune delle risorse ospitate sul Web di Microsoft includono:

* L'API di posta unificata di Office 365: `https://outlook.office.com`
* L'API Graph di Azure AD: `https://graph.windows.net`
* Microsoft Graph: `https://graph.microsoft.com`

Anche le risorse di terze parti integrate con Azure AD devono disporre di tale identificatore.  Tali risorse possono inoltre definire un set di autorizzazioni che può essere usato per suddividere le funzionalità di tale risorsa in blocchi più piccoli.  Ad esempio, Microsoft Graph ha definito alcune autorizzazioni:

* Lettura del calendario dell'utente
* Scrittura nel calendario dell'utente
* Invio di messaggi di posta elettronica come utente
* [Più informazioni](https://graph.microsoft.io)

Con la definizione di queste autorizzazioni, la risorsa può avere un controllo accurato dei dati e di come si espone all'esterno.  Un'app di terze parti può infine richiedere tali autorizzazioni da un utente finale e quest'ultimo deve approvare le autorizzazioni prima che l'app possa agire per conto proprio.  Suddividendo le funzionalità della risorsa in set di autorizzazioni più piccoli, è possibile creare le app di terze parti affinché richiedano solo le autorizzazioni specifiche necessarie per il relativo funzionamento.  In questo modo, gli utenti finali possono sapere esattamente come un'app userà i propri dati e possono essere più sicuri che l'app non agisca per fini dannosi.

In Azure AD e OAuth queste autorizzazioni vengono definite **ambiti** o  **oAuth2Permissions**.  Un ambito è rappresentato in Azure AD come valore stringa.  Nell'esempio relativo a Microsoft Graph il valore di ambito per ogni autorizzazione è:

* Lettura del calendario dell'utente: `Calendar.Read`
* Scrittura nel calendario dell'utente: `Mail.ReadWrite`
* Invio di messaggi di posta elettronica come utente: `Mail.Send`

Un'app può richiedere tali autorizzazioni specificando gli ambiti nelle richieste all'endpoint 2.0, come descritto di seguito.

## <a name="openid-connect-scopes"></a>Ambiti di OpenId Connect
L'implementazione della versione 2.0 di OpenID Connect presenta alcuni ambiti ben definiti che non si applicano a una risorsa in particolare: `openid`, `email`, `profile` e `offline_access`.

#### <a name="openid"></a>OpenID
Se un'app esegue l'accesso usando [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow), deve richiedere l'ambito `openid`.  L'ambito `openid` viene visualizzato nella schermata di consenso dell'account aziendale come autorizzazione di accesso e nella schermata di consenso dell'account personale Microsoft come autorizzazione per la visualizzazione del profilo e la connessione ad app e servizi mediante l'account Microsoft.  Questa autorizzazione consente a un'app di ricevere un identificatore univoco per l'utente sotto forma di attestazione `sub` .  e concede all'app l'accesso all'endpoint delle informazioni utente.  L'ambito `openid` può essere usato anche nell'endpoint del token 2.0 per acquisire token ID, che possono essere usati per proteggere le chiamate HTTP tra diversi componenti di un'app.

#### <a name="email"></a>Email
L'ambito `email` può essere incluso con l'ambito `openid` e con tutti gli altri.  Consente all'applicazione di accedere all'indirizzo di posta elettronica primario dell'utente sotto forma di attestazione `email` .  L'attestazione `email` verrà inclusa nei token solo quando un indirizzo di posta elettronica è associato all'account utente, condizione che non è sempre vera.  Se si usa l'ambito `email`, l'applicazione deve essere pronta per gestire il caso in cui l'attestazione `email` non esiste nel token.

#### <a name="profile"></a>Profilo
L'ambito `profile` può essere incluso con l'ambito `openid` e con tutti gli altri.  Consente all'applicazione di accedere a numerose informazioni sull'utente,  ad esempio il nome e il cognome dell'utente, il nome utente preferito, l'ID dell'oggetto e così via.  Per un elenco completo delle attestazioni profilo disponibili nei token ID per un determinato utente, vedere il [riferimento al token della versione 2.0](active-directory-v2-tokens.md).

#### <a name="offline_access"></a>offline_access
L' [`offline_access`](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) consente all'app di accedere alle risorse per conto dell'utente per un periodo di tempo prolungato.  Nella schermata di consenso dell'account aziendale, l'ambito verrà visualizzato come autorizzazione per l'accesso ai dati in qualsiasi momento.  Nella schermata di consenso dell'account personale Microsoft, verrà visualizzato come autorizzazione per l'accesso alle informazioni in qualsiasi momento.  Quando un utente approva l'ambito `offline_access` , l'app viene abilitata per la ricezione di token di aggiornamento dall'endpoint del token 2.0.  I token di aggiornamento hanno una durata elevata e consentono all'app di acquisire nuovi token di accesso alla scadenza dei precedenti.

Se l'app non richiede l'ambito `offline_access`, non riceverà i token di aggiornamento.  Pertanto, se si riscatta un codice di autorizzazione nel [flusso del codice di autorizzazione di OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow), si riceverà solo un token di accesso dall'endpoint `/token`.  Tale token di accesso rimarrà valido per un breve periodo di tempo (in genere un'ora), per poi scadere.  A questo punto, l'app reindirizza l'utente all'endpoint `/authorize` per recuperare un nuovo codice di autorizzazione.  Durante il reindirizzamento, l'utente può o meno avere esigenza di immettere nuovamente le proprie credenziali o fornire il consenso per le autorizzazioni, a seconda del tipo di app.

Per altre informazioni su come ottenere e usare i token di aggiornamento, vedere il [riferimento al protocollo della versione 2.0](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Richiesta di consenso per un singolo utente
In una richiesta di autorizzazione [OpenID Connect o OAuth 2.0](active-directory-v2-protocols.md) un'app può richiedere le autorizzazioni necessarie usando il parametro di query `scope`.  Ad esempio, quando un utente accede a un'app, questa può inviare una richiesta simile alla seguente (con interruzioni di riga per migliorare la leggibilità):

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

Il parametro `scope` è un elenco di ambiti separati da spazi richiesti dall'app.  Ogni ambito è indicato dall'aggiunta del valore dell'ambito all'identificatore della risorsa (URI ID dell'app).  La richiesta precedente indica che l'app richiede l'autorizzazione per la lettura del calendario dell'utente e l'invio di messaggi di posta elettronica come utente.

Dopo che l'utente immette le proprie credenziali, l'endpoint 2.0 verifica la disponibilità di un record corrispondente di **consenso dell'utente**.  Se l'utente non ha fornito in precedenza il consenso per nessuna delle autorizzazioni necessarie, l'endpoint 2.0 chiede all'utente di fornire il consenso per le autorizzazioni obbligatorie.  

![Schermata di consenso dell'account aziendale](../media/active-directory-v2-flows/work_account_consent.png)

Quando l'utente approva l'autorizzazione, il consenso viene registrato in modo che non debba essere fornito nuovamente agli accessi successivi.

## <a name="requesting-consent-for-an-entire-tenant"></a>Richiesta di consenso per un intero tenant
Quando un'organizzazione acquista una licenza o una sottoscrizione a un'applicazione, desidera fornire accesso completo ai dipendenti.  Come parte di questo processo, un amministratore dell'azienda può concedere il consenso all'applicazione ad agire per conto di qualsiasi dipendente.  Concedendo l'autorizzazione per un intero tenant, i dipendenti dell'organizzazione non visualizzeranno la schermata di consenso per l'applicazione.

Per richiedere il consenso per tutti gli utenti in un tenant, è possibile usare l'applicazione dell' **endpoint di consenso dell'amministratore**come descritto di seguito.

## <a name="admin-restricted-scopes"></a>Ambiti riservati all'amministratore
Alcune autorizzazioni con privilegi elevati nell'ecosistema Microsoft possono essere contrassegnati come **riservati all'amministratore**.  Esempi di questi ambiti:

* Lettura dei dati della directory aziendale: `Directory.Read`
* Scrittura di dati in una directory aziendale: `Directory.ReadWrite`
* Lettura dei gruppi di sicurezza nella directory aziendale: `Groups.Read.All`

Mentre un utente consumer può concedere a un'applicazione l'accesso a questi dati, gli utenti aziendali non possono concedere accesso allo stesso set di dati riservati dell'azienda.  Se l'applicazione richiede l'accesso a una di queste autorizzazioni da un utente aziendale, l'utente riceve un messaggio di errore che indica che non è autorizzato a fornire il consenso alle autorizzazioni dell'applicazione.

Se l'applicazione richiede accesso a questi ambiti riservati all'amministratore delle organizzazioni, è necessario richiederle direttamente all'amministratore dell'azienda anche tramite l' **endpoint di consenso dell'amministratore**, come descritto di seguito.

Quando un amministratore concede le autorizzazioni tramite l'endpoint di consenso dell'amministratore, il consenso viene concesso a tutti gli utenti del tenant, come descritto in precedenza.

## <a name="using-the-admin-consent-endpoint"></a>Uso dell'endpoint di consenso dell'amministratore
Seguendo questa procedura, l'applicazione potrà ottenere le autorizzazioni per tutti gli utenti di un dato tenant, inclusi gli ambiti riservati all'amministratore.  Per un esempio di codice che implementa la procedura seguente, vedere l' [esempio sugli ambiti riservati all'amministratore](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Richiedere le autorizzazioni nel portale di registrazione dell'app
* Passare all'applicazione in [apps.dev.microsoft.com](https://apps.dev.microsoft.com) o [creare un'app](active-directory-v2-app-registration.md) se ancora non è stata creata.
* Individuare la sezione **Microsoft Graph Permissions** (Autorizzazioni Microsoft Graph) e aggiungere le autorizzazioni richieste dall'applicazione.
* **Salvare** la registrazione dell'app.

#### <a name="recommended:-sign-the-user-into-your-app"></a>Consigliato: disconnettere l'utente dall'applicazione
In genere durante la compilazione di un'applicazione che usa l'endpoint di consenso dell'amministratore, l'app disporrà di una pagina/vista che consenta all'amministratore di approvare le autorizzazioni dell'applicazione.  Questa pagina può essere parte del flusso di iscrizione all'app, delle impostazioni dell'applicazione o di un flusso di "connessione" dedicato.  In molti casi, è utile per l'applicazione visualizzare la pagina di "connessione" solo dopo che un utente ha eseguito l'accesso con un account di lavoro o dell'istituto di istruzione Microsoft.

L'accesso dell'utente nell'app consente di identificarne l'organizzazione di appartenenza dell'amministratore prima di richiedere l'approvazione delle autorizzazioni necessarie.  Sebbene non sia strettamente necessario, questo consente di creare un'esperienza più intuitiva per gli utenti dell'organizzazione.  Per l'accesso utente, seguire le [esercitazioni sui protocolli 2.0](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Richiedere le autorizzazioni da un amministratore di directory
Quando si è pronti per richiedere le autorizzazioni dall'amministratore dell'azienda, è possibile reindirizzare l'utente sull' **endpoint di consenso dell'amministratore**2.0.

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro Tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parametro |  | Descrizione |
| --- | --- | --- |
| tenant |Obbligatoria |Il tenant della directory da cui si desidera richiedere autorizzazioni.  Può essere fornito nel formato di nome descrittivo o guid. |
| client_id |Obbligatoria |ID applicazione che il portale di registrazione ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) ha assegnato all'app. |
| redirect_uri |Obbligatoria |URI di reindirizzamento in cui si desidera che venga inviata la risposta per la gestione da parte dell'app.  Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale. |
| state |Consigliato |Valore incluso nella richiesta che verrà restituito anche nella risposta del token.  Può trattarsi di una stringa di qualsiasi contenuto.  Lo stato viene usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |

A questo punto, Azure AD imporrà che solo un amministratore tenant possa accedere per completare la richiesta.  L'amministratore dovrà approvare tutte le autorizzazioni richieste per l'app nel portale di registrazione. 

##### <a name="successful-response"></a>Risposta con esito positivo
Se l'amministratore approva le autorizzazioni per l'applicazione, la risposta con esito positivo sarà:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametro | Descrizione |
| --- | --- | --- |
| tenant |Tenant della directory che ha concesso all'applicazione le autorizzazioni richieste, in formato guid. |
| state |Valore incluso nella richiesta che verrà restituito anche nella risposta del token.  Può trattarsi di una stringa di qualsiasi contenuto.  Lo stato viene usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| admin_consent |Sarà impostato su `True`. |

##### <a name="error-response"></a>Risposta di errore
Se l'amministratore non approva le autorizzazioni per l'applicazione, la risposta di errore sarà:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametro | Descrizione |
| --- | --- | --- |
| error |Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| error_description |Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore. |

Dopo aver ricevuto una risposta con esito positivo dall'endpoint di consenso dell'amministratore, l'applicazione ha ottenuto le autorizzazioni richieste.  È ora possibile passare alla richiesta di un token per la risorsa desiderata, descritta di seguito.

## <a name="using-permissions"></a>Uso delle autorizzazioni
Dopo che l'utente fornisce il consenso alle autorizzazioni relative all'app, quest'ultima può acquisire token di accesso che rappresentano l'autorizzazione dell'app ad accedere a una risorsa.  Un determinato token di accesso può essere usato per una sola risorsa, ma al suo interno saranno codificate tutte le autorizzazioni della risorsa specifica per cui l'app ha ottenuto il consenso.  Per ottenere un token di accesso, l'app può eseguire una richiesta all'endpoint del token 2.0:

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

Il token di accesso risultante può quindi essere usato nelle richieste HTTP relative alla risorsa e indicherà in modo affidabile alla risorsa che l'app dispone dell'autorizzazione appropriata per eseguire un'attività specifica.  

Per informazioni dettagliate sul protocollo OAuth 2.0 e su come ottenere i token di accesso, vedere il [riferimento al protocollo dell'endpoint v2.0](active-directory-v2-protocols.md).

<!--HONumber=Oct16_HO2-->


