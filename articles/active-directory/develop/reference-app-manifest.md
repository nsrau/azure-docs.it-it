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
ms.date: 03/23/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: 6d9a4af5ee814282589959fcf840c1061358ca18
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80383940"
---
# <a name="azure-active-directory-app-manifest"></a>Manifesto dell'app Azure Active Directory

Il manifesto dell'applicazione contiene una definizione di tutti gli attributi di un oggetto applicazione in Microsoft Identity Platform. Funge inoltre da meccanismo per laggiornamento dell'oggetto applicazione. Per altre info sull'entità Application e sul relativo schema, vedi la [documentazione dell'entità Applicazione API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity).

È possibile configurare gli attributi di un'app tramite il portale di Azure o a livello di codice usando [l'API REST](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) o [PowerShell.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#applications) Tuttavia, esistono alcuni scenari in cui è necessario modificare il manifesto dell'applicazione per configurare l'attributo di un'applicazione. Tali scenari includono:

* Se l'app è stata registrata come account Microsoft personale e multi-tenant di Azure AD, non è possibile modificare gli account Microsoft supportati nell'interfaccia utente. Per modificare i tipi di account supportati è necessario usare l'editor del manifesto dell'applicazione.
* Se è necessario definire le autorizzazioni e i ruoli supportati dall'applicazione, è necessario modificare il manifesto dell'applicazione.

## <a name="configure-the-app-manifest"></a>Configurare il manifesto dell'applicazione

Per configurare il manifesto dell'applicazione:

1. Passare al [portale di Azure](https://portal.azure.com). Cercare e selezionare il servizio **Azure Active Directory.**
1. Selezionare **Registrazioni app**.
1. Selezionare l'applicazione da configurare.
1. Nella pagina **Panoramica** dell'app selezionare la sezione **Manifesto**. Si apre un editor di manifesto basato sul Web che consente di modificare il manifesto all'interno del portale. Facoltativamente è possibile selezionare **Scarica**, modificare il manifesto in locale e quindi usare **Carica** per riapplicarlo all'applicazione.

## <a name="manifest-reference"></a>Riferimento del manifesto

In questa sezione vengono descritti gli attributi presenti nel manifesto dell'applicazione.

### <a name="accesstokenacceptedversion-attribute"></a>accessTokenAcceptedVersion (attributo)

| Chiave | Tipo di valore |
| :--- | :--- |
| accessTokenAcceptedVersion | Nullable Int32 |

Specifica la versione del token di accesso prevista dalla risorsa. Questo parametro modifica la versione e il formato del token JWT prodotto indipendentemente dall'endpoint o dal client utilizzato per richiedere il token di accesso.

L'endpoint usato, v1.0 o v2.0, viene scelto dal client e influisce solo sulla versione di id_tokens. Le risorse devono configurare in modo esplicito `accesstokenAcceptedVersion` per indicare il formato di token di accesso supportato.

I valori possibili per `accesstokenAcceptedVersion` sono 1, 2 o Null. Se il valore è null, il valore predefinito di questo parametro è 1, che corrisponde all'endpoint v1.0.

Se `signInAudience` `AzureADandPersonalMicrosoftAccount`è , il `2`valore deve essere .

Esempio:

```json
    "accessTokenAcceptedVersion": 2,
```

### <a name="addins-attribute"></a>addIns (attributo)

| Chiave | Tipo di valore |
| :--- | :--- |
| addIns | Raccolta |

Definisce il comportamento personalizzato che un servizio può usare per chiamare un'app in contesti specifici. Ad esempio, le applicazioni che possono `addIns` eseguire il rendering dei flussi di file possono impostare la proprietà per la relativa funzionalità "FileHandler". Questo parametro consentirà a servizi come Office 365 di chiamare l'applicazione nel contesto di un documento su cui l'utente sta lavorando.

Esempio:

```json
    "addIns": [
       {
        "id": "968A844F-7A47-430C-9163-07AE7C31D407",
        "type":" FileHandler",
        "properties": [
           {
              "key": "version",
              "value": "2"
           }
        ]
       }
    ],
```

### <a name="allowpublicclient-attribute"></a>allowPublicClient (attributo)

| Chiave | Tipo di valore |
| :--- | :--- |
| allowPublicClient (informazioni in stato instato) | Boolean |

Specifica il tipo di applicazione di fallback. Azure AD deduce il tipo di applicazione da replyUrlsWithType per impostazione predefinita. Esistono alcuni scenari in cui Azure AD non è in grado di determinare il tipo di app client. Ad esempio, uno di questi scenari è il flusso [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) in cui la richiesta HTTP avviene senza un reindirizzamento URL). In questi casi, Azure AD interpreterà il tipo di applicazione in base al valore di questa proprietà. Se questo valore è impostato su true, il tipo di applicazione di fallback è impostato come client pubblico, ad esempio un'app installata in esecuzione in un dispositivo mobile. Il valore predefinito è false, che indica che il tipo di applicazione di fallback è un client riservato, ad esempio un'app Web.

Esempio:

```json
    "allowPublicClient": false,
```

### <a name="availabletoothertenants-attribute"></a>availableToOtherTenants (attributo)

| Chiave | Tipo di valore |
| :--- | :--- |
| availableToOtherTenants | Boolean |

Impostare su true se l'applicazione è condivisa con altri tenant; in caso contrario, false.

> [!NOTE]
> Questo attributo è disponibile solo nell'esperienza di **registrazione dell'app (legacy).** Sostituita `signInAudience` dall'esperienza di registrazione dell'App. [App registrations](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="appid-attribute"></a>attributo appId

| Chiave | Tipo di valore |
| :--- | :--- |
| appId | string |

Specifica l'identificatore univoco per l'app assegnato a un'app da Azure AD.

Esempio:

```json
    "appId": "601790de-b632-4f57-9523-ee7cb6ceba95",
```

### <a name="approles-attribute"></a>appRoles (attributo)

| Chiave | Tipo di valore |
| :--- | :--- |
| appRoles | Raccolta |

Specifica la raccolta di ruoli che un'app può dichiarare. Questi ruoli possono essere assegnati a utenti, gruppi o entità servizio. Per altri esempi e informazioni, vedere [Aggiungere ruoli dell'app nell'applicazione e riceverli nel token.](howto-add-app-roles-in-azure-ad-apps.md)

Esempio:

```json
    "appRoles": [
        {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "Read-only access to device information",
           "displayName": "Read Only",
           "id": "601790de-b632-4f57-9523-ee7cb6ceba95",
           "isEnabled": true,
           "value": "ReadOnly"
        }
    ],
```

### <a name="displayname-attribute"></a>displayName (attributo)

| Chiave | Tipo di valore |
| :--- | :--- |
| displayName | string |

Nome visualizzato dell'app.

> [!NOTE]
> Questo attributo è disponibile solo nell'esperienza di **registrazione dell'app (legacy).** Sostituita `name` dall'esperienza di registrazione dell'App. [App registrations](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="errorurl-attribute"></a>errorUrl (attributo)

| Chiave | Tipo di valore |
| :--- | :--- |
| errorUrl | string |

Non supportato.

### <a name="groupmembershipclaims-attribute"></a>Attributo groupMembershipClaims

| Chiave | Tipo di valore |
| :--- | :--- |
|groupMembershipClaims | string |

Configura l'attestazione `groups` rilasciata in un token di accesso utente oppure OAuth 2.0 previsto dall'app. Per impostare questo attributo, usare uno dei seguenti valori di stringa validi:

- `"None"`
- `"SecurityGroup"` (per gruppi di sicurezza e ruoli di Azure AD)
- `"All"` (verranno restituiti tutti i gruppi di sicurezza, i gruppi di distribuzione e i ruoli della directory di Azure AD a cui appartiene l'utente connesso.

Esempio:

```json
    "groupMembershipClaims": "SecurityGroup",
```

### <a name="homepage-attribute"></a>attributo homepage

| Chiave | Tipo di valore |
| :--- | :--- |
| homepage |string |

URL della home page dell'applicazione.

> [!NOTE]
> Questo attributo è disponibile solo nell'esperienza di **registrazione dell'app (legacy).** Sostituita `signInUrl` dall'esperienza di registrazione dell'App. [App registrations](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="objectid-attribute"></a>Attributo objectId

| Chiave | Tipo di valore |
| :--- | :--- |
|objectId | string |

Identificatore univoco per l'app nella directory.

Questa opzione è disponibile solo nell'esperienza di **registrazione dell'app (Legacy).** Sostituita `id` dall'esperienza di registrazione dell'App. [App registrations](https://go.microsoft.com/fwlink/?linkid=2083908)

Esempio:

```json
    "objectId": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="optionalclaims-attribute"></a>optionalClaims (attributo)

| Chiave | Tipo di valore |
| :--- | :--- |
| optionalClaims | string |

Attestazioni facoltative restituite nel token dal servizio token di sicurezza per questa specifica app.

A questo punto, le app che supportano sia account personali che account Azure AD (registrati tramite il portale di registrazione delle app) non possono usare le attestazioni facoltative. Tuttavia, le app registrate solo per Azure AD usando l'endpoint v2.0 possono ottenere le attestazioni facoltative richieste nel manifesto. Per altre info, vedi [Attestazioni facoltative](active-directory-optional-claims.md).

Esempio:

```json
    "optionalClaims": null,
```

### <a name="id-attribute"></a>id (attributo)

| Chiave | Tipo di valore |
| :--- | :--- |
| id | string |

Identificatore univoco per l'app nella directory. Questo ID non è l'identificatore usato per identificare l'app nelle transazioni di protocollo. Viene usato per fare riferimento all'oggetto nelle query di directory.

Esempio:

```json
    "id": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="identifieruris-attribute"></a>identifierUris (attributo)

| Chiave | Tipo di valore |
| :--- | :--- |
| identifierUris | String Array |

URI definiti dall'utente che identificano in modo univoco un'app Web entro il rispettivo tenant di Azure AD oppure entro un dominio personalizzato verificato se l'app è multi-tenant.

Esempio:

```json
    "identifierUris": "https://MyRegisteredApp",
```

### <a name="informationalurls-attribute"></a>attributo informationalUrls

| Chiave | Tipo di valore |
| :--- | :--- |
| informationalUrls | string |

Specifica i collegamenti alle condizioni d'uso del servizio e all'informativa sulla privacy dell'app. Le condizioni per l'utilizzo del servizio e l'informativa sulla privacy vengono presentate agli utenti tramite l'esperienza di consenso dell'utente Per altre informazioni, vedere l'articolo [Condizioni per l'utilizzo del servizio e informativa sulla privacy per le app di Azure Active Directory registrate](howto-add-terms-of-service-privacy-statement.md).

Esempio:

```json
    "informationalUrls": {
        "termsOfService": "https://MyRegisteredApp/termsofservice",
        "support": "https://MyRegisteredApp/support",
        "privacy": "https://MyRegisteredApp/privacystatement",
        "marketing": "https://MyRegisteredApp/marketing"
    },
```

### <a name="keycredentials-attribute"></a>keyCredentials (attributo)

| Chiave | Tipo di valore |
| :--- | :--- |
| keyCredentials | Raccolta |

Contiene riferimenti a credenziali assegnate dall'app, segreti condivisi basati su stringa e certificati X.509. Queste credenziali vengono usate quando si richiedono i token di accesso (quando l'app funge da client anziché da risorsa).

Esempio:

```json
    "keyCredentials": [
        {
           "customKeyIdentifier":null,
           "endDate":"2018-09-13T00:00:00Z",
           "keyId":"<guid>",
           "startDate":"2017-09-12T00:00:00Z",
           "type":"AsymmetricX509Cert",
           "usage":"Verify",
           "value":null
        }
    ],
```

### <a name="knownclientapplications-attribute"></a>attributo knownClientApplications

| Chiave | Tipo di valore |
| :--- | :--- |
| knownClientApplications | String Array |

Viene usato per unire il consenso se una soluzione contiene due parti, un'app client e un'app API Web personalizzata. Se si immette l'appID dell'app client in questo valore, l'utente deve fornire il consenso solo una volta per l'app client. Azure AD will know that consenting to the client means implicitly consenting to the web API. Il provisioning delle entità servizio verrà eseguito automaticamente per l'API client e Web contemporaneamente. Sia il client sia l'app dell'API Web devono essere registrati nello stesso tenant.

Esempio:

```json
    "knownClientApplications": ["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"],
```

### <a name="logourl-attribute"></a>logoUrl (attributo)

| Chiave | Tipo di valore |
| :--- | :--- |
| logoUrl (nome in logo) | string |

Valore di sola lettura che punta all'URL della rete CDN del logo caricato nel portale.

Esempio:

```json
    "logoUrl": "https://MyRegisteredAppLogo",
```

### <a name="logouturl-attribute"></a>attributo logoutUrl

| Chiave | Tipo di valore |
| :--- | :--- |
| logoutUrl | string |

URL per disconnettersi dall'app.

Esempio:

```json
    "logoutUrl": "https://MyRegisteredAppLogout",
```

### <a name="name-attribute"></a>name (attributo)

| Chiave | Tipo di valore |
| :--- | :--- |
| name | string |

Nome visualizzato dell'app.

Esempio:

```json
    "name": "MyRegisteredApp",
```

### <a name="oauth2allowimplicitflow-attribute"></a>attributo oauth2AllowImplicitFlow

| Chiave | Tipo di valore |
| :--- | :--- |
| oauth2AllowImplicitFlow | Boolean |

Specifica se questa app Web può richiedere token di accesso di flusso implicito OAuth 2.0. Il valore predefinito è false. Questo flag viene usato per le app basate su browser, ad esempio le app a pagina singola JavaScript. Per altre informazioni, immettere `OAuth 2.0 implicit grant flow` nel sommario e vedere gli argomenti sul flusso implicito.

Esempio:

```json
    "oauth2AllowImplicitFlow": false,
```

### <a name="oauth2allowidtokenimplicitflow-attribute"></a>attributo oauth2AllowIdTokenImplicitFlow

| Chiave | Tipo di valore |
| :--- | :--- |
| oauth2AllowIdTokenImplicitFlow | Boolean |

Specifica se questa app Web può richiedere token ID flusso implicito OAuth 2.0. Il valore predefinito è false. Questo flag viene usato per le app basate su browser, ad esempio le app a pagina singola JavaScript.

Esempio:

```json
    "oauth2AllowIdTokenImplicitFlow": false,
```

### <a name="oauth2permissions-attribute"></a>attributo oauth2Permissions

| Chiave | Tipo di valore |
| :--- | :--- |
| oauth2Permissions | Raccolta |

Specifica la raccolta di ambiti di autorizzazione OAuth 2.0 che l'app dell'API Web (risorsa) espone alle app client. Questi ambiti di autorizzazione possono essere concessi alle app client durante il consenso.

Esempio:

```json
    "oauth2Permissions": [
       {
          "adminConsentDescription": "Allow the app to access resources on behalf of the signed-in user.",
          "adminConsentDisplayName": "Access resource1",
          "id": "<guid>",
          "isEnabled": true,
          "type": "User",
          "userConsentDescription": "Allow the app to access resource1 on your behalf.",
          "userConsentDisplayName": "Access resources",
          "value": "user_impersonation"
        }
    ],
```

### <a name="oauth2requiredpostresponse-attribute"></a>attributo oauth2RequiredPostResponse

| Chiave | Tipo di valore |
| :--- | :--- |
| oauth2RequiredPostResponse | Boolean |

Specifica se, come parte delle richieste dei token OAuth 2.0, Azure AD consente richieste POST, anziché richieste GET. L'impostazione predefinita è false, che specifica che sono consentite solo richieste GET.

Esempio:

```json
    "oauth2RequirePostResponse": false,
```

### <a name="parentalcontrolsettings-attribute"></a>attributo parentalControlSettings

| Chiave | Tipo di valore |
| :--- | :--- |
| ambienti parentali | string |

- `countriesBlockedForMinors` specifica i paesi in cui l'app è bloccata per i minori.
- `legalAgeGroupRule` specifica la regola gruppo relativa all'età legale che si applica agli utenti dell'app. Può essere impostata su `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids` o `BlockMinors`.  

Esempio:

```json
    "parentalControlSettings": {
        "countriesBlockedForMinors": [],
        "legalAgeGroupRule": "Allow"
    },
```

### <a name="passwordcredentials-attribute"></a>attributo passwordCredentials

| Chiave | Tipo di valore |
| :--- | :--- |
| passwordCredentials | Raccolta |

Vedere la descrizione per la proprietà `keyCredentials`.

Esempio:

```json
    "passwordCredentials": [
      {
        "customKeyIdentifier": null,
        "endDate": "2018-10-19T17:59:59.6521653Z",
        "keyId": "<guid>",
        "startDate":"2016-10-19T17:59:59.6521653Z",
        "value":null
      }
    ],
```

### <a name="preauthorizedapplications-attribute"></a>attributo preAuthorizedApplications

| Chiave | Tipo di valore |
| :--- | :--- |
| preAuthorizedApplications | Raccolta |

Elenca le applicazioni e le autorizzazioni necessarie per il consenso implicito. Richiede che un amministratore abbia fornito il consenso all'applicazione. preAuthorizedApplications non richiede il consenso utente alle autorizzazioni richieste. Le autorizzazioni elencate in preAuthorizedApplications non richiedono il consenso utente. Tuttavia, le autorizzazioni richieste aggiuntive non elencate in preAuthorizedApplications richiedono il consenso utente.

Esempio:

```json
    "preAuthorizedApplications": [
       {
          "appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",
          "permissionIds": [
             "8748f7db-21fe-4c83-8ab5-53033933c8f1"
            ]
        }
    ],
```

### <a name="publicclient-attribute"></a>publicClient (attributo)

| Chiave | Tipo di valore |
| :--- | :--- |
| publicClient | Boolean|

Specifica se l'applicazione è un client pubblico (ad esempio, un'applicazione installata in esecuzione in un dispositivo mobile). 

Questa proprietà è disponibile solo nell'esperienza di **registrazione dell'app (legacy).** Sostituita `allowPublicClient` dall'esperienza di registrazione dell'App. [App registrations](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="publisherdomain-attribute"></a>attributo publisherDomain

| Chiave | Tipo di valore |
| :--- | :--- |
| editorDomain (dominio dell'editore | string |

Dominio dell'editore verificato per l'applicazione. Di sola lettura.

Esempio:

```json
    "publisherDomain": "https://www.contoso.com",
````

### <a name="replyurls-attribute"></a>Attributo replyUrls

| Chiave | Tipo di valore |
| :--- | :--- |
| replyUrls | Matrice di stringhe |

Questa proprietà multivalore contiene l'elenco dei valori redirect_uri registrati che Azure AD accetta come destinazioni in fase di restituzione dei token.

Questa proprietà è disponibile solo nell'esperienza di **registrazione dell'app (legacy).** Sostituita `replyUrlsWithType` dall'esperienza di registrazione dell'App. [App registrations](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="replyurlswithtype-attribute"></a>ReplyUrlsWithType (attributo)

| Chiave | Tipo di valore |
| :--- | :--- |
| replyUrlsWithType (informazioni in base al tano | Raccolta |

Questa proprietà multivalore contiene l'elenco dei valori redirect_uri registrati che Azure AD accetta come destinazioni in fase di restituzione dei token. Ogni valore URI deve contenere un valore di tipo di app associato. I valori di tipo supportati sono:Supported type values are:

- `Web`
- `InstalledClient`

Per ulteriori informazioni, consultate Restrizioni e limitazioni di [replyUrl.](https://docs.microsoft.com/azure/active-directory/develop/reply-url)

Esempio:

```json
    "replyUrlsWithType": [
       {
          "url": "https://localhost:4400/services/office365/redirectTarget.html",
          "type": "InstalledClient"
       }
    ],
```

### <a name="requiredresourceaccess-attribute"></a>requiredResourceAccess (attributo)

| Chiave | Tipo di valore |
| :--- | :--- |
| requiredResourceAccess | Raccolta |

Con il consenso dinamico, `requiredResourceAccess` attiva l'esperienza di consenso dell'amministratore e l'esperienza di consenso dell'utente per gli utenti che usano il consenso statico. Tuttavia, questo parametro non guida l'esperienza di consenso dell'utente per il caso generale.

- `resourceAppId` è l'identificatore univoco per la risorsa a cui l'app richiede l'accesso. Questo valore deve essere uguale all'appId dichiarato nell'app della risorsa di destinazione.
- `resourceAccess` è una matrice che contiene l'elenco dei ruoli delle app e degli ambiti delle autorizzazioni OAuth 2.0 che l'app richiede dalla risorsa specificata. Contiene i valori di tipo `id` e `type` per le risorse specificate.

Esempio:

```json
    "requiredResourceAccess": [
        {
            "resourceAppId": "00000002-0000-0000-c000-000000000000",
            "resourceAccess": [
                {
                    "id": "311a71cc-e848-46a1-bdf8-97ff7156d8e6",
                    "type": "Scope"
                }
            ]
        }
    ],
```

### <a name="samlmetadataurl-attribute"></a>samlMetadataUrl (attributo)

| Chiave | Tipo di valore |
| :--- | :--- |
| samlMetadataUrl | string |

URL dei metadati SAML per l'app.

Esempio:

```json
    "samlMetadataUrl": "https://MyRegisteredAppSAMLMetadata",
```

### <a name="signinurl-attribute"></a>attributo signInUrl

| Chiave | Tipo di valore |
| :--- | :--- |
| signInUrl (informazioni in stato in questo gruppo) | string |

Specifica l'URL della home page dell'app.

Esempio:

```json
    "signInUrl": "https://MyRegisteredApp",
```

### <a name="signinaudience-attribute"></a>signInAudience (attributo)

| Chiave | Tipo di valore |
| :--- | :--- |
| signInAudience (firmainAudience) | string |

Specifica gli account Microsoft supportati per l'applicazione corrente. I valori supportati sono:
- `AzureADMyOrg`- Utenti con un account aziendale o dell'istituto di istruzione Microsoft nel tenant di Azure AD dell'organizzazione (ad esempio, tenant singolo)- Users with a Microsoft work or school account in my organization's Azure AD tenant (for example, single tenant)
- `AzureADMultipleOrgs`- Utenti con un account aziendale o dell'istituto di istruzione Microsoft nel tenant di Azure AD di qualsiasi organizzazione (ad esempio, multi-tenant)- Users with a Microsoft work or school account in any organization's Azure AD tenant (for example, multi-tenant)
- `AzureADandPersonalMicrosoftAccount`- Utenti con un account Microsoft personale o un account aziendale o dell'istituto di istruzione nel tenant di Azure AD di qualsiasi organizzazione- Users with a personal Microsoft account, or a work or school account in any organization's Azure AD tenant
- `PersonalMicrosoftAccount`- Account personali che vengono utilizzati per accedere a servizi come Xbox e Skype.

Esempio:

```json
    "signInAudience": "AzureADandPersonalMicrosoftAccount",
```

### <a name="tags-attribute"></a>tags (attributo)

| Chiave | Tipo di valore |
| :--- | :--- |
| tags | String Array  |

Stringhe personalizzate che possono essere usate per classificare e identificare l'applicazione.

Esempio:

```json
    "tags": [
       "ProductionApp"
    ],
```

## <a name="common-issues"></a>Problemi comuni

### <a name="manifest-limits"></a>Limiti del manifesto

Un manifesto dell'applicazione ha più attributi che vengono definiti raccolte. ad esempio appRoles, keyCredentials, knownClientApplications, identifierUris, redirectUris, requiredResourceAccess e oauth2Permissions. All'interno del manifesto completo dell'applicazione per qualsiasi applicazione, il numero totale di voci in tutte le raccolte combinate è stato limitato a 1200. Se in precedenza si specificano 100 URI di reindirizzamento nel manifesto dell'applicazione, rimangono solo 1100 voci rimanenti da utilizzare in tutte le altre raccolte combinate che costituiscono il manifesto.

> [!NOTE]
> Nel caso in cui si tenta di aggiungere più di 1200 voci nel manifesto dell'applicazione, è possibile che venga visualizzato un errore **"Impossibile aggiornare l'applicazione xxxxxx. Dettagli errore: la dimensione del manifesto ha superato il limite. Ridurre il numero di valori e ripetere la richiesta."**

### <a name="unsupported-attributes"></a>Attributi non supportati

Il manifesto dell'applicazione rappresenta lo schema del modello di applicazione sottostante in Azure AD. Man mano che lo schema sottostante si evolve, l'editor del manifesto verrà aggiornato per riflettere di tanto in tanto il nuovo schema. Di conseguenza, è possibile notare nuovi attributi visualizzati nel manifesto dell'applicazione. In rare occasioni, è possibile notare una modifica sintattica o semantica negli attributi esistenti o potrebbe sembrare che un attributo esistente in precedenza non sia più supportato. Ad esempio, verranno visualizzati nuovi attributi nelle [registrazioni dell'app,](https://go.microsoft.com/fwlink/?linkid=2083908)noti con un nome diverso nell'esperienza di registrazione dell'app (Legacy).

| Registrazioni di app (legacy)| Registrazioni per l'app           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Per le descrizioni di questi attributi, vedere la sezione di riferimento del [manifesto.](#manifest-reference)

Quando si tenta di caricare un manifesto scaricato in precedenza, è possibile che venga visualizzato uno dei seguenti errori. Questo errore è probabile perché l'editor del manifesto supporta ora una versione più recente dello schema, che non corrisponde a quello che si sta tentando di caricare.

* "Impossibile aggiornare l'applicazione xxxxxx. Dettaglierrore: identificatore di oggetto non valido 'undefined'. []."
* "Impossibile aggiornare l'applicazione xxxxxx. Dettagli errore: uno o più valori di proprietà specificati non sono validi. []."
* "Impossibile aggiornare l'applicazione xxxxxx. Dettagli errore: Impossibile impostare availableToOtherTenants in questa versione api per l'aggiornamento. []."
* "Impossibile aggiornare l'applicazione xxxxxx. Dettaglio errore: gli aggiornamenti alla proprietà 'replyUrls' non sono consentiti per questa applicazione. Utilizzare invece la proprietà 'replyUrlsWithType'. []."
* "Impossibile aggiornare l'applicazione xxxxxx. Dettagli errore: è stato trovato un valore senza un nome di tipo e non è disponibile alcun tipo previsto. Quando viene specificato il modello, ogni valore nel payload deve avere un tipo che può essere specificato nel payload, in modo esplicito dal chiamante o deferito in modo implicito dal valore padre. []"

Quando viene visualizzato uno di questi errori, si consigliano le seguenti azioni:

1. Modificare gli attributi singolarmente nell'editor del manifesto anziché caricare un manifesto scaricato in precedenza. Utilizzare la tabella di riferimento del [manifesto](#manifest-reference) per comprendere la sintassi e la semantica degli attributi vecchi e nuovi in modo da poter modificare correttamente gli attributi a cui si è interessati. 
1. Se il flusso di lavoro richiede il salvataggio dei manifesti nel repository di origine per utilizzarli in un secondo momento, è consigliabile ricollegare i manifesti salvati nel repository con quello visualizzato nell'esperienza di **registrazione dell'app.**

## <a name="next-steps"></a>Passaggi successivi

* Per altre info sulla relazione tra l'applicazione di un'app e gli oggetti entità servizio, vedere [Oggetti applicazione e entità servizio in Azure AD.](app-objects-and-service-principals.md)
* Vedere il [glossario per sviluppatori di piattaforma di identità Microsoft](developer-glossary.md) per le definizioni di alcuni concetti fondamentali per gli sviluppatori di piattaforme di identità Microsoft.See the Microsoft identity platform developer glossary for definitions of some core Microsoft identity platform developer concepts.

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
