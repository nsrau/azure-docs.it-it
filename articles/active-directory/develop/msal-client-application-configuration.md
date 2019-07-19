---
title: Configurazione dell'applicazione client (Microsoft Authentication Library) | Azure
description: Informazioni sulle opzioni di configurazione per client pubblici e applicazioni client riservate in Microsoft Authentication Library (MSAL).
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
ms.date: 07/16/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 767f7362a6c46d864ba17f23f6506bf6cdb71414
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304738"
---
# <a name="application-configuration-options"></a>Opzioni di configurazione dell'applicazione

Nel codice si Inizializza una nuova applicazione client pubblica o riservata (o agente utente per MSAL. js) per autenticare e acquisire i token. È possibile impostare una serie di opzioni di configurazione quando si inizializza l'app client in Microsoft Authentication Library (MSAL). Queste opzioni rientrano in due gruppi:

- Opzioni di registrazione, tra cui:
    - [Autorità](#authority) di certificazione (costituito dall' [istanza](#cloud-instance) del provider di identità e dai [destinatari](#application-audience) dell'accesso per l'app e possibilmente dall'ID del tenant).
    - [ID client](#client-id).
    - [URI di reindirizzamento](#redirect-uri).
    - [Segreto client](#client-secret) (per le applicazioni client riservate).
- [Opzioni di registrazione](#logging), tra cui il livello di log, il controllo dei dati personali e il nome del componente che usa la libreria.

## <a name="authority"></a>Authority
L'autorità è un URL che indica una directory da cui MSAL può richiedere token. Le autorità comuni sono:

- https\://login.microsoftonline.com/\<tenant\>/, dove &lt;tenant&gt; è l'ID tenant del tenant Azure Active Directory (Azure ad) o di un dominio associato al tenant Azure ad. Utilizzato solo per l'accesso agli utenti di un'organizzazione specifica.
- https\://login.microsoftonline.com/common/. Usato per l'accesso agli utenti con account aziendali o dell'Istituto di istruzione o account Microsoft personali.
- https\://login.microsoftonline.com/Organizations/. Usato per l'accesso agli utenti con account aziendali o dell'Istituto di istruzione.
- https\://login.microsoftonline.com/consumers/. Consente di accedere agli utenti solo con account Microsoft personali (noti in precedenza come account Windows Live ID).

L'impostazione dell'autorità deve essere coerente con gli elementi dichiarati nel portale di registrazione dell'applicazione.

L'URL dell'autorità è costituito dall'istanza e dai destinatari.

L'autorità può essere:
- Un'autorità Azure AD cloud.
- Autorità Azure AD B2C. Vedere le [specifiche B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- Autorità Active Directory Federation Services (AD FS). Vedere [supporto ad FS](https://aka.ms/msal-net-adfs-support).

Azure AD autorità cloud hanno due parti:
- *Istanza* del provider di identità
- *Destinatari* dell'accesso per l'app

L'istanza e il gruppo di destinatari possono essere concatenati e forniti come URL dell'autorità. Nelle versioni di MSAL.NET precedenti a MSAL 3. *x*, era necessario comporre l'autorità autonomamente, in base al cloud di destinazione e al pubblico di accesso.  Questo diagramma mostra come viene composto l'URL dell'autorità:

![Composizione dell'URL dell'autorità](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Istanza cloud
L' *istanza* viene usata per specificare se l'app sta firmando gli utenti dal cloud pubblico di Azure o da cloud nazionali. Usando MSAL nel codice, è possibile impostare l'istanza cloud di Azure usando un'enumerazione o passando l'URL all' [istanza del cloud nazionale](authentication-national-cloud.md#azure-ad-authentication-endpoints) come `Instance` membro (se lo si conosce).

MSAL.NET genererà un'eccezione esplicita se `Instance` sono `AzureCloudInstance` specificati sia che.

Se non si specifica un'istanza, l'app sarà destinata all'istanza del cloud pubblico di Azure, ovvero `https://login.onmicrosoftonline.com`l'istanza dell'URL.

## <a name="application-audience"></a>Destinatari dell'applicazione

Il pubblico di accesso dipende dalle esigenze aziendali per l'app:
- Se sei uno sviluppatore line of business (LOB), probabilmente Produci un'applicazione a tenant singolo che verrà usata solo nell'organizzazione. In tal caso, è necessario specificare l'organizzazione, in base al relativo ID tenant (l'ID dell'istanza di Azure AD) o in base a un nome di dominio associato all'istanza di Azure AD.
- Se si è un ISV, potrebbe essere necessario eseguire l'accesso agli utenti con gli account aziendali e dell'Istituto di istruzione in qualsiasi organizzazione o in alcune organizzazioni (app multi-tenant). Tuttavia, potrebbe essere necessario che gli utenti possano accedere con i propri account Microsoft personali.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Come specificare i destinatari nel codice/configurazione
Utilizzando MSAL nel codice, è possibile specificare i destinatari utilizzando uno dei valori seguenti:
- Enumerazione del gruppo di destinatari dell'autorità Azure AD
- ID tenant, che può essere:
  - GUID (ID dell'istanza di Azure AD), per le applicazioni a tenant singolo
  - Un nome di dominio associato all'istanza di Azure AD (anche per le applicazioni a tenant singolo)
- Uno di questi segnaposto come ID tenant al posto dell'enumerazione del pubblico Azure AD autorità:
    - `organizations`per un'applicazione multi-tenant
    - `consumers`per accedere agli utenti solo con gli account personali
    - `common`per accedere agli utenti con account aziendali o dell'Istituto di istruzione o con account Microsoft personali

MSAL genererà un'eccezione significativa se si specificano i destinatari dell'autorità Azure AD e l'ID tenant.

Se non si specifica un gruppo di destinatari, l'app sarà destinata a Azure AD e account Microsoft personali come destinatari. (Ovvero, si comporterà come se `common` fosse specificato).

### <a name="effective-audience"></a>Destinatari efficaci
Il pubblico effettivo per l'applicazione sarà il valore minimo (se esiste un'intersezione) tra i destinatari impostati nell'app e i destinatari specificati nella registrazione dell'app. In realtà, l'esperienza di [registrazioni app](https://aka.ms/appregistrations) consente di specificare i destinatari (tipi di account supportati) per l'app. Per altre informazioni, vedere [Avvio rapido: Registrare un'applicazione con la piattaforma](quickstart-register-app.md)di identità Microsoft.

Attualmente, l'unico modo per ottenere un'app per l'accesso degli utenti con solo account Microsoft personali consiste nel configurare entrambe queste impostazioni:
- Impostare i destinatari della registrazione dell' `Work and school accounts and personal accounts`app su.
- Impostare il gruppo di destinatari nel codice/configurazione `AadAuthorityAudience.PersonalMicrosoftAccount` su ( `TenantID` o = "consumer").

## <a name="client-id"></a>ID client
L'ID client è l'ID univoco dell'applicazione (client) assegnato all'app da Azure AD quando l'app è stata registrata.

## <a name="redirect-uri"></a>URI di reindirizzamento
L'URI di reindirizzamento è l'URI al quale il provider di identità invierà di nuovo i token di sicurezza.

### <a name="redirect-uri-for-public-client-apps"></a>URI di reindirizzamento per le app client pubbliche
Se sei uno sviluppatore di app client pubbliche che usa MSAL:
- Si vuole usare `.WithDefaultRedirectUri()` nelle applicazioni desktop o UWP (MSAL.NET 4.1 +). Questo metodo imposterà la proprietà URI di reindirizzamento dell'applicazione client pubblica sull'URI di reindirizzamento consigliato predefinito per le applicazioni client pubbliche. 

  Piattaforma  | URI di reindirizzamento  
  ---------  | --------------
  App desktop (.NET FW) | `https://login.microsoftonline.com/common/oauth2/nativeclient` 
  UWP | valore di `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()`. Questo consente l'accesso SSO con il browser impostando il valore sul risultato di WebAuthenticationBroker. GetCurrentApplicationCallbackUri () che è necessario registrare
  .NET Core | [https://login.microsoftonline.com/consumers/](`https://localhost`). Ciò consente all'utente di usare il browser di sistema per l'autenticazione interattiva perché .NET Core non dispone al momento di un'interfaccia utente per la visualizzazione Web incorporata.

- Non è necessario aggiungere un URI di reindirizzamento se si sta creando un'applicazione Novell Android e iOS che non supporta Service Broker (l'URI di reindirizzamento viene impostato `msal{ClientId}://auth` automaticamente su per Novell Android e iOS

- È necessario configurare l'URI di reindirizzamento in [registrazioni app](https://aka.ms/appregistrations):

   ![URI di reindirizzamento in Registrazioni app](media/msal-client-application-configuration/redirect-uri.png)

È possibile eseguire l'override dell'URI di Reindirizzamento `RedirectUri` usando la proprietà (ad esempio, se si usano i broker). Di seguito sono riportati alcuni esempi di URI di reindirizzamento per lo scenario:

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos`= $ "msauth. {Bundle. ID}://auth ";

Per informazioni dettagliate, vedere la [documentazione per Android e iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS).

### <a name="redirect-uri-for-confidential-client-apps"></a>URI di reindirizzamento per le app client riservate
Per le app Web, l'URI di reindirizzamento (o URI di risposta) è l'URI che Azure AD utilizzerà per restituire il token all'applicazione. Questo URI può essere l'URL dell'app Web/API Web se l'app riservata è uno di questi. L'URI di reindirizzamento deve essere registrato nella registrazione dell'app. Questa registrazione è particolarmente importante quando si distribuisce un'app che è stata inizialmente testata localmente. È quindi necessario aggiungere l'URL di risposta dell'app distribuita nel portale di registrazione dell'applicazione.

Per le app daemon non è necessario specificare un URI di reindirizzamento.

## <a name="client-secret"></a>Segreto client
Questa opzione specifica il segreto client per l'app client riservata. Questo segreto (password dell'app) viene fornito dal portale di registrazione delle applicazioni o fornito a Azure AD durante la registrazione dell'app con PowerShell AzureAD, PowerShell AzureRM o l'interfaccia della riga di comando di Azure.

## <a name="logging"></a>Registrazione
Le altre opzioni di configurazione consentono la registrazione e la risoluzione dei problemi. Per informazioni dettagliate su come usarli, vedere l'articolo relativo alla [registrazione](msal-logging.md) .

## <a name="next-steps"></a>Passaggi successivi
Informazioni sulla [creazione di istanze di applicazioni client tramite MSAL.NET](msal-net-initializing-client-applications.md).

Informazioni sulla [creazione di istanze di applicazioni client con MSAL. js](msal-js-initializing-client-applications.md).
