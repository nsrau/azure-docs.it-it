---
title: Informazioni sul manifesto dell'app Azure Active Directory | Microsoft Docs
description: Descrizione dettagliata del manifesto dell'app Azure Active Directory, che rappresenta una configurazione dell'identità dell'applicazione in un tenant di Azure AD e che viene usato per facilitare l'autorizzazione OAuth, per l'esperienza di consenso e altro ancora.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: a12715ba9aac77461d4968bd9b8f3de30af243c4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262750"
---
# <a name="azure-active-directory-app-manifest"></a>Manifesto dell'app Azure Active Directory

Il manifesto dell'applicazione contiene una definizione di tutti gli attributi di un oggetto applicazione in Microsoft Identity Platform. Funge inoltre da meccanismo per laggiornamento dell'oggetto applicazione. Per ulteriori informazioni sull'entità applicazione e sul relativo schema, vedere la [documentazione relativa all'entità dell'applicazione API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity).

È possibile configurare gli attributi di un'app tramite il portale di Azure o a livello di codice usando l' [API REST](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) o [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#applications). Tuttavia, esistono alcuni scenari in cui è necessario modificare il manifesto dell'applicazione per configurare l'attributo di un'applicazione. Tali scenari includono:

* Se l'app è stata registrata come Azure AD account Microsoft multi-tenant e personali, non è possibile modificare gli account Microsoft supportati nell'interfaccia utente. Per modificare i tipi di account supportati è necessario usare l'editor del manifesto dell'applicazione.
* Se è necessario definire le autorizzazioni e i ruoli supportati dall'applicazione, è necessario modificare il manifesto dell'applicazione.

## <a name="configure-the-app-manifest"></a>Configurare il manifesto dell'applicazione

Per configurare il manifesto dell'applicazione:

1. Accedere al [portale di Azure](https://portal.azure.com). Cercare e selezionare il servizio **Azure Active Directory** .
1. Selezionare **Registrazioni per l'app**.
1. Selezionare l'applicazione da configurare.
1. Nella pagina **Panoramica** dell'app selezionare la sezione **Manifesto**. Si apre un editor di manifesto basato sul Web che consente di modificare il manifesto all'interno del portale. Facoltativamente è possibile selezionare **Scarica**, modificare il manifesto in locale e quindi usare **Carica** per riapplicarlo all'applicazione.

## <a name="manifest-reference"></a>Riferimento del manifesto


### <a name="key-value-type-accesstokenacceptedversion-nullable-int32"></a>Chiave, tipo di valore: `accessTokenAcceptedVersion`, nullable Int32 
Specifica la versione del token di accesso prevista dalla risorsa. Questo parametro modifica la versione e il formato del JWT prodotto indipendente dall'endpoint o dal client usato per richiedere il token di accesso.

L'endpoint usato, v1.0 o v2.0, viene scelto dal client e influisce solo sulla versione di id_tokens. Le risorse devono configurare in modo esplicito `accesstokenAcceptedVersion` per indicare il formato di token di accesso supportato.

I valori possibili per `accesstokenAcceptedVersion` sono 1, 2 o Null. Se il valore è null, il valore predefinito di questo parametro è 1, che corrisponde all'endpoint v 1.0. 

Se `signInAudience` è `AzureADandPersonalMicrosoftAccount`, il valore deve essere `2`  

Valore di esempio: `2` 

### <a name="key-value-type-addins-collection"></a>Chiave, tipo valore: `addIns`, raccolta 
Definisce il comportamento personalizzato che un servizio consumer può usare per chiamare un'app in contesti specifici. Ad esempio, le applicazioni in grado di eseguire il rendering di flussi di file possono impostare la proprietà addIns per la relativa funzionalità "FileHandler". Questo parametro consente ai servizi come Office 365 di chiamare l'applicazione nel contesto di un documento su cui l'utente sta lavorando. 

Valore di esempio: 
<code>{<br>&nbsp;&nbsp;&nbsp;"id":"968A844F-7A47-430C-9163-07AE7C31D407"<br>&nbsp;&nbsp;&nbsp;"type": "FileHandler",<br>&nbsp;&nbsp;&nbsp;"properties": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{"key": "version", "value": "2" }<br>&nbsp;&nbsp;&nbsp;]<br>}</code>

### <a name="key-value-type-allowpublicclient-boolean"></a>Chiave, tipo di valore: `allowPublicClient`, booleano 
Specifica il tipo di applicazione di fallback. Azure AD deduce il tipo di applicazione da replyUrlsWithType per impostazione predefinita. Esistono alcuni scenari in cui Azure AD non è in grado di determinare il tipo di app client. Ad esempio, uno scenario di questo tipo è il flusso [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) in cui la richiesta HTTP avviene senza reindirizzamento URL. In questi casi, Azure AD interpreterà il tipo di applicazione in base al valore di questa proprietà. Se questo valore è impostato su true, il tipo di applicazione di fallback è impostato come client pubblico, ad esempio un'app installata in esecuzione in un dispositivo mobile. Il valore predefinito è false, che indica che il tipo di applicazione di fallback è un client riservato, ad esempio un'app Web. 

Valore di esempio: `false` 

### <a name="key-value-type-availabletoothertenants-boolean"></a>Chiave, tipo di valore: `availableToOtherTenants`, booleano 
true se l'applicazione è condivisa con altri tenant. in caso contrario, false. <br><br> Nota: questa funzionalità è disponibile solo nell'esperienza **registrazioni app (legacy)** . Sostituito da `signInAudience` nell'esperienza [registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) . 

### <a name="key-value-type-appid-string"></a>Chiave, tipo valore: `appId`, stringa 
Specifica l'identificatore univoco per l'app assegnato a un'app da Azure AD. 

Valore di esempio: `"601790de-b632-4f57-9523-ee7cb6ceba95"` 

### <a name="key-value-type-approles-collection"></a>Chiave, tipo valore: `appRoles`, raccolta 
Specifica la raccolta di ruoli che un'app può dichiarare. Questi ruoli possono essere assegnati a utenti, gruppi o entità servizio. Per altri esempi e informazioni, vedere [Aggiungere ruoli dell'app in un'applicazione e riceverli nel token](howto-add-app-roles-in-azure-ad-apps.md) 

Valore di esempio: 
<code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  

### <a name="key-value-type-displayname-string"></a>Chiave, tipo valore: `displayName`, stringa 
Nome visualizzato dell'app. <br><br> Nota: questa funzionalità è disponibile solo nell'esperienza **registrazioni app (legacy)** . Sostituito da `name` nell'esperienza [registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) . 

Valore di esempio: `"MyRegisteredApp"` 

### <a name="key-value-type-errorurl-string"></a>Chiave, tipo valore: `errorUrl`, stringa 
Non supportato. 

### <a name="key-value-type-groupmembershipclaims-string"></a>Chiave, tipo valore: `groupMembershipClaims`, stringa 
Configura l'attestazione `groups` rilasciata in un token di accesso utente oppure OAuth 2.0 previsto dall'app. Per impostare questo attributo, usare uno dei seguenti valori di stringa validi:
- `"None"`
- `"SecurityGroup"` (per i gruppi di sicurezza e i ruoli di Azure AD)
- `"All"` (ottiene tutti i gruppi di sicurezza, i gruppi di distribuzione e Azure AD ruoli della directory di cui l'utente connesso è membro. 

Valore di esempio: `"SecurityGroup"` 

### <a name="key-value-type-homepage-string"></a>Chiave, tipo valore: `homepage`, stringa 
URL della Home page dell'applicazione. <br><br> Nota: questa funzionalità è disponibile solo nell'esperienza **registrazioni app (legacy)** . Sostituito da `signInUrl` nell'esperienza [registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) . 

Valore di esempio: `"https://MyRegisteredApp"` 

### <a name="key-value-type-objectid-string"></a>Chiave, tipo valore: `objectId`, stringa 
Identificatore univoco per l'app nella directory. <br><br> Questa funzionalità è disponibile solo nell'esperienza **registrazioni app (legacy)** . Sostituito da `id` nell'esperienza [registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) . 

Valore di esempio: `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` 

### <a name="key-value-type-optionalclaims-string"></a>Chiave, tipo valore: `optionalClaims`, stringa 
Attestazioni facoltative restituite nel token dal servizio token di sicurezza per questa specifica app.<br>A questo punto, le app che supportano sia account personali che account Azure AD (registrati tramite il portale di registrazione delle app) non possono usare le attestazioni facoltative. Tuttavia, le app registrate solo per Azure AD usando l'endpoint v2.0 possono ottenere le attestazioni facoltative richieste nel manifesto. Per altre informazioni, vedere l'articolo relativo alle [attestazioni facoltative](active-directory-optional-claims.md). 

Valore di esempio:  
`null` 

### <a name="key-value-type-id-string"></a>Chiave, tipo valore: `id`, stringa 
Identificatore univoco per l'app nella directory. Questo ID non è l'identificatore usato per identificare l'app nelle transazioni di protocollo. Viene usato per fare riferimento all'oggetto nelle query di directory. 

Valore di esempio: `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` 

### <a name="key-value-type-identifieruris-string-array"></a>Chiave, tipo di valore: `identifierUris`, matrice di stringhe 
URI definiti dall'utente che identificano in modo univoco un'app Web entro il rispettivo tenant di Azure AD oppure entro un dominio personalizzato verificato se l'app è multi-tenant. 

Valore di esempio: 
<code>[<br>&nbsp;&nbsp;"https://MyRegisteredApp"<br>]</code> 

### <a name="key-value-type-informationalurls-string"></a>Chiave, tipo valore: `informationalUrls`, stringa 
Specifica i collegamenti alle condizioni d'uso del servizio e all'informativa sulla privacy dell'app. Le condizioni per l'utilizzo del servizio e l'informativa sulla privacy vengono presentate agli utenti tramite l'esperienza di consenso dell'utente Per altre informazioni, vedere l'articolo [Condizioni per l'utilizzo del servizio e informativa sulla privacy per le app di Azure Active Directory registrate](howto-add-terms-of-service-privacy-statement.md). 

Valore di esempio: 
<code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> 

### <a name="key-value-type-keycredentials-collection"></a>Chiave, tipo valore: `keyCredentials`, raccolta 
Contiene riferimenti a credenziali assegnate dall'app, segreti condivisi basati su stringa e certificati X.509. ). 

Valore di esempio: 
<code>[<br>&nbsp;{<br>&nbsp;&These credentials are used when requesting access tokens (when the app is acting as a client rather that as resourcenbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> 

### <a name="key-value-type-knownclientapplications-string-array"></a>Chiave, tipo di valore: `knownClientApplications`, matrice di stringhe 
Viene usato per unire il consenso se una soluzione contiene due parti, un'app client e un'app API Web personalizzata. Se si immette l'appID dell'app client in questo valore, l'utente deve fornire il consenso solo una volta per l'app client. Azure AD saprà che il consenso al client implica il consenso implicito all'API Web. Eseguirà automaticamente il provisioning delle entità servizio per il client e l'API Web nello stesso momento. Sia il client sia l'app dell'API Web devono essere registrati nello stesso tenant. 

Valore di esempio: `["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"]` 

### <a name="key-value-type-logourl-string"></a>Chiave, tipo valore: `logoUrl`, stringa 
Valore di sola lettura che punta all'URL della rete CDN del logo caricato nel portale. 

Valore di esempio: `"https://MyRegisteredAppLogo"` 

### <a name="key-value-type-logouturl-string"></a>Chiave, tipo valore: `logoutUrl`, stringa 
URL per disconnettersi dall'app. 

Valore di esempio:  
`"https://MyRegisteredAppLogout"` 

### <a name="key-value-type-name-string"></a>Chiave, tipo valore: `name`, stringa 
Nome visualizzato dell'app. 

Valore di esempio: `"MyRegisteredApp"` 

### <a name="key-value-type-oauth2allowimplicitflow-boolean"></a>Chiave, tipo di valore: `oauth2AllowImplicitFlow`, booleano 
Specifica se questa app Web può richiedere token di accesso di flusso implicito OAuth 2.0. Il valore predefinito è false. Questo flag viene usato per le app basate su browser, ad esempio le app JavaScript a pagina singola. Per altre informazioni, immettere `OAuth 2.0 implicit grant flow` nel sommario e vedere gli argomenti sul flusso implicito. 

Valore di esempio: `false` 

### <a name="key-value-type-oauth2allowidtokenimplicitflow-boolean"></a>Chiave, tipo di valore: `oauth2AllowIdTokenImplicitFlow`, booleano 
Specifica se questa app Web può richiedere token ID flusso implicito OAuth 2.0. Il valore predefinito è false. Questo flag viene usato per le app basate su browser, ad esempio le app JavaScript a pagina singola. 

Valore di esempio: `false` 

### <a name="key-value-type-oauth2permissions-collection"></a>Chiave, tipo valore: `oauth2Permissions`, raccolta 
Specifica la raccolta di ambiti di autorizzazione OAuth 2.0 che l'app dell'API Web (risorsa) espone alle app client. Questi ambiti di autorizzazione possono essere concessi alle app client durante il consenso. 

Valore di esempio: 
<code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>] </code>

### <a name="key-value-type-oauth2requiredpostresponse-boolean"></a>Chiave, tipo di valore: `oauth2RequiredPostResponse`, booleano 
Specifica se, come parte delle richieste dei token OAuth 2.0, Azure AD consente richieste POST, anziché richieste GET. L'impostazione predefinita è false, che specifica che sono consentite solo richieste GET. 

Valore di esempio: `false` 

### <a name="key-value-type-parentalcontrolsettings-string"></a>Chiave, tipo valore: `parentalControlSettings`, stringa 

`countriesBlockedForMinors` specifica i paesi in cui l'app è bloccata per i minori.<br>`legalAgeGroupRule` specifica la regola gruppo relativa all'età legale che si applica agli utenti dell'app. Può essere impostata su `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids` o `BlockMinors`.  

Valore di esempio: 
<code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> 

### <a name="key-value-type-passwordcredentials-collection"></a>Chiave, tipo valore: `passwordCredentials`, raccolta 
Vedere la descrizione per la proprietà `keyCredentials`. 

Valore di esempio: 
<code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> 

### <a name="key-value-type-preauthorizedapplications-collection"></a>Chiave, tipo valore: `preAuthorizedApplications`, raccolta 
Elenca le applicazioni e le autorizzazioni necessarie per il consenso implicito. Richiede che un amministratore abbia fornito il consenso all'applicazione. preAuthorizedApplications non richiede il consenso utente alle autorizzazioni richieste. Le autorizzazioni elencate in preAuthorizedApplications non richiedono il consenso utente. Tuttavia, le autorizzazioni richieste aggiuntive non elencate in preAuthorizedApplications richiedono il consenso utente. 

Valore di esempio: 
<code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",<br>&nbsp;&nbsp;&nbsp;&nbsp;"permissionIds": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"8748f7db-21fe-4c83-8ab5-53033933c8f1"<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> 

### <a name="key-value-type-publicclient-boolean"></a>Chiave, tipo di valore: `publicClient`, booleano 
Specifica se l'applicazione è un client pubblico (ad esempio, un'applicazione installata in esecuzione su un dispositivo mobile). 

Questa proprietà è disponibile solo nell'esperienza **registrazioni app (legacy)** . Sostituito da `allowPublicClient` nell'esperienza [registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) . 

### <a name="key-value-type-publisherdomain-string"></a>Chiave, tipo valore: `publisherDomain`, stringa 
Dominio del server di pubblicazione verificato per l'applicazione. Di sola lettura. 

Valore di esempio: `https://www.contoso.com`

### <a name="key-value-type-replyurls-string-array"></a>Chiave, tipo di valore: `replyUrls`, matrice di stringhe 
Questa proprietà multivalore contiene l'elenco dei valori redirect_uri registrati che Azure AD accetta come destinazioni in fase di restituzione dei token. <br><br> Questa proprietà è disponibile solo nell'esperienza **registrazioni app (legacy)** . Sostituito da `replyUrlsWithType` nell'esperienza [registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) . 

### <a name="key-value-type-replyurlswithtype-collection"></a>Chiave, tipo valore: `replyUrlsWithType`, raccolta 
Questa proprietà multivalore contiene l'elenco dei valori redirect_uri registrati che Azure AD accetta come destinazioni in fase di restituzione dei token. Ogni valore URI deve contenere un valore del tipo di app associato. I valori di tipo supportati sono: <ul><li>`Web`</li><li>`InstalledClient`</li></ul><br> Altre informazioni sulle [restrizioni e le limitazioni di replyUrl](https://docs.microsoft.com/azure/active-directory/develop/reply-url). 

Valore di esempio: 
<code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> 

### <a name="key-value-type-requiredresourceaccess-collection"></a>Chiave, tipo valore: `requiredResourceAccess`, raccolta 
Con il consenso dinamico, `requiredResourceAccess` attiva l'esperienza di consenso dell'amministratore e l'esperienza di consenso dell'utente per gli utenti che usano il consenso statico. Tuttavia, questo parametro non guida l'esperienza di consenso dell'utente per il caso generale.<br>`resourceAppId` è l'identificatore univoco per la risorsa a cui l'app richiede l'accesso. Questo valore deve essere uguale all'appId dichiarato nell'app della risorsa di destinazione.<br>`resourceAccess` è una matrice che contiene l'elenco dei ruoli delle app e degli ambiti delle autorizzazioni OAuth 2.0 che l'app richiede dalla risorsa specificata. Contiene i valori di tipo `id` e `type` per le risorse specificate. 

Valore di esempio: 
<code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>] </code> 

### <a name="key-value-type-samlmetadataurl-string"></a>Chiave, tipo valore: `samlMetadataUrl`, stringa 
URL dei metadati SAML per l'app. 

Valore di esempio: `https://MyRegisteredAppSAMLMetadata` 

### <a name="key-value-type-signinurl-string"></a>Chiave, tipo valore: `signInUrl`, stringa 

Specifica l'URL della home page dell'app. 

Valore di esempio: `https://MyRegisteredApp` 

### <a name="key-value-type-signinaudience-string"></a>Chiave, tipo valore: `signInAudience`, stringa 
Specifica gli account Microsoft supportati per l'applicazione corrente. I valori supportati sono:
- **AzureADMyOrg** -utenti con un account Microsoft aziendale o dell'Istituto di istruzione nel tenant Azure ad dell'organizzazione (ad esempio, singolo tenant)
- **AzureADMultipleOrgs** -utenti con un account Microsoft aziendale o dell'Istituto di istruzione nel tenant Azure ad di qualsiasi organizzazione (ad esempio multi-tenant)
- **AzureADandPersonalMicrosoftAccount** -utenti con un account Microsoft personale o un account aziendale o dell'Istituto di istruzione nel tenant Azure ad di qualsiasi organizzazione 

Valore di esempio:  
`AzureADandPersonalMicrosoftAccount` 

### <a name="key-value-type-tags-string-array"></a>Chiave, tipo di valore: `tags`, matrice di stringhe 
Stringhe personalizzate che possono essere usate per classificare e identificare l'applicazione. 

Valore di esempio: 
<code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code>

## <a name="common-issues"></a>Problemi comuni

### <a name="manifest-limits"></a>Limiti del manifesto

Un manifesto dell'applicazione ha più attributi detti raccolte. ad esempio, approles, le credenziali di della knownclientapplications, identifierUris, rediretUris, requiredResourceAccess e oauth2Permissions. All'interno del manifesto dell'applicazione completo per qualsiasi applicazione, il numero totale di voci in tutte le raccolte combinate è stato limitato a 1200. Se in precedenza si specificano gli URI di reindirizzamento 100 nel manifesto dell'applicazione, si rimarranno solo 1100 le voci rimanenti da usare in tutte le altre raccolte combinate che compongono il manifesto.

> [!NOTE]
> Se si tenta di aggiungere più di 1200 voci nel manifesto dell'applicazione, è possibile che venga visualizzato un errore **"Impossibile aggiornare l'applicazione xxxxxx. Dettagli errore: le dimensioni del manifesto hanno superato il limite. Ridurre il numero di valori e ripetere la richiesta ".**

### <a name="unsupported-attributes"></a>Attributi non supportati

Il manifesto dell'applicazione rappresenta lo schema del modello di applicazione sottostante in Azure AD. Con l'evolversi dello schema sottostante, l'editor del manifesto verrà aggiornato per riflettere il nuovo schema di tanto in tanto. Di conseguenza, è possibile notare che i nuovi attributi vengono visualizzati nel manifesto dell'applicazione. In rare occasioni, è possibile notare una modifica sintattica o semantica negli attributi esistenti oppure è possibile trovare un attributo che precedentemente non è più supportato. Ad esempio, nella [registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908)verranno visualizzati nuovi attributi, noti con un nome diverso nell'esperienza registrazioni app (legacy).

| Registrazioni app (legacy)| Registrazioni per l'app           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Per le descrizioni di questi attributi, vedere la sezione [riferimento al manifesto](#manifest-reference) .

Quando si tenta di caricare un manifesto scaricato in precedenza, è possibile che venga visualizzato uno degli errori seguenti. Questo errore è probabilmente dovuto al fatto che l'editor del manifesto supporta ora una versione più recente dello schema, che non corrisponde a quello che si sta provando a caricare.

* "Non è stato possibile aggiornare l'applicazione xxxxxx. Dettagli errore: identificatore di oggetto non valido ' undefined '. []."
* "Non è stato possibile aggiornare l'applicazione xxxxxx. Dettagli errore: uno o più valori di proprietà specificati non sono validi. []."
* "Non è stato possibile aggiornare l'applicazione xxxxxx. Dettagli errore: non è consentito impostare availableToOtherTenants in questa versione dell'API per l'aggiornamento. []."
* "Non è stato possibile aggiornare l'applicazione xxxxxx. Dettagli errore: gli aggiornamenti della proprietà' replyUrls ' non sono consentiti per questa applicazione. Usare invece la proprietà' replyUrlsWithType '. []."
* "Non è stato possibile aggiornare l'applicazione xxxxxx. Dettagli errore: è stato trovato un valore senza un nome di tipo e non è disponibile alcun tipo previsto. Quando il modello viene specificato, ogni valore nel payload deve avere un tipo che può essere specificato nel payload, in modo esplicito dal chiamante o dedotto in modo implicito dal valore padre. []"

Quando viene visualizzato uno di questi errori, è consigliabile eseguire le operazioni seguenti:

1. Modificare gli attributi singolarmente nell'editor del manifesto anziché caricare un manifesto precedentemente scaricato. Usare la tabella di [riferimento del manifesto](#manifest-reference) per comprendere la sintassi e la semantica degli attributi vecchi e nuovi, in modo da poter modificare correttamente gli attributi a cui si è interessati. 
1. Se il flusso di lavoro richiede di salvare i manifesti nel repository di origine per usarli in seguito, è consigliabile ribasare i manifesti salvati nel repository con quello visualizzato nell'esperienza **registrazioni app** .

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla relazione tra gli oggetti applicazione e gli oggetti entità servizio di un'applicazione, vedere [oggetti applicazione e oggetti entità servizio in Azure ad](app-objects-and-service-principals.md).
* Per le definizioni di alcuni concetti fondamentali per gli sviluppatori della piattaforma Microsoft Identity, vedere il [Glossario per sviluppatori Microsoft Identity Platform](developer-glossary.md) .

Usare la sezione dei commenti seguente per offrire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
