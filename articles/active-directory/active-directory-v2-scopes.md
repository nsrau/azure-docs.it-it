<properties
	pageTitle="Ambiti, autorizzazioni e consenso di Modello app v2.0 | Microsoft Azure"
	description="Descrizione del modello di autorizzazione in Modello app 2.0 di Azure AD, inclusi gli ambiti, le autorizzazioni e il consenso."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/10/2015"
	ms.author="dastrock"/>

# Anteprima di Modello app 2.0: Ambiti, autorizzazioni e consenso

Le app che si integrano con Azure AD seguono un modello di autorizzazione che consente agli utenti di controllare la modalità di accesso ai dati da parte di un'app. In Modello app 2.0 l'implementazione di questo modello di autorizzazione è stata aggiornata, modificando la modalità di interazione di un'app con Azure AD. Questo argomento illustra i concetti di base di questo modello di autorizzazione, inclusi gli ambiti, le autorizzazioni e il consenso.

> [AZURE.NOTE]Queste informazioni fanno riferimento all'anteprima pubblica di Modello app 2.0. Per istruzioni su come eseguire l'integrazione con il servizio Azure AD disponibile a livello generale, consultare la [Guida per gli sviluppatori di Azure Active Directory](active-directory-developers-guide.md).

## Ambiti e autorizzazioni

Modello app 2.0 implementa il protocollo di autorizzazione [OAuth 2.0](active-directory-v2-protocols.md), ossia un metodo che consente a un'app di terze parti di accedere a risorse ospitate sul Web per conto dell'utente. Qualsiasi risorsa ospitata sul Web che si integra con Azure AD dovrà disporre di un identificatore di risorsa o di un **URI ID dell'app**. Ad esempio, alcune delle risorse ospitate sul Web di Microsoft includono:

- L'API di posta unificata di Office 365: `https://outlook.office.com`
- L'API di Gestione risorse di Azure: `https://management.azure.com`
- L'API Graph di Azure AD: `https://graph.windows.net`

Anche le risorse di terze parti integrate con Azure AD devono disporre di tale identificatore. Tali risorse possono inoltre definire un set di autorizzazioni che può essere usato per suddividere le funzionalità di tale risorsa in blocchi più piccoli. Ad esempio, l'API di posta unificata di Office 365 definisce le autorizzazioni di base seguenti:

- Lettura della cassetta postale di un utente
- Scrittura nella cassetta postale di un utente
- Invio di messaggi di posta elettronica come utente

Con la definizione di queste autorizzazioni, la risorsa può avere un controllo accurato dei dati e di come si espone all'esterno. Un'app di terze parti può infine richiedere tali autorizzazioni da un utente finale e quest'ultimo deve approvare le autorizzazioni prima che l'app possa agire per conto proprio. Suddividendo le funzionalità della risorsa in set di autorizzazioni più piccoli, è possibile creare le app di terze parti affinché richiedano solo le autorizzazioni specifiche necessarie per il relativo funzionamento. In questo modo, gli utenti finali possono sapere esattamente come un'app userà i propri dati e possono essere più sicuri che l'app non agisca per fini dannosi.

In Azure AD e OAuth queste autorizzazioni vengono definite **ambiti** o **oAuth2Permissions**. Un ambito è rappresentato in Azure AD come valore stringa. Nell'esempio relativo all'API di posta unificata di Office 365 il valore di ambito per ogni autorizzazione è:

- Lettura della cassetta postale di un utente: `Mail.Read`
- Scrittura nella cassetta postale di un utente: `Mail.ReadWrite`
- Invio di messaggi di posta elettronica come utente: `Mail.Send`

Un'app può richiedere tali autorizzazioni specificando gli ambiti nelle richieste all'endpoint 2.0, come descritto di seguito.

## Consenso

In una richiesta di autorizzazione [OpenID Connect o OAuth 2.0](active-directory-v2-protocols.md) un'app può richiedere le autorizzazioni necessarie usando il parametro di query `scope`. Ad esempio, quando un utente accede a un'app, questa può inviare una richiesta simile alla seguente (con interruzioni di riga per migliorare la leggibilità):

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Foutlook.office.com%2Fmail.read%20
https%3A%2F%2Foutlook.office.com%2Fmail.send
&state=12345
```

Il parametro `scope` è un elenco di ambiti separati da spazi richiesti dall'app. Ogni ambito è indicato dall'aggiunta del valore dell'ambito all'identificatore della risorsa (URI ID dell'app). La richiesta precedente indica che l'app richiede l'autorizzazione per la lettura della cassetta postale dell'utente e l'invio di messaggi di posta elettronica come utente.

Dopo che l'utente immette le proprie credenziali, l'endpoint 2.0 verifica la disponibilità di un record corrispondente di **consenso dell'utente**. Se l'utente non ha fornito in precedenza il consenso per nessuna delle autorizzazioni necessarie, l'endpoint 2.0 chiede all'utente di fornire il consenso per le autorizzazioni obbligatorie.

![Schermata di consenso dell'account aziendale](../media/active-directory-v2-flows/work_account_consent.png)

Quando l'utente approva l'autorizzazione, il consenso viene registrato in modo che non debba essere fornito nuovamente agli accessi successivi.

## Consenso incrementale

L'app non deve richiedere tutte le autorizzazioni di cui necessita al primo accesso o alla prima iscrizione dell'utente, in quanto è possibile specificare gli ambiti in base alle richieste. Ossia, l'app può eseguire il "consenso incrementale" e scegliere quale sia il momento migliore per chiedere all'utente di fornire il consenso. Di seguito sono elencati alcuni dei motivi comuni per cui un'app non può chiedere tutte le autorizzazioni contemporaneamente:

- App che richiedono molte autorizzazioni. Se l'app accede a molte risorse diverse ed esegue funzionalità complesse su ogni risorsa, l'elenco di autorizzazioni necessarie per l'app può diventare rapidamente molto esteso. In passato, la presenza di lunghi elenchi di autorizzazioni ha portato gli utenti a rinunciare all'iscrizione a un'app riducendone, di conseguenza, l'adozione. Anziché richiedere tutte queste autorizzazioni contemporaneamente, l'app può richiedere al primo accesso di approvare un sottoinsieme di autorizzazioni e quindi chiedere in modo incrementale l'approvazione di autorizzazioni aggiuntive man mano che l'utente tenta di usare funzionalità avanzate.
- App che aumentano nel tempo le proprie funzionalità. Quasi ogni app inizia con un numero ridotto di funzionalità per poi aumentare nel tempo incorporando nuove funzionalità. Con il consenso incrementale, è possibile apportare facilmente modifiche all'app e richiedere senza problemi nuove autorizzazioni all'utente. È sufficiente aggiornare il codice per inviare ambiti aggiuntivi nelle richieste di autorizzazione.
- Autorizzazioni riservate all'amministratore. Alcune risorse definiscono autorizzazioni che possono essere approvate **solo** dall'amministratore dell'organizzazione. Ad esempio, l'API Graph di Azure AD può definire un'autorizzazione `Directory.Write` che consente a un'app di creare, aggiornare ed eliminare utenti e gruppi. È facile immaginare perché tale autorizzazione richieda l'approvazione da parte di un amministratore con privilegi elevati. Mediante il consenso incrementale, l'app può esporre un set di base di funzionalità a cui tutti gli utenti possono iscriversi senza l'approvazione di un amministratore. Tuttavia, quando tentano di eseguire un'azione che richiede privilegi elevati, è possibile definire un'autorizzazione riservata all'amministratore e chiedere l'iscrizione come amministratore prima di accedere a tale parte dell'app.

## Uso delle autorizzazioni

Dopo che l'utente fornisce il consenso alle autorizzazioni relative all'app, quest'ultima può acquisire token di accesso che rappresentano l'autorizzazione dell'app ad accedere a una risorsa. Un determinato token di accesso può essere usato per una sola risorsa, ma al suo interno saranno codificate tutte le autorizzazioni della risorsa specifica per cui l'app ha ottenuto il consenso. Per ottenere un token di accesso, l'app può eseguire una richiesta all'endpoint del token 2.0:

```
POST common/v2.0/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "2d4d11a2-f814-46a7-890a-274a72a7309e",
	"scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
	"client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Il token di accesso risultante può quindi essere usato nelle richieste HTTP relative alla risorsa e indicherà in modo affidabile alla risorsa che l'app dispone dell'autorizzazione appropriata per eseguire un'attività specifica.

Per informazioni dettagliate sul protocollo OAuth 2.0 e su come ottenere i token di accesso, vedere il [riferimento al protocollo di Modello app 2.0](active-directory-v2-protocols.md).

## OpenID e Offline\_Access

Modello app 2.0 dispone di due ambiti ben definiti che non si applicano a una risorsa specifica: `openid` e `offline_access`.

#### OpenID

Se un'app esegue l'accesso usando [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow), deve richiedere l'ambito `openid`. L'ambito `openid` viene visualizzato nella schermata di consenso dell'account aziendale come autorizzazione di accesso e nella schermata di consenso dell'account personale Microsoft come autorizzazione per la visualizzazione del profilo e la connessione ad app e servizi mediante l'account Microsoft. Questa autorizzazione consente a un'app di accedere all'endpoint di informazioni sull'utente di OpenID Connect e pertanto richiede l'approvazione dell'utente. L'ambito `openid` può essere usato anche nell'endpoint del token 2.0 per acquisire token ID, che possono essere usati per proteggere le chiamate HTTP tra diversi componenti di un'app.

#### Offline\_Access

L'ambito `offline_access` consente all'app di accedere alle risorse per conto dell'utente per un periodo di tempo prolungato. Nella schermata di consenso dell'account aziendale, l'ambito verrà visualizzato come autorizzazione per l'accesso ai dati in qualsiasi momento. Nella schermata di consenso dell'account personale Microsoft, verrà visualizzato come autorizzazione per l'accesso alle informazioni in qualsiasi momento. Quando un utente approva l'ambito `offline_access`, l'app viene abilitata per la ricezione di token di aggiornamento dall'endpoint del token 2.0. I token di aggiornamento hanno una durata elevata e consentono all'app di acquisire nuovi token di accesso alla scadenza dei precedenti.

Se l'app non richiede l'ambito `offline_access`, non riceverà i token di aggiornamento. Pertanto, se si riscatta un codice di autorizzazione nel [flusso del codice di autorizzazione di OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow), si riceverà solo un token di accesso dall'endpoint `/token`. Tale token di accesso rimarrà valido per un breve periodo di tempo (in genere un'ora), per poi scadere. A questo punto, l'app reindirizza l'utente all'endpoint `/authorize` per recuperare un nuovo codice di autorizzazione. Durante il reindirizzamento, l'utente può o meno avere esigenza di immettere nuovamente le proprie credenziali o fornire il consenso per le autorizzazioni, a seconda del tipo di app.

Per altre informazioni su come ottenere e usare i token di aggiornamento, consultare il [riferimento al protocollo di Modello app 2.0](active-directory-v2-protocols.md).

<!---HONumber=Nov15_HO3-->