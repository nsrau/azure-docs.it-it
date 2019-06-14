---
title: Configurazione dell'applicazione client (Microsoft Authentication Library) | Azure
description: Informazioni sulle opzioni di configurazione per client riservati e pubblica applicazioni in Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5e175a8cdd1622add90bd80df63303fe914ab9c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66430816"
---
# <a name="application-configuration-options"></a>Opzioni di configurazione dell'applicazione

Nel codice, si inizializza un nuovo client pubblici o riservati (o user-agent per msal. js) dell'applicazione per l'autenticazione e acquisire i token. È possibile impostare un numero di opzioni di configurazione quando si inizializza l'app client in Microsoft Authentication Library (MSAL). Queste opzioni rientrano in due gruppi:

- Opzioni di registrazione, tra cui:
    - [Autorità](#authority) (costituito da provider di identità [istanza](#cloud-instance) e Accedi [audience](#application-audience) per l'app ed eventualmente l'ID tenant).
    - [ID client](#client-id).
    - [URI di reindirizzamento](#redirect-uri).
    - [segreto client](#client-secret) (per le applicazioni client riservato).
- [Le opzioni di registrazione](#logging), inclusi il nome del componente usando la libreria di livello di registrazione e controllo dei dati personali.

## <a name="authority"></a>Authority
L'autorità è un URL che indica una directory che MSAL può richiedere token da. Autorità comuni sono:

- https://login.microsoftonline.com/&lt ; tenant&gt; /, dove &lt; tenant&gt; è l'ID tenant del tenant di Azure Active Directory (Azure AD) o in un dominio associati al tenant Azure AD. Usato solo per consentire agli utenti di un'organizzazione specifica.
- https://login.microsoftonline.com/common/. Utilizzato per consentire agli utenti con lavoro e dell'istituto di istruzione o account Microsoft personali.
- https://login.microsoftonline.com/organizations/. Utilizzato per consentire agli utenti con account aziendali e dell'istituto di istruzione.
- https://login.microsoftonline.com/consumers/. Utilizzato per consentire agli utenti con un solo account Microsoft personale (precedentemente noto come account Windows Live ID).

L'impostazione dell'autorità deve essere coerente con ciò che viene dichiarata nel portale di registrazione dell'applicazione.

L'URL dell'autorità è costituito l'istanza e il gruppo di destinatari.

L'autorità può essere:
- Un'autorità di cloud di Azure AD.
- un'autorità di Azure AD B2C. Visualizzare [B2C specifiche](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- Un'autorità di Active Directory Federation Services (ADFS). Visualizzare [supporto di ad FS](https://aka.ms/msal-net-adfs-support).

Le autorità di Azure AD cloud hanno due parti:
- il provider di identità *istanza*
- L'accesso al posto *audience* per l'app

L'istanza e il gruppo di destinatari possono essere concatenati e forniti come l'URL dell'autorità. Nelle versioni precedenti a MSAL 3 di MSAL.NET. *x*, si doveva compose manualmente l'autorità, basato su cloud si vuole ricevere come destinazione e il pubblico accesso.  Questo diagramma mostra come l'URL dell'autorità è composto da:

![Come è costituito da dell'URL dell'autorità](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Istanza cloud
Il *istanza* consente di specificare se l'app esegue l'accesso degli utenti dal cloud pubblico di Azure o dal cloud nazionali. Con MSAL nel codice, è possibile impostare l'istanza di cloud di Azure tramite un'enumerazione o passando l'URL per il [istanza del cloud nazionale](authentication-national-cloud.md#azure-ad-authentication-endpoints) come il `Instance` membro (se lo si conosce).

MSAL.NET genererà un'eccezione esplicita se entrambe `Instance` e `AzureCloudInstance` specificati.

Destinazione per l'istanza di cloud pubblico di Azure se non si specifica un'istanza, l'app (l'istanza di URL `https://login.onmicrosoftonline.com`).

## <a name="application-audience"></a>Destinatari dell'applicazione

Il pubblico accesso dipende dalle esigenze aziendali per l'app:
- Se si ha una linea di sviluppatore di business (LOB), si sarà probabilmente produrre un'applicazione a tenant singolo che verrà usata solo all'interno dell'organizzazione. In tal caso, è necessario specificare l'organizzazione, con relativi ID tenant (l'ID dell'istanza di Azure AD) o un nome di dominio associato all'istanza di Azure AD.
- Se sei un fornitore di software indipendente, è possibile eseguire l'accesso degli utenti con i propri account aziendali e dell'istituto di istruzione in tutte le organizzazioni o in alcune organizzazioni (app multi-tenant). Ma si potrebbe anche voler chiedere agli utenti di accedere con l'account Microsoft personale.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Come specificare i destinatari nella codice/configurazione
Si usa MSAL nel codice, specificare i destinatari usando uno dei valori seguenti:
- L'enumerazione di destinatari dell'autorità di Azure AD
- L'ID tenant, che può essere:
  - Un GUID (l'ID dell'istanza di Azure AD), per applicazioni a tenant singolo
  - Un nome di dominio associato con l'istanza di Azure AD (anche per le applicazioni a tenant singolo)
- Uno di questi segnaposto come ID tenant al posto dell'enumerazione di destinatari autorità di Azure AD:
    - `organizations` per un'applicazione multi-tenant
    - `consumers` per consentire agli utenti solo con i propri account personali
    - `common` per consentire agli utenti con il proprio lavoro e dell'istituto di istruzione o l'account Microsoft personale

MSAL genererà un'eccezione significativa se si specificano sia il gruppo di destinatari di autorità di Azure AD e l'ID tenant.

Se non si specifica un gruppo di destinatari, destinazione per l'app Azure AD e gli account Microsoft personali come un gruppo di destinatari. (Vale a dire, questo si comporta come se `common` sono state specificate.)

### <a name="effective-audience"></a>Gruppo di destinatari efficace
I destinatari efficaci per l'applicazione sarà il valore minimo (se esiste un'intersezione) del gruppo di destinatari che è impostato nell'app e i destinatari che sono specificato nella registrazione dell'app. In effetti, il [registrazioni per l'App](https://aka.ms/appregistrations) esperienza consente di specificare i destinatari (i tipi di account supportato) per l'app. Per altre informazioni, vedere [Avvio rapido: Registrare un'applicazione con la piattaforma delle identità Microsoft](quickstart-register-app.md).

Attualmente, l'unico modo per ottenere un'app per consentire agli utenti con un solo account Microsoft personale consiste nel configurare entrambe queste impostazioni:
- Impostare i destinatari di registrazione di app su `Work and school accounts and personal accounts`.
- Impostare il gruppo di destinatari di codice/configurazione `AadAuthorityAudience.PersonalMicrosoftAccount` (o `TenantID` = "utenti").

## <a name="client-id"></a>ID client
L'ID client è l'ID univoco dell'applicazione (client) assegnato all'app da Azure AD quando è stata registrata l'app.

## <a name="redirect-uri"></a>URI di reindirizzamento
Il reindirizzamento URI è il provider di identità verrà restituire i token di sicurezza.

### <a name="redirect-uri-for-public-client-apps"></a>URI di reindirizzamento per le app client pubblici
Se sei uno sviluppatore di app client pubblico, che usa MSAL:
- Non è necessario passare `RedirectUri` perché viene calcolato automaticamente da MSAL. Questo reindirizzamento URI è impostato su uno di questi valori, a seconda della piattaforma:
   - `urn:ietf:wg:oauth:2.0:oob` per tutte le piattaforme Windows
   - `msal{ClientId}://auth` per Xamarin. Android e iOS

- È necessario configurare il reindirizzamento in URI [registrazioni per l'App](https://aka.ms/appregistrations):

   ![URI di reindirizzamento nelle registrazioni per l'App](media/msal-client-application-configuration/redirect-uri.png)

È possibile sostituire l'URI di reindirizzamento usando il `RedirectUri` proprietà (ad esempio, se si usa Broker). Di seguito sono riportati alcuni esempi degli URI di reindirizzamento per tale scenario:

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $"msauth. {Bundle.ID}://auth";

Per informazioni dettagliate, vedere la [documentazione di Android e iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS).

### <a name="redirect-uri-for-confidential-client-apps"></a>URI di reindirizzamento per le app client riservato
Per le app web, l'URI di reindirizzamento (o l'URI di risposta) è l'URI che verrà usato per inviare il token all'applicazione Azure AD. Ciò può essere l'URL di web app/API Web se l'app riservata è uno dei seguenti. Il reindirizzamento URI deve essere registrata nella registrazione dell'app. Questa registrazione è particolarmente importante quando si distribuisce un'app che inizialmente testata in locale. È quindi necessario aggiungere l'URL di risposta dell'app distribuita nel portale di registrazione dell'applicazione.

Per le app daemon, non devi specificare un URI di reindirizzamento.

## <a name="client-secret"></a>Segreto client
Questa opzione specifica il segreto client per l'app client riservato. Il segreto (password dell'app) è fornito dal portale di registrazione dell'applicazione o fornito ad Azure AD durante la registrazione dell'app con PowerShell AzureAD, PowerShell AzureRM o CLI di Azure.

## <a name="logging"></a>Registrazione
Le altre opzioni di configurazione abilitare la registrazione e risoluzione dei problemi. Vedere le [Logging](msal-logging.md) articolo per informazioni su come usarli.

## <a name="next-steps"></a>Passaggi successivi
Scopri [creazione di applicazioni client tramite MSAL.NET](msal-net-initializing-client-applications.md).

Scopri [creazione di applicazioni client usando msal. js](msal-js-initializing-client-applications.md).
