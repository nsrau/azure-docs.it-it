---
title: Informazioni sui diversi tipi di token e di attestazione supportati da Azure AD | Documentazione Microsoft
description: Una guida alla comprensione e alla valutazione delle attestazioni nei token SAML 2.0 e JSON Web Tokens (JWT) emessi da Azure Active Directory (AAD)
documentationcenter: na
author: bryanla
services: active-directory
manager: mbaldwin
editor: 
ms.assetid: 166aa18e-1746-4c5e-b382-68338af921e2
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/07/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: ba958d029e5bf1bc914a2dff4b6c09282d578c67
ms.openlocfilehash: 4612c1f516dca51aea925343f79649761448c05d


---
# <a name="azure-ad-token-reference"></a>Riferimento al token di Azure AD
Azure Active Directory (Azure AD) rilascia tipi diversi di token di sicurezza durante l'elaborazione di ogni flusso di autenticazione. Questo documento descrive il formato, le caratteristiche di sicurezza e i contenuti di ogni tipo di token.

## <a name="types-of-tokens"></a>Tipi di token
Azure AD supporta il [protocollo di autorizzazione OAuth 2.0](active-directory-protocols-oauth-code.md), che usa sia token di accesso che token di aggiornamento.  L'endpoint supporta inoltre l'autenticazione e l'accesso tramite [OpenID Connect](active-directory-protocols-openid-connect-code.md), che introduce un terzo tipo di token: il token ID.  Ognuno di questi token viene rappresentato come "token di connessione".

Un token di connessione è un token di sicurezza leggero che consente al "portatore" di accedere a una risorsa protetta. In questo senso, per "portatore" si intende qualsiasi parte che sia in grado di presentare il token. Per ricevere un token di connessione è richiesta l'autenticazione con Azure AD ed è necessario seguire una procedura per proteggere il token onde evitare l'intercettazione da parte di soggetti non autorizzati. Dal momento che i token di connessione non hanno meccanismi integrati per prevenire l'uso non autorizzato, devono essere trasportati su un canale protetto, ad esempio Transport Layer Security (HTTPS). Se un token di connessione viene trasmesso senza essere protetto, un utente malintenzionato potrebbe usare un attacco "man in the middle" per acquisire il token e ottenere accesso non autorizzato a una risorsa protetta. Gli stessi principi di sicurezza si applicano quando un token di connessione viene archiviato o memorizzato nella cache per un uso futuro. Assicurarsi sempre che l'app trasmetta e archivi i token di connessione in modo sicuro. Per altre considerazioni sulla sicurezza dei token di connessione, vedere la [sezione 5 della specifica RFC 6750](http://tools.ietf.org/html/rfc6750).

Molti dei token rilasciati da Azure AD vengono implementati come token JSON Web o JWT.  Un token JWT è un modo compatto e indipendente dall'URL di trasferimento delle informazioni tra due parti.  Le informazioni contenute nei token JWT sono note come "attestazioni" o asserzioni di informazioni sulla connessione e sull'oggetto del token.  Le attestazioni nei token JWT sono oggetti JSON codificati e serializzati per la trasmissione.  Dato che i token JWT rilasciati da Azure AD sono firmati, ma non crittografati, è possibile esaminarne facilmente i contenuti per il debug.  Sono disponibili diversi strumenti per questa operazione, ad esempio [jwt.calebb.net](http://jwt.calebb.net). Per altre informazioni sui token JWT, fare riferimento alla [specifica di JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## <a name="idtokens"></a>Token ID
I token ID sono un tipo di token di sicurezza di accesso che l'app riceve quando esegue l'autenticazione usando [OpenID Connect](active-directory-protocols-openid-connect-code.md).  Vengono rappresentati come [JWT](#types-of-tokens) e contengono attestazioni che è possibile usare per l'accesso dell'utente all'app.  È possibile usare le attestazioni in un token ID in base alle proprie esigenze. In genere, vengono usate per la visualizzazione di informazioni sull'account o per prendere decisioni relative al controllo di accesso in un'app.

Attualmente i token ID sono firmati, ma non crittografati.  Quando l'app riceve un token ID, deve [convalidare la firma](#validating-tokens) per dimostrare l'autenticità del token e convalidare alcune attestazioni nel token per dimostrarne la validità.  Le attestazioni convalidate da un'app variano a seconda dei requisiti dello scenario, ma esistono alcune [convalide di attestazione comuni](#validating-tokens) che l'app deve eseguire in ogni scenario.

Vedere la sezione seguente per informazioni su attestazioni ed esempio di token ID.  Si noti che le attestazioni nei token ID non vengono restituite in un ordine particolare.  Inoltre, nei token ID è possibile introdurre nuove attestazioni in qualsiasi momento. L'app non deve interrompersi quando vengono introdotte nuove attestazioni.  L'elenco seguente include le attestazioni che l'app può interpretare in modo affidabile al momento della stesura di questo articolo.  Se necessario, sono presenti informazioni più dettagliate nella [specifica di OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-idtoken"></a>Token ID di esempio
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.
```

> [!TIP]
> A scopo di esercitazione, provare a verificare le attestazioni nel token ID di esempio incollandole in [calebb.net](http://jwt.calebb.net).
> 
> 

#### <a name="claims-in-idtokens"></a>Attestazioni nei token ID
> [!div class="mx-codeBreakAll"]
| Attestazione JWT | Nome | Descrizione |
| --- | --- | --- |
| `appid` |ID applicazione |Identifica l'applicazione che usa il token per l'accesso a una risorsa. L'applicazione può fungere per conto proprio o per conto dell'utente. L'ID dell'applicazione in genere rappresenta un oggetto applicazione, ma può anche rappresentare un oggetto di entità servizio in Azure AD. <br><br> **Valore token JWT di esempio**: <br> `"appid":"15CB020F-3984-482A-864D-1D92265E8268"` |
| `aud` |Destinatari |Destinatario previsto per il token. L'applicazione che riceve il token deve verificare che il valore del gruppo di destinatari sia corretto e rifiuta tutti i token destinati a un gruppo di destinatari diverso. <br><br> **Valore SAML di esempio**: <br> `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>` <br><br> **Valore token JWT di esempio**: <br> `"aud":"https://contoso.com"` |
| `appidacr` |Riferimento alla classe contesto di autenticazione applicazione |Indica la modalità di autenticazione del client. Per un client pubblico, il valore è 0. Se vengono usati l'ID client e il segreto client, il valore è 1. <br><br> **Valore token JWT di esempio**: <br> `"appidacr": "0"` |
| `acr` |Riferimento alla classe contesto di autenticazione |Indica la modalità di autenticazione del soggetto, invece del client, nell'attestazione di riferimento alla classe contesto di autenticazione dell'applicazione. Il valore "0" indica che l'autenticazione dell'utente finale non soddisfa i requisiti ISO/IEC 29115. <br><br> **Valore token JWT di esempio**: <br> `"acr": "0"` |
| Istante di autenticazione |Registra la data e l'ora in cui è avvenuta l'autenticazione. <br><br> **Valore SAML di esempio**: <br> `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` | |
| `amr` |Metodo di autenticazione |Identifica la modalità di autenticazione dell'oggetto del token. <br><br> **Valore SAML di esempio**: <br> `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` <br><br> **Valore token JWT di esempio**: `“amr”: ["pwd"]` |
| `given_name` |Nome |Fornisce il nome o il nome "specificato" dell'utente, come impostato nell'oggetto utente di Azure AD. <br><br> **Valore SAML di esempio**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>`<br>`<AttributeValue>Frank<AttributeValue>` <br><br> **Valore token JWT di esempio**: <br> `"given_name": "Frank"` |
| `groups` |Gruppi |Fornisce gli ID oggetto che rappresentano le appartenenze ai gruppi del soggetto. Questi valori sono univoci (vedere ID oggetto) e possono essere usati in modo sicuro per la gestione dell'accesso, ad esempio l'attivazione dell'autorizzazione per accedere a una risorsa. I gruppi inclusi nella relativa attestazione sono configurati in base all'applicazione, tramite la proprietà "groupMembershipClaims" del manifesto dell'applicazione. Un valore null escluderà tutti i gruppi, un valore "SecurityGroup" includerà solo le appartenenze al gruppo di sicurezza di Active Directory e un valore "All" includerà sia i gruppi di sicurezza che le liste di distribuzione di Office 365. <br><br> **Valore SAML di esempio**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` <br><br> **Valore token JWT di esempio**: <br> `“groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]` |
| `idp` |Provider di identità |Registra il provider di identità che ha autenticato l'oggetto del token. Questo valore è identico al valore dell'attestazione Autorità di certificazione, a meno che l'account utente non sia in un tenant diverso rispetto all'autorità di certificazione. <br><br> **Valore SAML di esempio**: <br> `<Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` <br><br> **Valore token JWT di esempio**: <br> `"idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `iat` |IssuedAt |Archivia l'ora in cui è stato rilasciato il token. Viene spesso usata per misurare la validità del token. <br><br> **Valore SAML di esempio**: <br> `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` <br><br> **Valore token JWT di esempio**: <br> `"iat": 1390234181` |
| `iss` |Issuer |Identifica il servizio token di sicurezza (STS) che costruisce e restituisce il token. Nel token restituito da Azure AD l'autorità di certificazione è sts.windows.net. Il GUID del valore di attestazione dell'autorità di certificazione è l'ID tenant della directory di Azure AD. L'ID tenant è un identificatore non modificabile e affidabile della directory. <br><br> **Valore SAML di esempio**: <br> `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` <br><br> **Valore token JWT di esempio**: <br>  `"iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `family_name` |Cognome |Fornisce il cognome dell'utente, come definito nell'oggetto utente di Azure AD. <br><br> **Valore SAML di esempio**: <br> `<Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>`<br>`<AttributeValue>Miller<AttributeValue>` <br><br> **Valore token JWT di esempio**: <br> `"family_name": "Miller"` |
| `unique_name` |Nome |Fornisce un valore leggibile che identifica l'oggetto del token. Questo valore potrebbe non essere univoco all'interno di un tenant e può essere usato solo per scopi di visualizzazione. <br><br> **Valore SAML di esempio**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>` <br><br> **Valore token JWT di esempio**: <br> `"unique_name": "frankm@contoso.com"` |
| `oid` |ID oggetto |Contiene un identificatore univoco di un oggetto in Azure AD. Questo valore non è modificabile e non può essere riassegnato o riutilizzato. Usare l'ID oggetto per identificare un oggetto nelle query ad Azure AD. <br><br> **Valore SAML di esempio**: <br> `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` <br><br> **Valore token JWT di esempio**: <br> `"oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"` |
| `roles` |Ruoli |Rappresenta tutti i ruoli applicazione concessi al soggetto sia direttamente che indirettamente tramite l'appartenenza a gruppi e può essere usata per imporre il controllo degli accessi in base al ruolo. I ruoli applicazione sono definiti in base all'applicazione, tramite la proprietà `appRoles` del manifesto dell'applicazione. La proprietà `value` di ogni ruolo applicazione è il valore visualizzato nell'attestazione Ruoli. <br><br> **Valore SAML di esempio**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`<br>`<AttributeValue>Admin</AttributeValue>` <br><br> **Valore token JWT di esempio**: <br> `“roles”: ["Admin", … ]` |
| `scp` |Scope |Indica le autorizzazioni di rappresentazione concesse all'applicazione client. L'autorizzazione predefinita è `user_impersonation`. Il proprietario della risorsa protetta può registrare valori aggiuntivi in Azure AD. <br><br> **Valore token JWT di esempio**: <br> `"scp": "user_impersonation"` |
| `sub` |Subject |Identifica l'entità su cui il token asserisce informazioni, ad esempio l'utente di un'applicazione. Questo valore non è modificabile e non può essere riassegnato o riutilizzato, è quindi possibile usarlo per eseguire controlli di autorizzazione in modo sicuro. Dato che il soggetto è sempre presente nei token rilasciati da Azure AD, è consigliabile l'uso di questo valore in un sistema di autorizzazione per utilizzo generico. <br> `SubjectConfirmation` non è un'attestazione. Descrive la modalità di verifica del soggetto del token. `Bearer` indica che il soggetto viene confermato dal possesso del token. <br><br> **Valore SAML di esempio**: <br> `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>` <br><br> **Valore token JWT di esempio**: <br> `"sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"` |
| `tid` |ID tenant |Identificatore non modificabile e non riutilizzabile che identifica il tenant della directory che ha rilasciato il token. È possibile usare questo valore per accedere alle risorse di directory specifiche del tenant in un'applicazione multi-tenant. Ad esempio, è possibile usare questo valore per identificare il tenant in una chiamata all'API Graph. <br><br> **Valore SAML di esempio**: <br> `<Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>` <br><br> **Valore token JWT di esempio**: <br> `"tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"` |
| `nbf`, `exp` |Durata del token |Definisce l'intervallo di tempo entro il quale un token è valido. Il servizio che convalida il token deve verificare che la data corrente sia compresa nella durata del token. In caso contrario, deve rifiutare il token. La tolleranza del servizio è fino a cinque minuti oltre l'intervallo della durata del token per tener conto di eventuali differenze di orario ("mancata sincronizzazione dell'ora") tra Azure AD e il servizio. <br><br> **Valore SAML di esempio**: <br> `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br><br> **Valore token JWT di esempio**: <br> `"nbf":1363289634, "exp":1363293234` |
| `upn` |Nome dell'entità utente |Archivia il nome utente dell'entità utente.<br><br> **Valore token JWT di esempio**: <br> `"upn": frankm@contoso.com` |
| `ver` |Versione |Archivia il numero di versione del token. <br><br> **Valore token JWT di esempio**: <br> `"ver": "1.0"` |

## <a name="access-tokens"></a>Token di accesso

Se l'app *usa* i token di accesso solo per ottenere l'accesso alle API, è possibile (e consigliabile) trattare i token di accesso come se fossero completamente opachi, dal momento che sono semplicemente stringhe che l'app può trasmettere alle risorse nelle richieste HTTP.

Quando si richiede un token di accesso, Azure AD restituisce anche alcuni metadati relativi al token di accesso per l'utilizzo dell'app.  Queste informazioni includono l'ora di scadenza del token di accesso e gli ambiti per i quali è valido.  In questo modo l'app può eseguire operazioni di memorizzazione intelligente dei token di accesso senza la necessità di analizzare il token di accesso stesso.

Se l'app è un'API protetta con Azure AD che prevede token di accesso nelle richieste HTTP, è necessario eseguire la convalida e l'ispezione dei token ricevuti. Per informazioni dettagliate su come eseguire questa operazione con .NET, vedere [Proteggere un'API Web usando token di connessione di Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

## <a name="refresh-tokens"></a>Token di aggiornamento

I token di aggiornamento sono token di sicurezza che l'app può usare per acquisire nuovi token di accesso in un flusso di OAuth 2.0.  Consentono all'app di ottenere l'accesso a lungo termine alle risorse per conto dell'utente senza l'interazione dell'utente.

I token di aggiornamento sono multirisorsa, ovvero possono essere ricevuti durante una richiesta di token per una risorsa e riscattati per i token di accesso che fanno riferimento a una risorsa completamente diversa. Per specificare più risorse, impostare il parametro `resource` nella richiesta sulla risorsa di destinazione.

I token di aggiornamento sono completamente opachi per l'app. Hanno una durata elevata, ma l'app non deve essere scritta aspettandosi che un token di aggiornamento duri per un periodo indefinito.  I token di aggiornamento possono essere annullati in qualsiasi momento per vari motivi.  L'unico modo per l'app di sapere se un token di aggiornamento è valido è provare a riscattarlo inviando una richiesta di token all'endpoint di token di Azure AD.

Quando si riscattano i token di aggiornamento per un nuovo token di accesso, viene visualizzato un nuovo token di aggiornamento nella risposta del token.  È consigliabile salvare il token di aggiornamento appena generato sostituendo quello usato nella richiesta.  In questo modo, il token di aggiornamento rimarrà valido più a lungo possibile.

## <a name="validating-tokens"></a>Convalida dei token

Per convalidare un id_token o un access_token, l'app deve convalidarne la firma e le attestazioni.

Sono disponibili librerie ed esempi di codice che illustrano come gestire facilmente la convalida dei token per comprenderne il processo sottostante.  Sono disponibili anche numerose librerie open source di terze parti per la convalida dei token JWT. Esiste almeno un'opzione per ogni piattaforma e linguaggio disponibili. Per altre informazioni sulle librerie di autenticazione di Azure AD e per ottenere esempi di codice, vedere [Azure Active Directory Authentication Library](active-directory-authentication-libraries.md).

#### <a name="validating-the-signature"></a>Convalida della firma

Un token JWT contiene tre segmenti separati dal carattere `.` .  Il primo segmento è noto come **intestazione**, il secondo come **corpo** e il terzo come **firma**.  Il segmento di firma può essere usato per convalidare l'autenticità del token, in modo che possa essere considerato attendibile dall'app.

I token emessi da Azure AD vengono firmati usando algoritmi di crittografia asimmetrica standard del settore, come RSA 256. L'intestazione del token JWT contiene informazioni sulla chiave e sul metodo di crittografia usati per firmare il token:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "kriMPdmBvx68skT8-mPAB3BseeA"
}
```

L'attestazione `alg` indica l'algoritmo usato per firmare il token, mentre l'attestazione `x5t` indica la chiave pubblica specifica usata per firmare il token.

In qualsiasi momento, Azure AD può firmare un token ID usando un determinato set di coppie di chiavi pubbliche/private. Azure AD ruota il set di chiavi su base periodica, quindi l'app deve essere scritta in modo da gestire automaticamente le modifiche delle chiavi.  Una frequenza ragionevole per la ricerca di aggiornamenti per le chiavi pubbliche usate da Azure AD è di circa 24 ore.

È possibile acquisire i dati della chiave per la firma necessari per convalidare la firma usando il documento dei metadati OpenID Connect disponibile all'indirizzo:

```
https://login.microsoftonline.com/common/.well-known/openid-configuration
```

> [!TIP]
> Provare questo URL in un browser.
> 
> 

Il documento dei metadati è un oggetto JSON contenente diverse informazioni utili, ad esempio la posizione dei vari endpoint necessari per eseguire l'autenticazione di OpenID Connect.  

Include inoltre un oggetto `jwks_uri`che fornisce la posizione del set di chiavi pubbliche usate per firmare i token.  Il documento JSON disponibile in `jwks_uri` contiene tutte le informazioni sulla chiave pubblica usata in un determinato momento.  L'app può usare l'attestazione `kid` nell'intestazione JWT per selezionare quale chiave pubblica è stata usata in questo documento per firmare un determinato token.  Può quindi eseguire la convalida della firma usando la chiave pubblica corretta e l'algoritmo indicato.

L'esecuzione della convalida della firma non rientra nell'ambito di questo documento, sono tuttavia disponibili numerose librerie open source che contengono informazioni per eseguire questa operazione.

#### <a name="validating-the-claims"></a>Convalida delle attestazioni

Quando l'applicazione riceve un token (id_token all'accesso dell'utente o un token di accesso come token di connessione nella richiesta HTTP) è necessario eseguire anche alcuni controlli nelle attestazioni nel token.  Sono incluse, ad esempio:

* Attestazione **Audience**: per verificare che il token fosse destinato all'app.
* Attestazioni **Not Before** e **Expiration Time**: per verificare che il token non sia scaduto.
* Attestazione **Issuer** : per verificare che il token sia stato effettivamente rilasciato all'app da Azure AD.
* Attestazione **Nonce**:per mitigare attacchi di riproduzione del token.
* E altro ancora...

Per un elenco completo delle convalide di attestazione che l'app deve eseguire per i token ID, vedere le [specifiche di OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation). Per informazioni dettagliate sui valori previsti per tali attestazioni, vedere la [sezione Token ID](#id-tokens) precedente.

## <a name="sample-tokens"></a>Token di esempio

Questa sezione consente di visualizzare esempi di token SAML e JWT restituiti da Azure AD. Questi esempi consentono di vedere le attestazioni nel contesto.

### <a name="saml-token"></a>Token SAML

Questo è un esempio di un tipico token SAML.

    <?xml version="1.0" encoding="UTF-8"?>
    <t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
      <t:Lifetime>
        <wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
      </t:Lifetime>
      <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
          <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
      </wsp:AppliesTo>
      <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
          <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
          <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
            <ds:SignedInfo>
              <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
              <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
              <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                <ds:Transforms>
                  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
                <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
              </ds:Reference>
            </ds:SignedInfo>
            <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
            <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
              <X509Data>
                <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
              </X509Data>
            </KeyInfo>
          </ds:Signature>
          <Subject>
            <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
            <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
          </Subject>
          <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
            <AudienceRestriction>
              <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
            </AudienceRestriction>
          </Conditions>
          <AttributeStatement>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
              <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
              <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
              <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
              <AttributeValue>Admin</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
              <AttributeValue>Sample</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
              <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
              <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
              <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
              <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
              <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
              <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
              <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
              <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
              <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
              <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
              <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
              <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
              <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
              <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
            </Attribute>
          </AttributeStatement>
          <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
            <AuthnContext>
              <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
            </AuthnContext>
          </AuthnStatement>
        </Assertion>
      </t:RequestedSecurityToken>
      <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedAttachedReference>
      <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedUnattachedReference>
      <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
      <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
      <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
    </t:RequestSecurityTokenResponse>

### <a name="jwt-token---user-impersonation"></a>Token JWT: rappresentazione dell'utente
Questo è un esempio di un tipico token JSON Web (JWT) usato in un flusso di concessione del codice di autorizzazione.
Oltre alle attestazioni, il token include un numero di versione **ver** e **appidacr**, il riferimento alla classe contesto di autenticazione, che indica la modalità di autenticazione client. Per un client pubblico, il valore è 0. Se vengono usati l'ID client o il segreto client, il valore è 1.

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onmicrosoft.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onmicrosoft.com",
     unique_name: "sample.user@contoso.onmicrosoft.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

## <a name="related-content"></a>Contenuti correlati
* Per altre informazioni sulla gestione dei criteri di durata dei token tramite l'API Graph di Azure AD, vedere gli articoli relativi alle [operazioni sui criteri](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) e all'[entità dei criteri](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity) di Azure AD Graph.
* Per altre informazioni ed esempi sulla gestione dei criteri tramite i cmdlet PowerShell, vedere [Configurable token lifetimes in Azure AD](../active-directory-configurable-token-lifetimes.md) (Durata dei token configurabile in Azure AD). 




<!--HONumber=Jan17_HO4-->


