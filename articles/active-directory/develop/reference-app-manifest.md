---
title: Informazioni sul manifesto dell'app Azure Active Directory
description: Descrizione dettagliata del manifesto dell'app Azure Active Directory, che rappresenta una configurazione dell'identità dell'applicazione in un tenant di Azure AD e che viene usato per facilitare l'autorizzazione OAuth, per l'esperienza di consenso e altro ancora.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: 3338c71d37a176206ff106a8229c3b583209ddd4
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83737334"
---
# <a name="azure-active-directory-app-manifest"></a>Manifesto dell'app Azure Active Directory

Il manifesto dell'applicazione contiene una definizione di tutti gli attributi di un oggetto applicazione in Microsoft Identity Platform. Funge inoltre da meccanismo per laggiornamento dell'oggetto applicazione. Per altre informazioni sull'entità applicazione e il relativo schema, vedere la [documentazione sull'entità applicazione dell'API Graph](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity).

È possibile configurare gli attributi di un'app tramite il portale di Azure o a livello di codice usando l'[API REST](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) o [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#applications). Tuttavia, esistono alcuni scenari in cui è necessario modificare il manifesto dell'applicazione per configurare l'attributo di un'applicazione. Tali scenari includono:

* Se l'app è stata registrata come multi-tenant di Azure AD e per account Microsoft personali, non è possibile modificare gli account Microsoft supportati nell'interfaccia utente. Per modificare i tipi di account supportati è necessario usare l'editor del manifesto dell'applicazione.
* Se è necessario definire le autorizzazioni e i ruoli supportati dall'applicazione, è necessario modificare il manifesto dell'applicazione.

## <a name="configure-the-app-manifest"></a>Configurare il manifesto dell'applicazione

Per configurare il manifesto dell'applicazione:

1. Accedere al [portale di Azure](https://portal.azure.com). Cercare e selezionare il servizio **Azure Active Directory**.
1. Selezionare **Registrazioni per l'app**.
1. Selezionare l'applicazione da configurare.
1. Nella pagina **Panoramica** dell'app selezionare la sezione **Manifesto**. Si apre un editor di manifesto basato sul Web che consente di modificare il manifesto all'interno del portale. Facoltativamente è possibile selezionare **Scarica**, modificare il manifesto in locale e quindi usare **Carica** per riapplicarlo all'applicazione.

## <a name="manifest-reference"></a>Riferimento del manifesto

Questa sezione descrive gli attributi trovati nel manifesto dell'applicazione.

### <a name="id-attribute"></a>Attributo id

| Chiave | Tipo di valore |
| :--- | :--- |
| id | string |

Identificatore univoco per l'app nella directory. Questo ID non è l'identificatore usato per identificare l'app nelle transazioni di protocollo. Viene usato per fare riferimento all'oggetto nelle query di directory.

Esempio:

```json
    "id": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="accesstokenacceptedversion-attribute"></a>Attributo accessTokenAcceptedVersion

| Chiave | Tipo di valore |
| :--- | :--- |
| accessTokenAcceptedVersion | Nullable Int32 |

Specifica la versione del token di accesso prevista dalla risorsa. Questo parametro modifica la versione e il formato del token JWT generato indipendentemente dall'endpoint o dal client usato per richiedere il token di accesso.

L'endpoint usato, v1.0 o v2.0, viene scelto dal client e influisce solo sulla versione di id_tokens. Le risorse devono configurare in modo esplicito `accesstokenAcceptedVersion` per indicare il formato di token di accesso supportato.

I valori possibili per `accesstokenAcceptedVersion` sono 1, 2 o Null. Se il valore è Null, il parametro usa l'impostazione predefinita 1 che corrisponde all'endpoint v1.0.

Se `signInAudience` è `AzureADandPersonalMicrosoftAccount`, il valore deve essere `2`.

Esempio:

```json
    "accessTokenAcceptedVersion": 2,
```

### <a name="addins-attribute"></a>Attributo addIns

| Chiave | Tipo di valore |
| :--- | :--- |
| addIns | Raccolta |

Definisce il comportamento personalizzato che un servizio consumer può usare per chiamare un'app in contesti specifici. Ad esempio, le applicazioni in grado di eseguire il rendering di flussi di file possono impostare la proprietà `addIns` per la relativa funzionalità "FileHandler". Questo parametro consente ai servizi come Office 365 di chiamare l'applicazione nel contesto di un documento su cui l'utente sta lavorando.

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

### <a name="allowpublicclient-attribute"></a>Attributo allowPublicClient

| Chiave | Tipo di valore |
| :--- | :--- |
| allowPublicClient | Boolean |

Specifica il tipo di applicazione di fallback. Azure AD deduce il tipo di applicazione da replyUrlsWithType per impostazione predefinita. Esistono alcuni scenari in cui Azure AD non è in grado di determinare il tipo di app client. Ad esempio, uno scenario di questo tipo è il flusso [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) in cui si verifica la richiesta HTTP senza un reindirizzamento URL. In questi casi, Azure AD interpreterà il tipo di applicazione in base al valore di questa proprietà. Se questo valore è impostato su true, il tipo di applicazione di fallback è impostato come client pubblico, ad esempio un'app installata in esecuzione in un dispositivo mobile. Il valore predefinito è false, che indica che il tipo di applicazione di fallback è un client riservato, ad esempio un'app Web.

Esempio:

```json
    "allowPublicClient": false,
```

### <a name="availabletoothertenants-attribute"></a>Attributo availableToOtherTenants

| Chiave | Tipo di valore |
| :--- | :--- |
| availableToOtherTenants | Boolean |

Impostare su true se l'applicazione è condivisa con altri tenant. In caso contrario, false.

> [!NOTE]
> Questo attributo è disponibile solo nell'esperienza **Registrazioni app (legacy)** . Sostituito da `signInAudience` nell'esperienza [Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908).

### <a name="appid-attribute"></a>Attributo appId

| Chiave | Tipo di valore |
| :--- | :--- |
| appId | string |

Specifica l'identificatore univoco per l'app assegnato a un'app da Azure AD.

Esempio:

```json
    "appId": "601790de-b632-4f57-9523-ee7cb6ceba95",
```

### <a name="approles-attribute"></a>Attributo appRoles

| Chiave | Tipo di valore |
| :--- | :--- |
| appRoles | Raccolta |

Specifica la raccolta di ruoli che un'app può dichiarare. Questi ruoli possono essere assegnati a utenti, gruppi o entità servizio. Per altri esempi e informazioni, vedere [Aggiungere ruoli dell'app in un'applicazione e riceverli nel token](howto-add-app-roles-in-azure-ad-apps.md).

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

### <a name="displayname-attribute"></a>Attributo displayName

| Chiave | Tipo di valore |
| :--- | :--- |
| displayName | string |

Nome visualizzato dell'app.

> [!NOTE]
> Questo attributo è disponibile solo nell'esperienza **Registrazioni app (legacy)** . Sostituito da `name` nell'esperienza [Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908).

### <a name="errorurl-attribute"></a>Attributo errorUrl

| Chiave | Tipo di valore |
| :--- | :--- |
| errorURL | string |

Non supportato.

### <a name="groupmembershipclaims-attribute"></a>Attributo groupMembershipClaims

| Chiave | Tipo di valore |
| :--- | :--- |
|groupMembershipClaims | string |

Configura l'attestazione `groups` rilasciata in un token di accesso utente oppure OAuth 2.0 previsto dall'app. Per impostare questo attributo, usare uno dei seguenti valori di stringa validi:

- `"None"`
- `"SecurityGroup"` (per gruppi di sicurezza e ruoli di Azure AD)
- `"All"` (verranno restituiti tutti i gruppi di sicurezza, i gruppi di distribuzione e i ruoli della directory di Azure AD a cui appartiene l'utente connesso).

Esempio:

```json
    "groupMembershipClaims": "SecurityGroup",
```

### <a name="homepage-attribute"></a>Attributo homepage

| Chiave | Tipo di valore |
| :--- | :--- |
| homepage |string |

URL della home page dell'applicazione.

> [!NOTE]
> Questo attributo è disponibile solo nell'esperienza **Registrazioni app (legacy)** . Sostituito da `signInUrl` nell'esperienza [Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908).

### <a name="objectid-attribute"></a>Attributo objectId

| Chiave | Tipo di valore |
| :--- | :--- |
|objectId | string |

Identificatore univoco per l'app nella directory.

Questo attributo è disponibile solo nell'esperienza **Registrazioni app (legacy)** . Sostituito da `id` nell'esperienza [Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908).

Esempio:

```json
    "objectId": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="optionalclaims-attribute"></a>Attributo optionalClaims

| Chiave | Tipo di valore |
| :--- | :--- |
| optionalClaims | string |

Attestazioni facoltative restituite nel token dal servizio token di sicurezza per questa specifica app.

A questo punto, le app che supportano sia account personali che account Azure AD (registrati tramite il portale di registrazione delle app) non possono usare le attestazioni facoltative. Tuttavia, le app registrate solo per Azure AD usando l'endpoint v2.0 possono ottenere le attestazioni facoltative richieste nel manifesto. Per altre informazioni, vedere [Attestazioni facoltative](active-directory-optional-claims.md).

Esempio:

```json
    "optionalClaims": null,
```



### <a name="identifieruris-attribute"></a>Attributo identifierUris

| Chiave | Tipo di valore |
| :--- | :--- |
| identifierUris | String Array |

URI definiti dall'utente che identificano in modo univoco un'app Web entro il rispettivo tenant di Azure AD oppure entro un dominio personalizzato verificato se l'app è multi-tenant.

Esempio:

```json
    "identifierUris": "https://MyRegisteredApp",
```

### <a name="informationalurls-attribute"></a>Attributo informationalUrls

| Chiave | Tipo di valore |
| :--- | :--- |
| informationalUrls | string |

Specifica i collegamenti alle condizioni d'uso del servizio e all'informativa sulla privacy dell'app. Le condizioni per l'utilizzo del servizio e l'informativa sulla privacy vengono presentate agli utenti tramite l'esperienza di consenso dell'utente Per altre informazioni, vedere [Procedura: Aggiungere condizioni per l'utilizzo del servizio e informativa sulla privacy per le app di Azure AD registrate](howto-add-terms-of-service-privacy-statement.md).

Esempio:

```json
    "informationalUrls": {
        "termsOfService": "https://MyRegisteredApp/termsofservice",
        "support": "https://MyRegisteredApp/support",
        "privacy": "https://MyRegisteredApp/privacystatement",
        "marketing": "https://MyRegisteredApp/marketing"
    },
```

### <a name="keycredentials-attribute"></a>Attributo keyCredentials

| Chiave | Tipo di valore |
| :--- | :--- |
| keyCredentials | Raccolta |

Contiene riferimenti a credenziali assegnate dall'app, segreti condivisi basati su stringa e certificati X.509. Queste credenziali vengono usate in fase di richiesta dei token di accesso (quando l'app funge da client, anziché da risorsa).

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

### <a name="knownclientapplications-attribute"></a>Attributo knownClientApplications

| Chiave | Tipo di valore |
| :--- | :--- |
| knownClientApplications | String Array |

Viene usato per unire il consenso se una soluzione contiene due parti, un'app client e un'app API Web personalizzata. Se si immette l'appID dell'app client in questo valore, l'utente deve fornire il consenso solo una volta per l'app client. Azure AD saprà che il consenso al client implica il consenso implicito all'API Web. Eseguirà automaticamente il provisioning delle entità servizio per il client e l'API Web nello stesso momento. Sia il client sia l'app dell'API Web devono essere registrati nello stesso tenant.

Esempio:

```json
    "knownClientApplications": ["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"],
```

### <a name="logourl-attribute"></a>Attributo logoUrl

| Chiave | Tipo di valore |
| :--- | :--- |
| logoUrl | string |

Valore di sola lettura che punta all'URL della rete CDN del logo caricato nel portale.

Esempio:

```json
    "logoUrl": "https://MyRegisteredAppLogo",
```

### <a name="logouturl-attribute"></a>Attributo logoutUrl

| Chiave | Tipo di valore |
| :--- | :--- |
| logoutUrl | string |

URL per disconnettersi dall'app.

Esempio:

```json
    "logoutUrl": "https://MyRegisteredAppLogout",
```

### <a name="name-attribute"></a>Attributo name

| Chiave | Tipo di valore |
| :--- | :--- |
| name | string |

Nome visualizzato dell'app.

Esempio:

```json
    "name": "MyRegisteredApp",
```

### <a name="oauth2allowimplicitflow-attribute"></a>Attributo oauth2AllowImplicitFlow

| Chiave | Tipo di valore |
| :--- | :--- |
| oauth2AllowImplicitFlow | Boolean |

Specifica se questa app Web può richiedere token di accesso di flusso implicito OAuth 2.0. Il valore predefinito è false. Questo flag viene usato per le app basate su browser, ad esempio le app JavaScript a pagina singola. Per altre informazioni, immettere `OAuth 2.0 implicit grant flow` nel sommario e vedere gli argomenti sul flusso implicito.

Esempio:

```json
    "oauth2AllowImplicitFlow": false,
```

### <a name="oauth2allowidtokenimplicitflow-attribute"></a>Attributo oauth2AllowIdTokenImplicitFlow

| Chiave | Tipo di valore |
| :--- | :--- |
| oauth2AllowIdTokenImplicitFlow | Boolean |

Specifica se questa app Web può richiedere token ID flusso implicito OAuth 2.0. Il valore predefinito è false. Questo flag viene usato per le app basate su browser, ad esempio le app JavaScript a pagina singola.

Esempio:

```json
    "oauth2AllowIdTokenImplicitFlow": false,
```

### <a name="oauth2permissions-attribute"></a>Attributo oauth2Permissions

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

### <a name="oauth2requiredpostresponse-attribute"></a>Attributo oauth2RequiredPostResponse

| Chiave | Tipo di valore |
| :--- | :--- |
| oauth2RequiredPostResponse | Boolean |

Specifica se, come parte delle richieste dei token OAuth 2.0, Azure AD consente richieste POST, anziché richieste GET. L'impostazione predefinita è false, che specifica che sono consentite solo richieste GET.

Esempio:

```json
    "oauth2RequirePostResponse": false,
```

### <a name="parentalcontrolsettings-attribute"></a>Attributo parentalControlSettings

| Chiave | Tipo di valore |
| :--- | :--- |
| parentalControlSettings | string |

- `countriesBlockedForMinors` specifica i paesi e le aree in cui l'app è bloccata per i minori.
- `legalAgeGroupRule` specifica la regola gruppo relativa all'età legale che si applica agli utenti dell'app. Può essere impostata su `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids` o `BlockMinors`.  

Esempio:

```json
    "parentalControlSettings": {
        "countriesBlockedForMinors": [],
        "legalAgeGroupRule": "Allow"
    },
```

### <a name="passwordcredentials-attribute"></a>Attributo passwordCredentials

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

### <a name="preauthorizedapplications-attribute"></a>Attributo preAuthorizedApplications

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

### <a name="publicclient-attribute"></a>Attributo publicClient

| Chiave | Tipo di valore |
| :--- | :--- |
| publicClient | Boolean|

Specifica se l'applicazione è un client pubblico, ad esempio un'applicazione installata in esecuzione in un dispositivo mobile. 

Questa proprietà è disponibile solo nell'esperienza **Registrazioni app (legacy)** . Sostituita da `allowPublicClient` nell'esperienza [Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908).

### <a name="publisherdomain-attribute"></a>Attributo publisherDomain

| Chiave | Tipo di valore |
| :--- | :--- |
| publisherDomain | string |

Dominio del server di pubblicazione verificato per l'applicazione. Di sola lettura.

Esempio:

```json
    "publisherDomain": "https://www.contoso.com",
````

### <a name="replyurls-attribute"></a>Attributo replyUrls

| Chiave | Tipo di valore |
| :--- | :--- |
| replyUrls | Matrice di stringhe |

Questa proprietà multivalore contiene l'elenco dei valori redirect_uri registrati che Azure AD accetta come destinazioni in fase di restituzione dei token.

Questa proprietà è disponibile solo nell'esperienza **Registrazioni app (legacy)** . Sostituita da `replyUrlsWithType` nell'esperienza [Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908).

### <a name="replyurlswithtype-attribute"></a>Attributo replyUrlsWithType

| Chiave | Tipo di valore |
| :--- | :--- |
| replyUrlsWithType | Raccolta |

Questa proprietà multivalore contiene l'elenco dei valori redirect_uri registrati che Azure AD accetta come destinazioni in fase di restituzione dei token. Ogni valore di URI deve contenere un valore per il tipo di app associato. I valori supportati sono:

- `Web`
- `InstalledClient`

Per altre informazioni, vedere [Restrizioni e limitazioni di replyUrl](https://docs.microsoft.com/azure/active-directory/develop/reply-url).

Esempio:

```json
    "replyUrlsWithType": [
       {
          "url": "https://localhost:4400/services/office365/redirectTarget.html",
          "type": "InstalledClient"
       }
    ],
```

### <a name="requiredresourceaccess-attribute"></a>Attributo requiredResourceAccess

| Chiave | Tipo di valore |
| :--- | :--- |
| requiredResourceAccess | Raccolta |

Con il consenso dinamico, `requiredResourceAccess` attiva l'esperienza di consenso dell'amministratore e l'esperienza di consenso dell'utente per gli utenti che usano il consenso statico. Questo parametro non attiva, tuttavia, l'esperienza di consenso dell'utente per i casi generici.

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

### <a name="samlmetadataurl-attribute"></a>Attributo samlMetadataUrl

| Chiave | Tipo di valore |
| :--- | :--- |
| samlMetadataUrl | string |

URL dei metadati SAML per l'app.

Esempio:

```json
    "samlMetadataUrl": "https://MyRegisteredAppSAMLMetadata",
```

### <a name="signinurl-attribute"></a>Attributo signInUrl

| Chiave | Tipo di valore |
| :--- | :--- |
| signInUrl | string |

Specifica l'URL della home page dell'app.

Esempio:

```json
    "signInUrl": "https://MyRegisteredApp",
```

### <a name="signinaudience-attribute"></a>Attributo signInAudience

| Chiave | Tipo di valore |
| :--- | :--- |
| signInAudience | string |

Specifica gli account Microsoft supportati per l'applicazione corrente. I valori supportati sono:
- `AzureADMyOrg`: utenti con un account aziendale o dell'istituto di istruzione Microsoft nel tenant di Azure AD dell'organizzazione (ad esempio, tenant singolo)
- `AzureADMultipleOrgs`: utenti con un account aziendale o dell'istituto di istruzione Microsoft nel tenant di Azure AD di qualsiasi organizzazione (ad esempio, multi-tenant)
- `AzureADandPersonalMicrosoftAccount`: utenti con un account Microsoft personale o un account aziendale o dell'istituto di istruzione nel tenant di Azure AD di qualsiasi organizzazione
- `PersonalMicrosoftAccount`: account personali usati per accedere a servizi quali Xbox e Skype.

Esempio:

```json
    "signInAudience": "AzureADandPersonalMicrosoftAccount",
```

### <a name="tags-attribute"></a>Attributo tags

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

Un manifesto dell'applicazione ha più attributi detti raccolte, ad esempio appRoles, keyCredentials, knownClientApplications, identifierUris, redirectUris, requiredResourceAccess e oauth2Permissions. All'interno del manifesto dell'applicazione completo per qualsiasi applicazione, il numero totale di voci in tutte le raccolte combinate è stato limitato a 1200. Se si specificano 100 URI di reindirizzamento nel manifesto dell'applicazione, rimarranno solo 1100 voci da usare in tutte le altre raccolte combinate che compongono il manifesto.

> [!NOTE]
> Se si tenta di aggiungere più di 1200 voci nel manifesto dell'applicazione, è possibile che venga visualizzato un errore simile a **"Non è stato possibile aggiornare l'applicazione xxxxxx. Dettagli dell'errore: Limite delle dimensioni del manifesto superato. Ridurre il numero di valori e ripetere la richiesta".**

### <a name="unsupported-attributes"></a>Attributi non supportati

Il manifesto dell'applicazione rappresenta lo schema del modello di applicazione sottostante in Azure AD. Con l'evolversi dello schema sottostante, l'editor del manifesto verrà aggiornato in modo che rifletta il nuovo schema di tanto in tanto. Di conseguenza, è possibile notare che i nuovi attributi vengono visualizzati nel manifesto dell'applicazione. In rare occasioni, è possibile notare una modifica sintattica o semantica negli attributi esistenti oppure è possibile trovare un attributo precedentemente esistente che non è più supportato. Ad esempio, verranno visualizzati nuovi attributi in [Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908), noti con un nome diverso nell'esperienza Registrazioni app (legacy).

| Registrazioni app (legacy)| Registrazioni per l'app           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Per le descrizioni di questi attributi, vedere la sezione [Riferimento del manifesto](#manifest-reference).

Quando si tenta di caricare un manifesto scaricato in precedenza, è possibile che venga visualizzato un errore simile a quelli elencati di seguito. Questo errore è probabilmente dovuto al fatto che l'editor del manifesto supporta ora una versione più recente dello schema, che non corrisponde a quella che si sta provando a caricare.

* "Non è stato possibile aggiornare l'applicazione xxxxxx. Dettagli errore: Identificatore oggetto 'non definito' non valido. []."
* "Non è stato possibile aggiornare l'applicazione xxxxxx. Dettagli errore: Uno o più valori di proprietà specificati non sono validi. []."
* "Non è stato possibile aggiornare l'applicazione xxxxxx. Dettagli errore: Non è consentito impostare availableToOtherTenants in questa versione dell'API per l'aggiornamento. []."
* "Non è stato possibile aggiornare l'applicazione xxxxxx. Dettagli errore: Gli aggiornamenti della proprietà 'replyUrls' non sono consentiti per questa applicazione. Usare invece la proprietà 'replyUrlsWithType'. []."
* "Non è stato possibile aggiornare l'applicazione xxxxxx. Dettagli errore: È stato trovato un valore senza nome di tipo e il tipo previsto non è disponibile. Se il modello è specificato, ogni valore nel payload deve disporre di un tipo che può essere specificato nel payload in modo esplicito dal chiamante o dedotto in modo implicito dal valore padre. []"

Se viene visualizzato uno di questi errori, è consigliabile eseguire le operazioni seguenti:

1. Modificare gli attributi singolarmente nell'editor del manifesto anziché caricare un manifesto scaricato in precedenza. Usare la tabella in [Riferimento del manifesto](#manifest-reference) per comprendere la sintassi e la semantica degli attributi vecchi e nuovi, in modo da poter modificare correttamente gli attributi a cui si è interessati. 
1. Se il flusso di lavoro richiede di salvare i manifesti nel repository di origine per usarli in seguito, è consigliabile riassegnare i manifesti salvati nel repository a quello visualizzato nell'esperienza **Registrazioni app**.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla relazione tra gli oggetti applicazione e gli oggetti entità servizio di un'app, vedere [Oggetti applicazione e oggetti entità servizio in Azure AD](app-objects-and-service-principals.md).
* Per le definizioni di alcuni concetti fondamentali per gli sviluppatori di Microsoft Identity Platform, vedere [Glossario per gli sviluppatori di Microsoft Identity Platform](developer-glossary.md).

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
