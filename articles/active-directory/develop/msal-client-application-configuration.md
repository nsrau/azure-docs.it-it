---
title: Configurazione dell'applicazione client (Microsoft Authentication Library) | Azure
description: Informazioni sulle opzioni di configurazione per client riservati e pubblica applicazioni in Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
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
ms.openlocfilehash: 3a48eea9fedd2d82f44693d58b31ee0d5c8c288d
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138546"
---
# <a name="application-configuration-options"></a>Opzioni di configurazione dell'applicazione

Nel codice, si inizializza un nuovo client pubblici o riservati (o user-agent per msal. js) dell'applicazione per l'autenticazione e acquisire i token.  Esistono una serie di diverse opzioni di configurazione che può essere impostato durante l'inizializzazione all'applicazione client in MSAL. Queste opzioni possono essere suddivisi in due gruppi:

- Opzioni di registrazione, tra cui:
    - [Autorità](#authority) (costituito da provider di identità [istanza](#cloud-instance) e Accedi [audience](#application-audience) per l'applicazione ed eventualmente l'ID tenant).
    - [ID client](#client-id)
    - [URI di reindirizzamento](#redirect-uri)
    - [segreto client](#client-secret) (per le applicazioni client riservato).
- [Le opzioni di registrazione](#logging), inclusi il nome del componente usando la libreria di livello di registrazione e controllo dei dati personali.

## <a name="authority"></a>Authority
L'autorità è un URL che indica una directory che MSAL può richiedere token da. Autorità di solito sono:

- https://login.microsoftonline.com/&lt; tenant&gt;/, dove &lt;tenant&gt; è l'ID tenant del tenant di Azure AD o in un dominio associati al tenant Azure AD.  Usato solo per consentire agli utenti di un'organizzazione specifica.
- https://login.microsoftonline.com/common/. Utilizzato per consentire agli utenti con e dell'istituto di istruzione oppure un account persona Microsoft.
- https://login.microsoftonline.com/organizations/. Utilizzato per consentire agli utenti con account aziendali e dell'istituto di istruzione.
- https://login.microsoftonline.com/consumers/. Utilizzato per consentire agli utenti con account Microsoft personale solo (interattiva).

L'impostazione dell'autorità deve essere coerenza con ciò che viene dichiarata nel portale di registrazione dell'applicazione.

L'URL dell'autorità è costituito l'istanza e il gruppo di destinatari.

L'autorità può essere:
- un'autorità di Cloud di Azure Active directory
- un'autorità di Azure AD B2C. Visualizzare [B2C specifiche](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- un'autorità di ad FS. Visualizzare [supporto di ad FS](https://aka.ms/msal-net-adfs-support).

Le autorità di Azure AD cloud hanno due parti:
- il provider di identità **istanza**
- l'accesso al posto **audience** per l'applicazione

L'istanza e il gruppo di destinatari possono essere concatenati e forniti come l'URL dell'autorità. Nelle versioni di MSAL.NET prima MSAL 3.x, era necessario compose manualmente nel cloud che si desidera di destinazione e il pubblico accesso in base l'autorità.  Il diagramma seguente mostra come viene composto l'URL dell'autorità.

![Authority](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Istanza cloud
Il **istanza** consente di specificare se l'applicazione esegue l'accesso degli utenti dal cloud pubblico di Microsoft Azure o dal cloud nazionali. Con MSAL nel codice, l'istanza di cloud di Azure può essere impostata tramite un'enumerazione o passando l'URL per il [istanza del cloud nazionale](authentication-national-cloud.md#azure-ad-authentication-endpoints) come il `Instance` membro (se lo si conosce).

MSAL.NET genererà un'eccezione esplicita se entrambe `Instance` e `AzureCloudInstance` specificati. 

Destinazione per l'istanza di cloud pubblico di Azure se non si specifica un'istanza, l'app (l'istanza di URL `https://login.onmicrosoftonline.com`).

## <a name="application-audience"></a>Destinatari dell'applicazione

Il pubblico accesso dipende dalle esigenze aziendali per l'applicazione:
- Se si ha una linea di sviluppatore di business (LOB), si sarà probabilmente produrre un'applicazione Single-tenant, che verrà usata solo all'interno dell'organizzazione. In tal caso, è necessario specificare i dati questa organizzazione, l'ID tenant (ID di Azure Active Directory) o un nome di dominio associata a questa Azure Active Directory.
- Se sei un fornitore di software indipendente, è possibile eseguire l'accesso degli utenti con il proprio lavoro e gli account dell'istituto di istruzione in qualsiasi organizzazione, o alcune organizzazioni (app multi-tenant), ma è anche possibile chiedere agli utenti di accedere con l'account persona Microsoft.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Come specificare i destinatari nella codice/configurazione
Si usa MSAL nel codice, specificare i destinatari usando:
- entrambi Azure AD autorità audience enumerazione. 
- o l'ID tenant, che può essere:
  - un GUID per le applicazioni a tenant singolo, (l'ID di Azure Active Directory),
  - un nome di dominio associato con Azure Active Directory (anche per le applicazioni a tenant singolo)
- o uno di questi segnaposto come ID tenant al posto dell'enumerazione di destinatari autorità di Azure AD:
    - `organizations` per un'applicazione di più tenant
    - `consumers` per consentire agli utenti solo con i propri account personali
    - `common` per consentire agli utenti con loro e dell'istituto di istruzione o account personali Microsoft

MSAL genererà un'eccezione significativa se si specificano sia pubblico dell'autorità di Azure AD e l'ID tenant. 

Se non si specifica un gruppo di destinatari destinazione per l'app Azure AD e gli account Microsoft personali come un gruppo di destinatari (vale a dire `common`).

### <a name="effective-audience"></a>Gruppo di destinatari efficace
I destinatari efficaci per l'applicazione sarà il valore minimo (se esiste un'intersezione) del gruppo di destinatari che è impostato nell'applicazione e i destinatari specificati nella registrazione dell'applicazione. In effetti, l'esperienza di registrazione applicazione ([registrazione dell'App](https://aka.ms/appregistrations)) consente di specificare i destinatari (tipi di account supportati) per l'applicazione. Vedere [Avvio rapido: Registrare un'applicazione con la piattaforma delle identità Microsoft](quickstart-register-app.md) per altre informazioni.

Attualmente, l'unico modo per ottenere un'applicazione per consentire agli utenti con account personali di Microsoft consiste nell'impostare:
- impostare i destinatari di registrazione app "Lavoro e dell'istituto di istruzione e gli account personali" e,
- e, impostare i destinatari nel codice / configurazione `AadAuthorityAudience.PersonalMicrosoftAccount` (o `TenantID `= "utenti")

## <a name="client-id"></a>ID client
ID univoco dell'applicazione (client) assegnato all'app da Azure AD quando è stata registrata l'app.

## <a name="redirect-uri"></a>URI di reindirizzamento
L'URI di reindirizzamento corrisponde all'URI in cui il provider di identità invierà nuovamente i token di sicurezza. 

### <a name="redirect-uri-for-public-client-applications"></a>URI di reindirizzamento per le applicazioni client pubblici
Per gli sviluppatori di applicazioni client pubblico con MSAL:
- Non è necessario passare il ``RedirectUri`` come viene calcolato automaticamente da MSAL. Questo reindirizzamento URI è impostato su valori a seconda della piattaforma seguenti:

- ``urn:ietf:wg:oauth:2.0:oob`` per tutte le piattaforme Windows
- ``msal{ClientId}://auth`` per Xamarin. Android e iOS

Tuttavia, il reindirizzamento dell'URI deve essere configurato nel [registrazione dell'App](https://aka.ms/appregistrations).

![URI di reindirizzamento nel portale](media/msal-client-application-configuration/redirect-uri.png)

È possibile sostituire l'URI di reindirizzamento usando il `RedirectUri` proprietà, ad esempio se si usa Broker. Di seguito sono riportati alcuni esempi di URI di reindirizzamento in questo caso:

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $"msauth. {Bundle.ID}://auth";

Per informazioni dettagliate, vedere le specifiche di Android e [specifiche di iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS)

### <a name="redirect-uri-for-confidential-client-applications"></a>URI di reindirizzamento per le applicazioni client riservato
Per le app web, l'URI reindirizzamento (o l'URI di risposta), è l'URI in cui Azure AD contatterà nuovamente l'applicazione con il token. Ciò può essere l'URL dell'applicazione Web / API Web se uno di questi è la riservatezza.  Questo URI di reindirizzamento deve essere registrata nella registrazione dell'app. Ciò è particolarmente importante quando si distribuisce un'applicazione che è inizialmente testati in locale. È quindi necessario aggiungere l'URL di risposta dell'applicazione distribuita nel portale di registrazione dell'applicazione.

Per le app daemon, non devi specificare un URI di reindirizzamento.

## <a name="client-secret"></a>Segreto client
Il segreto client per l'applicazione client riservato. Il segreto (password applicazione) è fornito dal portale di registrazione dell'applicazione o fornito ad Azure AD durante la registrazione dell'applicazione con PowerShell AzureAD, PowerShell AzureRM o CLI di Azure.

## <a name="logging"></a>Registrazione
Le altre opzioni di abilitare la registrazione e risoluzione dei problemi. Per altre informazioni, vedere la [Logging](msal-logging.md) pagina per altri dettagli su come usarli.

## <a name="next-steps"></a>Passaggi successivi
Scopri [creazione di applicazioni client tramite MSAL.NET](msal-net-initializing-client-applications.md).

Scopri [creazione di applicazioni client usando msal. js](msal-js-initializing-client-applications.md).
