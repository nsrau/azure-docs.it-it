---
title: Ambiti, autorizzazioni e consenso della piattaforma di identità Microsoft | Microsoft Docs
description: Descrizione dell'autorizzazione nell'endpoint della piattaforma di identità Microsoft, inclusi gli ambiti, le autorizzazioni e il consenso.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 8f98cbf0-a71d-4e34-babf-e644ad9ff423
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur
ms.custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: edb6d96dfdca63f1bacf45ab0af01d18aafcf302
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73667868"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Autorizzazioni e consenso nell'endpoint di Microsoft Identity Platform

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Le applicazioni che si integrano con Microsoft Identity Platform seguono un modello di autorizzazione che offre a utenti e amministratori il controllo sulle modalità di accesso ai dati. L'implementazione del modello di autorizzazione è stata aggiornata nell'endpoint della piattaforma di identità Microsoft e cambia il modo in cui un'app deve interagire con la piattaforma di identità Microsoft. Questo articolo illustra i concetti di base di questo modello di autorizzazione, inclusi gli ambiti, le autorizzazioni e il consenso.

> [!NOTE]
> L'endpoint della piattaforma Microsoft Identity non supporta tutti gli scenari e le funzionalità. Per determinare se è necessario usare l'endpoint della piattaforma Microsoft Identity, vedere le informazioni sulle [limitazioni della piattaforma di identità Microsoft](active-directory-v2-limitations.md).

## <a name="scopes-and-permissions"></a>Ambiti e autorizzazioni

Microsoft Identity Platform implementa il protocollo di autorizzazione [OAuth 2.0](active-directory-v2-protocols.md). OAuth 2.0 è un metodo tramite cui un'applicazione di terze parti può accedere alle risorse ospitate sul Web per conto dell'utente. Qualsiasi risorsa ospitata sul Web che si integra con Microsoft Identity Platform ha un identificatore di risorsa, o *URI dell'ID applicazione*. Ad esempio, alcune delle risorse ospitate sul Web di Microsoft includono:

* Microsoft Graph: `https://graph.microsoft.com`
* API Office 365 Mail: `https://outlook.office.com`
* Azure AD Graph: `https://graph.windows.net`

> [!NOTE]
> È consigliabile usare Microsoft Graph invece di Azure AD Graph, dell'API Office 365 Mail e così via.

Lo stesso vale per le risorse di terze parti integrate con Microsoft Identity Platform. Tali risorse possono anche definire un set di autorizzazioni che può essere usato per suddividere le funzionalità di tale risorsa in blocchi più piccoli. Ad esempio, [Microsoft Graph](https://graph.microsoft.com) ha definito le autorizzazioni per eseguire le attività seguenti, tra le altre:

* Lettura del calendario dell'utente
* Scrittura nel calendario dell'utente
* Invio di messaggi di posta elettronica come utente

Con la definizione di questi tipi di autorizzazioni, la risorsa può avere un controllo accurato dei dati e dell'esposizione delle funzionalità API. Un'app di terze parti può richiedere queste autorizzazioni da utenti e amministratori, che devono approvare la richiesta prima che l'app possa accedere ai dati o agire per conto di un utente. Suddividendo le funzionalità della risorsa in set di autorizzazioni più piccoli, è possibile creare le app di terze parti affinché richiedano solo le autorizzazioni specifiche necessarie per il relativo funzionamento. Gli utenti e gli amministratori possono conoscere esattamente i dati a cui l'app ha accesso e possono essere più sicuri che non si comportano con finalità dannose. Gli sviluppatori devono sempre rispettare il concetto di privilegio minimo, richiedendo solo le autorizzazioni necessarie per il funzionamento delle loro applicazioni.

In OAuth 2.0 questi tipi di autorizzazioni vengono definiti *ambiti* o Sono anche spesso denominate *autorizzazioni*. In Microsoft Identity Platform un'autorizzazione è rappresentata come valore stringa. Nell'esempio relativo a Microsoft Graph il valore stringa per ogni autorizzazione è:

* Lettura del calendario dell'utente tramite `Calendars.Read`
* Scrittura del calendario dell'utente tramite `Calendars.ReadWrite`
* Invio di messaggi di posta elettronica come utente tramite `Mail.Send`

Un'app richiede in genere queste autorizzazioni specificando gli ambiti nelle richieste all'endpoint di autorizzazione della piattaforma di identità Microsoft. Tuttavia, alcune autorizzazioni con privilegi elevati possono essere concesse solo tramite il consenso dell'amministratore e richieste/concesse tramite l' [endpoint di consenso dell'amministratore](v2-permissions-and-consent.md#admin-restricted-permissions). Per altre informazioni, continuare la lettura.

## <a name="permission-types"></a>Tipi di autorizzazioni

Microsoft Identity Platform supporta due tipi di autorizzazioni: **autorizzazioni delegate** e **autorizzazioni dell'applicazione**.

* Le **autorizzazioni delegate** sono usate dalle app con un utente connesso. Per queste app, l'utente o un amministratore acconsente alle autorizzazioni richieste dall'app e l'app è autorizzata a fungere da utente connesso quando effettua chiamate alla risorsa di destinazione. Alcune autorizzazioni delegate possono essere concesse da utenti senza privilegi di amministratore, mentre altre con privilegi più elevati richiedono il [consenso dell'amministratore](v2-permissions-and-consent.md#admin-restricted-permissions). Per informazioni su quali ruoli di amministratore possono fornire il consenso per le autorizzazioni delegate, vedere [Autorizzazioni del ruolo di amministratore in Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

* Le **autorizzazioni dell'applicazione** sono usate dalle app che vengono eseguite senza un utente connesso, ad esempio le app eseguite come servizi in background o daemon.  Le autorizzazioni dell'applicazione possono essere [concesse esclusivamente da un amministratore](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

Le _autorizzazioni valide_ sono le autorizzazioni che l'app avrà quando effettuerà le richieste alla risorsa di destinazione. È importante comprendere la differenza tra le autorizzazioni delegate e dell'applicazione concesse all'app e le autorizzazioni valide quando si effettuano chiamate alla risorsa di destinazione.

- Per le autorizzazioni delegate, le _autorizzazioni valide_ dell'app sono costituite dall'intersezione con meno privilegi tra le autorizzazioni delegate concesse all'app (tramite il consenso) e i privilegi dell'utente attualmente connesso. L'app non può mai avere più privilegi rispetto all'utente connesso. All'interno delle organizzazioni, i privilegi dell'utente connesso possono essere determinati da criteri o dall'appartenenza a uno o più ruoli di amministratore. Per informazioni su quali ruoli di amministratore possono fornire il consenso per le autorizzazioni delegate, vedere [Autorizzazioni del ruolo di amministratore in Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

   Si supponga, ad esempio, che all'app sia stata concessa l'autorizzazione delegata _User.ReadWrite.All_. Questa autorizzazione concede nominalmente all'app l'autorizzazione per leggere e aggiornare il profilo di ogni utente in un'organizzazione. Se l'utente connesso è un amministratore globale, l'app sarà in grado di aggiornare il profilo di tutti gli utenti dell'organizzazione. Tuttavia, se l'utente che ha eseguito l'accesso non è incluso in un ruolo di amministratore, l'app sarà in grado di aggiornare solo il profilo dell'utente che ha eseguito l'accesso. Non sarà in grado di aggiornare i profili di altri utenti nell'organizzazione, perché l'utente per conto del quale è autorizzata ad agire non dispone di tali privilegi.
  
- Per le autorizzazioni dell'applicazione, le _autorizzazioni valide_ dell'app corrispondono al livello completo di privilegi impliciti nell'autorizzazione. Un'app che ha l'autorizzazione dell'applicazione _User.ReadWrite.All_ può ad esempio aggiornare il profilo di tutti gli utenti dell'organizzazione. 

## <a name="openid-connect-scopes"></a>Ambiti di OpenID Connect

L'implementazione della piattaforma di identità Microsoft di OpenID Connect presenta alcuni ambiti ben definiti che non si applicano a una risorsa specifica: `openid`, `email`, `profile`e `offline_access`. Gli ambiti `address` e `phone` di OpenID Connect non sono supportati.

### <a name="openid"></a>openid

Se un'app esegue l'accesso usando [OpenID Connect](active-directory-v2-protocols.md), deve richiedere l'ambito `openid`. L'ambito `openid` viene visualizzato nella pagina di consenso dell'account aziendale come autorizzazione di accesso e nella pagina di consenso dell'account personale Microsoft come autorizzazione per la visualizzazione del profilo e la connessione ad app e servizi tramite l'account Microsoft. Questa autorizzazione consente a un'app di ricevere un identificatore univoco per l'utente sotto forma di attestazione `sub` e concede all'app l'accesso all'endpoint delle informazioni utente. L'ambito `openid` può essere usato nell'endpoint token della piattaforma di identità Microsoft per acquisire i token ID, che possono essere usati dall'app per l'autenticazione.

### <a name="email"></a>email

L'ambito `email` può essere usato con l'ambito `openid` e con tutti gli altri. Consente all'applicazione di accedere all'indirizzo di posta elettronica primario dell'utente sotto forma di attestazione `email`. L'attestazione `email` è inclusa in un token solo se l'indirizzo di posta elettronica è associato all'account utente, che non è sempre il caso. Se si usa l'ambito `email`, l'applicazione deve essere pronta per gestire il caso in cui l'attestazione `email` non esiste nel token.

### <a name="profile"></a>Profilo

L'ambito `profile` può essere usato con l'ambito `openid` e con tutti gli altri. Consente all'applicazione di accedere a numerose informazioni sull'utente, Le informazioni a cui può accedere includono, a titolo esemplificativo, il nome, il cognome, il nome utente preferito e l'ID oggetto specificati dall'utente. Per un elenco completo delle attestazioni profilo disponibili nel parametro id_tokens per un determinato utente, vedere le [informazioni di riferimento su `id_tokens`](id-tokens.md).

### <a name="offline_access"></a>offline_access

L'ambito [`offline_access`](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) consente all'app di accedere alle risorse per conto dell'utente per un periodo di tempo prolungato. Nella pagina di consenso l'ambito viene visualizzato come autorizzazione "Mantieni l'accesso ai dati per cui hai concesso l'accesso a". Quando un utente approva l'ambito di `offline_access`, l'app può ricevere i token di aggiornamento dall'endpoint del token della piattaforma Microsoft Identity. I token di aggiornamento sono di lunga durata. L'applicazione può ottenere nuovi token di accesso quando i vecchi scadono.

Se l'app non richiede in modo esplicito l'ambito `offline_access`, non riceverà i token di aggiornamento. Pertanto, se si riscatta un codice di autorizzazione nel [flusso del codice di autorizzazione di OAuth 2.0](active-directory-v2-protocols.md), si riceve solo un token di accesso dall'endpoint `/token`. Il token di accesso è valido per un breve periodo. Il token di accesso ha in genere una durata di un'ora. A questo punto, l'app reindirizza l'utente all'endpoint `/authorize` per recuperare un nuovo codice di autorizzazione. Durante il reindirizzamento, a seconda del tipo di app, l'utente potrebbe dover immettere nuovamente le proprie credenziali o fornire il consenso per le autorizzazioni. Mentre l'ambito `offline_access` viene richiesto automaticamente dal server, il client deve comunque richiederlo per ricevere i token di aggiornamento.

Per ulteriori informazioni su come ottenere e usare i token di aggiornamento, vedere il [riferimento al protocollo Microsoft Identity Platform](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Richiesta di consenso per un singolo utente

In una richiesta di autorizzazione [OpenID Connect o OAuth 2.0](active-directory-v2-protocols.md) un'app può richiedere le autorizzazioni necessarie usando il parametro di query `scope`. Ad esempio, quando un utente accede a un'app, questa può inviare una richiesta simile alla seguente (con interruzioni di riga aggiunte per leggibilità):

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

Il parametro `scope` è un elenco di autorizzazioni delegate separate da spazi richieste dall'app. Ogni autorizzazione è indicata dall'aggiunta del valore dell'autorizzazione all'identificatore della risorsa (URI dell'ID applicazione). Nella richiesta di esempio precedente, l'app richiede l'autorizzazione per la lettura del calendario dell'utente e l'invio di messaggi di posta elettronica come utente.

Dopo che l'utente immette le proprie credenziali, l'endpoint della piattaforma di identità Microsoft verifica la presenza di un record corrispondente di *consenso dell'utente*. Se l'utente non ha acconsentito a nessuna delle autorizzazioni richieste in passato, né dispone di un amministratore autorizzato a queste autorizzazioni per conto dell'intera organizzazione, l'endpoint della piattaforma Microsoft Identity chiede all'utente di concedere le autorizzazioni richieste.

> [!NOTE]
> Attualmente, le autorizzazioni `offline_access` ("Mantieni l'accesso ai dati per cui hai concesso l'accesso a") e `user.read` ("Accedi e visualizza il profilo personale") vengono automaticamente incluse nel consenso iniziale per un'applicazione.  Queste autorizzazioni sono in genere necessarie per il corretto funzionamento dell'app: `offline_access` consente all'app di accedere a token di aggiornamento, essenziali per le app Web e native, mentre `user.read` consente di accedere all'attestazione `sub`, permettendo al client o all'app di identificare correttamente l'utente nel tempo e accedere a informazioni utente elementari.  

![Schermata di esempio che mostra il consenso dell'account di lavoro](./media/v2-permissions-and-consent/work_account_consent.png)

Quando l'utente approva la richiesta di autorizzazione, il consenso viene registrato e l'utente non deve fornirlo nuovamente agli accessi successivi all'applicazione.

## <a name="requesting-consent-for-an-entire-tenant"></a>Richiesta di consenso per un intero tenant

Spesso, quando un'organizzazione acquista una licenza o una sottoscrizione per un'applicazione, vuole configurare in modo proattivo l'applicazione per l'uso da parte di tutti i membri dell'organizzazione. Come parte di questo processo, un amministratore può concedere il consenso all'applicazione ad agire per conto di qualsiasi utente nel tenant. Se l'amministratore concede il consenso per l'intero tenant, gli utenti dell'organizzazione non visualizzeranno la pagina di consenso per l'applicazione.

Per richiedere il consenso per le autorizzazioni delegate per tutti gli utenti in un tenant, l'app può usare l'endpoint di consenso amministratore.

Le applicazioni devono inoltre usare l'endpoint di consenso amministratore per richiedere le autorizzazioni dell'applicazione.

## <a name="admin-restricted-permissions"></a>Autorizzazioni riservate all'amministratore

Alcune autorizzazioni con privilegi elevati nell'ecosistema Microsoft possono essere impostati come *riservati all'amministratore*. Tra gli esempi di questi tipi di autorizzazioni sono incluse le seguenti:

* Lettura dei profili completi degli utenti tramite `User.Read.All`
* Scrittura di dati in una directory aziendale tramite `Directory.ReadWrite.All`
* Lettura di tutti i gruppi nella directory aziendale tramite `Groups.Read.All`

Sebbene un utente consumer possa concedere a un'applicazione l'accesso a questi tipi di dati, gli utenti aziendali non possono concedere accesso allo stesso set di dati riservati dell'azienda. Se l'applicazione richiede l'accesso a una di queste autorizzazioni da un utente aziendale, l'utente riceve un messaggio di errore che indica che non è autorizzato a fornire il consenso alle autorizzazioni dell'app.

Se l'applicazione richiede accesso ad ambiti riservati all'amministratore delle organizzazioni, è necessario richiederle direttamente all'amministratore dell'azienda anche tramite l'endpoint di consenso dell'amministratore, come descritto di seguito.

Se l'applicazione richiede autorizzazioni delegate con privilegi elevati e un amministratore concede queste autorizzazioni tramite l'endpoint di consenso amministratore, il consenso viene concesso a tutti gli utenti del tenant.

Se l'applicazione richiede le autorizzazioni dell'applicazione e un amministratore concede tali autorizzazioni tramite l'endpoint di consenso dell'amministratore, la concessione non viene eseguita per conto di un utente specifico. Al contrario, all'applicazione client le autorizzazioni verranno concesse *direttamente*. Questi tipi di autorizzazioni vengono usati solo dai servizi daemon e da altre applicazioni non interattive eseguite in background.

## <a name="using-the-admin-consent-endpoint"></a>Uso dell'endpoint di consenso dell'amministratore

> [!NOTE] 
> Si noti che dopo aver concesso il consenso dell'amministratore tramite l'endpoint di consenso dell'amministratore, l'utente ha terminato di concedere il consenso dell'amministratore e gli utenti non devono eseguire altre azioni aggiuntive. Dopo aver concesso il consenso dell'amministratore, gli utenti possono ottenere un token di accesso tramite un tipico flusso di autenticazione e il token di accesso risultante avrà le autorizzazioni consentite. 

Quando un amministratore aziendale usa l'applicazione e viene indirizzato all'endpoint di autorizzazione, Microsoft Identity Platform rileverà il ruolo dell'utente e chiederà se vuole fornire il consenso per conto dell'intero tenant per le autorizzazioni richieste. Tuttavia, è disponibile anche un endpoint di consenso amministratore dedicato utilizzabile per richiedere in modo proattivo che un amministratore conceda l'autorizzazione per conto dell'intero tenant. L'uso di questo endpoint è necessario anche per richiedere le autorizzazioni dell'applicazione, che non possono essere richieste usando l'endpoint di autorizzazione.

Seguendo questa procedura, l'app può richiedere le autorizzazioni per tutti gli utenti di un tenant, inclusi gli ambiti riservati all'amministratore. Questa è un'operazione con privilegi elevati e deve essere eseguita solo se necessario per lo scenario in uso.

Per un esempio di codice che implementa la procedura, vedere l'[esempio sugli ambiti riservati all'amministratore](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Richiedere le autorizzazioni nel portale di registrazione dell'app

Il consenso amministratore non accetta un parametro di ambito, pertanto tutte le autorizzazioni richieste devono essere definite in modo statico nella registrazione dell'applicazione. In generale, è consigliabile assicurarsi che le autorizzazioni definite in modo statico per una determinata applicazione siano un superset delle autorizzazioni che verranno richieste in modo dinamico/incrementale.

#### <a name="to-configure-the-list-of-statically-requested-permissions-for-an-application"></a>Per configurare l'elenco delle autorizzazioni richieste in modo statico per un'applicazione

1. Passare all'applicazione nell'esperienza [portale di Azure-registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) oppure [creare un'app](quickstart-register-app.md) , se non è già stata eseguita.
2. Individuare la sezione **autorizzazioni API** e, all'interno delle autorizzazioni API, fare clic su Aggiungi un'autorizzazione.
3. Selezionare **Microsoft Graph** dall'elenco delle API disponibili e quindi aggiungere le autorizzazioni richieste dall'app.
3. **Salvare** la registrazione dell'app.

### <a name="recommended-sign-the-user-into-your-app"></a>Consigliato: accedere all'utente nell'app

In genere, durante la compilazione di un'applicazione che usa l'endpoint di consenso dell'amministratore, l'app necessita di una pagina o vista che consenta all'amministratore di approvare le autorizzazioni dell'applicazione. Questa pagina può essere parte del flusso di iscrizione all'app, delle impostazioni dell'applicazione o di un flusso di "connessione" dedicato. In molti casi, è utile per l'applicazione visualizzare la pagina di "connessione" solo dopo che un utente ha eseguito l'accesso con un account di lavoro o dell'istituto di istruzione Microsoft.

L'accesso dell'utente nell'app consente di identificarne l'organizzazione di appartenenza dell'amministratore prima di richiedere l'approvazione delle autorizzazioni necessarie. Sebbene non sia strettamente necessario, questo consente di creare un'esperienza più intuitiva per gli utenti dell'organizzazione. Per accedere all'utente, seguire le [esercitazioni del protocollo Microsoft Identity Platform](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Richiedere le autorizzazioni da un amministratore di directory

Quando si è pronti per richiedere le autorizzazioni all'amministratore dell'organizzazione, è possibile reindirizzare l'utente all'endpoint di *consenso dell'amministratore*di Microsoft Identity Platform.

```
// Line breaks are for legibility only.
    GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
  client_id=6731de76-14a6-49ae-97bc-6eba6914391e
  &state=12345
  &redirect_uri=http://localhost/myapp/permissions
    &scope=
    https://graph.microsoft.com/calendars.read 
    https://graph.microsoft.com/mail.send
```


| Parametro     | Condizione     | Descrizione                                                                               |
|--------------:|--------------:|:-----------------------------------------------------------------------------------------:|
| `tenant` | Obbligatorio | Il tenant della directory da cui si desidera richiedere autorizzazioni. Può essere specificato in formato di GUID o nome descrittivo OPPURE con il riferimento generico `common` come illustrato nell'esempio. |
| `client_id` | Obbligatorio | **ID dell'applicazione (client)** che la [portale di Azure registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) l'esperienza assegnata all'app. |
| `redirect_uri` | Obbligatorio |URI di reindirizzamento in cui si desidera che venga inviata la risposta per la gestione da parte dell'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale di registrazione delle applicazioni. |
| `state` | Consigliato | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Usare questo stato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
|`scope`        | Obbligatorio      | Definisce il set di autorizzazioni richieste dall'applicazione. Può trattarsi di un ambito statico (con/.default) o di ambiti dinamici.  Possono essere inclusi gli ambiti OIDC (`openid`, `profile``email`). | 


A questo punto, Azure AD richiede che solo un amministratore tenant possa accedere per completare la richiesta. All'amministratore viene chiesto di approvare tutte le autorizzazioni richieste nel parametro `scope`.  Se è stato usato un valore statico (`/.default`), funzionerà come l'endpoint di consenso dell'amministratore della versione 1.0 e richiederà il consenso per tutti gli ambiti presenti nelle autorizzazioni necessarie per l'app.

#### <a name="successful-response"></a>Risposta con esito positivo

Se l'amministratore approva le autorizzazioni per l'app, la risposta con esito positivo si presenta come segue:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametro | Descrizione |
| --- | --- |
| `tenant` | Tenant della directory che ha concesso all'applicazione le autorizzazioni richieste, in formato GUID. |
| `state` | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Lo stato viene usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| `admin_consent` | Sarà impostato su `True`. |

#### <a name="error-response"></a>Risposta di errore

Se l'amministratore non approva le autorizzazioni per l'app, la risposta di errore si presenta come segue:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametro | Descrizione |
| --- | --- |
| `error` | Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| `error_description` | Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore. |

Dopo aver ricevuto una risposta con esito positivo dall'endpoint di consenso dell'amministratore, l'applicazione ha ottenuto le autorizzazioni richieste. Successivamente, è possibile richiedere un token per la risorsa desiderata.

## <a name="using-permissions"></a>Uso delle autorizzazioni

Dopo che l'utente fornisce il consenso alle autorizzazioni relative all'app, quest'ultima può acquisire token di accesso che rappresentano l'autorizzazione dell'app ad accedere a una risorsa. Un token di accesso può essere usato per una sola risorsa, ma al suo interno saranno codificate tutte le autorizzazioni della risorsa specifica per cui l'app ha ottenuto il consenso. Per acquisire un token di accesso, l'app può eseguire una richiesta all'endpoint token della piattaforma di identità Microsoft, come indicato di seguito:

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

Per ulteriori informazioni sul protocollo OAuth 2,0 e su come ottenere i token di accesso, vedere il [riferimento al protocollo dell'endpoint della piattaforma di identità Microsoft](active-directory-v2-protocols.md).

## <a name="the-default-scope"></a>Ambito /.default

È possibile usare l'ambito `/.default` per eseguire la migrazione delle app dall'endpoint v 1.0 all'endpoint della piattaforma Microsoft Identity. Si tratta di un ambito predefinito per ogni applicazione che fa riferimento all'elenco statico di autorizzazioni configurate nella registrazione dell'applicazione. Un valore `scope` di `https://graph.microsoft.com/.default` è funzionalmente identico a `resource=https://graph.microsoft.com` degli endpoint v1.0, vale a dire che richiede un token con gli ambiti in Microsoft Graph per cui l'applicazione è registrata nel portale di Azure.

L'ambito/.default può essere usato in qualsiasi flusso OAuth 2,0, ma è necessario per il flusso di [credenziali client](v2-oauth2-client-creds-grant-flow.md)e [di flusso per conto di](v2-oauth2-on-behalf-of-flow.md) .  

> [!NOTE]
> I client non possono combinare il consenso statico (`/.default`) e dinamico in un'unica richiesta. Di conseguenza, `scope=https://graph.microsoft.com/.default+mail.read` comporterà un errore dovuto alla combinazione dei tipi di ambito.

### <a name="default-and-consent"></a>/.default e consenso

L'ambito `/.default` attiva il comportamento dell'endpoint v1.0 anche per `prompt=consent`. Richiede il consenso per tutte le autorizzazioni registrate dall'applicazione, indipendentemente dalla risorsa. Se incluso come parte della richiesta, l'ambito `/.default` restituisce un token contenente gli ambiti per la risorsa richiesta.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default quando l'utente ha già dato il consenso

Dal momento che `/.default` è funzionalmente identico al comportamento dell'endpoint v1.0 incentrato su `resource`, rispecchia anche il comportamento di consenso dell'endpoint v1.0. Nello specifico, `/.default` attiva una richiesta di consenso solo se non è stata concessa alcuna autorizzazione tra il client e la risorsa da parte dell'utente. Se tale consenso esiste, verrà restituito un token che contiene tutti gli ambiti concessi dall'utente per tale risorsa. Tuttavia, se non è stata concessa alcuna autorizzazione o se è stato specificato il parametro `prompt=consent`, verrà visualizzata una richiesta di consenso per tutti gli ambiti registrati dall'applicazione client.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Esempio 1: l'utente o l'amministratore del tenant ha concesso le autorizzazioni

L'utente (o un amministratore del tenant) ha concesso al client le autorizzazioni di Microsoft Graph `mail.read` e `user.read`. Se il client effettua una richiesta per `scope=https://graph.microsoft.com/.default`, non verrà visualizzata alcuna richiesta di consenso indipendentemente dal contenuto delle autorizzazioni registrate delle applicazioni client per Microsoft Graph. Verrà restituito un token contenente gli ambiti `mail.read` e `user.read`.

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Esempio 2: l'utente non ha concesso le autorizzazioni tra il client e la risorsa

Non esiste alcuna autorizzazione per l'utente tra il client e Microsoft Graph. Il client è stato registrato per le autorizzazioni `user.read` e `contacts.read`, oltre che per l'ambito di Azure Key Vault `https://vault.azure.net/user_impersonation`. Quando il client richiede un token per `scope=https://graph.microsoft.com/.default`, l'utente visualizzerà una schermata di consenso per gli ambiti `user.read`, `contacts.read` e `user_impersonation` di Key Vault. Il token restituito avrà solo gli ambiti `user.read` e `contacts.read`.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-additional-scopes"></a>Esempio 3: l'utente ha acconsentito e il client richiede ambiti aggiuntivi

L'utente ha già dato il consenso per `mail.read` per il client. Il client è stato registrato per l'ambito `contacts.read` nella relativa registrazione. Quando il client effettua una richiesta per un token usando `scope=https://graph.microsoft.com/.default` e richiede il consenso tramite `prompt=consent`, l'utente visualizzerà una schermata di consenso solo per le autorizzazioni registrate dall'applicazione. `contacts.read` sarà presente nella schermata di consenso, a differenza di `mail.read`. Il token restituito sarà per Microsoft Graph e conterrà `mail.read` e `contacts.read`.

### <a name="using-the-default-scope-with-the-client"></a>Uso dell'ambito /.default con il client

Un caso speciale dell'ambito `/.default` sussiste quando un client richiede il proprio ambito `/.default`. L'esempio seguente illustra questo scenario.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Questa operazione produce una schermata di consenso per tutte le autorizzazioni registrate (se applicabili in base alle suddette descrizioni del consenso e `/.default`), quindi restituisce un id_token, anziché un token di accesso.  Questo comportamento esiste per determinati client legacy che passano da ADAL a MSAL e non devono essere usati da nuovi client destinati all'endpoint della piattaforma di identità Microsoft.  

## <a name="troubleshooting-permissions-and-consent"></a>Risoluzione dei problemi di autorizzazioni e consenso

Se durante il processo di consenso vengono visualizzati errori imprevisti durante il processo di consenso, vedere questo articolo per la procedura di risoluzione dei problemi: [errore imprevisto durante l'esecuzione del consenso per un'applicazione](../manage-apps/application-sign-in-unexpected-user-consent-error.md).
