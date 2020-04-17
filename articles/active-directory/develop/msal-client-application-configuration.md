---
title: Configurazione dell'applicazione client (MSAL) Azure
titleSuffix: Microsoft identity platform
description: Informazioni sulle opzioni di configurazione per le applicazioni client pubbliche e riservate che utilizzano Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/27/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: b4595a63613afa3c6fef2fa2a85647d8b70b1388
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534466"
---
# <a name="application-configuration-options"></a>Opzioni di configurazione dell'applicazione

Nel codice, inizializzare una nuova applicazione client pubblica o riservata (o agente utente per MSAL.js) per autenticare e acquisire i token. È possibile impostare una serie di opzioni di configurazione quando si inizializza l'app client in Microsoft Authentication Library (MSAL). Queste opzioni rientrano in due gruppi:

- Opzioni di registrazione, tra cui:
    - [Autorità](#authority) (composta [dall'istanza](#cloud-instance) del provider di identità e dal [gruppo](#application-audience) di destinatari di accesso per l'app ed eventualmente dall'ID tenant).
    - [ID client](#client-id).
    - [URI di reindirizzamento](#redirect-uri).
    - [Segreto client](#client-secret) (per applicazioni client riservate).
- [Opzioni di registrazione](#logging), inclusi il livello di registrazione, il controllo dei dati personali e il nome del componente tramite la libreria.

## <a name="authority"></a>Authority

L'autorità è un URL che indica una directory da cui MSAL può richiedere token. Le autorità comuni sono:

- https\:\<//login.microsoftonline.com/\>tenant &lt;/, dove tenant&gt; è l'ID tenant del tenant di Azure Active Directory (Azure AD) o un dominio associato a questo tenant di Azure AD. Utilizzato solo per accedere agli utenti di un'organizzazione specifica.
- https\://login.microsoftonline.com/common/. Utilizzato per accedere agli utenti con account aziendali e dell'istituto di istruzione o account Microsoft personali.
- https\:/login.microsoftonline.com/organizations/. Utilizzato per accedere agli utenti con account aziendali e dell'istituto di istruzione.
- https\://login.microsoftonline.com/consumers/. Utilizzato per accedere agli utenti con solo account Microsoft personali (precedentemente noti come account Windows Live ID).

L'impostazione dell'autorità deve essere coerente con ciò che viene dichiarato nel portale di registrazione dell'applicazione.

L'URL dell'autorità è composto dall'istanza e dal gruppo di destinatari.

L'autorità può essere:
- Un'autorità cloud di Azure AD.
- Un'autorità B2C di Azure AD. Vedere [Specifiche B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- Un'autorità di Active Directory Federation Services (ADFS). Vedere [Supporto di ADFS](https://aka.ms/msal-net-adfs-support).

Le autorità cloud di Azure AD sono parti:
- Istanza del provider *di* identità
- Il gruppo di *destinatari* di accesso per l'app

L'istanza e il gruppo di destinatari possono essere concatenati e forniti come URL dell'autorità. Nelle versioni di MSAL.NET precedenti a MSAL 3. *x*, era necessario comporre l'autorità manualmente, in base al cloud di destinazione e al gruppo di destinatari di accesso.  Questo diagramma mostra come è composto l'URL dell'autorità:

![Modalità di composizione dell'URL dell'autorità](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Istanza cloud

*L'istanza* viene usata per specificare se l'app firma gli utenti dal cloud pubblico di Azure o dai cloud nazionali. Usando MSAL nel codice, è possibile impostare l'istanza cloud di Azure usando un'enumerazione o passando l'URL all'istanza [cloud nazionale](authentication-national-cloud.md#azure-ad-authentication-endpoints) come `Instance` membro (se lo si conosce).

MSAL.NET genererà un'eccezione esplicita se entrambi `Instance` e `AzureCloudInstance` vengono specificati.

Se non si specifica un'istanza, l'app sarà destinata all'istanza del cloud pubblico di Azure (l'istanza dell'URL). `https://login.onmicrosoftonline.com`

## <a name="application-audience"></a>Destinatari dell'applicazione

Il pubblico di accesso dipende dalle esigenze aziendali per la tua app:
- Se sei uno sviluppatore line-of-business (LOB), probabilmente si produrrà un'applicazione single-tenant che verrà utilizzata solo nell'organizzazione. In tal caso, è necessario specificare l'organizzazione in base all'ID tenant (ID dell'istanza di Azure AD) o in base a un nome di dominio associato all'istanza di Azure AD.
- Se sei un ISV, potresti voler accedere agli utenti con i loro account aziendali e dell'istituto di istruzione in qualsiasi organizzazione o in alcune organizzazioni (app multi-tenant). Ma potresti anche voler fare in modo che gli utenti esechino con i loro account Microsoft personali.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Come specificare il pubblico nel codice/configurazione

Utilizzando MSAL nel codice, specificare il gruppo di destinatari utilizzando uno dei seguenti valori:
- Enumerazione del gruppo di destinatari dell'autorità di Azure ADThe Azure AD authority audience enumeration
- L'ID tenant, che può essere:
  - Un GUID (ID dell'istanza di Azure AD) per le applicazioni single-tenant
  - Un nome di dominio associato all'istanza di Azure AD (anche per le applicazioni single-tenant)A domain name associated with your Azure AD instance (also for single-tenant applications)
- Uno di questi segnaposto come ID tenant al posto dell'enumerazione del gruppo di destinatari dell'autorità di Azure AD:One of these placeholders as a tenant ID in place of the Azure AD authority audience enumeration:
    - `organizations`per un'applicazione multi-tenant
    - `consumers`per accedere agli utenti solo con i propri account personali
    - `common`per accedere agli utenti con i loro account aziendali e dell'istituto di istruzione o i loro account Microsoft personali

MSAL genererà un'eccezione significativa se si specificano sia il gruppo di destinatari dell'autorità di Azure AD che l'ID tenant.

Se non specifichi un gruppo di destinatari, l'app sarà destinata ad Azure AD e agli account Microsoft personali come gruppo di destinatari. (Ovvero, si comporterà come `common` se fossero specificati.)

### <a name="effective-audience"></a>Un pubblico efficace

Il pubblico effettivo per l'applicazione sarà il numero minimo (se è presente un'intersezione) del pubblico impostato nell'app e del pubblico specificato nella registrazione dell'app. Infatti, l'esperienza di [registrazione dell'app](https://aka.ms/appregistrations) consente di specificare il gruppo di destinatari (i tipi di account supportati) per l'app. Per ulteriori informazioni, vedere [Guida introduttiva: Registrare un'applicazione con la piattaforma di identità Microsoft](quickstart-register-app.md).

Attualmente, l'unico modo per ottenere un'app per accedere agli utenti con solo account Microsoft personali consiste nel configurare entrambe queste impostazioni:
- Impostare il pubblico `Work and school accounts and personal accounts`di registrazione dell'app su .
- Impostare il pubblico nel `AadAuthorityAudience.PersonalMicrosoftAccount` codice/configurazione su (o `TenantID` "consumatori").

## <a name="client-id"></a>ID client

L'ID client è l'ID univoco dell'applicazione (client) assegnato all'app da Azure AD al momento della registrazione dell'app.

## <a name="redirect-uri"></a>URI di reindirizzamento

L'URI di reindirizzamento è l'URI a cui il provider di identità invierà nuovamente i token di sicurezza.

### <a name="redirect-uri-for-public-client-apps"></a>URI di reindirizzamento per le app client pubbliche

Se sei uno sviluppatore di app client pubblico che usa MSAL:
- Si desidera utilizzare `.WithDefaultRedirectUri()` in applicazioni desktop o UWP (MSAL.NET 4.1 e versioni successive). Questo metodo imposterà la proprietà uri di reindirizzamento dell'applicazione client pubblica sull'URI di reindirizzamento consigliato predefinito per le applicazioni client pubbliche.

  Piattaforma  | URI di reindirizzamento
  ---------  | --------------
  Desktop app (.NET FW) | `https://login.microsoftonline.com/common/oauth2/nativeclient`
  UWP | valore `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()`di . In questo modo SSO con il browser impostando il valore sul risultato di WebAuthenticationBroker.GetCurrentApplicationCallbackUri() che è necessario registrare
  .NET Core | `https://localhost`. Ciò consente all'utente di utilizzare il browser di sistema per l'autenticazione interattiva poiché .NET Core non dispone di un'interfaccia utente per la visualizzazione Web incorporata al momento.

- Non è necessario aggiungere un URI di reindirizzamento se stai creando un'applicazione Xamarin Android e iOS `msal{ClientId}://auth` che non supporta broker (l'URI di reindirizzamento viene impostato automaticamente su per Xamarin Android e iOS

- È necessario configurare l'URI di reindirizzamento nelle [registrazioni delle app:](https://aka.ms/appregistrations)

   ![URI di reindirizzamento nelle registrazioni delle app](media/msal-client-application-configuration/redirect-uri.png)

È possibile eseguire l'override `RedirectUri` dell'URI di reindirizzamento utilizzando la proprietà (ad esempio, se si utilizzano i broker). Di seguito sono riportati alcuni esempi di URI di reindirizzamento per tale scenario:Here are some examples of redirect URIs for that scenario:

- `RedirectUriOnAndroid`"msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos`"msauth. "Bundle.ID" ://auth";

Per ulteriori dettagli su iOS, vedere [Eseguire la migrazione di applicazioni iOS che utilizzano Microsoft Authenticator da ADAL.NET allMSAL.NET](msal-net-migration-ios-broker.md) e sfruttando il broker in [iOS.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS)
Per ulteriori dettagli su Android, vedere [Autenticazione brokered in Android](brokered-auth.md).

### <a name="redirect-uri-for-confidential-client-apps"></a>URI di reindirizzamento per le app client riservate

Per le app Web, l'URI di reindirizzamento (o URI di risposta) è l'URI che verrà usato da Azure AD per inviare il token all'applicazione. Questo URI può essere l'URL dell'app Web o dell'API Web se l'app confidenziale è una di queste. L'URI di reindirizzamento deve essere registrato nella registrazione dell'app. Questa registrazione è particolarmente importante quando si distribuisce un'app che è stata inizialmente testata in locale. È quindi necessario aggiungere l'URL di risposta dell'app distribuita nel portale di registrazione dell'applicazione.

Per le app daemon, non è necessario specificare un URI di reindirizzamento.

## <a name="client-secret"></a>Segreto client

Questa opzione specifica il segreto client per l'app client riservata. Questo segreto (password dell'app) viene fornito dal portale di registrazione dell'applicazione o fornito ad Azure AD durante la registrazione dell'app con PowerShell AzureAD, PowerShell AzureRM o l'interfaccia della riga di comando di Azure.This secret (app password) is provided by the application registration portal or provided to Azure AD during app registration with PowerShell AzureAD, PowerShell AzureRM, or Azure CLI.

## <a name="logging"></a>Registrazione

Le altre opzioni di configurazione abilitano la registrazione e la risoluzione dei problemi. Vedere l'articolo [Registrazione](msal-logging.md) per informazioni dettagliate su come utilizzarli.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulla [creazione di istanze di applicazioni client tramite MSAL.NET](msal-net-initializing-client-applications.md).
Informazioni sulla [creazione di istanze di applicazioni client tramite MSAL.js](msal-js-initializing-client-applications.md).
